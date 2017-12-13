---
title: Domande frequenti sulla migrazione di Archiviazione di Azure | Microsoft Docs
description: Risposte alle domande comuni sulla migrazione di Archiviazione di Azure
services: storage
documentationcenter: na
author: genlin
manager: timlt
editor: tysonn
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 11/16/2017
ms.author: genli
ms.openlocfilehash: 54ca65ac6fa794c542fc07cd64458b17c327d56d
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2017
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Domande frequenti sulla migrazione di Archiviazione di Azure

Questo articolo risponde alle domande comuni sulla migrazione di Archiviazione di Azure. 

## <a name="faq"></a>domande frequenti

**Come si crea uno script per copiare i file da un contenitore a un altro?**

Per copiare i file tra i contenitori, è possibile usare AzCopy. Vedere l'esempio seguente:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

AzCopy usa l'[API Copy Blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) per copiare ciascun file nel contenitore.  
  
Per eseguire AzCopy, è possibile usare qualsiasi macchina virtuale o computer locale con accesso a Internet. È anche possibile usare la pianificazione di Batch di Azure per eseguire questa operazione automaticamente, ma la procedura risulta più complessa.  
  
Lo script di automazione è progettato per la distribuzione di Azure Resource Manager invece che per la manipolazione del contenuto di archiviazione. Per altre informazioni, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Sono previsti costi per la copia dei dati tra due condivisioni file diverse nello stesso account di archiviazione all'interno della stessa area?**

No. Per questo processo non vengono addebitati costi.

**Come si esegue il backup dell'intero account di archiviazione in un altro account di archiviazione?**

Non è possibile eseguire il backup di un intero account di archiviazione direttamente. È però possibile spostare manualmente il contenitore nell'account di archiviazione in un altro account usando AzCopy o Storage Explorer. I passaggi seguenti illustrano come usare AzCopy per spostare il contenitore:  
 

1.  Installare lo strumento da riga di comando [AzCopy](storage-use-azcopy.md). Questo strumento consente di spostare il file del disco rigido virtuale (VHD) tra account di archiviazione.

2.  Dopo l'installazione di AzCopy in Windows mediante il programma di installazione, aprire una finestra del prompt dei comandi e passare alla cartella di installazione di AzCopy nel computer. Per impostazione predefinita, AzCopy viene installato in **%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy** o in **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.  Eseguire il comando seguente per spostare il contenitore. È necessario sostituire il testo con il valore effettivo.   
     
            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

    - `/Source`: specificare l'URI per l'account di archiviazione di origine (fino al contenitore).  
    - `/Dest`: specificare l'URI per l'account di archiviazione di destinazione (fino al contenitore).  
    - `/SourceKey`: specificare la chiave primaria per l'account di archiviazione di origine. È possibile copiare questa chiave dal portale di Azure, selezionando l'account di archiviazione.  
    - `/DestKey`: specificare la chiave primaria per l'account di archiviazione di destinazione. È possibile copiare questa chiave dal portale selezionando l'account di archiviazione.

Dopo avere eseguito questo comando, i file contenitore vengono spostati nell'account di archiviazione di destinazione.

> [!NOTE]
> L'interfaccia della riga di comando di AzCopy non funziona con l'opzione **Pattern** quando si esegue la copia da un BLOB di Azure a un altro.
>
> È possibile copiare e modificare direttamente il comando di AzCopy e verificare che l'opzione **Pattern** corrisponda all'origine. Assicurarsi anche che i caratteri jolly **/S** siano attivi. Per altre informazioni, vedere [Parametri di AzCopy](storage-use-azcopy.md).

**Come si spostano i dati da un contenitore di archiviazione a un altro?**

A tale scopo, seguire questa procedura:

1.  Creare il contenitore (cartella) nel BLOB di destinazione.

