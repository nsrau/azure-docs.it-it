---
title: Eseguire una factory di immagini da Azure DevOps in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come creare una factory di immagini personalizzate in Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: abb85d568e26e4b6f85b960a2560aae570daf201
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61320620"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Eseguire una factory di immagini da Azure DevOps
Questo articolo illustra tutte le operazioni preliminari necessari per eseguire la factory di immagini dalla metodologia DevOps di Azure (noto in precedenza come Visual Studio Team Services).

> [!NOTE]
> Funzionerà qualsiasi motore di orchestrazione. DevOps di Azure non è obbligatorio. La factory di immagini viene eseguita usando script di Azure PowerShell, pertanto potrebbe essere eseguito manualmente, usando l'utilità di pianificazione di Windows, altri sistemi di integrazione continua/recapito Continuo e così via.

## <a name="create-a-lab-for-the-image-factory"></a>Creare un lab per la factory di immagini
Il primo passaggio nella configurazione la factory dell'immagine consiste nel creare un lab in Azure DevTest Labs. Questo lab è il lab di factory di immagini in cui si creano le macchine virtuali e salvare le immagini personalizzate. In questo laboratorio viene considerato come parte del processo complessivo image factory. Dopo aver creato un lab, assicurarsi di salvare il nome perché sarà necessaria in un secondo momento.

