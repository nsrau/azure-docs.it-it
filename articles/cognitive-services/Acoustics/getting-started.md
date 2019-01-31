---
title: Introduzione a Project Acoustics
titlesuffix: Azure Cognitive Services
description: Questa guida introduttiva illustra come integrare il plug-in nel progetto Unity, effettuare il bake della scena e applicare l'acustica alle sorgenti audio.
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 0aad231b4a57c2d9927a553a43394681282b5494
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155953"
---
# <a name="getting-started-with-project-acoustics"></a>Introduzione a Project Acoustics
Questa guida introduttiva illustra come integrare il plug-in nel progetto Unity, effettuare il bake della scena e applicare l'acustica alle sorgenti audio. Per questa guida introduttiva, è necessario prima di tutto creare un [account Azure Batch](create-azure-account.md). Questa guida presuppone una certa conoscenza di Unity.

## <a name="download-the-plugin"></a>Scaricare il plug-in
Registrarsi [qui](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u) per partecipare alla Designer Preview.

## <a name="supported-platforms-for-quickstart"></a>Piattaforme supportate per la guida introduttiva
* [Unity 2018.2+](http://www.unity3d.com)
  * È necessario impostare il progetto sulla versione del runtime di scripting **.NET 4.x Equivalent** 
  * Richiede l'editor Unity basato su Windows

## <a name="import-the-plugin"></a>Importare il plug-in
Importare il pacchetto di acustica UnityPackage nel progetto. 
* In Unity passare a **Assets -> Import Package -> Custom Package** (Asset -> Importa pacchetto -> Pacchetto personalizzato).

![Importare il pacchetto](media/ImportPackage.png)  

* Scegliere **MicrosoftAcoustics.unitypackage**

Se si sta importando il plug-in un progetto esistente, nella radice del progetto può già essere presente un file **mcs.rsp**, che specifica le opzioni per il compilatore C#. È necessario unire il contenuto di tale file con il file mcs.rsp fornito con il plug-in Project Acoustics.

## <a name="enable-the-plugin"></a>Abilitare il plug-in
La parte per il baking del toolkit di acustica richiede la versione del runtime di scripting .NET 4.x. L'importazione del pacchetto aggiornerà le impostazioni del lettore Unity. Riavviare Unity per l'applicazione di queste impostazioni.

![Impostazioni del lettore](media/PlayerSettings.png)

![.NET 4.5](media/Net45.png)

## <a name="create-a-navigation-mesh"></a>Creare una mesh di navigazione
Usare il [flusso di lavoro standard di Unity](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html) per creare una mesh di navigazione per il progetto. Per informazioni su come usare mesh di navigazione personalizzate, vedere la [procedura dettagliata per effettuare il bake dell'interfaccia utente](bake-ui-walkthrough.md).

## <a name="mark-static-meshes-for-acoustics"></a>Contrassegnare le mesh statiche per l'acustica
Visualizzare la finestra dell'acustica selezionando **Window > Acoustics**  (Finestra > Acustica) in Unity. In questa finestra può essere ancorata accanto all'Inspector.

![Aprire la finestra Acoustics (Acustica)](media/WindowAcoustics.png)

Nella finestra della gerarchia di Unity deselezionare qualsiasi elemento selezionato. Nella scheda **Objects** (Oggetti) fare clic sulla casella di controllo "Acoustics Geometry" (Geometria acustica) per contrassegnare tutte le mesh e i terreni nella scena come geometria acustica.

Nella scheda **Materials** (Materiali) assegnare i materiali acustici ai materiali usati nella scena. Il materiale **predefinito** ha un assorbimento equivalente al calcestruzzo. Per altre informazioni su come specificare proprietà dei materiali personalizzate, vedere la [pagina del processo di progettazione](design-process.md).

![Scheda Materials (Materiali)](media/MaterialsTab.png)

## <a name="preview-the-probes"></a>Visualizzare in anteprima i probe
Nella scheda **Probes** (Probe) fare clic su **Calculate** (Calcola). Il calcolo può richiedere alcuni minuti, a seconda delle dimensioni della scena. Una volta completato il calcolo, nella visualizzazione scena compariranno delle sfere fluttuanti che contrassegnano le posizioni per la simulazione acustica, denominate "punti di probe". Se si osserva un oggetto sufficientemente da vicino nella finestra della scena, è anche possibile visualizzare la voxelizzazione della scena. I voxel verdi dovrebbero allinearsi con gli oggetti contrassegnati come geometria. È possibile attivare/disattivare la visualizzazione dei punti di probe e dei voxel nel menu Gizmos nella parte superiore destra della visualizzazione scena.

![Anteprima Gizmos](media/BakePreviewWithGizmos.png)

## <a name="bake-the-scene"></a>Effettuare il bake della scena
Nella scheda **Bake** immettere le credenziali di Azure e fare clic su **Bake**. Se non si ha un account Azure Batch, vedere [questa procedura dettagliata per la configurazione consigliata dell'account](create-azure-account.md).
Al termine del bake, il file di dati verrà automaticamente scaricato nella directory **Assets/AcousticsData** del progetto.

## <a name="set-up-audio-runtime-dsp"></a>Configurare il DSP del runtime audio
Il DSP del runtime audio per l'acustica viene incorporato nel framework dello spazializzatore di Unity e integrato con la spazializzazione basata su HRTF. Per abilitare l'elaborazione dell'acustica, passare allo spazializzatore **Microsoft Acoustics** selezionando **Edit > Project Settings > Audio** (Modifica > Impostazioni di progetto > Audio) e selezionare **Microsoft Acoustics** come **Spatializer Plugin** (Plug-in di spazializzazione) per il progetto. Inoltre, assicurarsi che **DSP Buffer Size** (Dimensioni buffer DSP) sia impostato su Best Performance (Prestazioni ottimali).

![Impostazioni progetto](media/ProjectSettings.png)  

![Spazializzatore di Project Acoustics](media/ChooseSpatializer.png)

Aprire il mixer audio selezionando **Window > Audio Mixer** (Finestra > Mixer audio). Assicurarsi di avere almeno un mixer, con un gruppo. In caso contrario, fare clic sul pulsante "+" a destra di **Mixers** (Mixer). Fare clic sulla parte inferiore della barra del canale nella sezione effetti e aggiungere l'effetto **Microsoft Acoustics Mixer** (Mixer Microsoft Acoustics). Si noti che è supportato un solo mixer di Project Acoustics alla volta.

![Mixer audio](media/AudioMixer.png)

## <a name="set-up-the-acoustics-lookup-table"></a>Configurare la tabella di ricerca dell'acustica
Trascinare e rilasciare l'oggetto Prefab **Microsoft Acoustics** dal pannello del progetto alla scena:

![Prefab Acoustics](media/AcousticsPrefab.png)

Fare clic sull'oggetto gioco **ProjectAcoustics** e passare al relativo pannello inspector. Specificare il percorso del risultato del bake (il file con estensione ACE, in **Assets/AcousticsData**) trascinandolo nello script Acoustics Manager o facendo clic sul pulsante circolare accanto alla casella di testo.

![Acoustics Manager](media/AcousticsManager.png)  

## <a name="apply-acoustics-to-sound-sources"></a>Applicare l'acustica alle sorgenti audio
Creare una sorgente audio. Fare clic sulla casella di controllo nella parte inferiore del pannello inspector della sorgente audio con la dicitura **Spatialize** (Spazializza). Assicurarsi che **Spatial Blend** (Miscelazione spaziale) sia completamente impostato su 3D.  

![Sorgente audio](media/AudioSource.png)

## <a name="apply-post-bake-design"></a>Applicare la progettazione post-bake
È possibile collegare lo script **AcousticsAdjust** a un'origine audio nella scena per abilitare altri parametri di progettazione dell'origine, facendo clic su **Add Component** (Aggiungi componente) e scegliendo **Scripts > Acoustics Adjust** (Script > Regolazione acustica):

![AcousticsAdjust](media/AcousticsAdjust.png)

Sono disponibili parametri anche nel **Mixer Microsoft Acoustics**. Per altre informazioni sulla progettazione post-bake, vedere i [parametri di progettazione](design-process.md).

## <a name="next-steps"></a>Passaggi successivi
* Provare la [scena di esempio](sample-walkthrough.md)
* Informazioni sul set completo di [funzionalità di bake](bake-ui-walkthrough.md)
* Esaminare in dettaglio i [parametri di progettazione](design-process.md)

