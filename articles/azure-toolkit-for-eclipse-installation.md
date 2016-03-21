<properties
	pageTitle="Installare il Toolkit di Azure per Eclipse."
	description="Informazioni su come installare il Toolkit di Azure per Eclipse"
	services=""
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="multiple"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="03/09/2016" 
	ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

# Installare il Toolkit di Azure per Eclipse.

Il Toolkit di Azure per Eclipse offre modelli e funzionalità che permettono di creare, sviluppare, testare e distribuire con facilità applicazioni di Azure tramite l'ambiente di sviluppo Eclipse. Si tratta di un progetto Open Source, il cui codice sorgente è disponibile sotto la licenza Apache 2.0 dal sito del progetto in GitHub all'indirizzo seguente:

<https://github.com/MSOpenTech/WindowsAzureToolkitForEclipseWithJava>.

La procedura seguente mostra come installare il Toolkit di Azure per Eclipse.

[AZURE.INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## Per installare il Toolkit di Azure per Eclipse

1. Avviare Eclipse.
2. In Eclipse, nel menu fare clic su <strong>Guida</strong>, quindi fare clic su <strong>Installa nuovo Software</strong>, come illustrato nel diagramma seguente. ![Installare il Toolkit di Azure per Eclipse.][ic590123]
3. Nella finestra di dialogo <strong>Software disponibile</strong>, all'interno della casella di testo <strong>usare</strong> digitare <strong>http://dl.microsoft.com/eclipse</strong> seguiti dalla chiave <strong>Invio</strong>.
4. Nel riquadro <strong>Nome</strong>, controllare il <strong>Toolkit di Azure per Eclipse</strong>, e deselezionare <strong>Contattare tutti i siti di aggiornamento durante l'installazione per trovare il software richiesto</strong>. L'aspetto della schermata dovrebbe essere simile al seguente: ![Installare il Toolkit di Azure per Eclipse.][ic719482]
5. Se si espande il <strong>Toolkit di Azure per Eclipse</strong>, verrà visualizzato quanto segue:
    * **Il filtro Servizio di controllo di accesso di Azure**: fornisce il supporto per l’applicazione dell’autenticazione degli utenti con ACS di Azure.
    * **Plug-in comune di Azure**: questo componente contiene la funzionalità condivisa ritenuta attendibile da altri componenti.
    * **Il Toolkit di Azure per Eclipse**: questo componente contiene la logica di configurazione del progetto, la procedura guidata di pubblicazione al cloud e l’interfaccia utente.
    * **Server Microsoft JDBC Driver 4.0 per SQL**: questo componente semplifica lo sviluppo di applicazioni mediante il Database SQL.
    * **Pacchetto per Apache Qpid Client Libraries per JMS**: questo componente fornisce la libreria client JMS dal progetto Apache Qpid per consentire all'applicazione di usare la messaggistica basata su Advanced Messaging Queuing Protocol (AMQP) in Azure
    * **Pacchetto per librerie di Azure per Java**: questo componente consente di compilare applicazioni di Azure in Java che consentano di sfruttare i vantaggi delle risorse di computing del cloud scalabile di Azure.
    * **Plug-in di Application Insights per Java**: questo componente consente di usare servizi di analisi e di registrazione dei dati di telemetria di Azure per le applicazioni e le istanze del server.
6. Fare clic su **Avanti**. (Se si verificano ritardi insoliti durante l'installazione del toolkit, assicurarsi che **Contattare tutti i siti di aggiornamento durante l'installazione per trovare il software richiesto** sia deselezionato.)
7. Nella finestra di dialogo **Installare dettagli**, fare clic su **Avanti**.
8. Nella finestra di dialogo **Esaminare licenze**, rivedere le condizioni dei contratti di licenza. Se si accettano le condizioni dei contratti di licenza, fare clic su **Accetto le condizioni dei contratti di licenza** e quindi fare clic su **Fine**. (I passaggi rimanenti suppongono che le condizioni dei contratti di licenza siano state accettate. Se non si accettano le condizioni dei contratti di licenza, uscire dal processo di installazione.)
9. Se viene richiesto di riavviare Eclipse per completare l'installazione, fare clic su **Riavvia ora**.

## Vedere anche

[Toolkit di Azure per Eclipse]

[Creare un'applicazione Hello World per Azure in Eclipse]

[Novità nel Toolkit di Azure per Eclipse]

Per altre informazioni sull'uso di Azure con Java, visitare il [centro per sviluppatori Java di Azure].

<!-- URL List -->

[Toolkit di Azure per Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[centro per sviluppatori Java di Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Creare un'applicazione Hello World per Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Novità nel Toolkit di Azure per Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic590123]: ./media/azure-toolkit-for-eclipse-installation/ic590123.png
[ic719482]: ./media/azure-toolkit-for-eclipse-installation/ic719482.png

<!---HONumber=AcomDC_0309_2016-->