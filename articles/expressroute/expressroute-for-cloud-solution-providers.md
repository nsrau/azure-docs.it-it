---
title: Azure ExpressRoute per Cloud Solution Provider | Microsoft Docs
description: Questo articolo fornisce informazioni per i provider di servizi cloud che vogliono incorporare i servizi di Azure e ExpressRoute nelle offerte.
documentationcenter: na
services: expressroute
author: richcar
manager: carmonm
editor: 
ms.assetid: f6c5f8ee-40ba-41a1-ae31-67669ca419a6
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: richcar
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 26c9420c9b8ba1aff6b016c01b8ed51853c91506
ms.contentlocale: it-it
ms.lasthandoff: 12/08/2016


---
# <a name="expressroute-for-cloud-solution-providers-csp"></a>ExpressRoute per Cloud Solution Provider (CSP)
Microsoft fornisce servizi su vasta scala per consentire a rivenditori e distributori tradizionali (CSP) di effettuare rapidamente il provisioning di nuovi servizi e soluzioni per i clienti senza dover investire nello sviluppo di questi nuovi servizi. Per offrire al Cloud Solution Provider (CSP) la possibilità di gestire direttamente questi nuovi servizi, Microsoft fornisce programmi e API che consentono al CSP di gestire le risorse di Microsoft Azure per conto dei clienti. Una di queste risorse è ExpressRoute. ExpressRoute consente al CSP di connettere le risorse esistenti dei clienti ai servizi di Azure. ExpressRoute è un collegamento delle comunicazioni privato a velocità elevata ai servizi in Azure. 

ExpresRoute è costituito da una coppia di circuiti per la disponibilità elevata che vengono collegati a una o più sottoscrizioni di un singolo cliente e non possono essere condivise da più clienti. Ogni circuito deve terminare in router diverso per mantenere la disponibilità elevata.

> [!NOTE]
> Poiché in ExpressRoute esistono limiti di larghezza di banda e di connessione, le implementazioni di grandi dimensioni/complesse richiederanno più circuiti di ExpressRoute per un singolo cliente.
> 
> 

Microsoft Azure fornisce un numero crescente di servizi che è possibile offrire ai clienti.  Per sfruttare al massimo questi servizi, sarà necessario usare le connessioni di ExpressRoute per fornire accesso a bassa latenza a velocità elevata all'ambiente Microsoft Azure.

## <a name="microsoft-azure-management"></a>Gestione di Microsoft Azure
Microsoft fornisce ai CSP le API per gestire le sottoscrizioni dei clienti di Azure consentendo l'integrazione programmatica con i sistemi di gestione dei servizi. Le funzionalità di gestione supportate sono disponibili [qui](https://msdn.microsoft.com/library/partnercenter/dn974944.aspx).

## <a name="microsoft-azure-resource-management"></a>Gestione di risorse di Microsoft Azure
La gestione della sottoscrizione dipende dal contratto stipulato con il cliente. Il CSP può gestire direttamente la creazione e la manutenzione delle risorse oppure il cliente può mantenere il controllo della sottoscrizione di Microsoft Azure e creare le risorse di Azure necessarie. Se il cliente gestisce la creazione delle risorse nella sottoscrizione di Microsoft Azure, userà il modello di "connessione indiretta" o il modello di "connessione diretta". Questi modelli vengono descritti in dettaglio nelle sezioni seguenti.  

### <a name="connect-through-model"></a>Modello di connessione indiretta
![testo alternativo](./media/expressroute-for-cloud-solution-providers/connect-through.png)  

In questo modello il CSP crea una connessione diretta tra il data center e la sottoscrizione di Azure del cliente. La connessione diretta viene stabilita usando ExpressRoute, che connette la rete ad Azure. Il cliente quindi si connette alla rete. Questo scenario prevede che il cliente passi attraverso la rete CSP per accedere ai servizi di Azure. 

Se il cliente ha altre sottoscrizioni di Azure non gestite dall'utente, userà Internet pubblico o la connessione privata per connettersi a tali servizi di cui viene effettuato il provisioning con la sottoscrizione non CSP. 

