---
title: Esercitazione - Istruzioni dettagliate per la creazione di una nuova app HoloLens Unity usando Ancoraggi nello spazio di Azure | Microsoft Docs
description: Questa esercitazione illustra come creare una nuova app HoloLens Unity usando Ancoraggi nello spazio di Azure.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/14/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3c125c54c1e0a70cdec19af912b17759d82a9936
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969430"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Esercitazione: Istruzioni dettagliate per la creazione di una nuova app HoloLens Unity usando Ancoraggi nello spazio di Azure

Questa esercitazione illustra come creare una nuova app HoloLens Unity con Ancoraggi nello spazio di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, accertarsi di avere:

1. Un computer Windows con <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017+</a> installato con il carico di lavoro **Sviluppo di app per la piattaforma UWP (Universal Windows Platform)** , il componente **Windows 10 SDK (10.0.17763.0 o versione successiva)** e <a href="https://git-scm.com/download/win" target="_blank">Git per Windows</a>.
2. [C++/WinRT Visual Studio Extension (VSIX)](https://aka.ms/cppwinrt/vsix) per Visual Studio installato da [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
3. Un dispositivo HoloLens con la [modalità sviluppatore](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) abilitata. Questo articolo richiede un dispositivo HoloLens con l'[Aggiornamento di Windows 10 (ottobre 2018)](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (noto anche come RS5). Per aggiornare alla versione più recente per HoloLens, aprire l'app **Impostazioni**, scegliere **Aggiornamento e sicurezza**, quindi selezionare il pulsante **Verifica disponibilità aggiornamento**.

## <a name="getting-started"></a>Introduzione

Per prima cosa configurare il progetto e la scena Unity:
1. Riavviare Unity.
2. Selezionare **Nuovo**.
4. Verificare che **3D** sia selezionato.
5. Denominare il progetto e immettere un **Percorso** di salvataggio.
6. Fare clic su **Crea progetto**.
7. Salvare la scena predefinita vuota in un nuovo file usando: **File** > **Salva con nome**.
8. Denominare la nuova scena **Principale** e premere il pulsante **Salva**.

**configurare le Impostazioni del Progetto**

Ora si imposteranno alcune impostazioni del progetto Unity che aiutano a usare l'SDK Windows Holographic come destinazione per lo sviluppo. 

In primo luogo, impostiare le impostazioni di qualità per l'applicazione. 
1. Selezionare **Modifica** > **Impostazioni progetto** > **Qualità**
2. Nella colonna sotto il logo **Windows Store**, fare clic sulla freccia nella riga **Impostazione predefinita** e selezionare **Molto bassa**. Si saprà che l'impostazione è stata applicata in modo corretto quando la casella nella colonna **Windows Store** e la riga **Molto bassa** è di colore verde.

È necessario informare Unity che l'app che si sta cercando di esportare deve creare una vista immersiva anziché una vista 2D. Creare una vista immersiva abilitando il supporto di realtà virtuale su Unity usando l'SDK Windows 10 come destinazione.

1. Andare a **Modifica** > **Impostazioni progetto** > **Lettore**.
2. Nel **Pannello inspector** per **Impostazioni lettore**, selezionare l'icona **Windows Store**.
3. Espandere il gruppo **Impostazioni XR**.
4. Nella sezione **Rendering**, spuntare la casella di controllo **Realtà virtuale supportato** per aggiungere un nuovo elenco di **SDK di Realtà virtuale**.
5. Verificare che **Realtà mista di Windows** sia visualizzato nell'elenco. In caso contrario, selezionare il pulsante **+** nella parte inferiore dell'elenco e scegliere **Realtà mista di Windows**.
 
> [!NOTE]
> Se non è possibile visualizzare l'icona di Windows Store, ricontrollare per assicurarsi di aver selezionato il back-end di scripting Windows Store .NET prima dell'installazione. In caso contrario, potrebbe essere necessario reinstallare Unity con la corretta installazione di Windows.

**verificare la configurazione .NET**
1. Passare a **Modifica** > **Impostazioni progetto** > **Lettore** (**Lettore** potrebbe essere ancora aperto dal precedente passaggio).
2. Nel **Pannello inspector** per **Impostazioni lettore**, selezionare l'icona **Windows Store**.
3. Nella sezione di Configurazione **Altre impostazioni**, assicurarsi che il **Back-end di scripting** sia impostato su **.NET**.

**impostare funzionalità**
1. Passare a **Modifica** > **Impostazioni progetto** > **Lettore** (**Lettore** potrebbe essere ancora aperto dal precedente passaggio).
2. Nel **Pannello inspector** per **Impostazioni lettore**, selezionare l'icona **Windows Store**.
3. Nella sezione di Configurazione **Impostazioni di pubblicazione**, spuntare **InternetClientServer** e **SpatialPerception**.

**configurare la fotocamera virtuale principale**
1. Nel **Pannello di gerarchia**, selezionare **Fotocamera principale**.
2. Nell'**Inspector**, impostare la posizione di trasformazione su **0,0,0**.
3. Trovare la proprietà **Cancella flag** e modificare l'elenco a discesa da **Skybox** a **Tinta unita**.
4. Fare clic sul campo **Sfondo** per aprire un editor dei colori.
5. Impostare **R, G, B e A** su **0**.
6. Selezionare **Aggiungi componente** e cercare **Collider mapping spaziale**.

**creare lo script**
1. Nel riquadro **Progetto**, creare una nuova cartella denominata **Script** nella cartella **Risorse**. 
2. Fare clic con il pulsante destro del mouse sulla cartella, quindi selezionare **Crea >** , **Script C#** . Intitolarlo **AzureSpatialAnchorsScript**. 
3. Andare a **GameObject** -> **Crea vuoto**. 
4. Selezionarlo e nell'**Inspector** rinominarlo da **GameObject** a **MixedRealityCloud**. Selezionare **Aggiungi componente** e cercare e aggiungere l'**AzureSpatialAnchorsScript**.

## <a name="trying-it-out"></a>Prova pratica
Per verificare che tutto funzioni, compilare l'app in **Unity** e distribuirla da **Visual Studio**. Seguire il Capitolo 6 dal corso [**Informazioni di base MR 100: Introduzione a Unity**](https://docs.microsoft.com/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) a questo scopo. Dovrebbe essere visibile la schermata di avvio di Unity e quindi una schermata chiara.

## <a name="place-an-object-in-the-real-world"></a>Posizionare un oggetto nel mondo reale
Creare e posizionare un oggetto con l'app. Aprire la soluzione di Visual Studio creata quando si è [distribuita l'app](#trying-it-out). 

Prima di tutto, aggiungere le istruzioni import seguenti in `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Quindi aggiungere le variabili membro seguenti nella classe `AzureSpatialAnchorsScript`: 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-37,43-47,55-74)]

Aggiungere poi il codice seguente nel metodo `Start()`. Questo codice si collegherà a `GestureRecognizer`, che rileva quando è presente una chiamata `HandleTap`.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=76-85,88&highlight=4-10)]

Ora è necessario aggiungere il metodo `HandleTap()` seguente sotto `Update()`. Eseguirà un cast di ray e otterrà un punto di riscontro in cui collocare una sfera. 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=264-274,295-297,301-309)]

È ora necessario creare la sfera. La sfera inizialmente sarà bianca, ma questo valore verrà regolato successivamente. Aggiungere il metodo `CreateAndSaveSphere()` seguente:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=311-324,389)]

Eseguire l'app da **Visual Studio** per convalidarla ancora una volta. Quindi, toccare lo schermo per creare e posizionare la sfera bianca sopra la superficie scelta.

## <a name="set-up-the-dispatcher-pattern"></a>Configurare il modello di dispatcher

Quando si lavora con Unity, tutte le API di Unity, ad esempio le API che si utilizzano per eseguire gli aggiornamenti dell'interfaccia utente, devono essere eseguite sul thread principale. Nel codice che si scriverà, tuttavia, si riceveranno callback su altri thread. Per aggiornare l'interfaccia utente in questi callback, è necessario un modo per passare da un thread laterale al thread principale. Per eseguire il codice nel thread principale da un thread laterale, usare il modello di dispatcher. 

Aggiungere una variabile membro, dispatchQueue, ovvero una Coda di Azioni. Inserire le Azioni nella coda e quindi rimuovere la coda ed eseguire le Azioni nel thread principale. 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=33-46&highlight=6-9)]

Successivamente, aggiungere un modo per aggiungere un'Azione alla Coda. Aggiungere `QueueOnUpdate()` subito dopo `Update()` :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=102-112)]

È possibile ora usare il ciclo Update() per verificare se è presente un'Azione in coda. In questo caso, l'azione verrà rimossa dalla cosa ed eseguita.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=90-100&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Ottenere l'SDK degli Ancoraggi nello spazio di Azure

Scaricare l'SDK degli Ancoraggi nello spazio di Azure. Andare alla [pagina delle versioni di GitHub degli Ancoraggi nello spazio di Azure](https://github.com/Azure/azure-spatial-anchors-samples/releases). In Risorse, scaricare il file **AzureSpatialAnchors.unitypackage**. 

In Unity, andare a **Risorse** e fare clic su **Importa pacchetto** > **Pacchetto personalizzato...** . Passare al pacchetto e selezionare **Apri**.

Nella nuova finestra **Importa pacchetto Unity** che viene visualizzata, selezionare **Nessuno** in basso a sinistra. Quindi in Plugin **AzureSpatialAnchorsPlugin** >  **** , selezionare **Comuni**, **Editor** e **HoloLens**. Fare clic su **Importa** nell'angolo in basso a destra.

È ora necessario ripristinare i pacchetti Nuget per ottenere l'SDK degli Ancoraggi nello spazio di Azure. Eseguire la compilazione da **Unity** e quindi aprire e compilare nuovamente la soluzione **Visual Studio** risultante, come descritto in maniera dettagliata in [Prova pratica](#trying-it-out). 

Nella soluzione **Visual Studio**, aggiungere l'importazione seguente in `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

Quindi aggiungere le variabili membro seguenti nella classe `AzureSpatialAnchorsScript`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=43-58&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Collegare un Ancoraggio nello spazio di Azure locale all'ancoraggio locale

Configurare CloudSpatialAnchorSession dell'ancoraggio nello spazio di Azure. Aggiungere poi il metodo `InitializeSession()` seguente nella classe `AzureSpatialAnchorsScript`. Quando viene chiamato, questo metodo assicura che durante l'avvio dell'app venga creata e inizializzata correttamente una sessione di Ancoraggi nello spazio di Azure.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=169-197,200-204)]

È ora necessario scrivere il codice per gestire le chiamate al delegato. Se ne aggiungeranno altre mentre si prosegue.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=206-221)]

