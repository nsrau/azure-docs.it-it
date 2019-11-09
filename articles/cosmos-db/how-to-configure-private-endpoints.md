---
title: Configurare il collegamento privato di Azure per un account Azure Cosmos
description: Informazioni su come configurare il collegamento privato di Azure per accedere a un account Azure Cosmos usando un indirizzo IP privato in una rete virtuale.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: 3f987b9e05bcdcda9afe26a1eb1354e5e2450ac5
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73846526"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account-preview"></a>Configurare il collegamento privato di Azure per un account Azure Cosmos (anteprima)

Con il collegamento privato di Azure è possibile connettersi a un account Azure Cosmos tramite un endpoint privato. L'endpoint privato è un set di indirizzi IP privati in una subnet all'interno della rete virtuale. Usando il collegamento privato, è possibile limitare l'accesso a un determinato account Azure Cosmos tramite indirizzi IP privati. In combinazione con i criteri NSG limitati, il collegamento privato consente di ridurre il rischio di exfiltration dei dati. Per altre informazioni sugli endpoint privati, vedere l'articolo relativo al [collegamento privato di Azure](../private-link/private-link-overview.md) .

Il collegamento privato consente inoltre di accedere a un account Azure Cosmos dall'interno della rete virtuale o da qualsiasi rete virtuale con peering. Le risorse mappate al collegamento privato sono accessibili anche da locale tramite peering privato tramite VPN o ExpressRoute. 

È possibile connettersi a un account Azure Cosmos configurato con un collegamento privato usando i metodi di approvazione "automatici" o "manuali". Per ulteriori informazioni, vedere la sezione relativa al [flusso di lavoro di approvazione](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) della documentazione del collegamento privato. Questo articolo descrive i passaggi per creare un collegamento privato presumendo che si stia usando il metodo di approvazione automatica.

## <a name="create-a-private-link-using-the-azure-portal"></a>Creare un collegamento privato usando il portale di Azure

Usare la procedura seguente per creare un collegamento privato per un account Azure Cosmos esistente usando portale di Azure:

1. Dal riquadro **tutte le risorse** individuare l'account Azure Cosmos DB che si desidera proteggere.

