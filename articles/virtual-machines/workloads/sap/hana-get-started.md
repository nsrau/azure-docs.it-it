---
title: 'Avvio rapido: Installazione manuale di SAP HANA a istanza singola nelle macchine virtuali di Azure | Microsoft Docs'
description: Guida introduttiva per l'installazione manuale di SAP HANA a istanza singola nelle macchine virtuali di Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: hermannd
ms.openlocfilehash: 8d4e7b7056f4d5e53785366818fad05e24cfc605
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100061"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-virtual-machines"></a>Avvio rapido: Installazione manuale di SAP HANA a istanza singola in macchine virtuali di Azure
## <a name="introduction"></a>Introduzione
Questa guida consente di configurare una SAP HANA a istanza singola in macchine virtuali di Azure quando si installano manualmente SAP NetWeaver 7,5 e SAP HANA 1,0 SP12. Questa guida è incentrata su come distribuire SAP HANA in Azure. Non sostituisce la documentazione SAP. 

> [!NOTE]
> Questa guida descrive le distribuzioni di SAP HANA in macchine virtuali di Azure. Per informazioni su come distribuire SAP HANA in istanze large di HANA, vedere [usare SAP in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Prerequisiti
Questa guida presuppone che l'utente abbia familiarità con le nozioni di base dell'infrastruttura distribuita come servizio (IaaS) come:
 * Come distribuire macchine virtuali (VM) o reti virtuali tramite il portale di Azure o PowerShell.
 * Interfaccia della riga di comando multipiattaforma di Azure, che include l'opzione per l'uso di modelli di JavaScript Object Notation (JSON).

Questa guida presuppone anche che l'utente abbia familiarità con:
* SAP HANA e SAP NetWeaver e loro installazione locale.
* Come installare e usare SAP HANA e le istanze dell'applicazione SAP in Azure.
* I concetti e le procedure seguenti:
   * Pianificazione per la distribuzione di SAP in Azure, che include la pianificazione della rete virtuale di Azure e l'utilizzo di archiviazione di Azure. Vedere [SAP NetWeaver in macchine virtuali di Azure: Guida alla pianificazione e all'implementazione](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Principi di distribuzione e modalità di distribuzione delle macchine virtuali in Azure. Vedere [Distribuzione di macchine virtuali di Azure per SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Disponibilità elevata per SAP NetWeaver ABAP SAP Central Services (ASC), SAP Central Services (SCS) e Accodamento replica server (ERS) in Azure. Vedere [Disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Informazioni dettagliate su come migliorare l'efficienza in un'installazione a più SID di ASC/SCS in Azure. Vedere [Creare una configurazione di SAP NetWeaver a più SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Principi di esecuzione di SAP NetWeaver basato su macchine virtuali Linux in Azure. Vedere [eseguire SAP NetWeaver in Microsoft Azure macchine virtuali SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Questa guida fornisce impostazioni specifiche per Linux in macchine virtuali di Azure. Fornisce anche informazioni su come alleghi correttamente i dischi di archiviazione di Azure alle macchine virtuali Linux.

I tipi di macchine virtuali di Azure che possono essere usati per gli scenari di produzione sono elencati nella [documentazione SAP per IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Per gli scenari non di produzione, è disponibile un'ampia gamma di tipi di VM nativi di Azure.
Per altre informazioni sulla configurazione e sulle operazioni della macchina virtuale, vedere [SAP Hana le configurazioni dell'infrastruttura e le operazioni in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
Per SAP HANA disponibilità elevata, vedere [SAP Hana disponibilità elevata per macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).

Se si vuole ottenere un'istanza SAP HANA o un sistema S/4HANA o BW/4HANA distribuito rapidamente, provare a usare [SAP Cloud Appliance Library](https://cal.sap.com). È possibile trovare la documentazione relativa alla distribuzione di un sistema S/4HANA tramite SAP Cloud Appliance Library in Azure, ad esempio, in [questa guida](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). È sufficiente una sottoscrizione di Azure e un utente SAP che può essere registrato con SAP Cloud Appliance Library.

## <a name="additional-resources"></a>Risorse aggiuntive
### <a name="sap-hana-backup"></a>Backup di SAP HANA
Per informazioni su come eseguire il backup di SAP HANA database in macchine virtuali di Azure, vedere:
* [Guida al backup per SAP Hana in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide).
* [SAP Hana backup di Azure a livello di file](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level).
* [SAP Hana backup basato su snapshot di archiviazione](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots).

### <a name="sap-cloud-appliance-library"></a>SAP Cloud Appliance Library
Per informazioni su come usare SAP Cloud Appliance Library per distribuire S/4HANA o BW/4HANA, vedere [distribuire SAP S/4HANA o BW/4HANA in Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>Sistemi operativi supportati di SAP HANA
Per informazioni sui sistemi operativi supportati da SAP Hana, vedere [la nota SAP 2235581-SAP Hana: Sistemi](https://launchpad.support.sap.com/#/notes/2235581/E)operativi supportati. Le macchine virtuali di Azure supportano soltanto alcuni di questi sistemi operativi. I sistemi operativi seguenti sono supportati per la distribuzione di SAP HANA in Azure: 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

Per altra documentazione SAP su SAP HANA e sui diversi sistemi operativi Linux, vedere:

* [Nota SAP 171356: Software SAP in Linux: Informazioni](https://launchpad.support.sap.com/#/notes/1984787)generali.
* [Nota SAP 1944799: Linee guida SAP HANA per l'installazione](https://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)del sistema operativo SLES.
* [Nota SAP 2205917: Impostazioni del sistema operativo consigliate SAP HANA database per SLES 12](https://launchpad.support.sap.com/#/notes/2205917/E)for SAP Applications.
* [Nota SAP 1391070: Soluzioni](https://launchpad.support.sap.com/#/notes/1391070)UUID Linux.
* [SAP Note 2009879: Linee guida SAP HANA per il sistema](https://launchpad.support.sap.com/#/notes/2009879)operativo Red Hat Enterprise Linux (RHEL).
* [Nota SAP 2292690: DATABASE SAP HANA: Impostazioni del sistema operativo consigliate](https://launchpad.support.sap.com/#/notes/2292690/E)per RHEL 7.

### <a name="sap-monitoring-in-azure"></a>Monitoraggio SAP in Azure
Per informazioni sul monitoraggio SAP in Azure:

* [Nota sap 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) illustra il monitoraggio avanzato di SAP con macchine virtuali Linux in Azure. 
* La [Nota SAP 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) illustra le informazioni su SAPOSCOL in Linux. 
* La [Nota sap 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) illustra le metriche di monitoraggio principali per sap in Microsoft Azure.

### <a name="azure-vm-types"></a>Tipi di macchine virtuali di Azure
I tipi di VM di Azure e gli scenari di carico di lavoro supportati da SAP usati con SAP HANA sono documentati nelle [piattaforme IaaS certificate SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). 

I tipi di VM di Azure certificati da SAP per SAP NetWeaver o il livello dell'applicazione S/4HANA sono [descritti nella nota SAP 1928533: Applicazioni SAP in Azure: Prodotti supportati e tipi](https://launchpad.support.sap.com/#/notes/1928533/E)di VM di Azure.

> [!NOTE]
> L'integrazione tra SAP, Linux e Azure è supportata solo nel modello di distribuzione Azure Resource Manager e non in quello classico. 

## <a name="manual-installation-of-sap-hana"></a>Installazione manuale di SAP HANA

> [!IMPORTANT]
> Verificare che il sistema operativo selezionato sia SAP Certified per SAP HANA sui tipi di VM specifici usati. L'elenco dei tipi di VM SAP HANA certificati e le versioni del sistema operativo per questi tipi di VM possono essere cercati in [SAP Hana piattaforme IaaS certificate](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Assicurarsi di fare clic sui dettagli del tipo di macchina virtuale elencato per ottenere l'elenco completo delle versioni del sistema operativo supportate da SAP HANA per il tipo di macchina virtuale specifico. Per l'esempio in questo documento è stata usata una versione del sistema operativo SUSE Linux Enterprise Server (SLES) che non è supportata da SAP per SAP HANA sulle macchine virtuali della serie M.
>

La guida descrive due diverse modalità per installare manualmente SAP HANA nelle macchine virtuali di Azure:

* Usare SAP software Provisioning Manager (SWPM) come parte di un'installazione di NetWeaver distribuita nel passaggio "installa istanza database".
* Utilizzare lo strumento SAP HANA database Lifecycle Manager (HDBLCM), quindi installare NetWeaver.

È anche possibile usare SWPM per installare tutti i componenti, ad esempio SAP HANA, il server applicazioni SAP e l'istanza di ASC, in un'unica macchina virtuale. I passaggi sono descritti in questo [annuncio del blog SAP Hana](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Questa opzione non è descritta in questa Guida introduttiva, ma i problemi che è necessario prendere in considerazione sono gli stessi.

Prima di iniziare un'installazione, è consigliabile leggere la sezione "preparare le VM di Azure per l'installazione manuale di SAP HANA" più avanti in questa guida. In questo modo, è possibile evitare diversi errori di base che possono verificarsi quando si usa solo una configurazione predefinita della VM di Azure.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Passaggi principali per l'installazione di SAP HANA quando si usa SAP SWPM
Questa sezione elenca i passaggi chiave da seguire per un'installazione manuale di SAP HANA a istanza singola, quando si usa SAP SWPM per eseguire un'installazione di SAP NetWeaver 7.5 distribuita. I singoli passaggi sono descritti in dettaglio negli screenshot successivi di questa guida.

1. Creare una rete virtuale di Azure che includa le due macchine virtuali di test.
2. Distribuire le due macchine virtuali di Azure con i sistemi operativi in base al modello di Azure Resource Manager. Questo esempio usa SUSE Linux Enterprise Server e SLES for SAP Applications 12 SP1. 
3. Alleghi due dischi di archiviazione standard o Premium di Azure, ad esempio dischi da 75 GB o 500 GB, alla VM del server applicazioni.
4. Collegare i dischi di archiviazione Premium alla macchina virtuale del server del database HANA. Per ulteriori informazioni, vedere la sezione "configurazione dei dischi" più avanti in questa guida.
5. A seconda dei requisiti di dimensione o velocità effettiva, alleghi più dischi. Quindi creare i volumi con striping. Usare la gestione dei volumi logici (LVM) o uno strumento di amministrazione di più dispositivi (mdadm) a livello di sistema operativo all'interno della macchina virtuale.
6. Creare file system XFS nei volumi logici o nei dischi collegati.
7. Montare i nuovi file system XFS a livello di sistema operativo. Usare un unico file system per tutto il software SAP. Usare ad esempio un altro file system per la directory /sapmnt e i backup. Nel server di database SAP HANA montare i file system XFS sui dischi di archiviazione Premium come /hana e /usr/sap. Questo processo è necessario per impedire che il file system radice si riempia. Il file system radice non è di grandi dimensioni nelle macchine virtuali Linux di Azure. 
8. Immettere gli indirizzi IP locali delle macchine virtuali di test nel file /etc/hosts.
9. Immettere il parametro **nofail** nel file /etc/fstab.
10. Impostare i parametri del kernel Linux in base alla versione del sistema operativo Linux usata. Per ulteriori informazioni, vedere le note SAP che illustrano HANA e la sezione "parametri del kernel" in questa guida.
11. Aggiungere spazio di swapping.
12. Facoltativamente, installare un desktop con interfaccia grafica nelle VM di test. In alternativa, usare un'installazione SAPinst remota.
13. Scaricare il software SAP da SAP Service Marketplace.
14. Installare l'istanza di SAP ASCS nella VM del server app.
15. Condividere la directory /sapmnt tra le VM di test mediante NFS. La macchina virtuale del server applicazioni è il server NFS.
16. Installare l'istanza del database, che include HANA, usando SWPM nella VM del server di database.
17. Installare il server dell'applicazione primaria, PAS, nella macchina virtuale del server applicazioni.
18. Avviare la console di gestione SAP (SAP MC). Connettersi ad esempio all'interfaccia utente grafica di SAP o HANA Studio.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Passaggi principali per l'installazione di SAP HANA quando si usa HDBLCM
Questa sezione elenca i passaggi principali per un'installazione manuale di SAP HANA a istanza singola quando si usa SAP HDBLCM per eseguire un'installazione di SAP NetWeaver 7.5 distribuita. I singoli passaggi sono descritti in dettaglio negli screenshot successivi di questa guida.

1. Creare una rete virtuale di Azure che includa le due macchine virtuali di test.
2. Distribuire due macchine virtuali di Azure con sistemi operativi in base al modello di Azure Resource Manager. Questo esempio USA SLES e SLES for SAP Applications 12 SP1.
3. Aggiungere due dischi di archiviazione standard o Premium di Azure, ad esempio dischi da 75 GB o 500 GB, alla VM del server app.
4. Collegare i dischi di archiviazione Premium alla macchina virtuale del server del database HANA. Per ulteriori informazioni, vedere la sezione "configurazione dei dischi" più avanti in questa guida.
5. A seconda dei requisiti di dimensione o velocità effettiva, alleghi più dischi. Creare volumi con striping usando la gestione dei volumi logici o uno strumento mdadm a livello di sistema operativo all'interno della macchina virtuale.
6. Creare file system XFS nei volumi logici o nei dischi collegati.
7. Montare i nuovi file system XFS a livello di sistema operativo. Usare un unico file system per tutto il software SAP. Usare ad esempio un altro file system per la directory /sapmnt e i backup. Nel server di database SAP HANA montare i file system XFS sui dischi di archiviazione Premium come /hana e /usr/sap. Questo processo è necessario per impedire che il file system radice si riempia. Il file system radice non è di grandi dimensioni nelle macchine virtuali Linux di Azure.
8. Immettere gli indirizzi IP locali delle macchine virtuali di test nel file /etc/hosts.
9. Immettere il parametro **nofail** nel file /etc/fstab.
10. Impostare i parametri del kernel in base alla versione del sistema operativo Linux usata. Per ulteriori informazioni, vedere le note SAP che illustrano HANA e la sezione "parametri del kernel" in questa guida.
11. Aggiungere spazio di swapping.
12. Facoltativamente, installare un desktop con interfaccia grafica nelle VM di test. In alternativa, usare un'installazione SAPinst remota.
13. Scaricare il software SAP da SAP Service Marketplace.
14. Creare un gruppo denominato sapsys con ID gruppo 1001 nella macchina virtuale del server di database HANA.
15. Installare SAP HANA nella macchina virtuale del server di database usando HANA database Lifecycle Manager.
16. Installare l'istanza di SAP ASCS nella VM del server app.
17. Condividere la directory /sapmnt tra le VM di test mediante NFS. La macchina virtuale del server applicazioni è il server NFS.
18. Installare l'istanza del database, che include HANA, usando SWPM nella VM del server di database HANA.
19. Installare il server applicazioni primario nella macchina virtuale del server applicazioni.
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
Verificare la disponibilità di aggiornamenti e correzioni del sistema operativo Linux prima di installare software aggiuntivo. Installando una patch, è possibile evitare una chiamata al supporto tecnico.

Assicurarsi di usare:
* SUSE Linux Enterprise Server per applicazioni SAP.
* Red Hat Enterprise Linux per applicazioni SAP o Red Hat Enterprise Linux per SAP HANA. 

Se non è già stato fatto, registrare la distribuzione del sistema operativo con la sottoscrizione Linux dal fornitore di Linux. SUSE include immagini del sistema operativo per le applicazioni SAP che includono già servizi e che vengono registrate automaticamente.

Di seguito è riportato un esempio di come verificare la disponibilità di patch per SUSE Linux usando il comando **zypper** :

 `sudo zypper list-patches`

A seconda del tipo di problema, le patch sono suddivise per categoria e gravità. I valori di uso comune per Category sono: 
- Security
- Consigliato
- Facoltativo
- Funzionalità
- Documento
- YaST

I valori usati comunemente per la gravità sono:

- Critico
- Importante
- Moderata
- Basso
- Non specificata

Il comando **zypper** ricerca solo gli aggiornamenti necessari ai pacchetti installati. Ad esempio, è possibile usare questo comando:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

È possibile aggiungere il parametro `--dry-run` per testare l'aggiornamento senza effettivamente aggiornare il sistema.


### <a name="disk-setup"></a>Configurazione dei dischi
Il file system radice di una macchina virtuale Linux in Azure ha un limite di dimensioni. È quindi necessario alleghi spazio su disco aggiuntivo a una macchina virtuale di Azure per l'esecuzione di SAP. Per le macchine virtuali di Azure del server applicazioni SAP, l'uso dei dischi di archiviazione standard di Azure potrebbe essere sufficiente. Per SAP HANA macchine virtuali di Azure DBMS, l'uso di dischi di archiviazione Premium di Azure per le implementazioni di produzione e non di produzione è obbligatorio.

In base ai [requisiti di archiviazione di SAP Hana TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), viene suggerita la seguente configurazione di archiviazione Premium di Azure: 

| SKU di VM | RAM |  /hana/data and /hana/log <br /> con striping con LVM o mdadm | /hana/shared | volume /root | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

Nella configurazione del disco suggerita il volume di dati HANA e il volume di log vengono posizionati nello stesso set di dischi di archiviazione Premium di Azure con striping LVM o mdadm. Non è necessario definire alcun livello di ridondanza RAID perché archiviazione Premium di Azure mantiene tre immagini dei dischi per la ridondanza. 

Per assicurarsi di configurare spazio di archiviazione sufficiente, vedere [SAP Hana i requisiti di archiviazione TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) e la [Guida all'installazione e all'aggiornamento del server SAP Hana](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm). Considerare anche i diversi volumi di velocità effettiva del disco rigido virtuale (VHD) dei diversi dischi di archiviazione Premium di Azure, come documentato in [archiviazione Premium a prestazioni elevate e dischi gestiti per le macchine virtuali](../../windows/disks-types.md). 

Per archiviare i backup del database o del log delle transazioni, è possibile aggiungere altri dischi di archiviazione Premium alle VM DBMS HANA.

Per ulteriori informazioni sui due strumenti principali utilizzati per la configurazione dello striping, vedere:

* [Configurare RAID software in Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Configurare LVM in una macchina virtuale Linux in Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Per altre informazioni su come aggiungere dischi alle macchine virtuali di Azure che eseguono Linux come sistema operativo guest, vedere [aggiungere un disco a una VM Linux](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Con le unità SSD Premium di Azure è possibile definire le modalità di memorizzazione nella cache del disco. Per il set con striping che include/Hana/data e/Hana/log, disabilitare la memorizzazione nella cache del disco. Per gli altri volumi, ovvero i dischi, impostare la modalità di memorizzazione nella cache su sola **lettura**.

Per trovare modelli JSON di esempio da usare per creare macchine virtuali, vedere i [modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates).
Il modello vm-simple-sles è un modello di base. Include una sezione di archiviazione, con un disco dati di 100 GB aggiuntivo. Usare questo modello come base. È possibile adattare il modello a una configurazione specifica.

> [!NOTE]
> È importante aggiungere il disco di archiviazione di Azure usando un UUID come descritto in [eseguire SAP NetWeaver in Microsoft Azure VM SUSE Linux](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Nell'ambiente di test, due dischi di archiviazione standard di Azure sono collegati alla VM del server app SAP, come illustrato nello screenshot seguente. Un disco archivia tutto il software SAP, ad esempio NetWeaver 7,5, SAP GUI e SAP HANA per l'installazione. Il secondo disco garantisce che sia disponibile sufficiente spazio libero per requisiti aggiuntivi. Ad esempio, i dati di backup e di test e la directory/sapmnt, ovvero i profili SAP, devono essere condivisi tra tutte le macchine virtuali appartenenti allo stesso panorama applicativo SAP.

![Finestra dei dischi del server applicazioni SAP HANA con due dischi dati e le relative dimensioni](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Parametri del kernel
SAP HANA richiede impostazioni specifiche del kernel Linux. Queste impostazioni del kernel non fanno parte delle immagini della raccolta standard di Azure e devono essere impostate manualmente. A seconda che venga usato Red Hat o SUSE, i parametri possono essere diversi. Le note SAP elencate in precedenza forniscono informazioni su tali parametri. Nelle schermate illustrate è stato usato SUSE Linux 12 SP1. 

SLES for SAP Applications 12 disponibilità generale e SLES for SAP Applications 12 SP1 hanno un nuovo strumento, **Tuned-adm**, che sostituisce lo strumento **sapconf** precedente. Per **tuned-adm** è disponibile un profilo di SAP HANA specifico. Per ottimizzare il sistema per SAP HANA, immettere il profilo seguente come utente root:

   `tuned-adm profile sap-hana`

Per altre informazioni su **tuned-adm**, vedere la [documentazione SUSE su tuned-adm](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Nello screenshot seguente è possibile vedere come **Tuned-adm** ha modificato i `transparent_hugepage` valori e `numa_balancing` , in base alle impostazioni di SAP Hana richieste:

![Lo strumento tuned-adm modifica i valori in base alle impostazioni obbligatorie di SAP HANA](./media/hana-get-started/image005.jpg)

Per rendere permanenti le impostazioni del kernel di SAP HANA, usare **grub2** in SLES 12. Per ulteriori informazioni su **GRUB2**, vedere la sezione relativa alla [struttura dei file di configurazione](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) della documentazione di SUSE.

Lo screenshot seguente illustra come le impostazioni del kernel sono state modificate nel file di configurazione e quindi compilate tramite **grub2-mkconfig**:

![Impostazioni del kernel modificate nel file di configurazione e compilate tramite grub2-mkconfig](./media/hana-get-started/image006.jpg)

È anche possibile modificare le impostazioni usando YaST e le impostazioni **Boot Loader** > **Kernel Parameters** (Caricatore d'avvio > Parametri del kernel):

![Scheda delle impostazioni Kernel Parameters (Parametri del kernel) di YaST Boot Loader](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>File system
Lo screenshot seguente mostra due file system che sono stati creati nella VM del server app SAP oltre ai due dischi di archiviazione Standard di Azure collegati. Entrambi i file System sono di tipo XFS e sono montati in/sapdata e/sapsoftware.

Non è obbligatorio strutturare i file System in questo modo. Sono disponibili altre opzioni per la struttura dello spazio su disco. La considerazione più importante riguarda la necessità di evitare che il file system radice esaurisca lo spazio disponibile.

![Due file system creati nella VM del server app SAP](./media/hana-get-started/image008.jpg)

Per la macchina virtuale SAP HANA DB, durante un'installazione del database, quando si usa SAPinst con SWPM e l'opzione di installazione **tipica** , tutto viene installato in/Hana e/usr/SAP. Il percorso predefinito del backup dei log di SAP HANA è /usr/sap. Anche in questo caso è importante impedire che lo spazio di archiviazione venga esaurito dall'file system radice. Verificare che sia disponibile spazio sufficiente in/Hana e/usr/SAP prima di installare SAP HANA usando SWPM.

Per una descrizione del layout del file System standard di SAP HANA, vedere la [Guida all'installazione e all'aggiornamento del server SAP Hana](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).

![File system aggiuntivi creati nella macchina virtuale del server applicazioni SAP](./media/hana-get-started/image009.jpg)

Quando si installa SAP NetWeaver in un'immagine della raccolta standard di Azure SLES/SLES for SAP Applications 12, viene visualizzato un messaggio che indica che non è disponibile spazio di swapping, come illustrato nello screenshot seguente. Per ignorare questo messaggio, è possibile aggiungere manualmente un file di scambio usando **dd**, **mkswap** e **swapon**. Per informazioni su come, cercare "aggiunta manuale di un file di scambio" nella sezione [uso del partitioner YaST](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) della documentazione di SUSE.

Un'altra opzione consiste nel configurare lo spazio di swapping tramite l'agente di macchine virtuali Linux. Per altre informazioni, vedere [Guida dell'utente dell'agente Linux di Azure](../../extensions/agent-linux.md).

![Messaggio popup che informa che non è disponibile spazio di swapping sufficiente](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>File /etc/hosts
Prima di iniziare a installare SAP, assicurarsi di includere i nomi host e gli indirizzi IP delle macchine virtuali SAP nel file /etc/hosts. Distribuire tutte le macchine virtuali SAP all'interno di una rete virtuale di Azure. Usare quindi gli indirizzi IP interni, come illustrato di seguito:

![Nomi host e indirizzi IP delle VM SAP elencati nel file /etc/hosts](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>File /etc/fstab

È utile aggiungere il parametro **nofail** al file fstab. In questo modo, se si verificano problemi con i dischi, la macchina virtuale non smette di rispondere nel processo di avvio. Ricordare tuttavia che è possibile che non sia disponibile spazio su disco aggiuntivo e che i processi occupino interamente il file system radice. Se /hana non è presente, SAP HANA non viene avviato.

![Aggiungere il parametro nofail nel file fstab](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Desktop GNOME con interfaccia grafica in SLES 12/SLES for SAP Applications 12
Questa sezione illustra come eseguire queste operazioni:

* Installare il desktop GNOME e xrdp in SLES 12/SLES for SAP Applications 12.
* Eseguire SAP MC basato su Java con Firefox in SLES 12/SLES for SAP Applications 12.

È anche possibile usare alternative come xterminal o VNC, che non sono descritte in questa guida.

### <a name="install-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Installare il desktop GNOME e xrdp in SLES 12/SLES for SAP Applications 12
Se si dispone di uno sfondo Windows, è possibile usare facilmente un desktop con interfaccia grafica direttamente nelle macchine virtuali Linux SAP per eseguire Firefox, SAPinst, SAP GUI, SAP MC o HANA Studio. È quindi possibile connettersi alla macchina virtuale tramite il Remote Desktop Protocol (RDP) da un computer Windows. A seconda delle politiche aziendali relative all'aggiunta di GUI a sistemi di produzione e non di produzione basati su Linux, potrebbe essere necessario installare GNOME sul server. Seguire questa procedura per installare il desktop GNOME in una VM SLES 12/SLES for SAP Applications 12 di Azure.

1. Installare il desktop GNOME immettendo il comando seguente, ad esempio, in una finestra PuTTy:

   `zypper in -t pattern gnome-basic`

2. Installare xrdp per consentire la connessione alla VM tramite RDP:

   `zypper in xrdp`

3. Modificare /etc/sysconfig/windowmanager e impostare la gestione finestre predefinita su GNOME:

   `DEFAULT_WM="gnome"`

4. Eseguire **chkconfig** per verificare che xrdp venga avviato automaticamente dopo un riavvio:

   `chkconfig -level 3 xrdp on`

5. Se si riscontra un problema con la connessione RDP, provare a riavviare, ad esempio, da una finestra PuTTy:

   `/etc/xrdp/xrdp.sh restart`

6. Se il riavvio di xrdp descritto nel passaggio precedente non funziona, cercare un file con estensione pid:

   `check /var/run` 

   Cercare `xrdp.pid`. Se viene trovato, rimuoverlo e provare a riavviare di nuovo.

### <a name="start-sap-mc"></a>Avviare SAP MC
Dopo aver installato il desktop GNOME, avviare l'interfaccia grafica SAP MC basata su Java da Firefox. Se viene eseguito in una VM SLES 12/SLES for SAP Applications 12 di Azure, potrebbe essere visualizzato un errore. L'errore si verifica a causa del plug-in del browser Java mancante.

L'URL per avviare SAP MC è `<server>:5<instance_number>13`.

Per ulteriori informazioni, vedere [avvio della console di gestione SAP basata sul Web](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

Lo screenshot seguente mostra il messaggio di errore visualizzato quando manca il plug-in Java-browser:

![Messaggio di errore che indica la mancanza del plug-in Java nel browser](./media/hana-get-started/image013.jpg)

Un modo per risolvere il problema consiste nell'installare il plug-in mancante con YaST, come illustrato nello screenshot seguente:

![Uso di YaST per installare il plug-in mancante](./media/hana-get-started/image014.jpg)

Quando si immette nuovamente l'URL della console di gestione di SAP, viene richiesto di attivare il plug-in:

![Finestra di dialogo con la richiesta di attivazione del plug-in](./media/hana-get-started/image015.jpg)

È anche possibile che venga visualizzato un altro messaggio di errore relativo a un file mancante, javafx.properties. Si riferisce al requisito di Oracle Java 1,8 per SAP GUI 7,4. Per altre informazioni, vedere la [Nota SAP 2059429](https://launchpad.support.sap.com/#/notes/2059424).
La versione IBM Java e il pacchetto OpenJDK fornito con SLES/SLES for SAP Applications 12 non includono il file JavaFX. Properties necessario. Per risolvere il problema, scaricare e installare Java SE 8 da Oracle.

Per informazioni su un problema simile relativo a openjdk in openSUSE, vedere il thread di discussione [SAPGui 7.4 Java for openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306).

## <a name="manual-installation-of-sap-hana-swpm"></a>Installazione manuale di SAP HANA: SWPM
La serie di schermate in questa sezione illustra i passaggi principali per l'installazione di SAP NetWeaver 7,5 e SAP HANA SP12 quando si usa SWPM con SAPinst. Come parte di un'installazione di NetWeaver 7,5, SWPM può anche installare il database HANA come istanza singola.

In un ambiente di test di esempio è stato installato un server app Advanced Business Application Programming (ABAP). Come illustrato nello screenshot seguente, è stata usata l'opzione di **sistema distribuito** per installare le istanze di ASC e del server applicazioni primarie in una VM di Azure. È stato usato SAP HANA come sistema di database in un'altra macchina virtuale di Azure.

![Istanza ASCS e istanza Primary Application Server installate tramite l'opzione Distributed System (Sistema distribuito)](./media/hana-get-started/image012.jpg)

Dopo l'installazione dell'istanza di ASC nella macchina virtuale del server app, questa viene identificata da un'icona verde nella console di gestione di SAP. La directory/sapmnt, che include la directory del profilo SAP, deve essere condivisa con la macchina virtuale del server di SAP HANA database. La procedura di installazione del database deve accedere a queste informazioni. Il modo migliore per garantire l'accesso consiste nell'usare NFS, che può essere configurato con YaST.

![Console di gestione SAP che mostra l'istanza di ASC installata nella macchina virtuale del server app usando un'icona verde](./media/hana-get-started/image016.jpg)

Nella VM del server app, la directory/sapmnt è condivisa tramite NFS usando le opzioni **RW** e **no_root_squash** . I valori predefiniti sono **ro** e **root_squash** che possono causare problemi quando si installa l'istanza del database.

![Condivisione della directory /sapmnt tramite NFS usando le opzioni rw e no_root_squash](./media/hana-get-started/image017b.jpg)

Come illustrato nella schermata successiva, la condivisione/sapmnt dalla macchina virtuale del server app deve essere configurata nella macchina virtuale del server di SAP HANA database usando **NFS client** e YaST:

![Condivisione/sapmnt configurata tramite il client NFS](./media/hana-get-started/image018b.jpg)

Per eseguire un'installazione distribuita di NetWeaver 7,5, ovvero un' **istanza del database**, accedere alla macchina virtuale del server di SAP Hana DB e avviare SWPM:

![Installazione di un'istanza del database tramite l'accesso alla VM del server di database SAP HANA e l'avvio di SWPM](./media/hana-get-started/image019.jpg)

Dopo aver selezionato l'installazione **tipica** e il percorso del supporto di installazione, immettere un SID di database, il nome host, il numero di istanza e la password dell'amministratore di sistema del database:

![Pagina di accesso dell'amministratore di sistema del database SAP HANA](./media/hana-get-started/image035b.jpg)

Immettere la password per lo schema DBACOCKPIT:

![Casella di immissione della password per lo schema DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Immettere una domanda per la password dello schema SAPABAP1:

![Immettere una domanda per la password dello schema SAPABAP1](./media/hana-get-started/image037b.jpg)

Al termine di ogni attività, viene visualizzato un segno di spunta verde accanto a ogni fase del processo di installazione del database. Viene visualizzato il messaggio "Execution of ... Database Instance has completed" (Esecuzione dell'istanza di database ... completata).

![Finestra in cui viene visualizzata l'attività completata con il messaggio di conferma](./media/hana-get-started/image023.jpg)

Al termine dell'installazione, anche la console di gestione SAP Mostra l'istanza di database con un'icona verde. Viene visualizzato l'elenco completo dei processi di SAP HANA, ad esempio hdbindexserver e hdbcompileserver.

![Finestra della console di gestione SAP con l'elenco dei processi di SAP HANA](./media/hana-get-started/image024.jpg)

Lo screenshot seguente mostra le parti della struttura di file nella directory /hana/shared creata da SWPM durante l'installazione di HANA. Poiché non è disponibile un'opzione per specificare un percorso diverso, è importante montare ulteriore spazio su disco nella directory/Hana prima dell'installazione SAP HANA usando SWPM. Questo passaggio impedisce l'esaurimento dello spazio disponibile nel file system radice.

![Struttura di file della directory /hana/shared creata durante l'installazione di HANA](./media/hana-get-started/image025.jpg)

Questo screenshot mostra la struttura di file della directory /usr/sap:

![Struttura di file della directory /usr/sap](./media/hana-get-started/image026.jpg)

L'ultimo passaggio dell'installazione distribuita di ABAP consiste nell'installare l'istanza del server applicazioni principale:

![Installazione di ABAP che mostra l'istanza del server applicazioni principale come passaggio finale](./media/hana-get-started/image027b.jpg)

Dopo l'installazione dell'istanza del server applicazioni principale e dell'interfaccia utente grafica SAP, utilizzare la transazione del **pozzetto DBA** per verificare che l'installazione del SAP Hana sia stata completata correttamente:

![Finestra DBA Cockpit (Pannello di controllo DBA) di conferma dell'installazione](./media/hana-get-started/image028b.jpg)

Come passaggio finale, potrebbe essere necessario installare HANA studio nella macchina virtuale del server applicazioni SAP. Connettersi quindi al SAP HANA istanza in esecuzione nella macchina virtuale del server di database.

![Installazione di SAP HANA Studio nella VM del server app SAP](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>Installazione manuale di SAP HANA: HDBLCM
Oltre a installare SAP HANA come parte di un'installazione distribuita tramite SWPM, è possibile installare prima la versione autonoma di HANA usando HDBLCM (HANA Database Lifecycle Manager). Sarà quindi possibile installare ad esempio SAP NetWeaver 7.5. Gli screenshot di questa sezione illustrano questo processo.

Per altre informazioni sullo strumento HANA HDBLCM, vedere:

* [Scegliere il SAP Hana corretto HDBLCM per l'attività](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm).
* [Strumenti di gestione del ciclo](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm)di vita SAP Hana.
* [Guida all'installazione e all'aggiornamento del server SAP Hana](https://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf).

Si desidera evitare problemi con un'impostazione predefinita per l' `\<HANA SID\>adm user`ID gruppo per, che viene creato dallo strumento HDBLCM. Prima di installare SAP HANA tramite HDBLCM, definire un nuovo gruppo denominato `sapsys` usando l'ID `1001`gruppo:

![Nuovo gruppo "sapsys" definito con ID gruppo 1001](./media/hana-get-started/image030.jpg)

Quando si avvia HDBLCM per la prima volta, viene visualizzato un menu di avvio semplice. Selezionare l'elemento 1, **Install New System**:

![Opzione "Install new system" (Installa nuovo sistema) nella finestra di avvio di HDBLCM](./media/hana-get-started/image031.jpg)

Nello screenshot riportato di seguito sono visualizzate tutte le opzioni chiave selezionate in precedenza.

> [!IMPORTANT]
> Directory denominate per i volumi di dati e di log di HANA e il percorso di installazione,/Hana/Shared in questo esempio, e/usr/SAP non devono far parte del file system radice. Queste directory appartengono ai dischi dati di Azure collegati alla macchina virtuale. Per ulteriori informazioni, vedere la sezione "configurazione dei dischi". 

Questo approccio consente di evitare che il file system radice esaurisca lo spazio disponibile. Si noti che l'amministratore di sistema Hana dispone `1005` di un `sapsys` ID utente ed è parte del gruppo `1001`, con ID, che è stato definito prima dell'installazione.

![Elenco di tutti i componenti chiave di SAP HANA selezionati in precedenza](./media/hana-get-started/image032.jpg)

Controllare i `\<HANA SID\>adm user` dettagli nella directory/etc/passwd `azdadm`Cercare, come illustrato nello screenshot seguente:

![Dettagli dell'utente \<HANA SID\>adm di HANA visualizzati nella directory /etc/passwd](./media/hana-get-started/image033.jpg)

Dopo aver installato SAP HANA tramite HDBLCM, è possibile visualizzare la struttura di file in SAP HANA Studio, come illustrato nello screenshot seguente. Lo schema SAPABAP1, che include tutte le tabelle di SAP NetWeaver, non è ancora disponibile.

![Struttura di file di SAP HANA in SAP HANA Studio](./media/hana-get-started/image034.jpg)

Dopo l'installazione di SAP HANA, è possibile installare SAP NetWeaver. Come illustrato nello screenshot seguente, l'installazione è stata eseguita come installazione distribuita usando SWPM. Questo processo è descritto nella sezione precedente. Quando si installa l'istanza del database tramite SWPM, è necessario immettere gli stessi dati utilizzando HDBLCM. Ad esempio, immettere il nome host, il SID HANA e il numero di istanza. SWPM usa quindi l'installazione esistente di HANA aggiungendo altri schemi.

![Installazione distribuita eseguita tramite SWPM](./media/hana-get-started/image035b.jpg)

Lo screenshot seguente illustra il passaggio dell'installazione tramite SWPM in cui vengono immessi i dati relativi allo schema DBACOCKPIT:

![Passaggio dell'installazione tramite SWPM in cui vengono immessi i dati relativi allo schema DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Immettere i dati sullo schema SAPABAP1:

![Immissione dei dati sullo schema SAPABAP1](./media/hana-get-started/image037b.jpg)

Al termine dell'installazione dell'istanza di database SWPM, è possibile visualizzare lo schema SAPABAP1 in SAP HANA Studio:

![Schema SAPABAP1 in SAP HANA Studio](./media/hana-get-started/image038b.jpg)

Infine, dopo aver completato le installazioni del server applicazioni SAP e dell'interfaccia utente grafica SAP, verificare l'istanza del database HANA usando la transazione **DBA Cockpit** :

![Istanza del database HANA verificata con la transazione DBA Cockpit](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Download del software SAP
È possibile scaricare il software da SAP Service Marketplace, come illustrato negli screenshot seguenti.

Download di NetWeaver 7.5 per Linux/HANA:

 ![Finestra di installazione e aggiornamento del servizio SAP per il download di NetWeaver 7,5](./media/hana-get-started/image001.jpg)

Download di HANA SP12 Platform Edition:

 ![Finestra di installazione e aggiornamento del servizio SAP per il download di HANA SP12 Platform Edition](./media/hana-get-started/image002.jpg)

