---
title: Creare una pipeline di integrazione continua/recapito continuo con il processo di analisi scientifica dei dati Azure Pipelines team
description: Creare una pipeline di integrazione continua e recapito continuo per le applicazioni di intelligenza artificiale (AI) usando Docker e Kubernetes.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/06/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: f07ce8e8834a2804b6a5b7668718c8e6bff00fa6
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260673"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>Creare pipeline di integrazione continua/recapito continuo per app per intelligenza artificiale con Azure Pipelines, Docker e Kubernetes

Un'applicazione di intelligenza artificiale è un codice dell'applicazione incorporato con un modello di Machine Learning (ML) con training. Per un'applicazione di intelligenza artificiale sono sempre presenti due flussi di lavoro: I data scientist sviluppano il modello di machine learning e gli sviluppatori di app compilano l'app ed esporla agli utenti finali per l'utilizzo. Questo articolo descrive come implementare una pipeline di integrazione continua e recapito continuo (CI/CD) per un'applicazione di intelligenza artificiale che incorpora il modello ML nel codice sorgente dell'app. Il codice e l'esercitazione di esempio usano una semplice applicazione Web Python Flask e recuperano un modello pretrainato da un account di archiviazione BLOB di Azure privato. È anche possibile usare un account di archiviazione di AWS s3.

> [!NOTE]
> Il processo seguente è uno dei diversi modi per eseguire CI/CD. Sono disponibili alternative a questo strumento e ai prerequisiti.

## <a name="source-code-tutorial-and-prerequisites"></a>Codice sorgente, esercitazione e prerequisiti

È possibile scaricare il [codice sorgente](https://github.com/Azure/DevOps-For-AI-Apps) e un' [esercitazione dettagliata](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) da GitHub. Seguire i passaggi dell'esercitazione per implementare una pipeline CI/CD per la propria applicazione.

Per usare il codice sorgente scaricato e l'esercitazione, sono necessari i prerequisiti seguenti: 

- Il [repository del codice sorgente](https://github.com/Azure/DevOps-For-AI-Apps) è stato duplicato nell'account github
- Un' [organizzazione di Azure DevOps](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli)
- Un [cluster del servizio contenitore di Azure per Kubernetes (AKS)](/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) eseguire i comandi e recuperare la configurazione dal cluster AKS 
- Un [account Azure container Registry (ACR)](/azure/container-registry/container-registry-get-started-portal)

## <a name="cicd-pipeline-summary"></a>Riepilogo della pipeline CI/CD

Ogni nuovo commit Git avvia la pipeline di compilazione. La compilazione estrae in modo sicuro l'ultimo modello ML da un account di archiviazione BLOB e lo inserisce nel codice dell'app in un singolo contenitore. Questa separazione dei flussi di lavoro per lo sviluppo di applicazioni e data science garantisce che l'app di produzione esegua sempre il codice più recente con il modello ML più recente. Se l'app supera il test, la pipeline archivia in modo sicuro l'immagine di compilazione in un contenitore Docker in ACR. La pipeline di rilascio distribuisce quindi il contenitore usando AKS. 

## <a name="cicd-pipeline-steps"></a>Passaggi della pipeline CI/CD

Il diagramma e i passaggi seguenti descrivono l'architettura della pipeline CI/CD:

![Architettura della pipeline CI/CD](./media/ci-cd-flask/architecture.png)

1. Gli sviluppatori lavorano sul codice dell'applicazione nell'IDE preferito.
2. Gli sviluppatori hanno eseguito il commit del codice per Azure Repos, GitHub o un altro provider del controllo del codice sorgente git. 
3. Separatamente, i data scientist lavorano per lo sviluppo del modello ML.
4. I data scientist pubblicano il modello finito in un repository di modelli, in questo caso un account di archiviazione BLOB. 
5. Azure Pipelines avvia una compilazione basata sul commit Git.
6. La pipeline di compilazione estrae il modello ML più recente dall'archivio BLOB e crea un contenitore.
7. La pipeline esegue il push dell'immagine di compilazione nel repository di immagini private in ACR.
8. La pipeline di rilascio viene avviata in base alla compilazione riuscita.
9. La pipeline estrae l'immagine più recente da ACR e la distribuisce attraverso il cluster Kubernetes su AKS.
10. Le richieste degli utenti per l'app passano attraverso il server DNS.
11. Il server DNS passa le richieste a un servizio di bilanciamento del carico e restituisce le risposte agli utenti.

## <a name="see-also"></a>Vedere anche

- [Team Data Science Process (TDSP)](/azure/machine-learning/team-data-science-process/)
- [Azure Machine Learning (AML)](/azure/machine-learning/)
- [Azure DevOps](https://azure.microsoft.com/services/devops/)
- [Servizio Azure Kubernetes](/azure/aks/intro-kubernetes)
