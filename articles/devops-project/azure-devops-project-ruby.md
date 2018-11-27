---
title: 'Guida introduttiva: Creare una pipeline CI/CD per Ruby on Rails con Azure DevOps Projects'
description: Azure DevOps Projects permette di iniziare a usare Azure velocemente. Con pochi rapidi passaggi, è possibile avviare un'app Web Ruby in un servizio di Azure.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 4cf3feeb92f04b4e97cbdc83c539c206790a78c8
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52264523"
---
# <a name="create-a-cicd-pipeline-for-ruby-on-rails-by-using-azure-devops-projects"></a>Creare una pipeline CI/CD per Ruby on Rails con Azure DevOps Projects

Configurare integrazione continua (CI, Continuous Integration) e recapito continuo (CD, Continuous Delivery) per l'app Ruby on Rails con Azure DevOps Projects. DevOps Projects semplifica la configurazione iniziale di una pipeline di compilazione e di versione di Azure DevOps.

Se non si ha ancora una sottoscrizione di Azure, è possibile ottenerne una gratuita tramite [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Azure DevOps Projects crea una pipeline CI/CD in Azure Repos. È possibile creare una nuova organizzazione di Azure DevOps o usare un'organizzazione esistente. DevOps Projects crea anche risorse di Azure nella sottoscrizione di Azure di propria scelta.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel riquadro a sinistra selezionare **Crea risorsa**.

1. Nella casella di ricerca, digitare **DevOps Projects**, quindi selezionare **Crea**.

    ![Dashboard di DevOps Projects](_img/azure-devops-project-github/fullbrowser.png)

## <a name="select-a-sample-app-and-azure-service"></a>Selezionare un'app di esempio e un servizio di Azure

1. Selezionare l'app di esempio **Ruby**.

1. Selezionare il framework applicazione **Ruby on Rails**. Al termine, selezionare **Avanti**.

1. La destinazione della distribuzione predefinita è **App Web in Linux**.  
    Facoltativamente, è possibile selezionare **App Web per contenitori**. Il framework applicazione, scelto in precedenza, determina il tipo di destinazione della distribuzione del servizio di Azure disponibile. 
    
1. Selezionare il servizio di destinazione preferito, quindi selezionare **Avanti**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurare Azure DevOps e una sottoscrizione di Azure 

1. Creare una nuova organizzazione gratuita di Azure DevOps Services o scegliere un'organizzazione esistente. 

1. Immettere un nome per il progetto Azure DevOps. 

1. Selezionare la sottoscrizione di Azure e la posizione, immettere un nome per l'app, quindi selezionare **Fine**.  
    Dopo pochi minuti, il dashboard di DevOps Projects viene visualizzato nel portale di Azure. Viene configurata un'app di esempio in un repository nell'organizzazione Azure DevOps, viene eseguita una compilazione e l'app viene distribuita in Azure. 
    
    Il dashboard fornisce visibilità su repository di codice, pipeline CI/CD e l'app in Azure. A destra, selezionare **Sfoglia** per visualizzare l'app in esecuzione.

    ![Visualizzazione dashboard](_img/azure-devops-project-go/dashboardnopreview.png) 

## <a name="commit-your-code-changes-and-execute-the-cicd"></a>Eseguire il commit delle modifiche al codice e la pipeline CI/CD

Azure DevOps Projects crea un repository Git in Azure Pipelines o GitHub. Per visualizzare il repository e apportare modifiche al codice nell'app, seguire questa procedura:

1. Nel dashboard di DevOps Projects, a sinistra, selezionare il collegamento per il ramo master.  
    Il collegamento apre una visualizzazione del repository Git appena creato.

1. Per visualizzare l'URL clone del repository, selezionare **Clona** in alto a destra.  
    È possibile clonare il repository Git nell'IDE preferito. Nei passaggi successivi, è possibile usare il Web browser per apportare modifiche al codice ed eseguirne il commit direttamente nel ramo master.

1. A sinistra, passare al file *app/views/pages/home.html.erb*, quindi selezionare **Modifica**.

1. Apportare una modifica al file. Modificare, ad esempio, parte del testo all'interno di uno dei tag div.

1. Selezionare **Esegui commit** e quindi salvare le modifiche.

1. Nel browser passare al dashboard di DevOps Projects.  
    Dovrebbe essere in corso una compilazione. Le modifiche apportate vengono compilate e distribuite automaticamente tramite una pipeline CI/CD.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Esaminare la pipeline CI/CD di Azure Pipelines

Azure DevOps Projects configura automaticamente una pipeline CI/CD completa nell'organizzazione di Azure DevOps. Esplorare e personalizzare la pipeline in base alle esigenze. Per acquisire familiarità con le pipeline di compilazione e di versione di Azure DevOps, eseguire questa procedura:

1. Passare al dashboard di DevOps Projects.

1. In alto, selezionare **Pipeline di compilazione**.  
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

1. Selezionare **Compilazione e versione** e quindi **Versioni**.  
    DevOps Projects crea una pipeline di versione per gestire le distribuzioni in Azure.

1. Selezionare i puntini di sospensione (...) accanto alla pipeline di versione e quindi scegliere **Modifica**.  
    La pipeline di versione contiene una *pipeline* che definisce il processo per la versione.

1. In **Artefatti** selezionare **Elimina**.  
    La pipeline di compilazione esaminata in precedenza produce l'output usato per l'artefatto. 

1. A destra dell'icona **Elimina** selezionare **Trigger di distribuzione continua**.  
    Questa pipeline di versione ha un trigger di distribuzione continua abilitato, che esegue una distribuzione ogni volta che è disponibile un nuovo artefatto di compilazione. Facoltativamente, è possibile disabilitare il trigger, in modo che le distribuzioni richiedano l'esecuzione manuale. 

1. A sinistra selezionare **Attività**.  
    Le attività sono le operazioni eseguite dal processo di distribuzione. In questo esempio è stata creata un'attività per la distribuzione in Servizio app di Azure.

1. A destra, selezionare **Visualizza versioni** per visualizzare una cronologia delle versioni.

1. Selezionare i puntini di sospensione (...) accanto a una versione e quindi selezionare **Apri**.  
    È possibile esplorare diversi menu, ad esempio un riepilogo delle versioni, gli elementi di lavoro associati e i test.

1. Selezionare **Commit**.  
    Questa visualizzazione mostra i commit di codice associati a questa distribuzione. 

1. Selezionare **Log**.  
    I log contengono informazioni utili sul processo di distribuzione. È possibile visualizzarli durante e dopo le distribuzioni.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, è possibile eliminare l'istanza del Servizio app di Azure e le risorse correlate create in questa guida introduttiva. A tale scopo, usare la funzionalità **Elimina** nel dashboard di DevOps Projects.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla modifica delle pipeline di compilazione e di versione in base alle esigenze del team, vedere:

> [!div class="nextstepaction"]
> [Definire la pipeline di distribuzione continua (CD) in più fasi](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
