# yodlee-unofficial-docs
Questions and information about Yodlee's APIs that are not well documented (or hard to find).

If you have any additional confusing, unexpected or undocumented behaviors please submit an issue or PR.


## List of things to expand (YSL 1.1)

- `autoRefresh` on Account model
  - doesn't it make more sense on ProviderAccount?
  - will it actually be different between Accounts on a given ProviderAccount?
 
- `ProviderAccount.dataset[].lastUpdated` does not seem to be updated after a PARTIAL_SUCCESS. However, on 
  the ProviderAccount's Accounts, `dataset[].lastUpdated` IS updated. Intentional?


## A Note on Domain Language ##

Yodlee seems highly inconsistent with their domain language across their API and documents, which makes it
very difficult to understand at times. In an attempt to understand Yodlee's API better, we've settled on some
domain language for the purpose of this document that we believe can be used to describe Yodlee's behavior in
cases where Yodlee's own language is scattered.

### Customer / User ###

This one is fairly consistent in Yodlee's documentation but is a little hard to find:

- **CUSTOMER** -> the entity that pays Yodlee for access to the APIs. Probably you, actually.
- **USER** -> the CUSTOMER's customers. From Yodlee's perspective, it's mostly just part of the data model
  for keeping things organized. (As an aside, *Plaid* has no such concept in their aggregation API.)

### Refresh / Update ###

<pre>
┌─────────────┐            ┌────────┐           ┌──────────────┐
│ Institution │──REFRESH──▶│ Yodlee │──UPDATE──▶│ API Customer │
└─────────────┘            └────────┘           └──────────────┘
</pre>

A **REFRESH** is this document's term for the `Institution -> Yodlee` interaction. During a refresh, Yodlee retrieves new information from an Institution on behalf of a customer/user. There are two sub-types of refresh:

  - An **AUTO-REFRESH** is a refresh initiated by Yodlee of their own volition by schedule or other means. Yodlee
    refers to this as auto refresh, cache refresh, etc.
  - An **INTERACTIVE REFRESH** is a refresh initiated by a CUSTOMER on behalf of a USER. The user is presumed to be
    presently interacting with the customers' product. There are two ways to initiate an Interactive Refresh:
      - **API-BASED** interactive refreshes are triggered by the customer using the `PUT /providerAccounts` API.
        *It is expected that the customer's site will present in-progress information to the user, and has implemented
        a custom UX for presenting MFA challenges to the user.<sup>(NOTE: Move this last sentence to an implementation 
        section at some point, it's not really a part of the definition itself.)</sup>*
      - **WIDGET-BASED** interactive refreshes are driven by Yodlee's Fastlink UX exclusively.

An **UPDATE** is this document's term for the `Yodlee -> API Customer` interaction. During an update, the customer
retrieves new information from Yodlee. The most obvious form of this is pulling new data via the relevant APIs 
(accounts, transactions, etc). But this also refers to the process utilizing the Data Extract API.
  - Possible source of confusion: Yodlee seems to be using the term "Update" to refer to Refresh in some cases.
    The most confusing instance of this seems to be the renaming of some fields<sup>(1)</sup> between the YSL 1.0 and YSL 1.1
    API versions (listed below).
    
    *Open Question: Does this rename signify something important?? Because the original names *seem* more
    accurate.*
    
    - `nextRefreshScheduled` -> `nextUpdateScheduled`
    - `lastRefreshed` -> `lastUpdated`
    - `lastRefreshAttempt` -> `lastUpdateAttempt`


## Hints to Intentions ##

Yodlee's documentation tends to lack detailed explanation of how a given data model is intended to be used.

As such, here is a collection of hints for various parts of their API. Ideally each of these is given its own
section with detailed explanations once those are available or figured out.

- `ProviderAccount.dataset[]` vs `Account(s).dataset[]`: are they the same? Experience shows they are NOT, which came as a surprise.

  - *"Previously, to detect the success of the aggregation process, you checked statusCode at the account-level. In Yodlee Core API v1.1 you have to check the dataset.additionalStatus at the account-level."*<sup>(1)</sup>  Why "account-level?"
  - *"The account.updateEligibility field provided in the GET accounts endpoint can be used to display the refresh or edit credentials options."*<sup>(1)</sup> `account.updateEligibility` isn't even a thing, but assuming they mean account-level dataset.updateEligibility.


## Interactive Refresh / Account Status ##

### Fields That Help Display the Refresh and Edit Credentials Options
<sup>... from https://developer.yodlee.com/Yodlee_API/docs/v1_1/Migrating_Yodlee_API_1_0_To_1_1_New</sup>

> The account.updateEligibility field provided in the GET accounts endpoint can be used to display the refresh 
> or edit credentials options.
>
> The refresh or retrieve the latest updates option can be displayed to your users when the value is 
> account.updateEligibility = ALLOW_UPDATE.
>
> The edit credentials option can be displayed when the value is account.updateEligibility = ALLOW_UPDATE_WITH_CREDENTIALS.
>
> Note: When the refresh is not allowed due to incorrect or expired credentials, the edit credential option should 
> be provided in the response.


## Auto Refresh ##

### Enabled/Disabled ###

There are a number of places in the YSL API that provide hints as to the AutoRefresh status for a given account. 
They don't always seem to agree and it's not entirely obvious which settings take precedence. One is writeable, but 
it's unclear if it can override more restrictive settings or not (can you enable autoRefresh for an account when 
the associated Provider has it's autoRefresh DISABLED?).

- `ProviderAccount.preferences.isAutoRefreshEnabled` is a settable preference. Does it override all other information?
- `Account.autoRefresh.status` -> shouldn't this be on the `ProviderAccount`?
- `ProviderAccount.dataset[].nextUpdateScheduled` and `Account.dataset[].nextUpdateScheduled`
  - are these two different values or the same value returned in two different locations?
- `Provider.isAutoRefreshEnabled`


Footnotes
1. https://developer.yodlee.com/Yodlee_API/docs/v1_1/Migrating_Yodlee_API_1_0_To_1_1_New

