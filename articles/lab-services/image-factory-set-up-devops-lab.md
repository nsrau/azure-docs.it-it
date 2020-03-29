---
title: Eseguire una factory di immagini da Azure DevOps in Azure DevTest LabsRun an image factory from Azure DevOps in Azure DevTest Labs
description: Questo articolo illustra tutti i preparativi necessari per eseguire la factory di immagini da Azure DevOps (in precedenza Visual Studio Team Services).
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: bb67f765684c77ed5f8527226bef578e450579e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76758683"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Eseguire una factory di immagini da Azure DevOps
Questo articolo illustra tutti i preparativi necessari per eseguire la factory di immagini da Azure DevOps (in precedenza Visual Studio Team Services).

> [!NOTE]
> Qualsiasi motore di orchestrazione funzionerà! DevOps di Azure non è obbligatorio. La factory di immagini viene eseguita usando gli script di Azure PowerShell, pertanto può essere eseguita manualmente, usando l'Utilità di pianificazione di Windows, altri sistemi CI/CD e così via.

## <a name="create-a-lab-for-the-image-factory"></a>Creare un lab per la factory di immaginiCreate a lab for the image factory
The first step in setting up the image factory is to create a lab in Azure DevTest Labs. Questo lab è il lab della factory di immagini in cui vengono create le macchine virtuali e vengono salvate immagini personalizzate. Questo laboratorio è considerato come parte del processo complessivo di image factory. Dopo aver creato un lab, assicurarsi di salvare il nome poiché sarà necessario in un secondo momento.

## <a name="scripts-and-templates"></a>Script e modelli
Il passo successivo nell'adozione della fabbrica di immagini per il tuo team è capire cosa è disponibile. Gli script e i modelli della factory di immagini sono disponibili pubblicamente in [DevTest Labs GitHub Repo](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Ecco un contorno dei pezzi:

- Fabbrica di immagini. È la cartella principale.
    - Configurazione. Gli input per la fabbrica di immagini
        - GoldenImages. Questa cartella contiene file JSON che rappresentano le definizioni di immagini personalizzate.
        - Labs.json. File in cui i team si iscrivono per ricevere immagini personalizzate specifiche.
- Script. Motore per la fabbrica di immagini.

Negli articoli di questa sezione vengono fornite ulteriori informazioni su questi script e modelli.

## <a name="create-an-azure-devops-team-project"></a>Creare un progetto team DevOps di AzureCreate an Azure DevOps team project
DevOps di Azure consentono di archiviare il codice sorgente e di eseguire Azure PowerShell in un'unica posizione. È possibile pianificare esecuzioni ricorrenti per mantenere aggiornate le immagini. Esistono buone funzionalità per registrare i risultati per diagnosticare eventuali problemi.  L'uso di Azure DevOps non è tuttavia un requisito, è possibile usare qualsiasi cablaggio/motore che può connettersi ad Azure ed eseguire Azure PowerShell.Using Azure DevOps isn't a requirement however, you can use any harness/engine that can connect to Azure and can run Azure PowerShell.

Se invece si dispone di un account DevOps esistente o di un progetto che si vuole usare, ignorare questo passaggio.

Per iniziare, creare un account gratuito in DevOps di Azure.To get started, create a free account in Azure DevOps. Visitare https://www.visualstudio.com/ e selezionare **Guida gratuita direttamente** in Azure **DevOps** (in precedenza VSTS). Dovrai scegliere un nome account univoco e assicurarti di scegliere di gestire il codice usando Git. Una volta creato, salvare l'URL nel progetto team. Di seguito è `https://<accountname>.visualstudio.com/MyFirstProject`riportato un URL di esempio: .

