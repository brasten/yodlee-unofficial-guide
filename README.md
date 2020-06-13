# yodlee-unofficial-docs
A collection of information about Yodlee's APIs that are derived from experience, but not necessarily (well) documented.

If you have any additional confusing, unexpected or undocumented behaviors please submit an issue or PR.


## List of things to expand (YSL 1.1)

- `autoRefresh` on Account model
  - doesn't it make more sense on ProviderAccount?
  - will it actually be different between Accounts on a given ProviderAccount?
 
- `ProviderAccount.dataset[].lastUpdated` does not seem to be updated after a PARTIAL_SUCCESS. However, on the ProviderAccount's Accounts, `dataset[].lastUpdated` IS updated. Intentional?

- 
