---
title: Creare una pipeline CI/CD con Pipeline di Azure - Processo di analisi scientifica dei dati del teamCreate a CI/CD pipeline with Azure Pipelines - Team Data Science Process
description: Crea una pipeline di integrazione continua e distribuzione continua per le applicazioni di Intelligenza Artificiale (AI) utilizzando Docker e Kubernetes.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: 42433ec419ac9e02077cd0359e18b5114206f27d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721830"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>Creare pipeline CI/CD per le app di controllo di sicurezza elettronica usando pipeline di Azure, Docker e KubernetesCreate CI/CD pipelines for AI apps using Azure Pipelines, Docker, and Kubernetes

Un'applicazione di Intelligenza Artificiale (AI) è codice dell'applicazione incorporato con un modello di Machine Learning (ML) pre-addestrato. Esistono sempre due flussi di lavoro per un'applicazione AI: gli scienziati dei dati creano il modello di Configurazione guidata impostazioni di conservazione e gli sviluppatori di app compilano l'app ed espongono la pagina agli utenti finali da utilizzare. Questo articolo descrive come implementare una pipeline di integrazione continua e distribuzione continua (CI/CD) per un'applicazione di applicazione a livello di codice AI che incorpora il modello di configurazione di configurazione di configurazione di funzionalità nel codice sorgente dell'app. Il codice di esempio e l'esercitazione usano un'applicazione Web Python Flask e recuperano un modello con training preliminare da un account di archiviazione BLOB di Azure privato. Puoi anche usare un account di archiviazione AWS S3.

> [!NOTE]
> Il processo seguente è uno dei diversi modi per eseguire CI/CD. Esistono alternative a questo strumento e ai prerequisiti.

## <a name="source-code-tutorial-and-prerequisites"></a>Codice sorgente, esercitazione e prerequisiti

È possibile scaricare [il codice sorgente](https://github.com/Azure/DevOps-For-AI-Apps) e [un'esercitazione dettagliata](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) da GitHub.You can download source code and a detailed tutorial from GitHub. Seguire i passaggi dell'esercitazione per implementare una pipeline CI/CD per la propria applicazione.

Per usare il codice sorgente scaricato e l'esercitazione, sono necessari i prerequisiti seguenti:To use the downloaded source code and tutorial, you need the following prerequisites: 

- Il repository del [codice sorgente](https://github.com/Azure/DevOps-For-AI-Apps) forato al tuo account GitHub
- Un'organizzazione DevOps di [AzureAn Azure DevOps Organization](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Interfaccia della riga di comando di AzureAzure](/cli/azure/install-azure-cli)
- Un cluster di servizio contenitore di [Azure per Kubernetes (AKS)An Azure Container Service for Kubernetes (AKS) cluster](/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) per eseguire comandi e recuperare la configurazione dal cluster AKS 
- Un account del Registro di sistema [del contenitore di AzureAn Azure Container Registry (ACR) account](/azure/container-registry/container-registry-get-started-portal)

## <a name="cicd-pipeline-summary"></a>Riepilogo pipeline CI/CD

Ogni nuovo commit Git avvia la pipeline di compilazione. La compilazione estrae in modo sicuro il modello di ML più recente da un account di archiviazione BLOB e lo comprime con il codice dell'app in un singolo contenitore. Questo disaccoppiamento dei flussi di lavoro di sviluppo e analisi scientifica dei dati delle applicazioni garantisce che l'app di produzione esegua sempre il codice più recente con il modello ML più recente. Se l'app supera i test, la pipeline archivia in modo sicuro l'immagine di compilazione in un contenitore Docker in ACR. La pipeline di rilascio distribuisce quindi il contenitore usando AKS. 

## <a name="cicd-pipeline-steps"></a>Passaggi della pipeline CI/CD

Il diagramma e i passaggi seguenti descrivono l'architettura della pipeline CI/CD:

![Architettura della pipeline CI/CD](./media/ci-cd-flask/architecture.png)

1. Gli sviluppatori lavorano sul codice dell'applicazione nell'IDE di propria scelta.
2. Gli sviluppatori eseguono il commit del codice in Azure Repos, GitHub o un altro provider del controllo del codice sorgente Git.The developers commit the code to Azure Repos, GitHub, or other Git source control provider. 
3. Separatamente, gli scienziati dei dati lavorano allo sviluppo del loro modello di ML.
4. Gli scienziati dei dati pubblicano il modello finito in un repository di modelli, in questo caso un account di archiviazione BLOB. 
5. Pipeline di Azure avvia una compilazione basata sul commit Git.Azure Pipelines kicks a build based on the Git commit.
6. La pipeline di compilazione esegue il pull del modello di ML più recente dall'archiviazione BLOB e crea un contenitore.
7. La pipeline esegue il push dell'immagine di compilazione nel repository di immagini private in ACR.
8. La pipeline di rilascio viene avviata in base alla compilazione riuscita.
9. La pipeline estrae l'immagine più recente da ACR e la distribuisce nel cluster Kubernetes in AKS.
10. Le richieste degli utenti per l'app passano attraverso il server DNS.
11. Il server DNS passa le richieste a un servizio di bilanciamento del carico e invia le risposte agli utenti.

## <a name="see-also"></a>Vedere anche

- [Team Data Science Process (TDSP)](/azure/machine-learning/team-data-science-process/)
- [Azure Machine Learning (AML)](/azure/machine-learning/)
- [Azure DevOps](https://azure.microsoft.com/services/devops/)
- [Servizio Azure Kubernetes](/azure/aks/intro-kubernetes)
