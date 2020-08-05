---
title: Cambiare il piano tariffario di uno spazio dei nomi di Hub di notifica | Microsoft Docs
description: Informazioni su come modificare il piano tariffario di uno spazio dei nomi di hub di notifica di Azure.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/03/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 1455259bc42aea9d506a9a2a19d725cac3d643f8
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87562770"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Cambiare il piano tariffario di uno spazio dei nomi di Hub di notifica di Azure

Hub di notifica è disponibile con tre piani tariffari: **Gratuito**, **Basic** e **Standard**. Questo articolo illustra come cambiare il piano tariffario per uno spazio dei nomi di Hub di notifica di Azure.

## <a name="overview"></a>Panoramica

In hub di notifica di Azure, un *Hub di notifica* è la risorsa/entità più piccola. Generalmente esegue il mapping a un'applicazione e può conservare un certificato per ogni Platform Notification System (PNS) supportato per l'app. L'applicazione può essere un'applicazione ibrida o nativa e multipiattaforma.

Uno *spazio dei nomi* è una raccolta di hub di notifica. Ogni spazio dei nomi in genere è costituito da hub correlati e usati per uno scopo specifico. Ad esempio, è possibile avere tre diversi spazi dei nomi a scopo di sviluppo, test e produzione, rispettivamente.

È possibile associare uno spazio dei nomi ai piani tariffari **gratuito**, **Basic**o **standard** . Per qualsiasi spazio dei nomi, è possibile usare il livello più adatto ai propri requisiti. Le sezioni seguenti illustrano come cambiare il piano tariffario di uno spazio dei nomi di Hub di notifica.

## <a name="use-azure-portal"></a>Usare il portale di Azure

Quando si usa la portale di Azure, è possibile modificare il piano tariffario per uno spazio dei nomi nella pagina spazio dei nomi o in una pagina Hub. Se si opera nella pagina di un hub, in pratica si cambia il piano tariffario a livello di spazio dei nomi. Pertanto, lo si cambia per lo spazio dei nomi e per tutti gli hub in esso inclusi.

### <a name="change-tier-on-the-namespace-page"></a>Cambiare piano tariffario nella pagina dello spazio dei nomi

Nella procedura riportata di seguito viene illustrato come modificare il piano tariffario per uno spazio dei nomi nella pagina spazio dei nomi. Quando si modifica il livello per uno spazio dei nomi, si applica a tutti gli Hub nello spazio dei nomi.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **tutti i servizi** nel menu a sinistra.
3. Selezionare **Spazi dei nomi dell'hub di notifica** nella sezione **Internet delle cose**. Se si seleziona l'asterisco ( `*` ) accanto al testo, questo viene aggiunto alla barra di spostamento a sinistra in **Preferiti**. Questo consente di accedere alla pagina spazi dei nomi più velocemente. Dopo averlo aggiunto ai **Preferiti**, selezionare **spazi dei nomi dell'hub di notifica**.

    ![Tutti i servizi -> Spazi dei nomi dell'hub di notifica](./media/change-pricing-tier/all-services-nhub.png)

4. Nella pagina **Spazi dei nomi dell'hub di notifica** selezionare lo spazio dei nomi per cui si vuole cambiare il piano tariffario.
5. Nella pagina **Spazio dei nomi dell'hub di notifica** relativa allo spazio dei nomi desiderato è possibile vedere il piano tariffario corrente nella sezione **Informazioni di base**. Nell'immagine seguente è possibile vedere che il piano tariffario dello spazio dei nomi è **Gratuito**.

    ![Piano tariffario corrente nella pagina dello spazio dei nomi](./media/change-pricing-tier/pricing-tier-before.png)

6. Nella pagina **spazio dei nomi dell'hub di notifica** per lo spazio dei nomi selezionare piano **tariffario** nella sezione **Gestisci** .

    ![Selezionare il piano tariffario nella pagina dello spazio dei nomi](./media/change-pricing-tier/namespace-select-pricing-menu.png)

7. Modificare il piano tariffario, quindi fare clic sul pulsante **Seleziona** .
8. È possibile visualizzare lo stato dell'azione di modifica del livello negli **avvisi**.
9. Passare alla pagina **Panoramica**. Verificare che il nuovo piano tariffario sia visualizzato per il campo **Piano tariffario** nella sezione **Informazioni di base**.
10. Questo passaggio è facoltativo. Selezionare un hub qualsiasi nello spazio dei nomi. Verificare che sia visualizzato lo stesso piano tariffario nella sezione **Informazioni di base**. Verrà visualizzato lo stesso piano tariffario per tutti gli Hub nello spazio dei nomi.

### <a name="change-tier-on-the-hub-page"></a>Cambiare piano tariffario nella pagina dell'hub

Per modificare il piano tariffario di uno spazio dei nomi nella pagina Hub, seguire questa procedura. Anche se si eseguono questi passaggi a partire dalla pagina Hub, si modifica effettivamente il piano tariffario per lo spazio dei nomi e tutti gli Hub nello spazio dei nomi:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **tutti i servizi** nel menu a sinistra.
3. Selezionare **Hub di notifica** nella sezione **Internet delle cose**.
4. Selezionare l'**hub** di notifica.
5. Scegliere **Piano tariffario** dal menu a sinistra.
6. Cambiare piano tariffario e fare clic sul pulsante **Seleziona**. Questa azione modifica l'impostazione del piano tariffario per lo spazio dei nomi contenente l'hub. In questo modo il nuovo piano tariffario verrà visualizzato nella pagina dello spazio dei nomi e nelle pagine di tutti gli hub.

> [!NOTE]
> Tutte le modifiche al piano tariffario sono effettive immediatamente.

## <a name="use-rest-api"></a>Usare l'API REST

È possibile usare le seguenti API REST del provider di risorse per recuperare il piano tariffario corrente e aggiornarlo.

### <a name="get-current-pricing-tier-for-a-namespace"></a>Recuperare il piano tariffario corrente per uno spazio dei nomi

Per ottenere il livello dello spazio dei nomi corrente, inviare un comando GET, come illustrato nell'esempio seguente:

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Aggiornare il piano tariffario per uno spazio dei nomi

Per aggiornare il livello dello spazio dei nomi, inviare un comando PUT, come illustrato nell'esempio seguente:

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su questi piani tariffari e sui prezzi, vedere [Prezzi di Hub di notifica](https://azure.microsoft.com/pricing/details/notification-hubs/).
