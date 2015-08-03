<properties 
	pageTitle="Archiviazione di script del server nel controllo del codice sorgente - Servizi mobili di Azure" 
	description="Informazioni sull'uso del componente aggiuntivo New Relic per monitorare il servizio mobile." 
	documentationCenter="" 
	authors="stepsic-microsoft-com" 
	manager="carolz"
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="stepsic"/>

# Utilizzare New Relic per il monitoraggio dei Servizi mobili

In questo argomento viene descritto come configurare il componente aggiuntivo New Relic di terze parti per funzionare con i Servizi mobili di Azure al fine di fornire il monitoraggio avanzato del servizio mobile.

In questa esercitazione vengono descritte le operazioni seguenti:

1. [Iscrizione a New Relic utilizzando Azure Store].
2. [Installazione del modulo New Relic].
3. [Abilitazione di Developer Analytics di New Relic per il servizio mobile].
4. [Monitoraggio del servizio mobile nel dashboard di New Relic].

Per completare l'esercitazione, è necessario avere già creato un servizio mobile in base alle procedure descritte nell'esercitazione [Introduzione a Servizi mobili] o [Introduzione ai dati].

##<a name="sign-up"></a>Iscriversi a New Relic usando Azure Store

Il primo passaggio consiste nell'acquisto del servizio New Relic. In questa esercitazione viene illustrato come acquistare questo servizio in Azure Store. I Servizi mobili supportano le sottoscrizioni a New Relic acquistate all'esterno di Azure Store.

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).

2. Nel riquadro inferiore del portale di gestione fare clic su **New**.

3. Fare clic su **Store**.

4. Nella finestra di dialogo **Choose an Add-on** selezionare **New Relic** e fare clic su **Next**.

5. Nella finestra di dialogo **Personalize Add-on** selezionare il piano New Relic a cui si desidera iscriversi.

7. Immettere il nome con il quale si desidera che il servizio New Relic venga visualizzato nelle impostazioni di Azure oppure utilizzare il valore predefinito **NewRelic**. Il nome deve essere univoco nell'elenco di elementi di Azure Store sottoscritti.

8. Scegliere un valore per l'area, ad esempio **West US**.

9. Fare clic su **Next**.

10. Nella finestra di dialogo **Review Purchase** rivedere le informazioni sul piano e sui prezzi e consultare le note legali. Se si accettano le condizioni, fare clic su **Purchase**.

11. Dopo avere fatto clic su **Purchase**, verrà avviata la creazione del nuovo account New Relic. È possibile monitorare lo stato nel portale di gestione di Azure.

##<a name="install-module"></a>Installare il modulo New Relic

Dopo aver effettuato l'iscrizione al servizio New Relic è necessario installare il modulo Node.js di New Relic nel servizio mobile. Affinché sia possibile caricare questo modulo è necessario che sia abilitato il controllo del codice sorgente per il servizio mobile.

1. Qualora non sia stato già fatto, seguire la procedura riportata nell'esercitazione [Archiviazione di script del server nel controllo del codice sorgente] per abilitare il controllo del codice sorgente per il servizio mobile, clonare il repository e installare <a href="http://nodejs.org/" target="_blank">Node Package Manager (NPM)</a>.

2. Passare alla cartella `.\service` del repository Git locale e quindi dal prompt dei comandi eseguire il comando seguente:

		npm install newrelic

	NPM consente di installare il [modulo New Relic][newrelic] nella sottodirectory `\newrelic`.

3. Aprire uno strumento da riga di comando Git, ad esempio **GitBash** (Windows) o **Bash** (Unix Shell) e digitare il comando seguente nel prompt dei comandi Git:

		$ git add .
		$ git commit -m "added newrelic module"
		$ git push origin master
		
	Verrà caricato il nuovo modulo `newrelic` nel servizio mobile.

Sarà quindi necessario abilitare il monitoraggio di New Relic del servizio mobile nel [portale di gestione][Azure Management Portal].

##<a name="enable-service"></a>Abilitare Developer Analytics di New Relic per il servizio mobile

1. Nel [portale di gestione][Azure Management Portal] selezionare il servizio mobile e quindi fare clic sulla scheda **Configure**.

	![][0]

2. Scorrere verso il basso fino a **Developer analytics** ed eseguire una delle operazioni seguenti, a seconda della modalità di acquisto della sottoscrizione di New Relic:

	+ Se acquistata in Azure store:

		Fare clic su **Add-on**, selezionare il componente aggiuntivo New Relic in **Choose add-on** e quindi fare clic su **Save**.

		![][1]

	+ Se acquistata direttamente da New Relic:

		Fare clic su **Custom**, selezionare New Relic in **Provider**, immettere la chiave e quindi fare clic su **Save**.

		![][2]

		La chiave può essere ottenuta nel dashboard di New Relic.

3. Al termine della registrazione verrà visualizzato un nuovo valore in **App settings**:

	![][3]

##<a name="monitor"></a>Monitorare il servizio mobile nel dashboard di New Relic

1. Eseguire l'app client per generare richieste di lettura, creazione, aggiornamento ed eliminazione per il servizio mobile.

2. Attendere alcuni minuti l'elaborazione dei dati, quindi passare al dashboard di New Relic.

	Quando la sottoscrizione di New Relic è stata acquistata come componente aggiuntivo selezionarla nel [portale di gestione][Azure Management Portal] e fare clic su **Manage**.

3. In New Relic fare clic su **Applications**, quindi fare clic sul servizio mobile.

	![][4]

4. Fare clic su **Web transactions** per visualizzare le richieste inviate di recente al servizio mobile:

	![][5]

##<a name="next-steps"> </a>Passaggi successivi

+ Per ottimizzare le prestazioni dell'app per dispositivi mobili **iOS**/**Android**, vedere [New Relic Mobile].
+ Per informazioni sui prezzi, vedere la [pagina dedicata a New Relic in Azure Store].
+ Per ulteriori informazioni sull'utilizzo di New Relic, vedere [Applications Overview] nella documentazione di New Relic. 

<!-- Anchors. -->
[Iscrizione a New Relic utilizzando Azure Store]: #sign-up
[Installazione del modulo New Relic]: #install-module
[Abilitazione di Developer Analytics di New Relic per il servizio mobile]: #enable-service
[Monitoraggio del servizio mobile nel dashboard di New Relic]: #monitor
[Next steps]: #next-steps

<!-- Images. -->
[0]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-tab.png
[1]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring.png
[2]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring-custom.png
[3]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring-complete.png
[4]: ./media/store-new-relic-mobile-services-monitor/mobile-new-relic-dashboard.png
[5]: ./media/store-new-relic-mobile-services-monitor/mobile-new-relic-dashboard-2.png

<!-- URLs. -->
[Source control]: http://msdn.microsoft.com/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Work with server scripts in Mobile Services]: ../work-with-server-scripts.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Node.js API Documentation: Modules]: http://nodejs.org/api/modules.html
[Archiviazione di script del server nel controllo del codice sorgente]: /develop/mobile/tutorials/store-scripts-in-source-control/
[newrelic]: https://npmjs.org/package/newrelic
[pagina dedicata a New Relic in Azure Store]: /gallery/store/new-relic/new-relic/
[Applications Overview]: https://docs.newrelic.com/docs/applications-dashboards/applications-overview
[Introduzione a Servizi mobili]: /develop/mobile/tutorials/get-started/
[Introduzione ai dati]: /develop/mobile/tutorials/get-started-with-data-dotnet
[New Relic Mobile]: http://newrelic.com/mobile-monitoring

 

<!---HONumber=July15_HO4-->