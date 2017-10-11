---
title: Migrazione di VM ad Archiviazione Premium di Azure | Documentazione Microsoft
description: Eseguire la migrazione delle VM esistenti in Archiviazione Premium di Azure. Archiviazione Premium offre prestazioni elevate e supporto per dischi a bassa latenza per carichi di lavoro con I/O intensivo in esecuzione su Macchine virtuali di Azure.
services: storage
documentationcenter: na
author: yuemlu
manager: tadb
editor: tysonn
ms.assetid: 272250b3-fd4e-41d2-8e34-fd8cc341ec87
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: yuemlu
ms.openlocfilehash: ca893f87b155a92c457e3bf6d9d39aaf86bf5fb3
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Migrazione in Archiviazione Premium di Azure (dischi non gestiti)

> [!NOTE]
> Questo articolo descrive come migrare una VM che usa dischi Standard non gestiti a una VM che usa dischi Premium non gestiti. È consigliabile usare Azure Managed Disks per le nuove VM e convertire i dischi non gestiti esistenti in dischi gestiti. Managed Disks gestisce automaticamente gli account di archiviazione sottostanti e non è necessario eseguire questa attività manualmente. Per altre informazioni, vedere [Panoramica di Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).
>

Archiviazione Premium di Azure offre prestazioni elevate e supporto per dischi a bassa latenza per le macchine virtuali che eseguono carichi di lavoro con I/O intensivo. Per trarre vantaggio dalla velocità e dalle prestazioni di questi dischi, è possibile migrare i dischi delle VM dell'applicazione ad Archiviazione Premium di Azure.

Lo scopo di questa guida è preparare i nuovi utenti di Archiviazione Premium di Microsoft Azure a eseguire una transizione senza intoppi dal sistema corrente ad Archiviazione Premium. Questa guida descrive tre aspetti chiave di questo processo:

