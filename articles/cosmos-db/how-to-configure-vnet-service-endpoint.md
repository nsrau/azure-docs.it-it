---
title: Come configurare l'accesso basato su rete virtuale e subnet per l'account di Azure Cosmos
description: Questo documento descrive i passaggi necessari per configurare l'endpoint di servizio di Rete virtuale per Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: a7c2d1e41fa4ac26854e2e6ab57184cd6ed0bd0c
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633683"
---
# <a name="how-to-access-azure-cosmos-db-resources-from-virtual-networks"></a>Come accedere alle risorse di Azure Cosmos DB dalle reti virtuali

Gli account Azure Cosmos DB possono essere configurati per consentire l'accesso solo da una subnet specifica di Rete virtuale di Azure. Sono necessari due passaggi per limitare l'accesso all'account di Azure Cosmos con connessioni da una subnet di una rete virtuale (VNET).
 
1. Abilitare la subnet per inviare l'identità della subnet e della rete virtuale a Azure Cosmos DB. Per eseguire questa operazione, è necessario abilitare l'endpoint di servizio per Azure Cosmos DB nella subnet specifica.

1. Aggiungere una regola nell'account di Azure Cosmos specificando la subnet come origine da cui è possibile accedere all'account.

> [!NOTE]
> Dopo aver abilitato l'endpoint di servizio per l'account di Azure Cosmos in una subnet, l'origine del traffico che raggiunge Azure Cosmos DB passa dall'indirizzo IP pubblico alla rete virtuale e alla subnet. La commutazione del traffico si applica a tutti gli account di Azure Cosmos a cui si accede da questa subnet. Se l'account o gli account di Azure Cosmos dispongono di un firewall basato su protocollo IP per consentire l'accesso a questa subnet, le richieste provenienti dalla subnet abilitata al servizio non corrisponderebbero più alle regole del firewall IP e verrebbero rifiutate. Per altre informazioni, vedere la procedura descritta nella sezione [Migrazione dalla regola del firewall IP all'elenco di controllo di accesso della rete virtuale](#migrate-from-firewall-to-vnet) di questo articolo. 

Le sezioni seguenti descrivono come configurare l'endpoint di servizio della rete virtuale per un account di Azure Cosmos DB.

## <a id="configure-using-portal"></a>Configurare l'endpoint di servizio tramite il portale di Azure

### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Configurare l'endpoint di servizio per una rete virtuale e una subnet Azure esistenti

1. Nel pannello **Tutte le risorse** individuare l'account di Azure Cosmos da proteggere.

1. Selezionare **Firewall e reti virtuali** dal menu delle impostazioni e scegliere di consentire l'accesso da **Reti selezionate**.

1. Per concedere l'accesso a una subnet della rete virtuale esistente, in **Reti virtuali** selezionare **Add existing Azure virtual network** (Aggiungi rete virtuale di Azure esistente).

1. Selezionare la **sottoscrizione** da cui si vuole aggiungere la rete virtuale di Azure. In **Reti virtuali** e **Subnet** di Azure selezionare le reti a cui si vuole consentire di accedere all'account di Azure Cosmos. Selezionare quindi **Abilita** per abilitare le reti selezionate con gli endpoint di servizio per "Microsoft.AzureCosmosDB". Al termine selezionare **Aggiungi**. 

   ![Selezionare la rete virtuale e la subnet](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)


1. Dopo aver abilitato l'account di Azure Cosmos ad accedere da una rete virtuale, il traffico sarà consentito solo dalla subnet scelta. La rete virtuale e la subnet aggiunte verranno visualizzate come illustrato nello screenshot seguente:

   ![rete virtuale e subnet configurate correttamente](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Per abilitare gli endpoint di servizio delle reti virtuali sono necessarie le autorizzazioni di sottoscrizione seguenti:
  * Sottoscrizione con rete virtuale: Collaboratore Rete
  * Sottoscrizione con account di Azure Cosmos: Collaboratore Account DocumentDB

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Configurare l'endpoint di servizio per una rete virtuale e una subnet Azure nuove

1. Nel pannello **Tutte le risorse** individuare l'account Azure Cosmos DB da proteggere.  

2. Prima di abilitare l'endpoint di servizio della rete virtuale, copiare le informazioni sul firewall IP associate all'account Azure Cosmos DB per un utilizzo futuro. Dopo avere configurato l'endpoint di servizio, è possibile riabilitare il firewall IP.  

3. Selezionare **Firewall e reti virtuali di Azure** dal menu delle impostazioni e scegliere di consentire l'accesso da **Reti selezionate**.  

4. Per concedere l'accesso a una nuova rete virtuale di Azure, in Reti virtuali selezionare **Add new virtual network** (Aggiungi nuova rete virtuale).  

5. Fornire i dettagli necessari per creare una nuova rete virtuale e quindi selezionare Crea. La subnet verrà creata con un endpoint di servizio per "Microsoft.AzureCosmosDB" abilitato.

   ![Selezionare la rete virtuale e la subnet per la nuova rete virtuale](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Se l'account di Azure Cosmos viene usato da altri servizi di Azure come Ricerca di Azure o viene eseguito l'accesso all'account da Analisi di flusso o Power BI, è possibile consentire l'accesso selezionando Consenti l'accesso a Servizi di Azure.

Per verificare che si disponga dell'accesso alla metrica di Azure Cosmos DB dal portale, è necessario abilitare le opzioni **Consenti l'accesso al portale di Azure**. Per altre informazioni su queste opzioni, vedere le sezioni sulle richieste dal portale di Azure e dai servizi PaaS di Azure nell'articolo relativo alla [configurazione del firewall IP](how-to-configure-firewall.md). Dopo avere selezionato l'accesso, selezionare **Salva** per salvare le impostazioni.

## <a id="remove-vnet-or-subnet"></a>Rimuovere una rete virtuale o una subnet 

1. Dal pannello **Tutte le risorse** individuare l'account Azure Cosmos DB per cui sono stati assegnati gli endpoint di servizio.  

2. Selezionare **Firewall e reti virtuali** dal menu delle impostazioni.  

3. Per rimuovere una regola della rete virtuale o della subnet, selezionare "..." accanto alla rete virtuale o alla subnet quindi selezionare **Rimuovi**.

   ![Rimuovere una rete virtuale](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4.  Fare clic su **Salva** per applicare le modifiche.

## <a id="configure-using-powershell"></a>Configurare l'endpoint di servizio usando Azure PowerShell 

Usare la procedura seguente per configurare l'endpoint di servizio per un account di Azure Cosmos DB tramite Azure PowerShell:  

1. Installare la versione più aggiornata di [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) ed [effettuare l'accesso](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Abilitare l'endpoint di servizio per una subnet esistente di una rete virtuale.  

   ```powershell
   $rgname= "<Resource group name>"
   $vnName = "<virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

1. Preparare l'abilitazione dell'elenco ACL per l'account di Azure Cosmos verificando che la rete virtuale e la subnet dispongano dell'endpoint di servizio abilitato per l'account.

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName  -ResourceGroupName $rgName
   ```

1. Ottenere le proprietà dell'account Azure Cosmos DB eseguendo il cmdlet seguente:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Inizializzare le variabili per un utilizzo successivo. Configurare tutte le variabili dalla definizione di account esistente; se si dispone di più percorsi, è necessario aggiungerli come parte della matrice. In questo passaggio si configura anche l'endpoint di servizio della rete virtuale impostando la variabile "accountVNETFilterEnabled" su "True". Questo valore viene assegnato in un secondo momento al parametro "isVirtualNetworkFilterEnabled". 

   ```powershell
   $locations = @(@{})

   <# If you have read regions in addition to a write region, use the following code to set the $locations variable instead.

   $locations = @(@{"locationName"="<Write location>"; 
                 "failoverPriority"=0}, 
               @{"locationName"="<Read location>"; 
                  "failoverPriority"=1}) #>

   $consistencyPolicy = @{}
   $cosmosDBProperties = @{}

   $locations[0]['failoverPriority'] = $cosmosDBConfiguration.Properties.failoverPolicies.failoverPriority
   $locations[0]['locationName'] = $cosmosDBConfiguration.Properties.failoverPolicies.locationName

   $consistencyPolicy = $cosmosDBConfiguration.Properties.consistencyPolicy

   $accountVNETFilterEnabled = $True
   $subnetID = $vnProp.Id+"/subnets/" + $sname  
   $virtualNetworkRules = @(@{"id"=$subnetID})
   $databaseAccountOfferType = $cosmosDBConfiguration.Properties.databaseAccountOfferType
   ```

1. Aggiornare le proprietà dell'account Azure Cosmos DB con la nuova configurazione eseguendo i cmdlet seguenti: 

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName -Properties $CosmosDBProperties
   ```

1. Eseguire il comando seguente per verificare che l'account Azure Cosmos DB venga aggiornato con l'endpoint di servizio della rete virtuale configurato nel passaggio precedente:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>Configurare l'endpoint di servizio tramite l'interfaccia della riga di comando di Azure 

1. Abilitare l'endpoint di servizio per una subnet di una rete virtuale.

1. Aggiornare l'account di Cosmos Azure esistente con gli elenchi di controllo di accesso della subnet

   ```azurecli-interactive

   name="<Azure Cosmos account name>"
   resourceGroupName="<Resource group name>"

   az cosmosdb update \
      --name $name \
    --resource-group $resourceGroupName \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/frontend"
   ```

1. Creare un nuovo account di Cosmos Azure con gli elenchi di controllo di accesso della subnet

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

## <a id="migrate-from-firewall-to-vnet"></a>Eseguire la migrazione dalla regola del firewall IP all'elenco di controllo di accesso della rete virtuale 

I passaggi seguenti sono necessari solo per gli account di Azure Cosmos con regole del firewall IP esistenti che consentono l'uso di una subnet. Si vogliono usare gli elenchi di controllo di accesso basati sulla rete virtuale e sulla subnet anziché la regola del firewall IP.

Dopo che l'endpoint di servizio per l'account di Azure Cosmos è stato attivato per una subnet, le richieste vengono inviate con l'origine contenente i dati della rete virtuale e della subnet anziché l'indirizzo IP pubblico. Tali richieste non corrispondono quindi a un filtro IP. La commutazione dell'origine si verifica per tutti gli account di Azure Cosmos a cui si accede dalla subnet con l'endpoint di servizio abilitato. Per evitare tempi di inattività, seguire questa procedura:

1. Ottenere le proprietà dell'account di Azure Cosmos eseguendo il cmdlet seguente:

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Cosmos account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Inizializzare le variabili per usarle successivamente. Configurare tutte le variabili della definizione di account esistente. Aggiungere l'elenco di controllo di accesso della rete virtuale a tutti gli account di Azure Cosmos a cui si accede dalla subnet con il contrassegno `ignoreMissingVNetServiceEndpoint`.  

   Se sono presenti più posizioni, è necessario aggiungere gli elenchi come parte della matrice. In questo passaggio si configura anche l'endpoint di servizio della rete virtuale impostando la variabile "accountVNETFilterEnabled" su "True". Questo valore viene assegnato in un secondo momento al parametro "isVirtualNetworkFilterEnabled".

   ```powershell
   $locations = @(@{})

   <# If you have read regions in addition to a write region, use the following code to set the $locations variable instead.

   $locations = @(@{"locationName"="<Write location>"; 
              "failoverPriority"=0}, 
            @{"locationName"="<Read location>"; 
               "failoverPriority"=1}) #>

   $consistencyPolicy = @{}
   $cosmosDBProperties = @{}

   $locations[0]['failoverPriority'] = $cosmosDBConfiguration.Properties.failoverPolicies.failoverPriority
   $locations[0]['locationName'] = $cosmosDBConfiguration.Properties.failoverPolicies.locationName
   $consistencyPolicy = $cosmosDBConfiguration.Properties.consistencyPolicy
   $accountVNETFilterEnabled = $True
   $subnetID = “Subnet ARM URL” e.g "/subscriptions/f7ddba26-ab7b-4a36-a2fa-7d01778da30b/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/subnet1"

   $virtualNetworkRules = @(@{"id"=$subnetID, "ignoreMissingVNetServiceEndpoint"="True"})
   $databaseAccountOfferType = $cosmosDBConfiguration.Properties.databaseAccountOfferType
   ```

1. Aggiornare le proprietà dell'account di Azure Cosmos con la nuova configurazione eseguendo i cmdlet seguenti:

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource `
    -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
    -ApiVersion $apiVersion `
    -ResourceGroupName $rgName `
    -Name $acctName -Properties $CosmosDBProperties
   ```

1. Ripetere i passaggi da 1 a 3 per tutti gli account di Azure Cosmos accessibili dalla subnet.

1.  Attendere 15 minuti e quindi aggiornare la subnet per abilitare l'endpoint di servizio.

1.  Abilitare l'endpoint di servizio per una subnet esistente di una rete virtuale.

   ```powershell
   $rgname= "<Resource group name>"
   $vnName = "<virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

1. Rimuovere regola del firewall IP per la subnet.

## <a name="next-steps"></a>Passaggi successivi

* Per configurare un firewall per Azure Cosmos DB, vedere l'articolo [Supporto del firewall di Azure Cosmos DB](firewall-support.md).

