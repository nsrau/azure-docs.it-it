---
title: Registrare la sottoscrizione di Azure in Cloudyn | Microsoft Docs
description: Questa guida introduttiva illustra nei dettagli il processo di registrazione necessario per creare una sottoscrizione di valutazione di Cloudyn e accedere al suo portale.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/07/2018
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management
manager: benshy
ms.openlocfilehash: b5975f3d11a76d7713928ab008dbd9ec19b85738
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53082579"
---
# <a name="register-an-individual-azure-subscription-and-view-cost-data"></a>Registrare una singola sottoscrizione di Azure e visualizzare i dati sui costi

È possibile usare la sottoscrizione di Azure per effettuare la registrazione in Cloudyn. La registrazione consente di accedere al portale di Cloudyn. Questa guida introduttiva illustra nei dettagli il processo di registrazione necessario per creare una sottoscrizione di valutazione di Cloudyn e accedere al suo portale. Descrive anche come iniziare a visualizzare da subito i dati sui costi.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

- Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="register-with-cloudyn"></a>Registrarsi in Cloudyn

1. Nel portale di Azure fare clic su **Gestione dei costi e fatturazione** nell'elenco dei servizi.
2. In **Panoramica** fare clic su **Cloudyn**  
    ![Pagina di Cloudyn mostrata nel portale di Azure](./media/quick-register-azure-sub/cost-mgt-billing-service.png)
3. Nella pagina **Gestione costi**  fare clic su **Vai a Gestione costi** per aprire la pagina di registrazione di Cloudyn in una nuova finestra.
4. Nella pagina della registrazione di valutazione del portale di Cloudyn digitare il nome della società e selezionare **Azure Individual Subscription Owner** (Proprietario della singola sottoscrizione di Azure) e fare clic su **Avanti**. Il nome account e l'ID tenant vengono compilati automaticamente.  
    ![Pagina di registrazione di valutazione in cui immettere le informazioni di registrazione](./media/quick-register-azure-sub/trial-reg-ind.png)
5. Selezionare **Offer ID - Name** (ID offerta - Nome) associato alla sottoscrizione. In caso di dubbi sull'ID tariffa per la sottoscrizione, è possibile cercare l'**ID offerta** nella fattura di Azure.
6. Accettare le condizioni per l'utilizzo, convalidare le informazioni e fare clic su **Avanti**.
7. Nella pagina **Gather additional data** (Raccogli dati aggiuntivi) fare clic su **Avanti** per autorizzare Cloudyn a raccogliere i dati delle risorse di Azure. Dalla sottoscrizione vengono raccolti dati riguardanti l'uso, le prestazioni, la fatturazione e dati dei tag.  
    ![Raccogliere la pagina di dati aggiuntivi in cui si autorizza Cloudyn](./media/quick-register-azure-sub/gather-additional.png)
8. Nel browser si apre la pagina di accesso a Cloudyn. Accedere con le credenziali della sottoscrizione di Azure.
9. Fare clic su **Go to Cloudyn** (Passa a Cloudyn) per aprire il portale di Cloudyn. Nella pagina **Accounts Management** (Gestione account) saranno visualizzate le informazioni sugli account della sottoscrizione di Azure.  
    ![Pagina di gestione degli account che mostra le informazioni di sottoscrizione di Azure](./media/quick-register-azure-sub/accounts-mgt.png)

Per un'esercitazione video sulla registrazione della sottoscrizione di Azure, vedere [Finding your Directory GUID and Rate ID for use in Cloudyn](https://youtu.be/PaRjnyaNGMI) (Individuare il GUID della directory e l'ID tariffa da usare in Cloudyn).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva sono state usate le informazioni della sottoscrizione di Azure per registrarsi in Cloudyn. È stato anche eseguito l'accesso al portale di Cloudyn ed è stata avviata la visualizzazione dei dati sui costi. Per altre informazioni su Cloudyn, passare all'esercitazione su Cloudyn.

> [!div class="nextstepaction"]
> [Esaminare l'utilizzo e i costi](./tutorial-review-usage.md)
