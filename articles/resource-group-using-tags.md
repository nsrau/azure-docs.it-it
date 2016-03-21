<properties
	pageTitle="Uso dei tag per organizzare le risorse di Azure | Microsoft Azure"
	description="Mostra come applicare i tag per organizzare le risorse per la fatturazione e la gestione."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="AzurePortal"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="tomfitz"/>


# Uso dei tag per organizzare le risorse di Azure

La Gestione risorse consente di organizzare logicamente le risorse tramite l'applicazione di tag. I tag sono costituiti da coppie chiave/valore che identificano le risorse con le proprietà definite. Per contrassegnare le risorse come appartenenti alla stessa categoria, applicare lo stesso tag a tali risorse.

Quando si visualizzano le risorse con un tag specifico, vengono visualizzate le risorse da tutti i gruppi di risorse. Non si è limitati unicamente alle risorse che appartengono allo stesso gruppo di risorse, cosa che consente di organizzare le risorse in modo indipendente rispetto alle relazioni di distribuzione. I tag possono essere particolarmente utili quando è necessario organizzare le risorse per la fatturazione o la gestione.

Ogni tag aggiunto a una risorsa o a un gruppo di risorse viene aggiunto automaticamente alla tassonomia a livello di sottoscrizione. È anche possibile precompilare tale tassonomia con nomi e valori di tag che si vogliono usare in futuro come risorse provviste di tag.

Ciascuna risorsa o gruppo di risorse può avere un massimo di 15 tag. Il nome del tag è limitato a 512 caratteri e il valore del tag è limitato a 256 caratteri.

> [AZURE.NOTE] È possibile applicare dei tag solo alle risorse che supportano le operazioni di gestione delle risorse. Se è stata creata una macchina virtuale, una rete virtuale o un'archiviazione tramite il modello di distribuzione classica, ad esempio tramite il portale di Azure o l'API di gestione del servizio, non è possibile applicare un tag a tale risorsa. È necessario distribuire nuovamente tali risorse tramite Gestione risorse per supportare l'assegnazione di tag. Tutte le altre risorse supportano l'assegnazione di tag.

## Tag nei modelli

Per applicare un tag a una risorsa durante la distribuzione, è sufficiente aggiungere l'elemento **tags** alla risorsa che si intende distribuire e specificare il nome e il valore del tag. Non è necessario che il nome e il valore del tag esistano già nella sottoscrizione. È possibile specificare fino a 15 tag per ogni risorsa.

L'esempio seguente illustra un account di archiviazione con un tag.

    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2015-06-15",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "tags": {
                "dept": "Finance"
            },
            "properties": 
            {
                "accountType": "Standard_LRS"
            }
        }
    ]

Attualmente Gestione risorse non supporta l'elaborazione di un oggetto per i nomi e i valori del tag. In alternativa, è possibile passare un oggetto per i valori di tag, ma è comunque necessario specificare i nomi dei tag, come illustrato di seguito.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "tagvalues": {
          "type": "object",
          "defaultValue": {
            "dept": "Finance",
            "project": "Test"
          }
        }
      },
      "resources": [
      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "examplestorage",
        "tags": {
          "dept": "[parameters('tagvalues').dept]",
          "project": "[parameters('tagvalues').project]"
        },
        "location": "[resourceGroup().location]",
        "properties": {
          "accountType": "Standard_LRS"
        }
      }]
    }


## Tag nel portale

È possibile aggiungere tag a risorse e gruppi di risorse esistenti tramite il portale. Usare l'hub Sfoglia per passare alla risorsa o al gruppo di risorse a cui aggiungere i tag e fare clic sull'area Tag nella sezione Panoramica nella parte superiore del pannello.

![Parte relativa ai tag nella risorsa e nei pannelli relativi ai gruppi di risorse](./media/resource-group-using-tags/tag-icon.png)

Verrà aperto un pannello con l'elenco dei tag che sono già stati applicati. Se questo è il primo tag, l'elenco sarà vuoto. Per aggiungere un tag, specificare un nome e un valore, quindi premere INVIO. Dopo aver aggiunto alcuni tag, si noteranno alcune opzioni di completamento automatico basate sui nomi e sui valori dei tag preesistenti. Questi consentono di garantire una tassonomia coerente tra le risorse e di evitare errori comuni, ad esempio errori di ortografia.

![Assegnare tag a risorse con coppie nome-valore](./media/resource-group-using-tags/tag-resources.png)

Per visualizzare la tassonomia dei tag nel portale, usare l'hub Sfoglia per visualizzare tutti gli elementi e quindi selezionare la voce Tag.

