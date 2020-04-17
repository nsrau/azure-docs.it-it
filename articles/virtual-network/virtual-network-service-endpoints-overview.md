---
title: Endpoint servizio di rete virtuale di Azure
titlesuffix: Azure Virtual Network
description: Informazioni su come abilitare l'accesso diretto alle risorse di Azure da una rete virtuale tramite gli endpoint del servizio.
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2019
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: b75e0c1e53f1e00579de73897197cdd2f14d79af
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455411"
---
# <a name="virtual-network-service-endpoints"></a>Endpoint servizio di rete virtuale

Gli endpoint del servizio di rete virtuale (VNet) estendono lo spazio di indirizzi privata della rete virtuale. Gli endpoint estendono anche l'identità della rete virtuale ai servizi di Azure tramite una connessione diretta. Gli endpoint consentono di associare le risorse critiche dei servizi di Azure solo alle proprie reti virtuali. Il traffico che transita dalla rete virtuale al servizio di Azure rimane sempre nella rete backbone di Microsoft Azure.

Questa funzionalità è disponibile per i servizi e le aree di Azure seguenti. La risorsa *Microsoft.\* * è tra parentesi. Abilitare questa risorsa dal lato della subnet durante la configurazione degli endpoint del servizio per il servizio:Enable this resource from the subnet side while configuring service endpoints for your service:

**Disponibile a livello generale**

