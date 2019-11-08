---
title: Configurare l'accesso basato su rete virtuale e subnet per l'account Azure Cosmos DB
description: Questo documento descrive i passaggi necessari per configurare un endpoint servizio di rete virtuale per Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: c399bed803145659bae1863e9e0b919f33254627
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820212"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>Configurare l'accesso da reti virtuali

È possibile configurare gli account Azure Cosmos DB per consentire l'accesso da una sola subnet specifica di una rete virtuale di Azure. Per limitare l'accesso a un account Azure Cosmos DB con le connessioni da una subnet in una rete virtuale:

1. Abilitare la subnet per inviare l'identità della subnet e della rete virtuale ad Azure Cosmos DB. Per eseguire questa operazione, è necessario abilitare un endpoint di servizio per Azure Cosmos DB nella subnet specifica.

1. Aggiungere una regola nell'account Azure Cosmos DB per specificare la subnet come origine da cui è possibile accedere all'account.

> [!NOTE]
> Dopo aver abilitato l'endpoint di servizio per l'account Azure Cosmos DB in una subnet, l'origine del traffico che raggiunge Azure Cosmos DB passa da un indirizzo IP pubblico a una rete virtuale e una subnet. La commutazione del traffico si applica a tutti gli account di Azure Cosmos DB a cui si accede da questa subnet. Se gli account di Azure Cosmos usano un firewall basato su protocollo IP per consentire l'accesso a questa subnet, le richieste provenienti dalla subnet abilitata al servizio non corrispondono più alle regole del firewall IP e vengono rifiutate.
>
> Per altre informazioni, vedere la procedura descritta nella sezione [Migrazione da una regola del firewall IP a un elenco di controllo di accesso della rete virtuale](#migrate-from-firewall-to-vnet) di questo articolo.

Le sezioni seguenti descrivono come configurare un endpoint servizio di rete virtuale per un account Azure Cosmos DB.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a id="configure-using-portal"></a>Configurare un endpoint di servizio tramite il portale di Azure

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Configurare un endpoint di servizio per una rete virtuale e una subnet Azure esistenti

1. Nel pannello **Tutte le risorse** individuare l'account Azure Cosmos DB da proteggere.

1. Selezionare **Firewall e reti virtuali** dal menu delle impostazioni e scegliere di consentire l'accesso da **Reti selezionate**.

1. Per concedere l'accesso a una subnet della rete virtuale esistente, in **Reti virtuali** selezionare **Add existing Azure virtual network** (Aggiungi rete virtuale di Azure esistente).

1. Selezionare la **sottoscrizione** da cui si vuole aggiungere una rete virtuale di Azure. Selezionare le **reti virtuali** e le **subnet** di Azure a cui si vuole consentire l'accesso al proprio account Azure Cosmos DB. Selezionare quindi **Abilita** per abilitare le reti selezionate con gli endpoint di servizio per "Microsoft.AzureCosmosDB". Al termine selezionare **Aggiungi**.

   ![Selezionare la rete virtuale e la subnet](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)

1. Dopo aver abilitato l'account Azure Cosmos DB per l'accesso da una rete virtuale, il traffico sarà consentito solo dalla subnet scelta. La rete virtuale e la subnet aggiunte verranno visualizzate come illustrato nello screenshot seguente:

   ![Rete virtuale e subnet configurate correttamente](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Per abilitare gli endpoint servizio di rete virtuale sono necessarie le autorizzazioni seguenti per la sottoscrizione:
>   * Sottoscrizione con la rete virtuale: Collaboratore Rete
>   * Sottoscrizione con account Azure Cosmos DB: Collaboratore Account DocumentDB
>   * Se la rete virtuale e l'account Azure Cosmos DB si trovano in sottoscrizioni diverse, verificare che anche la sottoscrizione con rete virtuale disponga di `Microsoft.DocumentDB` provider di risorse registrato. Per registrare un provider di risorse, vedere l'articolo sui [tipi e i provider di risorse di Azure](../azure-resource-manager/resource-manager-supported-services.md) .

Di seguito sono riportate le istruzioni per la registrazione della sottoscrizione con il provider di risorse.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Configurare un endpoint di servizio per una rete virtuale e una subnet Azure nuove

1. Nel pannello **Tutte le risorse** individuare l'account Azure Cosmos DB da proteggere.  

1. Selezionare **Firewall e reti virtuali** dal menu delle impostazioni e scegliere di consentire l'accesso da **Reti selezionate**.  

1. Per concedere l'accesso a una nuova rete virtuale di Azure, in **Reti virtuali** selezionare **Add new virtual network** (Aggiungi nuova rete virtuale).  

1. Fornire i dettagli necessari per creare una nuova rete virtuale e quindi selezionare **Crea**. La subnet verrà creata con un endpoint di servizio per "Microsoft.AzureCosmosDB" abilitato.

   ![Selezionare una rete virtuale e una subnet da una nuova rete virtuale](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Se l'account Azure Cosmos DB viene usato da altri servizi di Azure come Azure ricerca cognitiva oppure è accessibile da analisi di flusso o Power BI, si consente l'accesso selezionando **accetta connessioni dai data center globali di Azure**.

Per assicurarsi di avere accesso alle metriche di Azure Cosmos DB dal portale, è necessario abilitare le opzioni **Consentire l'accesso dal portale di Azure**. Per altre informazioni su queste opzioni, vedere l'articolo [Configurare un firewall IP](how-to-configure-firewall.md). Dopo avere abilitato l'accesso, selezionare **Salva** per salvare le impostazioni.

## <a id="remove-vnet-or-subnet"></a>Rimuovere una rete virtuale o una subnet

1. Dal pannello **Tutte le risorse** individuare l'account Azure Cosmos DB per cui sono stati assegnati gli endpoint di servizio.  

2. Selezionare **Firewall e reti virtuali** dal menu delle impostazioni.  

3. Per rimuovere una regola della rete virtuale o della subnet, selezionare **...** accanto alla rete virtuale o alla subnet, quindi selezionare **Rimuovi**.

   ![Rimuovere una rete virtuale](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4. Selezionare **Salva** per applicare le modifiche.

## <a id="configure-using-powershell"></a>Configurare un endpoint di servizio tramite Azure PowerShell

> [!NOTE]
> Quando si usa PowerShell o l'interfaccia della riga di comando di Azure, assicurarsi di specificare l'elenco completo dei filtri IP e degli ACL della rete virtuale nei parametri e non solo quelli che devono essere aggiunti.

Usare questa procedura per configurare un endpoint di servizio per un account Azure Cosmos DB usando Azure PowerShell:  

1. Installare [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) e [accedere](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Abilitare l'endpoint di servizio per una subnet esistente di una rete virtuale.  

   ```powershell
   $rgname = "<Resource group name>"
   $vnName = "<Virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzVirtualNetwork
   ```

1. Ottenere informazioni sulla rete virtuale.

   ```powershell
   $vnProp = Get-AzVirtualNetwork `
     -Name $vnName `
     -ResourceGroupName $rgName
   ```

1. Ottenere le proprietà dell'account Azure Cosmos DB eseguendo il cmdlet seguente:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Inizializzare le variabili per un utilizzo successivo. Configurare tutte le variabili della definizione di account esistente.

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $virtualNetworkRules = @(@{
      id = "$($vnProp.Id)/subnets/$sname";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. Aggiornare le proprietà dell'account Azure Cosmos DB con la nuova configurazione eseguendo i cmdlet seguenti: 

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName `
     -Properties $CosmosDBProperties
   ```

1. Eseguire il comando seguente per verificare che l'account Azure Cosmos DB venga aggiornato con l'endpoint servizio di rete virtuale configurato nel passaggio precedente:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>Configurare un endpoint di servizio tramite l'interfaccia della riga di comando di Azure

Gli account Azure Cosmos possono essere configurati per gli endpoint di servizio quando vengono creati o aggiornati in un secondo momento se la subnet è già stata configurata. Gli endpoint di servizio possono anche essere abilitati nell'account Cosmos in cui la subnet non è ancora configurata e quindi inizierà a funzionare quando la subnet viene configurata in un secondo momento. Questa flessibilità consente agli amministratori che non hanno accesso all'account Cosmos e alle risorse della rete virtuale di rendere le proprie configurazioni indipendenti tra loro.

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>Creare un nuovo account Cosmos e connetterlo a una subnet back-end per una nuova rete virtuale

In questo esempio la rete virtuale e la subnet vengono create con gli endpoint di servizio abilitati per entrambi quando vengono creati.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet with service endpoints enabled for Cosmos DB
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values and service endpoints
az cosmosdb create \
   -n $accountName \
   -g $resourceGroupName \
   --enable-virtual-network true \
   --virtual-network-rules $svcEndpoint
```

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Connettere e configurare un account Cosmos in una subnet back-end in modo indipendente

Questo esempio illustra come connettere un account Azure Cosmos a una nuova rete virtuale esistente in cui la subnet non è ancora configurata per gli endpoint di servizio. Questa operazione viene eseguita tramite il parametro `--ignore-missing-vnet-service-endpoint`. Ciò consente di completare la configurazione dell'account Cosmos senza errori prima che la configurazione alla subnet della rete virtuale sia stata completata. Una volta completata la configurazione della subnet, l'account Cosmos diventerà accessibile tramite la subnet configurata.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet
# that is not yet enabled for service endpoints.

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet but without configuring service endpoints (--service-endpoints Microsoft.AzureCosmosDB)
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values
az cosmosdb create -n $accountName -g $resourceGroupName

# Add the virtual network rule but ignore the missing service endpoint on the subnet
az cosmosdb network-rule add \
   -n $accountName \
   -g $resourceGroupName \
   --virtual-network $vnetName \
   --subnet svcEndpoint \
   --ignore-missing-vnet-service-endpoint true

read -p'Press any key to now configure the subnet for service endpoints'

az network vnet subnet update \
   -n $backEnd \
   -g $resourceGroupName \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB
```

## <a id="migrate-from-firewall-to-vnet"></a>Migrazione da una regola del firewall IP a un ACL di rete virtuale

Usare i passaggi seguenti solo per gli account Azure Cosmos DB con regole del firewall IP esistenti che consentono una subnet, quando si vogliono usare ACL basati sulla rete virtuale e la subnet anziché una regola del firewall IP.

Dopo aver attivato un endpoint di servizio per un account Azure Cosmos DB per una subnet, le richieste vengono inviate con un'origine che contiene informazioni sulla rete virtuale e la subnet anziché un indirizzo IP pubblico. Queste richieste non corrispondono a un filtro IP. La commutazione dell'origine si verifica per tutti gli account Azure Cosmos a cui si accede dalla subnet con un endpoint di servizio abilitato. Per evitare tempi di inattività, seguire questa procedura:

1. Ottenere le proprietà dell'account Azure Cosmos DB eseguendo il cmdlet seguente:

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Inizializzare le variabili per usarle successivamente. Configurare tutte le variabili della definizione di account esistente. Aggiungere l'elenco di controllo di accesso della rete virtuale a tutti gli account di Azure Cosmos DB a cui si accede dalla subnet con il contrassegno `ignoreMissingVNetServiceEndpoint`.

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $subnetID = "Subnet ARM URL" e.g "/subscriptions/f7ddba26-ab7b-4a36-a2fa-7d01778da30b/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/subnet1"

   $virtualNetworkRules = @(@{
      id = $subnetID;
      ignoreMissingVNetServiceEndpoint = "True";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. Aggiornare le proprietà dell'account Azure Cosmos DB con la nuova configurazione eseguendo i cmdlet seguenti:

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzResource `
      -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
      -ApiVersion $apiVersion `
      -ResourceGroupName $rgName `
      -Name $acctName `
      -Properties $CosmosDBProperties
   ```

1. Ripetere i passaggi da 1 a 3 per tutti gli account di Azure Cosmos DB accessibili dalla subnet.

1.  Attendere 15 minuti e quindi aggiornare la subnet per abilitare l'endpoint di servizio.

1.  Abilitare l'endpoint di servizio per una subnet esistente di una rete virtuale.

    ```powershell
    $rgname= "<Resource group name>"
    $vnName = "<virtual network name>"
    $sname = "<Subnet name>"
    $subnetPrefix = "<Subnet address range>"

    Get-AzVirtualNetwork `
       -ResourceGroupName $rgname `
       -Name $vnName | Set-AzVirtualNetworkSubnetConfig `
       -Name $sname `
       -AddressPrefix $subnetPrefix `
       -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzVirtualNetwork
    ```

1. Rimuovere la regola del firewall IP per la subnet.

## <a name="next-steps"></a>Passaggi successivi

* Per configurare un firewall per Azure Cosmos DB, vedere l'articolo [Supporto del firewall](firewall-support.md).
