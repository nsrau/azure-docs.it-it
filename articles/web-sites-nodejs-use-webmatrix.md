<properties urlDisplayName="Website with WebMatrix" pageTitle="Sito Web Node.js con WebMatrix - Esercitazione su Azure" metaKeywords="" description="A tutorial that teaches you how to WebMatrix to develop and deploy a Node.js application to an Azure website." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build and deploy a Node.js website to Azure using WebMatrix" authors="larryfr" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />


# Creare e distribuire un sito Web Node.js in Azure con WebMatrix

In questa esercitazione viene illustrato come usare WebMatrix per sviluppare e distribuire un'applicazione Node.js in un sito Web di Azure. WebMatrix è uno strumento di sviluppo Web gratuito di Microsoft che include tutto quanto necessario per lo sviluppo di siti Web. In WebMatrix sono incluse numerose funzionalità che semplificano l'uso di Node.js, ad esempio completamento del codice, modelli precompilati e supporto dell'editor per Jade, LESS e CoffeScript. Altre informazioni su [WebMatrix per Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409).

Dopo avere completato questa guida, si avrà un sito Web Node.js in esecuzione in Azure.
 
Di seguito è riportata una schermata dell'applicazione completata:

![Azure node Web site][webmatrix-node-completed]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Accesso ad Azure

Per creare un sito Web di Azure, attenersi alla procedura seguente.

<div class="dev-callout"><strong>Nota</strong>
<p>Per completare questa esercitazione, è necessario disporre di un account Azure in cui sia abilitata la funzionalità Siti Web di Azure.</p>
<p>Se non si ha un account, è possibile creare un account di versione di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Versione di valutazione gratuita di Azure</a>.</p>
</div>
<br />

1. Avvio di WebMatrix
2. Se è la prima volta che si usa WebMatrix, verrà richiesto di effettuare l'accesso ad Azure.  In caso contrario, è possibile fare clic sul pulsante **Accedi** e scegliere **Aggiungi account**.  Scegliere **Accedi** per accedere usando il proprio account Microsoft.

	![Add Account][addaccount]

3. Se è stata effettuata l'iscrizione per un account Azure, è possibile accedere con il proprio account Microsoft:

	![Sign into Azure][signin]	


## Creazione di un sito usando un modello predefinito per Azure

1. Nella schermata Start fare clic sul pulsante **Nuovo** e scegliere **Raccolta modelli** per creare un nuovo sito dalla raccolta modelli:

	![New site from Template Gallery][sitefromtemplate]

2. Nella finestra di dialogo **Sito da modello**, selezionare **Node.js** e quindi selezionare **Sito di Express**. Infine, fare clic su **Successivo**. Se non sono presenti tutti i prerequisiti necessari per il modello **Sito di Express**, verrà richiesto di installarli.

	![select express template][webmatrix-templates]

3. Se è stato effettuato l'accesso ad Azure, a questo punto è possibile creare un sito Web di Azure per il sito locale.  Scegliere un nome univoco e selezionare il data center in cui si desidera venga creato il sito: 

	![Create site on Azure][nodesitefromtemplateazure]
	
4. Dopo la creazione del sito Web con WebMatrix, verrà visualizzato l'ambiente IDE WebMatrix.

	![webmatrix ide][webmatrix-ide]

##Pubblicazione dell'applicazione in Azure

1. In WebMatrix fare clic su **Pubblica**sulla barra multifunzione **Home** per visualizzare la finestra di dialogo **Anteprima della pubblicazione** per il sito Web.

	![publish preview][webmatrix-node-publishpreview]

2. Fare clic su **Continua**. Quando la pubblicazione viene completata, nella parte inferiore dell'IDE WebMatrix viene visualizzato l'URL del sito Web in Azure.

	![publish complete][webmatrix-publish-complete]

3. Fare clic sul collegamento per aprire il sito Web nel browser.

	![Express web site][webmatrix-node-express-site]

##Modifica e ripubblicazione dell'applicazione

Modificare e ripubblicare l'applicazione è facile. Ora verrà effettuata una semplice modifica all'intestazione nel file **index.jade** e si ripubblicherà l'applicazione.

1. In WebMatrix selezionare **File**, quindi espandere la cartella **views**. Fare doppio clic sul file **index.jade** per aprirlo.

	![webmatrix viewing index.jade][webmatrix-modify-index]

2. Sostituire la seconda riga con la riga seguente:

		p Welcome to #{title} with WebMatrix on Azure!

3. Salvare le modifiche e quindi fare clic sull'icona di pubblicazione. Infine, fare clic su **Continua**nella finestra di dialogo **Pubblica**e attendere che l'aggiornamento venga pubblicato.

	![publish preview][webmatrix-republish]

4. Dopo che la pubblicazione è completata, usare il collegamento restituito al termine del processo di pubblicazione per visualizzare il sito aggiornato.

	![Azure node Web site][webmatrix-node-completed]

##Passaggi successivi

Per informazioni sulle versioni di Node.js fornite con Azure e su come specificare la versione da usare con l'applicazione, vedere [Specifica di una versione di Node.js in un'applicazione Azure](/it-it/documentation/articles/nodejs-specify-node-version-azure-apps/).

Se si verificano problemi con l'applicazione dopo la distribuzione in Azure, vedere [Come eseguire il debug di un'applicazione Node.js in Siti Web di Azure](http://www.windowsazure.com/it-it/develop/nodejs/how-to-guides/Debug-Website/) per informazioni su come diagnosticare l'errore.


[Portale di gestione di Azure]: http://manage.windowsazure.com
[Sito Web di WebMatrix]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
[WebMatrix per Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409

[Pubblicazione di un sito Web di Azure usando Git]: /it-it/develop/nodejs/common-tasks/publishing-with-git/
[gratuitamente]: /it-it/pricing/free-trial
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
