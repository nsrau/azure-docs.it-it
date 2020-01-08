---
title: Eseguire una factory di immagini da Azure DevOps in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come creare una factory di immagini personalizzata in Azure DevTest Labs.
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
ms.openlocfilehash: 101ed792f091a5074b42e3d06eed27d606d3d2a7
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638953"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Eseguire una factory di immagini da Azure DevOps
Questo articolo illustra tutti i preparativi necessari per eseguire la factory di immagini da Azure DevOps (in precedenza Visual Studio Team Services).

> [!NOTE]
> Il motore di orchestrazione funzionerà. Azure DevOps non è obbligatorio. La factory di immagini viene eseguita con Azure PowerShell script, quindi può essere eseguita manualmente, usando Windows Utilità di pianificazione, altri sistemi CI/CD e così via.

## <a name="create-a-lab-for-the-image-factory"></a>Creare un Lab per la factory di immagini
Il primo passaggio per configurare la factory di immagini consiste nel creare un Lab in Azure DevTest Labs. Questo Lab è il Lab della factory di immagini in cui vengono create le macchine virtuali e salvate le immagini personalizzate. Questo Lab viene considerato come parte del processo di Factory di immagini generale. Dopo aver creato un Lab, assicurarsi di salvare il nome perché sarà necessario in un secondo momento.

