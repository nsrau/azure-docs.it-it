<properties
	pageTitle="Distribuire la prima app Web Java in Azure in cinque minuti | Microsoft Azure"
	description="Informazioni su come eseguire facilmente app Web nel servizio app mediante la distribuzione di un'app di esempio. È possibile eseguire rapidamente vere e proprie attività di sviluppo con risultati immediati."
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""
/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="09/16/2016"
	ms.author="cephalin"
/>

# Distribuire la prima app Web Java in Azure in cinque minuti

Questa esercitazione illustra come distribuire una semplice app Web Java nel [servizio app di Azure](../app-service/app-service-value-prop-what-is.md). Il servizio app consente di creare app Web, [back-end di app per dispositivi mobili](/documentation/learning-paths/appservice-mobileapps/) e [app per le API](../app-service-api/app-service-api-apps-why-best-platform.md).

Si apprenderà come:

- Creare un'app Web nel servizio app di Azure.
- Distribuire un'app Java di esempio.
- Osservare il codice in esecuzione nell'ambiente di produzione.

## Prerequisiti

- Ottenere un client FTP/FTPS, ad esempio [FileZilla](https://filezilla-project.org/).
- Ottenere un account Microsoft Azure. Se non si ha un account, è possibile [iscriversi per ottenere una versione di valutazione gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) oppure [attivare i vantaggi della sottoscrizione di Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] È possibile [provare il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751) senza avere un account Azure. Creare un'app iniziale e provarla per un'ora, senza impegno e senza dover usare la carta di credito.

<a name="create"></a>
## Creare un'app Web

1. Accedere al [portale di Azure](https://portal.azure.com) con il proprio account Azure.

2. Nel menu a sinistra fare clic su **Nuovo** > **Web e dispositivi mobili** > **App Web**.

    ![](./media/app-service-web-get-started-languages/create-web-app-portal.png)

3. Nel pannello di creazione dell'app usare le impostazioni seguenti per la nuova app:

    - **Nome dell'app**: digitare un nome univoco.
    - **Gruppo di risorse**: selezionare **Crea nuovo** e assegnare un nome al gruppo di risorse.
    - **Piano di servizio app/Località**: fare clic per configurare e quindi scegliere **Crea nuovo** per impostare il nome, la località e il piano tariffario del piano di servizio app. È possibile usare il piano tariffario **Gratuito**.

    Al termine, il pannello di creazione dell'app dovrebbe avere un aspetto simile al seguente:

    ![](./media/app-service-web-get-started-languages/create-web-app-settings.png)

3. Fare clic su **Crea** nella parte inferiore della schermata. È possibile fare clic sull'icona delle **notifiche** in alto per visualizzare lo stato di avanzamento.

    ![](./media/app-service-web-get-started-languages/create-web-app-started.png)

4. Al termine della distribuzione dovrebbe essere visualizzato il messaggio di notifica seguente. Fare clic sul messaggio per aprire il pannello della distribuzione.

    ![](./media/app-service-web-get-started-languages/create-web-app-finished.png)

5. Nel pannello **La distribuzione è riuscita** fare clic sul collegamento **Risorsa** per aprire il pannello della nuova app Web.

    ![](./media/app-service-web-get-started-languages/create-web-app-resource.png)

## Distribuire un'app Java nell'app Web

Verrà ora illustrato come distribuire un'app Java in Azure con FTPS.

5. Nel pannello dell'app Web scorrere verso il basso fino alla voce **Impostazioni dell'applicazione** oppure cercarla e quindi fare clic su di essa.

    ![](./media/app-service-web-get-started-languages/set-java-application-settings.png)

6. In **Versione Java** selezionare **Java 8** e fare clic su **Salva**.

    ![](./media/app-service-web-get-started-languages/set-java-application-settings.png)

    Quando viene visualizzata la notifica **Le impostazioni dell'app Web sono state aggiornate**, passare a http://*&lt;appname>*.azurewebsites.net per verificare il funzionamento del servlet JSP predefinito.

7. Tornare al pannello dell'app Web e scorrere verso il basso fino alla voce **Credenziali per la distribuzione** oppure cercarla e quindi fare clic su di essa.

8. Impostare le credenziali per la distribuzione e fare clic su **Salva**.

7. Tornare al pannello dell'app Web e fare clic su **Panoramica**. Fare clic sul pulsante **Copia** accanto a **Nome utente FTP/distribuzione** e **Nome host FTPS** per copiare tali valori.

    ![](./media/app-service-web-get-started-languages/get-ftp-url.png)

    A questo punto è possibile distribuire l'app Java con FTPS.

8. Nel client FTP/FTPS accedere al server FTP dell'app Web di Azure usando i valori copiati nell'ultimo passaggio. Usare la password per la distribuzione creata in precedenza.

    Lo screenshot seguente mostra l'accesso con FileZilla.

    ![](./media/app-service-web-get-started-languages/filezilla-login.png)

    Potrebbero essere visualizzati avvisi di sicurezza per il certificato SSL non riconosciuto di Azure. Continuare.

9. Fare clic su [questo collegamento](https://github.com/Azure-Samples/app-service-web-java-get-started/raw/master/webapps/ROOT.war) per scaricare il file WAR nel computer locale.

9. Nel client FTP/FTPS passare a **/site/wwwroot/webapps** nel sito remoto e trascinare in tale directory remota il file WAR scaricato nel computer locale.

    ![](./media/app-service-web-get-started-languages/transfer-war-file.png)

    Fare clic su **OK** per sostituire il file in Azure.

    >[AZURE.NOTE] In conformità al comportamento predefinito di Tomcat, il nome file **ROOT.war** in /site/wwwroot/webapps indica l'app Web radice (http://*&lt;appname>*.azurewebsites.net) e il nome file ***&lt;nome>*.war** indica un'app Web denominata (http://*&lt;appname>*.azurewebsites.net/*&lt;nome>*).

La procedura è terminata. A questo punto l'app Java è in esecuzione in Azure. Nel browser passare a http://*&lt;appname>*.azurewebsites.net per verificarne il funzionamento.

## Eseguire aggiornamenti dell'app

Ogni volta che è necessario eseguire un aggiornamento, è sufficiente caricare il nuovo file WAR nella stessa directory remota con il client FTP/FTPS.

## Passaggi successivi

[Creare un'app Web Java da un modello in Azure Marketplace](app-service-web-java-get-started.md#marketplace). È possibile ottenere un contenitore Tomcat completamente personalizzabile e usare la familiare interfaccia utente di gestione.

Eseguire il debug dell'app Web di Azure direttamente in [IntelliJ](app-service-web-debug-java-web-app-in-intellij.md) o [Eclipse](app-service-web-debug-java-web-app-in-eclipse.md).

In alternativa, è possibile fare altre prove con la prima app Web, ad esempio:

- Provare [altri modi per distribuire il codice in Azure](../app-service-web/web-sites-deploy.md). Per eseguire la distribuzione da un repository GitHub, ad esempio, è sufficiente selezionare **GitHub** anziché **Repository Git locale** in **Opzioni di distribuzione**.
- Ottimizzare l'app Azure: autenticare gli utenti, ridimensionare l'app in base alla richiesta e configurare alcuni avvisi sulle prestazioni, tutto con pochi clic. Vedere [Aggiungere funzionalità alla prima app Web](app-service-web-get-started-2.md).

<!----HONumber=AcomDC_0920_2016-->
