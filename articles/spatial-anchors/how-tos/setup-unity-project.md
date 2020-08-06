---
title: Installare gli ancoraggi spaziali di Azure per Unity
description: Configurare un progetto Unity per l'uso di ancoraggi spaziali di Azure
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/31/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: a23cff8bcef2d62b1e415997ffd6afc80cf47793
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87812313"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Configurazione di ancoraggi spaziali di Azure in un progetto Unity

## <a name="requirements"></a>Requisiti

Gli ancoraggi spaziali di Azure attualmente supportano Unity 2019,4 (LTS) con le configurazioni seguenti.

* Unity 2019,4 con AR Foundation 3,1 è supportato negli ancoraggi spaziali di Azure 2.4.0 +.

## <a name="configuring-a-project"></a>Configurazione di un progetto

Gli ancoraggi spaziali di Azure per Unity vengono attualmente distribuiti usando un pacchetto di asset Unity ( `.unitypackage` ), disponibile nelle [versioni di GitHub](https://github.com/Azure/azure-spatial-anchors-samples/releases).

### <a name="import-the-asset-package"></a>Importare il pacchetto di asset

1. Scaricare il `AzureSpatialAnchors.unitypackage` file per la versione che si vuole usare come destinazione dalle [versioni di GitHub](https://github.com/Azure/azure-spatial-anchors-samples/releases).
2. Seguire le istruzioni riportate [qui](https://docs.unity3d.com/Manual/AssetPackagesImport.html) per importare il pacchetto di asset Unity nel progetto.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedura: creare e individuare ancoraggi in Unity](./create-locate-anchors-unity.md)
