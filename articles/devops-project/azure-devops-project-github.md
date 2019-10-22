---
title: 'Esercitazione: Creare una pipeline CI/CD per il codice esistente usando Azure DevOps Projects'
description: Azure DevOps Projects permette di iniziare a usare Azure velocemente. DevOps Projects consente di usare il proprio codice e repository di GitHub per avviare un'app in un servizio di Azure di propria scelta in pochi rapidi passaggi.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/27/2019
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 4a48b5c413f6dfb58a1ba3f896ded49250cde306
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286266"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Esercitazione: Creare una pipeline CI/CD per il codice esistente usando Azure DevOps Projects

Azure DevOps Projects offre un'esperienza semplificata in cui è possibile usare il codice esistente e il repository Git oppure scegliere un'applicazione di esempio per creare una pipeline di integrazione continua (CI, Continuous Integration) e recapito continuo (CD, Continuous Delivery) in Azure.

Si apprenderà come:

> [!div class="checklist"]
> * Usare DevOps Projects per creare una pipeline CI/CD
> * Configurare l'accesso al repository GitHub e scegliere un framework
> * Configurare Azure DevOps e una sottoscrizione di Azure 
> * Eseguire il commit delle modifiche in GitHub e la distribuzione automatica in Azure
> * Esaminare la pipeline CI/CD di Azure Pipelines
> * Pulire le risorse

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. È possibile ottenerne una gratuita tramite [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Accedere a GitHub o a un repository Git esterno contenente codice .NET, Java, PHP, Node, Python o Web statico.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Azure DevOps Projects crea una pipeline CI/CD in Azure Pipelines. È possibile creare una nuova organizzazione di Azure DevOps o usare un'organizzazione esistente. Azure DevOps Projects crea anche risorse di Azure nella sottoscrizione di Azure di propria scelta.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel riquadro di spostamento sinistro selezionare **+ Crea una risorsa**, quindi cercare **DevOps Projects** e selezionare **Crea**.

   ![Dashboard di DevOps Projects](_img/azure-devops-project-github/azuredashboard.png)

3. Selezionare **Usa il codice personale** e quindi **Avanti**.

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>Configurare l'accesso al repository GitHub e scegliere un framework

1. Selezionare **GitHub** o un repository di codice **Git** esterno. Per questa esercitazione, selezionare **GitHub**. La prima volta che si completa questo passaggio, può essere necessario eseguire l'autenticazione con GitHub, per consentire ad Azure l'accesso al repository GitHub.

2. Infine, selezionare **Repository** e **Ramo**, quindi **Avanti**.

3. Se si usa un contenitore Docker, impostare **App inclusa in Docker** su **SÌ**. Per questa esercitazione, lasciare **NO** e selezionare **Avanti**. Per altre informazioni sull'uso dei contenitori Docker, passare il puntatore sull'icona **i**.

   ![.NET Framework](_img/azure-devops-project-github/appframework.png)

4. Negli elenchi a discesa scegliere il **runtime** e il **framework** dell'applicazione, quindi selezionare **Avanti**. Il framework dell'applicazione che si sceglie determina il tipo di destinazione della distribuzione del servizio di Azure disponibile.

5. Selezionare il **servizio di Azure** in cui distribuire l'applicazione, quindi selezionare **Avanti**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurare Azure DevOps e una sottoscrizione di Azure

1. Immettere il **nome del progetto**.

2. Creare una nuova **organizzazione Azure DevOps** gratuita o scegliere un'organizzazione esistente dall'elenco a discesa.

3. Selezionare la **sottoscrizione di Azure** e immettere un nome per l'**app Web** o accettare quello predefinito. Selezionare una **località**, quindi **Fatto**. Dopo alcuni minuti, nel portale di Azure verrà visualizzata la pagina Panoramica della distribuzione di DevOps Project.

4. Selezionare **Vai alla risorsa** per visualizzare il dashboard di DevOps Projects. Nell'angolo superiore destro aggiungere il **progetto** al dashboard, in modo da potervi accedere rapidamente. Azure DevOps Projects configura automaticamente un trigger di compilazione e di versione di integrazione continua. Il codice rimane nel repository GitHub o in un altro repository esterno. Viene configurata un'app di esempio in un repository nell'**organizzazione Azure DevOps**. Viene eseguita una compilazione e l'app viene distribuita in Azure.

   ![Visualizzazione dashboard di DevOps Projects](_img/azure-devops-project-github/projectsdashboard.png)

5. Il dashboard fornisce visibilità su repository di codice, pipeline CI/CD e l'app in Azure. A destra, in Risorse di Azure, selezionare **Sfoglia** per visualizzare l'app in esecuzione.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Eseguire il commit delle modifiche in GitHub e la distribuzione automatica in Azure

A questo punto, si è pronti per collaborare con un team a un'app usando un processo di CI/CD che distribuisce automaticamente il lavoro più recente nel sito Web. Ogni modifica al repository GitHub avvia una compilazione in DevOps di Azure e una pipeline CD esegue una distribuzione in Azure.

1. Nel dashboard di DevOps Projects selezionare **Repository**. Il repository GitHub si apre in una nuova scheda del browser. Apportare una modifica all'applicazione, quindi fare clic su **Commit changes** (Esegui il commit delle modifiche).

2. Dopo alcuni istanti, viene avviata una compilazione in Azure Pipelines. È possibile monitorare lo stato della compilazione nel dashboard di DevOps Projects oppure nell'organizzazione di Azure DevOps selezionando la scheda **Pipeline di compilazione** nel dashboard del progetto.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Esaminare la pipeline CI/CD di Azure Pipelines

Azure DevOps Projects configura automaticamente una pipeline CI/CD in Azure Pipelines. Esplorare e personalizzare la pipeline in base alle esigenze. Per acquisire familiarità con le pipeline di compilazione e di versione eseguire questa procedura:

1. Nel dashboard di DevOps Projects selezionare **Pipeline di compilazione**.

2. Quando si apre la pagina **Azure Pipelines** verrà visualizzata la cronologia delle compilazioni più recenti e lo stato di ognuna.

   ![Compilazioni delle pipeline di Azure DevOps](_img/azure-devops-project-github/pipelinesbuildpage.png)

3. Nell'angolo in alto a destra della pagina **Compilazioni** sono visualizzate le opzioni **Modifica** per modificare la compilazione corrente, **Coda** per inserire nella coda una nuova compilazione e i puntini di sospensione ( **&#8942;** ) per aprire un menu con altre opzioni. Selezionare **Modifica**.

4. La compilazione esegue diverse attività, ad esempio il recupero delle origini dal repository, il ripristino delle dipendenze e la pubblicazione degli output per le distribuzioni. A destra sotto a **Nome** sostituire il nome della pipeline di compilazione con qualcosa di più descrittivo. Selezionare **Salva e accoda**, quindi **Salva**, lasciare un commento e quindi selezionare di nuovo **Salva**.

   ![Pagina Compilazioni di Azure DevOps](_img/azure-devops-project-github/buildpage.png)

5. Per vedere un audit trail delle modifiche recenti epr la compilazione, selezionare la scheda **Cronologia**. Azure DevOps tiene traccia di tutte le modifiche apportate alla pipeline di compilazione e consente di confrontare le versioni.

6. Selezionare la scheda **Trigger**. Azure DevOps Projects crea automaticamente un trigger di CI con alcune impostazioni predefinite. I trigger come **Abilita l'integrazione continua** possono essere impostati per eseguire una compilazione ogni volta che viene eseguito il commit di una modifica del codice oppure per pianificare l'esecuzione delle compilazioni in specifici orari.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, è possibile eliminare il servizio app Azure e le risorse correlate create in questa esercitazione. A tale scopo, usare la funzionalità **Elimina** nel dashboard di DevOps Projects.

## <a name="next-steps"></a>Passaggi successivi

Quando è stato configurato il processo di CI/CD in questa esercitazione, sono state create automaticamente pipeline di compilazione e di versione in Azure DevOps Projects. È possibile modificare queste pipeline di compilazione e di versione in base alle esigenze del team. Si è appreso come:

> [!div class="checklist"]
>  * Usare DevOps Projects per creare una pipeline CI/CD
> * Configurare l'accesso al repository GitHub e scegliere un framework
> * Configurare Azure DevOps e una sottoscrizione di Azure
> * Eseguire il commit delle modifiche in GitHub e la distribuzione automatica in Azure
> * Esaminare la pipeline CI/CD di Azure Pipelines
> * Pulire le risorse

Per altre informazioni sulla pipeline CI/CD, vedere:

> [!div class="nextstepaction"]
> [Definire la pipeline di distribuzione continua (CD) in più fasi](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

Per altre informazioni sul monitoraggio delle applicazioni, vedere:
  
 > [!div class="nextstepaction"]
 > [Informazioni su Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview)
