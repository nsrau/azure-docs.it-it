---
title: Avvio rapido per Progetto Acustica con Unity
titlesuffix: Azure Cognitive Services
description: Usando il contenuto di esempio, sperimentare i controlli di progettazione di Progetto Acustica in Unity ed eseguire la distribuzione in Windows Desktop.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/14/2019
ms.author: kegodin
ms.openlocfilehash: b4eedabbc47738eb2f5797ffd67a3e3ebc9529ca
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136207"
---
# <a name="project-acoustics-unity-quickstart"></a>Avvio rapido per Progetto Acustica in Unity
Usare il contenuto di esempio di Progetto Acustica per Unity per sperimentare i controlli di progettazione supportati dalla simulazione.

Requisiti software:
* [Unity 2018.2+](http://unity3d.com) per Windows
* [Pacchetto di contenuto di esempio per Progetto Acustica](https://www.microsoft.com/en-us/download/details.aspx?id=57346)

Contenuto del pacchetto di esempio:
* Scena Unity con geometria, sorgenti sonore e controlli di gioco
* Plug-in Progetto Acustica 
* Asset di acustica di cui è stato effettuato il bake per la scena

## <a name="import-the-sample-package"></a>Importare il pacchetto di esempio
Importare il pacchetto di esempio in un nuovo progetto Unity. 
* In Unity passare a **Assets -> Import Package -> Custom Package** (Asset -> Importa pacchetto -> Pacchetto personalizzato).

    ![Importare il pacchetto](media/import-package.png)  

* Scegliere **ProjectAcoustics.unitypackage**.

Se si importa il pacchetto in un progetto esistente, per altri passaggi e note vedere l'articolo relativo all'[integrazione in Unity](unity-integration.md).

## <a name="restart-unity"></a>Riavviare Unity
La parte per il baking del toolkit di acustica richiede la versione del runtime di scripting .NET 4.x. L'importazione del pacchetto aggiornerà le impostazioni del lettore Unity. Riavviare Unity per l'applicazione di queste impostazioni.

È possibile verificare che l'impostazione sia stata applicata aprendo le **impostazioni del lettore**:

![Impostazioni del lettore](media/player-settings.png)

![.NET 4.5](media/net45.png)

## <a name="experiment-with-design-controls"></a>Sperimentare i controlli di progettazione
Aprire la scena di esempio nella cartella **ProjectAcousticsSample** e fare clic sul pulsante di riproduzione nell'editor Unity. Usare W, A, S, D e il mouse per spostarsi. Per confrontare la scena con e senza acustica, premere il pulsante **R** fino a quando il testo di sovrimpressione diventa rosso e indica "Acustica: disabilitata". Per visualizzare i tasti di scelta rapida per altri controlli, premere **F1**. È possibile usare i controlli anche facendo clic con il pulsante destro del mouse per selezionare l'azione da eseguire e quindi con il pulsante sinistro del mouse per eseguirla.

Lo script **AcousticsAdjust** è associato alle sorgenti sonore nella scena di esempio e abilita i parametri di progettazione in base alla sorgente. 

![AcousticsAdjust](media/acoustics-adjust.png)

Di seguito vengono illustrati alcuni degli effetti che possono essere generati con i controlli disponibili. Per informazioni dettagliate su ogni controllo, vedere l'[esercitazione sulla progettazione in Unity per Progetto Acustica](unreal-workflow.md).

### <a name="modify-distance-based-attenuation"></a>Modificare l'attenuazione basata sulla distanza
Il DSP audio fornito dal plug-in di spazializzazione di Unity **Progetto Acustica** rispetta l'attenuazione basata sulla distanza per ogni sorgente predefinita nell'editor Unity. I controlli per l'attenuazione basata sulla distanza sono nel componente **Audio Source** (Sorgente audio) del pannello **Inspector** (Controllo) delle sorgenti sonore in **3D Sound Settings** (Impostazioni audio 3D):

![Attenuazione della distanza](media/distance-attenuation.png)

Progetto Acustica esegue il calcolo in una "area di simulazione", un riquadro con al centro la posizione del giocatore. Dato che il bake degli asset di acustica del pacchetto di esempio è stato effettuato con una dimensione dell'area di simulazione di 45 metri intorno al giocatore, l'attenuazione dell'audio deve essere progettata in modo da ridursi a 0 a circa 45 metri.

### <a name="modify-occlusion-and-transmission"></a>Modificare occlusione e trasmissione
* Se il moltiplicatore **Occlusion** (Occlusione) è maggiore di 1, che è il valore predefinito, l'occlusione sarà estrema. Con un'impostazione inferiore a 1, l'effetto di occlusione è più leggero.

* Per abilitare la trasmissione attraverso le pareti, allontanare il dispositivo di scorrimento **Transmission (dB)** (Trasmissione - dB) dal livello più basso. 

### <a name="modify-wetness-for-a-source"></a>Modificare l'intensità del riverbero per una sorgente
* Per modificare la rapidità con cui l'intensità del riverbero cambia con la distanza, usare **Perceptual Distance Warp** (Distorsione della distanza percettiva). I livelli di riverbero vengono calcolati da **Progetto Acustica** per tutto lo spazio dalla simulazione e variano in modo uniforme con la distanza offrendo indicazioni della distanza percettiva. L'aumento della distorsione della distanza accentua questo effetto aumentando i livelli di riverbero correlati alla distanza. Valori di distorsione inferiori a 1 rendono il cambiamento del riverbero in base alla distanza più leggero. È anche possibile regolare questo effetto in modo più specifico modificando **Wetness (dB)** (Intensità riverbero - dB).

* Aumentare il tempo di decadimento nello spazio modificando **Decay Time Scale** (Scala tempo di decadimento). Se il risultato della simulazione per una determinata coppia sorgente-posizione ascoltatore è un tempo di decadimento di 1,5 secondi e l'opzione **Decay Time Scale** (Scala tempo di decadimento) è impostata su 2, il tempo di decadimento applicato alla sorgente sarà 3 secondi.

## <a name="next-steps"></a>Passaggi successivi
* Vedere tutti i dettagli sui [controlli di progettazione di Progetto Acustica basati su Unity](unity-workflow.md)
* Approfondire i concetti alla base del [processo di progettazione](design-process.md)
* [Creare un account Azure](create-azure-account.md) per esplorare i processi pre-bake e di bake

