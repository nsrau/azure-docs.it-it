---
title: ExpressRoute per Cloud Solution Provider - Azure | Microsoft Docs
description: Questo articolo fornisce informazioni per i Cloud Solution Provider che vogliono incorporare i servizi di Azure e ExpressRoute nelle loro offerte.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 10/10/2016
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 18ee64e6866764e250cfa08a1d4721674bb66e5a
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92097338"
---
# <a name="expressroute-for-cloud-solution-providers-csp"></a>ExpressRoute per Cloud Solution Provider (CSP)
Microsoft fornisce servizi su vasta scala per consentire a rivenditori e distributori tradizionali (CSP) di effettuare rapidamente il provisioning di nuovi servizi e soluzioni per i clienti senza dover investire nello sviluppo di questi nuovi servizi. Per offrire al Cloud Solution Provider (CSP) la possibilità di gestire direttamente questi nuovi servizi, Microsoft fornisce programmi e API che consentono al CSP di gestire le risorse di Microsoft Azure per conto dei clienti. Una di queste risorse è ExpressRoute. ExpressRoute consente al CSP di connettere le risorse esistenti dei clienti ai servizi di Azure. ExpressRoute è un collegamento di comunicazione privata ad alta velocità ai servizi in Azure. 

ExpressRoute è costituito da una coppia di circuiti per la disponibilità elevata collegata a una o più sottoscrizioni di un singolo cliente e che non possono essere condivise da più clienti. Ogni circuito deve terminare in router diverso per mantenere la disponibilità elevata.

> [!NOTE]
> Sono previsti limiti per la larghezza di banda e il numero di connessioni possibili in ogni circuito ExpressRoute. Se le esigenze di un singolo cliente superano questi limiti, saranno necessari più circuiti ExpressRoute per l'implementazione della rete ibrida.
> 
> 

Microsoft Azure fornisce un numero crescente di servizi che è possibile offrire ai clienti. ExpressRoute consente all'utente e ai clienti di sfruttare i vantaggi offerti da questi servizi fornendo un accesso a bassa latenza ad alta velocità all'ambiente Microsoft Azure.

## <a name="microsoft-azure-management"></a>Gestione di Microsoft Azure
Microsoft fornisce CSP con API per gestire le sottoscrizioni dei clienti di Azure consentendo l'integrazione a livello di codice con i sistemi di gestione dei servizi. Le funzionalità di gestione supportate sono disponibili [qui](https://msdn.microsoft.com/library/partnercenter/dn974944.aspx).

## <a name="microsoft-azure-resource-management"></a>Gestione di risorse di Microsoft Azure
La modalità di gestione della sottoscrizione dipende dal contratto stipulato con il cliente. Il CSP può gestire direttamente la creazione e la manutenzione delle risorse oppure il cliente può mantenere il controllo della sottoscrizione di Microsoft Azure e creare le risorse di Azure necessarie. Se il cliente gestisce la creazione delle risorse nella sottoscrizione di Microsoft Azure, utilizzerà uno dei due modelli seguenti: modello "*Connect-through*" o "*Direct-to*". Questi modelli vengono descritti in dettaglio nelle sezioni seguenti.  

### <a name="connect-through-model"></a>Modello di connessione indiretta
![Diagramma che mostra il modello "Connect-through".](./media/expressroute-for-cloud-solution-providers/connect-through.png)  

In questo modello il CSP crea una connessione diretta tra il data center e la sottoscrizione di Azure del cliente. La connessione diretta viene stabilita usando ExpressRoute, che connette la rete ad Azure. Il cliente quindi si connette alla rete. Questo scenario prevede che il cliente passi attraverso la rete CSP per accedere ai servizi di Azure. 

Se il cliente ha altre sottoscrizioni di Azure non gestite dall'utente, utilizzerebbe la rete Internet pubblica o la propria connessione privata per connettersi ai servizi di cui è stato effettuato il provisioning nella sottoscrizione non CSP. 

