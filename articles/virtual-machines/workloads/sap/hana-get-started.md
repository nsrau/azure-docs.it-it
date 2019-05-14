---
title: 'Avvio rapido: Installazione manuale di SAP HANA a istanza singola nelle macchine virtuali di Azure | Microsoft Docs'
description: Guida introduttiva per l'installazione manuale di SAP HANA a istanza singola nelle macchine virtuali di Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: hermannd
ms.openlocfilehash: 5091932989849943f00cb71f72378dd17af23a4a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60204819"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-virtual-machines"></a>Avvio rapido: Installazione manuale di istanza singola SAP HANA in macchine virtuali di Azure
## <a name="introduction"></a>Introduzione
Questa Guida consente di configurare un'istanza singola SAP HANA in macchine virtuali di Azure quando si installano manualmente SAP NetWeaver 7.5 e SAP HANA 1.0 SP12. L'obiettivo di questa guida è su come distribuire SAP HANA in Azure. Non sostituisce la documentazione SAP. 

> [!NOTE]
> Questa guida descrive le distribuzioni di SAP HANA in macchine virtuali di Azure. Per informazioni su come distribuire SAP HANA in istanze large di HANA, vedere [usare SAP nelle macchine virtuali Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Prerequisiti
Questa guida si presuppone che si abbia familiarità con questo tipo infrastruttura distribuita come un fondamenti del servizio (IaaS) come:
 * Come distribuire macchine virtuali (VM) o reti virtuali tramite il portale di Azure o PowerShell.
 * Lo sviluppo multipiattaforma interfaccia della riga di comando, che include la possibilità di usare i modelli di JavaScript Object Notation (JSON).

Questa guida presuppone anche che si abbia familiarità con:
* SAP HANA e SAP NetWeaver e loro installazione locale.
* Come installare e usare SAP HANA e le istanze dell'applicazione SAP in Azure.
* I concetti e le procedure seguenti:
   * Pianificazione della distribuzione SAP in Azure, che include la pianificazione di rete virtuale di Azure e l'utilizzo di archiviazione di Azure. Visualizzare [SAP NetWeaver in macchine virtuali di Azure - Guida alla pianificazione e implementazione](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Principi di distribuzione e modalità di distribuzione delle macchine virtuali in Azure. Vedere [Distribuzione di macchine virtuali di Azure per SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Disponibilità elevata per SAP NetWeaver ABAP SAP Central Services (ASCS) /SAP Central Services (SCS) ed Enqueue Replication Server (ERS) in Azure. Vedere [Disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Informazioni dettagliate su come migliorare l'efficienza di un'installazione multi-SID di ASCS/SCS in Azure. Vedere [Creare una configurazione di SAP NetWeaver a più SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Principi di esecuzione di SAP NetWeaver basato su macchine virtuali Linux in Azure. Visualizzare [eseguire SAP NetWeaver in VM SUSE Linux di Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Questa guida fornisce le impostazioni specifiche per Linux in macchine virtuali di Azure. Vengono fornite inoltre informazioni su come collegare correttamente i dischi di archiviazione di Azure alle macchine virtuali Linux.

I tipi di macchine virtuali di Azure che possono essere usati per gli scenari di produzione sono elencati nella [documentazione SAP per IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Per gli scenari non di produzione, è disponibile un'ampia gamma di tipi di VM di Azure native.
Per altre informazioni sulla configurazione della macchina virtuale e sulle operazioni, vedere [operazioni in Azure e le configurazioni dell'infrastruttura di SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
Per la disponibilità elevata di SAP HANA, vedere [disponibilità elevata di SAP HANA per le macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).

Se si vuole ottenere un'istanza di SAP HANA o sistema S/4 Hana o BW/4 Hana distribuito rapidamente, è consigliabile usare [SAP Cloud Appliance Library](https://cal.sap.com). È possibile trovare documentazione su come distribuire un sistema S/4 Hana tramite SAP Cloud Appliance Library in Azure, ad esempio, nella [questa Guida](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Tutto quello che serve è una sottoscrizione di Azure e un utente SAP che può essere registrato con SAP Cloud Appliance Library.

## <a name="additional-resources"></a>Risorse aggiuntive
### <a name="sap-hana-backup"></a>Backup di SAP HANA
Per informazioni su come eseguire il backup dei database SAP HANA in macchine virtuali di Azure, vedere:
* [Guida del backup per SAP HANA in macchine virtuali Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide).
* [Backup di Azure SAP HANA a livello di file](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level).
* [Backup di SAP HANA basato su snapshot di archiviazione](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots).

### <a name="sap-cloud-appliance-library"></a>SAP Cloud Appliance Library
Per informazioni sull'uso di SAP Cloud Appliance Library per distribuire S/4 Hana o BW/4 Hana, vedere [distribuire SAP S/4 Hana o BW/4 Hana in Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>Sistemi operativi supportati di SAP HANA
Per informazioni sui sistemi operativi supportati di SAP HANA, vedere [SAP nota 2235581 - SAP HANA: Sistemi operativi supportati](https://launchpad.support.sap.com/#/notes/2235581/E). Le macchine virtuali di Azure supportano soltanto alcuni di questi sistemi operativi. I sistemi operativi seguenti sono supportati per la distribuzione di SAP HANA in Azure: 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

Per altra documentazione SAP su SAP HANA e sui diversi sistemi operativi Linux, vedere:

* [SAP Note 171356: Software SAP in Linux: Informazioni generali](https://launchpad.support.sap.com/#/notes/1984787).
* [SAP Note 1944799: Linee guida di SAP HANA per l'installazione del sistema operativo SLES](https://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html).
* [SAP Note 2205917: SAP HANA DB del sistema operativo le impostazioni consigliate per SLES 12 per applicazioni SAP](https://launchpad.support.sap.com/#/notes/2205917/E).
* [SAP Note 1391070: Soluzioni UUID Linux](https://launchpad.support.sap.com/#/notes/1391070).
* [SAP Note 2009879: Linee guida di SAP HANA per sistema operativo Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879).
* [SAP Note 2292690: SAP HANA DB: Sistema operativo le impostazioni consigliate per RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E).

### <a name="sap-monitoring-in-azure"></a>Monitoraggio SAP in Azure
Per informazioni su Monitoraggio SAP in Azure:

* [Nota 2191498 di SAP](https://launchpad.support.sap.com/#/notes/2191498/E) illustra SAP enhanced monitoring con le macchine virtuali Linux in Azure. 
* [SAP 1102124 nota](https://launchpad.support.sap.com/#/notes/1102124/E) include informazioni su SAPOSCOL in Linux. 
* [SAP 2178632 nota](https://launchpad.support.sap.com/#/notes/2178632/E) vengono illustrate le principali metriche di monitoraggio per SAP in Microsoft Azure.

### <a name="azure-vm-types"></a>Tipi di macchine virtuali di Azure
Tipi di VM di Azure e gli scenari di carico di lavoro supportati da SAP usati con SAP HANA sono documentati in [piattaforme IaaS Certificate SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). 

Tipi di macchine Virtuali Azure sono certificati da SAP per SAP NetWeaver o il livello di applicazione S/4 Hana sono documentati in [nota 1928533 di SAP: Applicazioni SAP in Azure: Prodotti e tipi di macchine Virtuali di Azure supportati](https://launchpad.support.sap.com/#/notes/1928533/E).

> [!NOTE]
> L'integrazione tra SAP, Linux e Azure è supportata solo nel modello di distribuzione Azure Resource Manager e non in quello classico. 

## <a name="manual-installation-of-sap-hana"></a>Installazione manuale di SAP HANA

> [!IMPORTANT]
> Assicurarsi che il sistema operativo scelto è certificate per SAP HANA sui tipi di macchina virtuale specifici usati per SAP. L'elenco di SAP HANA certified sistemi operativi e i tipi di VM rilascia per tali tipi di macchine Virtuali possono essere cercati nella [piattaforme IaaS certificate per SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Assicurarsi di selezionare i dettagli del tipo di VM elencati per ottenere l'elenco completo delle versioni del sistema operativo supportato da SAP HANA per il tipo di macchina virtuale specifico. Per un esempio in questo documento, abbiamo utilizzato una versione di SUSE Linux Enterprise Server (SLES) del sistema operativo che non è supportata da SAP per SAP HANA in macchine virtuali serie M.
>

La guida descrive due diverse modalità per installare manualmente SAP HANA nelle macchine virtuali di Azure:

* Usare SAP Software Provisioning Manager (SWPM) come parte di un'installazione di NetWeaver distribuita nel passaggio "istanza del database di installazione".
* Usare lo strumento di SAP HANA database lifecycle manager (HDBLCM) e quindi installare NetWeaver.

È anche possibile usare SWPM per installare tutti i componenti, ad esempio l'istanza ASCS, SAP HANA e il server applicazioni SAP in una singola macchina virtuale. I passaggi sono descritti in questo [annuncio del blog di SAP HANA](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Questa opzione non è descritta in questa Guida introduttiva, ma i problemi che è necessario prendere in considerazione sono gli stessi.

Prima di iniziare un'installazione, è consigliabile leggere il "preparare le VM di Azure per l'installazione manuale di SAP HANA" più avanti in questa Guida. In questo modo, è possibile evitare diversi errori di base che possono verificarsi quando si usa solo una configurazione predefinita della VM di Azure.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Passaggi principali per l'installazione di SAP HANA quando si usa SAP SWPM
Questa sezione elenca i passaggi chiave da seguire per un'installazione manuale di SAP HANA a istanza singola, quando si usa SAP SWPM per eseguire un'installazione di SAP NetWeaver 7.5 distribuita. I singoli passaggi sono descritti in dettaglio negli screenshot successivi di questa guida.

1. Creare una rete virtuale di Azure che includa le due macchine virtuali di test.
2. Distribuire due VM di Azure con i sistemi operativi in base al modello di Azure Resource Manager. Questo esempio Usa SUSE Linux Enterprise Server e SLES for SAP Applications 12 SP1. 
3. Collegare due dischi di archiviazione premium, ad esempio dischi da 75 o 500 GB, o di Azure standard alla VM del server applicazioni.
4. Collegare i dischi di archiviazione Premium alla macchina virtuale del server del database HANA. Per altre informazioni, vedere la sezione "Configurazione dei dischi" più avanti in questa Guida.
5. A seconda dei requisiti di dimensione o velocità effettiva, collegare più dischi. Quindi creare volumi con striping. Usare Gestione volumi logici (LVM) o uno strumento di amministrazione (mdadm) più dispositivi a livello di sistema operativo nella macchina virtuale.
6. Creare file system XFS nei volumi logici o nei dischi collegati.
7. Montare i nuovi file system XFS a livello di sistema operativo. Usare un unico file system per tutto il software SAP. Usare ad esempio un altro file system per la directory /sapmnt e i backup. Nel server di database SAP HANA montare i file system XFS sui dischi di archiviazione Premium come /hana e /usr/sap. Questo processo è fondamentale per impedire il file system radice esaurisca. Il file system radice ha dimensioni limitate nelle VM Linux in Azure. 
8. Immettere gli indirizzi IP locali delle macchine virtuali di test nel file /etc/hosts.
9. Immettere il parametro **nofail** nel file /etc/fstab.
10. Impostare i parametri del kernel Linux in base alla versione del sistema operativo Linux che è utilizzare. Per altre informazioni, vedere le note SAP che descrivono HANA e la sezione "Parametri del Kernel" in questa Guida.
11. Aggiungere spazio di swapping.
12. Facoltativamente, installare un desktop con interfaccia grafica nelle VM di test. In alternativa, usare un'installazione SAPinst remota.
13. Scaricare il software SAP da SAP Service Marketplace.
14. Installare l'istanza di SAP ASCS nella VM del server app.
15. Condividere la directory /sapmnt tra le VM di test mediante NFS. La macchina virtuale del server applicazioni è il server NFS.
16. Installare l'istanza del database incluso HANA tramite SWPM nella VM del server di database.
17. Installare il server dell'applicazione primaria, PAS, nella macchina virtuale del server applicazioni.
18. Avviare la console di gestione SAP (SAP MC). Connettersi ad esempio all'interfaccia utente grafica di SAP o HANA Studio.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Passaggi principali per l'installazione di SAP HANA quando si usa HDBLCM
Questa sezione elenca i passaggi principali per un'installazione manuale di SAP HANA a istanza singola quando si usa SAP HDBLCM per eseguire un'installazione di SAP NetWeaver 7.5 distribuita. I singoli passaggi sono descritti in dettaglio negli screenshot successivi di questa guida.

1. Creare una rete virtuale di Azure che includa le due macchine virtuali di test.
2. Distribuire due macchine virtuali di Azure con i sistemi operativi in base al modello di Azure Resource Manager. Questo esempio Usa SLES e SLES for SAP Applications 12 SP1.
3. Collegare due dischi di archiviazione premium, ad esempio dischi da 75 o 500 GB, o di Azure standard alla VM del server app.
4. Collegare i dischi di archiviazione Premium alla macchina virtuale del server del database HANA. Per altre informazioni, vedere la sezione "Configurazione dei dischi" più avanti in questa Guida.
5. A seconda dei requisiti di dimensione o velocità effettiva, collegare più dischi. Creare volumi con striping tramite Gestione volumi logici o uno strumento di mdadm a livello di sistema operativo nella macchina virtuale.
6. Creare file system XFS nei volumi logici o nei dischi collegati.
7. Montare i nuovi file system XFS a livello di sistema operativo. Usare un unico file system per tutto il software SAP. Usare ad esempio un altro file system per la directory /sapmnt e i backup. Nel server di database SAP HANA montare i file system XFS sui dischi di archiviazione Premium come /hana e /usr/sap. Questo processo è necessario per evitare che il file system radice esaurisca. Il file system radice ha dimensioni limitate nelle VM Linux in Azure.
8. Immettere gli indirizzi IP locali delle macchine virtuali di test nel file /etc/hosts.
9. Immettere il parametro **nofail** nel file /etc/fstab.
10. Impostare i parametri del kernel in base alla versione del sistema operativo Linux che è utilizzare. Per altre informazioni, vedere le note SAP che descrivono HANA e la sezione "Parametri del Kernel" in questa Guida.
11. Aggiungere spazio di swapping.
12. Facoltativamente, installare un desktop con interfaccia grafica nelle VM di test. In alternativa, usare un'installazione SAPinst remota.
13. Scaricare il software SAP da SAP Service Marketplace.
14. Creare un gruppo denominato sapsys con ID gruppo 1001 nella macchina virtuale del server di database HANA.
15. Installare SAP HANA in macchine Virtuali del server di database tramite gestione del ciclo di vita database HANA.
16. Installare l'istanza di SAP ASCS nella VM del server app.
17. Condividere la directory /sapmnt tra le VM di test mediante NFS. La macchina virtuale del server applicazioni è il server NFS.
18. Installare l'istanza del database incluso HANA tramite SWPM nella VM del server di database HANA.
19. Installare primary application server nella VM del server applicazioni.
20. Avviare SAP MC. Connettersi tramite l'interfaccia utente grafica di SAP o HANA Studio.

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>Preparare le VM di Azure per l'installazione manuale di SAP HANA
Questa sezione contiene gli argomenti seguenti:

* Aggiornamenti del sistema operativo
* Configurazione dei dischi
* Parametri del kernel
* File system
* File /etc/hosts
* File /etc/fstab

### <a name="os-updates"></a>Aggiornamenti del sistema operativo
Verifica degli aggiornamenti del sistema operativo Linux e le correzioni prima di installare software aggiuntivo. Tramite l'installazione di una patch, è possibile evitare una chiamata al supporto tecnico.

Assicurarsi che sia in uso:
* SUSE Linux Enterprise Server per applicazioni SAP.
* Red Hat Enterprise Linux per applicazioni SAP o Red Hat Enterprise Linux per SAP HANA. 

Se già stato fatto, registrare la distribuzione del sistema operativo con la sottoscrizione Linux ricevuta dal fornitore Linux. SUSE è disponibili immagini del sistema operativo per le applicazioni SAP che includono già i servizi e che sono state registrate automaticamente.

Ecco un esempio di come cercare le patch disponibili per SUSE Linux usando il **zypper** comando:

 `sudo zypper list-patches`

A seconda del tipo di problema, le patch sono suddivise per categoria e gravità. Valori usati comunemente per la categoria sono: 
- Security
- Consigliato
- Facoltativo
- Funzionalità
- Documento
- yast

Valori usati comunemente per la gravità sono:

- Critico
- Importante
- Moderata
- Basso
- Non specificata

Il comando **zypper** ricerca solo gli aggiornamenti necessari ai pacchetti installati. Ad esempio, è possibile utilizzare questo comando:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

È possibile aggiungere il parametro `--dry-run` per testare l'aggiornamento senza effettivamente aggiornare il sistema.


### <a name="disk-setup"></a>Configurazione dei dischi
Il file system radice di una macchina virtuale Linux in Azure ha un limite di dimensioni. Pertanto, è necessario aggiungere spazio su disco aggiuntivo a una VM di Azure per l'esecuzione di SAP. Per macchine virtuali di Azure del server applicazioni SAP, l'uso di dischi di archiviazione standard di Azure potrebbe essere sufficiente. Per VM di Azure per SAP HANA DBMS, l'uso di dischi di archiviazione premium di Azure per le implementazioni di produzione e non di produzione è obbligatorio.

In base il [requisiti di archiviazione di SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), viene suggerita la configurazione di archiviazione premium di Azure seguenti: 

| SKU di VM | RAM |  /hana/data and /hana/log <br /> con striping con LVM o mdadm | /hana/shared | volume /root | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

Nella configurazione del disco suggerita il volume di dati HANA e il volume di log vengono inseriti nello stesso set di dischi di archiviazione premium di Azure con striping con LVM o mdadm. Non è necessario definire alcun livello di ridondanza RAID poiché archiviazione premium di Azure mantiene tre immagini dei dischi per la ridondanza. 

Per assicurarsi di configurare sufficiente spazio di archiviazione, vedere [requisiti di archiviazione di SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) e [Guida all'installazione e aggiornamento server di SAP HANA](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm). Considerare anche i volumi di disco rigido virtuale (VHD) diverso velocità effettiva dei dischi di archiviazione premium di Azure diversi come documentato [archiviazione premium a prestazioni elevate e managed disks per le macchine virtuali](../../windows/disks-types.md). 

È possibile aggiungere ulteriori dischi di archiviazione premium alle macchine virtuali HANA DBMS per archiviare i backup del log database o delle transazioni.

Per altre informazioni sui due strumenti principali usati per configurare lo striping, vedere:

* [Configurare RAID software in Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Configurare LVM in una macchina virtuale Linux in Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Per altre informazioni su come collegare dischi per macchine virtuali di Azure che eseguono Linux come sistema operativo guest, vedere [aggiungere un disco a una VM Linux](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Con unità SSD premium di Azure, è possibile definire le modalità di caching del disco. Per il set di stripe che contiene /hana/data e /hana/log, disabilitare la cache del disco. Per altri volumi, vale a dire, dischi, impostare la modalità di memorizzazione nella cache **ReadOnly**.

Per trovare modelli JSON di esempio da usare per creare macchine virtuali, vedere la [modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates).
Il modello vm-simple-sles è un modello di base. Include una sezione di archiviazione, con un disco dati di 100 GB aggiuntivo. Usare questo modello come base. È possibile adattare il modello a una configurazione specifica.

> [!NOTE]
> È importante collegare il disco di archiviazione di Azure usando UUID, come documentato nel [esecuzione di SAP NetWeaver nelle VM SUSE Linux di Microsoft Azure](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Nell'ambiente di test, i dischi di archiviazione standard di Azure due vengono collegati al server app SAP macchina virtuale, come illustrato nello screenshot seguente. Un disco archivia tutto il software SAP, ad esempio NetWeaver 7.5 e SAP GUI, SAP HANA, per l'installazione. Il secondo disco assicura che lo spazio libero sufficiente è disponibile per i requisiti aggiuntivi. Ad esempio, i dati di backup e test e la directory /sapmnt, vale a dire, i profili SAP, necessario essere condivisi tra tutte le macchine virtuali che appartengono alla stessa infrastruttura SAP.

![Finestra dei dischi del server applicazioni SAP HANA con due dischi dati e le relative dimensioni](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Parametri del kernel
SAP HANA richiede impostazioni specifiche del kernel Linux. Queste impostazioni del kernel non fanno parte delle immagini della raccolta di Azure standard e devono essere impostate manualmente. A seconda che venga usato Red Hat o SUSE, i parametri possono essere diversi. Le note SAP elencate in precedenza forniscono informazioni su questi parametri. Nelle schermate illustrate è stato usato SUSE Linux 12 SP1. 

Per la disponibilità generale di SAP Applications 12 SLES e SLES for SAP Applications 12 SP1 hanno un nuovo strumento **tuned-adm**, che sostituisce la vecchia **sapconf** dello strumento. Per **tuned-adm** è disponibile un profilo di SAP HANA specifico. Per ottimizzare il sistema per SAP HANA, immettere il seguente profilo come utente radice:

   `tuned-adm profile sap-hana`

Per altre informazioni su **tuned-adm**, vedere la [documentazione SUSE su tuned-adm](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Nello screenshot seguente, è possibile visualizzare la modalità **tuned-adm** modificato il `transparent_hugepage` e `numa_balancing` valori, in base alle impostazioni obbligatorie di SAP HANA:

![Lo strumento tuned-adm modifica i valori in base alle impostazioni obbligatorie di SAP HANA](./media/hana-get-started/image005.jpg)

Per rendere permanenti le impostazioni del kernel di SAP HANA, usare **grub2** in SLES 12. Per altre informazioni sulle **grub2**, vedere la [struttura di file di configurazione](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) sezione della documentazione di SUSE.

Lo screenshot seguente illustra come le impostazioni del kernel sono state modificate nel file di configurazione e quindi compilate tramite **grub2-mkconfig**:

![Impostazioni del kernel modificate nel file di configurazione e compilate tramite grub2-mkconfig](./media/hana-get-started/image006.jpg)

È anche possibile modificare le impostazioni usando YaST e le impostazioni **Boot Loader** > **Kernel Parameters** (Caricatore d'avvio > Parametri del kernel):

![Scheda delle impostazioni Kernel Parameters (Parametri del kernel) di YaST Boot Loader](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>File system
Lo screenshot seguente mostra due file system che sono stati creati nella VM del server app SAP oltre ai due dischi di archiviazione Standard di Azure collegati. Entrambi i file System sono di tipo XFS e sono montati in /sapdata e /sapsoftware.

Non è obbligatorio per definire la struttura dei file System in questo modo. Sono disponibili altre opzioni per informazioni su come strutturare lo spazio su disco. La considerazione più importante riguarda la necessità di evitare che il file system radice esaurisca lo spazio disponibile.

![Due file system creati nella VM del server app SAP](./media/hana-get-started/image008.jpg)

Per la macchina virtuale del database di SAP HANA, durante l'installazione di un database, quando si usa SAPinst con SWPM e il **tipica** opzione di installazione, tutto ciò che viene installato in /hana e /usr/sap. Il percorso predefinito del backup dei log di SAP HANA è /usr/sap. Anche in questo caso, è importante evitare che il file system radice esaurisca lo spazio di archiviazione. Assicurarsi che vi sia spazio libero sufficiente in /hana e /usr/sap prima di installare SAP HANA tramite SWPM.

Per una descrizione del layout del file system standard di SAP HANA, vedere la [Guida all'installazione e aggiornamento server di SAP HANA](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).

![File system aggiuntivi creati nella macchina virtuale del server applicazioni SAP](./media/hana-get-started/image009.jpg)

Quando si installa SAP NetWeaver in un SLES/SLES standard per l'immagine della raccolta di Azure per SAP Applications 12, viene visualizzato un messaggio che afferma che non è disponibile spazio di swapping, come illustrato nello screenshot seguente. Per ignorare questo messaggio, è possibile aggiungere manualmente un file di scambio usando **dd**, **mkswap** e **swapon**. Per ulteriori informazioni, cercare "Aggiunta di un file di scambio manualmente" nel [tramite il partitioner YaST](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) sezione della documentazione di SUSE.

Un'altra opzione consiste nel configurare lo spazio di swapping tramite l'agente di macchine virtuali Linux. Per altre informazioni, vedere [Guida dell'utente dell'agente Linux di Azure](../../extensions/agent-linux.md).

![Messaggio popup che informa che vi sia spazio di swapping insufficiente](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>File /etc/hosts
Prima di iniziare a installare SAP, assicurarsi di includere i nomi host e gli indirizzi IP delle macchine virtuali SAP nel file /etc/hosts. Distribuire tutte le macchine virtuali SAP all'interno di una rete virtuale di Azure. Usare quindi gli indirizzi IP interni, come illustrato di seguito:

![Nomi host e indirizzi IP delle VM SAP elencati nel file /etc/hosts](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>File /etc/fstab

È utile aggiungere il **nofail** parametro nel file fstab. In questo modo, se si verificano problemi con i dischi, la macchina virtuale non bloccarsi nel processo di avvio. Ricordare tuttavia che è possibile che non sia disponibile spazio su disco aggiuntivo e che i processi occupino interamente il file system radice. Se /hana non è presente, SAP HANA non viene avviato.

![Aggiungere il parametro nofail nel file fstab](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Desktop GNOME con interfaccia grafica in SLES 12/SLES for SAP Applications 12
Questa sezione illustra come eseguire queste operazioni:

* Installare il desktop GNOME e xrdp in SLES 12/SLES for SAP Applications 12.
* Eseguire console SAP MC basata su Java con Firefox in SLES 12/SLES for SAP Applications 12.

È anche possibile usare alternative come Xterminal o VNC, che non sono descritte in questa Guida.

### <a name="install-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Installare il desktop GNOME e xrdp in SLES 12/SLES for SAP Applications 12
Se si dispone di uno sfondo di Windows, è possibile usare con facilità un desktop con interfaccia grafico direttamente nelle macchine virtuali Linux SAP per eseguire Firefox, SAPinst, SAP GUI, SAP MC o HANA Studio. È quindi possibile connettersi alla macchina virtuale tramite Remote Desktop Protocol (RDP) da un computer Windows. A seconda dei criteri aziendali relativi aggiunta GUI per i sistemi di produzione e non di produzione basati su Linux, è possibile installare GNOME nel server. Seguire questi passaggi per installare il desktop GNOME in un Azure SLES 12/SLES for SAP Applications 12 della macchina virtuale.

1. Installare il desktop GNOME immettendo il comando seguente, ad esempio, in una finestra PuTTY:

   `zypper in -t pattern gnome-basic`

2. Installare xrdp per consentire la connessione alla VM tramite RDP:

   `zypper in xrdp`

3. Modificare /etc/sysconfig/windowmanager e impostare la gestione finestre predefinita su GNOME:

   `DEFAULT_WM="gnome"`

4. Eseguire **chkconfig** per verificare che xrdp venga avviato automaticamente dopo un riavvio:

   `chkconfig -level 3 xrdp on`

5. Se si dispone di un problema con la connessione RDP, provare a riavviare, ad esempio, da una finestra PuTTY:

   `/etc/xrdp/xrdp.sh restart`

6. Se il riavvio di xrdp descritto nel passaggio precedente non funziona, cercare un file con estensione pid:

   `check /var/run` 

   Cercare `xrdp.pid`. Se viene trovato, rimuoverlo e provare a riavviare di nuovo.

### <a name="start-sap-mc"></a>Avviare SAP MC
Dopo aver installato il desktop GNOME, avviare SAP MC con interfaccia grafica di basate su Java da Firefox. Se viene eseguito in un Azure SLES 12/SLES per SAP Applications 12 VM, potrebbe essere visualizzato un errore. L'errore si verifica a causa di Java mancante nel browser del plug-in.

L'URL per avviare SAP MC è `<server>:5<instance_number>13`.

Per altre informazioni, vedere [avviare la Console di gestione SAP basata sul web](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

Lo screenshot seguente mostra il messaggio di errore visualizzato quando manca il plug-in Java-browser:

![Messaggio di errore che indica la mancanza del plug-in Java nel browser](./media/hana-get-started/image013.jpg)

Un modo per risolvere il problema consiste nell'installare il plug-in mancante con YaST, come illustrato nello screenshot seguente:

![Uso di YaST per installare il plug-in mancante](./media/hana-get-started/image014.jpg)

Quando si immettono di nuovo l'URL di SAP Management Console, viene richiesto di attivare il plug-in:

![Finestra di dialogo con la richiesta di attivazione del plug-in](./media/hana-get-started/image015.jpg)

È anche possibile che venga visualizzato un altro messaggio di errore relativo a un file mancante, javafx.properties. In relazione al requisito di Oracle Java 1.8 per SAP GUI 7.4. Per altre informazioni, vedere [nota SAP 2059429](https://launchpad.support.sap.com/#/notes/2059424).
Nella versione IBM Java e nel pacchetto openjdk fornito con SLES/SLES for SAP Applications 12 non includere il file javafx. Per risolvere il problema, scaricare e installare Java SE 8 da Oracle.

Per informazioni su un problema simile relativo a openjdk in openSUSE, vedere il thread di discussione [SAPGui 7.4 Java for openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306).

## <a name="manual-installation-of-sap-hana-swpm"></a>Installazione manuale di SAP HANA: SWPM
La serie di schermate in questa sezione vengono illustrati i passaggi chiave per l'installazione di SAP NetWeaver 7.5 e SAP HANA SP12 quando si usa SWPM con SAPinst. Come parte di un'installazione di NetWeaver 7.5, SWPM anche possibile installare il database HANA come istanza singola.

In un ambiente di test di esempio, viene installato un server di app Advanced Business Application Programming (ABAP). Come illustrato nello screenshot seguente, viene usata la **Distributed System** possibilità di installare l'istanza ASCS e istanza primary application server in una macchina virtuale di Azure. Abbiamo usato SAP HANA come sistema di database in un'altra macchina virtuale di Azure.

![Istanza ASCS e istanza Primary Application Server installate tramite l'opzione Distributed System (Sistema distribuito)](./media/hana-get-started/image012.jpg)

Dopo aver installato l'istanza ASCS nella VM del server app, viene identificata da un'icona verde in SAP Management Console. La directory /sapmnt, che include la directory dei profili SAP, deve essere condivisa con la VM del server di database SAP HANA. La procedura di installazione del database deve accedere a queste informazioni. Il modo migliore per garantire l'accesso consiste nell'usare NFS, che può essere configurato con YaST.

![Che mostra l'istanza ASCS installata nella VM con un'icona verde del server app SAP Management Console](./media/hana-get-started/image016.jpg)

Nella VM del server app, la directory /sapmnt è condivisa tramite NFS usando le **rw** e **no_root_squash** opzioni. I valori predefiniti sono **ro** e **root_squash** che possono causare problemi quando si installa l'istanza del database.

![Condivisione della directory /sapmnt tramite NFS usando le opzioni rw e no_root_squash](./media/hana-get-started/image017b.jpg)

Come illustrato nella schermata successiva, la condivisione di /sapmnt dalla VM del server app deve essere configurata nella VM del server di database SAP HANA usando **NFS Client** YaST e:

![La condivisione di /sapmnt configurata tramite NFS client](./media/hana-get-started/image018b.jpg)

Per eseguire un'installazione NetWeaver 7.5 distribuita, vale a dire, un **istanza di Database**, accedere alla VM del server di database SAP HANA e avviare SWPM:

![Installazione di un'istanza del database tramite l'accesso alla VM del server di database SAP HANA e l'avvio di SWPM](./media/hana-get-started/image019.jpg)

Dopo aver selezionato **tipica** installazione e il percorso del supporto di installazione, immettere un SID di database, il nome host, il numero di istanza e la password di amministratore di sistema di database:

![Pagina di accesso dell'amministratore di sistema del database SAP HANA](./media/hana-get-started/image035b.jpg)

Immettere la password per lo schema DBACOCKPIT:

![Casella di immissione della password per lo schema DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Immettere una domanda per la password dello schema SAPABAP1:

![Immettere una domanda per la password dello schema SAPABAP1](./media/hana-get-started/image037b.jpg)

Al termine di ogni attività, viene visualizzato un segno di spunta verde accanto a ogni fase del processo di installazione del database. Viene visualizzato il messaggio "Execution of ... Database Instance has completed" (Esecuzione dell'istanza di database ... completata).

![Finestra in cui viene visualizzata l'attività completata con il messaggio di conferma](./media/hana-get-started/image023.jpg)

Al termine dell'installazione, SAP Management Console visualizza anche l'istanza di database con un'icona verde. Visualizza l'elenco completo dei processi di SAP HANA, ad esempio hdbindexserver e hdbcompileserver.

![Finestra della console di gestione SAP con l'elenco dei processi di SAP HANA](./media/hana-get-started/image024.jpg)

Lo screenshot seguente mostra le parti della struttura di file nella directory /hana/shared creata da SWPM durante l'installazione di HANA. Poiché non è presente alcuna opzione per specificare un percorso diverso, è importante montare spazio aggiuntivo su disco nella directory /hana prima dell'installazione di SAP HANA tramite SWPM. Questo passaggio si evita il file system radice esaurisca lo spazio disponibile.

![Struttura di file della directory /hana/shared creata durante l'installazione di HANA](./media/hana-get-started/image025.jpg)

Questo screenshot mostra la struttura di file della directory /usr/sap:

![Struttura di file della directory /usr/sap](./media/hana-get-started/image026.jpg)

L'ultimo passaggio dell'installazione distribuita di ABAP consiste nell'installare l'istanza del server applicazioni principale:

![Installazione di ABAP che mostra l'istanza del server applicazioni principale come passaggio finale](./media/hana-get-started/image027b.jpg)

Dopo aver installato l'istanza del server applicazioni principale e SAP GUI, usare il **DBA Cockpit** delle transazioni per confermare che l'installazione di SAP HANA è stata completata correttamente:

![Finestra DBA Cockpit (Pannello di controllo DBA) di conferma dell'installazione](./media/hana-get-started/image028b.jpg)

Come passaggio finale, è possibile installare prima HANA Studio nella VM del server di app SAP. Quindi connettersi all'istanza di SAP HANA in esecuzione in VM del server di database.

![Installazione di SAP HANA Studio nella VM del server app SAP](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>Installazione manuale di SAP HANA: HDBLCM
Oltre a installare SAP HANA come parte di un'installazione distribuita tramite SWPM, è possibile installare prima la versione autonoma di HANA usando HDBLCM (HANA Database Lifecycle Manager). Sarà quindi possibile installare ad esempio SAP NetWeaver 7.5. Gli screenshot di questa sezione illustrano questo processo.

Per altre informazioni sullo strumento HANA HDBLCM, vedere:

* [Scegliere di SAP HANA HDBLCM corretto per l'attività di](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm).
* [Gli strumenti di gestione del ciclo di vita di SAP HANA](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm).
* [Guida all'installazione e aggiornamento server di SAP HANA](https://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf).

Per evitare problemi con un'impostazione di ID gruppo predefinito per il `\<HANA SID\>adm user`, che viene creato dallo strumento HDBLCM. Prima di installare SAP HANA tramite HDBLCM, definire un nuovo gruppo denominato `sapsys` usando l'ID del gruppo `1001`:

![Nuovo gruppo "sapsys" definito con ID gruppo 1001](./media/hana-get-started/image030.jpg)

Quando si avvia HDBLCM per la prima volta, un semplice iniziare dal menu Visualizza. Selezionare la voce 1, **installa nuovo sistema**:

![Opzione "Install new system" (Installa nuovo sistema) nella finestra di avvio di HDBLCM](./media/hana-get-started/image031.jpg)

Nello screenshot riportato di seguito sono visualizzate tutte le opzioni chiave selezionate in precedenza.

> [!IMPORTANT]
> Le directory denominate per il log HANA e i volumi di dati e il percorso di installazione, ovvero /hana/shared in questo esempio e /usr/sap non devono far parte del file system radice. Le directory appartengono ai dischi dati di Azure che sono stati collegati alla macchina virtuale. Per altre informazioni, vedere la sezione "Configurazione dei dischi". 

Questo approccio consente di evitare che il file system radice esaurisca lo spazio disponibile. Si noti che l'amministratore di sistema HANA ha l'ID utente `1005` e fa parte il `sapsys` gruppo, con ID `1001`, che è stato definito prima dell'installazione.

![Elenco di tutti i componenti chiave di SAP HANA selezionati in precedenza](./media/hana-get-started/image032.jpg)

Controllare il `\<HANA SID\>adm user` dettagli nella directory /etc/passwd/e così via. Cercare `azdadm`, come illustrato nello screenshot seguente:

![Dettagli dell'utente \<HANA SID\>adm di HANA visualizzati nella directory /etc/passwd](./media/hana-get-started/image033.jpg)

Dopo aver installato SAP HANA tramite HDBLCM, è possibile visualizzare la struttura di file in SAP HANA Studio, come illustrato nello screenshot seguente. Lo schema SAPABAP1, che include tutte le tabelle di SAP NetWeaver, non è ancora disponibile.

![Struttura di file di SAP HANA in SAP HANA Studio](./media/hana-get-started/image034.jpg)

Dopo l'installazione di SAP HANA, è possibile installare SAP NetWeaver. Come illustrato nello screenshot seguente, l'installazione è stata eseguita come un'installazione distribuita tramite SWPM. Questo processo è descritto nella sezione precedente. Quando si installa l'istanza del database tramite SWPM, è immettere gli stessi dati specificati in hdblcm. Ad esempio, immettere il nome host, SID HANA e numero di istanza. SWPM usa quindi l'installazione esistente di HANA aggiungendo altri schemi.

![Installazione distribuita eseguita tramite SWPM](./media/hana-get-started/image035b.jpg)

Lo screenshot seguente illustra il passaggio dell'installazione tramite SWPM in cui vengono immessi i dati relativi allo schema DBACOCKPIT:

![Passaggio dell'installazione tramite SWPM in cui vengono immessi i dati relativi allo schema DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Immettere i dati sullo schema SAPABAP1:

![Immissione dei dati sullo schema SAPABAP1](./media/hana-get-started/image037b.jpg)

Al termine dell'installazione istanza database con SWPM, è possibile visualizzare lo schema SAPABAP1 in SAP HANA Studio:

![Schema SAPABAP1 in SAP HANA Studio](./media/hana-get-started/image038b.jpg)

Infine, dopo che il server applicazioni SAP e le installazioni di SAP GUI al termine, verificare l'istanza del database HANA tramite il **DBA Cockpit** transazione:

![Istanza del database HANA verificata con la transazione DBA Cockpit](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Download del software SAP
È possibile scaricare il software da SAP Service Marketplace, come illustrato negli screenshot seguenti.

Download di NetWeaver 7.5 per Linux/HANA:

 ![SAP installazione e aggiornamento finestra del servizio per il download di NetWeaver 7.5](./media/hana-get-started/image001.jpg)

Download di HANA SP12 Platform Edition:

 ![SAP installazione e aggiornamento finestra del servizio per il download di HANA SP12 Platform Edition](./media/hana-get-started/image002.jpg)

