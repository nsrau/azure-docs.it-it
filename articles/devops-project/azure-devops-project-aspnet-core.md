---
title: 'Avvio rapido: Creare una pipeline CI/CD per .NET con Azure DevOps Starter'
description: Azure DevOps Starter consente di iniziare a usare Azure senza difficoltà. Con pochi rapidi passaggi, è possibile avviare un'app .NET in un servizio di Azure a scelta.
ms.prod: devops
ms.technology: devops-cicd
services: azure-devops-project
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: mlearned
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: c434abdac19c0afd3a76256c27fc3316a8b09940
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163697"
---
# <a name="create-a-cicd-pipeline-for-net-with-azure-devops-starter"></a>Creare una pipeline CI/CD per .NET con Azure DevOps Starter

Configurare l'integrazione continua (CI) e la distribuzione continua (CD) per un'applicazione .NET Core o ASP.NET con DevOps Starter. DevOps Starter semplifica la configurazione iniziale di una pipeline di compilazione e di versione in Azure Pipelines.

Se non si ha ancora una sottoscrizione di Azure, è possibile ottenerne una gratuita tramite [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

DevOps Starter crea una pipeline CI/CD in Azure DevOps. È possibile creare una nuova organizzazione di Azure DevOps o usare un'organizzazione esistente. DevOps Starter crea anche risorse di Azure nella sottoscrizione di Azure selezionata.

1. Accedere al [portale di Microsoft Azure](https://portal.azure.com).

1. Nella casella di ricerca digitare **DevOps Starter** e quindi selezionare. Fare clic su **Aggiungi** per crearne una nuova. 

    ![Dashboard di DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Selezionare un'applicazione di esempio e un servizio di Azure

1. Selezionare l'applicazione di esempio **.NET**. Gli esempi .NET consentono di scegliere tra il framework ASP.NET open source o il framework .NET Core multipiattaforma.

   ![.NET Framework](_img/azure-devops-project-aspnet-core/select-dotnet.png)

2. Questo esempio è un'applicazione MVC ASP.NET Core. Selezionare il framework applicazione **.NET Core**, quindi selezionare **Avanti**.    
    
3. Selezionare **App Web Windows** come destinazione della distribuzione, quindi selezionare **Avanti**. Facoltativamente, è possibile scegliere altri servizi di Azure per la distribuzione. Il framework applicazione, scelto in precedenza, determina il tipo di destinazione della distribuzione del servizio di Azure disponibile.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurare Azure DevOps e una sottoscrizione di Azure 

1. Immettere il **nome del progetto**.

2. Creare una nuova **organizzazione Azure DevOps** gratuita o scegliere un'organizzazione esistente dall'elenco a discesa.

3. Selezionare la **sottoscrizione di Azure**, immettere un nome per l'**app Web** o accettare quello predefinito, quindi selezionare **Fine**. Dopo alcuni minuti, nel portale di Azure viene visualizzata la panoramica della distribuzione di DevOps Starter. 

4. Selezionare **Vai alla risorsa** per visualizzare il dashboard di DevOps Starter. Nell'angolo superiore destro aggiungere il **progetto** al dashboard, in modo da potervi accedere rapidamente. Viene configurata un'app di esempio in un repository nell'**organizzazione Azure DevOps**. Viene eseguita una compilazione e l'app viene distribuita in Azure.

5. Il dashboard fornisce visibilità su repository di codice, pipeline CI/CD e l'app in Azure. A destra, in Risorse di Azure, selezionare **Sfoglia** per visualizzare l'app in esecuzione.

   ![Visualizzazione dashboard](_img/azure-devops-project-aspnet-core/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>Eseguire il commit delle modifiche al codice e la pipeline di CI/CD

DevOps Starter ha creato un repository Git in Azure Repos o in GitHub. Per visualizzare il repository e apportare modifiche al codice nell'applicazione, seguire questa procedura:

1. Sul lato sinistro del dashboard di DevOps Starter selezionare il collegamento per il ramo **master**. Questo collegamento apre una visualizzazione del repository Git appena creato.

2. Nei passaggi successivi, è possibile usare il Web browser per apportare modifiche al codice ed eseguirne il commit direttamente nel ramo **master**. È anche possibile clonare il repository Git nel proprio ambiente IDE preferito selezionando **Clona** nella parte superiore destra della pagina del repository. 

3. A sinistra, passare nella struttura dei file dell'applicazione a **Application/aspnet-core-dotnet-core/Pages/Index.cshtml**.

4. Selezionare **Modifica** e quindi apportare una modifica al titolo h2. Ad esempio, digitare **Iniziare subito con il progetto Azure DevOps Starter** o apportare altre modifiche.

      ![Editor di codice](_img/azure-devops-project-aspnet-core/codechange.png)

5. Selezionare **Commit**, lasciare un commento e selezionare nuovamente **Commit**.

6. Nel browser passare al dashboard di Azure DevOps Starter.  Dovrebbe venire visualizzata una compilazione in corso. Le modifiche apportate vengono compilate e distribuite automaticamente tramite una pipeline CI/CD.

## <a name="examine-the-cicd-pipeline"></a>Esaminare la pipeline CI/CD

Nel passaggio precedente Azure DevOps Starter ha configurato automaticamente una pipeline CI/CD completa. Esplorare e personalizzare la pipeline in base alle esigenze. Eseguire questa procedura per acquisire familiarità con le pipeline di compilazione e di versione di Azure DevOps.

1. Selezionare **Pipeline di compilazione** nel dashboard di DevOps Starter. Questo collegamento apre una scheda del browser e la pipeline di compilazione di Azure DevOps per il nuovo progetto.

1. Selezionare i puntini di sospensione (...).  Questa azione apre un menu da cui è possibile avviare diverse attività, ad esempio accodare una nuova compilazione, sospendere una compilazione e modificare la pipeline di compilazione.

1. Selezionare **Modifica**.

    ![Pipeline di compilazione](_img/azure-devops-project-aspnet-core/builddef.png)

1. In questo riquadro è possibile esaminare le diverse attività per la pipeline di compilazione. La compilazione esegue diverse attività, ad esempio il recupero delle origini dal repository Git, il ripristino delle dipendenze e la pubblicazione degli output usati per le distribuzioni.

1. Nella parte superiore della pipeline di compilazione selezionare il nome della pipeline di compilazione.

1. Modificare il nome della pipeline di compilazione scegliendo un testo più descrittivo, selezionare **Salva e accoda**, quindi selezionare **Salva**.

1. Sotto il nome della pipeline di compilazione selezionare **Cronologia**.   
Nel riquadro **Cronologia** verrà visualizzato un log di controllo delle modifiche recenti per la compilazione.  Azure Pipelines tiene traccia di tutte le modifiche apportate alla pipeline di compilazione e consente di confrontare le versioni.

1. Selezionare **Trigger**. DevOps Starter crea automaticamente un trigger di integrazione continua e ogni commit nel repository avvia una nuova compilazione. Facoltativamente, è possibile scegliere di includere o escludere rami dal processo di integrazione continua.

1. Selezionare **Conservazione**. A seconda dello scenario specifico, è possibile indicare i criteri per conservare o rimuovere un determinato numero di compilazioni.

1. Selezionare **Compilazione e versione**, quindi **Versioni**.  
DevOps Starter crea una pipeline di versione per gestire le distribuzioni per Azure.

1.  A sinistra, selezionare i puntini di sospensione (...) accanto alla pipeline di versione e quindi scegliere **Modifica**. La pipeline di versione contiene una pipeline che definisce il processo per la versione.  

1. In **Artefatti** selezionare **Elimina**. La pipeline di compilazione esaminata nei passaggi precedenti produce l'output usato per l'artefatto. 

1. Accanto all'icona **Elimina** selezionare **Trigger di distribuzione continua**. Questa pipeline di versione ha un trigger di distribuzione continua abilitato, che esegue una distribuzione ogni volta che è disponibile un nuovo artefatto di compilazione. Facoltativamente, è possibile disabilitare il trigger, in modo che le distribuzioni richiedano l'esecuzione manuale.  

1. A sinistra selezionare **Attività**.  Le attività sono le operazioni eseguite dal processo di distribuzione. In questo esempio è stata creata un'attività per la distribuzione in Servizio app di Azure.

1. A destra, selezionare **Visualizza versioni**. Questa visualizzazione mostra una cronologia delle versioni.

1. Selezionare i puntini di sospensione (...) accanto a una delle versioni e quindi selezionare **Apri**. Ci sono diversi menu da esplorare, ad esempio un riepilogo delle versioni, gli elementi di lavoro associati e i test.

1. Selezionare **Commit**. Questa visualizzazione mostra i commit di codice associati alla distribuzione specifica. 

1. Selezionare **Log**. I log contengono informazioni utili sul processo di distribuzione. Possono essere visualizzati durante e dopo le distribuzioni.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile eliminare il Servizio app di Azure e altre risorse correlate creati in precedenza. Usare la funzionalità **Elimina** del dashboard di DevOps Starter.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla modifica delle pipeline di compilazione e di versione in base alle esigenze del team, vedere questa esercitazione:

> [!div class="nextstepaction"]
> [Personalizzare il processo di distribuzione continua](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Video

> [!VIDEO https://www.youtube.com/embed/itwqMf9aR0w]