Per il CSP che gestisce i servizi di Azure, si presuppone che il CSP disponga di un archivio di identità dei clienti precedentemente stabilito, che verrebbe quindi replicato in Azure Active Directory per la gestione della sottoscrizione CSP tramite l'amministrazione per conto di (AOBO). I driver chiave per questo scenario includono il fatto che un determinato partner o provider di servizi ha stabilito una relazione con il cliente, il cliente utilizza attualmente i servizi del provider o il partner ha il desiderio di fornire una combinazione di soluzioni ospitate dal provider e ospitate da Azure per fornire flessibilità e risolvere le esigenze dei clienti che non possono essere soddisfatte solo da CSP. Questo modello è illustrato nella **Figura** seguente.

![Diagramma che mostra uno scenario dettagliato per il modello "Connect-through".](./media/expressroute-for-cloud-solution-providers/connect-through-model.png)

### <a name="connect-to-model"></a>Modello di connessione diretta
![Diagramma che mostra il modello "Connetti a".](./media/expressroute-for-cloud-solution-providers/connect-to.png)

In questo modello il provider di servizi crea una connessione diretta tra il data center del cliente e la sottoscrizione di Azure di cui viene effettuato il provisioning dal CSP usando ExpressRoute nella rete del cliente (cliente).

> [!NOTE]
> Per ExpressRoute il cliente dovrà creare e gestire il circuito ExpressRoute.  
> 
> 

Questo scenario di connettività richiede che il cliente si connetta direttamente tramite una rete del cliente per accedere alla sottoscrizione di Azure gestita da CSP, usando una connessione di rete diretta creata, di proprietà e gestita interamente o in parte dal cliente. Per questi clienti si presuppone che il provider non disponga attualmente di un archivio di identità del cliente stabilito e che il provider contribuisca al cliente a replicare il proprio archivio di identificazione corrente nel Azure Active Directory per la gestione della propria sottoscrizione tramite AOBO. I fattori chiave di questo scenario includono dove un determinato partner o provider di servizi ha stabilito una relazione con il cliente, dove il cliente utilizza attualmente i servizi del provider o dove il partner vuole fornire servizi basati esclusivamente su soluzioni ospitate da Azure senza la necessità di un data center o di un'infrastruttura del provider esistente.

![Diagramma che mostra uno scenario dettagliato per il modello "Connetti a".](./media/expressroute-for-cloud-solution-providers/connect-to-model.png)

Le scelte tra queste due opzioni sono basate sulle esigenze del cliente e sulla necessità di fornire i servizi di Azure. Informazioni dettagliate su questi modelli e sul controllo degli accessi in base al ruolo associato, sulla rete e sugli schemi progettuali delle identità sono disponibili nel collegamenti seguenti:

