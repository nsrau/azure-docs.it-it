---
title: Distribuire un'app ASP.NET Core in Azure Service Fabric con il progetto DevOps di Azure | Esercitazione di Azure DevOps Services
description: Il progetto DevOps consente di iniziare a usare Azure in modo semplice. Il progetto Azure DevOps permette di eseguire facilmente la distribuzione di un'app ASP.NET Core con Azure Service Fabric in pochi rapidi passaggi.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 6a0539b2213b99e09021a4f90d914172eac62560
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2018
ms.locfileid: "44300397"
---
# <a name="tutorial--deploy-your-aspnet-core-app-to-azure-service-fabric-with-the-azure-devops-project"></a>Esercitazione: Distribuire un'app ASP.NET Core in Azure Service Fabric con il progetto DevOps di Azure

Il progetto DevOps di Azure offre un'esperienza semplificata in cui è possibile usare il codice esistente e il repository Git oppure scegliere una delle applicazioni di esempio per creare una pipeline di integrazione continua (CI, Continuous Integration) e distribuzione continua (CD, Continuous Delivery) in Azure.  Il progetto DevOps crea automaticamente le risorse di Azure come Azure Service Fabric, crea e configura una pipeline di versione in Azure DevOps che include una pipeline di integrazione continua/distribuzione continua e quindi crea una risorsa di Azure Application Insights per il monitoraggio.

Si apprenderà come:

> [!div class="checklist"]
> * Creare un progetto Azure DevOps per un'app ASP.NET Core e Service Fabric
> * Configurare Azure DevOps Services e una sottoscrizione di Azure 
> * Esaminare la pipeline di integrazione continua
> * Esaminare la pipeline di distribuzione continua
> * Eseguire il commit in GIT e la distribuzione automatica in Azure
> * Pulire le risorse

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. È possibile ottenerne una gratuita tramite [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-an-azure-devops-project-for-an-aspnet-core-app-and-service-fabric"></a>Creare un progetto Azure DevOps per un'app ASP.NET Core e Service Fabric

Il progetto Azure DevOps crea una pipeline di integrazione continua/distribuzione continua.  È possibile creare una **nuova organizzazione di Azure DevOps Services** o usare un'**organizzazione esistente**.  Il progetto DevOps di Azure crea anche **risorse di Azure**, ad esempio un cluster di Service Fabric, nella **sottoscrizione di Azure** di propria scelta.

1. Accedere al [portale di Microsoft Azure](https://portal.azure.com).

1. Scegliere l'icona **Crea una risorsa** sulla barra di spostamento sinistra e quindi cercare **Progetto DevOps**.  Scegliere **Create**.

    ![Avvio del recapito continuo](_img/azure-devops-project-service-fabric/fullbrowser.png)

1. Selezionare **.NET** e quindi scegliere **Avanti**.

1. Per **Scegli un framework applicazione** selezionare **ASP.NET Core** e quindi selezionare **Avanti**.

1. Selezionare **Cluster di Service Fabric** e quindi scegliere **Avanti**.  

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Configurare Azure DevOps Services e una sottoscrizione di Azure

1. Creare una **nuova** organizzazione di Azure DevOps Services o selezionare un'organizzazione **esistente**.  Scegliere un **nome** per il progetto Azure DevOps.  

1. Selezionare la **sottoscrizione di Azure**.

1. Selezionare il collegamento **Modifica** per visualizzare impostazioni di configurazione di Azure aggiuntive e identificare le **dimensioni della macchina virtuale del nodo** e il **sistema operativo** per il **cluster di Service Fabric**.  Ci sono diverse opzioni per la configurazione del tipo e della posizione dei servizi di Azure.
 
1. Chiudere l'area di configurazione di Azure e scegliere **Fine**.

1. Il completamento del processo richiederà qualche minuto.  Viene configurata un'applicazione di esempio ASP.NET Core in un repository nell'organizzazione di Azure DevOps Services, viene creato un cluster Service Fabric, viene eseguita una pipeline di integrazione continua/distribuzione continua e l'applicazione viene distribuita in Azure.  

    Al termine, il **dashboard del progetto** Azure DevOps viene caricato nel portale di Azure.  È anche possibile passare al **dashboard del progetto DevOps di Azure** direttamente da **Tutte le risorse** nel **portale di Azure**.  

    Questo dashboard fornisce visibilità sul **repository di codice** di Azure DevOps Services, la **pipeline di integrazione continua/distribuzione continua di Azure DevOps Services** e il **cluster Service Fabric**.  È possibile configurare ulteriormente opzioni aggiuntive in Azure DevOps Services.  A destra del dashboard selezionare **Sfoglia** per visualizzare l'applicazione in esecuzione.

## <a name="examine-the-azure-devops-services-ci-pipeline"></a>Esaminare la pipeline di integrazione continua di Azure DevOps Services

Il progetto Azure DevOps configura automaticamente una pipeline di integrazione continua/distribuzione continua di Azure DevOps Services nell'organizzazione di Azure DevOps Services.  È possibile esplorare e personalizzare la pipeline.  Attenersi alla procedura seguente per acquisire familiarità con la pipeline di compilazione di Azure DevOps Services.

1. Passare al **dashboard del progetto DevOps di Azure**.

1. Selezionare **Pipeline di compilazione** nella parte **superiore** del **dashboard del progetto Azure DevOps**.  Questo collegamento apre una scheda del browser e la pipeline di compilazione di Azure DevOps Services per il nuovo progetto.

1. Spostare il cursore del mouse a destra della pipeline di compilazione accanto al campo **Stato**. Selezionare i **puntini di sospensione** visualizzati.  Questa azione apre un menu da cui è possibile avviare diverse attività, ad esempio **accodare una nuova compilazione**, **sospendere una compilazione** e **modificare la pipeline di compilazione**.

1. Selezionare **Modifica**.

1. In questa visualizzazione **esaminare le diverse attività** per la pipeline di compilazione.  La compilazione esegue diverse attività, ad esempio il recupero delle origini dal repository GIT per Azure DevOps Services, il ripristino delle dipendenze e la pubblicazione degli output usati per le distribuzioni.

1. Nella parte superiore della pipeline di compilazione selezionare il **nome della pipeline di compilazione**. 

1. Sotto il nome della pipeline di compilazione selezionare **Cronologia**.  Verrà visualizzato un log di controllo delle modifiche recenti per la compilazione.  Azure DevOps Services tiene traccia di tutte le modifiche apportate alla pipeline di compilazione e consente di confrontare le versioni.

1. Selezionare **Trigger**.  Il progetto Azure DevOps ha creato automaticamente un trigger di integrazione continua e ogni commit nel repository avvia una nuova compilazione.  Facoltativamente, è possibile scegliere di includere o escludere rami dal processo di integrazione continua.

1. Selezionare **Conservazione**.  In base allo scenario specifico, è possibile indicare i criteri per conservare o rimuovere un determinato numero di compilazioni.

## <a name="examine-the-azure-devops-services-cd-pipeline"></a>Esaminare la pipeline di distribuzione continua di Azure DevOps Services

Il progetto Azure DevOps crea e configura automaticamente i passaggi necessari per la distribuzione dall'organizzazione di Azure DevOps Services nella sottoscrizione di Azure.  Questi passaggi includono la configurazione di una connessione al servizio di Azure per eseguire l'autenticazione di Azure DevOps Services nella sottoscrizione di Azure.  L'automazione crea anche una pipeline di versione di Azure DevOps Services e questa versione fornisce la distribuzione continua in Azure.  Attenersi alla procedura seguente per esaminare in maggiore dettaglio la pipeline di versione di Azure DevOps Services.

1. Selezionare **Compilazione e versione** e quindi scegliere **Versioni**.  Il progetto Azure DevOps ha creato una pipeline di versione di Azure DevOps Services per gestire le distribuzioni in Azure.

1. Sul lato sinistro del browser selezionare i **puntini di sospensione** accanto alla pipeline di versione e quindi scegliere **Modifica**.

1. La pipeline di versione contiene una **pipeline** che definisce il processo per la versione.  In **Artefatti** selezionare **Elimina**.  La pipeline di compilazione esaminata nei passaggi precedenti produce l'output usato per l'artefatto. 

1. A destra dell'icona **Elimina** selezionare l'**icona** **Trigger di distribuzione continua** (a forma di fulmine).  Questa pipeline di versione ha un trigger di distribuzione continua abilitato.  Il trigger avvia una distribuzione ogni volta che è disponibile un nuovo artefatto di compilazione.  Facoltativamente, è possibile disabilitare il trigger, in modo che le distribuzioni richiedano l'esecuzione manuale. 

1. Sul lato destro del browser selezionare **Visualizza versioni**.  Questa visualizzazione mostra una cronologia delle versioni.

1. Selezionare i **puntini di sospensione** accanto a una delle versioni e scegliere **Apri**.  In questa visualizzazione ci sono diversi menu da esplorare, ad esempio un **riepilogo delle versioni**, gli **elementi di lavoro associati** e i **test**.

1. Selezionare **Commit**.  Questa visualizzazione mostra i commit di codice associati alla distribuzione specifica. È possibile confrontare le versioni per visualizzare le differenze di commit tra le distribuzioni.

1. Selezionare **Log**.  I log contengono informazioni utili sul processo di distribuzione.  Possono essere visualizzati durante e dopo le distribuzioni.

## <a name="commit-changes-to-git-and-automatically-deploy-to-azure"></a>Eseguire il commit in GIT e la distribuzione automatica in Azure 

 > [!NOTE]
 > La procedura seguente testa la pipeline di CI/CD con una semplice modifica al testo nell'app Web.

A questo punto, si è pronti per collaborare con un team a un'app con un processo di CI/CD che distribuisce automaticamente il lavoro più recente nel sito Web.  Ogni modifica al repository GIT avvia una compilazione e una versione distribuisce le modifiche in Azure.  Attenersi alla procedura seguente o usare altre tecniche per eseguire il commit delle modifiche nel repository.  Ad esempio, è possibile **clonare** il repository Git nello strumento o nell'IDE preferito e quindi eseguire il push delle modifiche in questo repository.

1. Selezionare **Codice** e quindi **File** dal menu di Azure DevOps Services e passare al repository.
1. Passare alla directory **Views\Home** e quindi selezionare i **puntini di sospensione** accanto al file **Index.cshtml** e scegliere **Modifica**.

1. Apportare una modifica al file, ad esempio nel testo all'interno dei **tag div**.  In alto a destra selezionare **Esegui commit**.  Selezionare di nuovo **Esegui commit** per effettuare il push della modifica. 

1. Dopo alcuni istanti, verrà **avviata una compilazione** e quindi verrà eseguita una versione per distribuire le modifiche.  È possibile monitorare lo **stato della compilazione** con il dashboard del progetto DevOps oppure nel browser con la registrazione in tempo reale di Azure DevOps Services.

1. Una volta completata la versione, **aggiornare l'applicazione** nel browser per verificare le modifiche.

## <a name="clean-up-resources"></a>Pulire le risorse

 > [!NOTE]
 > La procedura seguente elimina definitivamente le risorse.  Usare questa funzionalità solo dopo aver letto attentamente le indicazioni.

Se si stanno eseguendo test, è possibile pulire le risorse per evitare che vengano addebitati costi.  Quando il cluster di Azure Service Fabric e le risorse correlate create in questa esercitazione non sono più necessari, è possibile eliminarli usando la funzionalità **Elimina** nel dashboard del progetto DevOps di Azure.  **Prestare attenzione** in quanto la funzionalità di eliminazione cancella definitivamente i dati creati dal progetto Azure DevOps sia in Azure che in Azure DevOps Services e, una volta eliminati, non sarà più possibile recuperarli.

1. Dal **portale di Azure** passare al **progetto DevOps di Azure**.
2. Nella parte **superiore destra** del dashboard selezionare **Elimina**.  Dopo aver letto la richiesta di conferma, selezionare **Sì** per **eliminare definitivamente** le risorse.

## <a name="next-steps"></a>Passaggi successivi

Facoltativamente, è possibile modificare questa pipeline di integrazione continua/distribuzione continua in base alle esigenze del team. È anche possibile usare questo modello di CI/CD come modello per altri progetti.  Si è appreso come:

> [!div class="checklist"]
> * Creare un progetto Azure DevOps per un'app ASP.NET Core e Service Fabric
> * Configurare Azure DevOps Services e una sottoscrizione di Azure 
> * Esaminare la pipeline di integrazione continua
> * Esaminare la pipeline di distribuzione continua
> * Eseguire il commit in GIT e la distribuzione automatica in Azure
> * Pulire le risorse

Per altre informazioni su Service Fabric e sui microservizi, vedere:

> [!div class="nextstepaction"]
> [Usare un approccio ai microservizi per la compilazione di applicazioni](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
