---
title: Migrazione di NAS locale a Sincronizzazione file di Azure
description: Informazioni su come eseguire la migrazione di file da un percorso locale di archiviazione collegata alla rete (NAS) a una distribuzione cloud ibrida con Sincronizzazione file di Azure e condivisioni file di Azure.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 0d8d19256dfca21cc805c2689557099a6785f76b
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629207"
---
# <a name="migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Eseguire la migrazione da Network Attached Storage (NAS) a una distribuzione cloud ibrida con Sincronizzazione file di Azure

Sincronizzazione file di Azure funziona nei percorsi DAS (Direct Attached Storage) e non supporta la sincronizzazione con i percorsi di archiviazione collegata alla rete (NAS).
Questo fatto rende necessario eseguire la migrazione dei file e in questo articolo viene illustrata la pianificazione e l'esecuzione di una migrazione di questo tipo.

## <a name="migration-goals"></a>Obiettivi della migrazione

L'obiettivo è spostare le condivisioni presenti sul dispositivo NAS in un server Windows. Usare quindi Sincronizzazione file di Azure per una distribuzione cloud ibrida. Questa migrazione deve essere eseguita in modo da garantire l'integrità dei dati di produzione e la disponibilità durante la migrazione. Il secondo richiede il mantenimento del tempo di inattività minimo, in modo che possa rientrare o solo leggermente più di una normale finestra di manutenzione.

## <a name="migration-overview"></a>Panoramica della migrazione

Come indicato nell' [articolo Panoramica della migrazione](storage-files-migration-overview.md)di file di Azure, è importante usare lo strumento di copia e l'approccio corretti. Il dispositivo NAS espone le condivisioni SMB direttamente nella rete locale. RoboCopy, integrato in Windows Server, è il modo migliore per spostare i file in questo scenario di migrazione.

