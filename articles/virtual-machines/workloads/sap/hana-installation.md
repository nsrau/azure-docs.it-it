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
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 280001f9057825b9dcd98c5180340a54e2e239cf
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Come installare e configurare SAP HANA (istanze di grandi dimensioni) in Azure

Di seguito sono disponibili alcune definizioni importanti, che è necessario conoscere prima di leggere questa guida. In [Panoramica e architettura di SAP HANA (istanze Large) in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) vengono illustrate le due classi diverse di istanze Large di Hana con:

- S72, S72m, S144, S144m, S192 e S192m, definiti SKU di "Classe di tipo I".
- S384, S384m, S384xm, S576, S768 e S960, definiti SKU di "Classe di tipo II".

L'identificatore di classe verrà usato in tutta la documentazione relativa alle istanze Large di HANA, per fare riferimento a diverse funzionalità e requisiti in base agli SKU di istanze Large di HANA.

Ecco altre definizioni di uso frequente:
- **Modulo per istanze di grandi dimensioni:** uno stack dell'infrastruttura hardware con certificazione SAP HANA TDI, dedicato all'esecuzione di istanze SAP HANA in Azure.
- **SAP HANA in Azure (istanze di grandi dimensioni):** nome ufficiale dell'offerta in Azure per l'esecuzione di istanze HANA in un ambiente hardware con certificazione SAP HANA TDI e distribuito in moduli per istanze grandi dimensioni in diverse aree di Azure. Il termine **istanze Large di HANA** correlato è un'abbreviazione di SAP HANA in Azure (istanze Large) ed è ampiamente usato in questa guida alla distribuzione tecnica.


L'installazione di SAP HANA è responsabilità dell'utente. È possibile avviare l'attività immediatamente dopo la consegna di un nuovo SAP HANA su server di Azure (istanze Large) e dopo avere stabilito la connettività tra reti virtuali di Azure e le unità di istanze Large di HANA. 

> [!Note]
> In base ai criteri SAP, l'installazione di SAP HANA deve essere eseguita da una persona certificata per l'esecuzione di installazioni di SAP HANA, ovvero una persona che ha superato l'esame Certified SAP Technology Associate o l'esame di certificazione per l'installazione di SAP HANA oppure da un integratore di sistemi con certificazione SAP.

