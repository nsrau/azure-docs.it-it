---
title: 'Esercitazione: Creare una pipeline CI/CD per il codice esistente usando Azure DevOps Projects'
description: Azure DevOps Projects permette di iniziare a usare Azure velocemente. DevOps Projects consente di usare il proprio codice e repository di GitHub per avviare un'app in un servizio di Azure di propria scelta in pochi rapidi passaggi.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 88ee15a3b5cc53542d9e098dee485b8a526bb9a6
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52161754"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Esercitazione: Creare una pipeline CI/CD per il codice esistente usando Azure DevOps Projects

Azure DevOps Projects offre un'esperienza semplificata in cui è possibile usare il codice esistente e il repository Git oppure scegliere un'applicazione di esempio per creare una pipeline di integrazione continua (CI, Continuous Integration) e recapito continuo (CD, Continuous Delivery) in Azure.

Si apprenderà come:

> [!div class="checklist"]
> * Usare DevOps Projects per creare una pipeline CI/CD
> * Configurare l'accesso al repository GitHub e scegliere un framework
> * Configurare Azure DevOps e una sottoscrizione di Azure 
> * Eseguire il commit delle modifiche in GitHub e la distribuzione automatica in Azure
> * Esaminare la pipeline CI/CD di Azure Pipelines
> * Configurare il monitoraggio di Azure Application Insights
> * Pulire le risorse

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. È possibile ottenerne una gratuita tramite [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Accedere a GitHub o a un repository Git esterno contenente codice .NET, Java, PHP, Nodo, Python o Web statico.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Azure DevOps Projects crea una pipeline CI/CD in Azure Pipelines. È possibile creare una nuova organizzazione di Azure DevOps o usare un'organizzazione esistente. Azure DevOps Projects crea anche risorse di Azure nella sottoscrizione di Azure di propria scelta.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel riquadro a sinistra selezionare **Nuovo**.

1. Nella casella di ricerca, digitare **DevOps Projects**, quindi selezionare **Crea**.

    ![Dashboard di DevOps Projects](_img/azure-devops-project-github/fullbrowser.png)

1. Selezionare **Usa il codice personale** e quindi selezionare **Avanti**.

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>Configurare l'accesso al repository GitHub e scegliere un framework

1. Selezionare **GitHub** oppure un repository Git esterno, quindi selezionare il proprio repository e il ramo che contiene l'applicazione.

1. Selezionare il framework Web, quindi selezionare **Avanti**.

    ![.NET Framework](_img/azure-devops-project-github/webframework.png)

    Il framework applicazione, scelto in precedenza, determina il tipo di destinazione della distribuzione del servizio di Azure disponibile. 
    
1. Selezionare il servizio di destinazione, quindi selezionare **Avanti**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurare Azure DevOps e una sottoscrizione di Azure 

1. Creare una nuova organizzazione di Azure DevOps o selezionare un'organizzazione esistente.

    a. Immettere un nome per il progetto in Azure DevOps. 
    
    b. Selezionare la sottoscrizione di Azure e la posizione, immettere un nome per l'applicazione, quindi selezionare **Fine**.

    Dopo pochi minuti, il dashboard di DevOps Projects viene visualizzato nel portale di Azure. Viene configurata un'applicazione di esempio in un repository nell'organizzazione Azure DevOps, viene eseguita una compilazione e l'applicazione viene distribuita in Azure. Questo dashboard fornisce visibilità sul repository di codice GitHub, la pipeline CI/CD e l'applicazione in Azure. 
    
1. Selezionare **Sfoglia** per visualizzare l'applicazione in esecuzione.

    ![Visualizzazione dashboard di DevOps Projects](_img/azure-devops-project-github/dashboardnopreview.png) 
    
Azure DevOps Projects configura automaticamente un trigger di compilazione e di versione di integrazione continua. Il codice rimane nel repository GitHub o in un altro repository esterno. 

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Eseguire il commit delle modifiche in GitHub e la distribuzione automatica in Azure 

A questo punto, si è pronti per collaborare con un team a un'app usando un processo di CI/CD che distribuisce automaticamente il lavoro più recente nel sito Web. Ogni modifica al repository GitHub avvia una compilazione in DevOps di Azure e una pipeline CD esegue una distribuzione in Azure.

1. Apportare una modifica all'applicazione e quindi eseguire il commit della modifica nel repository GitHub.  
    Dopo alcuni istanti, viene avviata una compilazione in Azure Pipelines. È possibile monitorare lo stato della compilazione nel dashboard di DevOps Projects oppure nel browser con l'organizzazione di Azure DevOps.

1. Quando la compilazione sarà completa, aggiornare l'applicazione per verificare le modifiche.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Esaminare la pipeline CI/CD di Azure Pipelines

Azure DevOps Projects configura automaticamente una pipeline CI/CD in Azure Pipelines. Esplorare e personalizzare la pipeline in base alle esigenze. Per acquisire familiarità con le pipeline di compilazione e di versione eseguire questa procedura:

1. Selezionare **Pipeline di compilazione** nella parte superiore di DevOps Projects.  
    In una scheda del browser viene visualizzata la pipeline di compilazione per il nuovo progetto.

1. Puntare al campo **Stato** e quindi selezionare i puntini di sospensione (...).  
    Un menu mostra diverse opzioni, ad esempio accodare una nuova compilazione, sospendere una compilazione e modificare la pipeline di compilazione.

1. Selezionare **Modifica**.

1. In questo riquadro è possibile esaminare le diverse attività per la pipeline di compilazione.  
    La compilazione esegue diverse attività, ad esempio il recupero delle origini dal repository Git, il ripristino delle dipendenze e la pubblicazione degli output usati per le distribuzioni.

1. Nella parte superiore della pipeline di compilazione selezionare il nome della pipeline di compilazione.

1. Modificare il nome della pipeline di compilazione scegliendo un testo più descrittivo, selezionare **Salva e accoda**, quindi selezionare **Salva**.

1. Sotto il nome della pipeline di compilazione selezionare **Cronologia**.  
    Verrà visualizzato un log di controllo delle modifiche recenti per la compilazione. Azure DevOps tiene traccia di tutte le modifiche apportate alla pipeline di compilazione e consente di confrontare le versioni.

1. Selezionare **Trigger**.  
    Azure DevOps Projects crea automaticamente un trigger di integrazione continua e ogni commit nel repository avvia una nuova compilazione. Facoltativamente, è possibile scegliere di includere o escludere rami dal processo di integrazione continua.

1. Selezionare **Conservazione**.  
        A seconda dello scenario specifico, è possibile indicare i criteri per conservare o rimuovere un determinato numero di compilazioni.

1. Selezionare **Compilazione e versione** e quindi **Versioni**.  
    Azure DevOps Projects crea una pipeline di versione per gestire le distribuzioni in Azure.

1. Selezionare i puntini di sospensione (...) accanto alla pipeline di versione e quindi scegliere **Modifica**.  
    La pipeline di versione contiene una *pipeline* che definisce il processo per la versione. 
    
1. In **Artefatti** selezionare **Elimina**.  
    La pipeline di compilazione esaminata nei passaggi precedenti produce l'output usato per l'artefatto. 

1. Accanto all'icona **Elimina** selezionare **Trigger di distribuzione continua**.  
    Questa pipeline di versione ha un trigger di distribuzione continua abilitato, che esegue una distribuzione ogni volta che è disponibile un nuovo artefatto di compilazione. Facoltativamente, è possibile disabilitare il trigger, in modo che le distribuzioni richiedano l'esecuzione manuale. 

1. A sinistra selezionare **Attività**.  
    Le attività sono le operazioni eseguite dal processo di distribuzione. In questo esempio è stata creata un'attività per la distribuzione nel servizio app di Azure.

1. A destra, selezionare **Visualizza versioni** per visualizzare una cronologia delle versioni.

1. Selezionare i puntini di sospensione (...) accanto a una versione e quindi selezionare **Apri**.  
    Ci sono diversi menu da esplorare, ad esempio un riepilogo delle versioni, gli elementi di lavoro associati e i test.

1. Selezionare **Commit**.  
    Questa visualizzazione mostra i commit di codice associati a questa distribuzione. 

1. Selezionare **Log**.  
    I log contengono informazioni utili sul processo di distribuzione. È possibile visualizzarli durante e dopo le distribuzioni.

## <a name="configure-azure-application-insights-monitoring"></a>Configurare il monitoraggio di Azure Application Insights

Con Azure Application Insights, è possibile monitorare facilmente le prestazioni e l'utilizzo dell'applicazione. Azure DevOps Projects configura automaticamente una risorsa di Application Insights per l'applicazione. È possibile configurare ulteriormente varie funzionalità di avviso e di monitoraggio in base alle esigenze.

1. Nel portale di Azure passare al dashboard di DevOps Projects. 

1. In basso a destra, selezionare il collegamento **Application Insights** per l'app.  
    Viene visualizzato il riquadro di **Application Insights**. Questa visualizzazione contiene informazioni di monitoraggio sull'utilizzo, le prestazioni e la disponibilità per l'app.

    ![Riquadro di Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Selezionare **Intervallo di tempo** e quindi selezionare **Ultima ora**. Selezionare **Aggiorna** per filtrare i risultati.  
    È ora possibile visualizzare tutte le attività degli ultimi 60 minuti. Selezionare la **x** per uscire dall'intervallo di tempo.

1. Selezionare **Avvisi** e quindi selezionare **Aggiungi avviso per la metrica**. 

1. Immettere un nome per l'avviso.

1. Nell'elenco a discesa per l'**origine dell'avviso**, selezionare la **risorsa del servizio app**. <!-- Please confirm whether this should be "Source Alter on" or "Source Alert on" -->

1. Nell'elenco a discesa **Metrica**, esaminare le varie metriche di avviso.  
    L'avviso predefinito è per un **tempo di risposta del server superiore a 1 secondo**. È possibile configurare facilmente un'ampia gamma di avvisi per migliorare le funzionalità di monitoraggio dell'app.

1. Selezionare la casella di controllo **Invia messaggio di posta elettronica a proprietari, collaboratori e lettori**.  
    Facoltativamente, è possibile eseguire azioni aggiuntive quando viene visualizzato un avviso tramite l'esecuzione di un'app per la logica di Azure.

1. Fare clic su **OK** per creare l'avviso.  
    Dopo alcuni istanti, l'avviso viene visualizzato come attivo nel dashboard.
    
1. Fare clic su **Esci** per uscire dall'area Avvisi e tornare al riquadro di **Application Insights**.

1. Selezionare **Disponibilità** e quindi selezionare **Aggiungi test**. 

1. Immettere il nome di un test e quindi selezionare **Crea**.  
    Viene creato un semplice test ping per verificare la disponibilità dell'applicazione. Dopo alcuni minuti, i risultati dei test sono disponibili e nel dashboard di Application Insights viene visualizzato uno stato di disponibilità.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, è possibile eliminare il servizio app Azure e le risorse correlate create in questa esercitazione. A tale scopo, usare la funzionalità **Elimina** nel dashboard di DevOps Projects.

## <a name="next-steps"></a>Passaggi successivi

Quando è stato configurato il processo di CI/CD in questa esercitazione, sono state create automaticamente pipeline di compilazione e di versione in Azure DevOps Projects. È possibile modificare queste pipeline di compilazione e di versione in base alle esigenze del team. Si è appreso come:

> [!div class="checklist"]
> * Usare DevOps Projects per creare una pipeline CI/CD
> * Configurare l'accesso al repository GitHub e scegliere un framework
> * Configurare Azure DevOps e una sottoscrizione di Azure 
> * Eseguire il commit delle modifiche in GitHub e la distribuzione automatica in Azure
> * Esaminare la pipeline CI/CD di Azure Pipelines
> * Configurare il monitoraggio di Azure Application Insights
> * Pulire le risorse

Per altre informazioni sulla pipeline CI/CD, vedere:

> [!div class="nextstepaction"]
> [Definire la pipeline di distribuzione continua (CD) in più fasi](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
