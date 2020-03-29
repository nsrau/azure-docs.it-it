---
title: Come eliminare ed esportare dati personali da Azure DevTest Labs
description: Informazioni su come eliminare ed esportare dati personali dal servizio Azure DevLast Labs per adempiere agli obblighi derivanti dall'applicazione del Regolamento generale sulla protezione dei dati (GDPR).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: c87e2fb534480bbf9bbe625d67782e5a11eda18c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169690"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Esportare o eliminare dati personali da Azure DevTest Labs
Questo articolo illustra la procedura da eseguire per eliminare ed esportare dati personali dal servizio Azure DevTest Labs. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>Quali dati personali vengono raccolti da DevTest Labs?
DevTest Labs raccoglie due principali tipi di dati personali dell'utente: l'indirizzo di posta elettronica utente e l'ID oggetto utente. Queste informazioni sono fondamentali per il servizio per fornire funzionalità interne ad amministratori e utenti dei lab.

### <a name="user-email-address"></a>Indirizzo di posta elettronica utente
DevTest Labs usa l'indirizzo di posta elettronica utente per inviare notifiche di arresto automatico tramite posta elettronica agli utenti dei lab. Il messaggio di posta elettronica notifica agli utenti che il loro computer sta per essere arrestato. Gli utenti, se lo desiderano, possono posporre o ignorare l'arresto. L'indirizzo di posta elettronica viene configurato a livello di lab o a livello di macchina virtuale.

**Impostazione dell'indirizzo di posta elettronica a livello di lab:**

