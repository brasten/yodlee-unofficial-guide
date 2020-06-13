# yodlee-unofficial-docs
A collection of information about Yodlee's APIs that are derived from experience, but not necessarily (well) documented.

If you have any additional confusing, unexpected or undocumented behaviors please submit an issue or PR.


## List of things to expand (YSL 1.1)

- `autoRefresh` on Account model
  - doesn't it make more sense on ProviderAccount?
  - will it actually be different between Accounts on a given ProviderAccount?
 
- `ProviderAccount.dataset[].lastUpdated` does not seem to be updated after a PARTIAL_SUCCESS. However, on 
  the ProviderAccount's Accounts, `dataset[].lastUpdated` IS updated. Intentional?


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
