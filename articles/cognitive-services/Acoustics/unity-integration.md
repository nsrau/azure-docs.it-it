---
title: Integrazione e distribuzione di Project Acoustics Unity
titlesuffix: Azure Cognitive Services
description: In questo articolo viene descritto come integrare il plug-in Unità acustica progetto nel progetto Unity.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: a8ddb0e4ca2ee4396a25a70c8b60b653aebb72d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243007"
---
# <a name="project-acoustics-unity-integration"></a>Integrazione di Project Acoustics Unity
In questo articolo viene descritto come integrare il plug-in Unità acustica progetto nel progetto Unity.

Requisiti software:
* [Unity 2018.2+](https://unity3d.com) per Windows
* [Pacchetto Project Acoustics Unity](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plug-in"></a>Importare il plug-in
1. Importare l'acustica UnityPackage nel progetto. 
 In Unity, passare a Pacchetto**personalizzato****di importazione** >  **risorse** > .

    ![Menu Unity Import Package](media/import-package.png)  

1. Scegliere **ProjectAcoustics.unitypackage**.

1. Selezionare il pulsante **Import** (Importa) per integrare il pacchetto Unity nel progetto.

    ![Finestra di dialogo Import Package in Unity](media/import-dialog.png)  

Se si sta importando il plug-in in un progetto esistente, è possibile che il progetto disponga già di un file *mcs.rsp* nella radice del progetto. Questo file consente di specificare le opzioni per il compilatore C. Unire il contenuto di tale file con il file mcs.rsp fornito con il plug-in Proione progetto.

## <a name="enable-the-plug-in"></a>Abilitare il plug-in
La parte bake del toolkit acustico richiede .NET 4. x versione runtime *di* scripting. L'importazione dei pacchetti aggiorna le impostazioni del lettore Unity. Riavviare Unity per l'applicazione di queste impostazioni.

![Il pannello Impostazioni di Unity Player](media/player-settings.png)

![Pannello Impostazioni di Unity Player con .NET 4.5 selezionato](media/net45.png)

## <a name="set-up-audio-dsp"></a>Configurare DSP audio
Project Acoustics include DSP di runtime audio che si integra nel framework dello spaziozzante del motore audio Unity. Include sia la spazializzazione basata su HRTF che la spazializzazione panning. Per attivare il DSP per l'acustica del progetto, accedere a **Modifica** > **impostazioni** > progetto**audio** per aprire le impostazioni audio di Unity. Selezionare Acustica progetto come **plug-in** **dello spatializer** per il progetto. Assicurarsi che **Dimensione buffer DSP** sia impostato su *Prestazioni ottimali*.

![Menu Settings del progetto Unity](media/project-settings.png)  

![Pannello delle impostazioni dello spazioizzatore Unity con lo spazializzatore proiette progetti selezionato](media/choose-spatializer.png)

Successivamente, aprire il Mixer audio (**Miscelatore audio****finestra** > ). Assicurarsi di avere almeno un mixer, con un gruppo. Se non si dispone di **+** uno, selezionare il pulsante a destra di **Mixers**. Fare clic sulla parte inferiore della barra del canale nella sezione effetti e aggiungere l'effetto **Microsoft Acoustics Mixer** (Mixer Microsoft Acoustics). È supportato un solo mixer di acustica di progetto alla volta.

![Il mixer Unity Audio che ospita il mixer prodittiera del progetto](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Abilitare l'acustica sulle sorgenti sonore
Creare una sorgente audio: seleziona la casella di controllo **Spazializza** nella parte inferiore del pannello di impostazioni AudioSource. Assicuratevi che **Fusione spaziale** sia impostata su *3D*completo.  

![Pannello Sorgente audio Unity](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Abilitare la progettazione acustica
Associare lo script *AcousticsAdjust* a una sorgente audio nella scena per abilitare parametri di progettazione sorgente aggiuntivi: selezionate **Aggiungi componente** e scegliete**Regolazione acustica** **script** > .

![Script AcousticsAdjust in Unity](media/acoustics-adjust.png)

## <a name="next-steps"></a>Passaggi successivi
* [Cuocere la scena con Project Acoustics for Unity](unity-baking.md).
* [Creare un account Batch](create-azure-account.md) di Azure per cuocere la scena nel cloud.
* Esplora il processo di [progettazione di Project Acoustics Unity.](unity-workflow.md)
