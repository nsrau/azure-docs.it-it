---
title: Integrazione non reale e Wwise dell'acustica del progetto
titlesuffix: Azure Cognitive Services
description: Questo articolo descrive l'integrazione del progetto Acoustics Unreal e Wwise plug-in nel progetto.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: e57212a3002390754aaebc5f2aa9ffb10af230a2
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243052"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Integrazione non reale e Wwise dell'acustica del progetto
Questo articolo descrive come integrare il pacchetto del plug-in Project Acoustics nel progetto di gioco Unreal e Wwise esistente.

Requisiti software:
* [Unreal Engine](https://www.unrealengine.com/) 4.20 +
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018,1
* [Plug-in Wwise per Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  
  Se si usa un'integrazione diretta di Wwise SDK anziché il plug-in Wwise Unreal, consultare il progetto Acoustics Unreal plug-in e Adjust Wwise API Calls.

Per usare le funzionalità acustiche del progetto con un motore audio diverso da Wwise, effettuare una richiesta di miglioramento nel [Forum di discussione sull'acustica del progetto](https://github.com/microsoft/ProjectAcoustics/issues). È possibile usare il plug-in Acoustics del progetto per eseguire query sui dati acustici ed effettuare chiamate API al motore.

## <a name="download-project-acoustics"></a>Scarica l'acustica del progetto
Se non è già stato fatto, scaricare il [pacchetto del plug-in Wwise e Unreal del progetto Acoustics](https://www.microsoft.com/download/details.aspx?id=58090) .

Un plug-in Unreal Engine e un plug-in Wwise mixer sono inclusi nel pacchetto. Il plug-in Unreal fornisce l'integrazione di editor e Runtime. Durante il gameplay, il plug-in del progetto Acoustics Unreal Calcola parametri come l'occlusione per ogni oggetto gioco per ogni frame. Questi parametri vengono convertiti in chiamate API Wwise.

## <a name="integration-steps"></a>Procedura di integrazione

Per installare il pacchetto e distribuirlo nel gioco, seguire questa procedura.

### <a name="install-the-project-acoustics-mixer-plug-in"></a>Installare il plug-in del mixer Acoustics del progetto
1. Aprire l'utilità di avvio Wwise. Nella scheda **plug-** in, in **Installa nuovi plug-** in, selezionare **Aggiungi da directory**.

    ![Installare un plug-in nell'utilità di avvio di Wwise](media/wwise-install-new-plugin.png)

1. Selezionare la directory *AcousticsWwisePlugin\ProjectAcoustics* che si trova nel pacchetto di download. Contiene il bundle del plug-in Wwise mixer.

   Wwise installerà il plug-in. Il progetto Acoustics dovrebbe essere visualizzato nell'elenco dei plug-in installati in Wwise.  
![Elenco dei plug-in installati di Wwise dopo l'installazione di Acoustics](media/unreal-integration-post-mixer-plugin-install.png)

### <a name="dedeploy-wwise-into-your-game"></a>Annulla la distribuzione di Wwise nel gioco
Ridistribuire Wwise nel gioco anche se Wwise è già stato integrato. Questo passaggio integra il plug-in Wwise per il progetto Acoustics.

   > [!NOTE]
   > **Plug-in del motore:** Se è stato installato Wwise come plug-in di gioco in un progetto Unreal C++ , ignorare questo passaggio. Se viene installato invece come plug-in del motore, ad esempio perché il progetto Unreal è solo progetto, la distribuzione di Wwise con il plug-in del mixer è più complessa. Creare un progetto non reale C++ vuoto fittizio. Chiudere l'editor Unreal se si apre e seguire la procedura rimanente per distribuire Wwise nel progetto fittizio. Quindi, copiare il plug-in Wwise distribuito.
 
1. Dall'utilità di avvio di Wwise selezionare la scheda **Unreal Engine** . Selezionare il menu "hamburger" (icona) accanto a **progetti di Unreal Engine recenti** e quindi selezionare **Cerca progetto**. Aprire il file progetto *. progetto* non reale del gioco.

    ![Scheda Unreal dell'utilità di avvio Wwise](media/wwise-unreal-tab.png)

1. Selezionare **integra Wwise in Project** o **modificare Wwise in Project**. Questo passaggio integra i file binari di Wwise nel progetto, incluso il plug-in del mixer Acoustics di Project.

   > [!NOTE]
   > **Plug-in del motore:** Se si usa Wwise come plug-in del motore ed è stato creato il progetto fittizio come descritto in precedenza, copiare la cartella distribuita da Wwise: *[DummyUProject] \Plugins\Wwise*. Incollarlo sopra *[UESource] \Engine\Plugins\Wwise*. *[DummyUProject]* è il percorso di progetto C++ non reale vuoto e *[UESource]* è il punto in cui sono installate le origini Unreal Engine. Dopo aver copiato la cartella, è possibile eliminare il progetto fittizio.

### <a name="add-the-project-acoustics-unreal-plug-in-to-your-game"></a>Aggiungere il plug-in del progetto Acoustics Unreal al gioco
 
1. Copiare la cartella *Unreal\ProjectAcoustics* nel pacchetto plug-in. Creare una nuova cartella *[UProjectDir] \Plugins\ProjectAcoustics*, dove *[UProjectDir]* è la cartella del progetto del gioco che contiene il file con *estensione uproject* .

   > [!NOTE]
   > **Plug-in del motore**: Se si usa Wwise come plug-in del motore, è consigliabile usare l'acustica del progetto anche come plug-in Unreal Engine. Anziché la directory di destinazione citata in precedenza, utilizzare *[UESource] \Engine\Plugins\ProjectAcoustics*.

1. Verificare che venga visualizzata una cartella *Wwise* insieme alla cartella *ProjectAcoustics* . Contiene il plug-in Wwise insieme ai file binari per il plug-in del mixer distribuito in precedenza.

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Estendi la funzionalità di plug-in Wwise Unreal
Il plug-in del progetto Acoustics Unreal richiede un comportamento aggiuntivo esposto dall'API del plug-in Wwise Unreal in base a [queste linee guida](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). È stato incluso un file batch per automatizzare la procedura di applicazione delle patch.

* All'interno di *Plugins\ProjectAcoustics\Resources*eseguire *PatchWwise. bat*. Nell'immagine di esempio seguente viene usato il progetto di esempio AcousticsGame.

    ![Finestra di Esplora risorse con lo script da applicare alla patch Wwise evidenziato](media/patch-wwise-script.png)

* Se DirectX SDK non è installato: A seconda della versione di Wwise in uso, potrebbe essere necessario impostare come commento la riga che contiene `DXSDK_DIR` in *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*:

    ![L'editor di codice che mostra ' DXSDK ' è stato impostato come commento](media/directx-sdk-comment.png)

* Se si esegue la compilazione con Visual Studio 2019: Per aggirare un errore di collegamento con Wwise, modificare manualmente il valore predefinito `VSVersion` in *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* in **vc150**:

    ![L'editor di codice che mostra "VSVersion" è stato modificato in "vc150"](media/vsversion-comment.png)

### <a name="build-the-game-and-check-that-python-is-enabled"></a>Compilare il gioco e verificare che Python sia abilitato

1. Compilare il gioco e assicurarsi che venga compilato correttamente. Se non viene compilato, controllare attentamente i passaggi precedenti prima di continuare.

1. Aprire il progetto in Unreal Editor.

    > [!NOTE]
    > **Plug-in del motore:** Se si usa ProjectAcoustics come plug-in del motore, assicurarsi anche che sia abilitato in plug-in "predefiniti".

    Verrà visualizzata una nuova modalità, che indica che il progetto Acoustics è stato integrato.

    ![Modalità acustica completa in Unreal](media/acoustics-mode-full.png)

1. Verificare che il plug-in Python per Unreal sia abilitato in modo che l'integrazione dell'editor funzioni correttamente.

    ![Estensioni di Python nell'editor Unreal abilitato](media/ensure-python.png)

### <a name="set-up-your-wwise-project-to-use-project-acoustics"></a>Configurare il progetto Wwise per l'uso dell'acustica del progetto

Un esempio di progetto Wwise è incluso nel download degli esempi. Si consiglia di visualizzarlo insieme a queste istruzioni. Le schermate più avanti in questo articolo sono riportate in questo progetto.

#### <a name="bus-setup"></a>Configurazione del bus
Il plug-in del progetto Acoustics Unreal Cerca il plug-in del mixer associato in un bus con il nome esatto `Project Acoustics Bus`. Creare un nuovo bus audio con lo stesso nome. Il plug-in del mixer può funzionare in varie configurazioni. Per il momento, tuttavia, si presuppone che venga utilizzato solo per l'elaborazione dei verbi. Questo bus conterrà il segnale di riverbero misto per tutte le origini che usano acustica. È possibile combinare upstream in qualsiasi struttura di combinazione del bus. Un esempio è illustrato di seguito dal progetto di esempio Wwise incluso nel download di esempio.

![Bus Wwise che mostrano il bus acustico del progetto](media/acoustics-bus.png)

1. Impostare la configurazione del canale sul bus su *1,0*, *2,0*, *4,0*, *5,1*o *7,1*. Qualsiasi altra impostazione non comporterà alcun output sul bus.

    ![Opzioni di configurazione del canale per il bus Acoustics del progetto](media/acoustics-bus-channel-config.png)

1. Passare al progetto Acoustics bus Details e verificare che sia possibile visualizzare la scheda **plug-in del mixer** .

    ![Wwise con la scheda plug-in del mixer per il bus Acoustics del progetto abilitato](media/mixer-tab-enable.png)

1. Passare alla scheda **plug-in del mixer** e aggiungere il plug-in del mixer Acoustics del progetto al bus.

    ![Diagramma dell'aggiunta del plug-in del mixer Acoustics del progetto al bus Wwise](media/add-mixer-plugin.png)

#### <a name="actor-mixer-hierarchy-setup"></a>Impostazione della gerarchia Actor-mixer
Per ottenere prestazioni ottimali, i progetti acustici applicano l'elaborazione di segnali digitali audio a tutte le origini simultaneamente. Il plug-in deve pertanto funzionare come plug-in del mixer. Wwise richiede che i plug-in del mixer si trovino sul bus di output, anche se il bus di output contiene in genere il segnale di output asciutto. Per l'acustica del progetto è necessario che il segnale secco venga instradato tramite bus AUX, mentre il segnale bagnato viene mantenuto nel `Project Acoustics Bus`. Il processo seguente supporta la migrazione graduale a questo flusso del segnale.

Supponiamo di avere un progetto esistente con una gerarchia Actor-mixer che contiene i *passi*, le *armi*e altri utenti al primo livello. Ogni ha un bus di output corrispondente per la combinazione secca. Supponiamo di voler migrare i passi per usare l'acustica. Prima di tutto, creare un bus aux corrispondente per portare il submix secco che è un figlio del bus di output dei passi. Ad esempio, è stato usato un prefisso "Dry" nell'immagine seguente per organizzare i bus, anche se il nome esatto non è importante. Tutti i contatori o gli effetti presenti sul bus dei passi continueranno a funzionare come prima.

![Installazione consigliata di Wwise dry mix](media/wwise-dry-mix-setup.png)

Modificare quindi la struttura di output del bus per il passaggio Actor-mixer come indicato di seguito, con il *bus Acoustics del progetto* impostato come **bus di output**e *Dry_Footsteps* impostato come bus aux definito dall'utente.

![Installazione consigliata del bus Wwise Actor](media/actor-mixer-bus-settings.png)

Ora tutti i passi ricevono un trattamento acustico e ne generano il riverbero sul bus Acoustics del progetto. Il segnale secco viene instradato tramite Dry_Footsteps e spaziale come di consueto.

Il progetto Acoustics si applica solo ai suoni con una posizione 3D nel mondo. Seguendo la [documentazione di Wwise](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/), è necessario impostare le proprietà di posizionamento come illustrato. L'impostazione di **spazializzazione 3D** può essere *posizione* o *posizione + orientamento* in base alle esigenze.

![Impostazioni consigliate per il posizionamento degli attori Wwise](media/wwise-positioning.png)

Non è possibile impostare il **bus di output** su un altro bus che combina upstream nel *bus Acoustics del progetto*. Wwise impone questo requisito nei plug-in del mixer.

Se si vuole che un elemento figlio nella gerarchia Actor-mixer passi non usi l'acustica, è possibile usare l'opzione "Sostituisci elemento padre" in modo che non venga usata.

Se si usano invii definiti dall'utente o definiti dall'utente per il riverbero in qualsiasi attore-mixer nel gioco, disattivarli in questo Actor-mixer per evitare di applicare due volte il riverbero.

#### <a name="spatialization"></a>Spazializzazione
Il plug-in di Wwise mixer per il progetto Acoustics applica il riverbero di convoluzione per impostazione predefinita, lasciando Wwise per eseguire la spazializzazione della panoramica. Quando si usa l'acustica del progetto in questa configurazione predefinita di soli reverbi, è possibile usare qualsiasi metodo di configurazione e spazializzazione del canale per la combinazione secca. Quindi, è possibile combinare e associare quasi tutti i Spatializer con il riverbero Acoustics del progetto. Le opzioni disponibili includono [Ambisonics biarch spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/) e [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
Il progetto Acoustics include un Spatializer facoltativo che supporta il rendering e la Panoramica di HRTF ad alta risoluzione basata su oggetti. Selezionare la casella di controllo **Esegui spazializzazione** nelle impostazioni del plug-in del mixer e scegliere tra *HRTF* o *panning*. Disabilitare inoltre le trasmissioni aux definite dall'utente a tutti i bus a secco per evitare spatializing due volte dal plug-in del mixer Acoustics del progetto e da Wwise. Non è possibile modificare la modalità di spazializzazione in tempo reale perché richiede una rigenerazione della banca sonora. Riavviare Unreal, quindi rigenerare SoundBanks prima di selezionare Play per integrare le modifiche di configurazione del plug-in del mixer, ad esempio l'impostazione della casella di controllo **Esegui spazializzazione** .

![Impostazioni di spazializzazione del plug-in Wwise mixer](media/mixer-spatial-settings.png)

Sfortunatamente, altri plug-in Spatializer basati su oggetti non sono attualmente supportati. Sono implementati come plug-in di mixer e Wwise non consente l'assegnazione di più plug-in di mixer a un singolo Actor-mixer.  

### <a name="audio-setup-in-unreal"></a>Installazione audio in Unreal
1. Prima di tutto, è necessario preparare il livello di gioco per produrre un asset acustico, che verrà inserito in *Content\Acoustics*. Vedere l' [esercitazione Unreal Bake](unreal-baking.md). Alcuni livelli pre-cotti sono inclusi nel pacchetto di esempio.

1. Creare un attore di spazio acustico nella scena. Creare solo uno di questi attori in un livello, perché rappresenta l'acustica per l'intero livello.

    ![Creazione di un attore dello spazio acustico nell'editor Unreal](media/create-acoustics-space.png)

1. Assegnare l'asset di dati acustici al forno per lo slot di dati acustici nell'attore dello spazio acustico. La tua scena ora presenta acustici.

    ![Assegnazione di asset acustici nell'editor non reale](media/acoustics-asset-assign.png)

1. Aggiungere un attore vuoto. Configurarla come indicato di seguito.

    ![L'editor Unreal Mostra l'utilizzo dei componenti acustici in un attore vuoto](media/acoustics-component-usage.png)

       
    <sup>1</sup> aggiungere un componente audio acustico all'attore. Questo componente aggiunge la funzionalità acustica del progetto al componente audio Wwise.
        
    <sup>2</sup> per impostazione predefinita, la casella **Riproduci in avvio** è selezionata. Questa impostazione attiva un evento Wwise associato all'avvio del livello.</li>
         
    <sup>3</sup> utilizzare la casella di controllo **Mostra parametri acustici** per stampare le informazioni di debug sullo schermo relative all'origine.

    ![Pannello acustico dell'editor non reale nell'origine audio con valori di debug abilitati](media/debug-values.png)

    <sup>4</sup> assegnare un evento Wwise per il normale flusso di lavoro Wwise.
       
    <sup>5</sup> assicurarsi che l'opzione **usa audio spaziale** sia spenta. Se si usa l'acustica del progetto per un particolare componente audio, non è possibile usare contemporaneamente il motore audio spaziale di Wwise per l'acustica.</li>
       
Le impostazioni sono state completate. Passa alla scena ed Esplora gli effetti acustici.

## <a name="next-steps"></a>Passaggi successivi
* Provare il [progetto Acoustics Unreal/Wwise design tutorial](unreal-workflow.md).
* Scopri [come eseguire](unreal-baking.md) le operazioni di cottura per le tue scene di gioco.
