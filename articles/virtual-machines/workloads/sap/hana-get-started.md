---
title: 'Guida introduttiva: Installazione manuale di SAP HANA a istanza singola nelle VM di Azure | Documentazione Microsoft'
description: Guida introduttiva per l&quot;installazione manuale di SAP HANA a istanza singola nelle VM di Azure
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: e95d3a57594d55b7d7176d873cde8abfa6e12ecd
ms.contentlocale: it-it
ms.lasthandoff: 05/15/2017


---
# <a name="quick-start-guide-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>Guida introduttiva: Installazione manuale di SAP HANA a istanza singola nelle VM di Azure
## <a name="introduction"></a>Introduzione
Questa guida introduttiva consente di configurare un prototipo o un sistema demo di SAP HANA a istanza singola nelle macchine virtuali di Azure quando si installano manualmente SAP NetWeaver 7.5 e SAP HANA 1.0 SP12. Non sostituisce la documentazione SAP, ma si concentra maggiormente sull'aspetto della distribuzione in Azure.

>[!Note]
>Si noti che questa guida fa riferimento alle distribuzioni di SAP HANA nelle macchine virtuali di Azure. Tutte le considerazioni sulla distribuzione di SAP HANA in istanze di grandi dimensioni HANA sono descritte in un'altra documentazione disponibile anche in https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started
 

La guida presuppone che l'utente abbia familiarità con le nozioni di base dell'IaaS di Azure come:
 * La modalità di distribuzione di macchine virtuali o reti virtuali tramite il portale di Azure o PowerShell.
 * Lo strumento dell'interfaccia della riga di comando multipiattaforma di Azure, tra cui l'opzione per usare i modelli di JSON (JavaScript Object Notification).

La guida presuppone inoltre che l'utente abbia familiarità con SAP HANA e SAP NetWeaver e con le relative procedure di installazione in locale.

Altre informazioni che è necessario tenere presente e conoscere per installare e rendere operativi SAP HANA e le istanze dell'applicazione SAP in Azure includono:

La pianificazione della distribuzione SAP in Azure, che comprende la pianificazione della rete virtuale e l'uso di Archiviazione di Azure: consultare [SAP NetWeaver in macchine virtuali di Azure: guida alla pianificazione e all'implementazione](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).

I principi di distribuzione e i modi per distribuire le macchine virtuali in Azure: consultare [Distribuzione di macchine virtuali di Azure per SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)

Gli aspetti di disponibilità elevata di SAP NetWeaver ASCS, SCS ed ERS in Azure: studiare la guida [Disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide)

I dettagli su come migliorare l'efficienza all'uso di un'installazione a più SID di ASCS/SCS in Azure illustrati nella guida [Creare una configurazione di SAP NetWeaver a più SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid).

I principi per l'esecuzione di SAP NetWeaver basati sulle macchine virtuali di Linux in Azure illustrati in [Esecuzione di SAP NetWeaver nelle VM SUSE Linux di Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Leggere questa guida che contiene informazioni dettagliate su impostazioni specifiche per le macchine virtuali Linux in Azure e dettagli su come collegare correttamente i dischi di archiviazione di Azure alle macchine virtuali Linux.

Finora le macchine virtuali di Azure sono certificate da SAP solo per le configurazioni per passare a SAP HANA. Le configurazioni con scalabilità orizzontale con il carico di lavoro SAP HANA non sono ancora supportate. Per la disponibilità elevata di SAP HANA in caso di configurazioni per passare a un piano superiore, consultare [Disponibilità elevata di SAP HANA in Macchine virtuali di Azure (VM)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)


Le indicazioni e la possibilità di eseguire il backup dei database di SAP HANA nelle macchine virtuali di Azure sono elencate in queste guide

