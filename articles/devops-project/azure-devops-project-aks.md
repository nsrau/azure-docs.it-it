---
title: 'Esercitazione: Distribuire app ASP.NET Core nel servizio Azure Kubernetes usando Azure DevOps Projects'
description: Azure DevOps Projects permette di iniziare a usare Azure velocemente. Con DevOps Projects è possibile distribuire un'app ASP.NET Core con il servizio Azure Kubernetes in pochi rapidi passaggi.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: b27d56d78296dc5500f97802f811a8923c4e87a8
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71969679"
---
# <a name="tutorial-deploy-aspnet-core-apps-to-azure-kubernetes-service-with-azure-devops-projects"></a>Esercitazione: Distribuire app ASP.NET Core nel servizio Azure Kubernetes usando Azure DevOps Projects

Azure DevOps Projects offre un'esperienza semplificata in cui è possibile usare il codice esistente e il repository Git oppure scegliere un'applicazione di esempio per creare una pipeline di integrazione continua (CI, Continuous Integration) e recapito continuo (CD, Continuous Delivery) in Azure. 

DevOps Projects permette anche di:
* Creare automaticamente risorse di Azure, ad esempio un servizio Azure Kubernetes.
* Creare e configurare una pipeline di versione in Azure DevOps per configurare una compilazione e pipeline di versione per CI/CD.
* Creare una risorsa di Azure Application Insights per il monitoraggio.
* Consente a [Monitoraggio di Azure per i contenitori](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview) di monitorare le prestazioni per i carichi di lavoro dei contenitori nel cluster del servizio Azure Kubernetes

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Usare DevOps Projects per distribuire un'app ASP.NET Core in servizio Azure Kubernetes
> * Configurare Azure DevOps e una sottoscrizione di Azure 
> * Esaminare il cluster del servizio Azure Kubernetes
> * Esaminare la pipeline di integrazione continua
> * Esaminare la pipeline di distribuzione continua
> * Eseguire il commit delle modifiche in Git e la distribuzione automatica in Azure
> * Pulire le risorse

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. È possibile ottenerne una gratuita tramite [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-projects-to-deploy-an-aspnet-core-app-to-aks"></a>Usare DevOps Projects per distribuire un'app ASP.NET Core in servizio Azure Kubernetes

DevOps Projects crea una pipeline CI/CD in Azure Pipelines. È possibile creare una nuova organizzazione di Azure DevOps o usare un'organizzazione esistente. DevOps Projects crea anche risorse di Azure, ad esempio un cluster servizio Azure Kubernetes, nella sottoscrizione di Azure di propria scelta.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel riquadro a sinistra selezionare **Crea risorsa**.

1. Nella casella di ricerca, digitare **DevOps Projects**, quindi selezionare **Crea**.

    ![Dashboard di DevOps Projects](_img/azure-devops-project-github/fullbrowser.png)

1. Selezionare **.NET**, quindi **Avanti**.

1. In **Scegli un framework applicazione** selezionare **ASP.NET Core**.

1. Selezionare **Servizio Kubernetes**, quindi **Avanti**. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurare Azure DevOps e una sottoscrizione di Azure

1. Creare una nuova organizzazione di Azure DevOps o selezionare un'organizzazione esistente. 

1. Immettere un nome per il progetto Azure DevOps. 

1. Selezionare la sottoscrizione di Azure.

1. Per visualizzare le impostazioni di configurazione aggiuntive di Azure e identificare il numero di nodi per il cluster servizio Azure Kubernetes, selezionare **Modifica**.  
    Questo riquadro mostra diverse opzioni per la configurazione del tipo e della posizione dei servizi di Azure.
 
1. Chiudere l'area di configurazione di Azure e selezionare **Fine**.  
    Il processo verrà completato dopo alcuni minuti. Viene configurata un'app di esempio ASP.NET Core in un repository Git nell'organizzazione di Azure DevOps, viene creato un cluster servizio Azure Kubernetes, viene eseguita una pipeline CI/CD e l'applicazione viene distribuita in Azure. 

    Al termine, il dashboard di Azure DevOps Projects viene visualizzato nel portale di Azure. È anche possibile passare al dashboard di DevOps Projects direttamente da **Tutte le risorse** nel portale di Azure. 

    Questo dashboard fornisce visibilità su repository di codice di Azure DevOps, pipeline CI/CD e cluster servizio Azure Kubernetes. È possibile configurare le opzioni aggiuntive di CI/CD nella pipeline di Azure DevOps. A destra, selezionare **Sfoglia** per visualizzare l'app in esecuzione.

## <a name="examine-the-aks-cluster"></a>Esaminare il cluster del servizio Azure Kubernetes

DevOps Projects configura automaticamente un cluster servizio Azure Kubernetes, che è possibile esplorare e personalizzare. Per acquisire familiarità con il cluster servizio Azure Kubernetes, eseguire questa procedura:

1. Passare al dashboard di DevOps Projects.

1. A destra, selezionare il servizio servizio Azure Kubernetes.  
    Viene visualizzato un riquadro per il cluster servizio Azure Kubernetes. Da questa visualizzazione è possibile eseguire varie azioni, ad esempio monitorare l'integrità dei contenitori, eseguire ricerche nei log e aprire il dashboard di Kubernetes.

1. A destra, selezionare **Visualizza dashboard di Kubernetes**.  
    Facoltativamente, seguire i passaggi per aprire il dashboard di Kubernetes.

## <a name="examine-the-ci-pipeline"></a>Esaminare la pipeline di integrazione continua

DevOps Projects configura automaticamente una pipeline CI/CD nell'organizzazione di Azure DevOps. È possibile esplorare e personalizzare la pipeline. Per acquisire familiarità, eseguire questa procedura:

1. Passare al dashboard di DevOps Projects.

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
    Questo riquadro mostra un log di controllo delle modifiche recenti per la compilazione. Azure DevOps tiene traccia di tutte le modifiche apportate alla pipeline di compilazione e consente di confrontare le versioni.

1. Selezionare **Trigger**.  
    DevOps Projects crea automaticamente un trigger di integrazione continua e ogni commit nel repository avvia una nuova compilazione. Facoltativamente, è possibile scegliere di includere o escludere rami dal processo di integrazione continua.

1. Selezionare **Conservazione**.  
    A seconda dello scenario specifico, è possibile indicare i criteri per conservare o rimuovere un determinato numero di compilazioni.

## <a name="examine-the-cd-release-pipeline"></a>Esaminare la pipeline di versione CD

DevOps Projects crea e configura automaticamente i passaggi necessari per la distribuzione dall'organizzazione di Azure DevOps nella sottoscrizione di Azure. Questi passaggi includono la configurazione di una connessione al servizio di Azure per eseguire l'autenticazione di Azure DevOps nella sottoscrizione di Azure. L'automazione crea anche una pipeline di versione che fornisce la distribuzione continua in Azure. Per altre informazioni sulla pipeline di versione, eseguire le operazioni seguenti:

1. Selezionare **Compilazione e versione** e quindi **Versioni**.  
    DevOps Projects crea una pipeline di versione per gestire le distribuzioni in Azure.

1. Selezionare i puntini di sospensione (...) accanto alla pipeline di versione e quindi scegliere **Modifica**.  
    La pipeline di versione contiene una *pipeline* che definisce il processo per la versione.

1. In **Artefatti** selezionare **Elimina**.  
    La pipeline di compilazione esaminata nei passaggi precedenti produce l'output usato per l'artefatto. 

1. A destra dell'icona **Elimina** selezionare **Trigger di distribuzione continua**.  
    Questa pipeline di versione ha un trigger di distribuzione continua abilitato, che esegue una distribuzione ogni volta che è disponibile un nuovo artefatto di compilazione. Facoltativamente, è possibile disabilitare il trigger, in modo che le distribuzioni richiedano l'esecuzione manuale. 

1. A destra, selezionare **Visualizza versioni** per visualizzare una cronologia delle versioni.

1. Selezionare i puntini di sospensione (...) accanto a una versione e quindi selezionare **Apri**.  
    È possibile esplorare diversi menu, ad esempio un riepilogo delle versioni, gli elementi di lavoro associati e i test.

1. Selezionare **Commit**.  
    Questa visualizzazione mostra i commit di codice associati a questa distribuzione. È possibile confrontare le versioni per visualizzare le differenze di commit tra le distribuzioni.

1. Selezionare **Log**.  
    I log contengono informazioni utili sul processo di distribuzione. È possibile visualizzarli durante e dopo le distribuzioni.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Eseguire il commit delle modifiche in Azure Repos e la distribuzione automatica in Azure 

 > [!NOTE]
 > La procedura seguente verifica la pipeline CI/CD, apportando una modifica di testo semplice.

A questo punto, si è pronti per collaborare con un team a un'app usando un processo di CI/CD che distribuisce automaticamente il lavoro più recente nel sito Web. Ogni modifica al repository Git avvia una compilazione in DevOps di Azure e una pipeline CD esegue una distribuzione in Azure. Seguire la procedura in questa sezione o usare altre tecniche per eseguire il commit delle modifiche nel repository. Ad esempio, è possibile clonare il repository Git nello strumento o nell'IDE preferito e quindi eseguire il push delle modifiche in questo repository.

1. Nel menu di Azure DevOps, selezionare **Codice** > **File**e quindi passare al repository.

1. Passare alla directory *Views\Home*, selezionare i puntini di sospensione (...) accanto al file *Index.cshtml* e scegliere **Modifica**.

1. Apportare una modifica al file, ad esempio aggiungere del testo all'interno di uno dei tag div. 

1. In alto a destra, selezionare **Commit**, quindi selezionare **Commit** nuovamente per eseguire il push della modifica.  
    Dopo alcuni istanti verrà avviata una compilazione in Azure DevOps e verrà eseguita una versione per distribuire le modifiche. Monitorare lo stato della compilazione nel dashboard di DevOps Projects oppure nel browser con l'organizzazione di Azure DevOps.

1. Quando la versione sarà completa, aggiornare l'app per verificare le modifiche.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si stanno eseguendo test, è possibile pulire le risorse per evitare che vengano addebitati costi. Quando non sono più necessari, è possibile eliminare il cluster servizio Azure Kubernetes e le risorse correlate create in questa esercitazione. A tale scopo, usare la funzionalità **Elimina** nel dashboard di DevOps Projects.

> [!IMPORTANT]
> La procedura seguente elimina definitivamente le risorse. La funzionalità di *eliminazione* cancella definitivamente i dati creati dal progetto in DevOps Projects sia in Azure che in Azure DevOps e non sarà più possibile recuperarli. Usare questa procedura solo dopo aver letto attentamente le istruzioni visualizzate.

1. Nel portale di Azure passare al dashboard di DevOps Projects.
2. In alto a destra selezionare **Elimina**. 
3. Al prompt, selezionare **Sì** per *eliminare definitivamente* le risorse.

## <a name="next-steps"></a>Passaggi successivi

Facoltativamente, è possibile modificare queste pipeline di compilazione e di versione in base alle esigenze del team. È anche possibile usare questo modello di CI/CD come modello per altre pipeline. Questa esercitazione illustra come:

> [!div class="checklist"]
> * Usare DevOps Projects per distribuire un'app ASP.NET Core in servizio Azure Kubernetes
> * Configurare Azure DevOps e una sottoscrizione di Azure 
> * Esaminare il cluster del servizio Azure Kubernetes
> * Esaminare la pipeline di integrazione continua
> * Esaminare la pipeline di distribuzione continua
> * Eseguire il commit delle modifiche in Git e la distribuzione automatica in Azure
> * Pulire le risorse

Per altre informazioni sull'uso del dashboard di Kubernetes vedere:

> [!div class="nextstepaction"]
> [Usare il dashboard Kubernetes](https://docs.microsoft.com/azure/aks/kubernetes-dashboard)
