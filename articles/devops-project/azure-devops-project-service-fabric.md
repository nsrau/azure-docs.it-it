---
title: "Esercitazione: distribuire l'app ASP.NET Core in Azure Service Fabric usando Azure DevOps Starter"
description: Azure DevOps Starter consente di iniziare a usare Azure in modo semplice. Con DevOps Projects è possibile distribuire un'app ASP.NET Core in Azure Service Fabric in pochi rapidi passaggi.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: ce35f3af38b98f43ebbd0d3f42136ab8358b19a5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233721"
---
# <a name="tutorial-deploy-your-aspnet-core-app-to-azure-service-fabric-by-using-azure-devops-starter"></a>Esercitazione: distribuire l'app ASP.NET Core in Azure Service Fabric usando Azure DevOps Starter

Azure DevOps Starter presenta un'esperienza semplificata in cui è possibile importare il codice esistente e il repository git oppure scegliere un'applicazione di esempio per creare una pipeline di integrazione continua e recapito continuo (CD) in Azure. 

DevOps Starter anche:

* Creare automaticamente risorse di Azure, ad esempio Azure Service Fabric.
* Creare e configurare una pipeline di versione in Azure DevOps per impostare una pipeline CI/CD.
* Creare una risorsa di Azure Application Insights per il monitoraggio.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Usare DevOps Starter per creare un'app ASP.NET Core e distribuirla in Service Fabric
> * Configurare Azure DevOps e una sottoscrizione di Azure 
> * Esaminare la pipeline di integrazione continua
> * Esaminare la pipeline di distribuzione continua
> * Eseguire il commit in GIT e la distribuzione automatica in Azure
> * Pulire le risorse

## <a name="prerequisites"></a>Prerequisites

