---
title: Configurare Google Firebase Cloud Messaging in hub di notifica di Azure | Microsoft Docs
description: Informazioni su come configurare un hub di notifica di Azure con Google Firebase Cloud Messaging Settings.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 803289476d4d8869f7828b2843a983ee0c86bfbd
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212490"
---
# <a name="configure-google-firebase-cloud-messaging-fcm-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurare le impostazioni di Google Firebase Cloud Messaging (FCM) per un hub di notifica nel portale di Azure
Questo articolo illustra come configurare le impostazioni di Google Firebase Cloud Messaging (FCM) per un hub di notifica di Azure usando il portale di Azure.  

## <a name="prerequisites"></a>Prerequisiti
Se non è stato già creato un hub di notifica, crearne uno ora. Per altre informazioni, vedere [Creare un hub di notifica di Azure nel portale di Azure](create-notification-hub-portal.md). 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Configurare Google Firebase Cloud Messaging (FCM)

La procedura seguente illustra i passaggi per configurare le impostazioni di Google Firebase Cloud Messaging (FCM) per un hub di notifica: 

1. Nella portale di Azure nella pagina Hub di **notifica** selezionare **Google (GCM/FCM)** dal menu a sinistra. 
2. Incollare il valore di **Chiave API** per il progetto FCM salvato in precedenza. 
3. Selezionare **Salva**. 

   ![Screenshot che illustra come configurare Hub di notifica per Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Passaggi successivi
Per un'esercitazione con istruzioni dettagliate per eseguire il push delle notifiche ai dispositivi Android usando hub di notifica di Azure e Google Firebase Cloud Messaging, vedere [notifiche push a dispositivi Android usando hub di notifica e Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).

