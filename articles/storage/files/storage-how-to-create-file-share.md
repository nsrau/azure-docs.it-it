---
title: Creare una condivisione file di Azure
titleSuffix: Azure Files
description: Come creare una condivisione file di Azure usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.How to create an Azure file share by using the Azure portal, PowerShell, or the Azure CLI.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 2/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ed6abbac7c5953eaec4fa4584248d0d98b49ba63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596902"
---
# <a name="create-an-azure-file-share"></a>Creare una condivisione file di Azure
Per creare una condivisione file di Azure, è necessario rispondere a tre domande su come usarla:To create an Azure file share, you need to answer three questions about how you will use it:

- **Quali sono i requisiti di prestazioni per la condivisione file di Azure?**  
    File di Azure offre condivisioni file standard, ospitate su hardware basato su disco rigido (basato su disco rigido) e condivisioni file premium, ospitate su hardware basato su disco a stato solido.

- **Di quali dimensioni è necessaria la condivisione file?**  
    Le condivisioni file standard possono estendersi fino a 100 TiB, tuttavia questa funzionalità non è abilitata per impostazione predefinita; se è necessaria una condivisione file superiore a 5 TiB, sarà necessario abilitare la funzionalità di condivisione file di grandi dimensioni per l'account di archiviazione. Le condivisioni file Premium possono estendersi fino a 100 TiB senza alcuna impostazione speciale, tuttavia viene eseguito il provisioning di condivisioni file premium, anziché pagare come si va come condivisioni di file standard. Ciò significa che il provisioning di una condivisione file molto più grande di quello necessario aumenterà il costo totale dello spazio di archiviazione.