* Una sottoscrizione di Azure. È possibile ottenerne una gratuita tramite [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-starter-to-create-an-aspnet-core-app-and-deploy-it-to-service-fabric"></a>Usare DevOps Starter per creare un'app ASP.NET Core e distribuirla in Service Fabric

DevOps Starter crea una pipeline CI/CD in Azure Pipelines. È possibile creare una nuova organizzazione di Azure DevOps o usare un'organizzazione esistente. DevOps Starter crea anche risorse di Azure, ad esempio un cluster Service Fabric, nella sottoscrizione di Azure di propria scelta.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nella casella di ricerca digitare **DevOps Starter**, quindi selezionare. Fare clic su **Aggiungi** per crearne uno nuovo.

    ![Dashboard Starter DevOps](_img/azure-devops-starter-aks/search-devops-starter.png) 

1. Selezionare **.NET**, quindi **Avanti**.

1. In **Scegli un framework applicazione** selezionare **ASP.NET Core** e quindi selezionare **Avanti**.

1. Selezionare **Cluster di Service Fabric** e quindi selezionare **Avanti**. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurare Azure DevOps e una sottoscrizione di Azure

1. Creare una nuova organizzazione di Azure DevOps o selezionare un'organizzazione esistente. 

1. Immettere un nome per il progetto Azure DevOps. 

1. Selezionare la sottoscrizione di Azure.

1. Per visualizzare impostazioni di configurazione di Azure aggiuntive e identificare le dimensioni della macchina virtuale del nodo e il sistema operativo per il cluster di Service Fabric, selezionare **Modifica**. Questo riquadro mostra diverse opzioni per la configurazione del tipo e della posizione dei servizi di Azure.
 
1. Chiudere l'area di configurazione di Azure e selezionare **Fine**.  
    Il processo verrà completato dopo alcuni minuti. Viene configurata un'app di esempio ASP.NET Core in un repository Git nell'organizzazione di Azure DevOps, viene creato un cluster di Service Fabric, viene eseguita una pipeline CI/CD e l'applicazione viene distribuita in Azure. 

    Al termine di questa operazione, il dashboard iniziale di DevOps viene visualizzato nella portale di Azure. È anche possibile accedere al dashboard DevOps Starter direttamente da **tutte le risorse** nella portale di Azure. 

    Questo dashboard fornisce visibilità su repository di codice di Azure DevOps, pipeline CI/CD e cluster di Service Fabric. È possibile configurare le opzioni aggiuntive per la pipeline CI/CD in Azure Repos. A destra, selezionare **Sfoglia** per visualizzare l'app in esecuzione.

## <a name="examine-the-ci-pipeline"></a>Esaminare la pipeline di integrazione continua

DevOps Starter configura automaticamente una pipeline CI/CD in Azure Pipelines. È possibile esplorare e personalizzare la pipeline. Per acquisire familiarità, eseguire questa procedura:

1. Passare al dashboard iniziale di DevOps.

1. Nella parte superiore del Dashboard Starter di DevOps selezionare **pipeline di compilazione**. In una scheda del browser viene visualizzata la pipeline di compilazione per il nuovo progetto.

1. Puntare al campo **stato** , quindi selezionare i puntini di sospensione (...). Un menu Visualizza diverse opzioni, ad esempio Accodamento di una nuova compilazione, sospensione di una compilazione e modifica della pipeline di compilazione.

1. Selezionare **Modifica**.

1. In questo riquadro è possibile esaminare le diverse attività per la pipeline di compilazione. La compilazione esegue diverse attività, ad esempio il recupero delle origini dal repository Git, il ripristino delle dipendenze e la pubblicazione degli output usati per le distribuzioni.

1. Nella parte superiore della pipeline di compilazione selezionare il nome della pipeline di compilazione. 

1. Sotto il nome della pipeline di compilazione selezionare **Cronologia**. Questo riquadro mostra un log di controllo delle modifiche recenti per la compilazione. Azure DevOps tiene traccia di tutte le modifiche apportate alla pipeline di compilazione e consente di confrontare le versioni.

1. Selezionare **Trigger**. DevOps Starter crea automaticamente un trigger CI e ogni commit nel repository avvia una nuova compilazione. Facoltativamente, è possibile scegliere di includere o escludere rami dal processo di integrazione continua.

1. Selezionare **Conservazione**. A seconda dello scenario specifico, è possibile indicare i criteri per conservare o rimuovere un determinato numero di compilazioni.

## <a name="examine-the-cd-pipeline"></a>Esaminare la pipeline di distribuzione continua

DevOps Starter crea e configura automaticamente i passaggi necessari per eseguire la distribuzione dall'organizzazione DevOps di Azure alla sottoscrizione di Azure. Questi passaggi includono la configurazione di una connessione al servizio di Azure per eseguire l'autenticazione di Azure DevOps nella sottoscrizione di Azure. L'automazione crea anche una pipeline di versione che fornisce la distribuzione continua in Azure. Per altre informazioni sulla pipeline di versione, eseguire le operazioni seguenti:

1. Selezionare **Compilazione e versione** e quindi **Versioni**. DevOps Starter crea una pipeline di versione per gestire le distribuzioni in Azure.

1. Selezionare i puntini di sospensione (...) accanto alla pipeline di versione e quindi scegliere **Modifica**. La pipeline di versione contiene una *pipeline* che definisce il processo per la versione.

1. In **Artefatti** selezionare **Elimina**. La pipeline di compilazione esaminata in precedenza produce l'output usato per l'artefatto. 

1. A destra dell'icona **Elimina** selezionare **Trigger di distribuzione continua**. Questa pipeline di versione ha un trigger di distribuzione continua abilitato, che esegue una distribuzione ogni volta che è disponibile un nuovo artefatto di compilazione. Facoltativamente, è possibile disabilitare il trigger, in modo che le distribuzioni richiedano l'esecuzione manuale. 

1. A destra, selezionare **Visualizza versioni** per visualizzare una cronologia delle versioni.

1. Selezionare i puntini di sospensione (...) accanto a una versione e quindi selezionare **Apri**. È possibile esplorare diversi menu, ad esempio un riepilogo delle versioni, gli elementi di lavoro associati e i test.

1. Selezionare **Commit**. Questa visualizzazione mostra i commit di codice associati a questa distribuzione. È possibile confrontare le versioni per visualizzare le differenze di commit tra le distribuzioni.

1. Selezionare **log**. I log contengono informazioni utili sul processo di distribuzione. È possibile visualizzarli durante e dopo le distribuzioni.

## <a name="commit-changes-to-git-and-automatically-deploy-them-to-azure"></a>Eseguire il commit delle modifiche in Git e la distribuzione automatica in Azure 

 > [!NOTE]
 > La procedura seguente verifica la pipeline CI/CD, apportando una modifica di testo semplice.

A questo punto, si è pronti per collaborare con un team a un'app usando un processo di CI/CD che distribuisce automaticamente il lavoro più recente nel sito Web. Ogni modifica al repository Git avvia una compilazione e una versione distribuisce le modifiche in Azure. Seguire la procedura in questa sezione o usare altre tecniche per eseguire il commit delle modifiche nel repository. Ad esempio, è possibile clonare il repository Git nello strumento o nell'IDE preferito e quindi eseguire il push delle modifiche in questo repository.

1. Nel menu Azure DevOps selezionare**file**di **codice** > e quindi passare al repository.

1. Passare alla directory *Views\Home*, selezionare i puntini di sospensione (...) accanto al file *Index.cshtml* e scegliere **Modifica**.

1. Apportare una modifica al file, ad esempio aggiungere del testo all'interno di uno dei tag div. 

1. In alto a destra, selezionare **Commit**, quindi selezionare **Commit** nuovamente per eseguire il push della modifica.  
    Dopo alcuni istanti, verrà avviata una compilazione e quindi verrà eseguita una versione per distribuire le modifiche. È possibile monitorare lo stato di compilazione nel dashboard iniziale di DevOps o nel browser con la registrazione in tempo reale di Azure DevOps.

1. Quando la versione sarà completa, aggiornare l'app per verificare le modifiche.

## <a name="clean-up-resources"></a>Pulizia delle risorse

Se si stanno eseguendo test, è possibile pulire le risorse per evitare che vengano addebitati costi. Quando non sono più necessari, è possibile eliminare il cluster di Azure Service Fabric e le risorse correlate create in questa esercitazione. A tale scopo, usare la funzionalità di **eliminazione** nel dashboard iniziale di DevOps.

> [!IMPORTANT]
> La procedura seguente elimina definitivamente le risorse. La funzionalità di *eliminazione* Elimina i dati creati dal progetto in DevOps Starter sia in Azure che in Azure DevOps e non sarà possibile recuperarli. Usare questa procedura solo dopo aver letto attentamente le istruzioni visualizzate.

1. Nel portale di Azure passare al dashboard di avvio di DevOps.
1. In alto a destra selezionare **Elimina**. 
1. Al prompt, selezionare **Sì** per *eliminare definitivamente* le risorse.

## <a name="next-steps"></a>Passaggi successivi

Facoltativamente, è possibile modificare questa pipeline di integrazione continua/distribuzione continua in base alle esigenze del team. È anche possibile usare questo modello di CI/CD come modello per altre pipeline. In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Usare DevOps Starter per creare un'app ASP.NET Core e distribuirla in Service Fabric
> * Configurare Azure DevOps e una sottoscrizione di Azure 
> * Esaminare la pipeline di integrazione continua
> * Esaminare la pipeline di distribuzione continua
> * Eseguire il commit delle modifiche in Git e la distribuzione automatica in Azure
> * Pulizia delle risorse

Per altre informazioni su Service Fabric e sui microservizi, vedere:

> [!div class="nextstepaction"]
> [Usare un approccio ai microservizi per la compilazione di applicazioni](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
