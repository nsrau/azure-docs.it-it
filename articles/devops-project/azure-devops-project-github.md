---
title: 'Esercitazione: Creare una pipeline CI/CD per il codice esistente usando Azure DevOps Starter'
description: Azure DevOps Starter consente di iniziare a usare Azure senza difficoltà. DevOps Projects consente di usare il proprio codice e il repository di GitHub per avviare un'app in un servizio di Azure con pochi rapidi passaggi.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: gwallace
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: mvc
ms.openlocfilehash: 55c6cbc18e37368dd47c47227041024b13987c47
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82233211"
---
# <a name="create-a-cicd-pipeline-for-github-repo-using-azure-devops-starter"></a>Creare una pipeline CI/CD per il repository GitHub con Azure DevOps Starter

Azure DevOps Starter presenta un processo semplificato per la creazione di una pipeline CI/CD (integrazione continua e distribuzione continua) in Azure. È possibile usare il proprio codice esistente e il repository Git oppure selezionare un'applicazione di esempio.

Si apprenderà come:

> [!div class="checklist"]
> * Usare DevOps Starter per creare una pipeline CI/CD
> * Configurare l'accesso al repository GitHub e scegliere un framework
> * Configurare Azure DevOps e una sottoscrizione di Azure 
> * Eseguire il commit delle modifiche in GitHub e la distribuzione automatica in Azure
> * Esaminare la pipeline CI/CD di Azure Pipelines
> * Pulire le risorse

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. È possibile ottenerne una gratuita tramite [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Accedere a GitHub o a un repository Git esterno contenente codice .NET, Java, PHP, Node.js, Python o Web statico.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Azure DevOps Starter crea una pipeline CI/CD in Azure Pipelines. È possibile creare una nuova organizzazione di Azure DevOps o usare un'organizzazione esistente. Azure DevOps Starter crea anche le risorse di Azure nella sottoscrizione di Azure selezionata.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nella casella di ricerca digitare **DevOps Starter** e quindi selezionare. Fare clic su **Aggiungi** per crearne una nuova.

    ![Dashboard di DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png)
    
1. Selezionare **Usa il codice personale** e quindi selezionare **Avanti**.

## <a name="configure-access-to-your-github-repo-and-select-a-framework"></a>Configurare l'accesso al repository GitHub e selezionare un framework

1. Selezionare **GitHub** o un repository di codice **Git** esterno. Per questa esercitazione, selezionare **GitHub**. Al primo tentativo può essere necessario eseguire l'autenticazione con GitHub per consentire ad Azure di accedere al repository GitHub.

1. Selezionare un **Repository** e un **Ramo**, quindi selezionare **Avanti**.

1. Se si usano contenitori Docker, impostare **App inclusa in Docker** su **SÌ**. Per questa esercitazione, lasciare selezionata l'opzione **NO**, quindi selezionare **Avanti**. Per altre informazioni sull'uso dei contenitori Docker, passare il puntatore sull'icona **i**.

   ![Selezione del framework applicazione nel menu a discesa](_img/azure-devops-project-github/appframework.png)

1. Dai menu a discesa scegliere un **runtime applicazione**  e un **framework applicazione**, quindi selezionare **Avanti**. Il framework applicazione determina il tipo di destinazione della distribuzione del servizio di Azure disponibile.

1. Selezionare un **servizio di Azure** per distribuire l'applicazione, quindi selezionare **Avanti**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurare Azure DevOps e una sottoscrizione di Azure

1. Immettere un nome in **Nome progetto**.

1. Creare una nuova organizzazione gratuita in **Organizzazione DevOps di Azure** oppure sceglierne una esistente dal menu a discesa.

1. Selezionare la propria sottoscrizione in **Sottoscrizione di Azure** e immettere un nome in **App Web** oppure usare il valore predefinito. Selezionare un'opzione per **Località**, quindi **Fine**. Dopo alcuni minuti, nel portale di Azure verrà visualizzata la pagina Panoramica della distribuzione di DevOps Starter.

1. Selezionare **Vai alla risorsa** per visualizzare il dashboard di DevOps Starter. Nell'angolo in alto a destra aggiungere **Progetto** al dashboard, in modo da potervi accedere rapidamente. Azure DevOps Starter configura automaticamente un trigger di compilazione e di versione di integrazione continua. Il codice rimane nel repository GitHub o in un altro repository esterno e in **Organizzazione DevOps di Azure** viene configurata un'app di esempio in un repository. Azure DevOps Starter esegue la versione e distribuisce l'app in Azure.

   ![Visualizzazione dashboard di DevOps Projects](_img/azure-devops-project-github/projectsdashboard.png)

1. Il dashboard mostra il repository di codice, la pipeline CI/CD e l'app in Azure. A destra, in Risorse di Azure, selezionare **Sfoglia** per visualizzare l'app in esecuzione.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Eseguire il commit delle modifiche in GitHub e la distribuzione automatica in Azure

A questo punto è possibile collaborare con un team per l'app. Il processo CI/CD distribuisce automaticamente le modifiche più recenti nel sito Web. Ogni modifica apportata nel repository GitHub avvia una compilazione in DevOps di Azure e una pipeline CD esegue una distribuzione in Azure.

1. Nel dashboard di DevOps Starter selezionare **Repository**. Il repository GitHub si apre in una nuova scheda del browser. Apportare una modifica all'applicazione, quindi selezionare **Commit changes** (Esegui il commit delle modifiche).

1. Dopo alcuni istanti, viene avviata una compilazione in Azure Pipelines. È possibile monitorare lo stato di compilazione nel dashboard di DevOps Starter. È anche possibile monitorarlo nell'organizzazione Azure DevOps selezionando la scheda **Pipeline di compilazione** nel dashboard di DevOps Starter.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Esaminare la pipeline CI/CD di Azure Pipelines

Azure DevOps Starter configura automaticamente una pipeline CI/CD in Azure Pipelines. Esplorare e personalizzare la pipeline in base alle esigenze. Per acquisire familiarità con le pipeline di compilazione e di versione eseguire questa procedura:

1. Selezionare **Pipeline di compilazione** nel dashboard di DevOps Starter.

1. Quando si apre la pagina **Azure Pipelines**, verrà visualizzata la cronologia delle compilazioni più recenti e lo stato di ognuna.

   ![Pagina Compilazioni di Azure Pipelines](_img/azure-devops-project-github/pipelinesbuildpage.png)

1. Nell'angolo in alto a destra della pagina **Compilazioni** è possibile selezionare **Modifica** per cambiare la compilazione corrente, **Coda** per aggiungere una nuova compilazione oppure il pulsante con i puntini di sospensione verticali ( **&#8942;** ) per aprire un menu con altre opzioni. Selezionare **Modifica**.

1. La compilazione esegue diverse attività, ad esempio il recupero delle origini dal repository, il ripristino delle dipendenze e la pubblicazione degli output per le distribuzioni. A destra, in **Nome**, sostituire il nome della pipeline di compilazione con qualcosa di più descrittivo. Selezionare **Salva e accoda** e quindi **Salva**. Immettere un commento e quindi selezionare di nuovo **Salva**.

   ![Pagina Compilazioni di Azure DevOps](_img/azure-devops-project-github/buildpage.png)

1. Per vedere un audit trail delle modifiche recenti epr la compilazione, selezionare la scheda **Cronologia**.  Azure DevOps tiene traccia di tutte le modifiche apportate alla pipeline di compilazione e consente di confrontare le versioni.

1. Selezionare la scheda **Trigger**. Azure DevOps Projects crea automaticamente un trigger di CI con alcune impostazioni predefinite. È possibile impostare trigger come **Abilita l'integrazione continua** per eseguire una compilazione ogni volta che si esegue il commit di una modifica del codice. È inoltre possibile impostare trigger per pianificare l'esecuzione delle compilazioni in momenti specifici.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile eliminare il Servizio app di Azure e le risorse correlate create in questa esercitazione. Usare la funzionalità **Elimina** nel dashboard di DevOps Projects.

## <a name="next-steps"></a>Passaggi successivi

Quando è stato configurato il processo di CI/CD in questa esercitazione, in Azure DevOps Projects sono state create automaticamente pipeline di compilazione e di versione. È possibile modificare queste pipeline di compilazione e di versione in base alle esigenze del team.

Per altre informazioni sulla pipeline CI/CD, vedere:

> [!div class="nextstepaction"]
> [Definire la pipeline di distribuzione continua (CD) in più fasi](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

Per altre informazioni sul monitoraggio delle applicazioni, vedere:
  
 > [!div class="nextstepaction"]
 > [Che cos'è Monitoraggio di Azure?](https://docs.microsoft.com/azure/azure-monitor/overview)
