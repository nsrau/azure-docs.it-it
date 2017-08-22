---
title: Distribuire un'app Umbraco nel Portale di Azure in cinque minuti | Microsoft Docs
description: Informazioni su come eseguire facilmente app Web nel servizio app distribuendo un'app ASP.NET di esempio. Visualizzare i risultati immediatamente.
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 6b1dede903083d1771733330a069b6ab533d9f00
ms.contentlocale: it-it
ms.lasthandoff: 04/06/2017

---
# <a name="deploy-an-umbraco-web-app-in-the-azure-portal-in-five-minutes"></a>Distribuire un'app Umbraco nel Portale di Azure in cinque minuti

Questa esercitazione illustra come distribuire un'app Web [Umbraco](https://our.umbraco.org/) nel [Servizio app di Azure](../app-service/app-service-value-prop-what-is.md) in pochi minuti.

![App Umbraco](./media/app-service-web-get-started-dotnet-portal/defaultpage.png)

## <a name="prerequisites"></a>Prerequisiti
È necessario un account Microsoft Azure. Se non si ha un account, è possibile [iscriversi per ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) oppure [attivare i vantaggi per i sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> È possibile [provare il servizio app](https://azure.microsoft.com/try/app-service/) senza avere un account Azure. Creare un'app iniziale e provarla per un'ora, senza impegno e senza dover usare la carta di credito.
> 
> 

## <a name="deploy-the-aspnet-app"></a>Distribuire l'app ASP.NET
1. Accedere al [portale di Azure](https://portal.azure.com).

2. Aprire [https://portal.azure.com/#create/umbracoorg.UmbracoCMS](https://portal.azure.com/#create/umbracoorg.UmbracoCMS).

    Questo collegamento è un scelta rapida per configurare immediatamente una nuova app di Umbraco nel Portale di Azure.

3. In **Nome app**, digitare un nome per l'App Web. Se il nome è univoco nel dominio `azurewebsites.net`, verrà visualizzato un segno di spunta verde nella casella.
   
5. In **Gruppi risorse**, fare clic su **Crea nuovo** per creare un nuovo [gruppo di risorse](../azure-resource-manager/resource-group-overview.md), quindi assegnargli un nome.

7. Fare clic su **Piano di servizio app/Località** > **Crea nuovo**. Configurare il [piano di servizio app](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) come illustrato:

    - In **Piano di servizio app** digitare il nome desiderato.
    - In **Località**, scegliere un percorso per ospitare il piano.
    - Fare clic su **Piano tariffario**, quindi selezionare **F1 Free** (F1 gratuito) o un altro livello adatto e fare clic su **Seleziona**.
    - Fare clic su **OK**.

    La configurazione CMS di Umbraco sarà simile alla seguente:

    ![Configurazione in corso: la prima app Umbraco in Servizio app di Azure](./media/app-service-web-get-started-dotnet-portal/configure-in-progress.png)

12. Fare clic su **Database SQL** > **Crea un nuovo database**. Configurare il Database SQL come illustrato:

    - In **Nome** digitare un nome, ad esempio **myDB**.
    - Fare clic su **Piano tariffario**, quindi selezionare **F Free** (F gratuito) o un altro livello adatto e fare clic su **Seleziona**.
    - Fare clic su **Server di destinazione** > **Creare un nuovo server**. Configurare il server del database, come illustrato:

        - In **Nome server** digitare il nome del server. Se il nome è univoco nel dominio `.database.windows.net`, verrà visualizzato un segno di spunta verde nella casella.
        - In **Account di accesso amministratore server** digitare il nome utente amministratore desiderato.
        - In **Password** e **Conferma password** digitare la password desiderata.
        - In Località, selezionare la stessa località usata per l'app Web.
        - Verificare che l'opzione **Consenti ai servizi di Azure di accedere al server** sia selezionata.
        - Fare clic su **Seleziona**.
    
    - Fare clic su **Seleziona**.

13. Fare clic su **Impostazioni app Web**, specificare il nome utente e la password del database e fare clic su **OK**.

    La configurazione CMS di Umbraco sarà simile alla seguente:

    ![Configurazione completata: la prima app Umbraco in Servizio app di Azure](./media/app-service-web-get-started-dotnet-portal/configure-complete.png)

14. Fare clic su **Crea**.
    
    A questo punto, Azure crea l'app di Umbraco in base alla configurazione. Verrà visualizzata la notifica **La distribuzione è stata avviata...**.

    ![Distribuzione riuscita, la prima app di Umbraco nel servizio app di Azure](./media/app-service-web-get-started-dotnet-portal/deployment-started.png)
   
## <a name="launch-and-manage-your-umrbaco-web-app"></a>Avviare e gestire l'app Web Umbraco

Quando Azure completa la distribuzione dell'app viene visualizzata un'altra notifica.

![Distribuzione riuscita, la prima app di Umbraco nel servizio app di Azure](./media/app-service-web-get-started-dotnet-portal/deployment-succeeded.png)

1. Fare clic sulla notifica. Se non è stata letta, è sempre possibile accedervi facendo clic sul simbolo della campana della notifica (![Campana di notifica, prima app di Umbraco nel Servizio app di Azure](./media/app-service-web-get-started-dotnet-portal/notification.png)).

    Dovrebbe ora essere visualizzato il [pannello](../azure-resource-manager/resource-group-portal.md#manage-resources) per la gestione dell'app Web (*pannello*: una pagina del portale visualizzata in orizzontale).

3. Nella parte superiore della pagina Panoramica, fare clic su **Sfoglia**.
   
    ![Sfoglia, la prima app di Umbraco nel servizio app di Azure](./media/app-service-web-get-started-dotnet-portal/browse.png)

    A questo punto viene visualizzata la pagina di **Benvenuto** di Umbraco. Configurare l'installazione di Umbraco e iniziare a usarla.

    ![Configurazione di Umbraco: la prima app Umbraco in Servizio app di Azure](./media/app-service-web-get-started-dotnet-portal/umbraco-config.png)
    
## <a name="next-steps"></a>Passaggi successivi
* [Distribuire un'app web ASP.NET in Servizio app di Azure, tramite Visual Studio](app-service-web-get-started-dotnet.md): informazioni su come creare una nuova app Web di Azure da Visual Studio, mediante uno dei modelli di applicazione incluso.
* [Distribuire il codice nel Servizio app di Azure](web-sites-deploy.md): informazioni su come distribuire da FTP o da repository di controllo del codice sorgente.
* [Aggiungere funzionalità alla prima app Web](app-service-web-get-started-2.md): eseguire l'app di Azure nel livello successivo. autenticare gli utenti, ridimensionare l'app in base alla richiesta e configurare alcuni avvisi sulle prestazioni, tutto con pochi clic.

