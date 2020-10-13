---
title: Come creare una sovrapposizione con Media Encoder Standard
description: Informazioni su come creare una sovrapposizione con Media Encoder Standard.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 6c93408bce8da9f8cd0e4a0d0bab615e2bd362dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89267327"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Come creare una sovrapposizione con Media Encoder Standard

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Il Media Encoder Standard consente di sovrapporre un'immagine a un video esistente. Attualmente, sono supportati i seguenti formati: png, jpg, gif e bmp.

## <a name="prerequisites"></a>Prerequisiti

* Raccogliere le informazioni sull'account necessarie per configurare il *appsettings.jssul* file nell'esempio. Se non si è certi di come eseguire questa operazione, vedere [Guida introduttiva: registrare un'applicazione con la piattaforma di identità Microsoft](../../active-directory/develop/quickstart-register-app.md). I valori seguenti sono previsti nel *appsettings.jssu* file.

    ```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Region": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
    ```

Se non si ha già familiarità con le trasformazioni, è consigliabile completare le attività seguenti:

* Leggi [la codifica di video e audio con servizi multimediali](encoding-concept.md)
* Leggere [le informazioni su come codificare con una trasformazione personalizzata-.NET](customize-encoder-presets-how-to.md). Attenersi alla procedura descritta in questo articolo per configurare .NET necessario per usare le trasformazioni, quindi tornare qui per provare un esempio di set di impostazioni sovrapposte.
* Vedere il [documento di riferimento per le trasformazioni](/rest/api/media/transforms).

Dopo aver acquisito familiarità con le trasformazioni, scaricare l'esempio overlays.

## <a name="overlays-preset-sample"></a>Esempio di set di impostazioni sovrapposte

Scaricare l' [esempio Media-Services-overlay](https://github.com/Azure-Samples/media-services-overlays) per iniziare a usare le sovrimpressioni.

## <a name="next-steps"></a>Passaggi successivi

* [Sottoclip di un video durante la codifica con servizi multimediali-.NET](subclip-video-dotnet-howto.md)