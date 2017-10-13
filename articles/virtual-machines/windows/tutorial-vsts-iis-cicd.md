---
title: Creare una pipeline CI/CD in Azure con Team Services | Microsoft Docs
description: Informazioni su come creare una pipeline di Visual Studio Team Services per l'integrazione e il recapito continui che distribuisce un'app Web su IIS in una macchina virtuale Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/12/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: a587f58fad2ec74c7633823c4d34f900e7c01f7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-continuous-integration-pipeline-with-visual-studio-team-services-and-iis"></a>Creare una pipeline di integrazione continua con Visual Studio Team Services e IIS
Per automatizzare le fasi di compilazione, test e distribuzione dello sviluppo di un'applicazione, è possibile usare una pipeline per l'integrazione e la distribuzione continui (CI/CD). In questa esercitazione viene creata una pipeline CI/CD tramite Visual Studio Team Services e una macchina virtuale Windows in Azure che esegue IIS. Si apprenderà come:

> [!div class="checklist"]
> * pubblicare un'applicazione Web ASP.NET in un progetto di Team Services
> * creare una definizione di compilazione che viene attivata dal commit del codice
> * installare e configurare IIS su una macchina virtuale in Azure
> * aggiungere l'istanza IIS a un gruppo di distribuzione in Team Services
> * creare una versione di definizione per pubblicare nuovi pacchetti di distribuzione in IIS
> * Testare la pipeline CI/CD

Questa esercitazione richiede il modulo Azure PowerShell 3.6 o versioni successive. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installare il modulo di Azure PowerShell).


