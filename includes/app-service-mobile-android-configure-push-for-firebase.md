---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 51a75ee7bf87c38e3916bdbc8d85abcfb14dca8b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140220"
---
1. Nel [Portale di Azure](https://portal.azure.com/) fare clic su **Browse All** (Esplora tutto)  > **Servizi app** e quindi scegliere il back-end dell'app per dispositivi mobili. In **Impostazioni** fare clic su **App Service Push** (Push servizio app), quindi fare clic sul nome dell'hub di notifica.
2. Passare a **Google (GCM)**, immettere il valore **Chiave server** ottenuto da Firebase nella procedura precedente, quindi fare clic su **Salva**.

    ![Impostare la chiave API nel portale](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

Il back-end dell'app per dispositivi mobili è ora configurato per l'uso di Firebase Cloud Messaging. Questo consente di inviare notifiche push all'app in esecuzione su un dispositivo Android usando l'hub di notifica.
