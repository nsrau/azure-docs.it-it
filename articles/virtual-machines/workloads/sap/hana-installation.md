---
title: Installare SAP HANA su SAP HANA in Azure (istanze di grandi dimensioni) | Documentazione Microsoft
description: Come installare SAP HANA in SAP HANA in Azure (istanza di grandi dimensioni).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dad088138fea2dd4fadc0cc9eed71245c32a8e0b
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162664"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Come installare e configurare SAP HANA (istanze di grandi dimensioni) in Azure

Prima di leggere questo articolo, acquisire familiarità con [termini comuni di istanze Large di HANA](hana-know-terms.md) e lo [SKU di istanze Large di HANA](hana-available-skus.md).

L'installazione di SAP HANA è responsabilità dell'utente. È possibile avviare l'attività immediatamente dopo la consegna di un nuovo SAP HANA su server di Azure (istanze Large) e dopo avere stabilito la connettività tra reti virtuali di Azure e le unità di istanze Large di HANA. 

> [!Note]
> In base ai criteri SAP, l'installazione di SAP HANA deve essere eseguita da una persona certificata per l'esecuzione di installazioni di SAP HANA, ovvero una persona che ha superato l'esame Certified SAP Technology Associate o l'esame di certificazione per l'installazione di SAP HANA oppure da un integratore di sistemi con certificazione SAP.

