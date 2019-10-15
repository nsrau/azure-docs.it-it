---
title: Avvio rapido per Progetto Acustica con Unreal
titlesuffix: Azure Cognitive Services
description: Usare il contenuto di esempio per sperimentare i controlli di progettazione di Progetto Acustica in Unreal e Wwise ed eseguire la distribuzione in Windows Desktop.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d3afcded894f72626a4f24bcbe85c34ac1329c29
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242916"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Avvio rapido per Progetto Acustica con Unreal/Wwise
In questo argomento di avvio rapido si sperimenteranno i controlli di progettazione di Progetto Acustica usando contenuto di esempio per Unreal Engine e Wwise.

Requisiti software per l'uso del contenuto di esempio:
* [Unreal Engine](https://www.unrealengine.com/) 4.22
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2019.1.2

## <a name="download-the-sample-package"></a>Scaricare il pacchetto di esempio
Scaricare il [pacchetto di esempio di Progetto Acustica per Unreal e Wwise](https://www.microsoft.com/download/details.aspx?id=58090). Il pacchetto di esempio contiene:
- Progetto Unreal Engine
- Progetto Wwise per il progetto Unreal
- Plug-in Wwise per Progetto Acustica

## <a name="set-up-the-project-acoustics-sample-project"></a>Configurare il progetto di esempio per Progetto Acustica
Installare prima di tutto il plug-in di Progetto Acustica in Wwise. Distribuire quindi i file binari di Wwise nel progetto Unreal. Infine, modificare il plug-in di Wwise Unreal in modo che supporti Progetto Acustica.

### <a name="install-the-project-acoustics-wwise-plug-in"></a>Installare il plug-in Wwise per Progetto Acustica
Aprire l'utilità di avvio di Wwise. Nella scheda **Plugins** (Plug-in) selezionare **Add from directory** (Aggiungi da directory) in **Install New Plugins** (Installa nuovi plug-in). Scegliere la directory *AcousticsWwisePlugin\ProjectAcoustics* che è stata inclusa nel pacchetto scaricato.

![Opzione per installare il plug-in Wwise nell'utilità di avvio di Wwise](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Aggiungere i file binari di Wwise al progetto di esempio Unreal per Progetto Acustica
1. Nell'utilità di avvio di Wwise selezionare la scheda **Unreal Engine**. 
1. Selezionare il menu con l'icona hamburger accanto a **Recent Unreal Engine Projects** (Progetti Unreal Engine recenti) e quindi selezionare **Browse for project** (Cerca progetto). Aprire il file *.uproject* del progetto Unreal di esempio nel pacchetto *AcousticsSample\AcousticsGame\AcousticsGame.uproject*.

   ![Scheda Unreal nell'utilità di avvio di Wwise](media/wwise-unreal-tab.png)

3. Accanto al progetto di esempio di Progetto Acustica selezionare **Integrate Wwise in Project** (Integra Wwise nel progetto).

   ![L'utilità di avvio Wwise mostra il progetto Acoustics Game Unreal con l'opzione Integrate Wwise in Project evidenziata.](media/wwise-acoustics-game-project.png)

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Estendere la funzionalità del plug-in Unreal di Wwise
Il plug-in Unreal per Progetto Acustica richiede l'esposizione di comportamenti aggiuntivi dall'API del plug-in Unreal di Wwise. Per automatizzare queste modifiche, eseguire il file batch fornito con il plug-in Unreal per Progetto Acustica.
* All'interno di *AcousticsGame\Plugins\ProjectAcoustics\Resources* eseguire *PatchWwise.bat*.

    ![Script per applicare la patch al progetto Wwise evidenziato in una finestra di Esplora risorse](media/patch-wwise-script.png)

* Se DirectX SDK non è installato, a seconda della versione di Wwise in uso, potrebbe essere necessario impostare come commento la riga che contiene `DXSDK_DIR` in *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*:

    ![Editor di codice che mostra DXSDK impostato come commento](media/directx-sdk-comment.png)

* Se si esegue la compilazione con Visual Studio 2019, per ovviare a un errore di collegamento con Wwise, cambiare manualmente il valore predefinito *VSVersion* in *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* in **vc150**:

    ![Editor di codice con VSVersion cambiato in "vc150"](media/vsversion-comment.png)

### <a name="open-the-unreal-project"></a>Aprire il progetto Unreal 
Quando si apre il progetto Unreal, viene chiesto di ricompilare i moduli. Selezionare **Sì**.

Se non è possibile aprire il progetto a causa di errori di compilazione, verificare che la versione installata del plug-in Wwise per Progetto Acustica sia uguale a quella di Wwise usata nel progetto di esempio di Progetto Acustica.

Se si usa una versione di [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) precedente alla 2019.1, non è possibile generare librerie audio usando il progetto di esempio di Progetto Acustica. È necessario integrare Wwise versione 2019.1 nel progetto di esempio.

## <a name="experiment-with-project-acoustics-design-controls"></a>Sperimentare i controlli di progettazione di Progetto Acustica
Ascoltare l'audio della scena selezionando il pulsante di riproduzione nell'editor Unreal. Usare i tasti W, A, S e D e il mouse per spostarsi. Per visualizzare i tasti di scelta rapida per altri controlli, premere F1.

Le informazioni seguenti descrivono alcune attività di progettazione da provare.

### <a name="modify-occlusion-and-transmission"></a>Modificare occlusione e trasmissione
Sono disponibili controlli di progettazione di Progetto Acustica specifici per sorgente in ogni attore del sistema audio Unreal.

![Controlli di progettazione dell'acustica nell'editor Unreal](media/demo-scene-sound-source-design-controls.png)

Se il moltiplicatore **Occlusion** (Occlusione) è maggiore di 1, che è il valore predefinito, l'occlusione è estrema. Con un'impostazione inferiore a 1, l'effetto di occlusione è più leggero.

Per consentire la trasmissione attraverso le pareti, allontanare il dispositivo di scorrimento **Transmission (dB)** (Trasmissione - dB) dal livello più basso.

### <a name="modify-wetness-for-a-source"></a>Modificare l'intensità del riverbero per una sorgente
Per modificare la rapidità con cui l'intensità del riverbero cambia con la distanza, usare **Perceptual Distance Warp** (Distorsione della distanza percettiva). Progetto Acustica calcola i livelli di riverbero nell'intero spazio tramite la simulazione. I livelli variano uniformemente con la distanza e forniscono indicazioni sulla distanza percettiva. Per accentuare questo effetto, aumentare i livelli di riverbero correlati alla distanza per aumentare la distorsione della distanza. Valori di distorsione inferiori a 1 rendono il cambiamento del riverbero in base alla distanza più leggero. È anche possibile apportare modifiche più precise a questo effetto tramite l'impostazione **Wetness (dB)**  (Intensità riverbero (dB)).

Per aumentare il tempo di decadimento nello spazio, modificare **Decay Time Scale** (Scala tempo di decadimento). Si consideri un caso in cui il risultato della simulazione corrisponde a un tempo di decadimento di 1,5 secondi. Se si imposta **Decay Time Scale** (Scala tempo di decadimento) su 2, alla sorgente viene applicato un tempo di decadimento di 3 secondi.

### <a name="modify-distance-based-attenuation"></a>Modificare l'attenuazione basata sulla distanza
Il plug-in del mixer Wwise per Progetto Acustica rispetta l'attenuazione basata sulla distanza per sorgente, predefinita in Wwise. Modificando questa curva cambia il livello del segnale dry. Il plug-in Progetto Acustica regolerà il livello di riverbero in modo da mantenere la combinazione wet/dry specificata dai controlli di simulazione e progettazione.

![Pannello della curva di attenuazione Wwise che mostra l'attenuazione che si avvicina allo zero prima del limite di simulazione](media/demo-sounds-attenuation.png)

Progetto Acustica esegue il calcolo in una "area di simulazione", un riquadro centrato attorno a ogni posizione simulata del lettore. Il bake degli asset di acustica del pacchetto di esempio è stato effettuato in un raggio di 45 metri nell'area di simulazione. Le attenuazioni sono state progettate in modo da ridursi a 0 prima dei 45 metri. Anche se questa riduzione non costituisce un requisito vincolante, implica che l'occlusione dei suoni verrà applicata solo alla geometria compresa entro 45 metri dall'ascoltatore.

## <a name="next-steps"></a>Passaggi successivi
* [Integrare il plug-in Progetto Acustica](unreal-integration.md) nel progetto Unreal.
* [Creare un account di Azure](create-azure-account.md) per effettuare i bake.
