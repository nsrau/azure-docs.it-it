---
title: Integrare Azure DevTest Labs nella pipeline di integrazione e distribuzione continue di Azure Pipelines | Microsoft Docs
description: Informazioni su come integrare Azure DevTest Labs nella pipeline di integrazione e distribuzione continue di Azure Pipelines
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2019
ms.author: spelluru
ms.openlocfilehash: 20ba297d22e26aa8c7e20db300173f12582d257e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "71224477"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>Integrare Azure DevTest Labs nella pipeline CI/CD Azure Pipelines

È possibile usare l'estensione *Azure DevTest Labs Tasks* per integrare le pipeline di compilazione e rilascio di Azure Pipelines Continuous Integration e recapito continuo (ci/CD) con Azure DevTest Labs. L'estensione installa varie attività, tra cui: 

- Creare una macchina virtuale (VM)
- Creare un'immagine personalizzata da una macchina virtuale
- Eliminare una macchina virtuale 

Queste attività semplificano, ad esempio, la distribuzione rapida di una macchina virtuale dell' *immagine dorata* per un'attività di test specifica e quindi l'eliminazione della macchina virtuale al termine del test.

Questo articolo illustra come usare Azure DevTest Labs attività per creare e distribuire una macchina virtuale, creare un'immagine personalizzata e quindi eliminare la macchina virtuale, come una sola pipeline di rilascio. Normalmente le attività vengono eseguite singolarmente nelle pipeline di compilazione, test e distribuzione personalizzate.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>Prerequisiti

- Registrare o accedere all'organizzazione [DevOps di Azure](https://dev.azure.com) e [creare un progetto](/vsts/organizations/projects/create-project) nell'organizzazione. 
  
