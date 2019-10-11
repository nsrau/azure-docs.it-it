---
title: Integrazione e distribuzione in Project Acoustics Unity
titlesuffix: Azure Cognitive Services
description: Questo articolo descrive come integrare il plug-in del progetto Acoustics Unity nel progetto Unity.
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
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243007"
---
# <a name="project-acoustics-unity-integration"></a>Integrazione di unità acustiche del progetto
Questo articolo descrive come integrare il plug-in del progetto Acoustics Unity nel progetto Unity.

Requisiti software:
* [Unity 2018.2+](https://unity3d.com) per Windows
* [Pacchetto di unità acustiche del progetto](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plug-in"></a>Importare il plug-in
1. Importare i file unitypackage Tools acustici nel progetto. 
 In Unity passare a **asset** > **Import Package** > **Package Custom**.

    ![Menu del pacchetto di importazione Unity](media/import-package.png)  

1. Scegliere **ProjectAcoustics. file unitypackage Tools**.

1. Selezionare il pulsante **Importa** per integrare il pacchetto Unity nel progetto.

    ![Finestra di dialogo Importa pacchetto di Unity](media/import-dialog.png)  

Se si sta importando il plug-in in un progetto esistente, il progetto potrebbe avere già un file *MCS. rsp* nella radice del progetto. Questo file specifica le opzioni per C# il compilatore. Unire il contenuto del file con il file MCS. rsp incluso nel plug-in Acoustics del progetto.

## <a name="enable-the-plug-in"></a>Abilitare il plug-in
La parte Bake del Toolkit Acoustics richiede .NET 4. *x* versione runtime di scripting. Importazione pacchetti Aggiorna le impostazioni del lettore Unity. Riavviare Unity per l'applicazione di queste impostazioni.

![Pannello Impostazioni lettore Unity](media/player-settings.png)

![Pannello Impostazioni lettore Unity con .NET 4,5 selezionato](media/net45.png)

## <a name="set-up-audio-dsp"></a>Configurare il DSP audio
Il progetto Acoustics include il DSP del runtime audio che si integra nel Framework Spatializer del motore audio di Unity. Include sia la spazializzazione basata su HRTF che quella di panoramica. Per abilitare il progetto Acoustics DSP, passare a **Edit** > **Project Settings** > **audio** per aprire le impostazioni audio di Unity. Selezionare **progetto Acoustics** come plug-in **Spatializer** per il progetto. Verificare che le **dimensioni del buffer DSP** siano impostate in modo da *ottenere prestazioni ottimali*.

![Menu Impostazioni progetto Unity](media/project-settings.png)  

![Pannello impostazioni Spatializer di Unity con Spatializer del progetto Acoustics selezionati](media/choose-spatializer.png)

Aprire quindi il mixer audio (**finestra** > **audio mixer**). Assicurarsi di disporre di almeno un mixer con un gruppo. Se non si dispone di un, selezionare il pulsante **+** a destra di **mixer**. Fare clic sulla parte inferiore della barra del canale nella sezione effetti e aggiungere l'effetto **Microsoft Acoustics Mixer** (Mixer Microsoft Acoustics). È supportato un solo Mixer acustico di un progetto alla volta.

![Mixer audio Unity che ospita il progetto Acoustics mixer](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Abilitare l'acustica nelle origini audio
Creare un'origine audio: Selezionare la casella di controllo **Spatialize** nella parte inferiore del pannello audiosource Inspector. Assicurarsi che **Blend spaziale** sia impostato su Full *3D*.  

![Pannello di origine audio Unity](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Abilitare la progettazione acustica
Alleghi lo script *AcousticsAdjust* a un'origine audio nella scena per abilitare parametri di progettazione dell'origine aggiuntivi: Selezionare **Add Component** e scegliere **script** > **Acoustics Adjust**.

![Script Unity AcousticsAdjust](media/acoustics-adjust.png)

## <a name="next-steps"></a>Passaggi successivi
* [Preparare la scena con l'acustica del progetto per Unity](unity-baking.md).
* [Creare un account Azure batch](create-azure-account.md) per preparare la scena nel cloud.
* Esplorare il [processo di progettazione di Project Acoustics Unity](unity-workflow.md).
