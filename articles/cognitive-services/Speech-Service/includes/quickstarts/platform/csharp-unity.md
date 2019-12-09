---
title: 'Guida introduttiva: Configurazione della piattaforma per C# Unity con Speech SDK - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Usare questa guida per configurare la piattaforma per C# Unity con il servizio Speech SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/10/2019
ms.author: erhopf
ms.openlocfilehash: 064916c0dad52749f724be8a1912f247128114b6
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816241"
---
Questa guida spiega come installare [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per [Unity](https://unity3d.com/).

> [!NOTE]
> Speech SDK per Unity supporta Windows Desktop (x86 e x64) o la piattaforma UWP (Universal Windows Platform) (x86, x64, ARM/ARM64), Android (x86, ARM32/64) e iOS (simulatore x64, ARM32 e ARM64).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

- [Unity 2018.3 o versioni successive](https://store.unity.com/) con [Unity 2019.1 che aggiunge il supporto per la piattaforma UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). È accettabile anche Visual Studio 2017 versione 15.9 o successive.
- Per il supporto di Windows ARM64, installare gli [strumenti di compilazione facoltativi per ARM64 e Windows 10 SDK per ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="install-the-speech-sdk"></a>Installare Speech SDK

Per installare Speech SDK per Unity, seguire questa procedura:

1. Scaricare e aprire [Speech SDK per Unity](https://aka.ms/csspeech/unitypackage), fornito come pacchetto di asset Unity (con estensione unitypackage). L'associazione con Unity dovrebbe essere già eseguita. All'apertura del pacchetto di asset, viene visualizzata la finestra di dialogo **Import Unity Package** (Importa pacchetto Unity). Potrebbe essere necessario creare e aprire un progetto vuoto affinché questo passaggio funzioni.

   [![Finestra di dialogo Import Unity Package nell'editor di Unity](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png#lightbox)

1. Verificare che tutti i file siano selezionati e quindi selezionare **Import** (Importa). Dopo alcuni istanti, il pacchetto di asset Unity viene importato nel progetto.

Per altre informazioni sull'importazione di pacchetti di asset in Unity, vedere la [documentazione di Unity](https://docs.unity3d.com/Manual/AssetPackages.html).

Ora è possibile procedere ai [Passaggi successivi](#next-steps) qui sotto.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [windows](../quickstart-list.md)]
