---
title: Integrazione con Unity acustica e distribuzione del progetto
titlesuffix: Azure Cognitive Services
description: Questa procedura descrive l'integrazione del plug-in di progetto acustica Unity nel progetto Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/14/2019
ms.author: kegodin
ms.openlocfilehash: 7d8edd7b092ee1ed4f953d753b2bbe864e075378
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58137731"
---
# <a name="project-acoustics-unity-integration"></a>Integrazione con Unity Project acustica
Questa procedura descrive l'integrazione del plug-in di progetto acustica Unity nel progetto Unity.

Requisiti software:
* [Unity 2018.2 +](http://unity3d.com) per Windows
* [Pacchetto del progetto Unity acustica](https://www.microsoft.com/en-us/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>Importare il plug-in
Importare il pacchetto di acustica UnityPackage nel progetto. 
* In Unity passare a **Assets -> Import Package -> Custom Package** (Asset -> Importa pacchetto -> Pacchetto personalizzato).

    ![Importare il pacchetto](media/import-package.png)  

* Scegliere **ProjectAcoustics.unitypackage**

Se si sta importando il plug-in un progetto esistente, nella radice del progetto può già essere presente un file **mcs.rsp**, che specifica le opzioni per il compilatore C#. È necessario unire il contenuto di tale file con il file mcs.rsp fornito con il plug-in Project Acoustics.

## <a name="enable-the-plugin"></a>Abilitare il plug-in
La parte per il baking del toolkit di acustica richiede la versione del runtime di scripting .NET 4.x. L'importazione del pacchetto aggiornerà le impostazioni del lettore Unity. Riavviare Unity per l'applicazione di queste impostazioni.

![Impostazioni del lettore](media/player-settings.png)

![.NET 4.5](media/net45.png)

## <a name="set-up-audio-dsp"></a>Configurare DSP audio
Progetto acustica include runtime audio DSP che integra il framework di spaziale audio motore Unity. Include spatialization HRTF sia su Panoramica. Abilitare DSP acustica progetto aprendo le impostazioni audio Unity usando **Modifica > Impostazioni progetto > Audio**, quindi selezionando **progetto acustica** come il **spaziale plug-in** per il progetto. Assicurarsi che il **le dimensioni del Buffer DSP** è impostato su prestazioni ottimali.

![Impostazioni progetto](media/project-settings.png)  

![Spazializzatore di Project Acoustics](media/choose-spatializer.png)

Aprire quindi il Mixer Audio (**finestra > Mixer Audio**). Assicurarsi di avere almeno un mixer, con un gruppo. In caso contrario, fare clic sul pulsante "+" a destra di **Mixers** (Mixer). Fare clic sulla parte inferiore dell'elenco di canale nella sezione effetti e aggiungere il **progetto acustica Mixer** effetto. Si noti che è supportato un solo mixer di Project Acoustics alla volta.

![Mixer audio](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Abilitare acustica su origini audio
Creare una sorgente audio. Fare clic sulla casella di controllo nella parte inferiore del pannello inspector della sorgente audio con la dicitura **Spatialize** (Spazializza). Assicurarsi che **Spatial Blend** (Miscelazione spaziale) sia completamente impostato su 3D.  

![Sorgente audio](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Abilita progettazione modello acustico
Collegare lo script **AcousticsAdjust** a un'origine audio nella scena per abilitare i parametri di progetto di origine aggiuntivi, fare clic su **Add Component** e scegliendo **script > acustica regolare** :

![AcousticsAdjust](media/acoustics-adjust.png)

## <a name="next-steps"></a>Passaggi successivi
* [Mostrandone la scena con acustica progetti per Unity](unity-baking.md)
* [Creare un account Azure Batch](create-azure-account.md) alla scena nel cloud, vengono inseriti
* Esplorare le [processo di progettazione progetto acustica Unity](unity-workflow.md).

