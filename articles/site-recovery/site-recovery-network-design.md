---
title: Progettazione dell&quot;infrastruttura di rete per il ripristino di emergenza | Microsoft Docs
description: In questo articolo vengono illustrate alcune considerazioni sulla progettazione di rete per Azure Site Recovery
services: site-recovery
documentationcenter: 
author: prateek9us
manager: jwhit
editor: 
ms.assetid: ce787731-d930-4f00-a309-e2fbc2e1f53b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/27/2017
ms.author: pratshar
ms.translationtype: Human Translation
ms.sourcegitcommit: 6e6d05d7a7595e17d026be6a448b2fa2cca9b816
ms.openlocfilehash: a62fe406af18c9c7d9b58839bfa0d6e785b614ef
ms.contentlocale: it-it
ms.lasthandoff: 02/22/2017


---
# <a name="designing-your-network-for-disaster-recovery"></a>Progettazione della rete per il ripristino di emergenza

Questo articolo si rivolge ai professionisti IT che sono responsabili dell'architettura, dell'implementazione e del supporto dell'infrastruttura di continuità aziendale e ripristino di emergenza (BCDR, Business Continuity and Disaster Recovery) e che vogliono sfruttare Microsoft Azure Site Recovery (ASR) per supportare e migliorare i servizi BCDR. Questo documento illustra le considerazioni pratiche per la distribuzione di server System Center Virtual Machine Manager, i pro e i contro delle subnet estese rispetto al failover su subnet e come strutturare il ripristino di emergenza per siti virtuali in Microsoft Azure.

