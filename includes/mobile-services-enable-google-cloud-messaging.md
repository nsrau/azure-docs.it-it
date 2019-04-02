---
title: File di inclusione
description: File di inclusione
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/06/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: a7d0a899b4a2ef86153e82a4e3f04e7ec430a1bc
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488043"
---
1. Passare a [Google Cloud Console](https://console.developers.google.com/cloud-resource-manager)ed eseguire l'accesso con le credenziali dell'account Google. 
2. Selezionare **Crea progetto** sulla barra degli strumenti. 
   
    ![Creare un nuovo progetto](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   
3. In **Project name** (Nome del progetto) immettere un nome per il progetto e fare clic su **Create** (Crea).
4. Selezionare il pulsante **Alerts** (Avvisi) sulla barra degli strumenti e selezionare il progetto nell'elenco. Verrà visualizzato il dashboard del progetto. È anche possibile passare direttamente al dashboard tramite l'URL: `https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>`

    ![Selezionare il progetto negli avvisi](./media/mobile-services-enable-google-cloud-messaging/alert-new-project.png)
5. Prendere nota del numero di progetto in **Project number** nel riquadro **Project info** (Informazioni sul progetto) del dashboard. 

    ![ID progetto](./media/mobile-services-enable-google-cloud-messaging/project-number.png)
6. Nel dashboard, nel riquadro **APIs**  (API) selezionare **Go to APIs overview** (Vai a panoramica API). 

    ![Collegamento alla panoramica delle API](./media/mobile-services-enable-google-cloud-messaging/go-to-api-overview.png)
7. Nella pagina **API** selezionare **Enable APIs and Services** (Abilita API e servizi). 

    ![Pulsante Enable APIs and Services (Abilita API e servizi)](./media/mobile-services-enable-google-cloud-messaging/enable-api-services-button.png)
8. Cercare e selezionare **Google Cloud Messaging**. 

    ![Cercare e selezionare Google Cloud Messaging](./media/mobile-services-enable-google-cloud-messaging/search-select-gcm.png)
9. Per abilitare Google Cloud Messaging per il progetto, selezionare **Enable** (Abilita).

    ![Abilitare Google Cloud Messaging](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-button.png)
10. Selezionare **Create credentials** (Crea credenziali) sulla barra degli strumenti. 

    ![Pulsante Create credentials (Crea credenziali)](./media/mobile-services-enable-google-cloud-messaging/create-credentials-button.png)
11. Nella pagina **Add credentials to your project** (Aggiungi credenziali al progetto) selezionare il collegamento **API key** (Chiave API). 

    ![Pulsante Create credentials (Crea credenziali)](./media/mobile-services-enable-google-cloud-messaging/api-key-button.png)    
12. Nella pagina **API key** (Chiave API) selezionare **Create/Save** (Crea/Salva). Nell'esempio seguente è selezionata l'opzione **IP addresses** (Indirizzi IP) e si immette **0.0.0.0/0** per gli indirizzi IP consentiti. È consigliabile limitare la chiave API in modo appropriato. 

    ![Chiave API - Pulsante Create (Crea)](./media/mobile-services-enable-google-cloud-messaging/api-key-create-button.png)
13. Copiare la **chiave API** negli Appunti e salvarla altrove. 

    ![Copiare la chiave API](./media/mobile-services-enable-google-cloud-messaging/copy-api-key.png)
   
    Questo valore della chiave dell'API verrà usato successivamente per abilitare Azure per l'autenticazione con GCM e l'invio di notifiche push per conto dell'app. Per tornare al dashboard del progetto, usare l'URL: `https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>`

