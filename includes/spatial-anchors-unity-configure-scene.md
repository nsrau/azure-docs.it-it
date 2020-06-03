---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/2/2019
ms.author: crtreasu
ms.openlocfilehash: 7e7825e8247e78cbc0c0e9e22bdbd9326939e0a8
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83998087"
---
Il passaggio successivo consiste nel configurare l'app in modo da usare l'identificatore e la chiave dell'account. Questi dati sono stati copiati in un editor di testo durante la [configurazione della risorsa Ancoraggi nello spazio](#create-a-spatial-anchors-resource).

Nel riquadro **Progetto** passare a `Assets\AzureSpatialAnchors.SDK\Resources`. Selezionare `SpatialAnchorConfig`. Nel riquadro **Inspector** (Controllo) immettere `Account Key` come valore per `Spatial Anchors Account Key` e `Account ID` come valore per `Spatial Anchors Account Id`.

Aprire quindi `SpatialAnchorManager.cs`. Individuare `CreateSessionAsync()` e aggiungere la riga seguente, sostituendo il dominio account definito in precedenza: `session.Configuration.AccountDomain = "MyAccountDomain";`. È possibile aggiungere questa riga direttamente prima di questo commento `// Configure authentication`.
