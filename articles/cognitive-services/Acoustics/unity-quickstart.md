---
title: Avvio rapido per Progetto Acustica con Unity
titlesuffix: Azure Cognitive Services
description: Usare il contenuto di esempio per sperimentare i controlli di progettazione di Progetto Acustica in Unity ed eseguire la distribuzione in Windows Desktop.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fabdd221ef99414eae0156babbd76dedb1f0745d
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243027"
---
# <a name="project-acoustics-unity-quickstart"></a>Avvio rapido per Progetto Acustica in Unity
Usare il contenuto di esempio di Progetto Acustica per Unity per sperimentare i controlli di progettazione supportati dalla simulazione.

Requisiti software:
* [Unity 2018.2+](https://unity3d.com) per Windows
* [Pacchetto di contenuto di esempio per Progetto Acustica](https://www.microsoft.com/download/details.aspx?id=57346)

Contenuto del pacchetto di esempio:
* Scena Unity con geometria, sorgenti sonore e controlli di gioco
* Plug-in Progetto Acustica
* Asset di acustica di cui è stato effettuato il bake per la scena

## <a name="import-the-sample-package"></a>Importare il pacchetto di esempio
Importare il pacchetto di esempio in un nuovo progetto Unity.
1. In Unity passare a **Assets** > **Import Package** > **Custom Package** (Asset -> Importa pacchetto -> Pacchetto personalizzato).

    ![Opzioni di Import Package in Unity](media/import-package.png)  

1. Scegliere **ProjectAcoustics.unitypackage**.

1. Selezionare il pulsante **Import** (Importa) per integrare il pacchetto Unity nel progetto.  
  
    ![Finestra di dialogo Import Package in Unity](media/import-dialog.png)  

Per importare il pacchetto in un progetto esistente, vedere [Integrazione in Unity](unity-integration.md) per altri passaggi e note.

>[!NOTE]
>Al termine dell'importazione, nel log della console verranno visualizzati diversi messaggi di errore. Procedere al passaggio successivo e riavviare Unity.

## <a name="restart-unity"></a>Riavviare Unity
La parte relativa al bake del toolkit di acustica richiede la versione del runtime di scripting .NET 4.*x*. L'importazione del pacchetto aggiorna le impostazioni del lettore Unity. Riavviare Unity per l'applicazione di queste impostazioni. Per verificare che l'impostazione sia stata applicata, aprire le impostazioni di **Player** (Lettore):

![Menu Settings del progetto Unity](media/player-settings.png)  

![Pannello di impostazioni del lettore Unity con l'opzione .NET 4.x selezionata](media/net45.png)  

>[!NOTE]
>Questo screenshot è stato acquisito da Unity 2018.*x*. L'immagine può essere diversa nelle versioni più recenti di Unity.

## <a name="open-the-project-acoustics-bake-window"></a>Aprire la finestra di bake di Progetto Acustica nell'editor
In Unity scegliere **Acoustics** (Acustica) dal menu **Window** (Finestra).

![Editor Unity con l'opzione Acoustics evidenziata nel menu Window](media/window-acoustics.png)

Si aprirà una finestra mobile **Acoustics** (Acustica), in cui vengono impostate le proprietà della simulazione acustica.

![Editor Unity con la finestra Acoustics aperta](media/unity-editor-plugin-window.png)  

## <a name="experiment-with-the-design-controls"></a>Sperimentare i controlli di progettazione
Aprire la scena di esempio nella cartella *ProjectAcousticsSample* e selezionare il pulsante di riproduzione nell'editor Unity. Usare i tasti W, A, S e D e il mouse per spostarsi. Per confrontare la scena con e senza acustica, premere il tasto R finché il testo in sovrimpressione non diventa rosso e indica "Acustica: disabilitata". Per visualizzare i tasti di scelta rapida per altri controlli, premere F1. È anche possibile fare clic con il pulsante destro del mouse per selezionare un'azione e quindi fare clic con il pulsante sinistro per eseguire tale azione.

Lo script *AcousticsAdjust* è collegato alle sorgenti audio nella scena di esempio. Abilita i parametri di progettazione per sorgente.

![Script AcousticsAdjust in Unity](media/acoustics-adjust.png)

Le sezioni seguenti illustrano alcuni degli effetti che è possibile creare usando i controlli disponibili. Per informazioni dettagliate su ogni controllo, vedere l'[esercitazione sulla progettazione in Unity per Progetto Acustica](unity-workflow.md).

### <a name="modify-distance-based-attenuation"></a>Modificare l'attenuazione basata sulla distanza
L'elaborazione del segnale digitale audio nel plug-in di spazializzazione Unity di **Progetto Acustica** rispetta l'attenuazione basata sulla distanza per ogni sorgente, predefinita nell'editor Unity. I controlli per l'attenuazione basata sulla distanza sono nel componente **Audio Source** (Sorgente audio), disponibile nel pannello **Inspector** (Controllo) delle sorgenti audio in **3D Sound Settings** (Impostazioni audio 3D):

![Riquadro di opzioni per l'attenuazione basata sulla distanza in Unity](media/distance-attenuation.png)

Progetto Acustica esegue il calcolo in una "area di simulazione", un riquadro centrato attorno alla posizione del lettore. Il bake degli asset di acustica del pacchetto di esempio è stato effettuato in un'area di simulazione di 45 metri attorno al lettore. L'attenuazione dell'audio dovrebbe quindi essere progettata in modo da ridursi a 0 a circa 45 metri.

### <a name="modify-occlusion-and-transmission"></a>Modificare occlusione e trasmissione
* Se il moltiplicatore **Occlusion** (Occlusione) è maggiore di 1, che è il valore predefinito, l'occlusione è estrema. Per rendere più leggero l'effetto di occlusione, usare un'impostazione inferiore a 1.

* Per consentire la trasmissione attraverso le pareti, allontanare il dispositivo di scorrimento **Transmission (dB)** (Trasmissione - dB) dall'impostazione più bassa.

### <a name="modify-wetness-for-a-source"></a>Modificare l'intensità del riverbero per una sorgente
* Per modificare la rapidità con cui l'intensità del riverbero cambia con la distanza, usare **Perceptual Distance Warp** (Distorsione della distanza percettiva). Tramite la simulazione, Progetto Acustica calcola i livelli di riverbero, che offrono indicazioni della distanza percettiva e variano in modo uniforme con la distanza. L'aumento della distorsione della distanza accentua questo effetto aumentando i livelli di riverbero correlati alla distanza. Valori di distorsione inferiori a 1 rendono il cambiamento del riverbero in base alla distanza più leggero.

   Per apportare modifiche più precise a questo effetto, cambiare l'impostazione **Wetness (dB)**  (Intensità riverbero (dB)).

* Per aumentare il tempo di decadimento nello spazio, modificare il valore di **Decay Time Scale** (Scala tempo di decadimento). Se il risultato della simulazione per una determinata coppia sorgente-posizione ascoltatore è un tempo di decadimento di 1,5 secondi e l'opzione **Decay Time Scale** (Scala tempo di decadimento) è impostata su 2, il tempo di decadimento applicato alla sorgente sarà 3 secondi.

## <a name="next-steps"></a>Passaggi successivi
* Vedere le informazioni dettagliate sui [controlli di progettazione di Progetto Acustica basati su Unity](unity-workflow.md).
* Approfondire i concetti alla base del [processo di progettazione](design-process.md).
* [Creare un account Azure](create-azure-account.md) per esplorare i processi pre-bake e di bake.
