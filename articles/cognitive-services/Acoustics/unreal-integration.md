---
title: Integrazione Wwise e Unreal acustica progetto
titlesuffix: Azure Cognitive Services
description: Questa guida procedurale descrive l'integrazione del progetto acustica Unreal e Wwise i plug-nel progetto.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: c6baa9f8330338c1e5fdc9ee0b5a8cc8b344e871
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61436016"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Integrazione Wwise e Unreal acustica progetto
Questa procedura viene descritta la procedura dettagliata l'integrazione del pacchetto progetto acustica plug-nel progetto gioco Unreal e Wwise esistente. 

Requisiti software:
* [Unreal Engine](https://www.unrealengine.com/) 4.20 o 4.21
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1.\*
* [Plug-in Wwise per Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  * Se si usa un'integrazione diretta di SDK Wwise invece di usare i plug-in Wwise Unreal, consultare il progetto acustica Unreal plug-in e modificare le chiamate API Wwise.

Se si desidera usare acustica progetto con un motore diverso da Wwise audio, contattaci sul [forum di Project acustica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics). È possibile usare il plug-in progetto acustica Unreal per eseguire query sui dati acustica e quindi chiamare le API per il motore.

## <a name="download-project-acoustics"></a>Download progetto acustica
Se hai già fatto, scaricare il [pacchetto di plug-in progetto acustica Unreal & Wwise](https://www.microsoft.com/download/details.aspx?id=58090)). 

Sono stati inclusi un plug-in Unreal Engine e un plug-in di mixer Wwise nel pacchetto. Il plug-in Unreal fornisce editor e runtime di integrazione. Durante la modalità di gioco, il plug-in progetto acustica Unreal calcola i parametri, ad esempio relative all'occlusione per ogni oggetto gioco ogni fotogramma. Questi parametri vengono convertiti in chiamate API Wwise.

## <a name="review-integration-steps"></a>Rivedere la procedura di integrazione

Sono disponibili questi passaggi principali per installare il pacchetto e distribuirla nel tuo gioco.
1. Installare il plug-in progetto acustica Wwise mixer
2. Distribuire il tuo gioco Wwise (ri). Questo passaggio si propaga il plug-in di mixer nel progetto di gioco.
3. Aggiungere il progetto acustica Unreal plug-in per il tuo gioco
4. Estendere la funzionalità del plug-in Unreal di Wwise
5. Compilare giochi e controllare che Python è abilitata
6. Configurare il progetto Wwise usare acustica progetto
7. Programma di installazione audio in Unreal

## <a name="1-install-the-project-acoustics-mixer-plugin"></a>1. Installare il plug-in progetto acustica mixer
* Aprire Wwise Launcher (Avvio Wwise) e quindi nella scheda **Plugins** (Plug-in) selezionare **Add From Directory** (Aggiungi da directory) in **Install New Plugins** (Installa nuovi plug-in). 

    ![Screenshot dell'installazione di un plug-nell'utilità di avvio Wwise](media/wwise-install-new-plugin.png)

* Scegliere la directory `AcousticsWwisePlugin\ProjectAcoustics` inclusa nel pacchetto scaricato. Contiene il bundle di plug-in di mixer Wwise.

* Wwise verrà installato il plug-in. Progetto acustica dovrebbe ora essere inclusa nell'elenco dei plug-in installati in Wwise.
![Elenco di plug-in screenshot di Wwise installato dopo l'installazione acustica progetto](media/unreal-integration-post-mixer-plugin-install.png)

## <a name="2-redeploy-wwise-into-your-game"></a>2. (Ri) distribuire Wwise nel tuo gioco
Distribuire di nuovo Wwise al gioco anche se già stato integrato Wwise. Tale filtro preleva il plug-in Wwise acustica progetto.

* **Plug-in di gestione:** Se hai installato come un plug-in gioco in un progetto C++ Unreal Wwise, ignorare questo passaggio. Se è installato come un plug-in di gestione, ad esempio perché il progetto Unreal distribuzione Wwise solo, progetto con il plug-in di mixer è più complessa. Creare un progetto C++ Unreal vuoto fittizio, chiuderla se viene aperto l'editor Unreal e seguire la procedura per distribuire Wwise in questo progetto fittizio rimanente. Copiare quindi il plug-in Wwise distribuito.
 
* Da Wwise Launcher (Avvio Wwise) fare clic sulla scheda **Unreal Engine** e quindi fare clic sul menu hamburger accanto a **Recent Unreal Engine Projects** (Progetti recenti Unreal Engine) e selezionare **Browse for project** (Cerca progetto). Aprire progetti Unreal del gioco `.uproject` file.

    ![Scheda Unreal del screenshot di Wwise dell'utilità di avvio](media/wwise-unreal-tab.png)

* Quindi fare clic su **integrare Wwise nel progetto** oppure **modificare Wwise nel progetto**. Questo passaggio (ri) si integra i file binari Wwise nel progetto, ora include il plug-in di mixer acustica di progetto.

* **Plug-in di gestione:** Se si usa Wwise come un plug-in di gestione e creazione del progetto fittizio come in precedenza, copiare la cartella Wwise distribuito: `[DummyUProject]\Plugins\Wwise` e incollarlo su `[UESource]\Engine\Plugins\Wwise`. `[DummyUProject]` è il percorso del progetto C++ Unreal vuoto e `[UESource]` è quando si dispone di origini Unreal Engine installate. Dopo aver completato la copia, è possibile eliminare il progetto fittizio.

## <a name="3-add-the-project-acoustics-unreal-plugin-to-your-game"></a>3. Aggiungere il progetto acustica Unreal plug-in per il tuo gioco
 
* Copia il `Unreal\ProjectAcoustics` cartella del plug-in pacchetti e creare una nuova cartella `[UProjectDir]\Plugins\ProjectAcoustics`, dove `UProjectDir` è la cartella di progetto del gioco contenente il `.uproject` file.
  * **Plug-in motore**: Se si usa Wwise come un plug-in di gestione, è opportuno utilizzare acustica progetto come un Unreal engine plug-in anche. Invece di directory di destinazione precedenti, usare: `[UESource]\Engine\Plugins\ProjectAcoustics`.

* Verificare una `Wwise` cartella insieme il `ProjectAcoustics` cartella. Contiene il plug-in di Wwise insieme ai file binari per il plug-in di mixer (re-) distribuito nel passaggio 2 precedente.

## <a name="4-extend-wwises-unreal-plugin-functionality"></a>4. Estendere la funzionalità del plug-in Unreal di Wwise
* Il plug-in progetto acustica Unreal comportamenti aggiuntivi è necessario essere esposta da Wwise Unreal plug-in API per ogni [queste linee guida](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). Abbiamo incluso un file batch per automatizzare la procedura dell'applicazione di patch. 
* All'interno di `Plugins\ProjectAcoustics\Resources` eseguire `PatchWwise.bat`. Immagine di esempio seguente usa il progetto di esempio AcousticsGame.

    ![Screenshot di Esplora Windows nella finestra evidenziata fornito lo script alla patch Wwise](media/patch-wwise-script.png)

* Se DirectX SDK non è installato, è necessario impostare come commento la riga che contiene DXSDK_DIR in `[UProject]\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![Screenshot dell'editor di codice che mostra DXSDK impostato come commento](media/directx-sdk-comment.png)

## <a name="5-build-game-and-check-python-is-enabled"></a>5. Compilare giochi e controllare che Python è abilitata

* Compilare il tuo gioco e assicurarsi che la compilazione in modo corretto. In caso contrario, controllare i passaggi precedenti con attenzione prima di procedere. 
* Aprire il progetto nell'Editor Unreal. 
* **Plug-in di gestione:** Se utilizza ProjectAcoustics come plug-in di gestione, assicurarsi anche che è abilitato, elencato in "predefiniti" plug-in.
* Verrà visualizzata una nuova modalità, che indica che è stato integrato acustica di progetto.

    ![Schermata di Unreal con acustica modalità completa](media/acoustics-mode-full.png)

* Confermare di che avere il plug-in Python per Unreal abilitata. È necessario per il corretto funzionamento dell'integrazione dell'editor.

    ![Schermata di abilitazione delle estensioni di Python nell'editor Unreal](media/ensure-python.png)

## <a name="6-wwise-project-setup"></a>6. Programma di installazione di Wwise progetto

Un progetto di esempio Wwise è incluso nel download di esempi. È consigliabile esaminare insieme a queste istruzioni. Gli screenshot seguenti sono tratti dal progetto.

### <a name="bus-setup"></a>Configurazione bus
* Il plug-in progetto acustica Unreal cercherà il plug-in di mixer associati in un bus avendolo ***esatta*** nome: `Project Acoustics Bus`. Creare un nuovo bus audio con questo nome. Il plug-in di mixer possono essere usati in diverse configurazioni, ma per il momento si presuppone che verrà utilizzato per riverbero solo l'elaborazione. Il bus trasporterà il segnale riverbero mista per tutte le origini che usano acustica. Possono essere combinati a monte nel bus di qualsiasi combinazione di struttura, un esempio è illustrato di seguito, eseguito da questo progetto di esempio Wwise incluso nel download di esempio.

    ![Schermata di Wwise bus visualizzazione progetto acustica Bus](media/acoustics-bus.png)

* La configurazione di canali sul bus deve essere impostato su uno di: `1.0, 2.0, 4.0, 5.1 or 7.1`. Altri Configs (configurazioni) verrà generato alcun output di questo bus.

    ![Screenshot delle opzioni di configurazione del canale per il progetto acustica Bus](media/acoustics-bus-channel-config.png)

* Inizia subito a acustica il progetto del bus di dettagli e assicurarsi che è possibile visualizzare la scheda del plug-in Mixer

    ![Schermata di Wwise che mostra come abilitare la scheda di Mixer plug-in per il Bus di acustica progetto](media/mixer-tab-enable.png)

* Quindi passare alla scheda Mixer plug-in e aggiungere il mixer acustica progetto plug-in per il bus di

    ![Screenshot di Wwise bus che illustra come aggiungere il plug-in progetto acustica Mixer](media/add-mixer-plugin.png)

### <a name="actor-mixer-hierarchy-setup"></a>Programma di installazione di attore mixer gerarchia
* Per motivi di prestazioni acustica progetto applica DSP audio contemporaneamente a tutte le origini. Questa operazione richiede il plug-in per operare come un plug-in di mixer. Wwise richiede mixer i plug-in da posizionare sul bus di output, sebbene il bus di output contiene in genere il segnale di output dry. Progetto acustica richiede il segnale sorgente essere instradata attraverso i bus aux mentre il segnale elaborato viene eseguito di `Project Acoustics Bus`. Il processo seguente supporta la migrazione graduale a questo flusso di segnale.

* Si supponga di avere un progetto esistente con una gerarchia dell'attore mixer contenente personaggi, armi e ad altri utenti al primo livello. Ognuno ha corrispondente del bus di output per la combinazione di dry. Consente ad esempio si vuole eseguire la migrazione di personaggi aggiornerò acustica. Creare innanzitutto un bus aux corrispondente per il trasporto di loro submix dry figlio del bus di output di personaggi. Ad esempio, è stato utilizzato un prefisso "Asciutto" nell'immagine seguente per organizzare questi, anche se il nome esatto non è importante. Qualsiasi misura o effetti che era sul bus personaggi continueranno a funzionare come in precedenza.

    ![Screenshot della configurazione combinazione Dry Wwise consigliata](media/wwise-dry-mix-setup.png)

* Quindi modificare la struttura di output del bus per l'attore-mixer personaggi come indicato di seguito, con il Bus di acustica progetto impostato come il Bus di Output e Dry_Footsteps impostato come un'istanza del bus aux definito dall'utente.

    ![Screenshot del programma di installazione di consigliato Wwise attore Mixer Bus](media/actor-mixer-bus-settings.png)

* Ora tutti i personaggi ricevono acustica e output relativi riverbero sul Bus acustica progetto. Il segnale sorgente viene instradato attraverso Dry_Footsteps e spatialized come di consueto.

* Progetto acustica si applica solo ai suoni con una posizione 3D in tutto il mondo. Seguendo [Wwise documentazione](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/), la proprietà di posizionamento deve essere impostata come illustrato. L'impostazione "Spatialization 3D" può essere "Position" o "Posizione + Orientation" in base alle esigenze.

    ![Screenshot delle impostazioni di posizionamento dell'attore Wwise consigliato](media/wwise-positioning.png)

* Se si imposta il Bus di Output su alcuni altri bus con una combinazione a monte nel **progetto acustica Bus** non funzionerà. Wwise impone questo requisito per i plug-in di mixer.

* Se si desidera che un elemento figlio nella gerarchia di attore mixer personaggi di non usare acustica, è sempre possibile usare "sostituzione padre" su di esso, escludere.

* Se si usa gioco - o definite dall'utente inviati per riverbero su qualsiasi mixer dell'attore in gioco, disabilitare quelli su questo mixer attore di evitare l'applicazione riverbero due volte.

### <a name="spatialization"></a>Spatialization
Per impostazione predefinita, il plug-in progetto acustica Wwise mixer applica riverbero convoluzione, lasciando Wwise a scopo spatialization Panoramica. Quando si usa Project acustica in questa configurazione predefinita, solo riverbero, si possono usare qualsiasi metodo di configurazione e spatialization canale sulla combinazione dry, consentendo di combinare e abbinare quasi qualsiasi spaziale con riverbero degli progetto acustica. Le opzioni disponibili includono [basate su Ambisonics binaurali spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/) e [Sonic Windows](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
Progetto acustica include un spaziale facoltativo che supporta il rendering basato su oggetti HRTF ad alta risoluzione e la panoramica. Selezionare la casella di controllo "Eseguire Spatialization" per le impostazioni di plug-in di mixer e scegliere tra HRTF o panoramica e disabilitare invia aux definito dall'utente, impostare sopra per tutti i bus dry per evitare spatializing due volte, sia con plug-in di mixer progetto acustica e Wwise. Impossibile modificare la modalità spatialization in tempo reale, perché è necessaria una rigenerazione bank audio. È necessario riavviare Unreal, quindi rigenerare banchi prima di raggiungere play per visualizzare le modifiche di configurazione di mixer plug-in, ad esempio la casella di controllo 'Eseguire Spatialization'.

![Impostazioni screenshot di Wwise Mixer plug-in Spatialization](media/mixer-spatial-settings.png)

Sfortunatamente, altri plug-in basate su oggetto spaziale non può essere supportata in questa fase vengono implementate come plug-in di mixer e Wwise attualmente non consente più plug-in di mixer assegnato a un singolo attore-mixer.  

## <a name="7-audio-setup-in-unreal"></a>7. Programma di installazione audio in Unreal
* È necessario prima di tutto, vengono inseriti il livello di gioco per generare un asset acustica, quale sarà inserito in `Content\Acoustics`. Consultare il [Unreal esercitazione mostrandone](unreal-baking.md) e riprendere qui. Alcuni livelli di pre-virtuali create con bake sono inclusi nel pacchetto di esempio.
* Creare un attore acustica spazio nella scena. Creare solo uno di questi attori in un livello in quanto rappresenta l'acustica per il livello di intero. 

    ![Schermata di Unreal editor che illustra la creazione dell'attore acustica spazio](media/create-acoustics-space.png)

* Assegnare a questo punto l'asset di dati acustici virtuali create con bake allo slot di dati acustica sull'actor acustica spazio. La scena ha ora acustica!

    ![Schermata di Unreal acustica di howing editor s assegnazione Asset](media/acoustics-asset-assign.png)

* A questo punto aggiungere un attore vuoto e seguire questa procedura:

    ![Schermata di Unreal editor che illustrano l'utilizzo di acustica componente in un attore vuoto](media/acoustics-component-usage.png)

1. Aggiungere un componente acustica Audio all'attore. Questo componente consente di estendere il componente audio Wwise con la funzionalità per progetto acustica.
2. La riproduzione nella finestra di avvio è selezionata per impostazione predefinita, che determinerà l'evento Wwise associato all'avvio del livello.
3. Utilizzare la casella di controllo Mostra parametri acustica per stampare le informazioni sull'origine di debug.
    ![Schermata di Unreal pannello acustica dell'editor in origine audio con valori di debug abilitato](media/debug-values.png)
4. Assegnare un evento Wwise per il normale flusso di lavoro Wwise
5. Assicurarsi che funzionalità spaziali Audio è disattivato. A questo punto, se si usa Project acustica per un particolare componente audio, è Impossibile usare contemporaneamente motore spaziali Audio del Wwise per acustica.

Le impostazioni sono state completate. Spostare la scena ed esplorare i modello acustici effetti!

## <a name="next-steps"></a>Passaggi successivi
* [Progettazione](unreal-workflow.md) esercitazione per progetto acustica in Unreal/Wwise
* [Informazioni su come eseguire questa operazione Prepara](unreal-baking.md) per le scene di gioco 
