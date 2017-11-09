---
title: 'Guida introduttiva: Installazione manuale di SAP HANA a istanza singola nelle macchine virtuali di Azure | Microsoft Docs'
description: Guida introduttiva per l'installazione manuale di SAP HANA a istanza singola nelle macchine virtuali di Azure
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
ms.openlocfilehash: 321a86d6ce355273820617e6de9df2b0816c73fa
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2017
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>Guida introduttiva: Installazione manuale di SAP HANA a istanza singola nelle macchine virtuali di Azure
## <a name="introduction"></a>Introduzione
Questa guida consente di configurare SAP HANA a istanza singola nelle macchine virtuali di Azure quando si installano manualmente SAP NetWeaver 7.5 e SAP HANA 1.0 SP12. L'argomento principale di questa guida è la distribuzione di SAP HANA in Azure. Questa guida non sostituisce la documentazione SAP. 

>[!Note]
>Questa guida descrive le distribuzioni di SAP HANA in macchine virtuali di Azure. Per informazioni sulla distribuzione di SAP HANA in istanze HANA di grandi dimensioni, vedere [Uso di SAP nelle macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Prerequisiti
La guida presuppone che l'utente abbia familiarità con le nozioni di base sull'infrastruttura distribuita come servizio (IaaS) come:
 * Come distribuire macchine virtuali o reti virtuali tramite il portale di Azure o PowerShell.
 * L'interfaccia della riga di comando multipiattaforma di Azure che include l'opzione per l'uso dei modelli JSON (JavaScript Object Notification).

Questo guida presuppone che l'utente abbia familiarità anche con le nozioni seguenti:
* SAP HANA e SAP NetWeaver e loro installazione locale.
* Installazione e uso delle istanze di applicazione SAP HANA e SAP in Azure.
* I concetti e le procedure seguenti:
   * Pianificazione della distribuzione SAP in Azure, inclusi la pianificazione della rete virtuale di Azure e l'uso dell'archiviazione di Azure. Vedere [SAP NetWeaver in macchine virtuali di Azure: guida alla pianificazione e all'implementazione](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Principi di distribuzione e modalità di distribuzione delle macchine virtuali in Azure. Vedere [Distribuzione di macchine virtuali di Azure per SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Disponibilità elevata per SAP NetWeaver ASCS (ABAP SAP Central Services), SCS (SAP Central Services) ed ERS (Evaluated Receipt Settlement) in Azure. Vedere [Disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Informazioni dettagliate su come migliorare l'efficienza di utilizzo di un'installazione a più SID di ASCS/SCS in Azure. Vedere [Creare una configurazione di SAP NetWeaver a più SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Principi di esecuzione di SAP NetWeaver basato su macchine virtuali Linux in Azure. Vedere [Esecuzione di SAP NetWeaver nelle VM SUSE Linux di Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Questa guida descrive le impostazioni specifiche per le macchine virtuali Linux in Azure e include informazioni dettagliate su come collegare correttamente i dischi di archiviazione di Azure alle macchine virtuali Linux.

Attualmente, le macchine virtuali di Azure sono certificate da SAP solo per le configurazioni per passare a SAP HANA. Le configurazioni con scalabilità orizzontale con il carico di lavoro SAP HANA non sono ancora supportate. Per la disponibilità elevata di SAP HANA nei casi di configurazioni per passare a un livello superiore, vedere [Disponibilità elevata di SAP HANA nelle macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability).

Se si cerca di ottenere un'istanza di SAP HANA o S/4HANA o un sistema BW/4HANA distribuito rapidamente, può essere utile usare [SAP Cloud Appliance Library](http://cal.sap.com). In [questa guida](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) è disponibile la documentazione sulla distribuzione ad esempio di un sistema S/4HANA tramite SAP CAL in Azure. È sufficiente avere una sottoscrizione di Azure e un utente SAP che è possibile registrare in SAP Cloud Appliance Library.

## <a name="additional-resources"></a>Risorse aggiuntive
### <a name="sap-hana-backup"></a>Backup di SAP HANA
Per informazioni sul backup dei database SAP HANA nelle macchine virtuali di Azure, vedere:
* [Guida del backup di SAP HANA in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [Backup di SAP HANA di Azure a livello di file](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
* [Backup di SAP HANA basato su snapshot di archiviazione](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

### <a name="sap-cloud-appliance-library"></a>SAP Cloud Appliance Library
Per informazioni sull'uso di SAP Cloud Appliance Library per la distribuzione di S/4HANA o BW/4HANA, vedere [Distribuire SAP S/4HANA o BW/4HANA in Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>Sistemi operativi supportati di SAP HANA
Per informazioni sui sistemi operativi supportati di SAP HANA, vedere [SAP Support Note #2235581 - SAP HANA: Supported Operating Systems](https://launchpad.support.sap.com/#/notes/2235581/E) (Nota di supporto SAP n. 2235581: SAP HANA: sistemi operativi supportati). Le macchine virtuali di Azure supportano soltanto alcuni di questi sistemi operativi. I sistemi operativi seguenti sono supportati per la distribuzione di SAP HANA in Azure: 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

Per altra documentazione SAP su SAP HANA e sui diversi sistemi operativi Linux, vedere:

* [SAP Support Note #171356 - SAP Software on Linux:  General Information](https://launchpad.support.sap.com/#/notes/1984787) (Nota di supporto SAP n. 171356: informazioni generali sul software SAP in Linux)
* [SAP Support Note #1944799 - SAP HANA Guidelines for SLES Operating System Installation](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html) (Nota di supporto SAP n. 1944799: linee guida di SAP HANA per l'installazione del sistema operativo SLES)
* [SAP Support Note #2205917 - SAP HANA DB Recommended OS Settings for SLES 12 for SAP Applications](https://launchpad.support.sap.com/#/notes/2205917/E) (Nota di supporto SAP n. 2205917: impostazioni del sistema operativo consigliate per il database di SAP HANA per SLES 12 for SAP Applications)
* [SAP Support Note #1984787 - SUSE Linux Enterprise Server 12:  Installation Notes](https://launchpad.support.sap.com/#/notes/1984787) (Nota di supporto SAP n. 1984787: SUSE Linux Enterprise Server 12: note di installazione)
* [SAP Support Note #1391070 – Linux UUID Solutions](https://launchpad.support.sap.com/#/notes/1391070) (Nota di supporto SAP n. 1391070: soluzioni UUID Linux)
* [SAP Support Note #2009879 - SAP HANA Guidelines for Red Hat Enterprise Linux (RHEL) Operating System](https://launchpad.support.sap.com/#/notes/2009879) (Nota di supporto SAP #2009879: linee guida di SAP HANA per il sistema operativo Red Hat Enterprise Linux, RHEL)
* [2292690 - SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E) (2292690 - SAP HANA DB: impostazioni del sistema operativo consigliate per RHEL 7)

### <a name="sap-monitoring-in-azure"></a>Monitoraggio SAP in Azure
Per informazioni sul monitoraggio SAP in Azure, vedere:

* [Nota SAP 2191498](https://launchpad.support.sap.com/#/notes/2191498/E). Questa nota descrive il monitoraggio avanzato SAP con macchine virtuali Linux in Azure. 
* [Nota SAP 1102124](https://launchpad.support.sap.com/#/notes/1102124/E). Questa nota include informazioni su SAPOSCOL in Linux. 
* [Nota SAP 2178632](https://launchpad.support.sap.com/#/notes/2178632/E). Questa nota descrive le metriche di monitoraggio principali per SAP in Microsoft Azure.

### <a name="azure-vm-types"></a>Tipi di macchine virtuali di Azure
I tipi di macchine virtuali di Azure e gli scenari di carico di lavoro supportati da SAP usati con SAP HANA sono documentati nelle [piattaforme IaaS certificate SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). 

I tipi di macchine virtuali di Azure certificati da SAP per SAP NetWeaver o il livello di applicazione S/4HANA sono documentati in [SAP Note 1928533 - SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533/E) (Nota SAP 1928533 - Applicazioni SAP in Azure: prodotti e tipi di macchine virtuali di Azure supportati).

>[!Note]
>L'integrazione tra SAP, Linux e Azure è supportata solo nel modello di distribuzione Azure Resource Manager e non in quello classico. 

## <a name="manual-installation-of-sap-hana"></a>Installazione manuale di SAP HANA
La guida descrive due diverse modalità per installare manualmente SAP HANA nelle macchine virtuali di Azure:

* Tramite SWPM (SAP Software Provisioning Manager) nell'ambito di un'installazione di NetWeaver distribuita nel passaggio di installazione dell'istanza del database
* Tramite lo strumento HDBLCM (HANA Database Lifecycle Manager) di SAP e la successiva installazione di NetWeaver

È anche possibile usare SWPM per installare tutti i componenti, ovvero SAP HANA, il server applicazioni SAP e l'istanza ASCS, in una singola macchina virtuale come descritto nell'[annuncio del blog di SAP HANA](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Sebbene questa opzione non sia descritta in questa guida rapida, si applicano le stesse considerazioni.

Prima di iniziare un'installazione, è consigliabile leggere la sezione "Preparare le VM di Azure per l'installazione manuale di SAP HANA" più avanti in questa guida. In questo modo, è possibile evitare diversi errori di base che possono verificarsi quando si usa solo una configurazione predefinita della VM di Azure.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Passaggi principali per l'installazione di SAP HANA quando si usa SAP SWPM
Questa sezione elenca i passaggi chiave da seguire per un'installazione manuale di SAP HANA a istanza singola, quando si usa SAP SWPM per eseguire un'installazione di SAP NetWeaver 7.5 distribuita. I singoli passaggi sono descritti in dettaglio negli screenshot successivi di questa guida.

1. Creare una rete virtuale di Azure che includa le due macchine virtuali di test.
2. Distribuire le due macchine virtuali di Azure con i sistemi operativi (nell'esempio SUSE Linux Enterprise Server (SLES) e SLES for SAP Applications 12 SP1), in base al modello di Azure Resource Manager.
3. Collegare due dischi di archiviazione Standard o Premium di Azure alla macchina virtuale del server applicazioni, ad esempio dischi da 75 o 500 GB.
4. Collegare i dischi di archiviazione Premium alla macchina virtuale del server del database HANA. Per informazioni dettagliate, vedere la sezione "Configurazione dei dischi" più avanti in questa guida.
5. A seconda dei requisiti di dimensione o velocità effettiva, collegare più dischi e creare volumi con striping tramite uno strumento di gestione di volumi logici o di amministrazione di più dispositivi (MDADM) a livello di sistema operativo all'interno della macchina virtuale.
6. Creare file system XFS nei volumi logici o nei dischi collegati.
7. Montare i nuovi file system XFS a livello di sistema operativo. Usare un unico file system per tutto il software SAP. Usare ad esempio un altro file system per la directory /sapmnt e i backup. Nel server di database SAP HANA montare i file system XFS sui dischi di archiviazione Premium come /hana e /usr/sap. Questo processo è necessario per impedire che venga occupato tutto lo spazio del file system radice, che ha dimensioni limitate nelle VM Linux in Azure.
8. Immettere gli indirizzi IP locali delle macchine virtuali di test nel file /etc/hosts.
9. Immettere il parametro **nofail** nel file /etc/fstab.
10. Impostare i parametri del kernel Linux in base alla versione del sistema operativo Linux in uso. Per altre informazioni, vedere le note SAP appropriate che descrivono HANA e la sezione "Parametri del kernel" in questa guida.
11. Aggiungere spazio di swapping.
12. Facoltativamente, installare un desktop con interfaccia grafica nelle VM di test. In alternativa, usare un'installazione SAPinst remota.
13. Scaricare il software SAP da SAP Service Marketplace.
14. Installare l'istanza di SAP ASCS nella VM del server app.
15. Condividere la directory /sapmnt tra le VM di test mediante NFS. La macchina virtuale del server applicazioni è il server NFS.
16. Installare l'istanza del database, incluso HANA, tramite SWPM nella VM del server di database.
17. Installare il server dell'applicazione primaria, PAS, nella macchina virtuale del server applicazioni.
18. Avviare la console di gestione SAP (SAP MC). Connettersi ad esempio all'interfaccia utente grafica di SAP o HANA Studio.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Passaggi principali per l'installazione di SAP HANA quando si usa HDBLCM
Questa sezione elenca i passaggi principali per un'installazione manuale di SAP HANA a istanza singola quando si usa SAP HDBLCM per eseguire un'installazione di SAP NetWeaver 7.5 distribuita. I singoli passaggi sono descritti in dettaglio negli screenshot successivi di questa guida.

1. Creare una rete virtuale di Azure che includa le due macchine virtuali di test.
2. Distribuire due macchine virtuali di Azure con sistema operativo, in questo caso SLES e SLES for SAP Applications 12 SP1, in base al modello di Azure Resource Manager.
3. Collegare due dischi di archiviazione Standard o Premium di Azure alla macchina virtuale del server applicazioni, ad esempio dischi da 75 o 500 GB.
4. Collegare i dischi di archiviazione Premium alla macchina virtuale del server del database HANA. Per informazioni dettagliate, vedere la sezione "Configurazione dei dischi" più avanti in questa guida.
5. A seconda dei requisiti di dimensione o velocità effettiva, collegare più dischi e creare volumi con striping tramite uno strumento di gestione di volumi logici o di amministrazione di più dispositivi (MDADM) a livello di sistema operativo all'interno della macchina virtuale.
6. Creare file system XFS nei volumi logici o nei dischi collegati.
7. Montare i nuovi file system XFS a livello di sistema operativo. Usare un unico file system per tutto il software SAP e un altro file system ad esempio per la directory /sapmnt e i backup. Nel server di database SAP HANA montare i file system XFS sui dischi di archiviazione Premium come /hana e /usr/sap. Questo processo è necessario per impedire che venga occupato tutto lo spazio del file system radice, che ha dimensioni limitate nelle VM Linux in Azure.
8. Immettere gli indirizzi IP locali delle macchine virtuali di test nel file /etc/hosts.
9. Immettere il parametro **nofail** nel file /etc/fstab.
10. Impostare i parametri del kernel in base alla versione del sistema operativo Linux in uso. Per altre informazioni, vedere le note SAP appropriate che descrivono HANA e la sezione "Parametri del kernel" in questa guida.
11. Aggiungere spazio di swapping.
12. Facoltativamente, installare un desktop con interfaccia grafica nelle VM di test. In alternativa, usare un'installazione SAPinst remota.
13. Scaricare il software SAP da SAP Service Marketplace.
14. Creare un gruppo denominato sapsys con ID gruppo 1001 nella macchina virtuale del server di database HANA.
15. Installare SAP HANA nella macchina virtuale del server di database usando HDBLCM (HANA Database Lifecycle Manager).
16. Installare l'istanza di SAP ASCS nella VM del server app.
17. Condividere la directory /sapmnt tra le VM di test mediante NFS. La macchina virtuale del server applicazioni è il server NFS.
18. Installare l'istanza del database, incluso HANA, tramite SWPM nella VM del server di database HANA.
19. Installare il server dell'applicazione primaria, PAS, nella macchina virtuale del server applicazioni.
20. Avviare SAP MC. Connettersi tramite l'interfaccia utente grafica di SAP o HANA Studio.

## <a name="preparing-azure-vms-for-a-manual-installation-of-sap-hana"></a>Preparare le macchine virtuali di Azure per l'installazione manuale di SAP HANA
Questa sezione contiene gli argomenti seguenti:

* Aggiornamenti del sistema operativo
* Configurazione dei dischi
* Parametri del kernel
* File system
* File /etc/hosts
* File /etc/fstab

### <a name="os-updates"></a>Aggiornamenti del sistema operativo
Prima di installare software aggiuntivo, verificare se sono disponibili aggiornamenti e correzioni del sistema operativo Linux. L'installazione di una patch può evitare una chiamata al supporto tecnico.

Assicurarsi di usare:
* SUSE Linux Enterprise Server per applicazioni SAP.
* Red Hat Enterprise Linux per applicazioni SAP o Red Hat Enterprise Linux per SAP HANA. 

Se non è ancora stato fatto, registrare la distribuzione del sistema operativo nella sottoscrizione Linux ricevuta dal fornitore Linux. Si noti che in SUSE sono disponibili immagini del sistema operativo per le applicazioni SAP che includono già i servizi e vengono registrate automaticamente.

Di seguito è riportato un esempio di ricerca di eventuali patch disponibili per SUSE Linux tramite il comando **zypper**:

 `sudo zypper list-patches`

A seconda del tipo di problema, le patch sono suddivise per categoria e gravità. I valori usati comunemente per la categoria sono: **sicurezza**, **consigliato**, **facoltativo**, **funzionalità**, **documento** o **yast**.
I valori usati comunemente per la gravità sono: **critica**, **importante**, **moderata**, **bassa** o **non specificata**.

Il comando **zypper** ricerca solo gli aggiornamenti necessari ai pacchetti installati. È possibile ad esempio usare il comando seguente:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

È possibile aggiungere il parametro `--dry-run` per testare l'aggiornamento senza effettivamente aggiornare il sistema.


### <a name="disk-setup"></a>Configurazione dei dischi
Il file system radice di una macchina virtuale Linux in Azure ha un limite di dimensioni. È quindi necessario aggiungere altro spazio su disco a una macchina virtuale di Azure per l'esecuzione di SAP. Per le macchine virtuali di Azure del server applicazioni SAP potrebbe essere sufficiente l'uso di dischi di archiviazione Standard di Azure. Tuttavia, per le macchine virtuali di Azure SAP HANA DBMS l'uso di dischi di Archiviazione Premium di Azure per le implementazioni di produzione e non di produzione è obbligatorio.

In base ai [requisiti di archiviazione TDI di SAP HANA](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), viene suggerita la seguente configurazione di Archiviazione Premium di Azure: 

| SKU di VM | RAM |  /hana/data and /hana/log <br /> con striping con LVM o MDADM | /hana/shared | volume /root | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

Nella configurazione del disco suggerita il volume di dati HANA e il volume del registro vengono inseriti nello stesso set di dischi di archiviazione Premium di Azure con striping LVM o MDADM. Non è necessario definire alcun livello di ridondanza RAID poiché Archiviazione Premium di Azure mantiene tre immagini dei dischi per la ridondanza. Per assicurarsi di configurare uno spazio di archiviazione sufficiente, vedere [SAP HANA TDI Storage Requirements](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) (Requisiti di archiviazione TDI di SAP HANA) e [SAP HANA Server Installation and Update Guide](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm) (Guida all'installazione e all'aggiornamento del server SAP HANA). Considerare anche i diversi volumi della velocità effettiva del disco rigido virtuale dei diversi dischi di archiviazione Premium di Azure, come documentato in [Archiviazione Premium a prestazioni elevate e dischi gestiti per le VM](https://docs.microsoft.com/azure/storage/storage-premium-storage). 

È possibile aggiungere altri dischi di Archiviazione Premium alle macchine virtuali HANA DBMS per archiviare i backup di database o dei log delle transazioni.

Per altre informazioni sui due strumenti principali per la configurazione dello striping, vedere gli articoli seguenti:

* [Configurare RAID software in Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Configurare LVM in una macchina virtuale Linux in Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Per altre informazioni sul collegamento di dischi alle macchine virtuali di Azure che eseguono Linux come sistema operativo guest, vedere [Aggiungere un disco a una VM Linux](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Archiviazione Premium di Azure consente di definire le modalità di memorizzazione nella cache del disco. Per il set di stripe che contiene /hana/data e /hana/log, la memorizzazione nella cache del disco deve essere disabilitata. Per altri volumi (dischi), la modalità di memorizzazione nella cache deve essere impostata su **ReadOnly**.

Per altre informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../../windows/premium-storage.md).

Per trovare modelli JSON di esempio per la creazione di VM, consultare [Azure Quickstart Templates](https://github.com/Azure/azure-quickstart-templates) (Modelli di avvio rapido di Azure).
Il modello vm-simple-sles è un modello di base. Include una sezione di archiviazione, con un disco dati di 100 GB aggiuntivo. Questo modello può essere usato come base. È possibile adattare il modello a una configurazione specifica.

>[!Note]
>È importante collegare il disco di Archiviazione di Azure usando UUID, come descritto in [Esecuzione di SAP NetWeaver nelle VM SUSE Linux di Microsoft Azure](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Nell'ambiente di test sono stati collegati due dischi di archiviazione Standard di Azure alla VM del server app SAP, come illustrato nello screenshot seguente. Un disco è stato usato per archiviare tutto il software SAP (NetWeaver 7.5, l'interfaccia utente grafica SAP e SAP HANA) per l'installazione. Il secondo disco è stato usato per assicurarsi di avere a disposizione spazio sufficiente per altre esigenze, ad esempio backup e test dei dati, e per la condivisione della directory /sapmnt (ovvero i profili SAP) tra tutte le macchine virtuali appartenenti alla stessa infrastruttura SAP.

![Finestra dei dischi del server applicazioni SAP HANA con due dischi dati e le relative dimensioni](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Parametri del kernel
SAP HANA richiede impostazioni specifiche del kernel Linux che non fanno parte delle immagini della raccolta standard di Azure e devono essere definite manualmente. A seconda che venga usato Red Hat o SUSE, i parametri possono essere diversi. Le note SAP elencate in precedenza contengono informazioni sui parametri. Nelle schermate illustrate è stato usato SUSE Linux 12 SP1. 

SLES for SAP Applications 12 GA e SLES for SAP Applications 12 SP1 includono un nuovo strumento, **tuned-adm**, che sostituisce lo strumento precedente **sapconf**. Per **tuned-adm** è disponibile un profilo di SAP HANA specifico. Per ottimizzare il sistema per SAP HANA, immettere quanto segue come utente radice:

   `tuned-adm profile sap-hana`

Per altre informazioni su **tuned-adm**, vedere la [documentazione SUSE su tuned-adm](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Nello screenshot seguente è possibile osservare come **tuned-adm** ha modificato i valori `transparent_hugepage` e `numa_balancing` in base alle impostazioni obbligatorie di SAP HANA.

![Lo strumento tuned-adm modifica i valori in base alle impostazioni obbligatorie di SAP HANA](./media/hana-get-started/image005.jpg)

Per rendere permanenti le impostazioni del kernel di SAP HANA, usare **grub2** in SLES 12. Per altre informazioni su **grub2**, vedere la sezione [Configuration File Structure](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) (Struttura del file di configurazione) della documentazione di SUSE.

Lo screenshot seguente illustra come le impostazioni del kernel sono state modificate nel file di configurazione e quindi compilate tramite **grub2-mkconfig**:

![Impostazioni del kernel modificate nel file di configurazione e compilate tramite grub2-mkconfig](./media/hana-get-started/image006.jpg)

È anche possibile modificare le impostazioni usando YaST e le impostazioni **Boot Loader** > **Kernel Parameters** (Caricatore d'avvio > Parametri del kernel):

![Scheda delle impostazioni Kernel Parameters (Parametri del kernel) di YaST Boot Loader](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>File system
Lo screenshot seguente mostra due file system che sono stati creati nella VM del server app SAP oltre ai due dischi di archiviazione Standard di Azure collegati. Entrambi i file system sono di tipo XFS e sono montati in /sapdata e /sapsoftware.

Non è obbligatorio per definire la struttura dei file system in questo modo. Esistono altre opzioni per strutturare lo spazio su disco. La considerazione più importante riguarda la necessità di evitare che il file system radice esaurisca lo spazio disponibile.

![Due file system creati nella VM del server app SAP](./media/hana-get-started/image008.jpg)

Per quanto riguarda la macchina virtuale del database SAP HANA, durante l'installazione di un database, quando si usa SAPinst (SWPM) e l'opzione di installazione **tipica**, il percorso di installazione è /hana e /usr/sap. Il percorso predefinito del backup dei log di SAP HANA è /usr/sap. Anche in questo caso, è importante evitare che il file system radice esaurisca lo spazio di archiviazione ed è quindi necessario verificare che sia disponibile spazio sufficiente in /hana e /usr/sap prima di installare SAP HANA tramite SWPM.

Per una descrizione del layout del file system standard di SAP HANA, vedere [SAP HANA Server Installation and Update Guide](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm) (Guida all'aggiornamento e all'installazione del server SAP HANA).

![File system aggiuntivi creati nella macchina virtuale del server applicazioni SAP](./media/hana-get-started/image009.jpg)

Quando si installa SAP NetWeaver in un'immagine della raccolta standard di Azure per SLES/SLES for SAP Applications 12, viene visualizzato un messaggio per segnalare che non è disponibile spazio di swapping, come illustrato nello screenshot seguente. Per ignorare questo messaggio, è possibile aggiungere manualmente un file di scambio usando **dd**, **mkswap** e **swapon**. Per istruzioni, cercare "Adding a Swap File Manually" (Aggiunta manuale di un file di scambio) nella sezione [Using the YaST Partitioner](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) (Uso del Partitioner YaST) della documentazione di SUSE.

Un'altra opzione consiste nel configurare lo spazio di swapping tramite l'agente di macchine virtuali Linux. Per altre informazioni, vedere [Azure Linux Agent User Guide](../../linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Guida dell'utente dell'agente Linux di Azure).

![Messaggio popup che segnala la presenza di spazio di swapping insufficiente](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>File /etc/hosts
Prima di iniziare a installare SAP, assicurarsi di includere i nomi host e gli indirizzi IP delle macchine virtuali SAP nel file /etc/hosts. Distribuire tutte le macchine virtuali SAP all'interno di un'unica rete virtuale di Azure e quindi usare gli indirizzi IP interni, come illustrato di seguito:

![Nomi host e indirizzi IP delle VM SAP elencati nel file /etc/hosts](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>File /etc/fstab

È utile aggiungere il parametro **nofail** nel file fstab. In questo modo, se si verificano problemi con i dischi, la macchina virtuale non si blocca nel processo di avvio. Ricordare tuttavia che è possibile che non sia disponibile spazio su disco aggiuntivo e che i processi occupino interamente il file system radice. Se /hana non è presente, SAP HANA non viene avviato.

![Aggiungere il parametro nofail nel file fstab](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Desktop GNOME con interfaccia grafica in SLES 12/SLES for SAP Applications 12
Questa sezione contiene gli argomenti seguenti:

* Installazione del desktop GNOME e xrdp in SLES 12/SLES for SAP Applications 12
* Esecuzione della console SAP MC basata su Java con Firefox in SLES 12/SLES for SAP Applications 12

È anche possibile usare strumenti alternativi, ad esempio Xterminal o VNC (non descritti in questa guida).

### <a name="installing-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Installazione del desktop GNOME e xrdp in SLES 12/SLES for SAP Applications 12
Se si conosce l'ambiente Windows è possibile usare con facilità un desktop con interfaccia grafica direttamente nelle macchine virtuali Linux SAP per eseguire Firefox, SAPinst, SAP GUI, SAP MC o HANA Studio e connettersi alla macchina virtuale tramite Remote Desktop Protocol (RDP) da un computer Windows. A seconda dei criteri aziendali relativi all'aggiunta di interfacce utente grafiche in sistemi Linux di produzione e non produzione, è possibile installare GNOME nel server. Per installare il desktop GNOME in una macchina virtuale SLES 12/SLES for SAP Applications 12 di Azure:

1. Installare il desktop GNOME immettendo il comando seguente (ad esempio in una finestra PuTTY):

   `zypper in -t pattern gnome-basic`

2. Installare xrdp per consentire la connessione alla VM tramite RDP:

   `zypper in xrdp`

3. Modificare /etc/sysconfig/windowmanager e impostare la gestione finestre predefinita su GNOME:

   `DEFAULT_WM="gnome"`

4. Eseguire **chkconfig** per verificare che xrdp venga avviato automaticamente dopo un riavvio:

   `chkconfig -level 3 xrdp on`

5. Se si verificano problemi con la connessione RDP, provare a riavviare (ad esempio da una finestra PuTTY):

   `/etc/xrdp/xrdp.sh restart`

6. Se il riavvio di xrdp descritto nel passaggio precedente non funziona, cercare un file con estensione pid:

   `check /var/run` 

   Cercare `xrdp.pid`. Se viene trovato, rimuoverlo e provare a riavviare di nuovo.

### <a name="starting-sap-mc"></a>Avvio di SAP MC
Se, dopo aver installato il desktop GNOME, si avvia la console SAP MC con interfaccia grafica basata su Java da Firefox in esecuzione in una macchina virtuale SLES 12/SLES for SAP Applications 12 di Azure, è possibile che venga visualizzato un errore dovuto all'assenza del plug-in Java mancante nel browser.

L'URL per avviare SAP MC è `<server>:5<instance_number>13`.

Per altre informazioni, vedere [Starting the Web-Based SAP Management Console](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm) (Avvio della console di gestione SAP basata sul Web).

Lo screenshot seguente illustra il messaggio di errore visualizzato quando il plug-in Java non è presente nel browser:

![Messaggio di errore che indica la mancanza del plug-in Java nel browser](./media/hana-get-started/image013.jpg)

Un modo per risolvere il problema consiste nell'installare il plug-in mancante con YaST, come illustrato nello screenshot seguente:

![Uso di YaST per installare il plug-in mancante](./media/hana-get-started/image014.jpg)

Quando si immette nuovamente l'URL di SAP Management Console, viene visualizzato un messaggio in cui viene richiesto di attivare il plug-in:

![Finestra di dialogo con la richiesta di attivazione del plug-in](./media/hana-get-started/image015.jpg)

È anche possibile che venga visualizzato un altro messaggio di errore relativo a un file mancante, javafx.properties. Questo è correlato al requisito di Oracle Java 1.8 per SAP GUI 7.4. (Vedere la [Nota SAP 2059429](https://launchpad.support.sap.com/#/notes/2059424)). Il file javafx.properties richiesto non è incluso né nella versione IBM Java né nel pacchetto openjdk fornito con SLES/SLES for SAP Applications 12. Per risolvere il problema, scaricare e installare Java SE 8 da Oracle.

Per informazioni su un problema simile relativo a openjdk in openSUSE, vedere il thread di discussione [SAPGui 7.4 Java for openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306).

## <a name="manual-installation-of-sap-hana-swpm"></a>Installazione manuale di SAP HANA: SWPM
I vari screenshot inclusi in questa sezione mostrano i principali passaggi da eseguire per l'installazione di SAP NetWeaver 7.5 e SAP HANA SP12 tramite SWPM (SAPinst). Nell'ambito di un'installazione di NetWeaver 7.5, SWPM consente anche di installare il database HANA come istanza singola.

Nell'ambiente di test di esempio è stato installato un solo server applicazioni ABAP (Advanced Business Application Programming). Come illustrato nello screenshot seguente, è stata usata l'opzione **Distributed System** (Sistema distribuito) per installare l'istanza ASCS e l'istanza Primary Application Server in una macchina virtuale di Azure e SAP HANA come sistema di database in un'altra macchina virtuale di Azure.

![Istanza ASCS e istanza Primary Application Server installate tramite l'opzione Distributed System (Sistema distribuito)](./media/hana-get-started/image012.jpg)

Dopo aver installato l'istanza ASCS nella macchina virtuale del server applicazioni e averla visualizzata in verde in SAP Management Console, la directory /sapmnt, che include la directory dei profili SAP, deve essere condivisa con la macchina virtuale del server di database SAP HANA. La procedura di installazione del database deve accedere a queste informazioni. Il modo migliore per garantire l'accesso consiste nell'usare NFS, che può essere configurato con YaST.

![Console di gestione SAP che mostra l'istanza ASCS installata nella VM del server app e visualizzata in verde](./media/hana-get-started/image016.jpg)

Nella VM del server app, la directory /sapmnt deve essere condivisa tramite NFS usando le opzioni **rw** e **no_root_squash**. I valori predefiniti sono **ro** e **root_squash** che possono causare problemi quando si installa l'istanza del database.

![Condivisione della directory /sapmnt tramite NFS usando le opzioni rw e no_root_squash](./media/hana-get-started/image017b.jpg)

Come illustrato nello screenshot seguente, la condivisione di /sapmnt dalla macchina virtuale del server applicazioni deve essere configurata nella macchina virtuale del server di database SAP HANA tramite **NFS Client** (Client NFS) e YaST.

![Condivisione di /sapmnt configurata tramite NFS Client (Client NFS)](./media/hana-get-started/image018b.jpg)

Per eseguire un'installazione distribuita di NetWeaver 7.5, con **Database Instance** (Istanza database) come illustrato nello screenshot seguente, accedere alla macchina virtuale del server di database SAP HANA e avviare SWPM.

![Installazione di un'istanza del database tramite l'accesso alla VM del server di database SAP HANA e l'avvio di SWPM](./media/hana-get-started/image019.jpg)

Dopo aver selezionato un'installazione **tipica** e il percorso del supporto di installazione, immettere un SID di database, il nome host, il numero di istanza e la password dell'amministratore di sistema del database.

![Pagina di accesso dell'amministratore di sistema del database SAP HANA](./media/hana-get-started/image035b.jpg)

Immettere la password per lo schema DBACOCKPIT:

![Casella di immissione della password per lo schema DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Immettere una domanda per la password dello schema SAPABAP1:

![Immettere una domanda per la password dello schema SAPABAP1](./media/hana-get-started/image037b.jpg)

Al termine di ogni attività, viene visualizzato un segno di spunta verde accanto a ogni fase del processo di installazione del database. Viene visualizzato il messaggio "Execution of ... Database Instance has completed" (Esecuzione dell'istanza di database ... completata).

![Finestra in cui viene visualizzata l'attività completata con il messaggio di conferma](./media/hana-get-started/image023.jpg)

Al termine dell'installazione, SAP Management Console visualizza anche l'istanza di database in verde e l'elenco completo dei processi di SAP HANA (hdbindexserver, hdbcompileserver e così via).

![Finestra della console di gestione SAP con l'elenco dei processi di SAP HANA](./media/hana-get-started/image024.jpg)

Lo screenshot seguente mostra le parti della struttura di file nella directory /hana/shared creata da SWPM durante l'installazione di HANA. Poiché non è possibile specificare un percorso diverso, è importante montare spazio aggiuntivo su disco nella directory /hana prima di installare SAP HANA tramite SWPM per evitare che il file system radice esaurisca lo spazio disponibile.

![Struttura di file della directory /hana/shared creata durante l'installazione di HANA](./media/hana-get-started/image025.jpg)

Questo screenshot mostra la struttura di file della directory /usr/sap:

![Struttura di file della directory /usr/sap](./media/hana-get-started/image026.jpg)

L'ultimo passaggio dell'installazione distribuita di ABAP consiste nell'installare l'istanza del server applicazioni principale:

![Installazione di ABAP che mostra l'istanza del server applicazioni principale come passaggio finale](./media/hana-get-started/image027b.jpg)

Dopo aver installato l'istanza del server applicazioni principale e l'interfaccia utente grafica SAP, è necessario usare la transazione **DBA Cockpit** per confermare che l'installazione di SAP HANA è stata completata:

![Finestra DBA Cockpit (Pannello di controllo DBA) di conferma dell'installazione](./media/hana-get-started/image028b.jpg)

Come ultimo passaggio, è possibile installare innanzitutto SAP HANA Studio nella macchina virtuale del server applicazioni SAP e quindi connettersi all'istanza di SAP HANA in esecuzione nella macchina virtuale del server di database:

![Installazione di SAP HANA Studio nella VM del server app SAP](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>Installazione manuale di SAP HANA: HDBLCM
Oltre a installare SAP HANA come parte di un'installazione distribuita tramite SWPM, è possibile installare prima la versione autonoma di HANA usando HDBLCM (HANA Database Lifecycle Manager). Sarà quindi possibile installare ad esempio SAP NetWeaver 7.5. Gli screenshot di questa sezione illustrano questo processo.

Per altre informazioni sullo strumento HANA HDBLCM, vedere:

* [Choosing the Correct SAP HANA HDBLCM for Your Task (Scelta dello strumento HDBLCM di SAP HANA per la propria attività)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)
* [SAP HANA Lifecycle Management Tools (Strumenti di SAP HANA Lifecycle Management)](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)
* [SAP HANA Server Installation and Update Guide (Guida all'installazione e all'aggiornamento del server SAP HANA)](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)

Per evitare problemi che possono verificarsi con l'impostazione di un ID gruppo predefinito per `\<HANA SID\>adm user` (creato dallo strumento HDBLCM), definire un nuovo gruppo denominato `sapsys` con ID gruppo `1001` prima di installare SAP HANA usando HDBLCM:

![Nuovo gruppo "sapsys" definito con ID gruppo 1001](./media/hana-get-started/image030.jpg)

Al primo avvio di HDBLCM, viene visualizzato un menu di avvio semplice. Selezionare la voce 1, **Install new system** (Installa nuovo sistema), come illustrato nello screenshot seguente:

![Opzione "Install new system" (Installa nuovo sistema) nella finestra di avvio di HDBLCM](./media/hana-get-started/image031.jpg)

Nello screenshot riportato di seguito sono visualizzate tutte le opzioni chiave selezionate in precedenza.

> [!IMPORTANT]
> Le directory denominate per i volumi di dati e log di HANA, nonché il percorso di installazione (/hana/shared in questo esempio) e /usr/sap, non devono essere inclusi nel file system radice. Le directory appartengono ai dischi dati di Azure collegati alla macchina virtuale, come descritto nella sezione "Configurazione dei dischi". Questo approccio consente di evitare che il file system radice esaurisca lo spazio disponibile. Nello screenshot seguente è possibile osservare che l'amministratore di sistema HANA ha l'ID utente `1005` e fa parte del gruppo `sapsys` (ID `1001`) definito prima dell'installazione.

![Elenco di tutti i componenti chiave di SAP HANA selezionati in precedenza](./media/hana-get-started/image032.jpg)

È possibile controllare i dettagli di `\<HANA SID\>adm user` (`azdadm` nello screenshot seguente) nella directory /etc/passwd:

![Dettagli dell'utente \<HANA SID\>adm di HANA visualizzati nella directory /etc/passwd](./media/hana-get-started/image033.jpg)

Dopo aver installato SAP HANA tramite HDBLCM, è possibile visualizzare la struttura di file in SAP HANA Studio, come illustrato nello screenshot seguente. Lo schema SAPABAP1, che include tutte le tabelle di SAP NetWeaver, non è ancora disponibile.

![Struttura di file di SAP HANA in SAP HANA Studio](./media/hana-get-started/image034.jpg)

Dopo l'installazione di SAP HANA, è possibile installare SAP NetWeaver. Come illustrato nello screenshot seguente, l'installazione è stata eseguita in modo distribuito tramite SWPM, come descritto nella sezione precedente. Quando si installa l'istanza del database tramite SWPM, è necessario immettere gli stessi dati specificati in HDBLCM, ad esempio il nome host, il SID HANA e il numero di istanza. SWPM usa quindi l'installazione esistente di HANA aggiungendo altri schemi.

![Installazione distribuita eseguita tramite SWPM](./media/hana-get-started/image035b.jpg)

Lo screenshot seguente illustra il passaggio dell'installazione tramite SWPM in cui vengono immessi i dati relativi allo schema DBACOCKPIT:

![Passaggio dell'installazione tramite SWPM in cui vengono immessi i dati relativi allo schema DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Immettere i dati sullo schema SAPABAP1:

![Immissione dei dati sullo schema SAPABAP1](./media/hana-get-started/image037b.jpg)

Dopo aver terminato l'installazione dell'istanza del database con SWPM, lo schema SAPABAP1 è visibile in SAP HANA Studio:

![Schema SAPABAP1 in SAP HANA Studio](./media/hana-get-started/image038b.jpg)

Infine, dopo l'installazione del server applicazioni SAP e dell'interfaccia utente grafica SAP, è possibile verificare l'istanza del database HANA con la transazione **DBA Cockpit**:

![Istanza del database HANA verificata con la transazione DBA Cockpit](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Download del software SAP
È possibile scaricare il software da SAP Service Marketplace, come illustrato negli screenshot seguenti.

Download di NetWeaver 7.5 per Linux/HANA:

 ![Finestra di installazione e aggiornamento del servizio SAP per il download di NetWeaver 7.5](./media/hana-get-started/image001.jpg)

Download di HANA SP12 Platform Edition:

 ![Finestra di installazione e aggiornamento del servizio SAP per il download di HANA SP12 Platform Edition](./media/hana-get-started/image002.jpg)