Si presuppone che il CSP che gestisce i servizi di Azure abbia un archivio identità dei clienti stabilito in precedenza che verrà quindi replicato in Azure Active Directory per la gestione della sottoscrizione CSP con Administrate-On-Behalf-Of (AOBO). I fattori chiave di questo scenario includono dove un determinato partner o provider di servizi ha stabilito una relazione con il cliente, dove il cliente utilizza attualmente i servizi del provider o dove il partner vuole fornire una combinazione di soluzioni ospitate dal provider e ospitate da Azure per garantire flessibilità e rispondere alle richieste dei clienti che non possono essere soddisfatte dal solo CSP. Questo modello è illustrato nella **figura**seguente.

![testo alternativo](./media/expressroute-for-cloud-solution-providers/connect-through-model.png)

### <a name="connect-to-model"></a>Modello di connessione diretta
![testo alternativo](./media/expressroute-for-cloud-solution-providers/connect-to.png)

In questo modello il provider di servizi crea una connessione diretta tra il data center del cliente e la sottoscrizione di Azure di cui viene effettuato il provisioning dal CSP usando ExpressRoute nella rete del cliente (cliente).

> [!NOTE]
> Per ExpressRoute il cliente dovrà creare e gestire il circuito ExpressRoute.  
> 
> 

In questo scenario di connettività il cliente deve connettersi direttamente tramite una rete cliente per accedere alla sottoscrizione di Azure gestita dal CSP, usando una connessione di rete diretta che il cliente crea, di cui è proprietario e che gestisce interamente o in parte. Per questi clienti si presuppone che il provider non abbia attualmente un archivio di identità dei clienti e che il provider presterà assistenza al cliente nella replica dell'archivio di identità corrente in Azure Active Directory per la gestione della sottoscrizione con AOBO. I fattori chiave di questo scenario includono dove un determinato partner o provider di servizi ha stabilito una relazione con il cliente, dove il cliente utilizza attualmente i servizi del provider o dove il partner vuole fornire servizi basati esclusivamente su soluzioni ospitate da Azure senza la necessità di un data center o di un'infrastruttura del provider esistente.

![testo alternativo](./media/expressroute-for-cloud-solution-providers/connect-to-model.png)

La scelta tra queste due opzioni si basa sulle esigenze del cliente e sulla propria esigenza corrente di fornire servizi di Azure. Informazioni dettagliate su questi modelli e sul controllo degli accessi in base al ruolo associato, sulla rete e sugli schemi progettuali delle identità sono disponibili nel collegamenti seguenti:

