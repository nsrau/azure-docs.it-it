---
title: Panoramica del progetto DevOps di Azure | Microsoft Docs
description: Comprendere il valore del progetto DevOps di Azure
services: devops-project
documentationcenter: ''
author: mlearned
manager: douge
editor: mlearned
ms.assetid: ''
ms.service: devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/03/2018
ms.author: mlearned
ms.openlocfilehash: 39dffad597b8382dea4df6fa1b0726d9582d67d1
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2018
ms.locfileid: "44293627"
---
# <a name="overview-of-azure-devops-project"></a>Panoramica del progetto DevOps di Azure

Il progetto DevOps di Azure permette di iniziare a usare Azure velocemente. La risorsa progetto DevOps consente di avviare il tipo di app preferito nel servizio di Azure desiderato in pochi passaggi rapidi dal portale di Azure. Il progetto DevOps configura tutto ciò che occorre per lo sviluppo, la distribuzione e il monitoraggio di un'applicazione.
Il dashboard del progetto DevOps consente di monitorare i commit, le compilazioni e le distribuzioni del codice, il tutto da una singola visualizzazione nel portale di Azure.

## <a name="why-should-i-use-the-azure-devops-project"></a>Perché usare il progetto DevOps di Azure?

Il progetto DevOps di Azure consente di automatizzare l'installazione di un'intera pipeline di integrazione continua (CI) e di recapito continuo (CD) in Azure.  È possibile iniziare con il codice esistente o usare una delle applicazioni di esempio fornite e quindi distribuire rapidamente l'applicazione in vari servizi di Azure, ad esempio macchine virtuali, servizio app, servizio contenitore di Azure, database SQL di Azure e Azure Service Fabric.  

Il progetto Azure DevOps esegue tutto il lavoro per la configurazione iniziale di una pipeline DevOps, ovvero tutte le operazioni che spaziano dalla configurazione del repository GIT iniziale, la configurazione della pipeline di integrazione continua/distribuzione continua, la creazione di una risorsa di Application Insights per il monitoraggio, fino alla presentazione di una singola visualizzazione dell'intera soluzione con la creazione di un dashboard del progetto Azure DevOps nel portale di Azure.

È possibile usare il progetto DevOps di Azure per:

* Distribuire velocemente l'applicazione in Azure
* Automatizzare la configurazione di una pipeline di integrazione continua/distribuzione continua di Azure
* Usare il progetto DevOps come modello per visualizzare e comprendere come configurare correttamente una pipeline di integrazione continua/distribuzione continua in Azure con Azure DevOps
* Iniziare a usare una pipeline CI e CD in Azure e quindi personalizzare la pipeline in base a scenari specifici

## <a name="how-do-i-use-the-azure-devops-project"></a>Perché si usa il progetto DevOps di Azure?

Il progetto DevOps di Azure è disponibile nel portale di Azure.  Si crea una risorsa progetto Azure DevOps esattamente come si crea qualsiasi altra risorsa Azure dal portale.  Il progetto DevOps offre un'esperienza dettagliata simile a una procedura guidata per le varie opzioni di configurazione.  

Si sceglieranno varie opzioni di configurazione come parte della configurazione iniziale.  Tali opzioni includono:

* Usare l'app di esempio fornita o usare codice personalizzato
* Selezionare un linguaggio app
* Scegliere un framework app in base al linguaggio
* Selezionare un servizio di Azure (destinazione di distribuzione)
* L'organizzazione di Azure DevOps (nuova o esistente)
* Scegliere la sottoscrizione di Azure
* Scegliere il percorso dei servizi di Azure
* Scegliere tra vari piani tariffari per i servizi di Azure

Dopo aver usato il progetto DevOps di Azure, è possibile eliminare tutte le risorse da un'unica posizione, il dashboard del progetto DevOps di Azure nel portale di Azure.

## <a name="azure-devops-project-and-azure-devops-integration"></a>Integrazione del progetto Azure DevOps e di Azure DevOps

I progetti DevOps sono supportati da Azure DevOps.  Il progetto DevOps consente di automatizzare tutte le operazioni necessarie in Azure DevOps per configurare le operazioni di integrazione continua/configurazione continua in Azure.  Viene creato un repository GIT in un'organizzazione di Azure DevOps nuova o esistente.  Il progetto DevOps esegue il commit di un'applicazione di esempio o di codice esistente in un nuovo repository GIT.  L'automazione stabilisce inoltre un trigger CI per la compilazione, in modo che ogni nuovo commit di codice avvii una compilazione.  Il progetto DevOps crea anche un trigger di distribuzione continua e distribuirà ogni nuova compilazione riuscita nel servizio di Azure desiderato.  È possibile personalizzare le pipeline di compilazione e versione per altri scenari.  È possibile anche clonare le pipeline di compilazione e di versione per usarle in altri progetti.

Dopo aver creato il progetto DevOps, è possibile:

* Personalizzare la pipeline di compilazione e rilascio
* Usare richieste pull per gestire il flusso del codice e mantenere una qualità alta
* Testare e compilare ogni commit prima di unire il codice per aumentare il livello di qualità
* Registrare il backlog e i problemi del progetto insieme all'applicazione

## <a name="how-do-i-start-using-the-azure-devops-project"></a>Perché iniziare a usare il progetto DevOps di Azure?

* [Iniziare a usare il progetto DevOps di Azure](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

## <a name="azure-devops-project-videos"></a>Video del progetto DevOps di Azure

* [Creare integrazione continua e distribuzione continua con il progetto DevOps di Azure](https://channel9.msdn.com/Events/Connect/2017/T174/player/)