* [Pianificare la migrazione ad Archiviazione Premium](#plan-the-migration-to-premium-storage)
* [Preparare e copiare i dischi rigidi virtuali in Archiviazione Premium](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Creare una macchina virtuale di Azure usando Archiviazione Premium](#create-azure-virtual-machine-using-premium-storage)

È possibile eseguire la migrazione di VM da altre piattaforme ad Archiviazione Premium di Azure o migrare VM esistenti di Azure da Archiviazione Standard ad Archiviazione Premium. Questa guida illustra i passaggi per entrambi i due scenari. Seguire i passaggi specificati nella sezione pertinente al proprio scenario.

> [!NOTE]
> Una panoramica delle funzionalità e dei prezzi di Archiviazione Premium è disponibile in [Archiviazione Premium: archiviazione dalle prestazioni elevate per carichi di lavoro di macchine virtuali di Azure](storage-premium-storage.md). È consigliabile eseguire la migrazione di qualsiasi disco di macchine virtuali che richiede un numero elevato di IOPS ad Archiviazione Premium di Azure per ottenere prestazioni ottimali per l'applicazione. Se il disco non richiede un numero elevato di IOPS, è possibile limitare i costi mantenendolo in Archiviazione Standard, che archivia i dati dei dischi delle macchine virtuali in unità disco rigido (HDD) invece che in unità SSD.
>

Per completare l'intero processo di migrazione, potrebbero essere necessarie altre azioni sia prima che dopo i passaggi descritti in questa guida. Tra gli esempi sono incluse la configurazione di reti virtuali o di endpoint oppure l'applicazione di modifiche al codice direttamente nell'applicazione che possono causare tempi di inattività all'applicazione. Queste azioni sono univoche per ogni applicazione ed è consigliabile completarle insieme ai passaggi descritti in questa guida per eseguire la transizione completa ad Archiviazione Premium con la massima semplicità possibile.

## <a name="plan-the-migration-to-premium-storage"></a>Pianificare la migrazione ad Archiviazione Premium
Questa sezione è preparatoria ai passaggi per la migrazione indicati nell'articolo, e aiuta a prendere la migliore decisione relativamente ai tipi di macchina virtuale e disco.

### <a name="prerequisites"></a>Prerequisiti
* È necessaria una sottoscrizione di Azure. Se non è disponibile, è possibile creare una sottoscrizione di [valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/) di un mese oppure visitare [Prezzi di Azure](https://azure.microsoft.com/pricing/) per altre opzioni.
* Per eseguire i cmdlet PowerShell è necessario il modulo di Microsoft Azure PowerShell. Vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview) per le istruzioni relative al punto di installazione e all’installazione.
* Quando si pianifica di usare macchine virtuali di Azure in esecuzione su Archiviazione Premium, è necessario usare macchine virtuali in grado di supportare Archiviazione Premium. Con le VM che supportano Archiviazione Premium è possibile usare dischi sia di Archiviazione Standard che di Archiviazione Premium. I dischi di archiviazione premium saranno disponibili con più tipi di macchine virtuali in futuro. Per altre informazioni su tutte le dimensioni e su tutti i tipi di dischi disponibili per le macchine virtuali di Azure, vedere [Dimensioni delle macchine virtuali](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [Dimensioni dei servizi cloud](../../cloud-services/cloud-services-sizes-specs.md).

### <a name="considerations"></a>Considerazioni
Una VM di Azure supporta il collegamento di diversi dischi di Archiviazione Premium e offre alle applicazioni fino a 256 TB di spazio di archiviazione per ogni VM. Con Archiviazione Premium le applicazioni possono raggiungere 80.000 IOPS (operazioni di input/output al secondo) per ogni macchina virtuale e 2000 MB al secondo di velocità effettiva dei dischi per ogni macchina virtuale, con una latenza estremamente bassa per le operazioni di lettura. Sono disponibili diverse opzioni in termini di macchine virtuali e dischi. Questa sezione aiuta a individuare l'opzione più adatta al carico di lavoro.

#### <a name="vm-sizes"></a>Dimensioni delle macchine virtuali
Le specifiche delle dimensioni delle VM di Azure sono elencate in [Dimensioni delle macchine virtuali](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Esaminare le caratteristiche delle prestazioni delle Macchine virtuali che usano Archiviazione Premium e scegliere le dimensioni delle VM maggiormente indicate per i propri carichi di lavoro. Assicurarsi che nella macchina virtuale sia disponibile larghezza di banda sufficiente per gestire il traffico dei dischi.

#### <a name="disk-sizes"></a>Dimensione disco
È possibile usare cinque tipi di dischi con la VM, ognuno con limiti di velocità effettiva e IOP specifici. Tenere in considerazione questi limiti nella scelta del tipo di disco per la macchina virtuale in base alle esigenze dell’applicazione in termini di capacità, prestazioni, scalabilità e carichi di picco.

| Tipo di disco Premium  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Dimensioni disco           | 128 GB| 512 GB| 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 
| IOPS per disco       | 500   | 2300  | 5000           | 7500           | 7500           | 
| Velocità effettiva per disco | 100 MB al secondo | 150 MB al secondo | 200 MB al secondo | 250 MB al secondo | 250 MB al secondo |

A seconda del carico di lavoro, determinare se per la macchina virtuale in uso sono necessari dischi dati aggiuntivi. È possibile collegare più dischi dati persistenti alla macchina virtuale in uso. Se necessario, è possibile eseguire lo striping dei dischi per aumentare la capacità e le prestazioni del volume. Per altre informazioni sullo striping del disco, vedere [qui](storage-premium-storage-performance.md#disk-striping). Se si esegue lo striping di dischi dati di Archiviazione Premium con [Spazi di archiviazione][4], è consigliabile configurare una colonna per ogni disco usato. In caso contrario, le prestazioni complessive del volume in cui è stato eseguito lo striping possono essere inferiori al previsto a causa di una distribuzione non uniforme del traffico di dati da un disco a un altro. Per le macchine virtuali Linux è possibile usare l'utilità *mdadm* per ottenere lo stesso risultato. Per informazioni dettagliate, vedere l'articolo sulla [configurazione del RAID software in Linux](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .

#### <a name="storage-account-scalability-targets"></a>Obiettivi di scalabilità per gli account di archiviazione
Gli account di Archiviazione Premium hanno i seguenti obiettivi di scalabilità oltre agli [obiettivi di scalabilità e prestazioni per Archiviazione di Azure](storage-scalability-targets.md). Se le esigenze dell'applicazione superano gli obiettivi di scalabilità di un singolo account di archiviazione, compilare l'applicazione in modo che sia possibile usare più account di archiviazione e partizionare i dati tra tali account di archiviazione.

| Capacità account totale | Larghezza di banda totale per un account di archiviazione con ridondanza locale |
|:--- |:--- |
| Capacità disco : 35 TB<br />Capacità snapshot: 10 TB |Fino a 50 gigabit al secondo per dati in ingresso e in uscita |

Per altre informazioni sulle specifiche di Archiviazione Premium, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](storage-premium-storage.md#scalability-and-performance-targets).

#### <a name="disk-caching-policy"></a>Criteri di memorizzazione nella cache su disco
Per impostazione predefinita, il criterio di memorizzazione nella cache su disco è impostato su *Sola lettura* per tutti i dischi di dati Premium e su *Lettura/scrittura* per il disco del sistema operativo Premium collegato alla macchina virtuale. Queste impostazioni di configurazione sono consigliate per ottenere prestazioni ottimali per le operazioni di I/O dell'applicazione. Per i dischi di dati con un utilizzo elevato della scrittura o di sola scrittura (ad esempio i file di log di SQL Server), disabilitare la memorizzazione nella cache su disco in modo da migliorare le prestazioni delle applicazioni. Le impostazioni della cache per i dischi dati esistenti possono essere aggiornate mediante il [portale di Azure](https://portal.azure.com) o il parametro *-HostCaching* del cmdlet *Set-AzureDataDisk*.

#### <a name="location"></a>Località
Selezionare una posizione in cui è disponibile il servizio Archiviazione Premium di Azure. Per informazioni aggiornate sulle località disponibili, vedere [Prodotti in base all'area](https://azure.microsoft.com/regions/#services) . Le macchine virtuali presenti nella stessa area dell'account di archiviazione in cui sono archiviati i dischi per la macchina virtuale offrono prestazioni superiori rispetto a quelle ubicate in aree separate.

#### <a name="other-azure-vm-configuration-settings"></a>Altre impostazioni di configurazione delle macchine virtuali di Azure
Quando si crea una macchina virtuale di Azure viene richiesto di configurare determinate impostazioni della macchina virtuale. Tenere presente che esistono alcune impostazioni fisse per la durata della macchina virtuale, mentre è possibile modificarne o aggiungerne altre in un secondo momento. Esaminare queste impostazioni di configurazione della macchina virtuale di Azure e assicurarsi che siano configurate in modo appropriato per soddisfare le esigenze del carico di lavoro.

### <a name="optimization"></a>Ottimizzazione
L'articolo [Archiviazione Premium di Azure: progettata per prestazioni elevate](storage-premium-storage-performance.md) fornisce indicazioni per lo sviluppo di applicazioni a prestazioni elevate mediante l'Archiviazione Premium di Azure. È possibile usare le linee guida disponibili in questo documento insieme alle procedure consigliate per le prestazioni applicabili alle tecnologie usate dall'applicazione.

## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Preparare e copiare i dischi rigidi virtuali in Archiviazione Premium
La sezione seguente offre le linee guida per preparare i dischi rigidi virtuali da una macchina virtuale e per copiarli in Archiviazione di Azure.

* [Scenario 1: Migrazione di VM di Azure esistenti in Archiviazione Premium di Azure](#scenario1)
* [Scenario 2: Migrazione di VM da altre piattaforme ad Archiviazione Premium di Azure](#scenario2)

### <a name="prerequisites"></a>Prerequisiti
Di seguito sono indicati i requisiti per preparare i dischi rigidi virtuali per la migrazione:

* Una sottoscrizione di Azure, un account di archiviazione e un contenitore in tale account di archiviazione in cui copiare il disco rigido virtuale. Si noti che l'account di archiviazione di destinazione può essere un account di archiviazione Standard o Premium in base alle esigenze dell’utente.
* Uno strumento per generalizzare il disco rigido virtuale se si pianifica di creare più istanze di macchine virtuali da esso. Ad esempio, sysprep per Windows o virt-sysprep per Ubuntu.
* Uno strumento per caricare il file del disco rigido virtuale nell'account di archiviazione. Vedere [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md) o usare [Esplora archivi di Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Questa guida descrive la copia del disco rigido virtuale utilizzando lo strumento AzCopy.

> [!NOTE]
> Se si sceglie l'opzione di copia sincronizzata con AzCopy, per ottenere prestazioni ottimali copiare il disco rigido virtuale eseguendo uno di questi strumenti da una macchina virtuale di Azure nella stessa area dell'account di archiviazione di destinazione. Se si copia un disco rigido virtuale da una macchina virtuale di Azure in un'area diversa, le prestazioni potrebbero essere più lente.
>
> Per la copia di una grande quantità di dati su una larghezza di banda limitata, prendere in considerazione l'[uso del servizio di importazione/esportazione di Azure per trasferire i dati nell'archivio BLOB](../storage-import-export-service.md). Ciò consente di trasferire i dati tramite l'invio delle unità disco rigido a un datacenter Azure. È possibile utilizzare il servizio di importazione/esportazione di Azure per copiare dati in un solo account di archiviazione standard. Una volta che i dati si trovano nell'account di archiviazione standard, è possibile usare l'[API Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) oppure AzCopy per trasferire i dati all'account di archiviazione Premium.
>
> Notare che Microsoft Azure supporta solo file di disco rigido virtuale a dimensione fissa. I file VHDX o i dischi rigidi virtuali dinamici non sono supportati. Se si dispone di un disco rigido virtuale dinamico, è possibile convertirlo in un disco a dimensione fissa utilizzando il cmdlet [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) .
>
>

### <a name="scenario1"></a>Scenario 1: Migrazione di VM di Azure esistenti in Archiviazione Premium di Azure
Se si esegue la migrazione di macchine virtuali di Azure esistenti, arrestare la macchina virtuale, preparare i dischi rigidi virtuali a seconda del tipo di disco rigido virtuale desiderato e quindi copiare il disco rigido Virtuale con AzCopy o PowerShell.

La macchina virtuale deve essere completamente inattiva per la migrazione a uno stato pulito. Il tempo di inattività proseguirà fino al termine della migrazione.

#### <a name="step-1-prepare-vhds-for-migration"></a>Passaggio 1. Preparare i dischi rigidi virtuali per la migrazione
Se si esegue la migrazione di macchine virtuali esistenti di Azure ad Archiviazione Premium, il disco rigido virtuale può essere:

* Un'immagine del sistema operativo generalizzata
* Un disco di sistema operativo univoco
* Un disco dati

Di seguito vengono illustrati tre scenari per la preparazione dei dischi rigidi virtuali.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Uso di un disco rigido virtuale del sistema operativo generalizzato per creare più istanze di macchine virtuali
Se si sta caricando un disco rigido virtuale che verrà utilizzato per creare più istanze di macchine virtuali di Azure generiche, è innanzitutto necessario generalizzare il disco rigido virtuale mediante un'utilità sysprep. Tale utilità si applica a un disco rigido virtuale che si trova in locale o nel cloud. Sysprep consente di rimuovere eventuali informazioni specifiche sul computer dal disco rigido virtuale.

> [!IMPORTANT]
> Eseguire un'istantanea o il backup della macchina virtuale prima di generalizzarla. L'esecuzione di sysprep eliminerà l'istanza della macchina virtuale. Eseguire i passaggi di seguito per utilizzare sysprep su un disco rigido virtuale di sistema operativo Windows. Si noti che l’esecuzione del comando Sysprep richiederà di arrestare la macchina virtuale. Per altre informazioni su Sysprep, vedere la [panoramica di Sysprep](http://technet.microsoft.com/library/hh825209.aspx) oppure il [materiale di riferimento tecnico di Sysprep](http://technet.microsoft.com/library/cc766049.aspx).
>
>

1. Aprire una finestra del Prompt dei comandi come amministratore.
2. Immettere il comando seguente per aprire Sysprep:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. Nell'Utilità preparazione sistema selezionare Enter System Out-of-Box Experience (OOBE), selezionare la casella di controllo Generalizza, selezionare **Arresto** e fare clic su **OK**, come illustrato nell'immagine seguente. Sysprep eseguirà la generalizzazione del sistema operativo e arresterà il sistema.

    ![][1]

Per una VM Ubuntu, utilizzare virt-sysprep per ottenere lo stesso risultato. Vedere [virt-sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) per ulteriori informazioni. Vedere alcuni [software open source di provisioning dei server Linux](http://www.cyberciti.biz/tips/server-provisioning-software.html) per altri sistemi operativi Linux.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Uso di un disco rigido virtuale di sistema operativo univoco per creare una singola istanza di macchina virtuale
Se si dispone di un'applicazione in esecuzione nella macchina virtuale che richiede i dati specifici del computer, non generalizzare il disco rigido virtuale. Un disco rigido virtuale non generalizzato può essere utilizzato per creare un'istanza univoca di macchina virtuale di Azure. Ad esempio, se si dispone di Controller di dominio sul disco rigido virtuale, l'esecuzione di sysprep lo renderà inefficace come controller di dominio. Rivedere le applicazioni in esecuzione sulla macchina virtuale e l'impatto dell'esecuzione di sysprep su di esse prima di generalizzare il disco rigido virtuale.

##### <a name="register-data-disk-vhd"></a>Registrare un disco rigido virtuale di dischi dati
Se si dispone di dischi dati in un'archiviazione di Azure di cui eseguire la migrazione, è necessario assicurarsi che le macchine virtuali che usano questi dischi dati vengano arrestate.

Attenersi alla procedura descritta di seguito per copiare il disco rigido virtuale in Archiviazione Premium di Azure e registrarlo come disco dati sottoposto a provisioning.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Passaggio 2. Crea la destinazione per il disco rigido virtuale
Creare un account di archiviazione per mantenere i dischi rigidi virtuali. Considerare i seguenti punti quando si pianifica la posizione in cui archiviare i dischi rigidi virtuali:

* L'account di archiviazione Premium di destinazione.
* La posizione dell'account di archiviazione deve essere uguale a quella delle macchine virtuali supportate da Azure che saranno create nella fase finale. È possibile eseguire la copia in un nuovo account di archiviazione oppure pianificare di utilizzare lo stesso account di archiviazione in base alle esigenze.
* Copiare e salvare la chiave dell’account di archiviazione dell'account di archiviazione di destinazione per la fase successiva.

Per i dischi dati, è possibile scegliere di mantenerne alcuni in un account di archiviazione Standard, ad esempio, i dischi che dispongono di un'archiviazione meno problematica, ma è consigliabile spostare tutti i dati affinché il carico di lavoro di produzione usi l'archiviazione Premium.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>Passaggio 3. Copia del disco rigido virtuale con AzCopy o PowerShell
Per elaborare una di queste due opzioni è necessario individuare il percorso del container e la chiave dell'account di archiviazione. Il percorso del contenitore e la chiave dell'account di archiviazione sono reperibili in **Portale di Azure** > **Archiviazione**. L'URL del contenitore è simile a: "https://myaccount.blob.core.windows.net/mycontainer/".

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Opzione 1: copia di un disco rigido virtuale con AzCopy (copia asincrona)
Tramite AzCopy è possibile caricare facilmente il disco rigido virtuale in Internet. A seconda della dimensione dei dischi rigidi virtuali, tale operazione potrebbe richiedere tempo. Ricordarsi di verificare i limiti di ingresso/uscita dell’account di archiviazione quando si utilizza questa opzione. Vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](storage-scalability-targets.md) per i dettagli.

1. Scaricare e installare AzCopy da qui: [versione più recente di AzCopy](http://aka.ms/downloadazcopy)
2. Aprire Azure PowerShell e passare alla cartella in cui è installato AzCopy.
3. Utilizzare il seguente comando per copiare il file di disco rigido virtuale da "Source" a "Destination".

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Esempio:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```

    Di seguito sono riportate le descrizioni dei parametri utilizzati nel comando AzCopy:

   * **/Source: *&lt;source&gt;:*** percorso della cartella o URL del contenitore di archiviazione che contiene il disco rigido virtuale.
   * **/SourceKey: *&lt;source-account-key&gt;:*** chiave dell'account di archiviazione di origine.
   * **/Dest: *&lt;destination&gt;:*** URL del contenitore di archiviazione in cui copiare il disco rigido virtuale.
   * **/DestKey: *&lt;dest-account-key&gt;:*** chiave dell'account di archiviazione di destinazione.
   * **/Pattern: *&lt;file-name&gt;:*** specificare il nome file del disco rigido virtuale da copiare.

Per informazioni dettagliate sull'uso dello strumento AzCopy, vedere [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Opzione 2: copia di un disco rigido virtuale con PowerShell (copia sincronizzata)
È anche possibile copiare il file VHD con il cmdlet di PowerShell Start-AzureStorageBlobCopy. Usare il comando seguente in Azure PowerShell per copiare il disco VHD. Sostituire i valori in <> con i valori corrispondenti dell'account di archiviazione di origine e di destinazione. Per usare questo comando, è necessario un contenitore chiamato vhds nell'account di archiviazione di destinazione. Se il contenitore non esiste, crearne uno prima di eseguire il comando.

```powershell
$sourceBlobUri = <source-vhd-uri>

$sourceContext = New-AzureStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

$destinationContext = New-AzureStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext
```

Esempio:

```powershell
C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

C:\PS> $sourceContext = New-AzureStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

C:\PS> $destinationContext = New-AzureStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

C:\PS> Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext
```

### <a name="scenario2"></a>Scenario 2: Migrazione di VM da altre piattaforme ad Archiviazione Premium di Azure
Se si esegue la migrazione del disco rigido virtuale dall’archiviazione cloud non Azure ad Azure, è necessario innanzitutto esportare il disco rigido virtuale in una directory locale. Copiare il percorso di origine completo della directory locale in cui è archiviato il disco rigido virtuale, quindi usare AzCopy per caricarlo in Archiviazione di Azure.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Passaggio 1. Esportare il disco rigido virtuale in una directory locale
##### <a name="copy-a-vhd-from-aws"></a>Copia di un disco rigido virtuale da AWS
1. Se si utilizza AWS, esportare l'istanza EC2 in un disco rigido virtuale in un bucket Amazon S3. Seguire i passaggi descritti nella documentazione di Amazon per l'esportazione delle istanze di Amazon EC2 per installare lo strumento di interfaccia della riga di comando Amazon EC2 ed eseguire il comando create-instance-export-task per esportare l'istanza di EC2 in un file di disco rigido virtuale. Assicurarsi di usare **VHD** per la variabile DISK&#95;IMAGE&#95;FORMAT quando si esegue il comando **create-instance-export-task**. Il file di disco rigido virtuale esportato viene salvato nel bucket Amazon S3 indicato durante tale processo.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Scaricare il file di disco rigido virtuale dal bucket S3. Selezionare il file del disco rigido virtuale e scegliere **Azioni** > **Download**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Copia di un disco rigido virtuale da altri cloud non Azure
Se si esegue la migrazione del disco rigido virtuale dall’archiviazione cloud non Azure ad Azure, è necessario innanzitutto esportare il disco rigido virtuale in una directory locale. Copiare il percorso di origine completo della directory locale in cui è archiviato il disco rigido virtuale.

##### <a name="copy-a-vhd-from-on-premises"></a>Copia di un disco rigido virtuale da locale
Se si esegue la migrazione del disco rigido virtuale da un ambiente locale, è necessario il percorso di origine completo in cui è archiviato il disco rigido virtuale. Il percorso di origine può essere un percorso server o una condivisione file.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Passaggio 2. Crea la destinazione per il disco rigido virtuale
Creare un account di archiviazione per mantenere i dischi rigidi virtuali. Considerare i seguenti punti quando si pianifica la posizione in cui archiviare i dischi rigidi virtuali:

* L'account di archiviazione di destinazione potrebbe essere standard o premium, a seconda delle esigenze dell'applicazione.
* L'area dell'account di archiviazione deve essere uguale a quella delle VM supportate da Azure che saranno create nella fase finale. È possibile eseguire la copia in un nuovo account di archiviazione oppure pianificare di utilizzare lo stesso account di archiviazione in base alle esigenze.
* Copiare e salvare la chiave dell’account di archiviazione dell'account di archiviazione di destinazione per la fase successiva.

È consigliabile spostare tutti i dati affinché il carico di lavoro di produzione usi l'archiviazione Premium.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Passaggio 3. Caricare il disco rigido virtuale in Archiviazione di Azure
Dopo avere creato il disco rigido virtuale nella directory locale, è possibile usare AzCopy o AzurePowerShell per caricare il file con estensione vhd in Archiviazione di Azure. Di seguito sono fornite entrambe le opzioni:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Opzione 1: uso del comando Add-AzureVhd di Azure PowerShell per caricare il file con estensione vhd

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Un esempio <Uri> potrebbe essere ***"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd"***. Un esempio <FileInfo> potrebbe essere ***"C:\path\to\upload.vhd"***.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Opzione 2: uso di AzCopy per caricare il file con estensione vhd
Tramite AzCopy è possibile caricare facilmente il disco rigido virtuale in Internet. A seconda della dimensione dei dischi rigidi virtuali, tale operazione potrebbe richiedere tempo. Ricordarsi di verificare i limiti di ingresso/uscita dell’account di archiviazione quando si utilizza questa opzione. Vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](storage-scalability-targets.md) per i dettagli.

1. Scaricare e installare AzCopy da qui: [versione più recente di AzCopy](http://aka.ms/downloadazcopy)
2. Aprire Azure PowerShell e passare alla cartella in cui è installato AzCopy.
3. Utilizzare il seguente comando per copiare il file di disco rigido virtuale da "Source" a "Destination".

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Esempio:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
    ```

    Di seguito sono riportate le descrizioni dei parametri utilizzati nel comando AzCopy:

   * **/Source: *&lt;source&gt;:*** percorso della cartella o URL del contenitore di archiviazione che contiene il disco rigido virtuale.
   * **/SourceKey: *&lt;source-account-key&gt;:*** chiave dell'account di archiviazione di origine.
   * **/Dest: *&lt;destination&gt;:*** URL del contenitore di archiviazione in cui copiare il disco rigido virtuale.
   * **/DestKey: *&lt;dest-account-key&gt;:*** chiave dell'account di archiviazione di destinazione.
   * **/BlobType:page:** specifica che la destinazione è un BLOB di pagine.
   * **/Pattern: *&lt;file-name&gt;:*** specificare il nome file del disco rigido virtuale da copiare.

Per informazioni dettagliate sull'uso dello strumento AzCopy, vedere [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Altre opzioni per il caricamento di un disco rigido virtuale
È inoltre possibile caricare un disco rigido virtuale nell'account di archiviazione utilizzando uno dei seguenti modi:

* [API Copy Blob di Archiviazione di Azure](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Caricamento di BLOB in Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)
* [Materiale di riferimento dell'API REST del servizio di importazione/esportazione dell'archiviazione](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> È consigliabile usare il servizio di importazione/esportazione se il tempo di caricamento stimato è maggiore di 7 giorni. È possibile usare [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) per stimare il tempo in base alla dimensione dei dati e all'unità di trasferimento.
>
> Il servizio Importazione/esportazione può essere usato per eseguire la copia in un account di archiviazione standard. Sarà necessario copiare dall'archiviazione standard all’account di archiviazione premium mediante uno strumento come AzCopy.
>
>

## <a name="create-azure-virtual-machine-using-premium-storage"></a>Creare macchine virtuali di Azure tramite Archiviazione Premium
Dopo aver caricato o copiato il disco rigido virtuale nell'account di archiviazione desiderato, seguire le istruzioni in questa sezione per registrare il disco rigido virtuale come immagine del sistema operativo oppure come disco del sistema operativo, a seconda dello scenario e quindi creare da esso un'istanza di macchina virtuale. Il disco rigido virtuale del disco dati può essere collegato alla macchina virtuale una volta che questa è stata creata.
Al termine di questa sezione viene fornito un esempio di script di migrazione. Si tratta di uno script semplificato, che non corrisponde a tutti gli scenari. Potrebbe essere necessario aggiornare lo script per farlo corrispondere allo scenario specifico. Per verificare se lo script è adatto al proprio scenario, vedere [Esempio di script di migrazione](#a-sample-migration-script).

### <a name="checklist"></a>Elenco di controllo
1. Attendere il completamento della copia di tutti i dischi rigidi virtuali.
2. Verificare che Archiviazione Premium sia disponibile nell'area in cui si eseguirà la migrazione.
3. Decidere la nuova serie di VM da usare. Dovrebbe supportare Archiviazione Premium, con una dimensione basata sulla disponibilità nell'area e sulle proprie esigenze.
4. Decidere le dimensioni esatte della VM da usare. Le dimensioni della VM devono essere abbastanza grandi da supportare tutti i dischi dati. Ad esempio, Se, ad esempio, ci sono 4 dischi dati, la macchina virtuale deve avere 2 o più memorie centrali. Considerare anche la potenza di elaborazione, la memoria e la larghezza di banda di rete necessarie.
5. Creare un account di archiviazione Premium nell'area di destinazione Questo account verrà usato per la nuova VM.
6. Tenere a portata di mano i dettagli della VM corrente, inclusi l'elenco di dischi e i BLOB VHD corrispondenti.

Preparare l'applicazione per il tempo di inattività. Per eseguire una migrazione senza problemi, è necessario arrestare ogni elaborazione nel sistema corrente. Solo a quel punto lo stato sarà coerente e sarà possibile eseguire la migrazione alla nuova piattaforma. La durata del tempo di inattività dipenderà dalla quantità di dati nei dischi di cui eseguire la migrazione.

> [!NOTE]
> Se si sta creando una VM di Azure Resource Manager da un disco rigido virtuale specializzato, fare riferimento a [questo modello](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) per la distribuzione della macchina virtuale di Resource Manager tramite un disco esistente.
>
>

### <a name="register-your-vhd"></a>Registrazione del disco rigido virtuale
Per creare una VM dal disco rigido virtuale del sistema operativo o collegare un disco dati a una nuova VM, è necessario innanzitutto registrarlo. Seguire la procedura riportata di seguito, a seconda dello scenario del disco rigido virtuale.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Disco rigido virtuale del sistema operativo generalizzato per creare più istanze di macchine virtuali di Azure
Dopo che il disco rigido virtuale dell'immagine del sistema operativo viene caricato nell'account di archiviazione, registrarlo come **immagine macchina virtuale di Azure** , in modo che sia possibile usarlo per creare una o più istanze di macchine virtuali. Utilizzare i cmdlet PowerShell riportati di seguito per registrare il disco rigido virtuale come immagine del sistema operativo di una macchina virtuale di Azure. Fornire l'URL completo del contenitore in cui è stato copiato il disco rigido virtuale.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Copiare e salvare il nome di questa nuova immagine di macchina virtuale di Azure. Nell'esempio precedente è *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Disco rigido virtuale di sistema operativo univoco per creare una singola istanza di macchina virtuale di Azure
Dopo aver caricato il disco rigido virtuale di sistema operativo univoco nell'account di archiviazione, registrarlo come **disco del sistema operativo Azure** in modo che sia possibile usarlo per creare un'istanza di macchina virtuale. Utilizzare i cmdlet PowerShell riportati di seguito per registrare il disco rigido virtuale come disco del sistema operativo di Azure. Fornire l'URL completo del contenitore in cui è stato copiato il disco rigido virtuale.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Copiare e salvare il nome di questo nuovo disco di sistema operativo di Azure. Nell'esempio precedente è *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Disco rigido virtuale del disco dati da collegare alle nuove istanze di macchine virtuali di Azure
Dopo avere caricato il disco rigido virtuale del disco dati nell'account di archiviazione, registrarlo come disco dati Azure in modo che possa essere collegato alla nuova istanza di VM di Azure serie DS, serie DSv2 o serie GS.

Utilizzare i cmdlet PowerShell riportati di seguito per registrare il disco rigido virtuale come disco dati di Azure. Fornire l'URL completo del contenitore in cui è stato copiato il disco rigido virtuale.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Copiare e salvare il nome di questo nuovo disco dati di Azure. Nell'esempio precedente è *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Creare una macchina virtuale che supporti Archiviazione Premium
Una volta che l'immagine del sistema operativo o il disco del sistema operativo sono registrati, creare una nuova VM di Azure della serie DS, DSv2 o GS. Si utilizzerà l'immagine del sistema operativo o il nome del disco del sistema operativo registrato. Selezionare il tipo di macchina virtuale dal livello Archiviazione Premium. Nell'esempio riportato di seguito viene usata la dimensione della macchina virtuale *Standard_DS2*.

> [!NOTE]
> Aggiornare le dimensioni del disco per assicurarsi che corrispondano alla capacità, ai requisiti di prestazione e alle dimensioni dei dischi di Azure disponibili.
>
>

Seguire i cmdlet di PowerShell dettagliati indicati di seguito per creare la nuova macchina virtuale. Innanzitutto, impostare i parametri comuni:

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

Innanzitutto, creare un servizio cloud in cui verranno ospitate le nuove VM.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

In secondo luogo, a seconda dello scenario, creare l'istanza di VM di Azure dall'immagine del sistema operativo o dal disco del sistema operativo registrato.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Disco rigido virtuale del sistema operativo generalizzato per creare più istanze di macchine virtuali di Azure
Creare le nuove istanze di macchine virtuali di Azure serie DS utilizzando l’ **immagine del sistema operativo Azure** registrata. Specificare questo nome immagine del sistema operativo nella configurazione della macchina virtuale quando si crea la nuova macchina virtuale come illustrato di seguito.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Disco rigido virtuale di sistema operativo univoco per creare una singola istanza di macchina virtuale di Azure
Creare una nuova istanza di macchina virtuale di Azure serie DS utilizzando il **disco del sistema operativo Azure** registrato. Specificare questo nome disco del sistema operativo nella configurazione della macchina virtuale quando si crea la nuova macchina virtuale come illustrato di seguito.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Specificare altre informazioni di macchina virtuale di Azure, ad esempio, un servizio cloud, l’area, l’account di archiviazione, il set di disponibilità e i criteri di memorizzazione nella cache. Si noti che l'istanza di macchina virtuale deve essere collocata con il sistema operativo o con i dischi dati associati, in modo che il servizio cloud, l'area e l'account di archiviazione selezionati si trovino tutti nella stessa posizione dei dischi rigidi virtuali sottostanti di questi dischi.

### <a name="attach-data-disk"></a>Collegare il disco dati
Infine, se sono stati registrati i dischi rigidi virtuali del disco dati, collegarli alla nuova macchina virtuale che supporta Archiviazione Premium.

Utilizzare il seguente cmdlet PowerShell per collegare il disco dati alla nuova macchina virtuale e specificare i criteri di memorizzazione nella cache. Nell'esempio riportato di seguito il criterio di memorizzazione nella cache è impostato su *ReadOnly*.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> È possibile che per supportare l'applicazione siano necessari passaggi specifici che non sono illustrati in questa guida.
>
>

### <a name="checking-and-plan-backup"></a>Controllo e pianificazione del backup
Una volta che la nuova VM è operativa, accedervi con lo stesso ID di accesso e la stessa password della VM originale e verificare che tutto funzioni come previsto. Tutte le impostazioni, inclusi i volumi con striping, dovrebbero essere presenti nella nuova VM.

L'ultimo passaggio è la pianificazione del backup e della manutenzione per la nuova VM in base alle esigenze dell'applicazione.

### <a name="a-sample-migration-script"></a>Esempio di script di migrazione
Se è necessario eseguire la migrazione di più VM, sarà utile l'automazione con gli script di PowerShell. Di seguito è riportato un esempio di script che automatizza la migrazione di una VM. Tenere presente che lo script seguente è solo un esempio e che sono state formulate alcune ipotesi sui dischi VM correnti. Potrebbe essere necessario aggiornare lo script per farlo corrispondere allo scenario specifico.

Presupposti:

* Si stanno creando macchine virtuali di Azure classico.
* I dischi del sistema operativo e i dischi dati di origine si trovano nello stesso account di archiviazione e nello stesso contenitore. Se i dischi del sistema operativo e i dischi dati non sono nella stessa posizione, è possibile usare AzCopy o Azure PowerShell per copiare i dischi rigidi virtuali nei contenitori e negli account di archiviazione. Fare riferimento al passaggio precedente: [Copia del disco rigido virtuale con AzCopy o PowerShell](#copy-vhd-with-azcopy-or-powershell). In alternativa,è possibile modificare lo script per adattarlo al proprio scenario, ma è consigliabile usare AzCopy o PowerShell poiché è più facile e veloce.

Di seguito viene fornito lo script di automazione. Sostituire il testo con le informazioni necessarie e aggiornare lo script affinché corrisponda a uno scenario specifico.

> [!NOTE]
> L'uso dello script esistente non mantiene la configurazione di rete della macchina virtuale di origine. In questo caso, è necessario riconfigurare le impostazioni di rete nelle macchine virtuali migrate.
>
>

```
    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a VM from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source VM to the new storage account.
    And then it will create a new VM from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement, but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED "AS IS" WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location "Southeast Asia"

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    http://azure.microsoft.com/documentation/articles/powershell-install-configure/
    http://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [Bool] $DataDiskOnly = $true,

    # how frequently to report the copy status in sceconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module verion" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article http://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you'd like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the VM manually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the sourve vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzureStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzureStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzureStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzureStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzureStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzureStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires deleting the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`t[Warning] You chose to copy data disks only. Moving VM requires removing the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. This is an irreversible action. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzureStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting utill the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
            Start-Sleep -Seconds 10
            $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzureStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a VM from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more custimization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location
```

#### <a name="optimization"></a>Ottimizzazione
La configurazione VM corrente potrebbe essere personalizzata per favorirne il funzionamento con i dischi standard, ad esempio, per migliorare le prestazioni usando più dischi in un volume con striping. Ad esempio, invece di usare 4 dischi diversi in Archiviazione Premium, sarà possibile ottimizzare il costo con un solo disco. Le ottimizzazioni di questo tipo devono essere gestite singolarmente e, dopo la migrazione, sono necessari passaggi personalizzati. Esiste anche la possibilità che questo processo non funzioni bene per i database e le applicazioni che dipendono dal layout del disco definito durante la configurazione.

##### <a name="preparation"></a>Operazioni preliminari
1. Completare la migrazione semplice descritta nella sezione più sopra. Le ottimizzazioni verranno eseguite nella nuova VM dopo la migrazione.
2. Definire le dimensioni dei nuovi dischi necessarie per la configurazione ottimizzata.
3. Determinare il mapping dei dischi/volumi correnti alle specifiche dei nuovi dischi.

##### <a name="execution-steps"></a>Passaggi di esecuzione
1. Creare i nuovi dischi con le dimensioni corrette nella VM di Archiviazione Premium.
2. Accedere alla VM e copiare i dati dal volume corrente al nuovo disco di cui viene eseguito il mapping a tale volume. Effettuare questa operazione per tutti i volumi correnti di cui è necessario eseguire il mapping a un nuovo disco.
3. Modificare quindi le impostazioni dell'applicazione in modo da passare ai nuovi dischi e scollegare i vecchi volumi.

Per ottimizzare l'applicazione e migliorare le prestazioni del disco, fare riferimento a [Ottimizzazione delle prestazioni dell'applicazione](storage-premium-storage-performance.md#optimizing-application-performance).

### <a name="application-migrations"></a>Migrazioni delle applicazioni
I database e altre applicazioni complesse potrebbero richiedere particolari passaggi in base a quanto definito dal provider dell'applicazione per la migrazione. Vedere la documentazione relativa a ogni applicazione. Ad esempio, è possibile in genere eseguire la migrazione dei database con il backup e ripristino.

## <a name="next-steps"></a>Passaggi successivi
Controllare le risorse seguenti per scenari specifici per la migrazione di macchine virtuali:

* [Eseguire la migrazione di macchine virtuali di Azure tra account di archiviazione](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Creare e caricare un disco rigido virtuale Windows Server in Azure.](../../virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Creazione e caricamento di un disco rigido virtuale contenente il sistema operativo Linux](../../virtual-machines/linux/classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Migrazione di macchine virtuali da Amazon AWS a Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Inoltre, controllare le seguenti risorse per altre informazioni su Archiviazione di Azure e sulle macchine virtuali di Azure:

* [Archiviazione di Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Macchine virtuali di Azure](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](storage-premium-storage.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: http://technet.microsoft.com/library/hh831739.aspx
