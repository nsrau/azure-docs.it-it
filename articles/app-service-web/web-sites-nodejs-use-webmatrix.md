---
title: Creazione e distribuzione di un sito Web Node.js in Azure con WebMatrix
description: Un&quot;esercitazione in cui viene illustrato come utilizzare WebMatrix per sviluppare un&quot;applicazione Node. js e distribuirla in Azure applicazione servizio Web Apps.
services: app-service\web
documentationcenter: nodejs
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 42986058-57b8-42ea-af76-d6c6ba508608
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 54ae907409bd860f26216e85e52932bc4bd825c1


---
# <a name="build-and-deploy-a-nodejs-web-app-to-azure-using-webmatrix"></a>Creazione e distribuzione di un sito Web Node.js in Azure con WebMatrix
Questa esercitazione illustra come usare WebMatrix per sviluppare un'applicazione Node.js e come distribuirla in app Web del [Servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714). WebMatrix è uno strumento di sviluppo Web gratuito di Microsoft che include tutto quanto necessario per lo sviluppo di siti Web o app Web. In WebMatrix sono incluse numerose funzionalità che semplificano l'utilizzo di Node.js, ad esempio completamento del codice, modelli precompilati e supporto dell'editor per Jade, LESS e CoffeScript. Altre informazioni su [WebMatrix](https://www.microsoft.com/web/webmatrix/).

Dopo avere completato questa guida, si disporrà di un sito Web Node.js in esecuzione in Azure.

Di seguito è riportata una schermata dell'applicazione completata:

![Sito Web node di Azure][webmatrix-node-completed]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

> [!NOTE]
> Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](https://azure.microsoft.com/try/app-service/), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
> 
> 

## <a name="sign-into-azure"></a>Accesso ad Azure
Seguire le istruzioni per creare un'app Web in Azure.

1. Avvio di WebMatrix
2. Se è la prima volta che si utilizza WebMatrix, verrà richiesto di effettuare l'accesso ad Azure.  In caso contrario, è possibile fare clic sul pulsante **Accedi** e scegliere **Aggiungi account**.  Scegliere **Sign in** utilizzando il proprio account Microsoft.
   
    ![Add Account][addaccount]
3. Se è stata effettuata l'iscrizione per un account Azure, è possibile accedere con il proprio account Microsoft:
   
    ![Accesso ad Azure][signin]    

## <a name="create-a-site-using-a-built-in-template-for-azure"></a>Creazione di un sito utilizzando un modello predefinito per Azure
1. Nella schermata Start fare clic sul pulsante **Nuovo** e scegliere **Raccolta modelli** per creare un nuovo sito dalla raccolta modelli:
   
    ![Nuovo sito dalla raccolta modelli][sitefromtemplate]
2. Nella finestra di dialogo **Sito da modello**, selezionare **Node.js** e quindi selezionare **Sito di Express**. Infine, fare clic su **Next**. Se non sono presenti tutti i prerequisiti necessari per il modello **Express Site** , verrà richiesto di installarli.
   
    ![selezione modello express][webmatrix-templates]
3. Se è stato effettuato l'accesso ad Azure, a questo punto è possibile creare un sito Web di Azure per il sito locale.  Scegliere un nome univoco e selezionare il data center in cui si desidera venga creata l'istanza di servizio app per app Web: 
   
    ![Creazione del sito in Azure][nodesitefromtemplateazure]
4. Dopo la creazione del sito locale e la creazione dell'applicazione web servizio App, viene visualizzato l'IDE di WebMatrix.
   
    ![IDE WebMatrix][webmatrix-ide]

## <a name="publish-your-application-to-azure"></a>Pubblicazione dell'applicazione in Azure
1. In WebMatrix fare clic su **Pubblica** sulla barra multifunzione **Home** per visualizzare la finestra di dialogo **Anteprima di pubblicazione** relativa al sito Web.
   
    ![Publish Preview][webmatrix-node-publishpreview]
2. Fare clic su **Continue**. Quando la pubblicazione viene completata, nella parte inferiore dell'IDE WebMatrix viene visualizzato l'URL del sito Web in Azure.
   
    ![Pubblicazione completata][webmatrix-publish-complete]
3. Fare clic sul collegamento per aprire l'applicazione web servizio App nel browser.
   
    ![Sito Web Express][webmatrix-node-express-site]

## <a name="modify-and-republish-your-application"></a>Modifica e ripubblicazione dell'applicazione
Modificare e ripubblicare l'applicazione è facile. Ora, si procederà all'aggiunta di una semplice modifica all'intestazione nel file **index.jade** e si ripubblicherà l'applicazione.

1. In WebMatrix selezionare **File**, quindi espandere la cartella **views**. Fare doppio clic sul file **index.jade** per aprirlo.
   
    ![Visualizzazione del file index.jage in WebMatrix][webmatrix-modify-index]
2. Sostituire la riga del paragrafo con la riga seguente:
   
        p Welcome to #{title} with WebMatrix on Azure!
3. Salvare le modifiche e quindi fare clic sull'icona di pubblicazione. Infine, fare clic su **Continue** in the **Publish Preview** e attendere che l'aggiornamento venga pubblicato.
   
    ![Publish Preview][webmatrix-republish]
4. Dopo che la pubblicazione è completata, utilizzare il collegamento restituito al termine del processo di pubblicazione per visualizzare il sito aggiornato.
   
    ![Sito Web node di Azure][webmatrix-node-completed]

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulle versioni di Node.js fornite con Azure e su come specificare la versione da utilizzare con l'applicazione, vedere [Specifica di una versione di Node.js in un'applicazione Azure](../nodejs-specify-node-version-azure-apps.md).

Se si verificano problemi con l'applicazione dopo la distribuzione in Azure, vedere [Come eseguire il debug di un'applicazione Node.js in Siti Web di Azure](web-sites-nodejs-debug.md) per informazioni su come diagnosticare l'errore.

## <a name="whats-changed"></a>Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)

[WebMatrix WebSite]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
[WebMatrix for Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409

[webmatrix-node-completed]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png
[webmatrix-templates]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png

[webmatrix-node-publishpreview]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png

[webmatrix-ide]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png
[webmatrix-publish-complete]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png
[webmatrix-node-express-site]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png
[webmatrix-modify-index]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png
[webmatrix-republish]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png
[addaccount]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png
[nodesitefromtemplateazure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png



<!--HONumber=Jan17_HO3-->