* **Controllo degli accessi in base al ruolo** : il controllo degli accessi in base al ruolo si basa su Azure Active Directory.  Per altre informazioni sul controllo degli accessi in base al ruolo di Azure, vedere [qui](../active-directory/role-based-access-control-configure.md).
* **Rete** : include diversi argomenti sulla rete in Microsoft Azure.
* **Azure Active Directory (AAD)** : AAD fornisce la gestione delle identità per Microsoft Azure e applicazioni SaaS di terze parti. Per altre informazioni su Azure AD, vedere [qui](https://azure.microsoft.com/documentation/services/active-directory/).  

## <a name="network-speeds"></a>Velocità di rete
ExpressRoute supporta le velocità di rete comprese tra 50 MB/s e 10 GB/s. Ciò consente ai clienti di acquistare la quantità di larghezza di banda di rete necessaria per un ambiente univoco.

> [!NOTE]
> La larghezza di banda di rete può essere aumentata in base alle esigenze senza dover interrompere le comunicazioni, invece per ridurre la velocità di rete, è necessario rimuovere il circuito e ricrearlo a una velocità di rete più bassa.  
> 
> 

ExpressRoute supporta la connessione di più reti virtuali a un solo circuito ExpressRoute per un miglior utilizzo delle connessioni con velocità più elevata. Un solo circuito ExpressRoute può essere condiviso tra più sottoscrizioni di Azure di proprietà dello stesso cliente.

## <a name="configuring-expressroute"></a>Configurazione di ExpressRoute
ExpressRoute può essere configurato per supportare tre tipi di traffico ([domini di routing](#ExpressRoute-routing-domains)) su un solo circuito ExpressRoute. Questo traffico viene separato nel peer Microsoft, nel peer pubblico di Azure e nel peer privato. È possibile scegliere uno o tutti i tipi di traffico da inviare tramite un solo circuito di ExpressRoute o usare più circuiti ExpressRoute a seconda delle dimensioni del circuito ExpressRoute e dell'isolamento richiesto dal cliente. Il comportamento di sicurezza del cliente potrebbe non consentire al traffico pubblico e al traffico privato di passare per lo stesso circuito.

### <a name="connect-through-model"></a>Modello di connessione indiretta
In una configurazione di connessione indiretta l'utente sarà responsabile di tutti i fondamenti della rete per connettere le risorse dei data center dei clienti alle sottoscrizioni ospitate in Azure. Ogni cliente che vuole usare le funzionalità di Azure dovrà avere la propria connessione ExpressRoute, che verrà gestita dall'utente. L'utente userà gli stessi metodi che userebbe il cliente per ottenere il circuito ExpressRoute. L'utente seguirà la stessa procedura indicata nell'articolo [Flussi di lavoro ExpressRoute per provisioning di un circuito e stati di circuito](expressroute-workflows.md) . L'utente configurerà quindi le route BGP (Border Gateway Protocol) per controllare il traffico che scorre tra la rete locale e la rete virtuale di Azure.

### <a name="connect-to-model"></a>Modello di connessione diretta
In una configurazione di connessione diretta il cliente ha già una connessione esistente ad Azure o avvierà una connessione al provider di servizi Internet collegando ExpressRoute dal proprio data center direttamente ad Azure, invece che dal data center dell'utente. Per iniziare il processo di provisioning, il cliente seguirà la procedura descritta sopra in Modello di connessione indiretta. Una volta stabilito il circuito, il cliente dovrà configurare i router locali per poter accedere sia alla rete dell'utente che alle reti virtuali di Azure.

È possibile fornire assistenza nella configurazione della connessione e delle route per consentire alle risorse nei data center dell'utente di comunicare con le risorse del cliente nel data center dell'utente o con le risorse ospitate in Azure.

## <a name="expressroute-routing-domains"></a>Domini di routing ExpressRoute
ExpressRoute offre tre domini di routing: peer pubblico, privato e Microsoft. Ogni dominio di routing è configurato con router identici in una configurazione di tipo attivo-attivo per offrire una disponibilità elevata. Per altri dettagli sui domini di routing ExpressRoute, vedere [qui](expressroute-circuit-peerings.md).

È possibile definire filtri di route personalizzati per consentire solo le route desiderate o necessarie. Per altre informazioni o per scoprire come apportare queste modifiche, vedere l'articolo [Creare e modificare il routing per un circuito ExpressRoute usando PowerShell](expressroute-howto-routing-classic.md) per altri dettagli sui filtri di routing.

> [!NOTE]
> Per il peer Microsoft e pubblico, la connettività deve essere stabilita con un indirizzo IP pubblico di proprietà del cliente o del CSP e deve seguire tutte le regole definite. Per altre informazioni, vedere la pagina [Prerequisiti per ExpressRoute](expressroute-prerequisites.md) .  
> 
> 

## <a name="routing"></a>Routing.
ExpressRoute si connette alle reti di Azure tramite il gateway di rete virtuale di Azure. I gateway di rete forniscono il routing per le reti virtuali di Azure.

Con la creazione delle reti virtuali di Azure viene creata anche una tabella di routing predefinita che consente alla rete virtuale di indirizzare il traffico alle/dalle subnet della rete virtuale. Se la tabella di route predefinita non è sufficiente per la soluzione, possono essere create route personalizzate per instradare il traffico in uscita ad appliance personalizzate o per bloccare le route a reti esterne o subnet specifiche.

### <a name="default-routing"></a>Routing predefinito
La tabella di route predefinita include le route seguenti:

* Routing in una subnet
* Da subnet a subnet nella rete virtuale
* A Internet
* Da rete virtuale a rete virtuale con un gateway VPN
* Da rete virtuale a rete locale con un gateway VPN o ExpressRoute

![testo alternativo](./media/expressroute-for-cloud-solution-providers/default-routing.png)  

### <a name="user-defined-routing-udr"></a>Routing definito dall'utente
Le route definite dall'utente consentono di controllare il traffico in uscita dalla subnet assegnata ad altre subnet nella rete virtuale o su uno degli altri gateway predefiniti (ExpressRoute, Internet o VPN). La tabella di routing di sistema predefinita può essere sostituita con una tabella di routing definita dall'utente che sostituisce la tabella di routing predefinita con route personalizzate. Con il routing definito dall'utente, i clienti possono creare route specifiche alle appliance, ad esempio firewall o appliance per il rilevamento di intrusioni, o bloccare l'accesso a subnet specifiche dalla subnet che ospita la route definita dall'utente. Per una panoramica delle route definite dall'utente, vedere [qui](../virtual-network/virtual-networks-udr-overview.md). 

## <a name="security"></a>Sicurezza
A seconda del modello in uso, connessione diretta o connessione indiretta, il cliente definisce i criteri di sicurezza nella rete virtuale o fornisce i requisiti dei criteri di sicurezza al CSP per definirli per le reti virtuali. Possono essere definiti i criteri di sicurezza seguenti:

1. **Isolamento del cliente** : la piattaforma Azure fornisce l'isolamento del cliente archiviando l'ID cliente e le informazioni sulla rete virtuale in un database sicuro, che viene usato per incapsulare il traffico di ogni cliente in un tunnel GRE.
2. **Gruppo di sicurezza di rete (NSG)** : sono disponibili regole per definire il traffico consentito in entrata e in uscita dalle subnet all'interno delle reti virtuali in Azure. Per impostazione predefinita, il gruppo NSG contiene regole di blocco per bloccare il traffico da Internet alla rete virtuale e regole di consenso per il traffico all'interno di una rete virtuale. Per altre informazioni sui gruppi di sicurezza di rete, vedere [qui](https://azure.microsoft.com/blog/network-security-groups/).
3. **Imponi tunneling** : opzione per reindirizzare tramite la connessione ExpressRoute il traffico associato a Internet che ha origine in Azure al data center locale. Per altre informazioni su Imponi tunneling, vedere [qui](expressroute-routing.md#advertising-default-routes).  
4. **Crittografia**: anche se i circuiti ExpressRoute sono dedicati a un cliente specifico, esiste la possibilità che il provider di rete possa essere violato, consentendo a un intruso di esaminare il traffico dei pacchetti. Per risolvere il problema, un cliente o un CSP può crittografare il traffico sulla connessione definendo criteri della modalità tunnel IPSec per tutto il traffico che scorre tra le risorse locali e le risorse di Azure. Vedere la modalità tunnel IPSec facoltativa per il cliente 1 sopra nella Figura 5: Sicurezza di ExpressRoute. La seconda opzione consiste nell'usare un'appliance firewall in ogni endpoint del circuito ExpressRoute. A questo scopo è necessario installare VM/appliance firewall di terze parti aggiuntive in entrambe le estremità per crittografare il traffico sul circuito ExpressRoute.

![testo alternativo](./media/expressroute-for-cloud-solution-providers/expressroute-security.png)  

## <a name="next-steps"></a>Passaggi successivi
Il servizio Cloud Solution Provider consente di acquisire un valore maggiore per i clienti senza dover acquistare costose infrastrutture e funzionalità e continuando a essere il provider di outsourcing primario. È possibile eseguire facilmente l'integrazione con Microsoft Azure tramite l'API CSP, che consente di integrare la gestione di Microsoft Azure nei framework di gestione esistenti.  

Per altre informazioni, fare clic sui collegamenti seguenti:

[Programma Microsoft Cloud Solution Provider](https://partner.microsoft.com/en-US/Solutions/cloud-reseller-overview).  
[Prepararsi alle transazioni come Cloud Solution Provider](https://partner.microsoft.com/en-us/solutions/cloud-reseller-pre-launch).  
[Risorse Microsoft Cloud Solution Provider](https://partner.microsoft.com/en-us/solutions/cloud-reseller-resources).