![Trovare tag tramite l'hub di esplorazione](./media/resource-group-using-tags/browse-tags.png)

Aggiungere i tag più importanti alla propria schermata iniziale per accedervi velocemente. A questo punto si è pronti per iniziare. Buon divertimento!

![Aggiungere tag alla Schermata iniziale  
](./media/resource-group-using-tags/pin-tags.png)

## Tag e PowerShell

I tag sono presenti direttamente nelle risorse e nei gruppi di risorse, quindi, per vedere quali tag sono già stati applicati, è possibile recuperare una risorsa o un gruppo di risorse rispettivamente con **Get-AzureRmResource** o **Get-AzureRmResourceGroup**. Iniziare con un gruppo di risorse.

    PS C:\> Get-AzureRmResourceGroup -Name tag-demo-group

    ResourceGroupName : tag-demo-group
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
                    Name         Value
                    ===========  ==========
                    Dept         Finance
                    Environment  Production


Questo cmdlet restituisce diversi bit di metadati sul gruppo di risorse, inclusi i tag che sono stati applicati, se presenti.

Durante il recupero dei metadati per una risorsa, i tag non vengono visualizzati direttamente. Di seguito è possibile notare che i tag vengono visualizzati solo come oggetto Hashtable.

    PS C:\> Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName tag-demo-group

    Name              : tfsqlserver
    ResourceId        : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    ResourceName      : tfsqlserver
    ResourceType      : Microsoft.Sql/servers
    Kind              : v12.0
    ResourceGroupName : tag-demo-group
    Location          : westus
    SubscriptionId    : {guid}
    Tags              : {System.Collections.Hashtable}

È possibile visualizzare i tag effettivi recuperando la proprietà **tag**.

    PS C:\> (Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName tag-demo-group).Tags | %{ $_.Name + ": " + $_.Value }
    Dept: Finance
    Environment: Production

Invece di visualizzare i tag per un determinato gruppo di risorse o una risorsa, è spesso necessario recuperare tutte le risorse o i gruppi di risorse che dispongono di un tag e di un valore specifico. Per recuperare i gruppi di risorse che presentano un tag specifico, usare il cmdlet **Find-AzureRmResourceGroup** con il parametro **-Tag**.

    PS C:\> Find-AzureRmResourceGroup -Tag @{ Name="Dept"; Value="Finance" } | %{ $_.Name }
    tag-demo-group
    web-demo-group

Per recuperare tutte le risorse con un tag e un valore specifico, usare il cmdlet **Find-AzureRmResource**.

    PS C:\> Find-AzureRmResource -TagName Dept -TagValue Finance | %{ $_.ResourceName }
    tfsqlserver
    tfsqldatabase

Per aggiungere un tag a un gruppo di risorse senza tag, usare semplicemente il comando **Set-AzureRmResourceGroup** e specificare un oggetto tag.

    PS C:\> Set-AzureRmResourceGroup -Name test-group -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} )

    ResourceGroupName : test-group
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name          Value
                    =======       =====
                    Dept          IT
                    Environment   Test
                    
È possibile aggiungere tag a una risorsa senza tag tramite il comando **SetAzureRmResource**.

    PS C:\> Set-AzureRmResource -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} ) -ResourceId /subscriptions/{guid}/resourceGroups/test-group/providers/Microsoft.Web/sites/examplemobileapp

I tag vengono aggiornati nel loro complesso, quindi se si aggiunge un tag a una risorsa che è già stata provvista di tag, è necessario usare una matrice con tutti i tag che si vogliono mantenere. A tale scopo, è innanzitutto possibile selezionare i tag esistenti, aggiungerne uno nuovo a tale set e riapplicare tutti i tag.

    PS C:\> $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    PS C:\> $tags += @{Name="status";Value="approved"}
    PS C:\> Set-AzureRmResourceGroup -Name test-group -Tag $tags

Se si vuole rimuovere uno o più tag, è sufficiente salvare la matrice senza i tag da rimuovere.

Il processo è lo stesso per le risorse, ad eccezione del fatto che si useranno i cmdlet **Get-AzureRmResource** e **Set-AzureRmResource**.

Per ottenere un elenco di tutti i tag all'interno di una sottoscrizione tramite PowerShell, usare il cmdlet **Get-AzureRmTag**.

    PS C:/> Get-AzureRmTag
    Name                      Count
    ----                      ------
    env                       8
    project                   1

È possibile che vengano visualizzati tag che iniziano con "hidden-" e "link:". Si tratta di tag interni, che dovrebbero essere ignorati e comunque non devono modificati.

Usare il cmdlet **New-AzureRmTag** per aggiungere nuovi tag alla tassonomia. Questi tag verranno inclusi nel completamento automatico anche se non sono stati ancora applicati a risorse o gruppi di risorse. Per rimuovere una coppia nome-valore di un tag, rimuovere prima il tag dalle eventuali risorse che ne fanno uso, quindi usare il cmdlet **Remove-AzureRmTag** per rimuoverlo dalla tassonomia.

## Tag e interfaccia della riga di comando di Azure

I tag sono presenti direttamente nelle risorse e nei gruppi di risorse, quindi, per vedere quali tag sono già stati applicati, è possibile recuperare un gruppo di risorse e le relative risorse con **azure group show**.

    azure group show -n tag-demo-group
    info:    Executing command group show
    + Listing resource groups
    + Listing resources for the group
    data:    Id:                  /subscriptions/{guid}/resourceGroups/tag-demo-group
    data:    Name:                tag-demo-group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: Dept=Finance;Environment=Production
    data:    Resources:
    data:
    data:      Id      : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    data:      Name    : tfsqlserver
    data:      Type    : servers
    data:      Location: eastus2
    data:      Tags    : Dept=Finance;Environment=Production
    ...

