---
title: Configurare il servizio Apple Push Notification hub di notifica di Azure | Microsoft Docs
description: Informazioni su come configurare un hub di notifica di Azure con le impostazioni di Apple Push Notification Service (APNS).
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9a9db9f05895569b050e56cdeec1ee2ee25af0df
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488390"
---
# <a name="configure-apple-push-notification-service-apns-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurare le impostazioni di Apple Push Notification Service (APNS) per un hub di notifica nel portale di Azure
Questo articolo illustra come configurare le impostazioni di Apple Push Notification Service (APNS) per un hub di notifica di Azure usando il portale di Azure. 

## <a name="prerequisites"></a>Prerequisiti
Se non hai già creato un hub di notifica, crearne uno ora. Per altre informazioni, vedere [creare un hub di notifica di Azure nel portale di Azure](create-notification-hub-portal.md). 

## <a name="configure-apple-push-notification-service"></a>Configurare il servizio Apple Push Notification

La procedura seguente illustra i passaggi per configurare le impostazioni di Apple Push Notification Service (APNS) per un hub di notifica:

1. Nel portale di Azure, nelle **Hub di notifica** pagina, selezionare **Apple (APNS)** nel menu a sinistra.

1. Per la **modalità di autenticazione**, selezionare **certificato** oppure **Token**.

   a. Se si seleziona **certificato**:
   * Selezionare l'icona di file e quindi selezionare il *p12* file da caricare.
   * Immettere una password.
   * Selezionare la modalità **Sandbox**. In alternativa, per inviare notifiche push agli utenti che hanno acquistato l'app dallo store, selezionare **produzione** modalità.

     ![Screenshot di un servizio APN il certificato nel portale di Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Se si seleziona **Token**:

   * Immettere i valori per **Key ID**, **ID Bundle**, **ID Team**, e **Token**.
   * Selezionare la modalità **Sandbox**. In alternativa, per inviare notifiche push agli utenti che hanno acquistato l'app dallo store, selezionare **produzione** modalità.

     ![Screenshot di un servizio APN token configurazione nel portale di Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Passaggi successivi
Per un'esercitazione con istruzioni dettagliate per eseguire il push di notifiche a dispositivi iOS, vedere l'articolo seguente: [Inviare notifiche push a dispositivi iOS con hub di notifica e servizio APN](notification-hubs-ios-apple-push-notification-apns-get-started.md)
