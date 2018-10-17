---
title: Distribuire un'app ASP.NET in Macchine virtuali di Azure con il progetto Azure DevOps | Esercitazione di Azure DevOps Services
description: Il progetto DevOps consente di iniziare a usare Azure in modo semplice. Il progetto DevOps di Azure permette di eseguire facilmente la distribuzione dell'app ASP.NET in Macchine virtuali di Azure in pochi rapidi passaggi.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: b05e2c2c46aa9bfa8c92d3d3c5c83d018c547b9f
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299135"
---
# <a name="tutorial--deploy-your-aspnet-app-to-azure-virtual-machines-with-the-azure-devops-project"></a>Esercitazione: Distribuire un'app ASP.NET in Macchine virtuali di Azure con il progetto DevOps di Azure

Il progetto DevOps di Azure offre un'esperienza semplificata in cui è possibile usare il codice esistente e il repository Git oppure scegliere una delle applicazioni di esempio per creare una pipeline di integrazione continua (CI, Continuous Integration) e distribuzione continua (CD, Continuous Delivery) in Azure.  Il progetto DevOps crea automaticamente le risorse di Azure, come una nuova macchina virtuale di Azure, crea e configura una pipeline di versione in Azure DevOps che include una pipeline di compilazione per CI, configura una pipeline di versione per CD e quindi crea una risorsa di Azure Application Insights per il monitoraggio.

Si apprenderà come:

