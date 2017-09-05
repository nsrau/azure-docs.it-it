---
title: Usare il servizio Rendering di Azure Batch per eseguire il rendering sul cloud | Microsoft Docs
description: Eseguire il rendering di processi nelle macchine virtuali di Azure direttamente da Maya e con pagamento in base al consumo.
services: batch
author: tamram
manager: timlt
ms.service: batch
ms.topic: hero-article
ms.date: 07/31/2017
ms.author: tamram
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: 4d22f92cafdbceee5213361d6d2b2f38904d12c6
ms.contentlocale: it-it
ms.lasthandoff: 08/01/2017

---

# <a name="get-started-with-the-batch-rendering-service"></a>Introduzione al servizio Rendering di Batch

Il servizio Rendering di Azure Batch offre funzionalità di rendering su scala cloud con pagamento in base al consumo. Il servizio Rendering di Batch gestisce la pianificazione e l'accodamento dei processi, la gestione degli errori e dei tentativi e il ridimensionamento automatico per il processo di rendering. Il servizio Rendering di Batch supporta Autodesk Maya, 3ds Max e Arnold. Il supporto per altre applicazioni sarà presto disponibile. Il plug-in Batch per Maya 2017 semplifica l'avvio di un processo di rendering in Azure direttamente dal desktop. 

## <a name="supported-applications"></a>Applicazioni supportate

Il servizio Rendering di Batch supporta attualmente le applicazioni seguenti:

- Autodesk Maya
- Autodesk 3ds Max
- Autodesk Arnold

## <a name="prerequisites"></a>Prerequisiti

Per usare il servizio Rendering di Batch, è necessario quanto segue:

