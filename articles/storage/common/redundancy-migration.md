---
title: Modificare la modalità di replica di un account di archiviazioneChange how a storage account is replicated
titleSuffix: Azure Storage
description: Informazioni su come modificare la modalità di replica dei dati in un account di archiviazione esistente.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 5c37dbdc34138faab8adae6ad18252c18a75cad4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337083"
---
# <a name="change-how-a-storage-account-is-replicated"></a>Modificare la modalità di replica di un account di archiviazioneChange how a storage account is replicated

Archiviazione di Azure archivia sempre più copie dei dati in modo che siano protetti da eventi pianificati e non pianificati, inclusi errori hardware temporanei, interruzioni della rete o dell'alimentazione e disastri naturali di grandi dimensioni. La ridondanza garantisce che l'account di archiviazione soddisfi il contratto di servizio [(SLA) per Archiviazione di Azure](https://azure.microsoft.com/support/legal/sla/storage/) anche in caso di errori.

Archiviazione di Azure offre i tipi di replica seguenti:Azure Storage offers the following types of replication:

- Archiviazione con ridondanza locale (LRS)
- Archiviazione con ridondanza della zona (ZRS).
- Archiviazione con ridondanza geografica (GRS) o archiviazione con ridondanza geografica con accesso in lettura (RA-GRS)
- Archiviazione con ridondanza geografica o archiviazione con ridondanza di rete geografica (RA-G-RS) (anteprima)

Per una panoramica di ognuna di queste opzioni, vedere [Ridondanza](storage-redundancy.md)di Archiviazione di Azure.For an overview of each of these options, see Azure Storage redundancy .

## <a name="switch-between-types-of-replication"></a>Passare da un tipo di replica all'altro

È possibile passare un account di archiviazione da un tipo di replica a qualsiasi altro tipo, ma alcuni scenari sono più semplici di altri. Se si vuole aggiungere o rimuovere la replica geografica o l'accesso in lettura all'area secondaria, è possibile usare il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure per aggiornare l'impostazione di replica. Tuttavia, se si desidera modificare la modalità di replica dei dati nell'area primaria, passando da LRS a ORS o viceversa, è necessario eseguire una migrazione manuale.

Nella tabella seguente viene fornita una panoramica su come passare da ogni tipo di replica a un altro:

| Commutazione | ... a LRS | ... a GRS/RA-GRS | ... a RS | ... all'opzione G-RS/RA-G-RS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>... da LRS</b> | N/D | Usare il portale di Azure, PowerShell o l'interfaccia della riga di comando per modificare l'impostazione di replica<sup>1Use</sup> Azure portal, PowerShell, or CLI to change the replication setting 1 | Eseguire una migrazione manuale <br /><br />Richiedere una migrazione in tempo realeRequest a live migration | Eseguire una migrazione manuale <br /><br /> o <br /><br /> Passare prima a GRS/RA-GRS e quindi richiedere una migrazione in tempo reale<sup>1</sup> |
| <b>... da GRS/RA-GRS</b> | Usare il portale di Azure, PowerShell o l'interfaccia della riga di comando per modificare l'impostazione di replicaUse Azure portal, PowerShell, or CLI to change the replication setting | N/D | Eseguire una migrazione manuale <br /><br /> o <br /><br /> Passare prima a LRS e quindi richiedere una migrazione in tempo reale | Eseguire una migrazione manuale <br /><br /> Richiedere una migrazione in tempo realeRequest a live migration |
| <b>... da .RS</b> | Eseguire una migrazione manuale | Eseguire una migrazione manuale | N/D | Usare il portale di Azure, PowerShell o l'interfaccia della riga di comando per modificare l'impostazione di replica<sup>1Use</sup> Azure portal, PowerShell, or CLI to change the replication setting 1 |
| <b>... (ARR/ RA-G-RS)</b> | Eseguire una migrazione manuale | Eseguire una migrazione manuale | Usare il portale di Azure, PowerShell o l'interfaccia della riga di comando per modificare l'impostazione di replicaUse Azure portal, PowerShell, or CLI to change the replication setting | N/D |

<sup>1</sup> Incorre in un costo in uscita una tantera.

> [!CAUTION]
> Se è stato eseguito un [failover dell'account](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance) per l'account (RA-)GRS o (RA-)G-RS, è configurato per essere ridondante in locale nella nuova area primaria. La migrazione in tempo reale a .RS o G-RS per tali account LRS non è supportata. È necessario eseguire [la migrazione manuale](https://docs.microsoft.com/azure/storage/common/redundancy-migration#perform-a-manual-migration-to-zrs).

## <a name="change-the-replication-setting"></a>Modificare l'impostazione di replica

È possibile usare il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure per modificare l'impostazione di replica per un account di archiviazione, purché non si modifichi la modalità di replica dei dati nell'area primaria. Se si esegue la migrazione da LRS nell'area primaria a 'RS nell'area primaria o viceversa, è necessario eseguire una [migrazione manuale](#perform-a-manual-migration-to-zrs) o una [migrazione in tempo reale](#request-a-live-migration-to-zrs).

La modifica della modalità di replica dell'account di archiviazione non comporta tempi di inattività per le applicazioni.

# <a name="portal"></a>[Portale](#tab/portal)

Per modificare l'opzione di ridondanza per l'account di archiviazione nel portale di Azure, eseguire la procedura seguente:To change the redundancy option for your storage account in the Azure portal, follow these steps:

1. Passare all'account di archiviazione nel portale di Azure.
1. Selezionare l'impostazione **Configurazione.**
1. Aggiornare l'impostazione **di replica.**

![Screenshot che mostra come modificare l'opzione di replica nel portale](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[Powershell](#tab/powershell)

Per modificare l'opzione di ridondanza per l'account di archiviazione `-SkuName` con PowerShell, chiamare il comando [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) e specificare il parametro:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

Per modificare l'opzione di ridondanza per l'account di archiviazione `--sku` con l'interfaccia della riga di comando di Azure, chiamare il comando az storage account update e specificare il parametro:To change the redundancy option for your storage account with Azure CLI, call the az storage account [update](/cli/azure/storage/account#az-storage-account-update) command and specify the parameter:

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs"></a>Eseguire una migrazione manuale a

Se si vuole modificare la modalità di replica dei dati nell'account di archiviazione nell'area primaria, passando da LRS a ORS o viceversa, è possibile scegliere di eseguire una migrazione manuale. Una migrazione manuale offre maggiore flessibilità rispetto a una migrazione in tempo reale. È possibile controllare la temporizzazione di una migrazione manuale, quindi utilizzare questa opzione se è necessario completare la migrazione entro una determinata data.

Quando si esegue una migrazione manuale da LRS a 'RS', nell'area primaria o viceversa, l'account di archiviazione di destinazione può essere ridondante geografica e può anche essere configurato per l'accesso in lettura all'area secondaria. Ad esempio, è possibile eseguire la migrazione di un conto LRS a un conto G-RS o RA-G-RS in un unico passaggio.

Una migrazione manuale può comportare tempi di inattività dell'applicazione. Se l'applicazione richiede disponibilità elevata, Microsoft offre anche un'opzione di migrazione in tempo reale. Una migrazione in tempo reale è una migrazione sul posto senza tempi di inattività.

Con una migrazione manuale, si copiano i dati dall'account di archiviazione esistente in un nuovo account di archiviazione che usa il sistema di gestione delle funzionalità nell'area primaria. Per eseguire una migrazione manuale, è possibile utilizzare una delle seguenti opzioni:

- Copiare i dati usando uno strumento esistente, ad esempio AzCopy, una delle librerie client di Archiviazione di Azure o uno strumento di terze parti affidabile.
- Se si ha familiarità con Hadoop o HDInsight, è possibile collegare sia l'account di archiviazione di origine che l'account dell'account di archiviazione di destinazione al cluster. Quindi, parallelizzare il processo di copia dei dati con uno strumento, ad esempio DistCp.

## <a name="request-a-live-migration-to-zrs"></a>Richiedere una migrazione in tempo reale a .

Se è necessario eseguire la migrazione dell'account di archiviazione da LRS o GRS a RS nell'area primaria senza tempi di inattività dell'applicazione, è possibile richiedere una migrazione in tempo reale da Microsoft. Durante una migrazione in tempo reale, è possibile accedere ai dati nell'account di archiviazione e senza perdita di durabilità o disponibilità. Il servizio di archiviazione di Archiviazione di Azure viene mantenuto durante il processo di migrazione. Non vi è alcuna perdita di dati associata a una migrazione in tempo reale. Gli endpoint del servizio, le chiavi di accesso, le firme di accesso condiviso e altre opzioni dell'account rimangono invariati dopo la migrazione.

Il servizio di gestione delle informazioni supporta solo gli account di versione 2 generici, quindi assicurati di aggiornare l'account di archiviazione prima di inviare una richiesta per una migrazione in tempo reale a . Per altre informazioni, vedere [Eseguire l'aggiornamento a un account di archiviazione v2 generico.](storage-account-upgrade.md) Un account di archiviazione deve contenere dati di cui eseguire la migrazione tramite migrazione in tempo reale.

La migrazione in tempo reale è supportata solo per gli account di archiviazione che usano la replica con l'archiviazione con ridondanza locale o l'archiviazione con ridondanza geografica. Se l'account usa l'archiviazione con ridondanza geografica e accesso in lettura, è necessario innanzitutto modificare il tipo di replica dell'account in archiviazione con ridondanza locale o archiviazione con ridondanza geografica prima di procedere. Questo passaggio intermedio rimuove, prima della migrazione, l'endpoint secondario di sola lettura fornito dall'archiviazione con ridondanza geografica e accesso in lettura.

Sebbene Microsoft gestisca tempestivamente la richiesta di migrazione in tempo reale, non c'è alcuna garanzia per quanto riguarda il tempo necessario per il completamento di questo tipo di migrazione. Se è necessario eseguire la migrazione dei dati in un'archiviazione con ridondanza della zona entro una certa data, Microsoft consiglia di eseguire una migrazione manuale. In genere, maggiore è la quantità di dati nell'account, più tempo richiederà la migrazione dei dati.

È necessario eseguire una migrazione manuale se:You must perform a manual migration if:

- Si vuole eseguire la migrazione dei dati in un account di archiviazione di tipo Rr che si trova in un'area diversa dall'account di origine.
- L'account di archiviazione è un BLOB di pagine premium o un account BLOB di blocchi.
- Si desidera eseguire la migrazione dei dati da LRS a LRS, GRS o RA-GRS.
- L'account di archiviazione include i dati nel livello di archiviazione.

È possibile richiedere la migrazione in tempo reale tramite il [portale del supporto tecnico di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). Nel portale selezionare l'account di archiviazione da convertire in archiviazione con ridondanza della zona.

1. Seleziona **nuova richiesta di supporto**
2. Completare le **nozioni di base** con le informazioni relative all'account. Nella sezione **Assistenza** selezionare **Gestione Account di archiviazione** e la risorsa che si vuole convertire in archiviazione con ridondanza della zona.
3. Fare clic su **Avanti**.
4. Specificare i valori seguenti nella sezione **Problema**:
    - **Gravità**: lasciare il valore predefinito.
    - **Tipo di problema**: selezionare **Migrazione dei dati**.
    - **Categoria**: Selezionare **Migra a .**
    - **Titolo**: digitare un titolo descrittivo, ad esempio **migrazione di account con archiviazione con ridondanza della zona**.
    - **Dettagli**: Digitare ulteriori dettagli nella casella **Dettagli,** ad esempio, si desidera eseguire la \_ \_ migrazione a RS da [LRS, GRS] nell'area.
5. Fare clic su **Avanti**.
6. Verificare che le informazioni di contatto nel pannello **Informazioni contatto** siano corrette.
7. Selezionare **Crea**.

Un addetto del supporto tecnico contatterà l'utente e fornirà l'assistenza necessaria.

> [!NOTE]
> La migrazione in tempo reale non è attualmente supportata per le condivisioni file Premium.Live migration is not currently supported for premium file shares. Attualmente è supportata solo la copia o lo spostamento manuale dei dati.
>
> Gli account di archiviazione G-RS non supportano attualmente il livello di archiviazione. Per altre informazioni, vedere Archiviazione BLOB di Azure: livelli di [accesso a caldo, ad accesso a caldo e ad archivio.](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)
>
> I dischi gestiti sono disponibili solo per LRS e non possono essere migrati in . È possibile archiviare istantanee e immagini per i dischi gestiti SSD standard nell'archiviazione HDD standard e scegliere tra le [opzioni LRS e .RS.](https://azure.microsoft.com/pricing/details/managed-disks/) Per informazioni sull'integrazione con i set di disponibilità, vedere [Introduzione ai dischi gestiti](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets)di Azure.For information about integration with availability sets, see Introduction to Azure managed disks .

## <a name="switch-from-zrs-classic"></a>Passare da .RS Classic

> [!IMPORTANT]
> Microsoft imposterà come deprecati gli account della versione classica dell'archiviazione con ridondanza della zona e ne eseguirà la migrazione il 31 marzo 2021. Altri dettagli verranno inviati ai clienti della versione classica dell'archiviazione con ridondanza della zona prima di essere deprecati.
>
> Dopo che in genere l'opzione di ripristino di accesso a un determinato sistema diventa disponibile in una determinata area, i clienti non saranno più in grado di creare gli account classici di .R dal portale di Azure in tale area. L'uso di Microsoft PowerShell e dell'interfaccia della riga di comando di Azure per creare gli account della versione classica dell'archiviazione con ridondanza della zona sarà disponibile finché la versione classica dell'archiviazione con ridondanza della zona viene deprecata. Per informazioni sulla posizione in cui è disponibile il server di replica di Archiviazione di Azure, vedere [Ridondanza](storage-redundancy.md)di Archiviazione di Azure.For information about where 'RS' are available, see Azure Storage redundancy .

La versione classica dell'archiviazione con ridondanza della zona replica i dati in modo asincrono tra data center in una o due aree. I dati replicati potrebbero non essere disponibili a meno che Microsoft non avvii il failover all'area secondaria. Gli account della versione classica dell'archiviazione con ridondanza della zona non possono essere convertiti in o da archiviazione con ridondanza locale, archiviazione con ridondanza geografica o archiviazione con ridondanza geografica e accesso in lettura. Gli account della versione classica dell'archiviazione con ridondanza della zona non supportano inoltre le metriche o la registrazione.

La versione classica dell'archiviazione con ridondanza della zona è disponibile solo per i **BLOB in blocchi** negli account di archiviazione di tipo Utilizzo generico V1 (GPv1). Per altre informazioni sugli account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](storage-account-overview.md).

Per eseguire manualmente la migrazione dei dati dell'account di Archiviazione di BitR in o da un account LRS, GRS, RA-GRS o Classic, usare uno degli strumenti seguenti: AzCopy, Azure Storage Explorer, PowerShell o l'interfaccia della riga di comando di Azure.To manually migrate 'RS account data to or from an LRS, GRS, RA-GRS, or 'RS Classic account, use one of the following tools: AzCopy, Azure Storage Explorer, PowerShell or Azure CLI. È anche possibile creare una soluzione di migrazione personalizzata con una delle librerie client di Archiviazione di Azure.

È anche possibile aggiornare l'account di archiviazione classico di RS a RS usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure nelle aree in cui è disponibile l'opzione di archiviazione.

# <a name="portal"></a>[Portale](#tab/portal)

Per eseguire l'aggiornamento a . **Configuration** **Upgrade**

![Aggiornamento di .R Classic a .RS nel portale](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[Powershell](#tab/powershell)

Per eseguire l'aggiornamento a , tramite PowerShell, chiamare il comando seguente:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

Per eseguire l'aggiornamento a UN'interfaccia utente con l'interfaccia della riga di comando di Azure, chiamare il comando seguente:To upgrade to 'RS using Azure CLI, call the following command:

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>Costi associati alla modifica della modalità di replica dei dati

I costi associati alla modifica della modalità di replica dei dati dipendono dal percorso di conversione. Ordinando dal meno al più costoso, le offerte di ridondanza di Archiviazione di Azure includono LRS, sRS, GRS, RA-GRS, G-RS e RA-G-RS.

Ad esempio, il passaggio *da* LRS a qualsiasi altro tipo di replica comporterà costi aggiuntivi perché ci si sta spostando a un livello di ridondanza più sofisticato. La migrazione *a* GRS o RA-GRS comporterà un addebito della larghezza di banda in uscita perché i dati (nell'area primaria) vengono replicati nell'area secondaria remota. Questo costo è un costo una tantum al momento della configurazione iniziale. Dopo la copia dei dati, non sono previsti ulteriori costi di migrazione. Per informazioni dettagliate sui costi addebitati per la larghezza di banda, vedere la [pagina dei prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Se si esegue la migrazione dell'account di archiviazione da GRS a LRS, non sono previsti costi aggiuntivi, ma i dati replicati vengono eliminati dalla posizione secondaria.

> [!IMPORTANT]
> Se si esegue la migrazione dell'account di archiviazione da RA-GRS a GRS o LRS, tale account viene fatturato come RA-GRS per altri 30 giorni oltre la data di conversione.

## <a name="see-also"></a>Vedere anche

- [Ridondanza di Archiviazione di AzureAzure Storage redundancy](storage-redundancy.md)
- [Controllare la proprietà Ora ultima sincronizzazione per un account di archiviazioneCheck the Last Sync Time property for a storage account](last-sync-time-get.md)
- [Progettazione di applicazioni a disponibilità elevata utilizzando l'archiviazione con ridondanza geografica ad accesso in lettura](storage-designing-ha-apps-with-ragrs.md)