Vedere di nuovo, soprattutto quando si prevede di installare HANA 2.0, [SAP Support Note #2235581 - SAP HANA: Supported Operating Systems](https://launchpad.support.sap.com/#/notes/2235581/E) (Nota di supporto SAP n. 2235581 - SAP HANA: sistemi operativi supportati) per verificare che il sistema operativo sia supportato con la versione di SAP HANA che si è deciso di installare. Come si può notare, il sistema operativo supportato per HANA 2.0 ha più restrizioni del sistema operativo supportato per HANA 1.0. 

> [!IMPORTANT] 
> Per le unità di tipo II, solo la versione del sistema operativo SLES 12 SP2 è supportata in questa fase. 

Prima di iniziare l'installazione di HANA, è necessario verificare quanto segue:
- [Convalidare le unità HLI](#validate-the-hana-large-instance-units)
- [Configurazione del sistema operativo](#operating-system)
- [Network configuration](#networking)
- [Configurazione dell'archiviazione](#storage)


## <a name="validate-the-hana-large-instance-units"></a>Convalidare l'unità di istanze Large di HANA

Dopo aver ricevuto le unità di istanze Large di HANA da Microsoft, convalidare le impostazioni seguenti e apportare le necessarie modifiche.

Il **primo passaggio** dopo aver ricevuto l'istanza Large di HANA e avere stabilito l'accesso e la connettività alle istanze consiste nel registrare il sistema operativo dell'istanza con il provider del sistema operativo. Questo passaggio include la registrazione del sistema operativo Linux SUSE in un'istanza di SMT SUSE che è stata distribuita in una VM in Azure. L'unità di istanze Large di HANA può connettersi a questa istanza SMT (consultare [Come configurare il server SMT per SUSE Linux](hana-setup-smt.md)). Oppure è necessario registrare il sistema operativo Red Hat con la gestione sottoscrizione di RedHat a cui è necessario connettersi. Vedere anche le osservazioni in questo [documento](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Questo passaggio è necessario anche per consentire di applicare patch al sistema operativo. Un'attività che rientra tra le responsabilità del cliente. Per quanto riguarda SUSE, la documentazione relativa all'installazione e alla configurazione di SMT è disponibile [qui](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

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

## <a name="operating-system"></a>Sistema operativo

> [!IMPORTANT] 
> Per le unità di tipo II, solo la versione del sistema operativo SLES 12 SP2 è supportata in questa fase. 

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

[Red Hat Enterprise Linux for SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) è un'altra offerta per l'esecuzione di SAP HANA in istanze di grandi dimensioni di HANA. Sono disponibili le versioni RHEL 6.7 e 7.2. Diversamente dalle macchine virtuali Azure native, che supportano solo RHEL 7.2 e le versioni più recenti, le istanze Large HANA supportano anche RHEL 6.7. Tuttavia, è consigliabile usare una versione RHEL 7.x.

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

### <a name="time-synchronization"></a>Sincronizzazione degli orari

Le applicazioni SAP basate su architettura SAP NetWeaver rilevano gli sfasamenti orari per i diversi componenti che costituiscono il sistema SAP. I dump SAP ABAP brevi con il titolo di errore ZDATE\_LARGE\_TIME\_DIFF sono probabilmente familiari, in quanto questi dump brevi compaiono quando l'ora di sistema di server o VM differenti è troppo sfasata.

Per SAP HANA in Azure (istanze di grandi dimensioni), la sincronizzazione dell'ora eseguita in Azure non si applica alle unità di calcolo nei timestamp dell'istanza di grandi dimensioni. Questa sincronizzazione non è applicabile all'esecuzione delle applicazioni SAP in VM native di Azure, poiché Azure assicura la corretta sincronizzazione dell'ora del sistema. Di conseguenza è necessario configurare un server di riferimento ora separato che possa essere utilizzato dai server applicazioni SAP in esecuzione nelle VM di Azure e dalle istanze di database SAP HANA in esecuzione nelle istanze HANA di grandi dimensioni. L'ora dell'infrastruttura di archiviazione nei timestamp dell'istanza di grandi dimensioni è sincronizzata con i server NTP.


## <a name="networking"></a>Rete
Si presuppone che siano state seguite le indicazioni relative alla progettazione delle reti virtuali di Azure e alla connessione di queste reti virtuali alle istanze Large di HANA come descritto in questi documenti:

- [Panoramica e architettura di SAP HANA (istanze di grandi dimensioni) in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Infrastruttura e connettività a SAP HANA (istanze di grandi dimensioni) in Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Esistono alcuni dettagli che vale la pena citare sulle reti delle singole unità. Ogni unità di istanza di grandi dimensioni HANA include due o tre indirizzi IP assegnati a due o tre porte NIC dell'unità. Tre indirizzi IP vengono usati nelle configurazioni con scalabilità orizzontale HANA e nello scenario di replica DFS HANA. Uno degli indirizzi IP assegnati alla scheda di interfaccia di rete dell'unità è esterno al pool di indirizzi IP del server che è stato descritto in [Panoramica e architettura di SAP HANA (istanze di grandi dimensioni) in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

Per informazioni sui dettagli relativi a Ethernet per l'architettura, vedere [Scenari HLI supportati](hana-supported-scenario.md).

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

Come si può notare, la condivisione HANA e usr/sap condividono lo stesso volume. La nomenclatura dei punti di montaggio include l'ID di sistema delle istanze HANA e i numeri di montaggio. Nelle distribuzioni con aumento delle prestazioni è disponibile un solo montaggio, ad esempio mnt00001. Nelle distribuzioni con scalabilità orizzontale, invece, il numero di montaggi corrisponde al numero di nodi di lavoro e nodi master. Per l'ambiente con scalabilità orizzontale i volumi relativi a dati, log e backup dei log sono condivisi e sono collegati a ogni nodo nella configurazione con scalabilità orizzontale. Per le configurazioni che eseguono più istanze di SAP viene creato un set diverso di volumi, che vengono collegati a un'unità di istanze Large di HANA. Per informazioni sui dettagli relativi al layout di archiviazione per lo scenario, vedere [Scenari HLI supportati](hana-supported-scenario.md).

Man mano che si prosegue nella lettura del documento e si cerca un'unità di istanze Large di HANA, ci si rende conto che le unità hanno un volume del disco piuttosto grande per HANA/data e un volume per HANA/log/backup. Le dimensioni di HANA/data sono così grandi perché gli snapshot di archiviazione offerti ai clienti usano lo stesso volume del disco. Maggiore è il numero di snapshot di archiviazione creati, maggiore sarà la quantità di spazio utilizzata dagli snapshot nei volumi di archiviazione assegnati. Il volume HANA/log/backup non è il volume in cui salvare i backup dei database. Le dimensioni sono appropriate per l'uso come volume di backup per i backup dei log delle transazioni HANA. Per informazioni dettagliate, vedere [Disponibilità elevata e ripristino di emergenza di SAP HANA (istanze Large) in Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

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

Per informazioni sui dettagli relativi al layout dell'archiviazione per l'architettura, vedere [Scenari HLI supportati](hana-supported-scenario.md).


**Passaggi successivi**

- Fare riferimento all'[installazione di HANA in HLI](hana-example-installation.md)










































 







 




