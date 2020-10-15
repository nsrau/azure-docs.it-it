---
title: 'Esercitazione: Distribuire app ASP.NET in Funzioni di Azure con Azure DevOps Starter'
description: Azure DevOps Starter consente di iniziare a usare Azure senza difficoltà. Con DevOps Starter è possibile distribuire un'app ASP.NET in Funzioni di Azure in pochi rapidi passaggi.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: devx-track-csharp
ms.openlocfilehash: 3277fefb5615fd492b2859b741ef8e6f991eaf23
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91355475"
---
# <a name="deploy-to-azure-functions-with-devops-starter"></a>Eseguire la distribuzione in Funzioni di Azure con DevOps Starter

Azure DevOps Starter offre un'esperienza semplificata in cui è possibile usare il codice esistente e il repository Git oppure scegliere un'applicazione di esempio per creare una pipeline di integrazione continua (CI, Continuous Integration) e recapito continuo (CD, Continuous Delivery) in Azure.

DevOps Starter consente inoltre di effettuare le seguenti operazioni:

* Creare automaticamente risorse di Azure, ad esempio Funzioni di Azure

* Creare e configurare una pipeline di versione in Azure DevOps per CI/CD

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
>* Usare DevOps Starter per distribuire un'app ASP.NET in una funzione di Azure
>* Configurare Azure DevOps e una sottoscrizione di Azure
>* Esaminare la funzione di Azure
>* Esaminare la pipeline di integrazione continua
>* Esaminare la pipeline di distribuzione continua
>* Eseguire il commit delle modifiche in Git e la distribuzione automatica in Azure
>* Pulire le risorse

