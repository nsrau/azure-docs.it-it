---
title: Creare una pipeline di CI/CD per il codice esistente con il progetto DevOps di Azure | Esercitazione di VSTS
description: Il progetto DevOps consente di iniziare a usare Azure in modo semplice. Consente di usare il proprio codice e repository di GitHub per avviare un'app in un servizio di Azure di propria scelta in pochi rapidi passaggi.
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
ms.openlocfilehash: 192992917432a64c2f9f81761e22bf7d9205703a
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205562"
---
# <a name="create-a-cicd-pipeline-for-your-existing-code-with-the-azure-devops-project"></a>Creare una pipeline di CI/CD per il codice esistente con il progetto DevOps di Azure

Il progetto DevOps di Azure presenta un'esperienza semplificata in cui è possibile usare il codice e il repository Git esistente oppure scegliere una delle applicazioni di esempio per creare una pipeline di integrazione continua (CI) e distribuzione continua (CD) in Azure.

Si apprenderà come:

> [!div class="checklist"]
> * Creare un progetto DevOps di Azure
> * Configurare l'accesso al repository GitHub e scegliere un framework
> * Configurare VSTS e una sottoscrizione di Azure 
> * Eseguire il commit delle modifiche in GitHub e la distribuzione automatica in Azure
> * Esaminare la pipeline di CI/CD VSTS
> * Configurare il monitoraggio di Azure Application Insights

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. È possibile ottenerne una gratuita tramite [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Accedere a GitHub o a un repository Git esterno contenente codice .NET, Java, PHP, Nodo, Python o Web statico.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Il progetto DevOps di Azure crea una pipeline di CI/CD in VSTS.  È possibile creare un **nuovo account VSTS** oppure usare un **account esistente**.  Il progetto DevOps di Azure crea anche **risorse di Azure** nella **sottoscrizione di Azure** di propria scelta.

1. Accedere al [portale di Microsoft Azure](https://portal.azure.com).

1. Scegliere l'icona **Nuovo** sulla barra di spostamento sinistra e quindi cercare **Progetto DevOps**.  Scegliere **Create**.

    ![Avvio del recapito continuo](_img/azure-devops-project-github/fullbrowser.png)

1. Selezionare **Usa il codice personale**.  Al termine, scegliere **Avanti**.

## <a name="configure-access-to-your-github-repository-and-choose-a-framework"></a>Configurare l'accesso al repository GitHub e scegliere un framework

1. Selezionare **GitHub**.  Facoltativamente, è possibile scegliere un **repository Git esterno**.  Scegliere il **repository** e il **ramo** che contiene l'applicazione.

1. Selezionare il **framework Web**.  Al termine, scegliere **Avanti**.

    ![.NET Framework](_img/azure-devops-project-github/webframework.png)

1. Il framework applicazione, scelto nei passaggi precedenti, determina il tipo di destinazione della distribuzione del servizio di Azure disponibile.  Selezionare il **servizio di destinazione** preferito.  Al termine, scegliere **Avanti**.

## <a name="configure-vsts-and-an-azure-subscription"></a>Configurare VSTS e una sottoscrizione di Azure 

1. Creare un **nuovo** account VSTS oppure scegliere un account **esistente**.  Scegliere un **nome** per il progetto VSTS.  Selezionare la **sottoscrizione di Azure** e la **posizione** e scegliere un **nome** per l'applicazione.  Al termine, scegliere **Fine**.

    ![Immettere le informazioni su VSTS](_img/azure-devops-project-github/vstsazureinfo.png)

1. In pochi minuti, il **dashboard del progetto** viene caricato nel portale di Azure.  Viene configurata un'applicazione di esempio in un repository nell'account VSTS, viene eseguita una compilazione e l'applicazione viene distribuita in Azure.  Questo dashboard fornisce visibilità sul **repository di codice** GitHub, la **pipeline di CI/CD VSTS** e l'**applicazione in Azure**.  A destra del dashboard selezionare **Sfoglia** per visualizzare l'applicazione in esecuzione.

    ![Visualizzazione dashboard](_img/azure-devops-project-github/dashboardnopreview.png) 
    
Il progetto DevOps di Azure configura automaticamente un trigger di compilazione e di versione di integrazione continua.  Il codice rimane nel repository GitHub o in un altro repository esterno.  

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>Eseguire il commit delle modifiche in GitHub e la distribuzione automatica in Azure 

A questo punto, si è pronti per collaborare con un team a un'app con un processo di CI/CD che distribuisce automaticamente il lavoro più recente nel sito Web.  Ogni modifica al repository GitHub avvia una compilazione in VSTS e una definizione di gestione del rilascio VSTS esegue una distribuzione in Azure.

1.  Apportare una modifica all'applicazione ed eseguire il **commit** della modifica nel repository GitHub.
2.  Dopo alcuni istanti, viene avviata una compilazione in VSTS.  È possibile monitorare lo stato della compilazione con il dashboard del progetto DevOps oppure nel browser con l'account VSTS.
3.  Dopo il completamento della compilazione, **aggiornare l'applicazione** nel browser per verificare le modifiche.

## <a name="examine-the-vsts-cicd-pipeline"></a>Esaminare la pipeline di CI/CD VSTS

Il progetto DevOps di Azure ha configurato automaticamente una pipeline di CI/CD VSTS completa nell'account VSTS.  Esplorare e personalizzare la pipeline in base alle esigenze.  Attenersi alla procedura seguente per acquisire familiarità con le definizioni di compilazione e di versione di VSTS.

1. Selezionare **Pipeline di compilazione** nella parte **superiore** del dashboard del progetto DevOps di Azure.  Questo collegamento apre una scheda del browser e la definizione di compilazione VSTS per il nuovo progetto.

1. Spostare il cursore del mouse a destra della definizione di compilazione accanto al campo **Stato**. Selezionare i **puntini di sospensione** visualizzati.  Questa azione apre un menu da cui è possibile avviare diverse attività, ad esempio accodare una nuova compilazione, sospendere una compilazione e modificare la definizione di compilazione.

1. Selezionare **Modifica**.

1. In questa visualizzazione **esaminare le diverse attività** per la definizione di compilazione.  La compilazione esegue diverse attività, ad esempio il recupero delle origini dal repository Git, il ripristino delle dipendenze e la pubblicazione degli output usati per le distribuzioni.

1. Nella parte superiore della definizione di compilazione selezionare il **nome della definizione di compilazione**.

1. Modificare il **nome** della definizione di compilazione scegliendo un testo più descrittivo.  Selezionare **Salva e accoda** e quindi **Salva**.

1. Sotto il nome della definizione di compilazione selezionare **Cronologia**.  Verrà visualizzato un log di controllo delle modifiche recenti per la compilazione.  VSTS tiene traccia di tutte le modifiche apportate alla definizione di compilazione e consente di confrontare le versioni.

1. Selezionare **Trigger**.  Il progetto DevOps di Azure ha creato automaticamente un trigger di integrazione continua e ogni commit nel repository avvia una nuova compilazione.  Facoltativamente, è possibile scegliere di includere o escludere rami dal processo di integrazione continua.

1. Selezionare **Conservazione**.  In base allo scenario specifico, è possibile indicare i criteri per conservare o rimuovere un determinato numero di compilazioni.

1. Selezionare **Compilazione e versione** e quindi scegliere **Versioni**.  Il progetto DevOps di Azure ha creato una definizione di versione VSTS per gestire le distribuzioni in Azure.

1. Sul lato sinistro del browser selezionare i **puntini di sospensione** accanto alla definizione di versione e quindi scegliere **Modifica**.

1. La definizione di versione contiene una **pipeline** che definisce il processo per la versione.  In **Artefatti** selezionare **Elimina**.  La definizione di compilazione esaminata nei passaggi precedenti produce l'output usato per l'artefatto. 

1. A destra dell'icona **Elimina** selezionare **Trigger di distribuzione continua**.  Questa definizione di versione ha un trigger di distribuzione continua abilitato, che esegue una distribuzione ogni volta che è disponibile un nuovo artefatto di compilazione.  Facoltativamente, è possibile disabilitare il trigger, in modo che le distribuzioni richiedano l'esecuzione manuale. 

1. Sul lato sinistro del browser selezionare **Attività**.  Le attività sono le operazioni eseguite dal processo di distribuzione.  In questo esempio è stata creata un'attività per la distribuzione in **Servizio app di Azure**.

1. Sul lato destro del browser selezionare **Visualizza versioni**.  Questa visualizzazione mostra una cronologia delle versioni.

1. Selezionare i **puntini di sospensione** accanto a una delle versioni e scegliere **Apri**.  In questa visualizzazione ci sono diversi menu da esplorare, ad esempio un riepilogo delle versioni, gli elementi di lavoro associati e i test.

1. Selezionare **Commit**.  Questa visualizzazione mostra i commit di codice associati alla distribuzione specifica. 

1. Selezionare **Log**.  I log contengono informazioni utili sul processo di distribuzione.  Possono essere visualizzati durante e dopo le distribuzioni.

## <a name="configure-azure-application-insights-monitoring"></a>Configurare il monitoraggio di Azure Application Insights

Con Azure Application Insights, è possibile monitorare facilmente le prestazioni e l'utilizzo dell'applicazione.  Il progetto DevOps di Azure ha configurato automaticamente una risorsa di Application Insights per l'applicazione.  È possibile configurare ulteriormente varie funzionalità di avviso e di monitoraggio in base alle esigenze.

1. Passare al dashboard del **progetto DevOps di Azure** nel portale di Azure.  In basso a destra del dashboard, scegliere il collegamento **Application Insights** per l'app.

1. Il pannello di **Application Insights** viene aperto nel portale di Azure.  Questa visualizzazione contiene informazioni di monitoraggio sull'utilizzo, le prestazioni e la disponibilità per l'app.

    ![Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Selezionare **Intervallo di tempo** e quindi scegliere **Ultima ora**.  Selezionare **Aggiorna** per filtrare i risultati.  È ora possibile visualizzare tutte le attività degli ultimi 60 minuti.  Selezionare la **x** per uscire dall'intervallo di tempo.

1. Selezionare **Avvisi** e quindi selezionare **+ Aggiungi avviso per la metrica**.  

1. Immettere un **Nome** per l'avviso.

1. Selezionare l'elenco a discesa per l'**origine dell'avviso**.  Scegliere la **risorsa del servizio app.**
<!-- Could you please confirm if this should be "Source Alter on" instead of "Source Alert on"? -->
1. L'avviso predefinito è per un **tempo di risposta del server superiore a 1 secondo**.  Selezionare l'elenco a discesa **Metrica** per esaminare le varie metriche di avviso.  È possibile configurare facilmente un'ampia gamma di avvisi per migliorare le funzionalità di monitoraggio dell'app.

1. Selezionare la casella di controllo **Invia messaggio di posta elettronica a proprietari, collaboratori e lettori**.  Facoltativamente, è possibile eseguire azioni aggiuntive quando viene generato un avviso tramite l'esecuzione di un'app per la logica di Azure.

1. Fare clic su **OK** per creare l'avviso.  Dopo alcuni istanti, l'avviso viene visualizzato come attivo nel dashboard.  Fare clic su **Esci** per uscire dall'area Avvisi e tornare al **pannello di Application Insights**.

1. Selezionare **Disponibilità** e quindi selezionare **+ Aggiungi test**. 

1. Immettere **Nome test** e quindi scegliere **Crea**.  Viene creato un semplice test ping per verificare la disponibilità dell'applicazione.  Dopo alcuni minuti, i risultati dei test sono disponibili e nel dashboard di Application Insights viene visualizzato uno stato di disponibilità.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando Servizio app di Azure e le risorse correlate create in questa guida introduttiva non sono più necessari, è possibile eliminarli usando la funzionalità **Elimina** nel dashboard del progetto DevOps di Azure.

## <a name="next-steps"></a>Passaggi successivi

Quando è stato configurato il processo di CI/CD in questa esercitazione, sono state create automaticamente definizioni di compilazione e di versione nel progetto VSTS. È possibile modificare queste definizioni di compilazione e di versione in base alle esigenze del team. Si è appreso come:

> [!div class="checklist"]
> * Creare un progetto DevOps di Azure
> * Configurare l'accesso al repository GitHub e scegliere un framework
> * Configurare VSTS e una sottoscrizione di Azure 
> * Eseguire il commit delle modifiche in GitHub e la distribuzione automatica in Azure
> * Esaminare la pipeline di CI/CD VSTS
> * Configurare il monitoraggio di Azure Application Insights

Per altre informazioni sulla pipeline VSTS, vedere questa esercitazione:

> [!div class="nextstepaction"]
> [Personalizzare il processo di distribuzione continua](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)
