---
title: Informazioni sulla suddivisione in livelli nel cloud di Sincronizzazione file di Azure | Microsoft Docs
description: Informazioni sulla funzionalità di suddivisione in livelli nel cloud di Sincronizzazione file di Azure
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 74887e6ee4656091aa647b481bc406dcc23b9c12
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460083"
---
# <a name="cloud-tiering-overview"></a>Panoramica della suddivisione in livelli nel cloud
La suddivisione in livelli nel cloud è una funzionalità facoltativa di Sincronizzazione file di Azure in base alla quale i file a cui si accede di frequente vengono memorizzati nella cache locale del server, mentre tutti gli altri file vengono archiviati a livelli in File di Azure in base alle impostazioni dei criteri. Quando un file è archiviato a livelli, il filtro del file system di Sincronizzazione file di Azure (StorageSync.sys) sostituisce il file in locale con un puntatore, o punto di analisi. Il punto di analisi rappresenta un URL del file in File di Azure. Un file archiviato a livelli include sia l'attributo "offline" sia l'attributo FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS impostato in NTFS, in modo che le applicazioni di terze parti possano identificare in modo sicuro questo tipo di file.
 
Quando un utente apre un file a livelli, Sincronizzazione file di Azure richiama facilmente i dati del file da File di Azure senza che l'utente debba essere a conoscenza che il file è archiviato in Azure. 
 
 > [!Important]  
 > La suddivisione in livelli nel cloud non è supportata nel volume di sistema Windows.
    
 > [!Important]  
 > Per richiamare i file a livelli, la larghezza di banda della rete deve essere di almeno 1 Mbps. Se la larghezza di banda di rete è inferiore a 1 Mbps, i file potrebbero non richiamarsi con un errore di timeout.

## <a name="cloud-tiering-faq"></a>Domande frequenti sulla suddivisione in livelli nel cloud

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Come funziona la suddivisione in livelli nel cloud?
Il filtro di sistema di Sincronizzazione file di Azure genera una "mappa termica" dello spazio dei nomi in ogni endpoint server. Monitora gli accessi (operazioni di lettura e scrittura) nel tempo e quindi, in base alla frequenza e alla recency di accesso, assegna un punteggio heat a ogni file. Un file usato frequentemente e aperto di recente viene considerato ad accesso frequente, mentre un file aggiornato appena e che non è stato usato per un determinato periodo di tempo viene considerato ad accesso sporadico. Quando il volume dei file in un server supera la soglia di spazio disponibile impostata, i file ad accesso molto sporadico vengono archiviati a livelli in File di Azure fino a quando non viene raggiunta la percentuale di spazio disponibile specificata.

Inoltre, è possibile specificare un criterio di data su ogni endpoint server che effettuerà il livello di tutti i file a cui non si accede entro un numero specificato di giorni, indipendentemente dalla capacità di archiviazione locale disponibile. Si tratta di una scelta ottimale per liberare in modo proattivo lo spazio su disco locale se si è certi che i file in tale endpoint server non devono essere conservati localmente oltre una determinata età. Questo consente di liberare la capacità del disco locale utile per altri endpoint nello stesso volume, per memorizzare nella cache più file.

Il mappa termica di suddivisione in livelli cloud è essenzialmente un elenco ordinato di tutti i file sincronizzati e si trovano in una posizione in cui è abilitata la suddivisione in livelli nel cloud. Per determinare la posizione relativa di un singolo file in tale mappa termica, il sistema usa il numero massimo di uno dei timestamp seguenti, in questo ordine: MAX (ora dell'ultimo accesso, ora dell'Ultima modifica, ora di creazione). In genere, l'ora dell'ultimo accesso viene rilevata e disponibile. Tuttavia, quando viene creato un nuovo endpoint server, con la suddivisione in livelli nel cloud abilitata, inizialmente non è stato sufficiente tempo per osservare l'accesso ai file. In assenza di una data e ora dell'ultimo accesso, l'ora dell'Ultima modifica viene utilizzata per valutare la posizione relativa in mappa termica. Lo stesso fallback è applicabile ai criteri relativi alla data. Senza l'ora dell'ultimo accesso, il criterio di data agirà sull'ora dell'Ultima modifica. Se non è disponibile, verrà eseguito il fallback all'ora di creazione di un file. Nel corso del tempo, il sistema osserverà sempre più richieste di accesso ai file e pivot per usare in modo predominante l'ora dell'ultimo accesso con rilevamento automatico.

