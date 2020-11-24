---
title: Installare gli ancoraggi spaziali di Azure per un'applicazione C++/WinRT HoloLens
description: Configurare un progetto C++/WinRT HoloLens per l'uso di ancoraggi spaziali di Azure
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 69134ef87d90fe69de2d9e4e9222e90f65edc785
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95507005"
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