- Installare l'estensione Azure DevTest Labs Tasks da Visual Studio Marketplace.
  
  1. Passare a [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
  1. Selezionare **Get it Free**.
  1. Selezionare l'organizzazione DevOps di Azure dall'elenco a discesa e selezionare **Installa**. 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>Creare il modello per compilare una macchina virtuale di Azure 

Questa sezione descrive come creare il modello di Azure Resource Manager usato per creare una macchina virtuale di Azure su richiesta.

1. Per creare un modello di Gestione risorse nella sottoscrizione, seguire la procedura descritta in [usare un modello di gestione risorse](devtest-lab-use-resource-manager-template.md).
   
1. Prima di generare il modello di Resource Manager, aggiungere l'[elemento WinRM](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) come parte della creazione della macchina virtuale. Le attività di distribuzione come la *copia di file di Azure* e *PowerShell nei computer di destinazione* richiedono l'accesso WinRM. Per l'artefatto WinRM è necessario specificare un nome host come parametro, che deve corrispondere al nome di dominio completo (FQDN) della macchina virtuale. 
   
   > [!NOTE]
   > Quando si usa WinRM con un indirizzo IP condiviso, è necessario aggiungere una regola NAT per il mapping di una porta esterna alla porta WinRM. Non è necessaria la regola NAT se si crea la macchina virtuale con un indirizzo IP pubblico.
   
   
1. Salvare il modello nel computer in uso come file denominato *CreateVMTemplate. JSON*.
   
1. Archiviare il modello nel sistema di controllo del codice sorgente.

## <a name="create-a-script-to-get-vm-properties"></a>Creare uno script per ottenere le proprietà della macchina virtuale

Quando si eseguono passaggi dell'attività come la *copia di file di Azure* o PowerShell nei computer di *destinazione* nella pipeline di rilascio, lo script seguente raccoglie i valori necessari per distribuire un'app in una macchina virtuale. Queste attività vengono in genere usate per distribuire l'app in una macchina virtuale di Azure. Le attività richiedono valori come il nome del gruppo di risorse della macchina virtuale, l'indirizzo IP e il nome di dominio completo.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per creare il file script:

1. Aprire un editor di testo e incollarvi lo script seguente.
   
   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. Salvare il file con un nome come *GetLabVMParams. ps1*e archiviarlo nel sistema di controllo del codice sorgente. 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>Creare una pipeline di versione in Azure Pipelines

Per creare una nuova pipeline di versione:

1. Dalla pagina del progetto Azure DevOps selezionare **pipeline** > **versioni** dal dispositivo di spostamento a sinistra.
1. Selezionare **Nuova pipeline**.
1. In **Seleziona un modello**scorrere verso il basso e selezionare **processo vuoto**e quindi selezionare **applica**.

### <a name="add-and-set-variables"></a>Aggiungere e impostare le variabili

Le attività della pipeline usano i valori assegnati alla macchina virtuale al momento della creazione del modello di Gestione risorse nel portale di Azure. 

Per aggiungere variabili per i valori: 

1. Nella pagina pipeline selezionare la scheda **variabili** .
   
1. Per ogni variabile, selezionare **Aggiungi** e immettere il nome e il valore:
   
   |Attività|Value|
   |---|---|
   |*vmName*|Nome della macchina virtuale assegnato nel modello di Gestione risorse|
   |*userName*|Nome utente per accedere alla macchina virtuale|
   |*password*|Password per il nome utente. Selezionare l'icona del lucchetto per nascondere e proteggere la password.

### <a name="create-a-devtest-labs-vm"></a>Creare una macchina virtuale DevTest Labs

Il passaggio successivo consiste nel creare la macchina virtuale dell'immagine dorata da usare per le distribuzioni future. Per creare la macchina virtuale all'interno dell'istanza di Azure DevTest Labs, usare l'attività *Azure DevTest Labs crea macchina virtuale* .

1. Nella scheda **pipeline** della pipeline di rilascio selezionare il testo con collegamento ipertestuale nella **fase 1** per **visualizzare le attività della fase**, quindi selezionare il **+** segno più accanto a **processo di Agent**. 
   
1. In **Aggiungi attività**selezionare **Azure DevTest Labs crea macchina virtuale**e selezionare **Aggiungi**. 
   
1. Selezionare **crea Azure DevTest Labs macchina virtuale** nel riquadro sinistro. 

1. Nel riquadro destro completare il modulo come segue:
   
   |Campo|Value|
   |---|---|
   |**Sottoscrizione di Azure RM**|Selezionare una connessione al servizio o una sottoscrizione dalle **connessioni al servizio di Azure disponibili** o dalle **sottoscrizioni di Azure disponibili** nell'elenco a discesa e selezionare **autorizza** se necessario.<br /><br />**Nota:** Per informazioni sulla creazione di una connessione con autorizzazioni più limitate alla sottoscrizione di Azure, vedere [Azure Resource Manager endpoint del servizio](/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).|
   |**Nome Lab**|Selezionare il nome di un lab esistente in cui verrà creata la macchina virtuale Lab.|
   |**Nome modello**|Immettere il percorso completo e il nome del file modello salvato nel repository del codice sorgente. È possibile utilizzare le proprietà predefinite per semplificare il percorso, ad esempio:<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |**Parametri di modello**|Immettere i parametri per le variabili definite in precedenza:<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   | > **ID macchina virtuale Lab** variabili di output|Immettere la variabile per l'ID macchina virtuale Lab creato. Se si usa il **labVMId**predefinito, è possibile fare riferimento alla variabile nelle attività successive come *$ (labVMId)* .<br /><br />È possibile creare un nome diverso da quello predefinito, ma ricordare di usare il nome corretto nelle attività successive. È possibile scrivere l'ID macchina virtuale Lab nel formato seguente:<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>Raccogliere i dettagli della macchina virtuale DevTest Labs

Eseguire lo script creato in precedenza per raccogliere i dettagli della macchina virtuale DevTest Labs. 

1. Nella scheda **pipeline** della pipeline di rilascio selezionare il testo con collegamento ipertestuale nella **fase 1** per **visualizzare le attività della fase**, quindi selezionare il **+** segno più accanto a **processo di Agent**. 
   
1. In **Aggiungi attività**selezionare **Azure PowerShell**e selezionare **Aggiungi**. 
   
1. Selezionare **Azure PowerShell script: FilePath** nel riquadro sinistro. 
   
1. Nel riquadro destro completare il modulo come segue:
   
   |Campo|Value|
   |---|---|
   |**Tipo di connessione di Azure**|Selezionare **Azure Resource Manager**.|
   |**Sottoscrizione di Azure**|Selezionare la sottoscrizione o la connessione al servizio.| 
   |**Tipo di script**|Selezionare il **percorso del file di script**.|
   |**Percorso script**|Immettere il percorso completo e il nome dello script di PowerShell salvato nel repository del codice sorgente. È possibile utilizzare le proprietà predefinite per semplificare il percorso, ad esempio:<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**Argomenti dello script**|Immettere il nome della variabile *labVmId* popolata dall'attività precedente, ad esempio:<br /><br />`-labVmId '$(labVMId)'`|

Lo script raccoglie i valori necessari e li archivia nelle variabili di ambiente all'interno della pipeline di rilascio, in modo da potervi fare riferimento nei passaggi successivi.

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>Creare un'immagine di macchina virtuale dalla macchina virtuale DevTest Labs

L'attività successiva consiste nel creare un'immagine della macchina virtuale appena distribuita nell'istanza di Azure DevTest Labs. È quindi possibile usare l'immagine per creare copie della macchina virtuale on demand ogni volta che si vuole eseguire un'attività di sviluppo o svolgere alcuni test. 

1. Nella scheda **pipeline** della pipeline di rilascio selezionare il testo con collegamento ipertestuale nella **fase 1** per **visualizzare le attività della fase**, quindi selezionare il **+** segno più accanto a **processo di Agent**. 
   
1. In **Aggiungi attività**selezionare **Azure DevTest Labs Crea immagine personalizzata**e selezionare **Aggiungi**. 
   
1. Configurare le attività in questo modo:
   
   |Campo|Value|
   |---|---|
   |**Sottoscrizione di Azure RM**|Selezionare la sottoscrizione o la connessione al servizio.|
   |**Nome Lab**|Consente di selezionare il nome di un lab esistente in cui verrà creata l'immagine.|
   |**Nome dell'immagine personalizzata**|Immettere un nome per l'immagine personalizzata.|
   |**Descrizione** di opzionale|Immettere una descrizione per facilitare la selezione dell'immagine corretta in un secondo momento.|
   | > **ID macchina virtuale Lab** di origine macchina virtuale del Lab di origine|Se è stato modificato il nome predefinito della variabile LabVMId, immetterlo qui. Il valore predefinito è **$(labVMId)** .|
   | > **ID immagine personalizzata** variabili di output|Se necessario, è possibile modificare il nome predefinito della variabile.|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>Distribuire l'app nella VM DevTest Labs (facoltativo)

È possibile aggiungere attività per distribuire l'app nella nuova macchina virtuale DevTest Labs. Le attività che in genere si usano per distribuire l'app sono *copia file di Azure* e *PowerShell nei computer di destinazione*.

Le informazioni sulla macchina virtuale necessarie per i parametri di queste attività sono archiviate in tre variabili di configurazione denominate **labVmRgName**, **labVMIpAddress**e **labVMFqdn** all'interno della pipeline di rilascio. Se si vuole solo provare a creare una macchina virtuale DevTest Labs e un'immagine personalizzata, senza distribuirvi un'app, è possibile ignorare questo passaggio.

### <a name="delete-the-vm"></a>Eliminare la macchina virtuale

L'attività finale consiste nell'eliminare la macchina virtuale distribuita nell'istanza di Azure DevTest Labs. La macchina virtuale viene normalmente eliminata dopo l'esecuzione delle attività di sviluppo o dei test necessari nella macchina virtuale distribuita. 

1. Nella scheda **pipeline** della pipeline di rilascio selezionare il testo con collegamento ipertestuale nella **fase 1** per **visualizzare le attività della fase**, quindi selezionare il **+** segno più accanto a **processo di Agent**. 
   
1. In **Aggiungi attività**selezionare **Azure DevTest Labs Elimina macchina virtuale**e selezionare **Aggiungi**. 
   
1. Configurare le attività in questo modo:
   
   - In **sottoscrizione di Azure RM**selezionare la connessione al servizio o la sottoscrizione. 
   - Per **ID macchina virtuale Lab**, se è stato modificato il nome predefinito della variabile LabVMId, immetterlo qui. Il valore predefinito è **$(labVMId)** .
   
### <a name="save-the-release-pipeline"></a>Salvare la pipeline di versione

Per salvare la nuova pipeline di rilascio:

1. Selezionare la **pipeline nome nuova versione** nella pagina della pipeline di rilascio e immettere un nuovo nome per la pipeline. 
   
1. Selezionare l'icona **Salva** in alto a destra. 

## <a name="create-and-run-a-release"></a>Creazione ed esecuzione di una versione

Per creare ed eseguire una versione usando la nuova pipeline:

1. Selezionare **Crea versione** in alto a destra nella pagina della pipeline di rilascio. 
   
1. In **elementi**selezionare la build più recente e quindi selezionare **Crea**.
   
1. Per ogni fase di rilascio, aggiornare la visualizzazione dell'istanza di DevTest Labs nel portale di Azure per visualizzare la creazione della macchina virtuale, la creazione di immagini e l'eliminazione della macchina virtuale.

È possibile usare l'immagine personalizzata per creare VM ogni volta che è necessario.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [Creare un ambiente con più VM con i modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md).
- È possibile esplorare altri modelli di avvio rapido di Resource Manager per l'automazione di DevTest Labs nel [repository pubblico GitHub per DevTest Labs](https://github.com/Azure/azure-quickstart-templates).
- Se necessario, vedere la pagina relativa alla [risoluzione dei problemi di Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/troubleshooting) .
 
