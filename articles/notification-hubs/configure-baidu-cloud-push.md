---
title: Configurare il push cloud Baidu in hub di notifica di Azure | Microsoft Docs
description: Informazioni su come configurare le impostazioni di Baidu per un hub di notifica di Azure.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 759e35ba353f470ea3abc5f5d4182fa2b2ea0e73
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212510"
---
# <a name="deprecated-configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>Deprecato Configurare le impostazioni di push cloud Baidu per un hub di notifica nel portale di Azure

Questo articolo illustra come configurare le impostazioni push cloud Baidu per un hub di notifica di Azure usando il portale di Azure.

> [!IMPORTANT]
> Questa esercitazione è deprecata. 

## <a name="prerequisites"></a>Prerequisiti
Se non è stato già creato un hub di notifica, crearne uno ora. Per altre informazioni, vedere [Creare un hub di notifica di Azure nel portale di Azure](create-notification-hub-portal.md). 

## <a name="configure-baidu-cloud-push"></a>Configurare Baidu Cloud Push
La procedura seguente illustra i passaggi per configurare le impostazioni di push cloud Baidu per un hub di notifica:

1. Nella portale di Azure nella pagina Hub di **notifica** selezionare **Baidu (Android China)** dal menu a sinistra. 
2. Immettere il valore **Chiave API** ottenuto dalla console di Baidu nel progetto push cloud Baidu. 
3. Immettere il valore **Chiave privata** ottenuto dalla console di Baidu nel progetto push cloud Baidu. 
4. Selezionare **Salva**. 

    ![Screenshot di Hub di notifica che illustra la configurazione di Baidu (Android China) per le notifiche push](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>Passaggi successivi
Per un'esercitazione con istruzioni dettagliate per il push delle notifiche a Baidu usando hub di notifica di Azure e Baidu cloud push, vedere [Introduzione ad hub di notifica tramite Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).
