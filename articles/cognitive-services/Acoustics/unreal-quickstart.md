---
title: Avvio rapido per Progetto Acustica con Unreal
titlesuffix: Azure Cognitive Services
description: Usando il contenuto di esempio, sperimentare i controlli di progettazione di Progetto Acustica in Unreal e Wwise ed eseguire la distribuzione in Windows Desktop.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/14/2019
ms.author: kegodin
ms.openlocfilehash: 1314e393d292145ef112e700abf6ab1ef199db7d
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58138167"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Avvio rapido per Progetto Acustica con Unreal/Wwise
Questo argomento di avvio rapido consente di sperimentare i controlli di progettazione di Progetto Acustica usando il contenuto di esempio fornito per Unreal Engine e Wwise.

Requisiti software:
* [Unreal Engine 4.21](https://www.unrealengine.com/)
* [Wwise 2018.1.6](https://www.audiokinetic.com/products/wwise/)

## <a name="download-the-sample-package"></a>Scaricare il pacchetto di esempio
Scaricare il [pacchetto di esempio Unreal + Wwise per Progetto Acustica](http://www.microsoft.com/downloads/details.aspx?FamilyID=f03dff5a-5780-462e-87ef-e6d039d0748d). Il pacchetto di esempio contiene un progetto Unreal Engine, il progetto Wwise per il progetto Unreal e il plug-in Wwise per Progetto Acustica.

## <a name="set-up-the-project-acoustics-sample-project"></a>Configurare il progetto di esempio per Progetto Acustica
Per configurare il progetto di esempio Unreal/Wwise per Progetto Acustica, è necessario installare prima il plug-in Progetto Acustica in Wwise. Distribuire quindi i file binari di Wwise nel progetto Unreal e adattare il plug-in Unreal di Wwise in modo che supporti Progetto Acustica.

### <a name="install-the-project-acoustics-wwise-plugin"></a>Installare il plug-in Wwise per Progetto Acustica
Aprire Wwise Launcher (Avvio Wwise) e quindi nella scheda **Plugins** (Plug-in) selezionare **Add From Directory** (Aggiungi da directory) in **Install New Plugins** (Installa nuovi plug-in). Scegliere la directory `AcousticsWwisePlugin\ProjectAcoustics` inclusa nel pacchetto scaricato.

![Installare il plug-in di Wwise](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Aggiungere i file binari di Wwise al progetto di esempio Unreal per Progetto Acustica
Da Wwise Launcher (Avvio Wwise) fare clic sulla scheda **Unreal Engine** e quindi fare clic sul menu hamburger accanto a **Recent Unreal Engine Projects** (Progetti recenti Unreal Engine) e selezionare **Browse for project** (Cerca progetto). Aprire il file di esempio `.uproject` del progetto Unreal nel pacchetto `AcousticsSample\AcousticsGame\AcousticsGame.uproject`.

![Scheda Unreal in Wwise](media/wwise-unreal-tab.png)

Accanto al progetto di esempio di Progetto Acustica fare clic su **Integrate Wwise in Project** (Integra Wwise nel progetto).

![Progetto Unreal AcousticsGame in Wwise](media/wwise-acoustics-game-project.png)

### <a name="extend-wwises-unreal-plugin-functionality"></a>Estendere la funzionalità del plug-in Unreal di Wwise
Il plug-in Unreal per Progetto Acustica richiede l'esposizione di comportamenti aggiuntivi dall'API del plug-in Unreal di Wwise. Per automatizzare queste modifiche, eseguire il file batch fornito con il plug-in Unreal per Progetto Acustica:
* All'interno di `AcousticsGame\Plugins\ProjectAcoustics\Resources` eseguire `PatchWwise.bat`.

    ![Script PatchWwise](media/patch-wwise-script.png)

* Se DirectX SDK non è installato, è necessario impostare come commento la riga che contiene DXSDK_DIR in `AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![Impostazione della riga DXSDK come commento](media/directx-sdk-comment.png)

### <a name="open-the-unreal-project"></a>Aprire il progetto Unreal. 
Quando viene chiesto di ricompilare i moduli, fare clic su Yes (Sì).

Se non è possibile aprire il progetto in caso di errori di compilazione, verificare che la versione installata del plug-in Wwise per Progetto Acustica sia uguale a quella di Wwise usata nel progetto di esempio di Progetto Acustica.

## <a name="experiment-with-project-acoustics-design-controls"></a>Sperimentare i controlli di progettazione di Progetto Acustica
Ascoltare l'audio della scena facendo clic sul pulsante di riproduzione nell'editor Unreal. Sul desktop usare i tasti W, A, S, D e il mouse per spostarsi. Per visualizzare i tasti di scelta rapida per altri controlli, premere **F1**. Ecco alcune attività di progettazione da provare:

### <a name="modify-occlusion-and-transmission"></a>Modificare occlusione e trasmissione
Sono disponibili controlli di progettazione di Progetto Acustica specifici per sorgente in ogni actor del sistema audio Unreal:

![DemoSceneSoundSourceDesignControls](media/demo-scene-sound-source-design-controls.png)

Se il moltiplicatore **Occlusion** (Occlusione) è maggiore di 1 (il valore predefinito è 1), l'occlusione sarà estrema. Con un'impostazione inferiore a 1, l'effetto di occlusione è più leggero.

Per abilitare la trasmissione attraverso le pareti, allontanare il dispositivo di scorrimento **Transmission (dB)** (Trasmissione - dB) dal livello più basso. 

### <a name="modify-wetness-for-a-source"></a>Modificare l'intensità del riverbero per una sorgente
Per modificare la rapidità con cui l'intensità del riverbero cambia con la distanza, usare **Perceptual Distance Warp** (Distorsione della distanza percettiva). I livelli di riverbero vengono calcolati da Progetto Acustica per tutto lo spazio dalla simulazione e variano in modo uniforme con la distanza offrendo indicazioni della distanza percettiva. L'aumento della distorsione della distanza accentua questo effetto aumentando i livelli di riverbero correlati alla distanza. Valori di distorsione inferiori a 1 rendono il cambiamento del riverbero in base alla distanza più leggero. È anche possibile regolare questo effetto in modo più specifico modificando **Wetness (dB)** (Intensità riverbero - dB).

Aumentare il tempo di decadimento nello spazio modificando **Decay Time Scale** (Scala tempo di decadimento). Si consideri il caso in cui il risultato della simulazione corrisponde a un tempo di decadimento di 1,5 secondi. Se si imposta **Decay Time Scale** (Scala tempo di decadimento) su 2, alla sorgente verrà applicato un tempo di decadimento di 3 secondi.

### <a name="modify-distance-based-attenuation"></a>Modificare l'attenuazione basata sulla distanza
Il plug-in del mixer Wwise per Progetto Acustica rispetta l'attenuazione basata sulla distanza per sorgente predefinita in Wwise. Modificando questa curva cambierà il livello del segnale dry. Il plug-in Progetto Acustica regolerà il livello wet in modo da mantenere la combinazione wet-dry specificata dai controlli di simulazione e progettazione.

![DemoSoundsAttenuation](media/demo-sounds-attenuation.png)

Progetto Acustica esegue il calcolo in una "area di simulazione", un riquadro incentrato sulla posizione simulata del giocatore. Il bake degli asset di acustica del pacchetto di esempio è stato effettuato in un'area di simulazione il cui raggio è pari a 45 metri e le attenuazioni sono state progettate in modo da ridursi a 0 prima dei 45 metri. Anche se questa riduzione non costituisce un requisito vincolante, implica che l'occlusione dei suoni verrà applicata solo alla geometria compresa entro 45 metri dall'ascoltatore.

## <a name="next-steps"></a>Passaggi successivi
* [Integrare il plug-in Progetto Acustica](unreal-integration.md) nel progetto Unreal
* [Creare un account di Azure](create-azure-account.md) per le preparazioni personalizzate


