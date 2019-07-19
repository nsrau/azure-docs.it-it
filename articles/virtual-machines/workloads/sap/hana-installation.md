---
title: Installare SAP HANA su SAP HANA in Azure (istanze di grandi dimensioni) | Documentazione Microsoft
description: Come installare SAP HANA in un SAP HANA in Azure (istanze large).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5bfd278cc4252167aace3aca52fec65fb3c6367f
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869126"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Come installare e configurare SAP HANA (istanze Large) in Azure

Prima di leggere questo articolo, acquisire familiarità con [termini comuni di istanze Large di HANA](hana-know-terms.md) e lo [SKU di istanze Large di HANA](hana-available-skus.md).

L'installazione di SAP HANA è responsabilità dell'utente. È possibile avviare l'installazione di un nuovo SAP HANA in Azure dopo aver stabilito la connettività tra reti virtuali di Azure e le unità di istanze Large di HANA. 

> [!Note]
> Per criterio SAP, l'installazione di SAP HANA deve essere eseguita da una persona che ha superato l'esame di associazione della tecnologia SAP certificata, l'esame di certificazione per l'installazione di SAP HANA o un integratore di sistemi con certificazione SAP.

Quando si prevede di installare HANA 2.0, vedere [SAP support note #2235581 - SAP HANA: Supported operating systems](https://launchpad.support.sap.com/#/notes/2235581/E) (Nota di supporto SAP n. 2235581 - SAP HANA: sistemi operativi supportati) per verificare che il sistema operativo sia supportato con la versione di SAP HANA da installare. Il sistema operativo supportato per HANA 2.0 ha più restrizioni di quello supportato per HANA 1.0. 

> [!IMPORTANT] 
> Per le unità di tipo II, solo la versione del sistema operativo SLES 12 SP2 è supportata attualmente. 

Prima di iniziare l'installazione di HANA, verificare quanto segue:
- [Unità HLI](#validate-the-hana-large-instance-units)
- [Configurazione del sistema operativo](#operating-system)
- [Network configuration](#networking)
- [Configurazione dell'archiviazione](#storage)


## <a name="validate-the-hana-large-instance-units"></a>Convalidare l'unità di istanze Large di HANA

Dopo aver ricevuto le unità di istanze Large di HANA da Microsoft, convalidare le impostazioni seguenti e apportare le necessarie modifiche.

Il **primo passaggio** dopo aver ricevuto l'istanza large di Hana e stabilito l'accesso e la connettività alle istanze consiste nell'archiviare portale di Azure se le istanze vengono visualizzate con gli SKU e il sistema operativo corretti nel portale di Azure. Leggere il [controllo delle istanze large di Azure Hana tramite portale di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal) per i passaggi necessari per eseguire i controlli.

Il **secondo passaggio** dopo aver ricevuto l'istanza large di Hana e aver stabilito l'accesso e la connettività alle istanze consiste nel registrare il sistema operativo dell'istanza con il provider del sistema operativo. Questo passaggio include la registrazione del sistema operativo Linux SUSE in un'istanza di SMT SUSE che è stata distribuita in una macchina virtuale di Azure. 

L'unità di istanze Large di HANA può connettersi a questa istanza SMT. Per altre informazioni, vedere [Configurazione del server SMT per SUSE Linux](hana-setup-smt.md). In alternativa, è necessario registrare il sistema operativo Red Hat con la gestione sottoscrizione di RedHat a cui eseguire la connessione. Per altre informazioni, vedere la sezione Osservazioni in [What is SAP HANA on Azure (Large Instances)?](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Informazioni su SAP HANA in Azure - istanze Large). 

Questo passaggio è necessario per l'applicazione di patch al sistema operativo, che è responsabilità del cliente. Per quanto riguarda SUSE, la documentazione relativa all'installazione e alla configurazione di SMT è disponibile in questa pagina sull'[installazione di SMT](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

Il **terzo passaggio** consiste nel verificare la presenza di nuove patch e correzioni della versione del sistema operativo specifica. Verificare che il livello di patch dell'istanza Large di HANA sia nello stato più recente. In alcuni casi le patch più recenti non sono incluse. Dopo aver acquisito un'unità di istanze Large di HANA, è obbligatorio verificare se sono presenti patch da applicare.

Il **quarto passaggio** consiste nell'estrarre le note SAP pertinenti per l'installazione e la configurazione di SAP Hana nella versione/versione specifica del sistema operativo. A causa della modifica dei suggerimenti o delle modifiche alle note su SAP o alle configurazioni che dipendono da singoli scenari di installazione, Microsoft non è sempre in grado di configurare correttamente l'unità di istanza Large dimensioni HANA. 

Il cliente è quindi tenuto a leggere le note SAP correlate a SAP HANA nella versione Linux corretta. Verificare anche le configurazioni della versione del sistema operativo necessarie e applicare le impostazioni di configurazione, se non è già stato fatto.

In particolare, controllare i parametri seguenti e modificarli se necessario:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

A partire da SLES12 SP1 e RHEL 7.2, è necessario impostare questi parametri in un file di configurazione nella directory /etc/sysctl.d. Ad esempio, è necessario creare un file di configurazione con il nome 91-NetApp-HANA.conf. Per le versioni precedenti di SLES e RHEL, questi parametri devono essere impostati in in/etc/sysctl.conf.

Per tutte le versioni RHEL che iniziano con RHEL 6,3, tenere presente quanto segue: 
- Il parametro sunrpc.tcp_slot_table_entries = 128 deve essere impostato in in/etc/modprobe.d/sunrpc-local.conf. Se il file non esiste, è necessario crearlo prima aggiungendo la voce: 
    - options sunrpc tcp_max_slot_table_entries=128

Il **quinto passaggio** consiste nel controllare l'ora di sistema dell'unità di istanze large di Hana. Le istanze vengono distribuite in base con un fuso orario del sistema che rappresenta la posizione dell'area di Azure in cui si trova il timestamp dell'istanza Large di HANA. È possibile modificare l'ora o il fuso orario del sistema delle istanze di cui si è proprietari. 

Se si ordinano più istanze nel tenant, è necessario adattare il fuso orario delle istanze consegnate. Microsoft non ha visibilità del fuso orario del sistema impostato con le istanze dopo la consegna. Le nuove istanze distribuite pertanto non possono essere impostate nello stesso fuso orario di quello modificato. È responsabilità del cliente controllare e, se necessario, adattare il fuso orario delle istanze consegnate. 

Il **sesto passaggio** consiste nel controllare etc/hosts. Al momento della consegna, ai pannelli sono assegnati indirizzi IP diversi per scopi differenti. Controllare il file etc/hosts. Quando le unità vengono aggiunte a un tenant esistente, non è prevista che etc/hosts dei nuovi sistemi distribuiti venga mantenuto correttamente con gli indirizzi IP dei sistemi distribuiti in precedenza. È compito del cliente quindi verificare che una nuova istanza distribuita possa interagire e risolvere i nomi delle unità distribuite in precedenza nel tenant. 


## <a name="operating-system"></a>Sistema operativo

> [!IMPORTANT] 
> Per le unità di tipo II, solo la versione del sistema operativo SLES 12 SP2 è supportata attualmente. 

Lo spazio di swapping dell'immagine del sistema operativo fornita è impostato su 2 GB in base a [SAP Support Note #1999997 - FAQ: SAP HANA Memory](https://launchpad.support.sap.com/#/notes/1999997/E) (Nota di supporto SAP n. 1999997 - Domande frequenti: memoria di SAP HANA). La configurazione di una diversa impostazione deve essere eseguita dal cliente.

[SUSE Linux Enterprise Server 12 SP1 for SAP Applications](https://www.suse.com/products/sles-for-sap/download/) è la distribuzione di Linux installata per SAP HANA in Azure (istanze Large). Questa particolare distribuzione offre funzionalità specifiche di SAP predefinite, inclusi parametri preimpostati per eseguire SAP in SLES in modo efficace.

Vedere i [white paper/le librerie di risorse](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) sul sito Web di SUSE e le informazioni relative a [SAP in SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) su SAP Community Network (SCN) per varie risorse utili relative alla distribuzione di SAP HANA in SLES (inclusa la configurazione di elevata disponibilità, la protezione avanzata specifica per le operazioni di SAP e altro ancora).

Collegamenti aggiuntivi utili correlati a SAP in SUSE:

- [Sito relativo a SAP HANA in SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Procedure consigliate per SAP: Replica di accodamento e SAP NetWeaver in SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)
- [ClamSAP: SLES Virus Protection for SAP](https://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (ClamSAP: protezione da virus SLES per SAP), incluso SLES 12 for SAP Applications

Note di supporto SAP applicabili all'implementazione di SAP HANA su SLES 12:

- [SAP support note #1944799 – SAP HANA guidelines for SLES operating system installation](https://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html) (Nota di supporto SAP n. 1944799: linee guida di SAP HANA per l'installazione del sistema operativo SLES)
- [SAP Support Note #2205917 – SAP HANA DB Recommended OS Settings for SLES 12 for SAP Applications](https://launchpad.support.sap.com/#/notes/2205917/E) (Nota di supporto SAP n. 2205917: impostazioni del sistema operativo consigliate per il database di SAP HANA per SLES 12 for SAP Applications)
- [SAP support note #1984787 – SUSE Linux Enterprise Server 12:  installation notes](https://launchpad.support.sap.com/#/notes/1984787) (Nota di supporto SAP n. 1984787: note di installazione per SUSE Linux Enterprise Server 12)
- [SAP Support Note #171356 – SAP Software on Linux:  General Information](https://launchpad.support.sap.com/#/notes/1984787) (Nota di supporto SAP n. 171356: informazioni generali sul software SAP in Linux)
- [SAP Support Note #1391070 – Linux UUID Solutions](https://launchpad.support.sap.com/#/notes/1391070) (Nota di supporto SAP n. 1391070: soluzioni UUID Linux)

[Red Hat Enterprise Linux for SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) è un'altra offerta per l'esecuzione di SAP HANA in istanze di grandi dimensioni di HANA. Sono disponibili le versioni RHEL 6.7 e 7.2. Si noti che, contrariamente alle VM native di Azure in cui sono supportati solo RHEL 7,2 e versioni più recenti, le istanze large di HANA supportano anche RHEL 6,7. È consigliabile tuttavia usare una versione RHEL 7.x.

Collegamenti aggiuntivi utili correlati a SAP in Red Hat:
- [Sito SAP HANA su Red Hat Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

Note di supporto SAP applicabili all'implementazione di SAP HANA su Red Hat:

- [SAP Support Note #2009879 - SAP HANA Guidelines for Red Hat Enterprise Linux (RHEL) Operating System](https://launchpad.support.sap.com/#/notes/2009879/E) (Nota di supporto SAP n. 2009879: linee guida di SAP HANA per il sistema operativo Red Hat Enterprise Linux, RHEL)
- [SAP support note #2292690 - SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/2292690) (2292690 - SAP HANA DB: impostazioni del sistema operativo consigliate per RHEL 7)
- [SAP Support Note #2247020 - SAP HANA DB: Recommended OS settings for RHEL 6.7](https://launchpad.support.sap.com/#/notes/2247020) (Nota di supporto SAP n. 2292690 - SAP HANA DB: impostazioni del sistema operativo consigliate per RHEL 7)
- [SAP Support Note #1391070 – Linux UUID Solutions](https://launchpad.support.sap.com/#/notes/1391070) (Nota di supporto SAP n. 1391070: soluzioni UUID Linux)
- [SAP support note #2228351 - Linux: SAP HANA Database SPS 11 revision 110 (or higher) on RHEL 6 or SLES 11](https://launchpad.support.sap.com/#/notes/2228351) (Nota di supporto SAP n. 2228351 - Linux: SAP HANA Database SPS 11 revisione 110, o successiva, in RHEL 6 o SLES 11)
- [SAP support note #2397039 - FAQ: SAP in RHEL](https://launchpad.support.sap.com/#/notes/2397039) (Nota di supporto SAP n. 2397039 - FAQ: SAP in RHEL)
- [SAP Support Note #1496410 - Red Hat Enterprise Linux 6.x: Installation and Upgrade](https://launchpad.support.sap.com/#/notes/1496410) (Nota di supporto SAP n. 1496410 - Red Hat Enterprise Linux 6.x: installazione e aggiornamento)
- [SAP Support Note #2002167 - Red Hat Enterprise Linux 7.x: Installazione e aggiornamento](https://launchpad.support.sap.com/#/notes/2002167) (Nota di supporto SAP n. 2002167 - Red Hat Enterprise Linux 7.x: installazione e aggiornamento)

### <a name="time-synchronization"></a>Sincronizzazione degli orari

Le applicazioni SAP basate su architettura SAP NetWeaver rilevano gli sfasamenti orari per i diversi componenti che costituiscono il sistema SAP. I dump SAP ABAP brevi con il titolo di errore ZDATE\_LARGE\_TIME\_DIFF sono probabilmente familiari perché vengono visualizzati quando l'ora di sistema di server o VM differenti è troppo sfasata.

Per SAP HANA in Azure (istanze Large), la sincronizzazione dell'ora eseguita in Azure non si applica alle unità di calcolo nei timestamp dell'istanza Large. Tale sincronizzazione non è applicabile all'esecuzione delle applicazioni SAP in macchine virtuali native di Azure, poiché Azure assicura la corretta sincronizzazione dell'ora del sistema. 

È necessario pertanto configurare un server di riferimento ora separato che possa essere usato dai server applicazioni SAP in esecuzione nelle macchine virtuali di Azure e dalle istanze di database SAP HANA in esecuzione nelle istanze Large di HANA. L'ora dell'infrastruttura di archiviazione nei timestamp dell'istanza Large è sincronizzata con i server NTP.


## <a name="networking"></a>Rete
Si presuppone che siano state seguite le indicazioni relative alla progettazione delle reti virtuali di Azure e alla connessione di queste reti virtuali alle istanze Large di HANA come descritto nei documenti seguenti:

- [SAP HANA in Azure (istanze Large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Infrastruttura e connettività a SAP HANA (istanze Large) in Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

È opportuno citare alcuni dettagli in relazione alla rete delle singole unità. Ogni unità di istanza Large di HANA include due o tre indirizzi IP assegnati a due o tre porte NIC. Tre indirizzi IP vengono usati nelle configurazioni con scalabilità orizzontale HANA e nello scenario di replica di sistema HANA. Uno degli indirizzi IP assegnati alla scheda di interfaccia di rete dell'unità è esterno al pool di indirizzi IP del server che è stato descritto in [SAP HANA in Azure (istanze Large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

Per altre informazioni sui dettagli Ethernet per l'architettura, vedere [Scenari supportati nelle istanze Large di Hana](hana-supported-scenario.md).

## <a name="storage"></a>Archiviazione

Il layout di archiviazione per SAP Hana in Azure (istanze large) viene configurato da SAP Hana in `service management` Azure tramite le linee guida consigliate per SAP. come illustrato nel white paper [SAP HANA storage requirements](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) (Requisiti per le risorse di archiviazione di SAP HANA). 

Le dimensioni approssimative dei diversi volumi delle diverse SKU di istanze Large di HANA sono illustrate in [SAP HANA in Azure (istanze Large)](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Le convenzioni di denominazione dei volumi di archiviazione sono elencate nella tabella seguente:

| Utilizzo delle risorse di archiviazione | Nome del montaggio | Nome del volume | 
| --- | --- | ---|
| Dati HANA | /Hana/data/SID/mnt0000\<m > | Storage IP:/hana_data_SID_mnt00001_tenant_vol |
| Log HANA | /Hana/log/SID/mnt0000\<m > | Storage IP:/hana_log_SID_mnt00001_tenant_vol |
| Backup dei log HANA | /hana/log/backups | Storage IP:/hana_log_backups_SID_mnt00001_tenant_vol |
| Condivisione HANA | /hana/shared/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/shared |
| usr/sap | /usr/sap/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*SID* è l'ID di sistema dell'istanza HANA. 

*Tenant* corrisponde a un'enumerazione interna delle operazioni durante la distribuzione di un tenant.

HANA e usr/sap condividono lo stesso volume. La nomenclatura dei punti di montaggio include l'ID di sistema delle istanze HANA e i numeri di montaggio. In distribuzioni con scalabilità verticale è presente un solo montaggio, ad esempio mnt00001. Nelle distribuzioni con scalabilità orizzontale, invece, il numero di montaggi corrisponde al numero di nodi di lavoro e nodi master. 

Per ambienti con scalabilità orizzontale i volumi relativi a dati, log e backup dei log sono condivisi e sono collegati a ogni nodo nella configurazione con scalabilità orizzontale. Per le configurazioni con più istanze di SAP viene creato un set diverso di volumi, che vengono collegati a un'unità di istanze Large di HANA. Per dettagli relativi al layout di archiviazione per lo scenario, vedere [Scenari supportati nelle istanze Large di Hana](hana-supported-scenario.md).

Quando si esamina un'unità di istanze Large di HANA, si nota che le unità hanno un volume del disco piuttosto grande per HANA/data e che è presente un volume per HANA/log/backup. Ciò è dovuto al fatto che gli snapshot di archiviazione offerti ai clienti usano lo stesso volume del disco. Maggiore è il numero di snapshot di archiviazione creati, maggiore è la quantità di spazio usata dagli snapshot nei volumi di archiviazione assegnati. 

Il volume HANA/log/backup non è il volume in cui salvare i backup dei database. Le dimensioni sono appropriate per l'uso come volume di backup per i backup dei log delle transazioni HANA. Per altre informazioni, vedere [Disponibilità elevata e ripristino di emergenza di SAP HANA (istanze Large) in Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Oltre alle risorse di archiviazione offerte, è possibile acquistare capacità di archiviazione aggiuntiva per incrementi di 1 TB. Questo ulteriore spazio di archiviazione può essere aggiunto come nuovi volumi alle istanze Large di HANA.

Durante l'onboarding con SAP Hana in `service management`Azure, il cliente specifica un ID utente (UID) e un ID gruppo (GID) per l'utente sidadm e il gruppo sapsys (ad esempio: ad esempio 1000,500. È necessario usare questi stessi valori durante l'installazione del sistema SAP HANA. Poiché si vogliono distribuire più istanze di HANA in un'unità, si ottengono più set di volumi, ovvero un set per ogni istanza. In fase di distribuzione è quindi necessario definire quanto segue:

- SID delle diverse istanze di HANA, da cui viene derivato il valore sidadm.
- Dimensioni della memoria delle diverse istanze di HANA. Le dimensioni della memoria per ogni istanza definiscono le dimensioni dei volumi nei singoli set di volumi.

In base alle indicazioni del provider di archiviazione, le opzioni di montaggio seguenti sono configurate per tutti i volumi montati, escluso il volume LUN di avvio:

- nfs    rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

Questi punti di montaggio sono configurati in /etc/fstab, come illustrato nell'immagine seguente:

![fstab di volumi montati in un'unità di istanze Large di HANA](./media/hana-installation/image1_fstab.PNG)

L'output del comando df -h in un'unità di istanze Large di HANA di tipo S72m è analogo al seguente:

![fstab di volumi montati in un'unità di istanze Large di HANA](./media/hana-installation/image2_df_output.PNG)


Il controller di archiviazione e i nodi nei timestamp dell'istanza di grandi dimensioni sono sincronizzati con i server NTP. Poiché si sincronizza SAP HANA nelle unità Azure (istanze Large) e le macchine virtuali di Azure in un server NTP, non dovrebbero verificarsi sfasamenti di orario significativi tra l'infrastruttura e le unità di calcolo nei timestamp di Azure o dell'istanza Large.

Per ottimizzare SAP HANA per la memoria sottostante, è necessario impostare anche i parametri di configurazione di SAP HANA seguenti:

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
Per le versioni 1.0 di SAP HANA fino a SPS12, questi parametri possono essere impostati durante l'installazione del database SAP HANA, come descritto in [SAP Note #2267798 - Configuration of the SAP HANA Database](https://launchpad.support.sap.com/#/notes/2267798) (Nota SAP n. 2267798 - Configurazione del database SAP HANA)

È anche possibile configurare i parametri dopo l'installazione del database SAP HANA tramite il framework hdbparam. 

Lo spazio di archiviazione usato nelle istanze large di HANA presenta una limitazione delle dimensioni del file. Il [limite di dimensione è di 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) per ogni file. Diversamente dalle limitazioni delle dimensioni dei file nei file System EXT3, HANA non è in grado di riconoscere in modo implicito la limitazione di archiviazione applicata dall'archiviazione delle istanze large di HANA. Di conseguenza HANA non creerà automaticamente un nuovo file di dati quando viene raggiunto il limite delle dimensioni dei file di 16TB. Quando HANA tenta di espandere il file oltre i 16 TB, HANA segnala gli errori e il server index si arresta in modo anomalo alla fine.

> [!IMPORTANT]
> Per evitare che HANA provi a espandere i file di dati oltre il limite delle dimensioni del file di 16 TB per l'archiviazione di istanze large di HANA, è necessario impostare i parametri seguenti nel file di configurazione SAP HANA Global. ini
> 
> - datavolume_striping = true
> - datavolume_striping_size_gb = 15000
> - Vedere anche la nota SAP [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Tenere presente la nota SAP [#2631285](https://launchpad.support.sap.com/#/notes/2631285)


Con SAP HANA 2.0, il framework hdbparam è stato deprecato. I parametri devono pertanto essere impostati tramite i comandi SQL. Per altre informazioni, vedere la [nota SAP n. 2399079: Eliminazione di hdbparam in HANA 2](https://launchpad.support.sap.com/#/notes/2399079).

Per altre informazioni sul layout di archiviazione per l'architettura in uso, vedere [Scenari supportati nelle istanze Large di HANA](hana-supported-scenario.md).


**Passaggi successivi**

- Vedere [Esempio di installazione di SAP HANA in istanze Large di HANA](hana-example-installation.md)










































 







 




