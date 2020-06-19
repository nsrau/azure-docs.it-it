---
title: Configurare il collegamento privato di Azure per un account Azure Cosmos
description: Informazioni su come configurare il collegamento privato di Azure per accedere a un account Azure Cosmos usando un indirizzo IP privato in una rete virtuale.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/14/2020
ms.author: thweiss
ms.openlocfilehash: 2c4044fded2d14b8c6a1d92f367de9588b7b2ca3
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697892"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account"></a>Configurare il collegamento privato di Azure per un account Azure Cosmos

Con il collegamento privato di Azure è possibile connettersi a un account Azure Cosmos tramite un endpoint privato. L'endpoint privato è un set di indirizzi IP privati in una subnet all'interno della rete virtuale. È quindi possibile limitare l'accesso a un account Azure Cosmos tramite indirizzi IP privati. Quando è combinato con criteri di gruppo di sicurezza di rete limitati, il collegamento privato contribuisce a ridurre il rischio di esfiltrazione dei dati. Per altre informazioni sugli endpoint privati, vedere l'articolo [Collegamento privato di Azure](../private-link/private-link-overview.md).

Il collegamento privato consente agli utenti di accedere a un account Azure Cosmos dall'interno della rete virtuale o da qualsiasi rete virtuale con peering. Le risorse mappate al collegamento privato sono accessibili anche in locale tramite peering privato attraverso la VPN o Azure ExpressRoute. 

È possibile connettersi a un account Azure Cosmos configurato con un collegamento privato usando il metodo di approvazione automatica o manuale. Per altre informazioni, vedere la sezione [Flusso di lavoro di approvazione](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) della documentazione del collegamento privato. 

Questo articolo descrive la procedura per creare un endpoint privato. Si presuppone che si stia usando il metodo di approvazione automatica.

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Creare un endpoint privato con il portale di Azure

Usare la procedura seguente per creare un endpoint privato per un account Azure Cosmos esistente usando il portale di Azure:

1. Dal riquadro **Tutte le risorse** scegliere un account Azure Cosmos.

