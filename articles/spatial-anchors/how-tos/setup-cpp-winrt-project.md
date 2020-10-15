---
title: Installare gli ancoraggi spaziali di Azure per un'applicazione C++/WinRT HoloLens
description: Configurare un progetto C++/WinRT HoloLens per l'uso di ancoraggi spaziali di Azure
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 43d5c1ae03c359dcbef21f8e7ba3205bc6ab0004
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096114"
---
# <a name="configuring-azure-spatial-anchors-in-a-cwinrt-hololens-project"></a>Configurazione di ancoraggi spaziali di Azure in un progetto C++/WinRT HoloLens

## <a name="requirements"></a>Requisiti

* [Visual Studio 2019](https://www.visualstudio.com/downloads/) installato con il carico di lavoro di **sviluppo piattaforma UWP (Universal Windows Platform)** e il componente **Windows 10 SDK (10.0.18362.0 o versione successiva)** .

## <a name="configuring-a-project"></a>Configurazione di un progetto

Gli ancoraggi spaziali di Azure per HoloLens e C++/WinRT vengono distribuiti con il pacchetto NuGet [Microsoft. Azure. SpatialAnchors. WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) .

Seguire le istruzioni riportate [qui](/nuget/consume-packages/install-use-packages-visual-studio) per usare Gestione pacchetti NuGet di Visual Studio per installare il pacchetto NuGet [Microsoft. Azure. SpatialAnchors. WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) nel progetto.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedura: creare e individuare ancoraggi in C++/WinRT](./create-locate-anchors-cpp-winrt.md)