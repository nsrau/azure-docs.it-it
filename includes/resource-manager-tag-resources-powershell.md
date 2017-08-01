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

Per visualizzare i tag esistenti per un **gruppo di risorse**, usare:

```powershell
(Get-AzureRmResourceGroup -Name examplegroup).Tags
```

Viene restituito il formato seguente:

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

Per visualizzare i tag esistenti per una **risorsa con un ID risorsa specificato**, usare:

```powershell
(Get-AzureRmResource -ResourceId {resource-id}).Tags
```

In alternativa, per visualizzare i tag esistenti per una **risorsa con un nome e un gruppo di risorse specificati**, usare:

```powershell
(Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Per ottenere i **gruppi di risorse con un tag specifico**, usare:

```powershell
(Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name 
```

Per ottenere le **risorse con un tag specifico**, usare:

```powershell
(Find-AzureRmResource -TagName Dept -TagValue Finance).Name
```

Ogni volta che si applicano tag a una risorsa o a un gruppo di risorse, si sovrascrivono i tag esistenti in tale risorsa o gruppo di risorse. È quindi necessario usare un approccio diverso se nella risorsa o nel gruppo di risorse esistono tag. 

Per aggiungere tag a un **gruppo di risorse senza tag esistenti**, usare:

```powershell
Set-AzureRmResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

Per aggiungere tag a un **gruppo di risorse con tag esistenti**, recuperare i tag esistenti, aggiungere il nuovo tag e riapplicare i tag:

```powershell
$tags = (Get-AzureRmResourceGroup -Name examplegroup).Tags
$tags += @{Status="Approved"}
Set-AzureRmResourceGroup -Tag $tags -Name examplegroup
```

Per aggiungere tag a una **risorsa senza tag esistenti**, usare:

```powershell
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName examplevnet -ResourceGroupName exampleroup
```

Per aggiungere tag a una **risorsa con tag esistenti**:

```powershell
$tags = (Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
$tags += @{Status="Approved"}
Set-AzureRmResource -Tag $tags -ResourceName examplevnet -ResourceGroupName examplegroup
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
Set-AzureRmResourceGroup -Tag @{} -Name examplegroup
```