## <a name="scripts-and-templates"></a>Script e modelli
Il passaggio successivo per adottare la factory di immagini per il team consiste nel comprendere le funzionalità disponibili. Gli script e i modelli della factory di immagini sono disponibili pubblicamente nel [repository GitHub di DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Ecco una descrizione delle parti:

- Factory di immagini. Si tratta della cartella radice.
    - Configurazione. Input per la factory di immagini
        - GoldenImages. Questa cartella contiene i file JSON che rappresentano le definizioni di immagini personalizzate.
        - Labs. JSON. File in cui i team si iscrivono per ricevere immagini personalizzate specifiche.
- Script. Motore per la factory di immagini.

Negli articoli di questa sezione vengono forniti altri dettagli su questi script e modelli.

## <a name="create-an-azure-devops-team-project"></a>Creare un progetto team di Azure DevOps
Azure DevOps consente di archiviare il codice sorgente, eseguire il Azure PowerShell in un'unica posizione. È possibile pianificare esecuzioni periodiche per rendere aggiornate le immagini. Sono disponibili ottime funzionalità per la registrazione dei risultati per diagnosticare eventuali problemi.  L'uso di Azure DevOps non è tuttavia un requisito, ma è possibile usare qualsiasi harness/motore in grado di connettersi ad Azure e può eseguire Azure PowerShell.

Se si ha un account o un progetto DevOps esistente che si vuole usare, ignorare questo passaggio.

Per iniziare, creare un account gratuito in Azure DevOps. Visitare https://www.visualstudio.com/ e selezionare inizia subito **gratuitamente** in **Azure DevOps** (in precedenza VSTS). È necessario scegliere un nome di account univoco e assicurarsi di scegliere di gestire il codice usando git. Al termine della creazione, salvare l'URL nel progetto team. Ecco un URL di esempio: `https://<accountname>.visualstudio.com/MyFirstProject`.

## <a name="check-in-the-image-factory-to-git"></a>Archiviare la factory di immagini in git
Tutti gli PowerShell, i modelli e la configurazione per la factory di immagini si trovano nel [repository DevTest Labs GitHub pubblico](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Il modo più rapido per ottenere il codice nel nuovo progetto team consiste nell'importare un repository. In questo modo viene effettuato il pull dell'intero repository di DevTest Labs (per ottenere altri documenti ed esempi).

1. Visitare il progetto DevOps di Azure creato nel passaggio precedente (l'URL è simile a **https:\//\<accountname >. VisualStudio. com/MyFirstProject**).
2. Selezionare **Importa un repository**.
3. Immettere l' **URL del clone** per il repository DevTest Labs: `https://github.com/Azure/azure-devtestlab`.
4. Selezionare **Importa**.

    ![Importa repository git](./media/set-up-devops-lab/import-git-repo.png)

Se si decide di archiviare solo esattamente gli elementi necessari (i file della factory di immagini), seguire i passaggi [qui](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) per clonare il repository git ed eseguire il push solo dei file presenti nella directory **Scripts/ImageFactory** .

## <a name="create-a-build-and-connect-to-azure"></a>Creare una compilazione e connettersi ad Azure
A questo punto, i file di origine sono archiviati in un repository git in Azure DevOps. A questo punto, è necessario configurare una pipeline per eseguire il Azure PowerShell. Sono disponibili numerose opzioni per eseguire questi passaggi. In questo articolo viene usata la definizione di compilazione per semplicità, ma funziona con DevOps Build, DevOps Release (uno o più ambienti), altri motori di esecuzione come Windows Utilità di pianificazione o qualsiasi altro harness che può eseguire Azure PowerShell.

> [!NOTE]
> Un punto importante da tenere presente che alcuni dei file di PowerShell impostano molto tempo per l'esecuzione quando sono presenti più immagini personalizzate da creare. Gli agenti di compilazione/versione DevOps ospitati gratuiti hanno un timeout di 30 minuti, pertanto non è possibile usare l'agente ospitato gratuito una volta avviata la compilazione di molte immagini. Questa richiesta di timeout si applica a qualsiasi harness che si decide di usare. è consigliabile verificare prima di tutto che sia possibile estendere i tipici timeout per gli script di Azure PowerShell a esecuzione prolungata. Nel caso di Azure DevOps, è possibile usare gli agenti ospitati a pagamento o usare un agente di compilazione personalizzato.

1. Per iniziare, selezionare **Configura compilazione** nella Home page del progetto DevOps:

    ![Pulsante di compilazione il programma di installazione](./media/set-up-devops-lab/setup-build-button.png)
2. Specificare un **nome** per la compilazione (ad esempio, compilare e distribuire immagini in DevTest Labs).
3. Selezionare una definizione di compilazione **vuota** e selezionare **applica** per creare la compilazione.
4. In questa fase è possibile scegliere **Hosted** per l'agente di compilazione.
5. **Salvare** la definizione di compilazione.

    ![Definizione di compilazione](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>Configurare le variabili di compilazione
Per semplificare i parametri della riga di comando, incapsulare i valori di chiave che guidano la factory di immagini a un set di variabili di compilazione. Selezionare la scheda **variabili** per visualizzare un elenco di diverse variabili predefinite. Ecco l'elenco di variabili da immettere in Azure DevOps:


| Nome variabile | Valore | Note |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configuration | Si tratta del percorso completo nel repository per la cartella di **configurazione** . Se è stato importato l'intero repository precedente, il valore a sinistra è corretto. In caso contrario, aggiornare per puntare al percorso di configurazione. |
| DevTestLabName | MyImageFactory | Il nome del Lab in Azure DevTest Labs usato come factory per produrre le immagini. Se non si dispone di un, crearne uno. Verificare che il Lab si trovi nella stessa sottoscrizione a cui ha accesso l'endpoint del servizio. |
| ImageRetention | 1 | Il numero di immagini che si desidera salvare di ogni tipo. Impostare il valore predefinito su 1. |
| MachinePassword | ******* | Password dell'account amministratore predefinito per le macchine virtuali. Si tratta di un account temporaneo, quindi assicurarsi che sia protetto. Selezionare la piccola icona a blocchi a destra per assicurarsi che sia una stringa sicura. |
| MachineUserName | ImageFactoryUser | Nome utente dell'account amministratore predefinito per le macchine virtuali. Si tratta di un account temporaneo. |
| StandardTimeoutMinutes | 30 | Il timeout è necessario attendere le normali operazioni di Azure. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | ID della sottoscrizione in cui esiste il Lab e a cui l'endpoint del servizio ha accesso. |
| VMSize | Standard_A3 | Dimensioni della macchina virtuale da utilizzare per il passaggio di **creazione** . Le macchine virtuali create sono temporanee. La dimensione deve essere quella [abilitata per il Lab](devtest-lab-set-lab-policy.md). Verificare che la quota di [core della sottoscrizione](../azure-resource-manager/management/azure-subscription-service-limits.md)sia sufficiente.

![Variabili di compilazione](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Connettersi ad Azure
Il passaggio successivo consiste nel configurare un'entità servizio. Si tratta di un'identità in Azure Active Directory che consente all'agente di compilazione DevOps di funzionare in Azure per conto dell'utente. Per configurarlo, iniziare con l'aggiunta del primo Azure PowerShell passaggio di compilazione.

1. Selezionare **Aggiungi attività**.
2. Cercare **Azure PowerShell**.
3. Una volta individuato, selezionare **Aggiungi** per aggiungere l'attività alla compilazione. Quando si esegue questa operazione, l'attività verrà visualizzata sul lato sinistro come aggiunto.

![Configurare il passaggio di PowerShell](./media/set-up-devops-lab/set-up-powershell-step.png)

Il modo più rapido per configurare un'entità servizio consiste nel consentire a Azure DevOps di eseguire questa operazione per noi.

1. Selezionare l' **attività** appena aggiunta.
2. Per **tipo di connessione di Azure**scegliere **Azure Resource Manager**.
3. Selezionare il collegamento **Gestisci** per configurare l'entità servizio.

Per altre informazioni, vedere questo [post di blog](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/). Quando si seleziona il collegamento **Gestisci** , viene visualizzata la posizione corretta in DevOps (seconda schermata del post di Blog) per configurare la connessione ad Azure. Assicurarsi di scegliere **Azure Resource Manager endpoint servizio** durante la configurazione.

## <a name="complete-the-build-task"></a>Completare l'attività di compilazione
Se si seleziona l'attività di compilazione, verranno visualizzati tutti i dettagli nel riquadro di destra che devono essere compilati.

1. In primo luogo, denominare l'attività di compilazione: **Create Virtual Machines**.
2. Scegliere l' **entità servizio** creata scegliendo **Azure Resource Manager**
3. Scegliere l' **endpoint del servizio**.
4. Per **percorso script**selezionare **... (puntini** di sospensione) a destra.
5. Passare allo script **MakeGoldenImageVMs. ps1** .
6. I parametri dello script dovrebbero essere simili al seguente: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![Completa la definizione di compilazione](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>Accoda la compilazione
Verificare che tutti gli elementi siano impostati correttamente eseguendo l'accodamento di una nuova compilazione. Durante l'esecuzione della compilazione, passare alla [portale di Azure](https://portal.azure.com) e selezionare tutte le **macchine virtuali** nel Lab della factory di immagini per verificare che tutto funzioni correttamente. Verranno visualizzate tre macchine virtuali nel Lab.

![Macchine virtuali nel Lab](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>Passaggi successivi
Il primo passaggio per la configurazione della factory di immagini basata su Azure DevTest Labs è completo. Nel prossimo articolo della serie si ottengono le VM generalizzate e salvate in immagini personalizzate. Quindi, sono stati distribuiti a tutti gli altri Lab. Vedere l'articolo successivo della serie: [salvare immagini personalizzate e distribuirle in più laboratori](image-factory-save-distribute-custom-images.md).
