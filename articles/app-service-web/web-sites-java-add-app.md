---
title: Distribuire l&quot;applicazione di esempio nelle app Web di Servizio app di Azure
description: "In questa esercitazione viene illustrato come aggiungere una pagina o applicazione per l&quot;istanza di Azure applicazione servizio Web App è già configurato per l&quot;utilizzo di Java."
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 9b46528b-e2d0-4f26-b8d7-af94bd8c31ef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: bc23bf745f0e212de8ea652c8e57e4797013e3cc
ms.lasthandoff: 04/06/2017


---
# <a name="add-a-java-application-to-azure-app-service-web-apps"></a>Distribuire l'applicazione di esempio nelle app Web di Servizio app di Azure
Dopo aver inizializzato l'app Web Java nel [servizio app di Azure][Azure App Service] come indicato in [Creazione di un'app Web Java nel servizio app di Azure](web-sites-java-get-started.md), è possibile caricare l'applicazione inserendo il file WAR nella cartella **webapps**.

Il percorso della cartella **webapps** varia a seconda della configurazione del sito Web.

* Se si configura il sito Web usando Azure Marketplace, il percorso della cartella **webapps** sarà nel formato **d:\home\site\wwwroot\bin\application\_server\webapps**, dove **application\_server** è il nome del server applicazioni usato per l'istanza di App Web. 
* Se si configura il sito Web usando l'interfaccia utente di configurazione di Azure, il percorso della cartella **webapps** sarà nel formato **d:\home\site\wwwroot\webapps**. 

Si noti che è possibile usare il controllo del codice sorgente per caricare l'applicazione o le pagine Web, anche negli [scenari di integrazione continua](app-service-continuous-deployment.md). È anche possibile usare l'FTP per caricare le pagine web o l'applicazione. Per altre informazioni sulla distribuzione delle applicazioni tramite FTP, vedere [Distribuire l'app nel Servizio app di Azure].

Dopo avere caricato il file WAR nella cartella **webapps** , il server applicazioni Tomcat ne rileverà l'aggiunta e lo caricherà automaticamente. Si noti che se vengono copiati file (eccetto i file WAR) nella directory ROOT, è necessario riavviare il server applicazioni per utilizzarli. La funzionalità di caricamento automatico per i siti Web Java Tomcat in esecuzione in Azure si basa su un nuovo file WAR aggiunto o su nuovi file o nuove directory create nella cartella **webapps** . 

<a name="see-also"></a>

## <a name="see-also"></a>Vedere anche
Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure].

[application-insights-app-insights-java-get-started](../application-insights/app-insights-java-get-started.md)

<!-- URL List -->

[Centro per sviluppatori Java di Azure]: https://azure.microsoft.com/develop/java/
[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Distribuire l'app nel Servizio app di Azure]: ./web-sites-deploy.md

