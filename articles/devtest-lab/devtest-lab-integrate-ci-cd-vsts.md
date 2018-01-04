---
title: Integrare Azure DevTest Labs in VSTS continua integrazione e il recapito pipeline | Documenti Microsoft
description: Informazioni su come integrare Azure DevTest Labs in una pipeline di recapito e integrazione continua di VSTS
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: tarcher
ms.openlocfilehash: 3cd6939e890f59e7c5b188edd8620da5c9fcc935
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2017
---
# <a name="integrate-azure-devtest-labs-into-your-vsts-continuous-integration-and-delivery-pipeline"></a>Integrare Azure DevTest Labs in una pipeline di recapito e integrazione continua di VSTS
È possibile utilizzare il **estensione Azure DevTest Labs attività** installato in Visual Studio Team Services (VSTS) per integrare la compilazione di CI/CD con facilità e rilasciare il pipeline con Azure DevTest Labs. L'installazione dell'estensione tre attività per creare una macchina virtuale, creare un'immagine personalizzata da una macchina virtuale ed eliminare una macchina virtuale. Questo processo semplifica, ad esempio, rapidamente la distribuzione di un' "immagine finale" per attività di test specifico, quindi eliminarlo al termine il test.

In questo articolo viene illustrato come creare e distribuire una macchina virtuale, creare un'immagine personalizzata, quindi eliminare la macchina virtuale, tutti come una singola pipeline completa. In genere, tuttavia, si useranno le attività singolarmente nel proprio personalizzato della pipeline di compilazione test distribuito.

## <a name="before-you-begin"></a>Prima di iniziare
Prima di poter integrare la pipeline CI/CD con Azure DevTest Labs, è necessario installare innanzitutto l'estensione da Visual Studio Marketplace.
1. Passare a [Azure DevTest Labs attività](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)
1. Scegliere Installa
1. Completare la procedura guidata

## <a name="create-a-resource-manager-template"></a>Creare un modello di gestione risorse
In questa sezione viene descritto come creare il modello di gestione risorse di Azure che consente di creare una macchina virtuale di Azure su richiesta.

1. Seguire i passaggi in [utilizza un modello di gestione risorse](devtest-lab-use-resource-manager-template.md) per creare un modello di gestione delle risorse nella sottoscrizione.
1. Aggiungere il [elemento WinRM](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) come parte della creazione della macchina virtuale (prima di generare il modello di gestione delle risorse).

   È necessario per l'accesso di gestione remota Windows utilizzare distribuire le attività, ad esempio **copia dei File di Azure** e **PowerShell nei computer di destinazione**.

   > [!NOTE]
   > Quando si usa WinRM con un indirizzo IP condiviso, è necessario aggiungere una regola NAT per eseguire il mapping di una porta esterna per la porta WinRM. Questo non è necessario se la macchina virtuale viene creata con un indirizzo IP pubblico.
   >
   >

1. Salvare il modello come file nel computer in uso. Nome del file **CreateVMTemplate.json**.
1. Controllare il modello nel sistema di controllo del codice sorgente.
1. Aprire un editor di testo e copiarvi lo script seguente.

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

1. Verificare lo script nel sistema di controllo del codice sorgente. Nome simile a **GetLabVMParams.ps1**.

   Quando viene eseguito l'agente come parte della definizione di versione, questo script raccoglie valori che è necessario distribuire l'app per la macchina virtuale, se si utilizza attività, ad esempio **copia dei File di Azure** o **PowerShell nei computer di destinazione**. È in genere utilizzare queste attività per distribuire le App in una macchina virtuale di Azure e richiedono valori, ad esempio il nome del gruppo di risorse macchina virtuale, l'indirizzo IP e il nome di dominio completo (FDQN).

## <a name="create-the-release-definition-in-release-management"></a>Creare la definizione di versione in Release Management
Eseguire questi passaggi per creare la definizione di versione.

