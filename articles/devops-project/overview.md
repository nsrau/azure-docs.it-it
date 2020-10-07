---
title: Panoramica di Azure DevOps Starter | Microsoft Docs
description: Informazioni su Azure DevOps Starter, che consente di avviare, distribuire e gestire le app da una singola visualizzazione nel portale di Azure.
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
ms.openlocfilehash: 91e62cae242279e1923fc31970c233ec70bf7f11
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "87461341"
---
# <a name="overview-of-azure-devops-starter"></a>Panoramica di Azure DevOps Starter

 Azure DevOps Starter consente di iniziare a usare Azure senza difficoltà. Consente di avviare l'app preferita nel servizio di Azure desiderato in pochi passaggi rapidi dal portale di Azure. 

 DevOps Starter configura tutto il necessario per lo sviluppo, la distribuzione e il monitoraggio di un'applicazione. È possibile usare il dashboard di DevOps Starter per monitorare i commit, le compilazioni e le distribuzioni del codice, il tutto da una singola visualizzazione nel portale di Azure.

## <a name="advantages-of-using-devops-starter"></a>Vantaggi dell'uso di DevOps Starter

  DevOps Starter automatizza la configurazione di un'intera pipeline di integrazione continua (CI) e di distribuzione continua (CD) in Azure.  È possibile iniziare con il codice esistente o usare una delle applicazioni di esempio fornite. Quindi sarà possibile distribuire rapidamente l'applicazione in vari servizi di Azure, ad esempio Macchine virtuali, Servizio app, servizi Azure Kubernetes, Database SQL di Azure e Azure Service Fabric.  

  DevOps Starter esegue tutto il lavoro per la configurazione iniziale di una pipeline DevOps, ovvero tutte le operazioni che spaziano dalla configurazione del repository Git iniziale, la configurazione della pipeline CI/CD, la creazione di una risorsa di Application Insights per il monitoraggio, fino alla presentazione di una singola visualizzazione dell'intera soluzione con la creazione di un dashboard di DevOps Projects nel portale di Azure.

È possibile usare DevOps Starter per:

* Distribuire velocemente l'applicazione in Azure
* Automatizzare la configurazione di una pipeline CI/CD
* Visualizzare e comprendere come configurare correttamente una pipeline CI/CD
* Personalizzare ulteriormente le pipeline di versione in base a scenari specifici

## <a name="how-to-use-devops-starter"></a>Come si usa DevOps Starter?

  DevOps Starter è disponibile nel portale di Azure. Le risorse di DevOps Starter possono essere create come tutte le altre risorse di Azure dal portale. DevOps Projects offre un'esperienza dettagliata simile a una procedura guidata per le varie opzioni di configurazione.  

Si sceglieranno varie opzioni di configurazione come parte della configurazione iniziale. Tali opzioni includono:

* Usare l'app di esempio fornita o usare codice personalizzato
* Selezionare un linguaggio app
* Scegliere un framework app in base al linguaggio
* Selezionare un servizio di Azure (destinazione di distribuzione)
* Creare una nuova organizzazione di Azure DevOps o usare un'organizzazione esistente 
* Scegliere la sottoscrizione di Azure
* Scegliere il percorso dei servizi di Azure
* Scegliere tra vari piani tariffari per i servizi di Azure

Dopo aver usato DevOps Starter, è possibile eliminare tutte le risorse da un'unica posizione, il dashboard di DevOps Starter nel portale di Azure.

## <a name="devops-starter-and-azure-devops-integration"></a>Integrazione di DevOps Starter e Azure DevOps

DevOps Starter è supportato da Azure DevOps. DevOps Starter automatizza tutte le operazioni necessarie in Azure Pipelines per configurare una pipeline CI/CD. Crea un repository Git in un'organizzazione Azure DevOps nuova o esistente e quindi esegue il commit di un'applicazione di esempio o di codice esistente in un nuovo repository Git.  

L'automazione stabilisce anche un trigger CI per la compilazione, in modo che ogni nuovo commit di codice avvii una compilazione. DevOps Starter crea un trigger CD e distribuisce ogni nuova compilazione riuscita nel servizio di Azure desiderato.  

È possibile personalizzare le pipeline di compilazione e versione per altri scenari. In più, è possibile clonare le pipeline di compilazione e di versione per usarle in altri progetti.

Dopo aver creato il progetto DevOps Starter, è possibile:

* Personalizzare la pipeline di compilazione e rilascio
* Usare richieste pull per gestire il flusso del codice e mantenere una qualità alta
* Testare e compilare ogni commit prima di unire il codice per aumentare il livello di qualità
* Registrare il backlog e i problemi insieme all'applicazione

## <a name="getting-started-with-devops-starter"></a>Introduzione a DevOps Starter

* [Introduzione a DevOps Starter](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-starter-videos"></a>Video su DevOps Starter

* [Creare una pipeline CI/CD con Azure DevOps Starter](https://www.youtube.com/watch?v=NuYDAs3kNV8)
