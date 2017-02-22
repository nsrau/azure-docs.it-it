---
title: Registro per contenitori Docker privati in Azure | Documentazione Microsoft
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
ms.date: 11/14/2016
ms.author: stevelas
translationtype: Human Translation
ms.sourcegitcommit: f299cff22d00a1c765a32838647818d18f3df85d
ms.openlocfilehash: 44bfd57feecc45e037717a35b64dd8e4bfb6a1b3

---
# <a name="what-is-azure-container-registry"></a>Informazioni su Registro contenitori di Azure
> [!NOTE]
> Registro contenitori è attualmente disponibile in anteprima.


Registro contenitori di Azure è un servizio gestito di [registri Docker](https://docs.docker.com/registry/) basato sull'applicazione open source Docker Registry versione&2;. Creare e gestire registri contenitori di Azure per archiviare e gestire immagini [contenitore Docker](https://www.docker.com/what-docker) private. È possibile usare i registri contenitori in Azure con la pipeline di sviluppo e distribuzione di contenitori esistente e attingere alle competenze della community Docker.

Per informazioni su Docker e sui contenitori, vedere:

* [Guida dell'utente di Docker](https://docs.docker.com/engine/userguide/)
* [Annuncio della versione di anteprima di Registro contenitori di Azure](https://azure.microsoft.com/blog/azure-container-registry-preview/) 

## <a name="key-concepts"></a>Concetti chiave
* **Registro**. Creare uno o più registri contenitori nella sottoscrizione di Azure. Ogni registro è supportato da un [account di archiviazione](../storage/storage-introduction.md) di Azure standard nella stessa località. Creare un registro nella stessa località di Azure delle distribuzioni per sfruttare l'archiviazione locale con prossimità di rete delle immagini contenitore. 

  Ai registri viene assegnato un nome in un dominio radice in base al [tenant Azure Active Directory](../active-directory/active-directory-howto-tenant.md) della sottoscrizione. In caso di account aziendale nel dominio Contoso, ad esempio, il nome completo del registro avrà il formato `myregistry-contoso.azurecr.io`. 
  
  Per [controllare l'accesso](container-registry-authentication.md) a un registro contenitori si usa un'[entità servizio](../active-directory/active-directory-application-objects.md) supportata da Azure Active Directory o un account amministratore specificato. Per l'autenticazione al registro, eseguire il comando `docker login` standard. 

* **Repository**. Un registro contiene uno o più repository, costituiti da gruppi di immagini contenitore. Registro contenitori di Azure supporta spazi dei nomi dei repository multilivello. Questa funzionalità consente di raggruppare raccolte di immagini correlate a un'app specifica oppure una raccolta di app per specifici team operativi o di sviluppo. Ad esempio:
  
  * `myregistry-contoso.azurecr.io/aspnetcore:1.0.1` rappresenta un'immagine a livello aziendale
  * `myregistry-contoso.azurecr.io/warrantydept/dotnet-build` rappresenta un'immagine usata per creare app .NET, condivisa nel reparto garanzia
  * `myregistry-contoso.azrecr.io/warrantydept/customersubmissions/web` rappresenta un'immagine Web, raggruppata nell'app customersubmissions, di proprietà del reparto garanzia

* **Immagine**. Ogni immagine archiviata in un repository è uno snapshot di sola lettura di un contenitore Docker. I registri contenitori di Azure possono includere immagini sia Windows che Linux. L'utente controlla i nomi delle immagini per tutte le distribuzioni di contenitori. Usare i [comandi di Docker](https://docs.docker.com/engine/reference/commandline/) standard per effettuare il pull e il push di immagini da e verso un repository. 

* **Contenitore**. Un contenitore definisce e racchiude un'applicazione software e le relative dipendenze in un file system completo, con codice, runtime, strumenti di sistema e librerie. I contenitori Docker vengono eseguiti in base alle immagini Windows o Linux di cui si effettua il pull da un registro contenitori. I contenitori eseguiti in un singolo computer condividono il kernel del sistema operativo. I contenitori Docker sono completamente portabili in tutte le principali distribuzioni Linux, Mac e Windows.

## <a name="use-cases"></a>Casi d'uso
Effettuare il pull di immagini da un registro contenitori di Azure a varie destinazioni di distribuzione:

* **Sistemi di orchestrazione scalabili** che gestiscono applicazioni in contenitori in cluster di host, quali [DC/OS](https://docs.mesosphere.com/), [Docker Swarm](https://docs.docker.com/swarm/) e [Kubernetes](http://kubernetes.io/docs/).
* **Servizi di Azure** che supportano la creazione e l'esecuzione di applicazioni su vasta scala, quali [Servizio contenitore](../container-service/index.md), [Servizio app](/app-service/index.md), [Batch](../batch/index.md) e [Service Fabric](../service-fabric/index.md). 

Gli sviluppatori possono anche effettuare il push in un registro contenitori nell'ambito di un flusso di lavoro di sviluppo di contenitori, ad esempio specificando come destinazione un Registro di sistema del contenitore da uno strumento di distribuzione e integrazione continua, quali [Visual Studio Team Services](https://www.visualstudio.com/docs/overview) o [Jenkins](https://jenkins.io/).





## <a name="next-steps"></a>Passaggi successivi
* [Creare un registro contenitori con il portale di Azure](container-registry-get-started-portal.md)
* [Create a container registry using the Azure CLI](container-registry-get-started-azure-cli.md) (Creare un registro contenitori con l'interfaccia della riga di comando di Azure)
* [Effettuare il push della prima immagine con l'interfaccia della riga di comando di Docker](container-registry-get-started-docker-cli.md)
* Per compilare un'integrazione continua e un flusso di lavoro di distribuzione usando Visual Studio Team Services, servizio contenitore di Azure e Registro di sistema del contenitore di Azure, vedere [questa esercitazione](../container-service/container-service-setup-ci-cd.md).
* Se si vuole impostare un registro Docker privato in Azure (senza un endpoint pubblico), vedere [Distribuzione del registro Docker privato in Azure](../virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md).



<!--HONumber=Feb17_HO3-->


