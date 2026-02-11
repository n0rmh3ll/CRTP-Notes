
* Get a list of all domain trusts for the current domain
```
Get-DomainTrust
Get-DomainTrust -Domain us.dollarcorp.moneycorp.local
```

# Domain Enumeration - Forest

* Get details about the current forest
```
Get-Forest
Get-Forest -Forest eurocorp.local
Get-ADForest
Get-ADForest -Identity eurocorp.local
```

* Get all domains in the current forest
```
Get-ForestDomain
Get-ForestDomain -Forest eurocorp.local
```

* Get all global catalogs for the current forest
```
Get-ForestGlobalCatalog
Get-ForestGlobalCatalog -Forest eurocorp.local
```