Associare quindi il metodo `initializeSession()` al metodo `Start()`.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=76-88&highlight=12)]

Infine, aggiungere il codice seguente nel metodo `CreateAndSaveSphere()`. Il codice collega un ancoraggio nello spazio di Azure locale alla sfera posizionata nel mondo reale.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=311-337,390&highlight=16-31)]

Prima di procedere, sarà necessario creare un identificatore e una chiave dell'account di Ancoraggi nello spazio di Azure, se non è già stato fatto. Per ottenerli, seguire la sezione seguente.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Caricare l'ancoraggio locale nel cloud

Dopo aver creato l'Identificatore e la Chiave dell'account degli Ancoraggi nello spazio di Azure, incollare `Account Id` in `SpatialAnchorsAccountId` e `Account Key` in `SpatialAnchorsAccountKey`.

Infine, associare tutti gli elementi tra loro. Aggiungere il codice seguente nel metodo `SpawnNewAnchoredObject()`. Il codice richiama il metodo `CreateAnchorAsync()` non appena verrà creata la sfera. Quando il metodo restituisce il risultato, il codice seguente esegue l'aggiornamento finale della sfera, cambiandone il colore in blu.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=311-389&highlight=28-78)]

Eseguire nuovamente l'app da **Visual Studio**. Muovere la testa e quindi indicare un punto per inserire la sfera. Quando il numero di fotogrammi è sufficiente, la sfera diventerà gialla e verrà avviato il caricamento nel cloud. Al termine del caricamento, la sfera diventerà blu. Facoltativamente, è anche possibile usare la finestra di Output all'interno di **Visual Studio** per monitorare i messaggi di log inviati dall'app. Sarà possibile guardare i consigli per creare progresso, nonché l'identificatore dell'ancoraggio restituito dal cloud al termine del caricamento.

