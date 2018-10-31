---
title: Proteggere l'accesso a un account Azure Cosmos DB usando l'endpoint di servizio di Rete virtuale di Azure | Microsoft Docs
description: Questo documento descrive i passaggi necessari per configurare l'endpoint di servizio di Rete virtuale per Azure Cosmos DB.
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: govindk
ms.openlocfilehash: 6d9d05a56376c07013fdea1c94b0a3262d2397c2
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50026297"
---
# <a name="secure-access-to-an-azure-cosmos-db-account-by-using-azure-virtual-network-service-endpoint"></a>Proteggere l'accesso a un account Azure Cosmos DB usando l'endpoint di servizio di Rete virtuale di Azure

Gli account Azure Cosmos DB possono essere configurati per consentire l'accesso solo da una subnet specifica di Rete virtuale di Azure. L'abilitazione di un [endpoint di servizio](../virtual-network/virtual-network-service-endpoints-overview.md) per Azure CosmosDB da una rete virtuale e la relativa subnet assicura che il traffico venga gestito sulla route più efficiente e sicura ad Azure Cosmos DB.  

Azure Cosmos DB è un servizio di database multimodello e distribuito a livello globale. È possibile replicare i dati presenti nell'account Azure Cosmos DB in più aree. Quando Azure Cosmos DB viene configurato con un endpoint di servizio di rete virtuale, ogni rete virtuale consente l'accesso dai propri indirizzi IP appartenenti alla subnet specifica. L'immagine seguente mostra Azure Cosmos DB con l'endpoint di servizio della rete virtuale abilitato:

