### Modifiche del cmdlet Tag nella versione più recente di PowerShell

La versione di agosto 2016 di [Azure PowerShell 2.0][powershell] include alcune importanti modifiche nell'uso dei tag. Prima di continuare, controllare la versione del modulo AzureRm.Resources.

    Get-Module -ListAvailable -Name AzureRm.Resources | Select Version

Se l'ultimo aggiornamento di Azure PowerShell è precedente al mese di agosto 2016, i risultati dovrebbero mostrare una versione inferiore alla 3.0.

    Version
    -------
    2.0.2

Se l'ultimo aggiornamento di Azure PowerShell è successivo al mese di agosto 2016, i risultati dovrebbero mostrare una versione superiore alla 3.0.

    Version
    -------
    3.0.1
    
Se la versione del modulo è la 3.0.1 o successiva, si dispone dei cmdlet più recenti per l'utilizzo dei tag. Questa versione del modulo Risorse di Azure viene installata automaticamente durante l'installazione o l'aggiornamento di Azure PowerShell tramite PowerShell Gallery, PowerShellGet o Installazione guidata piattaforma Web. Se la versione è precedente alla 3.0.1, è possibile continuare a utilizzarla, ma si potrebbe considerare la possibilità di effettuare l'aggiornamento alla versione più recente. La versione più recente include una serie di modifiche che semplificano l'utilizzo dei tag. Entrambe gli approcci sono illustrati in questo argomento.

### Aggiornare lo script per le modifiche nella versione più recente 

Nella versione più recente, il nome del parametro **Tag** è stato modificato in **Tag** e il tipo è cambiato da **Hashtable** a **Hashtable**. Non è più necessario fornire il **nome** e il **valore** per ciascuna voce di inserimento. Anzi, l'utente deve fornire le associazioni chiave-valore nel formato **Key = "Value"**.

Per aggiornare lo script esistente, modificare il parametro **Tag** in **Tag** e modificare il formato di tag come illustrato nell'esempio seguente.

    # Old
    New-AzureRmResourceGroup -Tags @{ Name = "testtag"; Value = "testval" } -Name $resourceGroupName -Location $location

    # New
    New-AzureRmResourceGroup -Tag @{ testtag = "testval" } -Name $resourceGroupName -Location $location 

Tuttavia, si noti che i gruppi di risorse e le risorse restituiscono ancora una proprietà **Tag** nei rispettivi metadati. Questa proprietà non è cambiata.

### Versione 3.0.1 o successiva

I tag risultano direttamente sulle risorse e sui gruppi di risorse. Per visualizzare i tag esistenti, visualizzare una risorsa tramite **Get-AzureRmResource** o un gruppo di risorse con **Get-AzureRmResourceGroup**.

Iniziare con un gruppo di risorse.

    Get-AzureRmResourceGroup -Name testrg1

Questo cmdlet restituisce diversi bit di metadati sul gruppo di risorse, inclusi i tag che sono stati applicati, se presenti.

    ResourceGroupName : testrg1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
                    Name         Value
                    ===========  ==========
                    Dept         Finance
                    Environment  Production

Per recuperare i metadati delle risorse, inclusi i tag, utilizzare l'esempio seguente.

    Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName testrg1

I nomi dei tag sono visibili nei risultati.

    Name              : tfsqlserver
    ResourceId        : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    ResourceName      : tfsqlserver
    ResourceType      : Microsoft.Sql/servers
    Kind              : v12.0
    ResourceGroupName : testrg1
    Location          : westus
    SubscriptionId    : {guid}
    Tags              : {Dept, Environment}

Utilizzare la proprietà **Tag** per ottenere i valori e i nomi dei tag.

    (Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName testrg1).Tags

Così facendo vengono restituiti i seguenti risultati:

    Name                   Value
    ----                   -----
    Dept                   Finance
    Environment            Production

