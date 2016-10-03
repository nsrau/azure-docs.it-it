<properties 
	pageTitle="Debug di un'App Web Java su Azure in IntelliJ | Microsoft Azure" 
	description="In questa esercitazione verrà mostrato come usare il Toolkit di Azure per IntelliJ per il debug di un'App Web Java in esecuzione su Azure." 
	services="app-service\web" 
	documentationCenter="java" 
	authors="selvasingh" 
	manager="wpickett" 
	editor=""/>  

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="09/20/2016" 
	ms.author="asirveda;robmcm"/>  

# Debug di un'App Web Java su Azure in IntelliJ

In questa esercitazione verrà mostrato come eseguire il debug di un'App Web Java in esecuzione su Azure usando il [Toolkit di Azure per IntelliJ]. Per praticità, verrà usato un esempio Java Server Page (JSP) molto semplice per questa esercitazione. Tuttavia i passaggi saranno simili a quelli per un servlet Java durante il debug su Azure.

Al termine di questa esercitazione, l'applicazione sarà simile a quanto illustrato di seguito durante il relativo debug in IntelliJ:

![][01]
 
## Prerequisiti

* Java Developer Kit (JDK) versione 1.8 o successiva.
* IntelliJ IDEA Ultimate Edition. È possibile scaricare il pacchetto all'indirizzo <https://www.jetbrains.com/idea/download/index.html>.
* Distribuzione di un server Web basato su Java o un server applicazioni, ad esempio Apache Tomcat o Jetty.
* Sottoscrizione di Azure, che può essere acquistata all'indirizzo <https://azure.microsoft.com/it-IT/free/> o <http://azure.microsoft.com/pricing/purchase-options/>.
* Azure Toolkit per IntelliJ. Per altre informazioni, vedere [Installazione di Azure Toolkit per IntelliJ].
* Un progetto Web dinamico creato e distribuito al Servizio app di Azure; ad esempio vedere [Creare un'app Web Hello World per Azure in IntelliJ].

## Per eseguire il debug di un'App Web Java su Azure

Per completare i passaggi contenuti in questa sezione, è possibile usare un progetto Web dinamico esistente già distribuito come App Web Java su Azure. È necessario scaricare un [progetto Web dinamico campione] e seguire i passaggi in [Creare un'app Web Hello World per Azure in IntelliJ] per distribuirlo su Azure.

1. Aprire il progetto App Web Java distribuito su Azure in IntelliJ.

1. Fare clic sul menu **Run** (Esegui), quindi su **Edit Configurations** (Modifica configurazioni).

    ![][02]

1. Quando si apre la finestra i dialogo **Run/Debug Configurations** (Esegui/Debug delle configurazioni):

    1. Selezionare **Azure Web App** (App Web di Azure).
    1. Fare clic sul simbolo **+** per aggiungere una nuova configurazione.
    1. Specificare il **Name** (Nome) della configurazione.
    1. Accettare i valori di default rimanenti suggeriti dal Toolkit di Azure, quindi fare clic su **OK**.

        ![][03]

1. Selezionare la configurazione di debug dell'App Web di Azure appena creata nella parte superiore destra del menu e fare clic su **Debug**.

    ![][04]

1. Quando viene chiesto **Enable remote debugging in the remote Web App now?** (Abilitare il debug remoto nell'App Web remota ora?), fare clic su **OK**.

1. Quando viene visualizzato **Your web app is now ready for remote debugging** (L'App Web è pronta per il debug remoto), fare clic su **OK**.

    ![][05]

1. Selezionare la configurazione di debug dell'App Web di Azure appena creata nella parte superiore destra del menu, quindi fare clic su **Debug**.

1. Si aprirà un prompt dei comandi Windows o una shell Unix e verrà stabilita la connessione richiesta per il debug. Sarà necessario attendere che la connessione all'App Web Java remota venga stabilita correttamente prima di procedere. Se il sistema operativo in uso è Windows, verrà visualizzato quanto illustrato di seguito:

    ![][06]

1. Inserire un punto di interruzione nella pagina JSP, quindi aprire l'URL per l'App Web Java in un browser:

    1. Aprire **Azure Explorer** in IntelliJ.
    1. Passare a **Web Apps** (App Web) e all'App Web Java di cui si desidera eseguire il debug.
    1. Fare clic con il pulsante destro del mouse sull'App Web, quindi su **Open in Browser** (Apri nel browser).
    1. IntelliJ entrerà in modalità di debug.

## Passaggi successivi

Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure].

Per altre informazioni sulla creazione di App Web di Azure, vedere la [Panoramica delle App Web].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->  

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Toolkit di Azure per IntelliJ]: ../azure-toolkit-for-intellij.md
[Installazione di Azure Toolkit per IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Creare un'app Web Hello World per Azure in IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[progetto Web dinamico campione]: http://go.microsoft.com/fwlink/?LinkId=817337

[Centro per sviluppatori Java di Azure]: https://azure.microsoft.com/develop/java/
[Panoramica delle App Web]: ./app-service-web-overview.md

<!-- IMG List -->  

[01]: ./media/app-service-web-debug-java-web-app-in-intellij/01-debug-java-web-app-in-intellij.png
[02]: ./media/app-service-web-debug-java-web-app-in-intellij/02-configure-intellij-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-intellij/03-debug-configuration.png
[04]: ./media/app-service-web-debug-java-web-app-in-intellij/04-select-debug.png
[05]: ./media/app-service-web-debug-java-web-app-in-intellij/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-intellij/06-windows-command-prompt-connection-successful-to-remote.png

<!---HONumber=AcomDC_0921_2016-->