## <a name="scripts-and-templates"></a>Script e modelli
Il passaggio successivo di adottare la factory di immagini per il team consiste nel comprendere ciò che è disponibile. Gli script di factory di immagini e modelli sono disponibili pubblicamente nel [repository GitHub di DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Ecco una descrizione delle parti:

- Factory di immagini. Si tratta della cartella radice.
    - Configurazione. Gli input per la factory di immagini
        - GoldenImages. Questa cartella contiene i file JSON che rappresentano le definizioni di immagini personalizzate.
        - Labs.json. File in cui i team di registrarsi per ricevere specifiche immagini personalizzate.
- Inserisce nello script. Il motore per la factory di immagini.

Gli articoli in questa sezione forniscono altri dettagli su questi script e modelli.

## <a name="create-an-azure-devops-team-project"></a>Creare un progetto team DevOps di Azure
DevOps di Azure consentono di archiviare il codice sorgente, eseguire Azure PowerShell in un'unica posizione. È possibile pianificare esecuzioni periodiche per mantenersi aggiornati sulle immagini. Sono disponibili funzionalità valida per la registrazione dei risultati per diagnosticare eventuali problemi.  DevOps di Azure non è un requisito, tuttavia, è possibile utilizzare qualsiasi harness/motore che può connettersi ad Azure e può eseguire Azure PowerShell.

Se hai un account di DevOps esistente o un progetto da utilizzare in alternativa, è possibile ignorare questo passaggio.

Per iniziare, creare un account gratuito di Azure DevOps. Visita https://www.visualstudio.com/ e selezionare **Provalo gratis** a destra sotto **Azure DevOps** (noto in precedenza come VSTS). È necessario scegliere un nome di account univoci e assicurarsi di scegliere di gestire il codice con Git. Dopo la creazione, salvare l'URL per il progetto team. Di seguito è un URL di esempio: `https://<accountname>.visualstudio.com/MyFirstProject`.

## <a name="check-in-the-image-factory-to-git"></a>Archivia la factory di immagini a Git
PowerShell, modelli e configurazione per la factory di immagini si trovano nel [pubblico DevTest Labs GitHub repository](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Il modo più rapido per ottenere il codice nel nuovo progetto team consiste nell'importare un repository. Si esegue il pull nel repository di DevTest Labs intero (quindi si otterranno extra documentazione ed esempi).

1. Visita il progetto DevOps di Azure creato nel passaggio precedente (URL è simile **https:\//\<NomeAccount >.visualstudio.com/MyFirstProject**).
2. Selezionare **importare un Repository**.
3. Immettere il **URL di clonazione** per il repository di DevTest Labs: `https://github.com/Azure/azure-devtestlab`.
4. Selezionare **Importa**.

    ![Importare un repository Git](./media/set-up-devops-lab/import-git-repo.png)

Se si decide di archiviare solo esattamente cosa è necessario (i file di factory di immagini), seguire la procedura [qui](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) clonare il repository Git ed eseguire il push solo i file che si trova nel **script/ImageFactory** directory.

## <a name="create-a-build-and-connect-to-azure"></a>Creare una build e connettersi ad Azure
A questo punto, è necessario i file di origine archiviati in un repository Git in Azure DevOps. A questo punto, è necessario configurare una pipeline per eseguire Azure PowerShell. Sono disponibili moltissime opzioni per eseguire questi passaggi. In questo articolo usare una definizione di compilazione per motivi di semplicità, ma funziona con DevOps Build, rilascio di DevOps (uno o più ambienti), altri motori di esecuzione, ad esempio utilità di pianificazione di Windows o qualsiasi altro harness che è possibile eseguire Azure PowerShell.

> [!NOTE]
> Un punto importante da tenere presente che alcuni dei file di PowerShell richiedere molto tempo per l'esecuzione quando sono presenti molte (10 +) le immagini personalizzate per creare. Gratuito agenti ospitati di compilazione/versione di DevOps hanno un timeout di 30 minuti, in modo che non è possibile usare l'agente ospitato gratuita una volta avviata la creazione di un numero di immagini. Questo problema di timeout si applica a qualsiasi harness si decide di utilizzare, è opportuno verificare anticipatamente che è possibile estendere i timeout tipici per gli script di Azure PowerShell esecuzione prolungata. Nel caso di DevOps di Azure, è possibile usare agenti ospitati a pagamento o usare il proprio agente di compilazione.

1. Per iniziare, selezionare **impostare la compilazione** nella home page del progetto DevOps:

    ![Pulsante di compilazione il programma di installazione](./media/set-up-devops-lab/setup-build-button.png)
2. Specificare una **nome** per la compilazione (ad esempio: Creare e distribuire le immagini in DevTest Labs).
3. Selezionare un **vuote** definizione di compilazione e selezionare **applica** per creare la compilazione.
4. In questa fase, è possibile scegliere **ospitato** per l'agente di compilazione.
5. **Salvare** la definizione di compilazione.

    ![Definizione di compilazione](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>Configurare le variabili di compilazione
Per semplificare i parametri della riga di comando, racchiudere i valori di chiave che determinano la factory di immagini a un set di variabili di compilazione. Selezionare il **variabili** scheda e si verrà visualizzato un elenco di variabili predefiniti diversi. Ecco l'elenco di variabili da immettere a DevOps di Azure:


| Nome variabile | Value | Note |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configuration | Questo è il percorso completo del repository per il **configurazione** cartella. Se è stato importato l'intero repository precedente, il valore a sinistra sia corretto. In caso contrario, vengono aggiornati per puntare al percorso di configurazione. |
| DevTestLabName | MyImageFactory | Il nome del lab in Azure DevTest Labs utilizzato perché la factory per produrre immagini. Se non si dispone di uno, crearne uno. Assicurarsi che il Lab sia nella stessa sottoscrizione di cui l'endpoint del servizio può accedere. |
| ImageRetention | 1 | Il numero di immagini di che voler salvare di ogni tipo. Impostare il valore predefinito su 1. |
| MachinePassword | ******* | La password dell'account amministratore predefinito per le macchine virtuali. Questo è un account temporaneo, quindi assicurarsi che sia protetta. Selezionare la piccola icona di blocco a destra per assicurarsi che sia una stringa sicura. |
| MachineUserName | ImageFactoryUser | Il nome utente account amministratore predefinito per le macchine virtuali. Questo è un account temporaneo. |
| StandardTimeoutMinutes | 30 | Il timeout che si attendano per le normali operazioni di Azure. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | L'ID della sottoscrizione in cui si trova il lab e l'endpoint del servizio con accesso a. |
| VMSize | Standard_A3 | Le dimensioni della macchina virtuale da usare per la **Create** passaggio. Le macchine virtuali create sono temporanee. La dimensione deve essere quello Ecco [abilitata per l'ambiente lab](devtest-lab-set-lab-policy.md). Verificare che vi sia sufficiente [quota di core sottoscrizione](../azure-subscription-service-limits.md).

![Le variabili di compilazione](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Connect to Azure
Il passaggio successivo consiste nell'impostare il servizio dell'entità. Si tratta di un'identità in Azure Active Directory che consente all'agente di compilazione di DevOps operare per conto dell'utente in Azure. Per configurarlo, iniziare con Azure PowerShell compilare innanzitutto l'aggiunta è.

1. Selezionare **aggiungere attività**.
2. Cercare **Azure PowerShell**.
3. Dopo averlo trovato, selezionare **Add** per aggiungere l'attività per la compilazione. Quando si esegue questa operazione, verrà visualizzato l'attività sul lato sinistro man mano che aggiunti.

![Configurare il passaggio di PowerShell](./media/set-up-devops-lab/set-up-powershell-step.png)

Il modo più rapido per configurare un servizio principale è consentire Azure DevOps farlo per noi.

1. Selezionare il **attività** si è appena aggiunta.
2. Per la **tipo di connessione di Azure**, scegliere **Azure Resource Manager**.
3. Selezionare il **Gestisci** collegamento per configurare l'entità servizio.

Per altre informazioni, vedere questo [post di blog](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/). Quando si seleziona il **Gestisci** collegamento, si apre nel posto giusto in DevOps (seconda schermata nel post di blog) per configurare la connessione ad Azure. Assicurarsi di scegliere **Endpoint di servizio di Azure Resource Manager** quando questa configurazione.

## <a name="complete-the-build-task"></a>Completare l'attività di compilazione
Se si seleziona l'attività di compilazione, si noterà tutti i dettagli nel riquadro di destra che deve essere compilato.

1. Assegnare un nome in primo luogo, l'attività di compilazione: **Creare macchine virtuali**.
2. Scegliere il **entità servizio** creato scegliendo **Azure Resource Manager**
3. Scegliere il **endpoint del servizio**.
4. Per la **percorso dello Script**, selezionare **... (puntini di sospensione)**  sulla destra.
5. Passare a **MakeGoldenImageVMs.ps1** script.
6. I parametri dello script dovrebbero essere simile al seguente: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![Completare la definizione di compilazione](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>Accodare la compilazione
È possibile verificare di avere tutte le impostazioni in modo corretto mediante l'accodamento di una nuova compilazione. Durante la compilazione è in esecuzione, passare al [portale di Azure](https://portal.azure.com) e selezionare **tutte le macchine virtuali** nell'ambiente lab factory immagine per verificare che tutto funzioni correttamente. Si dovrebbe ottenere create tre macchine virtuali nel lab.

![Macchine virtuali nel lab](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>Passaggi successivi
Il primo passaggio nella configurazione la factory di immagini basata su Azure DevTest Labs è stato completato. Nel prossimo articolo della serie, accedere a queste VM generalizzata e salvati in immagini personalizzate. Quindi, devono essere distribuite a tutti gli altri lab. Vedere l'articolo successivo della serie: [Salvare le immagini personalizzate e la distribuzione a più lab](image-factory-save-distribute-custom-images.md).