Invece di visualizzare i tag per un determinato gruppo di risorse o una risorsa, è spesso necessario recuperare tutte le risorse o i gruppi di risorse con un tag e un valore specifici. Per recuperare i gruppi di risorse che presentano un tag specifico, usare il cmdlet **Find-AzureRmResourceGroup** con il parametro **-Tag**.

Per recuperare i gruppi di risorse con un valore di tag, utilizzare il formato seguente.

    (Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name 

Per recuperare tutte le risorse con un tag e un valore specifico, usare il cmdlet **Find-AzureRmResource**.

    (Find-AzureRmResource -TagName Dept -TagValue Finance).Name
    
Per aggiungere un tag a un gruppo di risorse senza tag, usare il comando **Set-AzureRmResourceGroup** e specificare un oggetto tag.

    Set-AzureRmResourceGroup -Name test-group -Tag @{ Dept="IT"; Environment="Test" }

Verrà restituito il gruppo di risorse con i nuovi valori di tag.

    ResourceGroupName : test-group
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name          Value
                    =======       =====
                    Dept          IT
                    Environment   Test
                    
È possibile aggiungere tag a una risorsa senza tag usando il comando **Set-AzureRmResource**.

    Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId /subscriptions/{guid}/resourceGroups/test-group/providers/Microsoft.Web/sites/examplemobileapp

I tag vengono aggiornati nel loro complesso. Per aggiungere un tag a una risorsa che dispone di altri tag, utilizzare una matrice con tutti i tag che si desidera conservare. In primo luogo, selezionare i tag esistenti, aggiungerne uno al set e riapplicarli tutti.

    $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    $tags += @{Status="approved"}
    Set-AzureRmResourceGroup -Name test-group -Tag $tags

Se si vuole rimuovere uno o più tag, è sufficiente salvare la matrice senza i tag da rimuovere.

Il processo è lo stesso per le risorse, ad eccezione del fatto che si useranno i cmdlet **Get-AzureRmResource** e **Set-AzureRmResource**.

Per ottenere un elenco di tutti i tag all'interno di una sottoscrizione tramite PowerShell, usare il cmdlet **Get-AzureRmTag**.

    Get-AzureRmTag
    
In questo modo vengono restituiti i nomi dei tag e il conteggio del numero di risorse e gruppi di risorse con il tag

    Name                      Count
    ----                      ------
    Dept                       8
    Environment                8

È possibile che vengano visualizzati tag che iniziano con "hidden-" e "link:". Si tratta di tag interni, che dovrebbero essere ignorati e comunque non devono modificati.

Usare il cmdlet **New-AzureRmTag** per aggiungere nuovi tag alla tassonomia. Questi tag vengono inclusi nel completamento automatico anche se non sono stati ancora applicati a risorse o gruppi di risorse. Per rimuovere una coppia nome-valore di un tag, rimuovere prima il tag dalle eventuali risorse che ne fanno uso, quindi usare il cmdlet **Remove-AzureRmTag** per rimuoverlo dalla tassonomia.

### Versioni precedenti alla 3.0.1

I tag risultano direttamente sulle risorse e sui gruppi di risorse. Per visualizzare i tag esistenti, visualizzare una risorsa tramite **Get-AzureRmResource** o un gruppo di risorse con **Get-AzureRmResourceGroup**.

Iniziare con un gruppo di risorse.

    Get-AzureRmResourceGroup -Name testrg1

Questo cmdlet restituisce diversi bit di metadati sul gruppo di risorse, inclusi i tag che sono stati applicati, se presenti.

    ResourceGroupName : testrg1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
                    Name         Value
                    ===========  ==========
                    Dept         Finance
                    Environment  Production
                    
Per recuperare i metadati delle risorse, utilizzare l'esempio seguente. I metadati delle risorse non mostrano direttamente i tag.

    Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName testrg1

Nei risultati è possibile notare che i tag vengono visualizzati solo come oggetto Hashtable.

    Name              : tfsqlserver
    ResourceId        : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    ResourceName      : tfsqlserver
    ResourceType      : Microsoft.Sql/servers
    Kind              : v12.0
    ResourceGroupName : tag-demo-group
    Location          : westus
    SubscriptionId    : {guid}
    Tags              : {System.Collections.Hashtable}

È possibile visualizzare i tag effettivi recuperando la proprietà **Tag**.

    (Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName tag-demo-group).Tags | %{ $_.Name + ": " + $_.Value }
   
Verranno restituiti i risultati formattati:
    
    Dept: Finance
    Environment: Production
    
Invece di visualizzare i tag per un determinato gruppo di risorse o una risorsa, è spesso necessario recuperare tutte le risorse o i gruppi di risorse con un tag e un valore specifici. Per recuperare i gruppi di risorse che presentano un tag specifico, usare il cmdlet **Find-AzureRmResourceGroup** con il parametro **-Tag**.

Per recuperare i gruppi di risorse con un valore di tag, utilizzare il formato seguente.

    Find-AzureRmResourceGroup -Tag @{ Name="Dept"; Value="Finance" } | %{ $_.Name }
    
Per recuperare tutte le risorse con un tag e un valore specifico, usare il cmdlet Find-AzureRmResource.

    Find-AzureRmResource -TagName Dept -TagValue Finance | %{ $_.ResourceName }

Per aggiungere un tag a un gruppo di risorse senza tag, usare semplicemente il comando Set-AzureRmResourceGroup e specificare un oggetto tag.

    Set-AzureRmResourceGroup -Name test-group -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} )
    
