<properties linkid="mobile-services-monitor-new-relic" urlDisplayName="Use New Relic to monitor Mobile Services" pageTitle="Store server scripts in source control - Azure Mobile Services" metaKeywords="" description="Learn how to use the New Relic add-on to monitor your mobile service." metaCanonical="" disqusComments="1" umbracoNaviHide="0" documentationCenter="Mobile" title="Use New Relic to monitor Mobile Services" authors="new relic" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="new="" relic" />

# Utilizzare New Relic per il monitoraggio dei Servizi mobili

In questo argomento viene descritto come configurare il componente aggiuntivo New Relic di terze parti per funzionare con i Servizi mobili di Azure al fine di fornire il monitoraggio avanzato del servizio mobile.

In questa esercitazione vengono descritte le operazioni seguenti:

1.  [Iscrizione a New Relic utilizzando Azure Store][Iscrizione a New Relic utilizzando Azure Store].
2.  [Installazione del modulo New Relic][Installazione del modulo New Relic].
3.  [Abilitazione di Developer Analytics di New Relic per il servizio mobile][Abilitazione di Developer Analytics di New Relic per il servizio mobile].
4.  [Monitoraggio del servizio mobile nel dashboard di New Relic][Monitoraggio del servizio mobile nel dashboard di New Relic].

Per completare l'esercitazione, è necessario avere già creato un servizio mobile in base alle procedure descritte nell'esercitazione [Introduzione a Servizi mobili][Introduzione a Servizi mobili] o [Introduzione ai dati][Introduzione ai dati].

## <a name="sign-up"></a>Iscrizione a New Relic utilizzando Azure Store

Il primo passaggio consiste nell'acquisto del servizio New Relic. In questa esercitazione viene illustrato come acquistare questo servizio in Azure Store. I Servizi mobili supportano le sottoscrizioni a New Relic acquistate all'esterno di Azure Store.

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].

2.  Nel riquadro inferiore del portale di gestione fare clic su **New**.

3.  Fare clic su **Store**.

4.  Nella finestra di dialogo **Choose an Add-on** selezionare **New Relic** e fare clic su **Next**.

5.  Nella finestra di dialogo **Personalize Add-on** selezionare il piano New Relic a cui si desidera iscriversi.

6.  Immettere il nome con il quale si desidera che il servizio New Relic venga
    visualizzato nelle impostazioni di Azure oppure usare il valore predefinito **NewRelic**. Il nome deve essere univoco
    nell'elenco degli elementi di Azure Store sottoscritti.

7.  Scegliere un valore per l'area, ad esempio **West US**.

8.  Fare clic su **Avanti**.

9.  Nella finestra di dialogo **Rivedi acquisto** rivedere le informazioni sul piano e sui prezzi
    e consultare le note legali. Se si accettano le condizioni, fare clic su **Purchase**.

10. Dopo avere fatto clic su **Purchase**, verrà avviata la creazione del nuovo account New Relic. È possibile monitorare lo stato nel portale di gestione di Azure.

## <a name="install-module"></a>Installazione del modulo New Relic.

Dopo aver effettuato l'iscrizione al servizio New Relic è necessario installare il modulo Node.js di New Relic nel servizio mobile. Affinché sia possibile caricare questo modulo è necessario che sia abilitato il controllo del codice sorgente per il servizio mobile.

1.  Qualora non sia stato già fatto, attenersi alla procedura riportata nell'esercitazione [Archiviazione di script del server nel controllo del codice sorgente][Archiviazione di script del server nel controllo del codice sorgente] in modo da abilitare il controllo del codice sorgente per il servizio mobile, clonare l'archivio e installare [Node Package Manager (NPM)][Node Package Manager (NPM)].

2.  Passare alla cartella `.\service` dell'archivio Git locale e quindi dal prompt dei comandi eseguire il comando seguente:

        npm install newrelic

    NPM consente di installare il [modulo New Relic][modulo New Relic] nella sottodirectory `\newrelic`.

