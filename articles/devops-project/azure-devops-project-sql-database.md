---
title: "Esercitazione: Distribuire un'app ASP.NET e il codice di un database SQL di Azure usando Azure DevOps Projects"
description: DevOps Projects consente di iniziare a usare Azure in modo semplice. Con DevOps Projects è possibile distribuire l'app ASP.NET e il codice di un database SQL di Azure in pochi rapidi passaggi.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 32f33e4ac66ad456b5ff8807d6a1b5ea5f541fed
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52161401"
---
# <a name="tutorial-deploy-your-aspnet-app-and-azure-sql-database-code-by-using-azure-devops-projects"></a>Esercitazione: Distribuire un'app ASP.NET e il codice di un database SQL di Azure usando Azure DevOps Projects

Azure DevOps Projects offre un'esperienza semplificata in cui è possibile usare il codice esistente e il repository Git oppure scegliere un'applicazione di esempio per creare una pipeline di integrazione continua (CI, Continuous Integration) e recapito continuo (CD, Continuous Delivery) in Azure. 

DevOps Projects permette anche di:
* Creare automaticamente risorse di Azure, ad esempio un database SQL di Azure.
* Creare e configurare una pipeline di versione in Azure Pipelines che includa una pipeline di compilazione per CI.
* Configurare una pipeline di versione per CD. 
* Creare una risorsa di Azure Application Insights per il monitoraggio.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Usare Azure DevOps Projects per distribuire l'app ASP.NET e il codice del database SQL di Azure
> * Configurare Azure DevOps e una sottoscrizione di Azure 
> * Esaminare la pipeline di integrazione continua
> * Esaminare la pipeline di distribuzione continua
> * Eseguire il commit delle modifiche in Azure Repos e la distribuzione automatica in Azure
> * Connettersi al database SQL di Azure 
> * Pulire le risorse

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. È possibile ottenerne una gratuita tramite [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-a-project-in-devops-projects-for-an-aspnet-app-and-an-azure-sql-database"></a>Creare un progetto in DevOps Projects per un'app ASP.NET e un database SQL di Azure

DevOps Projects crea una pipeline CI/CD in Azure Pipelines. È possibile creare una nuova organizzazione di Azure DevOps o usare un'organizzazione esistente. DevOps Projects crea anche risorse di Azure, ad esempio un database SQL di Azure, nella sottoscrizione di Azure di propria scelta.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel riquadro a sinistra selezionare **Crea risorsa**.

1. Nella casella di ricerca, digitare **DevOps Projects**, quindi selezionare **Crea**.

    ![Dashboard di DevOps Projects](_img/azure-devops-project-github/fullbrowser.png)

1. Selezionare **.NET**, quindi **Avanti**.

1. In **Scegli un framework applicazione** selezionare **ASP.NET**.

1. Selezionare **Aggiungi un database** e quindi **Avanti**.  
    Il framework applicazione, scelto in un passaggio precedente, determina il tipo di destinazione della distribuzione del servizio di Azure disponibile. 
    
1. Selezionare **Avanti**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurare Azure DevOps e una sottoscrizione di Azure

1. Creare una nuova organizzazione di Azure DevOps o selezionare un'organizzazione esistente. 

1. Immettere un nome per il progetto Azure DevOps. 

1. Selezionare i servizi della sottoscrizione di Azure.  
    Facoltativamente, per visualizzare impostazioni di configurazione di Azure aggiuntive e identificare il nome utente nella sezione **Dettagli di accesso a server di database**, selezionare **Modifica**. Archiviare il nome utente per i passaggi successivi di questa esercitazione. Se si esegue questo passaggio facoltativo, chiudere l'area di configurazione di Azure prima di selezionare **Fine**.
 
1. Selezionare **Operazione completata**.  
    Dopo pochi minuti, il processo viene completato e il dashboard di DevOps Projects viene visualizzato nel portale di Azure. È anche possibile passare al dashboard direttamente da **Tutte le risorse** nel portale di Azure. A destra, selezionare **Sfoglia** per visualizzare l'applicazione in esecuzione.
    
## <a name="examine-the-ci-pipeline"></a>Esaminare la pipeline di integrazione continua

DevOps Projects configura automaticamente una pipeline CI/CD completa in Azure Repos. È possibile esplorare e personalizzare la pipeline. Per acquisire familiarità con la pipeline di compilazione di Azure DevOps, eseguire questa procedura:

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
    Questo riquadro mostra un log di controllo delle modifiche recenti per la compilazione. Azure Pipelines tiene traccia di tutte le modifiche apportate alla pipeline di compilazione e consente di confrontare le versioni.

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

1. A destra dell'icona **Elimina** selezionare **Trigger di distribuzione continua**.  
    Questa pipeline di versione ha un trigger di distribuzione continua abilitato, che esegue una distribuzione ogni volta che è disponibile un nuovo artefatto di compilazione. Facoltativamente, è possibile disabilitare il trigger, in modo che le distribuzioni richiedano l'esecuzione manuale. 

    DevOps Projects configura una password SQL casuale e la usa per la pipeline di versione.
    
1. A sinistra selezionare **Variabili**. 

  > [!NOTE]
  > Eseguire questo passaggio solo se è stata modificata la password di SQL Server. C'è un'unica variabile password.
  
1. Accanto alla casella **Valore**, selezionare l'icona del lucchetto, immettere la nuova password e quindi selezionare **Salva**.

1. A sinistra, selezionare **Attività**e quindi selezionare l'ambiente.  
    Le attività sono le operazioni eseguite dal processo di distribuzione e sono raggruppate in fasi. Questa pipeline di versione prevede un'unica fase, che contiene un'attività *Distribuzione servizio app di Azure* e *Distribuzione database SQL di Azure*.

1. Selezionare l'attività *Esegui attività SQL di Azure* ed esaminare le diverse proprietà usate per la distribuzione SQL.  
    In **Pacchetto di distribuzione**, l'attività usa un file *DACPAC SQL*.

1. A destra, selezionare **Visualizza versioni** per visualizzare una cronologia delle versioni.

1. Selezionare i puntini di sospensione (...) accanto a una versione e quindi selezionare **Apri**.  
    È possibile esplorare diversi menu, ad esempio un riepilogo delle versioni, gli elementi di lavoro associati e i test.

1. Selezionare **Commit**.  
    Questa visualizzazione mostra i commit di codice associati a questa distribuzione. È possibile confrontare le versioni per visualizzare le differenze di commit tra le distribuzioni.

1. Selezionare **Log**.  
    I log contengono informazioni utili sul processo di distribuzione. È possibile visualizzarli durante e dopo le distribuzioni.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Eseguire il commit delle modifiche in Azure Repos e la distribuzione automatica in Azure 

 > [!NOTE]
 > La procedura seguente verifica la pipeline CI/CD con una modifica di testo semplice. Per testare il processo di distribuzione SQL, è possibile facoltativamente apportare una modifica dello schema di SQL Server nella tabella.

A questo punto, si è pronti per collaborare con un team a un'app usando un processo di CI/CD che distribuisce automaticamente il lavoro più recente nel sito Web. Ogni modifica al repository Git avvia una compilazione in DevOps di Azure e una pipeline CD esegue una distribuzione in Azure. Seguire la procedura in questa sezione o usare altre tecniche per eseguire il commit delle modifiche nel repository. Le modifiche al codice avviano il processo di CI/CD e la distribuzione automatica delle modifiche in Azure.

1. Nel riquadro a sinistra selezionare **Codice** e quindi passare al repository.

1. Passare alla directory *SampleWebApplication\Views\Home* e quindi selezionare i puntini di sospensione (...) accanto al file *Index.cshtml* e selezionare **Modifica**. 

1. Apportare una modifica al file, ad esempio aggiungere del testo all'interno di uno dei tag div. 

1. In alto a destra, selezionare **Commit**, quindi selezionare **Commit** nuovamente per eseguire il push della modifica.  
    Dopo alcuni istanti verrà avviata una compilazione in Azure DevOps e verrà eseguita una versione per distribuire le modifiche. Monitorare lo stato della compilazione nel dashboard di DevOps Projects oppure nel browser con l'organizzazione di Azure DevOps.

1. Quando la versione sarà completa, aggiornare l'applicazione per verificare le modifiche.

## <a name="connect-to-the-azure-sql-database"></a>Connettersi al database SQL di Azure

Per connettersi al database SQL di Azure, sono necessarie le autorizzazioni appropriate.

1. Nel dashboard di DevOps Projects selezionare **Database SQL** per passare alla pagina di gestione per il database SQL.
   
1. Selezionare **Imposta firewall server** e quindi **Aggiungi IP client**. 

1. Selezionare **Salva**.  
    L'indirizzo IP del client può ora accedere alla risorsa SQL Server di Azure.

1. Tornare al riquadro **Database SQL**. 

1. A destra, selezionare il nome del server per passare alla pagina di configurazione per **SQL Server**.

1. Selezionare **Reimposta password**, immettere una password per l'account di accesso amministratore di SQL Server e quindi selezionare **Salva**.  
    Assicurarsi di conservare questa password per usarla più avanti in questa esercitazione.

    È facoltativamente possibile usare strumenti client come SQL Server Management Studio o Visual Studio per connettersi a SQL Server e al database SQL di Azure. Per la connessione, usare la proprietà **Nome server**.

    Se il nome utente del database non è stato modificato durante la configurazione iniziale di DevOps Projects, il nome utente è la parte locale dell'indirizzo di posta elettronica. Se, ad esempio, l'indirizzo di posta elettronica è *johndoe@microsoft.com*, il nome utente è *johndoe*.

 > [!NOTE]
 > Se si modifica la password per l'accesso SQL, è necessario modificare la password nella variabile della pipeline di versione, come descritto nella sezione "Esaminare la pipeline di distribuzione continua".

## <a name="clean-up-resources"></a>Pulire le risorse

Se si stanno eseguendo test, è possibile pulire le risorse per evitare che vengano addebitati costi. Quando non sono più necessari, è possibile eliminare il database SQL di Azure e le risorse correlate create in questa esercitazione. A tale scopo, usare la funzionalità **Elimina** nel dashboard di DevOps Projects.

> [!IMPORTANT]
> La procedura seguente elimina definitivamente le risorse. La funzionalità di *eliminazione* cancella definitivamente i dati creati dal progetto in DevOps Projects sia in Azure che in Azure DevOps e non sarà più possibile recuperarli. Usare questa procedura solo dopo aver letto attentamente le istruzioni visualizzate.

1. Nel portale di Azure passare al dashboard di DevOps Projects.
2. In alto a destra selezionare **Elimina**. 
3. Al prompt, selezionare **Sì** per *eliminare definitivamente* le risorse.

## <a name="next-steps"></a>Passaggi successivi

Facoltativamente, è possibile modificare queste pipeline di compilazione e di versione in base alle esigenze del team. È anche possibile usare questo modello di CI/CD come modello per altre pipeline. Questa esercitazione illustra come:

> [!div class="checklist"]
> * Usare Azure DevOps Projects per distribuire l'app ASP.NET e il codice del database SQL di Azure
> * Configurare Azure DevOps e una sottoscrizione di Azure 
> * Esaminare la pipeline di integrazione continua
> * Esaminare la pipeline di distribuzione continua
> * Eseguire il commit delle modifiche in Azure Repos e la distribuzione automatica in Azure
> * Connettersi al database SQL di Azure 
> * Pulire le risorse

Per altre informazioni sulla pipeline CI/CD, vedere:

> [!div class="nextstepaction"]
> [Definire la pipeline di distribuzione continua (CD) in più fasi](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Video

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]
