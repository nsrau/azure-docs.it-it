---
title: Distribuire l'app in Azure e Azure Stack | Documenti Microsoft
description: Informazioni su come distribuire le App in Azure e Azure Stack con una pipeline di CI/CD ibrido.
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.custom: mvc
ms.openlocfilehash: 6c073376db196b7d6c73c38d6a0a7b2c24949528
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-apps-to-azure-and-azure-stack"></a>Distribuire le App in Azure e Azure Stack
*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Una configurazione ibrida [integrazione continua](https://www.visualstudio.com/learn/what-is-continuous-integration/)/[il recapito continuo](https://www.visualstudio.com/learn/what-is-continuous-delivery/)pipeline (CI/CD) consente di compilare, testare e distribuire l'app in più aree.  In questa esercitazione si compila un ambiente di esempio per informazioni su come una pipeline di CI/CD ibrido consente di:
 
> [!div class="checklist"]
> * Avviare una nuova compilazione in base a codice commit nel repository di Visual Studio Team Services (VSTS).
> * Consente di distribuire automaticamente il codice appena compilato in Azure per il test di accettazione utente.
> * Una volta il codice ha superato i test, distribuire automaticamente allo Stack di Azure. 


## <a name="prerequisites"></a>Prerequisiti
Alcuni componenti necessari per creare una pipeline di CI/CD ibrida e potrebbero richiedere del tempo di preparazione.  Se si dispone già di alcuni di questi componenti, verificare che soddisfino i requisiti prima di iniziare.

In questo argomento si presuppone inoltre che si dispone di conoscenza dello Stack di Azure e Azure. Se si desidera per altre informazioni prima di procedere, assicurarsi di avviare con questi argomenti:

- [Introduzione a Azure](https://docs.microsoft.com/azure/fundamentals-introduction-to-azure)
- [Concetti chiave di Azure Stack](../azure-stack-key-features.md)

### <a name="azure"></a>Azure
 - Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
 - Creare un [App Web](../../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md)e configurarlo per [pubblicazione FTP](../../app-service/app-service-deploy-ftp.md).  Prendere nota dell'URL del nuovo Web App, perché è usato in un secondo momento.


### <a name="azure-stack"></a>Azure Stack
 - [Distribuire Azure Stack](../azure-stack-run-powershell-script.md).  In genere, l'installazione richiede alcune ore per completare, pertanto, pianificare di conseguenza.
 - Distribuire [servizio App](../azure-stack-app-service-deploy.md) servizi PaaS allo Stack di Azure.
 - Creare un'App Web e configurarlo per [pubblicazione FTP](../azure-stack-app-service-enable-ftp.md).  Prendere nota dell'URL del nuovo Web App, perché è usato in un secondo momento.  

### <a name="developer-tools"></a>Strumenti per sviluppatori
 - Creare un [dell'area di lavoro di Visual Studio Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services).  Il processo di iscrizione viene creato un progetto denominato "MyFirstProject".  
 - [Installare Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) e [Accedi a Visual Studio Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services#connect-and-share-code-from-visual-studio)
 - Connettersi al progetto e [clonare localmente](https://www.visualstudio.com/docs/git/gitquickstart).
 - Creare un [pool di agenti](https://www.visualstudio.com/docs/build/concepts/agents/pools-queues#creating-agent-pools-and-queues) in Visual Studio Team Services.
 - Installare Visual Studio e distribuire un [agente di compilazione VSTS](https://www.visualstudio.com/docs/build/actions/agents/v2-windows) a una macchina virtuale nello Stack di Azure. 
 

## <a name="create-app--push-to-vsts"></a>Creare app & push per Visual Studio Team Services

### <a name="create-application"></a>Crea applicazione
In questa sezione un'applicazione ASP.NET semplice creare e inviare a Visual Studio Team Services.  Questi passaggi rappresentano il flusso di lavoro di sviluppo normale e può essere adattati per linguaggi e strumenti di sviluppo. 

1.  Aprire Visual Studio.
2.  Dall'area di Team Explorer e **soluzioni...**  area, fare clic su **New**.
3.  Selezionare **Visual c#** > **Web** > **applicazione Web ASP.NET (.NET Framework)**.
4.  Specificare un nome per l'applicazione e fare clic su **OK**.
5.  Nella schermata successiva, mantenere le impostazioni predefinite (Web Form) e fare clic su **OK**.

### <a name="commit-and-push-changes-to-vsts"></a>Eseguire il commit e push delle modifiche in Visual Studio Team Services
1.  Utilizzando Team Explorer in Visual Studio, selezionare l'elenco a discesa e fare clic su **modifiche**.
2.  Specificare un messaggio di conferma e selezionare **Commit di tutti**. Può essere richiesto di salvare il file della soluzione, fare clic su Sì per salvare tutti.
3.  Una volta eseguito il commit, Visual Studio consente di sincronizzare le modifiche al progetto. Selezionare **Sincronizza**.

    ![immagine che illustra la schermata di commit, una volta completato il commit](./media/azure-stack-solution-pipeline/image1.png)

4.  Nella scheda sincronizzazione in *in uscita*, viene visualizzato il commit di nuovo.  Selezionare **Push** per sincronizzare la modifica in Visual Studio Team Services.

    ![passaggi di sincronizzazione con immagine](./media/azure-stack-solution-pipeline/image2.png)

### <a name="review-code-in-vsts"></a>Rivedere il codice in Visual Studio Team Services
Dopo aver eseguito il commit di una modifica e inserite in Visual Studio Team Services, controllare il codice dal portale di Visual Studio Team Services.  Selezionare **codice**e quindi **file** nel menu a discesa.  È possibile visualizzare la soluzione che è stato creato.

## <a name="create-build-definition"></a>Creare una definizione di compilazione
Il processo di compilazione definisce compilato e compresso per la distribuzione in ogni caso di commit delle modifiche al codice dell'applicazione. In questo esempio, si usa il modello incluso per configurare il processo di compilazione per un'applicazione ASP.NET, anche se questa configurazione può essere adattata a seconda dell'applicazione.

1.  Accedi all'area di lavoro di Visual Studio Team Services da un web browser.
2.  Intestazione, selezionare **compilazione & versione** e quindi **compilazioni**.
3.  Fare clic su **+ nuova definizione**.
4.  Nell'elenco dei modelli, selezionare **ASP.NET (anteprima)** e selezionare **applica**.
5.  Modificare il *argomenti MSBuild* campo *Compila soluzione* passaggio a:

    `/p:DeployOnBuild=True /p:WebPublishMethod=FileSystem /p:DeployDefaultTarget=WebPublish /p:publishUrl="$(build.artifactstagingdirectory)\\"`

6.  Selezionare il **opzioni** scheda e selezionare la coda dell'agente per l'agente di compilazione è stato distribuito a una macchina virtuale nello Stack di Azure. 
7.  Selezionare il **trigger** scheda e abilitare **integrazione continua**.
7.  Fare clic su **Salva e coda** e quindi selezionare **salvare** nell'elenco a discesa. 

## <a name="create-release-definition"></a>Creare una definizione della versione
Il processo di rilascio definisce la modalità di distribuzione in un ambiente di compilazioni nel passaggio precedente.  In questa esercitazione verrà pubblicato l'applicazione ASP.NET con FTP a un'App Web di Azure. Per configurare una versione per Azure, attenersi alla procedura seguente:

1.  Intestazione di Visual Studio Team Services, selezionare **compilazione & versione** e quindi **versioni**.
2.  Fare clic su verde **+ nuova definizione**.
3.  Selezionare **vuoto** e fare clic su **Avanti**.
4.  Selezionare la casella per *distribuzione continua*, quindi fare clic su **crea**.

Ora che hai creato una definizione di versione vuoto ed associato alla compilazione, è aggiungere passaggi per l'ambiente Azure:

1.  Fare clic su verde  **+**  per aggiungere le attività.
2.  Selezionare **tutti**, quindi nell'elenco, aggiungere **FTP caricare** e selezionare **Chiudi**.
3.  Selezionare il **FTP caricare** si è appena aggiunta di attività e configurare i parametri seguenti:
    
    | Parametro | Valore |
    | ----- | ----- |
    |Metodo di autenticazione| Immettere le credenziali|
    |URL server | URL dell'App Web FTP recuperato dal portale di Azure |
    |Username | Nome utente configurato al momento della creazione di credenziali FTP per l'App Web |
    |Password | Password creata quando si stabiliscono le credenziali FTP per l'App Web|
    |Directory di origine | $(System.DefaultWorkingDirectory)\**\ |
    |Directory remota | /Site/wwwroot / |
    |Mantenere i percorsi di file | Abilitato (selezionato)|

4.  Fare clic su **Save**

Infine, configurare la definizione di versione per l'utilizzo di pool di agenti che contiene l'agente distribuito utilizzando la procedura seguente:
1.  Selezionare la definizione di versione e fare clic su **modifica**.
2.  Selezionare **Esegui su agente** dalla colonna centrale.  Nella colonna destra, selezionare la coda dell'agente che contiene l'agente di compilazione in esecuzione nello Stack di Azure.  
    ![configurazione di visualizzazione immagine della definizione di versione da utilizzare una coda specifica](./media/azure-stack-solution-pipeline/image3.png)


## <a name="deploy-your-app-to-azure"></a>Distribuire l'app in Azure
Questo passaggio Usa la pipeline CI/CD appena compilata per distribuire l'applicazione ASP.NET in un'App Web in Azure. 

1.  Nell'intestazione in Visual Studio Team Services, selezionare **compilazione & versione**, quindi selezionare **compilazioni**.
2.  Fare clic su **...**  della definizione di compilazione creata in precedenza, quindi seleziona **Accoda nuova compilazione**.
3.  Accettare le impostazioni predefinite e fare clic su **Ok**.  La compilazione inizia e viene visualizzato lo stato di avanzamento.
4.  Una volta completata la compilazione, è possibile tenere traccia dello stato selezionando **compilazione & versione** e selezionando **versioni**.
5.  Una volta completata la compilazione, visitare il sito Web utilizzando l'URL indicato durante la creazione di App Web.    


## <a name="add-azure-stack-to-pipeline"></a>Aggiungere il pipeline di Stack di Azure
Ora che hai testato CI/CD pipeline con la distribuzione in Azure, è necessario aggiungere Azure Stack alla pipeline.  Nei passaggi seguenti, creare un nuovo ambiente e aggiungere un'attività FTP caricare per distribuire l'app allo Stack di Azure.  È inoltre possibile aggiungere un responsabile approvazione versione, che funge da simulare "off" firma in una versione di codice allo Stack di Azure.  

1.  Nella definizione di versione, selezionare **+ Aggiungi ambiente** e **creare un nuovo ambiente**.
2.  Selezionare **vuoto**, fare clic su **Avanti**.
3.  Selezionare **utenti specifici** e specificare l'account.  Selezionare **Create**.
4.  Rinominare l'ambiente selezionando il nome esistente e digitando *Azure Stack*.
5.  A questo punto, quindi selezionare l'ambiente dello Stack di Azure, selezione **aggiungere attività**.
6.  Selezionare il **FTP caricare** attività e selezionare **Aggiungi**, quindi selezionare **Chiudi**.


### <a name="configure-ftp-task"></a>Configurare l'attività FTP
Dopo aver creato una versione, è possibile configurare i passaggi necessari per la pubblicazione per l'App Web nello Stack di Azure.  Come è configurata l'attività di caricamento FTP per Azure, si configura l'attività per lo Stack di Azure:

1.  Selezionare il **FTP caricare** si è appena aggiunta di attività e configurare i parametri seguenti:
    
    | Parametro | Valore |
    | -----     | ----- |
    |Metodo di autenticazione| Immettere le credenziali|
    |URL server | URL dell'App Web FTP recuperato dal portale di Azure Stack |
    |Username | Nome utente configurato al momento della creazione di credenziali FTP per l'App Web |
    |Password | Password creata quando si stabiliscono le credenziali FTP per l'App Web|
    |Directory di origine | $(System.DefaultWorkingDirectory)\**\ |
    |Directory remota | /Site/wwwroot /|
    |Mantenere i percorsi di file | Abilitato (selezionato)|

2.  Fare clic su **Save**

Infine, configurare la definizione di versione per l'utilizzo di pool di agenti che contiene l'agente distribuito utilizzando la procedura seguente:
1.  Selezionare la definizione di versione e fare clic su **modifica**
2.  Selezionare **Esegui su agente** dalla colonna centrale. Nella colonna destra, selezionare la coda dell'agente che contiene l'agente di compilazione in esecuzione nello Stack di Azure.  
    ![configurazione di visualizzazione immagine della definizione di versione da utilizzare una coda specifica](./media/azure-stack-solution-pipeline/image3.png)

## <a name="deploy-new-code"></a>Distribuire nuovo codice
È ora possibile testare la pipeline di CI/CD ibrido, con il passaggio finale pubblicazione allo Stack di Azure.  In questa sezione, modificare il piè di pagina del sito e avviare la distribuzione tramite la pipeline.  Al termine dell'operazione, si noterà delle modifiche distribuite in Azure per la revisione, quindi quando si approva la versione, vengono pubblicati allo Stack di Azure.

1. In Visual Studio, aprire il *Site. master* file e modificare questa riga:
    
    `
        <p>&copy; <%: DateTime.Now.Year %> - My ASP.NET Application</p>
    `

    con questo:

    `
        <p>&copy; <%: DateTime.Now.Year %> - My ASP.NET Application delivered by VSTS, Azure, and Azure Stack</p>
    `
3.  Il commit delle modifiche e sincronizzare con Visual Studio Team Services.  
4.  Dall'area di lavoro di Visual Studio Team Services, verificare lo stato della compilazione selezionando **compilazione & versione** > **di compilazione**
5.  Si noterà una compilazione in corso.  Fare doppio clic sullo stato ed è possibile controllare lo stato di compilazione.  Una volta "Compilazione completata" viene visualizzato nella console, passare a controllare la versione **compilazione & versione** > **versione**.  Fare doppio clic sulla versione di.
6.  Si riceverà la notifica che una versione è necessario esaminare. Controllare l'URL dell'App Web e verificare che le nuove modifiche siano presenti.  Approvare il rilascio in Visual Studio Team Services.
    ![configurazione di visualizzazione immagine della definizione di versione da utilizzare una coda specifica](./media/azure-stack-solution-pipeline/image4.png)
    
7.  Verificare la pubblicazione in Azure Stack sia completa, visitare il sito Web utilizzando l'URL indicato durante la creazione di App Web.
    ![immagine che mostra ASP. NEt app con piè di pagina modificata](./media/azure-stack-solution-pipeline/image5.png)


È ora possibile utilizzare la nuova pipeline CI/CD ibrida come blocco predefinito per altri modelli di cloud ibrido.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato descritto come creare una configurazione ibrida di pipeline che CI/CD:

> [!div class="checklist"]
> * Inizializza una nuova compilazione in base al codice viene eseguito il commit nel repository di Visual Studio Team Services (VSTS).
> * Distribuisce automaticamente il codice appena compilato in Azure per il test di accettazione utente.
> * Una volta il codice ha superato i test, viene distribuito automaticamente allo Stack di Azure. 

Dopo aver creato una pipeline di CI/CD ibrida, continuare per imparare a sviluppare applicazioni per lo Stack di Azure.

> [!div class="nextstepaction"]
> [Sviluppare per Azure Stack](azure-stack-developer.md)