1. Selezionare **connessione endpoint privato** dal menu impostazioni e selezionare l'opzione **endpoint privato** :

   ![Creare un endpoint privato usando portale di Azure](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. In **creare un endpoint privato (anteprima)-nozioni di base**, riquadro Immettere o selezionare i dettagli seguenti:

    | Impostazione | Valore |
    | ------- | ----- |
    | **Dettagli del progetto** | |
    | Sottoscrizione | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare un gruppo di risorse.|
    | **Dettagli istanza** |  |
    | Name | Immettere un nome per l'endpoint privato. Se il nome viene creato, crearne uno univoco. |
    |Region| Selezionare l'area in cui si vuole distribuire il collegamento privato. L'endpoint privato deve essere creato nella stessa posizione in cui risiede la rete virtuale.|
    |||
1. Selezionare **Avanti: risorsa**.
1. In **creare una risorsa endpoint privato**immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    |Connection Method (Metodo di connessione)  | Selezionare Connettersi a una risorsa di Azure nella directory. <br/><br/> Questa opzione consente di scegliere una delle risorse per configurare un collegamento privato o connettersi alla risorsa di un altro utente con un ID di risorsa o un alias condiviso con l'utente.|
    | Sottoscrizione| Selezionare la propria sottoscrizione. |
    | Tipo di risorsa | Selezionare **Microsoft. AzureCosmosDB/databaseAccounts**. |
    | Risorsa |Selezionare l'account Azure Cosmos |
    |Sottorisorsa di destinazione |Selezionare il tipo di API di Cosmos DB di cui si desidera eseguire il mapping. Il valore predefinito è solo una delle opzioni per le API SQL, MongoDB e Cassandra. Per le API Gremlin e Table, è anche possibile scegliere *SQL* perché queste API sono interoperabili con l'API SQL. |
    |||

1. Selezionare **Avanti: configurazione**.
1. In **Crea un endpoint privato (anteprima) - Configurazione** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    |**Rete**| |
    | Rete virtuale| Selezionare la rete virtuale. |
    | Subnet | Selezionare la subnet. |
    |**Integrazione di DNS privato**||
    |Integra con la zona DNS privato |Selezionare **Sì**. <br><br/> Per connettersi privatamente all'endpoint privato, è necessario un record DNS. Si consiglia di integrare l'endpoint privato con una zona DNS privata. È anche possibile usare i propri server DNS o creare record DNS usando i file host delle macchine virtuali. |
    |Zona DNS privato |Seleziona *privatelink.Documents.Azure.com* <br><br/> La zona di DNS privato viene determinata automaticamente e non è possibile modificarla attualmente usando il portale di Azure.|
    |||

1. Selezionare **Rivedi e crea**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione.
1. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**.

Dopo aver approvato i collegamenti privati per un account Azure Cosmos, nella portale di Azure l'opzione **tutte le reti** nel riquadro **firewall e reti virtuali** è disabilitata.

La tabella seguente illustra il mapping tra diversi tipi di API dell'account Azure Cosmos, le sottorisorse supportate e i nomi di zona privati corrispondenti. Gli account Gremlin e API Tabella sono accessibili anche tramite l'API SQL, quindi sono disponibili 2 voci per queste API:

|Tipo di API dell'account Azure Cosmos  |Risorse secondarie supportate (o GroupID) |Nome zona privata  |
|---------|---------|---------|
|Sql    |   Sql      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  Sql       |  privatelink.documents.azure.com    |
|Tabella    |    Tabella     |   privatelink.table.cosmos.azure.com    |
|Tabella     |   Sql      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>Recuperare gli indirizzi IP privati

Una volta eseguito il provisioning dell'endpoint privato, è possibile eseguire una query sugli indirizzi IP. Per visualizzare gli indirizzi IP da portale di Azure, selezionare **tutte le risorse**, cercare l'endpoint privato creato in precedenza in questo caso "dbPrivateEndpoint3" e selezionare la scheda Panoramica per visualizzare le impostazioni DNS e gli indirizzi IP:

![Indirizzi IP privati in portale di Azure](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Per ogni endpoint privato vengono creati più indirizzi IP:

* Uno per l'endpoint globale (indipendente dall'area) dell'account Azure Cosmos.
* Uno per ogni area in cui viene distribuito l'account Azure Cosmos.

## <a name="create-a-private-link-using-azure-powershell"></a>Creare un collegamento privato usando Azure PowerShell

Eseguire lo script PowerSehll seguente per creare un collegamento privato denominato "MyPrivateEndpoint" per un account Azure Cosmos esistente. Assicurarsi di sostituire i valori delle variabili con i dettagli specifici dell'ambiente in uso.

```azurepowershell-interactive
Fill in these details, make sure to replace the variable values with the details specific to your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Cosmos DB and VNet resources live
$ResourceGroupName = "myResourceGroup"
# Name of the Cosmos DB account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Cosmos DB account: Sql or MongoDB or Cassandra or Gremlin or Table
$CosmosDbApiType = "Sql"
# Name of the existing VNet
$VNetName = "myVnet"
# Name of the target subnet in the VNet
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "MyPrivateEndpoint"
# Location where the private endpoint can be created. The private endpoint should be created in the same location where your subnet or the virtual network exists
$Location = "westcentralus"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnectionPS" -PrivateLinkServiceId $cosmosDbResourceId -GroupId $CosmosDbApiType
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $ResourceGroupName -Name $VNetName  
 
$subnet = $virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $SubnetName}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $ResourceGroupName -Name $PrivateEndpointName -Location "westcentralus" -Subnet  $subnet -PrivateLinkServiceConnection $privateEndpointConnection
```

### <a name="integrate-the-private-endpoint-with-private-dns-zone"></a>Integrare l'endpoint privato con la zona DNS privata

Dopo aver creato l'endpoint privato, è possibile integrarlo con una zona DNS privata usando il seguente script PowerSehll:

```azurepowershell-interactive
Import-Module Az.PrivateDns
$zoneName = "privatelink.documents.azure.com"
$zone = New-AzPrivateDnsZone -ResourceGroupName $ResourceGroupName `
  -Name $zoneName

$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $ResourceGroupName `
  -ZoneName $zoneName `
  -Name "myzonelink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$pe = Get-AzPrivateEndpoint -Name $PrivateEndpointName `
  -ResourceGroupName $ResourceGroupName

$networkInterface = Get-AzResource -ResourceId $pe.NetworkInterfaces[0].Id `
  -ApiVersion "2019-04-01"
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName `
  -RecordType A -ZoneName $zoneName  `
  -ResourceGroupName $ResourceGroupName -Ttl 600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
  -IPv4Address $ipconfig.properties.privateIPAddress)  
}
}
```

### <a name="fetch-the-private-ip-addresses"></a>Recuperare gli indirizzi IP privati

Dopo il provisioning dell'endpoint privato, è possibile eseguire una query sugli indirizzi IP e sul mapping FQDN usando il seguente script di PowerShell:

```azurepowershell-interactive

$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-link-using-a-resource-manager-template"></a>Creare un collegamento privato usando un modello di Gestione risorse

È possibile configurare il collegamento privato creando un endpoint privato in una subnet di rete virtuale. Questa operazione viene eseguita usando un modello di Azure Resource Manager. Creare un modello di Gestione risorse denominato "PrivateEndpoint_template. JSON" con il codice seguente. Questo modello crea un endpoint privato per un account API di Azure Cosmos SQL esistente in una rete virtuale esistente:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        },
        "privateEndpointName": {
            "type": "string"
        },
        "resourceId": {
            "type": "string"
        },
        "groupId": {
            "type": "string"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('privateEndpointName')]",
            "type": "Microsoft.Network/privateEndpoints",
            "apiVersion": "2019-04-01",
            "location": "[parameters('location')]",
            "properties": {
                "subnet": {
                    "id": "[parameters('subnetId')]"
                },
                "privateLinkServiceConnections": [
                    {
                        "name": "MyConnection",
                        "properties": {
                            "privateLinkServiceId": "[parameters('resourceId')]",
                            "groupIds": [ "[parameters('groupId')]" ],
                            "requestMessage": ""
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {
        "privateEndpointNetworkInterface": {
          "type": "string",
          "value": "[reference(concat('Microsoft.Network/privateEndpoints/', parameters('privateEndpointName'))).networkInterfaces[0].id]"
        }
    }
}
```

### <a name="define-the-template-parameters-file"></a>Definire il file dei parametri del modello

Creare un file di parametri per il modello e denominarlo "PrivateEndpoint_parameters. JSON". Aggiungere il codice seguente al file dei parametri:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateEndpointName": {
            "value": ""
        },
        "resourceId": {
            "value": ""
        },
        "groupId": {
            "value": ""
        },
        "subnetId": {
            "value": ""
        }
    }
}
```

### <a name="deploy-the-template-by-using-a-powershell-script"></a>Distribuire il modello usando uno script di PowerShell

Successivamente, creare uno script di PowerShell con il codice seguente. Prima di eseguire lo script, assicurarsi di sostituire l'ID sottoscrizione, il nome del gruppo di risorse e altri valori delle variabili con i dettagli specifici dell'ambiente:

```azurepowershell-interactive
### This script creates a private endpoint for an existing Cosmos DB account in an existing VNet

