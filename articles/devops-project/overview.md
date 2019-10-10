---
title: Panoramica dei Azure DevOps Projects | Microsoft Docs
description: Comprendere il valore di Azure DevOps Projects
services: devops-project
documentationcenter: ''
author: mlearned
manager: gwallace
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/03/2018
ms.author: mlearned
ms.openlocfilehash: d39702f8e30046dd3cf634fc67ed7095471cd629
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71971482"
---
# <a name="overview-of-azure-devops-projects"></a>Panoramica di Azure DevOps Projects

 Azure DevOps Projects permette di iniziare a usare Azure velocemente. Consente di avviare l'app preferita nel servizio di Azure desiderato in pochi passaggi rapidi dal portale di Azure. 

 DevOps Projects configura tutto ciò che occorre per lo sviluppo, la distribuzione e il monitoraggio di un'applicazione. È possibile usare il dashboard di DevOps Projects per monitorare i commit, le compilazioni e le distribuzioni del codice, il tutto da una singola visualizzazione nel portale di Azure.

## <a name="why-should-i-use-devops-projects"></a>Perché usare DevOps Projects?

  DevOps Projects automatizza l'installazione di un'intera pipeline di integrazione continua (CI) e di recapito continuo (CD) in Azure.  È possibile iniziare con il codice esistente o usare una delle applicazioni di esempio fornite. Quindi sarà possibile distribuire rapidamente l'applicazione in vari servizi di Azure, ad esempio Macchine virtuali, Servizio app, servizi Azure Kubernetes, Database SQL di Azure e Azure Service Fabric.  

  DevOps Projects esegue tutto il lavoro per la configurazione iniziale di una pipeline DevOps, ovvero tutte le operazioni che spaziano dalla configurazione del repository Git iniziale, la configurazione della pipeline CI/CD, la creazione di una risorsa di Application Insights per il monitoraggio, fino alla presentazione di una singola visualizzazione dell'intera soluzione con la creazione di un dashboard di DevOps Projects nel portale di Azure.

È possibile usare DevOps Projects per:

* Distribuire velocemente l'applicazione in Azure
* Automatizzare la configurazione di una pipeline CI/CD
* Visualizzare e comprendere come configurare correttamente una pipeline CI/CD
* Personalizzare ulteriormente le pipeline di versione in base a scenari specifici

## <a name="how-do-i-use-devops-projects"></a>Come usare DevOps Projects?

  DevOps Projects è disponibile nel portale di Azure. Si crea una risorsa di DevOps Projects esattamente come si crea qualsiasi altra risorsa Azure dal portale. DevOps Projects offre un'esperienza dettagliata simile a una procedura guidata per le varie opzioni di configurazione.  

Si sceglieranno varie opzioni di configurazione come parte della configurazione iniziale. Tali opzioni includono:

* Usare l'app di esempio fornita o usare codice personalizzato
* Selezionare un linguaggio app
* Scegliere un framework app in base al linguaggio
* Selezionare un servizio di Azure (destinazione di distribuzione)
* Creare una nuova organizzazione di Azure DevOps o usare un'organizzazione esistente 
* Scegliere la sottoscrizione di Azure
* Scegliere il percorso dei servizi di Azure
* Scegliere tra vari piani tariffari per i servizi di Azure

Dopo aver usato DevOps Projects, è possibile eliminare tutte le risorse da un'unica posizione, il dashboard di DevOps Projects nel portale di Azure.

## <a name="devops-projects-and-azure-devops-integration"></a>Integrazione di DevOps Projects e di Azure DevOps

DevOps Projects è supportato da Azure DevOps. DevOps Projects automatizza tutte le operazioni necessarie in Azure Pipelines per configurare una pipeline CI/CD. Crea un repository Git in un'organizzazione Azure DevOps nuova o esistente e quindi esegue il commit di un'applicazione di esempio o di codice esistente in un nuovo repository Git.  

L'automazione stabilisce anche un trigger CI per la compilazione, in modo che ogni nuovo commit di codice avvii una compilazione. DevOps Projects crea un trigger CD e distribuisce ogni nuova compilazione riuscita nel servizio di Azure desiderato.  

È possibile personalizzare le pipeline di compilazione e versione per altri scenari. In più, è possibile clonare le pipeline di compilazione e di versione per usarle in altri progetti.

Dopo aver creato il progetto DevOps, è possibile:

* Personalizzare la pipeline di compilazione e rilascio
* Usare richieste pull per gestire il flusso del codice e mantenere una qualità alta
* Testare e compilare ogni commit prima di unire il codice per aumentare il livello di qualità
* Registrare il backlog e i problemi insieme all'applicazione

## <a name="how-do-i-start-using-devops-projects"></a>Come iniziare a usare DevOps Projects?

* [Inizia a usare DevOps Projects](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-projects-videos"></a>Video di DevOps Projects

* [Creare CI/CD con Azure DevOps Projects](https://www.youtube.com/watch?v=NuYDAs3kNV8)