Verrà restituito il gruppo di risorse con i nuovi valori di tag.

    ResourceGroupName : test-group
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                Name          Value
                =======       =====
                Dept          IT
                Environment   Test

È possibile aggiungere tag a una risorsa senza tag usando il comando Set-AzureRmResource.

    Set-AzureRmResource -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} ) -ResourceId /subscriptions/{guid}/resourceGroups/test-group/providers/Microsoft.Web/sites/examplemobileapp

I tag vengono aggiornati nel loro complesso. Per aggiungere un tag a una risorsa che dispone di altri tag, utilizzare una matrice con tutti i tag che si desidera conservare. In primo luogo, selezionare i tag esistenti, aggiungerne uno al set e riapplicarli tutti.

    $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    $tags += @{Name="status";Value="approved"}
    Set-AzureRmResourceGroup -Name test-group -Tag $tags

Se si vuole rimuovere uno o più tag, è sufficiente salvare la matrice senza i tag da rimuovere.

Il processo è lo stesso per le risorse, ad eccezione del fatto che si useranno i cmdlet Get-AzureRmResource e Set-AzureRmResource.

Per ottenere un elenco di tutti i tag all'interno di una sottoscrizione tramite PowerShell, usare il cmdlet **Get-AzureRmTag**.

    Get-AzureRmTag
    
In questo modo vengono restituiti i nomi dei tag e il conteggio del numero di risorse e gruppi di risorse con il tag

    Name                      Count
    ----                      ------
    Dept                       8
    Environment                8

È possibile che vengano visualizzati tag che iniziano con "hidden-" e "link:". Si tratta di tag interni, che dovrebbero essere ignorati e comunque non devono modificati.

Usare il cmdlet **New-AzureRmTag** per aggiungere nuovi tag alla tassonomia. Questi tag vengono inclusi nel completamento automatico anche se non sono stati ancora applicati a risorse o gruppi di risorse. Per rimuovere una coppia nome-valore di un tag, rimuovere prima il tag dalle eventuali risorse che ne fanno uso, quindi usare il cmdlet **Remove-AzureRmTag** per rimuoverlo dalla tassonomia.


[powershell]: https://msdn.microsoft.com/library/mt619274(v=azure.200).aspx

<!---HONumber=AcomDC_0907_2016-->