1. Selezionare **Connessioni a endpoint privato** dall'elenco delle impostazioni e quindi selezionare **Endpoint privato**:

   ![Selezioni per la creazione di un endpoint privato nel portale di Azure](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. Nel riquadro **Crea un endpoint privato - Generale** immettere o selezionare i dettagli seguenti:

    | Impostazione | valore |
    | ------- | ----- |
    | **Dettagli del progetto** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare un gruppo di risorse.|
    | **Dettagli istanza** |  |
    | Nome | Immettere un nome per l'endpoint privato. Se il nome è già usato, creare un nome univoco. |
    |Region| Selezionare l'area in cui si vuole distribuire il collegamento privato. Creare l'endpoint privato nella stessa posizione in cui si trova la rete virtuale.|
    |||
1. Selezionare **Avanti: Risorsa**.
1. In **Crea un endpoint privato - Risorsa** immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    |Metodo di connessione  | Selezionare **Connettersi a una risorsa di Azure nella directory**. <br/><br/> È quindi possibile scegliere una delle risorse per configurare il collegamento privato. Oppure connettersi alla risorsa di un altro utente usando un ID risorsa o un alias condiviso.|
    | Subscription| Selezionare la propria sottoscrizione. |
    | Tipo di risorsa | Selezionare **Microsoft.AzureCosmosDB/databaseAccounts**. |
    | Risorsa |Selezionare l'account Azure Cosmos. |
    |Sottorisorsa di destinazione |Selezionare il tipo di API di Azure Cosmos DB di cui si vuole eseguire il mapping. Per impostazione predefinita viene selezionata un'unica opzione per le API SQL, MongoDB e Cassandra. Per le API Gremlin e Tabella, è anche possibile scegliere **SQL** poiché queste API sono interoperative con l'API SQL. |
    |||

1. Selezionare **Avanti: Configurazione**.
1. In **Crea un endpoint privato - Configurazione** immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    |**Rete**| |
    | Rete virtuale| Selezionare la rete virtuale. |
    | Subnet | Selezionare la subnet. |
    |**Integrazione DNS privato**||
    |Integra con la zona DNS privato |Selezionare **Sì**. <br><br/> Per connettersi in privato con l'endpoint privato, è necessario un record DNS. È consigliabile integrare l'endpoint privato con una zona DNS privata. È anche possibile usare i propri server DNS o creare record DNS usando i file host delle macchine virtuali. |
    |Zona DNS privato |Selezionare **privatelink.documents.azure.com**. <br><br/> La zona DNS privata viene determinata automaticamente. Non è possibile modificarla usando il portale di Azure.|
    |||

1. Selezionare **Rivedi e crea**. Nella pagina **Rivedi e crea** Azure convalida la configurazione.
1. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**.

Quando è stato approvato il collegamento privato per un account Azure Cosmos, nel portale di Azure l'opzione **Tutte le reti** nel riquadro **Firewall e reti virtuali** non è disponibile.

La tabella seguente illustra il mapping tra i diversi tipi di API dell'account Azure Cosmos, le risorse secondarie supportate e i nomi di zona privata corrispondenti. Poiché è anche possibile accedere agli account delle API Gremlin e Tabella tramite l'API SQL, sono disponibili due voci per queste API.

|Tipo di API dell'account Azure Cosmos  |Risorse secondarie supportate (o ID gruppo) |Nome della zona privata  |
|---------|---------|---------|
|Sql    |   Sql      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  Sql       |  privatelink.documents.azure.com    |
|Tabella    |    Tabella     |   privatelink.table.cosmos.azure.com    |
|Tabella     |   Sql      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>Recuperare gli indirizzi IP privati

Dopo aver eseguito il provisioning dell'endpoint privato, è possibile eseguire una query sugli indirizzi IP. Per visualizzare gli indirizzi IP dal portale di Azure:

1. Selezionare **Tutte le risorse**.
1. Cercare l'endpoint privato creato in precedenza. In questo caso, si tratta di **cdbPrivateEndpoint3**.
1. Selezionare la scheda **Panoramica** per visualizzare le impostazioni DNS e gli indirizzi IP.

![Indirizzi IP privati nel portale di Azure](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Per ogni endpoint privato vengono creati più indirizzi IP:

* Uno per l'endpoint globale (indipendente dall'area) dell'account Azure Cosmos
* Uno per ogni area in cui è distribuito l'account Azure Cosmos

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Creare un endpoint privato con Azure PowerShell

Eseguire lo script di PowerShell seguente per creare un endpoint privato denominato "MyPrivateEndpoint" per un account Azure Cosmos esistente. Sostituire i valori delle variabili con i dettagli dell'ambiente in uso.

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

Dopo aver creato l'endpoint privato, è possibile integrarlo con una zona DNS privata usando lo script di PowerShell seguente:

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

Dopo aver effettuato il provisioning dell'endpoint privato, è possibile eseguire una query sugli indirizzi IP e sul mapping FQDN usando lo script di PowerShell seguente:

```azurepowershell-interactive
$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-azure-cli"></a>Creare un endpoint privato con l'interfaccia della riga di comando di Azure

Eseguire lo script dell'interfaccia della riga di comando di Azure per creare un endpoint privato denominato "myPrivateEndpoint" per un account Azure Cosmos esistente. Sostituire i valori delle variabili con i dettagli dell'ambiente in uso.

```azurecli-interactive
# Resource group where the Azure Cosmos account and virtual network resources are located
ResourceGroupName="myResourceGroup"

# Subscription ID where the Azure Cosmos account and virtual network resources are located
SubscriptionId="<your Azure subscription ID>"

# Name of the existing Azure Cosmos account
CosmosDbAccountName="mycosmosaccount"

# API type of your Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
CosmosDbApiType="Sql"

# Name of the virtual network to create
VNetName="myVnet"

# Name of the subnet to create
SubnetName="mySubnet"

# Name of the private endpoint to create
PrivateEndpointName="myPrivateEndpoint"

# Name of the private endpoint connection to create
PrivateConnectionName="myConnection"

az network vnet create \
 --name $VNetName \
 --resource-group $ResourceGroupName \
 --subnet-name $SubnetName

az network vnet subnet update \
 --name $SubnetName \
 --resource-group $ResourceGroupName \
 --vnet-name $VNetName \
 --disable-private-endpoint-network-policies true

az network private-endpoint create \
    --name $PrivateEndpointName \
    --resource-group $ResourceGroupName \
    --vnet-name $VNetName  \
    --subnet $SubnetName \
    --private-connection-resource-id "/subscriptions/$SubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/$CosmosDbAccountName" \
    --group-ids $CosmosDbApiType \
    --connection-name $PrivateConnectionName
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrare l'endpoint privato con una zona DNS privata

Dopo aver creato l'endpoint privato, è possibile integrarlo con una zona DNS privata usando lo script dell'interfaccia della riga di comando di Azure seguente:

```azurecli-interactive
zoneName="privatelink.documents.azure.com"

az network private-dns zone create --resource-group $ResourceGroupName \
   --name  $zoneName

az network private-dns link vnet create --resource-group $ResourceGroupName \
   --zone-name  $zoneName\
   --name myzonelink \
   --virtual-network $VNetName \
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name $PrivateEndpointName --resource-group $ResourceGroupName --query 'networkInterfaces[0].id' -o tsv)
 
# Copy the content for privateIPAddress and FQDN matching the Azure Cosmos account 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
 
#Create DNS records 
az network private-dns record-set a create --name recordSet1 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName
az network private-dns record-set a add-record --record-set-name recordSet2 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName -a <Private IP Address>
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Creare un endpoint privato con un modello di Resource Manager

È possibile configurare il collegamento privato creando un endpoint privato in una subnet di rete virtuale. L'operazione può essere eseguita usando un modello di Azure Resource Manager.

Usare il codice seguente per creare un modello di Resource Manager denominato "PrivateEndpoint_template.json". Questo modello crea un endpoint privato per un account API SQL Azure Cosmos esistente in una rete virtuale esistente.

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

**Definire il file di parametri per il modello**

Creare un file di parametri per il modello e denominarlo "PrivateEndpoint_parameters.json". Aggiungere il codice seguente al file di parametri:

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

**Distribuire il modello usando uno script di PowerShell**

Creare uno script di PowerShell usando il codice seguente. Prima di eseguire lo script, sostituire l'ID sottoscrizione, il nome del gruppo di risorse e altri valori delle variabili con i dettagli per l'ambiente in uso.

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

Nello script di PowerShell la variabile `GroupId` può contenere un solo valore. Il valore è il tipo di API dell'account. I valori consentiti sono: `Sql`, `MongoDB`, `Cassandra`, `Gremlin` e `Table`. Alcuni tipi di account Azure Cosmos sono accessibili tramite più API. Ad esempio:

* È possibile accedere a un account API Gremlin dagli account API Gremlin e SQL.
* È possibile accedere a un account API Tabella dagli account API Tabella e SQL.

Per questi account, è necessario creare un endpoint privato per ogni tipo di API. Il tipo di API corrispondente è specificato nella matrice `GroupId`.

Al termine della distribuzione del modello, è possibile visualizzare un output simile a quello illustrato nell'immagine seguente. Il valore `provisioningState` è `Succeeded` se gli endpoint privati sono configurati correttamente.

![Output della distribuzione per il modello di Resource Manager](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

Dopo aver distribuito il modello, gli indirizzi IP privati vengono riservati all'interno della subnet. La regola del firewall dell'account Azure Cosmos è configurata per accettare solo connessioni dall'endpoint privato.

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrare l'endpoint privato con una zona DNS privata

Usare il codice seguente per creare un modello di Resource Manager denominato "PrivateZone_template.json". Questo modello crea una zona DNS privata per un account API SQL Azure Cosmos esistente in una rete virtuale esistente.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "type": "string"
        },
        "VNetId": {
            "type": "string"
        }        
    },
    "resources": [
        {
            "name": "[parameters('privateZoneName')]",
            "type": "Microsoft.Network/privateDnsZones",
            "apiVersion": "2018-09-01",
            "location": "global",
            "properties": {                
            }
        },
        {
            "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
            "apiVersion": "2018-09-01",
            "name": "[concat(parameters('privateZoneName'), '/myvnetlink')]",
            "location": "global",
            "dependsOn": [
                "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]"
            ],
            "properties": {
                "registrationEnabled": false,
                "virtualNetwork": {
                    "id": "[parameters('VNetId')]"
                }
            }
        }        
    ]
}
```

Usare il codice seguente per creare un modello di Resource Manager denominato "PrivateZoneRecords_template.json".

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "type": "string"
        },
        "IPAddress": {
            "type":"string"
        }        
    },
    "resources": [
         {
            "type": "Microsoft.Network/privateDnsZones/A",
            "apiVersion": "2018-09-01",
            "name": "[parameters('DNSRecordName')]",
            "properties": {
                "ttl": 300,
                "aRecords": [
                    {
                        "ipv4Address": "[parameters('IPAddress')]"
                    }
                ]
            }
        }    
    ]
}
```

**Definire il file di parametri per il modello**

Creare i due file di parametri seguenti per il modello. Creare il file "PrivateZone_parameters.json". con il codice seguente:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "value": ""
        },
        "VNetId": {
            "value": ""
        }
    }
}
```