## Step 1: Fill in these details, make sure to replace the variable values with the details specific to your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Cosmos DB and VNet resources live
$ResourceGroupName = "myResourceGroup"
# Name of the Cosmos DB account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Cosmos DB account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing VNet
$VNetName = "myVnet"
# Name of the target subnet in the VNet
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Sign in to your Azure account and select the target subscription.
Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet.
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private endpoint.
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName

$deploymentOutput
```

Nello script di PowerShell, la variabile "GroupId" può contenere un solo valore, ovvero il tipo di API dell'account. I valori consentiti sono: SQL, MongoDB, Cassandra, Gremlin e Table. Alcuni tipi di account Azure Cosmos sono accessibili tramite più API. Ad esempio:

* È possibile accedere a un account API Gremlin dagli account Gremlin e API SQL.
* È possibile accedere a un account API Tabella dagli account di tabella e dell'API SQL.

Per questi account, è necessario creare un endpoint privato per ogni tipo di API, con il tipo di API corrispondente specificato nella matrice "GroupId".

Al termine della distribuzione del modello, è possibile visualizzare un output simile a quello mostrato nella figura seguente. Il valore provisioningState è "succeeded" se gli endpoint privati sono configurati correttamente.

![Output della distribuzione del modello di Gestione risorse](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

Una volta distribuito il modello, gli indirizzi IP privati vengono riservati all'interno della subnet. La regola del firewall dell'account Azure Cosmos è configurata in modo da accettare solo connessioni dall'endpoint privato.

## <a name="configure-custom-dns"></a>Configurare un valore DNS personalizzato

È necessario usare un DNS privato all'interno della subnet in cui è stato creato l'endpoint privato. E configurare gli endpoint in modo che ogni indirizzo IP privato venga mappato a una voce DNS (vedere la proprietà "FQDN" nella risposta riportata sopra).

Quando si crea l'endpoint privato, è possibile integrarlo con una zona DNS privata in Azure. Se si sceglie di non integrare l'endpoint privato con la zona DNS privata in Azure e si usa invece un DNS personalizzato, è necessario configurare il DNS per aggiungere i record DNS per tutti gli indirizzi IP privati riservati per l'endpoint privato.

## <a name="firewall-configuration-with-private-link"></a>Configurazione del firewall con collegamento privato

Quando si usa un collegamento privato in combinazione con le regole del firewall, sono disponibili le situazioni e i risultati seguenti:

* Se non sono configurate regole del firewall, per impostazione predefinita un account Azure Cosmos è accessibile a tutto il traffico.

* Se viene configurato il traffico pubblico o l'endpoint di servizio e vengono creati endpoint privati, i diversi tipi di traffico in ingresso sono autorizzati dal tipo corrispondente di regola del firewall.

* Se non viene configurato alcun traffico pubblico o endpoint di servizio e vengono creati endpoint privati, l'account Azure Cosmos è accessibile solo tramite gli endpoint privati. Se non è configurato alcun endpoint di servizio o traffico pubblico, dopo che tutti gli endpoint privati approvati sono stati rifiutati o eliminati, l'account è aperto per tutta la rete.

## <a name="update-private-endpoint-when-you-add-or-remove-a-region"></a>Aggiornare l'endpoint privato quando si aggiunge o si rimuove un'area

Per aggiungere o rimuovere aree a un account Azure Cosmos è necessario aggiungere o rimuovere le voci DNS per tale account. Queste modifiche devono essere aggiornate di conseguenza nell'endpoint privato. È attualmente necessario apportare manualmente questa modifica attenendosi alla procedura seguente:

1. L'amministratore Azure Cosmos DB aggiunge o rimuove le aree. Quindi gli amministratori di rete riceveranno una notifica sulle modifiche in sospeso. L'endpoint privato mappato a un account Azure Cosmos Visualizza le proprietà "ActionsRequired" modificate da "None" a "recreate". Quindi, l'amministratore di rete aggiorna l'endpoint privato inviando una richiesta PUT con lo stesso payload Gestione risorse usato per crearlo.

1. Dopo questa operazione, anche il DNS privato della subnet deve essere aggiornato per riflettere le voci DNS aggiunte o rimosse e i rispettivi indirizzi IP privati.

Ad esempio, se si distribuisce un account Azure Cosmos in 3 aree: "Stati Uniti occidentali", "Stati Uniti centrali" ed "Europa occidentale". Quando si crea un endpoint privato per l'account, 4 indirizzi IP privati sono riservati nella subnet. Uno per ogni area, che viene conteggiato su un totale di 3 e uno per l'endpoint a livello globale/indipendente dall'area.

In seguito, se si aggiunge una nuova area, ad esempio "Stati Uniti orientali" all'account Azure Cosmos. Per impostazione predefinita, la nuova area non è accessibile dall'endpoint privato esistente. L'amministratore dell'account Azure Cosmos deve aggiornare la connessione all'endpoint privato prima di accedervi dalla nuova area. 

Quando si esegue il comando ` Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>`, l'output del comando contiene il parametro `actionsRequired`, che è impostato su "ricrea". Questo valore indica che l'endpoint privato deve essere aggiornato. L'amministratore dell'account Azure Cosmos esegue quindi il comando `Set-AzPrivateEndpoint` per attivare l'aggiornamento dell'endpoint privato.

```powershell
$pe = Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>

