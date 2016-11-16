---
title: Creare un&quot;App Web Java nel Servizio app di Azure | Microsoft Docs
description: Questa esercitazione illustra come distribuire un&quot;app Web Java nel servizio app di Azure.
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: d6e73cc3-8b71-4742-a197-3edeabc6a289
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: get-started-article
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: acacfbead6cf0d68ccfeb5e818a2b04f2be9b902


---
# <a name="create-a-java-web-app-in-azure-app-service"></a>Creazione di un'app Web Java nel servizio app di Azure
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Questa esercitazione illustra come creare un'[App Web Java nel Servizio app di Azure] tramite il [portale di Azure]. Il portale di Azure è un'interfaccia Web che si può usare per gestire le risorse di Azure.

> [!NOTE]
> Per completare l'esercitazione, è necessario un account Microsoft Azure. Se non si dispone di un account, è possibile [attivare i benefici della sottoscrizione Visual Studio] oppure [iscriversi per ottenere una versione di valutazione gratuita].
> 
> Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, passare alla pagina [Prova il servizio app]. In questa pagina è possibile creare immediatamente un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
> 
> 

## <a name="java-application-options"></a>Opzioni dell'applicazione Java
Si possono usare diversi modi per configurare un'applicazione Java in un'app Web del servizio app. 

