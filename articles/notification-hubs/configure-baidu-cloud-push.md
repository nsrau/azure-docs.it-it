---
title: Configurare Baidu Cloud Push nell'hub di notifica di Azure | Microsoft Docs
description: Informazioni su come configurare le impostazioni di Baidu per un hub di notifica di Azure.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9942b1d4d8b5d538f5150a36e596753282039be7
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488379"
---
# <a name="configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurare le impostazioni di Baidu Cloud Push per un hub di notifica nel portale di Azure
Questo articolo illustra come configurare le impostazioni di Baidu Cloud Push per un hub di notifica di Azure usando il portale di Azure. 

## <a name="prerequisites"></a>Prerequisiti
Se non hai già creato un hub di notifica, crearne uno ora. Per altre informazioni, vedere [creare un hub di notifica di Azure nel portale di Azure](create-notification-hub-portal.md). 

## <a name="configure-baidu-cloud-push"></a>Configurare Baidu Cloud Push
La procedura seguente illustra i passaggi per configurare le impostazioni di Baidu Cloud Push per un hub di notifica:

1. Nel portale di Azure, nelle **Hub di notifica** pagina, selezionare **Baidu (Android China)** nel menu a sinistra. 
2. Immettere il **chiave Api** ottenuta dalla console di Baidu nel progetto push cloud Baidu. 
3. Immettere il **chiave privata** ottenuta dalla console di Baidu nel progetto push cloud Baidu. 
4. Selezionare **Salva**. 

    ![Screenshot dell'hub di notifica che mostra la configurazione di Baidu (Android China) per le notifiche push](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>Passaggi successivi
Per un'esercitazione con istruzioni dettagliate per l'invio di notifiche alle Baidu tramite hub di notifica di Azure e Baidu Cloud Push, vedere [Introduzione ad hub di notifica tramite Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).
