---
title: Integrare Azure DevTest Labs nella pipeline di integrazione e distribuzione continue di Visual Studio Team Services | Microsoft Docs
description: Informazioni su come integrare Azure DevTest Labs nella pipeline di integrazione e distribuzione continue di Visual Studio Team Services
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: v-craic
ms.openlocfilehash: 6c6bd4fbd89ec87cbbdbfb9ed42f86a484acf7ad
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="integrate-azure-devtest-labs-into-your-vsts-continuous-integration-and-delivery-pipeline"></a>Integrare Azure DevTest Labs nella pipeline di integrazione e distribuzione continue di Visual Studio Team Services
È possibile usare l'estensione *Azure DevTest Labs Tasks*, installata in Visual Studio Team Services (VSTS), per integrare facilmente la pipeline di compilazione e rilascio di integrazione continua/distribuzione continua con Azure DevTest Labs. L'estensione installa tre attività: 
* Creare una macchina virtuale
* Creare un'immagine personalizzata da una VM
* Eliminare una macchina virtuale 

Il processo, ad esempio, semplifica la distribuzione rapida di una "immagine finale" per un'attività di test specifica e quindi la sua eliminazione al termine del test.

Questo articolo mostra come creare e distribuire una macchina virtuale, creare un'immagine personalizzata e quindi eliminare la macchina virtuale, il tutto come pipeline completa. Normalmente ogni attività viene eseguita singolarmente nella pipeline di compilazione-test-rilascio personalizzata.

