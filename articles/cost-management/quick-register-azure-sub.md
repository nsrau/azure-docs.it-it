---
title: Registrare la sottoscrizione di Azure in Cloudyn | Microsoft Docs
description: Usare la sottoscrizione di Azure per effettuare la registrazione in Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 08/07/2018
ms.topic: quickstart
ms.custom: ''
ms.service: cost-management
manager: dougeby
ms.openlocfilehash: e49be3d61cfbbf71a0e9cbeef4c171f3af222544
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46979557"
---
# <a name="register-an-individual-azure-subscription-and-view-cost-data"></a>Registrare una singola sottoscrizione di Azure e visualizzare i dati sui costi

È possibile usare la sottoscrizione di Azure per effettuare la registrazione in Cloudyn. La registrazione consente di accedere al portale di Cloudyn. Questa guida introduttiva illustra nei dettagli il processo di registrazione necessario per creare una sottoscrizione di valutazione di Cloudyn e accedere al suo portale. Descrive anche come iniziare a visualizzare da subito i dati sui costi.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

- Accedere al portale di Azure all'indirizzo http://portal.azure.com.

## <a name="register-with-cloudyn"></a>Registrarsi in Cloudyn

1. Nel portale di Azure fare clic su **Gestione dei costi e fatturazione** nell'elenco dei servizi.
2. In **Panoramica** fare clic su **Cloudyn**  
    ![Pagina Cloudyn](./media/quick-register-azure-sub/cost-mgt-billing-service.png)
3. Nella pagina **Gestione costi**  fare clic su **Vai a Gestione costi** per aprire la pagina di registrazione di Cloudyn in una nuova finestra.
4. Nella pagina della registrazione di valutazione del portale di Cloudyn digitare il nome della società e selezionare **Azure Individual Subscription Owner** (Proprietario della singola sottoscrizione di Azure) e fare clic su **Avanti**. Il nome account e l'ID tenant vengono compilati automaticamente.  
    ![Registrazione di valutazione](./media/quick-register-azure-sub/trial-reg-ind.png)
5. Selezionare **Offer ID - Name** (ID offerta - Nome) associato alla sottoscrizione. In caso di dubbi sull'ID tariffa per la sottoscrizione, è possibile cercare l'**ID offerta** nella fattura di Azure.
6. Accettare le condizioni per l'utilizzo, convalidare le informazioni e fare clic su **Avanti**.
7. Nella pagina **Gather additional data** (Raccogli dati aggiuntivi) fare clic su **Avanti** per autorizzare Cloudyn a raccogliere i dati delle risorse di Azure. Dalla sottoscrizione vengono raccolti dati riguardanti l'uso, le prestazioni, la fatturazione e dati dei tag.  
    ![Raccogliere dati aggiuntivi](./media/quick-register-azure-sub/gather-additional.png)
8. Nel browser si apre la pagina di accesso a Cloudyn. Accedere con le credenziali della sottoscrizione di Azure.
9. Fare clic su **Go to Cloudyn** (Passa a Cloudyn) per aprire il portale di Cloudyn. Nella pagina **Accounts Management** (Gestione account) saranno visualizzate le informazioni sugli account della sottoscrizione di Azure.  
    ![Gestione degli account](./media/quick-register-azure-sub/accounts-mgt.png)

Per un'esercitazione video sulla registrazione della sottoscrizione di Azure, vedere [Finding your Directory GUID and Rate ID for use in Cloudyn](https://youtu.be/PaRjnyaNGMI) (Individuare il GUID della directory e l'ID tariffa da usare in Cloudyn).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva sono state usate le informazioni della sottoscrizione di Azure per registrarsi in Cloudyn. È stato anche eseguito l'accesso al portale di Cloudyn ed è stata avviata la visualizzazione dei dati sui costi. Per altre informazioni su Cloudyn, passare all'esercitazione su Cloudyn.

> [!div class="nextstepaction"]
> [Esaminare l'utilizzo e i costi](./tutorial-review-usage.md)
