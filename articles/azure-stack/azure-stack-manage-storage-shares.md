---
title: "Gestire la capacità di archiviazione nello Stack di Azure | Documenti Microsoft"
description: Monitorare e gestire lo spazio di archiviazione disponibile per lo Stack di Azure.
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: b0e694e4-3575-424c-afda-7d48c2025a62
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/14/2017
ms.author: brenduns
ms.reviewer: jiahan
ms.openlocfilehash: f305f6ca3c92824aeed8a3b04181cc87e34b5321
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2017
---
# <a name="manage-storage-capacity-for-azure-stack"></a>Gestire la capacità di archiviazione per lo Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Le informazioni contenute in questo articolo consente il monitoraggio di operatore cloud Stack di Azure e gestire la capacità di archiviazione della loro distribuzione dello Stack di Azure. L'infrastruttura di archiviazione di Azure Stack viene allocato un subset della capacità di archiviazione totale della distribuzione, Stack di Azure da utilizzare per **servizi di archiviazione**. Servizi di archiviazione di archiviano dati di un tenant in condivisioni nei volumi che corrispondono ai nodi della distribuzione.

Come operatore di cloud, si dispone di una quantità limitata di archiviazione da utilizzare. La quantità di spazio di archiviazione è definita dalla soluzione di implementare. La soluzione viene fornita dal fornitore OEM quando si utilizza una soluzione a più nodi, o dall'hardware in cui si installa il Kit di sviluppo dello Stack di Azure.

Poiché lo Stack di Azure non supporta l'espansione della capacità di archiviazione, è importante [monitoraggio](#monitor-shares) dello spazio di archiviazione per assicurare il funzionamento efficiente vengono mantenute.  

