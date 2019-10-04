---
title: Integrazione e distribuzione in Project Acoustics Unity
titlesuffix: Azure Cognitive Services
description: Questa procedura spiega come integrare il plug-in Project Acoustics Unity nel progetto Unity.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 54bc98e0ddba0292c6a5dbb07f2bbdfce6a1cb45
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933150"
---
# <a name="project-acoustics-unity-integration"></a>Integrazione di unità acustiche del progetto
Questa procedura spiega come integrare il plug-in Project Acoustics Unity nel progetto Unity.

Requisiti software:
* [Unity 2018.2+](https://unity3d.com) per Windows
* [Pacchetto di unità acustiche del progetto](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>Importare il plug-in
Importare il pacchetto di acustica UnityPackage nel progetto. 
* In Unity passare a **Assets -> Import Package -> Custom Package** (Asset -> Importa pacchetto -> Pacchetto personalizzato).

    ![Screenshot del menu del pacchetto di importazione Unity](media/import-package.png)  

* Scegliere **ProjectAcoustics.unitypackage**.

* Fare clic sul pulsante **Importa** per integrare il pacchetto Unity nel progetto  

    ![Screenshot della finestra di dialogo Importa pacchetto di Unity](media/import-dialog.png)  

Se si sta importando il plug-in un progetto esistente, nella radice del progetto può già essere presente un file **mcs.rsp**, che specifica le opzioni per il compilatore C#. È necessario unire il contenuto di tale file con il file mcs.rsp fornito con il plug-in Project Acoustics.

## <a name="enable-the-plugin"></a>Abilitare il plug-in
La parte per il baking del toolkit di acustica richiede la versione del runtime di scripting .NET 4.x. L'importazione del pacchetto aggiornerà le impostazioni del lettore Unity. Riavviare Unity per l'applicazione di queste impostazioni.

![Screenshot del pannello di impostazioni del lettore in Unity](media/player-settings.png)

![Screenshot del pannello di impostazioni del lettore in Unity con l'opzione .NET 4.5 selezionata](media/net45.png)

## <a name="set-up-audio-dsp"></a>Configurare il DSP audio
Il progetto Acoustics include il DSP del runtime audio che si integra nel Framework Spatializer del motore audio di Unity. Include sia la spazializzazione basata su HRTF che quella di panoramica. Abilitare il progetto Acoustics DSP aprendo le impostazioni audio di Unity usando **modifica > impostazioni progetto > audio**, quindi selezionando **progetto Acoustics** come plug-in **Spatializer** per il progetto. Verificare che le **dimensioni del buffer DSP** siano impostate in modo da ottenere prestazioni ottimali.

![Screenshot del pannello Impostazioni progetto Unity](media/project-settings.png)  

![Screenshot del pannello delle impostazioni di Unity Spatializer con il progetto Acoustics Spatializer selezionato](media/choose-spatializer.png)

Aprire quindi il mixer audio (**finestra > mixer audio**). Assicurarsi di avere almeno un mixer, con un gruppo. In caso contrario, fare clic sul pulsante "+" a destra di **Mixers** (Mixer). Fare clic con il pulsante destro del mouse sulla parte inferiore della striscia del canale nella sezione effetti e aggiungere l'effetto **mixer acustici del progetto** . Si noti che è supportato un solo mixer di Project Acoustics alla volta.

![Screenshot del mixer audio di Unity che ospita il mixer acustici del progetto](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Abilitare l'acustica nelle origini audio
Creare una sorgente audio. Fare clic sulla casella di controllo nella parte inferiore del pannello inspector della sorgente audio con la dicitura **Spatialize** (Spazializza). Assicurarsi che **Spatial Blend** (Miscelazione spaziale) sia completamente impostato su 3D.  

![Screenshot del pannello di origine audio di Unity](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Abilitare la progettazione acustica
Allineare lo script **AcousticsAdjust** a un'origine audio nella scena per abilitare parametri di progettazione di origine aggiuntivi, facendo clic su **Aggiungi componente** e scegliendo **script > regolazione acustica**:

![Screenshot dello script AcousticsAdjust in Unity](media/acoustics-adjust.png)

## <a name="next-steps"></a>Passaggi successivi
* [Preparare la scena con i progetti acustici per Unity](unity-baking.md)
* [Creare un account Azure batch](create-azure-account.md) per preparare la scena nel cloud
* Esplorare il [processo di progettazione di Project Acoustics Unity](unity-workflow.md).

