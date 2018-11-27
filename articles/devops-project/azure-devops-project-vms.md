---
title: "Esercitazione: Distribuire un'app ASP.NET in Macchine virtuali di Azure usando Azure DevOps Projects"
description: DevOps Projects permette di iniziare a usare facilmente Azure e di distribuire l'app ASP.NET in Macchine virtuali di Azure in pochi rapidi passaggi.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 05643f342d51d99645d3c9204d6e63adcf2a0a73
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52165697"
---
# <a name="tutorial-deploy-your-aspnet-app-to-azure-virtual-machines-by-using-azure-devops-projects"></a>Esercitazione: Distribuire un'app ASP.NET in Macchine virtuali di Azure usando Azure DevOps Projects

Azure DevOps Projects offre un'esperienza semplificata in cui è possibile usare il codice esistente e il repository Git oppure scegliere un'applicazione di esempio per creare una pipeline di integrazione continua (CI, Continuous Integration) e recapito continuo (CD, Continuous Delivery) in Azure. 

DevOps Projects permette anche di:
* Creare automaticamente risorse di Azure, ad esempio una nuova macchina virtuale di Azure (VM).
* Creare e configurare una pipeline di versione in Azure DevOps che includa una pipeline di compilazione per CI.
* Configurare una pipeline di versione per CD. 
* Creare una risorsa di Azure Application Insights per il monitoraggio.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Usare DevOps Projects per distribuire la propria app ASP.NET
> * Configurare Azure DevOps e una sottoscrizione di Azure 
> * Esaminare la pipeline di integrazione continua
> * Esaminare la pipeline di distribuzione continua
> * Eseguire il commit delle modifiche in Azure Repos e la distribuzione automatica in Azure
> * Configurare il monitoraggio di Azure Application Insights
> * Pulire le risorse

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. È possibile ottenerne una gratuita tramite [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-projects-to-deploy-your-aspnet-app"></a>Usare DevOps Projects per distribuire la propria app ASP.NET

DevOps Projects crea una pipeline CI/CD in Azure Pipelines. È possibile creare una nuova organizzazione di Azure DevOps o usare un'organizzazione esistente. DevOps Projects crea anche risorse di Azure, ad esempio macchine virtuali, nella sottoscrizione di Azure di propria scelta.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel riquadro a sinistra selezionare **Nuovo**.

1. Nella casella di ricerca, digitare **DevOps Projects**, quindi selezionare **Crea**.

    ![Dashboard di DevOps Projects](_img/azure-devops-project-github/fullbrowser.png)

1. Selezionare **.NET**, quindi **Avanti**.

1. In **Scegli un framework applicazione** selezionare **ASP.NET** e quindi selezionare **Avanti**.  
    Il framework applicazione, scelto in un passaggio precedente, determina il tipo di destinazione della distribuzione del servizio di Azure disponibile. 

1. Selezionare la macchina virtuale e quindi scegliere **Avanti**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurare Azure DevOps e una sottoscrizione di Azure

1. Creare una nuova organizzazione di Azure DevOps o selezionare un'organizzazione esistente. 

1. Immettere un nome per il progetto Azure DevOps. 

1. Selezionare i servizi della sottoscrizione di Azure.  
    Facoltativamente, è possibile selezionare **Modifica** e quindi immettere altri dettagli di configurazione, ad esempio la posizione delle risorse di Azure.
 
1. Immettere un nome della macchina virtuale, nome utente e password per la nuova risorsa macchina virtuale di Azure e quindi selezionare **Fine**.  
    La macchina virtuale di Azure sarà pronta dopo alcuni minuti. Viene configurata un'applicazione di esempio ASP.NET in un repository nell'organizzazione di Azure DevOps, vengono eseguite una compilazione e una versione e l'applicazione viene distribuita nella nuova macchina virtuale di Azure creata. 

    Dopo il completamento, il dashboard di DevOps Projects viene visualizzato nel portale di Azure. È anche possibile passare al dashboard direttamente da **Tutte le risorse** nel portale di Azure. 

    Il dashboard fornisce visibilità su repository di codice Azure DevOps, sulla pipeline di CI/CD e sull'applicazione in esecuzione in Azure.   

    ![Visualizzazione dashboard](_img/azure-devops-project-vms/dashboardnopreview.png)

DevOps Projects configura automaticamente un trigger di compilazione e versione di integrazione continua che distribuisce le modifiche del codice nel repository. È possibile configurare ulteriormente opzioni aggiuntive in Azure DevOps. Selezionare **Sfoglia** per visualizzare l'applicazione in esecuzione.
    
## <a name="examine-the-ci-pipeline"></a>Esaminare la pipeline di integrazione continua
 
DevOps Projects ha configurato automaticamente una pipeline CI/CD in Azure Pipelines. È possibile esplorare e personalizzare la pipeline. Per acquisire familiarità con la pipeline di compilazione eseguire questa procedura:

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

## <a name="examine-the-cd-pipeline"></a>Esaminare la pipeline di distribuzione continua

DevOps Projects crea e configura automaticamente i passaggi necessari per la distribuzione dall'organizzazione di Azure DevOps nella sottoscrizione di Azure. Questi passaggi includono la configurazione di una connessione al servizio di Azure per eseguire l'autenticazione di Azure DevOps nella sottoscrizione di Azure. L'automazione crea anche una pipeline CD, che fornisce la distribuzione continua nella macchina virtuale di Azure. Per altre informazioni sulla pipeline CD di Azure DevOps, eseguire le operazioni seguenti:

1. Selezionare **Compilazione e versione** e quindi **Versioni**.  
    DevOps Projects crea una pipeline di versione per gestire le distribuzioni in Azure.

1. Selezionare i puntini di sospensione (...) accanto alla pipeline di versione e quindi scegliere **Modifica**.  
    La pipeline di versione contiene una *pipeline* che definisce il processo per la versione.

1. In **Artefatti** selezionare **Elimina**.  
    La pipeline di compilazione esaminata nei passaggi precedenti produce l'output usato per l'artefatto. 

1. Accanto all'icona **Elimina** selezionare **Trigger di distribuzione continua**.  
    Questa pipeline di versione ha un trigger di distribuzione continua abilitato, che esegue una distribuzione ogni volta che è disponibile un nuovo artefatto di compilazione. Facoltativamente, è possibile disabilitare il trigger, in modo che le distribuzioni richiedano l'esecuzione manuale. 

1. A sinistra, selezionare **Attività**e quindi selezionare l'ambiente.  
    Le attività sono le operazioni eseguite dal processo di distribuzione e sono raggruppate in fasi. La pipeline di versione si verifica in due fasi:
    * La prima fase contiene un'attività Distribuzione gruppo di risorse di Azure che esegue due operazioni:
      * Configura la macchina virtuale per la distribuzione
      * Aggiunge la nuova macchina virtuale a un gruppo di distribuzione di Azure DevOps. Il gruppo di distribuzione della macchina virtuale in Azure DevOps gestisce gruppi logici di macchine di destinazione della distribuzione.
    * Nella seconda fase, un'attività Gestione app Web IIS crea un sito Web IIS nella macchina virtuale. Viene creata una seconda attività Distribuzione app Web IIS per distribuire il sito.

1. A destra, selezionare **Visualizza versioni** per visualizzare una cronologia delle versioni.

1. Selezionare i puntini di sospensione (...) accanto a una versione e quindi selezionare **Apri**.  
    È possibile esplorare diversi menu, ad esempio un riepilogo delle versioni, gli elementi di lavoro associati e i test.

1. Selezionare **Commit**.  
    Questa visualizzazione mostra i commit di codice associati a questa distribuzione. È possibile confrontare le versioni per visualizzare le differenze di commit tra le distribuzioni.

1. Selezionare **Log**.  
    I log contengono informazioni utili sul processo di distribuzione. È possibile visualizzarli durante e dopo le distribuzioni.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Eseguire il commit delle modifiche in Azure Repos e la distribuzione automatica in Azure 

A questo punto, si è pronti per collaborare con un team a un'app usando un processo di CI/CD che distribuisce automaticamente il lavoro più recente nel sito Web. Ogni modifica al repository Git avvia una compilazione in DevOps di Azure e una pipeline CD esegue una distribuzione in Azure. Seguire la procedura in questa sezione o usare altre tecniche per eseguire il commit delle modifiche nel repository. Le modifiche al codice avviano il processo CI/CD e le modifiche vengono distribuite automaticamente nel sito Web IIS nella macchina virtuale di Azure.

1. Nel riquadro a sinistra selezionare **Codice** e quindi passare al repository.

1. Passare alla directory *Views\Home*, selezionare i puntini di sospensione (...) accanto al file *Index.cshtml* e scegliere **Modifica**.

1. Apportare una modifica al file, ad esempio aggiungere del testo all'interno di uno dei tag div. 

1. In alto a destra, selezionare **Commit**, quindi selezionare **Commit** nuovamente per eseguire il push della modifica.  
    Dopo alcuni istanti verrà avviata una compilazione in Azure DevOps e verrà eseguita una versione per distribuire le modifiche. Monitorare lo stato della compilazione nel dashboard di DevOps Projects oppure nel browser con l'organizzazione di Azure DevOps.

1. Quando la versione sarà completa, aggiornare l'applicazione per verificare le modifiche.

## <a name="configure-azure-application-insights-monitoring"></a>Configurare il monitoraggio di Azure Application Insights

Con Azure Application Insights, è possibile monitorare facilmente le prestazioni e l'utilizzo dell'applicazione. DevOps Projects configura automaticamente una risorsa di Application Insights per l'applicazione. È possibile configurare ulteriormente varie funzionalità di avviso e di monitoraggio in base alle esigenze.

1. Nel portale di Azure passare al dashboard di DevOps Projects. 

1. In basso a destra, selezionare il collegamento **Application Insights** per l'app.  
    Viene visualizzato il riquadro di **Application Insights**. Questa visualizzazione contiene informazioni di monitoraggio sull'utilizzo, le prestazioni e la disponibilità per l'app.

    ![Riquadro di Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Selezionare **Intervallo di tempo** e quindi selezionare **Ultima ora**. Selezionare **Aggiorna** per filtrare i risultati.  
    È ora possibile visualizzare tutte le attività degli ultimi 60 minuti. 
    
1. Selezionare la **x** per uscire dall'intervallo di tempo.

1. Selezionare **Avvisi** e quindi selezionare **Aggiungi avviso per la metrica**. 

1. Immettere un nome per l'avviso.

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

Se si stanno eseguendo test, è possibile pulire le risorse per evitare che vengano addebitati costi. Quando non sono più necessarie, è possibile eliminare la macchina virtuale di Azure e le risorse correlate create in questa esercitazione. A tale scopo, usare la funzionalità **Elimina** nel dashboard di DevOps Projects. 

> [!IMPORTANT]
> La procedura seguente elimina definitivamente le risorse. La funzionalità di *eliminazione* cancella definitivamente i dati creati dal progetto in DevOps Projects sia in Azure che in Azure DevOps e non sarà più possibile recuperarli. Usare questa procedura solo dopo aver letto attentamente le istruzioni visualizzate.

1. Nel portale di Azure passare al dashboard di DevOps Projects.
1. In alto a destra selezionare **Elimina**. 
1. Al prompt, selezionare **Sì** per *eliminare definitivamente* le risorse.

Facoltativamente, è possibile modificare queste pipeline di compilazione e di versione in base alle esigenze del team. È anche possibile usare questo modello di CI/CD come modello per altre pipeline. 

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Usare DevOps Projects per distribuire la propria app ASP.NET
> * Configurare Azure DevOps e una sottoscrizione di Azure 
> * Esaminare la pipeline di integrazione continua
> * Esaminare la pipeline di distribuzione continua
> * Eseguire il commit delle modifiche in Azure Repos e la distribuzione automatica in Azure
> * Configurare il monitoraggio di Azure Application Insights
> * Pulire le risorse

Per altre informazioni sulla pipeline CI/CD, vedere:

> [!div class="nextstepaction"]
> [Definire la pipeline di distribuzione continua (CD) in più fasi](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
