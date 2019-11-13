---
title: Configurare il collegamento privato di Azure per un account Azure Cosmos
description: Informazioni su come configurare il collegamento privato di Azure per accedere a un account Azure Cosmos usando un indirizzo IP privato in una rete virtuale.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: 826fe1195a142bd0826d6311eab5eb208bbc7e35
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007432"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account-preview"></a>Configurare il collegamento privato di Azure per un account Azure Cosmos (anteprima)

Con il collegamento privato di Azure è possibile connettersi a un account Azure Cosmos tramite un endpoint privato. L'endpoint privato è un set di indirizzi IP privati in una subnet all'interno della rete virtuale. È quindi possibile limitare l'accesso a un account Azure Cosmos tramite indirizzi IP privati. Quando il collegamento privato viene combinato con criteri NSG limitati, contribuisce a ridurre il rischio di exfiltration dei dati. Per altre informazioni sugli endpoint privati, vedere l'articolo relativo al [collegamento privato di Azure](../private-link/private-link-overview.md) .

Il collegamento privato consente agli utenti di accedere a un account Azure Cosmos dall'interno della rete virtuale o da qualsiasi rete virtuale con peering. Le risorse mappate al collegamento privato sono accessibili anche in locale tramite peering privato tramite VPN o Azure ExpressRoute. 

È possibile connettersi a un account Azure Cosmos configurato con un collegamento privato usando il metodo di approvazione automatico o manuale. Per ulteriori informazioni, vedere la sezione relativa al [flusso di lavoro di approvazione](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) della documentazione del collegamento privato. 

Questo articolo descrive i passaggi per creare un endpoint privato. Si presuppone che si stia usando il metodo di approvazione automatica.

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Creare un endpoint privato usando il portale di Azure

Usare la procedura seguente per creare un endpoint privato per un account Azure Cosmos esistente usando il portale di Azure:

1. Dal riquadro **tutte le risorse** scegliere un account Azure Cosmos.

