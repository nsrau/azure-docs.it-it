---
title: Panoramica dello starter di Azure DevOps | Microsoft Docs
description: Informazioni sul valore per Azure DevOps Starter
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
ms.openlocfilehash: 99a1fdb8caff9953041c996d0f5581318ce11c66
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82233687"
---
# <a name="overview-of-azure-devops-starter"></a>Panoramica dello starter di Azure DevOps

 Azure DevOps Starter consente di iniziare a usare Azure senza difficoltà. Consente di avviare l'app preferita nel servizio di Azure desiderato in pochi passaggi rapidi dal portale di Azure. 

 DevOps Starter configura tutti gli elementi necessari per lo sviluppo, la distribuzione e il monitoraggio dell'applicazione. È possibile usare il Dashboard Starter di DevOps per monitorare i commit del codice, le compilazioni e le distribuzioni, da una singola visualizzazione nel portale di Azure.

## <a name="advantages-of-using-devops-starter"></a>Vantaggi dell'uso di DevOps Starter

  DevOps Starter consente di automatizzare l'installazione di un'intera pipeline di integrazione continua e recapito continuo (CD) in Azure.  È possibile iniziare con il codice esistente o usare una delle applicazioni di esempio fornite. Quindi sarà possibile distribuire rapidamente l'applicazione in vari servizi di Azure, ad esempio Macchine virtuali, Servizio app, servizi Azure Kubernetes, Database SQL di Azure e Azure Service Fabric.  

  DevOps Starter esegue tutte le operazioni necessarie per la configurazione iniziale di una pipeline DevOps, inclusi tutti gli elementi, dall'impostazione del repository git iniziale, alla configurazione della pipeline CI/CD, alla creazione di una risorsa Application Insights per il monitoraggio e alla creazione di una singola visualizzazione dell'intera soluzione con la creazione di un dashboard DevOps Projects nel portale di Azure.

È possibile usare DevOps Starter per:

* Distribuire velocemente l'applicazione in Azure
* Automatizzare la configurazione di una pipeline CI/CD
* Visualizzare e comprendere come configurare correttamente una pipeline CI/CD
* Personalizzare ulteriormente le pipeline di versione in base a scenari specifici

## <a name="how-to-use-devops-starter"></a>Come si usa DevOps Starter?

  DevOps Starter è disponibile dal portale di Azure. Si crea una risorsa Starter di DevOps proprio come si crea qualsiasi altra risorsa di Azure dal portale. DevOps Projects offre un'esperienza dettagliata simile a una procedura guidata per le varie opzioni di configurazione.  

Si sceglieranno varie opzioni di configurazione come parte della configurazione iniziale. Tali opzioni includono:

* Usare l'app di esempio fornita o usare codice personalizzato
* Selezionare un linguaggio app
* Scegliere un framework app in base al linguaggio
* Selezionare un servizio di Azure (destinazione di distribuzione)
* Creare una nuova organizzazione di Azure DevOps o usare un'organizzazione esistente 
* Scegliere la sottoscrizione di Azure
* Scegliere il percorso dei servizi di Azure
* Scegliere tra vari piani tariffari per i servizi di Azure

Dopo aver usato DevOps Starter, è anche possibile eliminare tutte le risorse da un'unica posizione dal Dashboard Starter di DevOps nel portale di Azure.

## <a name="devops-starter-and-azure-devops-integration"></a>DevOps Starter e Azure DevOps Integration

DevOps Starter è alimentato da Azure DevOps. DevOps Starter consente di automatizzare tutto il lavoro necessario in Azure Pipelines per configurare una pipeline CI/CD. Crea un repository Git in un'organizzazione Azure DevOps nuova o esistente e quindi esegue il commit di un'applicazione di esempio o di codice esistente in un nuovo repository Git.  

L'automazione stabilisce anche un trigger CI per la compilazione, in modo che ogni nuovo commit di codice avvii una compilazione. DevOps Starter crea un trigger CD e distribuisce ogni nuova compilazione riuscita al servizio di Azure di propria scelta.  

È possibile personalizzare le pipeline di compilazione e versione per altri scenari. In più, è possibile clonare le pipeline di compilazione e di versione per usarle in altri progetti.

Dopo aver creato lo starter DevOps, è possibile:

* Personalizzare la pipeline di compilazione e rilascio
* Usare richieste pull per gestire il flusso del codice e mantenere una qualità alta
* Testare e compilare ogni commit prima di unire il codice per aumentare il livello di qualità
* Registrare il backlog e i problemi insieme all'applicazione

## <a name="getting-started-with-devops-starter"></a>Introduzione a DevOps Starter

* [Introduzione a DevOps Starter](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-starter-videos"></a>Video introduttivi su DevOps

* [Creare CI/CD con Azure DevOps Starter](https://www.youtube.com/watch?v=NuYDAs3kNV8)
