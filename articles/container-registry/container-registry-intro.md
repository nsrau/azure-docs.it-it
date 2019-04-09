---
title: Registri per contenitori Docker privati in Azure - panoramica
description: Introduzione al servizio Registro Azure Container, che offre registri Docker privati, gestiti e basati sul cloud.
services: container-registry
author: stevelas
ms.service: container-registry
ms.topic: overview
ms.date: 03/29/2019
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: 39f643bd66e2a96b0b9b93989d2941a9c30ea7fc
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58894014"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Introduzione ai registri per contenitori Docker privati in Azure

Registro Azure Container è un servizio gestito di [registri Docker](https://docs.docker.com/registry/) basato sull'applicazione open source Docker Registry 2.0. Creare e gestire registri contenitori di Azure per archiviare e gestire immagini [contenitore Docker](https://www.docker.com/what-docker) private.

È possibile usare i registri contenitori in Azure con le pipeline di sviluppo e distribuzione del contenitore esistente. Compilare immagini dei contenitori in Azure con la build di Registro Azure Container. La build su richiesta o le build completamente automatizzate con commit del codice sorgente e immagine di base aggiornano i trigger della build.

Per informazioni su Docker e sui contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).

## <a name="use-cases"></a>Casi d'uso

Effettuare il pull di immagini da un registro contenitori di Azure a varie destinazioni di distribuzione:

* **Sistemi di orchestrazione scalabili** che gestiscono applicazioni in contenitori fra più cluster host, quali [Kubernetes](https://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/) e [Docker Swarm](https://docs.docker.com/swarm/).
* **Servizi di Azure** che supportano la creazione e l'esecuzione di applicazioni su vasta scala, inclusi il [servizio Azure Kubernetes](../aks/index.yml), il [servizio app](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/) e altri.

Gli sviluppatori possono anche effettuare il push in un registro contenitori nell'ambito di un flusso di lavoro di sviluppo di contenitori, ad esempio specificando come destinazione un registro contenitori da uno strumento di distribuzione e integrazione continua, quale [Azure DevOps Services](https://docs.microsoft.com/azure/devops/) o [Jenkins](https://jenkins.io/).

Configurare le attività del Registro Azure Container per ricreare automaticamente le immagini delle applicazioni quando vengono aggiornate le immagini di base. Usare le attività di Registro Azure Container per automatizzare le build delle immagini quando il team esegue il commit del codice in un repository GIT.

## <a name="key-concepts"></a>Concetti chiave

* **Registro**. Creare uno o più registri contenitori nella sottoscrizione di Azure. I registri sono disponibili in tre SKU: [Basic, Standard e Premium](container-registry-skus.md), ognuno dei quali supporta l'integrazione webhook, l'autenticazione del registro con Azure Active Directory e la funzionalità di eliminazione. Sfruttare l'archiviazione locale con prossimità di rete delle immagini contenitore creando un registro nella stessa località di Azure delle distribuzioni. Usare la funzionalità di [replica geografica](container-registry-geo-replication.md) dei registri Premium per scenari avanzati di replica e distribuzione di immagini del contenitore. Il nome completo di un registro ha il formato `myregistry.azurecr.io`.

  Per [controllare l'accesso](container-registry-authentication.md) a un registro contenitori si usa un'identità di Azure, un'[entità servizio](../active-directory/develop/app-objects-and-service-principals.md) supportata da Azure Active Directory o un account amministratore specificato. Per accedere al registro, usare l'interfaccia della riga di comando di Azure oppure il comando `docker login` standard.

* **Repository**. Un registro contiene uno o più repository, che archiviano gruppi di immagini contenitore. Registro Azure Container supporta spazi dei nomi dei repository multilivello. Con gli spazi dei nomi multilivello è possibile raggruppare raccolte di immagini correlate a un'app specifica oppure una raccolta di app per team operativi o di sviluppo specifici. Ad esempio: 

  * `myregistry.azurecr.io/aspnetcore:1.0.1` rappresenta un'immagine a livello aziendale
  * `myregistry.azurecr.io/warrantydept/dotnet-build` rappresenta un'immagine usata per creare app .NET, condivise nel reparto garanzia
  * `myregistry.azurecr.io/warrantydept/customersubmissions/web` rappresenta un'immagine Web, raggruppata nell'app customersubmissions, di proprietà del reparto garanzia

* **Immagine**. Ogni immagine archiviata in un repository è uno snapshot di sola lettura di un contenitore compatibile con Docker. I registri contenitori di Azure possono includere immagini sia Windows che Linux. L'utente controlla i nomi delle immagini per tutte le distribuzioni di contenitori. Usare i [comandi di Docker](https://docs.docker.com/engine/reference/commandline/) standard per effettuare il pull e il push di immagini da e verso un repository. Oltre alle immagini del contenitore, Registro Azure Container archivia [formati del contenuto correlato](container-registry-image-formats.md) come i [grafici Helm](container-registry-helm-repos.md) usati per distribuire applicazioni in Kubernetes.

* **Contenitore**. Un contenitore definisce e racchiude un'applicazione software e le relative dipendenze in un file system completo, con codice, runtime, strumenti di sistema e librerie. I contenitori Docker vengono eseguiti in base alle immagini Windows o Linux di cui si effettua il pull da un registro contenitori. I contenitori eseguiti in un singolo computer condividono il kernel del sistema operativo. I contenitori Docker sono completamente portatili in tutte le principali distribuzioni Linux, MacOS e Windows.

## <a name="azure-container-registry-tasks"></a>Attività di Registro Azure Container

Le [attività di Registro Azure Container](container-registry-tasks-overview.md) sono un gruppo di funzionalità di Registro Azure Container che consente una compilazione efficiente e semplificata delle immagini del contenitore Docker in Azure. Usare le attività di Registro Azure Container per estendere il ciclo interno di sviluppo nel cloud usando le operazioni di offload `docker build` in Azure. Configurare le attività di compilazione per automatizzare la pipeline di applicazione delle patch al sistema operativo e al framework del contenitore e compilare automaticamente le immagini quando il team esegue il commit del codice nel controllo dell'origine.

Le [attività in più passi](container-registry-tasks-overview.md#multi-step-tasks) consentono di definire ed eseguire attività basate su passaggi per creare, testare e correggere le immagini del contenitore nel cloud. I passaggi dell'attività definiscono singole operazioni di compilazione e push dell'immagine contenitore. Possono anche definire l'esecuzione di uno o più contenitori, in cui ogni passaggio usa il contenitore come ambiente di esecuzione.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un registro contenitori con il portale di Azure](container-registry-get-started-portal.md)
* [Creare un registro contenitori usando l'interfaccia della riga di comando di Azure](container-registry-get-started-azure-cli.md)
* [Automate OS and framework patching with ACR Tasks (Automatizzare l'applicazione di patch al sistema operativo e al framework con ACR Tasks)](container-registry-tasks-overview.md)
