---
title: Registri per contenitori Docker privati in Azure | Microsoft Docs
description: Introduzione al servizio Registro contenitori di Azure, che offre registri Docker privati, gestiti e basati sul cloud.
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: ee2b652b-fb7c-455b-8275-b8d4d08ffeb3
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 4eb32dd18c57af8d46e7afb63047c0985458705d
ms.contentlocale: it-it
ms.lasthandoff: 07/25/2017

---
# <a name="introduction-to-private-docker-container-registries"></a>Introduzione ai registri per contenitori Docker privati


Registro contenitori di Azure è un servizio gestito di [registri Docker](https://docs.docker.com/registry/) basato sull'applicazione open source Docker Registry 2.0. Creare e gestire registri contenitori di Azure per archiviare e gestire immagini [contenitore Docker](https://www.docker.com/what-docker) private. È possibile usare i registri contenitori in Azure con la pipeline di sviluppo e distribuzione di contenitori esistente e attingere alle competenze della community Docker.

Per informazioni su Docker e sui contenitori, vedere:

* [Guida dell'utente di Docker](https://docs.docker.com/engine/userguide/)




## <a name="use-cases"></a>Casi d'uso
Effettuare il pull di immagini da un registro contenitori di Azure a varie destinazioni di distribuzione:

* **Sistemi di orchestrazione scalabili** che gestiscono applicazioni in contenitori in cluster di host, quali [DC/OS](https://docs.mesosphere.com/), [Docker Swarm](https://docs.docker.com/swarm/) e [Kubernetes](http://kubernetes.io/docs/).
* **Servizi di Azure** che supportano la creazione e l'esecuzione di applicazioni su vasta scala, come [Servizio contenitore](../container-service/index.yml), [Servizio app](/app-service/index.md), [Batch](../batch/index.md), [Service Fabric](/azure/service-fabric/) e altri.

Gli sviluppatori possono anche effettuare il push in un registro contenitori nell'ambito di un flusso di lavoro di sviluppo di contenitori, ad esempio specificando come destinazione un Registro di sistema del contenitore da uno strumento di distribuzione e integrazione continua, quali [Visual Studio Team Services](https://www.visualstudio.com/docs/overview) o [Jenkins](https://jenkins.io/).





## <a name="key-concepts"></a>Concetti chiave
* **Registro**. Creare uno o più registri contenitori nella sottoscrizione di Azure. Ogni registro è supportato da un [account di archiviazione](../storage/storage-introduction.md) di Azure standard nella stessa località. Sfruttare l'archiviazione locale con prossimità di rete delle immagini contenitore creando un registro nella stessa località di Azure delle distribuzioni. Il nome completo di un registro ha il formato `myregistry.azurecr.io`.

  Per [controllare l'accesso](container-registry-authentication.md) a un registro contenitori si usa un'[entità servizio](../active-directory/active-directory-application-objects.md) supportata da Azure Active Directory o un account amministratore specificato. Per l'autenticazione al registro, eseguire il comando `docker login` standard.

* **Registro gestito**: livello che offre funzionalità aggiuntive per i registri in tre SKU, ovvero Basic, Standard e Premium. Le immagini in questi SKU vengono archiviate in account di archiviazione gestiti dal servizio Registro contenitori di Azure, migliorando l'affidabilità e mettendo a disposizione nuove funzionalità. Le nuove funzionalità includono l'integrazione webhook, l'autenticazione nel repository con Azure Active Directory e il supporto della funzionalità di eliminazione. Gli utenti possono scegliere tra i registri gestiti oppure creare un registro supportato dai propri account di archiviazione durante la creazione di registri.

* **Repository**. Un registro contiene uno o più repository, costituiti da gruppi di immagini contenitore. Registro contenitori di Azure supporta spazi dei nomi dei repository multilivello. Questa funzionalità consente di raggruppare raccolte di immagini correlate a un'app specifica oppure una raccolta di app per specifici team operativi o di sviluppo. Ad esempio:

  * `myregistry.azurecr.io/aspnetcore:1.0.1` rappresenta un'immagine a livello aziendale
  * `myregistry.azurecr.io/warrantydept/dotnet-build` rappresenta un'immagine usata per creare app .NET, condivisa nel reparto garanzia
  * `myregistry.azrecr.io/warrantydept/customersubmissions/web` rappresenta un'immagine Web, raggruppata nell'app customersubmissions, di proprietà del reparto garanzia

* **Immagine**. Ogni immagine archiviata in un repository è uno snapshot di sola lettura di un contenitore Docker. I registri contenitori di Azure possono includere immagini sia Windows che Linux. L'utente controlla i nomi delle immagini per tutte le distribuzioni di contenitori. Usare i [comandi di Docker](https://docs.docker.com/engine/reference/commandline/) standard per effettuare il pull e il push di immagini da e verso un repository.

* **Contenitore**. Un contenitore definisce e racchiude un'applicazione software e le relative dipendenze in un file system completo, con codice, runtime, strumenti di sistema e librerie. I contenitori Docker vengono eseguiti in base alle immagini Windows o Linux di cui si effettua il pull da un registro contenitori. I contenitori eseguiti in un singolo computer condividono il kernel del sistema operativo. I contenitori Docker sono completamente portabili in tutte le principali distribuzioni Linux, Mac e Windows.




## <a name="next-steps"></a>Passaggi successivi
* [Creare un registro contenitori con il portale di Azure](container-registry-get-started-portal.md)
* [Create a container registry using the Azure CLI](container-registry-get-started-azure-cli.md) (Creare un registro contenitori con l'interfaccia della riga di comando di Azure)
* [Effettuare il push della prima immagine con l'interfaccia della riga di comando di Docker](container-registry-get-started-docker-cli.md)
* Per compilare un'integrazione continua e un flusso di lavoro di distribuzione usando Visual Studio Team Services, servizio contenitore di Azure e Registro di sistema del contenitore di Azure, vedere [questa esercitazione](../container-service/dcos-swarm/container-service-docker-swarm-setup-ci-cd.md).
* Se si vuole impostare un registro Docker privato in Azure (senza un endpoint pubblico), vedere [Distribuzione del registro Docker privato in Azure](../virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md).

