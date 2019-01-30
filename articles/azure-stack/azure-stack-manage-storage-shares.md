---
title: Gestire la capacità di archiviazione in Azure Stack | Microsoft Docs
description: Monitorare e gestire lo spazio di archiviazione disponibili per Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 96145906d40e465d2427a8100b3ad9333eec3f29
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55249096"
---
# <a name="manage-storage-capacity-for-azure-stack"></a>Gestire la capacità di archiviazione per Azure Stack 

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Le informazioni contenute in questo articolo consente il monitoraggio di operatore cloud di Azure Stack e gestire la capacità di archiviazione della loro distribuzione di Azure Stack. L'infrastruttura di archiviazione di Azure Stack consente di allocare un subset della capacità di archiviazione totale della distribuzione, Azure Stack da utilizzare per **servizi di archiviazione**. Servizi di archiviazione di archiviano i dati del tenant nelle condivisioni di volumi che corrispondono ai nodi della distribuzione.

Come operatore di cloud, si dispone di una quantità limitata di spazio di archiviazione da usare. La quantità di spazio di archiviazione viene definita dalla soluzione di implementare. La soluzione viene fornita dal fornitore OEM quando si usa una soluzione a più nodi, o dall'hardware in cui si installa Azure Stack Development Kit.

Perché Azure Stack non supporta l'espansione della capacità di archiviazione, è importante [monitor](#monitor-shares) dello spazio di archiviazione per assicurare il funzionamento efficiente vengono mantenute.  