## <a name="create-project-in-team-services"></a>Creare un progetto in Team Services
Visual Studio Team Services consente per uno sviluppo e una collaborazione facilitata senza mantenere una soluzione di gestione del codice in locale. Team Services offre i test sul codice cloud, compilazione e informazioni approfondite sull'applicazione. È possibile scegliere l'archivio di controllo della versione e l'IDE che meglio si adatta allo sviluppo del codice. Per questa esercitazione, è possibile usare un account gratuito per creare un'app Web ASP.NET di base e una pipeline CI/CD. Se non si dispone già di un account Team Services, [crearne uno](http://go.microsoft.com/fwlink/?LinkId=307137).

Per gestire il processo di commit del codice, compilare e rilasciare le definizioni, creare un progetto in Team Services come indicato di seguito:

1. Aprire il dashboard di Team Services in un browser Web e scegliere **Nuovo progetto**.
2. Immettere *myWebApp* per il **Nome progetto**. Lasciare tutti gli altri valori predefiniti per usare la versione di controllo *Git* e il processo dell'elemento di lavoro *Agile*.
3. Scegliere l'opzione **Share with** (Condividi con) *Membri del team*, quindi selezionare **Crea**.
5. Dopo aver creato il progetto, scegliere l'opzione per **esegui l'inizializzazione con un file LEGGIMI o gitignore**, quindi **Inizializza**.
6. All'interno del nuovo progetto scegliere **Dashboard** nella parte superiore, quindi selezionare **Apri in Visual Studio**.


## <a name="create-aspnet-web-application"></a>Creare un'applicazione Web ASP.NET
Nel passaggio precedente, è stato creato un progetto in Team Services. Il passaggio finale apre il nuovo progetto in Visual Studio. I commit del codice possono essere gestiti nella finestra **Team Explorer**. Creare una copia locale del nuovo progetto, quindi creare un'applicazione Web ASP.NET da un modello, come indicato di seguito:

1. Selezionare **Clona** per creare un repository GIT locale del progetto Team Services.
    
    ![Clonare l'archivio dal progetto Team Services](media/tutorial-vsts-iis-cicd/clone_repo.png)

2. In **Soluzioni** selezionare **Nuovo**.

    ![Creare una soluzione per l'applicazione Web](media/tutorial-vsts-iis-cicd/new_solution.png)

3. Selezionare i modelli **Web**, quindi scegliere il modello **Applicazione Web ASP.NET**.
    1. Immettere un nome per l'applicazione, ad esempio *myWebApp*, e deselezionare la casella per **Crea directory per soluzione**.
    2. Se l'opzione è disponibile, deselezionare la casella **Aggiungi Application Insights al progetto**. Application Insight richiede che l'utente autorizzi l'applicazione Web con Application Insights di Azure. Per semplicità in questa esercitazione ignorare questo processo.
    3. Selezionare **OK**.
4. Scegliere **MVC** dall'elenco di modelli.
    1. Selezionare **Modifica autenticazione**, scegliere **No Authentication** (Nessuna autenticazione), quindi selezionare **OK**.
    2. Selezionare **OK** per creare la soluzione.
5. Nella finestra **Team Explorer** finestra scegliere **Modifiche**.

    ![Eseguire il commit delle modifiche locali nel repository GIT di Team Services](media/tutorial-vsts-iis-cicd/commit_changes.png)

6. Nella casella di testo del commit immettere un messaggio, ad esempio *Commit iniziale*. Scegliere **Esegui commit di tutto e sincronizza** nel menu a discesa.


## <a name="create-build-definition"></a>Creare una definizione di compilazione
In Team Services usare una definizione di compilazione per spiegare come compilare l'applicazione. In questa esercitazione viene creata una definizione di base che accetta il codice sorgente, compila la soluzione, quindi crea un pacchetto di distribuzione Web che è possibile usare per eseguire l'app Web in un server IIS.

1. Nel progetto Team Services, nella parte superiore scegliere **Compilazione e versione**, quindi selezionare **Compilazioni**.
3. Selezionare **+ Nuova definizione**.
4. Scegliere il modello **ASP.NET (ANTEPRIMA)** e selezionare **Applica**.
5. Lasciare l'impostazione predefinita per tutti i valori dell'attività. In **Get sources** (Ottieni origini) assicurarsi che l'archivio *myWebApp* e il ramo *master* siano selezionati.

    ![Creare la definizione di compilazione nel progetto Team Services](media/tutorial-vsts-iis-cicd/create_build_definition.png)

6. Nella scheda **Attivazioni** spostare il dispositivo di scorrimento per **Enable this trigger** (Abilita questo trigger) su *Abilitato*.
7. Salvare la definizione di compilazione e accodare una nuova compilazione selezionando **Salva e accoda**, quindi **Salva e accoda** di nuovo. Lasciare le impostazioni predefinite e selezionare **Accoda**.

A questo punto si può osservare la compilazione che viene pianificata su un agente ospitato e che quindi inizia a compilare. L'output è simile all'esempio seguente:

![Completamento della compilazione del progetto Team Services](media/tutorial-vsts-iis-cicd/successful_build.png)


## <a name="create-virtual-machine"></a>Crea macchina virtuale
Per creare una piattaforma su cui eseguire l'app Web ASP.NET, è necessaria una macchina virtuale Windows che esegua IIS. Team Services usa un agente per interagire con l'istanza IIS mentre si esegue il commit del codice e le compilazioni vengono attivate.

Creare rapidamente una macchina virtuale Windows Server 2016 tramite [questo esempio di script](../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json). L'esecuzione dello script e la creazione della macchina virtuale richiedono alcuni minuti. Dopo aver creato la macchina virtuale, aprire la porta 80 per il traffico Web con [Add-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.resources/new-azurermresourcegroup) come indicato di seguito:

```powershell
Get-AzureRmNetworkSecurityGroup `
  -ResourceGroupName $resourceGroup `
  -Name "myNetworkSecurityGroup" | `
Add-AzureRmNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleWeb" `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority "1001" `
  -SourceAddressPrefix "*" `
  -SourcePortRange "*" `
  -DestinationAddressPrefix "*" `
  -DestinationPortRange "80" `
  -Access "Allow" | `
Set-AzureRmNetworkSecurityGroup
```

Per collegare la macchina virtuale, ottenere l'indirizzo IP pubblico con [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) come indicato di seguito:

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup | Select IpAddress
```

Creare una sessione di desktop remoto per la macchina virtuale:

```cmd
mstsc /v:<publicIpAddress>
```

Nella macchina virtuale aprire un prompt dei comandi **Administrator PowerShell** (Amministratore PowerShell). Installare IIS e le funzionalità .NET necessarie come segue:

```powershell
Install-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features
```


## <a name="create-deployment-group"></a>Creare un gruppo di distribuzione
Per escludere il pacchetto di distribuzione Web al server IIS, definire un gruppo di distribuzione in Team Services. Questo gruppo consente di specificare i server a cui sono indirizzate le nuove compilazioni quando si esegue il commit del codice a Team Services e vengono completate le compilazioni.

1. In Team Services scegliere **Build & Release** (Compilazione e versione) e quindi selezionare **Gruppi di distribuzione**.
2. Scegliere **Aggiungi gruppo di distribuzione**.
3. Immettere un nome per il gruppo, ad esempio *myIIS*, quindi selezionare **Crea**.
4. Nella sezione **Registra computer** assicurarsi che *Windows* sia selezionato, quindi selezionare la casella **Use a personal access token in the script for authentication** (Usare un token di accesso personale nello script per l'autenticazione).
5. Selezionare **Copy script to clipboard** (Copia lo script negli appunti).


### <a name="add-iis-vm-to-the-deployment-group"></a>Aggiungere la macchina virtuale IIS al gruppo di distribuzione
Una volta creato il gruppo di distribuzione, aggiungere Tutte le istanze IIS al gruppo. Team Services genera uno script che scarica e configura un agente nella macchina virtuale che riceve i nuovi pacchetti di distribuzione Web e li applica a IIS.

1. Tornando alla sessione **Administrator PowerShell** (Amministratore PowerShell) nella macchina virtuale incollare ed eseguire lo script copiato da Team Services.
2. Quando viene richiesto di configurare i tag per l'agente, scegliere *Y* e immettere *web*.
3. Quando richiesto per l'account utente, premere *Invio* per accettare le impostazioni predefinite.
4. Attendere che lo script termini con un messaggio *Service vstsagent.account.computername started successfully* (Servizio vstsagent.account.computername avviato correttamente).
5. Nella pagina **Gruppi di distribuzione** del menu **Build & Release** (Compilazione e versione) aprire il gruppo di distribuzione *myIIS*. Nella scheda **Computer** verificare che la macchina virtuale sia presente.

    ![Macchina virtuale aggiunta correttamente al gruppo di distribuzione di Team Services](media/tutorial-vsts-iis-cicd/deployment_group.png)


## <a name="create-release-definition"></a>Creare una definizione della versione
Per pubblicare le compilazioni, creare una definizione di versione in Team Services. Questa definizione viene attivata automaticamente dalla corretta compilazione dell'applicazione. Scegliere il gruppo di distribuzione in cui effettuare il push del pacchetto di distribuzione Web e definire le impostazioni IIS appropriate.

1. Scegliere **Build & Release** (Compilazione e versione), quindi selezionare **Compilazioni**. Scegliere la definizione di compilazione creata nel passaggio precedente.
2. In **Completati di recente** scegliere la compilazione più recente, quindi selezionare **Versione**.
3. Scegliere **Sì** per creare una definizione di versione.
4. Scegliere il **Vuoto** modello, quindi selezionare **Avanti**.
5. Verificare che la definizione di compilazione del progetto e dell'origine vengano popolati con il progetto.
6. Selezionare la casella di controllo **Distribuzione continua**, quindi selezionare **Crea**.
7. Selezionare la casella di riepilogo a discesa accanto a **+ Add tasks** (+ Aggiungi attività) e scegliere *Aggiungi una fase per gruppo di distribuzione*.
    
    ![Aggiunta di attività alla definizione di versione in Team Services](media/tutorial-vsts-iis-cicd/add_release_task.png)

8. Scegliere **Aggiungi** accanto a **IIS Web App Deploy(Preview)** (Distribuzione dell'app Web IIS - Anteprima), quindi selezionare **Chiudi**.
9. Selezionare l'attività principale **Esegui su gruppo di distribuzione**.
    1. Per **Gruppo di distribuzione**, selezionare il gruppo di distribuzione creato in precedenza, ad esempio *myIIS*.
    2. Nella casella **Tag computer** selezionare **Aggiungi** e scegliere il tag *web*.
    
    ![Attività del gruppo di distribuzione della definizione di versione per IIS](media/tutorial-vsts-iis-cicd/release_definition_iis.png)
 
11. Selezionare l'attività **Deploy: IIS Web App Deploy** (Distribuzione: distribuzione dell'app Web IIS) per configurare le impostazioni dell'istanza IIS come descritto di seguito:
    1. Per **Nome del sito Web**, immettere *Sito Web predefinito*.
    2. Lasciare tutte le altre impostazioni predefinite.
12. Scegliere **Slava**, quindi selezionare **OK** due volte.


## <a name="create-a-release-and-publish"></a>Creare e pubblicare una versione
È ora possibile effettuare il push del pacchetto di distribuzione Web come una nuova versione. Questo passaggio comunica con l'agente in ogni istanza che fa parte del gruppo di distribuzione, effettua il push del pacchetto di distribuzione Web, quindi configura IIS per eseguire l'applicazione Web aggiornata.

1. Nella definizione di versione selezionare **+ Release** (+ Versione), quindi scegliere **Crea versione**.
2. Verificare che la compilazione più recente sia selezionata nell'elenco a discesa, insieme a **Distribuzione automatizzata: dopo la creazione della versione**. Selezionare **Create**.
3. Viene visualizzata una piccola intestazione nella parte superiore della definizione di versione, ad esempio *È stata creata la versione "Versione-1"*. Selezionare il collegamento della versione.
4. Aprire la scheda **Log** per osservare lo stato della versione.
    
    ![Push del pacchetto di distribuzione Web e versione di Team Services corretti](media/tutorial-vsts-iis-cicd/successful_release.png)

5. Dopo aver completato la versione, aprire un Web browser e immettere l'indirizzo IIP pubblico della macchina virtuale. L'applicazione Web ASP.NET è in esecuzione.

    ![App web ASP.NET in esecuzione sulla macchina virtuale IIS](media/tutorial-vsts-iis-cicd/running_web_app.png)


## <a name="test-the-whole-cicd-pipeline"></a>Testare tutta la pipeline CI/CD
Mentre l'applicazione Web è in esecuzione in IIS, testare l'intera pipeline CI/CD. Dopo aver apportato una modifica in Visual Studio e aver eseguito il commit del codice, viene avviata una compilazione che quindi attiva una versione del pacchetto di distribuzione Web aggiornato in IIS:

1. Aprire la finestra **Esplora soluzioni** in Visual Studio.
2. Individuare e aprire *myWebApp | Viste | Home | Index.cshtml*
3. Modificare la riga 6 affinché si legga:

    `<h1>ASP.NET with VSTS and CI/CD!</h1>`

4. Salvare il file.
5. Aprire la finestra **Team Explorer**, selezionare il progetto *myWebApp*, quindi scegliere **Modifiche**.
6. Immettere un messaggio di commit, ad esempio *Test della pipeline CI/CD*, quindi scegliere **Esegui commit di tutto e sincronizza** dal menu a discesa.
7. Nell'area di lavoro di Team Services viene attivata una nuova compilazione dal commit di codice. 
    - Scegliere **Build & Release** (Compilazione e versione), quindi selezionare **Compilazioni**. 
    - Scegliere la definizione di compilazione, quindi selezionare la compilazione **In coda e in esecuzione** per osservare l'avanzamento della compilazione.
9. Quando la compilazione ha esito positivo, viene attivata una nuova versione.
    - Scegliere **Build & Release** (Compilazione e versione), quindi **Versioni** per vedere il pacchetto di distribuzione Web su cui è stato effettuato il push alla macchina virtuale IIS. 
    - Selezionare l'icona **Aggiorna** per aggiornare lo stato. Quando la colonna *Ambienti* mostra un segno di spunta verde, significa che la versione è stata distribuita correttamente in IIS.
11. Per visualizzare le modifiche applicate, aggiornare il sito Web IIS in un browser.

    ![App web ASP.NET in esecuzione sulla macchina virtuale IIS dalla pipeline CI/CD](media/tutorial-vsts-iis-cicd/running_web_app_cicd.png)


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione, è stata creata un'applicazione Web ASP.NET in Team Services e sono state configurate le definizioni di versione e compilazione per distribuire i nuovi pacchetti di distribuzione Web a IIS su ogni commit del codice. Si è appreso come:

> [!div class="checklist"]
> * pubblicare un'applicazione Web ASP.NET in un progetto di Team Services
> * creare una definizione di compilazione che viene attivata dal commit del codice
> * installare e configurare IIS su una macchina virtuale in Azure
> * aggiungere l'istanza IIS a un gruppo di distribuzione in Team Services
> * creare una versione di definizione per pubblicare nuovi pacchetti di distribuzione in IIS
> * Testare la pipeline CI/CD

Passare all'esercitazione successiva per apprendere come proteggere un server Web con i certificati SSL.

> [!div class="nextstepaction"]
> [Secure web server with SSL (Proteggere il server Web con SSL)](tutorial-secure-web-server.md)