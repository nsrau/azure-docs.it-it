---
title: Integrare Azure DevTest Labs in VSTS continua integrazione e il recapito pipeline | Documenti Microsoft
description: Informazioni su come integrare Azure DevTest Labs in una pipeline di recapito e integrazione continua di VSTS
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
ms.openlocfilehash: db2ee6a25626f0a47bf86c5ee286fddc2441d3f8
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="integrate-azure-devtest-labs-into-your-vsts-continuous-integration-and-delivery-pipeline"></a>Integrare Azure DevTest Labs in una pipeline di recapito e integrazione continua di VSTS
È possibile utilizzare il *Azure DevTest Labs attività* estensione installata con facilità in Visual Studio Team Services (VSTS) per integrare la pipeline di compilazione e versione CI/CD con Azure DevTest Labs. L'installazione dell'estensione tre attività: 
* Creare una macchina virtuale
* Creare un'immagine personalizzata da una VM
* Eliminare una macchina virtuale 

Il processo semplifica, ad esempio, distribuire rapidamente un' "immagine finale" per un'attività di test specifici e quindi eliminarlo al termine di test.

In questo articolo viene illustrato come creare e distribuire una macchina virtuale, creare un'immagine personalizzata e quindi eliminare la macchina virtuale, tutti come una singola pipeline completa. In genere eseguire ogni attività singolarmente nel proprio personalizzato della pipeline di compilazione test distribuito.

