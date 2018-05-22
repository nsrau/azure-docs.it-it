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
ms.openlocfilehash: b87cb14fc707d14638c2d6a52af6f295276a2279
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="overview-of-azure-devops-project"></a>Panoramica del progetto DevOps di Azure

Il progetto DevOps di Azure permette di iniziare a usare Azure velocemente. La risorsa progetto DevOps consente di avviare il tipo di app preferito nel servizio di Azure desiderato in pochi passaggi dal portale di Azure. Il progetto DevOps configura tutto ciò che occorre per lo sviluppo, la distribuzione e il monitoraggio di un'applicazione.
Il dashboard del progetto DevOps consente di monitorare i commit, le compilazioni e le distribuzioni del codice, il tutto da una singola visualizzazione nel portale di Azure.

## <a name="why-should-i-use-the-azure-devops-project"></a>Perché usare il progetto DevOps di Azure?

Il progetto DevOps di Azure consente di automatizzare l'installazione di un'intera pipeline di integrazione continua (CI) e di recapito continuo (CD) in Azure.  È possibile iniziare con il codice esistente o usare una delle applicazioni di esempio fornite e quindi distribuire rapidamente l'applicazione in vari servizi di Azure, ad esempio macchine virtuali, servizio app, servizio contenitore di Azure, database SQL di Azure e Azure Service Fabric.  

Il progetto DevOps di Azure esegue tutto il lavoro per la configurazione iniziale di una pipeline DevOps, ovvero tutte le operazioni che spaziano dalla configurazione del repository Git iniziale, la configurazione della pipeline CI/CD, la creazione di una risorsa Application Insights per il monitoraggio, fino alla presentazione di una singola visualizzazione dell'intera soluzione con la creazione di un dashboard del progetto DevOps di Azure nel portale di Azure.

È possibile usare il progetto DevOps di Azure per:

* Distribuire velocemente l'applicazione in Azure
* Automatizzare la configurazione di una pipeline CI o CD con VSTS
* Usare il progetto DevOps come modello per visualizzare e comprendere come configurare correttamente CI e CD in Azure con Visual Studio Team Services
* Iniziare a usare una pipeline CI e CD in Azure e quindi personalizzare la pipeline in base a scenari specifici

## <a name="how-do-i-use-the-azure-devops-project"></a>Perché si usa il progetto DevOps di Azure?

Il progetto DevOps di Azure è disponibile nel portale di Azure.  Si crea una risorsa progetto DevOps di Azure esattamente come qualsiasi altra risorsa di Azure dal portale.  Il progetto DevOps offre un'esperienza passo per passo simile a una procedura guidata per le varie opzioni di configurazione.  

Si sceglieranno varie opzioni di configurazione come parte della configurazione iniziale.  Tali opzioni includono:

* Usare l'app di esempio fornita o usare codice personalizzato
* Selezionare un linguaggio app
* Scegliere un framework app in base al linguaggio
* Selezionare un servizio di Azure (destinazione di distribuzione)
* Account VSTS (nuovo o esistente)
* Scegliere la sottoscrizione di Azure
* Scegliere il percorso dei servizi di Azure
* Scegliere tra vari piani tariffari per i servizi di Azure

Dopo aver usato il progetto DevOps di Azure, è possibile eliminare tutte le risorse da un'unica posizione, il dashboard del progetto DevOps di Azure nel portale di Azure.

## <a name="azure-devops-project-and-vsts-integration"></a>Integrazione di Visual Studio Team Services e il progetto DevOps di Azure

I progetti DevOps sono supportati da Visual Studio Team Services.  Il progetto DevOps consente di automatizzare tutte le operazioni necessarie in Visual Studio Team Services per configurare CI e CD in Azure.  Viene creato un repository Git in un account VSTS nuovo o esistente.  Il progetto DevOps esegue il commit di un'applicazione di esempio o di codice esistente in un nuovo repository Git.  L'automazione stabilisce inoltre un trigger CI per la compilazione, in modo che ogni nuovo commit di codice avvii una compilazione.  Il progetto DevOps crea anche un trigger CD e distribuirà ogni nuova compilazione riuscita al servizio di Azure desiderato.  È possibile personalizzare le definizioni di compilazione e rilascio per altri scenari.  Si può anche duplicare le definizioni di compilazione e di rilascio per usarle in altri progetti.

Dopo aver creato il progetto DevOps, è possibile:

* Personalizzare la pipeline di compilazione e rilascio
* Usare richieste pull per gestire il flusso del codice e mantenere una qualità alta
* Testare e compilare ogni commit prima di unire il codice per aumentare il livello di qualità
* Registrare il backlog e i problemi del progetto insieme all'applicazione

## <a name="how-do-i-start-using-the-azure-devops-project"></a>Perché iniziare a usare il progetto DevOps di Azure?

* [Iniziare a usare il progetto DevOps di Azure](https://docs.microsoft.com/vsts/build-release/actions/azure-devops-project-github)

## <a name="azure-devops-project-videos"></a>Video del progetto DevOps di Azure

* [Creare integrazione continua e distribuzione continua con il progetto DevOps di Azure](https://channel9.msdn.com/Events/Connect/2017/T174/player/)
