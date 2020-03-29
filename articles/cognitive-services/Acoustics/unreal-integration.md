---
title: Integrazione di Project Acoustics Unreal e Wwise
titlesuffix: Azure Cognitive Services
description: In questo articolo viene descritta l'integrazione dei plug-in Project Acoustics Unreal e Wwise nel progetto.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243052"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Integrazione di Project Acoustics Unreal e Wwise
In questo articolo viene descritto come integrare il pacchetto plug-in Project Acoustics nel progetto di gioco Unreal and Wwise esistente.

Requisiti software:
* [Motore irreale](https://www.unrealengine.com/) 4.20
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1
* [Plug-in Wwise per Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  
  Se si utilizza un'integrazione diretta dell'SDK di Wwise anziché del plug-in Wwise Unreal, consultare il plug-in Unreal di Project Acoustics e regolare le chiamate API Wwise.

Per utilizzare Project Acoustics con un motore audio diverso da Wwise, effettuare una richiesta di miglioramento nel forum di [discussione sull'acustica](https://github.com/microsoft/ProjectAcoustics/issues)del progetto. È possibile utilizzare il plug-in Non real di Project Acustics per eseguire query sui dati acustici ed effettuare chiamate API al motore.

## <a name="download-project-acoustics"></a>Scarica l'acustica del progetto
Scarica il [pacchetto di plug-in Project Acoustics Unreal e Wwise,](https://www.microsoft.com/download/details.aspx?id=58090) se non l'hai già fatto.

Un plug-in Unreal Engine e un plug-in del mixer Wwise sono inclusi nel pacchetto. Il plug-in Unreal fornisce l'integrazione di editor e runtime. Durante il gioco, il plug-in Unreal per l'acustica del progetto calcola parametri come l'occlusione per ogni oggetto del gioco per ogni fotogramma. Questi parametri vengono convertiti in chiamate API Wwise.

## <a name="integration-steps"></a>Procedura di integrazione

Segui questi passaggi per installare il pacchetto e distribuirlo nel gioco.

### <a name="install-the-project-acoustics-mixer-plug-in"></a>Installare il plug-in del mixer di acustica di progetto
1. Aprire il programma di avvio di Wwise. Nella scheda **Plug-in,** in **Installa nuovi plug-in**, selezionare **Aggiungi da directory**.

    ![Installare un plug-in nel programma di avvio di Wwise](media/wwise-install-new-plugin.png)

1. Selezionare la directory *AcousticsWwisePlugin-ProjectAcoustics* presente nel pacchetto di download. Esso contiene il pacchetto plug-in mixer Wwise.

   Wwise installerà il plug-in. L'acustica del progetto dovrebbe essere visualizzato nell'elenco dei plug-in installati in Wwise.  
![L'elenco dei plug-in installati Wwise dopo l'installazione di Project Acoustics](media/unreal-integration-post-mixer-plugin-install.png)

### <a name="dedeploy-wwise-into-your-game"></a>Dedistribuisci Wwise nel tuo gioco
Ridistribuisci Wwise nel tuo gioco anche se hai già integrato Wwise. Questo passaggio integra il plug-in Project Acoustics Wwise.

   > [!NOTE]
   > **Plug-in motore:** Se Wwise è installato come plug-in di gioco in un progetto Unreal C, salta questo passaggio. Se è installato invece come plug-in del motore, ad esempio perché il progetto Unreal è solo Blueprint, distribuzione Wwise con il nostro mixer plug-in è più complesso. Creare un progetto vuoto vuoto Unreal C. Chiudere l'editor Unreal se si apre e seguire la procedura rimanente per distribuire Wwise nel progetto fittizio. Quindi, copiare il plug-in Wwise distribuito.
 
1. Dal programma di avvio Wwise, selezionare la scheda **Unreal Engine.** Selezionare il menu "hamburger" (icona) accanto a **Recenti progetti di motori non reali** e quindi selezionare Cerca **progetto**. Apri il file di progetto Unreal del gioco *.project.*

    ![Scheda Unreal di avvio Wwise](media/wwise-unreal-tab.png)

1. Selezionare **Integra Wwise nel progetto** o **Modifica wwise nel progetto**. Questo passaggio integra i file binari Wwise nel progetto, incluso il plug-in del mixer Project Acoustics.

   > [!NOTE]
   > **Plug-in motore:** Se si utilizza Wwise come plug-in del motore ed è stato creato il progetto fittizio come descritto in precedenza, copiare la cartella distribuita da Wwise: *[DummyUProject].* Incollarlo sopra *[UESource],Engine/Plugins-Wwise*. *[DummyUProject]* è il percorso di progetto unreal C, vuoto, e *[UESource]* è il percorso in cui sono installate le origini unreal Engine. Dopo aver copiato la cartella, è possibile eliminare il progetto fittizio.

### <a name="add-the-project-acoustics-unreal-plug-in-to-your-game"></a>Aggiungi il plug-in Unreal per l'acustica del progetto al tuo gioco
 
1. Copiare la cartella *Unreal-ProjectAcoustics* nel pacchetto del plug-in. Creare una nuova cartella *[UProjectDir],Plugins-ProjectAcoustics*, dove *[UProjectDir]* è la cartella di progetto del gioco che contiene il file di progetto con *estensione u.*

   > [!NOTE]
   > **Plug-in del motore**: Se si utilizza Wwise come plug-in del motore, è necessario utilizzare Project Acoustics come plug-in del motore Unreal. Invece della directory di destinazione citata in precedenza, utilizzare *[UESource], Motore, Plugin, ProjectAcoustics*.

1. Verificare che venga visualizzata una cartella *Wwise* accanto alla cartella *ProjectAcoustics.* Contiene il plug-in Wwise insieme ai file binari per il plug-in mixer distribuito in precedenza.

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Estendere la funzionalità del plug-in Unreal di Wwise
Il plug-in Non real per l'acustica del progetto richiede un comportamento aggiuntivo esposto dall'API del plug-in Wwise Unreal in base alle [linee guida.](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html) Abbiamo incluso un file batch per automatizzare la procedura di patch.

* All'interno di *Plugins, ProjectAcoustics, Resources*, eseguire *PatchWwise.bat*. L'immagine di esempio seguente usa il progetto di esempio AcousticsGame.The following example image uses our AcousticsGame sample project.

    ![Una finestra di Esplora risorse con lo script per applicare la patch a Wwise evidenziato](media/patch-wwise-script.png)

* Se l'SDK di DirectX non è installato: a seconda della versione di Wwise in uso, potrebbe essere necessario impostare come commento la riga che contiene `DXSDK_DIR` l'opzione *AcousticsGame, Plugins, Wwise, Source, AkAudio.Build.cs*:

    ![L'editor di codice che mostra 'DXSDK' ha commentato](media/directx-sdk-comment.png)

* Se si esegue la compilazione utilizzando Visual Studio 2019: per risolvere `VSVersion` un errore di collegamento con Wwise, modificare manualmente il valore predefinito in *AcousticsGame, Plugins, Wwise, Source , AkAudio , AkAudio.Build.cs* a **vc150**:

    ![L'editor di codice con "VSVersion" modificato in "vc150"](media/vsversion-comment.png)

### <a name="build-the-game-and-check-that-python-is-enabled"></a>Costruisci il gioco e verifica che Python sia abilitato

1. Compila il tuo gioco e assicurati che venga compilato correttamente. Se non viene compilato, controllare attentamente i passaggi precedenti prima di continuare.

1. Apri il tuo progetto in Unreal Editor.

    > [!NOTE]
    > **Plug-in motore:** Se si utilizza ProjectAcoustics come plug-in del motore, assicurarsi inoltre che sia abilitato in plug-in "incorporati".

    Verrà visualizzata una nuova modalità, che indica che l'acustica del progetto è stata integrata.

    ![Modalità acustica completa in Unreal](media/acoustics-mode-full.png)

1. Verificare che il plug-in Python per Unreal sia abilitato in modo che l'integrazione dell'editor funzioni correttamente.

    ![Le estensioni Python nell'editor Unreal abilitate](media/ensure-python.png)

### <a name="set-up-your-wwise-project-to-use-project-acoustics"></a>Impostare il progetto Wwise per l'utilizzo di Project Acoustics

Un esempio di progetto Wwise è incluso nel download degli esempi. Si consiglia di visualizzarlo insieme a queste istruzioni. Le schermate più avanti in questo articolo sono da questo progetto.

#### <a name="bus-setup"></a>Configurazione del bus
Il plug-in Project Acoustics Unreal cercherà il plug-in mixer associato `Project Acoustics Bus`su un bus con il nome esatto. Creare un nuovo bus audio con lo stesso nome. Il plug-in del mixer può funzionare in varie configurazioni. Ma per ora, assumiamo che verrà utilizzato solo per l'elaborazione del riverbero. Questo bus porterà il segnale di riverbero misto per tutte le fonti che utilizzano l'acustica. Può mescolare a monte in qualsiasi struttura bus-mixing. Un esempio è illustrato di seguito dal progetto di esempio Wwise incluso nel download di esempio.

![Autobus Wwise con Project Acoustics Bus](media/acoustics-bus.png)

1. Impostare la configurazione del canale sul bus su *1.0*, *2.0*, *4.0*, *5.1*o *7.1*. Qualsiasi altra impostazione non comporterà alcunoutput sul bus.

    ![Opzioni di configurazione del canale per il bus di acustica del progetto](media/acoustics-bus-channel-config.png)

1. Accedere ai dettagli del bus acustico del progetto e assicurarsi di visualizzare la scheda **Plug-in Mixer.**

    ![Wwise con la scheda Plug-in Mixer per il bus di acustica del progetto abilitato](media/mixer-tab-enable.png)

1. Vai alla scheda **Plug-in Mixer** e aggiungi il plug-in del mixer acustico del progetto al bus.

    ![Diagramma di come aggiungere il plug-in del mixer per l'acustica del progetto al bus Wwise](media/add-mixer-plugin.png)

#### <a name="actor-mixer-hierarchy-setup"></a>Impostazione della gerarchia attore-mixer
Per ottenere prestazioni ottimali, Project Acoustics applica l'elaborazione del segnale audio digitale a tutte le sorgenti contemporaneamente. Così, il plug-in deve funzionare come un plug-in mixer. Wwise richiede che i plug-in del mixer siano sul bus di uscita, anche se il bus di uscita di solito trasporta il segnale di uscita secco. L'acustica di progetto richiede che il segnale secco venga instradato `Project Acoustics Bus`attraverso i bus aux, mentre il segnale bagnato viene trasportato sul . Il processo seguente supporta la migrazione graduale a questo flusso di segnale.

Si supponga di disporre di un progetto esistente con una gerarchia attore-mixer che contiene *passi,* *armi*e altri al livello superiore. Ognuno ha un bus di uscita corrispondente per il suo mix a secco. Supponiamo che tu voglia migrare i passi per usare l'acustica. In primo luogo, creare un bus aux corrispondente per portare il submix secco che è un figlio del bus di uscita passi. Ad esempio, abbiamo usato un prefisso "Dry" nell'immagine seguente per organizzare gli autobus, anche se il nome esatto non è importante. Tutti i metri o gli effetti che hai avuto sul bus passi funzioneranno ancora come prima.

![Configurazione consigliata del mix a secco Wwise](media/wwise-dry-mix-setup.png)

Successivamente, modificare la struttura di output del bus per l'attore-mixer Footsteps come indicato di seguito, con *il bus di acustica di progetto* impostato come bus di **output**e *Dry_Footsteps* impostato come bus aux definito dall'utente.

![Configurazione consigliata del bus del mixer attore Wwise](media/actor-mixer-bus-settings.png)

Ora tutti i passi ottenere il trattamento acustico e l'uscita del loro riverbero sul bus di acustica di progetto. Il segnale secco viene instradato attraverso Dry_Footsteps e spazializzato come al solito.

L'acustica del progetto si applica solo ai suoni che hanno una posizione 3D nel mondo. Seguendo la [documentazione di Wwise](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/), le proprietà di posizionamento devono essere impostate come illustrato. L'impostazione **Spazializzazione 3D** può essere *Posizione* o *Posizione - Orientamento* in base alle esigenze.

![Impostazioni di posizionamento attore Wwise consigliate](media/wwise-positioning.png)

Non è possibile impostare **il bus** di output su un altro bus che si mescola a monte in *Project Acoustics Bus*. Wwise impone questo requisito sui plug-in mixer.

Se si desidera che un figlio nei passi gerarchia attore-mixer per non utilizzare l'acustica, è possibile utilizzare "override padre" su di esso per rifiutare esplicitamente.

Se utilizzi invii per il riverbero definito dal gioco o dall'utente per qualsiasi attore-mixer nel gioco, disattivali su quell'attore-mixer per evitare di applicare il riverbero due volte.

#### <a name="spatialization"></a>Spazializzazione
Il plug-in del mixer Wwise Project Acoustics applica il riverbero di convoluzione per impostazione predefinita, lasciando Wwise a fare la spazializzazione panoramica. Quando si utilizza Project Acoustics in questa configurazione predefinita solo riverbero, è possibile utilizzare qualsiasi metodo di configurazione e spazializzazione del canale sul mix a secco. Così, è possibile mescolare e abbinare quasi qualsiasi spatializzatore con il riverbero di acustica di progetto. Le opzioni includono [spatializzatori binaurali basati su Ambisonics](https://www.audiokinetic.com/products/ambisonics-in-wwise/) e [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
Project Acoustics include uno strumento di spatialio opzionale che supporta sia il rendering HRTF basato su oggetti che il rendering HRTF basato su oggetti. Selezionare la casella di controllo **Esegui spazializzazione** nelle impostazioni del plug-in del mixer e scegliere tra *HRTF* o *Panning*. Inoltre, disabilitare aux definito dall'utente invia a tutti i bus a secco per evitare la spazializzazione due volte dal plug-in del mixer Project Acoustics e Wwise. La modalità di spazializzazione non può essere modificata in tempo reale perché richiede una solida rigenerazione bancaria. Riavviare Unreal, quindi rigenerare i soundbank prima di selezionare play per integrare le modifiche di configurazione del plug-in del mixer, ad esempio l'impostazione della casella di controllo **Esegui spazializzazione.**

![Impostazioni di spazializzazione del plug-in Wwise Mixer](media/mixer-spatial-settings.png)

Sfortunatamente, altri plug-in dello spatializer basato su oggetti non sono attualmente supportati. Sono implementati come plug-in mixer, e Wwise non consente più plug-in mixer da assegnare a un singolo attore-mixer.  

### <a name="audio-setup-in-unreal"></a>Configurazione audio in Unreal
1. In primo luogo, è necessario cuocere il livello di gioco per produrre una risorsa acustica, che verrà inserita in *Contenuto - Acustica*. Consultare [l'Esercitazione Sui Fori Unreal Bake](unreal-baking.md). Alcuni livelli precotti sono inclusi nel pacchetto di esempio.

1. Crea un attore spaziale acustica nella scena. Crea solo uno di questi attori in un livello, perché rappresenta l'acustica per l'intero livello.

    ![Creazione di un attore spaziale Acoustics nell'editore Unreal](media/create-acoustics-space.png)

1. Assegnare l'asset di dati acustici al set di dati Acustica nell'attore dello spazio Acustica. La tua scena ora ha l'acustica!

    ![Assegnazione di risorse acustiche nell'editor Unreal](media/acoustics-asset-assign.png)

1. Aggiungere un attore vuoto. Configurarlo come segue.

    ![L'editor Unreal mostra l'utilizzo del componente Acoustics in un attore vuoto](media/acoustics-component-usage.png)

       
    <sup>1</sup> Aggiungere un componente audio Acustica all'attore. Questo componente aggiunge la funzionalità di acustica di progetto al componente audio Wwise.
        
    <sup>2</sup> La casella **Riproduci all'inizio** è selezionata per impostazione predefinita. Questa impostazione attiva un evento Wwise associato all'avvio a livello.</li>
         
    <sup>3</sup> Utilizzare la casella di controllo **Mostra parametri acustici** per stampare le informazioni di debug su schermo sull'origine.

    ![Il pannello Acustica dell'editor Unreal sulla sorgente sonora con i valori di debug abilitati](media/debug-values.png)

    <sup>4</sup> Assegnare un evento Wwise per il normale flusso di lavoro Wwise.
       
    <sup>5</sup> Assicurarsi che **l'opzione Usa audio spaziale** sia disattivata. Se si utilizza Proibizioni progetto per un particolare componente audio, non è possibile utilizzare contemporaneamente il motore Audio spaziale di Wwise per l'acustica.</li>
       
Le impostazioni sono state completate. Muoversi intorno alla scena ed esplorare gli effetti acustici!

## <a name="next-steps"></a>Passaggi successivi
* Provare [l'esercitazione di progettazione di acustica di progetto unreal/Wwise](unreal-workflow.md).
* [Scopri come fare i dolci](unreal-baking.md) per le scene di gioco.