- **[Archiviazione di Azure](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (*Microsoft.Storage):* disponibile in genere in tutte le aree di Azure.Azure Storage ( Microsoft.Storage ): Generally available in all Azure regions.
- **[Database SQL di Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.Sql):* disponibile in genere in tutte le aree di Azure.Azure SQL Database ( Microsoft.Sql ): Generally available in all Azure regions.
- **[Azure SQL Data Warehouse](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.Sql):* disponibile in genere in tutte le aree di Azure.Azure SQL Data Warehouse ( Microsoft.Sql ): Generally available in all Azure regions.
- **[Database di Azure per il server PostgreSQL](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql):* disponibile in genere nelle aree di Azure in cui è disponibile il servizio di database.
- **[Database di Azure per il server MySQL](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.Sql):* disponibili in genere nelle aree di Azure in cui è disponibile il servizio di database.
- **[Database di Azure per MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** (*Microsoft.Sql):* disponibile in genere nelle aree di Azure in cui è disponibile il servizio di database.
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureCosmosDB):* disponibile in genere in tutte le aree di Azure.Azure Cosmos DB ( Microsoft.AzureCosmosDB ): Generally available in all Azure regions.
- **[Archiviazione delle chiavi di Azure](../key-vault/general/overview-vnet-service-endpoints.md)** (*Microsoft.KeyVault):* disponibile in genere in tutte le aree di Azure.Azure Key Vault ( Microsoft.KeyVault ): Generally available in all Azure regions.
- **[Bus di servizio di Azure](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.ServiceBus):* disponibile in genere in tutte le aree di Azure.Azure Service Bus ( Microsoft.ServiceBus ): Generally available in all Azure regions.
- Hub eventi di Azure (*Microsoft.EventHub):* disponibili in genere in tutte le aree di **[Azure.Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** ( Microsoft.EventHub ): Generally available in all Azure regions.
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.AzureActiveDirectory):* disponibile in genere in tutte le aree di Azure in cui è disponibile ADLS Gen1.Azure Data Lake Store Gen 1 ( Microsoft.AzureActiveDirectory ): Generally available in all Azure regions where ADLS Gen1 is available.
- **[Servizio app di Azure:](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)** disponibile in genere in tutte le aree di Azure in cui è disponibile il servizio app.

**Anteprima pubblica**

- **[Registro contenitori di Azure](../container-registry/container-registry-vnet.md)** (*Microsoft.ContainerRegistry):* anteprima disponibile in tutte le aree di Azure in cui è disponibile il Registro di sistema del contenitore di Azure.Azure Container Registry ( Microsoft.ContainerRegistry ): Preview available in all Azure regions where Azure Container Registry is available.

Per le notifiche più aggiornate, controllare la pagina Aggiornamenti della rete virtuale di Azure.For the most up-to-date [notifications,](https://azure.microsoft.com/updates/?product=virtual-network) check the Azure Virtual Network updates page.

## <a name="key-benefits"></a>Vantaggi principali

Gli endpoint di servizio offrono i vantaggi seguenti:

- **Sicurezza migliorata per le risorse del servizio di Azure:** gli spazi di indirizzi privati della rete virtuale possono sovrapporsi. Non è possibile utilizzare spazi sovrapposti per identificare in modo univoco il traffico proveniente dalla rete virtuale. Gli endpoint del servizio offrono la possibilità di proteggere le risorse del servizio di Azure alla rete virtuale estendendo l'identità della rete virtuale al servizio. Dopo aver abilitato gli endpoint del servizio nella rete virtuale, è possibile aggiungere una regola di rete virtuale per proteggere le risorse del servizio di Azure alla rete virtuale. L'aggiunta di regole garantisce una maggiore sicurezza rimuovendo completamente l'accesso pubblico a Internet alle risorse e consentendo il traffico solo dalla rete virtuale.
- **Routing ottimale per**il traffico del servizio di Azure dalla rete virtuale: oggi tutte le route della rete virtuale che forzano il traffico Internet verso le appliance locali e/o virtuali forzano inoltre il traffico del servizio di Azure a prendere la stessa strada del traffico Internet.Optimal routing for Azure service traffic from your virtual network : Today, any routes in your virtual network that force internet traffic to your on-premises and/or virtual appliances also force Azure service traffic to take the same route as the internet traffic. Gli endpoint di servizio forniscono il routing ottimale per il traffico di Azure. 

  Gli endpoint instradano sempre il traffico del servizio direttamente dalla rete virtuale al servizio nella rete backbone di Microsoft Azure. Mantenendo il traffico nella rete backbone di Azure è possibile continuare a monitorare e verificare il traffico Internet in uscita dalle reti virtuali, tramite il tunneling forzato, senza conseguenze per il traffico del servizio. Per altre informazioni sulle route definite dall'utente e sul tunneling forzato, vedere Routing del traffico di rete virtuale di Azure.For more information about user-defined routes and forced-tunneling, see [Azure virtual network traffic routing](virtual-networks-udr-overview.md).
- **Semplice da configurare con un minor overhead di gestione**: non sono più necessari indirizzi IP pubblici riservati nelle reti virtuali per proteggere le risorse di Azure attraverso il firewall IP. Non sono necessari dispositivi NAT (Network Address Translation) o gateway per configurare gli endpoint del servizio. È possibile configurare gli endpoint del servizio con un semplice clic su una subnet. Non sono previsti costi aggiuntivi per la gestione degli endpoint.

## <a name="limitations"></a>Limitazioni

- La funzionalità è disponibile solo per le reti virtuali distribuite con il modello di distribuzione Azure Resource Manager.
- Gli endpoint vengono abilitati nelle subnet configurate nelle reti virtuali di Azure. Gli endpoint non possono essere usati per il traffico dai locali ai servizi di Azure.Endpoints can't be used for traffic from your premises to Azure services. Per altre informazioni, vedere [Proteggere l'accesso al servizio di Azure da localeFor](#secure-azure-services-to-virtual-networks) more information, see Secure Azure service access from on-premises
- Per SQL di Azure, un endpoint di servizio si applica solo al traffico del servizio di Azure nell'area della rete virtuale. Per Archiviazione di Azure, gli endpoint si estendono anche per includere aree abbinate in cui si distribuisce la rete virtuale per supportare il traffico di archiviazione con ridondanza geografica di accesso in lettura (RA-GRS) e di archiviazione con ridondanza geografica (GRS). Per altre informazioni, vedere [Aree abbinate di Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
- Per Azure Data Lake Storage (ADLS) Gen 1, la funzionalità di integrazione della rete virtuale è disponibile solo per le reti virtuali all'interno della stessa area. Si noti inoltre che l'integrazione della rete virtuale per ADLS Gen1 usa la sicurezza dell'endpoint del servizio di rete virtuale tra la rete virtuale e Azure Active Directory (Azure AD) per generare attestazioni di sicurezza aggiuntive nel token di accesso. Queste attestazioni vengono quindi usate per autenticare la rete virtuale nell'account Data Lake Storage Gen1 e consentire l'accesso. Il tag Microsoft.AzureActiveDirectory elencato in servizi che supportano gli endpoint del servizio viene usato solo per supportare gli endpoint del servizio in ADLS Gen 1.The *Microsoft.AzureActiveDirectory* tag listed under services supporting service endpoints is used only for supporting service endpoints to ADLS Gen 1. Azure AD non supporta gli endpoint del servizio in modo nativo. Per altre informazioni sull'integrazione della rete virtuale di Azure Data Lake Store Gen 1, vedere Sicurezza di rete in Archiviazione dati di Azure1.For more information about Azure Data Lake Store Gen 1 VNet integration, see [Network security in Azure Data Lake Storage Gen1.](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="secure-azure-services-to-virtual-networks"></a>Proteggere i servizi di Azure nelle reti virtualiSecure Azure services to virtual networks

- Un endpoint servizio di rete virtuale fornisce l'identità della rete virtuale al servizio di Azure. Dopo aver abilitato gli endpoint del servizio nella rete virtuale, è possibile aggiungere una regola di rete virtuale per proteggere le risorse del servizio di Azure alla rete virtuale.
- Il traffico del servizio di Azure da una rete virtuale usa attualmente indirizzi IP pubblici come indirizzi IP di origine. Con gli endpoint di servizio, il traffico del servizio passa all'uso di indirizzi privati della rete virtuale come indirizzi IP di origine per l'accesso al servizio di Azure dalla rete virtuale. Questa opzione consente di accedere ai servizi senza che siano necessari gli indirizzi IP pubblici riservati usati nei firewall IP.

   >[!NOTE]
   > Con gli endpoint di servizio, gli indirizzi IP di origine delle macchine virtuali nella subnet per il traffico del servizio passano da indirizzi IPv4 pubblici a indirizzi IPv4 privati. Dopo questo passaggio, le regole del firewall dei servizi di Azure esistenti che usano indirizzi IP pubblici di Azure smettono di funzionare. Prima di configurare endpoint di servizio, assicurarsi che le regole del firewall dei servizi di Azure consentano questo passaggio. Durante la configurazione degli endpoint di servizio, può anche verificarsi un'interruzione temporanea del traffico dei servizi da questa subnet. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>Proteggere l'accesso al servizio di Azure da localeSecure Azure service access from on-premises

  Per impostazione predefinita, le risorse del servizio di Azure protette alle reti virtuali non sono raggiungibili dalle reti locali. Se si vuole consentire il traffico dall'ambiente locale, è necessario autorizzare anche gli indirizzi IP pubblici (generalmente NAT) dall'ambiente locale o da ExpressRoute. È possibile aggiungere questi indirizzi IP tramite la configurazione del firewall IP per le risorse del servizio Azure.You can add these IP addresses through the IP firewall configuration for Azure service resources.

  ExpressRoute: se usi [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) per il peering pubblico o il peering Microsoft dai locali, dovrai identificare gli indirizzi IP NAT in uso. Per il peering pubblico, ogni circuito ExpressRoute usa due indirizzi IP NAT, per impostazione predefinita, applicati al traffico del servizio di Azure quando il traffico entra nella backbone di rete di Microsoft Azure.For public peering, each ExpressRoute circuit uses two NAT IP addresses, by default, applied to Azure service traffic when the traffic enters the Microsoft Azure network backbone. Per il peering Microsoft, gli indirizzi IP NAT sono forniti dal cliente o forniti dal provider di servizi.Per consentire l'accesso alle risorse del servizio è necessario autorizzare questi indirizzi IP pubblici nell'impostazione del firewall IP per le risorse.Per trovare gli indirizzi IP del circuito ExpressRoute per il peering pubblico, [aprire un ticket di supporto con ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) tramite il portale di Azure. Per altre informazioni su NAT per ExpressRoute public e Microsoft peering, vedere [Requisiti NAT ExpressRoute.](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)

![Associazione di servizi di Azure a reti virtuali](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Configurazione

- Configurare gli endpoint di servizio in una subnet in una rete virtuale. Gli endpoint funzionano con qualsiasi tipo di istanza di calcolo in esecuzione in tale subnet.
- È possibile configurare più endpoint di servizio per tutti i servizi di Azure supportati (Archiviazione di Azure o Database SQL di Azure, ad esempio) in una subnet.
- Per Database SQL di Azure, le reti virtuali devono trovarsi nella stessa area della risorsa del servizio di Azure. Se si usano account di archiviazione di Azure GRS e RA-GRS, l'account principale deve trovarsi nella stessa area della rete virtuale. Per tutti gli altri servizi, è possibile proteggere le risorse del servizio di Azure alle reti virtuali in qualsiasi area. 
- La rete virtuale in cui è configurato l'endpoint può trovarsi nella stessa sottoscrizione della risorsa del servizio di Azure o in una sottoscrizione diversa. Per altre informazioni sulle autorizzazioni necessarie per configurare gli endpoint e proteggere i servizi di Azure, vedere la sezione [Provisioning](#provisioning).
- Per i servizi supportati, è possibile associare risorse nuove o esistenti alle reti virtuali tramite gli endpoint di servizio.

### <a name="considerations"></a>Considerazioni

- Dopo aver abilitato un endpoint del servizio, gli indirizzi IP di origine delle macchine virtuali nella subnet switch. Gli indirizzi IP di origine passano dall'utilizzo di indirizzi IPv4 pubblici all'utilizzo dell'indirizzo IPv4 privato durante la comunicazione con il servizio da tale subnet. Eventuali connessioni TCP aperte per il servizio vengono chiuse durante questo passaggio. Verificare che non siano in esecuzione attività critiche quando si abilita o disabilita un endpoint di servizio per un servizio in una subnet. Assicurarsi anche che le applicazioni possano connettersi automaticamente ai servizi di Azure dopo il cambio di indirizzo IP.

  Il cambio di indirizzo IP interessa solo il traffico del servizio dalla propria rete virtuale. Non c'è alcun impatto su nessun altro traffico indirizzato o dagli indirizzi IPv4 pubblici assegnati alle macchine virtuali. Per i servizi di Azure, se sono presenti regole del firewall che usano indirizzi IP pubblici di Azure, queste regole smetteranno di funzionare con il passaggio agli indirizzi privati della rete virtuale.
- Con gli endpoint di servizio, le voci DNS per i servizi di Azure rimangono oggi invariate e continuano a essere risolte in indirizzi IP pubblici assegnati al servizio di Azure.With service endpoints, DNS entries for Azure services remain as-is today and continue to resolve to public IP addresses assigned to the Azure service.

- Gruppi di sicurezza di rete (NGS) con gli endpoint di servizio:
  - Per impostazione predefinita, i gruppi di sicurezza di rete consentono il traffico Internet in uscita e consentono anche il traffico dalla rete virtuale ai servizi di Azure.By default, NSGs allow outbound internet traffic and also allow traffic from your VNet to Azure services. Questo traffico continua a funzionare con gli endpoint del servizio così com'è. 
  - Se si vuole negare tutto il traffico Internet in uscita e consentire solo il traffico verso specifici servizi di Azure, è possibile farlo usando i [tag di servizio](security-overview.md#service-tags) nei gruppi di sicurezza di rete. È possibile specificare i servizi di Azure supportati come destinazione nelle regole del gruppo di sicurezza di rete e Azure offre anche la manutenzione degli indirizzi IP sottostanti ogni tag. Per altre informazioni, vedere [Tag del servizio di Azure per i gruppi di sicurezza di rete](security-overview.md#service-tags). 

### <a name="scenarios"></a>Scenari

- **Reti virtuali con peering, connesse o multiple**: per associare i servizi di Azure a più subnet in una o più reti virtuali, è possibile abilitare gli endpoint di servizio in modo indipendente in ognuna di queste subnet e associare le risorse dei servizi di Azure a tutte queste subnet.
- **Filtraggio del traffico in uscita da una rete virtuale ai servizi**di Azure: se si vuole esaminare o filtrare il traffico inviato a un servizio di Azure da una rete virtuale, è possibile distribuire un'appliance virtuale di rete all'interno della rete virtuale. Sarà quindi possibile applicare gli endpoint di servizio alla subnet in cui è distribuita l'appliance virtuale di rete e associare le risorse del servizio di Azure solo a questa subnet. Questo scenario può essere utile se si vuole usare il filtro dell'appliance virtuale di rete per limitare l'accesso al servizio di Azure dalla rete virtuale solo a risorse di Azure specifiche. Per altre informazioni, vedere il [traffico in uscita con le appliance virtuali di rete](/azure/architecture/reference-architectures/dmz/nva-ha).
- **Protezione delle risorse di Azure nei servizi distribuiti direttamente nelle reti virtuali:** è possibile distribuire direttamente vari servizi di Azure in subnet specifiche in una rete virtuale. È possibile associare le risorse dei servizi di Azure a subnet di [servizi gestiti](virtual-network-for-azure-services.md), configurando un endpoint di servizio nella subnet del servizio gestito.
- **Traffico su disco da una macchina virtuale**di Azure: il traffico del disco della macchina virtuale per i dischi gestiti e non gestiti non è influenzato dagli endpoint del servizio che routing delle modifiche per Archiviazione di Azure.Disk traffic from an Azure virtual machine : Virtual Machine Disk traffic for managed and unmanaged disks't affected by service endpoints routing changes for Azure Storage. Questo traffico include diskIO, nonché montaggio e smontaggio. È possibile limitare l'accesso REST ai BLOB di pagine per selezionare le reti tramite gli endpoint del servizio e le regole di rete di Archiviazione di Azure.You can limit REST access to page blobs to select networks through service endpoints and [Azure Storage network rules.](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 

### <a name="logging-and-troubleshooting"></a>Registrazione e risoluzione dei problemi

Dopo aver configurato gli endpoint del servizio per un servizio specifico, verificare che la route dell'endpoint del servizio sia attiva: 
 
- Convalida dell'indirizzo IP di origine di qualsiasi richiesta di servizio nella diagnostica del servizio. Tutte le nuove richieste con gli endpoint di servizio mostrano l'indirizzo IP di origine della richiesta come indirizzo IP privato della rete virtuale, assegnato al client che effettua la richiesta dalla rete virtuale. Senza l'endpoint, questo indirizzo sarà un indirizzo IP pubblico di Azure.
- Visualizzazione delle route valide in qualsiasi interfaccia di rete di una subnet. La route per il servizio:
  - Mostra una route predefinita più specifica per l'intervallo dei prefissi di indirizzo di ogni servizio
  - Ha nextHopType con valore *VirtualNetworkServiceEndpoint*
  - Indica che è attiva una connessione più diretta al servizio rispetto a qualsiasi route di tunneling forzato

>[!NOTE]
> La route dell'endpoint di servizio sostituisce qualsiasi route BGP o UDR per la corrispondenza dei prefissi di indirizzo di un servizio di Azure. Per ulteriori informazioni, consultate [Risoluzione dei problemi con route efficaci.](diagnose-network-routing-problem.md)

## <a name="provisioning"></a>Provisioning

Gli endpoint di servizio possono essere configurati nelle reti virtuali in modo indipendente da un utente con accesso in scrittura a una rete virtuale. Per proteggere le risorse del servizio Di Azure in una rete virtuale, l'utente deve disporre dell'autorizzazione per *Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/azione* per le subnet aggiunte. I ruoli di amministratore del servizio predefiniti includono questa autorizzazione per impostazione predefinita. È possibile modificare l'autorizzazione creando ruoli personalizzati.

Per altre informazioni sui ruoli predefiniti, vedere Ruoli predefiniti per le risorse di Azure.For more information about built-in roles, see [Built-in roles for Azure resources.](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Per altre informazioni sull'assegnazione di autorizzazioni specifiche a ruoli personalizzati, vedere Ruoli personalizzati per le risorse di Azure.For more information about assigning specific permissions to custom roles, see [Custom roles for Azure resources.](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

Le reti virtuali e le risorse dei servizi di Azure possono trovarsi nella stessa sottoscrizione o in sottoscrizioni diverse. Se la rete virtuale e le risorse dei servizi di Azure si trovano in sottoscrizioni diverse, le risorse devono trovarsi nello stesso tenant di Active Directory (AD). 

## <a name="pricing-and-limits"></a>Prezzi e limiti

Non sono previsti costi aggiuntivi per l'utilizzo degli endpoint di servizio. Il modello di determinazione dei prezzi corrente per i servizi di Azure (Archiviazione di Azure, Database SQL di Azure e così via) si applica così com'è oggi.

Non esiste alcun limite al numero totale di endpoint del servizio in una rete virtuale.

Alcuni servizi di Azure, ad esempio gli account di archiviazione di Azure, possono applicare limiti al numero di subnet usate per la protezione della risorsa. Per informazioni dettagliate, fare riferimento alla documentazione relativa ai vari servizi nella sezione [Passaggi successivi.](#next-steps)

## <a name="vnet-service-endpoint-policies"></a>Criteri degli endpoint del servizio VNet 

I criteri degli endpoint del servizio VNet consentono di filtrare il traffico di rete virtuale verso i servizi di Azure.VNet service endpoint policies allow you to filter virtual network traffic to Azure services. Questo filtro consente solo risorse specifiche del servizio di Azure sugli endpoint del servizio. I criteri degli endpoint di servizio forniscono un controllo granulare dell'accesso per il traffico di rete virtuale verso i servizi di Azure. Per ulteriori informazioni, vedere [Criteri degli endpoint del servizio](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)di rete virtuale .

## <a name="faqs"></a>Domande frequenti

Per le domande frequenti, vedere Domande frequenti sugli endpoint del [servizio di rete virtuale.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints)

## <a name="next-steps"></a>Passaggi successivi

- [Configurare gli endpoint servizio di rete virtuale](tutorial-restrict-network-access-to-resources.md)
- [Proteggere un account di archiviazione di Azure in una rete virtualeSecure an Azure Storage account to a virtual network](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Proteggere un database SQL di Azure a una rete virtualeSecure an Azure SQL Database to a virtual network](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Proteggere un data warehouse SQL di Azure a una rete virtualeSecure an Azure SQL Data Warehouse to a virtual network](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Integrazione dei servizi di Azure nelle reti virtualiAzure service integration in virtual networks](virtual-network-for-azure-services.md)
- [Criteri degli endpoint servizio di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)
- [Modello di Azure Resource Manager](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)

