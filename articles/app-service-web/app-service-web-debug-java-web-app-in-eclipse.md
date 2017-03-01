---
title: Debug di un&quot;app Web Java in Azure in Eclipse | Documentazione Microsoft
description: "In questa esercitazione verrà mostrato come usare il Toolkit di Azure per Eclipse per il debug di un&quot;App Web Java in esecuzione su Azure."
services: app-service\web
documentationcenter: java
author: selvasingh
manager: erikre
editor: 
ms.assetid: 321d2d19-9ce0-4165-8555-b6b15e671393
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: asirveda;robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: 8a4f22442d030dae602171555bafb3733d61ebd7
ms.lasthandoff: 01/05/2017


---
# <a name="debug-a-java-web-app-on-azure-in-eclipse"></a>Debug di un'App Web Java su Azure in Eclipse
Questa esercitazione illustra come eseguire il debug di un'app Web Java in esecuzione in Azure usando [Azure Toolkit for Eclipse]. Per praticità, verrà usato un esempio Java Server Page (JSP) molto semplice per questa esercitazione. Tuttavia i passaggi saranno simili a quelli per un servlet Java durante il debug su Azure.

Al termine di questa esercitazione, l'applicazione sarà simile a quanto illustrato di seguito durante il relativo debug in Eclipse:

![][01]

## <a name="prerequisites"></a>Prerequisiti
* Java Developer Kit (JDK) versione 1.8 o successiva.
* IDE Eclipse per sviluppatori Java EE, Indigo o versione successiva. È possibile scaricare il pacchetto all'indirizzo <http://www.eclipse.org/downloads/>.
* Distribuzione di un server Web basato su Java o un server applicazioni, ad esempio Apache Tomcat o Jetty.
* Una sottoscrizione di Azure, che può essere ottenuta all'indirizzo <https://azure.microsoft.com/it-it/free/> o <http://azure.microsoft.com/pricing/purchase-options/>.
* Azure Toolkit per Eclipse. Per altre informazioni, vedere [Installazione di Azure Toolkit per Eclipse].
* Un progetto Web dinamico creato e distribuito al Servizio app di Azure; ad esempio vedere [Creare un'app Web Hello World per Azure in Eclipse].

## <a name="to-debug-a-java-web-app-on-azure"></a>Per eseguire il debug di un'App Web Java su Azure
Per completare i passaggi contenuti in questa sezione, è possibile usare un progetto Web dinamico esistente già distribuito come app Web Java in Azure. È necessario scaricare un [progetto Web dinamico di esempio] e seguire i passaggi contenuti in [Creare un'app Web Hello World per Azure in Eclipse] per distribuirlo in Azure. 

1. Aprire Eclipse.
2. Configurare i timeout per il debug remoto:
   
   1. In Eclipse scegliere **Preferences** (Preferenze) dal menu **Windows**.
   2. Espandere il nodo **Java**, quindi selezionare **Debug**.
   3. Impostare entrambi i valori **Debugger timeout (ms)** (Timeout (ms) del debugger) e **Launch timeout (ms)** (Timeout (ms) dell'avvio) su `120000`.
      
       ![][02]
   4. Fare clic su **OK** per chiudere la finestra di dialogo **Preferences** (Preferenze).
3. Nella visualizzazione Project Explorer (Esplora progetti) di Eclipse fare clic con il pulsante destro del mouse sul progetto Web dinamico distribuito in Azure. Quando viene visualizzato il menu di scelta rapida, scegliere **Debug As** (Debug come), quindi fare clic su **Azure Web App** (App Web di Azure).
   
    ![][03]
4. Se questa è la prima volta che viene eseguito il debug del progetto Web dinamico, si aprirà la finestra di dialogo **Debug Configurations** (Debug delle configurazioni). È possibile accettare i valori predefiniti specificati dal Toolkit nella scheda **Connect** (Connetti). Nella scheda **Source** (Origine) fare clic su **Add** (Aggiungi), quindi su **Java project** (Progetto Java), selezionare **Dynamic Web Project** (Progetto Web dinamico), quindi fare clic su **OK**. Dopo aver completato questi passaggi, fare clic su **Debug**.
   
    ![][04]
5. Quando viene chiesto **Enable remote debugging in the remote Web App now?** (Abilitare il debug remoto nell'App Web remota ora?), fare clic su **OK**.
6. Quando viene visualizzato **Your web app is now ready for remote debugging** (L'App Web è pronta per il debug remoto), fare clic su **OK**.
   
    ![][05]
7. Quando viene nuovamente visualizzata la finestra di dialogo **Debug Configurations** (Configurazioni di debug), fare clic su **Debug**.
8. Si aprirà un prompt dei comandi Windows o una shell Unix e verrà stabilita la connessione richiesta per il debug. Sarà necessario attendere che la connessione all'App Web Java remota venga stabilita correttamente prima di procedere. Se il sistema operativo in uso è Windows, verrà visualizzato quanto illustrato di seguito.
   
    ![][06]
9. Inserire un punto di interruzione nella pagina JSP, quindi aprire l'URL per l'App Web Java in un browser:
   
   1. Aprire **Azure Explorer** in Eclipse.
   2. Passare a **Web Apps** (App Web) e all'App Web Java di cui si desidera eseguire il debug.
   3. Fare clic con il pulsante destro del mouse sull'App Web, quindi su **Open in Browser**(Apri nel browser).
   4. Eclipse entrerà in modalità di debug.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure].

Per altre informazioni sulla creazione di App Web di Azure, vedere la [Panoramica delle App Web].

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure Toolkit for Eclipse]: ../azure-toolkit-for-eclipse.md
[Installazione di Azure Toolkit per Eclipse]: ../azure-toolkit-for-eclipse-installation.md
[Creare un'app Web Hello World per Azure in Eclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[progetto Web dinamico di esempio]: http://go.microsoft.com/fwlink/?LinkId=817337

[Centro per sviluppatori Java di Azure]: https://azure.microsoft.com/develop/java/
[Panoramica delle App Web]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-eclipse/01-debug-java-web-app-in-eclipse.png
[02]: ./media/app-service-web-debug-java-web-app-in-eclipse/02-configure-eclipse-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-eclipse/03-debug-as.png
[04]: ./media/app-service-web-debug-java-web-app-in-eclipse/04-debug-configurations.png
[05]: ./media/app-service-web-debug-java-web-app-in-eclipse/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-eclipse/06-windows-command-prompt-connection-successful-to-remote.png

