---
title: Come eliminare ed esportare dati personali da Azure DevTest Labs | Microsoft Docs
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
ms.date: 07/19/2019
ms.author: spelluru
ms.openlocfilehash: 82ab8ef2e444b71f41fbbd87e4e9f8669e83e508
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371180"
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

**Andamento dei costi stimato per il mese di calendario corrente:** 
![Andamento dei costi stimato per il mese di calendario corrente](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Costi stimati per risorsa da inizio mese:** 
![Costi stimati per risorsa da inizio mese](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Perché sono necessari i dati personali?
Il servizio DevTest Labs usa i dati personali per scopi operativi. Questi dati sono fondamentali per consentire al servizio di fornire funzionalità chiave. Se si imposta un criterio di conservazione per l'indirizzo di posta elettronica utente, gli utenti del lab, dopo l'eliminazione del loro indirizzo di posta elettronica dal sistema, non ricevono notifiche tempestive di arresto automatico tramite posta elettronica. Analogamente, se gli ID oggetto utente vengono eliminati in base a un criterio di conservazione, gli amministratori dei lab non sono in grado di visualizzare gli andamenti dei costi mensili e i costi per risorsa per i computer dei lab. Di conseguenza, questi dati devono essere mantenuti fino a quando la risorsa dell'utente è attiva nel lab.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Come è possibile fare in modo che il sistema dimentichi i dati personali?
Se un utente del lab vuole che i dati personali vengano eliminati, può provvedere a eliminare la risorsa corrispondente nel lab. Il servizio DevTest Labs rende anonimi i dati personali eliminati 30 giorni dopo l'eliminazione da parte dell'utente.

Se ad esempio si elimina la macchina virtuale o si rimuove l'indirizzo di posta elettronica, il servizio DevTest Labs rende anonimi questi dati dopo 30 giorni dall'eliminazione della risorsa. Il criterio di conservazione dei 30 giorni dopo l'eliminazione serve ad assicurare all'amministratore del lab che le previsioni dei costi mensili sono accurate.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Come si può richiedere un'esportazione dei dati personali?
È possibile esportare i dati personali e di utilizzo del Lab usando il portale di Azure o PowerShell. I dati vengono esportati come due diversi file CSV:

- **Disks. csv** : contiene informazioni sui dischi usati dalle diverse macchine virtuali
- **VirtualMachines. csv** : contiene informazioni sulle macchine virtuali nel Lab.

### <a name="azure-portal"></a>Portale di Azure
Un utente del lab può richiedere un'esportazione dei dati personali archiviati nel servizio DevTest Labs. Per richiedere un'esportazione, passare all'opzione **Dati personali** nella pagina **Panoramica** del lab. Selezionare il pulsante **Richiedi esportazione** per avviare la creazione di un file Excel scaricabile nell'account di archiviazione dell'amministratore del lab. È quindi possibile contattare l'amministratore di lab per visualizzare i dati.

1. Selezionare **Dati personali** nel menu a sinistra. 

    ![Pagina Dati personali](./media/personal-data-delete-export/personal-data-page.png)
2. Selezionare il **gruppo di risorse** che contiene il lab.

    ![Seleziona gruppo di risorse](./media/personal-data-delete-export/select-resource-group.png)
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

I componenti principali dell'esempio precedente sono i seguenti:

- Comando Invoke-AzureRmResourceAction.
   
    ```
    Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
    ```
- Due parametri di azione
    - **blobStorageAbsoluteSasUri** : URI dell'account di archiviazione con il token di firma di accesso condiviso (SAS). Nello script di PowerShell, questo valore può essere passato al posto della chiave di archiviazione.
    - **usageStartDate** : data di inizio per il pull dei dati e la data di fine rappresenta la data corrente in cui viene eseguita l'azione. La granularità è a livello di giorno, quindi anche se si aggiungono informazioni temporali, verranno ignorate.

### <a name="exported-data---a-closer-look"></a>Dati esportati: un aspetto più vicino
Verranno ora esaminati in dettaglio i dati esportati. Come indicato in precedenza, una volta che i dati sono stati esportati correttamente, saranno presenti due file CSV. 

**VirtualMachines. csv** contiene le colonne di dati seguenti:

| Nome colonna | Descrizione |
| ----------- | ----------- | 
| SubscriptionId | Identificatore della sottoscrizione in cui è presente il Lab. |
| LabUId | Identificatore GUID univoco per il Lab. |
| LabName | Nome del lab. |
| LabResourceId | ID risorsa lab completo. |
| ResourceGroupName | Nome del gruppo di risorse che contiene la macchina virtuale | 
| ResourceId | ID risorsa completo per la macchina virtuale. |
| ResourceUId | GUID della macchina virtuale |
| Name | Nome della macchina virtuale. |
| CreatedTime | Data e ora in cui è stata creata la macchina virtuale. |
| DeletedDate | Data e ora in cui la macchina virtuale è stata eliminata. Se è vuota, l'eliminazione non è ancora stata eseguita. |
| ResourceOwner | Proprietario della macchina virtuale. Se il valore è vuoto, si tratta di una macchina virtuale richiedibile o creata da un'entità servizio. |
| PricingTier | Piano tariffario della macchina virtuale |
| ResourceStatus | Stato di disponibilità della macchina virtuale. Attivo, se è ancora presente o inattivo, se la macchina virtuale è stata eliminata. |
| ComputeResourceId | Identificatore completo della risorsa di calcolo della macchina virtuale. |
| Disposizione degli utenti | Impostare su true se la macchina virtuale è una macchina virtuale richiedibile | 
| Oggetto environmentID nell' | Identificatore di risorsa dell'ambiente in cui è stata creata la macchina virtuale. È vuota quando la macchina virtuale non è stata creata come parte di una risorsa dell'ambiente. |
| ExpirationDate | Data di scadenza della macchina virtuale. È impostato su Empty, se non è stata impostata una data di scadenza.
| GalleryImageReferenceVersion |  Versione dell'immagine di base della macchina virtuale. |
| GalleryImageReferenceOffer | Offerta dell'immagine di base della macchina virtuale. |
| GalleryImageReferencePublisher | Autore dell'immagine di base della macchina virtuale. |
| GalleryImageReferenceSku | SKU dell'immagine di base della macchina virtuale |
| GalleryImageReferenceOsType | Tipo di sistema operativo dell'immagine di base della macchina virtuale |
| CustomImageId | ID completo dell'immagine personalizzata di base della macchina virtuale. |

Di seguito sono elencate le colonne di dati contenute in **Disks. csv** :

| Nome colonna | Descrizione | 
| ----------- | ----------- | 
| SubscriptionId | ID della sottoscrizione che contiene il Lab |
| LabUId | GUID per il Lab |
| LabName | Nome del Lab | 
| LabResourceId | ID risorsa completo per il Lab | 
| ResourceGroupName | Nome del gruppo di risorse che contiene il Lab | 
| ResourceId | ID risorsa completo per la macchina virtuale. |
| ResourceUId | GUID della macchina virtuale |
 |Name | Nome del disco collegato |
| CreatedTime |Data e ora di creazione del disco dati. |
| DeletedDate | Data e ora in cui è stato eliminato il disco dati. |
| ResourceStatus | Stato della risorsa. Attivo, se la risorsa esiste. Inattivo, quando viene eliminato. |
| DiskBlobName | Nome del BLOB per il disco dati. |
| DiskSizeGB | Dimensioni del disco dati. |
| DiskType | Tipo del disco dati. 0 per standard, 1 per Premium. |
| LeasedByVmId | ID risorsa della macchina virtuale a cui è stato collegato il disco dati. |


> [!NOTE]
> Se si gestiscono più Lab e si desidera ottenere informazioni generali, le due colonne chiave sono **LabUID** e **ResourceUId**, che sono gli ID univoci tra le sottoscrizioni.

I dati esportati possono essere modificati e visualizzati usando strumenti, ad esempio SQL Server, Power BI e così via. Questa funzionalità è particolarmente utile quando si vuole segnalare l'utilizzo del Lab al team di gestione che potrebbe non usare la stessa sottoscrizione di Azure.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti: 

- [Impostare i criteri per un lab](devtest-lab-get-started-with-lab-policies.md)
- [Domande frequenti](devtest-lab-faq.md)