2.  Usare [AzCopy](https://azure.microsoft.com/en-us/blog/azcopy-5-1-release/) per copiare il contenuto dal contenitore BLOB originale a un altro contenitore BLOB.

**Come si crea uno script di PowerShell per spostare i dati da una condivisione file di Azure a un'altra risorsa di archiviazione di Azure?**

Usare AzCopy per spostare i dati da una condivisione file di Azure a un'altra risorsa di archiviazione di Azure. Per altre informazioni, vedere [Trasferire dati con AzCopy in Windows](storage-use-azcopy.md) e [Trasferire dati con AzCopy in Linux](storage-use-azcopy-linux.md).

**Come si caricano i file CSV di grandi dimensioni in Archiviazione di Azure?**

Usare AzCopy per caricare file CSV di grandi dimensioni in Archiviazione di Azure. Per altre informazioni, vedere [Trasferire dati con AzCopy in Windows](storage-use-azcopy.md) e [Trasferire dati con AzCopy in Linux](storage-use-azcopy-linux.md).

**Ogni giorno si rende necessario spostare i log dall'unità D all'account di archiviazione di Azure. Come si automatizza questa operazione?**

È possibile usare AzCopy e creare un'attività nell'Utilità di pianificazione. Caricare i file in un account di archiviazione di Azure tramite uno script batch di AzCopy. Per altre informazioni, vedere [Come configurare ed eseguire attività di avvio per un servizio cloud](../../cloud-services/cloud-services-startup-tasks.md).

**Come si sposta l'account di archiviazione tra sottoscrizioni?**

Usare AzCopy per spostare l'account di archiviazione tra sottoscrizioni. Per altre informazioni, vedere [Trasferire dati con AzCopy in Windows](storage-use-azcopy.md) e [Trasferire dati con AzCopy in Linux](storage-use-azcopy-linux.md).

**Come è possibile spostare circa 10 TB di dati in una risorsa di archiviazione in un'altra area?**

Usare AzCopy per spostare i dati. Per altre informazioni, vedere [Trasferire dati con AzCopy in Windows](storage-use-azcopy.md) e [Trasferire dati con AzCopy in Linux](storage-use-azcopy-linux.md).

**Come è possibile copiare i dati da un ambiente locale ad Archiviazione di Azure?**

Usare AzCopy per copiare i dati. Per altre informazioni, vedere [Trasferire dati con AzCopy in Windows](storage-use-azcopy.md) e [Trasferire dati con AzCopy in Linux](storage-use-azcopy-linux.md).

**Come è possibile spostare i dati da un ambiente locale a File di Azure?**

Usare AzCopy per spostare i dati. Per altre informazioni, vedere [Trasferire dati con AzCopy in Windows](storage-use-azcopy.md) e [Trasferire dati con AzCopy in Linux](storage-use-azcopy-linux.md).

**Come si esegue il mapping di una cartella di contenitori in una macchina virtuale?**

Usare una condivisione file di Azure.

**Come si esegue il backup dell'archiviazione file di Azure?**

Non c'è una soluzione di backup. File di Azure supporta comunque anche la copia asincrona. È pertanto possibile copiare i file:

- Da una condivisione a un'altra condivisione all'interno di un account di archiviazione o in un altro account di archiviazione.

- Da una condivisione a un contenitore BLOB all'interno di un account di archiviazione o in un altro account di archiviazione.

Per altre informazioni, vedere [Trasferire dati con AzCopy in Windows](storage-use-azcopy.md).

**Come si spostano i dischi gestiti in un altro account di archiviazione?**

A tale scopo, seguire questa procedura:

1.  Arrestare la macchina virtuale a cui è collegato il disco gestito.

2.  Copiare il disco rigido virtuale del disco gestito da un'area a un'altra eseguendo lo script di Azure PowerShell seguente:

    ```
    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionId <ID>

    $sas = Grant-AzureRmDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzureStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzureStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Creare un disco gestito usando il file VHD in un'altra area in cui è stato copiato il disco rigido virtuale. A tale scopo, eseguire lo script di Azure PowerShell seguente:  

    ```
    $resourceGroupName = 'MDDemo'

    $diskName = 'contoso\_os\_disk1'

    $vhdUri = 'https://contoso.storageaccou.com.vhd

    $storageId = '/subscriptions/<ID>/resourceGroups/<RG name>/providers/Microsoft.Storage/storageAccounts/contosostorageaccount1'

    $location = 'westus'

    $storageType = 'StandardLRS'

    $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $vhdUri -StorageAccountId $storageId -DiskSizeGB 128

    $osDisk = New-AzureRmDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
    ``` 

Per altre informazioni su come distribuire una macchina virtuale da un disco gestito, vedere [CreateVmFromManagedOsDisk.ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1).

**Come è possibile scaricare 1-2 TB di dati dal portale di Azure?**

Usare AzCopy per scaricare i dati. Per altre informazioni, vedere [Trasferire dati con AzCopy in Windows](storage-use-azcopy.md) e [Trasferire dati con AzCopy in Linux](storage-use-azcopy-linux.md).

**Come si modifica la località secondaria nell'area Europa per un account di archiviazione?**

L'area primaria viene selezionata durante la creazione di un account di archiviazione. L'area secondaria viene determinata in base a quella primaria e non è possibile modificarla. Per altre informazioni, vedere [Replica di Archiviazione di Azure](storage-redundancy.md).

**Dove è possibile ottenere altre informazioni su Crittografia del servizio di archiviazione di Azure?**  
  
Vedere gli articoli seguenti:

-  [Guida alla sicurezza di Archiviazione di Azure](storage-security-guide.md)

-  [Crittografia del servizio di archiviazione di Azure per dati inattivi](storage-service-encryption.md)

**Come si spostano o si scaricano i dati da un account di archiviazione?**

Usare AzCopy per scaricare i dati. Per altre informazioni, vedere [Trasferire dati con AzCopy in Windows](storage-use-azcopy.md) e [Trasferire dati con AzCopy in Linux](storage-use-azcopy-linux.md).


**Come è possibile crittografare i dati in un account di archiviazione?**

Dopo avere abilitato la crittografia in un account di archiviazione, i dati esistenti non vengono crittografati. Per crittografare i dati esistenti, è necessario caricarli di nuovo nell'account di archiviazione.

Usare AzCopy per copiare i dati in un altro account di archiviazione e quindi spostarli di nuovo nell'account di archiviazione. È anche possibile usare la [crittografia per dati inattivi](storage-service-encryption.md).

**Come è possibile scaricare un disco rigido virtuale in un computer locale senza usare l'opzione di download nel portale?**

È possibile usare [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per scaricare un disco rigido virtuale.

**Esistono prerequisiti per la modifica della replica di un account di archiviazione passando dall'archiviazione con ridondanza geografica all'archiviazione con ridondanza locale?**

No. 

**Come si accede all'archiviazione ridondante di File di Azure?**

Per accedere all'archiviazione ridondante, è necessaria l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS). File di Azure supporta tuttavia solo l'archiviazione con ridondanza locale e l'archiviazione con ridondanza geografica standard che non consente l'accesso in sola lettura. 

**Come si effettua il downgrade da un account di archiviazione Premium a un account di archiviazione Standard?**

A tale scopo, seguire questa procedura:

1.  Creare un account di archiviazione standard. In alternativa, usare un account di archiviazione standard esistente nella sottoscrizione.

2.  Scaricare AzCopy. Eseguire uno dei comandi di AzCopy seguenti.
      
    Per copiare interi dischi nell'account di archiviazione:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S 

    Per copiare un solo disco, specificare il nome del disco in **Pattern**:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

   
L'operazione potrebbe richiedere diverse ore.

Per verificare il corretto completamento del trasferimento, esaminare il contenitore dell'account di archiviazione di destinazione nel portale di Azure. Dopo che i dischi sono stati copiati nell'account di archiviazione Standard, è possibile collegarli alla macchina virtuale come un disco esistente. Per altre informazioni, vedere [Collegare un disco dati gestito a una macchina virtuale Windows nel portale di Azure](../../virtual-machines/windows/attach-managed-disk-portal.md).  
  
**Come è possibile passare ad Archiviazione Premium di Azure per una condivisione file?**

Non è consentito usare Archiviazione Premium in una condivisione file di Azure.

**Come si esegue l'aggiornamento da un account di archiviazione Standard a un account di archiviazione Premium? Come si effettua il downgrade da un account di archiviazione Premium a un account di archiviazione Standard?**

È necessario creare l'account di archiviazione di destinazione, copiare i dati dall'account di origine a quello di destinazione e quindi eliminare l'account di origine. Per eseguire la copia dei dati, è possibile usare uno strumento come AzCopy.

Se si dispone di macchine virtuali, è necessario completare alcuni passaggi aggiuntivi prima di migrare i dati dell'account di archiviazione. Per altre informazioni, vedere [Migrazione in Archiviazione Premium di Azure (dischi non gestiti)](storage-migration-to-premium-storage.md).

**Come si passa da un account di archiviazione classico a un account di archiviazione di Azure Resource Manager?**

È possibile usare il cmdlet **Move-AzureStorageAccount**. Questo cmdlet ha più passaggi (convalida, preparazione, commit). È possibile convalidare lo spostamento prima di eseguirlo.

Se si dispone di macchine virtuali, è necessario completare alcuni passaggi aggiuntivi prima di migrare i dati dell'account di archiviazione. Per altre informazioni, vedere [Eseguire la migrazione di risorse IaaS dal modello classico al modello di Azure Resource Manager tramite Azure PowerShell](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md).

**Come è possibile scaricare i dati in un computer basato su Linux da un account di archiviazione di Azure oppure caricarli da un computer Linux?**

È possibile usare l'interfaccia della riga di comando di Azure.

- Scaricare un singolo BLOB:

      azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

- Caricare un singolo BLOB: 

      azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Come si concede ad altri utenti l'accesso alle risorse di archiviazione?**

Per concedere ad altri utenti l'accesso alle risorse di archiviazione:

-   Usare un token di firma di accesso condiviso (SAS) per consentire l'accesso a una risorsa. 

-   Fornire a un utente la chiave primaria o secondaria per l'account di archiviazione. Per altre informazioni, vedere [Gestire l'account di archiviazione](storage-create-storage-account.md#manage-your-storage-account).

-   Modificare i criteri di accesso per consentire l'accesso anonimo. Per altre informazioni, vedere [Concedere le autorizzazioni agli utenti anonimi per contenitori e BLOB](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

**Dove è installato AzCopy?**

-   Se si accede ad AzCopy dalla riga di comando di Archiviazione di Microsoft Azure, digitare **AzCopy**. La riga di comando viene installata insieme ad AzCopy.

-   Se è stata installata la versione a 32 bit, sarà disponibile qui: **%ProgramFiles(x86)%\\Microsoft SDKs\\Azure\\AzCopy**.

-   Se è stata installata la versione a 64 bit, sarà disponibile qui: **%ProgramFiles%\\Microsoft SDKs\\Azure\\AzCopy**.

**Per un account di archiviazione replicato (ad esempio l'archiviazione con ridondanza della zona, l'archiviazione con ridondanza geografica o l'archiviazione con ridondanza geografica e accesso in lettura), come è possibile accedere ai dati archiviati nell'area secondaria?**

-   Se si usa l'archiviazione con ridondanza della zona o l'archiviazione con ridondanza geografica, non è possibile accedere ai dati nell'area secondaria a meno che non si verifichi un failover. Per altre informazioni sul processo di failover, vedere [Cosa aspettarsi se si verifica un failover dell'archiviazione](storage-disaster-recovery-guidance.md#what-to-expect-if-a-storage-failover-occurs).

-   Se si usa l'archiviazione con ridondanza geografica e accesso in lettura, è possibile accedere ai dati dell'area secondaria in qualsiasi momento. Usare uno dei metodi seguenti:  
      
    - **AzCopy**: aggiungere **-secondary** al nome dell'account di archiviazione nell'URL per accedere all'endpoint secondario. ad esempio:  
     
      https://nomeaccountarchiviazione-secondary.blob.core.windows.net/vhds/BlobName.vhd

    - **Token SAS**: usare un token di firma di accesso condiviso (SAS) per accedere ai dati dall'endpoint. Per altre informazioni, vedere [Uso delle firme di accesso condiviso](storage-dotnet-shared-access-signature-part-1.md).

**Come si usa un dominio personalizzato HTTPS con l'account di archiviazione? Come è possibile, ad esempio, fare in modo che "https://nomeaccountarchiviazione.blob.core.windows.net/images/image.gif" venga visualizzato come "https://www.contoso.com/images/image.gif"?**

Gli account di archiviazione con domini personalizzati attualmente non supportano l'autenticazione SSL.
È però possibile usare domini personalizzati non HTTPS. Per altre informazioni, vedere [Configurare un nome di dominio personalizzato per l'endpoint di archiviazione BLOB](../blobs/storage-custom-domain-name.md).

**Come si usa il protocollo FTP per accedere ai dati che si trovano in un account di archiviazione?**

Non è possibile accedere direttamente a un account di archiviazione usando FTP. È però possibile configurare una macchina virtuale di Azure e quindi installare un server FTP nella macchina virtuale. È possibile fare in modo che il server FTP archivi i file in una condivisione di File di Azure o in un disco dati disponibile per la macchina virtuale.

Se si vuole solo scaricare i dati senza dover usare Storage Explorer o un'applicazione simile, potrebbe essere necessario usare un token di firma di accesso condiviso (SAS). Per altre informazioni, vedere [Uso delle firme di accesso condiviso](storage-dotnet-shared-access-signature-part-1.md).

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.

Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