Quando la capacità residua gratuita di una condivisione è più limitata, pianificare [gestire lo spazio](#manage-available-space) per impedire l'esecuzione esaurisce la capacità di condivisioni.

Le opzioni per gestire la capacità includono:
- Recuperare la capacità
- Eseguire la migrazione di un contenitore

Quando una condivisione è 100% di utilizzo, la risorsa di archiviazione del servizio non sono più funzioni per tale condivisione. Per ottenere assistenza per il ripristino di operazioni per la condivisione, contattare il supporto tecnico Microsoft.

## <a name="understand-volumes-and-shares-containers-and-disks"></a>Comprendere i volumi e condivisioni, contenitori e i dischi
### <a name="volumes-and-shares"></a>Condivisioni e volumi
Il *servizio di archiviazione* suddivide lo spazio di archiviazione disponibile in volumi separati ed è uguali allocate per contenere i dati del tenant. Il numero di volumi è uguale al numero di nodi nella distribuzione di Azure Stack:

- In una distribuzione di quattro nodi, sono previsti quattro volumi. Ogni volume dispone di una singola condivisione. Se un nodo viene rimosso o non funziona correttamente in una distribuzione a più nodi, il numero di condivisioni non viene ridotto.
- Se si utilizza il Kit per sviluppatori di Azure Stack, è presente un singolo volume con una singola condivisione.

Poiché le condivisioni del servizio di archiviazione sono per l'utilizzo esclusivo dei servizi di archiviazione, è necessario non direttamente modificare, aggiungere o rimuovere tutti i file nelle condivisioni. Solo i servizi di archiviazione dovrebbero funzionare per i file archiviati in tali volumi.

Le condivisioni nei volumi contengono i dati del tenant. I dati del tenant includono i BLOB di pagine, i BLOB in blocchi, accodare BLOB, tabelle, code, i database e gli archivi di metadati correlato. Poiché gli oggetti di archiviazione (BLOB e così via) singolarmente sono contenuti all'interno di una singola condivisione, la dimensione massima di ogni oggetto non può superare le dimensioni di una condivisione. Le dimensioni massime di nuovi oggetti dipendono dalla capacità che rimane in una condivisione di spazio inutilizzato quando viene creato il nuovo oggetto.

Quando una condivisione è meno intensa in spazio disponibile e le azioni [occupata](#reclaim-capacity) lo spazio non è riuscita o disponibile, l'operatore di cloud di Azure Stack può [eseguire la migrazione](#migrate-a-container-between) i contenitori di blob da una condivisione a un altro.

- Per altre informazioni sui contenitori e BLOB, vedere [archiviazione Blob](azure-stack-key-features.md#blob-storage) nella funzionalità chiave e concetti di Azure Stack.
- Per informazioni sul funzionano di utenti del tenant con un archivio blob in Azure Stack, vedere [servizi di archiviazione di Azure Stack](/azure/azure-stack/user/azure-stack-storage-overview#azure-stack-storage-services).


### <a name="containers"></a>Contenitori
Gli utenti tenant creare contenitori che vengono quindi usati per archiviare dati blob. Mentre l'utente decide contenitore in cui inserire i BLOB, il servizio di archiviazione Usa un algoritmo per determinare su quale volume in cui salvare il contenitore. L'algoritmo è in genere sceglie il volume con più spazio disponibile.  

Dopo che un blob viene inserito in un contenitore, tale blob può iniziare a utilizzare più spazio. Man mano che si aggiungono nuovi BLOB e con i BLOB di aumento delle dimensioni, lo spazio disponibile nel volume che contiene tale contenitore si riduce.  

I contenitori non sono limitati a una singola condivisione. Quando i dati blob combinati in un contenitore cresce usare l'80% o più lo spazio disponibile, il contenitore entra *overflow* modalità. In modalità di overflow, eventuali nuovi BLOB creati in tale contenitore vengono allocate in un volume diverso che vi sia spazio sufficiente. Nel corso del tempo, un contenitore in modalità di overflow può avere i blob che vengono distribuiti tra più volumi.

Quando viene usato 80% e quindi il 90% dello spazio disponibile in un volume, il sistema genera avvisi nel portale di amministrazione di Azure Stack. Gli operatori cloud devono esaminare la capacità di archiviazione disponibile e prevede di ribilanciare il contenuto. Il servizio di archiviazione smette di funzionare quando un disco è usata al 100%, e non altri avvisi vengono generati.

### <a name="disks"></a>Dischi
Dischi delle macchine Virtuali vengono aggiunte a contenitori dai tenant e includono un disco del sistema operativo. Le macchine virtuali possono anche avere uno o più dischi dati. Entrambi i tipi di dischi vengono archiviati come BLOB di pagine. Le indicazioni fornite per i tenant consiste nell'inserire ogni disco in un contenitore separato per migliorare le prestazioni della macchina virtuale.
- Ogni contenitore che include un disco dati (blob di pagine) da una macchina virtuale viene considerato un contenitore collegato alla macchina virtuale che è proprietario del disco.
- Un contenitore che non dispone di un disco da una macchina virtuale viene considerato un contenitore disponibile.

Le opzioni per liberare spazio in un contenitore collegato [sono limitati](#move-vm-disks).
> [!TIP]  
> Gli operatori cloud non si gestiscono direttamente, i dischi collegati a macchine virtuali (VM) che i tenant potrebbero aggiungere a un contenitore. Tuttavia, quando si intende gestire spazio in condivisioni di archiviazione, può essere d'uso per comprendere come dischi si riferiscono a contenitori e condivisioni.

## <a name="monitor-shares"></a>Condivisioni di monitoraggio
Usare PowerShell o il portale di amministrazione per monitorare le condivisioni, così sarà possibile capire quando lo spazio disponibile è limitato. Quando si usa il portale, si ricevere avvisi sulle condivisioni che su spazio sono insufficiente.    

### <a name="use-powershell"></a>Usare PowerShell
Come operatore di cloud, è possibile monitorare la capacità di archiviazione di una condivisione tramite PowerShell **Get-AzsStorageShare** cmdlet. Il cmdlet Get-AzsStorageShare restituisce lo spazio totale allocato e gratuito in byte in ognuna delle condivisioni.   
![Esempio: Restituire lo spazio disponibile per le condivisioni](media/azure-stack-manage-storage-shares/free-space.png)

- **Capacità totale** è lo spazio totale in byte che sono disponibili per la condivisione. Questo spazio viene usato per i dati e metadati gestiti da servizi di archiviazione.
- **Capacità usata** è la quantità di dati in byte che viene usato da tutti gli extent dai file di cui sono archiviati i dati dei tenant e i metadati associati.

### <a name="use-the-administrator-portal"></a>Usare il portale dell'amministratore
Come operatore di cloud, è possibile utilizzare il portale di amministrazione per visualizzare la capacità di archiviazione di tutte le condivisioni.

1. Accedi per il [del portale di amministrazione](https://adminportal.local.azurestack.external).
2. Selezionare **tutti i servizi** > **archiviazione** per aprire l'elenco di condivisione file in cui è possibile visualizzare le informazioni sull'utilizzo. 

  ![Esempio: Condivisioni file di archiviazione](media/azure-stack-manage-storage-shares/storage-file-shares.png)

  - **TOTALE** è lo spazio totale in byte che sono disponibili per la condivisione. Questo spazio viene usato per i dati e metadati gestiti da servizi di archiviazione.
  - **UTILIZZATO** è la quantità di dati in byte che viene usato da tutti gli extent dai file di cui sono archiviati i dati dei tenant e i metadati associati.

### <a name="storage-space-alerts"></a>Avvisi di spazio di archiviazione
Quando si usa il portale di amministrazione, si ricevere avvisi sulle condivisioni che su spazio sono insufficiente.

> [!IMPORTANT]
> Come operatore di cloud, mantenere le condivisioni di raggiungere l'utilizzo completo. Quando una condivisione è 100% di utilizzo, la risorsa di archiviazione del servizio non sono più funzioni per tale condivisione. Per recuperare spazio disponibile e ripristinare le operazioni in una condivisione che è usata al 100%, è necessario contattare il supporto tecnico Microsoft.

**Avviso**: Quando una condivisione file è superiore all'80% utilizzata, si riceve un *avviso* avviso nel portale di amministrazione: ![Esempio: Messaggio di avviso](media/azure-stack-manage-storage-shares/alert-warning.png)


**Critica**: Quando una condivisione file è oltre il 90% usato, si riceve un *critico* avviso nel portale di amministrazione: ![Esempio: Avviso critico](media/azure-stack-manage-storage-shares/alert-critical.png)

**Visualizzare i dettagli**: Nel portale di amministrazione è possibile aprire i dettagli per un avviso per visualizzare le opzioni di mitigazione: ![Esempio: Visualizzare i dettagli degli avvisi](media/azure-stack-manage-storage-shares/alert-details.png)


## <a name="manage-available-space"></a>Gestire lo spazio disponibile
Quando è necessario per liberare spazio in una condivisione, usare prima di tutto i metodi meno invasivi. Ad esempio, provare a recuperare lo spazio prima di scegliere di eseguire la migrazione di un contenitore.  

### <a name="reclaim-capacity"></a>Recuperare la capacità
*Questa opzione si applica a entrambe le distribuzioni a più nodi e Azure Stack Development Kit.*

È possibile recuperare la capacità usata dall'account di tenant che sono stati eliminati. Questa capacità è automaticamente recuperato quando i dati [periodo di conservazione](azure-stack-manage-storage-accounts.md#set-the-retention-period) viene raggiunto, oppure è possibile agire per liberare questo spazio immediatamente.

Per altre informazioni, vedere [recuperare la capacità](azure-stack-manage-storage-accounts.md#reclaim) nella gestione di risorse di archiviazione.

### <a name="migrate-a-container-between-volumes"></a>Eseguire la migrazione di un contenitore tra volumi
*Questa opzione si applica solo alle distribuzioni con più nodi.*

A causa di modelli di utilizzo di tenant, alcune condivisioni tenant usano più spazio rispetto ad altri utenti. Il risultato può essere una condivisione che in uno spazio prima altre condivisioni relativamente inutilizzate sia insufficiente.

È possibile provare a liberare spazio in una condivisione sovrautilizzata eseguendo manualmente la migrazione di alcuni contenitori di blob in un'altra condivisione. È possibile eseguire la migrazione di diversi contenitori più piccoli in una singola condivisione che dispone della capacità per contenere tutti. È possibile usare la migrazione per spostare *gratuita* contenitori. I contenitori gratuiti sono contenitori che non contengono un disco per una macchina virtuale.   

Migrazione consolida tutti un blob in contenitori nella nuova condivisione.

- Se un contenitore ha acceduto alla modalità di overflow e ha inserito i BLOB nei volumi aggiuntivi, la nuova condivisione deve avere una capacità sufficiente per contenere tutti i BLOB per il contenitore che si esegue la migrazione. Sono inclusi i blob che si trovano nelle condivisioni aggiuntive.

- Il cmdlet di PowerShell *Get-AzsStorageContainer* identifica solo lo spazio utilizzato nel volume iniziale per un contenitore. Il cmdlet non identifica lo spazio usato dai BLOB di mettere in altri volumi. Di conseguenza, le dimensioni di un contenitore completa potrebbero non essere evidente. È possibile che il consolidamento di un contenitore in una nuova condivisione può inviare tale nuova condivisione in una condizione di overflow in cui inserisce dati in condivisioni aggiuntive. Di conseguenza, potrebbe essere necessario ribilanciare le condivisioni nuovamente.

- Se si dispone delle autorizzazioni per un gruppo di risorse e non è possibile usare PowerShell per eseguire query sui volumi aggiuntivi per i dati di overflow, collaborare con il proprietario di tali gruppi di risorse e i contenitori per comprendere le dimensioni totali dei dati per eseguire la migrazione prima della migrazione di tali dati.  

> [!IMPORTANT]
> Migrazione di BLOB per un contenitore è un'operazione offline che richiede l'uso di PowerShell. Fino al completamento della migrazione, tutti i BLOB per il contenitore che si esegue la migrazione rimangano offline e non possono essere utilizzati. È anche consigliabile evitare l'aggiornamento di Azure Stack fino al completamento di tutto la migrazione in corso.

#### <a name="to-migrate-containers-using-powershell"></a>Per eseguire la migrazione dei contenitori tramite PowerShell
1. Verificare di avere [Azure PowerShell installato e configurato](https://azure.microsoft.com/documentation/articles/powershell-install-configure/). Per altre informazioni, vedere [Uso di Azure PowerShell con Gestione risorse di Azure](https://go.microsoft.com/fwlink/?LinkId=394767).
2.  Esaminare il contenitore per comprendere quali dati sono nella condivisione che si intende eseguire la migrazione. Per identificare i contenitori di candidati ottimali per la migrazione in un volume, usare il **Get-AzsStorageContainer** cmdlet:

    ```PowerShell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    $shares = Get-AzsStorageShare -FarmName $farm_name
    $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -FarmName $farm_name
    ```
    Esaminare quindi $containers:

    ```PowerShell
    $containers
    ```

    ![Esempio: $Containers](media/azure-stack-manage-storage-shares/containers.png)

3.  Identificare le condivisioni di destinazione migliore per contenere il contenitore che si esegue la migrazione:

    ```PowerShell
    $destinationshares = Get-AzsStorageShare -SourceShareName
    $shares[0].ShareName -Intent ContainerMigration
    ```

    Esaminare quindi $destinationshares:

    ```PowerShell 
    $destinationshares
    ```

    ![Esempio: $destination condivisioni](media/azure-stack-manage-storage-shares/examine-destinationshares.png)

4. Avviare la migrazione di un contenitore. La migrazione è asincrona. Se si avvia la migrazione dei contenitori aggiuntivi prima che venga completata la prima migrazione, usare l'id del processo per tenere traccia dello stato di ognuno.

  ```PowerShell
  $job_id = Start-AzsStorageContainerMigration -StorageAccountName $containers[0].Accountname -ContainerName $containers[0].Containername -ShareName $containers[0].Sharename -DestinationShareUncPath $destinationshares[0].UncPath -FarmName $farm_name
  ```

  Esaminare quindi $jobId. Nell'esempio seguente, sostituire *d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0* con l'id del processo che si desidera esaminare:

  ```PowerShell
  $jobId
  d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0
  ```

5. Usare l'id del processo per controllare lo stato del processo di migrazione. Al termine, la migrazione di contenitori **MigrationStatus** è impostata su **completa**.

  ```PowerShell 
  Get-AzsStorageContainerMigrationStatus -JobId $job_id -FarmName $farm_name
  ```

  ![Esempio: Stato di migrazione](media/azure-stack-manage-storage-shares/migration-status1.png)

6.  È possibile annullare un processo di migrazione in corso. Annullamento della migrazione dei processi vengono elaborati in modo asincrono. È possibile rilevare l'annullamento tramite $jobid:

  ```PowerShell
  Stop-AzsStorageContainerMigration -JobId $job_id -FarmName $farm_name
  ```

  ![Esempio: Stato rollback](media/azure-stack-manage-storage-shares/rollback.png)

7. È possibile eseguire nuovamente il comando del passaggio 6, fino a quando lo stato confermi che il processo di migrazione viene **Canceled**:  

    ![Esempio: Stato annullato](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>Spostare i dischi di macchina virtuale
*Questa opzione si applica solo alle distribuzioni con più nodi.*

Il metodo per gestire lo spazio più estremo comporta lo spostamento dei dischi delle macchine virtuali. Poiché lo spostamento di un contenitore collegato (uno che contiene un disco di macchina virtuale) è complesso, contattare il supporto tecnico Microsoft per completare l'azione.

## <a name="next-steps"></a>Fasi successive
Altre informazioni sulle [offerta di macchine virtuali agli utenti](azure-stack-tutorial-tenant-vm.md).