3.  Aprire uno strumento da riga di comando Git, ad esempio **GitBash** (Windows) o **Bash** (Unix Shell) e digitare il comando seguente nel prompt dei comandi Git:

        $ git add .
        $ git commit -m "added newrelic module"
        $ git push origin master

    Verrà caricato il nuovo modulo `newrelic` nel servizio mobile.

Sarà quindi necessario abilitare il monitoraggio di New Relic del servizio mobile nel [portale di gestione][portale di gestione].

## <a name="enable-service"></a>Abilitazione di Developer Analytics di New Relic per il servizio mobile

1.  Nel [portale di gestione][portale di gestione] selezionare il servizio mobile e quindi fare clic sulla scheda **Configure**.

    ![][0]

2.  Scorrere verso il basso fino a **Developer analytics** ed eseguire una delle operazioni seguenti, a seconda della modalità di acquisto della sottoscrizione di New Relic:

    -   Se acquistata in Azure store:

        Fare clic su **Add-on**, selezionare il componente aggiuntivo New Relic in **Choose add-on** e quindi fare clic su **Save**.

        ![][1]

    -   Se acquistata direttamente da New Relic:

        Fare clic su **Custom**, selezionare New Relic in **Provider**, immettere la chiave e quindi fare clic su **Save**.

        ![][2]

        La chiave può essere ottenuta nel dashboard di New Relic.

3.  Al termine della registrazione verrà visualizzato un nuovo valore in **App settings**:

    ![][3]

## <a name="monitor"></a>Monitoraggio del servizio mobile nel dashboard di New Relic

1.  Eseguire l'app client per generare richieste di lettura, creazione, aggiornamento ed eliminazione per il servizio mobile.

2.  Attendere alcuni minuti l'elaborazione dei dati, quindi passare al dashboard di New Relic.

    Quando la sottoscrizione di New Relic è stata acquistata come componente aggiuntivo selezionarla nel [portale di gestione][portale di gestione] e fare clic su **Manage**.

3.  In New Relic fare clic su **Applications**, quindi fare clic sul servizio mobile.

    ![][4]

4.  Fare clic su **Web transactions** per visualizzare le richieste inviate di recente al servizio mobile:

    ![][5]

## <a name="next-steps"> </a>Passaggi successivi

-   Per informazioni sui prezzi, vedere la [pagina dedicata a New Relic in Azure Store][pagina dedicata a New Relic in Azure Store].
-   Per ulteriori informazioni sull'utilizzo di New Relic, vedere [Applications Overview][Applications Overview] nella documentazione di New Relic.



  [Iscrizione a New Relic utilizzando Azure Store]: #sign-up
  [Installazione del modulo New Relic]: #install-module
  [Abilitazione di Developer Analytics di New Relic per il servizio mobile]: #enable-service
  [Monitoraggio del servizio mobile nel dashboard di New Relic]: #monitor
  [Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started/
  [Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-dotnet
  [portale di gestione di Azure]: https://manage.windowsazure.com
  [Archiviazione di script del server nel controllo del codice sorgente]: /it-it/develop/mobile/tutorials/store-scripts-in-source-control/
  [modulo New Relic]: https://npmjs.org/package/newrelic
  [portale di gestione]: https://manage.windowsazure.com/
  [0]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-tab.png
  [1]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring.png
  [2]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring-custom.png
  [3]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring-complete.png
  [4]: ./media/store-new-relic-mobile-services-monitor/mobile-new-relic-dashboard.png
  [5]: ./media/store-new-relic-mobile-services-monitor/mobile-new-relic-dashboard-2.png
  [pagina dedicata a New Relic in Azure Store]: /it-it/gallery/store/new-relic/new-relic/
  [Applications Overview]: https://docs.newrelic.com/docs/applications-dashboards/applications-overview
