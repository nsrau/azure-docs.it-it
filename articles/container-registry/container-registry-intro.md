---
title: Registri per contenitori Docker privati in Azure
description: Introduzione al servizio Registro contenitori di Azure, che offre registri Docker privati, gestiti e basati sul cloud.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: overview
ms.date: 05/08/2018
ms.author: stevelas
ms.custom: mvc
ms.openlocfilehash: 394297e87ef03541725aad0689f11bca17c05ed9
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576301"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Introduzione ai registri per contenitori Docker privati in Azure

Registro contenitori di Azure è un servizio gestito di [registri Docker](https://docs.docker.com/registry/) basato sull'applicazione open source Docker Registry 2.0. Creare e gestire registri contenitori di Azure per archiviare e gestire immagini [contenitore Docker](https://www.docker.com/what-docker) private.

È possibile usare i registri contenitori in Azure con le pipeline di sviluppo e distribuzione del contenitore esistente. Compilare immagini dei contenitori in Azure con la build del Registro contenitori di Azure. La build su richiesta o le build completamente automatizzate con commit del codice sorgente e immagine di base aggiornano i trigger della build.

Per informazioni su Docker e sui contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).

## <a name="use-cases"></a>Casi d'uso

Effettuare il pull di immagini da un registro contenitori di Azure a varie destinazioni di distribuzione:

* **Sistemi di orchestrazione scalabili** che gestiscono applicazioni in contenitori in cluster di host, quali [DC/OS](https://docs.mesosphere.com/), [Docker Swarm](https://docs.docker.com/swarm/) e [Kubernetes](http://kubernetes.io/docs/).
* **Servizi di Azure** che supportano la creazione e l'esecuzione di applicazioni su vasta scala, inclusi [Azure Kubernetes Service, ovvero AKS](../aks/index.yml), [Servizio app](/app-service/index.md), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/) e altri.

Gli sviluppatori possono anche effettuare il push in un registro contenitori nell'ambito di un flusso di lavoro di sviluppo di contenitori, ad esempio specificando come destinazione un Registro di sistema del contenitore da uno strumento di distribuzione e integrazione continua, quali [Visual Studio Team Services](https://www.visualstudio.com/docs/overview) o [Jenkins](https://jenkins.io/).

Configurare le attività di compilazione della [build del Registro contenitori di Azure](#azure-container-registry-build) per ricompilare automaticamente le immagini dell'applicazione quando vengono aggiornate le immagini di base. Usare la build del Registro contenitori di Azure per automatizzare le build delle immagini quando il team esegue il commit del codice in un repository GIT. *La build del Registro contenitori di Azure è attualmente in anteprima.*

## <a name="key-concepts"></a>Concetti chiave

* **Registro**. Creare uno o più registri contenitori nella sottoscrizione di Azure. I registri sono disponibili in tre SKU, ovvero [Basic, Standard e Premium](container-registry-skus.md), ognuno dei quali supporta l'integrazione webhook, l'autenticazione del registro con Azure Active Directory e la funzionalità di eliminazione. Sfruttare l'archiviazione locale con prossimità di rete delle immagini contenitore creando un registro nella stessa località di Azure delle distribuzioni. Usare la funzionalità di [replica geografica](container-registry-geo-replication.md) dei registri Premium per scenari avanzati di replica e distribuzione di immagini del contenitore. Il nome completo di un registro ha il formato `myregistry.azurecr.io`.

  Per [controllare l'accesso](container-registry-authentication.md) a un registro contenitori si usa un'[entità servizio](../active-directory/develop/app-objects-and-service-principals.md) supportata da Azure Active Directory o un account amministratore specificato. Per l'autenticazione al registro, eseguire il comando `docker login` standard.

* **Repository**. Un registro contiene uno o più repository, costituiti da gruppi di immagini contenitore. Registro contenitori di Azure supporta spazi dei nomi dei repository multilivello. Con gli spazi dei nomi multilivello è possibile raggruppare raccolte di immagini correlate a un'app specifica oppure una raccolta di app per team operativi o di sviluppo specifici. Ad esempio: 

  * `myregistry.azurecr.io/aspnetcore:1.0.1` rappresenta un'immagine a livello aziendale
  * `myregistry.azurecr.io/warrantydept/dotnet-build` rappresenta un'immagine usata per creare app .NET, condivisa nel reparto garanzia
  * `myregistry.azurecr.io/warrantydept/customersubmissions/web` rappresenta un'immagine Web, raggruppata nell'app customersubmissions, di proprietà del reparto garanzia

* **Immagine**. Ogni immagine archiviata in un repository è uno snapshot di sola lettura di un contenitore Docker. I registri contenitori di Azure possono includere immagini sia Windows che Linux. L'utente controlla i nomi delle immagini per tutte le distribuzioni di contenitori. Usare i [comandi di Docker](https://docs.docker.com/engine/reference/commandline/) standard per effettuare il pull e il push di immagini da e verso un repository.

* **Contenitore**. Un contenitore definisce e racchiude un'applicazione software e le relative dipendenze in un file system completo, con codice, runtime, strumenti di sistema e librerie. I contenitori Docker vengono eseguiti in base alle immagini Windows o Linux di cui si effettua il pull da un registro contenitori. I contenitori eseguiti in un singolo computer condividono il kernel del sistema operativo. I contenitori Docker sono completamente portatili in tutte le principali distribuzioni Linux, MacOS e Windows.

## <a name="azure-container-registry-build-preview"></a>Anteprima della build del Registro contenitori di Azure

La [build del Registro contenitori di Azure](container-registry-build-overview.md) è un gruppo di funzionalità del Registro contenitori di Azure che consente una compilazione efficiente e semplificata delle immagini del contenitore Docker in Azure. Usare la build del Registro contenitori di Azure per estendere il ciclo interno di sviluppo nel cloud usando le operazioni di offload `docker build` in Azure. Configurare le attività di compilazione per automatizzare la pipeline di applicazione delle patch al sistema operativo e al framework del contenitore e compilare automaticamente le immagini quando il team esegue il commit del codice nel controllo dell'origine.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Creare un registro contenitori con il portale di Azure](container-registry-get-started-portal.md)
* [Create a container registry using the Azure CLI](container-registry-get-started-azure-cli.md) (Creare un registro contenitori con l'interfaccia della riga di comando di Azure)
* Anteprima di [Automate OS and framework patching with ACR Build](container-registry-build-overview.md) (Automatizzare l'applicazione di patch al sistema operativo e al framework con la build del Registro contenitori di Azure)
