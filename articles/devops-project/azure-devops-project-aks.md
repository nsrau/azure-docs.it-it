---
title: Distribuire un'app ASP.NET Core nel servizio Kubernetes di Azure (AKS) con il progetto DevOps di Azure | Esercitazione di VSTS
description: Il progetto DevOps consente di iniziare a usare Azure in modo semplice. Il progetto DevOps di Azure permette di eseguire facilmente la distribuzione di un'app ASP.NET Core con il servizio Kubernetes di Azure (AKS) in pochi rapidi passaggi.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 98c585cc0c6136496fe190cd3eceb27fd856af21
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967368"
---
# <a name="tutorial--deploy-your-aspnet-core-app-to-azure-kubernetes-service-aks-with-the-azure-devops-project"></a>Esercitazione: Distribuire un'app ASP.NET Core nel servizio Kubernetes di Azure (AKS) con il progetto DevOps di Azure

Il progetto DevOps di Azure offre un'esperienza semplificata in cui è possibile usare il codice esistente e il repository Git oppure scegliere una delle applicazioni di esempio per creare una pipeline di integrazione continua (CI, Continuous Integration) e distribuzione continua (CD, Continuous Delivery) in Azure.  Il progetto DevOps crea automaticamente le risorse di Azure come AKS, crea e configura una pipeline di versione in Visual Studio Team Services che include una definizione di compilazione per l'integrazione continua, imposta una definizione di versione per la distribuzione continua e quindi crea una risorsa di Azure Application Insights per il monitoraggio.

Si apprenderà come:

> [!div class="checklist"]
> * Creare un progetto DevOps di Azure per un'app ASP.NET Core e il servizio Kubernetes di Azure (AKS)
> * Configurare VSTS e una sottoscrizione di Azure 
> * Esaminare il cluster del servizio Kubernetes di Azure
> * Esaminare la definizione di compilazione di integrazione continua VSTS
> * Esaminare la definizione di gestione del rilascio di distribuzione continua VSTS
> * Eseguire il commit delle modifiche in VSTS e la distribuzione automatica in Azure
> * Pulire le risorse

## <a name="prerequisites"></a>prerequisiti

