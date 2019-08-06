---
title: Integrazione non reale e Wwise dell'acustica del progetto
titlesuffix: Azure Cognitive Services
description: Questa procedura descrive l'integrazione del progetto Acoustics Unreal e Wwise plugins nel progetto.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: 5511dd6b9a7d77c0988a94fef747a30d25bb4fc3
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706616"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Integrazione non reale e Wwise dell'acustica del progetto
Questa procedura fornisce i passaggi dettagliati di integrazione del pacchetto di plug-in di Project Acoustics nel progetto di gioco Unreal e Wwise esistente. 

Requisiti software:
* [Unreal Engine](https://www.unrealengine.com/) 4,20 o 4,21
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018,1.\*
* [Plug-in Wwise per Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  * Se si usa un'integrazione diretta di Wwise SDK invece di usare i plug-in Wwise Unreal, consultare il plug-in Acoustics Unreal plugin e modificare le chiamate API di Wwise.

Se si vuole usare l'acustica del progetto con un motore audio diverso da Wwise, effettuare una richiesta di miglioramento nel [Forum di discussione sull'acustica del progetto](https://github.com/microsoft/ProjectAcoustics/issues). È possibile usare il plug-in Acoustics Unreal del progetto per eseguire query sui dati acustici e quindi effettuare chiamate API al motore.

## <a name="download-project-acoustics"></a>Scarica l'acustica del progetto
Se non è già stato fatto, scaricare il [progetto Acoustics unreal & pacchetto di plug](https://www.microsoft.com/download/details.aspx?id=58090)-in Wwise). 

Nel pacchetto è stato incluso un plug-in Unreal Engine e un plug-in Wwise mixer. Il plug-in Unreal fornisce l'integrazione di editor e Runtime. Durante il gameplay, il plug-in del progetto Acoustics Unreal Calcola parametri come l'occlusione per ogni oggetto di gioco ogni frame. Questi parametri vengono convertiti in chiamate API Wwise.

## <a name="review-integration-steps"></a>Esaminare i passaggi di integrazione

I passaggi principali per installare il pacchetto e distribuirlo nel gioco sono i seguenti.
1. Installare il plug-in Wwise mixer per il progetto Acoustics
2. (Re) distribuire Wwise nel gioco. Questo passaggio propaga il plug-in mixer nel progetto di gioco.
3. Aggiungere il plug-in del progetto Acoustics Unreal al gioco
4. Estendere la funzionalità del plug-in Unreal di Wwise
5. Il gioco di compilazione e il controllo Python è abilitato
6. Configurare il progetto Wwise per l'uso dell'acustica del progetto
7. Installazione audio in Unreal

## <a name="1-install-the-project-acoustics-mixer-plugin"></a>1. Installare il plug-in di Project Acoustics mixer
* Aprire Wwise Launcher (Avvio Wwise) e quindi nella scheda **Plugins** (Plug-in) selezionare **Add From Directory** (Aggiungi da directory) in **Install New Plugins** (Installa nuovi plug-in). 

    ![Screenshot dell'installazione di un plug-in in Wwise Launcher](media/wwise-install-new-plugin.png)

* Scegliere la directory `AcousticsWwisePlugin\ProjectAcoustics` inclusa nel pacchetto scaricato. Contiene il bundle del plug-in Wwise mixer.

* Wwise installerà il plug-in. Il progetto Acoustics dovrebbe essere ora visualizzato nell'elenco dei plug-in installati in Wwise.
![Screenshot dell'elenco dei plug-in installati di Wwise dopo l'installazione di Acoustics](media/unreal-integration-post-mixer-plugin-install.png)

## <a name="2-redeploy-wwise-into-your-game"></a>2. (Re) distribuire Wwise nel gioco
Ridistribuire Wwise nel gioco anche se Wwise è già stato integrato. Questa operazione preleva il plug-in Wwise per il progetto Acoustics.

* **Plug-in del motore:** Se è stato installato Wwise come plug-in del gioco in un C++ progetto Unreal, ignorare questo passaggio. Se viene installato invece come plug-in del motore, ad esempio perché il progetto Unreal è solo progetto, la distribuzione di Wwise con il plug-in del mixer è più complessa. Creare un progetto fittizio, vuoto C++ e non reale, chiuderlo se si apre l'editor Unreal e seguire la procedura rimanente per distribuire Wwise in questo progetto fittizio. Copiare quindi il plug-in Wwise distribuito.
 
* Da Wwise Launcher (Avvio Wwise) fare clic sulla scheda **Unreal Engine** e quindi fare clic sul menu hamburger accanto a **Recent Unreal Engine Projects** (Progetti recenti Unreal Engine) e selezionare **Browse for project** (Cerca progetto). Aprire il file di progetto `.uproject` Unreal del gioco.

    ![Screenshot della scheda Unreal dell'utilità di avvio Wwise](media/wwise-unreal-tab.png)

* Fare quindi clic su **integra Wwise in Project** o **modificare Wwise in Project**. Questo passaggio (re) integra i file binari di Wwise nel progetto, ora include il plug-in di Project Acoustics mixer.

* **Plug-in del motore:** Se si usa Wwise come plug- `[DummyUProject]\Plugins\Wwise` in del motore e il progetto fittizio è stato creato come descritto in precedenza, copiare la cartella Wwise distribuita e incollarla. `[UESource]\Engine\Plugins\Wwise` `[DummyUProject]`è il percorso di progetto C++ non reale vuoto ed `[UESource]` è la posizione in cui sono installate le origini Unreal Engine. Al termine della copia, è possibile eliminare il progetto fittizio.

## <a name="3-add-the-project-acoustics-unreal-plugin-to-your-game"></a>3. Aggiungere il plug-in del progetto Acoustics Unreal al gioco
 
* Copiare la `Unreal\ProjectAcoustics` cartella nel pacchetto di plug-in e creare una `[UProjectDir]\Plugins\ProjectAcoustics`nuova cartella `UProjectDir` , dove è la cartella di progetto del `.uproject` gioco che contiene il file.
  * **Plug**-in del motore: Se si usa Wwise come plug-in del motore, è consigliabile usare anche il progetto Acoustics come plug-in di Unreal Engine. Anziché la directory di destinazione precedente, usare: `[UESource]\Engine\Plugins\ProjectAcoustics`.

* Verificare che venga visualizzata `Wwise` una cartella insieme `ProjectAcoustics` alla cartella. Contiene il plug-in Wwise insieme ai file binari per il plug-in del mixer che è stato distribuito nel passaggio 2 precedente.

## <a name="4-extend-wwises-unreal-plugin-functionality"></a>4. Estendere la funzionalità del plug-in Unreal di Wwise
* Il plug-in del progetto Acoustics Unreal richiede che un comportamento aggiuntivo venga esposto dall'API del plug-in Wwise Unreal in base a [queste linee guida](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). È stato incluso un file batch per automatizzare la procedura di applicazione delle patch. 
* All'interno di `Plugins\ProjectAcoustics\Resources` eseguire `PatchWwise.bat`. L'immagine di esempio seguente usa il progetto di esempio AcousticsGame.

    ![Screenshot della finestra Esplora risorse che evidenzia lo script fornito per la patch Wwise](media/patch-wwise-script.png)

* Se DirectX SDK non è installato, è necessario impostare come commento la riga che contiene DXSDK_DIR in `[UProject]\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![Screenshot dell'editor di codice che mostra DXSDK impostato come commento](media/directx-sdk-comment.png)

## <a name="5-build-game-and-check-python-is-enabled"></a>5. Il gioco di compilazione e il controllo Python è abilitato

* Compilare il gioco e assicurarsi che venga compilato correttamente. In caso contrario, controllare attentamente i passaggi precedenti prima di procedere. 
* Aprire il progetto in Unreal Editor. 
* **Plug-in del motore:** Se si usa ProjectAcoustics come plug-in del motore, assicurarsi anche che sia abilitato, elencato in plug-in "integrati".
* Verrà visualizzata una nuova modalità, che indica che l'acustica del progetto è stata integrata.

    ![Screenshot della modalità di visualizzazione acustica completa](media/acoustics-mode-full.png)

* Confermare di avere il plug-in Python per Unreal Enabled. Questa operazione è necessaria per il corretto funzionamento dell'integrazione dell'editor.

    ![Screenshot dell'abilitazione delle estensioni Python in Unreal Editor](media/ensure-python.png)

## <a name="6-wwise-project-setup"></a>6. Installazione del progetto Wwise

Un esempio di progetto Wwise è incluso nel download degli esempi. Si consiglia di esaminarla insieme a queste istruzioni. Gli screenshot seguenti sono ricavati da questo progetto.

### <a name="bus-setup"></a>Configurazione del bus
* Il plug-in del progetto Acoustics Unreal Cerca il plug-in del mixer associato in un bus con questo `Project Acoustics Bus`nome esatto:. Creare un nuovo bus audio con questo nome. Il plug-in del mixer può funzionare in diverse configurazioni, ma per il momento si presuppone che venga usato solo per l'elaborazione del riverbero. Questo bus conterrà il segnale di riverbero misto per tutte le origini che usano acustica. È possibile combinare upstream in qualsiasi struttura di combinazione di bus, un esempio illustrato di seguito, tratto dal progetto di esempio Wwise incluso nel download di esempio.

    ![Screenshot di Wwise Buss che mostrano il bus acustico del progetto](media/acoustics-bus.png)

* La configurazione del canale sul bus deve essere impostata su uno dei seguenti: `1.0, 2.0, 4.0, 5.1 or 7.1`. Altre configurazioni non restituiranno alcun output in questo bus.

    ![Screenshot delle opzioni di configurazione del canale per il bus Acoustics del progetto](media/acoustics-bus-channel-config.png)

* Passare ora ai dettagli del bus di acustica del progetto e verificare che sia possibile visualizzare la scheda plug-in del mixer

    ![Screenshot di Wwise che illustra come abilitare la scheda plug-in del mixer per il bus Acoustics del progetto](media/mixer-tab-enable.png)

* Passare quindi alla scheda plug-in del mixer e aggiungere il plug-in di Project Acoustics mixer al bus

    ![Screenshow del bus Wwise che Mostra come aggiungere il plug-in di Project Acoustics mixer](media/add-mixer-plugin.png)

### <a name="actor-mixer-hierarchy-setup"></a>Impostazione della gerarchia Actor-mixer
* Per motivi di prestazioni, i progetti acustici applicano il DSP audio a tutte le origini simultaneamente. Questa operazione richiede che il plug-in funzioni come plug-in di mixer. Wwise richiede che i plug-in del mixer si trovino nel bus di output, anche se il bus di output contiene in genere il segnale di output asciutto. Per l'acustica del progetto è necessario che il segnale secco venga instradato attraverso gli autobus aux mentre il segnale `Project Acoustics Bus`bagnato viene portato sul. Il processo seguente supporta la migrazione graduale a questo flusso del segnale.

* Supponiamo di avere un progetto esistente con una gerarchia Actor-mixer contenente orme, armi e altri utenti al primo livello. Ogni ha un bus di output corrispondente per la combinazione secca. Si supponga di voler migrare i passi per usare le acustiche. Creare prima di tutto un bus aux corrispondente per portare il proprio submix secco che è un figlio del bus di output dei passi. Ad esempio, è stato usato un prefisso "Dry" nell'immagine seguente per organizzarle, anche se il nome esatto non è importante. Tutti i contatori o gli effetti presenti sul bus dei passi continueranno a funzionare come prima.

    ![Screenshot della configurazione consigliata di Wwise dry mix](media/wwise-dry-mix-setup.png)

* Modificare quindi la struttura di output del bus per i passi Actor-mixer come indicato di seguito, con il bus acustica del progetto impostato come bus di output e Dry_Footsteps impostato come bus aux definito dall'utente.

    ![Screenshot della configurazione consigliata del bus Wwise Actor](media/actor-mixer-bus-settings.png)

* Ora tutti i passi ricevono un trattamento acustico e ne generano il riverbero sul bus Acoustics del progetto. Il segnale secco viene instradato tramite Dry_Footsteps e spaziale come di consueto.

* Il progetto Acoustics si applica solo ai suoni con una posizione 3D nel mondo. Seguendo la [documentazione di Wwise](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/), è necessario impostare le proprietà di posizionamento come illustrato. L'impostazione "spazializzazione 3D" può essere "position" o "position + orientation" in base alle esigenze.

    ![Screenshot delle impostazioni consigliate per il posizionamento degli attori Wwise](media/wwise-positioning.png)

* L'impostazione del bus di output su un altro bus che combina upstream in **Project Acoustics bus** non funzionerà. Wwise impone questo requisito nei plug-in del mixer.

* Se si vuole che un figlio nella gerarchia Actor-mixer passi, non usi l'acustica, è sempre possibile usare l'opzione "Sostituisci elemento padre" per consentirne la scelta.

* Se si usano gli invii di gioco o definiti dall'utente per il riverbero in qualsiasi attore-mixer nel gioco, disattivarli in questo Actor-mixer per evitare di applicare due volte il riverbero.

### <a name="spatialization"></a>Spazializzazione
Per impostazione predefinita, il plug-in del mixer Wwise di Project Acoustics applica il riverbero della convoluzione, lasciando Wwise per eseguire la spazializzazione della panoramica. Quando si usa l'acustica del progetto in questa configurazione predefinita solo dei Riverb, è possibile usare qualsiasi metodo di configurazione e spazializzazione del canale per la combinazione a secco, consentendo di combinare praticamente qualsiasi Spatializer con il riverbero dei progetti acustici. Le opzioni disponibili includono [Ambisonics biarch spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/) e [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
Il progetto Acoustics include un Spatializer facoltativo che supporta il rendering HRTF ad alta risoluzione basato su oggetti e la panoramica. Selezionare la casella di controllo "Esegui spazializzazione" nelle impostazioni del plug-in del mixer e scegliere tra HRTF o panoramica e disabilitare le trasmissioni aux definite dall'utente impostate sopra a tutti i bus asciutti per evitare il spatializing due volte, sia con il plug-in del mixer del progetto acustico che con Wwise. Non è possibile modificare la modalità di spazializzazione in tempo reale perché richiede una rigenerazione della banca sonora. È necessario riavviare Unreal, quindi rigenerare SoundBanks prima di premere Play per prelevare le modifiche alla configurazione del plug-in del mixer, ad esempio la casella di controllo "Esegui spazializzazione".

![Screenshot delle impostazioni di spazializzazione del plug-in Wwise mixer](media/mixer-spatial-settings.png)

Sfortunatamente, in questo momento non è possibile supportare altri plug-in Spatializer basati su oggetti perché sono implementati come plug-in di mixer e Wwise attualmente non consente l'assegnazione di più plug-in di mixer a un singolo attore-mixer.  

## <a name="7-audio-setup-in-unreal"></a>7. Installazione audio in Unreal
* Prima di tutto è necessario preparare il livello di gioco per produrre un asset acustico, che verrà inserito in `Content\Acoustics`. Vedere l' [esercitazione Unreal Bake](unreal-baking.md) e riprendere qui. Alcuni livelli pre-cotti sono inclusi nel pacchetto di esempio.
* Creare un attore di spazio acustico nella scena. Creare solo uno di questi attori in un livello poiché rappresenta l'acustica per l'intero livello. 

    ![Screenshot di Unreal Editor che mostra la creazione di un attore di spazio acustico](media/create-acoustics-space.png)

* Assegnare ora l'asset di dati acustici al forno per lo slot di dati acustici nell'attore dello spazio acustico. La tua scena ora presenta acustici.

    ![Screenshot dell'assegnazione di asset howing Acoustics dell'editor non reale](media/acoustics-asset-assign.png)

* Aggiungere ora un attore vuoto ed eseguire le operazioni seguenti:

    ![Screenshot di Unreal Editor che mostra l'utilizzo dei componenti acustici in un attore vuoto](media/acoustics-component-usage.png)

1. Aggiungere un componente audio acustico all'attore. Questo componente estende il componente audio Wwise con funzionalità per l'acustica del progetto.
2. Per impostazione predefinita, la casella Riproduci in avvio è selezionata, che attiverà l'evento Wwise associato all'avvio del livello.
3. Utilizzare la casella di controllo Mostra parametri acustici per stampare le informazioni di debug sullo schermo relative all'origine.
    ![Screenshot del pannello Acoustics dell'editor non reale nell'origine audio con i valori di debug abilitati](media/debug-values.png)
4. Assegnare un evento Wwise per il normale flusso di lavoro di Wwise
5. Assicurarsi che l'opzione Usa audio spaziale sia spenta. A questo punto, se si usa l'acustica del progetto per un particolare componente audio, non è possibile usare contemporaneamente il motore audio spaziale di Wwise per l'acustica.

Le impostazioni sono state completate. Passa alla scena ed Esplora gli effetti acustici.

## <a name="next-steps"></a>Passaggi successivi
* Esercitazione di [progettazione](unreal-workflow.md) per l'acustica del progetto in Unreal/Wwise
* [Scopri come eseguire](unreal-baking.md) le operazioni di cottura per le tue scene di gioco 
