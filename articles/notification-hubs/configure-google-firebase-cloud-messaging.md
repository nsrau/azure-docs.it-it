---
title: Configurare la messaggistica google Firebase Cloud in Hub di notifica di Azure Documenti Microsoft
description: Informazioni su come configurare un hub di notifica di Azure con le impostazioni di Google Firebase Cloud Messaging.
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
ms.openlocfilehash: 1adbce654bc5c057270df9a874911731a0135034
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127464"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurare le impostazioni di Google Firebase per un hub di notifica nel portale di AzureConfigure Google Firebase settings for a notification hub in the Azure portal

Questo articolo illustra come configurare le impostazioni di Google Firebase Cloud Messaging (FCM) per un hub di notifica di Azure tramite il portale di Azure.This article shows you how to configure Google Firebase Cloud Messaging (FCM) settings for an Azure notification hub by using the Azure portal.  

## <a name="prerequisites"></a>Prerequisiti
Se non è stato già creato un hub di notifica, crearne uno ora. Per altre informazioni, vedere [Creare un hub di notifica di Azure nel portale di Azure](create-notification-hub-portal.md). 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Configurare Google Firebase Cloud Messaging (FCM)

La procedura seguente illustra come configurare le impostazioni di Google Firebase Cloud Messaging (FCM) per un hub di notifica: 

1. Nel portale di Azure, nella pagina **Hub di notifica,** seleziona **Google (GCM/FCM)** nel menu a sinistra. 
2. Incollare il valore di **Chiave API** per il progetto FCM salvato in precedenza. 
3. Selezionare **Salva**. 

   ![Screenshot che illustra come configurare Hub di notifica per Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Passaggi successivi
Per un'esercitazione con istruzioni dettagliate per il push delle notifiche ai dispositivi Android tramite Hub di notifica di Azure e Google Firebase Cloud Messaging, vedere [Notifiche push ai dispositivi Android tramite Hub di notifica e Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).