Per ottenere i tag solo per il gruppo di risorse, usare un'utilità JSON come [jq](http://stedolan.github.io/jq/download/).

    azure group show -n tag-demo-group --json | jq ".tags"
    {
      "Dept": "Finance",
      "Environment": "Production" 
    }

È possibile visualizzare i tag per una particolare risorsa tramite **azure resource show**.

    azure resource show -g tag-demo-group -n tfsqlserver -r Microsoft.Sql/servers -o 2014-04-01-preview --json | jq ".tags"
    {
      "Dept": "Finance",
      "Environment": "Production"
    }
    
È possibile recuperare tutte le risorse con un tag e un valore specifico come illustrato di seguito.

    azure resource list --json | jq ".[] | select(.tags.Dept == "Finance") | .name"
    "tfsqlserver"
    "tfsqlserver/tfsqldata"

I tag vengono aggiornati nel loro complesso, quindi se si aggiunge un tag a una risorsa che è già stata provvista di tag, sarà necessario recuperare tutti i tag esistenti che si vuole mantenere. Per impostare i valori dei tag per un gruppo di risorse, usare **azure group set** e fornire tutti i tag per il gruppo di risorse.

    azure group set -n tag-demo-group -t Dept=Finance;Environment=Production;Project=Upgrade
    info:    Executing command group set
    ...
    data:    Name:                tag-demo-group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: Dept=Finance;Environment=Production;Project=Upgrade
    ...
    
È possibile elencare i tag esistenti nella sottoscrizione con **azure tag list** e aggiungere un nuovo tag con **azure tag create**. Per rimuovere un tag dalla tassonomia della sottoscrizione, rimuovere innanzitutto il tag dalle eventuali risorse con cui può essere usato, quindi rimuoverlo con **azure tag delete**.

## Tag e API REST

Sia il portale che PowerShell usano l'[API REST di Gestione risorse](https://msdn.microsoft.com/library/azure/dn848368.aspx) dietro le quinte. Se è necessario integrare l'assegnazione di tag in un altro ambiente, è possibile ottenere i tag con un'operazione GET sull'ID di risorsa e aggiornare il set di tag con una chiamata PATCH.


## Tag e fatturazione

Per i servizi supportati, è possibile utilizzare i tag per raggruppare i dati di fatturazione. Ad esempio, [Macchine virtuali integrato con Gestione risorse di Azure](./virtual-machines/virtual-machines-azurerm-versus-azuresm.md) consente di definire e applicare i tag per organizzare l'utilizzo della fatturazione per le macchine virtuali. Se si eseguono più macchine virtuali per organizzazioni diverse, è possibile utilizzare i tag per raggruppare l'utilizzo in base al centro di costo. È inoltre possibile utilizzare i tag per classificare i costi in base all'ambiente di runtime; ad esempio, l'utilizzo di fatturazione per le macchine virtuali in esecuzione nell'ambiente di produzione.

È possibile recuperare le informazioni sui tag tramite l'[API di utilizzo della risorsa di Azure e della Rate Card](billing-usage-rate-card-overview.md) o il file con valori delimitati da virgole (CSV) di utilizzo che è possibile scaricare dal [portale degli account di Azure](https://account.windowsazure.com/) o dal [portale EA](https://ea.azure.com). Per altre informazioni sull'accesso a livello di codice alle informazioni sulla fatturazione, vedere [Ottenere informazioni dettagliate sul consumo delle risorse di Microsoft Azure](billing-usage-rate-card-overview.md). Per le operazioni API REST, vedere [Riferimento API REST alla fatturazione di Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Quando si scarica il CSV di utilizzo per i servizi che supportano i tag di fatturazione, i tag vengono visualizzati nella colonna **Tag**. Per ulteriori informazioni, vedere [Informazioni sulla fattura di Microsoft Azure](billing-understand-your-bill.md).

![Vedere i tag della fatturazione](./media/resource-group-using-tags/billing_csv.png)

## Passaggi successivi

- È possibile applicare restrizioni e convenzioni all’interno della sottoscrizione con criteri personalizzati. Il criterio definito potrebbe richiedere che un determinato tag sia impostato per tutte le risorse. Per altre informazioni, vedere [Usare i criteri per gestire le risorse e controllare l'accesso](resource-manager-policy.md).
- Per un'introduzione all'uso di Azure PowerShell durante la distribuzione delle risorse, vedere [Uso di Azure PowerShell con Gestione risorse di Azure](./powershell-azure-resource-manager.md).
- Per un'introduzione all'uso dell'interfaccia della riga di comando di Azure durante la distribuzione delle risorse, vedere [Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione risorse di Azure](./xplat-cli-azure-resource-manager.md).
- Per un'introduzione all'uso del portale, vedere [Uso del portale di Azure per gestire le risorse di Azure](./azure-portal/resource-group-portal.md)  

<!---HONumber=AcomDC_0309_2016-->