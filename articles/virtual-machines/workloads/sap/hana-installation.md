---
title: Installare SAP HANA su SAP HANA in Azure (istanze di grandi dimensioni) | Documentazione Microsoft
description: Come installare SAP HANA in SAP HANA in Azure (istanza di grandi dimensioni).
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: b791be369016dd52d95ec727e46fd8b554c09047
ms.contentlocale: it-it
ms.lasthandoff: 04/28/2017


---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Come installare e configurare SAP HANA (istanze di grandi dimensioni) in Azure

L'installazione di SAP HANA è responsabilità dell'utente. È possibile avviare l'attività dopo la consegna di un nuovo SAP HANA sul server di Azure, istanze di grandi dimensioni, e dopo aver stabilito la connettività tra le reti virtuali di Azure e le unità delle istanze di grandi dimensioni HANA. Si noti che, in base ai criteri SAP, l'installazione di SAP HANA deve essere eseguita da un installatore qualificato per eseguire l'installazione SAP HANA, ovvero un utente che ha superato l'esame di certificazione Certified SAP Technology Associate – SAP HANA Installation, o da un integratore di sistemi (SI) SAP certificato.

## <a name="first-steps-after-receiving-the-hana-large-instance-units"></a>Procedura da adottare subito dopo aver ricevuto le unità di istanza HANA di grandi dimensioni

