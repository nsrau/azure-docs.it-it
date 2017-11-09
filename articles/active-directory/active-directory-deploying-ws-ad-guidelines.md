---
title: Linee guida per la distribuzione di Windows Server Active Directory in macchine virtuali di Azure| Documentazione Microsoft
description: Se si conosce la procedura per distribuire Servizi di dominio Active Directory e Active Directory Federation Services in locale, sono disponibili informazioni su come funzionano nelle macchine virtuali di Azure.
services: active-directory
documentationcenter: 
author: femila
manager: femila
editor: 
ms.assetid: 04df4c46-e6b6-4754-960a-57b823d617fa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/26/2017
ms.author: femila
ms.openlocfilehash: 342d9e2787add3d04f1b744152e135db98848179
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="guidelines-for-deploying-windows-server-active-directory-on-azure-virtual-machines"></a>Linee guida per la distribuzione di Active Directory di Windows Server nelle macchine virtuali di Azure
Questo articolo descrive le differenze più importanti tra la distribuzione di Servizi di dominio Active Directory di Windows Server e Active Directory Federation Services (AD FS) in locale rispetto alla distribuzione nelle macchine virtuali di Microsoft Azure.

## <a name="scope-and-audience"></a>Ambito e destinatari
L'articolo è destinato a utenti già esperti nella distribuzione di Active Directory in locale. Illustra le differenze tra la distribuzione di Active Directory nelle macchine virtuali di Microsoft Azure o nelle reti virtuali di Azure e le tradizionali distribuzioni di Active Directory in locale. Le macchine virtuali e le reti virtuali di Azure fanno parte di un'offerta di infrastruttura distribuita come servizio (IaaS) destinata alle organizzazioni perché possano sfruttare le risorse di elaborazione nel cloud.