Quando la capacità residua disponibile di una condivisione è limitata, intende [gestire lo spazio](#manage-available-space) per impedire che le condivisioni di esaurire la capacità.

Le opzioni per gestire la capacità includono:
- Recuperare la capacità
- Eseguire la migrazione di un contenitore

Quando una condivisione è 100% utilizzato, lo spazio di archiviazione del servizio non è più funzioni per tale condivisione. Per ottenere assistenza in operazioni per la condivisione di ripristino, contattare il supporto tecnico Microsoft.

## <a name="understand-volumes-and-shares-containers-and-disks"></a>Comprendere i volumi e condivisioni, contenitori e i dischi
### <a name="volumes-and-shares"></a>Volumi e condivisioni
Il *servizio di archiviazione* partizioni dello spazio di archiviazione in volumi separati e uguali allocati per contenere i dati del tenant. Il numero di volumi è uguale al numero dei nodi nella distribuzione di Azure Stack:

- In una distribuzione a quattro nodi, sono disponibili quattro volumi. Ogni volume dispone di una singola condivisione. In una distribuzione a più nodi, il numero di condivisioni non viene ridotto se un nodo viene rimosso o non funziona correttamente.
- Se si utilizza il Kit di sviluppo dello Stack di Azure, è presente un solo volume con una singola condivisione.

Poiché le condivisioni del servizio di archiviazione per l'utilizzo esclusivo di servizi di archiviazione, è necessario non direttamente modificare, aggiungere o rimuovere i file nelle condivisioni di. Solo i servizi di archiviazione devono utilizzare i file archiviati in tali volumi.

Condivisioni nei volumi di archiviare dati di tenant. Dati del tenant includono i BLOB di pagine BLOB in blocchi, aggiungere BLOB, tabelle, code, i database e relativi archivi di metadati. Poiché gli oggetti di archiviazione (BLOB, e così via) singolarmente sono contenuti all'interno di una singola condivisione, la dimensione massima di ciascun oggetto non può superare le dimensioni di una condivisione. La dimensione massima di nuovi oggetti dipende dalle capacità che rimane in una condivisione di spazio inutilizzato quando viene creato il nuovo oggetto.

Quando una condivisione è bassa in spazio disponibile e le azioni da [recuperare](#reclaim-capacity) spazio non disponibile o ha esito positivo, l'operatore di cloud di Azure Stack può [migrare](#migrate-a-container-between) i contenitori di blob da una condivisione a un altro.

- Per ulteriori informazioni sui contenitori e BLOB, vedere [nell'archiviazione Blob](azure-stack-key-features.md#blob-storage) le funzionalità chiave e concetti nello Stack di Azure.
- Per informazioni sul funzionamento di utenti del tenant con archiviazione BLOB nello Stack di Azure, vedere [servizi di archiviazione di Azure Stack](/azure/azure-stack/user/azure-stack-storage-overview#azure-stack-storage-services).


### <a name="containers"></a>Contenitori
Gli utenti tenant creano contenitori che vengono quindi utilizzati per archiviare dati blob. Mentre l'utente decide di contenitore in cui inserire i BLOB, il servizio di archiviazione utilizza un algoritmo per determinare in quale volume di inserire il contenitore. L'algoritmo sceglie in genere il volume con più spazio disponibile.  

Dopo aver posizionato un blob in un contenitore, blob può iniziare a utilizzare più spazio. Quando si aggiungono nuovi BLOB ed esistente BLOB aumento delle dimensioni, lo spazio disponibile nel volume che contiene tale contenitore viene compattato.  

I contenitori non sono limitati a una singola condivisione. Quando i dati blob combinati in un contenitore diventa più di 80% di utilizzo dello spazio disponibile, il contenitore immette *overflow* modalità. In modalità di overflow, qualsiasi nuovo BLOB creati in tale contenitore sono allocati a un volume diverso che disponga di spazio sufficiente. Nel corso del tempo, un contenitore in modalità di overflow può avere blob che sono distribuite in più volumi.

Quando viene usato l'80% e il 90% dello spazio disponibile in un volume, il sistema genera avvisi nel portale di amministrazione di Azure Stack. Gli operatori di cloud devono esaminare la capacità di archiviazione disponibile e prevedere di ribilanciare il contenuto. Il servizio di archiviazione smette di funzionare quando un disco è 100% utilizzato e non esistono che alcun altri avvisi non vengono generati.

### <a name="disks"></a>Dischi
Dischi di macchina virtuale vengono aggiunti ai contenitori dai tenant e includono un disco del sistema operativo. Le macchine virtuali possono anche avere uno o più dischi dati. Entrambi i tipi di dischi vengono archiviati come BLOB di pagine. Le linee guida per i tenant consiste nell'inserire ogni disco in un contenitore separato per migliorare le prestazioni della macchina virtuale.
- Ogni contenitore che contiene un disco (blob di pagine) da una macchina virtuale viene considerato un contenitore collegato alla macchina virtuale che è proprietario del disco.
- Un contenitore che non contiene alcun disco da una macchina virtuale viene considerato un contenitore disponibile.

Le opzioni per liberare spazio in un contenitore collegato [sono limitati](#move-vm-disks).
> [!TIP]  
> Gli operatori di cloud non gestiscono direttamente, i dischi collegati alle macchine virtuali (VM) che i tenant è potrebbero aggiungere a un contenitore. Quando si intende gestire lo spazio nelle condivisioni di archiviazione, tuttavia, può essere utile per comprendere come dischi riguardano contenitori e le condivisioni.

## <a name="monitor-shares"></a>Azioni di monitoraggio
Utilizzare PowerShell o il portale di amministrazione per monitorare le condivisioni in modo da comprendere quando lo spazio disponibile è limitato. Quando si usa il portale, si ricevere avvisi sulle condivisioni che lo spazio sono insufficiente.    

### <a name="use-powershell"></a>Usare PowerShell
Come operatore di cloud, è possibile monitorare la capacità di archiviazione di una condivisione con PowerShell **Get AzsStorageShare** cmdlet. Il cmdlet Get-AzsStorageShare restituisce lo spazio allocato, disponibile e totale in byte in ognuna delle azioni.   
![Esempio: Restituire lo spazio disponibile per le condivisioni](media/azure-stack-manage-storage-shares/free-space.png)

- **Capacità totale** è lo spazio totale in byte che sono disponibili per la condivisione. Questo spazio viene utilizzato per i dati e metadati gestiti da servizi di archiviazione.
- **Capacità utilizzata** è la quantità di dati in byte che viene utilizzato da tutti gli extent dal file che memorizzano i dati del tenant e i metadati associati.

### <a name="use-the-administrator-portal"></a>Utilizzare il portale dell'amministratore
Come operatore di cloud, è possibile utilizzare il portale di amministrazione per visualizzare la capacità di archiviazione di tutte le condivisioni. **Passare all'archiviazione** > **condivisioni File** per aprire l'elenco di condivisione file in cui è possibile visualizzare le informazioni sull'utilizzo.
![Esempio: Condivisioni di file di archiviazione](media/azure-stack-manage-storage-shares/storage-file-shares.png)
- **TOTALE** è lo spazio totale in byte che sono disponibili per la condivisione. Questo spazio viene utilizzato per i dati e metadati gestiti da servizi di archiviazione.
- **UTILIZZATO** è la quantità di dati in byte che viene utilizzato da tutti gli extent dal file che memorizzano i dati del tenant e i metadati associati.

### <a name="storage-space-alerts"></a>Avvisi di spazio di archiviazione
Quando si usa il portale di amministrazione, si ricevere avvisi sulle condivisioni che lo spazio sono insufficiente.

> [!IMPORTANT]
> Un operatore di cloud, la conservazione condivisioni raggiungano l'utilizzo completo. Quando una condivisione è 100% utilizzato, lo spazio di archiviazione del servizio non è più funzioni per tale condivisione. Per recuperare spazio libero e ripristinare le operazioni in una condivisione utilizzata al 100%, è necessario contattare il supporto tecnico Microsoft.

**Avviso**: quando una condivisione file è superiore all'80% utilizzata, viene visualizzato un *avviso* avvisi nel portale di amministrazione: ![esempio: messaggio di avviso](media/azure-stack-manage-storage-shares/alert-warning.png)


**Critico**: quando una condivisione file è superiore a 90% utilizzato, viene visualizzato un *critico* avvisi nel portale di amministrazione: ![esempio: avviso critico](media/azure-stack-manage-storage-shares/alert-critical.png)

**Visualizzare i dettagli**: nel portale di amministrazione è possibile aprire i dettagli di un avviso visualizzare le opzioni di attenuazione: ![esempio: visualizzare i dettagli degli avvisi](media/azure-stack-manage-storage-shares/alert-details.png)


## <a name="manage-available-space"></a>Gestire lo spazio disponibile
Quando è necessario per liberare spazio in una condivisione, utilizzare i metodi meno invasivi prima. Ad esempio, provare a recuperare lo spazio prima di scegliere di eseguire la migrazione di un contenitore.  

### <a name="reclaim-capacity"></a>Recuperare la capacità
*Questa opzione si applica a entrambe le distribuzioni a più nodi e il Kit di sviluppo dello Stack di Azure.*

È possibile recuperare la capacità utilizzata dagli account tenant che sono stati eliminati. Questa capacità viene automaticamente recuperato quando i dati [periodo di memorizzazione](azure-stack-manage-storage-accounts.md#set-the-retention-period) viene raggiunto, oppure è possibile agire per recuperarlo immediatamente.

Per ulteriori informazioni, vedere [recuperare capacità](azure-stack-manage-storage-accounts.md#reclaim) in Gestione risorse di archiviazione.

### <a name="migrate-a-container-between-volumes"></a>Eseguire la migrazione di un contenitore tra volumi
*Questa opzione si applica solo alle distribuzioni a più nodi.*

Modelli di utilizzo di tenant, a causa di alcune condivisioni tenant usare più spazio rispetto ad altri. Il risultato può essere una condivisione di insufficiente spazio prima di altre condivisioni relativamente inutilizzate.

È possibile provare a liberare spazio in una condivisione sovrautilizzata eseguendo manualmente la migrazione di alcuni contenitori di blob in una condivisione diversa. È possibile migrare diversi contenitori più piccoli in un'unica condivisione che ha la capacità per contenere tutti. È possibile utilizzare la migrazione per spostare *libero* contenitori. Contenitori liberi sono contenitori che non contengono un disco per una macchina virtuale.   

Migrazione consolida tutte un blob in contenitori nella condivisione di nuovo.

- Se un contenitore è in modalità overflow e ha inserito i BLOB nei volumi aggiuntivi, la nuova condivisione deve avere una capacità sufficiente a contenere tutti i BLOB per il contenitore in cui che si esegue la migrazione. Sono inclusi i blog che si trovano su condivisioni aggiuntive.

- Il cmdlet PowerShell *Get AzsStorageContainer* identifica solo lo spazio utilizzato nel volume iniziale per un contenitore. Il cmdlet non identifica lo spazio utilizzato dal BLOB di mettere in volumi aggiuntivi. Pertanto, la dimensione totale di un contenitore potrebbe non essere evidente. È possibile che il consolidamento di un contenitore in una nuova condivisione può inviare tale nuova condivisione in una condizione di overflow in cui inserisce dati in condivisioni aggiuntive. Di conseguenza, potrebbe essere necessario ribilanciare condivisioni nuovamente.

- Se si dispone delle autorizzazioni per un gruppo di risorse e non è possibile utilizzare PowerShell per eseguire una query altri volumi di dati di overflow, lavorare con il proprietario di tali gruppi di risorse e di contenitori per comprendere le dimensioni totali dei dati per eseguire la migrazione prima della migrazione di tali dati.  

> [!IMPORTANT]
> Migrazione di oggetti BLOB per un contenitore è un'operazione offline che richiede l'utilizzo di PowerShell. Fino al completamento della migrazione, tutti i BLOB per il contenitore in cui che si esegue la migrazione rimangono offline e non possono essere utilizzati.

#### <a name="to-migrate-containers-using-powershell"></a>Per eseguire la migrazione di contenitori tramite PowerShell
1. Verificare di avere [Azure PowerShell installato e configurato](http://azure.microsoft.com/documentation/articles/powershell-install-configure/). Per altre informazioni, vedere [Uso di Azure PowerShell con Gestione risorse di Azure](http://go.microsoft.com/fwlink/?LinkId=394767).
2.  Esaminare il contenitore per capire quali dati si trovano la condivisione che si intende eseguire la migrazione. Per identificare i contenitori di candidati ottimali per la migrazione in un volume, utilizzare il **Get AzsStorageContainer** cmdlet:

    ```
    $shares = Get-AzsStorageShare
    $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -Intent Migration
    ```
    Esaminare quindi $containers:
    ```
    $containers
    ```
    ![Esempio: $Containers](media/azure-stack-manage-storage-shares/containers.png)

3.  Identificare le condivisioni di destinazione migliore per contenere il contenitore in cui che si esegue la migrazione:
    ```
    $destinationshares = Get-AzsStorageShare -SourceShareName
    $shares[0].ShareName -Intent ContainerMigration
    ```
    Esaminare quindi $destinationshares:
    ```
    $destinationshares
    ```    
    ![Esempio: $destination condivisioni](media/azure-stack-manage-storage-shares/examine-destinationshares.png)

4. Avvia la migrazione di un contenitore. La migrazione è asincrona. Se si avvia la migrazione di altri contenitori prima che venga completata la migrazione prima, utilizzare l'id di processo per tenere traccia dello stato di ogni.
  ```
  $jobId = Start-AzsStorageContainerMigration -ContainerToMigrate $containers[1] -DestinationShareUncPath $destinationshares[0].UncPath
  ```
  Esaminare quindi $jobId. Nell'esempio seguente, sostituire *d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0* con l'id di processo che si desidera esaminare:
  ```
  $jobId
  d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0
  ```
5. Utilizzare l'id di processo per verificare lo stato del processo di migrazione. Al termine, la migrazione di contenitore **MigrationStatus** è impostato su **completa**.
  ```
  Get-AzsStorageContainerMigrationStatus -JobId $jobId
  ```
  ![Esempio: Stato della migrazione](media/azure-stack-manage-storage-shares/migration-status1.png)

6.  È possibile annullare un processo di migrazione in corso. Annullare la migrazione, i processi vengono elaborati in modo asincrono. È possibile tenere traccia di annullamento tramite $jobid:

  ```
  Stop-AzsStorageContainerMigration -JobId $jobId
  ```
  ![Esempio: Lo stato di ripristino](media/azure-stack-manage-storage-shares/rollback.png)

7. È possibile eseguire nuovamente il comando al passaggio 6, fino a quando lo stato di conferma è il processo di migrazione **Canceled**:  
    ![Esempio: Stato annullato](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>Spostare i dischi di macchina virtuale
*Questa opzione si applica solo alle distribuzioni a più nodi.*

Il metodo per gestire lo spazio più estremo comporta lo spostamento di dischi di macchina virtuale. Poiché lo spostamento di un contenitore collegato (uno che contiene un disco di macchina virtuale) è complesso, contattare il supporto Microsoft per completare l'azione.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni, vedere [offerta di macchine virtuali per gli utenti](azure-stack-tutorial-tenant-vm.md).
