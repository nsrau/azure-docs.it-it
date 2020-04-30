---
title: 'Guida introduttiva: creare una pipeline CI/CD per PHP con Azure DevOps Starter'
description: DevOps Starter consente di iniziare a usare Azure in modo semplice. Con pochi rapidi passaggi, è possibile avviare un'app in un servizio di Azure a scelta.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 3e4913c4be0d59bc37e3da2358c4f8ec302a26e5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233789"
---
# <a name="create-a-cicd-pipeline-for-php-with-azure-devops-starter"></a>Creare una pipeline CI/CD per PHP con Azure DevOps Starter

Azure DevOps Starter presenta un'esperienza semplificata che consente di creare risorse di Azure e di configurare una pipeline di integrazione continua e recapito continuo (CD) per l'app PHP in Azure Pipelines.  

Se non si ha ancora una sottoscrizione di Azure, è possibile ottenerne una gratuita tramite [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

 DevOps Starter crea una pipeline CI/CD in Azure Pipelines. È possibile creare una nuova organizzazione gratuita di Azure DevOps o usare un'organizzazione esistente. DevOps Projects crea anche risorse di Azure nella sottoscrizione di Azure di propria scelta.

1. Accedere al [portale di Microsoft Azure](https://portal.azure.com).

1. Nella casella di ricerca digitare **DevOps Starter**, quindi selezionare. Fare clic su **Aggiungi** per crearne uno nuovo.

    ![Dashboard Starter DevOps](_img/azure-devops-starter-aks/search-devops-starter.png) 

## <a name="select-a-sample-application-and-azure-service"></a>Selezionare un'applicazione di esempio e un servizio di Azure

1. Selezionare l'applicazione di esempio PHP. Gli esempi di PHP includono diversi framework applicazione. Il framework di esempio predefinito è Laravel.
        
1. Lasciare l'impostazione predefinita e quindi selezionare **Avanti**.  

1. La destinazione della distribuzione predefinita è App Web per contenitori. Il framework applicazione, scelto in precedenza, determina il tipo di destinazione della distribuzione del servizio di Azure disponibile.  Lasciare il servizio predefinito e quindi selezionare **Avanti**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurare Azure DevOps e una sottoscrizione di Azure 

1. Creare una nuova organizzazione di Azure DevOps o selezionare un'organizzazione esistente. 

    1. Scegliere un nome per il progetto in Azure DevOps. 
    
    1. Selezionare la sottoscrizione di Azure e la posizione, immettere un nome per l'applicazione, quindi selezionare **Fine**.  
    
    Dopo alcuni minuti, il dashboard iniziale di DevOps viene visualizzato nella portale di Azure. Viene configurata un'applicazione di esempio in un repository nell'organizzazione Azure DevOps, viene eseguita una compilazione e l'applicazione viene distribuita in Azure. Questo dashboard fornisce visibilità su repository di codice, pipeline CI/CD e applicazione in Azure.  
        
2. Selezionare **Sfoglia** per visualizzare l'applicazione in esecuzione.

    ![Visualizzazione dashboard](_img/azure-devops-project-php/dashboardnopreview.png) 
    
   DevOps Starter ha configurato automaticamente un trigger di compilazione e rilascio CI.  A questo punto, si è pronti per collaborare con un team a un'app PHP con un processo di CI/CD che distribuisce automaticamente il lavoro più recente nel sito Web.

## <a name="commit-code-changes-and-execute-cicd"></a>Eseguire il commit delle modifiche al codice e la pipeline di CI/CD

 DevOps Starter crea un repository git in Azure Repos o GitHub. Per visualizzare il repository e apportare modifiche al codice nell'applicazione, seguire questa procedura:

1. Nella parte sinistra del dashboard di DevOps Starter selezionare il collegamento per il ramo master. Questo collegamento apre una visualizzazione del repository Git appena creato.

1. Per visualizzare l'URL clone del repository, selezionare **Clona** in alto a destra nel browser. È possibile clonare il repository Git nell'IDE preferito. Nei passaggi successivi, usare il Web browser per apportare modifiche al codice ed eseguirne il commit direttamente nel ramo master.

1. A sinistra, passare al file **resources/views/welcome.blade.php**.

1. Selezionare **Modifica** e quindi apportare una modifica al testo.  Modificare, ad esempio, una parte del testo per uno dei tag div.

1. Selezionare **Esegui commit** e quindi salvare le modifiche.

1. Nel browser passare al dashboard iniziale di DevOps. Verrà visualizzata una compilazione in corso. Le modifiche appena apportate vengono compilate e distribuite automaticamente tramite una pipeline CI/CD.

## <a name="examine-the-cicd-pipeline"></a>Esaminare la pipeline CI/CD

 DevOps Starter configura automaticamente una pipeline di integrazione continua/recapito continuo completa in Azure Pipelines. Esplorare e personalizzare la pipeline in base alle esigenze. Per acquisire familiarità con le pipeline di compilazione e di versione eseguire questa procedura:

1. Nella parte superiore del Dashboard Starter di DevOps selezionare **pipeline di compilazione**. Questo collegamento apre una scheda del browser e la pipeline di compilazione per il nuovo progetto.

1. Puntare al campo **stato** , quindi selezionare i **puntini** di sospensione (...). In un menu vengono visualizzate diverse opzioni, ad esempio l'accodamento di una nuova compilazione, la sospensione di una compilazione e la modifica della pipeline di compilazione.

1. Selezionare **Modifica**.

1. In questo riquadro è possibile esaminare le diverse attività per la pipeline di compilazione. La compilazione esegue varie attività, ad esempio il recupero delle origini dal repository Git, il ripristino delle dipendenze e la pubblicazione degli output usati per le distribuzioni.

1. Nella parte superiore della pipeline di compilazione selezionare il nome della pipeline di compilazione.

1. Modificare il nome della pipeline di compilazione scegliendo un testo più descrittivo, selezionare **Salva e accoda**, quindi selezionare **Salva**.

1. Sotto il nome della pipeline di compilazione selezionare **Cronologia**.  Il riquadro **Cronologia** mostra un log di controllo delle modifiche recenti per la compilazione. Azure Pipelines tiene traccia di tutte le modifiche apportate alla pipeline di compilazione e consente di confrontare le versioni.

1. Selezionare **Trigger**. DevOps Starter crea automaticamente un trigger CI e ogni commit nel repository avvia una nuova compilazione. Facoltativamente, è possibile scegliere di includere o escludere rami dal processo di integrazione continua.

1. Selezionare **Conservazione**. A seconda dello scenario specifico, è possibile indicare i criteri per conservare o rimuovere un determinato numero di compilazioni.

1. Selezionare **Compilazione e versione** e quindi **Versioni**.  DevOps Starter crea una pipeline di versione per gestire le distribuzioni in Azure.

1. Selezionare i puntini di sospensione (...) accanto alla pipeline di versione e quindi scegliere **Modifica**. La pipeline di versione contiene una pipeline che definisce il processo per la versione. 

12. In **Artefatti** selezionare **Elimina**. La pipeline di compilazione esaminata nei passaggi precedenti produce l'output usato per l'artefatto. 

1. Accanto all'icona **Elimina** selezionare **Trigger di distribuzione continua**. Questa pipeline di versione ha un trigger di distribuzione continua abilitato, che esegue una distribuzione ogni volta che è disponibile un nuovo artefatto di compilazione. Facoltativamente, è possibile disabilitare il trigger, in modo che le distribuzioni richiedano l'esecuzione manuale. 

1. A sinistra selezionare **Attività**. Le attività sono le operazioni eseguite dal processo di distribuzione. In questo esempio è stata creata un'attività per la distribuzione in Servizio app di Azure.

1. A destra, selezionare **Visualizza versioni** per visualizzare una cronologia delle versioni.

1. Selezionare i puntini di sospensione (...) accanto a una delle versioni e quindi selezionare **Apri**. In questa visualizzazione ci sono diversi menu da esplorare, ad esempio un riepilogo delle versioni, gli elementi di lavoro associati e i test.

1. Selezionare **Commit**. Questa visualizzazione mostra i commit di codice associati alla distribuzione specifica. 

1. Selezionare **log**. I log contengono informazioni utili sul processo di distribuzione. Possono essere visualizzati durante e dopo le distribuzioni.

## <a name="clean-up-resources"></a>Pulizia delle risorse

Quando non servono più, è possibile eliminare il Servizio app di Azure e altre risorse correlate. Usare la funzionalità di **eliminazione** nel dashboard iniziale di DevOps.

## <a name="next-steps"></a>Passaggi successivi

Quando è stato configurato il processo di CI/CD, sono state create automaticamente pipeline di compilazione e di versione. È possibile modificare queste pipeline di compilazione e di versione in base alle esigenze del team. Per altre informazioni sulla pipeline CI/CD, vedere questa esercitazione:

> [!div class="nextstepaction"]
> [Personalizzare il processo di distribuzione continua](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