1. Creare un'app e quindi configurare l'opzione **Impostazioni dell'applicazione**.
   
    Il servizio App fornisce diverse versioni di Tomcat e Jetty con una configurazione predefinita. Se l'applicazione che si ospiterà funziona con una delle versioni predefinite, questo metodo di configurazione di un contenitore Web è il più semplice ed è l'ideale quando si vuole solo caricare un file WAR in un contenitore Web. Per questo metodo, creare un'app nel portale di Azure e quindi passare al pannello **Impostazioni applicazione** per l'app per scegliere la versione di Java con il contenitore Web Java preferito. Quando si usa questo metodo, sia Java che il contenitore Web vengono eseguiti da Programmi. Gli altri metodi inseriscono il contenitore Web e potenzialmente la JVM sul disco usandone lo spazio. Quando si usa questo modello, non si ha accesso per modificare i file in questa parte del file system. Non è quindi possibile eseguire operazioni come configurare il file *server.xml* o inserire file di libreria nella cartella */lib*. Per altre informazioni, vedere la sezione [Creare e configurare un'app Web Java](#appsettings) più avanti in questa esercitazione.
2. Usare un modello da Azure Marketplace.
   
    In Azure Marketplace sono disponibili modelli per i creare e configurare automaticamente app Web Java con Tomcat o contenitori Web Jetty. I contenitori Web creati con i modelli sono configurabili. Per altre informazioni, vedere la sezione [Usare un modello Java da Azure Marketplace](#marketplace) di questa esercitazione.
3. Creare un'app e quindi copiare e modificare manualmente i file di configurazione 
   
    È possibile ospitare un'applicazione Java personalizzata che non viene distribuita in uno dei contenitori Web forniti dal servizio App. ad esempio:
   
   * L'applicazione Java richiede una versione di Tomcat o Jetty che non è direttamente supportata dal servizio app o fornita nella raccolta.
   * L'applicazione Java accetta richieste HTTP e non viene distribuita come file WAR in un contenitore Web preesistente.
   * Si vuole configurare manualmente da zero il contenitore Web. 
   * Si vuole usare una versione di Java non supportata nel servizio app e caricarla manualmente.
     
     Per casi come questi è possibile creare un'app tramite il portale di Azure e quindi mettere a disposizione manualmente i file di runtime appropriati. In questo caso, i file verranno conteggiati a fronte delle quote di spazio di archiviazione per il piano di servizio app. Per altre informazioni, vedere [Caricare un'app Web Java personalizzata in Azure].

## <a name="a-nameportala-create-and-configure-a-java-web-app"></a><a name="portal"></a> Creare e configurare un'app Web Java
Questa sezione illustra come creare un'app Web e configurarla per l'uso del pannello **Impostazioni applicazione** nel portale in Java.

1. Accedere al [portale di Azure].
2. Fare clic su **Nuovo > Web e dispositivi mobili > App Web**.
   
    ![Nuova app Web][newwebapp]
3. Immettere un nome per l'app Web nella casella **App Web** .
   
    Il nome deve essere univoco nel dominio azurewebsites.net perché l'URL dell'app Web sarà {nome}.azurewebsites.net. Se il nome immesso non è univoco, nella casella di testo verrà visualizzato un punto esclamativo rosso.
4. Selezionare un **Gruppo di risorse** o crearne uno nuovo.
   
    Per altre informazioni sui gruppi di risorse, vedere [Uso del portale di Azure per gestire le risorse di Azure].
5. Selezionare un **Piano di servizio app/Posizione** o crearne uno nuovo.
   
    Per altre informazioni sui piani di servizio app, vedere [Panoramica dei piani di servizio app di Azure].
6. Fare clic su **Crea**.
   
    ![Crea app Web][newwebapp2]
7. Una volta creata l'App Web, fare clic su **App Web > {App Web personale}**.
   
    ![Selezione dell'app Web][selectwebapp]
8. Nel pannello **App Web** fare clic su **Impostazioni**.
9. Fare clic su **Impostazioni applicazione**.
10. Scegliere la **Versione Java**desiderata. 
11. Scegliere la **Versione secondaria Java**desiderata. Se si seleziona **Più recente**, l'app userà la versione secondaria più recente disponibile nel servizio app per quella versione Java principale. L'elemento **Più recente** è univoco per le app Java create dalle **impostazioni dell'applicazione**. Se si crea l'app Java dalla raccolta, si dovranno gestire il proprio contenitore e le modifiche alla JVM. 
12. Scegliere il **Contenitore Web**desiderato. Se si seleziona un nome di contenitore che inizia con **Più recente**, verrà mantenuta la versione dell'app che corrisponde alla versione più recente della versione principale del contenitore Web disponibile nel servizio app. 
    
    ![Versioni del contenitore Web][versions]
13. Fare clic su **Save**.
    
    Dopo alcuni secondi, l'app Web risulterà basata su Java e sarà configurata per l'uso del contenitore Web selezionato.
14. Fare clic su **App Web > {nuova app Web}**.
15. Fare clic su **URL** per passare al nuovo sito.
    
    La pagina Web conferma che è stata creata un'app Web basata su Java.

## <a name="a-namemarketplacea-use-a-java-template-from-the-azure-marketplace"></a><a name="marketplace"></a> Usare un modello Java da Azure Marketplace
Questa sezione illustra come usare Azure Marketplace per creare un'app Web Java. Lo stesso flusso generale può essere usato anche per creare un'app per dispositivi mobili o un'app per le API basata su Java. 

1. Accedere al [portale di Azure]
2. Fare clic su **Nuovo > Marketplace**.
   
    ![Nuovo Marketplace][newmarketplace]
3. Fare clic su **Web e dispositivi mobili**.
   
    Potrebbe essere necessario scorrere verso sinistra per vedere il pannello **Marketplace** dove è possibile selezionare **Web e dispositivi mobili**.
4. Nella casella di testo di ricerca immettere il nome di un server applicazioni Java, ad esempio **Apache Tomcat** o **Jetty** e quindi premere INVIO.
5. Nei risultati della ricerca fare clic sul server applicazioni Java.
   
    ![Jetty per dispositivi mobili Web][webmobilejetty]
6. Nel primo pannello **Apache Tomcat** o **Jetty** fare clic su **Crea**.
   
    ![Pannello del portale di Jetty][jettyblade]
7. Nel pannello **Apache Tomcat** o **Jetty** successivo immettere un nome per l'App Web nella casella **App Web**.
   
    Il nome deve essere univoco nel dominio azurewebsites.net perché l'URL dell'app Web sarà {nome}.azurewebsites.net. Se il nome immesso non è univoco, nella casella di testo verrà visualizzato un punto esclamativo rosso.
8. Selezionare un **Gruppo di risorse** o crearne uno nuovo.
   
    Per altre informazioni sui gruppi di risorse, vedere [Uso del portale di Azure per gestire le risorse di Azure].
9. Selezionare un **Piano di servizio app/Posizione** o crearne uno nuovo.
   
    Per altre informazioni sui piani di servizio app, vedere [Panoramica dei piani di servizio app di Azure].
10. Fare clic su **Crea**.
    
    ![Creazione nel portale di Jetty][jettyportalcreate2]
    
    In meno di un minuto Azure completa la creazione della nuova app Web.
11. Fare clic su **App Web > {nuova app Web}**.
12. Fare clic su **URL** per passare al nuovo sito.
    
    ![URL Jetty][jettyurl]
    
    Tomcat viene fornito con un set di pagine predefinito. Se si è scelto Tomcat, verrà visualizzata una pagina simile all'esempio seguente.
    
    ![App Web che usa Apache Tomcat][tomcat]
    
    Se si è scelto Jetty, verrà visualizzata una pagina simile all'esempio seguente. Jetty non include un set di pagine predefinito, quindi viene riutilizzato lo stesso JSP usato per un sito vuoto Java.
    
    ![App Web che usa Jetty][jetty]

Una volta creata un'app Web con un contenitore di app, vedere la sezione [Passaggi successivi](#next-steps) per informazioni su come caricare l'applicazione nell'app Web.

## <a name="next-steps"></a>Passaggi successivi
A questo punto è disponibile un server applicazioni Java in esecuzione nell'app Web nel servizio app di Azure. Per distribuire il codice personalizzato all'app Web, vedere [Aggiungere un'applicazione o una pagina Web a un'app Web Java].

Per altre informazioni sullo sviluppo di applicazioni Java in Azure, visitare la pagina [Java Developer Center].

<!-- URL List -->

[Aggiungere un'applicazione o una pagina Web a un'app Web Java]: ./web-sites-java-add-app.md
[Panoramica dei piani di servizio app di Azure]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[portale di Azure]: https://portal.azure.com/
[attivare i vantaggi della sottoscrizione di Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
[iscriversi per ottenere una versione di valutazione gratuita]: http://go.microsoft.com/fwlink/?LinkId=623901
[Prova il servizio app]: http://go.microsoft.com/fwlink/?LinkId=523751
[App Web nel Servizio app di Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
[Java Developer Center]: /develop/java/
[Uso del portale di Azure per gestire le risorse di Azure]: ../azure-portal/resource-group-portal.md
[Caricare un'app Web Java personalizzata in Azure]: ./web-sites-java-custom-upload.md

<!-- IMG List -->

[newwebapp]: ./media/web-sites-java-get-started/newwebapp.png
[newwebapp2]: ./media/web-sites-java-get-started/newwebapp2.png
[selectwebapp]: ./media/web-sites-java-get-started/selectwebapp.png
[versions]: ./media/web-sites-java-get-started/versions.png
[newmarketplace]: ./media/web-sites-java-get-started/newmarketplace.png
[webmobilejetty]: ./media/web-sites-java-get-started/webmobilejetty.png
[jettyblade]: ./media/web-sites-java-get-started/jettyblade.png
[jettyportalcreate2]: ./media/web-sites-java-get-started/jettyportalcreate2.png
[jettyurl]: ./media/web-sites-java-get-started/jettyurl.png
[tomcat]: ./media/web-sites-java-get-started/tomcat.png
[jetty]: ./media/web-sites-java-get-started/jetty.png



<!--HONumber=Nov16_HO2-->