Vedere di nuovo, soprattutto quando si prevede di installare HANA 2.0, [SAP Support Note #2235581 - SAP HANA: Supported Operating Systems](https://launchpad.support.sap.com/#/notes/2235581/E) (Nota di supporto SAP n. 2235581 - SAP HANA: sistemi operativi supportati) per verificare che il sistema operativo sia supportato con la versione di SAP HANA che si è deciso di installare. Come si può notare, il sistema operativo supportato per HANA 2.0 ha più restrizioni del sistema operativo supportato per HANA 1.0. 

## <a name="first-steps-after-receiving-the-hana-large-instance-units"></a>Procedura da adottare subito dopo aver ricevuto le unità di istanza HANA di grandi dimensioni

Il **primo passaggio** dopo aver ricevuto l'istanza Large di HANA e avere stabilito l'accesso e la connettività alle istanze consiste nel registrare il sistema operativo dell'istanza con il provider del sistema operativo. Questo passaggio include la registrazione del sistema operativo Linux SUSE in un'istanza di SMT SUSE che è stata distribuita in una VM in Azure. L'unità di istanze Large di HANA può connettersi a questa istanza di SMT, come illustrato più avanti in questa documentazione. Oppure è necessario registrare il sistema operativo Red Hat con la gestione sottoscrizione di RedHat a cui è necessario connettersi. Vedere anche le osservazioni in questo [documento](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Questo passaggio è necessario anche per consentire di applicare patch al sistema operativo. Un'attività che rientra tra le responsabilità del cliente. Per quanto riguarda SUSE, la documentazione relativa all'installazione e alla configurazione di SMT è disponibile [qui](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

Il **secondo passaggio** consiste nel verificare la presenza di nuove patch e consente di correggere le versioni del sistema operativo specifiche. Verificare che il livello di patch dell'istanza di grandi dimensioni HANA sia allo stato più recente. In base al momento in cui vengono rese disponibili nuove patch/versioni del sistema operativo e alle modifiche all'immagine che possono essere distribuite da Microsoft, è possibile che in alcuni casi non siano incluse le patch più recenti. Dopo aver acquisito un'unità di istanze Large di HANA è quindi obbligatorio verificare se nel frattempo sono state rilasciate dal fornitore Linux specifico patch rilevanti a livello di sicurezza, funzionalità, disponibilità e prestazioni che devono essere applicate.

Il **terzo passaggio** consiste nel controllare le note SAP relative all'installazione e alla configurazione di SAP HANA nella specifica versione del sistema operativo. A causa della modifica dei suggerimenti o delle modifiche alle note su SAP o alle configurazioni che dipendono da singoli scenari di installazione, Microsoft non sarà sempre in grado di configurare correttamente l'unità di istanza di grandi dimensioni HANA. Il cliente è quindi tenuto a leggere le note SAP correlate a SAP HANA nella versione Linux esatta. Occorre anche controllare le configurazioni della versione del sistema operativo necessarie e applicare le impostazioni di configurazione, se non è già stato fatto.

In particolare, controllare i parametri seguenti e modificarli se necessario:

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

Il **quarto passaggio** consiste nel controllare l'ora del sistema dell'unità di istanza di grandi dimensioni HANA. Le istanze vengono distribuite con un fuso orario che rappresenta la posizione dell'area di Azure in cui si trova lo stamp dell'istanza Large di HANA. È possibile modificare l'ora o il fuso orario del sistema delle istanze di cui si è proprietari. In questo modo e ordinando istanze multiple nel tenant, ci si prepara nel caso in cui sia necessario adattare il fuso orario delle istanze appena consegnate. Le operazioni di Microsoft non eseguono altre azioni sul fuso orario del sistema impostato con le istanze in seguito alla consegna. Le istanze appena distribuite pertanto non possono essere impostate nello stesso fuso orario di quello modificato. È responsabilità del cliente quindi controllare e se necessario adattare il fuso orario delle istanze consegnate. 

Il **quinto passaggio** consiste nel controllare etc/hosts. Al momento della consegna, i pannelli hanno diversi indirizzi IP assegnati per scopi diversi. Vedere la sezione successiva. Controllare il file etc/hosts. Nei casi in cui le unità vengono aggiunte a un tenant esistente, non è prevista che etc/hosts dei sistemi appena distribuiti venga mantenuto correttamente con gli indirizzi IP dei sistemi precedentemente distribuiti. È compito del cliente quindi verificare le impostazioni corrette in modo che un'istanza appena distribuita possa interagire e risolvere i nomi delle unità precedentemente distribuite nel tenant. 

## <a name="networking"></a>Rete
Si presuppone che siano state seguite le indicazioni relative alla progettazione delle reti virtuali di Azure e alla connessione di queste reti virtuali alle istanze Large di HANA come descritto in questi documenti:

- [Panoramica e architettura di SAP HANA (istanze di grandi dimensioni) in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Infrastruttura e connettività a SAP HANA (istanze di grandi dimensioni) in Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Esistono alcuni dettagli che vale la pena citare sulle reti delle singole unità. Ogni unità di istanza di grandi dimensioni HANA include due o tre indirizzi IP assegnati a due o tre porte NIC dell'unità. Tre indirizzi IP vengono usati nelle configurazioni con scalabilità orizzontale HANA e nello scenario di replica DFS HANA. Uno degli indirizzi IP assegnati alla scheda di interfaccia di rete dell'unità è esterno al pool di indirizzi IP del server che è stato descritto in [Panoramica e architettura di SAP HANA (istanze di grandi dimensioni) in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

La distribuzione per le unità con due indirizzi IP assegnati dovrebbe appare come segue:

- A eth0.xx deve essere assegnato un indirizzo IP non compreso nell'intervallo di indirizzi del pool di indirizzi IP del Server inviati a Microsoft. Questo indirizzo IP deve essere usato per la gestione in /etc/hosts del sistema operativo.
- A eth1.xx deve essere assegnato un indirizzo IP usato per la comunicazione con NFS. Pertanto, tali indirizzi **NON** devono essere gestiti in etc/hosts per consentire il traffico da istanza a istanza nel tenant.

Una configurazione del pannello con due indirizzi IP assegnati non è appropriata per i casi di distribuzione di tipo replica del sistema HANA o di HANA con scalabilità orizzontale. Se sono stati assegnati solo due indirizzi IP e si vuole distribuire una configurazione di questo tipo, contattare SAP HANA in Azure Service Management per ottenere un terzo indirizzo IP in una terza VLAN assegnata. Per le unità di istanza Large di HANA con tre indirizzi IP assegnati alle tre porte NIC, si applicano le regole di utilizzo seguenti:

- A eth0.xx deve essere assegnato un indirizzo IP non compreso nell'intervallo di indirizzi del pool di indirizzi IP del Server inviati a Microsoft. Questo indirizzo, pertanto, IP deve non essere usato per la gestione in /etc/hosts del sistema operativo.
- A eth1.xx deve essere assegnato un indirizzo IP usato per la comunicazione con l'archivio NFS. Questo tipo di indirizzi, quindi, non deve essere conservato in etc/host.
- eth2.xx deve essere usato esclusivamente in etc/hosts per la comunicazione tra istanze diverse. Questi indirizzi corrispondono agli indirizzi IP da mantenere nelle configurazioni HANA con scalabilità orizzontale come indirizzi IP usati da HANA per la configurazione tra nodi.



## <a name="storage"></a>Archiviazione

Il layout delle risorse di archiviazione per SAP HANA in Azure (istanze Large) viene configurato da SAP HANA in Azure Service Management tramite le indicazioni consigliate di SAP, come illustrato nel white paper [SAP HANA Storage Requirements](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) (Requisiti per le risorse di archiviazione SAP HANA). Le dimensioni approssimative dei diversi volumi dei diversi SKU di istanze Large di HANA sono illustrate in [Panoramica e architettura di SAP HANA (istanze Large) in Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Le convenzioni di denominazione dei volumi di archiviazione sono elencate nella tabella seguente:

| Utilizzo delle risorse di archiviazione | Nome del montaggio | Nome del volume | 
| --- | --- | ---|
| Dati HANA | /hana/data/SID/mnt0000<m> | Storage IP:/hana_data_SID_mnt00001_tenant_vol |
| Log HANA | /hana/log/SID/mnt0000<m> | Storage IP:/hana_log_SID_mnt00001_tenant_vol |
| Backup dei log HANA | /hana/log/backups | Storage IP:/hana_log_backups_SID_mnt00001_tenant_vol |
| Condivisione HANA | /hana/shared/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/shared |
| usr/sap | /usr/sap/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

Dove SID corrisponde all'ID di sistema dell'istanza HANA. 

Tenant corrisponde a un'enumerazione interna delle operazioni durante la distribuzione di un tenant.

Come si può notare, la condivisione HANA e usr/sap condividono lo stesso volume. La nomenclatura dei punti di montaggio include l'ID di sistema delle istanze HANA e i numeri di montaggio. Nelle distribuzioni con aumento delle prestazioni è disponibile un solo montaggio, ad esempio mnt00001. Nelle distribuzioni con scalabilità orizzontale, invece, il numero di montaggi corrisponde al numero di nodi di lavoro e nodi master. Per l'ambiente con scalabilità orizzontale i volumi relativi a dati, log e backup dei log sono condivisi e sono collegati a ogni nodo nella configurazione con scalabilità orizzontale. Per le configurazioni che eseguono più istanze di SAP viene creato un set diverso di volumi, che vengono collegati a un'unità di istanze Large di HANA.

Man mano che si prosegue nella lettura del documento e si cerca un'unità di istanze Large di HANA, ci si rende conto che le unità hanno un volume del disco piuttosto grande per HANA/data e un volume per HANA/log/backup. Le dimensioni di HANA/data sono così grandi perché gli snapshot di archiviazione offerti ai clienti usano lo stesso volume del disco. Maggiore è il numero di snapshot di archiviazione creati, maggiore sarà la quantità di spazio utilizzata dagli snapshot nei volumi di archiviazione assegnati. Il volume HANA/log/backup non è il volume in cui salvare i backup dei database. Le dimensioni sono appropriate per l'uso come volume di backup per i backup dei log delle transazioni HANA. Nelle versioni futuri degli snapshot di archiviazione self-service questo volume verrà modificato in modo da consentire snapshot più frequenti, oltre a repliche più frequenti nel sito di ripristino di emergenza, se si vuole scegliere esplicitamente la funzionalità di ripristino di emergenza fornita dall'infrastruttura delle istanze Large di HANA. Per informazioni dettagliate, vedere [Disponibilità elevata e ripristino di emergenza di SAP HANA (istanze Large) in Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Oltre alle risorse di archiviazione offerte, è possibile capacità di archiviazione aggiuntiva per incrementi di 1 TB. Questo ulteriore spazio di archiviazione può essere aggiunto come nuovi volumi alle istanze HANA di grandi dimensioni.

Durante l'onboarding con SAP HANA in Azure Service Management, il cliente specifica un ID utente (UID) e un ID gruppo (GID) per l'utente sidadm e il gruppo sapsys, ad esempio 1000,500. È necessario che questi stessi valori vengano usati durante l'installazione del sistema SAP HANA. Poiché si vogliono distribuire più istanze di HANA in un'unità, si ottengono più set di volumi, ovvero un set per ogni istanza. In fase di distribuzione è quindi necessario definire quanto segue:

- Il SID delle diverse istanze di HANA, da cui viene derivato il valore sidadm.
- Le dimensioni della memoria delle diverse istanze di HANA. Le dimensioni della memoria per ogni istanza definiscono le dimensioni dei volumi nei singoli set di volumi.

In base alle indicazioni del provider di archiviazione, le opzioni di montaggio seguenti sono configurate per tutti i volumi montati, escluso il volume LUN di avvio:

- nfs    rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

Questi punti di montaggio sono configurati in /etc/fstab, come illustrato nell'immagine seguente:

![fstab di volumi montati in un'unità di istanze Large di HANA](./media/hana-installation/image1_fstab.PNG)

L'output del comando df -h in un'unità di istanze Large di HANA di tipo S72m sarà simile al seguente:

![fstab di volumi montati in un'unità di istanze Large di HANA](./media/hana-installation/image2_df_output.PNG)


Il controller di archiviazione e i nodi nei timestamp dell'istanza di grandi dimensioni sono sincronizzati con i server NTP. Poiché si sincronizza SAP HANA nelle unità Azure (istanze di grandi dimensioni) e le VM di Azure in un server NTP, non si dovrebbero verificare sfasamenti di orario significativi tra l'infrastruttura e le unità di calcolo nei timestamp di Azure o dell'istanza di grandi dimensioni.

Per ottimizzare la SAP HANA per la memoria sottostante, è necessario impostare anche i parametri di configurazione di SAP HANA seguenti:

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
Per le versioni 1.0 di SAP HANA fino a SPS12, questi parametri possono essere impostati durante l'installazione del database SAP HANA, come descritto in [SAP Note #2267798 - Configuration of the SAP HANA Database](https://launchpad.support.sap.com/#/notes/2267798) (Nota SAP #2267798 - Configurazione del database SAP HANA)

È anche possibile configurare i parametri dopo l'installazione del database SAP HANA tramite il framework hdbparam. 

Con SAP HANA 2.0, il framework hdbparam è stato deprecato. Di conseguenza i parametri devono essere impostati tramite i comandi SQL. Per informazioni dettagliate, vedere [SAP Note #2399079: Elimination of hdbparam in HANA 2](https://launchpad.support.sap.com/#/notes/2399079) (Nota SAP #2399079: eliminazione di hdbparam HANA 2).


## <a name="operating-system"></a>Sistema operativo

Lo spazio di swapping dell'immagine del sistema operativo fornita è impostato su 2 GB in base a [SAP Support Note #1999997 - FAQ: SAP HANA Memory](https://launchpad.support.sap.com/#/notes/1999997/E) (Nota di supporto SAP n. 1999997 - Domande frequenti: memoria di SAP HANA). La configurazione di una diversa impostazione deve essere effettuata dal cliente.

[SUSE Linux Enterprise Server 12 SP1 for SAP Applications](https://www.suse.com/products/sles-for-sap/hana) è la distribuzione di Linux installata per SAP HANA in Azure (istanze di grandi dimensioni). Questa particolare distribuzione offre funzionalità specifiche di SAP &quot;predefinite&quot; (inclusi parametri preimpostati per l'esecuzione efficace si SAP in SLES).

Vedere il [white paper sulle librerie di risorse](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) sul sito Web di SUSE e le informazioni relative a [SAP in SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) su SAP Community Network (SCN) per varie risorse utili relative alla distribuzione di SAP HANA in SLES (inclusa la configurazione di elevata disponibilità, la protezione avanzata specifica per le operazioni di SAP e altro ancora).

Collegamenti aggiuntivi utili correlati a SAP in SUSE:

- [Sito relativo a SAP HANA in SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Best Practice for SAP: Enqueue Replication – SAP NetWeaver on SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113) (Procedura consigliata per SAP: replica di accodamento e SAP NetWeaver in SUSE Linux Enterprise 12).
- [ClamSAP: SLES Virus Protection for SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (ClamSAP: protezione da virus SLES per SAP), incluso SLES 12 for SAP Applications

Note di supporto SAP applicabili all'implementazione di SAP HANA su SLES 12:

- [SAP Support Note #1944799 – SAP HANA Guidelines for SLES Operating System Installation](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html) (Nota di supporto SAP n. 1944799: linee guida di SAP HANA per l'installazione del sistema operativo SLES)
- [SAP Support Note #2205917 – SAP HANA DB Recommended OS Settings for SLES 12 for SAP Applications](https://launchpad.support.sap.com/#/notes/2205917/E) (Nota di supporto SAP n. 2205917: impostazioni del sistema operativo consigliate per il database di SAP HANA per SLES 12 for SAP Applications)
- [SAP Support Note #1984787 – SUSE Linux Enterprise Server 12:  Installation Notes](https://launchpad.support.sap.com/#/notes/1984787) (Nota di supporto SAP n. 1984787 - SUSE Linux Enterprise Server 12: note di installazione)
- [SAP Support Note #171356 – SAP Software on Linux:  General Information](https://launchpad.support.sap.com/#/notes/1984787) (Nota di supporto SAP n. 171356: informazioni generali sul software SAP in Linux)
- [SAP Support Note #1391070 – Linux UUID Solutions](https://launchpad.support.sap.com/#/notes/1391070) (Nota di supporto SAP n. 1391070: soluzioni UUID Linux)

[Red Hat Enterprise Linux for SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) è un'altra offerta per l'esecuzione di SAP HANA in istanze di grandi dimensioni di HANA. Sono disponibili le versioni RHEL 6.7 e 7.2. 

Utili collegamenti aggiuntivi correlati a SAP in Red Hat:
- [Sito SAP HANA on Red Hat Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

Note di supporto SAP applicabili all'implementazione di SAP HANA in Red Hat:

- [SAP Support Note #2009879 - SAP HANA Guidelines for Red Hat Enterprise Linux (RHEL) Operating System](https://launchpad.support.sap.com/#/notes/2009879/E) (Nota di supporto SAP n. 2009879: linee guida di SAP HANA per il sistema operativo Red Hat Enterprise Linux, RHEL)
- [SAP Support Note #2292690 - SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/2292690) (Nota di supporto n. 2292690 - SAP HANA DB: impostazioni del sistema operativo consigliate per RHEL 7)
- [SAP Support Note #2247020 - SAP HANA DB: Recommended OS settings for RHEL 6.7](https://launchpad.support.sap.com/#/notes/2247020) (Nota di supporto n. 2247020 - SAP HANA DB: impostazioni del sistema operativo consigliate per RHEL 6.7)
- [SAP Support Note #1391070 – Linux UUID Solutions](https://launchpad.support.sap.com/#/notes/1391070) (Nota di supporto SAP n. 1391070: soluzioni UUID Linux)
- [SAP Support Note #2228351 - Linux: SAP HANA Database SPS 11 revision 110 (or higher) on RHEL 6 or SLES 11](https://launchpad.support.sap.com/#/notes/2228351) (Nota di supporto SAP n. 2228351 - Linux: SAP HANA Database SPS 11 revisione 110 (o successiva) in RHEL 6 o SLES 11)
- [SAP Support Note #2397039 - FAQ: SAP on RHEL](https://launchpad.support.sap.com/#/notes/2397039) (Nota di supporto SAP n. 2397039 - Domande frequenti: SAP in RHEL)
- [SAP Support Note #1496410 - Red Hat Enterprise Linux 6.x: Installation and Upgrade](https://launchpad.support.sap.com/#/notes/1496410) (Nota di supporto SAP n. 1496410 - Red Hat Enterprise Linux 6.x: installazione e aggiornamento)
- [SAP Support Note #2002167 - Red Hat Enterprise Linux 7.x: Installation and Upgrade](https://launchpad.support.sap.com/#/notes/2002167) (Nota di supporto SAP n. 2002167 - Red Hat Enterprise Linux 7.x: installazione e aggiornamento)

## <a name="time-synchronization"></a>Sincronizzazione degli orari

Le applicazioni SAP basate su architettura SAP NetWeaver rilevano gli sfasamenti orari per i diversi componenti che costituiscono il sistema SAP. I dump SAP ABAP brevi con il titolo di errore ZDATE\_LARGE\_TIME\_DIFF sono probabilmente familiari, in quanto questi dump brevi compaiono quando l'ora di sistema di server o VM differenti è troppo sfasata.

Per SAP HANA in Azure (istanze di grandi dimensioni), la sincronizzazione dell'ora eseguita in Azure non si applica alle unità di calcolo nei timestamp dell'istanza di grandi dimensioni. Questa sincronizzazione non è applicabile all'esecuzione delle applicazioni SAP in VM native di Azure, poiché Azure assicura la corretta sincronizzazione dell'ora del sistema. Di conseguenza è necessario configurare un server di riferimento ora separato che possa essere utilizzato dai server applicazioni SAP in esecuzione nelle VM di Azure e dalle istanze di database SAP HANA in esecuzione nelle istanze HANA di grandi dimensioni. L'ora dell'infrastruttura di archiviazione nei timestamp dell'istanza di grandi dimensioni è sincronizzata con i server NTP.

## <a name="setting-up-smt-server-for-suse-linux"></a>Configurazione del server SMT per SUSE Linux
Le istanze Large di SAP HANA non hanno connettività diretta a Internet. La registrazione di un'unità di questo tipo nel provider del sistema operativo e il download e l'applicazione di patch non sono quindi processi semplici. Nel caso di SUSE Linux è possibile configurare un server SMT in una VM di Azure. La VM di Azure deve essere ospitata in una rete virtuale di Azure, connessa all'istanza Large di HANA. Con un server SMT, l'unità di istanze Large di HANA può eseguire la registrazione e scaricare patch. 

SUSE fornisce una guida più dettagliata in [Subscription Management Tool for SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf) (Strumento di gestione delle sottoscrizioni per SLES 12 SP2). 

Come precondizione per l'installazione di un server SMT che esegua questa attività per le istanze Large di HANA sono necessari gli elementi seguenti:

- Una rete virtuale di Azure connessa al circuito di ripristino di emergenza delle istanze Large di HANA.
- Un account SUSE associato a un'organizzazione. L'organizzazione deve avere una sottoscrizione di SUSE valida.

### <a name="installation-of-smt-server-on-azure-vm"></a>Installazione di un server SMT in una VM di Azure

In questo passaggio viene installato il server SMT in una VM di Azure. È prima di tutto necessario accedere a [SUSE Customer Center](https://scc.suse.com/)

Dopo l'accesso, passare a Organization (Organizzazione) --> Organization Credentials (Credenziali organizzazione). In questa sezione sono disponibili le credenziali necessarie per la configurazione del server SMT.

Il terzo passaggio consiste nell'installare una VM SUSE Linux nella rete virtuale di Azure. Per distribuire la macchina virtuale, acquisire un'immagine di tipo SLES 12 SP2 dalla raccolta di Azure. Durante il processo di distribuzione non definire alcun nome DNS e non usare indirizzi IP statici, come illustrato in questo screenshot.

![Distribuzione del server SMT](./media/hana-installation/image3_vm_deployment.png)

La macchina virtuale distribuita è una VM di dimensioni ridotte, con indirizzo IP interno 10.34.1.4 nella rete virtuale di Azure. Il nome della VM è smtserver. Dopo l'installazione viene verificata la connettività alle unità di istanze Large di HANA. In base al modo in cui è stata organizzata la risoluzione dei nomi, potrebbe essere necessario configurare la risoluzione delle unità di istanze Large di HANA etc/hosts della VM di Azure. Aggiungere un altro disco alla VM che verrà usata per il salvataggio delle patch. È possibile che il disco di avvio stesso sia troppo piccolo. Nel caso specifico, il disco è stato montato in /srv/www/htdocs, come mostrato nello screenshot seguente. Dovrebbe essere sufficiente un disco da 100 GB.

![Distribuzione del server SMT](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Accedere alle unità di istanze Large di HANA, rimanere in /etc/hosts e verificare se è possibile raggiungere la VM di Azure che dovrebbe eseguire il server SMT in rete.

Se questa verifica ha esito positivo, sarà necessario accedere alla VM di Azure che dovrebbe eseguire il server SMT. Se si usa putty per accedere alla VM, è necessario eseguire questa sequenza di comandi nella finestra di bash:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Dopo l'esecuzione di questi comandi, riavviare bash per attivare le impostazioni. Avviare quindi YAST.

In YAST passare a Software Maintenance (Manutenzione software) e cercare smt. Selezionare smt, che viene impostato automaticamente su yast2-smt, come illustrato di seguito.

![SMT in yast](./media/hana-installation/image5_smt_in_yast.PNG)


Accettare la selezione per l'installazione su smtserver. Dopo l'installazione, passare alla configurazione del server SMT e immettere le credenziali dell'organizzazione recuperate in precedenza da SUSE Customer Center. Immettere anche il nome host della VM di Azure come URL del server SMT. In questa dimostrazione corrisponde a https://smtserver, come illustrato nell'immagine seguente.

![Configurazione del server SMT](./media/hana-installation/image6_configuration_of_smtserver1.png)

Come passaggio successivo è necessario testare il funzionamento della connessione a SUSE Customer Center. Come illustrato nell'immagine seguente, nel caso della dimostrazione non sono stati rilevati problemi di funzionamento.

![Testare la connessione a SUSE Customer Center](./media/hana-installation/image7_test_connect.png)

Dopo l'avvio del programma di installazione di SMT, è necessario specificare una password del database. Poiché si tratta di una nuova installazione, è necessario definire la password, come illustrato nell'immagine seguente.

![Definire la password per il database](./media/hana-installation/image8_define_db_passwd.PNG)

L'interazione successiva consiste nella creazione di un certificato. Completare la finestra di dialogo, come illustrato di seguito, per completare il passaggio.

![Creare un certificato per il server SMT](./media/hana-installation/image9_certificate_creation.PNG)

È possibile che per il passaggio "Run synchronization check" (Esegui verifica della configurazione) alla fine della configurazione sia necessario attendere qualche minuto. Dopo l'installazione e la configurazione del server SMT, il repository della directory dovrebbe essere disponibile nel punto di montaggio /srv/www/htdocs/ e il repository dovrebbe includere alcune sottodirectory. 

Riavviare il server SMT e i servizi correlati con questi comandi.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

### <a name="download-of-packages-onto-smt-server"></a>Download di pacchetti nel server SMT

Dopo il riavvio di tutti i servizi, selezionare i pacchetti appropriati in SMT Management tramite Yast. La selezione del pacchetto dipende dall'immagine del sistema operativo del server di istanze Large di HANA e non dalla versione di SLES o dalla versione della VM che esegue il server SMT. Un esempio della schermata di selezione è riportato di seguito.

![Selezionare i pacchetti](./media/hana-installation/image10_select_packages.PNG)

Al termine della selezione dei pacchetti, è necessario avviare l'operazione iniziale di copia dei pacchetti selezionati nel server SMT configurato. L'operazione di copia viene attivata nella shell tramite il comando smt-mirror, come illustrato di seguito.


![Scaricare pacchetti nel server SMT](./media/hana-installation/image11_download_packages.PNG)

Come illustrato in precedenza, i pacchetti dovrebbero essere copiati nelle directory create nel punto di montaggio /srv/www/htdocs. Il processo potrebbe richiedere un po' di tempo. In base al numero di pacchetti selezionati, potrebbe essere necessario attendere più di un'ora.
Al termine del processo, è necessario passare all'installazione del client SMT. 

### <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Configurare il client SMT in unità di istanze Large di HANA

I client in questo caso sono le unità di istanze Large di HANA. Il programma di installazione del server SMT ha copiato lo script clientSetup4SMT.sh nella VM di Azure. Copiare tale script nell'unità di istanze Large di HANA da connettere al server SMT. Avviare lo script con l'opzione -h e assegnare allo script come parametro il nome del server SMT. Nell'esempio viene usato smtserver.

![Configurare il client SMT](./media/hana-installation/image12_configure_client.PNG)

È possibile che in uno scenario specifico il caricamento del certificato dal server da parte del client abbia esito positivo, ma la registrazione abbia esito negativo, come mostrato di seguito.

![Registrazione del client non riuscita](./media/hana-installation/image13_registration_failed.PNG)

In caso di esito negativo della registrazione, leggere questo [documento del supporto tecnico SUSE](https://www.suse.com/de-de/support/kb/doc/?id=7006024) e seguire la procedura indicata nel documento.

> [!IMPORTANT] 
> Come nome del server è necessario specificare il nome della VM, in questo caso smtserver, senza nome di dominio completo. È sufficiente usare il nome della VM. 

Al termine di questa procedura, è necessario eseguire il comando seguente nell'unità di istanze Large di HANA.

```
SUSEConnect –cleanup
```

> [!Note] 
> Nei nostri test è stato sempre necessario attendere qualche minuto dopo questo passaggio. L'esecuzione immediata di clientSetup4SMT.sh dopo le misure correttive descritte nell'articolo di SUSE comporta la visualizzazione di messaggi relativi al fatto che il certificato non è ancora valido. Un'attesa di 5-10 minuti prima dell'esecuzione di clientSetup4SMT.sh consente in genere di configurare correttamente il client.

Se si verifica questo problema ed è necessario risolverlo tramite la procedura illustrata nell'articolo relativo a SUSE, sarà necessario riavviare clientSetup4SMT.sh nell'unità di istanze Large di HANA. L'operazione dovrebbe essere completata correttamente, come illustrato di seguito.

![Registrazione del client riuscita](./media/hana-installation/image14_finish_client_config.PNG)

Questo passaggio consente di configurare il client SMT dell'unità di istanze Large di HANA per la connessione al server SMT installato nella VM di Azure. È ora possibile usare i comandi "zypper up" o "zypper in" per installare le patch del sistema operativo nelle istanze Large di HANA oppure installare pacchetti aggiuntivi. Si noti che è possibile ottenere solo le patch scaricate in precedenza nel server SMT.


## <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>Esempio di installazione di SAP HANA in istanze Large di HANA
Questa sezione illustra come installare SAP HANA in un'unità di istanze Large di HANA. Lo stato iniziale è simile al seguente:

- Sono stati forniti a Microsoft tutti i dati necessari per la distribuzione di un'istanza Large di SAP HANA.
- È stata ricevuta un'istanza Large di SAP HANA da Microsoft.
- È stata creata una rete virtuale di Azure connessa alla rete locale.
- Il circuito ExpressRoute per istanze Large di HANA è stato connesso alla stessa rete virtuale di Azure.
- È stata installata una VM di Azure da usare come jumpbox per istanze Large di HANA.
- È stata verificata la connessione dal jumpbox all'unità di istanze Large di HANA e viceversa.
- È stata verificata l'installazione di tutte le patch e di tutti i pacchetti necessari.
- Sono state controllate le note e la documentazione SAP relative all'installazione di HANA nel sistema operativo in uso ed è stato verificato il supporto nella versione del sistema operativo per la versione di HANA specifica.

Le sequenze successive illustrano il download dei pacchetti di installazione di HANA nella VM jumpbox, che in questo caso è in esecuzione in un sistema operativo VM, quindi la copia dei pacchetti nell'unità di istanze Large di HANA e infine la sequenza del programma di installazione.

### <a name="download-of-the-sap-hana-installation-bits"></a>Download dei bit di installazione di SAP HANA
Poiché le unità di istanze Large di HANA non hanno alcuna connettività diretta a Internet, non è possibile scaricare direttamente i pacchetti di installazione da SAP alla VM di istanze Large di HANA. Per risolvere il problema relativo all'assenza di connettività diretta a Internet, è necessario il jumpbox. I pacchetti vengono scaricati nella VM jumpbox.

Per scaricare i pacchetti di installazione di HANA, è necessario un utente S o un altro utente SAP, che consente di accedere a SAP Marketplace. Completare questa sequenza di schermate dopo l'accesso:

Passare a [SAP Service Marketplace](https://support.sap.com/en/index.html), quindi fare clic su Download Software (Download di software) > Installations and Upgrade (Installazioni e aggiornamento) > By Alphabetical Index (In base all'indice alfabetico) > Under H – SAP HANA Platform Edition (In H - SAP HANA Platform Edition) > SAP HANA Platform Edition 2.0 > Installation (Installazione) > Download the following files (Scaricare i file seguenti).

![Scaricare i file di installazione di HANA](./media/hana-installation/image16_download_hana.PNG)

Nel caso della dimostrazione sono stati scaricati i pacchetti di installazione per SAP HANA 2.0. Nella VM jumpbox di Azure espandere i file compressi autoestraenti nella directory, come illustrato di seguito.

![Estrarre i file di installazione di HANA](./media/hana-installation/image17_extract_hana.PNG)

Dopo l'estrazione dei file compressi, copiare la directory creata dall'estrazione, nel caso precedente 51052030, nell'unità di istanze Large di HANA nel volume /hana/shared in una directory creata.

> [!Important]
> Non copiare i pacchetti di installazione nel volume LUN radice o di avvio, poiché lo spazio è limitato e deve essere usato anche da altri processi.


### <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Installare SAP HANA nell'unità di istanze Large di HANA
Per installare SAP HANA, è necessario accedere come utente radice. Solo l'utente radice ha autorizzazioni sufficienti per l'installazione di SAP HANA.
È prima di tutto necessario configurare le autorizzazioni per la directory copiata in /hana/shared. Le autorizzazioni da configurare sono analoghe a queste:

```
chmod –R 744 <Installation bits folder>
```

Se si vuole installare SAP HANA tramite l'interfaccia grafica, è necessario installare il pacchetto gtk2 istanze Large di HANA. Verificare se l'installazione è stata effettuata con il comando seguente:

```
rpm –qa | grep gtk2
```

Nei passaggi successivi viene illustrato il programma di installazione di SAP HANA con l'interfaccia utente grafica. Passare prima di tutto alla directory di installazione e quindi alla sottodirectory HDB_LCM_LINUX_X86_64. Inizia

```
./hdblcmgui 
```
da tale directory. Viene ora visualizzata una procedura guidata con una sequenza di schermate in cui è necessario fornire i dati per l'installazione. Nel caso illustrato viene eseguita l'installazione del server di database SAP HANA e dei componenti client di SAP HANA. Viene quindi selezionato il valore "SAP HANA Database" (Database SAP HANA), come mostrato di seguito.

![Selezionare HANA nell'installazione](./media/hana-installation/image18_hana_selection.PNG)

Nella schermata successiva viene selezionata l'opzione "Install New System" (Installa nuovo sistema).

![Selezionare una nuova installazione di HANA](./media/hana-installation/image19_select_new.PNG)

Dopo questo passaggio è necessario selezionare alcuni componenti aggiuntivi che possono essere installati oltre al server di database SAP HANA.

![Selezionare altri componenti di HANA](./media/hana-installation/image20_select_components.PNG)

Per le finalità di questa documentazione sono stati selezionati SAP HANA Client e SAP HANA Studio. È stata installata anche un'istanza con scalabilità verticale. Nella schermata successiva è quindi necessario scegliere "Single-Host System" (Sistema a singolo host). 

![Selezionare l'installazione con scalabilità verticale](./media/hana-installation/image21_single_host.PNG)

Nella schermata successiva è necessario specificare alcuni dati.

![Specificare il SID di SAP HANA](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Come ID di sistema (SID) di HANA è necessario specificare lo stesso SID fornito a Microsoft quando è stata ordinata la distribuzione di tipo istanze Large di HANA. La selezione di un SID diverso provoca un errore dell'installazione a causa di problemi di accesso ai volumi diversi.

Come directory di installazione è necessario usare la directory /hana/shared. Nel passaggio successivo è necessario specificare le posizioni dei file di dati HANA e dei file di log HANA.


![Specificare la posizione dei log HANA](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Definire come file di dati e di log i volumi già dotati dei punti di montaggio che contengono il SID scelto nella schermata precedente. Se il SID non corrisponde a quello digitato nella schermata precedente, tornare indietro e modificare il SID specificando il valore disponibile nei punti di montaggio.

Nel passaggio successivo occorre verificare il nome host e, se necessario, correggerlo. 

![Verificare il nome host](./media/hana-installation/image24_review_host_name.PNG)

Nel passaggio successivo è necessario anche recuperare i dati forniti a Microsoft quando è stata ordinata la distribuzione di tipo istanze Large di HANA. 

![Specificare un UID e un GID per l'utente di sistema](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> È necessario specificare lo stesso ID di utente di sistema e lo stesso ID di gruppo utenti forniti a Microsoft quando è stata ordinata la distribuzione di unità. Se non si specificano esattamente gli stessi ID, non sarà possibile installare SAP HANA nell'unità di istanze Large di HANA.

Nelle due schermate successive, non riprodotte in questa documentazione, è necessario specificare la password per l'utente SYSTEM del database SAP HANA e la password per l'utente sapadm, che viene usata per l'agente host SAP, installato come parte dell'istanza di database SAP HANA.

Dopo la definizione della password, viene visualizzata una schermata di conferma. Verificare tutti i dati elencati e continuare con l'installazione. Viene visualizzata una schermata che indica lo stato dell'installazione, analoga a quella seguente.

![Verificare lo stato dell'installazione](./media/hana-installation/image27_show_progress.PNG)

Al termine dell'installazione dovrebbe essere visualizzata un'immagine simile alla seguente:

![L'installazione è stata completata](./media/hana-installation/image28_install_finished.PNG)

A questo punto l'istanza di SAP HANA dovrebbe essere attiva e pronta all'uso. Dovrebbe essere possibile connettersi all'istanza da SAP HANA Studio. Assicurarsi di verificare anche se sono disponibili patch recenti per SAP HANA e applicarle.
























































 







 