- **Quali sono i requisiti di ridondanza per la condivisione file di Azure?**  
    Le condivisioni file standard offrono l'archiviazione con ridondanza locale (LRS), ridondanza di zona, georidonziazione (GRS) o geo-zona ridondante, tuttavia la funzionalità di condivisione file di grandi dimensioni è supportata solo nelle condivisioni file ridondanti localmente e ridondanti di zona. Le condivisioni file Premium non supportano alcuna forma di ridondanza geografica.

    Le condivisioni file Premium sono disponibili con ridondanza locale nella maggior parte delle aree che offrono account di archiviazione e con ridondanza di zona in un sottoinsieme di aree più piccolo. Per scoprire se le condivisioni file premium sono attualmente disponibili nella tua area geografica, vedi la pagina [dei prodotti disponibili per area](https://azure.microsoft.com/global-infrastructure/services/?products=storage) geografica per Azure.To find out if premium file shares are currently available in your region, see the products available by region page for Azure. Per informazioni sulle aree che supportano il sistema di ridondanza di Archiviazione di Azure, vedere [Ridondanza](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)di Archiviazione di Azure.For information about regions that support .RS, see Azure Storage redundancy .

Per altre informazioni su queste tre opzioni, vedere Pianificazione di una distribuzione di File di Azure .For more information on these three choices, see [Planning for an Azure Files deployment](storage-files-planning.md).

## <a name="prerequisites"></a>Prerequisiti
- Questo articolo presuppone che sia già stata creata una sottoscrizione di Azure.This article assumes that you have already created an Azure subscription. Se non si ha già una sottoscrizione, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Se si intende usare Azure PowerShell, [installare la versione più recente.](https://docs.microsoft.com/powershell/azure/install-az-ps)
- Se si intende usare l'interfaccia della riga di comando di Azure, [installare la versione più recente.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-a-storage-account"></a>Creare un account di archiviazione
Le condivisioni file di Azure vengono distribuite *in account di archiviazione,* ovvero oggetti di primo livello che rappresentano un pool condiviso di archiviazione. Questo pool di archiviazione può essere utilizzato per distribuire più condivisioni file. 

Azure supports multiple types of storage accounts for different storage scenarios customers may have, but there are two main types of storage accounts for Azure Files. Il tipo di account di archiviazione da creare dipende dal fatto che si desideri creare una condivisione file standard o una condivisione file Premium:Which storage account type you need to create depends on whether you want to create a standard file share or a premium file share: 

- Account di **archiviazione generici versione 2 (GPv2):** gli account di archiviazione GPv2 consentono di distribuire condivisioni file di Azure in hardware basato su disco rigido/standard/HDD. Oltre a archiviare le condivisioni file di Azure, gli account di archiviazione GPv2 possono archiviare altre risorse di archiviazione, ad esempio contenitori BLOB, code o tabelle. 

- **Account di archiviazione fileStorage:** gli account di archiviazione FileStorage consentono di distribuire condivisioni file di Azure nell'hardware basato su disco premium/solid-state. Gli account FileStorage possono essere usati solo per archiviare condivisioni file di Azure.FileStorage accounts can only be used to store Azure file shares; non è possibile distribuire altre risorse di archiviazione (contenitori BLOB, code, tabelle e così via) in un account FileStorage.No other storage resources (blob containers, queues, tables, etc.) can be deployed in a FileStorage account.

# <a name="portal"></a>[Portale](#tab/azure-portal)
Per creare un account di archiviazione tramite il portale di Azure, selezionare **Crea una risorsa** dal dashboard. Nella finestra di ricerca di Azure Marketplace risultante cercare **l'account di archiviazione** e selezionare il risultato della ricerca risultante. Questo porterà a una pagina di panoramica per gli account di archiviazione; Selezionare **Crea** per procedere con la procedura guidata di creazione dell'account di archiviazione.

![Schermata dell'opzione di creazione rapida dell'account di archiviazione in un browser](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="the-basics-section"></a>Sezione Nozioni di base
La prima sezione da completare per creare un account di archiviazione è denominata **Nozioni di base**. Contiene tutti i campi obbligatori per creare un account di archiviazione. Per creare un account di archiviazione GPv2, verificare che il pulsante di opzione **Prestazioni** sia impostato su *Standard* e che l'elenco a discesa Tipo di **account** sia selezionato su *StorageV2 (uso generale v2).*

![Schermata del pulsante di opzione Prestazioni con Standard selezionato e Tipo di account con StorageV2 selezionato](media/storage-how-to-create-file-share/create-storage-account-1.png)

Per creare un account di archiviazione FileStorage, verificare che il pulsante di opzione **Prestazioni** sia impostato su *Premium* e che l'elenco a discesa **Tipo di** account sia selezionato in Archiviazione *file*.

![Schermata del pulsante di opzione Prestazioni con Premium selezionato e Tipo di account con FileStorage selezionato](media/storage-how-to-create-file-share/create-storage-account-2.png)

Gli altri campi di base sono indipendenti dalla scelta dell'account di archiviazione:The other basics fields are independent from the choice of storage account:
- **Sottoscrizione:** sottoscrizione dell'account di archiviazione da distribuire. 
- Gruppo di **risorse:** il gruppo di risorse in cui distribuire l'account di archiviazione. È possibile creare un nuovo gruppo di risorse o usare un gruppo di risorse esistente. Un gruppo di risorse è un contenitore logico per raggruppare i servizi di Azure. Quando si crea un account di archiviazione, è possibile creare un nuovo gruppo di risorse o usarne uno esistente.
- **Nome account di archiviazione:** nome della risorsa dell'account di archiviazione da creare. Questo nome deve essere univoco a livello globale, ma in caso contrario può qualsiasi nome desiderato. Il nome dell'account di archiviazione verrà usato come nome del server quando si monta una condivisione file di Azure tramite SMB.
- **Percorso:** l'area in cui distribuire l'account di archiviazione. Può trattarsi dell'area associata al gruppo di risorse o a qualsiasi altra area disponibile.
- **Replica**: Sebbene questa sia la replica etichettata, questo campo indica in realtà **ridondanza**; questo è il livello di ridondanza desiderato: ridondanza locale (LRS), ridondanza di zona (RS), ridondanza geografica (GRS) e ridondanza geografica. Questo elenco a discesa contiene anche la ridondanza geografica di accesso in lettura (RA-GRS) e la ridondanza delle zone geografiche di accesso in lettura, che non si applicano alle condivisioni file di Azure. qualsiasi condivisione file creata in un account di archiviazione con questi account selezionati sarà effettivamente ridondante geografica o geo-zona-ridondante, rispettivamente. A seconda dell'area o del tipo di account di archiviazione selezionato, alcune opzioni di ridondanza potrebbero non essere consentite.
- **Livello di accesso**: questo campo non si applica a File di Azure, pertanto è possibile scegliere uno dei pulsanti di opzione.

#### <a name="the-networking-blade"></a>La lama Rete
La sezione Networking consente di configurare le opzioni di rete. Queste impostazioni sono facoltative per la creazione dell'account di archiviazione e possono essere configurate in un secondo momento, se lo si desidera. Per altre informazioni su queste opzioni, vedere [Considerazioni](storage-files-networking-overview.md)sulla rete di File di Azure.For more information on these options, see Azure Files networking considerations .

#### <a name="the-advanced-blade"></a>La lama avanzata
La sezione avanzata contiene diverse impostazioni importanti per le condivisioni file di Azure:The advanced section contains several important settings for Azure file shares:

- **Passaggio sicuro obbligatorio:** questo campo indica se l'account di archiviazione richiede la crittografia in transito per la comunicazione con l'account di archiviazione. Si consiglia di attivarla, tuttavia, se è necessario il supporto per SMB 2.1, è necessario disattivarlo. Se si disabilita la crittografia, è consigliabile vincolare l'accesso dell'account di archiviazione a una rete virtuale con endpoint di servizio e/o privati.
- **Condivisioni file**di grandi dimensioni: questo campo abilita l'account di archiviazione per le condivisioni file che si estende fino a 100 TiB. L'abilitazione di questa funzionalità limiterà l'account di archiviazione solo alle opzioni di archiviazione ridondanti localmente e ridondazione di zona. Dopo aver abilitato un account di archiviazione GPv2 per condivisioni file di grandi dimensioni, non è possibile disabilitare la funzionalità di condivisione file di grandi dimensioni. Gli account di archiviazione fileStorage (account di archiviazione per condivisioni file premium) non dispongono di questa opzione, poiché tutte le condivisioni file premium possono essere scalate fino a 100 TiB. 

![Schermata delle impostazioni avanzate importanti che si applicano ai file di Azure](media/storage-how-to-create-file-share/create-storage-account-3.png)

The other settings that are available in the advanced tab (blob soft-delete, hierarchical namespace for Azure Data Lake storage gen 2, and NFSv3 for blob storage) do not apply to Azure Files.

#### <a name="tags"></a>Tag
I tag sono coppie nome/valore che consentono di classificare le risorse e visualizzare la fatturazione consolidata applicando lo stesso tag a più risorse e gruppi di risorse. Questi sono facoltativi e possono essere applicati dopo la creazione dell'account di archiviazione.

#### <a name="review--create"></a>Rivedi e crea
Il passaggio finale per creare l'account di archiviazione consiste nel selezionare il pulsante **Crea** nella scheda **Revisione e creazione.** Questo pulsante non sarà disponibile se tutti i campi obbligatori per un account di archiviazione non sono compilati.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Per creare un account di archiviazione usando `New-AzStorageAccount` PowerShell, useremo il cmdlet. Questo cmdlet ha molte opzioni; vengono visualizzate solo le opzioni richieste. Per ulteriori informazioni sulle opzioni avanzate, vedere la [ `New-AzStorageAccount` documentazione relativa](/powershell/module/az.storage/new-azstorageaccount)ai cmdlet .

Per semplificare la creazione dell'account di archiviazione e della successiva condivisione file, verranno archiviati diversi parametri nelle variabili. È possibile sostituire il contenuto della variabile con i valori desiderati, tuttavia si noti che il nome dell'account di archiviazione deve essere univoco a livello globale.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

Per creare un account di archiviazione in grado di archiviare condivisioni file standard di Azure, verrà usato il comando seguente. Il `-SkuName` parametro si riferisce al tipo di ridondanza desiderata; se si desidera un account di archiviazione con ridondanza geografica `-EnableLargeFileShare` o zona geografica, è necessario rimuovere anche il parametro.

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

Per creare un account di archiviazione in grado di archiviare condivisioni file Premium di Azure, verrà usato il comando seguente. Si noti che il `-SkuName` `Premium` parametro è stato modificato`LRS`per includere sia il livello di ridondanza desiderato di ridondanza locale ( ). Il `-Kind` parametro `FileStorage` `StorageV2` è invece di perché le condivisioni file premium devono essere create in un account di archiviazione FileStorage anziché in un account di archiviazione GPv2.

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)
Per creare un account di archiviazione usando l'interfaccia della riga di comando di Azure, verrà usato il comando az storage account create. Questo comando ha molte opzioni; vengono visualizzate solo le opzioni richieste. Per ulteriori informazioni sulle opzioni avanzate, vedere la [ `az storage account create` documentazione relativa](/cli/azure/storage/account)ai comandi .

Per semplificare la creazione dell'account di archiviazione e della successiva condivisione file, verranno archiviati diversi parametri nelle variabili. È possibile sostituire il contenuto della variabile con i valori desiderati, tuttavia si noti che il nome dell'account di archiviazione deve essere univoco a livello globale.

```azurecli-interactive
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

Per creare un account di archiviazione in grado di archiviare condivisioni file standard di Azure, verrà usato il comando seguente. Il `--sku` parametro si riferisce al tipo di ridondanza desiderata; se si desidera un account di archiviazione con ridondanza geografica `--enable-large-file-share` o zona geografica, è necessario rimuovere anche il parametro.

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

Per creare un account di archiviazione in grado di archiviare condivisioni file Premium di Azure, verrà usato il comando seguente. Si noti che il `--sku` `Premium` parametro è stato modificato`LRS`per includere sia il livello di ridondanza desiderato di ridondanza locale ( ). Il `--kind` parametro `FileStorage` `StorageV2` è invece di perché le condivisioni file premium devono essere create in un account di archiviazione FileStorage anziché in un account di archiviazione GPv2.

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>Creare la condivisione file
Dopo aver creato l'account di archiviazione, è sufficiente creare la condivisione file. Questo processo è per lo più lo stesso indipendentemente dal fatto che si sta utilizzando una condivisione file premium o una condivisione file standard. La differenza principale è la **quota** e ciò che rappresenta.

Per le condivisioni file standard, si tratta di un limite superiore della condivisione file di Azure, oltre il quale gli utenti finali non possono andare. Lo scopo principale per la quota per una condivisione file standard è il budget: "Non voglio che questa condivisione file cresca oltre questo punto". Se non viene specificata una quota, la condivisione file standard può estendersi fino a 100 TiB (o 5 TiB se la proprietà delle condivisioni file di grandi dimensioni non è impostata per un account di archiviazione).

Per le condivisioni file premium, la quota è sovraccaricata per indicare **le dimensioni di cui è stato eseguito**il provisioning . La dimensione di cui è stato eseguito il provisioning è l'importo che ti verrà fatturato, indipendentemente dall'utilizzo effettivo. Quando si esegue il provisioning di una condivisione file premium, si desidera considerare due fattori: 1) la crescita futura della condivisione dal punto di vista dell'utilizzo dello spazio e 2) le operazioni di I/O al secondo necessarie per il carico di lavoro. Ogni GiB di cui è stato eseguito il provisioning dà diritto a operazioni di I/O al secondo riservate ed espring aggiuntive. Per ulteriori informazioni su come pianificare una condivisione file premium, vedere provisioning di [condivisioni file premium](storage-files-planning.md#understanding-provisioning-for-premium-file-shares).

# <a name="portal"></a>[Portale](#tab/azure-portal)
Se l'account di archiviazione è stato appena creato, è possibile accedervi dalla schermata di distribuzione selezionando **Vai alla risorsa**. Se l'account di archiviazione è stato creato in precedenza, è possibile accedervi tramite il gruppo di risorse che lo contiene. Una volta nell'account di archiviazione, selezionare il riquadro Con l'etichetta **Condivisioni** file (è anche possibile passare a **Condivisioni file** tramite il sommario per l'account di archiviazione).

![Schermata del riquadro Condivisioni file](media/storage-how-to-create-file-share/create-file-share-1.png)

Nell'elenco delle condivisioni file dovrebbero essere visualizzate tutte le condivisioni file create in precedenza in questo account di archiviazione. una tabella vuota se non sono ancora state create condivisioni file. Selezionare **Condivisione file** per creare una nuova condivisione file.

Il nuovo pannello di condivisione file dovrebbe essere visualizzato sullo schermo. Completare i campi nel nuovo pannello di condivisione file per creare una condivisione file:Complete the fields in the new file share blade to create a file share:

- **Nome**: il nome della condivisione file da creare.
- **Quota**: la quota della condivisione file per le condivisioni file standard; dimensione della condivisione file di cui è stato eseguito il provisioning per le condivisioni file premium.

Selezionare **Crea** per completare la creazione della nuova condivisione. Si noti che se l'account di archiviazione si trova in una rete virtuale, non sarà possibile creare correttamente una condivisione file di Azure a meno che il client non si trovi anche nella rete virtuale. È anche possibile aggirare questa limitazione point-in-time utilizzando il cmdlet PowerShell di Azure.You can also work around this point-in-time limitation by using the Azure PowerShell `New-AzRmStorageShare` cmdlet.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
È possibile creare la condivisione file di Azure con il [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) cmdlet. I comandi di PowerShell seguenti `$resourceGroupName` presuppongono di aver impostato le variabili e come definito in precedenza nella sezione Creazione di un account di archiviazione con Azure PowerShell.The following PowerShell commands assume you have set the variables and `$storageAccountName` as defined above in the creating a storage account with Azure PowerShell section. 

> [!Important]  
> Per le condivisioni `-QuotaGiB` file premium, il parametro si riferisce alla dimensione di cui è stato eseguito il provisioning della condivisione file. La dimensione di cui è stato eseguito il provisioning della condivisione file è l'importo che ti verrà fatturato, indipendentemente dall'utilizzo. Le condivisioni file standard vengono fatturate in base all'utilizzo anziché alle dimensioni di cui è stato eseguito il provisioning.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 1024 | Out-Null
```

> [!Note]  
> Il nome della condivisione file deve essere composto solo da caratteri minuscoli. Per informazioni dettagliate sulla denominazione di condivisioni file e file, vedere [Denominazione e riferimento a condivisioni, directory, file e metadati](https://msdn.microsoft.com/library/azure/dn167011.aspx).

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)
Prima di poter creare una condivisione file di Azure con l'interfaccia della riga di comando di Azure, è necessario ottenere una chiave dell'account di archiviazione con cui autorizzare l'operazione di creazione della condivisione file. Questo può essere [`az storage account keys list`](/cli/azure/storage/account/keys) fatto con il comando:

```azurecli-interactive
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

Dopo aver creato la chiave dell'account di archiviazione, è possibile creare la condivisione file di Azure con il [`az storage share create`](/cli/azure/storage/share) comando. 

> [!Important]  
> Per le condivisioni `--quota` file premium, il parametro si riferisce alla dimensione di cui è stato eseguito il provisioning della condivisione file. La dimensione di cui è stato eseguito il provisioning della condivisione file è l'importo che ti verrà fatturato, indipendentemente dall'utilizzo. Le condivisioni file standard vengono fatturate in base all'utilizzo anziché alle dimensioni di cui è stato eseguito il provisioning.

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --output none
```

Questo comando avrà esito negativo se l'account di archiviazione è contenuto in una rete virtuale e il computer da cui si sta richiamando questo comando non fa parte della rete virtuale. È possibile aggirare questa limitazione di tempo usando `New-AzRmStorageShare` il cmdlet PowerShell di Azure come descritto in precedenza oppure eseguendo l'interfaccia della riga di comando di Azure da un computer che fa parte della rete virtuale, anche tramite una connessione VPN.

---

> [!Note]  
> Il nome della condivisione file deve essere composto solo da caratteri minuscoli. Per informazioni dettagliate sulla denominazione di condivisioni file e file, vedere [Denominazione e riferimento a condivisioni, directory, file e metadati](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="next-steps"></a>Passaggi successivi
- [Pianificare una distribuzione di File di Azure](storage-files-planning.md) o [Pianificare una distribuzione di Sincronizzazione file di Azure.](storage-sync-files-planning.md) 
- [Panoramica della rete](storage-files-networking-overview.md).
- Connettersi e montare una condivisione file su [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)e [Linux](storage-how-to-use-files-linux.md).