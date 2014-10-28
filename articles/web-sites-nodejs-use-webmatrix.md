<properties linkid="web-site-with-webmatrix" urlDisplayName="Website with WebMatrix" pageTitle="Node.js website with WebMatrix - Azure tutorial" metaKeywords="" description="A tutorial that teaches you how to WebMatrix to develop and deploy a Node.js application to an Azure website." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build and deploy a Node.js website to Azure using WebMatrix" authors="larryfr" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Creare e distribuire un sito Web Node.js in Azure con WebMatrix

In questa esercitazione viene illustrato come usare WebMatrix per sviluppare e distribuire un'applicazione Node.js in un sito Web di Azure. WebMatrix è uno strumento di sviluppo Web gratuito di Microsoft che include tutto quanto necessario per lo sviluppo di siti Web. In WebMatrix sono incluse numerose funzionalità che semplificano l'uso di Node.js, ad esempio completamento del codice, modelli precompilati e supporto dell'editor per Jade, LESS e CoffeScript. Altre informazioni su [WebMatrix per Azure][WebMatrix per Azure].

Dopo avere completato questa guida, si avrà un sito Web Node.js in esecuzione in Azure.

Di seguito è riportata una schermata dell'applicazione completata:

![Sito Web node di Azure][Sito Web node di Azure]

[WACOM.INCLUDE [create-account-and-websites-note][create-account-and-websites-note]]

## Accesso ad Azure

Per creare un sito Web di Azure, attenersi alla procedura seguente.

<div class="dev-callout"><strong>Nota</strong>
<p>Per completare questa esercitazione, &egrave; necessario disporre di un account Azure in cui sia abilitata la funzionalit&agrave; Siti Web di Azure.</p>
<p>Se non si dispone di un account, &egrave; possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Versione di valutazione gratuita di Azure</a>.</p>
</div>

1.  Avvio di WebMatrix
2.  Se è la prima volta che si usa WebMatrix, verrà richiesto di effettuare l'accesso ad Azure. In caso contrario, è possibile fare clic sul pulsante **Sign In** e scegliere **Add Account**. Scegliere **Sign in** usando il proprio account Microsoft.

    ![Aggiunta di un account][Aggiunta di un account]

3.  Se è stata effettuata l'iscrizione per un account Azure, è possibile accedere con il proprio account Microsoft:

    ![Accesso ad Azure][Accesso ad Azure]

## Creazione di un sito usando un modello predefinito per Azure

1.  Nella schermata Start fare clic sul pulsante **New** e scegliere **Template Gallery** per creare un nuovo sito dalla raccolta modelli:

    ![Nuovo sito dalla raccolta modelli][Nuovo sito dalla raccolta modelli]

2.  Nella finestra di dialogo **Site from Template** selezionare **Node** e quindi **Express Site**. Infine, fare clic su **Next**. Se non sono presenti tutti i prerequisiti necessari per il modello **Express Site**, verrà richiesto di installarli.

    ![selezione modello express][selezione modello express]

3.  Se è stato effettuato l'accesso ad Azure, a questo punto è possibile creare un sito Web di Azure per il sito locale. Scegliere un nome univoco e selezionare il data center in cui si desidera venga creato il sito:

    ![Creazione del sito in Azure][Creazione del sito in Azure]

4.  Dopo la creazione del sito Web con WebMatrix, verrà visualizzato l'ambiente IDE WebMatrix.

    ![IDE WebMatrix][IDE WebMatrix]

## Pubblicazione dell'applicazione in Azure

1.  In WebMatrix fare clic su **Publish** sulla barra multifunzione **Home** per visualizzare la finestra di dialogo **Publish Preview** relativa al sito Web.

    ![Anteprima della pubblicazione][Anteprima della pubblicazione]

2.  Fare clic su **Continue**. Quando la pubblicazione viene completata, nella parte inferiore dell'IDE WebMatrix viene visualizzato l'URL del sito Web in Azure.

    ![Pubblicazione completata][Pubblicazione completata]

3.  Fare clic sul collegamento per aprire il sito Web nel browser.

    ![Sito Web Express][Sito Web Express]

## Modifica e ripubblicazione dell'applicazione

Modificare e ripubblicare l'applicazione è facile. Ora, si procederà all'aggiunta di una semplice modifica all'intestazione nel file **index.jade** e si ripubblicherà l'applicazione.

1.  In WebMatrix selezionare **Files**, quindi espandere la cartella **views**. Fare doppio clic sul file **index.jade** per aprirlo.

    ![Visualizzazione del file index.jage in WebMatrix][Visualizzazione del file index.jage in WebMatrix]

2.  Sostituire la seconda riga con la riga seguente:

        p Welcome to #{title} with WebMatrix on Azure!

3.  Salvare le modifiche e quindi fare clic sull'icona di pubblicazione. Infine, fare clic su **Continue** nella finestra di dialogo**Publish Preview** e attendere che l'aggiornamento venga pubblicato.

    ![Anteprima della pubblicazione][1]

4.  Dopo che la pubblicazione è completata, usare il collegamento restituito al termine del processo di pubblicazione per visualizzare il sito aggiornato.

    ![Sito Web node di Azure][Sito Web node di Azure]

## Passaggi successivi

Per informazioni sulle versioni di Node.js fornite con Azure e su come specificare la versione da usare con l'applicazione, vedere [Specifica di una versione di Node.js in un'applicazione Azure][Specifica di una versione di Node.js in un'applicazione Azure].

Se si verificano problemi con l'applicazione dopo la distribuzione in Azure, vedere [Come eseguire il debug di un'applicazione Node.js in Siti Web di Azure][Come eseguire il debug di un'applicazione Node.js in Siti Web di Azure] per informazioni su come diagnosticare l'errore.

  [WebMatrix per Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409
  [Sito Web node di Azure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Versione di valutazione gratuita di Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A7171371E
  [Aggiunta di un account]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png
  [Accesso ad Azure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png
  [Nuovo sito dalla raccolta modelli]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png
  [selezione modello express]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png
  [Creazione del sito in Azure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png
  [IDE WebMatrix]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png
  [Anteprima della pubblicazione]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png
  [Pubblicazione completata]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png
  [Sito Web Express]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png
  [Visualizzazione del file index.jage in WebMatrix]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png
  [1]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png
  [Specifica di una versione di Node.js in un'applicazione Azure]: /it-it/documentation/articles/nodejs-specify-node-version-azure-apps/
  [Come eseguire il debug di un'applicazione Node.js in Siti Web di Azure]: http://www.windowsazure.com/it-it/develop/nodejs/how-to-guides/Debug-Website/