Creare il file "PrivateZoneRecords_parameters.json". con il codice seguente:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "value": ""
        },
        "IPAddress": {
            "type":"object"
        }
    }
}
```

**Distribuire il modello usando uno script di PowerShell**

Creare uno script di PowerShell usando il codice seguente. Prima di eseguire lo script, sostituire l'ID sottoscrizione, il nome del gruppo di risorse e altri valori delle variabili con i dettagli per l'ambiente in uso.

```azurepowershell-interactive
### This script:
### - creates a private zone
### - creates a private endpoint for an existing Cosmos DB account in an existing VNet
### - maps the private endpoint to the private zone

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
# Name of the private zone to create
$PrivateZoneName = "myPrivateZone.documents.azure.com"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateZoneTemplateFilePath = "PrivateZone_template.json"
$PrivateZoneParametersFilePath = "PrivateZone_parameters.json"
$PrivateZoneRecordsTemplateFilePath = "PrivateZoneRecords_template.json"
$PrivateZoneRecordsParametersFilePath = "PrivateZoneRecords_parameters.json"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Login your Azure account and select the target subscription
Login-AzAccount 
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private zone
New-AzResourceGroupDeployment -Name "PrivateZoneDeployment" `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $PrivateZoneTemplateFilePath `
    -TemplateParameterFile $PrivateZoneParametersFilePath `
    -PrivateZoneName $PrivateZoneName `
    -VNetId $VNetResourceId