1. Aprire il **versioni** scheda della finestra il **compilazione & versione** hub e scegliere il "**+**" icona.
1. Nel **Crea definizione di versione** finestra di dialogo Seleziona il **vuoto** modello, scegliere **Avanti**.
1. Nella pagina successiva, selezionare **scegliere in un secondo momento** e quindi scegliere **crea** per creare una nuova definizione di versione con l'ambiente di un valore predefinito e non ci sono artefatti collegati.
1. Nella nuova definizione di versione, scegliere i puntini di sospensione (...) accanto al nome di ambiente per aprire il menu di scelta rapida e selezionare **configurare le variabili**. 
1. Nel **- Configura ambiente** finestra di dialogo, immettere i valori seguenti per le variabili utilizzate nelle attività di definizione di versione:
   - **vmName**: immettere il nome assegnato alla macchina virtuale quando è stato creato il modello di gestione risorse nel portale di Azure.
   - **nome utente**: immettere il nome utente è assegnato alla macchina virtuale quando è stato creato il modello di gestione risorse nel portale di Azure.
   - **password**: immettere la password assegnata alla macchina virtuale è stato creato il modello di gestione risorse nel portale di Azure. Utilizzare "lucchetto" per nascondere e proteggere la password.

   <a name="create-a-vm"></a>Creare una macchina virtuale
   ---

   In questa distribuzione la prima fase consiste nel creare la macchina virtuale da usare come "immagine finale" per le distribuzioni successive. Creare la macchina virtuale all'interno dell'istanza di Azure DevTest Lab utilizzando l'attività sviluppato appositamente per questo scopo. Nella definizione di versione, selezionare **+ Aggiungi attività** e aggiungere un **Azure DevTest Labs creare VM** attività nella scheda Distribuisci. Configurare l'app come segue:

   Vedere [Azure DevTest Labs attività](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) per creare la macchina virtuale da utilizzare per le distribuzioni successive.
   - **Sottoscrizione di Azure RM**: selezionare una connessione dall'elenco in **connessioni del servizio di Azure disponibili** o creare una connessione di autorizzazioni più limitata alla sottoscrizione di Azure. Per ulteriori informazioni, vedere [endpoint del servizio Gestione risorse di Azure](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).
   - **Nome del lab**: selezionare il nome dell'istanza creata in precedenza.
   - **Nome del modello**: immettere il percorso completo e il nome del file modello è stato salvato nel repository del codice sorgente. È possibile utilizzare le proprietà predefinite di Release Management per semplificare il percorso, ad esempio:
      ```
      $(System.DefaultWorkingDirectory)/Contoso/ARMTemplates/CreateVMTemplate.json
      ```
   - **Parametri di modello**: immettere i parametri per le variabili definite nel modello. Utilizzare i nomi delle variabili definite nell'ambiente, ad esempio:
      ```
      -newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)
      ```
   - **Output di variabili - ID macchina virtuale Lab**: È necessario l'ID della macchina virtuale appena creata per i passaggi successivi. Il nome predefinito della variabile di ambiente che viene popolata automaticamente con questo ID è impostato **variabili di Output** sezione. È possibile modificare la variabile, se necessario, ma è necessario utilizzare il nome corretto nelle attività successive. L'ID di macchina virtuale Lab è nel formato:
      ```
      /subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.DevTestLab/labs/{labName}/virtualMachines/{vmName}
      ```
1. Eseguire lo script creata in precedenza per raccogliere i dettagli della macchina virtuale DevTest Labs. Nella definizione di versione, selezionare **+ Aggiungi attività** e aggiungere un **Azure PowerShell** attività dal **Distribuisci** scheda. Configurare l'attività come indicato di seguito:

   Vedere [Distribuisci: Azure PowerShell](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/AzurePowerShell) ed eseguire lo script per raccogliere i dettagli della macchina virtuale DevTest Labs.

   - **Tipo di connessione Azure**: Azure Resource Manager.
   - **Sottoscrizione di Azure RM**: selezionare una connessione dall'elenco in **connessioni del servizio di Azure disponibili** o creare una connessione di autorizzazioni più limitata alla sottoscrizione di Azure. Per ulteriori informazioni, vedere [endpoint del servizio Gestione risorse di Azure](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).
   - **Tipo di script**: File di Script.
   - **Percorso di script**: immettere il percorso completo e il nome dello script è stato salvato nel repository del codice sorgente. È possibile utilizzare le proprietà predefinite di Release Management per semplificare il percorso, ad esempio:
      ```
      $(System.DefaultWorkingDirectory/Contoso/Scripts/GetLabVMParams.ps1
      ```
   - **Argomenti script**: immettere come argomento di script, il nome della variabile di ambiente che è stata popolata automaticamente con l'ID del laboratorio di macchina virtuale dall'attività precedente, ad esempio: 
      ```
      -labVmId '$(labVMId)'
      ```
   Lo script raccoglie i valori richiesti e li archivia in variabili di ambiente all'interno della definizione di versione in modo da poter facilmente fare riferimento a tali passaggi successivi di attività.