La suddivisione in livelli cloud non dipende dalla funzionalità NTFS per il rilevamento dell'ora dell'ultimo accesso. Questa funzionalità NTFS è disattivata per impostazione predefinita e, a causa delle considerazioni sulle prestazioni, non è consigliabile abilitare manualmente questa funzionalità. La suddivisione in livelli nel cloud tiene traccia dell'ora dell'ultimo accesso separatamente ed efficiente.

<a id="tiering-minimum-file-size"></a>
### <a name="what-is-the-minimum-file-size-for-a-file-to-tier"></a>Qual è la dimensione minima del file per un file a livello?

Per gli agenti versione 9 e successive, le dimensioni minime del file per un file a livello sono basate sulle dimensioni del cluster file system. Le dimensioni minime dei file idonee per la suddivisione in livelli nel cloud vengono calcolate in base alla dimensione del cluster e a un minimo di 8 KB. La tabella seguente illustra le dimensioni minime dei file che possono essere suddivise a livelli, in base alle dimensioni del cluster di volumi:

|Dimensioni cluster del volume (byte) |I file di questa dimensione o di dimensioni maggiori possono essere a livelli  |
|----------------------------|---------|
|4 KB o inferiore (4096)      | 8 KB    |
|8 KB (8192)                 | 16 KB   |
|16 KB (16384)               | 32 KB   |
|32 KB (32768)               | 64 KB   |
|64 KB (65536)               | 128 KB  |

Con Windows Server 2019 e Sincronizzazione file di Azure Agent versione 12 e successive, sono supportate anche le dimensioni del cluster fino a 2 MB e la suddivisione in livelli delle dimensioni del cluster più grandi funziona allo stesso modo. Le versioni precedenti del sistema operativo o dell'agente supportano dimensioni del cluster fino a 64 KB.

Tutti i file System usati da Windows, organizzano il disco rigido in base alle dimensioni del cluster, note anche come dimensioni dell'unità di allocazione. Dimensioni del cluster rappresenta la quantità minima di spazio su disco che può essere usata per contenere un file. Quando le dimensioni dei file non vengono riportate a un multiplo pari delle dimensioni del cluster, è necessario usare spazio aggiuntivo per contenere il file fino al multiplo successivo delle dimensioni del cluster.

Sincronizzazione file di Azure è supportato nei volumi NTFS con Windows Server 2012 R2 e versioni successive. Nella tabella seguente vengono descritte le dimensioni predefinite del cluster quando si crea un nuovo volume NTFS. 

|Dimensioni del volume    |Windows Server 2012R2 e versioni successive |
|---------------|---------------|
|7 MB – 16 TB   | 4 KB          |
|16TB-32 TB   | 8 KB          |
|32 TB-64 TB   | 16 KB         |
|64 TB-128 TB  | 32 KB         |
|128TB-256 TB | 64 KB         |
|> 256 TB       | Non supportate |

