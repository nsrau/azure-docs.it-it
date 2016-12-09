---
title: "Come usare il plug-in per app Web Azure con le funzionalità di integrazione continua Jenkins | Documentazione Microsoft"
description: "Descrive come usare il plug-in per app Web Azure con le funzionalità di integrazione continua Jenkins."
services: app-service\web
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
ms.assetid: be6c4e62-da76-44f6-bb00-464902734805
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 10/19/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c6f18c9b802d6fa18c4f50d3a537003eaaf25d14


---
# <a name="how-to-use-the-azure-web-app-plugin-with-jenkins-continuous-integration"></a>Come usare il plug-in per app Web Azure con le funzionalità di integrazione continua Jenkins
Il plug-in per app Web Azure per Jenkins facilita sia la creazione di app Web in Azure quando si eseguono compilazioni distribuite sia la distribuzione di un file WAR nell'app Web.

### <a name="prerequisites"></a>Prerequisiti
Prima di eseguire le procedure descritte in questo articolo, è necessario registrare e autorizzare l'applicazione client e quindi recuperare l'ID e il segreto client che verranno inviati ad Azure Active Directory durante l'autenticazione. Per altre informazioni su questi prerequisiti, vedere gli articoli seguenti:

* [Integrazione di applicazioni con Azure Active Directory][integrate-apps-with-AAD]
* [Registrare un'app client][register-client-app]

È anche necessario scaricare il file **azure-webapp-plugin.hpi** dall'URL seguente:

* [https://github.com/Microsoft/azure-webapp-plugin/tree/master/install](https://github.com/Microsoft/azure-webapp-plugin/tree/master/install)

## <a name="how-to-install-the-azure-web-app-plugin-for-jenkins"></a>Come installare il plug-in per app Web Azure per Jenkins
1. [Scaricare il file **azure-webapp-plugin.hpi** da GitHub][azure-webapp-plugin-install].
2. Accedere al dashboard di Jenkins.
3. Nel dashboard fare clic su **Manage Jenkins** (Gestisci Jenkins).
   
    ![Gestire Jenkins][jenkins-dashboard]
4. Nella pagina **Manage Jenkins** fare clic su **Manage Plugins**.
   
    ![Gestire i plug-in][manage-jenkins]
5. Fare clic sulla scheda **Advanced** (Avanzate) e quindi su **Browse** (Sfoglia) nella sezione **Upload Plugin** (Carica plug-in). Passare al percorso in cui è stato scaricato il file **azure-webapp-plugin.hpi** nella sezione **Prerequisiti** e dopo aver selezionato il file fare clic su **Upload** (Carica).
   
    ![Caricare il plug-in][upload-plugin]
6. Riavviare Jenkins, se necessario.

## <a name="configure-the-azure-web-app-plugin"></a>Configurare il plug-in per app Web Azure
1. Nel dashboard di Jenkins fare clic su un progetto.
   
    ![Selezionare il progetto][select-project]
2. Quando viene visualizzata la pagina del progetto, fare clic su **Configure** (Configura) nel menu a sinistra.
   
    ![Configurare il progetto][configure-project]
3. Nella sezione **Post-build Actions** (Azioni post-compilazione) fare clic sul menu a discesa **Add post-build action** (Aggiungi azione post-compilazione) e scegliere **Azure Webapp Configuration** (Configurazione app Web Azure). 
   
    ![Opzioni avanzate][advanced-options]
4. Quando viene visualizzata la sezione **Azure Webapp Configuration** (Configurazione app Web Azure), in **Azure Profile Configuration** (Configurazione profilo Azure) immettere le proprie informazioni nei campi **Subscription ID** (ID sottoscrizione), **Client ID** (ID client), **Client Secret** (Segreto client) e **OAuth 2.0 Token Endpoint** (Endpoint di token OAuth 2.0).
   
    ![Configurazione del profilo Azure][azure-profile-configuration]
5. Nella sezione **Webapp Configuration** (Configurazione app Web) immettere le proprie informazioni nei campi **Resource Group Name** (Nome gruppo di risorse), **Location** (Località), **Hosting Plan Name** (Nome piano di hosting), **Web App Name** (Nome app Web), **Sku Name** (Nome SKU), **Sku Capacity** (Capacità SKU) e **War File Path** (Percorso file WAR).
   
    ![Configurazione app Web][webapp-configuration]
6. Fare clic su **Save** (Salva) per salvare le impostazioni per il progetto.
   
    ![Salvare il progetto][save-project]
7. Fare clic su **Build Now** (Compila) nel menu a sinistra.
   
    ![Compilare il progetto][build-project]

> [!NOTE]
> Verrà creato un contenitore di app Web solo se non ne esiste già uno.
> 
> 

<a name="see-also"></a>

## <a name="see-also"></a>Vedere anche
Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure].

Per altre informazioni su questo plug-in per Jenkins, vedere il progetto relativo al [plug-in per app Web Azure] in GitHub.

<!-- URL List -->

[Centro per sviluppatori Java di Azure]: https://azure.microsoft.com/develop/java/
[integrate-apps-with-AAD]: http://msdn.microsoft.com/library/azure/dn132599.aspx
[register-client-app]: http://msdn.microsoft.com/dn877542.aspx
[plug-in per app Web Azure]: https://github.com/Microsoft/azure-webapp-plugin
[azure-webapp-plugin-install]: https://github.com/Microsoft/azure-webapp-plugin/tree/master/install

<!-- IMG List -->

[jenkins-dashboard]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/jenkins-dashboard.png
[manage-jenkins]:    ./media/app-service-web-azure-web-app-plugin-for-jenkins/manage-jenkins.png
[upload-plugin]:     ./media/app-service-web-azure-web-app-plugin-for-jenkins/upload-plugin.png
[select-project]:    ./media/app-service-web-azure-web-app-plugin-for-jenkins/select-project.png
[configure-project]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/configure-project.png
[advanced-options]:  ./media/app-service-web-azure-web-app-plugin-for-jenkins/advanced-options.png
[azure-profile-configuration]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/azure-profile-configuration.png
[build-project]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/build-project.png
[save-project]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/save-project.png
[webapp-configuration]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/webapp-configuration.png



<!--HONumber=Nov16_HO3-->


