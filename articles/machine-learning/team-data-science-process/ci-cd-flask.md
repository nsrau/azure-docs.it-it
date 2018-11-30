---
title: "DevOps per le applicazioni di intelligenza artificiale: creazione di una pipeline di integrazione continua in Azure mediante un'applicazione Docker, Kubernetes e Python Flask"
description: 'DevOps per le applicazioni di intelligenza artificiale: creazione di una pipeline di integrazione continua in Azure mediante Docker e Kubernetes'
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 05/22/2018
ms.author: tdsp
ms.custom: (previous author=jainr, ms.author=jainr)
ms.openlocfilehash: c232680d5d1bf0eb761ff974ebf6608b67922f33
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52496754"
---
# <a name="devops-for-artificial-intelligence-ai-applications-creating-continuous-integration-pipeline-on-azure-using-docker-and-kubernetes"></a>DevOps per le applicazioni di intelligenza artificiale: creazione di una pipeline di integrazione continua in Azure mediante Docker e Kubernetes
Per un'applicazione di intelligenza artificiale sono spesso presenti due flussi di lavoro: i data scientist che compilano modelli di Machine Learning e gli sviluppatori di app che compilano l'applicazione e la espongono per gli utenti finali. Questo articolo illustra come implementare una pipeline di integrazione continua/recapito continuo per un'applicazione di intelligenza artificiale. L'applicazione di intelligenza artificiale è una combinazione del codice di applicazione incorporato con un modello di Machine Learning (ML) di cui è stato eseguito il training. In questo articolo viene recuperato un modello di cui è già stato eseguito il training da un account di archiviazione BLOB privato di Azure, ma è possibile usare anche un account AWS S3. Nell'articolo viene usata una semplice applicazione Web Python Flask.

> [!NOTE]
> Si tratta di uno dei metodi con cui è possibile eseguire l'integrazione continua e il recapito continuo. Sono disponibili strumenti alternativi e altri prerequisiti descritti più avanti. Ulteriori contenuti verranno pubblicati non appena disponibili.
>
>

## <a name="github-repository-with-document-and-code"></a>Repository GitHub con documento e codice
È possibile scaricare il codice sorgente da [GitHub](https://github.com/Azure/DevOps-For-AI-Apps). È anche disponibile un'[esercitazione dettagliata](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md).

## <a name="pre-requisites"></a>Prerequisiti
I prerequisiti seguenti si applicano alla pipeline di integrazione continua/recapito continuo descritta di seguito:
* [Organizzazione DevOps di Azure](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Cluster del servizio contenitore di Azure (AKS) che esegue Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
* [Account del Registro contenitori di Azure (ACR)](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal)
* [Installare Kubectl per eseguire i comandi nel cluster Kubernetes.](https://kubernetes.io/docs/tasks/tools/install-kubectl/) Necessario per recuperare la configurazione dal cluster ACS. 
* Creare una fork del repository nell'account GitHub.

## <a name="description-of-the-cicd-pipeline"></a>Descrizione della pipeline di integrazione continua/recapito continuo
La pipeline viene attivata per ogni nuovo commit e viene eseguito il gruppo di test. Se il test ha esito positivo, viene usata l'ultima compilazione che viene inserita in un contenitore Docker. Il contenitore viene quindi distribuito usando il servizio contenitore di Azure (ACS), mentre le immagini vengono archiviate in modo sicuro nel Registro contenitori di Azure (ACR). ACS esegue Kubernetes per la gestione dei cluster contenitore, ma è possibile scegliere Docker Swarm o Mesos.

L'applicazione esegue il pull del modello più recente da un account di archiviazione di Azure e lo inserisce come parte dell'applicazione. L'applicazione distribuita ha il codice dell'app e il modello di Machine Learning inserito come singolo contenitore. Ciò consente di separare gli sviluppatori di app dai data scientist per garantire che la relativa app di produzione esegua sempre il codice più recente con il modello di Machine Learning più recente.

L'architettura della pipeline è descritta di seguito. 

![Architettura](./media/ci-cd-flask/Architecture.PNG?raw=true)

## <a name="steps-of-the-cicd-pipeline"></a>Passaggi della pipeline di integrazione continua/recapito continuo
1. Gli sviluppatori usano l'IDE preferito nel codice dell'applicazione.
2. Eseguono il commit del codice nel controllo del codice sorgente desiderato (Azure DevOps supporta diversi controlli del codice sorgente)
3. Il data scientist lavora separatamente per lo sviluppo del modello.
4. Al termine, il modello viene pubblicato in un repository di modelli, in questo caso un account di archiviazione BLOB. 
5. Viene avviata una compilazione in Azure DevOps in base al commit in GitHub.
6. La pipeline di compilazione Azure DevOps esegue il pull del modello più recente dal contenitore BLOB e crea un contenitore.
7. Azure DevOps esegue il push dell'immagine nel repository delle immagini privato nel Registro contenitori di Azure
8. La pipeline di versione viene avviata in base alla pianificazione impostata (notturna).
9. Viene eseguito il pull dell'immagine più recente di ACR che viene distribuita nel cluster Kubernetes in ACS.
10. La richiesta dell'app da parte degli utenti avviene tramite il server DNS.
11. Il server DNS inoltra la richiesta al bilanciamento del carico e invia la risposta all'utente.

## <a name="next-steps"></a>Passaggi successivi
* Fare riferimento all'[esercitazione](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) per i dettagli e per implementare la pipeline di integrazione continua/recapito continuo per l'applicazione.

## <a name="references"></a>Riferimenti
* [Team Data Science Process (TDSP)](https://aka.ms/tdsp)
* [Azure Machine Learning (AML)](https://docs.microsoft.com/azure/machine-learning/service/)
* [Azure DevOps](https://www.visualstudio.com/vso/)
* [Azure Kubernetes Services (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)
