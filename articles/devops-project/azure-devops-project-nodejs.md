---
title: Creare una pipeline CI/CD per un PWA con GatsbyJS e Azure DevOps Starter
description: DevOps Starter consente di iniziare a usare Azure in modo semplice. Con pochi rapidi passaggi, è possibile avviare un'app in un servizio di Azure a scelta.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: arob98
manager: angrobe
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: angrobe
ms.custom: mvc
ms.openlocfilehash: 7db4fa2a780a3a1f53ecd73a40c247583cb6a79a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233848"
---
# <a name="create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-starter"></a>Creare una pipeline CI/CD in Azure Pipelines per node. js con Azure DevOps Starter

Questa Guida introduttiva illustra come creare un'app Web NodeJS progressive (PWA) usando [GatsbyJS](https://www.gatsbyjs.org/) e l'esperienza semplificata per la creazione degli starter di Azure DevOps. Al termine, sarà disponibile una pipeline di integrazione continua e distribuzione continua (CI/CD) per l'app Web progressiva in Azure Pipelines. Azure DevOps Starter configura gli elementi necessari per lo sviluppo, la distribuzione e il monitoraggio.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Un'organizzazione di [Azure DevOps](https://azure.microsoft.com/services/devops/).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

DevOps Starter crea una pipeline CI/CD in Azure Pipelines. È possibile creare una nuova organizzazione di Azure DevOps o usare un'organizzazione esistente. DevOps Starter crea anche risorse di Azure nella sottoscrizione di Azure di propria scelta.

1. Accedere al [portale di Azure](https://portal.azure.com) e, nel riquadro sinistro, selezionare **Crea una risorsa**. 

   ![Creare una risorsa di Azure nel portale di Azure](_img/azure-devops-project-nodejs/create-azure-resource.png)

1. Nella casella di ricerca digitare **DevOps Starter**, quindi selezionare. Fare clic su **Aggiungi** per crearne uno nuovo.

    ![Dashboard Starter DevOps](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Selezionare un'applicazione di esempio e un servizio di Azure

1. Selezionare l'applicazione di esempio Node.js.   

    ![Selezionare l'esempio node. js](_img/azure-devops-project-nodejs/select-nodejs.png) 

1. Il Framework di esempio predefinito è **Express. js**. Modificare la selezione in **app node. js semplice** e quindi fare clic su **Avanti**. 

    ![Selezionare la semplice app node. js](_img/azure-devops-project-nodejs/select-nodejs-framework.png) 

1. Le destinazioni di distribuzione disponibili in questo passaggio sono determinate dal framework applicazione selezionato nel passaggio 2. In questo esempio, l' **app Web di Windows** è la destinazione di distribuzione predefinita. Lasciare **app Web per contenitori** impostare e selezionare **Avanti**.

    ![Selezionare la destinazione di distribuzione](_img/azure-devops-project-nodejs/select-web-server.png)

## <a name="configure-a-project-name-and-an-azure-subscription"></a>Configurare un nome di progetto e una sottoscrizione di Azure

1. Nel passaggio finale del flusso di lavoro di creazione iniziale di DevOps, assegnare un nome di progetto, selezionare una sottoscrizione di Azure e fare clic su **fine**.  

    ![Assegnare un nome di progetto e selezionare una sottoscrizione](_img/azure-devops-project-nodejs/assign-project-name.png)

1. Quando il progetto viene compilato e l'applicazione viene distribuita in Azure, viene visualizzata una pagina di riepilogo. Dopo un breve periodo di tempo, viene creato un progetto nell' [organizzazione di Azure DevOps](https://dev.azure.com/) che include un repository git, una bacheca Kanban, una pipeline di distribuzione, piani di test e gli artefatti richiesti dall'app.  

## <a name="managing-your-project"></a>Gestione del progetto

1. Passare a **tutte le risorse** e trovare lo starter DevOps. Selezionare l' **avvio di DevOps**.

    ![Dashboard di Azure DevOps nell'elenco delle risorse](_img/azure-devops-project-nodejs/devops-starter-list.png)

1. Si viene indirizzati a un dashboard che fornisce visibilità nella Home page del progetto, nel repository di codice, nella pipeline CI/CD e un collegamento all'app in esecuzione. Selezionare la **Home page del progetto** per visualizzare l'applicazione in **Azure DevOps** e in un'altra scheda del browser selezionare l' **endpoint dell'applicazione** per visualizzare l'app di esempio Live. Questo esempio verrà modificato in un secondo momento per usare il PWA generato da GatsbyJS.

    ![Dashboard di Azure DevOps](_img/azure-devops-project-nodejs/devops-projects-dashboard.png) 

1. Dal progetto DevOps di Azure è possibile invitare i membri del team a collaborare e stabilire una bacheca Kanban per iniziare a tenere traccia del lavoro. Per altre informazioni, vedere [qui](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops?view=azure-devops).

![Panoramica di Azure DevOps](_img/azure-devops-project-nodejs/azure-devops-overview.png)

## <a name="clone-the-repo-and-install-your-gatsby-pwa"></a>Clonare il repository e installare Gatsby PWA

DevOps Starter crea un repository git in Azure Repos o GitHub. Questo esempio ha creato un repository di Azure. Il passaggio successivo consiste nel clonare il repository e apportare modifiche.

1. Selezionare **repository** dal **progetto DevOps** , quindi fare clic su **clona**.  Esistono diversi meccanismi per clonare il repository Git sul desktop.  Scegli quello che soddisfa la tua esperienza di sviluppo.  

    ![Clonare il repository](_img/azure-devops-project-nodejs/clone-the-repo.png)

1. Dopo aver clonato il repository sul desktop, apportare alcune modifiche al modello iniziale. Per iniziare, installare l'interfaccia della riga di comando di GatsbyJS dal terminale.

   ```powershell
    npm install -g gatsby
   ```

1. Dal terminale passare alla radice del repository. Dovrebbe contenere tre cartelle simili a quelle riportate di seguito:

    ```powershell
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----        2/23/2020  10:42 PM                Application
    d-----        2/23/2020   3:05 PM                ArmTemplates
    d-----        2/23/2020   3:05 PM                Tests
    ```
    
1. Non sono desiderati tutti i file nella cartella dell'applicazione perché verranno sostituiti con un starter di Gatsby. Eseguire i comandi seguenti, in sequenza, per rimuoverli.
    
    ```powershell
    cp .\Application\Dockerfile .
    rmdir Application
    ```

1. Usare l'interfaccia della riga di comando di Gatsby per generare un esempio PWA. Eseguire `gatsby new` dal terminale per avviare la procedura guidata di PWA e `gatsby-starter-blog` Selezionare per il modello iniziale. Dovrebbe essere simile a questo esempio:

    ```powershell
    c:\myproject> gatsby new
    √ What is your project called? ... my-gatsby-project
    ? What starter would you like to use? » - Use arrow-keys. Return to submit.
        gatsby-starter-default
        gatsby-starter-hello-world
    >   gatsby-starter-blog
        (Use a different starter)
    ```
    
1. A questo punto è disponibile una `my-gatsby-project`cartella denominata. Rinominarlo `Application` e copiarvi `Dockerfile` il.
    
    ```powershell
    mv my-gatsby-project Application
    mv Dockerfile Application
    ```
    
1. Nell'editor preferito aprire il Dockerfile e modificare la prima riga da `FROM node:8` a. `FROM node:12` Questa modifica garantisce che il contenitore usi node. js versione 12. x invece della versione 8. x. GatsbyJS richiede versioni più moderne di node. js.

1. Successivamente, aprire il file Package. JSON nella cartella dell'applicazione e modificare il [campo script](https://docs.npmjs.com/files/package.json#scripts) per assicurarsi che i server di sviluppo e di produzione siano in ascolto su tutte le interfacce di rete disponibili, ad esempio 0.0.0.0, e la porta 80. Senza queste impostazioni, il servizio app contenitore non è in grado di instradare il traffico all'app node. js in esecuzione all'interno del contenitore. Il `scripts` campo deve essere simile a quello riportato di seguito. In particolare, si desidera modificare le `develop`destinazioni `serve`, e `start` dalle impostazioni predefinite.

    ```json
      "scripts": {
        "build": "gatsby build",
        "develop": "gatsby develop  -H 0.0.0.0 -p 80",
        "format": "prettier --write \"**/*.{js,jsx,json,md}\"",
        "start": "npm run serve",
        "serve": "npm run build && gatsby serve -H 0.0.0.0 -p 80",
        "clean": "gatsby clean",
        "test": "echo \"Write tests! -> https://gatsby.dev/unit-testing\" && exit 1"
      }
    ```
    
## <a name="edit-your-cicd-pipelines"></a>Modificare le pipeline CI/CD

1. Prima di eseguire il commit del codice nella sezione precedente, apportare alcune modifiche alle pipeline di compilazione e rilascio. Modificare la "pipeline di compilazione" e aggiornare l'attività node per l'uso di node. js versione 12. x. Impostare il campo **versione attività** su 1. x e il campo **versione** su 12. x.

    ![Aggiornare node. js a 12. x](_img/azure-devops-project-nodejs/build-pipeline-update-node.png)

1. In questa Guida introduttiva non vengono creati unit test ed è in corso la disabilitazione di questi passaggi nella pipeline di compilazione. Quando si scrivono i test, è possibile riabilitare questi passaggi. Fare clic con il pulsante destro del mouse per selezionare le attività **Installa dipendenze test** ed **Esegui unit test** e disabilitarle.

    ![Disabilitare i test di compilazione](_img/azure-devops-project-nodejs/disable-build-unittests.png)

1. Modificare la pipeline di rilascio.

    ![Modificare la pipeline di versione](_img/azure-devops-project-nodejs/edit-release-pipeline.png)

1. Come per la pipeline di compilazione, modificare l'attività node in modo da usare 12. x e disabilitare le due attività di test. Il rilascio dovrebbe essere simile a questo screenshot.

    ![Pipeline di rilascio completata](_img/azure-devops-project-nodejs/release-pipeline-complete.png)

1. Sul lato sinistro del browser passare al file **views/index.pug**.

1. Selezionare **Modifica** e quindi apportare una modifica al titolo h2.  Inserire, ad esempio, **iniziare subito a usare Azure DevOps Starter** o apportare altre modifiche.

1. Selezionare **Esegui commit** e quindi salvare le modifiche.

1. Nel browser passare al dashboard iniziale di DevOps.   
Verrà visualizzata una compilazione in corso. Le modifiche apportate vengono compilate e distribuite automaticamente tramite una pipeline CI/CD.

## <a name="commit-your-changes-and-examine-the-azure-cicd-pipeline"></a>Eseguire il commit delle modifiche ed esaminare la pipeline di integrazione continua/recapito continuo di Azure

Nei due passaggi precedenti è stato aggiunto un PWA generato da Gatsby al repository git e sono state modificate le pipeline per compilare e distribuire il codice. È possibile eseguire il commit del codice e osservare lo stato di avanzamento attraverso la pipeline di compilazione e rilascio.

1. Dalla radice del repository git del progetto in un terminale, eseguire i comandi seguenti per effettuare il push del codice nel progetto DevOps di Azure:

    ```powershell
    git add .
    git commit -m "My first Gatsby PWA"
    git push
    ```
    
1. Una compilazione viene avviata non appena `git push` viene completata. È possibile seguire lo stato di avanzamento dal **dashboard di Azure DevOps**.

3. Dopo alcuni minuti, le pipeline di compilazione e di versione verranno completate e l'app Web progressiva verrà distribuita in un contenitore. Fare clic sul collegamento **Endpoint applicazione** dal dashboard precedente per visualizzare un progetto di base Gatsby per i blog.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile eliminare il Servizio app di Azure e altre risorse correlate creati in precedenza. Usare la funzionalità di **eliminazione** nel dashboard iniziale di DevOps.

## <a name="next-steps"></a>Passaggi successivi

Quando si configura il processo di CI/CD, le pipeline di compilazione e versione vengono create automaticamente. È possibile modificare queste pipeline di compilazione e di versione in base alle esigenze del team. Per altre informazioni sulla pipeline CI/CD, vedere:

> [!div class="nextstepaction"]
> [Personalizzare il processo di distribuzione continua](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