* **Controllo degli accessi in base al ruolo di Azure (RBAC di Azure)** : RBAC si basa su Azure Active Directory.  Per ulteriori informazioni su RBAC di Azure, vedere [qui](../role-based-access-control/role-assignments-portal.md).
* **Rete** : include diversi argomenti sulla rete in Microsoft Azure.
* **Azure Active Directory (Azure ad)** : Azure ad fornisce la gestione delle identità per le applicazioni SaaS Microsoft Azure e di terze parti. Per ulteriori informazioni su Azure AD, vedere [qui](https://azure.microsoft.com/documentation/services/active-directory/).  

## <a name="network-speeds"></a>Velocità di rete
ExpressRoute supporta velocità di rete comprese tra 50 MB/s e 10 GB/s. Ciò consente ai clienti di acquistare la quantità di larghezza di banda di rete necessaria per un ambiente univoco.

> [!NOTE]
> La larghezza di banda di rete può essere aumentata in base alle esigenze senza dover interrompere le comunicazioni, invece per ridurre la velocità di rete, è necessario rimuovere il circuito e ricrearlo a una velocità di rete più bassa.  
> 
> 

ExpressRoute supporta la connessione di più reti virtuali a un solo circuito ExpressRoute per un miglior utilizzo delle connessioni con velocità più elevata. Un solo circuito ExpressRoute può essere condiviso tra più sottoscrizioni di Azure di proprietà dello stesso cliente.

## <a name="configuring-expressroute"></a>Configurazione di ExpressRoute
ExpressRoute può essere configurato per supportare tre tipi di traffico ([domini di routing](#expressroute-routing-domains)) su un solo circuito ExpressRoute. Questo traffico viene separato in peering privato, peering Microsoft e peering pubblico (deprecato). È possibile scegliere uno o tutti i tipi di traffico da inviare tramite un solo circuito di ExpressRoute o usare più circuiti ExpressRoute a seconda delle dimensioni del circuito ExpressRoute e dell'isolamento richiesto dal cliente. Il comportamento di sicurezza del cliente potrebbe non consentire al traffico pubblico e al traffico privato di passare per lo stesso circuito.

### <a name="connect-through-model"></a>Modello di connessione indiretta
In una configurazione di connessione, si sarà responsabili di tutti i componenti di rete che connettono le risorse del data center del cliente alle sottoscrizioni ospitate in Azure. Ognuno dei clienti che vogliono usare le funzionalità di Azure richiederà la propria connessione ExpressRoute, che verrà gestita dall'utente. Si utilizzeranno gli stessi metodi che il cliente utilizzerebbe per ottenere il circuito ExpressRoute. Si seguiranno gli stessi passaggi descritti nell'articolo [flussi di lavoro ExpressRoute](expressroute-workflows.md) per il provisioning del circuito e gli Stati del circuito. Si configureranno quindi le route del Border Gateway Protocol (BGP) per controllare il flusso di traffico tra la rete locale e Azure vNet.

### <a name="connect-to-model"></a>Modello di connessione diretta
In una configurazione di connessione, il cliente dispone già di una connessione esistente ad Azure o avvierà una connessione al provider di servizi Internet che collega ExpressRoute dal proprio Data Center direttamente in Azure, invece che nel Data Center. Per iniziare il processo di provisioning, il cliente seguirà la procedura descritta sopra in Modello di connessione indiretta. Una volta stabilito il circuito, il cliente dovrà configurare i router locali per poter accedere sia alla rete che a reti virtuali di Azure.

È possibile fornire assistenza nella configurazione della connessione e delle route per consentire alle risorse nei data center dell'utente di comunicare con le risorse del cliente nel data center dell'utente o con le risorse ospitate in Azure.

## <a name="expressroute-routing-domains"></a>Domini di routing ExpressRoute
ExpressRoute offre due domini di routing per i nuovi circuiti: peering privato e peering Microsoft. Ogni dominio di routing è configurato con router identici nella configurazione Active-Active per la disponibilità elevata. Per altri dettagli sui domini di routing ExpressRoute, vedere [qui](expressroute-circuit-peerings.md).

È possibile definire filtri di route personalizzati per consentire solo le route desiderate o necessarie. Per altre informazioni o per scoprire come apportare queste modifiche, vedere l'articolo [Creare e modificare il routing per un circuito ExpressRoute usando PowerShell](expressroute-howto-routing-classic.md) per altri dettagli sui filtri di routing.

> [!NOTE]
> Per il peering Microsoft, la connettività deve essere un indirizzo IP pubblico di proprietà del cliente o del CSP e deve rispettare tutte le regole definite. Per ulteriori informazioni, vedere la pagina dei [prerequisiti di ExpressRoute](expressroute-prerequisites.md) .  
> 
> 

## <a name="routing"></a>Routing
ExpressRoute si connette alle reti di Azure tramite il gateway di rete virtuale di Azure. I gateway di rete forniscono il routing per le reti virtuali di Azure.

Con la creazione delle reti virtuali di Azure viene creata anche una tabella di routing predefinita che consente alla rete virtuale di indirizzare il traffico alle/dalle subnet della rete virtuale. Se la tabella di route predefinita non è sufficiente per la soluzione, è possibile creare route personalizzate per indirizzare il traffico in uscita a Appliance personalizzate o per bloccare le route a subnet o reti esterne specifiche.

### <a name="default-routing"></a>Routing predefinito
La tabella di route predefinita include le route seguenti:

* Routing in una subnet
* Da subnet a subnet nella rete virtuale
* A Internet
* Da rete virtuale a rete virtuale con un gateway VPN
* Da rete virtuale a rete locale con un gateway VPN o ExpressRoute

![Diagramma che mostra le opzioni di routing predefinite.](./media/expressroute-for-cloud-solution-providers/default-routing.png)  

### <a name="user-defined-routing-udr"></a>Routing definito dall'utente
Le route definite dall'utente consentono di controllare il traffico in uscita dalla subnet assegnata ad altre subnet nella rete virtuale o su uno degli altri gateway predefiniti (ExpressRoute, Internet o VPN). La tabella di routing di sistema predefinita può essere sostituita con una tabella di routing definita dall'utente che sostituisce la tabella di routing predefinita con route personalizzate. Con il routing definito dall'utente, i clienti possono creare route specifiche alle appliance, ad esempio firewall o appliance per il rilevamento di intrusioni, o bloccare l'accesso a subnet specifiche dalla subnet che ospita la route definita dall'utente. Per una panoramica delle route User-Defined, vedere [qui](../virtual-network/virtual-networks-udr-overview.md). 

## <a name="security"></a>Sicurezza
A seconda del modello in uso, connessione diretta o connessione indiretta, il cliente definisce i criteri di sicurezza nella rete virtuale o fornisce i requisiti dei criteri di sicurezza al CSP per definirli per le reti virtuali. Possono essere definiti i criteri di sicurezza seguenti:

1. **Isolamento del cliente** : la piattaforma Azure fornisce l'isolamento del cliente archiviando l'ID cliente e le informazioni sulla rete virtuale in un database sicuro, che viene usato per incapsulare il traffico di ogni cliente in un tunnel GRE.
2. **Gruppo di sicurezza di rete (NSG)** : sono disponibili regole per definire il traffico consentito in entrata e in uscita dalle subnet all'interno delle reti virtuali in Azure. Per impostazione predefinita, NSG contiene regole di blocco per bloccare il traffico da Internet a vNet e consentire le regole per il traffico in un vNet. Per altre informazioni sui gruppi di sicurezza di rete, vedere [qui](https://azure.microsoft.com/blog/network-security-groups/).
3. **Imponi tunneling** : opzione per reindirizzare tramite la connessione ExpressRoute il traffico associato a Internet che ha origine in Azure al data center locale. Per altre informazioni su Imponi tunneling, vedere [qui](expressroute-routing.md#advertising-default-routes).  
4. **Crittografia**: anche se i circuiti ExpressRoute sono dedicati a un cliente specifico, esiste la possibilità che il provider di rete possa essere violato, consentendo a un intruso di esaminare il traffico dei pacchetti. Per risolvere il problema, un cliente o un CSP può crittografare il traffico sulla connessione definendo criteri della modalità tunnel IPSec per tutto il traffico che scorre tra le risorse locali e le risorse di Azure. Vedere la modalità tunnel IPSec facoltativa per il cliente 1 sopra nella Figura 5: Sicurezza di ExpressRoute. La seconda opzione consiste nell'usare un'appliance firewall in ogni endpoint del circuito ExpressRoute. Questa operazione richiede l'installazione di altre VM/appliance del firewall di terze parti su entrambe le estremità per crittografare il traffico sul circuito ExpressRoute.

![testo alternativo](./media/expressroute-for-cloud-solution-providers/expressroute-security.png)  

## <a name="next-steps"></a>Passaggi successivi
Il servizio Cloud Solution Provider consente di acquisire un valore maggiore per i clienti senza dover acquistare costose infrastrutture e funzionalità e continuando a essere il provider di outsourcing primario. È possibile eseguire facilmente l'integrazione con Microsoft Azure tramite l'API CSP, che consente di integrare la gestione di Microsoft Azure nei framework di gestione esistenti.  

Per altre informazioni, fare clic sui collegamenti seguenti:

[Azure nel programma Cloud Solution Provider](https://docs.microsoft.com/azure/cloud-solution-provider).  
[Prepararsi alle transazioni come Cloud Solution Provider](https://partner.microsoft.com/solutions/cloud-reseller-pre-launch).  
[Risorse Microsoft Cloud Solution Provider](https://partner.microsoft.com/solutions/cloud-reseller-resources).
