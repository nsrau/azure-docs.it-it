---
title: Configurare Google Firebase Cloud Messaging nell'hub di notifica di Azure | Microsoft Docs
description: Informazioni su come configurare un hub di notifica di Azure con le impostazioni di Google Firebase Cloud Messaging.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: d200f5746d5f7c62ff3a52873d5d5d6c3e1322dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60239191"
---
# <a name="configure-google-firebase-cloud-messaging-fcm-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurare le impostazioni di Google Firebase Cloud Messaging (FCM) per un hub di notifica nel portale di Azure
Questo articolo illustra come configurare le impostazioni di Google Firebase Cloud Messaging (FCM) per un hub di notifica di Azure usando il portale di Azure.  

## <a name="prerequisites"></a>Prerequisiti
Se non è stato già creato un hub di notifica, crearne uno ora. Per altre informazioni, vedere [Creare un hub di notifica di Azure nel portale di Azure](create-notification-hub-portal.md). 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Configurare Google Firebase Cloud Messaging (FCM)

La procedura seguente illustra i passaggi per configurare le impostazioni di Google Firebase Cloud Messaging (FCM) per un hub di notifica: 

1. Nel portale di Azure, nelle **Hub di notifica** pagina, selezionare **Google (GCM/FCM)** nel menu a sinistra. 
2. Incollare il valore di **Chiave API** per il progetto FCM salvato in precedenza. 
3. Selezionare **Salva**. 

   ![Screenshot che illustra come configurare Hub di notifica per Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Passaggi successivi
Per un'esercitazione con istruzioni dettagliate per il push di notifiche a dispositivi Android con hub di notifica di Azure e Google Firebase Cloud Messaging, vedere [inviare notifiche Push a dispositivi Android con hub di notifica e Google FCM ](notification-hubs-android-push-notification-google-fcm-get-started.md).

