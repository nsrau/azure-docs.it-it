---
title: Configurare Baidu Cloud Push negli hub di notifica di Azure Documenti Microsoft
description: Informazioni su come configurare le impostazioni Baidu per un hub di notifica di Azure.Learn how to configure Baidu settings for an Azure notification hub.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212510"
---
# <a name="deprecated-configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>Deprecato: Configurare le impostazioni di Baidu Cloud Push per un hub di notifica nel portale di AzureDeprecated: Configure Baidu Cloud Push settings for a notification hub in the Azure portal

Questo articolo illustra come configurare le impostazioni Baidu Cloud Push per un hub di notifica di Azure usando il portale di Azure.This article shows you how to configure Baidu Cloud Push settings for an Azure notification hub by using the Azure portal.

> [!IMPORTANT]
> Questa esercitazione è deprecata. 

## <a name="prerequisites"></a>Prerequisiti
Se non è stato già creato un hub di notifica, crearne uno ora. Per altre informazioni, vedere [Creare un hub di notifica di Azure nel portale di Azure](create-notification-hub-portal.md). 

## <a name="configure-baidu-cloud-push"></a>Configurare Baidu Cloud Push
La procedura seguente illustra come configurare le impostazioni Baidu Cloud Push per un hub di notifica:

1. Nel portale di Azure, nella pagina **Hub di notifica,** selezionare **Baidu (Android Cina)** nel menu a sinistra. 
2. Immettere il valore **Chiave API** ottenuto dalla console di Baidu nel progetto push cloud Baidu. 
3. Immettere il valore **Chiave privata** ottenuto dalla console di Baidu nel progetto push cloud Baidu. 
4. Selezionare **Salva**. 

    ![Screenshot di Hub di notifica che illustra la configurazione di Baidu (Android China) per le notifiche push](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>Passaggi successivi
Per un'esercitazione con istruzioni dettagliate per il push delle notifiche a Baidu tramite Hub di notifica di Azure e Baidu Cloud Push, vedere Introduzione agli hub di [notifica tramite Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).
