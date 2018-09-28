---
title: Registrare il contratto Enterprise di Azure in Cloudyn | Microsoft Docs
description: Usare il contratto Enterprise per registrarsi in Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/14/2018
ms.topic: quickstart
ms.custom: ''
ms.service: cost-management
manager: dougeby
ms.openlocfilehash: bed1ea3785c35d1053a0ff2147c3bdd797e28581
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996636"
---
# <a name="register-an-azure-enterprise-agreement-and-view-cost-data"></a>Registrare un Azure Enterprise Agreement e visualizzare i dati sui costi

È possibile usare il contratto Enterprise per registrarsi in Cloudyn. La registrazione consente di accedere al portale di Cloudyn. Questa guida introduttiva illustra nei dettagli il processo di registrazione necessario per creare una sottoscrizione di valutazione di Cloudyn e accedere al suo portale. Descrive anche come iniziare a visualizzare da subito i dati sui costi.

Gestione costi di Azure offre funzionalità simili a quelle di Cloudyn. Gestione costi di Azure è una soluzione di gestione dei costi nativa di Azure. Il servizio aiuta ad analizzare i costi, a creare e gestire budget, esportare i dati ed esaminare e implementare i consigli per l'ottimizzazione in modo da risparmiare. Per altre informazioni, vedere [Gestione costi di Azure](overview-cost-mgt.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

- Accedere al portale di Azure all'indirizzo http://portal.azure.com.

## <a name="register-with-cloudyn"></a>Registrarsi in Cloudyn

1. Nel portale di Azure fare clic su **Gestione dei costi e fatturazione** nell'elenco dei servizi.
2. In **Panoramica** fare clic su **Cloudyn**  
    ![Pagina Cloudyn](./media/quick-register-ea/cost-mgt-billing-service.png)
3. Nella pagina **Cloudyn** fare clic su **Vai a Cloudyn** per aprire la pagina di registrazione di Cloudyn in una nuova finestra.
4. Nella pagina della registrazione di valutazione del portale Cloudyn digitare il nome della società e quindi selezionare **Azure Enterprise Enrollment Administrator** (Amministratore delle registrazioni di Azure Enterprise).  
    ![registrazione di valutazione](./media/quick-register-ea/trial-reg.png)
5. Immettere la chiave API di registrazione di Enterprise Portal. Se non si dispone della chiave necessaria fare clic sul link [Enterprise Portal](https://ea.azure.com) ed eseguire i passaggi seguenti:
  1. Accedere al sito Web di Azure Enterprise e fare clic su **Report**, quindi su **API Access Key** (Chiave di accesso dell'API) e copiare la chiave primaria.  
    ![Chiave API di EA](./media/quick-register-ea/ea-key.png)
  3. Tornare alla pagina di registrazione e copiare la chiave API.
6. Accettare le condizioni d'uso e convalidare la chiave. Fare clic su **Avanti** per autorizzare Cloudyn a raccogliere i dati sulle risorse di Azure. Dalla sottoscrizione vengono raccolti dati riguardanti l'uso, le prestazioni, la fatturazione e dati dei tag.  
    ![convalida della chiave](./media/quick-register-ea/ea-key-validated.png)
7. In **Invite other stakeholder** (Invita altri stakeholder) è possibile aggiungere utenti digitando i loro indirizzi di posta elettronica. Al termine, fare clic su **Avanti**. A seconda delle dimensioni della registrazione di Azure, l'aggiunta dei dati di fatturazione a Cloudyn può richiedere fino a 24 ore.
8. Fare clic su **Go to Cloudyn** (Passa a Cloudyn) per aprire il portale di Cloudyn. Nella pagina **Cloud Accounts Management** (Gestione account cloud) saranno visualizzate le informazioni sugli account EA registrati.

Per un'esercitazione video sulla registrazione del contratto Enterprise, vedere [How to Find Your EA Enrollment ID and API Key for use in Cloudyn](https://youtu.be/u_phLs_udig) (Come individuare l'ID registrazione del contratto Enterprise e la chiave API da usare in Cloudyn).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva sono state usate le informazioni sul contratto Enterprise di Azure per registrarsi in Cloudyn. È stato anche eseguito l'accesso al portale di Cloudyn ed è stata avviata la visualizzazione dei dati sui costi. Per altre informazioni su Cloudyn, passare all'esercitazione su Cloudyn.

> [!div class="nextstepaction"]
> [Esaminare l'utilizzo e i costi](./tutorial-review-usage.md)
