---
title: Configurare il servizio di notifica Push di Windows in Hub di notifica di Azure Documenti Microsoft
description: Informazioni su come configurare le impostazioni del servizio di notifica Push Windows per un hub di notifica di Azure.Learn how to configure Windows Push Notification Service settings for an Azure notification hub.
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
ms.openlocfilehash: 73304e191242725c80204efb132c26aede9ce7e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127321"
---
# <a name="configure-windows-push-notification-service-settings-in-the-azure-portal"></a>Configurare le impostazioni del servizio notifica Push Windows nel portale di AzureConfigure Windows Push Notification Service settings in the Azure portal

Questo articolo illustra come configurare le impostazioni del servizio di notifica di Windows (WNS) per un hub di notifica di Azure usando il portale di Azure.This article shows you how to configure Windows Notification Service (WNS) settings for an Azure notification hub by using the Azure portal.  

## <a name="prerequisites"></a>Prerequisiti
Se non è stato già creato un hub di notifica, crearne uno ora. Per altre informazioni, vedere [Creare un hub di notifica di Azure nel portale di Azure](create-notification-hub-portal.md). 

## <a name="configure-windows-push-notification-service-wns"></a>Configurare il servizio Notifica Push Windows (WNS)

La procedura seguente illustra come configurare le impostazioni del servizio notifica Push Windows (WNS) per un hub di notifica:The following procedure gives you steps to configure Windows Push Notification Service (WNS) settings for a notification hub: 

1. Nel portale di Azure, nella pagina **Hub di notifica,** selezionare **Windows (WNS)** nel menu a sinistra.
2. Immettere i valori per **SID pacchetto** e **Chiave di sicurezza**.
3. Selezionare **Salva**.

   ![Screenshot che illustra le caselle SID pacchetto e Chiave di sicurezza](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Passaggi successivi
Per un'esercitazione con istruzioni dettagliate per il push delle notifiche alle applicazioni della piattaforma Windows universale tramite Hub di notifica di Azure e il servizio notifica Push Windows (WNS), vedere [Inviare notifiche alle app UWP tramite Hub di notifica](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)di Azure.


