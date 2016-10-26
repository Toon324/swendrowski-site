Published: 10-26-16
Title: Integrating with Netsuite using C#
PromoImageUrl: http://www.allunna.com/hs-fs/hub/334319/file-599227512-jpg/Allunnas_Pages_Pictures/Netsuite.jpg
Category: csharp
Intro: A detailed guide to how to make heads or tails of the Netsuite SOAP API in C#
---

It's 4pm on a bright and sunny Friday as you're chatting with coworkers about weekend plans. You're all packed up for the weekend and it's looking like a good chance to get outside before Fall starts setting in. Your boss chimes into the conversation with a simple question.

> Do you think we could pull everyone's logged time out of our finance tool, Netsuite? It sure would be nice to integrate that data with some other tools.

The sun blots out as heavy clouds start unleashing a downpour outside, and your hopes at an easy weekend start to drain.


If this story sounds like you, you've come to the right place - the goal of this post is to help you make heads or tails of what on earth is going on with Netsuite's API, and hopefully let you get your data without spending your weekend reading through every Google result hoping to stumble upon something.


## Before you Proceed

![](http://cloud-elements.com/wp-content/uploads/2014/09/ce-logo.png)

Check to see if Cloud Elements can provide the data you need. Cloud Elements does the heavy-lifting of integrating with various services and opens them up via a standardized Rest API that is much simpler to consume than a full Netsuite SOAP integration.

Cloud Elements provides the following "Hubs":

[CRM Hub](http://cloud-elements.com/hubs/crm-hub/)
- Contacts
- Companies
- Leads
- Lists

[Finance Hub](http://cloud-elements.com/hubs/finance-hub/)
- Customers
- Vendors
- Invoices
- Payments

[Human Capital Hub](http://cloud-elements.com/hubs/human-capital-hub/)
- Employees
- Requisitions

If all of the data you need is in any of these lists, I encourage you to try out Cloud Elements and only fall back to a native implementation if needed.


## Netsuite C# Integration - Where do I start?

The best resource you can reference for integrating is the example C# CLI application that Netsuite provides [here](http://www.netsuite.com/portal/developers/resources/suitetalk-sample-applications.shtml). Download the CRM .Net example and take a look - examples of most of what you would want to do are littered throughout the various methods (although poorly explained), and there are a few methods you will want to borrow.

The first thing you will need to do is reference the Netsuite SOAP service so that C# classes can be generated and the API can be consumed. This is done by adding a Web or Service reference [more on that here](http://stackoverflow.com/a/2158270).

The URL you need to consume is **https://webservices.netsuite.com/wsdl/v2016_1_0/netsuite.wsdl**

As seen in the .Net example, you will need a constuctor similar to the following:

```
public NetsuiteService()
{
    IsAuthenticated = false;

    Service = new DataCenterAwareNetSuiteService(ConfigurationManager.AppSettings["login.acct"])
    {
        Timeout = 1000 * 60 * 60 * 2,
        CookieContainer = new CookieContainer()
    };

    // Enables SOAPscope to work through SSL
    ServicePointManager.ServerCertificateValidationCallback +=
        (sender, certificate, chain, sslPolicyErrors) => true;
}
```

This relies on a custom NetSuite service class which can also be found in the example, but is copied below for convenience.

```
public class DataCenterAwareNetSuiteService : NetSuiteService
{
    public DataCenterAwareNetSuiteService(string account)
    {
        var originalUri = new Uri(Url);
        var urls = getDataCenterUrls(account).dataCenterUrls;
        var dataCenterUri = new Uri(urls.webservicesDomain + originalUri.PathAndQuery);
        Url = dataCenterUri.ToString();
    }
}
```

Finally, every request requires a login, so the following two methods will allow you to easily login.

```
public ApplicationInfo CreateApplicationId()
{
    var applicationInfo = new ApplicationInfo
    {
        applicationId = "C7D3925E-778B-491C-A67D-E0613D82287B"
    };
    return applicationInfo;
}

public void Login()
{
    if (!IsAuthenticated)
    {
        Service.applicationInfo = CreateApplicationId();

        // Populate Passport object with all login information
        var passport = new Passport();
        var role = new RecordRef();

        passport.email = ConfigurationManager.AppSettings["login.email"];
        passport.password = ConfigurationManager.AppSettings["login.password"];
        role.internalId = ConfigurationManager.AppSettings["login.roleId"];
        passport.role = role;
        passport.account = ConfigurationManager.AppSettings["login.acct"];

        Service.login(passport);

        IsAuthenticated = true;
    }
}
```

## Helpful Methods

#### Search Results

Netsuite will only result the first page of results by default. Additionally, results are returned as `SearchRow`'s rather than the proper Object by default. If you want all results in a single list of the proper Object, the following method I adapted from someone else (sorry I forgot your name!) allows you to easily execute a search and get back the desired result. This method initially had an issue with failing out if the login expired due to a long program runtime, so the new method ensures the login is valid.

```
private IEnumerable<T> Get<T>(SearchRecord searchRecord)
{
    // This is returned.
    List<T> list = new List<T>();

    // The suitetalk service return this.
    SearchResult result = null;

    do
    {
        // .search returns the first page of data.
        if (result == null)
        {
            result = this.LoggedInSearch(searchRecord);
        }
        else // .searchMore returns the next page(s) of data.
        {
            result = Service.searchMoreWithId(result.searchId, result.pageIndex + 1);
        }

        if (result.status.isSuccess)
        {
            foreach (var record in result.recordList)
            {
                if (record is T)
                {
                    list.Add((T)Convert.ChangeType(record, typeof(T)));
                }
            }
        }
    }
    while (result.pageIndex < result.totalPages);

    return list;
}

. . .

public static class ServiceExtensions
{
    public static SearchResult LoggedInSearch(this NetsuiteService netsuiteService, SearchRecord searchToExecute)
    {
        try
        {
            return netsuiteService.Service.search(searchToExecute);
        }
        catch
        {
            service.IsAuthenticated = false;
            service.Login();
            return netsuiteService.Service.search(searchToExecute);
        }
    }
}
```

#### Custom Field Values

Netsuite supports Custom fields on records, but only seems to return the field if it has a value, making it more difficult to grab the value. Examples on usage with further notes are in the Search section below.

```
public static class CustomFieldRefExtensions
{
    public static T GetCustomField<T>(this CustomFieldRef[] customFields, string fieldName) where T : CustomFieldRef
    {
        return (T)customFields.FirstOrDefault(x => x.scriptId == "custentity" + fieldName);
    }
}
```

## Searches and You

There are 3 kinds of Searches - `Search`, `SearchAdvanced`, and `SearchBasic`. Despite what you would assume, **ONLY** `Search` and `SearchAdvanced` can actually be executed as searches.

Each type of Object has its own series of `Search` objects that usually match up to their name in Netsuite - if you're searching on logged time as in our examples, you want `TimeBillSearch` which returns `TimeBill` objects

### Search

For a basic search where you want to get all results, search is fairly easy to write and execute.

```
public IEnumerable<TimeBill> GetAllTime()
{
	// Every search requires a valid session
	Login();

    var search = new TimeBillSearch();

    return Get<TimeBill>(search);
}
```

![](https://images.duckduckgo.com/iu/?u=http%3A%2F%2Fwww.jillstanek.com%2Fwp%2Fwp-content%2Fuploads%2F2014%2F08%2FThat-Was-Easy-Button.png)

This will return a massive object with very few fields actually filled out, but you'll have access to the raw data you want via the fields `internalId`, `tranDate` (Date Logged), `hours.TimeSpan` (hours logged), and `isBillable`.

### SearchBasic

Returning all results is slow, so what if we want to restrict the results to just time in the last month that is labled as "Actual Time"?

`SearchBasic`, rather than being a basic search, is an object that allows you to filter searches so that Netsuite only returns a subset of results. **This object has to be assigned to the `Search` object in order for it to taken into account**

```
public IEnumerable<TimeBill> GetAllTime(DateTime from, DateTime to)
{
    // Every search requires a valid session
    Login();

    var search = new TimeBillSearch();
    var searchBasic = new TimeBillSearchBasic();

    searchBasic.dateCreated = new SearchDateField
    {
        @operator = SearchDateFieldOperator.within,
        operatorSpecified = true,
        searchValue = from,
        searchValueSpecified = true,
        searchValue2 = to,
        searchValue2Specified = true
    };

    searchBasic.type = new SearchEnumMultiSelectField
    {
        @operator = SearchEnumMultiSelectFieldOperator.anyOf,
        operatorSpecified = true,
        searchValue = new string[] { "_actualTime" }
    };

    search.basic = searchBasic;

    return Get<TimeBill>(search);
}
```

`SearchBasic` has several `Field` objects that can be created and passed values in order to filter results. These usually match up with the search screen found in Netsuite. Each field seems to require the `operatorSpecified = true` line, which is annoying at best. Having method Autocomplete in your editor is highly recommended for figuring out what parameters a search has - `TimeBillSearchBasic` is different from `ProjectTaskSearchBasic`, and so on.

This method can be cleaned up a bit by using Object instantialization:

```
public IEnumerable<TimeBill> GetAllTime(DateTime from, DateTime to)
{
    // Every search requires a valid session
    Login();

    var search = new TimeBillSearch
    {
    	basic = new TimeBillSearchBasic
        {
        	dateCreated = new SearchDateField
            {
                @operator = SearchDateFieldOperator.within,
                operatorSpecified = true,
                searchValue = from,
                searchValueSpecified = true,
                searchValue2 = to,
                searchValue2Specified = true
            },
            type = new SearchEnumMultiSelectField
            {
                @operator = SearchEnumMultiSelectFieldOperator.anyOf,
                operatorSpecified = true,
                searchValue = new [] { "_actualTime" }
            }
        }
    };

    return Get<TimeBill>(search);
}
```

### SearchAdvanced

`SearchAdvanced` is where things get extremely obtuse. With `SearchAdvanced`, you can execute on a custom Saved Search in Netsuite (useful if you prefer setting up filters in Netsuite rather than through code) as well as specify what fields get returned in a result.

The `searchOpportunityWithAdvSearch()` method is a great example of how this search is constructed, with over 100 lines of code for a single search.

Executing a saved search isn't too much hassle:

```
public IEnumerable<TimeBill> GetSavedSearchTime()
{
	 // Every search requires a valid session
    Login();

    var searchAdvanced = new TimeBillSearchAdvanced
    {
        savedSearchId = "444"
    };

    return Get<TimeBill>(searchAdvanced);
}
```

Custom columns, however, are less than ideal. If you want filtered results, `SearchAdvanced` needs a `Search` object assigned to its `criteria` field to filter, and that `Search` object then needs the appropriate `SearchBasic` object similiar to our previous examples. Let's say we want only the hours and Employee ID as results:

```
public IEnumerable<TimeBill> GetAllTime(DateTime from, DateTime to)
{
    // Every search requires a valid session
    Login();

    var searchAdvanced = new TimeBillSearchAdvanced
    {
        criteria = new TimeBillSearch
        {
            basic = new TimeBillSearchBasic
            {
                dateCreated = new SearchDateField
                {
                    @operator = SearchDateFieldOperator.within,
                    operatorSpecified = true,
                    searchValue = from,
                    searchValueSpecified = true,
                    searchValue2 = to,
                    searchValue2Specified = true
                },
                type = new SearchEnumMultiSelectField
                {
                    @operator = SearchEnumMultiSelectFieldOperator.anyOf,
                    operatorSpecified = true,
                    searchValue = new[] {"_actualTime"}
                }
            }
        },
        columns = new TimeBillSearchRow
        {
            basic = new TimeBillSearchRowBasic
            {
                internalId = new [] { new SearchColumnSelectField() },
                hours = new [] { new SearchColumnStringField() }
            }
        }
    };

    return Get<TimeBill>(searchAdvanced);
}
```

Arrays that hold a single `Field` Object indicate that the Column should be returned.


### Joined Searches

Up until now, we have been getting back the Employee who is logging time as only a Netsuite ID - not very useful if we want to know who is logging time to what projects. Taking all of the resulting ID's and then doing another search in inefficent, but luckily Netsuite supports Joined searches, with one important note: **Netsuite does not return Joins by default and needs to be configured to do so.**

The Netsuite service itself (in the example below it is `Service`) starts off with a `null` `searchPreferences` object, which will default all results to Netsuite's server-side defaults.

Setting the preferences to return Joins is done by creating a new object and assigning it to the service:

```
Service.searchPreferences = new SearchPreferences
{
    bodyFieldsOnly = false,
    returnSearchColumns = true
};
```

This will now apply to EVERY future search with the existing searches, which may have performance implications.

Much like the rest of the API, you register which Joins you want returned by creating an Object and assigning it to the `Search` object.

```
public IEnumerable<TimeBill> GetAllTime(DateTime from, DateTime to)
{
    // Every search requires a valid session
    Login();

    Service.searchPreferences = new SearchPreferences
    {
        bodyFieldsOnly = false,
        returnSearchColumns = true
    };

    var search = new TimeBillSearch
    {
    	basic = new TimeBillSearchBasic
        {
        	dateCreated = new SearchDateField
            {
                @operator = SearchDateFieldOperator.within,
                operatorSpecified = true,
                searchValue = from,
                searchValueSpecified = true,
                searchValue2 = to,
                searchValue2Specified = true
            },
            type = new SearchEnumMultiSelectField
            {
                @operator = SearchEnumMultiSelectFieldOperator.anyOf,
                operatorSpecified = true,
                searchValue = new [] { "_actualTime" }
            }
        },
        employeeJoin = new EmployeeSearchBasic()
    };

    return Get<TimeBill>(search);
}
```

Each `TimeBill` should now have the `.employee` property populated, so now you can happily output their name rather than their ID. This Object is a `RecordRef` type - if you want the full `Employee` Object, you can call `Service.get(<RecordRef>)`, but be warned that this hits the API again.


### Custom Fields

You finally have the Search Results you want, but wait - your Time entries have a buisness rule that Netsuite doesn't support by default! In order to correctly run weekly reports, your friendly neighborhood Netsuite Admin has added in the `OvertimeHours` custom field as a checkbox and now you need to read out the value.

All custom values for a result are stored in the `customFieldList` property, but as previously noted, the field will only exist if a value is present. Using the `GetCustomField` extension method is a slightly easier way of grabbing these values. The name of the custom field can be found in Netsuite or by attaching a debugger and looking at a result's `customFieldList` (which is dynamically populated on runtime).

*Note 1: During testing, all custom fields in my system were prefixed with "custentity" in the name. If this is different in your system, modify the extension method or remove automatic prefixes entirely.*
*Note 2: It is recommended to wrap each call to this in a `try catch` just in case you have 0 custom fields (no part of my system has this test case, so I am unable to test.)*

```
try
{
	var isOvertime = aTimeBillObject.customFieldList
        .GetCustomField<BooleanCustomFieldRef>("OvertimeHours").value;
}
catch {}
```

## Wrap-Up

If you made it this far, congrats on your brand new Netsuite integration. The weather looks like it's going to clear up, and you can now go into work on Monday with good news for the Boss, well rested from a weekend spent doing things other than tearing out your hair.


<br/>
Questions? Comments? Suggestions? Leave them below!