1. Selezionare **connessioni a endpoint privato** dall'elenco di impostazioni e quindi selezionare **endpoint privato**:

   ![Selezioni per creare un endpoint privato nel portale di Azure](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. Nel riquadro **Crea un endpoint privato (anteprima)-nozioni di base** immettere o selezionare i dettagli seguenti:

    | Impostazione | Valore |
    | ------- | ----- |
    | **Dettagli del progetto** | |
    | sottoscrizione | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare un gruppo di risorse.|
    | **Dettagli istanza** |  |
    | Nome | Immettere un nome per l'endpoint privato. Se il nome viene creato, crearne uno univoco. |
    |Area| Selezionare l'area in cui si vuole distribuire il collegamento privato. Creare l'endpoint privato nella stessa posizione in cui risiede la rete virtuale.|
    |||
1. Selezionare **Avanti: risorsa**.
1. In **Crea un endpoint privato - Risorsa** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    |Connection Method (Metodo di connessione)  | Selezionare **Connetti a una risorsa di Azure nella directory**. <br/><br/> È quindi possibile scegliere una delle risorse per configurare il collegamento privato. In alternativa, è possibile connettersi alla risorsa di un altro utente usando un ID risorsa o un alias condiviso con l'utente.|
    | sottoscrizione| Selezionare la propria sottoscrizione. |
    | Tipo di risorsa | Selezionare **Microsoft. AzureCosmosDB/databaseAccounts**. |
    | Risorsa |Selezionare l'account Azure Cosmos. |
    |Sottorisorsa di destinazione |Selezionare il tipo di API di Azure Cosmos DB di cui si desidera eseguire il mapping. Il valore predefinito è solo una delle opzioni per le API SQL, MongoDB e Cassandra. Per le API Gremlin e Table, è anche possibile scegliere **SQL** perché queste API sono interoperative con l'API SQL. |
    |||

1. Selezionare **Avanti: configurazione**.
1. In **Crea un endpoint privato (anteprima) - Configurazione** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    |**Rete**| |
    | rete virtuale| Selezionare la rete virtuale. |
    | Subnet | Selezionare la subnet. |
    |**Integrazione di DNS privato**||
    |Integra con la zona DNS privato |Selezionare **Sì**. <br><br/> Per connettersi privatamente all'endpoint privato, è necessario un record DNS. Si consiglia di integrare l'endpoint privato con una zona DNS privata. È anche possibile usare i propri server DNS o creare record DNS usando i file host nelle macchine virtuali. |
    |Zona DNS privato |Selezionare **privatelink.Documents.Azure.com**. <br><br/> La zona DNS privata viene determinata automaticamente. Non è possibile modificarlo usando il portale di Azure.|
    |||

1. Selezionare **Rivedi e crea**. Nella pagina **Verifica e crea** , Azure convalida la configurazione.
1. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**.

Dopo aver approvato il collegamento privato per un account Azure Cosmos, nella portale di Azure l'opzione **tutte le reti** nel riquadro **firewall e reti virtuali** non è disponibile.

La tabella seguente illustra il mapping tra i diversi tipi di API dell'account Azure Cosmos, le risorse secondarie supportate e i nomi di zona privati corrispondenti. È anche possibile accedere agli account Gremlin e API Tabella tramite l'API SQL, quindi sono disponibili due voci per queste API.

|Tipo di API dell'account Azure Cosmos  |Risorse secondarie supportate (o ID gruppo) |Nome zona privata  |
|---------|---------|---------|
|Sql    |   Sql      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  Sql       |  privatelink.documents.azure.com    |
|tabella    |    tabella     |   privatelink.table.cosmos.azure.com    |
|tabella     |   Sql      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>Recuperare gli indirizzi IP privati

Una volta eseguito il provisioning dell'endpoint privato, è possibile eseguire una query sugli indirizzi IP. Per visualizzare gli indirizzi IP dal portale di Azure:

1. Selezionare **Tutte le risorse**.
1. Cercare l'endpoint privato creato in precedenza. In questo caso, è **cdbPrivateEndpoint3**.
1. Selezionare la scheda **Panoramica** per visualizzare le impostazioni DNS e gli indirizzi IP.

![Indirizzi IP privati nell'portale di Azure](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Per ogni endpoint privato vengono creati più indirizzi IP:

* Uno per l'endpoint globale (indipendente dall'area) dell'account Azure Cosmos
* Uno per ogni area in cui è distribuito l'account Azure Cosmos

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Creare un endpoint privato utilizzando Azure PowerShell

Eseguire lo script di PowerShell seguente per creare un endpoint privato denominato "MyPrivateEndpoint" per un account Azure Cosmos esistente. Sostituire i valori delle variabili con i dettagli relativi all'ambiente.

```azurepowershell-interactive
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
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

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrare l'endpoint privato con una zona DNS privata

Dopo aver creato l'endpoint privato, è possibile integrarlo con una zona DNS privata usando il seguente script di PowerShell:

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

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Creare un endpoint privato usando un modello di Gestione risorse

È possibile configurare il collegamento privato creando un endpoint privato in una subnet di rete virtuale. A tale scopo, è possibile usare un modello di Azure Resource Manager. 

Usare il codice seguente per creare un modello di Gestione risorse denominato "PrivateEndpoint_template. JSON". Questo modello crea un endpoint privato per un account API di Azure Cosmos SQL esistente in una rete virtuale esistente.

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

### <a name="define-the-parameters-file-for-the-template"></a>Definire il file dei parametri per il modello

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

Creare uno script di PowerShell usando il codice seguente. Prima di eseguire lo script, sostituire l'ID sottoscrizione, il nome del gruppo di risorse e altri valori delle variabili con i dettagli relativi all'ambiente.

```azurepowershell-interactive
### This script creates a private endpoint for an existing Azure Cosmos account in an existing virtual network

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
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

Nello script di PowerShell, la variabile `GroupId` può contenere un solo valore. Tale valore è il tipo di API dell'account. I valori consentiti sono: `Sql`, `MongoDB`, `Cassandra`, `Gremlin`e `Table`. Alcuni tipi di account Azure Cosmos sono accessibili tramite più API. Ad esempio:

* È possibile accedere a un account API Gremlin dagli account Gremlin e API SQL.
* È possibile accedere a un account API Tabella dagli account di tabella e dell'API SQL.

Per questi account, è necessario creare un endpoint privato per ogni tipo di API. Il tipo di API corrispondente è specificato nella matrice `GroupId`.

Al termine della distribuzione del modello, è possibile visualizzare un output simile a quello illustrato nell'immagine seguente. Il valore `provisioningState` viene `Succeeded` se gli endpoint privati sono configurati correttamente.

![Output della distribuzione per il modello di Gestione risorse](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

Una volta distribuito il modello, gli indirizzi IP privati vengono riservati all'interno della subnet. La regola del firewall dell'account Azure Cosmos è configurata in modo da accettare solo connessioni dall'endpoint privato.

## <a name="configure-custom-dns"></a>Configurare un valore DNS personalizzato

È consigliabile usare una zona DNS privata all'interno della subnet in cui è stato creato l'endpoint privato. Configurare gli endpoint in modo che ogni indirizzo IP privato venga mappato a una voce DNS. (Vedere la `fqdns` proprietà nella risposta mostrata in precedenza).

Quando si crea l'endpoint privato, è possibile integrarlo con una zona DNS privata in Azure. Se si sceglie di usare invece una zona DNS personalizzata, è necessario configurarla per aggiungere i record DNS per tutti gli indirizzi IP privati riservati per l'endpoint privato.

## <a name="private-link-combined-with-firewall-rules"></a>Collegamento privato combinato con regole del firewall

Quando si usa un collegamento privato in combinazione con le regole del firewall, sono possibili le situazioni e i risultati seguenti:

* Se non si configurano le regole del firewall, per impostazione predefinita tutto il traffico può accedere a un account Azure Cosmos.

* Se si configura il traffico pubblico o un endpoint di servizio e si creano endpoint privati, i diversi tipi di traffico in ingresso sono autorizzati dal tipo corrispondente di regola del firewall.

* Se non si configura alcun traffico pubblico o endpoint di servizio e si creano endpoint privati, l'account Azure Cosmos sarà accessibile solo tramite gli endpoint privati. Se non si configura il traffico pubblico o un endpoint del servizio, dopo che tutti gli endpoint privati approvati vengono rifiutati o eliminati, l'account è aperto per l'intera rete.

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Aggiornare un endpoint privato quando si aggiunge o si rimuove un'area

Per aggiungere o rimuovere aree a un account Azure Cosmos è necessario aggiungere o rimuovere le voci DNS per tale account. Aggiornare le modifiche di conseguenza nell'endpoint privato attenendosi alla procedura seguente:

1. Quando l'amministratore di Azure Cosmos DB aggiunge o rimuove aree, l'amministratore di rete riceve una notifica sulle modifiche in sospeso. Per l'endpoint privato mappato a un account Azure Cosmos, il valore della proprietà `ActionsRequired` cambia da `None` a `Recreate`. Quindi, l'amministratore di rete aggiorna l'endpoint privato inviando una richiesta PUT con lo stesso payload Gestione risorse usato per crearlo.

1. Dopo l'aggiornamento dell'endpoint privato, è possibile aggiornare la zona DNS privata della subnet in modo da riflettere le voci DNS aggiunte o rimosse e i rispettivi indirizzi IP privati.

Si supponga, ad esempio, di distribuire un account Azure Cosmos in tre aree: "Stati Uniti occidentali", "Stati Uniti centrali" ed "Europa occidentale". Quando si crea un endpoint privato per l'account, nella subnet sono riservati quattro indirizzi IP privati. Esiste un solo IP per ognuna delle tre aree ed esiste un solo IP per l'endpoint globale/indipendente dall'area.

Successivamente, è possibile aggiungere una nuova area (ad esempio, "Stati Uniti orientali") all'account Azure Cosmos. Per impostazione predefinita, la nuova area non è accessibile dall'endpoint privato esistente. L'amministratore dell'account Azure Cosmos deve aggiornare la connessione all'endpoint privato prima di accedervi dalla nuova area. 

Quando si esegue il comando ` Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>`, l'output del comando contiene il parametro `actionsRequired`. Questo parametro è impostato su `Recreate`. Questo valore indica che l'endpoint privato deve essere aggiornato. L'amministratore dell'account Azure Cosmos esegue quindi il comando `Set-AzPrivateEndpoint` per attivare l'aggiornamento dell'endpoint privato.

```powershell
$pe = Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>

Set-AzPrivateEndpoint -PrivateEndpoint $pe
```

Un nuovo indirizzo IP privato viene riservato automaticamente nella subnet in questo endpoint privato. Il valore per `actionsRequired` diventa `None`. Se non si dispone di alcuna integrazione privata con la zona DNZ (in altre parole, se si usa una zona DNS privata personalizzata), è necessario configurare la zona DNS privata per aggiungere un nuovo record DNS per l'indirizzo IP privato che corrisponde alla nuova area.

Quando si rimuove un'area, è possibile usare la stessa procedura. L'indirizzo IP privato dell'area rimossa viene recuperato automaticamente e il flag di `actionsRequired` viene `None`. Se non si dispone di alcuna integrazione privata con la zona DNZ, è necessario configurare la zona DNS privata per rimuovere il record DNS per l'area rimossa.

I record DNS nella zona DNS privata non vengono rimossi automaticamente quando viene eliminato un endpoint privato o viene rimossa un'area dall'account Azure Cosmos. È necessario rimuovere manualmente i record DNS.

## <a name="current-limitations"></a>Limitazioni correnti

Quando si usa un collegamento privato con un account Azure Cosmos, si applicano le limitazioni seguenti:

* Il supporto dei collegamenti privati per gli account e le reti virtuali di Azure Cosmos è disponibile solo in aree specifiche. Per un elenco delle aree supportate, vedere la sezione [aree disponibili](../private-link/private-link-overview.md#availability) nell'articolo relativo al collegamento privato. 

  > [!NOTE]
  > Per creare un endpoint privato, assicurarsi che la rete virtuale e l'account Azure Cosmos si trovino in aree supportate.

* Quando si usa un collegamento privato con un account Azure Cosmos usando una connessione in modalità diretta, è possibile usare solo il protocollo TCP. Il protocollo HTTP non è ancora supportato.

* Quando si usa l'API Azure Cosmos DB per gli account MongoDB, un endpoint privato è supportato solo per gli account nella versione Server 3,6, ovvero gli account che usano l'endpoint nel formato `*.mongo.cosmos.azure.com`. Il collegamento privato non è supportato per gli account nella versione Server 3,2, ovvero gli account che usano l'endpoint nel formato `*.documents.azure.com`. Per usare il collegamento privato, è necessario eseguire la migrazione degli account precedenti alla nuova versione.

* Quando si usano gli account dell'API Azure Cosmos DB per MongoDB con collegamento privato, non è possibile usare strumenti quali Robo 3T, studio 3T e Mangusta. L'endpoint può avere un supporto di collegamento privato solo se è specificato il parametro `appName=<account name>`. Un esempio è `replicaSet=globaldb&appName=mydbaccountname`. Poiché questi strumenti non passano il nome dell'app nella stringa di connessione al servizio, non è possibile usare un collegamento privato. Tuttavia, è comunque possibile accedere a questi account usando driver SDK con la versione 3,6.

* Non è possibile spostare o eliminare una rete virtuale se contiene un collegamento privato.

* Non è possibile eliminare un account Azure Cosmos se è collegato a un endpoint privato.

* Non è possibile eseguire il failover di un account Azure Cosmos in un'area di cui non è stato eseguito il mapping a tutti gli endpoint privati collegati all'account.

* A un amministratore di rete deve essere concessa almeno l'autorizzazione "*/PrivateEndpointConnectionsApproval" nell'ambito dell'account Azure Cosmos per creare endpoint privati approvati automaticamente.

### <a name="limitations-to-private-dns-zone-integration"></a>Limitazioni per l'integrazione della zona DNS privata

I record DNS nella zona DNS privata non vengono rimossi automaticamente quando si elimina un endpoint privato o si rimuove un'area dall'account Azure Cosmos. È necessario rimuovere manualmente i record DNS prima di:

* Aggiunta di un nuovo endpoint privato collegato a questa zona DNS privata.
* Aggiunta di una nuova area a qualsiasi account di database con endpoint privati collegati a questa zona DNS privata.

Se non si puliscono i record DNS, potrebbero verificarsi problemi imprevisti del piano dati. Questi problemi includono l'interruzione dei dati alle aree aggiunte dopo la rimozione dell'endpoint privato o la rimozione dell'area.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle funzionalità di sicurezza di Azure Cosmos DB, vedere gli articoli seguenti:

* Per configurare un firewall per Azure Cosmos DB, vedere [supporto del firewall](firewall-support.md).

* Per informazioni su come configurare un endpoint di servizio di rete virtuale per l'account Azure Cosmos, vedere [configurare l'accesso da reti virtuali](how-to-configure-vnet-service-endpoint.md).

* Per altre informazioni sul collegamento privato, vedere la documentazione del [collegamento privato di Azure](../private-link/private-link-overview.md) .
