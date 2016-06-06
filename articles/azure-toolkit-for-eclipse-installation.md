<properties
	pageTitle="Installare il Toolkit di Azure per Eclipse | Microsoft Azure"
	description="Informazioni su come installare il Toolkit di Azure per Eclipse."
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
	ms.date="05/19/2016" 
	ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

# Installare il Toolkit di Azure per Eclipse.

Il Toolkit di Azure per Eclipse offre modelli e funzionalità che permettono di creare, sviluppare, testare e distribuire con facilità applicazioni di Azure tramite l'ambiente di sviluppo Eclipse. Il Toolkit di Azure per Eclipse è un progetto Open Source, il cui codice sorgente è disponibile con la licenza MIT dal sito del progetto in GitHub all'indirizzo seguente:

<https://github.com/microsoft/azure-tools-for-java>

La procedura seguente mostra come installare il Toolkit di Azure per Eclipse.

[AZURE.INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## Per installare il Toolkit di Azure per Eclipse

1. Avviare Eclipse.

1. Dal menu di Eclipse fare clic su **Help** (?), quindi fare clic su **Install New Software** (Installa nuovo software), come illustrato nel diagramma seguente.

    ![Installare il Toolkit di Azure per Eclipse.][01]

1. Nella finestra di dialogo **Available Software** (Software disponibile), all'interno della casella di testo **Work with** (Usa), digitare **http://dl.microsoft.com/eclipse** e premere **Invio**.

1. Nel riquadro **Name** (Nome), selezionare **Azure Toolkit for Eclipse** (Toolkit di Azure per Eclipse) e deselezionare **Contact all update sites during install to find required software** (Contattare tutti i siti di aggiornamento durante l'installazione per trovare il software richiesto). Verrà visualizzata una schermata simile alla seguente:

    ![Installare il Toolkit di Azure per Eclipse.][02]

1. Espandendo **Azure Toolkit for Eclipse** (Toolkit di Azure per Eclipse), verranno visualizzate le voci seguenti:

    * **Plug-in di Application Insights per Java**: questo componente consente di usare servizi di analisi e di registrazione dei dati di telemetria di Azure per le applicazioni e le istanze del server.
    * **Azure Access Control Services Filter** (Filtro dei servizi di controllo accessi di Azure): questo componente supporta l'autenticazione degli utenti dell'applicazione ad ACS di Azure, offrendo scenari Single Sign-On ed esternalizzando la logica delle identità dall'applicazione.
    * **Azure Common Plugin** (Plug-in comune di Azure): questo componente offre le funzionalità comuni necessarie per gli altri componenti del toolkit.
    * **Azure Explorer for Eclipse** (Azure Explorer per Eclipse): questo componente offre le funzionalità comuni necessarie per gli altri componenti del toolkit.
    * **Azure Plugin for Eclipse with Java** (Plug-in di Azure per Eclipse con Java): questo componente supporta lo sviluppo di progetti per compilare, testare e distribuire le applicazioni Java per il cloud di Microsoft Azure in Eclipse e tramite riga di comando.
    * **Azure Web Apps Plugin with Java** (Plug-in di App Web di Azure con Java): questo componente supporta la distribuzione di applicazioni Web Java in contenitori App Web di Microsoft Azure.
    * **Microsoft JDBC Driver 4.2 for SQL Server** (Microsoft JDBC Driver 4.2 per SQL Server): questo componente fornisce l'API JDBC per SQL Server e il database SQL di Microsoft Azure per Java Platform Enterprise Edition 8.
    * **Package for Apache Qpid Client Libraries for JMS** (Pacchetto per le librerie client Apache Qpid per JMS): questo componente fornisce il componente client JSMS dal progetto Apache Qpid per consentire all'applicazione di usare la messaggistica basata su AMQP in Microsoft Azure.
    * **Package for Microsoft Azure Libraries for Java** (Pacchetto per le librerie di Microsoft Azure per Java): questo componente fornisce le API per accedere ai servizi di Microsoft Azure, ad esempio archiviazione, bus di servizio, runtime del servizio e così via.

1. Fare clic su **Avanti**. (Se si verificano ritardi insoliti durante l'installazione del toolkit, assicurarsi che **Contattare tutti i siti di aggiornamento durante l'installazione per trovare il software richiesto** sia deselezionato.)

1. Nella finestra di dialogo **Installare dettagli**, fare clic su **Avanti**.

    ![Verificare i dettagli di installazione][03]

1. Nella finestra di dialogo **Esaminare licenze**, rivedere le condizioni dei contratti di licenza. Se si accettano le condizioni dei contratti di licenza, fare clic su **Accetto le condizioni dei contratti di licenza** e quindi fare clic su **Fine**. (I passaggi rimanenti suppongono che le condizioni dei contratti di licenza siano state accettate. Se non si accettano le condizioni dei contratti di licenza, uscire dal processo di installazione.)

    ![Verificare le licenze][04]

    Eclipse scarica e installa i pacchetti necessari.

    ![Stato dell'installazione][05]

1. Se viene richiesto di riavviare Eclipse per completare l'installazione, fare clic su **Sì**.

    ![Riavviare il prompt dei comandi][06]

## Vedere anche

[Toolkit di Azure per Eclipse]

[Creare un'app Web Hello World per Azure in Eclipse]

[Novità di Azure Toolkit per Eclipse]

Per altre informazioni sull'uso di Azure con Java, visitare il [centro per sviluppatori Java di Azure].

<!-- URL List -->

[Toolkit di Azure per Eclipse]: ./azure-toolkit-for-eclipse.md
[centro per sviluppatori Java di Azure]: https://azure.microsoft.com/develop/java/
[Creare un'app Web Hello World per Azure in Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Novità di Azure Toolkit per Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md

<!-- IMG List -->

[01]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-01.png
[02]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-02.png
[03]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-03.png
[04]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-04.png
[05]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-05.png
[06]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-06.png

<!---HONumber=AcomDC_0525_2016-->