## <a name="overview"></a>Overview
[Azure Site Recovery (ASR)](https://azure.microsoft.com/services/site-recovery/) è un servizio di Microsoft Azure che orchestra la protezione e il ripristino delle applicazioni virtualizzate per finalità di continuità aziendale e ripristino di emergenza. Questo documento è una guida al processo di progettazione delle reti, che si concentra sull'architettura di intervalli IP e di subnet nel sito di ripristino di emergenza, quando si replicano macchine virtuali (VM) usando Site Recovery.

Questo articolo illustra inoltre come Site Recovery consenta di architettare e implementare un data center virtuale multisito per supportare i servizi BCDR in fase di test o in caso di emergenza.

In un mondo in cui tutti si aspettano una connettività 24/7, è più importante che mai mantenere operative l'infrastruttura e le applicazioni. Lo scopo della continuità aziendale e del ripristino di emergenza è di ripristinare i componenti in cui si sono verificati problemi in modo che l'organizzazione possa rapidamente riprendere le normali operazioni. Sviluppare strategie di ripristino di emergenza per gestire improbabili eventi disastrosi è molto difficile, a causa dell'intrinseca difficoltà di prevedere il futuro, soprattutto in relazione a eventi improbabili, e del costo elevato richiesto dalle misure di protezione adeguate contro catastrofi di vasta portata.

Nell'ambito di un piano di ripristino di emergenza è necessario definire l'obiettivo del tempo di ripristino (RTO, Recovery Time Objective) e l'obiettivo del punto di ripristino (RPO, Recovery Point Objective), indispensabili per la pianificazione BCDR. Quando si verifica un'emergenza nel data center del cliente, usando Azure Site Recovery, il cliente può portare rapidamente (RTO basso) online le macchine virtuali replicate presenti nel data center secondario o in Microsoft Azure con una perdita di dati minima (RPO basso).

Il failover è possibile grazie ad ASR che all'inizio copia le macchine virtuali designate dal data center primario al data center secondario o in Azure (a seconda dello scenario) e quindi aggiorna periodicamente le repliche. Durante la pianificazione dell'infrastruttura, la progettazione della rete deve essere considerata un potenziale collo di bottiglia che può impedire di raggiungere gli obiettivi RTO e RPO aziendali.  

Quando gli amministratori pianificano di distribuire una soluzione di ripristino di emergenza, una delle prime cose a cui pensano è come poter raggiungere la macchina virtuale al termine del failover. Azure Site Recovery consente all'amministratore di scegliere la rete a cui una macchina virtuale può connettersi dopo il failover. Se il sito primario è gestito da un server VMM, questa operazione avviene tramite il mapping di rete. Per altre informazioni dettagliate, vedere l'articolo sulla [preparazione al mapping di rete](site-recovery-vmm-to-vmm.md#prepare-for-network-mapping) .

Durante la progettazione della rete per il sito di ripristino, l'amministratore ha due scelte:

* Usare un intervallo di indirizzi IP diverso per la rete nel sito di ripristino. In questo scenario la macchina virtuale dopo il failover otterrà un nuovo indirizzo IP e l'amministratore dovrà eseguire un aggiornamento DNS. Per altre informazioni, leggere [qui](site-recovery-test-failover-vmm-to-vmm.md#preparing-infrastructure-for-test-failover).
* Usare lo stesso intervallo di indirizzi IP per la rete nel sito di ripristino. In alcuni scenari gli amministratori preferiscono mantenere gli indirizzi IP che hanno nel sito primario dopo il failover. In un normale scenario un amministratore dovrà aggiornare le route per indicare la nuova posizione degli indirizzi IP, ma nello scenario in cui una VLAN estesa viene distribuita tra il sito primario e quello di ripristino mantenere gli indirizzi IP per le macchine virtuali diventa un'opzione interessante. Mantenere gli stessi indirizzi IP semplifica il processo di ripristino eliminando i passaggi post-failover correlati alla rete.

Quando gli amministratori pianificano di distribuire una soluzione di ripristino di emergenza, una delle prime cose a cui pensano è come poter raggiungere le applicazioni al termine del failover. Le applicazioni moderne necessitano quasi sempre di un collegamento in rete, quindi lo spostamento fisico di un servizio da un sito a un altro è difficoltoso. Nelle soluzioni di ripristino di emergenza questo problema viene gestito principalmente in due modi. Il primo approccio consiste nel mantenere fissi gli indirizzi IP. Nonostante lo spostamento dei servizi e la presenza dei server di hosting in posizioni fisiche diverse, le applicazioni mantengono la configurazione degli indirizzi IP nella nuova posizione. Il secondo approccio richiede la modifica completa dell'indirizzo IP durante la transizione al sito ripristinato. Ogni approccio presenta diverse varianti di implementazione che vengono riepilogate sotto.

Durante la progettazione della rete per il sito di ripristino, l'amministratore ha due scelte:

## <a name="option-1-retain-ip-addresses"></a>Opzione 1: Mantenere gli indirizzi IP
Dal punto di vista del processo di ripristino di emergenza, l'uso di indirizzi IP fissi si presenta come il metodo più semplice da implementare. In realtà è l'approccio meno comune a causa delle diverse potenziali difficoltà che comporta. Azure Site Recovery consente di conservare gli indirizzi IP in tutte le situazioni. Prima di decidere di mantenere l'IP, è opportuno considerare i vincoli che vengono imposti alle funzionalità di failover. Verranno analizzati i fattori che possono indurre a decidere di mantenere gli indirizzi IP oppure no. A tale scopo, è possibile usare una subnet estesa o eseguire un failover completo della subnet.

### <a name="stretched-subnet"></a>Subnet estesa
La subnet è resa disponibile simultaneamente sia nella località primaria che in quella di ripristino di emergenza. In altre parole, è possibile spostare un server e la relativa configurazione IP (livello 3) nel secondo sito e la rete instraderà automaticamente il traffico nella nuova posizione. Dal punto di vista del server si tratta di un'operazione semplice, tuttavia presenta una serie di difficoltà:

* Considerando il livello 2 (livello di collegamento dati), tale approccio richiederà apparecchiature di rete in grado di gestire una VLAN estesa, ma non dovrebbe essere un problema perché oggi questo tipo di apparecchiature è ampiamente disponibile. Il secondo e più difficile problema è il fatto che, estendendo la VLAN, il dominio di errore potenziale viene esteso a entrambi i siti, creando in sostanza un singolo punto di errore. Anche se è un'eventualità improbabile, è accaduto che una broadcast storm fosse avviata, ma non potesse essere isolata. Sono state riscontrate opinioni diverse in merito a quest'ultimo problema e se da un lato sono state effettuate con successo molte implementazioni, dall'altro c'è stato anche un netto rifiuto di implementare questa tecnologia.
* Non è possibile adottare la subnet estesa se si usa Microsoft Azure come sito di ripristino di emergenza.

### <a name="subnet-failover"></a>Failover sulla subnet
È possibile implementare il failover sulla subnet per ottenere i vantaggi della soluzione di subnet estesa illustrati sopra senza estendere la subnet su più siti. Qui qualsiasi subnet specificata sarà presente nel sito 1 o nel sito 2, ma mai in entrambi i siti contemporaneamente. Per mantenere lo spazio degli indirizzi IP in caso di failover, è possibile programmare l'infrastruttura del router per lo spostamento delle subnet da un sito a un altro. In uno scenario di failover le subnet verranno spostate con le macchine virtuali protette associate. Lo svantaggio principale di questo approccio è che, in caso di errore, è necessario spostare l'intera subnet. Ciò può funzionare, ma può influire sulle considerazioni relative alla granularità di failover.

Verrà ora esaminato come un'azienda fittizia, denominata Contoso, possa replicare le proprie macchine virtuali in un percorso di ripristino, durante il failover dell'intera subnet. Verrà prima di tutto esaminato come Contoso possa gestire le subnet durante la replica delle VM tra due posizioni locali e quindi verrà illustrato il funzionamento del failover sulla subnet quando [si usa Azure come sito di ripristino di emergenza](#failover-to-azure).

#### <a name="failover-to-a-secondary-on-premises-site"></a>Eseguire un failover a un sito locale secondario
Verrà ora esaminato uno scenario in cui si vuole mantenere l'IP di ogni VM ed effettuare il failover della subnet completa. Il sito primario dispone di applicazioni in esecuzione nella subnet 192.168.1.0/24. Quando il failover viene effettuato, tutte le macchine virtuali che fanno parte di questa subnet verranno sottoposte a failover nel sito di ripristino e manterranno gli indirizzi IP. Le route dovranno essere modificate in modo appropriato per rispecchiare il fatto che tutte le macchine virtuali appartenenti alla subnet 192.168.1.0/24 sono state spostate nel sito di ripristino.

Nell'illustrazione seguente le route tra il sito primario e il sito di ripristino, il terzo sito e il sito primario e il terzo sito e il sito di ripristino dovranno essere modificate in modo appropriato.

Le figure seguenti illustrano le subnet prima del failover. La subnet 192.168.0.1/24 è attiva nel sito primario prima del failover e diventa attiva nel sito di ripristino dopo il failover.

![Prima del failover](./media/site-recovery-network-design/network-design2.png)

Prima del failover

La figura seguente illustra le reti e le subnet dopo il failover.

![Dopo il failover](./media/site-recovery-network-design/network-design3.png)

Dopo il failover

Nel sito secondario la subnet è locale e si usa un server VMM per gestirla, quindi quando si abilita la protezione per una macchina virtuale specifica, ASR dovrà allocare le risorse di rete in base al flusso di lavoro seguente:

* ASR alloca un indirizzo IP per ogni interfaccia di rete nella macchina virtuale dal pool di indirizzi IP statici definiti nella rete pertinente per ogni istanza di System Center VMM.
* Se l'amministratore definisce nel sito di ripristino per la rete lo stesso pool di indirizzi IP della rete nel sito primario, durante l'allocazione dell'indirizzo IP nella macchina virtuale di replica ASR allocherà lo stesso indirizzo IP della macchina virtuale primaria.  L'IP è riservato in VMM, ma non è impostato come IP di failover. L'IP di failover viene impostato subito prima del failover.

![Mantenere l'indirizzo IP](./media/site-recovery-network-design/network-design4.png)


L'immagine precedente illustra le impostazioni TCP/IP del failover per la macchina virtuale di replica (nella console di Hyper-V). Queste impostazioni verranno popolate subito prima dell'avvio della macchina virtuale dopo un failover.

Se non è disponibile lo stesso IP, ASR allocherà un altro indirizzo IP disponibile dal pool di indirizzi IP definiti.

Dopo avere abilitato la protezione nella VM, è possibile usare lo script di esempio seguente per verificare l'IP allocato nella macchina virtuale. Lo stesso IP dovrebbe essere impostato come IP di failover e assegnato alla VM al momento del failover:

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

> [!NOTE]
> Nello scenario in cui le macchine virtuali usano DHCP, la gestione degli indirizzi IP è completamente al di fuori del controllo di ASR. Un amministratore deve assicurarsi che il server DHCP che gestisce gli indirizzi IP nel sito di ripristino possa gestirli dallo stesso intervallo del sito primario.
>
>

#### <a name="failover-to-azure"></a>Failover in Azure
Azure Site Recovery (ASR) consente di usare Microsoft Azure come sito di ripristino di emergenza per le macchine virtuali. In questo caso, sarà necessario gestire un ulteriore vincolo.

Verrà ora esaminato uno scenario in cui una società fittizia, denominata Woodgrove Bank, ha un'infrastruttura locale che ospita le applicazioni line-of-business, mentre le applicazioni per dispositivi mobili sono ospitate in Azure. La connettività tra le VM di Woodgrove Bank in Azure e i server locali è fornita da una rete privata virtuale (VPN) da sito a sito (S2S). La VPN S2S consente di considerare la rete virtuale di Woodgrove Bank in Azure come un'estensione della rete locale di Woodgrove Bank. Questa comunicazione è abilitata dalla VPN S2S tra il perimetro di Woodgrove Bank e la rete virtuale di Azure. Ora Woodgrove vuole usare ASR per replicare i carichi di lavoro in esecuzione nel data center in Azure. Questa opzione soddisfa le esigenze di Woodgrove, che vuole un'opzione di ripristino di emergenza economica e può archiviare i dati in ambienti di cloud pubblico. Woodgrove deve tenere conto delle applicazioni e delle configurazioni che dipendono da indirizzi IP hardcoded, quindi ha la necessità di mantenere gli indirizzi IP delle rispettive applicazioni dopo il failover in Azure.

Woodgrove ha deciso di assegnare gli indirizzi IP dall'intervallo di indirizzi IP (172.16.1.0/24, 172.16.2.0/24) alle risorse in esecuzione in Azure.

Per consentire a Woodgrove di replicare le macchine virtuali in Azure mantenendo al contempo gli indirizzi IP, è necessario creare una rete virtuale di Azure, che dovrà essere un'estensione della rete locale, in modo che le applicazioni possano effettuare facilmente il failover dal sito locale ad Azure. Azure consente di aggiungere alle reti virtuali create in Azure la connettività VPN da sito a sito oltre che da punto a sito. Quando si configura la connessione da sito a sito, la rete di Azure consente di instradare il traffico verso il percorso locale (Azure lo definisce rete locale) solo se l'intervallo di indirizzi IP è diverso da quello degli indirizzi IP locali, perché Azure non supporta l'estensione delle subnet.  Ciò significa che se si dispone di una subnet 192.168.1.0/24 locale, è impossibile aggiungere una rete locale 192.168.1.0/24 nella rete di Azure. Ciò è previsto poiché Azure non rileva l'assenza di macchine virtuali attive nella subnet, né riconosce che la subnet viene creata solo per scopi di ripristino di emergenza. Per poter instradare correttamente il traffico di rete all'esterno di una rete di Azure, le subnet nella rete e quelle nella rete locale non devono essere in conflitto.

![Prima del failover sulla subnet](./media/site-recovery-network-design/network-design7.png)

Prima del failover

Per consentire a Woodgrove di soddisfare i requisiti aziendali, è necessario implementare i flussi di lavoro seguenti:

* Creare una rete aggiuntiva, definita rete di ripristino, in cui verranno create le macchine virtuali di cui verrà effettuato il failover.
* Per assicurarsi che l'IP per una VM venga mantenuto dopo un failover, passare alla scheda Configura nelle proprietà della VM, specificare lo stesso IP locale della VM e quindi fare clic su Salva. Quando viene effettuato il failover della VM, Azure Site Recovery assegnerà l'IP specificato alla macchina virtuale.

![Proprietà di rete](./media/site-recovery-network-design/network-design8.png)

Una volta attivato il failover e create le macchine virtuali nella rete di ripristino con l'IP desiderato, la connettività a questa rete può essere stabilita usando una [connessione da rete virtuale a rete virtuale](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Se necessario, è possibile creare script per questa azione.  Come illustrato nella sezione precedente relativa al failover sulla subnet, anche nel caso di failover in Azure sarà necessario modificare in modo appropriato le route per rispecchiare il fatto che la subnet 192.168.1.0/24 è stata spostata in Azure.

![Dopo il failover sulla subnet](./media/site-recovery-network-design/network-design9.png)

Dopo il failover

Se non è disponibile una 'rete di Azure', come illustrato nella figura precedente, è possibile creare una connessione VPN da sito a sito tra il 'sito primario' e la 'rete di ripristino' dopo il failover.  

## <a name="option-2-changing-ip-addresses"></a>Opzione 2: Modifica degli indirizzi IP
Questo approccio, in base a quanto descritto, sembra quello prevalente. Consiste nel modificare l'indirizzo IP di ogni VM coinvolta nel failover. Un inconveniente di questo approccio è che la rete in entrata deve "sapere" che l'applicazione che si trovava all'IPx ora si trova all'IPy. Anche se IPx e IPy sono nomi logici, le voci DNS in genere devono essere modificate o scaricate in tutta la rete e le voci memorizzate nella cache nelle tabelle di rete devono essere aggiornate o scaricate, quindi potrebbe verificarsi un tempo di inattività a seconda di come è stata configurata l'infrastruttura DNS. È possibile ridurre questi problemi usando valori TTL bassi nel caso di applicazioni Intranet e usando [Gestione traffico di Azure con ASR](http://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) per le applicazioni basate su Internet.

### <a name="changing-the-ip-addresses---illustration"></a>Modifica degli indirizzi IP - Illustrazione
Verrà ora esaminato lo scenario in cui si pianifica di usare IP diversi nel sito primario e in quello di ripristino. Nell'esempio seguente è compreso un terzo sito da cui è possibile accedere alle applicazioni ospitate nel sito primario o in quello di ripristino.

![Indirizzo IP diverso - Prima del failover](./media/site-recovery-network-design/network-design10.png)


Nell'immagine precedente, alcune applicazioni sono ospitate nella subnet 192.168.1.0/24 nel sito primario e sono state configurate per passare al sito di ripristino nella subnet 172.16.1.0/24 in seguito a un failover. La configurazione dei percorsi di rete o delle connessioni VPN è avvenuta in modo corretto, pertanto tutti e tre i siti dispongono dell'accesso reciproco.

Come illustrato nell'immagine seguente, dopo il failover di una o più applicazioni, queste verranno ripristinate nella subnet di ripristino. In questo caso non esiste il vincolo di effettuare il failover dell'intera subnet contemporaneamente. Non sono necessarie modifiche per riconfigurare la VPN o le route di rete. Un failover e alcuni aggiornamenti DNS verificheranno che le applicazioni rimangano accessibili. Se il DNS è configurato per consentire aggiornamenti dinamici, le macchine virtuali potrebbero registrarsi con il nuovo IP, una volta avviate dopo un failover.

![Indirizzo IP diverso - Dopo il failover](./media/site-recovery-network-design/network-design11.png)


Al termine del failover, la macchina virtuale di replica potrebbe avere un indirizzo IP diverso da quello della macchina virtuale primaria. Le macchine virtuali aggiorneranno il server DNS in uso dopo l'avvio. Di solito, occorre modificare o cancellare le voci DNS in tutta la rete, così come occorre aggiornare o cancellare le voci memorizzate nella cache nelle tabelle di rete, pertanto non è insolito riscontrare tempi di inattività durante tali modifiche. È possibile ridurre tale problema nei modi seguenti:

* Utilizzando valori TTL bassi per le applicazioni Intranet.
* Usando Gestione traffico di Azure con ASR per le applicazioni basate su Internet.
* Utilizzando il seguente script all'interno del piano di ripristino per aggiornare il server DNS, al fine di garantire un aggiornamento tempestivo (lo script non è obbligatorio se è stata configurata la registrazione di DNS dinamici)

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

### <a name="changing-the-ip-addresses--dr-to-azure"></a>Modifica degli indirizzi IP - Ripristino di emergenza in Azure
Il post di blog dedicato alla [configurazione dell'infrastruttura di rete per Microsoft Azure come sito di ripristino di emergenza](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) spiega come configurare l'infrastruttura di rete di Azure necessaria quando non è obbligatorio mantenere gli indirizzi IP. All'inizio viene descritta l'applicazione, quindi viene illustrato come configurare la rete in locale e in Azure e infine viene illustrato come effettuare un failover di test e un failover pianificato.

## <a name="next-steps"></a>Passaggi successivi
[Informazioni](site-recovery-vmm-to-vmm.md#prepare-for-network-mapping) sul mapping di reti di origine e destinazione da parte di Site Recovery quando viene usato un server VMM per gestire il sito primario.