> [!div class="checklist"]
> * Creare un progetto Azure DevOps per un'app ASP.NET
> * Configurare Azure DevOps Services e una sottoscrizione di Azure 
> * Esaminare la pipeline di integrazione continua di Azure DevOps Services
> * Esaminare la pipeline di distribuzione continua di Azure DevOps Services
> * Eseguire il commit delle modifiche in Azure DevOps Services e la distribuzione automatica in Azure
> * Configurare il monitoraggio di Azure Application Insights
> * Pulire le risorse

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. È possibile ottenerne una gratuita tramite [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-an-azure-devops-project-for-an-aspnet-app"></a>Creare un progetto Azure DevOps per un'app ASP.NET

Il progetto Azure DevOps crea una pipeline di integrazione continua/distribuzione continua in Azure.  È possibile creare una **nuova organizzazione di Azure DevOps Services** o usare un'**organizzazione esistente**.  Il progetto DevOps di Azure crea anche **risorse di Azure**, ad esempio macchine virtuali, nella **sottoscrizione di Azure** di propria scelta.

1. Accedere al [portale di Microsoft Azure](https://portal.azure.com).

1. Scegliere l'icona **+ Nuovo** sulla barra di spostamento sinistra e quindi cercare **Progetto DevOps**.  Scegliere **Create**.

    ![Avvio del recapito continuo](_img/azure-devops-project-github/fullbrowser.png)

1. Selezionare **.NET** e quindi scegliere **Avanti**.

1. Per **Scegli un framework applicazione** selezionare **ASP.NET**  e quindi scegliere **Avanti**. 

1. Il framework applicazione, scelto nei passaggi precedenti, determina il tipo di destinazione della distribuzione del servizio di Azure disponibile.  Selezionare **Macchina virtuale** e quindi scegliere **Avanti**.

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Configurare Azure DevOps Services e una sottoscrizione di Azure

1. Creare una **nuova** organizzazione di Azure DevOps Services o selezionare un'organizzazione **esistente**.  Scegliere un **nome** per il progetto Azure DevOps.  

1. Selezionare la **sottoscrizione di Azure e Azure DevOps Services**.  È possibile selezionare facoltativamente il collegamento **Modifica** e quindi immettere altri dettagli di configurazione, ad esempio modificare la posizione delle risorse di Azure.
 
1. Immettere **Nome macchina virtuale**, **Nome utente** e **Password** per la nuova risorsa macchina virtuale di Azure e quindi scegliere **Fine**.

1. Per la preparazione della macchina virtuale di Azure saranno necessari vari minuti.  Viene configurata un'applicazione di esempio ASP.NET in un repository nell'organizzazione di Azure DevOps Services, vengono eseguite una compilazione e una versione e l'applicazione viene distribuita nella nuova macchina virtuale di Azure creata.  

    Al termine, il **dashboard del progetto** DevOps di Azure viene caricato nel portale di Azure.  È anche possibile passare al **dashboard del progetto DevOps di Azure** direttamente da **Tutte le risorse** nel **portale di Azure**.  

    Questo dashboard fornisce visibilità sul **repository di codice** di Azure DevOps Services, la **pipeline di integrazione continua/distribuzione continua di Azure** e l'**applicazione in esecuzione in Azure**.    

    ![Visualizzazione dashboard](_img/azure-devops-project-vms/dashboardnopreview.png)

1. Il progetto Azure DevOps configura automaticamente un trigger di compilazione e versione di integrazione continua che distribuisce automaticamente eventuali modifiche del codice nel repository.  È possibile configurare ulteriormente opzioni aggiuntive in Azure DevOps.  A destra del dashboard selezionare **Sfoglia** per visualizzare l'applicazione in esecuzione.
    
## <a name="examine-the-azure-devops-services-ci-pipeline"></a>Esaminare la pipeline di integrazione continua di Azure DevOps Services
 
Il progetto Azure DevOps configura automaticamente una pipeline di integrazione continua/distribuzione continua di Azure completa nell'organizzazione di Azure DevOps Services.  È possibile esplorare e personalizzare la pipeline.  Attenersi alla procedura seguente per acquisire familiarità con la pipeline di compilazione di Azure DevOps Services.

1. Selezionare **Pipeline di compilazione** nella parte **superiore** del **dashboard del progetto Azure DevOps**.  Questo collegamento apre una scheda del browser e la pipeline di compilazione di Azure DevOps Services per il nuovo progetto.

1. Spostare il cursore del mouse a destra della pipeline di compilazione accanto al campo **Stato**. Selezionare i **puntini di sospensione** visualizzati.  Verrà aperto un menu da cui è possibile eseguire diverse attività, ad esempio **accodare una nuova compilazione**, **sospendere una compilazione** e **modificare la pipeline di compilazione**.

1. Selezionare **Modifica**.

1. In questa visualizzazione **esaminare le diverse attività** per la pipeline di compilazione.  La compilazione esegue diverse attività, ad esempio il recupero delle origini dal repository GIT per Azure DevOps Services, il ripristino delle dipendenze e la pubblicazione degli output usati per le distribuzioni.

1. Nella parte superiore della pipeline di compilazione selezionare il **nome della pipeline di compilazione**.

1. Modificare il **nome** della pipeline di compilazione scegliendo un testo più descrittivo.  Selezionare **Salva e accoda** e quindi **Salva**.

1. Sotto il nome della pipeline di compilazione selezionare **Cronologia**.  Verrà visualizzato un log di controllo delle modifiche recenti per la compilazione.  Azure DevOps Services tiene traccia di tutte le modifiche apportate alla pipeline di compilazione e consente di confrontare le versioni.

1. Selezionare **Trigger**.  Il progetto Azure DevOps ha creato automaticamente un trigger di integrazione continua e ogni commit nel repository avvia una nuova compilazione.  Facoltativamente, è possibile scegliere di includere o escludere rami dal processo di integrazione continua.

1. Selezionare **Conservazione**.  In base allo scenario specifico, è possibile indicare i criteri per conservare o rimuovere un determinato numero di compilazioni.

## <a name="examine-the-azure-devops-services-cd-pipeline"></a>Esaminare la pipeline di distribuzione continua di Azure DevOps Services

Il progetto Azure DevOps crea e configura automaticamente i passaggi necessari per la distribuzione dall'organizzazione di Azure DevOps Services nella sottoscrizione di Azure.  Questi passaggi includono la configurazione di una connessione al servizio di Azure per eseguire l'autenticazione di Azure DevOps Services nella sottoscrizione di Azure.  L'automazione crea anche una pipeline di distribuzione continua di Azure DevOps Services e in questo modo fornisce la distribuzione continua nella macchina virtuale di Azure.  Attenersi alla procedura seguente per esaminare in maggiore dettaglio la pipeline di distribuzione continua di Azure DevOps Services.

1. Selezionare **Compilazione e versione** e quindi scegliere **Versioni**.  Il progetto Azure DevOps ha creato una pipeline di versione di Azure DevOps Services per gestire le distribuzioni in Azure.

1. Sul lato sinistro del browser selezionare i **puntini di sospensione** accanto alla pipeline di versione e quindi scegliere **Modifica**.

1. La pipeline di versione contiene una **pipeline** che definisce il processo per la versione.  In **Artefatti** selezionare **Elimina**.  La pipeline di compilazione esaminata nei passaggi precedenti produce l'output usato per l'artefatto. 

1. A destra dell'icona **Elimina** selezionare l'**icona** **Trigger di distribuzione continua** (a forma di fulmine).  Questa pipeline di versione ha un trigger di distribuzione continua abilitato.  Il trigger avvia una distribuzione ogni volta che è disponibile un nuovo artefatto di compilazione.  Facoltativamente, è possibile disabilitare il trigger, in modo che le distribuzioni richiedano l'esecuzione manuale. 

1. Sul lato sinistro del browser selezionare **Attività** e quindi scegliere il proprio **ambiente**.  

1. Le attività sono le operazioni eseguite dal processo di distribuzione e sono raggruppate in **Fasi**.  Esistono due **fasi** per questa pipeline di versione.  La prima fase contiene un'attività **Distribuzione gruppo di risorse di Azure** che consente di configurare la macchina virtuale per la distribuzione e aggiunge la nuova macchina virtuale a un **gruppo di distribuzione di Azure DevOps**.  Il gruppo di distribuzione della macchina virtuale in Azure DevOps gestisce gruppi logici di macchine di **destinazione della distribuzione**.

1. In questa seconda fase è stata creata un'attività **Gestione app Web IIS** per creare un sito Web IIS nella macchina virtuale.  È stata creata una seconda attività**Distribuzione app Web IIS** per distribuire il sito.

1. Sul lato destro del browser selezionare **Visualizza versioni**.  Questa visualizzazione mostra una cronologia delle versioni.

1. Selezionare i **puntini di sospensione** accanto a una delle versioni e scegliere **Apri**.  In questa visualizzazione ci sono diversi menu da esplorare, ad esempio un **riepilogo delle versioni**, gli **elementi di lavoro associati** e i **test**.

1. Selezionare **Commit**.  Questa visualizzazione mostra i commit di codice associati alla distribuzione specifica. È possibile confrontare le versioni per visualizzare le differenze di commit tra le distribuzioni.

1. Selezionare **Log**.  I log contengono informazioni utili sul processo di distribuzione.  Possono essere visualizzati durante e dopo le distribuzioni.

## <a name="commit-changes-to-azure-devops-services-and-automatically-deploy-to-azure"></a>Eseguire il commit delle modifiche in Azure DevOps Services e la distribuzione automatica in Azure 

A questo punto, si è pronti per collaborare con un team a un'app con un processo di CI/CD che distribuisce automaticamente il lavoro più recente nel sito Web.  Ogni modifica al repository GIT per Azure DevOps Services avvia una compilazione in Azure DevOps Services e una pipeline di distribuzione continua di DevOps Services esegue una distribuzione nella macchina virtuale di Azure.  Attenersi alla procedura seguente o usare altre tecniche per eseguire il commit delle modifiche nel repository.  Le modifiche al codice avviano il processo di integrazione continua/recapito continuo e le nuove modifiche vengono distribuite automaticamente nel sito Web IIS nella macchina virtuale di Azure.

1. Selezionare **Codice** dal menu di Azure DevOps Services e passare al repository.

1. Passare alla directory **Views\Home** e quindi selezionare i **puntini di sospensione** accanto al file **Index.cshtml** e scegliere **Modifica**.

1. Apportare una modifica al file, ad esempio nel testo all'interno dei **tag div**.  In alto a destra selezionare **Esegui commit**.  Selezionare di nuovo **Esegui commit** per effettuare il push della modifica. 

1. Dopo alcuni istanti verrà **avviata una compilazione in Azure DevOps Services** e quindi verrà eseguita una versione per distribuire le modifiche.  Monitorare lo **stato della compilazione** con il dashboard del progetto DevOps oppure nel browser con l'organizzazione di Azure DevOps Services.

1. Una volta completata la versione, **aggiornare l'applicazione** nel browser per verificare le modifiche.

## <a name="configure-azure-application-insights-monitoring"></a>Configurare il monitoraggio di Azure Application Insights

Con Azure Application Insights, è possibile monitorare facilmente le prestazioni e l'utilizzo dell'applicazione.  Il progetto Azure DevOps ha configurato automaticamente una risorsa di Application Insights per l'applicazione.  È possibile configurare ulteriormente varie funzionalità di avviso e di monitoraggio in base alle esigenze.

1. Passare al dashboard del **progetto DevOps di Azure** nel **portale di Azure**.  In basso a destra del dashboard, scegliere il collegamento **Application Insights** per l'app.

1. Il pannello di **Application Insights** viene aperto nel portale di Azure.  Questa visualizzazione contiene informazioni di monitoraggio sull'utilizzo, le prestazioni e la disponibilità per l'app.

    ![Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Selezionare **Intervallo di tempo** e quindi scegliere **Ultima ora**.  Selezionare **Aggiorna** per filtrare i risultati.  È ora possibile visualizzare tutte le attività degli ultimi 60 minuti.  Selezionare la **x** per uscire dall'intervallo di tempo.

1. È possibile trovare **Avvisi** e numerosi altri collegamenti utili nella parte superiore del dashboard.  Selezionare **Avvisi** e quindi selezionare **+ Aggiungi avviso per la metrica**.

1. Immettere un **Nome** per l'avviso.

1. L'avviso predefinito è per un **tempo di risposta del server superiore a 1 secondo**.  Selezionare l'elenco a discesa **Metrica** per esaminare le varie metriche di avviso.  Ad esempio, è possibile configurare **Tempo di esecuzione della richiesta ASP.NET** per un'app ASP.NET.  È possibile configurare facilmente un'ampia gamma di avvisi per migliorare le funzionalità di monitoraggio dell'app.

1. Selezionare la casella di controllo **Invia messaggio di posta elettronica a proprietari, collaboratori e lettori**.  Facoltativamente, è possibile eseguire azioni aggiuntive quando viene generato un avviso tramite l'esecuzione di un'app per la logica di Azure.

1. Fare clic su **OK** per creare l'avviso.  Dopo alcuni istanti, l'avviso viene visualizzato come attivo nel dashboard.  Fare clic su **Esci** per uscire dall'area Avvisi e tornare al **pannello di Application Insights**.

1. Selezionare **Disponibilità** e quindi selezionare **+ Aggiungi test**. 

1. Immettere **Nome test** e quindi scegliere **Crea**.  Viene così creato un semplice test ping per verificare la disponibilità dell'applicazione.  Dopo alcuni minuti, i risultati dei test sono disponibili e nel dashboard di Application Insights viene visualizzato uno stato di disponibilità.

## <a name="clean-up-resources"></a>Pulire le risorse

 > [!NOTE]
 > La procedura seguente elimina definitivamente le risorse.  Usare questa funzionalità solo dopo aver letto attentamente le indicazioni.

Se si stanno eseguendo test, è possibile pulire le risorse per evitare che vengano addebitati costi.  Quando la macchina virtuale di Azure e le risorse correlate create in questa esercitazione non sono più necessarie, è possibile eliminarle usando la funzionalità **Elimina** nel dashboard del progetto DevOps di Azure.  **Prestare attenzione** in quanto la funzionalità di eliminazione cancella definitivamente i dati creati dal progetto Azure DevOps sia in Azure che in Azure DevOps e, una volta eliminati, non sarà più possibile recuperarli.

1. Dal **portale di Azure** passare al **progetto DevOps di Azure**.
2. Nella parte **superiore destra** del dashboard selezionare **Elimina**.  Dopo aver letto la richiesta di conferma, selezionare **Sì** per **eliminare definitivamente** le risorse.

## <a name="next-steps"></a>Passaggi successivi

Facoltativamente, è possibile modificare queste pipeline di compilazione e di versione in base alle esigenze del team. È anche possibile usare questo modello di CI/CD come modello per altri progetti.  Si è appreso come:

> [!div class="checklist"]
> * Creare un progetto Azure DevOps per un'app ASP.NET
> * Configurare Azure DevOps Services e una sottoscrizione di Azure 
> * Esaminare la pipeline di integrazione continua di Azure DevOps Services
> * Esaminare la pipeline di distribuzione continua di Azure DevOps Services
> * Eseguire il commit delle modifiche in Azure DevOps Services e la distribuzione automatica in Azure
> * Configurare il monitoraggio di Azure Application Insights
> * Pulire le risorse

Per altre informazioni sulla pipeline di integrazione continua/distribuzione continua di Azure, vedere questa esercitazione:

> [!div class="nextstepaction"]
> [Personalizzare il processo di distribuzione continua](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
