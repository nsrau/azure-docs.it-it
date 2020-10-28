---
title: Panoramica di DevOps Starter per Azure | Microsoft Docs
description: Informazioni sui vantaggi di DevOps Starter
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
ms.date: 03/24/2020
ms.author: mlearned
ms.openlocfilehash: 7c36539ef5be503b2cb7e14047e596522ef8e962
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92330649"
---
# <a name="overview-of-devops-starter"></a>Panoramica di DevOps Starter

 DevOps Starter consente di iniziare a usare Azure senza difficoltà, usando GitHub Actions o Azure DevOps. Consente di avviare l'app preferita nel servizio di Azure desiderato in pochi passaggi rapidi dal portale di Azure. 

 DevOps Starter configura tutto il necessario per lo sviluppo, la distribuzione e il monitoraggio di un'applicazione. È possibile usare il dashboard di DevOps Starter per monitorare i commit, le compilazioni e le distribuzioni del codice, il tutto da una singola visualizzazione nel portale di Azure.

## <a name="advantages-of-using-devops-starter"></a>Vantaggi dell'uso di DevOps Starter

  DevOps Starter supporta i due provider CI/CD seguenti per automatizzare le distribuzioni
  * [GitHub Actions](https://github.com/features/actions)
  * [Azure DevOps](https://azure.microsoft.com/services/devops)

  DevOps Starter automatizza la configurazione di un'intera pipeline di integrazione continua (CI) e distribuzione continua (CD) per l'applicazione in Azure.  È possibile iniziare con il codice esistente o usare una delle applicazioni di esempio fornite. Quindi sarà possibile distribuire rapidamente l'applicazione in vari servizi di Azure, ad esempio Macchine virtuali, Servizio app, servizi Azure Kubernetes, Database SQL di Azure e Azure Service Fabric.  

  DevOps Starter esegue tutte le attività di configurazione iniziale di una pipeline di DevOps, tra cui la configurazione del repository Git iniziale, la configurazione della pipeline CI/CD, la creazione di una risorsa di Application Insights per il monitoraggio e la creazione di un dashboard di DevOps Starter nel portale di Azure che offre una singola visualizzazione dell'intera soluzione.

È possibile usare DevOps Starter per:

* Distribuire velocemente l'applicazione in Azure
* Automatizzare la configurazione di un flusso di lavoro o una pipeline CI/CD
* Visualizzare e comprendere come configurare correttamente un flusso di lavoro o una pipeline CI/CD
* Personalizzare ulteriormente le pipeline di versione in base a scenari specifici

## <a name="how-to-use-devops-starter"></a>Come si usa DevOps Starter?

  DevOps Starter è disponibile nel portale di Azure. Le risorse di DevOps Starter possono essere create come tutte le altre risorse di Azure dal portale. DevOps Projects offre un'esperienza dettagliata simile a una procedura guidata per le varie opzioni di configurazione.  

Si sceglieranno varie opzioni di configurazione come parte della configurazione iniziale. Tali opzioni includono:

* Selezione del provider CI/CD preferito
* Uso dell'app di esempio fornita o del proprio codice (solo per Azure DevOps)
* Selezionare un linguaggio app
* Scegliere un framework app in base al linguaggio
* Selezionare un servizio di Azure (destinazione di distribuzione)
* Selezionare l'organizzazione di GitHub o Azure DevOps
* Scegliere la sottoscrizione di Azure
* Scegliere il percorso dei servizi di Azure
* Scegliere tra vari piani tariffari per i servizi di Azure

Dopo aver creato il progetto DevOps Starter, è possibile:

* Personalizzare il flusso di lavoro GitHub o la pipeline di Azure DevOps
* Usare richieste pull per gestire il flusso del codice e mantenere una qualità alta
* Testare e compilare ogni commit prima di unire il codice per aumentare il livello di qualità

Dopo aver usato DevOps Starter, è possibile eliminare tutte le risorse da un'unica posizione, il dashboard di DevOps Starter nel portale di Azure.

## <a name="devops-starter-and-github-integration"></a>Integrazione di DevOps Starter e GitHub

DevOps Starter ora supporta GitHub Actions come provider CI/CD. Automatizza tutte le attività necessarie in GitHub per configurare un flusso di lavoro CI/CD tramite GitHub Actions. Crea un nuovo repository GitHub in un'organizzazione di GitHub al cui interno esegue il commit di un'applicazione di esempio.  

L'automazione stabilisce anche un trigger per il flusso di lavoro, per cui ogni commit di nuovo codice avvia un processo di compilazione e distribuzione all'interno del flusso di lavoro. L'applicazione viene distribuita in un servizio di Azure a scelta. Il flusso di lavoro GitHub può essere personalizzato per scenari aggiuntivi. 

## <a name="devops-starter-and-azure-devops-integration"></a>Integrazione di DevOps Starter e Azure DevOps

DevOps Starter con Azure DevOps automatizza tutte le attività necessarie in Azure Pipelines per configurare una pipeline CI/CD. Crea un repository Git in un'organizzazione Azure DevOps nuova o esistente e quindi esegue il commit di un'applicazione di esempio o di codice esistente in un nuovo repository Git.  

L'automazione stabilisce anche un trigger CI per la compilazione, in modo che ogni nuovo commit di codice avvii una compilazione. DevOps Starter crea un trigger CD e distribuisce ogni nuova compilazione riuscita nel servizio di Azure desiderato.  

È possibile personalizzare le pipeline di compilazione e versione per altri scenari. In più, è possibile clonare le pipeline di compilazione e di versione per usarle in altri progetti.

## <a name="getting-started-with-devops-starter"></a>Introduzione a DevOps Starter

* [Introduzione a DevOps Starter](./azure-devops-project-github.md)

##  <a name="devops-starter-videos"></a>Video su DevOps Starter

* [Creare una pipeline CI/CD con Azure DevOps Starter](https://www.youtube.com/watch?v=NuYDAs3kNV8)
