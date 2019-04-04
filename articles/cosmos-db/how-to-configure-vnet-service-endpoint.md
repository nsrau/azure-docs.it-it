---
title: Configurare l'accesso basato su rete virtuale e subnet per l'account Azure Cosmos DB
description: Questo documento descrive i passaggi necessari per configurare un endpoint di rete virtuale per Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: c0b1b415db9d8a530a495e09805ad9788c1edfbe
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58904220"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>Configurare l'accesso da reti virtuali

È possibile configurare gli account Azure Cosmos DB per consentire l'accesso da una sola subnet specifica di una rete virtuale di Azure. Per limitare l'accesso a un account Azure Cosmos DB con le connessioni da una subnet in una rete virtuale:
 
1. Abilitare la subnet per inviare l'identità della subnet e della rete virtuale ad Azure Cosmos DB. Per eseguire questa operazione, è necessario abilitare un endpoint di servizio per Azure Cosmos DB nella subnet specifica.

1. Aggiungere una regola nell'account Azure Cosmos DB per specificare la subnet come origine da cui è possibile accedere all'account.

> [!NOTE]
> Dopo aver abilitato l'endpoint di servizio per l'account Azure Cosmos DB in una subnet, l'origine del traffico che raggiunge Azure Cosmos DB passa da un indirizzo IP pubblico a una rete virtuale e una subnet. La commutazione del traffico si applica a tutti gli account di Azure Cosmos DB a cui si accede da questa subnet. Se gli account di Azure Cosmos usano un firewall basato su protocollo IP per consentire l'accesso a questa subnet, le richieste provenienti dalla subnet abilitata al servizio non corrispondono più alle regole del firewall IP e vengono rifiutate. 
>
> Per altre informazioni, vedere la procedura descritta nella sezione [Migrazione da una regola del firewall IP a un elenco di controllo di accesso della rete virtuale](#migrate-from-firewall-to-vnet) di questo articolo. 

Le sezioni seguenti descrivono come configurare un endpoint di servizio della rete virtuale per un account Azure Cosmos DB.

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
> Per abilitare gli endpoint di servizio della rete virtuale sono necessarie le autorizzazioni seguenti per la sottoscrizione:
>   * Sottoscrizione con la rete virtuale: Collaboratore Rete
>   * Sottoscrizione con account Azure Cosmos DB: Collaboratore Account DocumentDB
>   * Se la rete virtuale e un account Azure Cosmos DB si trovano in sottoscrizioni diverse, assicurarsi che la sottoscrizione con rete virtuale disponga anche `Microsoft.DocumentDB` provider di risorse registrato. Per registrare un provider di risorse, vedere [provider di risorse di Azure e i tipi](../azure-resource-manager/resource-manager-supported-services.md) articolo. 

Ecco le istruzioni per la registrazione di abbonamento con provider di risorse.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Configurare un endpoint di servizio per una rete virtuale e una subnet Azure nuove

1. Nel pannello **Tutte le risorse** individuare l'account Azure Cosmos DB da proteggere.  

1. Selezionare **Firewall e reti virtuali** dal menu delle impostazioni e scegliere di consentire l'accesso da **Reti selezionate**.  

1. Per concedere l'accesso a una nuova rete virtuale di Azure, in **Reti virtuali** selezionare **Add new virtual network** (Aggiungi nuova rete virtuale).  

1. Fornire i dettagli necessari per creare una nuova rete virtuale e quindi selezionare **Crea**. La subnet verrà creata con un endpoint di servizio per "Microsoft.AzureCosmosDB" abilitato.

   ![Selezionare una rete virtuale e una subnet da una nuova rete virtuale](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Se l'account Azure Cosmos DB viene usato da altri servizi di Azure, come Ricerca di Azure o si accede a questo account da Analisi di flusso o Power BI, consentire l'accesso selezionando **Accetta connessioni dai data center di Azure pubblici**.

Per assicurarsi di avere accesso alle metriche di Azure Cosmos DB dal portale, è necessario abilitare le opzioni **Consentire l'accesso dal portale di Azure**. Per altre informazioni su queste opzioni, vedere l'articolo [Configurare un firewall IP](how-to-configure-firewall.md). Dopo avere abilitato l'accesso, selezionare **Salva** per salvare le impostazioni.

## <a id="remove-vnet-or-subnet"></a>Rimuovere una rete virtuale o una subnet 

1. Dal pannello **Tutte le risorse** individuare l'account Azure Cosmos DB per cui sono stati assegnati gli endpoint di servizio.  

2. Selezionare **Firewall e reti virtuali** dal menu delle impostazioni.  

3. Per rimuovere una regola della rete virtuale o della subnet, selezionare **...** accanto alla rete virtuale o alla subnet, quindi selezionare **Rimuovi**.

   ![Rimuovere una rete virtuale](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4.  Selezionare **Salva** per applicare le modifiche.

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

1. Eseguire il comando seguente per verificare che l'account Azure Cosmos DB venga aggiornato con l'endpoint di servizio della rete virtuale configurato nel passaggio precedente:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>Configurare un endpoint di servizio tramite l'interfaccia della riga di comando di Azure 

1. Abilitare l'endpoint di servizio per una subnet di una rete virtuale.

1. Aggiornare l'account Azure Cosmos DB esistente con gli elenchi di controllo di accesso (ACL) per la subnet.

   ```azurecli-interactive

   name="<Azure Cosmos DB account name>"
   resourceGroupName="<Resource group name>"

   az cosmosdb update \
    --name $name \
    --resource-group $resourceGroupName \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/frontend"
   ```

1. Creare un nuovo account di Cosmos Azure con gli elenchi di controllo di accesso della subnet.

   ```azurecli-interactive
   az cosmosdb create \
    --name $name \
    --kind GlobalDocumentDB \
    --resource-group $resourceGroupName \
    --max-interval 10 \
    --max-staleness-prefix 200 \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/default"
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
