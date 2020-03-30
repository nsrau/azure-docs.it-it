---
title: Configurare il servizio di notifica Push Microsoft in Hub di notifica di Azure Documenti Microsoft
description: Informazioni su come configurare le impostazioni del servizio di notifica Push Microsoft per un hub di notifica di Azure.Learn how to configure Microsoft Push Notification Service settings for an Azure notification hub.
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
ms.openlocfilehash: 99f29e7910fe6070c6202f6a936173455f979732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127331"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-in-the-azure-portal"></a>Configurare le impostazioni del servizio notifica Push Microsoft (MPNS) nel portale di AzureConfigure Microsoft Push Notification Service (MPNS) settings in the Azure portal

Questo articolo illustra come configurare le impostazioni del servizio notifica Push Microsoft (MPNS) per un hub di notifica di Azure usando il portale di Azure.This article shows you how to configure Microsoft Push Notification Service (MPNS) settings for an Azure notification hub by using the Azure portal. 

## <a name="prerequisites"></a>Prerequisiti
Se non è stato già creato un hub di notifica, crearne uno ora. Per altre informazioni, vedere [Creare un hub di notifica di Azure nel portale di Azure](create-notification-hub-portal.md). 

## <a name="configure-microsoft-push-notification-service-mpns"></a>Configurare microsoft Push Notification Service (MPNS)

La procedura seguente illustra come configurare le impostazioni del servizio notifica Push Microsoft (MPNS) per un hub di notifica: 

1. Nel portale di Azure, nella pagina **Hub di notifica,** selezionare **Windows Phone (MPNS)** nel menu a sinistra.
1. Abilitare le notifiche push non autenticate o autenticate:

   a. Per abilitare le notifiche push non autenticate, selezionare Abilita > **salvataggio** **push non autenticato**.

      ![Screenshot che illustra come abilitare le notifiche push non autenticate](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Per abilitare le notifiche push autenticate:
      * Selezionare **Carica certificato** sulla barra degli strumenti.
      * Selezionare l'icona del file e quindi il file del certificato.
      * Immettere la password per il certificato.
      * Selezionare **OK**.
      * Nella pagina **Windows Phone(MPNS)** selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi
Per un'esercitazione con istruzioni dettagliate per il push delle notifiche ai dispositivi Windows Phone tramite Hub di notifica di Azure e Microsoft Push Notification Service (MPNS), vedere Notifiche push alle app di [Windows Phone tramite Hub](notification-hubs-windows-mobile-push-notifications-mpns.md)di notifica .