**Primo passaggio** dopo aver ricevuto l'istanza di grandi dimensioni HANA e aver stabilito che l'accesso e la connettività alle istanze consiste nel registrare il sistema operativo dell'istanza con il provider del sistema operativo. Ciò include la registrazione del sistema operativo Linux SUSE in un'istanza di SMT SUSE che è stata distribuita. Oppure è necessario registrare il sistema operativo Red Hat con la gestione sottoscrizione di RedHat a cui è necessario connettersi. Vedere anche le osservazioni in questo [documento](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Questo passaggio è necessario anche per consentire di inserire le patch nel sistema operativo in futuro. Un'attività che rientra tra le responsabilità del cliente. 

Il **secondo passaggio** consiste nel verificare la presenza di nuove patch e consente di correggere le versioni del sistema operativo specifiche. Verificare che il livello di patch dell'istanza di grandi dimensioni HANA sia allo stato più recente. In base all'intervallo delle patch/rilasci del sistema operativo e alle modifiche in base all'immagine che Microsoft può distribuire potrebbero esserci casi in cui le patch potrebbero non essere incluse. Di conseguenza è un passaggio obbligatorio in seguito alla sostituzione di un'unità di istanza di grandi dimensioni HANA e all'installazione del sistema operativo registrato con il distributore di Linux, per controllare se le patch relative a sicurezza, funzionalità, prestazioni e disponibilità sono state rilasciate nel frattempo dal fornitore specifico di Linux e devono essere applicate.

Il **terzo passaggio** consiste nel controllare le note SAP relative all'installazione e alla configurazione di SAP HANA nella specifica versione del sistema operativo. A causa della modifica dei suggerimenti o delle modifiche alle note su SAP o alle configurazioni che dipendono da singoli scenari di installazione, Microsoft non sarà sempre in grado di configurare correttamente l'unità di istanza di grandi dimensioni HANA. È pertanto obbligatorio per il cliente, leggere le note SAP, almeno quelle elencate di seguito, controllare che le configurazioni della versione del sistema operativo necessarie vengano applicate alle impostazioni di configurazione laddove questa operazione non sia già stata eseguita.

In particolare, controllare i parametri seguenti e adattarli a quanto segue:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

A partire da SLES12 SP1 e RHEL 7.2, è necessario impostare questi parametri in un file di configurazione nella directory /etc/sysctl.d. Ad esempio, è necessario creare un file di configurazione con il nome 91-NetApp-HANA.conf. Per le versioni precedenti di SLES e RHEL, questi parametri devono essere impostati in in/etc/sysctl.conf.

Per tutte le versioni di RHEL e a partire da SLES12, il parametro 
- sunrpc.tcp_slot_table_entries = 128

deve essere impostato in in/etc/modprobe.d/sunrpc-local.conf. Se il file non esiste, è necessario innanzitutto crearlo aggiungendo la voce seguente: 
- options sunrpc tcp_max_slot_table_entries=128

Il **quarto passaggio** consiste nel controllare l'ora del sistema dell'unità di istanza di grandi dimensioni HANA. Le istanze verranno distribuite con un fuso orario del sistema che rappresenta la posizione dell'area di Azure in cui si trova il timbro dell'istanza di grandi dimensioni istanza HANA. È possibile modificare l'ora o il fuso orario del sistema delle istanze di cui si è proprietari. In questo modo e ordinando istanze multiple nel tenant, ci si prepara nel caso in cui sia necessario adattare il fuso orario delle istanze appena consegnate. Le operazioni di Microsoft non eseguono altre azioni sul fuso orario del sistema impostato con le istanze in seguito alla consegna. Le istanze appena distribuite pertanto non possono essere impostate nello stesso fuso orario di quello modificato. È responsabilità del cliente quindi controllare e se necessario adattare il fuso orario delle istanze consegnate. 

Il **quinto passaggio** consiste nel controllare etc/hosts. Al momento della consegna, i pannelli hanno diversi indirizzi IP assegnati per scopi diversi. Vedere la sezione successiva. Controllare etc/hosts. Nei casi in cui le unità vengono aggiunte a un tenant esistente, non è prevista che etc/hosts dei sistemi appena distribuiti venga mantenuto correttamente con gli indirizzi IP dei sistemi precedentemente distribuiti. È compito del cliente quindi verificare le impostazioni corrette in modo che un'istanza appena distribuita possa interagire e risolvere i nomi delle unità precedentemente distribuite nel tenant. 

## <a name="networking"></a>Rete
Si presuppone che siano state seguite le indicazioni relative alla progettazione delle reti virtuali di Azure e alla connessione di queste reti alle istanze di grandi dimensioni HANA come descritto in questi documenti:

- [Panoramica e architettura di SAP HANA (istanze di grandi dimensioni) in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Infrastruttura e connettività a SAP HANA (istanze di grandi dimensioni) in Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Esistono alcuni dettagli che vale la pena citare sulle reti delle singole unità. Ogni unità di istanza di grandi dimensioni HANA include due o tre indirizzi IP assegnati a due o tre porte NIC dell'unità. Tre indirizzi IP vengono usati nelle configurazioni con scalabilità orizzontale HANA e nello scenario di replica DFS HANA. Uno degli indirizzi IP assegnati alla scheda di interfaccia di rete dell'unità è esterno al pool di indirizzi IP del server che è stato descritto in [Panoramica e architettura di SAP HANA (istanze di grandi dimensioni) in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

La distribuzione per le unità con due indirizzi IP assegnati dovrebbe appare come segue:

- A eth0.xx deve essere assegnato un indirizzo IP non compreso nell'intervallo di indirizzi del pool di indirizzi IP del Server inviati a Microsoft. Questo indirizzo IP deve essere usato per la gestione in /etc/hosts del sistema operativo.
- A eth1.xx deve essere assegnato un indirizzo IP usato per la comunicazione con NFS. Pertanto, tali indirizzi **NON** devono essere gestiti in etc/hosts per consentire il traffico da istanza a istanza nel tenant.

Per i casi di distribuzione di replica DFS HANA o HANA con scalabilità orizzontale, una configurazione del pannello con due indirizzi IP assegnati non è appropriata. In caso siano stati assegnati solo due indirizzi IP e si desidera distribuire tale configurazione, contattare Microsoft Service Management per ottenere un terzo indirizzo IP in una VLAN terza assegnata. Per le unità di istanza di grandi dimensioni HANA con tre indirizzi IP assegnati alle tre porte NIC, si applica quanto segue:

- A eth0.xx deve essere assegnato un indirizzo IP non compreso nell'intervallo di indirizzi del pool di indirizzi IP del Server inviati a Microsoft. Questo indirizzo, pertanto, IP deve non essere usato per la gestione in /etc/hosts del sistema operativo.
- A eth1.xx deve essere assegnato un indirizzo IP usato per la comunicazione con l'archivio NFS. Questo tipo di indirizzi, quindi, non deve essere conservato in etc/host.
- eth2.xx deve essere usato esclusivamente in etc/hosts per la comunicazione tra istanze diverse. Queste sono anche gli indirizzi IP che devono essere mantenuti nelle configurazioni con scalabilità orizzontale HANA, in base a come vengono usati gli indirizzi IP HANA per la configurazione tra i nodi.



## <a name="storage"></a>Archiviazione

Il layout di archiviazione per SAP HANA in Azure (istanze di grandi dimensioni) viene configurato da SAP HANA in Azure Service Management tramite procedure consigliate SAP: vedere il white paper sui [requisiti di archiviazione di SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Man mano che si prosegue nella lettura del documento e si cerca un'unità di istanza di grandi dimensioni HANA, ci si rende conto che le unità hanno un volume del disco piuttosto grande per HANA/data e un volume per HANA/log/backup. Il motivo per cui abbiamo le dimensioni di HANA/data sono molto grandi è che gli snapshot di archiviazione offerti al cliente usano lo stesso volume del disco. Ciò significa che più snapshot di archiviazione vengono eseguiti, maggiore sarà lo spazio necessario. Il volume di HANA/log/backup non è pensato come volume in cui inserire i backup di database, ma per essere sfruttato come volume di backup per il log delle transazioni HANA. Nelle versioni successive del servizio self-service di archiviazione degli snapshot questo volume specifico dovrà avere snapshot e repliche più frequenti per il sito di ripristino di emergenza se si desidera acconsentire esplicitamente alla funzionalità di ripristino di emergenza offerta dall'infrastruttura dell'istanza di grandi dimensioni HANA. Per informazioni dettagliate, vedere [Disponibilità elevata e ripristino di emergenza di SAP HANA (istanze di grandi dimensioni) in Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

Oltre alle risorse di archiviazione offerte, è possibile capacità di archiviazione aggiuntiva per incrementi di 1 TB. Questo ulteriore spazio di archiviazione può essere aggiunto come nuovi volumi alle istanze HANA di grandi dimensioni.

Durante il caricamento Microsoft Service Management, il cliente specifica un ID utente (UID) e un ID di gruppo (GID) per l'<SID>utente ADM e il gruppo sapsys, ad esempio: 1000, 500. Durante l'installazione del sistema SAP HANA è necessario usare questi stessi valori.

Il controller di archiviazione e i nodi nei timestamp dell'istanza di grandi dimensioni sono sincronizzati con i server NTP. Poiché si sincronizza SAP HANA nelle unità Azure (istanze di grandi dimensioni) e le VM di Azure in un server NTP, non si dovrebbero verificare sfasamenti di orario significativi tra l'infrastruttura e le unità di calcolo nei timestamp di Azure o dell'istanza di grandi dimensioni.

Per ottimizzare la SAP HANA per la memoria sottostante, è necessario impostare anche i parametri di configurazione di SAP HANA seguenti:

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
Per le versioni 1.0 di SAP HANA fino a SPS12, questi parametri possono essere impostati durante l'installazione del database SAP HANA, come descritto in [SAP Note #2267798 - Configuration of the SAP HANA Database](https://launchpad.support.sap.com/#/notes/2267798) (Nota SAP #2267798 - Configurazione del database SAP HANA)

È anche possibile configurare i parametri dopo l'installazione del database SAP HANA tramite il framework hdbparam. 

Con SAP HANA 2.0, il framework hdbparam è stato deprecato. Di conseguenza il parametro deve essere impostato tramite i comandi SQL. Per informazioni dettagliate, vedere [SAP Note #2399079: Elimination of hdbparam in HANA 2](https://launchpad.support.sap.com/#/notes/2399079) (Nota SAP #2399079: eliminazione di hdbparam HANA 2).


## <a name="operating-system"></a>Sistema operativo

[SUSE Linux Enterprise Server 12 SP1 for SAP Applications](https://www.suse.com/products/sles-for-sap/hana) è la distribuzione di Linux installata per SAP HANA in Azure (istanze di grandi dimensioni). Questa particolare distribuzione offre funzionalità specifiche di SAP &quot;predefinite&quot; (inclusi parametri preimpostati per l'esecuzione efficace si SAP in SLES).

Vedere il [white paper sulle librerie di risorse](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) sul sito Web di SUSE e le informazioni relative a [SAP in SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) su SAP Community Network (SCN) per varie risorse utili relative alla distribuzione di SAP HANA in SLES (inclusa la configurazione di elevata disponibilità, la protezione avanzata specifica per le operazioni di SAP e altro ancora).

Utili collegamenti aggiuntivi riguardanti SLES:

- [Sito relativo a SAP HANA in SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Procedure consigliate per SAP: replica di accodamento e SAP NetWeaver in SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)
- [ClamSAP: SLES Virus Protection for SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (incluso SLES 12 for SAP Applications)

Note di supporto SAP applicabili all'implementazione di SAP HANA su SLES 12 SP1:

- [Nota di supporto SAP #1944799: linee guida di SAP HANA per l'installazione del sistema operativo SLES](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
- [Nota di supporto SAP #2205917: impostazioni del sistema operativo consigliate per il database di SAP HANA per SLES 12 for SAP Applications](https://launchpad.support.sap.com/#/notes/2205917/E)
- [Nota di supporto SAP #1984787: note di installazione per SUSE Linux Enterprise Server 12](https://launchpad.support.sap.com/#/notes/1984787)
- [Nota di supporto SAP #171356: informazioni generali sul software SAP in Linux](https://launchpad.support.sap.com/#/notes/1984787)
- [Nota di supporto SAP #1391070: soluzioni UUID Linux](https://launchpad.support.sap.com/#/notes/1391070)

## <a name="time-synchronization"></a>Sincronizzazione degli orari

SAP è molto sensibili alle differenze di orario per i vari componenti che costituiscono il sistema SAP. I dump SAP ABAP brevi con il titolo di errore ZDATE\_LARGE\_TIME\_DIFF sono probabilmente familiari per chi ha lavorato a lungo con SAP (base), in quanto questi dump brevi compaiono quando l'ora di sistema di server o VM differenti è troppo sfasata.

Per SAP HANA in Azure (istanze di grandi dimensioni), la sincronizzazione dell'ora eseguita in Azure non si applica alle unità di calcolo nei timestamp dell'istanza di grandi dimensioni. Questo non vale per l'esecuzione delle applicazioni SAP in modo nativo in Azure (nelle VM), poiché Azure assicura la corretta sincronizzazione dell'ora del sistema. Di conseguenza è necessario configurare un server di riferimento ora separato che possa essere utilizzato dai server applicazioni SAP in esecuzione nelle VM di Azure e dalle istanze di database SAP HANA in esecuzione nelle istanze HANA di grandi dimensioni. L'ora dell'infrastruttura di archiviazione nei timestamp dell'istanza di grandi dimensioni è sincronizzata con i server NTP.



