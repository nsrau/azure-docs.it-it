<properties
	pageTitle="Creazione di un'app Web Java nel servizio app di Azure"
	description="Questa esercitazione illustra come distribuire un'app Web Java nel servizio app di Azure."
	services="app-service\web"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor="jimbe"/>
<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="hero-article"
	ms.date="06/03/2015"
	ms.author="robmcm"/>

# Creazione di un'app Web Java nel servizio app di Azure

In questa esercitazione verrà illustrato come creare un'app Web in Microsoft Azure con Java, utilizzando Azure Marketplace o l'interfaccia di configurazione del [servizio app per app Web](http://go.microsoft.com/fwlink/?LinkId=529714).

Se non si vuole usare nessuna di queste tecniche, ad esempio perché si preferisce personalizzare il proprio contenitore di applicazioni, vedere [Caricare un'app Web Java personalizzata in Azure](web-sites-java-custom-upload.md).

> [AZURE.NOTE]Per completare l'esercitazione, è necessario un account Microsoft Azure. Se non si dispone di un account, è possibile <a href="/it-it/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">attivare i benefici della sottoscrizione MSDN</a> oppure <a href="/it-it/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">iscriversi per ottenere una versione di valutazione gratuita</a>.

Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Creazione di un'app Web Java usando l'Azure Marketplace

Queste informazioni mostrano come utilizzare Azure Marketplace per selezionare un contenitore di applicazioni Java (Apache Tomcat o Jetty) per la propria app Web.

Di seguito viene illustrato l'aspetto di un'app Web creata utilizzando Tomcat da Azure Marketplace:

<!--todo:![Web app using Apache Tomcat](./media/web-sites-java-get-started/tomcat.png)-->

Di seguito viene illustrato l'aspetto di un'app Web creata utilizzando Jetty da Azure Marketplace:

<!--todo:![Web app using Jetty](./media/web-sites-java-get-started/jetty.png)-->

1. Accedere al [Portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715).
2. Fare clic su **Nuovo** nella parte inferiore sinistra della pagina.
3. Fare clic sul pannello **Web + Mobile**.
4. Fare clic su **Azure Marketplace** nella parte inferiore del pannello **Web + Mobile**.
5. Fare clic su **Web**.
6. La parte superiore della pagina **Web** contiene una casella di testo di ricerca. In questa casella di testo, digitare il server dell'applicazione Java desiderato, ad esempio, **Apache Tomcat** o **Jetty**.
4. Selezionare il server dell'applicazione Java desiderato.
5. Fare clic su **Create**.
6. Specificare il nome di URL.
6. Scegliere un'area, ad esempio **West US**.
7. Fare clic su **Create**.

L'app Web verrà creata dopo alcuni istanti. Per visualizzare l'app Web, all'interno del portale di gestione di Azure, nel pannello **App Web**, fare clic sull'app Web appena creata, quindi selezionare l'URL dell'app Web.

Ora che è stata creata un'app Web con un contenitore per le app, vedere la sezione **Passaggi successivi** per informazioni sul caricamento dell'applicazione nell'app Web.

## Creazione di un'app Web Java utilizzando l'interfaccia utente di configurazione di Azure

Queste informazioni mostrano come utilizzare l'interfaccia utente di configurazione di Azure per selezionare un contenitore di applicazioni Java (Apache Tomcat o Jetty) per la propria app Web.

1. Accedere al portale di gestione di Azure.
2. Fare clic su **Nuovo** nella parte inferiore sinistra della pagina.
3. Fare clic sul pannello **Web + Mobile**.
4. Fare clic su **Azure Marketplace** nella parte inferiore del pannello **Web + Mobile**.
5. Fare clic su **Web**.
6. Fare clic su **App Web**.
7. Fare clic su **Create**.
8. Specificare il nome di URL.
9. Scegliere un'area, ad esempio **West US**.
10. Fare clic su **Create**.
11. Quando l'app Web è stata creata, fare clic su **Tutte le impostazioni**.
12. Fare clic su **Impostazioni applicazione**.
13. Fare clic sulla versione di Java desiderata.
14. Vengono visualizzate le opzioni per il contenitore Web, ad esempio Tomcat e Jetty. Selezionare il contenitore **Web desiderato**.
15. Fare clic su **Save**.

Dopo alcuni secondi, l'app Web diventerà basata su Java. Per verificare che è basata su Java, fare clic sull'URL dell'app Web. Tenere presente che nella pagina verrà visualizzato del testo che indica che la nuova app Web è un'app Web basata su Java.

Ora che è stata creata un'app Web con un contenitore per le app, vedere la sezione **Passaggi successivi** per informazioni sul caricamento dell'applicazione nell'app Web.

## Passaggi successivi

A questo punto si disporrà di un server applicazioni Java in esecuzione come app Web Java in Azure. Per aggiungere la propria applicazione o pagina Web, vedere [Aggiunta di un'applicazione o di una pagina Web a un'app Web Java](web-sites-java-add-app.md).

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una Guida per la modifica del portale precedente per il nuovo portale, vedere: [riferimento per lo spostamento tra il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=July15_HO3-->