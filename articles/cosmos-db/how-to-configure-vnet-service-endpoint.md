---
title: Configurare l'accesso in base alla rete virtuale per un account Azure Cosmos
description: Questo documento descrive i passaggi necessari per configurare un endpoint servizio di rete virtuale per Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/04/2020
ms.author: mjbrown
ms.openlocfilehash: a061676714c35b4e8868ce3df9c71be05297ba99
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85261665"
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

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>Configurare un endpoint di servizio tramite il portale di Azure

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Configurare un endpoint di servizio per una rete virtuale e una subnet Azure esistenti

1. Nel pannello **Tutte le risorse** individuare l'account Azure Cosmos DB da proteggere.

1. Selezionare **Firewall e reti virtuali** dal menu delle impostazioni e scegliere di consentire l'accesso da **Reti selezionate**.

1. Per concedere l'accesso a una subnet della rete virtuale esistente, in **Reti virtuali** selezionare **Add existing Azure virtual network** (Aggiungi rete virtuale di Azure esistente).

1. Selezionare la **sottoscrizione** da cui si vuole aggiungere una rete virtuale di Azure. Selezionare le **reti virtuali** e le **subnet** di Azure a cui si vuole consentire l'accesso al proprio account Azure Cosmos DB. Selezionare quindi **Abilita** per abilitare le reti selezionate con gli endpoint di servizio per "Microsoft.AzureCosmosDB". Al termine, selezionare **Aggiungi**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png" alt-text="Selezionare la rete virtuale e la subnet":::

1. Dopo aver abilitato l'account Azure Cosmos DB per l'accesso da una rete virtuale, il traffico sarà consentito solo dalla subnet scelta. La rete virtuale e la subnet aggiunte verranno visualizzate come illustrato nello screenshot seguente:

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png" alt-text="Rete virtuale e subnet configurate correttamente":::

> [!NOTE]
> Per abilitare gli endpoint servizio di rete virtuale sono necessarie le autorizzazioni seguenti per la sottoscrizione:
>   * Sottoscrizione con la rete virtuale: Collaboratore Rete
>   * Sottoscrizione con account Azure Cosmos DB: Collaboratore Account DocumentDB
>   * Se la rete virtuale e l'account Azure Cosmos DB si trovano in sottoscrizioni diverse, verificare che anche la sottoscrizione con rete virtuale disponga di un `Microsoft.DocumentDB` provider di risorse registrato. Per registrare un provider di risorse, vedere l'articolo sui [tipi e i provider di risorse di Azure](../azure-resource-manager/management/resource-providers-and-types.md) .

Di seguito sono riportate le istruzioni per la registrazione della sottoscrizione con il provider di risorse.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Configurare un endpoint di servizio per una rete virtuale e una subnet Azure nuove

1. Nel pannello **Tutte le risorse** individuare l'account Azure Cosmos DB da proteggere.  

1. Selezionare **Firewall e reti virtuali** dal menu delle impostazioni e scegliere di consentire l'accesso da **Reti selezionate**.  

1. Per concedere l'accesso a una nuova rete virtuale di Azure, in **Reti virtuali** selezionare **Add new virtual network** (Aggiungi nuova rete virtuale).  

1. Fornire i dettagli necessari per creare una nuova rete virtuale e quindi selezionare **Crea**. La subnet verrà creata con un endpoint di servizio per "Microsoft.AzureCosmosDB" abilitato.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png" alt-text="Selezionare una rete virtuale e una subnet da una nuova rete virtuale":::

Se l'account Azure Cosmos DB viene usato da altri servizi di Azure come Azure ricerca cognitiva oppure è accessibile da analisi di flusso o Power BI, si consente l'accesso selezionando **accetta connessioni dai data center globali di Azure**.

Per assicurarsi di avere accesso alle metriche di Azure Cosmos DB dal portale, è necessario abilitare le opzioni **Consentire l'accesso dal portale di Azure**. Per altre informazioni su queste opzioni, vedere l'articolo [Configurare un firewall IP](how-to-configure-firewall.md). Dopo avere abilitato l'accesso, selezionare **Salva** per salvare le impostazioni.

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>Rimuovere una rete virtuale o una subnet

1. Dal pannello **Tutte le risorse** individuare l'account Azure Cosmos DB per cui sono stati assegnati gli endpoint di servizio.  

1. Selezionare **Firewall e reti virtuali** dal menu delle impostazioni.  

1. Per rimuovere una regola della rete virtuale o della subnet, selezionare **...** accanto alla rete virtuale o alla subnet, quindi selezionare **Rimuovi**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png" alt-text="Rimuovere una rete virtuale":::