## <a name="check-in-the-image-factory-to-git"></a>Archiviare la factory di immagini in Git
Tutti i modelli di PowerShell, i modelli e la configurazione per la factory di immagini si trovano nel [repository pubblico DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Il modo più rapido per ottenere il codice nel nuovo progetto team consiste nell'importare un repository. Viene eseguito il pull dell'intero repository DevTest Labs (in modo da ottenere documenti aggiuntivi ed esempi).

1. Visitare il progetto DevOps di Azure creato nel passaggio precedente (url è simile **a\//\<https: nomeaccount>.visualstudio.com/MyFirstProject**).
2. Selezionare **Importa un repository**.
3. Immettere **l'URL** del clone per `https://github.com/Azure/azure-devtestlab`DevTest Labs Repo: .
4. Selezionare **Importa**.

    ![Importa repository Git](./media/set-up-devops-lab/import-git-repo.png)

Se si decide di archiviare esattamente ciò che è necessario (i file di factory delle immagini), seguire i passaggi [qui](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) per clonare il repository Git ed eseguire il push solo dei file che si trovano nella directory **scripts/ImageFactory.**

## <a name="create-a-build-and-connect-to-azure"></a>Creare una compilazione e connettersi ad AzureCreate a build and connect to Azure
A questo punto, i file di origine vengono archiviati in un repository Git in DevOps di Azure.At this point, you have the source files stored in a Git repo in Azure DevOps. A questo punto, è necessario configurare una pipeline per eseguire Azure PowerShell.Now, you need to set up a pipeline to run the Azure PowerShell. Ci sono un sacco di opzioni per fare questi passaggi. In this article, you use build definition for simplicity, but it works with DevOps Build, DevOps Release (single or multiple environments), other execution engines like Windows Task Scheduler or any other harness that can execute Azure PowerShell.

> [!NOTE]
> Un punto importante da tenere presente che alcuni dei file di PowerShell richiedono molto tempo per l'esecuzione quando sono presenti molte (10) immagini personalizzate da creare. Gli agenti di compilazione/rilascio DevOps ospitati gratuiti hanno un timeout di 30 min, pertanto non è possibile usare l'agente ospitato gratuito una volta che si inizia a creare molte immagini. Questa verifica del timeout si applica a qualsiasi cablaggio che si decide di usare, è consigliabile verificare in anticipo che è possibile estendere i timeout tipici per gli script di Azure PowerShell a esecuzione prolungata. Nel caso di DevOps di Azure, è possibile usare agenti ospitati a pagamento o usare il proprio agente di compilazione.

1. Per iniziare, seleziona **Imposta compilazione** nella home page del progetto DevOps:

    ![Pulsante Crea](./media/set-up-devops-lab/setup-build-button.png)
2. Specificare un **nome** per la compilazione, ad esempio Compilazione e recapito di immagini in DevTest Labs.
3. Selezionare una definizione di compilazione **vuota** e selezionare **Applica** per creare la compilazione.
4. In questa fase, è possibile scegliere **Ospitato** per l'agente di compilazione.
5. **Salvare** la definizione di compilazione.

    ![Definizione di compilazione](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>Configurare le variabili di compilazione
Per semplificare i parametri della riga di comando, incapsulare i valori chiave che indirizzano la factory di immagini a un set di variabili di compilazione. Seleziona la scheda **Variabili** e vedrai un elenco di diverse variabili predefinite. Ecco l'elenco delle variabili da immettere in Azure DevOps:Here's the list of variables to enter in to Azure DevOps:


| Nome variabile | valore | Note |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configurazione | Questo è il percorso completo nel repository della cartella **Configuration.** Se è stato importato l'intero repository precedente, il valore a sinistra è corretto. In caso contrario, eseguire l'aggiornamento in modo che punti al percorso di configurazione. |
| DevTestLabName (Nome DevTestLab) | MyImageFactory (informazioni in questo sito) | Nome del lab in Azure DevTest Labs usato come factory per la produzione di immagini. Se non ne hai uno, creane uno. Assicurarsi che il lab si trova nella stessa sottoscrizione a cui l'endpoint del servizio ha accesso. |
| ImageRetention | 1 | Il numero di immagini che si desidera salvare di ciascun tipo. Impostare il valore predefinito su 1.Set default value to 1. |
| PasswordMacchina | ******* | Password dell'account amministratore predefinito per le macchine virtuali. Si tratta di un account temporaneo, quindi assicurati che sia sicuro. Seleziona la piccola icona a destra per assicurarti che sia una stringa sicura. |
| NomeUtenteComputer | ImageFactoryUser (Utente ImageFactoryUser) | Nome utente dell'account amministratore predefinito per le macchine virtuali. Questo è un conto transitorio. |
| StandardTimeoutMinutes | 30 | Timeout da attendere per le normali operazioni di Azure.The timeout we should wait for regular Azure operations. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | ID della sottoscrizione in cui è presente il lab e a cui ha accesso l'endpoint del servizio. |
| VMSize | Standard_A3 | Dimensioni della macchina virtuale da utilizzare per il passaggio **Crea.** Le macchine virtuali create sono temporanee. La dimensione deve essere quella [abilitata per il lab.](devtest-lab-set-lab-policy.md) Verificare che la [quota dei core](../azure-resource-manager/management/azure-subscription-service-limits.md)di sottoscrizione sia sufficiente.

![Variabili di compilazione](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Connettersi ad Azure
Il passaggio successivo consiste nell'impostare l'entità servizio. Si tratta di un'identità in Azure Active Directory che consente all'agente di compilazione DevOps di operare in Azure per conto dell'utente. Per configurarlo, iniziare con l'aggiunta del primo passaggio di compilazione di Azure PowerShell.To set it up, start with adding you first Azure PowerShell Build Step.

1. Selezionare **Aggiungi attività**.
2. Cercare **Azure PowerShell**.
3. Dopo averlo trovato, selezionare **Aggiungi** per aggiungere l'attività alla compilazione. In questo caso, l'attività verrà visualizzata sul lato sinistro quando viene aggiunta.

![Configurare il passaggio di PowerShellSet up PowerShell step](./media/set-up-devops-lab/set-up-powershell-step.png)

Il modo più rapido per configurare un'entità servizio consiste nel consentire ad Azure DevOps di eseguire questa operazione per noi.

1. Selezionare **l'attività** appena aggiunta.
2. Per **Tipo di connessione di Azure**scegliere Azure Resource **Manager**.
3. Selezionare il collegamento **Gestisci** per configurare l'entità servizio.

Per ulteriori informazioni, consultate questo post di [blog.](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) Quando si seleziona il collegamento **Gestisci,** si arriva nel posto giusto in DevOps (seconda schermata nel post di blog) per configurare la connessione ad Azure.When you select the Manage link, you'll getand in the right place in DevOps (second screenshot in the blog post) to set up the connection to Azure. Assicurarsi di scegliere **l'endpoint del servizio Azure Resource Manager** quando si configura questa impostazione.

## <a name="complete-the-build-task"></a>Completare l'attività di compilazione
Se si seleziona l'attività di compilazione, nel riquadro destro verranno visualizzati tutti i dettagli che devono essere compilati.

1. Assegnare innanzitutto il nome all'attività di compilazione: **Crea macchine virtuali**.
2. Scegliere **l'entità servizio** creata scegliendo **Azure Resource Manager**
3. Scegliere **l'endpoint**del servizio .
4. Per **Percorso script**, selezionare **... (ellipsi)** a destra.
5. Passare allo script **MakeGoldenImageVMs.ps1.**
6. I parametri di script dovrebbero essere simili al seguente:Script Parameters should look like this:`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![Completare la definizione di compilazioneComplete the build definition](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>Accodare la compilazione
Verifichiamo che tutto sia impostato correttamente accodando una nuova compilazione. Mentre la compilazione è in esecuzione, passare al portale di [Azure](https://portal.azure.com) e selezionare Tutte **le macchine virtuali** nel lab della factory di immagini per verificare che tutto funzioni correttamente. Dovrebbero essere create tre macchine virtuali nel lab.

![Macchine virtuali in laboratorio](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>Passaggi successivi
Il primo passaggio nella configurazione della factory di immagini in base a Azure DevTest Labs è stato completato. Nell'articolo successivo della serie, le macchine virtuali vengono generalizzate e salvate in immagini personalizzate. Quindi, li hai distribuiti a tutti gli altri laboratori. Vedere l'articolo successivo della serie: [Salvare immagini personalizzate e distribuirle in più lab.](image-factory-save-distribute-custom-images.md)