Al momento della creazione del volume, è possibile che il volume sia stato formattato manualmente con dimensioni del cluster diverse. Se il volume deriva da una versione precedente di Windows, le dimensioni predefinite del cluster possono essere diverse. [Questo articolo contiene informazioni più dettagliate sulle dimensioni predefinite del cluster.](https://support.microsoft.com/help/140365/default-cluster-size-for-ntfs-fat-and-exfat) Anche se si sceglie una dimensione del cluster inferiore a 4 KB, viene applicato un limite di 8 KB come dimensione del file più piccola che può essere suddivisa a livelli. (Anche se le dimensioni del cluster tecnicamente 2x equivalgono a meno di 8 KB).

Il motivo del valore minimo assoluto è il modo in cui NTFS archivia i file di dimensioni molto ridotte da 1 KB a 4 KB. A seconda degli altri parametri del volume, è possibile che i file di piccole dimensioni non vengano archiviati in un cluster su disco. È possibilmente più efficiente archiviare tali file direttamente nella tabella dei file master del volume o nel "record MFT". Il punto di analisi a livelli cloud viene sempre archiviato su disco e occupa esattamente un cluster. La suddivisione in livelli di file di questo tipo può finire senza risparmiare spazio. Casi estremi possono anche finire usando più spazio con la suddivisione in livelli nel cloud abilitata. Per salvaguardarsi da tale dimensione, le dimensioni minime di un file a livello di suddivisione in livelli nel cloud sono pari a 8 KB in una dimensione del cluster di 4 KB o inferiore.

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>Come funzionano i criteri di suddivisione in livelli dello spazio disponibile nel volume?
Lo spazio disponibile nel volume è la quantità di spazio disponibile da riservare nel volume in cui si trova un endpoint server. Se ad esempio lo spazio disponibile in un volume con un endpoint server è impostato su 20%, fino all'80% dello spazio nel volume sarà occupato dai file usati più di recente, mentre gli eventuali file rimanenti che non rientrano in questo spazio verranno archiviati a livelli in Azure. Lo spazio disponibile nel volume si applica a livello di volume, non a livello di singoli gruppi di sincronizzazione o directory. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>Come funzionano i criteri di suddivisione in livelli dello spazio disponibile nel volume per quanto riguarda i nuovi endpoint server?
Quando un endpoint server di cui è stato appena effettuato il provisioning viene connesso a una condivisione file di Azure, il server recupera prima lo spazio dei nomi e quindi i file effettivi fino a quando non viene raggiunta la soglia di spazio disponibile nel volume. Questo processo è noto anche come ripristino di emergenza rapido o ripristino rapido dello spazio dei nomi.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>Come viene interpretato lo spazio disponibile nel volume quando in un volume sono presenti più endpoint server?
Quando in un volume sono presenti più endpoint server, la soglia valida dello spazio disponibile del volume è data dal valore più elevato specificato per tale spazio su qualsiasi endpoint server in tale volume. I file vengono suddivisi in livelli in base ai criteri di uso indipendentemente dall'endpoint server di appartenenza. Ad esempio, se sono presenti due endpoint server in un volume, Endpoint1 ed Endpoint2, dove Endpoint1 è associato a una soglia di spazio disponibile del volume pari a 25% mentre Endpoint2 è associato a una soglia di spazio disponibile del volume pari a 50%, la soglia valida per entrambi gli endpoint server sarà pari a 50%. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>Come funzionano i criteri di suddivisione in livelli in base alla data insieme ai criteri di suddivisione in livelli in base allo spazio disponibile nel volume? 
Quando si abilita il cloud a livelli in un endpoint server, si imposta un criterio di spazio disponibile nel volume, che ha sempre la precedenza su qualsiasi altro criterio, incluso il criterio di data. Facoltativamente, è possibile abilitare un criterio di data per ogni endpoint server in tale volume. Questo criterio gestisce che solo i file a cui si accede, ovvero letti o scritti, entro l'intervallo di giorni descritti da questo criterio verranno mantenuti locali. I file a cui non si accede con il numero di giorni specificato verranno suddivisi in livelli. 

La suddivisione in livelli cloud usa l'ora dell'ultimo accesso per determinare quali file devono essere a livelli. Il driver del filtro di suddivisione in livelli cloud (storagesync.sys) tiene traccia dell'ora dell'ultimo accesso e registra le informazioni nell'archivio termico di suddivisione in livelli nel cloud. È possibile visualizzare l'archivio di calore usando un cmdlet di PowerShell locale.

```powershell
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'
Get-StorageSyncHeatStoreInformation '<LocalServerEndpointPath>'
```

> [!IMPORTANT]
> Il timestamp dell'ultimo accesso non è una proprietà rilevata da NTFS e pertanto non è visibile per impostazione predefinita in Esplora file. Non usare l'ultimo timestamp modificato in un file per verificare se il criterio di data funziona come previsto. Questo timestamp tiene traccia solo delle Scritture, non delle letture. Usare il cmdlet illustrato per ottenere il timestamp dell'ultimo accesso per la valutazione.

> [!WARNING]
> Non attivare la funzionalità NTFS di rilevamento del timestamp dell'ultimo accesso per file e cartelle. Questa funzionalità è disattivata per impostazione predefinita perché ha un notevole effetto sulle prestazioni. Sincronizzazione file di Azure rileverà i tempi di accesso ultimo automaticamente e in modo molto efficiente e non utilizzerà questa funzionalità NTFS.

Tenere presente che i criteri di spazio disponibile del volume hanno sempre la precedenza e quando non è disponibile spazio sufficiente sul volume per conservare il numero di giorni per i file come descritto nel criterio relativo alla data, Sincronizzazione file di Azure continuerà a eseguire la suddivisione in livelli dei file più freddi fino a quando non viene soddisfatta la percentuale di spazio disponibile nel volume.

Supponiamo ad esempio di avere un criterio di suddivisione in livelli in base alla data impostato su 60 giorni e un criterio di suddivisione in livelli in base allo spazio disponibile nel volume impostato su 20%. Se dopo aver applicato i criteri di data, è presente meno del 20% di spazio disponibile nel volume, i criteri di spazio disponibile del volume vengono avviati ed eseguono l'override dei criteri di data. Come risultato, verrà archiviato a livelli un maggior numero di file, in modo che la quantità di dati mantenuti sul server possa essere ridotta da 60 giorni a 45 giorni di dati. Di contro, questo criterio forzerà l'archiviazione a livelli dei file che non rientrano nell'intervallo di tempo anche se non è stata raggiunta la soglia dello spazio disponibile, pertanto un file vecchio di 61 giorni verrà archiviato a livelli anche se il volume è vuoto.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>Come viene determinata la quantità appropriata di spazio disponibile nel volume?
La quantità di dati da mantenere in locale è determinata da una serie di fattori: la larghezza di banda, il modello di accesso del set di dati e il budget disponibile. Se si dispone di una connessione a banda ridotta, è possibile mantenere in locale una maggior quantità di dati per assicurarsi che il tempo di ritardo per gli utenti sia minimo. Altrimenti è possibile basarsi sulla percentuale di varianza durante un determinato periodo. Se ad esempio si sa che circa il 10% del set di dati da 1 TB viene modificato o viene attivamente usato ogni mese, può essere opportuno mantenere in locale 100 GB per evitare di richiamare i file frequentemente. Se il volume è di 2 TB, sarà opportuno mantenere in locale il 5% (pari a 100 GB) e il rimanente 95% sarà la percentuale di spazio disponibile nel volume. È tuttavia consigliabile aggiungere un buffer per tener conto di periodi di maggiore varianza. In altre parole, è consigliabile partire da una percentuale inferiore di spazio disponibile nel volume e quindi modificarla successivamente in caso di necessità. 

Tenere in locale una maggior quantità di dati significa ridurre i costi in uscita, dal momento che verrà richiamato da Azure un numero inferiore di file, ma implica anche dover mantenere più spazio di archiviazione in locale, che richiede costi aggiuntivi. Quando si ha un'istanza di Sincronizzazione file di Azure distribuita, è possibile esaminare l'uscita dell'account di archiviazione per misurare approssimativamente se le impostazioni dello spazio disponibile nel volume sono appropriate per l'utilizzo. Supponendo che l'account di archiviazione contenga solo l'endpoint cloud di Sincronizzazione file di Azure, ovvero la condivisione di sincronizzazione dell'utente, un elevato traffico in uscita indica che molti file vengono richiamati dal cloud e potrebbe essere opportuno aumentare la cache locale.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>Ho aggiunto un nuovo endpoint server. Quanto tempo deve trascorrere prima di vedere i file in questo livello server?

Il fatto che i file debbano o meno essere suddivisi in livelli per ogni criterio di set viene valutato una volta all'ora. Quando viene creato un nuovo endpoint server, è possibile riscontrare due situazioni:

1. Quando si aggiunge un nuovo endpoint server, spesso i file sono presenti nel percorso del server. Prima di iniziare la suddivisione in livelli cloud, è necessario caricarli per primi. Il criterio di spazio disponibile del volume non inizierà a funzionare fino al completamento del caricamento iniziale di tutti i file. Tuttavia, il criterio di data facoltativo inizierà a funzionare in base a un singolo file, non appena un file viene caricato. L'intervallo di un'ora si applica anche qui. 
2. Quando si aggiunge un nuovo endpoint server, è possibile connettere un percorso server vuoto a una condivisione file di Azure con i dati in esso contenuti. Indica se si tratta di un secondo server o durante una situazione di ripristino di emergenza. Se si sceglie di scaricare lo spazio dei nomi e di richiamare il contenuto durante il download iniziale nel server, dopo che lo spazio dei nomi si è arrestato, i file verranno richiamati in base al timestamp dell'Ultima modifica. Solo il numero di file verrà richiamato in base ai criteri di spazio libero del volume e ai criteri di data facoltativi.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>Come si può stabilire se un file è archiviato a livelli?
Esistono diversi modi per verificare se un file è archiviato a livelli in una condivisione file di Azure:
    
   *  **Controllare gli attributi del file nel file stesso.**
     Fare clic con il pulsante destro del mouse su un file, scegliere **Dettagli** e quindi scorrere verso il basso fino alla proprietà **Attributi**. Un file archiviato a livelli avrà il set di attributi seguente:     
        
        | Lettera di attributo | Attributo | Definizione |
        |:----------------:|-----------|------------|
        | Una | Archiviazione | Indica che deve essere eseguito un backup del file tramite il software di backup. Questo attributo è sempre impostato indipendentemente dal fatto che il file sia archiviato a livelli o archiviato completamente su disco. |
        | P | File sparse | Indica che il file è un file sparse. Un file sparse è un tipo specializzato di file offerto da NTFS per un uso efficiente quando il flusso di file su disco è pressoché vuoto. Sincronizzazione file di Azure usa i file sparse perché un file è completamente archiviato a livelli o parzialmente richiamato. In un file completamente archiviato a livelli, il flusso di file viene archiviato nel cloud. In un file parzialmente richiamato, tale parte del file è già su disco. Se un file è completamente richiamato su disco, Sincronizzazione file di Azure lo converte da file sparse in un file regolare. Questo attributo viene impostato solo in Windows Server 2016 e versioni precedenti.|
        | M | Richiama nell'accesso ai dati | Indica che i dati del file non sono completamente presenti nella risorsa di archiviazione locale. La lettura del file provocherà il recupero di almeno parte del contenuto del file da una condivisione file di Azure a cui è connesso l'endpoint server. Questo attributo è impostato solo in Windows Server 2019. |
        | L | Reparse point | Indica che il file contiene un reparse point. Un reparse point è un puntatore speciale utilizzabile da un filtro del file system. Sincronizzazione file di Azure usa i reparse point per definire la posizione nel cloud dove è archiviato il file per il filtro del file system di Sincronizzazione file di Azure (StorageSync.sys). È supportato l'accesso facile. Non è necessario che gli utenti sappiano che è in uso Sincronizzazione file di Azure o come ottenere l'accesso al file nella condivisione file di Azure. Quando un file viene richiamato completamente, Sincronizzazione file di Azure rimuove il reparse point dal file. |
        | O | Offline | Indica che il contenuto del file non è archiviato interamente o parzialmente su disco. Quando un file viene richiamato completamente, Sincronizzazione file di Azure rimuove questo attributo. |

        ![Finestra di dialogo Proprietà per un file con la scheda Dettagli selezionata](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        È possibile visualizzare gli attributi per tutti i file in una cartella aggiungendo il campo **Attributi** alla visualizzazione tabella di Esplora file. A tale scopo, fare clic con il pulsante destro del mouse su una colonna esistente (ad esempio, **Dimensioni**), scegliere **Altro** e quindi selezionare **Attributi** nell'elenco a discesa.
        
   * **Usare `fsutil` per verificare la presenza di reparse point in un file.**
       Come descritto per l'opzione precedente, per un file archiviato a più livelli è sempre impostato un reparse point. Un reparse point è un puntatore speciale per il filtro del file system di Sincronizzazione file di Azure (StorageSync.sys). Per verificare se un file contiene un reparse point, eseguire l'utilità `fsutil` in un prompt dei comandi con privilegi elevati o in una sessione di PowerShell:
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        Se il file contiene un reparse point, dovrebbe essere visualizzato **Reparse Tag Value: 0x8000001e** (Valore tag di reparse: 0x8000001e). Questo valore esadecimale è il valore del reparse point di proprietà di Sincronizzazione file di Azure. L'output contiene anche i dati di reparse che rappresentano il percorso del file nella condivisione file di Azure.

        > [!WARNING]  
        > Il comando dell'utilità `fsutil reparsepoint` offre anche la possibilità di eliminare un reparse point. Non eseguire questo comando, a meno che non venga richiesto dal team di progettazione di Sincronizzazione file di Azure. L'esecuzione di questo comando può causare la perdita di dati. 

<a id="afs-recall-file"></a>
### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>Un file da usare è stato archiviato a livelli. Come è possibile richiamare il file su disco per usarlo in locale?
Il modo più semplice per richiamare un file su disco è aprirlo. Il filtro del file system di Sincronizzazione file di Azure (StorageSync.sys) scarica automaticamente il file dalla condivisione file di Azure senza che sia necessario eseguire alcuna operazione. Per i tipi di file che possono essere letti parzialmente, ad esempio i file ZIP o multimediali, l'apertura di un file non comporta il download dell'intero file.

È anche possibile usare PowerShell per forzare il richiamo di un file. Questa opzione potrebbe essere utile per richiamare più file contemporaneamente, ad esempio tutti i file all'interno di una cartella. Aprire una sessione di PowerShell per il nodo server in cui è installato Sincronizzazione file di Azure e quindi eseguire i comandi di PowerShell seguenti:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Parametri facoltativi:
* `-Order CloudTieringPolicy`richiamerà prima i file modificati o a cui si accede più di recente ed è consentito dai criteri di suddivisione in livelli correnti. 
    * Se è configurato un criterio di spazio disponibile nel volume, i file verranno richiamati finché non viene raggiunta l'impostazione dei criteri di spazio libero del volume. Se, ad esempio, l'impostazione dei criteri volume Free è 20%, il richiamo verrà interrotto quando lo spazio disponibile nel volume raggiunge il 20%.  
    * Se lo spazio disponibile nel volume e i criteri di data sono configurati, i file verranno richiamati fino a quando non viene raggiunta l'impostazione di spazio disponibile del volume o data. Se, ad esempio, l'impostazione dei criteri di volume Free è 20% e il criterio relativo alla data è 7 giorni, il richiamo verrà interrotto quando lo spazio disponibile nel volume raggiunge il 20% oppure tutti i file a cui si accede o modificati entro 7 giorni sono locali.
* `-ThreadCount`determina il numero di file che possono essere richiamati in parallelo.
* `-PerFileRetryCount`determina la frequenza con cui viene eseguito il tentativo di richiamo di un file attualmente bloccato.
* `-PerFileRetryDelaySeconds`determina il tempo in secondi tra i tentativi per richiamare i tentativi e deve essere sempre utilizzato in combinazione con il parametro precedente.

Esempio:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
``` 

> [!Note]  
> - Il cmdlet Invoke-StorageSyncFileRecall può essere usato anche per migliorare le prestazioni di download dei file quando si aggiunge un nuovo endpoint server a un gruppo di sincronizzazione esistente.  
>- Se il volume locale che ospita il server non ha abbastanza spazio libero per richiamare tutti i dati archiviati a livelli, il cmdlet `Invoke-StorageSyncFileRecall` ha esito negativo.  

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Perché la proprietà *Dimensioni su disco* per un file non corrisponde alla proprietà *Dimensioni* dopo l'uso di Sincronizzazione file di Azure? 
Esplora file di Windows espone due proprietà per rappresentare le dimensioni di un file: **Dimensioni** e **Dimensioni su disco**. Queste proprietà hanno un significato leggermente diverso. **Dimensioni** rappresenta le dimensioni complete del file. **Dimensioni su disco** rappresenta le dimensioni del flusso di file archiviato sul disco. I valori di queste proprietà possono essere diversi per svariati motivi, ad esempio la compressione, l'uso della deduplicazione dei dati o la suddivisione in livelli nel cloud con Sincronizzazione file di Azure. Se un file è a livelli in una condivisione file di Azure, le dimensioni sul disco sono pari a zero perché il flusso di file viene archiviato nella condivisione file di Azure e non sul disco. È anche possibile che un file sia parzialmente a livelli (o parzialmente richiamato). In un file parzialmente a livelli, parte del file è su disco. Questa situazione può verificarsi quando i file vengono letti parzialmente da applicazioni come i lettori multimediali o le utilità di compressione. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Come è possibile forzare l'archiviazione a livelli di un file o una directory?

> [!NOTE]
> Quando si seleziona una directory a livelli, solo i file attualmente presenti nella directory vengono suddivisi in livelli. Tutti i file creati dopo tale periodo non vengono automaticamente suddivisi in livelli.

Quando la funzionalità di suddivisione in livelli nel cloud è abilitata, i file vengono archiviati automaticamente a livelli in base all'ora dell'ultimo accesso e dell'ultima modifica per ottenere la percentuale di spazio disponibile nel volume specificata nell'endpoint cloud. Tuttavia, a volte potrebbe essere necessario forzare manualmente l'archiviazione a livelli di un file. Ciò potrebbe essere utile se si salva un file di grandi dimensioni che non si intende riutilizzare per molto tempo e si vuole destinare lo spazio libero sul volume ad altri file e cartelle. È possibile forzare la suddivisione in livelli con i comandi seguenti di PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

<a id="afs-image-thumbnail"></a>
### <a name="why-are-my-tiered-files-not-showing-thumbnails-or-previews-in-windows-explorer"></a>Perché i file a livelli non visualizzano anteprime o anteprime in Esplora risorse?
Per i file a livelli, le anteprime e le anteprime non saranno visibili nell'endpoint server. Questo comportamento è previsto perché la funzionalità della cache delle anteprime in Windows ignora intenzionalmente la lettura dei file con l'attributo offline. Con la suddivisione in livelli nel cloud abilitata, la lettura tramite file a livelli ne determina il download (richiamata).

Questo comportamento non è specifico per Sincronizzazione file di Azure, in Esplora risorse viene visualizzata una "X grigia" per tutti i file in cui è impostato l'attributo offline. Quando si accede ai file tramite SMB, viene visualizzata l'icona X. Per una spiegazione dettagliata di questo comportamento, vedere[https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)

<a id="afs-tiering-disabled"></a>
### <a name="i-have-cloud-tiering-disabled-why-are-there-tiered-files-in-the-server-endpoint-location"></a>La suddivisione in livelli nel cloud è disabilitata perché sono presenti file a livelli nel percorso dell'endpoint server?

Esistono due motivi per cui i file a livelli possono esistere nel percorso dell'endpoint server:

- Quando si aggiunge un nuovo endpoint server a un gruppo di sincronizzazione esistente, i metadati vengono innanzitutto sincronizzati con il server e i file vengono quindi scaricati nel server in background. I file verranno visualizzati a livelli fino a quando non vengono scaricati localmente. Per migliorare le prestazioni di download dei file quando si aggiunge un nuovo server a un gruppo di sincronizzazione, usare il cmdlet [Invoke-StorageSyncFileRecall](storage-sync-cloud-tiering.md#afs-recall-file) .

- Se la suddivisione in livelli nel cloud è stata abilitata nell'endpoint server e quindi disabilitata, i file rimarranno a livelli fino a quando non saranno accessibili.


## <a name="next-steps"></a>Passaggi successivi
* [Pianificazione di una distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md)
