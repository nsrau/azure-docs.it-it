---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 79459be30a5a2018dc82486a84895b1a954941bc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140490"
---
1. Nel [portale di Azure](https://portal.azure.com/) selezionare **Esplora tutto** > **Servizi app**. Selezionare quindi il back-end di App per dispositivi mobili. In **Impostazioni** selezionare **App Service Push** (Push servizio app). Selezionare quindi il nome dell'hub di notifica.
2. Passare a **Windows (WNS)**. Immettere quindi la **chiave di sicurezza** (segreto client) e il **SID pacchetto** ottenuti dal sito dei servizi Live. Selezionare quindi **Salva**.

    ![Impostare la chiave WNS nel portale](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

Il back-end ora è configurato per poter inviare le notifiche push tramite WSN.