## <a name="before-you-begin"></a>Prima di iniziare
Prima di poter integrare la pipeline CI/CD con Azure DevTest Labs, è necessario installare l'estensione da Visual Studio Marketplace.
1. Passare a [Azure DevTest Labs attività](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
1. Selezionare **Installa**.
1. Completare la procedura guidata.

## <a name="create-a-resource-manager-template"></a>Creare un modello di gestione risorse
In questa sezione viene descritto come creare il modello di gestione risorse di Azure che consente di creare una macchina virtuale di Azure su richiesta.

1. Per creare un modello di gestione delle risorse nella sottoscrizione, completare la procedura in [utilizza un modello di gestione risorse](devtest-lab-use-resource-manager-template.md).
1. Prima di generare il modello di gestione delle risorse, aggiungere il [elemento WinRM](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) come parte della creazione della macchina virtuale.

   Accesso di gestione remota Windows è necessario utilizzare le attività di distribuzione, ad esempio *copia dei File di Azure* e *PowerShell nei computer di destinazione*.

   > [!NOTE]
   > Quando si usa WinRM con un indirizzo IP condiviso, è necessario aggiungere una regola NAT per eseguire il mapping di una porta esterna per la porta WinRM. Questo passaggio non è obbligatorio se si crea la macchina virtuale con un indirizzo IP pubblico.
   >
   >

1. Salvare il modello come file nel computer in uso. Nome del file **CreateVMTemplate.json**.
1. Archiviare il modello di sistema di controllo del codice sorgente.
1. Aprire un editor di testo e incollare lo script seguente.

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

1. Controllare lo script sistema di controllo del codice sorgente. Nome simile **GetLabVMParams.ps1**.

   Quando si esegue questo script per l'agente come parte della definizione di versione e, se si utilizza attività, ad esempio *copia dei File di Azure* o *PowerShell nei computer di destinazione*, lo script raccoglie i valori che è necessario distribuire l'app per la macchina virtuale. In genere si utilizzerà queste attività per distribuire le App in una macchina virtuale di Azure. Le attività richiedono valori, ad esempio il nome del gruppo di risorse macchina virtuale, l'indirizzo IP e il nome di dominio completo (FDQN).

## <a name="create-a-release-definition-in-release-management"></a>Creare una definizione di versione in Release Management
Per creare la definizione di versione, eseguire le operazioni seguenti:

1. Nel **versioni** scheda della finestra il **compilazione & versione** hub, selezionare il pulsante sul segno più (+).
2. Nel **Crea definizione di versione** finestra, seleziona il **vuoto** modello e quindi selezionare **Avanti**.
3. Selezionare **scegliere in un secondo momento**, quindi selezionare **crea** per creare una nuova definizione di versione con l'ambiente di un valore predefinito e non ci sono artefatti collegati.
4. Per aprire il menu di scelta rapida, nella nuova definizione di versione, selezionare i puntini di sospensione (...) accanto al nome dell'ambiente, quindi **configurare le variabili**. 
5. Nel **- Configura ambiente** finestra, per le variabili utilizzate nelle attività di definizione di versione, immettere i valori seguenti:

   a. Per **vmName**, immettere il nome assegnato alla macchina virtuale quando è stato creato il modello di gestione risorse nel portale di Azure.

   b. Per **userName**, immettere il nome utente è assegnato alla macchina virtuale quando è stato creato il modello di gestione risorse nel portale di Azure.

   c. Per **password**, immettere la password assegnata alla macchina virtuale quando è stato creato il modello di gestione risorse nel portale di Azure. Utilizzare "lucchetto" per nascondere e proteggere la password.

### <a name="create-a-vm"></a>Creare una macchina virtuale

La fase successiva della distribuzione consiste nel creare la macchina virtuale da usare come "immagine finale" per le distribuzioni successive. Creare la macchina virtuale all'interno dell'istanza di Azure DevTest Lab tramite l'attività sviluppato appositamente per questo scopo. 

1. Nella definizione di versione, selezionare **aggiungere attività**.
2. Nel **Distribuisci** scheda, aggiungere un *Azure DevTest Labs creare VM* attività. Configurare l'attività come indicato di seguito:

   > [!NOTE]
   > Per creare la macchina virtuale da utilizzare per le distribuzioni successive, vedere [attività Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Per **sottoscrizione di Azure RM**, selezionare una connessione nel **connessioni del servizio di Azure disponibili** elenco o creare una connessione di autorizzazioni più limitata alla sottoscrizione di Azure. Per ulteriori informazioni, vedere [endpoint del servizio Gestione risorse di Azure](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

   b. Per **nome Lab**, selezionare il nome dell'istanza creata in precedenza.

   c. Per **nome modello**, immettere il percorso completo e il nome del file di modello che è stato salvato nel repository di codice sorgente. È possibile utilizzare le proprietà predefinite di Release Management per semplificare il percorso, ad esempio:

   ```
   $(System.DefaultWorkingDirectory)/Contoso/ARMTemplates/CreateVMTemplate.json
   ```

   d. Per **parametri di modello**, immettere i parametri per le variabili definite nel modello. Utilizzare i nomi delle variabili definite nell'ambiente, ad esempio:

   ```
   -newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)
   ```

   e. Per **variabili di Output - ID macchina virtuale Lab**, è necessario l'ID della macchina virtuale appena creata per i passaggi successivi. Impostare il nome predefinito della variabile di ambiente che viene popolata automaticamente con questo ID nel **variabili di Output** sezione. È possibile modificare la variabile, se necessario, ma è necessario utilizzare il nome corretto nelle attività successive. L'ID di macchina virtuale Lab è scritto nel formato seguente:

   ```
   /subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.DevTestLab/labs/{labName}/virtualMachines/{vmName}
   ```

3. Eseguire lo script creata in precedenza per raccogliere i dettagli della macchina virtuale DevTest Labs. 
4. Nella definizione di versione, selezionare **aggiungere attività** e quindi scegliere il **Distribuisci** scheda, aggiungere un *Azure PowerShell* attività. Configurare l'attività come indicato di seguito:

   > [!NOTE]
   > Per raccogliere i dettagli della macchina virtuale DevTest Labs, vedere [Distribuisci: Azure PowerShell](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/AzurePowerShell) ed eseguire lo script.

   a. Per **il tipo di connessione di Azure**selezionare **Azure Resource Manager**.

   b. Per **sottoscrizione di Azure RM**, selezionare una connessione dall'elenco in **connessioni del servizio di Azure disponibili**, o creare una connessione di autorizzazioni più limitata alla sottoscrizione di Azure. Per ulteriori informazioni, vedere [endpoint del servizio Gestione risorse di Azure](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

   c. Per **tipo Script**selezionare **File Script**.
 
   d. Per **percorso dello Script**, immettere il percorso completo e il nome dello script salvato nel repository del codice sorgente. È possibile utilizzare le proprietà predefinite di Release Management per semplificare il percorso, ad esempio:
      ```
      $(System.DefaultWorkingDirectory/Contoso/Scripts/GetLabVMParams.ps1
      ```
   e. Per **argomenti Script**, immettere il nome della variabile di ambiente che è stata popolata automaticamente con l'ID del laboratorio di macchina virtuale dall'attività precedente, ad esempio: 
      ```
      -labVmId '$(labVMId)'
      ```
    Lo script raccoglie i valori richiesti e li archivia in variabili di ambiente all'interno della definizione di versione in modo che è possibile fare riferimento a essi è facilmente nei passaggi successivi.

5. Distribuire l'app per la nuova macchina virtuale di DevTest Labs. Le attività è in genere utilizzare per distribuire l'app sono *copia dei File di Azure* e *PowerShell nei computer di destinazione*.
   Le informazioni sulla macchina virtuale è necessario per i parametri di queste attività vengono archiviate in tre variabili di configurazione denominate **labVmRgName**, **labVMIpAddress**, e **labVMFqdn**all'interno della definizione di versione. Se si desidera sperimentare con la creazione di una macchina virtuale di DevTest Labs e un'immagine personalizzata, senza distribuire un'app a esso, è possibile ignorare questo passaggio.

### <a name="create-an-image"></a>Creare un'immagine

La fase successiva consiste nel creare un'immagine della macchina virtuale appena distribuita nell'istanza di Azure DevTest Labs. È quindi possibile utilizzare l'immagine per creare copie della macchina virtuale su richiesta, ogni volta che si desidera eseguire un'attività di sviluppo o alcuni test. 

1. Nella definizione di versione, selezionare **aggiungere attività**.
2. Nel **Distribuisci** scheda, aggiungere un **Azure DevTest Labs crea personalizzato immagine** attività. Configurare l'app come segue:

   > [!NOTE]
   > Per creare l'immagine, vedere [attività Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Per **sottoscrizione di Azure RM**nella **connessioni del servizio di Azure disponibili** elenco, selezionare una connessione dall'elenco o creare una connessione di autorizzazioni più limitata alla sottoscrizione di Azure. Per ulteriori informazioni, vedere [endpoint del servizio Gestione risorse di Azure](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

   b. Per **nome Lab**, selezionare il nome dell'istanza creata in precedenza.

   c. Per **nome immagine personalizzato**, immettere un nome per l'immagine personalizzata.

   d. (Facoltativo) Per **descrizione**, immettere una descrizione che rendono più semplice selezionare l'immagine corretta in un secondo momento.

   e. Per **origine Lab VM - ID macchina virtuale di origine Lab**, se è stato modificato il nome predefinito della variabile di ambiente che è stata popolata automaticamente con l'ID del laboratorio di macchina virtuale da un'attività precedente, modificarlo qui. Il valore predefinito è **$(labVMId)**.

   f. Per **variabili di Output - ID immagine personalizzata**, quando si desidera gestire o eliminarlo, è necessario l'ID dell'immagine appena creato. Il nome predefinito della variabile di ambiente che viene popolata automaticamente con questo ID è impostato **variabili di Output** sezione. Se necessario, è possibile modificare la variabile.

### <a name="delete-the-vm"></a>Eliminare la macchina virtuale

La fase finale consiste nell'eliminare la macchina virtuale che è stato distribuito nell'istanza di Azure DevTest Labs. In genere, è possibile eliminare la macchina virtuale dopo aver eseguito le attività di sviluppo o eseguire i test desiderati nella macchina virtuale distribuita. 

1. Nella definizione di versione, selezionare **aggiungere attività** e quindi scegliere il **Distribuisci** scheda, aggiungere un *Azure DevTest Labs eliminare VM* attività. Configurare l'app come segue:

      > [!NOTE]
      > Per eliminare la macchina virtuale, vedere [Azure DevTest Labs attività](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Per **sottoscrizione di Azure RM**, selezionare una connessione nel **connessioni del servizio di Azure disponibili** elenco o creare una connessione di autorizzazioni più limitata alla sottoscrizione di Azure. Per ulteriori informazioni, vedere [endpoint del servizio Gestione risorse di Azure](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).
 
   b. Per **ID macchina virtuale Lab**, se è stato modificato il nome predefinito della variabile di ambiente che è stata popolata automaticamente con l'ID del laboratorio di macchina virtuale da un'attività precedente, modificarlo qui. Il valore predefinito è **$(labVMId)**.

2. Immettere un nome per la definizione di versione e quindi salvarlo.
3. Creare una nuova versione, selezionare la build più recente e distribuirlo nell'ambiente solo nella definizione.

In ogni fase, aggiornare la visualizzazione dell'istanza di DevTest Labs nel portale di Azure per visualizzare la macchina virtuale e l'immagine che si stanno creando e la macchina virtuale da eliminare nuovamente.

È ora possibile utilizzare l'immagine personalizzata per creare macchine virtuali quando sono necessari.


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [Creare un ambiente con più VM con i modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md).
* Più modelli di gestione risorse di avvio rapido per l'automazione di DevTest Labs da esplorare il [pubblica repository GitHub di DevTest Labs](https://github.com/Azure/azure-quickstart-templates).
* Se necessario, vedere il [la risoluzione dei problemi di VSTS](https://docs.microsoft.com/vsts/build-release/actions/troubleshooting) pagina.
 