## Step 5: Create the private endpoint
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

## Step 6: Map the private endpoint to the private zone
$networkInterface = Get-AzResource -ResourceId $deploymentOutput.Outputs.privateEndpointNetworkInterface.Value -ApiVersion "2019-04-01"
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) {
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) {
        $recordName = $fqdn.split('.',2)[0]
        $dnsZone = $fqdn.split('.',2)[1]
        Write-Output "Deploying PrivateEndpoint DNS Record $($PrivateZoneName)/$($recordName) Template on $($resourceGroupName)"
        New-AzResourceGroupDeployment -Name "PrivateEndpointDNSDeployment" `
            -ResourceGroupName $ResourceGroupName `
            -TemplateFile $PrivateZoneRecordsTemplateFilePath `
            -TemplateParameterFile $PrivateZoneRecordsParametersFilePath `
            -DNSRecordName "$($PrivateZoneName)/$($RecordName)" `
            -IPAddress $ipconfig.properties.privateIPAddress
    }
}
```

## <a name="configure-custom-dns"></a>Configurare un valore DNS personalizzato

È consigliabile usare una zona DNS privata all'interno della subnet in cui è stato creato l'endpoint privato. Configurare gli endpoint in modo che ogni indirizzo IP privato venga mappato a una voce DNS. (Vedere la proprietà `fqdns` nella risposta illustrata in precedenza).

Quando si crea l'endpoint privato, è possibile integrarlo con una zona DNS privata in Azure. Se si sceglie di usare invece una zona DNS personalizzata, è necessario configurarla per aggiungere i record DNS per tutti gli indirizzi IP privati riservati per l'endpoint privato.

## <a name="private-link-combined-with-firewall-rules"></a>Collegamento privato combinato con le regole del firewall

Quando si usa il collegamento privato in combinazione con le regole del firewall, sono possibili le situazioni e i risultati seguenti:

* Se non si configura alcuna regola del firewall, per impostazione predefinita tutto il traffico può accedere a un account Azure Cosmos.

* Se si configura il traffico pubblico o un endpoint di servizio e si creano endpoint privati, i diversi tipi di traffico in ingresso sono autorizzati in base al tipo corrispondente di regola del firewall.

* Se non si configura alcun traffico pubblico o endpoint di servizio e si creano endpoint privati, l'account Azure Cosmos sarà accessibile solo tramite gli endpoint privati. Se non si configura il traffico pubblico o un endpoint di servizio, dopo che tutti gli endpoint privati approvati vengono rifiutati o eliminati, l'account è aperto per l'intera rete.

## <a name="blocking-public-network-access-during-account-creation"></a>Blocco dell'accesso alla rete pubblica durante la creazione dell'account

Come descritto nella sezione precedente, se non sono state impostate regole del firewall specifiche, l'aggiunta di un endpoint privato rende l'account Azure Cosmos accessibile solo tramite endpoint privati. Ciò significa che è possibile raggiungere l'account Azure Cosmos dal traffico pubblico dopo che è stato creato e prima che venga aggiunto un endpoint privato. Per assicurarsi che l'accesso alla rete pubblica sia disabilitato anche prima della creazione di endpoint privati, è possibile impostare il flag `publicNetworkAccess` su `Disabled` durante la creazione dell'account. Per un esempio che illustra come usare questo flag, vedere [questo modello di Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-cosmosdb-private-endpoint/).

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Aggiornare un endpoint privato quando si aggiunge o si rimuove un'area

Per aggiungere o rimuovere aree in un account Azure Cosmos è necessario aggiungere o rimuovere le voci DNS per l'account. Dopo l'aggiunta o la rimozione delle aree, è possibile aggiornare la zona DNS privata della subnet in modo da riflettere le voci DNS aggiunte o rimosse e i rispettivi indirizzi IP privati.

Ad esempio, si supponga di distribuire un account Azure Cosmos in tre aree: "Stati Uniti occidentali", "Stati Uniti centrali" ed "Europa occidentale". Quando si crea un endpoint privato per l'account, nella subnet vengono riservati quattro indirizzi IP privati. Esiste un solo IP per ognuna delle tre aree ed esiste un solo IP per l'endpoint globale o indipendente dall'area.

Successivamente, è possibile aggiungere una nuova area, ad esempio "Stati Uniti orientali", all'account Azure Cosmos. Dopo aver aggiunto la nuova area, è necessario aggiungere un record DNS corrispondente alla zona DNS privata o al DNS personalizzato.

Quando si rimuove un'area, è possibile usare la stessa procedura. Dopo aver rimosso l'area, è necessario rimuovere il record DNS corrispondente dalla zona DNS privata o dal DNS personalizzato.

## <a name="current-limitations"></a>Limitazioni correnti

Quando si usa il collegamento privato con un account Azure Cosmos, si applicano le limitazioni seguenti:

* Quando si usa il collegamento privato con un account Azure Cosmos usando una connessione in modalità diretta, è possibile usare solo il protocollo TCP. Il protocollo HTTP non è attualmente supportato.

* Quando si usa l'API di Azure Cosmos DB per gli account MongoDB, un endpoint privato è supportato solo per gli account nella versione del server 3.6, ovvero gli account che usano l'endpoint nel formato `*.mongo.cosmos.azure.com`. Il collegamento privato non è supportato per gli account nella versione del server 3.2, ovvero gli account che usano l'endpoint nel formato `*.documents.azure.com`. Per usare il collegamento privato, è necessario eseguire la migrazione degli account precedenti alla nuova versione.

* Quando si usa un'API di Azure Cosmos DB per l'account MongoDB che ha il collegamento privato, alcuni strumenti o librerie potrebbero non funzionare poiché rimuovono automaticamente il parametro `appName` dalla stringa di connessione. Questo parametro è necessario per connettersi all'account tramite un endpoint privato. Alcuni strumenti, ad esempio Visual Studio Code, non rimuovono il parametro dalla stringa di connessione e sono pertanto compatibili.

* È necessario concedere a un amministratore di rete almeno l'autorizzazione `Microsoft.DocumentDB/databaseAccounts/PrivateEndpointConnectionsApproval/action` nell'ambito dell'account Azure Cosmos per creare automaticamente endpoint privati approvati.

* La modalità diretta non è attualmente supportata nelle aree di Azure in Cina.

### <a name="limitations-to-private-dns-zone-integration"></a>Limitazioni per l'integrazione della zona DNS privata

I record DNS nella zona DNS privata non vengono rimossi automaticamente quando si elimina un endpoint privato o si rimuove un'area dall'account Azure Cosmos. È necessario rimuovere manualmente i record DNS prima di:

* Aggiungere un nuovo endpoint privato collegato alla zona DNS privata.
* Aggiungere una nuova area a un account di database con endpoint privati collegati alla zona DNS privata.

Se non si puliscono i record DNS, potrebbero verificarsi problemi imprevisti del piano dati. Questi problemi includono l'interruzione dei dati per le aree aggiunte dopo la rimozione dell'endpoint privato o la rimozione dell'area.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle funzioni di sicurezza di Azure Cosmos DB, vedere gli articoli seguenti:

* Per configurare un firewall per Azure Cosmos DB, vedere [Supporto del firewall](firewall-support.md).

* Per informazioni su come configurare un endpoint di servizio di rete virtuale per l'account Azure Cosmos, vedere [Configurare l'accesso dalle reti virtuali](how-to-configure-vnet-service-endpoint.md).

* Per altre informazioni sul collegamento privato, vedere la documentazione sul [collegamento privato di Azure](../private-link/private-link-overview.md).