* Una sottoscrizione di Azure. È possibile ottenerne una gratuita tramite [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-an-azure-devops-project-for-an-aspnet-core-app-and-aks"></a>Creare un progetto DevOps di Azure per un'app ASP.NET Core e il servizio Kubernetes di Azure (AKS)

Il progetto DevOps di Azure crea una pipeline di CI/CD in VSTS.  È possibile creare un **nuovo account VSTS** oppure usare un **account esistente**.  Il progetto DevOps di Azure crea anche **risorse di Azure**, ad esempio un cluster del servizio Kubernetes di Azure, nella **sottoscrizione di Azure** di propria scelta.

1. Accedere al [portale di Microsoft Azure](https://portal.azure.com).

1. Scegliere l'icona **Crea una risorsa** sulla barra di spostamento sinistra e quindi cercare **Progetto DevOps**.  Scegliere **Create**.

    ![Avvio del recapito continuo](_img/azure-devops-project-aks/fullbrowser.png)

1. Selezionare **.NET** e quindi scegliere **Avanti**.

1. Per **Scegli un framework applicazione** selezionare **ASP.NET Core** e quindi selezionare **Avanti**.

1. Selezionare **Servizio Kubernetes** e quindi scegliere **Avanti**.  

## <a name="configure-vsts-and-an-azure-subscription"></a>Configurare VSTS e una sottoscrizione di Azure

1. Creare un **nuovo** account VSTS oppure scegliere un account **esistente**.  Scegliere un **nome** per il progetto VSTS.  

1. Selezionare la **sottoscrizione di Azure**.

1. Selezionare il collegamento **Modifica** per visualizzare le impostazioni di configurazione aggiuntive di Azure e identificare il **numero di nodi** per il **cluster Kubernetes**.  Ci sono diverse opzioni per la configurazione del tipo e della posizione dei servizi di Azure.
 
1. Chiudere l'area di configurazione di Azure e scegliere **Fine**.

1. Il completamento del processo richiederà qualche minuto.  Viene configurata un'applicazione di esempio ASP.NET Core in un repository nell'account VSTS, viene creato un cluster del servizio Kubernetes di Azure, vengono eseguite una compilazione e una versione e l'applicazione viene distribuita in Azure.  

    Al termine, il **dashboard del progetto** DevOps di Azure viene caricato nel portale di Azure.  È anche possibile passare al **dashboard del progetto DevOps di Azure** direttamente da **Tutte le risorse** nel **portale di Azure**.  

    Questo dashboard fornisce visibilità su **repository di codice** VSTS, **pipeline di CI/CD VSTS** e **cluster del servizio Kubernetes di Azure**.  È possibile configurare ulteriormente opzioni aggiuntive in VSTS.  A destra del dashboard selezionare **Sfoglia** per visualizzare l'applicazione in esecuzione.

## <a name="examine-the-aks-cluster"></a>Esaminare il cluster del servizio Kubernetes di Azure

Il progetto DevOps di Azure configura automaticamente un cluster del servizio Kubernetes di Azure.  È possibile esplorare e personalizzare il cluster.  Attenersi alla procedura seguente per acquisire familiarità con il servizio Kubernetes di Azure.

1. Passare al **dashboard del progetto DevOps di Azure**.

1. A destra del dashboard del progetto DevOps, selezionare il **servizio Kubernetes**.

1. Viene visualizzato un pannello per il cluster del servizio Kubernetes di Azure.  Da questa visualizzazione è possibile eseguire varie azioni, ad esempio **monitorare l'integrità dei contenitori**, **eseguire ricerche nei log** e aprire il **dashboard di Kubernetes**.

1. Sul lato destro della schermata, selezionare **Visualizza dashboard di Kubernetes**.  Facoltativamente, seguire i passaggi per aprire il dashboard di Kubernetes.

## <a name="examine-the-vsts-ci-build-definition"></a>Esaminare la definizione di compilazione di integrazione continua VSTS

Il progetto DevOps di Azure configura automaticamente una pipeline di CI/CD VSTS completa nell'account VSTS.  È possibile esplorare e personalizzare la pipeline.  Attenersi alla procedura seguente per acquisire familiarità con la definizione di compilazione VSTS.

1. Passare al **dashboard del progetto DevOps di Azure**.

1. Selezionare **Pipeline di compilazione** nella parte **superiore** del **dashboard del progetto DevOps di Azure**.  Questo collegamento apre una scheda del browser e la definizione di compilazione VSTS per il nuovo progetto.

1. Spostare il cursore del mouse a destra della definizione di compilazione accanto al campo **Stato**. Selezionare i **puntini di sospensione** visualizzati.  Questa azione apre un menu da cui è possibile eseguire diverse attività, ad esempio **accodare una nuova compilazione**, **sospendere una compilazione** e **modificare la definizione di compilazione**.

1. Selezionare **Modifica**.

1. In questa visualizzazione **esaminare le diverse attività** per la definizione di compilazione.  La compilazione esegue diverse attività, ad esempio il recupero delle origini dal repository Git VSTS, il ripristino delle dipendenze e la pubblicazione degli output usati per le distribuzioni.

1. Nella parte superiore della definizione di compilazione selezionare il **nome della definizione di compilazione**.

1. Modificare il **nome** della definizione di compilazione scegliendo un testo più descrittivo.  Selezionare **Salva e accoda** e quindi **Salva**.

1. Sotto il nome della definizione di compilazione selezionare **Cronologia**.  Verrà visualizzato un log di controllo delle modifiche recenti per la compilazione.  VSTS tiene traccia di tutte le modifiche apportate alla definizione di compilazione e consente di confrontare le versioni.

1. Selezionare **Trigger**.  Il progetto DevOps di Azure ha creato automaticamente un trigger di integrazione continua e ogni commit nel repository avvia una nuova compilazione.  Facoltativamente, è possibile scegliere di includere o escludere rami dal processo di integrazione continua.

1. Selezionare **Conservazione**.  In base allo scenario specifico, è possibile indicare i criteri per conservare o rimuovere un determinato numero di compilazioni.

## <a name="examine-the-vsts-cd-release-management-definition"></a>Esaminare la definizione di gestione del rilascio di distribuzione continua VSTS

Il progetto DevOps di Azure crea e configura automaticamente i passaggi necessari per la distribuzione dall'account VSTS alla sottoscrizione di Azure.  Tali passaggi includono la configurazione di una connessione al servizio di Azure per eseguire l'autenticazione di VSTS nella sottoscrizione di Azure.  L'automazione crea anche una definizione di versione VSTS e la definizione di versione fornisce la distribuzione continua in Azure.  Attenersi alla procedura seguente per esaminare più a fondo la definizione di versione VSTS.

1. Selezionare **Compilazione e versione** e quindi scegliere **Versioni**.  Il progetto DevOps di Azure ha creato una definizione di versione VSTS per gestire le distribuzioni in Azure.

1. Sul lato sinistro del browser selezionare i **puntini di sospensione** accanto alla definizione di versione e quindi scegliere **Modifica**.

1. La definizione di versione contiene una **pipeline** che definisce il processo per la versione.  In **Artefatti** selezionare **Elimina**.  La definizione di compilazione esaminata nei passaggi precedenti produce l'output usato per l'artefatto. 

1. A destra dell'icona **Elimina** selezionare l'**icona** **Trigger di distribuzione continua** (a forma di fulmine).  Questa definizione di versione ha un trigger di distribuzione continua abilitato.  Il trigger crea una distribuzione ogni volta che è disponibile un nuovo artefatto di compilazione.  Facoltativamente, è possibile disabilitare il trigger, in modo che le distribuzioni richiedano l'esecuzione manuale. 

1. Sul lato destro del browser selezionare **Visualizza versioni**.  Questa visualizzazione mostra una cronologia delle versioni.

1. Selezionare i **puntini di sospensione** accanto a una delle versioni e scegliere **Apri**.  In questa visualizzazione ci sono diversi menu da esplorare, ad esempio un **riepilogo delle versioni**, gli **elementi di lavoro associati** e i **test**.

1. Selezionare **Commit**.  Questa visualizzazione mostra i commit di codice associati alla distribuzione specifica. È possibile confrontare le versioni per visualizzare le differenze di commit tra le distribuzioni.

1. Selezionare **Log**.  I log contengono informazioni utili sul processo di distribuzione.  Possono essere visualizzati durante e dopo le distribuzioni.

## <a name="commit-changes-to-vsts-and-automatically-deploy-to-azure"></a>Eseguire il commit delle modifiche in VSTS e la distribuzione automatica in Azure 

 > [!NOTE]
 > La procedura seguente testa la pipeline di CI/CD con una semplice modifica al testo nell'app Web.

A questo punto, si è pronti per collaborare con un team a un'app con un processo di CI/CD che distribuisce automaticamente il lavoro più recente nel sito Web.  Ogni modifica al repository Git VSTS avvia una compilazione in VSTS e una definizione di gestione del rilascio VSTS distribuisce le modifiche in Azure.  Attenersi alla procedura seguente o usare altre tecniche per eseguire il commit delle modifiche nel repository.  Ad esempio, è possibile **clonare** il repository Git nello strumento o nell'IDE preferito e quindi eseguire il push delle modifiche in questo repository.

1. Selezionare **Codice** e quindi **File** dal menu di VSTS e passare al repository.
1. Passare alla directory **Views\Home** e quindi selezionare i **puntini di sospensione** accanto al file **Index.cshtml** e scegliere **Modifica**.

1. Apportare una modifica al file, ad esempio nel testo all'interno dei **tag div**.  In alto a destra selezionare **Esegui commit**.  Selezionare di nuovo **Esegui commit** per effettuare il push della modifica. 

1. Dopo alcuni istanti, verrà **avviata una compilazione in VSTS** e quindi verrà eseguita una versione per distribuire le modifiche.  È possibile monitorare lo **stato della compilazione** con il dashboard del progetto DevOps oppure nel browser con l'account VSTS.

1. Una volta completata la versione, **aggiornare l'applicazione** nel browser per verificare le modifiche.

## <a name="clean-up-resources"></a>Pulire le risorse

 > [!NOTE]
 > La procedura seguente elimina definitivamente le risorse.  Usare questa funzionalità solo dopo aver letto attentamente le indicazioni.

Se si stanno eseguendo test, è possibile pulire le risorse per evitare che vengano addebitati costi.  Quando il cluster del servizio Kubernetes di Azure e le risorse correlate create in questa esercitazione non sono più necessari, è possibile eliminarli usando la funzionalità **Elimina** nel dashboard del progetto DevOps di Azure.  **Fare attenzione** in quanto la funzionalità di eliminazione elimina definitivamente i dati creati dal progetto DevOps di Azure sia in Azure che in VSTS e, una volta eliminati, non sarà più possibile recuperarli.

1. Dal **portale di Azure** passare al **progetto DevOps di Azure**.
2. Nella parte **superiore destra** del dashboard selezionare **Elimina**.  Dopo aver letto la richiesta di conferma, selezionare **Sì** per **eliminare definitivamente** le risorse.

## <a name="next-steps"></a>Passaggi successivi

Facoltativamente, è possibile modificare queste definizioni di compilazione e di versione in base alle esigenze del team. È anche possibile usare questo modello di CI/CD come modello per altri progetti.  Si è appreso come:

> [!div class="checklist"]
> * Creare un progetto DevOps di Azure per un'app ASP.NET Core e il servizio Kubernetes di Azure (AKS)
> * Configurare VSTS e una sottoscrizione di Azure 
> * Esaminare il cluster del servizio Kubernetes di Azure
> * Esaminare la definizione di compilazione di integrazione continua VSTS
> * Esaminare la definizione di gestione del rilascio di distribuzione continua VSTS
> * Eseguire il commit delle modifiche in VSTS e la distribuzione automatica in Azure
> * Pulire le risorse

Per altre informazioni sull'uso del dashboard di Kubernetes vedere di seguito:

> [!div class="nextstepaction"]
> [Usare il dashboard Kubernetes](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)
