---
title: Configurare Google Firebase Cloud Messaging in hub di notifica di Azure | Microsoft Docs
description: Informazioni su come configurare un hub di notifica di Azure con Google Firebase Cloud Messaging Settings.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 0da103c11e2412108535ca322917632f5d95559d
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760839"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurare le impostazioni di Google Firebase per un hub di notifica nel portale di Azure

Questo articolo illustra come configurare le impostazioni di Google Firebase Cloud Messaging (FCM) per un hub di notifica di Azure usando il portale di Azure.  

## <a name="prerequisites"></a>Prerequisiti

Se non è stato già creato un hub di notifica, crearne uno ora. Per altre informazioni, vedere [Creare un hub di notifica di Azure nel portale di Azure](create-notification-hub-portal.md).

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Configurare Google Firebase Cloud Messaging (FCM)

La procedura seguente descrive i passaggi per configurare le impostazioni di Google Firebase Cloud Messaging (FCM) per un hub di notifica:

1. Nella portale di Azure nella pagina Hub di **notifica** selezionare **Google (GCM/FCM)** dal menu a sinistra.
2. Incollare il valore di **Chiave API** per il progetto FCM salvato in precedenza.
3. Selezionare **Salva**.

   ![Screenshot che illustra come configurare Hub di notifica per Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Passaggi successivi

Per un'esercitazione con istruzioni dettagliate per l'invio di notifiche a dispositivi Android usando hub di notifica di Azure e Google Firebase Cloud Messaging, vedere [inviare notifiche push a dispositivi Android usando hub di notifica e Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).