## <a name="before-you-begin"></a>Prima di iniziare
Prima di poter integrare la pipeline di integrazione continua/distribuzione continua con Azure DevTest Labs, è necessario installare l'estensione da Visual Studio Marketplace.
1. Passare a [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
1. Selezionare **Installa**.
1. Completare la procedura guidata.

## <a name="create-a-resource-manager-template"></a>Creare un modello di Resource Manager
Questa sezione descrive come creare il modello di Azure Resource Manager usato per creare una macchina virtuale di Azure on demand.

1. Per creare un modello di Resource Manager nella sottoscrizione, completare la procedura in [Usare un modello di Resource Manager](devtest-lab-use-resource-manager-template.md).
1. Prima di generare il modello di Resource Manager, aggiungere l'[elemento WinRM](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) come parte della creazione della macchina virtuale.

   L'accesso a WinRM è necessario per usare attività di distribuzione come *Copia dei file di Azure* e *PowerShell in computer di destinazione*.

   > [!NOTE]
   > Quando si usa WinRM con un indirizzo IP condiviso, è necessario aggiungere una regola NAT per il mapping di una porta esterna alla porta WinRM. Questo passaggio non è necessario se la macchina virtuale viene creata con un indirizzo IP pubblico.
   >
   >

1. Salvare il modello come file nel computer. Denominare il file **CreateVMTemplate.json**.
1. Archiviare il modello nel sistema di controllo del codice sorgente.
1. Aprire un editor di testo e incollarvi lo script seguente.

   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzureRmResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzureRmResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzureRmResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzureRmPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzureRmPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. Archiviare lo script nel sistema di controllo del codice sorgente. Assegnare allo script un nome simile a **GetLabVMParams.ps1**.

   Quando si esegue questo script nell'agente come parte della definizione di versione e si usano passaggi di attività come *Copia dei file di Azure* o *PowerShell in computer di destinazione*, lo script raccoglie i valori necessari per distribuire l'app nella macchina virtuale. Queste attività vengono normalmente usate per distribuire app in una macchina virtuale di Azure. Le attività richiedono valori come il nome del gruppo di risorse della macchina virtuale, l'indirizzo IP e il nome di dominio completo (FDQN).

## <a name="create-a-release-definition-in-release-management"></a>Creare una definizione di versione in Release Management
Per creare una definizione di versione, eseguire le operazioni seguenti:

1. Nella scheda **Versioni** dell'hub **Compilazione e versione** selezionare il segno di addizione (+).
2. Nella finestra **Crea definizione di versione** selezionare il modello **Vuoto** e quindi fare clic su **Avanti**.
3. Selezionare **Scegli dopo** e quindi **Crea** per creare una nuova definizione di versione con un ambiente predefinito e nessun elemento collegato.
4. Per aprire il menu di scelta rapida, nella nuova definizione di versione selezionare i puntini di sospensione (...) accanto al nome dell'ambiente e quindi fare clic su **Configura variabili**. 
5. Nella finestra **Configura ambiente** immettere i valori seguenti per le variabili usate nelle attività della definizione di versione:

   a. Per **vmName** immettere il nome assegnato alla macchina virtuale durante la creazione del modello di Resource Manager nel portale di Azure.

   b. Per **userName** immettere il nome utente assegnato alla macchina virtuale durante la creazione del modello di Resource Manager nel portale di Azure.

   c. Per **password** immettere la password assegnata alla macchina virtuale durante la creazione del modello di Resource Manager nel portale di Azure. Usare l'icona di lucchetto per nascondere e proteggere la password.

### <a name="create-a-vm"></a>Creare una macchina virtuale

La fase successiva della distribuzione consiste nel creare la macchina virtuale da usare come "immagine finale" per le distribuzioni successive. È necessario creare la macchina virtuale all'interno dell'istanza di Azure DevTest Labs usando l'attività sviluppata appositamente a questo scopo. 

1. Nella definizione di versione selezionare **Aggiungi attività**.
2. Nella scheda **Distribuisci** aggiungere un'attività *Crea macchina virtuale* di Azure DevTest Labs. Configurare le attività in questo modo:

   > [!NOTE]
   > Per creare la macchina virtuale da usare per le distribuzioni successive, vedere [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Per **Sottoscrizione di Gestione risorse di Azure** selezionare una connessione nell'elenco **Connessioni ai servizi di Azure disponibili** oppure creare una connessione con autorizzazioni con maggiori restrizioni alla sottoscrizione di Azure. Per altre informazioni, vedere [Azure Resource Manager service endpoint](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm) (Endpoint di servizio di Azure Resource Manager).

   b. Per **Nome del lab** selezionare il nome dell'istanza creata in precedenza.

   c. Per **Nome modello** immettere il percorso completo e il nome del file modello salvato nel repository del codice sorgente. È possibile usare le proprietà predefinite di Release Management per semplificare il percorso, ad esempio:

   ```
   $(System.DefaultWorkingDirectory)/Contoso/ARMTemplates/CreateVMTemplate.json
   ```

   d. Per **Parametri del modello** immettere i parametri per le variabili definite nel modello. Usare i nomi delle variabili definite nell'ambiente, ad esempio:

   ```
   -newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)
   ```

   e. Per **Variabili di output - Lab VM ID** (ID macchina virtuale lab), è necessario l'ID della macchina virtuale appena creata per i passaggi successivi. È necessario impostare il nome predefinito della variabile di ambiente automaticamente popolata con questo ID nella sezione **Variabili di output**. Se necessario, è possibile modificare la variabile, ma ricordare di usare il nome corretto nelle attività successive. L'ID macchina virtuale lab è scritto nel formato seguente:

   ```
   /subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.DevTestLab/labs/{labName}/virtualMachines/{vmName}
   ```

3. Eseguire lo script creato in precedenza per raccogliere i dettagli della macchina virtuale DevTest Labs. 
4. Nella definizione di versione selezionare **Aggiungi attività** e quindi aggiungere un'attività **Azure PowerShell** nella scheda *Distribuisci*. Configurare le attività in questo modo:

   > [!NOTE]
   > Per raccogliere i dettagli della macchina virtuale DevTest Labs, vedere [Azure PowerShell](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/AzurePowerShell) ed eseguire lo script.

   a. Per **Tipo di connessione ad Azure** selezionare **Azure Resource Manager**.

   b. Per **Sottoscrizione di Gestione risorse di Azure** selezionare una connessione nell'elenco in **Connessioni ai servizi di Azure disponibili** oppure creare una connessione con autorizzazioni con maggiori restrizioni alla sottoscrizione di Azure. Per altre informazioni, vedere [Azure Resource Manager service endpoint](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm) (Endpoint di servizio di Azure Resource Manager).

   c. Per **Tipo di script** selezionare **File di script**.
 
   d. Per **Percorso dello script** immettere il percorso completo e il nome dello script salvato nel repository del codice sorgente. È possibile usare le proprietà predefinite di Release Management per semplificare il percorso, ad esempio:
      ```
      $(System.DefaultWorkingDirectory/Contoso/Scripts/GetLabVMParams.ps1
      ```
   e. Per **Argomenti script** immettere il nome della variabile di ambiente automaticamente popolata con l'ID della macchina virtuale lab nell'attività precedente, ad esempio: 
      ```
      -labVmId '$(labVMId)'
      ```
    Lo script raccoglie i valori necessari e li archivia in variabili di ambiente all'interno della definizione di versione, per permettere di farvi riferimento facilmente nei passaggi successivi.

5. Distribuire l'app nella nuova macchina virtuale DevTest Labs. Le attività usate normalmente per distribuire l'app sono *Copia dei file di Azure* e *PowerShell in computer di destinazione*.
   Le informazioni sulla macchina virtuale necessaria per i parametri di queste attività sono archiviate in tre variabili di configurazione denominate **labVmRgName**, **labVMIpAddress** e **labVMFqdn** all'interno della definizione di versione. Se si vuole solo provare a creare una macchina virtuale DevTest Labs e un'immagine personalizzata, senza distribuirvi un'app, è possibile ignorare questo passaggio.

### <a name="create-an-image"></a>Creare un'immagine

La fase successiva consiste nel creare un'immagine della nuova macchina virtuale distribuita nell'istanza di Azure DevTest Labs. È quindi possibile usare l'immagine per creare copie della macchina virtuale on demand ogni volta che si vuole eseguire un'attività di sviluppo o svolgere alcuni test. 

1. Nella definizione di versione selezionare **Aggiungi attività**.
2. Nella scheda **Distribuisci** aggiungere un'attività **Crea immagine personalizzata** di Azure DevTest Labs. Configurare l'app come segue:

   > [!NOTE]
   > Per creare l'immagine, vedere [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Per **Sottoscrizione di Gestione risorse di Azure** selezionare una connessione nell'elenco **Connessioni ai servizi di Azure disponibili** oppure creare una connessione con autorizzazioni con maggiori restrizioni alla sottoscrizione di Azure. Per altre informazioni, vedere [Azure Resource Manager service endpoint](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm) (Endpoint di servizio di Azure Resource Manager).

   b. Per **Nome del lab** selezionare il nome dell'istanza creata in precedenza.

   c. Per **Nome immagine** immettere un nome per l'immagine personalizzata.

   d. Facoltativo: per **Descrizione** immettere una descrizione per semplificare la selezione dell'immagine corretta nei passaggi successivi.

   e. Per **Source Lab VM - Source Lab VM ID** (Macchina virtuale lab di origine - ID macchina virtuale lab di origine), se è stato modificato il nome predefinito della variabile di ambiente automaticamente popolata con l'ID della macchina virtuale lab in un'attività precedente, modificarlo qui. Il valore predefinito è **$(labVMId)**.

   f. Per **Variabili di output - ID immagine personalizzata** è necessario l'ID della nuova immagine creata se si vuole gestirla o eliminarla. Il nome predefinito della variabile di ambiente automaticamente popolata con questo ID viene impostato nella sezione **Variabili di output**. Se necessario, è possibile modificare la variabile.

### <a name="delete-the-vm"></a>Eliminare la macchina virtuale

La fase finale consiste nell'eliminare la macchina virtuale distribuita nell'istanza di Azure DevTest Labs. La macchina virtuale viene normalmente eliminata dopo l'esecuzione delle attività di sviluppo o dei test necessari nella macchina virtuale distribuita. 

1. Nella definizione di versione selezionare **Aggiungi attività** e quindi aggiungere un'attività **Delete VM** (Elimina macchina virtuale) di Azure DevTest Labs nella scheda *Distribuisci*. Configurare l'app come segue:

      > [!NOTE]
      > Per eliminare la macchina virtuale, vedere [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Per **Sottoscrizione di Gestione risorse di Azure** selezionare una connessione nell'elenco **Connessioni ai servizi di Azure disponibili** oppure creare una connessione con autorizzazioni con maggiori restrizioni alla sottoscrizione di Azure. Per altre informazioni, vedere [Azure Resource Manager service endpoint](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm) (Endpoint di servizio di Azure Resource Manager).
 
   b. Per **Lab VM ID** (ID macchina virtuale lab), se è stato modificato il nome predefinito della variabile di ambiente automaticamente popolata con l'ID della macchina virtuale lab in un'attività precedente, è possibile modificarlo qui. Il valore predefinito è **$(labVMId)**.

2. Immettere un nome per la definizione di versione e quindi salvarla.
3. Creare una nuova versione, selezionare la build più recente e distribuirla nel singolo ambiente nella definizione.

In ogni fase aggiornare la visualizzazione dell'istanza di DevTest Labs nel portale di Azure per visualizzare la macchina virtuale e l'immagine create, insieme alla macchina virtuale eliminata di nuovo.

È ora possibile usare l'immagine personalizzata per creare macchine virtuali ogni volta che sono necessarie.


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [Creare un ambiente con più VM con i modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md).
* È possibile esplorare altri modelli di avvio rapido di Resource Manager per l'automazione di DevTest Labs nel [repository pubblico GitHub per DevTest Labs](https://github.com/Azure/azure-quickstart-templates).
* Se necessario, vedere la pagina [VSTS Troubleshooting](https://docs.microsoft.com/vsts/build-release/actions/troubleshooting) (Risoluzione dei problemi relativi a Visual Studio Team Services).
 