1. È ora possibile distribuire l'app per la nuova macchina virtuale di DevTest Labs. Le attività in genere utilizzato per distribuire sono **copia dei File di Azure** e **PowerShell nei computer di destinazione**.
   - Le informazioni sulla macchina virtuale è necessario per i parametri di queste attività vengono archiviate in tre variabili di configurazione denominate **labVmRgName**, **labVMIpAddress**, e **labVMFqdn**all'interno della definizione di versione.
   - Se si desidera sperimentare con la creazione di una macchina virtuale di DevTest Labs e un'immagine personalizzata, senza distribuire un'app a esso, è possibile ignorare questo passaggio.

   <a name="create-an-image"></a>Creare un'immagine
   ---

   La fase successiva consiste nel creare un'immagine della macchina virtuale appena distribuita nell'istanza di Azure DevTest Labs. È quindi possibile utilizzare questa immagine per creare copie della macchina virtuale su richiesta, ogni volta che si desidera eseguire un'attività di sviluppo o alcuni test. Nella definizione di versione, selezionare **+ Aggiungi attività** e aggiungere un **Azure DevTest Labs crea personalizzato immagine** attività dal **Distribuisci** scheda. Configurare l'app come segue:

   Vedere [Azure DevTest Labs attività](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) per creare l'immagine.
   - **Sottoscrizione di Azure RM**: selezionare una connessione dall'elenco in **connessioni del servizio di Azure disponibili** o creare una connessione di autorizzazioni più limitata alla sottoscrizione di Azure. Per ulteriori informazioni, vedere [endpoint del servizio Gestione risorse di Azure](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).
   - **Nome del lab**: selezionare il nome dell'istanza creata in precedenza.
   - **Nome dell'immagine personalizzata**: immettere un nome per l'immagine personalizzata.
   - **Descrizione**: immettere facoltativamente una descrizione che rendono più semplice selezionare l'immagine corretta in un secondo momento.
   - **Origine Lab VM - ID macchina virtuale di origine Lab**: se è stato modificato il nome predefinito della variabile di ambiente che è stata popolata automaticamente con l'ID del laboratorio di macchina virtuale da un'attività precedente, modificarlo qui. Il valore predefinito è *$(labVMId)*.
   - **Output di variabili - ID immagine personalizzata**: quando si desidera gestire o eliminarlo, è necessario l'ID dell'immagine appena creato. Il nome predefinito della variabile di ambiente che viene popolata automaticamente con questo ID è impostato **variabili di Output** sezione. Se necessario, è possibile modificare la variabile.
   
   <a name="delete-the-vm"></a>Eliminare la macchina virtuale
   ---
   
   La fase finale, in questo esempio consiste nell'eliminare la macchina virtuale è distribuito nell'istanza di Azure DevTest Labs. In genere, si elimina la macchina virtuale dopo aver eseguito le attività di sviluppo o eseguire i test che necessari nella macchina virtuale distribuita. Nella definizione di versione, selezionare **+ Aggiungi attività** e aggiungere un **Azure DevTest Labs eliminare VM** attività dal **Distribuisci** scheda. Configurare l'app come segue:

   Vedere [Azure DevTest Labs attività](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) per eliminare la macchina virtuale.
   - **Sottoscrizione di Azure RM**: selezionare una connessione dall'elenco in **connessioni del servizio di Azure disponibili** o creare una connessione di autorizzazioni più limitata alla sottoscrizione di Azure. Per ulteriori informazioni, vedere [endpoint del servizio Gestione risorse di Azure](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).
   - **ID di macchina virtuale lab**: se è stato modificato il nome predefinito della variabile di ambiente che è stata popolata automaticamente con l'ID del laboratorio di macchina virtuale da un'attività precedente, modificarlo qui. Il valore predefinito è *$(labVMId)*.
1. Immettere un nome per la definizione di versione e salvarlo.
1. Creare una nuova versione, selezionare la build più recente e distribuirlo nell'ambiente solo nella definizione.

In ogni fase, aggiornare la visualizzazione dell'istanza del DevTest Labs nel portale di Azure per visualizzare la macchina virtuale e l'immagine viene creata e la macchina virtuale in corso l'eliminazione nuovamente.

È ora possibile utilizzare l'immagine personalizzata per creare macchine virtuali quando necessario.


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [Creare un ambiente con più VM con i modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md).
* Più modelli di gestione risorse di avvio rapido per l'automazione di DevTest Labs da esplorare il [pubblica repository GitHub di DevTest Labs](https://github.com/Azure/azure-quickstart-templates).
* Se necessario, vedere il [la risoluzione dei problemi di VSTS](https://docs.microsoft.com/vsts/build-release/actions/troubleshooting) pagina.
