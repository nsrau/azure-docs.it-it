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
ms.openlocfilehash: 72c2c90f7a71bd9bf251adb492168fa5d2baa60a
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74378683"
---
# <a name="virtual-network-service-endpoints"></a>Endpoint servizio di rete virtuale

Gli endpoint di servizio della rete virtuale (VNet) estendono lo spazio di indirizzi privato della rete virtuale. Gli endpoint estendono anche l'identità della VNet ai servizi di Azure tramite una connessione diretta. Gli endpoint consentono di associare le risorse critiche dei servizi di Azure solo alle proprie reti virtuali. Il traffico che transita dalla rete virtuale al servizio di Azure rimane sempre nella rete backbone di Microsoft Azure.

Questa funzionalità è disponibile per i servizi e le aree di Azure seguenti. La risorsa *Microsoft.\** è racchiusa tra parentesi. Abilitare questa risorsa dal lato subnet durante la configurazione degli endpoint di servizio per il servizio:

**Disponibile a livello generale**

- **[Archiviazione di Azure](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (*Microsoft. storage*): disponibile a livello generale in tutte le aree di Azure.
- **[Database SQL di Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): disponibile a livello generale in tutte le aree di Azure.
- **[Azure SQL data warehouse](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): disponibile a livello generale in tutte le aree di Azure.
- **[Database di Azure per il server PostgreSQL](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): disponibile a livello generale nelle aree di Azure in cui è disponibile il servizio di database.
- **[Database di Azure per il server MySQL](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): disponibile a livello generale nelle aree di Azure in cui è disponibile il servizio di database.
- **[Database di Azure per MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** (*Microsoft. SQL*): disponibile a livello generale nelle aree di Azure in cui è disponibile il servizio di database.
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. AzureCosmosDB*): disponibile a livello generale in tutte le aree di Azure.
- **[Azure Key Vault](../key-vault/key-vault-overview-vnet-service-endpoints.md)** (*Microsoft. Vault*): disponibile a livello generale in tutte le aree di Azure.
- **[Bus di servizio di Azure](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. ServiceBus*): disponibile a livello generale in tutte le aree di Azure.
- **[Hub eventi di Azure](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. EventHub*): disponibile a livello generale in tutte le aree di Azure.
- **[Azure Data Lake Store generazione 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. AzureActiveDirectory*): disponibile a livello generale in tutte le aree di Azure in cui è disponibile ADLS Gen1.
- **[Servizio app Azure](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)** : disponibile a livello generale in tutte le aree di Azure in cui è disponibile il servizio app.

**Anteprima pubblica**

- **[Azure container Registry](../container-registry/container-registry-vnet.md)** (*Microsoft. ContainerRegistry*): anteprima disponibile in tutte le aree di Azure in cui è disponibile Azure container Registry.

Per le notifiche più aggiornate, vedere la pagina [Aggiornamenti della rete virtuale di Azure](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="key-benefits"></a>Vantaggi principali

Gli endpoint di servizio offrono i vantaggi seguenti:

- **Sicurezza migliorata per le risorse dei servizi di Azure: gli spazi degli**indirizzi privati VNet possono sovrapporsi. Non è possibile usare spazi sovrapposti per identificare in modo univoco il traffico originato dal VNet. Gli endpoint di servizio offrono la possibilità di proteggere le risorse dei servizi di Azure nella rete virtuale estendendo l'identità VNet al servizio. Una volta abilitati gli endpoint di servizio nella rete virtuale, è possibile aggiungere una regola della rete virtuale per proteggere le risorse dei servizi di Azure nella rete virtuale. L'aggiunta della regola offre una maggiore sicurezza rimuovendo completamente l'accesso Internet pubblico alle risorse e consentendo il traffico solo dalla rete virtuale.
- **Routing ottimale per il traffico del servizio di Azure dalla rete virtuale**: attualmente, le route nella rete virtuale che forzano il traffico Internet verso le appliance locali e/o virtuali forzano anche il traffico del servizio di Azure ad adottare la stessa route del traffico Internet. Gli endpoint di servizio forniscono il routing ottimale per il traffico di Azure. 

  Gli endpoint instradano sempre il traffico del servizio direttamente dalla rete virtuale al servizio nella rete backbone di Microsoft Azure. Mantenendo il traffico nella rete backbone di Azure è possibile continuare a monitorare e verificare il traffico Internet in uscita dalle reti virtuali, tramite il tunneling forzato, senza conseguenze per il traffico del servizio. Per altre informazioni sulle route definite dall'utente e sul tunneling forzato, vedere [routing del traffico di rete virtuale di Azure](virtual-networks-udr-overview.md).
- **Semplice da configurare con un minor overhead di gestione**: non sono più necessari indirizzi IP pubblici riservati nelle reti virtuali per proteggere le risorse di Azure attraverso il firewall IP. Non sono necessari dispositivi NAT (Network Address Translation) o gateway per configurare gli endpoint del servizio. È possibile configurare gli endpoint di servizio tramite un semplice clic su una subnet. Non è previsto un sovraccarico aggiuntivo per la gestione degli endpoint.

## <a name="limitations"></a>Limitazioni

- La funzionalità è disponibile solo per le reti virtuali distribuite con il modello di distribuzione Azure Resource Manager.
- Gli endpoint vengono abilitati nelle subnet configurate nelle reti virtuali di Azure. Gli endpoint non possono essere usati per il traffico dalla propria sede ai servizi di Azure. Per altre informazioni, vedere [proteggere l'accesso ai servizi di Azure dall'ambiente locale](#secure-azure-services-to-virtual-networks)
- Per SQL di Azure, un endpoint di servizio si applica solo al traffico del servizio di Azure nell'area della rete virtuale. Per archiviazione di Azure, gli endpoint si estendono anche per includere aree abbinate in cui si distribuisce la rete virtuale per supportare l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) e il traffico di archiviazione con ridondanza geografica (GRS). Per altre informazioni, vedere [Aree abbinate di Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
- Per Azure Data Lake Storage (ADLS) gen 1, la funzionalità di integrazione VNet è disponibile solo per le reti virtuali all'interno della stessa area. Si noti anche che l'integrazione della rete virtuale per ADLS Gen1 usa la sicurezza degli endpoint di servizio della rete virtuale tra la rete virtuale e la Azure Active Directory (Azure AD) per generare attestazioni di sicurezza aggiuntive nel token di accesso. Queste attestazioni vengono quindi usate per autenticare la rete virtuale nell'account Data Lake Storage Gen1 e consentire l'accesso. Il tag *Microsoft. AzureActiveDirectory* elencato in servizi che supportano gli endpoint servizio viene usato solo per supportare gli endpoint di servizio per ADLS generazione 1. Azure AD non supporta gli endpoint di servizio in modo nativo. Per ulteriori informazioni sull'integrazione di Azure Data Lake Store generazione 1 VNet, vedere [sicurezza di rete in Azure Data Lake storage Gen1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="secure-azure-services-to-virtual-networks"></a>Proteggere i servizi di Azure in reti virtuali

- Un endpoint servizio di rete virtuale fornisce l'identità della rete virtuale al servizio di Azure. Una volta abilitati gli endpoint di servizio nella rete virtuale, è possibile aggiungere una regola della rete virtuale per proteggere le risorse dei servizi di Azure nella rete virtuale.
- Il traffico del servizio di Azure da una rete virtuale usa attualmente indirizzi IP pubblici come indirizzi IP di origine. Con gli endpoint di servizio, il traffico del servizio passa all'uso di indirizzi privati della rete virtuale come indirizzi IP di origine per l'accesso al servizio di Azure dalla rete virtuale. Questa opzione consente di accedere ai servizi senza che siano necessari gli indirizzi IP pubblici riservati usati nei firewall IP.

   >[!NOTE]
   > Con gli endpoint di servizio, gli indirizzi IP di origine delle macchine virtuali nella subnet per il traffico del servizio passano da indirizzi IPv4 pubblici a indirizzi IPv4 privati. Dopo questo passaggio, le regole del firewall dei servizi di Azure esistenti che usano indirizzi IP pubblici di Azure smettono di funzionare. Prima di configurare endpoint di servizio, assicurarsi che le regole del firewall dei servizi di Azure consentano questo passaggio. Durante la configurazione degli endpoint di servizio, può anche verificarsi un'interruzione temporanea del traffico dei servizi da questa subnet. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>Proteggere l'accesso ai servizi di Azure dall'ambiente locale

  Per impostazione predefinita, le risorse del servizio di Azure protette per le reti virtuali non sono raggiungibili da reti locali. Se si vuole consentire il traffico dall'ambiente locale, è necessario autorizzare anche gli indirizzi IP pubblici (generalmente NAT) dall'ambiente locale o da ExpressRoute. È possibile aggiungere questi indirizzi IP tramite la configurazione del firewall IP per le risorse dei servizi di Azure.

  ExpressRoute: se si usa [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) per il peering pubblico o il peering Microsoft dalla propria sede, è necessario identificare gli indirizzi IP NAT che si sta usando. Per il peering pubblico, ogni circuito ExpressRoute usa due indirizzi IP NAT, per impostazione predefinita, applicato al traffico del servizio di Azure quando il traffico entra nel backbone di rete Microsoft Azure. Per il peering Microsoft, gli indirizzi IP NAT sono forniti dal cliente o forniti dal provider di servizi. Per consentire l'accesso alle risorse del servizio, è necessario consentire questi indirizzi IP pubblici nell'impostazione del firewall IP delle risorse. Per trovare gli indirizzi IP del circuito ExpressRoute di peering pubblico, [aprire un ticket di supporto con ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) tramite il portale di Azure. Per altre informazioni su NAT per il peering pubblico e Microsoft ExpressRoute, vedere [requisiti NAT di ExpressRoute](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

![Associazione di servizi di Azure a reti virtuali](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Configurazione

- Configurare gli endpoint di servizio in una subnet in una rete virtuale. Gli endpoint funzionano con qualsiasi tipo di istanza di calcolo in esecuzione in tale subnet.
- È possibile configurare più endpoint di servizio per tutti i servizi di Azure supportati (ad esempio, archiviazione di Azure o database SQL di Azure) in una subnet.
- Per Database SQL di Azure, le reti virtuali devono trovarsi nella stessa area della risorsa del servizio di Azure. Se si usano account di archiviazione di Azure GRS e RA-GRS, l'account principale deve trovarsi nella stessa area della rete virtuale. Per tutti gli altri servizi, è possibile proteggere le risorse dei servizi di Azure nelle reti virtuali di qualsiasi area. 
- La rete virtuale in cui è configurato l'endpoint può trovarsi nella stessa sottoscrizione della risorsa del servizio di Azure o in una sottoscrizione diversa. Per altre informazioni sulle autorizzazioni necessarie per configurare gli endpoint e proteggere i servizi di Azure, vedere la sezione [Provisioning](#provisioning).
- Per i servizi supportati, è possibile associare risorse nuove o esistenti alle reti virtuali tramite gli endpoint di servizio.

### <a name="considerations"></a>Considerazioni

- Dopo aver abilitato un endpoint di servizio, gli indirizzi IP di origine delle macchine virtuali nell'opzione della subnet. Gli indirizzi IP di origine passano dall'uso di indirizzi IPv4 pubblici all'uso dell'indirizzo IPv4 privato durante la comunicazione con il servizio da tale subnet. Eventuali connessioni TCP aperte per il servizio vengono chiuse durante questo passaggio. Verificare che non siano in esecuzione attività critiche quando si abilita o disabilita un endpoint di servizio per un servizio in una subnet. Assicurarsi anche che le applicazioni possano connettersi automaticamente ai servizi di Azure dopo il cambio di indirizzo IP.

  Il cambio di indirizzo IP interessa solo il traffico del servizio dalla propria rete virtuale. Non vi è alcun effetto su altri traffici indirizzati a o dagli indirizzi IPv4 pubblici assegnati alle macchine virtuali. Per i servizi di Azure, se sono presenti regole del firewall che usano indirizzi IP pubblici di Azure, queste regole smetteranno di funzionare con il passaggio agli indirizzi privati della rete virtuale.
- Con gli endpoint di servizio, le voci DNS per i servizi di Azure rimangono invariate oggi e continuano a risolversi in indirizzi IP pubblici assegnati al servizio di Azure.

- Gruppi di sicurezza di rete (NGS) con gli endpoint di servizio:
  - Per impostazione predefinita, gruppi consente il traffico Internet in uscita e consente anche il traffico dalla VNet ai servizi di Azure. Questo traffico continua a funzionare con gli endpoint del servizio così come sono. 
  - Se si vuole negare tutto il traffico Internet in uscita e consentire solo il traffico verso servizi di Azure specifici, è possibile usare i [tag di servizio](security-overview.md#service-tags) in gruppi. È possibile specificare i servizi di Azure supportati come destinazione nelle regole NSG e Azure fornisce anche la manutenzione degli indirizzi IP sottostanti ogni tag. Per altre informazioni, vedere [Tag del servizio di Azure per i gruppi di sicurezza di rete](security-overview.md#service-tags). 

### <a name="scenarios"></a>Scenari

- **Reti virtuali con peering, connesse o multiple**: per associare i servizi di Azure a più subnet in una o più reti virtuali, è possibile abilitare gli endpoint di servizio in modo indipendente in ognuna di queste subnet e associare le risorse dei servizi di Azure a tutte queste subnet.
- **Filtrare il traffico in uscita da una rete virtuale ai servizi di Azure**: se si vuole controllare o filtrare il traffico inviato a un servizio di Azure da una rete virtuale, è possibile distribuire un'appliance virtuale di rete all'interno della rete virtuale. Sarà quindi possibile applicare gli endpoint di servizio alla subnet in cui è distribuita l'appliance virtuale di rete e associare le risorse del servizio di Azure solo a questa subnet. Questo scenario può essere utile se si vuole usare il filtro dell'appliance virtuale di rete per limitare l'accesso ai servizi di Azure dalla rete virtuale solo a risorse di Azure specifiche. Per altre informazioni, vedere il [traffico in uscita con le appliance virtuali di rete](/azure/architecture/reference-architectures/dmz/nva-ha).
- **Protezione delle risorse di Azure per i servizi distribuiti direttamente in reti virtuali**: è possibile distribuire direttamente diversi servizi di Azure in subnet specifiche in una rete virtuale. È possibile associare le risorse dei servizi di Azure a subnet di [servizi gestiti](virtual-network-for-azure-services.md), configurando un endpoint di servizio nella subnet del servizio gestito.
- **Traffico del disco da una macchina virtuale di Azure**: il traffico del disco della macchina virtuale per i dischi gestiti e non gestiti non è influenzato dagli endpoint del servizio che instradano le modifiche per archiviazione di Azure. Questo traffico include diskIO, oltre a montare e smontare. È possibile limitare l'accesso REST ai BLOB di pagine per selezionare le reti tramite gli endpoint di servizio e [le regole di rete di archiviazione di Azure](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

### <a name="logging-and-troubleshooting"></a>Registrazione e risoluzione dei problemi

Una volta configurati gli endpoint di servizio per un servizio specifico, verificare che la route dell'endpoint di servizio sia attiva da: 
 
- Convalida dell'indirizzo IP di origine di qualsiasi richiesta di servizio nella diagnostica del servizio. Tutte le nuove richieste con gli endpoint di servizio mostrano l'indirizzo IP di origine della richiesta come indirizzo IP privato della rete virtuale, assegnato al client che effettua la richiesta dalla rete virtuale. Senza l'endpoint, questo indirizzo sarà un indirizzo IP pubblico di Azure.
- Visualizzazione delle route valide in qualsiasi interfaccia di rete di una subnet. La route per il servizio:
  - Mostra una route predefinita più specifica per l'intervallo dei prefissi di indirizzo di ogni servizio
  - Ha nextHopType con valore *VirtualNetworkServiceEndpoint*
  - Indica che è attiva una connessione più diretta al servizio rispetto alle route di tunneling forzato

>[!NOTE]
> La route dell'endpoint di servizio sostituisce qualsiasi route BGP o UDR per la corrispondenza dei prefissi di indirizzo di un servizio di Azure. Per ulteriori informazioni, vedere [risoluzione dei problemi con route valide](diagnose-network-routing-problem.md).

## <a name="provisioning"></a>Provisioning

Gli endpoint di servizio possono essere configurati in reti virtuali indipendentemente da un utente con accesso in scrittura a una rete virtuale. Per proteggere le risorse dei servizi di Azure in una VNet, l'utente deve disporre delle autorizzazioni per *Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/Action* per le subnet aggiunte. Per impostazione predefinita, i ruoli di amministratore del servizio incorporati includono questa autorizzazione. Per modificare l'autorizzazione, è possibile creare ruoli personalizzati.

Per altre informazioni sui ruoli predefiniti, vedere [ruoli predefiniti per le risorse di Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Per altre informazioni sull'assegnazione di autorizzazioni specifiche ai ruoli personalizzati, vedere [ruoli personalizzati per le risorse di Azure](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Le reti virtuali e le risorse dei servizi di Azure possono trovarsi nella stessa sottoscrizione o in sottoscrizioni diverse. Se la rete virtuale e le risorse dei servizi di Azure si trovano in sottoscrizioni diverse, le risorse devono trovarsi nello stesso tenant di Active Directory (AD). 

## <a name="pricing-and-limits"></a>Prezzi e limiti

Non sono previsti costi aggiuntivi per l'uso degli endpoint di servizio. Il modello di determinazione dei prezzi corrente per i servizi di Azure (archiviazione di Azure, database SQL di Azure e così via) si applica così com'è oggi.

Non esiste alcun limite al numero totale di endpoint di servizio in una rete virtuale.

Alcuni servizi di Azure, ad esempio gli account di archiviazione di Azure, possono applicare limiti al numero di subnet usate per la protezione della risorsa. Per informazioni dettagliate, vedere la documentazione relativa a diversi servizi nella sezione [passaggi successivi](#next-steps) .

## <a name="vnet-service-endpoint-policies"></a>Criteri dell'endpoint del servizio VNet 

I criteri dell'endpoint del servizio VNet consentono di filtrare il traffico di rete virtuale ai servizi di Azure. Questo filtro consente solo risorse specifiche del servizio di Azure sugli endpoint di servizio. I criteri degli endpoint di servizio forniscono un controllo granulare dell'accesso per il traffico di rete virtuale verso i servizi di Azure. Per ulteriori informazioni, vedere [criteri di endpoint del servizio rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview).

## <a name="faqs"></a>Domande frequenti

Per domande frequenti, vedere [domande frequenti sull'endpoint di servizio di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints).

## <a name="next-steps"></a>Passaggi successivi

- [Configurare gli endpoint del servizio rete virtuale](tutorial-restrict-network-access-to-resources.md)
- [Proteggere un account di archiviazione di Azure in una rete virtuale](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Proteggere un database SQL di Azure in una rete virtuale](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Proteggere un Azure SQL Data Warehouse a una rete virtuale](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Integrazione di servizi di Azure nelle reti virtuali](virtual-network-for-azure-services.md)
- [Criteri degli endpoint servizio di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview).
- [Modello di Azure Resource Manager](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)