* [Guida del backup di SAP HANA in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [Backup di SAP HANA di Azure a livello di file](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
* [Backup di SAP HANA basato su snapshot di archiviazione](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

L'uso SAP Cloud Appliance Library per distribuire S/4HANA o BW/4HANA è descritto in [Distribuire SAP S/4HANA o BW/4HANA in Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

SAP documenta i sistemi operativi supportati da SAP HANA [SAP Support Note #2235581 - SAP HANA: Supported Operating Systems](https://launchpad.support.sap.com/#/notes/2235581/E) (Nota di supporto SAP #2235581 - SAP HANA: sistemi operativi supportati). Le macchine virtuali di Azure supportano solo un sottoinsieme di questi sistemi operativi. Vale a dire che il sistema operativo seguente è supportato per la distribuzione di SAP HANA in Azure: 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

La documentazione aggiuntiva di SAP relativa ai diversi sistemi operativi Linux CAB e SAP HANA è elencata di seguito:

* [Nota di supporto SAP #171356: informazioni generali sul software SAP in Linux](https://launchpad.support.sap.com/#/notes/1984787)
* [Nota di supporto SAP #1944799: linee guida di SAP HANA per l'installazione del sistema operativo SLES](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
* [Nota di supporto SAP #2205917: impostazioni del sistema operativo consigliate per il database di SAP HANA per SLES 12 for SAP Applications](https://launchpad.support.sap.com/#/notes/2205917/E)
* [Nota di supporto SAP #1984787: note di installazione per SUSE Linux Enterprise Server 12](https://launchpad.support.sap.com/#/notes/1984787)
* [Nota di supporto SAP #1391070: soluzioni UUID Linux](https://launchpad.support.sap.com/#/notes/1391070)
* [SAP Support Note #2009879 - SAP HANA Guidelines for Red Hat Enterprise Linux (RHEL) Operating System](https://launchpad.support.sap.com/#/notes/2009879) (Nota di supporto SAP #2009879: linee guida di SAP HANA per il sistema operativo Red Hat Enterprise Linux, RHEL)
* [2292690 - SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E) (2292690 - SAP HANA DB: impostazioni del sistema operativo consigliate per RHEL 7)

Considerare anche queste due note SAP che riguardano il monitoraggio SAP in Azure:

* Nota SAP sul monitoraggio avanzato di SAP con VM Linux in Azure: [Nota SAP 2191498](https://launchpad.support.sap.com/#/notes/2191498/E).
* Nota SAP con informazioni su SAPOSCOL in Linux: [Nota SAP 1102124](https://launchpad.support.sap.com/#/notes/1102124/E).
* Metriche chiave del monitoraggio per SAP in Microsoft Azure: [Nota SAP 2178632](https://launchpad.support.sap.com/#/notes/2178632/E).


I tipi di macchine virtuali di Azure e gli scenari del carico di lavoro supportati da SAP su SAP HANA sono documentati nelle [piattaforme IaaS certificate SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). I tipi di macchina virtuale Azure che possono essere usati e certificati da SAP per il livello dell'applicazione SAP NetWeaver o S/4HANA sono documentati da SAP in [SAP Note 1928533 - SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533/E) (Nota SAP 1928533 - Applicazioni SAP in Azure: prodotti e tipi di macchina virtuale di Azure supportati).

>[!Note]
>SAP, Linux e Azure sono supportati solo nel modello di distribuzione Azure Resource Manager, ARM. e non in quello classico, ASM. 


La guida descrive due diverse modalità per installare manualmente SAP HANA nelle macchine virtuali di Azure:

* Tramite SWPM (SAP Software Provisioning Manager) nell'ambito di un'installazione di NetWeaver distribuita nel passaggio di installazione dell'istanza del database.
* Tramite lo strumento HDBLCM di HANA Lifecycle Management e la successiva installazione di NetWeaver.

È anche possibile usare SWPM e installare tutti i componenti, ovvero SAP HANA, server applicazioni SAP, istanza ASCS, in una singola macchina virtuale come descritto [qui](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Questa opzione, tuttavia, non è descritta in questa guida, ma gli aspetti da prendere in considerazione sono gli stessi.

Prima di iniziare un'installazione, assicurarsi di leggere la sezione "Preparare le VM di Azure per l'installazione manuale di SAP HANA", immediatamente dopo i due elenchi di controllo per l'installazione di SAP HANA. In questo modo, è possibile evitare diversi errori di base che possono verificarsi quando si usa solo una configurazione predefinita della VM di Azure.

## <a name="checklist-for-sap-hana-installation-using-sap-swpm"></a>Elenco di controllo per l'installazione di SAP HANA tramite SAP SWPM
Questa sezione elenca i passaggi chiave da seguire per un'installazione manuale di SAP HANA a istanza singola, quando si usa SAP SWPM per eseguire un'installazione di SAP NetWeaver 7.5 distribuita. Le singole voci vengono spiegate più in dettaglio negli screenshot presenti nella guida:

* Creare una rete virtuale di Azure che includa le due VM di test.
* Distribuire due macchine virtuali di Azure con sistema operativo, in questo caso SLES/SLES-for-SAP Applications 12 SP1, in base al modello di Azure Resource Manager.
* Collegare due dischi di archiviazione Standard o Premium alla macchina virtuale del server app, ad esempio dischi da 75 o 500 GB.
* Collegare i dischi di archiviazione Premium alla macchina virtuale del server del database HANA. Per informazioni dettagliate consultare la sezione sulla configurazione dei dischi.
* A seconda dei requisiti di dimensione o velocità effettiva, collegare più dischi e creare volumi con striping tramite la gestione di volumi logici o un'utilità di amministrazione di più dispositivi (mdadm) a livello di sistema operativo nella VM.
* Creare file system XFS nei volumi logici o nei dischi collegati.
* Montare i nuovi file system XFS a livello di sistema operativo. Usare un file system per tutto il software SAP e l'altro, ad esempio, per la directory /sapmnt ed eventualmente i backup. Nel server di database SAP HANA montare i file system XFS sui dischi di archiviazione Premium come /hana e /usr/sap. Questo processo è necessario per impedire che venga occupato tutto lo spazio del file system radice, che ha dimensioni limitate nelle VM Linux in Azure.
* Immettere gli indirizzi IP locali delle VM di test in /etc/hosts.
* Immettere il parametro nofail in /etc/fstab.
* Impostare i parametri del kernel Linux in base alle note SAP relative ad HANA e alla versione del sistema operativo Linux usata. Per altre informazioni, vedere la sezione "Parametri del kernel".
* Aggiungere spazio di swapping.
* Facoltativamente, installare un desktop con interfaccia grafica nelle VM di test. In alternativa, usare un'installazione SAPinst remota.
* Scaricare il software SAP da SAP Service Marketplace.
* Installare l'istanza di SAP ASCS nella VM del server app.
* Condividere la directory /sapmnt tra le VM di test mediante NFS. La macchina virtuale del server applicazioni è il server NFS.
* Installare l'istanza del database, incluso HANA, tramite SWPM nella VM del server di database.
* Installare il server dell'applicazione primaria, PAS, nella macchina virtuale del server applicazioni.
* Avviare la console di gestione SAP (SAP MC) e connettersi tramite, ad esempio, SAP GUI/HANA Studio.

## <a name="checklist-for-sap-hana-installation-using-hdblcm"></a>Elenco di controllo per l'installazione di SAP HANA tramite HDBLCM
Questa sezione elenca i passaggi chiave da seguire per un'installazione manuale di SAP HANA a istanza singola, a scopo di dimostrazione o per la creazione di prototipi, quando si usa SAP HDBLCM per eseguire un'installazione di SAP NetWeaver 7.5 distribuita. Le singole voci vengono spiegate più in dettaglio negli screenshot presenti nella guida:

* Creare una rete virtuale di Azure che includa le due VM di test.
* Distribuire due macchine virtuali di Azure con sistema operativo, in questo caso SLES/SLES-for-SAP Applications 12 SP1, in base al modello di Azure Resource Manager.
* Collegare due dischi di archiviazione Standard o Premium alla macchina virtuale del server app, ad esempio dischi da 75 o 500 GB.
* Collegare i dischi di archiviazione Premium alla macchina virtuale del server del database HANA. Per informazioni dettagliate consultare la sezione sulla configurazione dei dischi.
* A seconda dei requisiti di dimensione o velocità effettiva, collegare più dischi e creare volumi con striping tramite la gestione di volumi logici o un'utilità di amministrazione di più dispositivi (mdadm) a livello di sistema operativo nella VM.
* Creare file system XFS nei volumi logici o nei dischi collegati.
* Montare i nuovi file system XFS a livello di sistema operativo. Usare un file system per tutto il software SAP e l'altro, ad esempio, per la directory /sapmnt ed eventualmente i backup. Nel server di database SAP HANA montare i file system XFS sui dischi di archiviazione Premium come /hana e /usr/sap. Questo processo è necessario per impedire che venga occupato tutto lo spazio del file system radice, che ha dimensioni limitate nelle VM Linux in Azure.
* Immettere gli indirizzi IP locali delle VM di test in /etc/hosts.
* Immettere il parametro nofail in /etc/fstab.
* Impostare i parametri del kernel in base alle note SAP relative ad HANA e alla versione del sistema operativo Linux usata. Per altre informazioni, vedere la sezione "Parametri del kernel".
* Aggiungere spazio di swapping.
* Facoltativamente, installare un desktop con interfaccia grafica nelle VM di test. In alternativa, usare un'installazione SAPinst remota.
* Scaricare il software SAP da SAP Service Marketplace.
* Creare un gruppo "sapsys" con ID gruppo 1001 nella VM del server di database HANA.
* Installare SAP HANA nella VM del server di database usando HDBLCM (HANA Database Lifecycle Manager).
* Installare l'istanza di SAP ASCS nella VM del server app.
* Condividere la directory /sapmnt tra le VM di test mediante NFS. La macchina virtuale del server applicazioni è il server NFS.
* Installare l'istanza del database, incluso HANA, tramite SWPM nella VM del server di database HANA.
* Installare il server dell'applicazione primaria, PAS, nella macchina virtuale del server applicazioni.
* Avviare la console SAP MC e connettersi tramite SAP GUI/HANA Studio.

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>Preparare le VM di Azure per l'installazione manuale di SAP HANA
Questa sezione contiene gli argomenti seguenti:

* Aggiornamenti del sistema operativo
* Configurazione dei dischi
* Parametri del kernel
* File system
* /etc/hosts
* /etc/fstab

### <a name="os-updates"></a>Aggiornamenti del sistema operativo
Poiché i distributori del sistema operativo Linux offrono aggiornamenti e correzioni per il sistema operativo che contribuiscono alla sicurezza e al corretto funzionamento, è consigliabile verificare se sono disponibili aggiornamenti prima di installare il software aggiuntivo.
Molte volte le chiamate al supporto si potrebbero evitare aggiornando il sistema con tutte le patch. Assicurarsi anche di usare:
* SUSE Linux Enterprise Server per applicazioni SAP
* Red Hat Enterprise Linux per applicazioni SAP o per SAP HANA 

Registrare la distribuzione del sistema operativo con la sottoscrizione di Linux ricevuta dal fornitore di Linux, se non lo si è ancora fatto. Tenere presente che per quanto riguarda SUSE, esistono anche immagini del sistema operativo SUSE per le applicazioni SAP che includono già i servizi e che sono state registrate automaticamente.

Ad esempio, con SUSE Linux controllare semplicemente le patch disponibili con:

 `sudo zypper list-patches`

A seconda del tipo di errore, le patch sono suddivise per categoria e gravità.

Valori utilizzati comunemente per le categorie sono: sicurezza, consigliato, facoltativo, funzionalità, documento o yast.

Valori utilizzati comunemente per la gravità sono: critica, importante, moderata, bassa o non specificata.

zypper cerca solo gli aggiornamenti necessari per i pacchetti installati.

Pertanto un comando di esempio potrebbe essere:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

Se si aggiunge il parametro *--dry-run* è possibile testare l'aggiornamento, ma il sistema non viene effettivamente aggiornato.


### <a name="disk-setup"></a>Configurazione dei dischi
Il file system radice di una VM Linux in Azure ha dimensioni limitate. È quindi necessario aggiungere altro spazio su disco a una macchina virtuale di Azure per l'esecuzione di SAP. Per le macchine virtuali di Azure del server applicazioni SAP potrebbe essere consentito l'uso dei dischi di Archiviazione Standard di Azure. Tuttavia per le macchine virtuali di Azure SAP HANA DBMS l'uso dei dischi di Archiviazione Premium di Azure per le implementazioni di produzione e non è obbligatorio.

In base ai [requisiti di archiviazione SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), viene suggerita la seguente configurazione di Archiviazione Premium di Azure: 

| SKU di VM | RAM |  /hana/data and /hana/log <br /> con striping con LVM o MDAADM | /hana/shared | volume /root | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

Nella configurazione del disco suggerita il volume di dati HANA e il volume del registro verranno inseriti nello stesso set di dischi di Archiviazione Premium di Azure con striping LVM o MDADM. Non è necessario definire tutti i livelli di ridondanza RAID, perché l'Archiviazione Premium di Azure manterrà tre immagini dei dischi per motivi di ridondanza. È possibile stabilire diverse configurazioni del disco. Tuttavia, consultare gli articoli [SAP HANA TDI storage Requirements](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) (Requisiti di archiviazione di SAP HANA TDI) e la [Guida all'installazione e all'aggiornamento del server SAP HANA](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm) per assicurarsi di aver configurato uno spazio di archiviazione sufficiente. Considerare anche i diversi volumi della velocità effettiva del disco rigido virtuale dei diversi dischi di Archiviazione Premium di Azure, come documentato nei [limiti relativi ai dischi di archiviazione Premium](https://docs.microsoft.com/azure/storage/storage-premium-storage). 

In base alle esigenze, è possibile aggiungere altri dischi di archiviazione Premium alle macchine virtuali HANA DBMS allo scopo di archiviare i database o i backup del log delle transazioni.

Per altre informazioni sui due strumenti principali per la configurazione dello striping, vedere gli articoli seguenti:

* [Configurare RAID software in Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Configurare LVM in una macchina virtuale Linux in Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Per altre informazioni su come collegare i dischi a una macchina virtuale di Azure che esegue Linux come sistema operativo guest, vedere [come collegare i dischi a una macchina virtuale Linux](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

L'Archiviazione Premium di Azure consente di definire le modalità di memorizzazione nella cache del disco. Per il set di stripe che contiene /hana/data e /hana/log la memorizzazione nella cache del disco deve essere disabilitata. Per altri volumi, ovvero dischi, la modalità di memorizzazione nella cache deve essere impostata su READONLY.

Per altre informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../../../storage/storage-premium-storage.md).

Per trovare modelli JSON di esempio per la creazione di VM, consultare [Azure Quickstart Templates](https://github.com/Azure/azure-quickstart-templates) (Modelli di avvio rapido di Azure).
"vm-simple-sles" mostra un modello di base che include una sezione di archiviazione con un disco dati aggiuntivo da 100 GB. Questo modello può essere usato come base. È previsto che il modello vega adattato alla configurazione specifica.

>[!Note]
>Tenere presente che è importante collegare il disco di Archiviazione di Azure usando UUID, come descritto in [Esecuzione di SAP NetWeaver nelle VM SUSE Linux di Microsoft Azure](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


Nell'ambiente di test sono stati collegati due dischi di archiviazione Standard di Azure alla VM del server app SAP, come illustrato nello screenshot seguente. Un disco è stato usato per archiviare tutto il software SAP per l'installazione, ad esempio NetWeaver 7.5, SAP GUI, SAP HANA e così via. Il secondo disco è stato usato per assicurarsi di avere a disposizione spazio sufficiente per altre esigenze, ad esempio backup e test dei dati, e per la condivisione della directory /sapmnt (ovvero i profili SAP) tra tutte le VM appartenenti alla stessa infrastruttura SAP.

![Finestra Disks (Dischi) del server app SAP HANA con due dischi dati e le relative dimensioni](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Parametri del kernel
SAP HANA richiede impostazioni specifiche del kernel Linux che non fanno parte delle immagini della raccolta standard di Azure e devono essere definite manualmente. In base a Red Hat o SUSE, i parametri possono essere diversi. Le note SAP elencate in precedenza, contengono le informazioni su tali parametri. Nelle schermate illustrate è stato usato SUSE Linux 12 SP1. 

SLES-for-SAP Applications 12 GA e SP1 hanno un nuovo strumento che sostituisce la vecchia utilità sapconf. Per questo strumento, chiamato tuned-adm, è disponibile un apposito profilo di SAP HANA. Per ottimizzare il sistema per SAP HANA è sufficiente digitare, come utente root, il comando: "tuned-adm profile sap-hana"

Per altre informazioni su tuned-adm, vedere la documentazione di SUSE:

* [Documentazione di SLES-for-SAP Applications 12 SP1 sull'uso di tuned-adm profile sap-hana.](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)

Nello screenshot seguente si può osservare come tuned-adm ha modificato i valori di transparent_hugepage e numa_balancing in base alle impostazioni obbligatorie di SAP HANA.

![Lo strumento tuned-adm modifica i valori in base alle impostazioni obbligatorie di SAP HANA](./media/hana-get-started/image005.jpg)

Per rendere permanenti le impostazioni del kernel di SAP HANA, usare grub2 in SLES 12. Per altre informazioni su grub2, vedere la [sezione "Configuration File Structure" (Struttura del file di configurazione) della documentazione di SUSE](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Lo screenshot seguente illustra come le impostazioni del kernel sono state modificate nel file di configurazione e quindi compilate tramite grub2-mkconfig.

![Impostazioni del kernel modificate nel file di configurazione e compilate tramite grub2-mkconfig](./media/hana-get-started/image006.jpg)

È anche possibile modificare le impostazioni tramite YaST e le impostazioni di **Boot Loader** > **Kernel Parameters** (Parametri del kernel).

![Scheda delle impostazioni Kernel Parameters (Parametri del kernel) di YaST Boot Loader](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>File system
Lo screenshot seguente mostra due file system che sono stati creati nella VM del server app SAP oltre ai due dischi di archiviazione Standard di Azure collegati. Entrambi i file system sono di tipo XFS e sono montati in /sapdata e /sapsoftware.

Non è obbligatorio per definire la struttura dei file system in questo modo. Esistono altre opzioni per strutturare lo spazio su disco. La considerazione più importante riguarda la necessità di evitare che il file system radice esaurisca lo spazio disponibile.

![Due file system creati nella VM del server app SAP](./media/hana-get-started/image008.jpg)

Per quanto riguarda la macchina virtuale del database SAP HANA, è importante sapere che durante l'installazione di un database, tramite SAPinst, SWPM, e la semplice opzione di installazione "tipica", il percorso di installazione è /hana e /usr/sap. L'impostazione predefinita per il percorso di backup dei log di SAP HANA è /usr/sap. Anche in questo caso, è importante evitare che il file system radice esaurisca lo spazio di archiviazione ed è quindi necessario verificare che sia disponibile spazio sufficiente in /hana e /usr/sap prima di installare SAP HANA tramite SWPM.

Per una descrizione del layout del file system standard di SAP HANA, vedere [SAP HANA Server Installation and Update Guide](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm) (Guida all'aggiornamento e all'installazione del server SAP HANA).
![File system aggiuntivi creati nella VM del server app SAP](./media/hana-get-started/image009.jpg)

Durante l'installazione di SAP NetWeaver in un'immagine della raccolta standard di Azure per SLES/SLES-for-SAP Applications 12, viene visualizzato un messaggio per segnalare che non è disponibile spazio di swapping. Per ignorare questo messaggio, è possibile aggiungere manualmente un file di scambio usando dd, mkswap e swapon. Per istruzioni, cercare "Adding a Swap File Manually" (Aggiunta manuale di un file di scambio) nella [sezione "Using the YaST Partitioner" (Uso del Partitioner YaST) della documentazione di SUSE](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Un'altra opzione consiste nel configurare lo spazio di swapping tramite l'agente di macchine virtuali Linux. Per altre informazioni, vedere [Azure Linux Agent User Guide](../../linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Guida dell'utente dell'agente Linux di Azure).

![Messaggio popup che segnala la presenza di spazio di swapping insufficiente](./media/hana-get-started/image010.jpg)


### <a name="etchosts"></a>/etc/hosts
Un altro aspetto importante da considerare prima di iniziare a installare SAP è quello di includere i nomi host e gli indirizzi IP delle VM SAP nel file /etc/hosts. Distribuire tutte le VM SAP all'interno di una rete virtuale di Azure e quindi usare gli indirizzi IP interni.

![Nomi host e indirizzi IP delle VM SAP elencati nel file /etc/hosts](./media/hana-get-started/image011.jpg)

### <a name="etcfstab"></a>/etc/fstab

Durante la fase di test si è rivelato opportuno aggiungere il parametro nofail a fstab. In questo modo, anche se si verificano problemi con i dischi, la VM rimane operativa e non si blocca nel processo di avvio. È tuttavia necessario prestare attenzione perché è possibile che in uno scenario di questo tipo non sia disponibile spazio aggiuntivo sul disco e che i processi occupino interamente il file system radice. Se /hana non è presente, SAP HANA non viene avviato.

![Aggiungere il parametro nofail a fstab](./media/hana-get-started/image000c.jpg)

## <a name="install-graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Installare il desktop con interfaccia grafica Gnome in SLES 12/SLES-for-SAP Applications 12
Questa sezione contiene gli argomenti seguenti:

* Installazione del desktop Gnome e xrdp in SLES 12/SLES-for-SAP Applications 12
* Esecuzione della console SAP MC basata su Java con Firefox in SLES 12/SLES-for-SAP Applications 12

In alternativa è possibile usare anche Xterminal e VNC, ma alla data di settembre 2016 questa guida include solo la descrizione relativa a xrdp.

### <a name="installing-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Installazione del desktop Gnome e xrdp in SLES 12/SLES-for-SAP Applications 12
In un ambiente Microsoft Windows è possibile usare con facilità un desktop con interfaccia grafica direttamente nelle VM Linux SAP per eseguire Firefox, Sapinst, SAP GUI, SAP MC o HANA Studio e connettersi alla VM tramite RDP da un computer Windows. Anche se questa operazione potrebbe non essere sempre appropriata per un server di database di produzione, è accettabile per un prototipo o un ambiente dimostrativo puro. Di seguito è illustrata la procedura per installare il desktop Gnome in una VM SLES 12/SLES-for-SAP Applications 12 di Azure:

Installare il desktop Gnome immettendo, ad esempio in una finestra PuTTY, il comando seguente:

`zypper in -t pattern gnome-basic`

Installare xrdp per consentire la connessione alla VM tramite RDP:

`zypper in xrdp`

Modificare /etc/sysconfig/windowmanager e impostare la gestione delle finestre predefinita su Gnome:

`DEFAULT_WM="gnome"`

Eseguire chkconfig per verificare che xrdp venga avviato automaticamente dopo un riavvio:

`chkconfig -level 3 xrdp on`

Se si verificano problemi con la connessione RDP, provare a riavviare, magari da una finestra PuTTY:

`/etc/xrdp/xrdp.sh restart`

Se il riavvio di xrdp descritto in precedenza non funziona, verificare se è presente un file con estensione pid e rimuoverlo:

`check /var/run` e cercare `xrdp.pid`

Rimuovere il file e ripetere quindi l'operazione di riavvio.

### <a name="sap-mc"></a>SAP MC
Se, dopo aver installato il desktop Gnome, si avvia la console SAP MC con interfaccia grafica basata su Java da Firefox in esecuzione in una VM SLES 12/SLES-for-SAP Applications 12 di Azure, è possibile che venga generato un errore dovuto al plug-in Java mancante nel browser.

L'URL per avviare SAP MC è <server>:5<numero_istanza>13.

Per altre informazioni, vedere [Starting the Web-Based SAP Management Console](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm) (Avvio della console di gestione SAP basata sul Web).

Lo screenshot seguente mostra il messaggio di errore visualizzato quando manca il plug-in Java nel browser.

![Messaggio di errore che indica la mancanza del plug-in Java nel browser](./media/hana-get-started/image013.jpg)

Un modo per risolvere il problema consiste semplicemente nell'installare il plug-in mancante con YaST, come illustrato nello screenshot seguente.

![Uso di YaST per installare il plug-in mancante](./media/hana-get-started/image014.jpg)

Immettendo di nuovo l'URL della console di gestione SAP, viene visualizzata una finestra di dialogo con la richiesta di attivare il plug-in.

![Finestra di dialogo con la richiesta di attivazione del plug-in](./media/hana-get-started/image015.jpg)

Un altro problema che potrebbe verificarsi è la visualizzazione di un messaggio di errore relativo a un file mancante: javafx.properties. Questo è correlato al requisito di Oracle Java 1.8 per SAP GUI 7.4. [Vedere la nota SAP 2059429](https://launchpad.support.sap.com/#/notes/2059424) Il file javafx richiesto non è incluso né nella versione IBM Java né nel pacchetto openjdk fornito con SLES/SLES-for-SAP Applications 12. La soluzione è scaricare e installare Java SE8 da Oracle.

Un articolo che parla di un problema analogo in openSUSE con openjdk è [SAP GUI 7.4 Java for openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306).

## <a name="install-sap-hana-manually-by-using-swpm-as-part-of-a-netweaver-75-installation"></a>Installare SAP HANA manualmente tramite SWPM nell'ambito di un'installazione di NetWeaver 7.5
I vari screenshot inclusi in questa sezione mostrano i principali passaggi da eseguire per l'installazione di SAP NetWeaver 7.5 e SAP HANA SP12 tramite SWPM (SAPinst). Nell'ambito di un'installazione di NetWeaver 7.5, SWPM consente anche di installare il database HANA come istanza singola.

Nell'ambiente di test di esempio è stato installato un solo server app ABAP (Advanced Business Application Programming). Come illustrato nello screenshot seguente, è stata utilizzata l'opzione "Distributed System" (Sistema distribuito) per installare l'istanza ASCS e l'istanza Primary Application Server in una VM di Azure e SAP HANA come sistema di database in un'altra VM di Azure.

![Istanza ASCS e istanza Primary Application Server installate tramite l'opzione "Distributed System" (Sistema distribuito)](./media/hana-get-started/image012.jpg)

Dopo che l'istanza ASCS è stata installata nella VM del server app e visualizzata in verde nella console di gestione SAP, la directory /sapmnt, che include la directory dei profili SAP, deve essere condivisa con la VM del server di database SAP HANA. La procedura di installazione del database deve accedere a queste informazioni. Il modo migliore per garantire l'accesso consiste nell'usare NFS, che può essere configurato con YaST.

![Console di gestione SAP che mostra l'istanza ASCS installata nella VM del server app e visualizzata in verde](./media/hana-get-started/image016.jpg)

Nella VM del server app, la directory /sapmnt deve essere condivisa tramite NFS usando le opzioni **rw** e **no_root_squash**. I valori predefiniti sono "ro" e "root_squash", che possono causare problemi quando si installa l'istanza del database.

![Condivisione della directory /sapmnt tramite NFS usando le opzioni "rw" e "no_root_squash"](./media/hana-get-started/image017b.jpg)

Come illustrato nello screenshot successivo, la condivisione di /sapmnt dalla VM del server app deve essere configurata nella VM del server di database SAP HANA tramite **NFS Client** (Client NFS), con l'aiuto di YaST.

![Condivisione /sapmnt configurata tramite "NFS Client" (Client NFS)](./media/hana-get-started/image018b.jpg)

Per eseguire un'installazione distribuita di NetWeaver 7.5, con **Database Instance** (Istanza database) come illustrato nello screenshot seguente, accedere alla VM del server di database SAP HANA e avviare SWPM.

![Installazione di un'istanza del database tramite l'accesso alla VM del server di database SAP HANA e l'avvio di SWPM](./media/hana-get-started/image019.jpg)

Dopo aver selezionato un'installazione tipica e il percorso del supporto di installazione, immettere un SID di database, il nome host, il numero di istanza e la password dell'amministratore di sistema del database.

![Pagina di accesso dell'amministratore di sistema del database SAP HANA](./media/hana-get-started/image035b.jpg)

Immettere la password per lo schema DBACOCKPIT.

![Casella di immissione della password per lo schema DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Immettere una domanda per la password dello schema SAPABAP1.

![Immettere una domanda per la password dello schema SAPABAP1](./media/hana-get-started/image037b.jpg)

Al termine dell'attività, viene visualizzato un segno di spunta verde accanto a ogni fase del processo di installazione del database. In una finestra distinta viene visualizzato il messaggio "Execution of ... Database Instance has completed" (Esecuzione dell'istanza di database ... completata).

![Finestra in cui viene visualizzata l'attività completata con il messaggio di conferma](./media/hana-get-started/image023.jpg)

Al termine dell'installazione, inoltre, la console di gestione SAP visualizza l'istanza di database in verde e l'elenco completo dei processi di SAP HANA (hdbindexserver, hdbcompileserver e così via).

![Finestra della console di gestione SAP con l'elenco dei processi di SAP HANA](./media/hana-get-started/image024.jpg)

Lo screenshot seguente mostra le parti della struttura di file in /hana/shared creata da SWPM durante l'installazione di HANA. Poiché non è possibile specificare un percorso diverso, è importante montare spazio aggiuntivo su disco in /hana prima di installare SAP HANA tramite SWPM per evitare che il file system radice esaurisca lo spazio disponibile.

![Struttura di file di /hana/shared creata durante l'installazione di HANA](./media/hana-get-started/image025.jpg)

Questo screenshot mostra la struttura di file della directory /usr/sap.

![Struttura di file della directory /usr/sap](./media/hana-get-started/image026.jpg)

L'ultimo passaggio dell'installazione distribuita di ABAP è costituito dall'istanza Primary Application Server.

![Installazione di ABAP che mostra l'istanza Primary Application Server come passaggio finale](./media/hana-get-started/image027b.jpg)

Dopo aver installato l'istanza Primary Application Server e SAP GUI, è necessario usare la transazione "dbacockpit" per confermare che l'installazione di SAP HANA è stata completata.

![Finestra DBA Cockpit (Pannello di controllo DBA) di conferma dell'installazione](./media/hana-get-started/image028b.jpg)

Come ultimo passaggio, è possibile prima installare SAP HANA Studio nella VM del server app SAP e quindi connettersi all'istanza di SAP HANA in esecuzione nella VM del server di database.

![Installazione di SAP HANA Studio nella VM del server app SAP](./media/hana-get-started/image038b.jpg)

## <a name="install-sap-hana-manually-by-using-the-hana-lifecycle-management-tool-hdblcm"></a>Installare SAP HANA manualmente tramite lo strumento HDBLCM
Oltre a installare SAP HANA come parte di un'installazione distribuita tramite SWPM, è possibile installare la versione autonoma di HANA usando lo strumento HDBLCM (HANA Database Lifecycle Manager). Successivamente, è possibile installare, ad esempio, SAP NetWeaver 7.5. Gli screenshot riportati di seguito illustrano questo processo.

Di seguito sono indicate tre fonti di informazioni sullo strumento HDBLCM:

* [Choosing the Correct SAP HANA HDBLCM for Your Task (Scelta dello strumento HDBLCM di SAP HANA per la propria attività)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)
* [SAP HANA Lifecycle Management Tools (Strumenti di SAP HANA Lifecycle Management)](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)
* [SAP HANA Server Installation and Update Guide (Guida all'installazione e all'aggiornamento del server SAP HANA)](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)

Per evitare gli eventuali problemi che possono verificarsi con l'impostazione di un ID gruppo predefinito per l'utente \<SID HANA\>adm (creato dallo strumento HDBLCM), è necessario definire un nuovo gruppo denominato "sapsys" con ID gruppo 1001 prima di installare SAP HANA usando HDBLCM.

![Nuovo gruppo "sapsys" definito con ID gruppo 1001](./media/hana-get-started/image030.jpg)

Al primo avvio di HDBLCM, viene visualizzato un menu di avvio semplice, in cui è necessario selezionare la voce 1, **Install new system** (Installa nuovo sistema), come illustrato nello screenshot seguente.

![Opzione "Install new system" (Installa nuovo sistema) nella finestra di avvio di HDBLCM](./media/hana-get-started/image031.jpg)

Nello screenshot riportato di seguito sono visualizzate tutte le opzioni chiave selezionate in precedenza.

> [!IMPORTANT]
> Le directory denominate per i volumi di dati e log di HANA, nonché il percorso di installazione (/hana/shared in questo esempio) e /usr/sap, non devono essere inclusi nel file system radice. Le directory appartengono, infatti, ai dischi dati di Azure collegati alla VM, come descritto nella sezione relativa alla configurazione della VM di Azure. In questo modo, è possibile evitare che il file system radice esaurisca lo spazio disponibile. Si può anche osservare che l'ID utente dell'amministratore di HANA è 1005 e appartiene al gruppo sapsys (ID 1001) definito prima dell'installazione.

![Elenco di tutti i componenti chiave di SAP HANA selezionati in precedenza](./media/hana-get-started/image032.jpg)

È possibile esaminare i dettagli dell'utente \<SID HANA\>adm di HANA (azdadm nello screenshot seguente) in /etc/passwd.

![Dettagli dell'utente \<SID HANA\>adm disponibili in /etc/passwd](./media/hana-get-started/image033.jpg)

Dopo aver installato SAP HANA tramite HDBLCM, è possibile visualizzare la struttura di file in SAP HANA Studio. Lo schema SAPABAP1, che include tutte le tabelle di SAP NetWeaver, non è ancora disponibile.

![Struttura di file di SAP HANA in SAP HANA Studio](./media/hana-get-started/image034.jpg)

Dopo l'installazione di SAP HANA, è possibile installare SAP NetWeaver. Come mostra questo screenshot, l'installazione è stata eseguita in modo distribuito tramite SWPM (come descritto nella sezione precedente). Quando si installa l'istanza del database tramite SWPM, è necessario immettere gli stessi dati specificati in HDBLCM, ad esempio il nome host, il SID HANA e il numero di istanza. SWPM usa quindi l'installazione esistente di HANA aggiungendo altri schemi.

![Installazione distribuita eseguita tramite SWPM](./media/hana-get-started/image035b.jpg)

Lo screenshot seguente mostra il passaggio dell'installazione tramite SWPM in cui vengono immessi i dati relativi allo schema DBACOCKPIT.

![Passaggio dell'installazione tramite SWPM in cui vengono immessi i dati relativi allo schema DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Immettere i dati sullo schema SAPABAP1.

![Immissione dei dati sullo schema SAPABAP1](./media/hana-get-started/image037b.jpg)

Dopo aver terminato l'installazione dell'istanza del database con SWPM, lo schema SAPABAP1 è visibile in SAP HANA Studio.

![Schema SAPABAP1 in SAP HANA Studio](./media/hana-get-started/image038b.jpg)

Infine, dopo l'installazione del server app SAP e di SAP GUI, è possibile verificare l'istanza del database HANA con la transazione "dbacockpit".

![Istanza del database HANA verificato con la transazione "dbacockpit"](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Download del software SAP
È possibile scaricare il software da SAP Service Marketplace, come illustrato negli screenshot seguenti.

* Download di NetWeaver 7.5 per Linux/HANA:

 ![Finestra di installazione e aggiornamento del servizio SAP per il download di NetWeaver 7.5](./media/hana-get-started/image001.jpg)
* Download di HANA SP12 Platform Edition:

 ![Finestra di installazione e aggiornamento del servizio SAP per il download di HANA SP12 Platform Edition](./media/hana-get-started/image002.jpg)


