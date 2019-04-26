---
title: Registri per contenitori Docker privati in Azure - panoramica
description: Introduzione al servizio Registro Azure Container, che offre registri Docker privati, gestiti e basati sul cloud.
services: container-registry
author: stevelas
ms.service: container-registry
ms.topic: overview
ms.date: 04/03/2019
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: ce870bfb8d29f7a808962e4d273388ab31186f10
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997407"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Introduzione ai registri per contenitori Docker privati in Azure

Registro Azure Container è un servizio gestito di [registri Docker](https://docs.docker.com/registry/) basato sull'applicazione open source Docker Registry 2.0. Creare e gestire registri contenitori di Azure per archiviare e gestire immagini [contenitore Docker](https://www.docker.com/what-docker) private.

Usare i registri contenitori in Azure con la pipeline di sviluppo e distribuzione di contenitori esistente oppure usare [Attività del Registro Azure Container](#azure-container-registry-tasks) per compilare immagini di contenitore in Azure. La build su richiesta o le build completamente automatizzate con commit del codice sorgente e immagine di base aggiornano i trigger della build.

Per informazioni su Docker e sui contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).

## <a name="use-cases"></a>Casi d'uso

Effettuare il pull di immagini da un registro contenitori di Azure a varie destinazioni di distribuzione:

* **Sistemi di orchestrazione scalabili** che gestiscono applicazioni in contenitori fra più cluster host, quali [Kubernetes](https://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/) e [Docker Swarm](https://docs.docker.com/swarm/).
* **Servizi di Azure** che supportano la creazione e l'esecuzione di applicazioni su vasta scala, inclusi il [servizio Azure Kubernetes](../aks/index.yml), il [servizio app](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/) e altri.

Gli sviluppatori possono anche effettuare il push in un registro contenitori nell'ambito di un flusso di lavoro di sviluppo di contenitori, ad esempio specificando come destinazione un registro contenitori da uno strumento di distribuzione e integrazione continua, quale [Azure DevOps Services](https://docs.microsoft.com/azure/devops/) o [Jenkins](https://jenkins.io/).

Configurare Attività del Registro Azure Container per ricompilare automaticamente le immagini dell'applicazione quando vengono aggiornate le immagini di base oppure automatizzare la compilazione di immagini quando il team esegue il commit di codice in un repository Git. Creare attività in più passaggi per automatizzare la compilazione, i test e l'applicazione di patch di più immagini del contenitore in parallelo nel cloud.

Azure offre strumenti, tra cui l'interfaccia della riga di comando di Azure, il portale di Azure e il supporto di API, per gestire i registri contenitori di Azure. Installare eventualmente l'[estensione Docker per Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) e l'estensione [Account Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) per l'uso dei registri contenitori di Azure. Eseguire il pull e il push delle immagini in un registro contenitori di Azure o eseguire Attività del Registro Azure Container, il tutto all'interno di Visual Studio Code.

## <a name="key-concepts"></a>Concetti chiave

* **Registro**. Creare uno o più registri contenitori nella sottoscrizione di Azure. I registri sono disponibili in tre SKU: [Basic, Standard e Premium](container-registry-skus.md), ognuno dei quali supporta l'integrazione webhook, l'autenticazione del registro con Azure Active Directory e la funzionalità di eliminazione. Sfruttare l'archiviazione locale con prossimità di rete delle immagini contenitore creando un registro nella stessa località di Azure delle distribuzioni. Usare la funzionalità di [replica geografica](container-registry-geo-replication.md) dei registri Premium per scenari avanzati di replica e distribuzione di immagini del contenitore. Il nome completo di un registro ha il formato `myregistry.azurecr.io`.

  Per [controllare l'accesso](container-registry-authentication.md) a un registro contenitori si usa un'identità di Azure, un'[entità servizio](../active-directory/develop/app-objects-and-service-principals.md) supportata da Azure Active Directory o un account amministratore specificato. Per accedere al registro, usare l'interfaccia della riga di comando di Azure oppure il comando `docker login` standard.

* **Repository**: un registro contiene uno o più repository, che sono gruppi virtuali di immagini del contenitore con lo stesso nome, ma tag o digest diversi. Registro Azure Container supporta spazi dei nomi dei repository multilivello. Con gli spazi dei nomi multilivello è possibile raggruppare raccolte di immagini correlate a un'app specifica oppure una raccolta di app per team operativi o di sviluppo specifici. Ad esempio: 

  * `myregistry.azurecr.io/aspnetcore:1.0.1` rappresenta un'immagine a livello aziendale
  * `myregistry.azurecr.io/warrantydept/dotnet-build` rappresenta un'immagine usata per creare app .NET, condivisa nel reparto garanzia
  * `myregistry.azurecr.io/warrantydept/customersubmissions/web` rappresenta un'immagine Web, raggruppata nell'app customersubmissions, di proprietà del reparto garanzia

* **Immagine**. Ogni immagine archiviata in un repository è uno snapshot di sola lettura di un contenitore compatibile con Docker. I registri contenitori di Azure possono includere immagini sia Windows che Linux. L'utente controlla i nomi delle immagini per tutte le distribuzioni di contenitori. Usare i [comandi di Docker](https://docs.docker.com/engine/reference/commandline/) standard per effettuare il pull e il push di immagini da e verso un repository. Oltre alle immagini del contenitore Docker, Registro Azure Container archivia i [formati del contenuto correlato](container-registry-image-formats.md), ad esempio [grafici Helm](container-registry-helm-repos.md) e immagini incorporate nella [Specifica di formato di immagine OCI (Open Container Initiative)](https://github.com/opencontainers/image-spec/blob/master/spec.md).

* **Contenitore**. Un contenitore definisce e racchiude un'applicazione software e le relative dipendenze in un file system completo, con codice, runtime, strumenti di sistema e librerie. I contenitori Docker vengono eseguiti in base alle immagini Windows o Linux di cui si effettua il pull da un registro contenitori. I contenitori eseguiti in un singolo computer condividono il kernel del sistema operativo. I contenitori Docker sono completamente portatili in tutte le principali distribuzioni Linux, MacOS e Windows.

## <a name="azure-container-registry-tasks"></a>Attività di Registro Azure Container

Le [attività di Registro Azure Container](container-registry-tasks-overview.md) sono un gruppo di funzionalità di Registro Azure Container che consente una compilazione efficiente e semplificata delle immagini del contenitore Docker in Azure. Usare le attività di Registro Azure Container per estendere il ciclo interno di sviluppo nel cloud usando le operazioni di offload `docker build` in Azure. Configurare le attività di compilazione per automatizzare la pipeline di applicazione delle patch al sistema operativo e al framework del contenitore e compilare automaticamente le immagini quando il team esegue il commit del codice nel controllo dell'origine.

Le [attività in più passi](container-registry-tasks-overview.md#multi-step-tasks) consentono di definire ed eseguire attività basate su passaggi per creare, testare e correggere le immagini del contenitore nel cloud. I passaggi dell'attività definiscono singole operazioni di compilazione e push dell'immagine contenitore. Possono anche definire l'esecuzione di uno o più contenitori, in cui ogni passaggio usa il contenitore come ambiente di esecuzione.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un registro contenitori con il portale di Azure](container-registry-get-started-portal.md)
* [Create a container registry using the Azure CLI](container-registry-get-started-azure-cli.md) (Creare un registro contenitori con l'interfaccia della riga di comando di Azure)
* [Automate OS and framework patching with ACR Tasks](container-registry-tasks-overview.md) (Automatizzare la correzione di sistema operativo e framework con le attività di Registro Azure Container)