Attualmente i runtime supportati per le funzioni sono **.NET** e **Node.js**. Per questa esercitazione si usa il runtime .NET per distribuire in Funzioni di Azure. 

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. È possibile ottenerne una gratuita tramite [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-starter-to-deploy-an-aspnet-app-to-azure-functions"></a>Usare DevOps Starter per distribuire un'app ASP.NET in Funzioni di Azure

DevOps Starter crea una pipeline CI/CD in Azure Pipelines. È possibile creare una nuova organizzazione di Azure DevOps o usare un'organizzazione esistente. DevOps Projects crea anche risorse di Azure, ad esempio un hub IoT, nella sottoscrizione di Azure di propria scelta.

1. Accedere al [portale di Azure](https://portal.azure.com)

1. Nella casella di ricerca digitare **DevOps Starter** e quindi selezionare. Fare clic su **Aggiungi** per crearne una nuova.

    ![Dashboard di DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Selezionare **.NET**, quindi **Avanti**. In **Scegli un framework applicazione** selezionare **ASP.NET** e fare clic su **Avanti**.

1. Selezionare **App per le funzioni** e quindi selezionare **Avanti**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Configurare Azure DevOps e una sottoscrizione di Azure

1. Immettere un nome per il progetto Azure DevOps.

1. Creare una nuova organizzazione di Azure DevOps o selezionare un'organizzazione esistente.

1. Selezionare la sottoscrizione di Azure.

1. Per visualizzare le impostazioni di configurazione aggiuntive di Azure e identificare il piano tariffario e la posizione, fare clic su **Impostazioni aggiuntive**. Questo riquadro mostra diverse opzioni per la configurazione del piano tariffario e della posizione dei servizi di Azure.

1. Chiudere l'area di configurazione di Azure e selezionare **Fine**.

1. Il processo verrà completato dopo alcuni minuti. Viene configurata un'app di esempio ASP.NET in un repository Git nell'organizzazione di Azure DevOps, vengono creati un'app per le funzioni e Application Insights, viene eseguita una pipeline CI/CD e l'app viene distribuita in Azure.

   Al termine, il dashboard di Azure DevOps Starter viene visualizzato nel portale di Azure. È anche possibile passare al dashboard di DevOps Starter direttamente da **Tutte le risorse** nel portale di Azure.

   Questo dashboard fornisce visibilità su repository di codice di Azure DevOps, pipeline CI/CD e la funzione di Azure. È possibile configurare le opzioni aggiuntive di CI/CD nella pipeline di Azure DevOps. A destra selezionare **App per le funzioni** per visualizzarla.

## <a name="examine-the-function-app"></a>Esaminare l'app per le funzioni

DevOps Starter configura automaticamente l'app per le funzioni, che è possibile esplorare e personalizzare. Per familiarizzare con l'app per le funzioni seguire questa procedura:

1. Passare al dashboard di DevOps Starter.

    ![Dashboard di DevOps Projects](_img/azure-devops-project-functions/fapp-dashboard.png)

1. A destra selezionare l'app per le funzioni. Viene visualizzato un riquadro per l'app per le funzioni. Da questa visualizzazione è possibile eseguire varie azioni, ad esempio monitorare le operazioni ed eseguire ricerche nei log.

    ![App per le funzioni](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>Esaminare la pipeline di integrazione continua

DevOps Starter configura automaticamente una pipeline CI/CD nell'organizzazione di Azure DevOps. È possibile esplorare e personalizzare la pipeline. Per acquisire familiarità, eseguire questa procedura:

1. Passare al dashboard di DevOps Starter.

1. Fare clic sul collegamento ipertestuale sotto **Compilare**. In una scheda del browser viene visualizzata la pipeline di compilazione per il nuovo progetto.

    ![Screenshot che mostra il dashboard DevOps Starter nel portale di Azure con una freccia che punta a un collegamento sotto Compila nella pipeline CI/CD.](_img/azure-devops-project-functions/build.png)

1. Selezionare **Modifica**. In questo riquadro è possibile esaminare le diverse attività per la pipeline di compilazione. La compilazione esegue diverse attività, ad esempio il recupero del codice sorgente dal repository Git, la compilazione dell'applicazione, l'esecuzione di unit test e la pubblicazione degli output usati per le distribuzioni.

1. Selezionare **Trigger**. DevOps Starter crea automaticamente un trigger di integrazione continua e a ogni commit nel repository viene avviata una nuova compilazione. Facoltativamente, è possibile scegliere di includere o escludere rami dal processo di integrazione continua.

1. Selezionare **Conservazione**. A seconda dello scenario specifico, è possibile indicare i criteri per conservare o rimuovere un determinato numero di compilazioni.

1. Nella parte superiore della pipeline di compilazione selezionare il nome della pipeline di compilazione.

1. Modificare il nome della pipeline di compilazione scegliendo un testo più descrittivo, quindi selezionare **Salva** dall'elenco a discesa **Salva e accoda**.

1. Sotto il nome della pipeline di compilazione selezionare **Cronologia**. Questo riquadro mostra un log di controllo delle modifiche recenti per la compilazione. Azure DevOps tiene traccia di tutte le modifiche apportate alla pipeline di compilazione e consente di confrontare le versioni.

## <a name="examine-the-cd-release-pipeline"></a>Esaminare la pipeline di versione CD

DevOps Starter crea e configura automaticamente i passaggi necessari per la distribuzione dall'organizzazione di Azure DevOps alla sottoscrizione di Azure. Questi passaggi includono la configurazione di una connessione al servizio di Azure per eseguire l'autenticazione di Azure DevOps nella sottoscrizione di Azure. L'automazione crea anche una pipeline di versione che fornisce la distribuzione continua in Azure. Per altre informazioni sulla pipeline di versione, eseguire le operazioni seguenti:

1. Passare a **Pipeline | Versioni**.

1. Fare clic su **Edit** (Modifica).

1. In **Artefatti** selezionare **Elimina**. La pipeline di compilazione esaminata nei passaggi precedenti produce l'output usato per l'artefatto.

1. A destra dell'icona **Elimina** selezionare **Trigger di distribuzione continua**. Questa pipeline di versione ha un trigger di distribuzione continua abilitato, che esegue una distribuzione ogni volta che è disponibile un nuovo artefatto di compilazione. Facoltativamente, è possibile disabilitare il trigger, in modo che le distribuzioni richiedano l'esecuzione manuale.

1. A destra, selezionare **Visualizza versioni** per visualizzare una cronologia delle versioni.

1. Fare clic sulla versione, in cui viene visualizzata la pipeline. Fare clic su qualsiasi ambiente per verificare **il riepilogo e i commit** della versione e gli **elementi di lavoro** associati.

1. Selezionare **Commit**. Questa visualizzazione mostra i commit di codice associati a questa distribuzione. È possibile confrontare le versioni per visualizzare le differenze di commit tra le distribuzioni.

1. Selezionare **Visualizza log**. I log contengono informazioni utili sul processo di distribuzione. È possibile visualizzarli durante e dopo le distribuzioni.

## <a name="commit-code-changes-and-execute-cicd"></a>Eseguire il commit delle modifiche al codice e la pipeline di CI/CD

> [!NOTE]
> La procedura seguente verifica la pipeline CI/CD, apportando una modifica di testo semplice.

A questo punto, si è pronti per collaborare con un team a un'app usando un processo di CI/CD che distribuisce automaticamente il lavoro più recente nella funzione di Azure. Ogni modifica al repository Git avvia una compilazione in DevOps di Azure e una pipeline CD esegue una distribuzione in Azure. Seguire la procedura in questa sezione o usare altre tecniche per eseguire il commit delle modifiche nel repository. Ad esempio, è possibile clonare il repository Git nello strumento o nell'IDE preferito e quindi eseguire il push delle modifiche in questo repository.

1. Nel menu di Azure DevOps, selezionare **Repos | File** e quindi passare al repository.

1. Il repository contiene già il codice denominato **SampleFunctionApp** basato sulla lingua dell'applicazione che si è scelta nel processo di creazione. Aprire il file **Application/SampleFunctionApp/Function1.cs**.

1. Selezionare **Modifica** e quindi apportare una modifica al **numero di riga 31**. Ad esempio, è possibile aggiornarlo con **Salve! Benvenuti in Funzioni di Azure con DevOps Starter**

1. In alto a destra, selezionare **Commit**, quindi selezionare **Commit** nuovamente per eseguire il push della modifica.

1. Aprire il file **Application/SampleFunctionApp.Test/Function1TestRunner.cs**. 

1. Selezionare **Modifica** e quindi apportare una modifica al **numero di riga 21**. Ad esempio, è possibile aggiornarlo con **Salve! Benvenuti in Funzioni di Azure con Azure DevOps Starter**.

     Dopo alcuni istanti verrà avviata una compilazione in Azure DevOps e verrà eseguita una versione per distribuire le modifiche. Monitorare lo stato della compilazione nel dashboard di DevOps Starter oppure nel browser con l'organizzazione di Azure DevOps.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile eliminare le risorse correlate create in precedenza. Usare la funzionalità **Elimina** del dashboard di DevOps Starter.

## <a name="next-steps"></a>Passaggi successivi

Facoltativamente, è possibile modificare queste pipeline di compilazione e di versione in base alle esigenze del team. È anche possibile usare questo modello di CI/CD come modello per altre pipeline. In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Usare DevOps Starter per distribuire un'app ASP.NET Core in una funzione di Azure
> * Configurare Azure DevOps e una sottoscrizione di Azure 
> * Esaminare la funzione di Azure
> * Esaminare la pipeline di integrazione continua
> * Esaminare la pipeline di distribuzione continua
> * Eseguire il commit delle modifiche in Git e la distribuzione automatica in Azure
> * Pulire le risorse