1. Selezionare **Salva** per applicare le modifiche.

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>Configurare un endpoint di servizio tramite Azure PowerShell

> [!NOTE]
> Quando si usa PowerShell o l'interfaccia della riga di comando di Azure, assicurarsi di specificare l'elenco completo dei filtri IP e degli ACL della rete virtuale nei parametri e non solo quelli che devono essere aggiunti.

Usare questa procedura per configurare un endpoint di servizio per un account Azure Cosmos DB usando Azure PowerShell:  

1. Installare [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) e [accedere](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Abilitare l'endpoint di servizio per una subnet esistente di una rete virtuale.  

   ```powershell
   $resourceGroupName = "<Resource group name>"
   $vnetName = "<Virtual network name>"
   $subnetName = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"
   $serviceEndpoint = "Microsoft.AzureCosmosDB"

   Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName | Set-AzVirtualNetworkSubnetConfig `
      -Name $subnetName `
      -AddressPrefix $subnetPrefix `
      -ServiceEndpoint $serviceEndpoint | Set-AzVirtualNetwork
   ```

1. Ottenere informazioni sulla rete virtuale.

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Preparare una regola della rete virtuale Cosmos DB

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Aggiornare Azure Cosmos DB proprietà dell'account con la nuova configurazione dell'endpoint della rete virtuale: 

   ```powershell
   $accountName = "<Cosmos DB account name>"

   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Eseguire il comando seguente per verificare che l'account Azure Cosmos DB venga aggiornato con l'endpoint servizio di rete virtuale configurato nel passaggio precedente:

   ```powershell
   $account = Get-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName

   $account.IsVirtualNetworkFilterEnabled
   $account.VirtualNetworkRules
   ```

## <a name="configure-a-service-endpoint-by-using-the-azure-cli"></a><a id="configure-using-cli"></a>Configurare un endpoint di servizio tramite l'interfaccia della riga di comando di Azure

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

Questo esempio illustra come connettere un account Azure Cosmos a una nuova rete virtuale esistente in cui la subnet non è ancora configurata per gli endpoint di servizio. Questa operazione viene eseguita tramite il `--ignore-missing-vnet-service-endpoint` parametro. Ciò consente di completare la configurazione dell'account Cosmos senza errori prima che la configurazione alla subnet della rete virtuale sia stata completata. Una volta completata la configurazione della subnet, l'account Cosmos diventerà accessibile tramite la subnet configurata.

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

## <a name="port-range-when-using-direct-mode"></a>Intervallo di porte quando si usa la modalità diretta

Quando si usano gli endpoint di servizio con un account Azure Cosmos tramite una connessione in modalità diretta, è necessario assicurarsi che l'intervallo di porte TCP da 10000 a 20000 sia aperto.

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>Migrazione da una regola del firewall IP a un ACL di rete virtuale

Per eseguire la migrazione di un account Azure Cosmos DB usando le regole del firewall IP per usare gli endpoint del servizio rete virtuale, seguire questa procedura.

Dopo aver configurato un account di Azure Cosmos DB per un endpoint di servizio per una subnet, le richieste provenienti da tale subnet vengono inviate a Azure Cosmos DB con le informazioni sull'origine della rete virtuale e della subnet invece di un indirizzo IP pubblico di origine. Queste richieste non corrisponderanno più a un filtro IP configurato per l'account Azure Cosmos DB, motivo per cui è necessario eseguire i passaggi seguenti per evitare tempi di inattività.

Prima di procedere, abilitare l'endpoint del servizio Azure Cosmos DB nella rete virtuale e nella subnet usando il passaggio illustrato in "abilitare l'endpoint del servizio per una subnet esistente di una rete virtuale".

1. Ottenere informazioni su rete virtuale e subnet:

   ```powershell
   $resourceGroupName = "myResourceGroup"
   $accountName = "mycosmosaccount"
   $vnetName = "myVnet"
   $subnetName = "mySubnet"

   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Preparare un nuovo oggetto regola della rete virtuale per l'account Azure Cosmos DB:

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Aggiornare l'account Azure Cosmos DB per abilitare l'accesso all'endpoint di servizio dalla subnet:

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Ripetere i passaggi precedenti per tutti gli account di Azure Cosmos DB a cui si accede dalla subnet.

1. Rimuovere la regola del firewall IP per la subnet dalle regole del firewall dell'account Azure Cosmos DB.

## <a name="next-steps"></a>Passaggi successivi

* Per configurare un firewall per Azure Cosmos DB, vedere l'articolo [Supporto del firewall](firewall-support.md).
