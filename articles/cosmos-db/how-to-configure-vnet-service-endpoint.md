---
title: Configurare l'accesso basato sulla rete virtuale per un account Cosmos di AzureConfigure virtual network based access for an Azure Cosmos account
description: Questo documento descrive i passaggi necessari per configurare un endpoint servizio di rete virtuale per Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mjbrown
ms.openlocfilehash: 442623880c1b95f3d7e038ae44832b74853d2c4a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366242"
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

   ![Selezionare la rete virtuale e la subnet](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)

1. Dopo aver abilitato l'account Azure Cosmos DB per l'accesso da una rete virtuale, il traffico sarà consentito solo dalla subnet scelta. La rete virtuale e la subnet aggiunte verranno visualizzate come illustrato nello screenshot seguente:

   ![Rete virtuale e subnet configurate correttamente](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Per abilitare gli endpoint servizio di rete virtuale sono necessarie le autorizzazioni seguenti per la sottoscrizione:
>   * Sottoscrizione con la rete virtuale: Collaboratore Rete
>   * Sottoscrizione con account Azure Cosmos DB: Collaboratore Account DocumentDB
>   * Se la rete virtuale e l'account del database Cosmos di Azure si `Microsoft.DocumentDB` trovano in sottoscrizioni diverse, assicurarsi che nella sottoscrizione con rete virtuale sia registrato anche il provider di risorse. Per registrare un provider di risorse, vedere l'articolo Tipi di [provider di risorse di Azure.To](../azure-resource-manager/management/resource-providers-and-types.md) register a resource provider, see Azure resource providers and types article.

Di seguito sono riportate le istruzioni per la registrazione della sottoscrizione con il provider di risorse.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Configurare un endpoint di servizio per una rete virtuale e una subnet Azure nuove

1. Nel pannello **Tutte le risorse** individuare l'account Azure Cosmos DB da proteggere.  

1. Selezionare **Firewall e reti virtuali** dal menu delle impostazioni e scegliere di consentire l'accesso da **Reti selezionate**.  

1. Per concedere l'accesso a una nuova rete virtuale di Azure, in **Reti virtuali** selezionare **Add new virtual network** (Aggiungi nuova rete virtuale).  

1. Fornire i dettagli necessari per creare una nuova rete virtuale e quindi selezionare **Crea**. La subnet verrà creata con un endpoint di servizio per "Microsoft.AzureCosmosDB" abilitato.

   ![Selezionare una rete virtuale e una subnet da una nuova rete virtuale](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Se l'account del database Cosmos di Azure viene usato da altri servizi di Azure, ad esempio Ricerca cognitiva di Azure, oppure è accessibile da Analisi di flusso o Power BI, è possibile concedere l'accesso selezionando **Accetta connessioni all'interno**di data center di Azure globali.

Per assicurarsi di avere accesso alle metriche di Azure Cosmos DB dal portale, è necessario abilitare le opzioni **Consentire l'accesso dal portale di Azure**. Per altre informazioni su queste opzioni, vedere l'articolo [Configurare un firewall IP](how-to-configure-firewall.md). Dopo avere abilitato l'accesso, selezionare **Salva** per salvare le impostazioni.

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>Rimuovere una rete virtuale o una subnet

1. Dal pannello **Tutte le risorse** individuare l'account Azure Cosmos DB per cui sono stati assegnati gli endpoint di servizio.  

1. Selezionare **Firewall e reti virtuali** dal menu delle impostazioni.  

1. Per rimuovere una regola della rete virtuale o della subnet, selezionare **...** accanto alla rete virtuale o alla subnet, quindi selezionare **Rimuovi**.

   ![Rimuovere una rete virtuale](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

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

1. Preparare una regola di rete virtuale Cosmos DBPrepare a Cosmos DB Virtual Network Rule

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Aggiornare le proprietà dell'account di Azure Cosmos DB con la nuova configurazione dell'endpoint della rete virtuale:Update Azure Cosmos DB account properties with the new Virtual Network endpoint configuration: 

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

Gli account Di Azure Cosmos possono essere configurati per gli endpoint di servizio quando vengono creati o aggiornati in un secondo momento se la subnet è già configurata per essi. Gli endpoint di servizio possono essere abilitati anche nell'account Cosmos in cui la subnet non è ancora configurata per essi e quindi inizieranno a funzionare quando la subnet verrà configurata in un secondo momento. Questa flessibilità consente agli amministratori che non hanno accesso sia all'account Cosmos che alle risorse di rete virtuale di rendere le proprie configurazioni indipendenti l'una dall'altra.

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>Creare un nuovo account Cosmos e connetterlo a una subnet back-end per una nuova rete virtualeCreate a new Cosmos account and connect it to a back-end subnet for a new virtual network

In questo esempio la rete virtuale e la subnet vengono create con gli endpoint di servizio abilitati per entrambi al momento della creazione.

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

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Connettere e configurare un account Cosmos a una subnet back-end in modo indipendente

Questo esempio ha lo scopo di mostrare come connettere un account Cosmos di Azure a una nuova rete virtuale esistente in cui la subnet non è ancora configurata per gli endpoint del servizio. Questa operazione viene `--ignore-missing-vnet-service-endpoint` eseguita utilizzando il parametro . Ciò consente il completamento della configurazione dell'account Cosmos senza errori prima del completamento della configurazione nella subnet della rete virtuale. Una volta completata la configurazione della subnet, l'account Cosmos diventerà accessibile tramite la subnet configurata.

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

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>Migrazione da una regola del firewall IP a un ACL di rete virtuale

Per eseguire la migrazione di un account di database Cosmos di Azure dall'uso delle regole del firewall IP all'uso degli endpoint del servizio di rete virtuale, eseguire la procedura seguente.

Dopo aver configurato un account di database Cosmos di Azure per un endpoint del servizio per una subnet, le richieste provenienti da tale subnet vengono inviate al database Cosmos di Azure con informazioni sulla rete virtuale e sull'origine subnet anziché un indirizzo IP pubblico di origine. Queste richieste non corrisponderanno più a un filtro IP configurato nell'account azure Cosmos DB, motivo per cui i passaggi seguenti sono necessari per evitare tempi di inattività.

Prima di procedere, abilitare l'endpoint del servizio database Cosmos di Azure nella rete virtuale e nella subnet usando il passaggio illustrato in precedenza in "Abilitare l'endpoint del servizio per una subnet esistente di una rete virtuale".

1. Ottenere informazioni sulla rete virtuale e sulla subnet:Get virtual network and subnet information:

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

1. Preparare un nuovo oggetto regola di rete virtuale per l'account del database Cosmos di Azure:Prepare a new Virtual Network rule object for the Azure Cosmos DB account:

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Aggiornare l'account del database Cosmos di Azure per abilitare l'accesso all'endpoint del servizio dalla subnet:Update the Azure Cosmos DB account to enable service endpoint access from the subnet:

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Ripetere i passaggi precedenti per tutti gli account di database Cosmos di Azure a cui si accede dalla subnet.

1. Rimuovere la regola del firewall IP per la subnet dalle regole del firewall dell'account di database Cosmos di Azure.Remove the IP firewall rule for the subnet from the Azure Cosmos DB account's Firewall rules.

## <a name="next-steps"></a>Passaggi successivi

* Per configurare un firewall per Azure Cosmos DB, vedere l'articolo [Supporto del firewall](firewall-support.md).