![architettura dell'endpoint di servizio della rete virtuale](./media/vnet-service-endpoint/vnet-service-endpoint-architecture.png)

Dopo la configurazione dell'account Azure Cosmos DB con un endpoint di servizio della rete virtuale è possibile eseguire l'accesso solo dalla subnet specificata e viene rimosso completamente l'accesso pubblico/a Internet. Per informazioni dettagliate sugli endpoint di servizio, vedere l'articolo [Endpoint del servizio Rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md) di Azure.

## <a name="configure-service-endpoint-by-using-azure-portal"></a>Configurare l'endpoint di servizio tramite il portale di Azure
### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Configurare l'endpoint di servizio per una rete virtuale e una subnet Azure esistenti

1. Dal pannello **Tutte le risorse** individuare la rete virtuale per cui si vuole configurare l'endpoint di servizio per Azure Cosmos DB. Passare al pannello **Endpoint servizio** e assicurarsi che la subnet della rete virtuale sia stata abilitata per l'endpoint di servizio "Azure.CosmosDB".  

   ![Confermare l'endpoint di servizio abilitato](./media/vnet-service-endpoint/confirm-service-endpoint-enabled.png)

2. Nel pannello **Tutte le risorse** individuare l'account Azure Cosmos DB da proteggere.  

3. Prima di abilitare l'endpoint di servizio della rete virtuale, copiare le informazioni sul firewall IP associate all'account Azure Cosmos DB per un utilizzo futuro. Dopo avere configurato l'endpoint di servizio, è possibile riabilitare il firewall IP.  

4. Selezionare **Firewall e reti virtuali** dal menu delle impostazioni e scegliere di consentire l'accesso da **Reti selezionate**.  

3. Per concedere l'accesso a una subnet della rete virtuale esistente, in Reti virtuali, selezionare **Add existing Azure virtual network** (Aggiungi rete virtuale di Azure esistente).  

4. Selezionare la **sottoscrizione** da cui si vuole aggiungere la rete virtuale di Azure. Selezionare le **reti virtuali** e le **subnet** di Azure a cui si vuole fornire l'accesso al proprio account Azure Cosmos DB. Selezionare quindi **Abilita** per abilitare le reti selezionate con gli endpoint di servizio per "Microsoft.AzureCosmosDB". Al termine selezionare **Aggiungi**.  

   ![Selezionare la rete virtuale e la subnet](./media/vnet-service-endpoint/choose-subnet-and-vnet.png)

   > [!NOTE]
   > Se in precedenza per le reti virtuali e le subnet Azure selezionate non è stato configurato un endpoint di servizio per Azure Cosmos DB, è possibile configurarlo nel corso di questa operazione. L'abilitazione dell'accesso richiede fino a 15 minuti. È molto importante disabilitare il firewall IP dopo avere preso nota dei contenuti dell'elenco di controllo di accesso del firewall per riabilitarli in seguito. 

   ![rete virtuale e subnet configurate correttamente](./media/vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

Ora l'account Azure Cosmos DB consentirà solo il traffico proveniente da questa subnet scelta. Se in precedenza è stato abilitato il firewall IP, riabilitarlo usando le informazioni precedenti.

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Configurare l'endpoint di servizio per una rete virtuale e una subnet Azure nuove

1. Nel pannello **Tutte le risorse** individuare l'account Azure Cosmos DB da proteggere.  

   > [!NOTE]
   > Se è stato configurato un firewall IP per l'account Azure Cosmos DB, annotare la configurazione del firewall, rimuovere il firewall IP e quindi abilitare l'endpoint di servizio. Se si abilita l'endpoint di servizio senza disabilitare il firewall, il traffico proveniente da tale intervallo IP perderà l'identità IP virtuale e verrà eliminato con un messaggio di errore relativo al filtro IP. Per evitare questo errore, è quindi consigliabile disabilitare sempre le regole del firewall, copiarle, abilitare l'endpoint di servizio dalla subnet e infine aggiungere l'elenco di controllo di accesso alla subnet da Cosmos DB. Dopo avere configurato l'endpoint di servizio e avere aggiunto l'elenco di controllo di accesso, è possibile riabilitare il firewall IP, se necessario.

2. Prima di abilitare l'endpoint di servizio della rete virtuale, copiare le informazioni sul firewall IP associate all'account Azure Cosmos DB per un utilizzo futuro. Dopo avere configurato l'endpoint di servizio, è possibile riabilitare il firewall IP.  

3. Selezionare **Firewall e reti virtuali di Azure** dal menu delle impostazioni e scegliere di consentire l'accesso da **Reti selezionate**.  

4. Per concedere l'accesso a una nuova rete virtuale di Azure, in Reti virtuali selezionare **Aggiungi nuova rete virtuale**.  

5. Fornire i dettagli necessari per creare una nuova rete virtuale e quindi selezionare Crea. La subnet verrà creata con un endpoint di servizio per "Microsoft.AzureCosmosDB" abilitato.

   ![Selezionare la rete virtuale e la subnet per la nuova rete virtuale](./media/vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

## <a name="allow-access-from-azure-portal"></a>Consentire l'accesso dal portale di Azure

Dopo l'abilitazione degli endpoint di servizio di Rete virtuale di Azure per l'account di database Azure Cosmos DB, l'accesso dal portale o dagli altri servizi Azure è disabilitato per impostazione predefinita. L'accesso all'account di database Azure Cosmos DB dai computer all'esterno della subnet configurata viene bloccato, incluso l'accesso dal portale.

![Consentire l'accesso dal portale:](./media/vnet-service-endpoint/allow-access-from-portal.png)

Se l'account Azure Cosmos DB viene usato da altri servizi Azure come Ricerca di Azure o viene eseguito l'accesso da Analisi di flusso di Azure o Power BI, consentire l'accesso selezionando **Consenti l'accesso a Servizi di Azure**.

Per verificare che si disponga dell'accesso alla metrica di Azure Cosmos DB dal portale, è necessario abilitare le opzioni **Consenti l'accesso al portale di Azure**. Per altre informazioni su queste opzioni, vedere le sezioni [connessioni dal portale di Azure](firewall-support.md#connections-from-the-azure-portal) e [connessioni dai servizi di Azure PaaS](firewall-support.md#connections-from-global-azure-datacenters-or-azure-paas-services). Dopo avere selezionato l'accesso, selezionare **Salva** per salvare le impostazioni.

## <a name="remove-a-virtual-network-or-subnet"></a>Rimuovere una rete virtuale o una subnet 

1. Dal pannello **Tutte le risorse** individuare l'account Azure Cosmos DB per cui sono stati assegnati gli endpoint di servizio.  

2. Selezionare **Firewall e reti virtuali** dal menu delle impostazioni.  

3. Per rimuovere una regola della rete virtuale o della subnet, selezionare "..." accanto alla rete virtuale o alla subnet quindi selezionare **Rimuovi**.

   ![Rimuovere una rete virtuale](./media/vnet-service-endpoint/remove-a-vnet.png)

4.  Fare clic su **Salva** per applicare le modifiche.

## <a name="configure-service-endpoint-by-using-azure-powershell"></a>Configurare l'endpoint di servizio usando Azure PowerShell 

Usare la procedura seguente per configurare l'endpoint di servizio per un account Azure Cosmos DB usando Azure PowerShell:  

1. Installare la versione più aggiornata di [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) ed [effettuare l'accesso](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  Annotare le impostazioni del firewall IP ed eliminare completamente il firewall IP prima di abilitare l'endpoint di servizio per l'account.

  > [!NOTE]
  > Se è stato configurato un firewall IP per l'account Azure Cosmos DB, annotare la configurazione del firewall, rimuovere il firewall IP e quindi abilitare l'endpoint di servizio. Se si abilita l'endpoint di servizio senza disabilitare il firewall, il traffico proveniente da tale intervallo IP perderà l'identità IP virtuale e verrà eliminato con un messaggio di errore relativo al filtro IP. Per evitare questo errore, è quindi consigliabile disabilitare sempre le regole del firewall, copiarle, abilitare l'endpoint di servizio dalla subnet e infine aggiungere l'elenco di controllo di accesso alla subnet da Cosmos DB. Dopo avere configurato l'endpoint di servizio e avere aggiunto l'elenco di controllo di accesso, è possibile riabilitare il firewall IP, se necessario.

2. Prima di abilitare l'endpoint di servizio della rete virtuale, copiare le informazioni sul firewall IP associate all'account Azure Cosmos DB per un utilizzo futuro. Dopo avere configurato l'endpoint di servizio, riabilitare il firewall IP.  

3. Abilitare l'endpoint di servizio per una subnet esistente di una rete virtuale.  

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

4. Preparare l'abilitazione dell'elenco ACL per l'account Cosmos DB verificando che la rete virtuale e la subnet dispongano dell'endpoint di servizio abilitato per Azure Cosmos DB.

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName  -ResourceGroupName $rgName
   ```

5. Ottenere le proprietà dell'account Azure Cosmos DB eseguendo il cmdlet seguente:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

6. Inizializzare le variabili per un utilizzo successivo. Configurare tutte le variabili dalla definizione di account esistente; se si dispone di più percorsi, è necessario aggiungerli come parte della matrice. In questo passaggio si configura anche l'endpoint di servizio della rete virtuale impostando la variabile "accountVNETFilterEnabled" su "True". Questo valore viene assegnato in un secondo momento al parametro "isVirtualNetworkFilterEnabled".  

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

7. Aggiornare le proprietà dell'account Azure Cosmos DB con la nuova configurazione eseguendo i cmdlet seguenti: 

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

8. Eseguire il comando seguente per verificare che l'account Azure Cosmos DB venga aggiornato con l'endpoint di servizio della rete virtuale configurato nel passaggio precedente:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a name="add-virtual-network-service-endpoint-for-an-azure-cosmos-db-account-that-has-ip-firewall-enabled"></a>Aggiungere l'endpoint di servizio della rete virtuale per un account Azure Cosmos DB con il firewall IP abilitato

1. Disabilitare prima l'accesso con il firewall IP all'account Azure Cosmos DB.  

2. Abilitare l'endpoint di rete virtuale per l'account Azure Cosmos DB usando uno dei metodi descritti nelle sezioni precedenti.  

3. Abilitare nuovamente l'accesso al firewall IP. 

## <a name="test-the-access"></a>Testare l'accesso

Per controllare se gli endpoint di servizio per Azure Cosmos DB sono stati configurati come previsto, usare la procedura seguente:

* Configurare due subnet in una rete virtuale come front-end e back-end, abilitare l'endpoint di servizio Cosmos DB per la subnet back-end.  

* Abilitare l'accesso nell'account Cosmos DB per la subnet back-end.  

* Creare due macchine virtuali: una nella subnet back-end e un'altra nella subnet front-end.  

* Provare l'accesso all'account Azure Cosmos DB da entrambe le macchine virtuali. Dovrebbe essere possibile connettersi dalla macchina virtuale creata nella subnet back-end, ma non da quella creata nella subnet front-end. La richiesta genererà un errore 404 quando si tenta la connessione dalla subnet front-end confermando che l'accesso ad Azure Cosmos DB è protetto tramite l'endpoint di servizio della rete virtuale. La macchina nella subnet back-end sarà in grado di funzionare correttamente.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="what-happens-when-you-access-an-azure-cosmos-db-account-that-has-virtual-network-access-control-list-acl-enabled"></a>Cosa accade quando si accede a un account Azure Cosmos DB con un elenco di controllo di accesso (ACL) di rete virtuale abilitato?  

Viene restituito l'errore HTTP 404.  

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-cosmos-db-account-in-another-region-for-example-if-azure-cosmos-db-account-is-in-west-us-or-east-us-and-virtual-networks-are-in-multiple-regions-can-the-virtual-network-access-azure-cosmos-db"></a>Le subnet di una rete virtuale create in aree diverse sono in grado di accedere a un account Azure Cosmos DB in un'altra area? La rete virtuale può accedere ad Azure Cosmos DB se, ad esempio, l'account Azure Cosmos DB si trova negli Stati Uniti occidentali o negli Stati Uniti orientali e le reti virtuali si trovano in più aree?  

Sì, le reti virtuali create in aree diverse possono accedere con la nuova funzionalità. 

### <a name="can-an-azure-cosmos-db-account-have-both-virtual-network-service-endpoint-and-a-firewall"></a>Un account Azure Cosmos DB può avere sia un endpoint di servizio della rete virtuale che un firewall?  

Sì, l'endpoint di servizio della rete virtuale e un firewall possono coesistere. In generale è necessario assicurarsi che l'accesso al portale sia sempre abilitato prima di configurare un endpoint di servizio della rete virtuale per consentire la visualizzazione delle metriche associate al contenitore.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>È possibile accettare le connessioni nei datacenter di Azure pubblici quando viene abilitato l'accesso all'endpoint del servizio per Azure Cosmos DB?  

Questa operazione è necessaria solo quando si vuole consentire l'accesso all'account Azure Cosmos DB da altri servizi proprietari Azure come Azure Data Factory, Ricerca di Azure o qualsiasi servizio distribuito in una specifica area di Azure.

### <a name="how-many-virtual-network-service-endpoints-are-allowed-for-azure-cosmos-db"></a>Quanti endpoint di servizio di rete virtuale sono consentiti per Azure Cosmos DB?  

Sono consentiti 64 endpoint di servizio di rete virtuale per un account Azure Cosmos DB.

### <a name="what-is-the-relationship-between-service-endpoint-and-network-security-group-nsg-rules"></a>Qual è la relazione tra l'endpoint di servizio e le regole del gruppo di sicurezza di rete (NSG)?  

Le regole NSG in Azure Cosmos DB consentono di limitare l'accesso a uno specifico intervallo di indirizzi IP di Azure Cosmos DB. Se si vuole consentire l'accesso a un'istanza di Azure Cosmos DB presente in un'[area](https://azure.microsoft.com/global-infrastructure/regions/) specifica, è possibile specificare l'area nel formato seguente: 

    AzureCosmosDB.<region name>

Per altre informazioni sui tag NSG, vedere l'articolo sui [tag di servizio delle reti virtuali](../virtual-network/security-overview.md#service-tags). 
  
### <a name="what-is-relationship-between-an-ip-firewall-and-virtual-network-service-endpoint-capability"></a>Qual è la relazione tra un firewall IP e la funzionalità di endpoint di servizio della rete virtuale?  

Queste due funzionalità si completano reciprocamente per assicurare l'isolamento degli asset Azure Cosmos DB e la protezione. L'utilizzo del firewall IP garantisce che gli indirizzi IP statici possano accedere all'account Azure Cosmos DB.  

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gatewayvpn-or-express-route-gateway-access-azure-cosmos-db-account"></a>L'indirizzo IP del dispositivo locale che è connesso tramite il gateway di rete virtuale (VPN) di Azure o il gateway ExpressRoute può accedere a un account Azure DB Cosmos?  

Per accedere all'account Azure Cosmos DB, l'indirizzo IP del dispositivo locale o l'intervallo di indirizzi IP deve essere aggiunto all'elenco di IP statici.  

### <a name="what-happens-if-you-delete-a-virtual-network-that-has-service-endpoint-setup-for-azure-cosmos-db"></a>Cosa accade se si elimina una rete virtuale con un endpoint di servizio configurato per Azure Cosmos DB?  

Quando viene eliminata una rete virtuale, le informazioni ACL associate con questo Azure Cosmos DB vengono eliminate e si rimuove l'interazione tra la rete virtuale e l'account Azure Cosmos DB. 

### <a name="what-happens-if-an-azure-cosmos-db-account-that-has-virtual-network-service-endpoint-enabled-is-deleted"></a>Cosa accade se si elimina un account Azure Cosmos DB con un endpoint di servizio della rete virtuale abilitato?

I metadati associati all'account Azure Cosmos DB specifico vengono eliminati dalla subnet. È responsabilità dell'utente finale eliminare la subnet e la rete virtuale usati.

### <a name="can-i-use-a-peered-virtual-network-to-create-service-endpoint-for-azure-cosmos-db"></a>È possibile usare una rete virtuale con peering per creare un endpoint di servizio per Azure Cosmos DB?  

No, solo la rete virtuale diretta e le relative subnet possono creare gli endpoint di servizio per Azure Cosmos DB.

### <a name="what-happens-to-the-source-ip-address-of-resource-like-virtual-machine-in-the-subnet-that-has-azure-cosmos-db-service-endpoint-enabled"></a>Cosa accade all'indirizzo IP di origine della risorsa come la macchina virtuale nella subnet con un endpoint di servizio Azure Cosmos DB abilitato?

Quando sono abilitati gli endpoint di servizio della rete virtuale, gli indirizzi IP di origine delle risorse nella subnet della rete virtuale passeranno dall'utilizzo di indirizzi IPV4 pubblici agli indirizzi privati di Rete virtuale di Azure per il traffico verso Azure Cosmos DB.

### <a name="does-azure-cosmos-db-reside-in-the-azure-virtual-network--provided-by-the-customer"></a>Azure Cosmos DB si trova nella rete virtuale di Azure fornita dal cliente?  

Azure Cosmos DB è un servizio multi-tenant con un indirizzo IP pubblico. Quando si limita l'accesso a una subnet di una Rete virtuale di Azure usando la funzionalità di endpoint di servizio, l'accesso viene limitato per l'account Azure Cosmos DB tramite la rete virtuale di Azure specificata e la relativa subnet.  L'account Azure Cosmos DB non risiede in tale rete virtuale di Azure. 

### <a name="what-if-anything-will-be-logged-in-log-analytics-if-it-is-enabled"></a>Cosa accade se un elemento qualsiasi viene registrato in Log Analytics se è abilitato?  

Azure DB Cosmos effettuerà il push dei log con l'indirizzo IP (senza l'ultimo ottetto) con lo stato 403 per la richiesta bloccata dall'elenco ACL.  

## <a name="next-steps"></a>Passaggi successivi
Per configurare un firewall per Azure Cosmos DB, vedere l'articolo [Supporto del firewall di Azure Cosmos DB](firewall-support.md).