Per coloro che non hanno familiarità con la distribuzione di Active Directory, vedere [Guida alla distribuzione di Servizi di dominio Active Directory](https://technet.microsoft.com/library/cc753963) o [Pianificazione della distribuzione di AD FS](https://technet.microsoft.com/library/dn151324.aspx), a seconda del caso.

Nell'articolo si presuppone che il lettore abbia familiarità con i concetti seguenti:

* Distribuzione e gestione di Servizi di dominio Active Directory di Windows Server
* Distribuzione e configurazione di DNS per supportare un'infrastruttura di Servizi di dominio Active Directory di Windows Server
* Distribuzione e gestione di AD FS di Windows Server
* Distribuzione, configurazione e gestione di applicazioni relying party, ovvero siti e servizi Web, che possono utilizzare token di AD FS di Windows Server
* Concetti generali sulle macchine virtuale, ad esempio come configurare una macchina virtuale, i dischi virtuali e le reti virtuali

Questo articolo illustra i requisiti per uno scenario di distribuzione ibrida in cui Servizi di dominio Active Directory di Windows Server o AD FS di Windows Server viene distribuito parte in locale e parte in macchine virtuali di Azure. La prima parte del documento illustra le differenze critiche tra l'esecuzione di Active Directory Domain Services e AD FS di Windows Server in macchine virtuali di Azure e in locale. Descrive anche le decisioni importanti che interessano la progettazione e la distribuzione. La parte restante del documento illustra più in dettaglio le linee guida per ogni decisione e la modalità di applicazione di queste linee guida ai diversi scenari di distribuzione.

Questo articolo non descrive la configurazione di [Azure Active Directory](http://azure.microsoft.com/services/active-directory/), un servizio basato su REST che fornisce funzionalità di gestione delle identità e di controllo di accesso per le applicazioni cloud. Azure Active Directory (Azure AD) e Servizi di dominio Active Directory di Windows Server sono tuttavia progettati per interagire in modo da garantire una soluzione di gestione delle identità e dell'accesso per le applicazioni moderne e gli ambienti IT ibridi attuali. Per comprendere le differenze e le relazioni tra Servizi di dominio Active Directory di Windows Server e Azure AD, si tenga presente quanto riportato di seguito:

1. È possibile eseguire Servizi di dominio Active Directory di Windows Server nel cloud in macchine virtuali di Azure quando si usa Azure per estendere il data center locale nel cloud.
2. È possibile usare Azure AD per fornire agli utenti l'accesso Single Sign-On alle applicazioni software come un servizio (SaaS). Questa tecnologia viene usata ad esempio in Microsoft Office 365, nonché in applicazioni eseguite in Azure o in altre piattaforme cloud.
3. È possibile usare il Servizio di controllo di accesso di Azure AD per consentire agli utenti di accedere con le identità di Facebook, Google, Microsoft e di altri provider di identità alle applicazioni ospitate nel cloud o in locale.

Per altre informazioni su queste differenze, vedere [Nozioni fondamentali sulla gestione delle identità di Azure](fundamentals-identity.md).

## <a name="related-resources"></a>Risorse correlate
È possibile scaricare ed eseguire lo strumento [Azure Virtual Machine Readiness Assessment](https://www.microsoft.com/download/details.aspx?id=40898). Lo strumento di valutazione esamina automaticamente l'ambiente locale e genera un report personalizzato in base alle informazioni aggiuntive fornite in questo argomento per supportare la migrazione dell'ambiente ad Azure.

È consigliabile esaminare prima di tutto le esercitazioni, le guide e i video relativi agli argomenti seguenti:

* [Creare una rete virtuale usando il portale di Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)
* [Creare una rete virtuale con una connessione VPN da sito a sito con il portale di Azure classico](../vpn-gateway/vpn-gateway-site-to-site-create.md)
* [Installazione di una nuova foresta Active Directory in una rete virtuale di Azure](active-directory-new-forest-virtual-machine.md)
* [Installare un controller di dominio Active Directory di replica in una rete virtuale di Azure](active-directory-install-replica-active-directory-domain-controller.md)
* [Microsoft Azure IaaS per professionisti IT: (01) Dati fondamentali delle macchine virtuali](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IaaS per professionisti IT: (05) Creazione di reti virtuali e connettività cross-premise](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

## <a name="introduction"></a>Introduzione
Esistono differenze minime tra i requisiti essenziali per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure e quelli per la distribuzione in macchine virtuali locali e, in una certa misura, in computer fisici. Ad esempio, nel caso di Servizi di dominio Active Directory di Windows Server, se i controller di dominio distribuiti in macchine virtuali di Azure sono repliche in un dominio o una foresta aziendale locale esistente, la distribuzione di Azure può essere gestita sostanzialmente nello stesso modo in cui si gestisce qualsiasi altro sito Active Directory di Windows Server aggiuntivo. In altre parole, le subnet devono essere definite in Servizi di dominio Active Directory di Windows Server, deve essere creato un sito, le subnet devono essere collegate a questo sito e connesse ad altri siti usando collegamenti di sito appropriati. Esistono tuttavia alcune differenze comuni a tutte le distribuzioni di Azure, mentre altre variano in base allo scenario di distribuzione specifico. Di seguito sono descritte due differenze fondamentali:

### <a name="azure-virtual-machines-may-need-connectivity-to-the-on-premises-corporate-network"></a>Per le macchine virtuali di Azure potrebbe essere necessaria la connettività alla rete aziendale locale.
Per la riconnessione di macchine virtuali di Azure a una rete aziendale locale è necessario Rete virtuale di Azure, che include un componente di rete privata virtuale (VPN) da sito a sito o da sito a punto per connettersi direttamente alle macchine virtuali di Azure e ai computer locali. Questo componente VPN può anche consentire ai computer membri del dominio locale di accedere a un dominio Active Directory di Windows Server i cui controller di dominio sono ospitati esclusivamente in macchine virtuali di Azure. È tuttavia importante notare che se la connessione VPN non riesce, anche l'autenticazione e le altre operazioni che dipendono da Active Directory di Windows Server non riusciranno. Anche se gli utenti possono accedere usando le credenziali esistenti memorizzate nella cache, ogni tentativo di autenticazione peer-to-peer o da client a server, per cui i ticket devono ancora essere emessi o risultano obsoleti, avrà esito negativo.

Per un video dimostrativo e un elenco di esercitazioni dettagliate, vedere la documentazione relativa alla [rete virtuale](http://azure.microsoft.com/documentation/services/virtual-network/), ad esempio [Creare una rete virtuale con una connessione VPN da sito a sito con il portale di Azure classico](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [!NOTE]
> È anche possibile distribuire Active Directory di Windows Server in una rete virtuale di Azure senza connettività a una rete locale. Le linee guida in questo argomento presuppongono tuttavia l'uso di una rete virtuale di Azure, perché offre funzionalità di indirizzamento IP essenziali per Windows Server.
> 
> 

### <a name="static-ip-addresses-must-be-configured-with-azure-powershell"></a>Gli indirizzi IP statici devono essere configurati con Azure PowerShell.
Gli indirizzi dinamici vengono allocati per impostazione predefinita, ma per assegnare un indirizzo IP statico si usa invece il cmdlet Set-AzureStaticVNetIP. L'indirizzo IP statico impostato sarà mantenuto durante la correzione del servizio e l'arresto o il riavvio della macchina virtuale. Per altre informazioni, vedere il blog relativo all' [indirizzo IP interno statico per le macchine virtuali](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/).

## <a name="BKMK_Glossary"></a>Termini e definizioni
Di seguito è riportato un elenco non esaustivo dei termini relativi alle diverse tecnologie di Azure a cui viene fatto riferimento in questo articolo.

* **Macchine virtuali di Azure**: offerta IaaS di Azure che consente ai clienti di distribuire VM che eseguono quasi tutti i carichi di lavoro server tradizionalmente locali.
* **Rete virtuale di Azure**: servizio di rete in Azure che consente ai clienti di creare e gestire reti virtuali in Azure e collegarle in modo sicuro alla propria infrastruttura di rete locale tramite una rete privata virtuale (VPN).
* **Indirizzo IP virtuale**: indirizzo IP con connessione Internet non è associato a un computer o una scheda di interfaccia di rete specifica. Ai servizi cloud viene assegnato un indirizzo IP virtuale per ricevere il traffico di rete che viene reindirizzato a una VM di Azure. Un indirizzo IP virtuale è una proprietà di un servizio cloud che può contenere una o più macchine virtuali. Una rete virtuale di Azure può anche contenere uno o più servizi cloud. Gli indirizzi IP virtuali forniscono funzionalità native di bilanciamento del carico.
* **Indirizzo IP dinamico**: è l'indirizzo IP solo interno. Deve essere configurato come un indirizzo IP statico, tramite il cmdlet Set-AzureStaticVNetIP, per le VM che ospitano i ruoli del server controller di dominio/DNS.
* **Correzione del servizio**: processo durante il quale Azure ripristina automaticamente lo stato di esecuzione di un servizio dopo averne rilevata una condizione di errore. La correzione del servizio è uno degli aspetti di Azure che supportano disponibilità e resilienza. Anche se poco probabile, la conseguenza di un evento imprevisto di correzione del servizio per un controller di dominio in esecuzione in una VM è simile a un riavvio non pianificato, ma presenta alcuni effetti collaterali:
  
  * La scheda di rete virtuale nella VM viene modificata
  * L'indirizzo MAC della scheda di rete virtuale viene modificato
  * L'ID della CPU/del processore della VM viene modificato
  * La configurazione IP della scheda di rete virtuale non cambia, a condizione che la VM sia collegata a una rete virtuale e l'indirizzo IP della VM sia statico.
  
  Nessuno di questi comportamenti influisce su Active Directory di Windows Server perché non dipende dall'indirizzo MAC o dall'ID della CPU/del processore. È anche consigliabile eseguire tutte le distribuzioni di Active Directory di Windows Server in una rete virtuale di Azure, come indicato sopra.

## <a name="is-it-safe-to-virtualize-windows-server-active-directory-domain-controllers"></a>Sicurezza della virtualizzazione dei controller di dominio Active directory di Windows Server
La distribuzione di controller di dominio Active Directory di Windows Server in macchine virtuali di Azure è soggetta alle stesse linee guida per l'esecuzione di controller di dominio locali in una macchina virtuale. L'esecuzione di controller di dominio virtualizzati è una procedura sicura, a condizione di seguire le linee guida per il backup e il ripristino dei controller di dominio. Per altre informazioni su vincoli e linee guida per l'esecuzione di controller di dominio virtualizzati, vedere [Esecuzione di controller di dominio in Hyper-V](https://technet.microsoft.com/library/dd363553).

Gli hypervisor forniscono o considerano insignificanti tecnologie che possono causare problemi a molti sistemi distribuiti, tra cui Active Directory di Windows Server. Ad esempio, in un server fisico è possibile clonare un disco o usare metodi non supportati per ripristinare lo stato di un server, incluso l'uso di reti SAN e così via, tuttavia questa operazione in un server fisico è molto più difficile del ripristino di uno snapshot macchina virtuale in un hypervisor. In Azure sono disponibili funzionalità che possono generare la stessa condizione indesiderata. Non è ad esempio consigliabile copiare file VHD di controller di dominio invece di eseguire backup regolari, perché il ripristino di questi file può generare una situazione analoga all'uso delle funzionalità di ripristino di snapshot.

Questi rollback introducono gap USN che possono provocare stati permanentemente divergenti tra i controller di dominio. Questa condizione può comportare problemi come i seguenti:

* Oggetti residui
* Password incoerenti
* Valori di attributi incoerenti
* Mancata corrispondenza dello schema in caso di rollback del master schema

Per altre informazioni sull'impatto sui controller di dominio, vedere la sezione relativa a [USN e rollback di USN](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv.aspx#usn_and_usn_rollback).

A partire da Windows Server 2012 sono state [introdotte misure di sicurezza aggiuntive in Servizi di dominio Active Directory](https://technet.microsoft.com/library/hh831734.aspx). Queste misure di sicurezza consentono di proteggere i controller di dominio virtualizzati dai problemi precedenti, purché la piattaforma hypervisor sottostante supporti VM-GenerationID. Azure supporta VM-GenerationID e ciò significa che nei controller di dominio che eseguono Windows Server 2012 o versione successiva in macchine virtuali di Azure sono disponibili misure di sicurezza aggiuntive.

> [!NOTE]
> È consigliabile arrestare e riavviare una VM che esegue il ruolo controller di dominio in Azure nel sistema operativo guest, invece di usare l'opzione **Arresta** nel portale di Azure. Attualmente l'uso del portale per arrestare una macchina virtuale ne comporta la deallocazione. Una VM deallocata ha il vantaggio di non essere soggetta ad addebiti, ma reimposta anche l'attributo VM-GenerationID, un approccio sconsigliato per un controller di dominio. Quando VM-GenerationID viene reimpostato, anche l'attributo invocationID del database di Servizi di dominio Active Directory viene reimpostato, il pool di RID viene eliminato e SYSVOL è contrassegnato come non autorevole. Per altre informazioni, vedere [Introduzione alla virtualizzazione di Servizi di dominio Active Directory](https://technet.microsoft.com/library/hh831734.aspx) e il blog relativo alla [virtualizzazione di DFSR in modo sicuro](http://blogs.technet.com/b/filecab/archive/2013/04/05/safely-virtualizing-dfsr.aspx).
> 
> 

## <a name="why-deploy-windows-server-ad-ds-on-azure-virtual-machines"></a>Perché distribuire Servizi di dominio Active Directory di Windows Server in macchine virtuali di Azure
Molti scenari di distribuzione di Servizi di dominio Active Directory di Windows Server sono particolarmente adatti per la distribuzione come VM in Azure. Si supponga ad esempio di avere una società in Europa che deve autenticare gli utenti in una sede remota in Asia. La società non ha ancora distribuito i controller di dominio Active Directory di Windows Server in Asia a causa del costo di distribuzione e delle scarse competenze per la gestione dei server post-distribuzione. Di conseguenza, le richieste di autenticazione dall'Asia vengono gestite da controller di dominio in Europa con risultati non ottimali. In questo caso, è possibile distribuire un controller di dominio in una VM specificata che deve trovarsi nel data center di Azure in Asia. Il collegamento di questo controller di dominio a una rete virtuale di Azure connessa direttamente alla sede remota determinerà un miglioramento delle prestazioni di autenticazione.

Azure è ideale anche come soluzione alternativa a siti di ripristino di emergenza che sarebbero altrimenti costosi. Il costo relativamente basso dell'hosting di un numero limitato di controller di dominio e una singola rete virtuale in Azure rappresenta un'alternativa interessante.

Infine è possibile distribuire un'applicazione di rete in Azure, ad esempio SharePoint, che richiede Active Directory di Windows Server, ma non ha dipendenze dalla rete locale o dall'istanza di Active Directory di Windows Server aziendale. In questo caso, la distribuzione di una foresta isolata in Azure per soddisfare requisiti del server SharePoint è la soluzione ottimale. La distribuzione di applicazioni di rete che richiedono la connettività alla rete locale e all'istanza di Active Directory aziendale è supportata anche in questo caso.

> [!NOTE]
> Poiché offre una connessione di livello 3, il componente VPN che fornisce la connettività tra una rete virtuale di Azure e una rete locale può anche consentire a server membri eseguiti in locale di sfruttare i controller di dominio eseguiti come VM nella rete virtuale di Azure. Se però la VPN non è disponibile, la comunicazione tra i computer locali e i controller di dominio basati su Azure non funzionerà, generando errori di autenticazione e di altra natura.  
> 
> 

## <a name="contrasts-between-deploying-windows-server-active-directory-domain-controllers-on-azure-virtual-machines-versus-on-premises"></a>Differenze tra la distribuzione di controller di dominio Active Directory di Windows Server in macchine virtuali di Azure e in locale
* Per qualsiasi scenario di distribuzione di Active Directory di Windows Server che include più di una singola VM, è necessario usare una rete virtuale di Azure per la coerenza degli indirizzi IP. Questa guida presuppone che i controller di dominio siano eseguiti in una rete virtuale di Azure.
* Come per i controller di dominio locali, è consigliabile usare indirizzi IP statici. Un indirizzo IP statico può essere configurato solo tramite Azure PowerShell. Per altre informazioni, vedere il blog relativo all' [indirizzo IP interno statico per le macchine virtuali](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/) . Se si usano sistemi di monitoraggio o altre soluzioni che controllano la configurazione dell'indirizzo IP statico all'interno del sistema operativo guest, è possibile assegnare lo stesso indirizzo IP statico alle proprietà della scheda di rete della VM. Tenere però presente che la scheda di rete verrà ignorata se la VM viene sottoposta a correzione del servizio o se viene arrestata nel portale e il relativo indirizzo viene deallocato. In questo caso, l'indirizzo IP statico nel sistema operativo guest dovrà essere reimpostato.
* La distribuzione di VM in una rete virtuale non implica né richiede che venga ristabilita la connettività a una rete locale. Questa possibilità è semplicemente abilitata dalla rete virtuale. È necessario creare una rete virtuale per le comunicazioni private tra Azure e la rete locale. È necessario distribuire un endpoint VPN nella rete locale. La VPN viene aperta da Azure alla rete locale. Per altre informazioni, vedere [Panoramica di Rete virtuale](../virtual-network/virtual-networks-overview.md) e [Creare una rete virtuale con una connessione VPN da sito a sito con il portale di Azure classico](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [!NOTE]
> È disponibile un'opzione per [creare una VPN da punto a sito](../vpn-gateway/vpn-gateway-point-to-site-create.md) per connettere singoli computer basati su Windows direttamente a una rete virtuale di Azure.
> 
> 

* Indipendentemente dal fatto che venga creata o meno una rete virtuale, Azure addebita il costo del traffico in uscita ma non di quello in entrata. Le diverse opzioni di progettazione di Active Directory di Windows Server possono influire sulla quantità di traffico in uscita generato da una distribuzione. Ad esempio, la distribuzione di un controller di dominio di sola lettura limita il traffico in uscita perché non viene replicato. La decisione di distribuire un controller di dominio di sola lettura tuttavia deve essere soppesata, valutando anche la necessità di eseguire operazioni di scrittura nel controller di dominio e la [compatibilità](https://technet.microsoft.com/library/cc755190) delle applicazioni e dei servizi del sito con i controller di dominio di sola lettura. Per altre informazioni sugli addebiti per il traffico, vedere [Prezzi di Azure](http://azure.microsoft.com/pricing/).
* Anche se si ha il controllo completo sulle risorse del server da usare per le VM locali, ad esempio la quantità di RAM, le dimensioni del disco e così via, in Azure è necessario scegliere da un elenco di dimensioni del server preconfigurate. Per un controller di dominio è necessario un disco dati, oltre al disco del sistema operativo, per poter archiviare il database di Active Directory di Windows Server.

## <a name="can-you-deploy-windows-server-ad-fs-on-azure-virtual-machines"></a>Possibilità di distribuire AD FS di Windows Server in macchine virtuali di Azure
È possibile distribuire AD FS di Windows Server in macchine virtuali di Azure e le [procedure consigliate per la distribuzione di AD FS](https://technet.microsoft.com/library/dn151324.aspx) in locale si applicano anche alla distribuzione di AD FS in Azure. Alcune di queste procedure consigliate, come il bilanciamento del carico e la disponibilità elevata, richiedono però tecnologie più avanzate di quelle offerte da AD FS, che devono quindi essere fornite dall'infrastruttura sottostante. Si esamineranno alcune di queste procedure consigliate e si vedrà come attuarle usando VM di Azure e una rete virtuale di Azure.

1. **Non esporre mai i server del servizio token di sicurezza direttamente a Internet.**
   
    Ciò è importante perché questo servizio rilascia i token di sicurezza. Di conseguenza, i server del servizio token di sicurezza come i server AD FS devono essere gestiti con lo stesso livello di protezione di un controller di dominio. Se un servizio token di sicurezza viene compromesso, gli utenti malintenzionati hanno la possibilità di rilasciare token di accesso contenenti attestazioni potenzialmente dannose per applicazioni relying party e altri server del servizio token di sicurezza in organizzazioni attendibili.
2. **Distribuire controller di dominio Active Directory per tutti i domini utente nella stessa rete dei server AD FS.**
   
    I server AD FS usano Servizi di dominio Active Directory per autenticare gli utenti. È consigliabile distribuire i controller di dominio nella stessa rete dei server AD FS. In questo modo si garantiscono la continuità aziendale in caso di interruzione del collegamento tra la rete di Azure e la rete locale, una latenza più bassa e prestazioni migliori per gli accessi.
3. **Distribuire più nodi AD FS per la disponibilità elevata e il bilanciamento del carico.**
   
    Nella maggior parte dei casi, l'errore di un'applicazione abilitata da AD FS non è accettabile, perché le applicazioni che richiedono token di sicurezza sono spesso cruciali. Di conseguenza e poiché AD FS svolge un ruolo fondamentale per l'accesso alle applicazioni cruciali, il servizio AD FS deve garantire la disponibilità elevata in più proxy e server AD FS. Per la corretta distribuzione delle richieste, i servizi di bilanciamento del carico vengono in genere distribuiti a monte di proxy e server AD FS.
4. **Distribuire uno o più nodi Proxy applicazione Web per l'accesso a Internet.**
   
    Quando gli utenti devono accedere alle applicazioni protette dal servizio AD FS, è necessario che questo deve sia disponibile da Internet. A questo scopo viene distribuito il servizio Proxy applicazione Web. È consigliabile distribuire più di un nodo, ai fini della disponibilità elevata e del bilanciamento del carico.
5. **Limitare l'accesso alle risorse di rete interne dai nodi Proxy applicazione Web.**
   
    Per consentire agli utenti esterni di accedere ad AD FS da internet, è necessario distribuire nodi Proxy applicazione Web, detti nodi Proxy AD FS nelle versioni precedenti di Windows Server. I nodi Proxy applicazione Web sono esposti direttamente a Internet. Non devono essere necessariamente aggiunti a un dominio e devono accedere solo ai server AD FS sulle porte TCP 443 e 80. È consigliabile bloccare la comunicazione con tutti gli altri computer, in particolare i controller di dominio.
   
    Questa configurazione si ottiene in genere in locale per mezzo di una rete perimetrale. I firewall usano una modalità operativa di tipo elenco elementi consentiti per limitare il traffico dalla rete perimetrale alla rete locale, ovvero è consentito solo il traffico dagli indirizzi IP specificati e su porte specificate, mentre il resto del traffico viene bloccato.

Il diagramma seguente mostra una tradizionale distribuzione di AD FS in locale.

![Diagramma di una distribuzione tradizionale di Active Directory Federation Services locale](media/active-directory-deploying-ws-ad-guidelines/ADFS_onprem.png)

Poiché tuttavia Azure non fornisce funzionalità firewall native complete, è necessario usare altre opzioni per limitare il traffico. La tabella seguente illustra ogni opzione e i relativi vantaggi e svantaggi.

| Opzione | Vantaggio | Svantaggio |
| --- | --- | --- |
| [ACL di rete di Azure](../virtual-network/virtual-networks-acl.md) |Configurazione iniziale meno costosa e più semplice. |È necessaria la configurazione di altri ACL di rete se vengono aggiunte nuove VM alla distribuzione. |
| [Barracuda NG firewall](https://www.barracuda.com/products/ngfirewall) |Modalità operativa di tipo elenco elementi consentiti, non richiede alcuna configurazione di ACL di rete. |Costi e complessità maggiori per la configurazione iniziale. |

In questo caso, ecco i passaggi generali per distribuire AD FS:

1. Creare una rete virtuale con connettività cross-premise usando una VPN o [ExpressRoute](http://azure.microsoft.com/services/expressroute/).
2. Distribuire i controller di dominio nella rete virtuale. Questo passaggio è facoltativo ma consigliato.
3. Distribuire server AD FS aggiunti a un dominio nella rete virtuale.
4. Creare un [set con carico bilanciato interno](http://azure.microsoft.com/blog/internal-load-balancing/) che include i server AD FS e usa un nuovo indirizzo IP privato all'interno della rete virtuale, ovvero un indirizzo IP dinamico.
   
   1. Aggiornare il DNS per creare l'FQDN che punterà all'indirizzo IP privato (dinamico) del set con carico bilanciato interno.
5. Creare un servizio cloud o una rete virtuale separata per i nodi Proxy applicazione Web.
6. Distribuire i nodi Proxy applicazione Web nel servizio cloud o nella rete virtuale.
   
   1. Creare un set con carico bilanciato esterno che include i nodi Proxy applicazione Web.
   2. Aggiornare il nome DNS esterno (FQDN) che punterà all'indirizzo IP pubblico del servizio cloud, ovvero l'indirizzo IP virtuale.
   3. Configurare i proxy AD FS per l'uso dell'FQDN che corrisponde al set con carico bilanciato interno per i server AD FS.
   4. Aggiornare i siti Web basati su attestazioni per l'uso dell'FQDN esterno per i relativi provider di attestazioni.
7. Limitare l'accesso tra il Proxy applicazione Web e qualsiasi computer nella rete virtuale di AD FS.

Per limitare il traffico, il set con carico bilanciato per il servizio di bilanciamento del carico interno di Azure deve essere configurato solo per il traffico verso le porte TCP 80 e 443, mentre tutto il resto del traffico verso l'indirizzo IP dinamico interno del set con carico bilanciato viene eliminato.

![Diagramma di ACL di rete AD FS con porte TCP 443 e 80 consentite.](media/active-directory-deploying-ws-ad-guidelines/ADFS_ACLs.png)

Il traffico verso i server AD FS sarà consentito solo da queste origini:

* Servizio di bilanciamento del carico interno di Azure.
* Indirizzo IP di un amministratore della rete locale.

> [!WARNING]
> La progettazione deve impedire ai nodi Proxy applicazione Web di accedere ad altre VM nella rete virtuale di Azure o a qualsiasi percorso nella rete locale. A questo scopo, è possibile configurare regole firewall nell'appliance locale per le connessioni ExpressRoute o nel dispositivo VPN per le connessioni VPN da sito a sito.
> 
> 

Uno svantaggio di questa opzione è la necessità di configurare gli ACL di rete per più dispositivi, tra cui il servizio di bilanciamento del carico interno, i server AD FS e gli eventuali altri server aggiunti alla rete virtuale. Se un dispositivo viene aggiunto alla distribuzione senza configurare gli ACL di rete per limitare il traffico destinato al dispositivo stesso, l'intera distribuzione può essere a rischio. Se gli indirizzi IP dei nodi Proxy applicazione Web cambiano, è necessario reimpostare gli ACL di rete, ovvero i proxy devono essere configurati per l'uso di [indirizzi IP interni statici](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/).

![AD FS in Azure con ACL di rete.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure.png)

Un'altra opzione consiste nell'usare il dispositivo [Barracuda NG Firewall](https://www.barracuda.com/products/ngfirewall) per controllare il traffico tra i server proxy AD FS e i server AD FS. Questa opzione è conforme alle procedure consigliate per la sicurezza e la disponibilità elevata e richiede meno attività di amministrazione dopo l'installazione iniziale, perché il dispositivo Barracuda NG Firewall offre una modalità di tipo elenco elementi consentiti per l'amministrazione del firewall e può essere installato direttamente in una rete virtuale di Azure. Questo approccio elimina la necessità di configurare gli ACL di rete ogni volta che si aggiunge un nuovo server alla distribuzione. Questa opzione comporta tuttavia maggiori costi e complessità per la distribuzione iniziale.

In questo caso, vengono distribuite due reti virtuali invece di una, ad esempio rete virtuale 1 e rete virtuale 2. La rete virtuale 1 contiene i proxy, mentre la rete virtuale 2 contiene i servizi token di sicurezza e la connessione di rete alla rete aziendale. La rete virtuale 1 è quindi fisicamente, anche se virtualmente, isolata dalla rete virtuale 2 e di conseguenza anche dalla rete aziendale. La rete virtuale 1 è quindi connessa alla rete virtuale 2 con una speciale tecnologia di tunneling chiamata Transport Independent Network Architecture (TINA). Il tunnel TINA è collegato a ognuna delle reti virtuali tramite un dispositivo Barracuda NG Firewall, uno in ogni rete virtuale.  Per garantire la disponibilità elevata, è consigliabile distribuire due dispositivi Barracuda in ogni rete virtuale, uno attivo e l'altro passivo. Questi dispositivi offrono funzionalità firewall complesse che consentono di simulare il funzionamento di una tradizionale rete perimetrale locale in Azure.

![AD FS in Azure con firewall.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure_firewall.png)

Per altre informazioni, vedere [AD FS: estendere a Internet un'applicazione front-end locale in grado di riconoscere attestazioni](#BKMK_CloudOnlyFed).

### <a name="an-alternative-to-ad-fs-deployment-if-the-goal-is-office-365-sso-alone"></a>Alternativa alla distribuzione di AD FS se l'obiettivo è solo SSO di Office 365
Esiste un'altra alternativa alla distribuzione completa di AD FS se l'obiettivo è solo l'abilitazione dell'accesso per Office 365. In questo caso, è possibile distribuire semplicemente DirSync con la sincronizzazione delle password in locale e ottenere lo stesso risultato finale con una complessità di distribuzione minima, perché questo approccio non richiede AD FS o Azure.

La tabella seguente confronta il funzionamento dei processi di accesso con e senza distribuzione di AD FS.

| Single Sign-On in Office 365 con AD FS e DirSync | Lo stesso Single Sign-On in Office 365 con DirSync e sincronizzazione password |
| --- | --- |
| 1. L'utente accede a una rete aziendale e viene autenticato in Active Directory di Windows Server. |1. L'utente accede a una rete aziendale e viene autenticato in Active Directory di Windows Server. |
| 2. L'utente prova ad accedere a Office 365 (nome utente @contoso.com). |2. L'utente prova ad accedere a Office 365 (nome utente @contoso.com). |
| 3. Office 365 reindirizza l'utente ad Azure AD. |3. Office 365 reindirizza l'utente ad Azure AD. |
| 4. Azure AD non può autenticare l'utente e rileva l'esistenza di una relazione di trust con AD FS in locale, quindi reindirizza l'utente ad AD FS. |4.  Azure AD non può accettare direttamente i ticket Kerberos e non esiste alcuna relazione di trust, quindi richiede all'utente di immettere le credenziali. |
| 5. L'utente invia un ticket Kerberos al servizio token di sicurezza di AD FS. |5. L'utente immette la stessa password locale e Azure AD la convalida rispetto al nome utente e alla password sincronizzati da DirSync. |
| 6. AD FS trasforma il ticket Kerberos nelle attestazioni o nel formato dei token richiesto e reindirizza l'utente ad Azure AD. |6. Azure AD reindirizza l'utente a Office 365. |
| 7. L'utente esegue l'autenticazione ad Azure AD, ovvero si verifica un'altra trasformazione. |7. L'utente può accedere a Office 365 e OWA con lo stesso token di Azure AD. |
| 8. Azure AD reindirizza l'utente a Office 365. | |
| 9. L'utente viene connesso automaticamente a Office 365. | |

In Office 365 con uno scenario DirSync con sincronizzazione delle password (non AD FS), Single Sign-On è sostituito da "Same Sign-On", dove "same" indica semplicemente che gli utenti devono immettere di nuovo le stesse credenziali locali per accedere a Office 365. Questi dati possono essere memorizzati dal browser dell'utente per ridurre le richieste successive.

### <a name="additional-food-for-thought"></a>Altri elementi di riflessione
* Se si distribuisce un proxy AD FS in una macchina virtuale di Azure, è necessaria la connettività ai server AD FS. Se i server sono locali, è consigliabile sfruttare la connettività VPN da sito a sito fornita dalla rete virtuale per consentire ai nodi Proxy applicazione Web di comunicare con i server AD FS.
* Se si distribuisce un server AD FS in una macchina virtuale di Azure, è necessaria la connettività ai controller di dominio Active Directory di Windows Server, agli archivi attributi e ai database di configurazione e può anche essere necessaria una connessione ExpressRoute o VPN da sito a sito tra la rete virtuale di Azure e la rete locale.
* Gli addebiti vengono applicati a tutto il traffico in uscita dalle macchine virtuali di Azure. Se il costo è un fattore determinante, è consigliabile distribuire i nodi Proxy applicazione Web in Azure, lasciando i server AD FS in locale. Se i server AD FS vengono distribuiti anche in macchine virtuali di Azure, si dovranno sostenere costi aggiuntivi per autenticare gli utenti locali. Il traffico in uscita comporta un costo indipendentemente dal fatto che avvenga tramite una connessione ExpressRoute o VPN da sito a sito.
* Se si sceglie di usare le funzionalità native di bilanciamento del carico dei server di Azure per la disponibilità elevata dei server AD FS, il bilanciamento del carico offre probe che consentono di determinare l'integrità delle macchine virtuali nel servizio cloud. Nel caso di macchine virtuali di Azure, invece di ruoli Web o di lavoro, è necessario usare un probe personalizzato perché l'agente che risponde ai probe predefiniti non è presente nelle macchine virtuali di Azure. Per semplicità, è possibile usare un probe TCP personalizzato. A questo scopo, è solo necessario stabilire correttamente una connessione TCP, ovvero un segmento TCP SYN inviato e una risposta con un segmento TCP SYN ACK, per determinare l'integrità della macchina virtuale. È possibile configurare il probe personalizzato per l'uso di qualsiasi porta TCP su cui le macchine virtuali sono attivamente in ascolto.

> [!NOTE]
> Le macchine che devono esporre lo stesso set di porte direttamente in Internet, ad esempio le porte 80 e 443, non possono condividere lo stesso servizio cloud. È quindi consigliabile creare un servizio cloud dedicato per i server AD FS di Windows Server in uso, per evitare possibili sovrapposizioni tra i requisiti della porta per un'applicazione e AD FS di Windows Server.
> 
> 

## <a name="deployment-scenarios"></a>Scenari di distribuzione
La sezione seguente descrive scenari di distribuzione comuni per mettere in evidenza alcune considerazioni importanti a cui prestare attenzione. In ogni scenario sono disponibili collegamenti ad altri dettagli sulle decisioni e sui fattori da considerare.

1. [Servizi di dominio Active Directory: distribuire un'applicazione compatibile con Servizi di dominio Active Directory senza requisiti per la connettività di rete aziendale](#BKMK_CloudOnly)
   
    Ad esempio, un servizio SharePoint con connessione Internet viene distribuito in una macchina virtuale di Azure. L'applicazione non ha dipendenze delle risorse di rete aziendale. L'applicazione richiede Servizi di dominio Active Directory di Windows Server, ma NON Servizi di dominio Active Directory di Windows Server aziendale.
2. [AD FS: estendere a Internet un'applicazione front-end locale in grado di riconoscere attestazioni.](#BKMK_CloudOnlyFed)
   
    Ad esempio, un'applicazione in grado di riconoscere attestazioni distribuita correttamente in locale e usata dagli utenti aziendali deve essere accessibile da Internet. L'accesso all'applicazione direttamente tramite Internet deve essere garantito sia ai partner commerciali con le relative identità aziendali sia agli utenti esistenti.
3. [Servizi di dominio Active Directory: distribuire un'applicazione compatibile con Servizi di dominio Active Directory di Windows Server che richiede la connettività di rete aziendale](#BKMK_HybridExt)
   
    Ad esempio, un'applicazione compatibile con LDAP, che supporta l'autenticazione integrata di Windows e usa Servizi di dominio Active Directory di Windows Server come archivio per i dati di configurazione e del profilo utente, viene distribuita in una macchina virtuale di Azure. È consigliabile che l'applicazione sfrutti Servizi di dominio Active Directory di Windows Server aziendale esistente e fornisca l'accesso Single Sign-On. L'applicazione non è in grado di riconoscere attestazioni.

### <a name="BKMK_CloudOnly"></a>1. Servizi di dominio Active Directory: distribuire un'applicazione compatibile con Servizi di dominio Active Directory senza requisiti per la connettività di rete aziendale
![Distribuzione di Servizi di dominio Active Directory solo cloud](media/active-directory-deploying-ws-ad-guidelines/ADDS_cloud.png)
**Figura 1**

#### <a name="description"></a>Description
SharePoint viene distribuito in una macchina virtuale di Azure e l'applicazione non ha dipendenze dalle risorse di rete aziendale. L'applicazione richiede Servizi di dominio Active Directory di Windows Server, ma *non* Servizi di dominio Active Directory di Windows Server aziendale. Non sono richiesti trust Kerberos né trust federativi perché il provisioning degli utenti viene effettuato automaticamente tramite l'applicazione nel dominio di Servizi di dominio Active Directory di Windows Server, ospitato anche nelle macchine virtuali di Azure nel cloud.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Considerazioni sullo scenario e modalità di applicazione di aree tecnologiche allo scenario
* [Topologia di rete](#BKMK_NetworkTopology): creare una rete virtuale di Azure senza connettività cross-premise, nota anche come connettività da sito a sito.
* [Configurazione della distribuzione di un controller di dominio](#BKMK_DeploymentConfig): distribuire un nuovo controller di dominio in una nuova foresta Active Directory di Windows Server a dominio singolo. La distribuzione dovrà avvenire insieme al server DNS di Windows.
* [Topologia del sito Active Directory di Windows Server](#BKMK_ADSiteTopology): usare il sito Active Directory di Windows Server predefinito (tutti i computer faranno parte di Nome-predefinito-primo-sito).
* [Indirizzamento IP e DNS](#BKMK_IPAddressDNS):
  
  * Impostare un indirizzo IP statico per il controller di dominio tramite il cmdlet di Azure PowerShell Set-AzureStaticVNetIP.
  * Installare e configurare il DNS di Windows Server nei controller di dominio in Azure.
  * Configurare le proprietà della rete virtuale con il nome e l'indirizzo IP della VM che ospita i ruoli del server DNS e controller di dominio.
* [Catalogo globale](#BKMK_GC): il primo controller di dominio nella foresta deve essere un server di catalogo globale. È necessario configurare come cataloghi globali anche controller di dominio aggiuntivi, perché in una foresta a dominio singolo il catalogo globale non richiede attività aggiuntive da parte del controller di dominio.
* [Posizione del database di Servizi di dominio Active Directory di Windows Server e di SYSVOL](#BKMK_PlaceDB): aggiungere un disco dati ai controller di dominio eseguiti come VM di Azure per archiviare il database di Active Directory di Windows Server, i log e SYSVOL.
* [Backup e ripristino](#BKMK_BUR): determinare la posizione in cui archiviare i backup dello stato del sistema. Se necessario, aggiungere un altro disco dati alla VM del controller di dominio per archiviare i backup.

### <a name="BKMK_CloudOnlyFed"></a>2. AD FS: estendere a Internet un'applicazione front-end locale in grado di riconoscere attestazioni
![Federazione con connettività cross-premise](media/active-directory-deploying-ws-ad-guidelines/Federation_xprem.png)
**Figura 2**

#### <a name="description"></a>Descrizione
Un'applicazione in grado di riconoscere attestazioni distribuita correttamente in locale e usata dagli utenti aziendali deve essere accessibile direttamente da Internet. L'applicazione viene usata come front-end Web in un database SQL in cui vengono archiviati i dati. I server SQL usati dall'applicazione sono presenti anche nella rete aziendale. Per fornire l'accesso agli utenti aziendali, sono stati distribuiti in locale due servizi token di sicurezza di AD FS di Windows Server e un servizio di bilanciamento del carico. A questo punto, l'accesso all'applicazione direttamente tramite Internet deve essere garantito sia ai partner commerciali con le relative identità aziendali sia agli utenti aziendali esistenti.

Per semplificare il processo e soddisfare le esigenze di configurazione e distribuzione di questo nuovo requisito, si è deciso di installare due front-end Web aggiuntivi e due server proxy AD FS di Windows Server nelle macchine virtuali di Azure. Queste quattro VM saranno tutte esposte direttamente a Internet e riceveranno la connettività alla rete locale tramite la funzionalità VPN da sito a sito della rete virtuale di Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Considerazioni sullo scenario e modalità di applicazione di aree tecnologiche allo scenario
* [Topologia di rete](#BKMK_NetworkTopology): creare una rete virtuale di Azure e [configurare la connettività cross-premise](../vpn-gateway/vpn-gateway-site-to-site-create.md).
  
  > [!NOTE]
  > Per ognuno dei certificati di AD FS di Windows Server verificare che l'URL definito nel modello di certificato e nei certificati risultanti sia raggiungibile dalle istanze di AD FS di Windows Server eseguite in Azure. Questa condizione potrebbe richiedere la connettività cross-premise a parti dell'infrastruttura a chiave pubblica (PKI). Ad esempio, se l'endpoint di CRL è basato su LDAP e ospitato esclusivamente in locale, sarà necessaria la connettività cross-premise. Se ciò non è auspicabile, può essere necessario usare i certificati emessi da un'autorità di certificazione i cui CRL sono accessibili tramite Internet.
  > 
  > 
* [Configurazione di servizi cloud](#BKMK_CloudSvcConfig): assicurarsi di avere due servizi cloud per fornire due indirizzi IP virtuali con carico bilanciato. L'indirizzo IP virtuale del primo servizio cloud verrà indirizzato alle due VM del proxy AD FS di Windows Server sulle porte 80 e 443. Le VM del proxy AD FS di Windows Server verranno configurate in modo che puntino all'indirizzo IP del servizio di bilanciamento del carico locale usato dai servizi token di sicurezza di AD FS di Windows Server. L'indirizzo IP virtuale del secondo servizio cloud verrà indirizzato alle due VM che eseguono il front-end Web sempre sulle porte 80 e 443. Configurare un probe personalizzato per assicurarsi che il servizio di bilanciamento del carico indirizzi il traffico solo al proxy AD FS di Windows Server e alle VM front-end Web funzionanti.
* [Configurazione del server federativo](#BKMK_FedSrvConfig): configurare AD FS di Windows Server come server federativo (servizio token di sicurezza) per generare i token di sicurezza per la foresta Active Directory di Windows Server creata nel cloud. Impostare le relazioni di trust del provider di attestazioni federativo con i diversi partner da cui si accetteranno le identità e configurare le relazioni di trust della relying party con le diverse applicazioni in cui si vogliono generare i token.
  
    In quasi tutti gli scenari i server proxy AD FS di Windows Server vengono distribuiti in una soluzione con connessione Internet per motivi di sicurezza, mentre le relative controparti federative di AD FS di Windows Server rimangono isolate dalla connettività Internet diretta. Indipendentemente dallo scenario di distribuzione, è necessario configurare il servizio cloud in uso con un indirizzo IP virtuale che fornirà un indirizzo IP esposto pubblicamente, nonché una porta in grado di bilanciare il carico tra le due istanze del servizio token di sicurezza di AD FS di Windows Server o tra le istanze proxy.
* [Configurazione con disponibilità elevata di AD FS di Windows Server](#BKMK_ADFSHighAvail): è consigliabile distribuire una farm AD FS di Windows Server con almeno due server per il failover e il bilanciamento del carico. Valutare la possibilità di usare Database interno di Windows per i dati di configurazione di AD FS di Windows Server e usare la funzionalità di bilanciamento del carico interno di Azure per distribuire le richieste in ingresso tra i server della farm.

Per altre informazioni, vedere la [Guida alla distribuzione di Servizi di dominio Active Directory](https://technet.microsoft.com/library/cc753963).

### <a name="BKMK_HybridExt"></a>3. Servizi di dominio Active Directory: distribuire un'applicazione compatibile con Servizi di dominio Active Directory di Windows Server che richiede la connettività di rete aziendale
![Distribuzione di Servizi di dominio Active Directory cross-premise](media/active-directory-deploying-ws-ad-guidelines/ADDS_xprem.png)
**Figura 3**

#### <a name="description"></a>Descrizione
Un'applicazione compatibile con LDAP viene distribuita in una macchina virtuale di Azure. Supporta l'autenticazione integrata di Windows e usa Servizi di dominio Active Directory di Windows Server come archivio per i dati di configurazione e del profilo utente. L'obiettivo dell'applicazione consiste nello sfruttare Servizi di dominio Active Directory di Windows Server aziendale esistente e fornire l'accesso Single Sign-On. L'applicazione non è in grado di riconoscere attestazioni. Gli utenti devono anche accedere all'applicazione direttamente da Internet. Per ottimizzare le prestazioni e i costi, si è deciso di distribuire due controller di dominio aggiuntivi, che fanno parte del dominio aziendale, insieme all'applicazione in Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Considerazioni sullo scenario e modalità di applicazione di aree tecnologiche allo scenario
* [Topologia di rete](#BKMK_NetworkTopology): creare una rete virtuale di Azure con la [connettività cross-premise](../vpn-gateway/vpn-gateway-site-to-site-create.md).
* [Metodo di installazione](#BKMK_InstallMethod): distribuire controller di dominio di replica dal dominio Active Directory di Windows Server aziendale. Per un controller di dominio di replica è possibile installare Servizi di dominio Active Directory di Windows Server nella VM e, facoltativamente, usare la funzionalità Installazione da supporto per ridurre la quantità di dati che devono essere replicati nel nuovo controller di dominio durante l'installazione. Per un'esercitazione, vedere [Installazione di un controller di dominio Active Directory di replica in una rete virtuale di Azure](active-directory-install-replica-active-directory-domain-controller.md). Anche se si usa Installazione da supporto, potrebbe risultare più pratico compilare il controller di dominio virtuale in locale e spostare l'intero disco rigido virtuale (VHD) nel cloud, invece di replicare Servizi di dominio Active Directory di Windows Server durante l'installazione. Per motivi di sicurezza, è consigliabile eliminare il disco rigido virtuale dalla rete locale dopo che è stato copiato in Azure.
* [Topologia del sito Active Directory di Windows Server](#BKMK_ADSiteTopology): creare un nuovo sito di Azure in Siti e servizi di Active Directory. Creare un oggetto subnet di Active Directory di Windows Server per rappresentare la rete virtuale di Azure e aggiungere la subnet al sito. Creare un nuovo collegamento di sito che include il nuovo sito di Azure e il sito in cui si trova l'endpoint VPN della rete virtuale di Azure per controllare e ottimizzare il traffico di Active Directory di Windows Server da e verso Azure.
* [Indirizzamento IP e DNS](#BKMK_IPAddressDNS):
  
  * Impostare un indirizzo IP statico per il controller di dominio tramite il cmdlet di Azure PowerShell Set-AzureStaticVNetIP.
  * Installare e configurare il DNS di Windows Server nei controller di dominio in Azure.
  * Configurare le proprietà della rete virtuale con il nome e l'indirizzo IP della VM che ospita i ruoli del server DNS e controller di dominio.
* [Controller di dominio con distribuzione geografica](#BKMK_DistributedDCs): configurare reti virtuali aggiuntive in base alle esigenze. Se la topologia del sito Active Directory richiede la presenza di controller di dominio in aree geografiche che corrispondono ad aree di Azure diverse, è possibile creare siti Active Directory corrispondenti.
* [Controller di dominio di sola lettura](#BKMK_RODC): è possibile distribuire un controller di dominio di sola lettura nel sito di Azure, a seconda dei requisiti per l'esecuzione di operazioni di scrittura nel controller di dominio e della compatibilità di applicazioni e servizi nel sito con controller di dominio di sola lettura. Per altre informazioni sulla compatibilità delle applicazioni, vedere la guida alla [Compatibilità delle applicazioni con i controller di dominio di sola lettura](https://technet.microsoft.com/library/cc755190).
* [Catalogo globale](#BKMK_GC): i cataloghi globali sono necessari per soddisfare le richieste di accesso in foreste con più domini. Se non si distribuisce un catalogo globale nel sito di Azure, verranno applicati addebiti per il traffico in uscita, perché le richieste di autenticazione generano query sui cataloghi globali in altri siti. Per ridurre il traffico, è possibile abilitare la memorizzazione nella cache dell'appartenenza al gruppo universale per il sito di Azure in Siti e servizi di Active Directory.
  
    Se si distribuisce un catalogo globale, configurare i collegamenti di sito e i relativi costi in modo che il catalogo globale nel sito di Azure non sia scelto come controller di dominio di origine preferito da altri cataloghi globali che devono replicare le stesse partizioni di dominio parziali.
* [Posizione del database di Servizi di dominio Active Directory di Windows Server e di SYSVOL](#BKMK_PlaceDB): aggiungere un disco dati ai controller di dominio eseguiti in VM di Azure per archiviare il database di Active Directory di Windows Server, i log e SYSVOL.
* [Backup e ripristino](#BKMK_BUR): determinare la posizione in cui archiviare i backup dello stato del sistema. Se necessario, aggiungere un altro disco dati alla VM del controller di dominio per archiviare i backup.

## <a name="deployment-decisions-and-factors"></a>Decisioni e fattori relativi alla distribuzione
Nella tabella seguente sono riepilogate le aree tecnologiche di Active Directory di Windows Server prese in considerazione negli scenari precedenti, con le relative decisioni di cui tener conto e i collegamenti a informazioni più dettagliate. Alcune aree tecnologiche potrebbero non essere applicabili a tutti gli scenari di distribuzione e alcune di esse potrebbero essere più critiche in uno scenario di distribuzione rispetto ad altre.

Ad esempio, se si distribuisce un controller di dominio di replica in una rete virtuale e la foresta ha un solo dominio, la scelta di distribuire un server di catalogo globale in questo caso non sarà critica per lo scenario di distribuzione, perché non creerà requisiti di replica aggiuntivi. D'altra parte, se la foresta ha diversi domini, la decisione di distribuire un catalogo globale in una rete virtuale potrebbe influire sulla larghezza di banda disponibile, sulle prestazioni, sull'autenticazione, sulle ricerche nella directory e così via.

| Active Directory di Windows Server in macchine virtuali di Azure | Decisioni | Fattori |
| --- | --- | --- |
| [Topologia di rete](#BKMK_NetworkTopology) |Si crea una rete virtuale? |<li>Requisiti per l'accesso alle risorse aziendali</li> <li>Autenticazione</li> <li>Account Management</li> |
| [Configurazione della distribuzione di un controller di dominio](#BKMK_DeploymentConfig) |<li>Distribuire una foresta separata senza trust?</li> <li>Distribuire una nuova foresta con federazione?</li> <li>Distribuire una nuova foresta con trust tra foreste di Active Directory di Windows Server o Kerberos?</li> <li>Estendere la foresta aziendale distribuendo una controller di dominio di replica?</li> <li>Estendere la foresta aziendale distribuendo un nuovo dominio figlio o albero di dominio?</li> |<li>Sicurezza</li> <li>Conformità</li> <li>Costi</li> <li>Resilienza e tolleranza di errore</li> <li>Compatibilità tra le versioni</li> |
| [Topologia del sito Active Directory di Windows Server](#BKMK_ADSiteTopology) |Come si configurano subnet, siti e collegamenti di sito con Rete virtuale di Microsoft Azure per ottimizzare il traffico e ridurre i costi? |<li>Definizioni di sito e subnet</li> <li>Proprietà dei collegamenti di sito e notifica delle modifiche</li> <li>Compressione della replica</li> |
| [Indirizzamento IP e DNS](#BKMK_IPAddressDNS) |Come configurare gli indirizzi IP e risoluzione dei nomi? |<li>Usare il cmdlet Set-AzureStaticVNetIP per assegnare un indirizzo IP statico</li> <li>Installare un server DNS di Windows Server e configurare le proprietà della rete virtuale con il nome e l'indirizzo IP della VM che ospita i ruoli del server DNS e controller di dominio</li> |
| [Controller di dominio con distribuzione geografica](#BKMK_DistributedDCs) |Come eseguire la replica in controller di dominio in reti virtuali separate? |Se la topologia del sito Active Directory richiede la presenza di controller di dominio in aree geografiche che corrispondono ad aree di Azure diverse, è possibile creare siti Active Directory corrispondenti. [Configurare la rete virtuale per la connettività di rete virtuale](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) per la replica tra controller di dominio in reti virtuali separate. |
| [Controller di dominio di sola lettura](#BKMK_RODC) |Usare controller di dominio di sola lettura o scrivibili? |<li>Filtrare gli attributi HBI/PII</li> <li>Filtrare le chiavi private</li> <li>Limitare il traffico in uscita</li> |
| [Catalogo globale](#BKMK_GC) |Installare il catalogo globale? |<li>Per una foresta con dominio singolo, convertire tutti i controller di dominio in cataloghi globali</li> <li>Per una foresta a più domini, i cataloghi globali sono necessari per l'autenticazione</li> |
| [Metodo di installazione](#BKMK_InstallMethod) |Come installare un controller di dominio in Azure? |È possibile:  <li>Installare Servizi di dominio Active Directory usando Windows PowerShell o Dcpromo</li> <li>Spostare il disco rigido virtuale di un controller di dominio virtuale locale</li> |
| [Posizione del database di Servizi di dominio Active Directory di Windows Server e di SYSVOL](#BKMK_PlaceDB) |Dove archiviare il database di Active Directory di Windows Server, i log e SYSVOL? |Modificare i valori predefiniti di Dcpromo.exe. Questi file critici di Active Directory *devono* trovarsi su dischi dati di Azure invece che su dischi del sistema operativo che implementano la memorizzazione nella cache in scrittura. |
| [Backup e ripristino](#BKMK_BUR) |Come proteggere e ripristinare i dati? |Creare backup dello stato del sistema. |
| [Configurazione del server federativo](#BKMK_FedSrvConfig) |<li>Distribuire una nuova foresta con federazione nel cloud?</li> <li>Distribuire AD FS in locale ed esporre un proxy nel cloud?</li> |<li>Sicurezza</li> <li>Conformità</li> <li>Costi</li> <li>Accesso alle applicazioni da parte di partner commerciali</li> |
| [Configurazione di servizi cloud](#BKMK_CloudSvcConfig) |Un servizio cloud viene distribuito in modo implicito la prima volta che si crea una macchina virtuale. È necessario distribuire servizi cloud aggiuntivi? |<li>Una o più VM richiedono l'esposizione diretta a Internet?</li> <li> Il servizio richiede il bilanciamento del carico?</li> |
| [Requisiti del server federativo per l'indirizzamento IP pubblico e privato (confronto tra indirizzo IP dinamico e indirizzo IP virtuale)](#BKMK_FedReqVIPDIP) |<li>L'istanza di AD FS di Windows Server deve essere raggiunta direttamente da Internet?</li> <li>L'applicazione distribuita nel cloud richiede una porta e un indirizzo IP con connessione Internet specifici?</li> |Creare un servizio cloud per ogni indirizzo IP virtuale necessario per la distribuzione |
| [Configurazione con disponibilità elevata di AD FS di Windows Server](#BKMK_ADFSHighAvail) |<li>Quanti nodi sono necessari nella server farm AD FS di Windows Server?</li> <li>Quanti nodi occorre distribuire nella farm del proxy AD FS di Windows Server?</li> |Resilienza e tolleranza di errore |

### <a name="BKMK_NetworkTopology"></a>Topologia di rete
Per soddisfare i requisiti di DNS e di coerenza degli indirizzi IP di Servizi di dominio Active Directory di Windows Server, è necessario creare prima di tutto la [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md) a cui connettere le macchine virtuali. Durante la creazione è necessario stabilire se estendere facoltativamente la connettività alla rete aziendale locale, che connette in modo trasparente le macchine virtuali di Azure ai computer locali. A questo scopo si usano le tecnologie VPN tradizionali ed è richiesta l'esposizione di un endpoint VPN sul perimetro della rete aziendale. In altre parole, la rete VPN viene avviata da Azure alla rete aziendale, non viceversa.

Si noti che, oltre agli addebiti standard per ogni VM, vengono applicate spese aggiuntive quando si estende una rete virtuale alla rete locale. In particolare, vengono addebitati per il tempo di CPU del gateway di rete virtuale di Azure e il traffico in uscita generato dalle comunicazioni di ogni VM con i computer locali nella VPN. Per altre informazioni sugli addebiti per il traffico di rete, vedere [Prezzi di Azure](http://azure.microsoft.com/pricing/).

### <a name="BKMK_DeploymentConfig"></a>Configurazione della distribuzione di un controller di dominio
La modalità di configurazione del controller di dominio dipende dai requisiti per il servizio che si vuole eseguire in Azure. Ad esempio, è possibile distribuire una nuova foresta, isolata dalla propria foresta aziendale, per testare un modello di verifica, una nuova applicazione o altri progetti a breve termine che richiedono servizi directory, ma non l'accesso specifico a risorse aziendali interne.

Il vantaggio che deriva dalla mancata replica di un controller di dominio di una foresta isolata con i controller di dominio locali consente di ridurre il traffico di rete in uscita generato dal sistema stesso e di conseguenza i costi. Per altre informazioni sugli addebiti per il traffico di rete, vedere [Prezzi di Azure](http://azure.microsoft.com/pricing/).

Sempre a titolo di esempio, si supponga di avere requisiti di privacy per un servizio, ma che quest'ultimo dipenda dall'accesso ad Active Directory di Windows Server interno. Se è consentito l'hosting dei dati per il servizio nel cloud, è possibile distribuire un nuovo dominio figlio per la foresta in Azure interna. In questo caso, è possibile distribuire un controller di dominio per il nuovo dominio figlio, senza il catalogo globale per risolvere problemi relativi alla privacy. Questo scenario richiede una rete virtuale per la connettività ai controller di dominio locali, insieme alla distribuzione di un controller di dominio di replica.

Se si crea una nuova foresta, scegliere se usare [trust di Active Directory](https://technet.microsoft.com/library/cc771397) o [trust federativi](https://technet.microsoft.com/library/dd807036). Bilanciare i requisiti determinati da compatibilità, sicurezza, conformità, costo e resilienza. Ad esempio, per ottimizzare l' [autenticazione selettiva](https://technet.microsoft.com/library/cc755844) , è possibile scegliere di distribuire una nuova foresta in Azure e creare trust di Active Directory di Windows Server tra la foresta locale e quella cloud. Se l'applicazione è in grado di riconoscere attestazioni, è tuttavia possibile distribuire trust federativi invece di trust tra foreste Active Directory. Un altro fattore da considerare sarà il costo per replicare più dati estendendo Active Directory di Windows Server locale nel cloud o per generare più traffico in uscita come risultato dell'autenticazione e del carico delle query.

Anche i requisiti di disponibilità e tolleranza di errore influiscono sulla scelta, Ad esempio, se il collegamento viene interrotto, è possibile che lo siano anche le applicazioni che usano un trust Kerberos o un trust federativo, a meno che non sia stata distribuita un'infrastruttura sufficiente in Azure. Le configurazioni di distribuzione alternative come i controller di dominio di replica (controller di dominio di sola lettura o scrivibili) aumentano la probabilità di tolleranza delle interruzioni del collegamento.

### <a name="BKMK_ADSiteTopology"></a>Topologia del sito Active Directory di Windows Server
È necessario definire correttamente i siti e i collegamenti di sito per ottimizzare il traffico e ridurre i costi. Siti, collegamenti di sito e subnet influiscono sulla topologia di replica tra i controller di dominio e il flusso del traffico di autenticazione. Considerare gli addebiti per il traffico seguenti e quindi distribuire e configurare i controller di dominio in base ai requisiti del proprio scenario di distribuzione:

* Esiste una tariffa nominale oraria per il gateway stesso:
  
  * Può essere avviato e arrestato nel modo desiderato
  * Se arrestato, le VM di Azure vengono isolate dalla rete aziendale
* Il traffico in ingresso è gratuito
* Il traffico in uscita viene addebitato in base ai [prezzi di Azure](http://azure.microsoft.com/pricing/). È possibile ottimizzare le proprietà dei collegamenti di sito tra i siti locali e i siti cloud come indicato di seguito:
  
  * Se si usano più reti virtuali, configurare i collegamenti di sito e i relativi costi in modo appropriato, per impedire che Servizi di dominio Active Directory di Windows Server assegni la priorità al sito di Azure rispetto a un sito in grado di fornire gli stessi livelli di servizio gratuitamente. È anche possibile prendere in considerazione la disabilitazione dell'opzione relativa al bridge dei collegamenti di sito, abilitata per impostazione predefinita. Ciò garantisce che la replica venga eseguita solo tra i siti connessi direttamente. Nei controller di dominio di siti connessi in modo transitivo la replica non può più essere eseguita in reciprocamente e direttamente, bensì tramite uno o più siti comuni. Se per qualche motivo i siti intermedi non sono disponibili, la replica tra controller di dominio nei siti connessi in modo transitivo non verrà eseguita, anche se la connettività tra i siti è presente. Infine, dove sezioni di comportamento di replica transitiva possono essere utili, creare bridge di collegamenti di sito che contengono siti e collegamenti di sito appropriati, ad esempio siti di rete aziendale in locale.
  * [Configurare il costo del collegamento di sito](https://technet.microsoft.com/library/cc794882) in modo appropriato per evitare traffico imprevisto. Ad esempio, se è abilitata l'opzione **Prova sito più vicino successivo** , assicurarsi che i siti della rete virtuale non siano tra quelli più vicini successivi aumentando il costo associato dell'oggetto collegamento di sito che riconnette il sito di Azure alla rete aziendale.
  * Configurare gli [intervalli](https://technet.microsoft.com/library/cc794878) e le [pianificazioni](https://technet.microsoft.com/library/cc816906) del collegamento di sito in base ai requisiti di coerenza e alla frequenza delle modifiche dell'oggetto. Allineare la pianificazione di replica alla tolleranza della latenza. I controller di dominio replicano solo l'ultimo stato di un valore, quindi la riduzione dell'intervallo di replica può consentire una diminuzione dei costi se la frequenza delle modifiche dell'oggetto è sufficiente.
* Se la riduzione dei costi è una priorità, assicurarsi che la replica venga pianificata e che la notifica delle modifiche non sia abilitata. Questa è la configurazione predefinita per la replica tra siti. Ciò non è importante se si distribuisce un controller di dominio di sola lettura in una rete virtuale, perché questo tipo di controller non replica le modifiche in uscita. Se però si distribuisce un controller di dominio scrivibile, assicurarsi che il collegamento di sito non sia configurato per la replica degli aggiornamenti con una frequenza non necessaria. Se si distribuisce un server di catalogo globale, verificare che tutti gli altri siti che contengono un catalogo globale replichino le partizioni di dominio da un controller di dominio di origine in un sito connesso con collegamenti di sito che hanno un costo inferiore rispetto al catalogo globale nel sito di Azure.
* È possibile ridurre ancora di più il traffico di rete generato dalla replica tra siti modificando l'algoritmo di compressione della replica. L'algoritmo di compressione è controllato dalla voce del Registro di sistema REG_DWORD HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Replicator compression algorithm. Il valore predefinito è 3, che è correlato all'algoritmo di compressione Xpress. È possibile impostare il valore su 2, che modifica l'algoritmo in MSZip. Nella maggior parte dei casi ciò comporta un aumento della compressione, che però incide sull'utilizzo della CPU. Per altre informazioni, vedere l'articolo relativo al [funzionamento della topologia di replica di Active Directory](https://technet.microsoft.com/library/cc755994).

### <a name="BKMK_IPAddressDNS"></a>Indirizzamento IP e DNS
Alle macchine virtuali di Azure vengono allocati "indirizzi con lease DHCP" per impostazione predefinita. Poiché gli indirizzi dinamici della rete virtuale di Azure sono persistenti in una macchina virtuale per la durata della macchina virtuale stessa, i requisiti di Servizi di dominio Active Directory di Windows Server vengono soddisfatti.

Di conseguenza, quando si usa un indirizzo dinamico in Azure, si usa in effetti un indirizzo IP statico, perché è instradabile per il periodo del lease, che a sua volta equivale alla durata del servizio cloud.

Se tuttavia la macchina virtuale viene arrestata, il relativo indirizzo IP dinamico viene deallocato. Per evitare che l'indirizzo IP venga deallocato, è possibile [usare Set-AzureStaticVNetIP per assegnare un indirizzo IP statico](http://social.technet.microsoft.com/wiki/contents/articles/23447.how-to-assign-a-private-static-ip-to-an-azure-vm.aspx).

Per la risoluzione dei nomi, distribuire l'infrastruttura del server DNS o usare quella esistente. Il DNS fornito da Azure non soddisfa i requisiti avanzati di risoluzione dei nomi di Servizi di dominio Active Directory di Windows Server. Ad esempio, non supporta i record SRV dinamici e così via. La risoluzione dei nomi è un elemento di configurazione critico per i controller di domino e i client aggiunti a un dominio. I controller di dominio devono essere in grado di registrare i record di risorse e di risolvere altri record di risorse del controller di dominio.
Per motivi di prestazioni e tolleranza di errore, la soluzione ottimale consiste nell'installare il servizio DNS di Windows Server nei controller di dominio eseguiti in Azure. Configurare quindi le proprietà della rete virtuale con il nome e l'indirizzo IP del server DNS. All'avvio di altre macchine virtuali nella rete virtuale, vengono configurate le relative impostazioni del resolver del client DNS con il server DNS come parte dell'allocazione dinamica di indirizzi IP dinamici.

> [!NOTE]
> Non è possibile aggiungere i computer locali a un dominio Active Directory di Servizi di dominio Active Directory di Windows Server ospitato in Azure direttamente su Internet. I requisiti della porta per Active Directory e l'operazione di aggiunta al dominio non consentono di esporre direttamente a Internet le porte necessarie e, in effetti, un controller di dominio intero.
> 
> 

Le VM registrano il relativo nome DNS automaticamente all'avvio o in caso di modifica di un nome.

Per altre informazioni su questo esempio e su un altro che illustra il provisioning della prima VM e l'installazione in quest'ultima di Servizi di dominio Active Directory, vedere [Installare una nuova foresta Active Directory in una rete virtuale di Azure](active-directory-new-forest-virtual-machine.md). Per altre informazioni sull'uso di Windows PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs) e [Cmdlet di gestione di Azure](/powershell/module/azurerm.compute/#virtual_machines).

### <a name="BKMK_DistributedDCs"></a>Controller di dominio con distribuzione geografica
Azure offre vantaggi quando si ospitano più controller di dominio in reti virtuali diverse:

* Tolleranza di errore multisito
* Prossimità fisica alle succursali (latenza più bassa)

Per informazioni sulla configurazione della comunicazione diretta tra reti virtuali, vedere [Configurare una connessione da rete virtuale a rete virtuale](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="BKMK_RODC"></a>Controller di dominio di sola lettura
È necessario scegliere se distribuire controller di dominio scrivibili o di sola lettura. Si potrebbe pensare di distribuire controller di dominio di sola lettura perché non si avrà un controllo fisico su di essi, ma questi controller sono progettati per essere distribuiti nelle sedi in cui la sicurezza fisica è a rischio, ad esempio le succursali.

Azure non presenta rischi di sicurezza fisica di una succursale, ma i controller di dominio di sola lettura potrebbero comunque risultare più economici perché le funzionalità che forniscono sono adatte a questi ambienti, anche se per motivi molto diversi. Ad esempio, i controller di dominio di sola lettura non hanno repliche in uscita e consentono di popolare selettivamente i segreti (password). D'altra parte, la mancanza di questi segreti potrebbe richiedere traffico in uscita su richiesta per la relativa convalida al momento dell'autenticazione di un utente o un computer. I segreti possono però essere prepopolati e memorizzati nella cache in modo selettivo.

I controller di dominio di sola lettura forniscono un vantaggio aggiuntivo per quanto riguarda i problemi relativi a informazioni personali e dati ad alto impatto aziendale, perché è possibile aggiungere attributi che contengono dati sensibili al set di attributi con filtro per controller di dominio di sola lettura. Il set di attributi con filtro è un set personalizzabile di attributi che non vengono replicati nei controller di dominio di sola lettura. È possibile usare il set di attributi con filtro come strumento di sicurezza qualora non sia possibile o non si vogliano archiviare informazioni personali e dati ad alto impatto aziendale in Azure. Per altre informazioni, vedere [set di attributi con filtro per controller di dominio di sola lettura[(https://technet.microsoft.com/library/cc753459)].

Verificare che le applicazioni siano compatibili con i controller di dominio di sola lettura che si prevede di usare. Molte applicazioni abilitate per Active Directory di Windows Server funzionano bene con i controller di dominio di sola lettura, ma l'esecuzione di alcune di queste può risultare inefficiente o non riuscire se non hanno accesso a un controller di dominio scrivibile. Per altre informazioni, vedere la Guida alla [Compatibilità delle applicazioni con i controller di dominio di sola lettura](https://technet.microsoft.com/library/cc755190).

### <a name="BKMK_GC"></a>Catalogo globale
È necessario scegliere se installare un catalogo globale. In una foresta a dominio singolo è necessario configurare tutti i controller di dominio come server di catalogo globale. I costi non aumenteranno perché non vi sarà alcun traffico di replica aggiuntivo.

In una foresta con più domini i cataloghi globali sono necessari per espandere le appartenenze a gruppi universali durante il processo di autenticazione. Se non si distribuisce un catalogo globale, i carichi di lavoro nella rete virtuale che eseguono l'autenticazione in un controller di dominio in Azure genereranno indirettamente traffico di autenticazione in uscita per l'esecuzione di query sui cataloghi globali locali durante ogni tentativo di autenticazione.

I costi associati ai cataloghi globali sono meno prevedibili perché ospitano ogni dominio (in parte). Se il carico di lavoro ospita un servizio con connessione Internet e autentica gli utenti in Servizi di dominio Active Directory di Windows Server, potrebbe essere difficile prevedere i costi. Per ridurre le query dei cataloghi globali al di fuori del sito cloud durante l'autenticazione, è possibile [abilitare la memorizzazione nella cache dell'appartenenza al gruppo universale](https://technet.microsoft.com/library/cc816928).

### <a name="BKMK_InstallMethod"></a>Metodo di installazione
È necessario scegliere come installare i controller di dominio nella rete virtuale:

* Alzare di livello nuovi controller di dominio. Per altre informazioni, vedere [Installare una nuova foresta Active Directory in una rete virtuale di Azure](active-directory-new-forest-virtual-machine.md).
* Spostare il disco rigido virtuale di un controller di dominio virtuale locale nel cloud. In questo caso, è necessario assicurarsi che il controller di dominio virtuale locale venga "spostato", non "copiato" o "clonato".

Usare solo macchine virtuali per i controller di dominio, invece di macchine virtuali del ruolo "Web" o "di lavoro" di Azure. Sono durevoli e la durabilità di stato per un controller di dominio è essenziale. Le macchine virtuali di Azure sono progettate per i carichi di lavoro, ad esempio i controller di dominio.

Non usare SYSPREP per distribuire o clonare i controller di dominio. La possibilità di clonare i controller di dominio è disponibile solo a partire da Windows Server 2012. La funzionalità di clonazione richiede il supporto per VMGenerationID nell'hypervisor sottostante. Hyper-V in Windows Server 2012 e le reti virtuali di Azure supportano entrambi VMGenerationID, come fanno i fornitori di software di virtualizzazione di terze parti.

### <a name="BKMK_PlaceDB"></a>Posizione del database di Servizi di dominio Active Directory di Windows Server e di SYSVOL
Selezionare la posizione per il database di Servizi di dominio Active Directory di Windows Server, i log e SYSVOL. Devono essere distribuiti nei dischi dati di Azure.

> [!NOTE]
> Le dimensioni dei dischi dati di Azure sono vincolate a 1 TB.
> 
> 

Per impostazione predefinita, le unità disco dati non memorizzano le scritture nella cache. Le unità disco dati collegate a una macchina virtuale usano la funzionalità cache write-through. La funzionalità cache write-through assicura il commit della scrittura nell'archiviazione di Azure durevole prima del completamento della transazione dal punto di vista del sistema operativo della VM. Garantisce durabilità, ma le scritture sono leggermente più lente.

Questo aspetto è importante per Servizi di dominio Active Directory di Windows Server perché la funzionalità cache write-behind su disco invalida i presupposti del controller di dominio. Servizi di dominio Active Directory di Windows Server prova a disabilitare la cache in scrittura, ma spetta al sistema di I/O su disco riconoscere l'impostazione. In determinate circostanze, la mancata disabilitazione della cache in scrittura può introdurre il rollback degli USN, causando oggetti residui e altri problemi.

La procedura consigliata per i controller di dominio virtuali prevede quanto segue:

* Impostare Preferenze cache dell'host del disco dati di Azure su NESSUNA. In questo modo si evitano problemi di memorizzazione nella cache in scrittura per le operazioni di Servizi di dominio Active Directory.
* Archiviare il database, i log e SYSVOL nello stesso disco dati o in dischi dati separati. In genere, si tratta di un disco separato da quello usato per il sistema operativo stesso. L'aspetto più importante è che il database di Servizi di dominio Active Directory di Windows Server e SYSVOL non devono essere archiviati in un tipo di disco del sistema operativo Azure. Per impostazione predefinita, il processo di installazione di Servizi di dominio Active Directory di Windows Server installa i componenti nella cartella %systemroot%, ma questa NON è una soluzione consigliata per Azure.

### <a name="BKMK_BUR"></a>Backup e ripristino
Tenere presente quali elementi sono o meno supportati per il backup e ripristino di un controller di dominio in generale e, più in particolare, quelli in esecuzione in una VM. Vedere [Considerazioni sul backup e il ripristino dei controller di dominio virtualizzati](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv#backup_and_restore_considerations_for_virtualized_domain_controllers).

Creare backup dello stato del sistema usando solo software per backup che riconosca specificatamente i requisiti di backup per Servizi di dominio Active Directory di Windows Server, ad esempio Windows Server Backup.

Non copiare o clonare file VHD di controller di dominio invece di eseguire backup regolari. Se è necessario un ripristino e l'operazione viene eseguita usando VHD clonati o copiati senza Windows Server 2012 e un hypervisor supportato, verranno introdotti gap USN.

### <a name="BKMK_FedSrvConfig"></a>Configurazione del server federativo
La configurazione di server federativi AD FS di Windows Server (servizi token di sicurezza) dipende in parte dal fatto che le applicazioni da distribuire in Azure richiedano o meno l'accesso alle risorse nella rete locale.

Se le applicazioni soddisfano i criteri seguenti, possono essere distribuite isolate dalla rete locale.

* Accettano token di sicurezza SAML
* Possono essere esposte a Internet
* Non accedono alle risorse locali

In questo caso, configurare i servizi token di sicurezza di AD FS di Windows Server come segue:

1. Configurare una foresta a dominio singolo isolata in Azure.
2. Fornire l'accesso federato alla foresta configurando una server farm federativa AD FS di Windows Server.
3. Configurare AD FS di Windows Server, server farm federativa e farm proxy server federativo, nella foresta locale.
4. Stabilire una relazione di trust federativa tra le istanze di Azure e locali di AD FS di Windows Server.

D'altra parte, se le applicazioni richiedono l'accesso alle risorse locali, è possibile configurare AD FS di Windows Server con l'applicazione in Azure come indicato di seguito:

1. Configurare la connettività tra le reti locali e Azure.
2. Configurare una server farm federativa AD FS di Windows Server nella foresta locale.
3. Configurare una farm proxy server federativo AD FS di Windows Server in Azure.

Questa configurazione offre il vantaggio di ridurre l'esposizione delle risorse locali, analogamente alla configurazione di AD FS di Windows Server con applicazioni in una rete perimetrale.

Si noti che in entrambi gli scenari è possibile stabilire relazioni di trust con più provider di identità, se è necessaria la collaborazione business-to-business.

### <a name="BKMK_CloudSvcConfig"></a>Configurazione di servizi cloud
I servizi cloud sono necessari se si vuole esporre una VM direttamente a Internet o esporre un'applicazione con bilanciamento del carico con connessione Internet. Ciò è possibile perché ogni servizio cloud offre un singolo indirizzo IP virtuale configurabile.

### <a name="BKMK_FedReqVIPDIP"></a>Requisiti del server federativo per l'indirizzamento IP pubblico e privato (confronto tra indirizzo IP dinamico e indirizzo IP virtuale)
Ogni macchina virtuale di Azure riceve un indirizzo IP dinamico. Un indirizzo IP dinamico è un indirizzo privato accessibile solo all'interno di Azure. Nella maggior parte dei casi sarà tuttavia necessario configurare un indirizzo IP virtuale per le distribuzioni di AD FS di Windows Server. L'indirizzo IP virtuale è necessario per esporre a Internet gli endpoint AD FS di Windows Server che saranno usati da partner e client federativi per l'autenticazione e la gestione continuativa. Un indirizzo IP virtuale è una proprietà di un servizio cloud che contiene una o più macchine virtuali di Azure. Se le sessioni dell'applicazione in grado di riconoscere attestazioni distribuita in Azure e AD FS di Windows Server sono entrambi con connessione Internet e condividono porte comuni, per ognuna sarà necessario un indirizzo IP virtuale specifico e sarà quindi necessario creare un servizio cloud per l'applicazione e un altro per AD FS di Windows Serve.

Per le definizioni dei termini "indirizzo IP virtuale", "condizioni" e "indirizzo IP dinamico", vedere [Termini e definizioni](#BKMK_Glossary).

### <a name="BKMK_ADFSHighAvail"></a>Configurazione con disponibilità elevata di AD FS di Windows Server
Anche se è possibile distribuire servizi federativi AD FS di Windows Server autonomi, è consigliabile distribuire una farm con almeno due nodi sia per il servizio token di sicurezza di AD FS che per i proxy per gli ambienti di produzione.

Per stabilire le opzioni di configurazione della distribuzione che meglio soddisfano esigenze particolari, vedere [Considerazioni sulla topologia di distribuzione di AD FS 2.0](https://technet.microsoft.com/library/gg982489) nella [Guida alla progettazione di AD FS 2.0](https://technet.microsoft.com/library/dd807036).

> [!NOTE]
> Per ottenere il bilanciamento del carico per gli endpoint AD FS di Windows Server in Azure, configurare tutti i membri della farm AD FS di Windows Server nello stesso servizio cloud e usare la funzionalità di bilanciamento del carico di Azure per le porte HTTP (80 predefinita) e HTTPS (443 predefinita). Per altre informazioni, vedere l'articolo relativo al [probe di bilanciamento del carico di Azure](https://msdn.microsoft.com/library/azure/jj151530).
> Il bilanciamento del carico di rete di Windows Server non è supportato in Azure.
> 
> 