![Impostazione dell'indirizzo di posta elettronica a livello di lab](./media/personal-data-delete-export/lab-user-email.png)

**Impostazione dell'indirizzo di posta elettronica a livello di macchina virtuale:**

![Impostazione dell'indirizzo di posta elettronica a livello di macchina virtuale](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>ID oggetto utente
DevTest Labs usa l'ID oggetto utente per mostrare agli amministratori dei lab gli andamenti mensili dei costi e le informazioni sui costi per risorsa. Consente di tenere traccia dei costi e gestire i valori di soglia per i lab. 

**Tendenza dei costi stimati per il mese di calendario corrente:**
![tendenza dei costi stimati per il mese di calendario corrente](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Costo stimato**
![mensile per risorsa: costo stimato da un mese alla data](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Perché sono necessari i dati personali?
Il servizio DevTest Labs usa i dati personali per scopi operativi. Questi dati sono fondamentali per consentire al servizio di fornire funzionalità chiave. Se si imposta un criterio di conservazione per l'indirizzo di posta elettronica utente, gli utenti del lab, dopo l'eliminazione del loro indirizzo di posta elettronica dal sistema, non ricevono notifiche tempestive di arresto automatico tramite posta elettronica. Analogamente, se gli ID oggetto utente vengono eliminati in base a un criterio di conservazione, gli amministratori dei lab non sono in grado di visualizzare gli andamenti dei costi mensili e i costi per risorsa per i computer dei lab. Di conseguenza, questi dati devono essere mantenuti fino a quando la risorsa dell'utente è attiva nel lab.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Come è possibile fare in modo che il sistema dimentichi i dati personali?
Se un utente del lab vuole che i dati personali vengano eliminati, può provvedere a eliminare la risorsa corrispondente nel lab. Il servizio DevTest Labs rende anonimi i dati personali eliminati 30 giorni dopo l'eliminazione da parte dell'utente.

Se ad esempio si elimina la macchina virtuale o si rimuove l'indirizzo di posta elettronica, il servizio DevTest Labs rende anonimi questi dati dopo 30 giorni dall'eliminazione della risorsa. Il criterio di conservazione dei 30 giorni dopo l'eliminazione serve ad assicurare all'amministratore del lab che le previsioni dei costi mensili sono accurate.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Come si può richiedere un'esportazione dei dati personali?
È possibile esportare i dati di utilizzo personali e lab usando il portale di Azure o PowerShell.You can export personal and lab usage data by using the Azure portal or PowerShell. I dati vengono esportati come due file CSV diversi:

- **disks.csv** - contiene informazioni sui dischi utilizzati dalle diverse macchine virtuali
- **virtualmachines.csv:** contiene informazioni sulle macchine virtuali nel lab.

### <a name="azure-portal"></a>Portale di Azure
Un utente del lab può richiedere un'esportazione dei dati personali archiviati nel servizio DevTest Labs. Per richiedere un'esportazione, passare all'opzione **Dati personali** nella pagina **Panoramica** del lab. Selezionare il pulsante **Richiedi esportazione** per avviare la creazione di un file Excel scaricabile nell'account di archiviazione dell'amministratore del lab. È quindi possibile contattare l'amministratore di lab per visualizzare i dati.

1. Selezionare **Dati personali** nel menu a sinistra. 

    ![Pagina Dati personali](./media/personal-data-delete-export/personal-data-page.png)
2. Selezionare il **gruppo di risorse** che contiene il lab.

    ![Selezionare il gruppo di risorse](./media/personal-data-delete-export/select-resource-group.png)
3. Selezionare l'**account di archiviazione** nel gruppo di risorse.
4. Nella pagina **Account di archiviazione** selezionare **BLOB**.

    ![Selezionare il riquadro BLOB](./media/personal-data-delete-export/select-blobs-tile.png)
5. Selezionare il contenitore denominato **labresourceusage** nell'elenco dei contenitori.

    ![Selezionare il contenitore BLOB](./media/personal-data-delete-export/select-blob-container.png)
6. Selezionare la **cartella** denominata in base al lab. In questa cartella sono presenti file con estensione **csv** per i **dischi** e le **macchine virtuali** del lab. È possibile scaricare i file con estensione csv, filtrare il contenuto relativo all'utente del lab che richiede un accesso e condividere il contenuto con tale utente.

    ![Scaricare il file con estensione csv](./media/personal-data-delete-export/download-csv-file.png)

### <a name="azure-powershell"></a>Azure PowerShell

```powershell
Param (
    [Parameter (Mandatory=$true, HelpMessage="The storage account name where to store usage data")]
    [string] $storageAccountName,

    [Parameter (Mandatory=$true, HelpMessage="The storage account key")]
    [string] $storageKey,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab name to get usage data from")]
    [string] $labName,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab subscription")]
    [string] $labSubscription
    )

#Login
Login-AzureRmAccount

# Set the subscription for the lab
Get-AzureRmSubscription -SubscriptionId $labSubscription  | Select-AzureRmSubscription

# DTL will create this container in the storage when invoking the action, cannot be changed currently
$containerName = "labresourceusage"

# Get the storage context
$Ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageKey 
$SasToken = New-AzureStorageAccountSASToken -Service Blob, File -ResourceType Container, Service, Object -Permission rwdlacup -Protocol HttpsOnly -Context $Ctx

# Generate the storage blob uri
$blobUri = $Ctx.BlobEndPoint + $SasToken

# blobStorageAbsoluteSasUri and usageStartDate are required
$actionParameters = @{
    'blobStorageAbsoluteSasUri' = $blobUri    
}

$startdate = (Get-Date).AddDays(-7)

$actionParameters.Add('usageStartDate', $startdate.Date.ToString())

# Get the lab resource group
$resourceGroupName = (Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $labName}).ResourceGroupName
    
# Create the lab resource id
$resourceId = "/subscriptions/" + $labSubscription + "/resourceGroups/" + $resourceGroupName + "/providers/Microsoft.DevTestLab/labs/" + $labName + "/"

# !!!!!!! this is the new resource action to get the usage data.
$result = Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
 
# Finish up cleanly
if ($result.Status -eq "Succeeded") {
    Write-Output "Telemetry successfully downloaded for " $labName
    return 0
}
else
{
    Write-Output "Failed to download lab: " + $labName
    Write-Error $result.toString()
    return -1
}
```

I componenti chiave nell'esempio precedente sono:

- Il comando Invoke-AzureRmResourceAction.The Invoke-AzureRmResourceAction command.
   
    ```
    Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
    ```
- Due parametri di azione
    - **blobStorageAbsoluteSasUri:** URI dell'account di archiviazione con il token firma di accesso condiviso (SAS). Nello script di PowerShell questo valore può essere passato anziché la chiave di archiviazione.
    - **usageStartDate** - La data di inizio per il pull dei dati, con la data di fine è la data corrente in cui viene eseguita l'azione. La granularità è a livello di giorno, quindi anche se si aggiungono informazioni sull'ora, verrà ignorata.

### <a name="exported-data---a-closer-look"></a>Dati esportati - uno sguardo più da vicino
Ora diamo un'occhiata più da vicino ai dati esportati. Come accennato in precedenza, una volta esportati correttamente i dati, saranno presenti due file CSV. 

Il **file virtualmachines.csv** contiene le colonne di dati seguenti:

| Nome colonna | Descrizione |
| ----------- | ----------- | 
| SubscriptionId | Identificatore della sottoscrizione in cui è presente il lab. |
| IDLabUId (IdLaboratorio) | Identificatore GUID univoco per il lab. |
| Nomelabi | Nome del lab. |
| LabResourceId | ID risorsa lab completo. |
| ResourceGroupName | Nome del gruppo di risorse che contiene la macchina virtualeName of the resource group that contains the VM | 
| ResourceId | ID risorsa completo per la macchina virtuale. |
| ResourceUId | GUID for the VM |
| Nome | Nome macchina virtuale. |
| CreatedTime | Data e ora di creazione della macchina virtuale. |
| DeletedDate | Data e ora in cui la macchina virtuale è stata eliminata. Se è vuota, l'eliminazione non si è ancora verificata. |
| ResourceOwner (Proprietario risorsa) | Proprietario della macchina virtuale. Se il valore è vuoto, si tratta di una macchina virtuale rivendicabile o creata da un'entità servizio. |
| Livello prezzi | Piano tariffario della macchina virtualePricing tier of the VM |
| ResourceStatus (Statorisorsa) | Stato di disponibilità della macchina virtuale. Attivo, se esiste ancora o Inattivo, se la macchina virtuale è stata eliminata. |
| ComputeResourceIdComputeResourceId | Identificatore completo della risorsa di calcolo della macchina virtuale. |
| Rivendicabile | Impostato su true se la macchina virtuale è una macchina virtuale rivendicabileSet to true if the VM is a claimable VM | 
| Idambiente | Identificatore di risorsa dell'ambiente all'interno del quale è stata creata la macchina virtuale. È vuoto quando la macchina virtuale non è stata creata come parte di una risorsa di ambiente. |
| ExpirationDate | Data di scadenza per la macchina virtuale. È impostata su vuoto, se non è stata impostata una data di scadenza.
| RaccoltaImageReferenceVersion |  Versione dell'immagine di base della macchina virtuale. |
| RaccoltaImageReferenceOffer | Offerta dell'immagine di base della macchina virtuale. |
| GalleryImageReferencePublisher | Autore dell'immagine di base della macchina virtuale. |
| RaccoltaImmaginiReferenceSku | Sku dell'immagine di base della macchina virtuale |
| GalleryImageReferenceOsType | Tipo di sistema operativo dell'immagine di base della macchina virtuale |
| CustomImageId | ID completo dell'immagine personalizzata di base della macchina virtuale. |

Le colonne di dati contenute in **disks.csv** sono elencate di seguito:

| Nome colonna | Descrizione | 
| ----------- | ----------- | 
| SubscriptionId | ID della sottoscrizione che contiene il lab |
| IDLabUId (IdLaboratorio) | GUID per il lab |
| Nomelabi | Nome del laboratorio | 
| LabResourceId | ID risorsa completo per il lab | 
| ResourceGroupName | Nome del gruppo di risorse che contiene il lab | 
| ResourceId | ID risorsa completo per la macchina virtuale. |
| ResourceUId | GUID for the VM |
 |Nome | Il nome del disco collegato |
| CreatedTime |Data e ora di creazione del disco dati. |
| DeletedDate | Data e ora di eliminazione del disco dati. |
| ResourceStatus (Statorisorsa) | Stato della risorsa. Attivo, se la risorsa esiste. Inattivo, quando eliminato. |
| Nome Dischio | Nome BLOB per il disco dati. |
| DiskSizeGB | Dimensioni del disco dati. |
| DiskType | Tipo di disco dati. 0 per Standard, 1 per Premium. |
| LeasedByVmIdLeasedByVmId | ID risorsa della macchina virtuale a cui è stato collegato il disco dati. |


> [!NOTE]
> Se si ha a che fare con più lab e si desidera ottenere informazioni generali, le due colonne chiave sono **LabUID** e **ResourceUId**, ovvero gli ID univoci tra le sottoscrizioni.

I dati esportati possono essere manipolati e visualizzati usando strumenti come SQL Server, Power BI e così via. Questa funzionalità è particolarmente utile quando si vuole segnalare l'utilizzo del lab al team di gestione che potrebbe non usare la stessa sottoscrizione di Azure.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti: 

- [Impostare i criteri per un lab](devtest-lab-get-started-with-lab-policies.md)
- [Domande frequenti](devtest-lab-faq.md)
