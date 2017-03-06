La versione 3.0 del modulo AzureRm.Resources include modifiche significative all'uso dei tag. Prima di continuare, controllare la versione:

```powershell
Get-Module -ListAvailable -Name AzureRm.Resources | Select Version
```

Se i risultati indicano la versione 3.0 o successiva, gli esempi di questo argomento sono adatti all'ambiente in uso. Se non è disponibile la versione 3.0 o successiva, [aggiornare la versione](/powershell/azureps-cmdlets-docs/) usando PowerShell Gallery o l'Installazione guidata piattaforma Web prima di continuare con questo argomento.

```powershell
Version
-------
3.5.0
```

Ogni volta che si applicano tag a una risorsa o a un gruppo di risorse, si sovrascrivono i tag esistenti in tale risorsa o gruppo di risorse. È quindi necessario usare un approccio diverso se nella risorsa o nel gruppo di risorse esistono tag che si vuole mantenere o meno. Per aggiungere a:

* un gruppo di risorse senza tag esistenti.

  ```powershell
  Set-AzureRmResourceGroup -Name TagTestGroup -Tag @{ Dept="IT"; Environment="Test" }
  ```

* un gruppo di risorse con tag esistenti.

  ```powershell
  $tags = (Get-AzureRmResourceGroup -Name TagTestGroup).Tags
  $tags += @{Status="Approved"}
  Set-AzureRmResourceGroup -Tag $tags -Name TagTestGroup
  ```

* una risorsa senza tag esistenti.

  ```powershell
  Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName storageexample -ResourceGroupName TagTestGroup -ResourceType Microsoft.Storage/storageAccounts
  ```

* una risorsa con tag esistenti.

  ```powershell
  $tags = (Get-AzureRmResource -ResourceName storageexample -ResourceGroupName TagTestGroup).Tags
  $tags += @{Status="Approved"}
  Set-AzureRmResource -Tag $tags -ResourceName storageexample -ResourceGroupName TagTestGroup -ResourceType Microsoft.Storage/storageAccounts
  ```

Per applicare tutti i tag da un gruppo di risorse alle risorse **senza mantenere i tag esistenti nelle risorse**, usare lo script seguente:

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups) 
{
    Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force } 
}
```

Per applicare tutti i tag da un gruppo di risorse alle risorse **mantenendo i tag esistenti nelle risorse non duplicate**, usare lo script seguente:

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups) 
{
    if ($g.Tags -ne $null) {
        $resources = Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName 
        foreach ($r in $resources)
        {
            $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
            foreach ($key in $g.Tags.Keys)
            {
                if ($resourcetags.ContainsKey($key)) { $resourcetags.Remove($key) }
            }
            $resourcetags += $g.Tags
            Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Per rimuovere tutti i tag, passare una tabella hash vuota.

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name TagTestGgroup
```

Per ottenere i gruppi di risorse con un tag specifico, usare il cmdlet `Find-AzureRmResourceGroup`.

```powershell
(Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name 
```

Per ottenere tutte le risorse con un tag e un valore specifici, usare il cmdlet `Find-AzureRmResource`.

```powershell
(Find-AzureRmResource -TagName Dept -TagValue Finance).Name
```