- Un [account Azure](https://azure.microsoft.com/free/). 
- Un **account Azure Batch**. Per indicazioni sulla creazione di un account Batch nel portale di Azure, vedere [Creare un account Batch nel portale di Azure](batch-account-create-portal.md).
- Un **account di archiviazione di Azure**. Gli asset usati per il processo di rendering vengono archiviati in Archiviazione di Azure. È possibile creare automaticamente un account di archiviazione quando si configura l'account Batch. È possibile anche usare un account di archiviazione esistente. Per altre informazioni sugli account di archiviazione, vedere [Come creare, gestire o eliminare un account di archiviazione nel portale di Azure](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

Per usare il plug-in di Batch per Maya, è necessario quanto segue:

- **Maya 2017**
- **Arnold for Maya**

È anche possibile usare il [portale di Azure](https://portal.azure.com) per creare pool di macchine virtuali preconfigurati con Maya, 3ds Max e Arnold. È possibile usare il portale per monitorare i processi e diagnosticare le attività non riuscite scaricando i log delle applicazioni e connettendosi in modalità remota alle singole VM usando RDP o SSH.

## <a name="basic-batch-concepts"></a>Concetti di base relativi a Batch

Prima di iniziare a usare il servizio Rendering di Batch, è consigliabile acquisire familiarità con alcuni concetti relativi a Batch, inclusi i nodi, i pool e i processi. Per altre informazioni generali su Azure Batch, vedere [Eseguire carichi di lavoro intrinsecamente paralleli con Batch](batch-technical-overview.md).

### <a name="pools"></a>Pool

Batch è un servizio piattaforma per l'esecuzione di operazioni a elevato utilizzo di calcolo, come il rendering, in un **pool** di **nodi di calcolo**. Ogni nodo di calcolo in un pool è una macchina virtuale di Azure che esegue Linux o Windows. 

Per altre informazioni sui pool e sui nodi di calcolo di Batch, vedere le sezioni [Pool](batch-api-basics.md#pool) e [Nodi di calcolo](batch-api-basics.md#compute-node) in [Sviluppare soluzioni di calcolo parallele su larga scala con Batch](batch-api-basics.md).

### <a name="jobs"></a>Processi

Un **processo** di Batch è una raccolta di attività in esecuzione nei nodi di calcolo in un pool. Quando si invia un processo di rendering, Batch divide il processo in attività e distribuisce le attività ai nodi di calcolo nel pool per l'esecuzione.

Per altre informazioni sui processi di Batch, vedere la sezione [Processo](batch-api-basics.md#job) in [Sviluppare soluzioni di calcolo parallele su larga scala con Batch](batch-api-basics.md).

## <a name="use-the-batch-plug-in-for-maya-to-submit-a-render-job"></a>Usare il plug-in di Batch per Maya per inviare un processo di rendering

Con il plug-in di Batch per Maya è possibile inviare un processo al servizio Rendering di Batch direttamente da Maya. Le sezioni seguenti illustrano come configurare il processo dal plug-in e quindi inviarlo. 

### <a name="load-the-batch-plug-in-in-maya"></a>Caricare il plug-in di Batch in Maya

Il plug-in di Batch è disponibile su [GitHub](https://github.com/Azure/azure-batch-maya/releases). Decomprimere il file compresso in una directory appropriata. È possibile caricare il plug-in direttamente dalla directory *azure_batch_maya*.

Per caricare il plug-in in Maya:

1. Eseguire Maya.
2. Aprire **Window** (Finestra)  > **Settings/Preferences** (Impostazioni/Preferenze)  > **Plug-in Manager** (Gestione plug-in).
3. Fare clic su **Sfoglia**.
4. Passare ad *azure_batch_maya/plug-in/AzureBatch.py* e selezionarlo.

### <a name="authenticate-access-to-your-batch-and-storage-accounts"></a>Autenticare l'accesso agli account Batch e di archiviazione

Per usare il plug-in, è necessario eseguire l'autenticazione usando le chiavi dell'account Azure e dell'account di archiviazione di Azure. Per recuperare le chiavi dell'account:

1. Visualizzare il plug-in in Maya e quindi selezionare la scheda **Config**.
2. Passare al [portale di Azure](https://portal.azure.com).
3. Selezionare **Account Batch** dal menu a sinistra. Se necessario, fare clic su **Altri servizi** e specificare _Batch_ come criterio per il filtro.
4. Individuare l'account Batch da usare nell'elenco.
5. Selezionare la voce di menu **Chiavi** per visualizzare il nome dell'account, l'URL dell'account e le chiavi di accesso:
    - Incollare l'URL dell'account Batch nel campo **Servizio** del plug-in Batch.
    - Incollare il nome dell'account nel campo **Account Batch**.
    - Incollare la chiave dell'account primaria nel campo **Batch Key** (Chiave Batch).
7. Selezionare Account di archiviazione dal menu a sinistra. Se necessario, fare clic su **Altri servizi** e specificare _Archiviazione_ come criterio per il filtro.
8. Individuare l'account di archiviazione da usare nell'elenco.
9. Selezionare la voce di menu **Chiavi di accesso** per visualizzare il nome e le chiavi dell'account di archiviazione.
    - Incollare il nome dell'account di archiviazione nel campo **Account di archiviazione** del plug-in di Batch.
    - Incollare la chiave dell'account primaria nel campo **Chiave di archiviazione**.
10. Fare clic su **Autenticazione** per assicurarsi che il plug-in possa accedere a entrambi gli account.

Dopo avere completato correttamente l'autenticazione, il plug-in imposta il campo relativo allo stato su **Autenticazione eseguita**: 

![Autenticare gli account Batch e di archiviazione](./media/batch-rendering-service/authentication.png)

### <a name="configure-a-pool-for-a-render-job"></a>Configurare un pool per un processo di rendering

Dopo avere completato l'autenticazione degli account Batch e di archiviazione, configurare un pool per il processo di rendering. Il plug-in salva le opzioni selezionate tra le sessioni. Dopo avere impostato la configurazione preferita, sarà necessario modificarla solo in caso di cambiamenti.

Le sezioni seguenti illustrano in modo dettagliato le opzioni disponibili nella scheda **Invia**:

#### <a name="specify-a-new-or-existing-pool"></a>Specificare un pool nuovo o esistente

Per specificare un pool in cui eseguire il processo di rendering, selezionare la scheda **Invia**. Questa scheda offre opzioni per la creazione di un pool o per la selezione di un pool esistente:

- È possibile scegliere l'opzione predefinita, **Auto provision a pool for this job** (Effettua automaticamente il provisioning di un pool per questo processo). Quando si sceglie questa opzione, Batch crea il pool esclusivamente per il processo corrente ed elimina automaticamente il pool al termine del processo di rendering. Questa opzione è ideale quando è presente un singolo processo di rendering da completare.
- È possibile scegliere l'opzione **Reuse an existing persistent pool** (Riutilizza un pool persistente esistente). Se è presente un pool esistente inattivo, è possibile specificare tale pool per l'esecuzione del processo di rendering selezionandolo dall'elenco a discesa. Il riutilizzo di un pool persistente esistente consente di ridurre il tempo necessario per il provisioning del pool.  
- È possibile scegliere l'opzione **Create a new persistent pool** (Crea un nuovo pool persistente). Se si seleziona questa opzione, verrà creato un nuovo pool per l'esecuzione del processo. Il pool non viene eliminato al termine del processo, quindi è possibile riutilizzarlo per processi futuri. Selezionare questa opzione quando è necessario eseguire continuamente processi di rendering. Nei processi successivi è possibile selezionare l'opzione **Reuse an existing persistent pool** (Riutilizza un pool persistente esistente) per usare il pool persistente creato per il primo processo.

![Specificare il pool, l'immagine del sistema operativo, le dimensioni della VM e la licenza](./media/batch-rendering-service/submit.png)

Per altre informazioni sul calcolo degli addebiti per le VM di Azure, vedere [Domande frequenti sui prezzi per Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#faq) e [Domande frequenti sui prezzi per Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/#faq).

#### <a name="specify-the-os-image-to-provision"></a>Specificare l'immagine del sistema operativo di cui effettuare il provisioning

È possibile specificare il tipo di immagine del sistema operativo da usare per il provisioning dei nodi di calcolo nel pool nella scheda **Env** (Ambiente). Batch supporta attualmente le opzioni di immagine seguenti per i processi di rendering:

|Sistema operativo  |Image  |
|---------|---------|
|Linux     |Anteprima di Batch CentOS |
|Windows     |Anteprima di Batch Windows |

#### <a name="choose-a-vm-size"></a>Scegliere le dimensioni per la macchina virtuale

È possibile specificare le dimensioni della VM nella scheda **Env** (Ambiente). Per altre informazioni sulle dimensioni di VM disponibili, vedere [Dimensioni delle macchine virtuali Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) e [Dimensioni per le macchine virtuali Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes). 

![Specificare l'immagine del sistema operativo e le dimensioni della VM nella scheda Env (Ambiente)](./media/batch-rendering-service/environment.png)

#### <a name="specify-licensing-options"></a>Specificare le opzioni di licenza

È possibile specificare le licenze da usare nella scheda **Env** (Ambiente). Le opzioni includono:

- **Maya**, abilitata per impostazione predefinita.
- **Arnold**, abilitata se viene rilevato Arnold come motore di rendering attivo in Maya.

 Se si vuole eseguire il rendering usando la propria licenza, è possibile configurare l'endpoint della licenza aggiungendo le variabili di ambiente appropriate alla tabella. In questo caso, assicurarsi di deselezionare le opzioni di licenza predefinite.

> [!IMPORTANT]
> Viene addebitato l'uso delle licenze quando le VM sono in esecuzione nel pool, anche se le VM non sono attualmente usate per il rendering. Per evitare addebiti supplementari, passare alla scheda **Pool** e ridimensionare il pool specificando 0 nodi, fino a quando non si è pronti per eseguire un altro processo di rendering. 
>
>

#### <a name="manage-persistent-pools"></a>Gestire i pool persistenti

È possibile gestire un pool persistente esistente nella scheda **Pool**. Se si seleziona un pool dall'elenco, viene visualizzato lo stato corrente del pool.

Dalla scheda **Pool** è anche possibile eliminare il pool e ridimensionare il numero di VM nel pool. È possibile ridimensionare un pool specificando 0 nodi per evitare addebiti tra i carichi di lavoro.

![Visualizzare, ridimensionare ed eliminare i pool](./media/batch-rendering-service/pools.png)

### <a name="configure-a-render-job-for-submission"></a>Configurare un processo di rendering per l'invio

Dopo avere specificato i parametri per il pool che eseguirà il processo di rendering, configurare il processo stesso. 

#### <a name="specify-scene-parameters"></a>Specificare i parametri della scena

Il plug-in di Batch rileva il motore di rendering attualmente in uso in Maya e mostra le impostazioni di rendering appropriate nella scheda **Invia**. Queste impostazioni includono il fotogramma iniziale, il fotogramma finale, il prefisso di output e il passaggio di fotogramma. È possibile eseguire l'override delle impostazioni di rendering del file della scena, specificando impostazioni diverse nel plug-in. Le modifiche apportate alle impostazioni del plug-in non vengono salvate in modo permanente nelle impostazioni di rendering del file della scena, quindi è possibile apportare modifiche per i singoli processi, senza che sia necessario ricaricare il file della scena.

Il plug-in avvisa nel caso in cui il motore di rendering selezionato in Maya non sia supportato.

Se si carica una nuova scena quando il plug-in è aperto, fare clic sul pulsante **Aggiorna** per assicurarsi che le impostazioni siano aggiornate.

#### <a name="resolve-asset-paths"></a>Risolvere i percorsi degli asset

Quando si carica il plug-in, viene eseguita l'analisi del file della scena per individuare eventuali riferimenti a file esterni. Questi riferimenti vengono visualizzati nella scheda **Asset**. Se non è possibile risolvere un percorso a cui si fa riferimento, il plug-in prova a individuare il file in alcuni percorsi predefiniti, ad esempio:

- Percorso del file della scena 
- Directory _sourceimages_ del progetto corrente
- Directory di lavoro corrente 

Se non è comunque possibile individuare l'asset, accanto all'asset viene visualizzata un'icona di avviso:

![Gli asset mancanti vengono visualizzati con un'icona di avviso](./media/batch-rendering-service/missing_assets.png)

Se si conosce il percorso di un riferimento di file non risolto, è possibile fare clic sull'icona di avviso, in modo che venga visualizzata la richiesta di aggiunta di un percorso di ricerca. Il plug-in usa quindi il percorso di ricerca per provare a risolvere eventuali asset mancanti. È possibile aggiungere qualsiasi numero di percorsi di ricerca aggiuntivi.

Quando viene risolto, il riferimento viene elencato con un'icona di colore verde chiaro:

![Asset risolti con icona di colore verde chiaro](./media/batch-rendering-service/found_assets.png)

Se la scena richiede altri file non rilevati dal plug-in, è possibile aggiungere altri file o directory. Usare i pulsanti **Aggiungi file** e **Aggiungi directory**. Se si carica una nuova scena quando il plug-in è aperto, assicurarsi di fare clic su **Aggiorna** per aggiornare i riferimenti della scena.

#### <a name="upload-assets-to-an-asset-project"></a>Caricare asset in un progetto asset

Quando si invia un processo di rendering, i file visualizzati nella scheda **Asset** vengono caricati automaticamente in Archiviazione di Azure come progetto asset. È anche possibile caricare indipendentemente i file di asset di un processo di rendering, usando il pulsante **Carica** nella scheda **Asset**. Il nome del progetto asset viene specificato nel campo **Progetto** e il nome viene specificato per impostazione predefinita in base al progetto Maya corrente. Quando vengono caricati file di asset, viene mantenuta la struttura di file locale. 

Dopo il caricamento, qualsiasi processo di rendering può fare riferimento agli asset. Tutti gli asset caricati sono disponibili a qualsiasi processo che fa riferimento al progetto asset, indipendentemente dal fatto che siano inclusi nella scena. Per modificare il progetto asset a cui fa riferimento il processo successivo, cambiare il nome nel campo **Progetto** della scheda **Asset**. Se sono presenti file a cui si fa riferimento da escludere dal caricamento, deselezionarli usando il pulsante verde accanto a ogni voce dell'elenco.

#### <a name="submit-and-monitor-the-render-job"></a>Inviare e monitorare il processo di rendering

Dopo aver configurato il processo di rendering nel plug-in, fare clic sul pulsante **Invia processo** nella scheda **Invia** per inviare il processo a Batch:

![Inviare il processo di rendering](./media/batch-rendering-service/submit_job.png)

È possibile monitorare un processo in corso dalla scheda **Processi** del plug-in. Selezionare un processo dall'elenco per visualizzare lo stato corrente del processo. È anche possibile usare questa scheda per annullare ed eliminare processi, oltre che per scaricare gli output e i log di rendering. 

Per scaricare gli output, modificare il campo **Output** per impostare la directory di destinazione da usare. Fare clic sull'icona a forma di ingranaggio per avviare un processo in background che controlla il processo e scarica gli output nel corso dell'esecuzione del processo: 

![Visualizzare lo stato del processo e scaricare gli output](./media/batch-rendering-service/jobs.png)

È possibile chiudere Maya senza interrompere il processo di download.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Batch, vedere [Eseguire carichi di lavoro intrinsecamente paralleli con Batch](batch-technical-overview.md).
