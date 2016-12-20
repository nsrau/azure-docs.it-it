---
title: Plug-in del servizio contenitore di Azure per Jenkins | Documentazione Microsoft
description: Distribuire un contenitore Docker in un cluster del servizio contenitore di Azure usando il plug-in del servizio contenitore di Azure per Jenkins.
services: container-service
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
tags: azure-container-service, jenkins
keywords: Azure, contenitori, Jenkins
ms.assetid: dd719bda-6d1f-452b-a918-9a3aa9302107
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/19/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 435262867e8ead23d1453e8a2171f1c215fc5205


---
# <a name="azure-container-service-plugin-for-jenkins"></a>Plug-in del servizio contenitore di Azure per Jenkins
Il plug-in del servizio contenitore di Azure per Jenkins consente di semplificare il processo di distribuzione di contenitori [Docker] usando [Marathon] in un cluster del servizio contenitore di Azure che esegue un sistema di elaborazione distribuita come [DC/OS (Datacenter Operating System) di Mesosphere][mesosphere] o [Apache Mesos][mesos].

Questa esercitazione illustrerà come installare il plug-in del servizio contenitore di Azure per Jenkins e come configurare un progetto Jenkins che creerà un servizio contenitore di Azure con Marathon e un agente di orchestrazione DC/OS e userà Marathon per distribuire un contenitore Docker nel cluster. Se il cluster del servizio contenitore di Azure non esiste, verrà creato da Jenkins, che userà Marathon per distribuire il contenitore Docker nel cluster al momento della creazione. In caso contrario, Jenkins distribuirà il contenitore Docker nel cluster del servizio contenitore di Azure esistente usando Marathon.

Per altre informazioni sul servizio contenitore di Azure, vedere l'articolo [Introduzione al servizio contenitore di Azure][acs-intro].

### <a name="prerequisites"></a>Prerequisiti
Prima di eseguire le procedure descritte in questo articolo, è necessario registrare e autorizzare l'applicazione client e quindi recuperare l'ID e il segreto client che verranno inviati ad Azure Active Directory durante l'autenticazione. Per altre informazioni su questi prerequisiti, vedere gli articoli seguenti:

* [Integrazione di applicazioni con Azure Active Directory][integrate-apps-with-AAD]
* [Registrare un'app client][register-client-app]

È anche necessario scaricare il file **azure-acs-plugin.hpi** dall'URL seguente:

* [https://github.com/Microsoft/azure-acs-plugin/tree/master/install](https://github.com/Microsoft/azure-acs-plugin/tree/master/install)

## <a name="how-to-install-the-azure-container-service-plugin-for-jenkins"></a>Come installare il plug-in del servizio contenitore di Azure per Jenkins
1. [Scaricare il file **azure-acs-plugin.hpi** da GitHub][azure-acs-plugin-install].
2. Accedere al dashboard di Jenkins.
3. Nel dashboard fare clic su **Manage Jenkins** (Gestisci Jenkins).
   
    ![Gestire Jenkins][jenkins-dashboard]
4. Nella pagina **Manage Jenkins** fare clic su **Manage Plugins**.
   
    ![Gestire i plug-in][manage-jenkins]
5. Fare clic sulla scheda **Advanced** (Avanzate) e quindi su **Browse** (Sfoglia) nella sezione **Upload Plugin** (Carica plug-in). Passare al percorso in cui è stato scaricato il file **azure-acs-plugin.hpi** nella sezione **Prerequisiti** e dopo aver selezionato il file fare clic su **Upload** (Carica).
   
    ![Caricare il plug-in][upload-plugin]
6. Riavviare Jenkins, se necessario.

## <a name="configure-the-azure-container-service-plugin"></a>Configurare il plug-in del servizio contenitore di Azure
1. Nel dashboard di Jenkins fare clic su un progetto.
   
    ![Selezionare il progetto][select-project]
2. Quando viene visualizzata la pagina del progetto, fare clic su **Configure** (Configura) nel menu a sinistra.
   
    ![Configurare il progetto][configure-project]
3. Nella sezione **Post-build Actions** (Azioni post-compilazione) fare clic sul menu a discesa **Add post-build action** (Aggiungi azione post-compilazione) e scegliere **Azure Container Service** (Servizio contenitore di Azure). 
   
    ![Opzioni avanzate][advanced-options]
4. Quando viene visualizzata la sezione **Azure Container Service** (Servizio contenitore di Azure), immettere le informazioni relative a ID sottoscrizione, ID client, segreto client ed endpoint di token OAuth 2.0.
   
    ![Configurazione del servizio contenitore di Azure][azure-container-service-config]
5. Nella sezione **ACS Profile Configuration** (Configurazione profilo servizio contenitore di Azure) immettere area, prefisso del nome DNS, numero di agenti, dimensioni di VM degli agenti, nome utente dell'amministratore, numero di master e chiave pubblica SSH RSA.
   
    ![Configurazione del profilo del servizio contenitore di Azure][acs-profile-configuration]
6. Nella sezione **Marathon Profile Configuration** (Configurazione profilo Marathon) immettere il percorso del file config Marathon e il percorso e la password del file privato SSH RSA.
   
    ![Configurazione del profilo Marathon][marathon-profile-configuration]
7. Fare clic su **Save** (Salva) per salvare le impostazioni per il progetto.
   
    ![Salvare il progetto][save-project]
8. Fare clic su **Build Now** (Compila) nel menu a sinistra.
   
    ![Compilare il progetto][build-project]

Al termine della compilazione, nella console delle compilazioni saranno disponibili i log.

> [!NOTE]
> Se il cluster del servizio contenitore di Azure non esiste, verrà creato da Jenkins, che userà Marathon per distribuire il contenitore Docker nel cluster al momento della creazione. In caso contrario, Jenkins distribuirà il contenitore Docker nel cluster del servizio contenitore di Azure esistente usando Marathon.
> 
> 

<a name="see-also"></a>

## <a name="see-also"></a>Vedere anche
Per altre informazioni sull'uso di cluster DC/OS e del servizio contenitore di Azure, vedere gli articoli seguenti:

* [Introduzione al servizio contenitore di Azure][acs-intro]
* [Distribuire un cluster del servizio contenitore di Azure][acs-deploy]
* [Connettersi a un cluster del servizio contenitore di Azure][acs-connect]
* [Gestione di contenitori tramite l'interfaccia utente Web][acs-webui-management]

Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure].

<!-- URL List -->

[azure-acs-plugin-install]: https://github.com/Microsoft/azure-acs-plugin/tree/master/install
[acs-intro]: ./container-service-intro.md
[acs-deploy]: ./container-service-deployment.md
[acs-connect]: ./container-service-connect.md
[acs-webui-management]: ./container-service-mesos-marathon-ui.md
[integrate-apps-with-AAD]: http://msdn.microsoft.com/library/azure/dn132599.aspx
[register-client-app]: http://msdn.microsoft.com/dn877542.aspx

[Marathon]: https://mesosphere.github.io/marathon/
[Docker]: http://docker.io/
[mesosphere]: https://mesosphere.com/products/
[mesos]: https://mesos.apache.org/

[Centro per sviluppatori Java di Azure]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[jenkins-dashboard]: ./media/container-service-plugin-for-jenkins/jenkins-dashboard.png
[manage-jenkins]: ./media/container-service-plugin-for-jenkins/manage-jenkins.png
[search-plugins]: ./media/container-service-plugin-for-jenkins/search-for-azure-plugin.png
[install-plugin]: ./media/container-service-plugin-for-jenkins/install-plugin.png
[select-project]: ./media/container-service-plugin-for-jenkins/select-project.png
[configure-project]: ./media/container-service-plugin-for-jenkins/configure-project.png
[advanced-options]: ./media/container-service-plugin-for-jenkins/advanced-options.png
[azure-container-service-config]: ./media/container-service-plugin-for-jenkins/azure-container-service-configuration.png
[acs-profile-configuration]: ./media/container-service-plugin-for-jenkins/acs-profile-configuration.png
[marathon-profile-configuration]: ./media/container-service-plugin-for-jenkins/marathon-profile-configuration.png
[save-project]: ./media/container-service-plugin-for-jenkins/save-project.png
[build-project]: ./media/container-service-plugin-for-jenkins/build-project.png
[upload-plugin]: ./media/container-service-plugin-for-jenkins/upload-plugin.png



<!--HONumber=Nov16_HO3-->


