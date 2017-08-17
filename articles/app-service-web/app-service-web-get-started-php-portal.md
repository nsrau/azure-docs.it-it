---
title: Distribuire un'app di WordPress nel Portale di Azure in cinque minuti | Microsoft Docs
description: Informazioni su come eseguire facilmente App Web nel servizio app mediante la distribuzione di un'app di WordPress. Visualizzare i risultati immediatamente.
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 02/13/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 7ef5802866bf96859d3f44cdb58cbb412b08a700
ms.contentlocale: it-it
ms.lasthandoff: 03/01/2017

---
# <a name="deploy-a-wordpress-app-in-the-azure-portal-in-five-minutes"></a>Distribuire un'app di WordPress nel Portale di Azure in cinque minuti

Questa esercitazione illustra come distribuire la prima App Web di [WordPress](https://wordpress.org/) nel [servizio app di Azure](../app-service/app-service-value-prop-what-is.md) in pochi minuti.

![Sito WordPress](./media/app-service-web-get-started-php-portal/wpdashboard.png)

## <a name="prerequisites"></a>Prerequisiti
È necessario un account Microsoft Azure. Se non si ha un account, è possibile [iscriversi per ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) oppure [attivare i vantaggi per i sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> È possibile [provare il servizio app](https://azure.microsoft.com/try/app-service/) senza avere un account Azure. Creare un'app iniziale e provarla per un'ora, senza impegno e senza dover usare la carta di credito.
> 
> 

## <a name="deploy-the-wordpress-app"></a>Distribuire l'app di WordPress
1. Accedere al [portale di Azure](https://portal.azure.com).

2. Aprire [https://portal.azure.com/#create/WordPress.WordPress](https://portal.azure.com/#create/WordPress.WordPress).

    Questo collegamento è un scelta rapida per configurare immediatamente una nuova app di WordPress nel Portale di Azure.

3. In **Nome app**, digitare un nome per l'App Web. Se il nome è univoco nel dominio `azurewebsites.net`, verrà visualizzato un segno di spunta verde nella casella.
   
5. In **Gruppi risorse**, fare clic su **Crea nuovo** per creare un nuovo [gruppo di risorse](../azure-resource-manager/resource-group-overview.md), quindi assegnargli un nome.

6. In **Provider di database** selezionare **CleaDB**.

7. Fare clic su **Piano di servizio app/Località** > **Crea nuovo**. Configurare il [piano di servizio app](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) come illustrato:

    - In **Piano di servizio app** digitare il nome desiderato.
    - In **Località**, scegliere un percorso per ospitare il piano.
    - Fare clic su **Piano tariffario**, quindi selezionare **F1 Free** (F1 gratuito) o un altro livello adatto e fare clic su **Seleziona**.
    - Fare clic su **OK**.

8. Fare clic su **Database** > **Creare nuovo**. Configurare il Database SQL come illustrato:

    - In **Nome database** digitare un nome per il database. 
    - In **Località** scegliere la stessa ubicazione del piano di servizio App.
    - Fare clic su **Piano tariffario**, quindi selezionare **Mercurio** o un altro livello adatto e fare clic su **Seleziona**.
    - Fare clic su **Note legali** e quindi su **Acquista**.
    - Fare clic su **OK**.

9. Fare clic su **Crea**.

    A questo punto, Azure crea l'app di WordPress in base alla configurazione. Verrà visualizzata la notifica **La distribuzione è stata avviata...**.

    ![Distribuzione avviata, la prima app di WordPress nel servizio app di Azure](./media/app-service-web-get-started-php-portal/deployment-started.png)
   
## <a name="launch-and-manage-your-wordpress-web-app"></a>Avviare e gestire l'app Web WordPress

Quando Azure completa la distribuzione dell'app viene visualizzata un'altra notifica.

![Distribuzione riuscita, la prima app di WordPress nel servizio app di Azure](./media/app-service-web-get-started-php-portal/deployment-succeeded.png)

1. Fare clic sulla notifica. Se non è stata letta, è sempre possibile accedervi facendo clic sul simbolo della campana della notifica (![Campana di notifica, prima app di WordPress nel Servizio app di Azure](./media/app-service-web-get-started-dotnet-portal/notification.png)).

    Dovrebbe ora essere visualizzato il [pannello](../azure-resource-manager/resource-group-portal.md#manage-resources) per la gestione dell'App Web (*pannello*: una pagina del portale visualizzata in orizzontale).

3. Nella parte superiore della pagina Panoramica, fare clic su **Sfoglia**.
   
    ![Sfoglia, la prima app di WordPress nel servizio app di Azure](./media/app-service-web-get-started-php-portal/browse.png)

    A questo punto viene visualizzata la pagina di **Benvenuto** di WordPress. Configurare l'installazione di WordPress e iniziare a usarlo.

    ![Configurazione di WordPress, la prima app di WordPress nel Servizio app di Azure](./media/app-service-web-get-started-php-portal/wordpress-config.png)
    
## <a name="next-steps"></a>Passaggi successivi
* [Creare, configurare e distribuire un'App Web Laravel in Azure](app-service-web-php-get-started.md): informazioni sulle nozioni di base necessarie per eseguire le App Web PHP in Azure, ad esempio:

    * Creare e configurare app in Azure da PowerShell/Bash.
    * Impostare la versione di PHP.
    * Usare un file di avvio che non si trova nella directory dell'applicazione radice.
    * Abilitare l'automazione Composer.
    * Accedere a variabili specifiche dell'ambiente.
    * Risolvere i problemi comuni.

* [Distribuire il codice nel Servizio app di Azure](web-sites-deploy.md): informazioni su come distribuire da FTP o da repository di controllo del codice sorgente.
* [Aggiungere funzionalità alla prima App Web](app-service-web-get-started-2.md): eseguire l'app di Azure nel livello successivo. autenticare gli utenti, ridimensionare l'app in base alla richiesta e configurare alcuni avvisi sulle prestazioni, tutto con pochi clic.