Set-AzPrivateEndpoint -PrivateEndpoint $pe
```

Un nuovo indirizzo IP privato viene riservato automaticamente nella subnet in questo endpoint privato e il valore `actionsRequired` viene `None`. Se non si dispone di alcuna integrazione privata con la zona DNZ (in altre parole, se si usa un DNS privato personalizzato), è necessario configurare il DNS privato per aggiungere un nuovo record DNS per l'indirizzo IP privato corrispondente alla nuova area.

Quando si rimuove un'area, è possibile usare la stessa procedura. L'indirizzo IP privato dell'area rimossa viene recuperato automaticamente e il flag di `actionsRequired` viene `None`. Se non si dispone di alcuna integrazione privata con la zona DNZ, è necessario configurare il DNS privato per rimuovere il record DNS per l'area rimossa.

I record DNS nella zona DNS privata non vengono rimossi automaticamente quando viene eliminato un endpoint privato o viene rimossa un'area dall'account Azure Cosmos. È necessario rimuovere manualmente i record DNS.

## <a name="current-limitations"></a>Limitazioni correnti

Quando si usa il collegamento privato con un account Azure Cosmos, si applicano le limitazioni seguenti:

* Il supporto dei collegamenti privati per gli account Azure Cosmos e reti virtuali è disponibile solo in aree specifiche. Per un elenco delle aree supportate, vedere la sezione [aree disponibili](../private-link/private-link-overview.md#availability) nell'articolo relativo al collegamento privato. **Per poter creare un endpoint privato, è necessario che l'account VNET e Azure Cosmos si trovino nelle aree supportate**.

* Quando si usano i collegamenti privati con l'account Azure Cosmos usando la connessione in modalità diretta, è possibile usare solo il protocollo TCP. Il protocollo HTTP non è ancora supportato

* Quando si usa l'API di Azure Cosmos DB per gli account MongoDB, l'endpoint privato è supportato solo per gli account nel server versione 3,6, ovvero gli account che usano l'endpoint nel formato `*.mongo.cosmos.azure.com`. Il collegamento privato non è supportato per gli account nella versione Server 3,2, ovvero gli account che usano l'endpoint nel formato `*.documents.azure.com`. Per usare il collegamento privato, è necessario eseguire la migrazione degli account precedenti alla nuova versione.

* Quando si usa l'API Azure Cosmos DB per gli account MongoDB con collegamento privato, non è possibile usare strumenti quali Robo 3T, studio 3T, Mangusta e così via. L'endpoint può avere un supporto di collegamento privato solo se viene specificato il parametro appName =<account name>. Ad esempio: replicaSet = globaldb & appName = mydbaccountname. Poiché questi strumenti non passano il nome dell'app nella stringa di connessione al servizio, non è possibile usare il collegamento privato. Tuttavia, è comunque possibile accedere a questi account con driver SDK con la versione 3,6.

* Una rete virtuale non può essere spostata o eliminata se contiene un collegamento privato.

* Non è possibile eliminare un account Azure Cosmos se è collegato a un endpoint privato.

* Non è possibile eseguire il failover di un account Azure Cosmos in un'area di cui non è stato eseguito il mapping a tutti gli endpoint privati collegati all'account. Per altre informazioni, vedere Aggiunta o rimozione di aree nella sezione precedente.

* A un amministratore di rete deve essere concessa almeno l'autorizzazione "*/PrivateEndpointConnectionsApproval" nell'ambito dell'account Azure Cosmos da parte di un amministratore per creare endpoint privati approvati automaticamente.

### <a name="private-dns-zone-integration-limitations"></a>Limitazioni dell'integrazione della zona DNS privato

I record DNS nella zona DNS privata non vengono rimossi automaticamente quando viene eliminato un endpoint privato o viene rimossa un'area dall'account Azure Cosmos. È necessario rimuovere manualmente i record DNS prima di:

* Aggiunta di un nuovo endpoint privato collegato a questa zona DNS privata.
* Aggiunta di una nuova area a qualsiasi account di database con endpoint privati collegati a questa zona DNS privata.

Senza pulire i record DNS, è possibile che si verifichino problemi del piano dati imprevisti, ad esempio l'interruzione dei dati alle aree aggiunte dopo la rimozione di un endpoint privato

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle altre funzionalità di sicurezza di Azure Cosmos DB, vedere l'articolo seguente:

* Per configurare un firewall per Azure Cosmos DB, vedere [supporto del firewall](firewall-support.md).

* [Come configurare l'endpoint del servizio di rete virtuale per l'account Azure Cosmos.](how-to-configure-vnet-service-endpoint.md)

* Per altre informazioni sul collegamento privato, vedere la documentazione del [collegamento privato di Azure](../private-link/private-link-overview.md) .