- Fase 1: [identificare il numero di condivisioni file di Azure necessarie](#phase-1-identify-how-many-azure-file-shares-you-need)
- Fase 2: effettuare [il provisioning di un Windows Server locale adatto](#phase-2-provision-a-suitable-windows-server-on-premises)
- Fase 3: [distribuire la risorsa sincronizzazione file di Azure cloud](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- Fase 4: [distribuire le risorse di archiviazione di Azure](#phase-4-deploy-azure-storage-resources)
- Fase 5: [distribuire l'agente di sincronizzazione file di Azure](#phase-5-deploy-the-azure-file-sync-agent)
- Fase 6: [configurare sincronizzazione file di Azure in Windows Server](#phase-6-configure-azure-file-sync-on-the-windows-server)
- Fase 7: [Robocopy](#phase-7-robocopy)
- Fase 8: [riduzione dell'utente](#phase-8-user-cut-over)

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Fase 1: identificare il numero di condivisioni file di Azure necessarie

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>Fase 2: effettuare il provisioning di un Windows Server locale adatto

* Creare una macchina virtuale di Windows Server 2019, almeno 2012R2, come macchina virtuale o server fisico. È supportato anche un cluster di failover di Windows Server.
* Effettuare il provisioning o aggiungere l'archiviazione collegata direttamente (DAS rispetto a NAS, che non è supportata).

    Se si usa la funzionalità di suddivisione in [livelli cloud](storage-sync-cloud-tiering.md) di sincronizzazione file di Azure, la quantità di spazio di archiviazione di cui si effettua il provisioning può essere inferiore a quella attualmente in uso nel dispositivo NAS.
    Tuttavia, quando si copiano i file dallo spazio NAS più ampio al volume di Windows Server più piccolo in una fase successiva, sarà necessario lavorare in batch:

    1. Spostare un set di file che si integrano sul disco
    2. consente di attivare la sincronizzazione file e la suddivisione in livelli nel cloud
    3. Quando viene creato più spazio libero nel volume, procedere con il batch successivo di file. 
    
    È possibile evitare questo approccio di invio in batch eseguendo il provisioning dello spazio equivalente nel server Windows che i file occupano sul dispositivo NAS. Si consideri la deduplicazione su NAS/Windows. Se non si vuole eseguire il commit permanente di questa quantità elevata di spazio di archiviazione in Windows Server, è possibile ridurre le dimensioni del volume dopo la migrazione e prima di modificare i criteri di suddivisione in livelli nel cloud. Che consente di creare una cache locale più piccola delle condivisioni file di Azure.

La configurazione della risorsa (calcolo e RAM) del server Windows distribuito dipende principalmente dal numero di elementi (file e cartelle) che verranno sincronizzati. Se si verificano problemi, è consigliabile procedere con una configurazione di prestazioni superiore.

[Informazioni su come ridimensionare un server Windows in base al numero di elementi (file e cartelle) che è necessario sincronizzare.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> L'articolo collegato in precedenza presenta una tabella con un intervallo di memoria del server (RAM). È possibile orientarsi verso il numero più piccolo per il server, ma si prevede che la sincronizzazione iniziale possa richiedere molto più tempo.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>Fase 3: distribuire la risorsa Sincronizzazione file di Azure cloud

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>Fase 4: distribuire le risorse di archiviazione di Azure

In questa fase, consultare la tabella di mapping della fase 1 e usarla per eseguire il provisioning del numero corretto di account di archiviazione di Azure e delle condivisioni file al suo interno.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>Fase 5: distribuire l'agente di Sincronizzazione file di Azure

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>Fase 6: configurare Sincronizzazione file di Azure in Windows Server

Per questo processo, è necessario che il server Windows locale registrato sia pronto e connesso a Internet.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> La suddivisione in livelli nel cloud è la funzionalità AFS che consente al server locale di avere una capacità di archiviazione inferiore a quella archiviata nel cloud, ma è disponibile lo spazio dei nomi completo. I dati localmente interessanti vengono anche memorizzati nella cache localmente per ottenere prestazioni di accesso rapido. La suddivisione in livelli nel cloud è una funzionalità facoltativa per ogni Sincronizzazione file di Azure "endpoint server".

> [!WARNING]
> Se è stato effettuato il provisioning di una quantità minore di spazio di archiviazione sui volumi di Windows Server rispetto ai dati usati nell'appliance NAS, la suddivisione in livelli nel cloud è obbligatoria. Se non si attiva la suddivisione in livelli nel cloud, il server non libera spazio per archiviare tutti i file. Impostare i criteri di suddivisione in livelli, temporaneamente per la migrazione, sullo spazio disponibile nel volume del 99%. Assicurarsi di tornare alle impostazioni di suddivisione in livelli nel cloud al termine della migrazione e impostarlo su un livello utile più lungo.

Ripetere i passaggi per la creazione del gruppo di sincronizzazione e l'aggiunta della cartella del server corrispondente come endpoint server per tutte le condivisioni file o i percorsi server di Azure che devono essere configurati per la sincronizzazione.

Dopo la creazione di tutti gli endpoint server, la sincronizzazione funziona. È possibile creare un file di test e visualizzarne la sincronizzazione dal percorso del server alla condivisione file di Azure connessa (come descritto dall'endpoint cloud nel gruppo di sincronizzazione).

In entrambi i casi, le cartelle del server e le condivisioni file di Azure sono altrimenti vuote e in attesa dei dati in entrambe le posizioni. Nel passaggio successivo si inizierà a copiare i file in Windows Server per Sincronizzazione file di Azure per spostarli nel cloud. Nel caso in cui sia stata abilitata la suddivisione in livelli nel cloud, il server inizierà a eseguire il livello dei file, in caso di esaurimento della capacità nei volumi locali.

## <a name="phase-7-robocopy"></a>Fase 7: RoboCopy

L'approccio di migrazione di base è RoboCopy dal dispositivo NAS al server Windows e Sincronizzazione file di Azure alle condivisioni file di Azure.

Eseguire la prima copia locale nella cartella di destinazione di Windows Server:

* Identificare la prima posizione nel dispositivo NAS.
* Identificare la cartella corrispondente nel server Windows in cui è già stata configurata Sincronizzazione file di Azure.
* Avviare la copia con RoboCopy

Il comando RoboCopy seguente consente di copiare i file dalla risorsa di archiviazione NAS alla cartella di destinazione di Windows Server. Windows Server lo sincronizza con le condivisioni file di Azure. 

Se è stato effettuato il provisioning di una quantità minore di spazio di archiviazione nel server Windows rispetto a quella dei file, è stata configurata la suddivisione in livelli nel cloud. Con l'esaurimento del volume locale di Windows Server, la suddivisione in [livelli cloud](storage-sync-cloud-tiering.md) verrà avviata e i file di livello che sono già stati sincronizzati correttamente. La suddivisione in livelli cloud genererà spazio sufficiente per continuare la copia dal dispositivo NAS. La suddivisione in livelli nel cloud viene controllata una volta all'ora per vedere cosa è stato sincronizzato e liberare spazio su disco per raggiungere lo spazio disponibile nel volume del 99%.
È possibile che RoboCopy sposti i file più velocemente di quanto sia possibile sincronizzare al cloud e al livello localmente, esaurendo così lo spazio su disco locale. RoboCopy avrà esito negativo. Si consiglia di usare le condivisioni in una sequenza che lo impedisce. Ad esempio, non avviare i processi RoboCopy per tutte le condivisioni contemporaneamente o solo le condivisioni che rientrano nella quantità corrente di spazio disponibile sul server Windows, per citarne alcune.

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Sfondo:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Consente a RoboCopy di eseguire multithread. Il valore predefinito è 8, Max è 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:\<file name\>
   :::column-end:::
   :::column span="1":::
      Restituisce lo stato del file di LOG come UNICODE (sovrascrive il log esistente).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Output nella finestra della console. Utilizzato in combinazione con l'output in un file di log.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      / B
   :::column-end:::
   :::column span="1":::
      Esegue RoboCopy nella stessa modalità usata da un'applicazione di backup. Consente a RoboCopy di spostare i file che l'utente corrente non dispone delle autorizzazioni per.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Consente a di eseguire questo comando RoboCopy più volte, in sequenza nella stessa destinazione/destinazione. Identifica gli elementi che sono stati copiati in precedenza e li omette. Verranno elaborate solo le modifiche, le aggiunte e le " *eliminazioni* ", che si sono verificate dall'ultima esecuzione. Se il comando non è stato eseguito in precedenza, non viene omesso nulla. Il flag */Mir* è un'ottima opzione per le posizioni di origine che vengono ancora utilizzate e modificate in modo attivo.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      fedeltà della copia del file (il valore predefinito è/COPY: DAT), flag di copia: D = dati, A = attributi, T = timestamp, S = Security = ACL NTFS, O = informazioni sul proprietario, U = informazioni sul controllo
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      COPIA tutte le informazioni sul file (equivalente a/COPY: DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      fedeltà per la copia delle directory (il valore predefinito è/DCOPY: DA), i flag di copia: D = dati, A = attributi, T = timestamp
   :::column-end:::
:::row-end:::

## <a name="phase-8-user-cut-over"></a>Fase 8: riduzione dell'utente

Quando si esegue il comando RoboCopy per la prima volta, gli utenti e le applicazioni continuano ad accedere ai file del NAS e potenzialmente modificarli. È possibile che RoboCopy abbia elaborato una directory, passi alla successiva, quindi un utente nel percorso di origine (NAS) aggiunge, modifica o Elimina un file che ora non verrà elaborato in questa esecuzione di RoboCopy corrente. Si tratta di un comportamento previsto.

La prima esecuzione consiste nello trasferire la maggior parte dei dati in Windows Server e nel cloud tramite Sincronizzazione file di Azure. Questa prima copia può richiedere molto tempo, a seconda di:

* larghezza di banda di download
* larghezza di banda di caricamento
* la velocità della rete locale e il numero di come il numero di thread RoboCopy corrisponde in modo ottimale
* il numero di elementi (file e cartelle) che devono essere elaborati da RoboCopy e Sincronizzazione file di Azure

Una volta completata l'esecuzione iniziale, eseguire di nuovo il comando.

La seconda volta che verrà completata più velocemente, perché è necessario solo trasferire le modifiche apportate dall'ultima esecuzione. Durante la seconda esecuzione, tuttavia, le nuove modifiche possono accumularsi.

Ripetere questa procedura fino a quando non si è soddisfatti del tempo necessario per completare un RoboCopy per una posizione specifica all'interno di una finestra accettabile per i tempi di inattività.

Quando si considera il tempo di inattività accettabile e si è pronti a portare offline il percorso NAS: per consentire l'accesso utente offline, è possibile modificare gli ACL nella radice della condivisione in modo che gli utenti non possano più accedere al percorso o eseguire qualsiasi altro passaggio appropriato che impedisce la modifica del contenuto in questa cartella nel server NAS.

Eseguire un ultimo round RoboCopy. Rileverà le modifiche che potrebbero essere state perse.
Il tempo necessario per il passaggio finale dipende dalla velocità dell'analisi RoboCopy. È possibile stimare l'intervallo di tempo (uguale al tempo di inattività) misurando il tempo impiegato per l'esecuzione precedente.

Creare una condivisione nella cartella di Windows Server ed eventualmente modificare la distribuzione di DFS-N in modo che punti a essa. Assicurarsi di impostare le stesse autorizzazioni a livello di condivisione della condivisione SMB NAS. Se si dispone di un NAS di livello aziendale aggiunto al dominio, i SID utente corrisponderanno automaticamente mentre gli utenti sono presenti nel Active Directory e RoboCopy copia i file e i metadati con la massima fedeltà. Se sono stati usati utenti locali nel NAS, è necessario ricrearli come utenti locali di Windows Server ed eseguire il mapping dei SID esistenti con RoboCopy spostati nel server Windows ai SID del nuovo utente locale di Windows Server.

È stata completata la migrazione di una condivisione o di un gruppo di condivisioni in una radice o un volume comune. (A seconda del mapping dalla fase 1)

È possibile provare a eseguire alcune di queste copie in parallelo. Si consiglia di elaborare l'ambito di una condivisione file di Azure alla volta.

> [!WARNING]
> Dopo aver spostato tutti i dati dal NAS al server Windows e aver completato la migrazione: tornare a * **tutti** i gruppi di sincronizzazione nella portale di Azure e modificare il valore percentuale spazio disponibile del volume del cloud in modo che sia più adatto per l'utilizzo della cache, ad indicare il 20%. 

I criteri di spazio libero del volume di suddivisione in livelli nel cloud agiscono a livello di volume con potenzialmente più endpoint server sincronizzati. Se si dimentica di regolare lo spazio libero su un solo endpoint server, la sincronizzazione continuerà ad applicare la regola più restrittiva e tenterà di mantenere il 99% di spazio libero su disco, rendendo la cache locale non funziona come previsto. A meno che non si tratti dell'obiettivo di avere solo lo spazio dei nomi per un volume che contiene solo i dati di archiviazione a cui si accede raramente e si riserva il resto dello spazio di archiviazione per un altro scenario.

## <a name="troubleshoot"></a>Risolvere problemi

Il problema più probabile in cui è possibile eseguire è che il comando RoboCopy ha esito negativo con _ "volume full" * sul lato Windows Server. La suddivisione in livelli nel cloud funziona una volta ogni ora per l'evacuazione del contenuto dal disco locale di Windows Server, sincronizzato. Il suo obiettivo è raggiungere il 99% di spazio disponibile nel volume.

Consentire la sincronizzazione dello stato di avanzamento e la suddivisione in livelli nel cloud liberare spazio su disco. È possibile osservare che in Esplora file in Windows Server.

Quando Windows Server dispone di sufficiente capacità disponibile, la riesecuzione del comando risolverà il problema. Niente si interrompe quando si entra in questa situazione ed è possibile procedere con fiducia. L'unica conseguenza è l'inconveniente di eseguire di nuovo il comando.

Per la risoluzione dei problemi Sincronizzazione file di Azure, vedere il collegamento nella sezione seguente.

## <a name="next-steps"></a>Passaggi successivi

Sono disponibili altre informazioni sulle condivisioni file di Azure e Sincronizzazione file di Azure. Gli articoli seguenti consentono di comprendere le opzioni avanzate, le procedure consigliate e contengono anche la guida alla risoluzione dei problemi. Questi articoli sono collegati alla [documentazione di condivisione file di Azure](storage-files-introduction.md) nel modo appropriato.

* [Panoramica di AFS](./storage-sync-files-planning.md)
* [Guida alla distribuzione di AFS](storage-files-deployment-guide.md)
* [Risoluzione dei problemi di AFS](storage-sync-files-troubleshoot.md)