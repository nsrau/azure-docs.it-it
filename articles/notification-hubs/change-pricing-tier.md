---
title: Cambiare il piano tariffario di uno spazio dei nomi di Hub di notifica | Microsoft Docs
description: Informazioni su come cambiare il piano tariffario di uno spazio dei nomi di Hub di notifica di Azure.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/28/2019
ms.author: jowargo
ms.openlocfilehash: fb84cc0a6e2fc427727fa0c50583b7f1a37e0160
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55198868"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Cambiare il piano tariffario di uno spazio dei nomi di Hub di notifica di Azure
Hub di notifica è disponibile con tre piani tariffari: **Gratuito**, **Basic** e **Standard**. Questo articolo illustra come cambiare il piano tariffario per uno spazio dei nomi di Hub di notifica di Azure. 

## <a name="overview"></a>Panoramica
In Hub di notifica di Azure un **hub** corrisponde alla risorsa o entità più piccola, in genere mappata a un'applicazione e in grado di contenere un certificato per ogni Platform Notification System supportato per l'app. L'applicazione può essere ibrida o nativa e multipiattaforma.

Uno **spazio dei nomi** è una raccolta di hub di notifica. Ogni spazio dei nomi in genere è costituito da hub correlati e usati per uno scopo specifico. Ad esempio, si possono avere tre diversi spazi dei nomi rispettivamente a scopo di sviluppo, test e produzione. 

A livello di spazio dei nomi è possibile associare un piano tariffario. Hub di notifica supporta tre piani tariffari: **Gratuito**, **Basic** e **Standard**. Per uno spazio dei nomi usare il livello più adatto alle proprie esigenze. Le sezioni seguenti illustrano come cambiare il piano tariffario di uno spazio dei nomi di Hub di notifica. 

## <a name="use-azure-portal"></a>Usare il portale di Azure 
Quando si usa il portale di Azure, è possibile cambiare il piano tariffario per uno spazio dei nomi nella pagina dello spazio dei nomi o nella pagina di un hub.  Se si opera nella pagina di un hub, in pratica si cambia il piano tariffario a livello di spazio dei nomi. Pertanto, lo si cambia per lo spazio dei nomi e per tutti gli hub in esso inclusi. 

### <a name="change-tier-on-the-namespace-page"></a>Cambiare piano tariffario nella pagina dello spazio dei nomi
La procedura seguente illustra i passaggi per cambiare il piano tariffario per uno spazio dei nomi nella pagina dello spazio dei nomi. Quando si cambia il piano tariffario per uno spazio dei nomi, la modifica viene applicata a tutti gli hub nello spazio dei nomi.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Tutti i servizi** dal menu a sinistra. 
3. Selezionare **Spazi dei nomi dell'hub di notifica** nella sezione **Internet delle cose**. Se si seleziona la stella (`*`) accanto al testo, la pagina viene aggiunta alla barra di spostamento a sinistra sotto **PREFERITI**. Da questo momento in poi, si riuscirà ad accedere più rapidamente alla pagina degli spazi dei nomi. Dopo aver aggiunto la pagina ai PREFERITI, selezionare **Spazi dei nomi dell'hub di notifica**. 

    ![Tutti i servizi -> Spazi dei nomi dell'hub di notifica](./media/change-pricing-tier/all-services-nhub.png)
1. Nella pagina **Spazi dei nomi dell'hub di notifica** selezionare lo spazio dei nomi per cui si vuole cambiare il piano tariffario. 
2. Nella pagina **Spazio dei nomi dell'hub di notifica** relativa allo spazio dei nomi desiderato è possibile vedere il piano tariffario corrente nella sezione **Informazioni di base**. Nell'immagine seguente è possibile vedere che il piano tariffario dello spazio dei nomi è **Gratuito**. 

    ![Piano tariffario corrente nella pagina dello spazio dei nomi](./media/change-pricing-tier/pricing-tier-before.png)
1. Nella pagina **Spazio dei nomi dell'hub di notifica** relativa allo spazio dei nomi in questione selezionare **Piano tariffario** nella sezione **Gestisci**. 

    ![Selezionare il piano tariffario nella pagina dello spazio dei nomi](./media/change-pricing-tier/namespace-select-pricing-menu.png)
6. Cambiare piano tariffario e fare clic sul pulsante **Seleziona**.    
7. Lo stato dell'azione di modifica del piano tariffario è visibile negli **avvisi**. 
8. Passare alla pagina **Panoramica**. Verificare che il nuovo piano tariffario sia visualizzato per il campo **Piano tariffario** nella sezione **Informazioni di base**.     
1. Questo passaggio è facoltativo. Selezionare un hub qualsiasi nello spazio dei nomi. Verificare che sia visualizzato lo stesso piano tariffario nella sezione **Informazioni di base**. Dovrebbe essere visibile lo stesso piano tariffario per tutti gli hub inclusi nello spazio dei nomi. 

### <a name="change-tier-on-the-hub-page"></a>Cambiare piano tariffario nella pagina dell'hub
La procedura seguente illustra i passaggi per cambiare il piano tariffario per uno spazio dei nomi nella pagina dell'hub. Anche se si parte dalla pagina dell'hub per eseguire questi passaggi, in realtà si cambia il piano tariffario per lo spazio dei nomi e per tutti gli hub in esso inclusi. 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Tutti i servizi** dal menu a sinistra.
3. Selezionare **Hub di notifica** nella sezione **Internet delle cose**. 
4. Selezionare l'**hub** di notifica. 
5. Scegliere **Piano tariffario** dal menu a sinistra. 
6. Cambiare piano tariffario e fare clic sul pulsante **Seleziona**. Questa azione modifica l'impostazione del piano tariffario per lo spazio dei nomi contenente l'hub. In questo modo il nuovo piano tariffario verrà visualizzato nella pagina dello spazio dei nomi e nelle pagine di tutti gli hub. 

## <a name="use-rest-api"></a>Usare l'API REST
È possibile usare le seguenti API REST del provider di risorse per recuperare il piano tariffario corrente e aggiornarlo. 

### <a name="get-current-pricing-tier-for-a-namespace"></a>Recuperare il piano tariffario corrente per uno spazio dei nomi
Per recuperare il **piano tariffario corrente dello spazio dei nomi**, inviare un comando GET come illustrato nell'esempio seguente: 

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Aggiornare il piano tariffario per uno spazio dei nomi
Per **aggiornare il piano tariffario dello spazio dei nomi**, inviare un comando PUT come illustrato nell'esempio seguente: 

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```



## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su questi piani tariffari e sui prezzi, vedere [Prezzi di Hub di notifica](https://azure.microsoft.com/pricing/details/notification-hubs/).