> [!NOTE]
> Se viene visualizzato "DllNotFoundException: Unable to load DLL 'AzureSpatialAnchors': The specified module could not be found.", è necessario **rimuovere** e **compilare** nuovamente la soluzione.

## <a name="locate-your-cloud-spatial-anchor"></a>Individuare l'ancoraggio nello spazio nel cloud

Dopo aver caricato l'ancoraggio nel cloud, è possibile provare a individuarlo di nuovo. Aggiungere il codice seguente nel metodo `HandleTap()`. Il codice consentirà di:

* Chiamare `ResetSession()`, che interromperà `CloudSpatialAnchorSession` e rimuoverà la sfera blu esistente dalla schermata.
* Inizializzare `CloudSpatialAnchorSession` nuovamente. Questa operazione permette di assicurarsi che l'ancoraggio da individuare proviene dal cloud e non è l'ancoraggio locale creato.
* Creare un **Watcher** che cercherà l'ancoraggio caricato negli Ancoraggi nello spazio di Azure.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=264-302&highlight=13-31,34-36)]

Aggiungere i metodi `ResetSession()` e `CleanupObjects()`. È possibile posizionarli sotto `QueueOnUpdate()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=114-167)]

Ora è necessario associare il codice che verrà richiamato quando verrà individuato l'ancoraggio tramite la query. All'interno di `InitializeSession()`, aggiungere i callback seguenti:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=195-201&highlight=4-5)]

 
Aggiungere il codice che creerà e posizionerà una sfera verde quando verrà individuato il CloudSpatialAnchor. Consente inoltre di toccare di nuovo lo schermo, quindi sarà possibile ripetere ancora una volta l'intero scenario, ossia creare un altro ancoraggio locale, caricarlo e individuarlo.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=223-262)]

L'operazione è terminata. Eseguire l'app da **Visual Studio** un'ultima volta per provare l'intero scenario dall'inizio alla fine. Muovere il dispositivo e posizionare la sfera bianca. Quindi continuare a muovere la testa per acquisire i dati dell'ambiente finché la sfera non diventa gialla. L'ancoraggio locale verrà caricato e la sfera diventerà blu. Infine, toccare ancora una volta lo schermo in modo da rimuovere l'ancoraggio locale ed eseguire una query per trovare la controparte nel cloud. Continuare a muovere il dispositivo finché non viene individuato l'ancoraggio nello spazio nel cloud. Dovrebbe comparire una sfera verde nella posizione corretta ed è possibile pulire e ripetere di nuovo l'intero scenario.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]