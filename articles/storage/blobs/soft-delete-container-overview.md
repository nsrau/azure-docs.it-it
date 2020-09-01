---
title: Eliminazione temporanea per i contenitori (anteprima)
titleSuffix: Azure Storage
description: L'eliminazione temporanea per i contenitori (anteprima) consente di proteggere i dati in modo da poter ripristinare più facilmente i dati quando vengono erroneamente modificati o eliminati da un'applicazione o da un altro utente dell'account di archiviazione.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/25/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: f41caf61ac23b05ba0ff95d785be2e7449adf75d
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89228631"
---
# <a name="soft-delete-for-containers-preview"></a>Eliminazione temporanea per i contenitori (anteprima)

L'eliminazione temporanea per i contenitori (anteprima) impedisce che i dati vengano accidentalmente o modificati o eliminati in modo errato. Quando l'eliminazione temporanea del contenitore è abilitata per un account di archiviazione, qualsiasi contenitore eliminato e il relativo contenuto vengono conservati in archiviazione di Azure per il periodo specificato. Durante il periodo di memorizzazione è possibile ripristinare i contenitori eliminati in precedenza e tutti i BLOB in essi contenuti.

Per la protezione end-to-end per i dati BLOB, Microsoft consiglia di abilitare le funzionalità di protezione dei dati seguenti:

- Eliminazione temporanea del contenitore, per evitare l'eliminazione o la sovrascrittura accidentale di un contenitore. Per informazioni su come abilitare l'eliminazione temporanea del contenitore, vedere [Enable and Manage soft delete for Containers](soft-delete-container-enable.md).
- Eliminazione temporanea del BLOB, per evitare l'eliminazione o la sovrascrittura accidentale di un singolo BLOB. Per informazioni su come abilitare l'eliminazione temporanea del BLOB, vedere [eliminazione temporanea per i BLOB](soft-delete-blob-overview.md).
- Controllo delle versioni dei BLOB, per gestire automaticamente le versioni precedenti di un BLOB. Quando è abilitata la funzionalità di controllo delle versioni dei BLOB, è possibile ripristinare una versione precedente di un BLOB per ripristinare i dati se vengono erroneamente modificati o eliminati. Per informazioni su come abilitare il controllo delle versioni dei BLOB, vedere [abilitare e gestire il controllo delle versioni dei BLOB](versioning-enable.md).

> [!WARNING]
> L'eliminazione di un account di archiviazione non può essere annullata. L'eliminazione temporanea non protegge dall'eliminazione di un account di archiviazione. Per evitare l'eliminazione accidentale di un account di archiviazione, configurare un blocco **CannotDelete** sulla risorsa dell'account di archiviazione. Per altre informazioni sul blocco delle risorse di Azure, vedere [bloccare le risorse per impedire modifiche impreviste](../../azure-resource-manager/management/lock-resources.md).

## <a name="how-container-soft-delete-works"></a>Funzionamento dell'eliminazione temporanea del contenitore

Quando si Abilita l'eliminazione temporanea del contenitore, è possibile specificare un periodo di conservazione per i contenitori eliminati tra 1 e 365 giorni. Il periodo di memorizzazione predefinito è 7 giorni. Durante il periodo di memorizzazione, è possibile ripristinare un contenitore eliminato chiamando l'operazione di **annullamento dell'eliminazione del contenitore** .

Quando si ripristina un contenitore, è possibile ripristinarne il nome originale se tale nome non è stato riutilizzato. Se è stato usato il nome del contenitore originale, è possibile ripristinare il contenitore con un nuovo nome.

Una volta scaduto il periodo di conservazione, il contenitore viene eliminato definitivamente dall'archiviazione di Azure e non può essere recuperato. Il clock viene avviato nel periodo di conservazione nel punto in cui il contenitore viene eliminato. È possibile modificare il periodo di memorizzazione in qualsiasi momento, ma tenere presente che un periodo di conservazione aggiornato si applica solo ai contenitori appena eliminati. I contenitori eliminati in precedenza verranno eliminati definitivamente in base al periodo di memorizzazione che era attivo al momento dell'eliminazione del contenitore.

La disabilitazione dell'eliminazione temporanea del contenitore non comporta l'eliminazione permanente di contenitori che in precedenza sono stati eliminati temporaneamente. Eventuali contenitori eliminati temporaneamente verranno eliminati definitivamente alla scadenza del periodo di memorizzazione che era attivo al momento dell'eliminazione del contenitore.

## <a name="about-the-preview"></a>Informazioni sulla versione di anteprima

L'eliminazione temporanea del contenitore è disponibile in anteprima nelle aree seguenti:

- Francia centrale
- Canada orientale
- Canada centrale

> [!IMPORTANT]
> L'anteprima dell'eliminazione temporanea del contenitore è destinata solo all'uso non in produzione. I contratti di servizio (SLA) di produzione non sono al momento disponibili.

La versione 2019-12-12 e successive dell'API REST di archiviazione di Azure supporta l'eliminazione temporanea del contenitore.

### <a name="storage-account-support"></a>Supporto dell'account di archiviazione

L'eliminazione temporanea del contenitore è disponibile per i tipi di account di archiviazione seguenti:

- Account di archiviazione per utilizzo generico V2
- Bloccare gli account di archiviazione BLOB
- Account di archiviazione BLOB

Se l'account di archiviazione è un account per utilizzo generico V1, usare il portale di Azure per eseguire l'aggiornamento a un account per utilizzo generico V2. Per altre informazioni sugli account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](../common/storage-account-overview.md).

Sono supportati anche gli account di archiviazione con uno spazio dei nomi gerarchico abilitato per l'uso con Azure Data Lake Storage Gen2.

### <a name="register-for-the-preview"></a>Registrarsi per l'anteprima

Per iscriversi all'anteprima per l'eliminazione temporanea del contenitore, usare PowerShell o l'interfaccia della riga di comando di Azure per inviare una richiesta di registrazione della funzionalità con la sottoscrizione. Dopo che la richiesta è stata approvata, è possibile abilitare l'eliminazione temporanea del contenitore con tutti gli account di archiviazione BLOB in blocchi, archiviazione BLOB o Premium per utilizzo generico nuovi o esistenti.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per eseguire la registrazione con PowerShell, chiamare il comando [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) .

```powershell
# Register for container soft delete (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName ContainerSoftDelete

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per eseguire la registrazione con l'interfaccia della riga di comando di Azure, chiamare il comando [AZ feature Register](/cli/azure/feature#az-feature-register) .

```azurecli
az feature register --namespace Microsoft.Storage --name ContainerSoftDelete
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-the-status-of-your-registration"></a>Verificare lo stato della registrazione

Per verificare lo stato della registrazione, usare PowerShell o l'interfaccia della riga di comando di Azure.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per verificare lo stato della registrazione con PowerShell, chiamare il comando [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) .

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName ContainerSoftDelete
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per verificare lo stato della registrazione con l'interfaccia della riga di comando di Azure, chiamare il comando [AZ feature](/cli/azure/feature#az-feature-show) .

```azurecli
az feature show --namespace Microsoft.Storage --name ContainerSoftDelete
```

---

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

Non sono previsti costi aggiuntivi per abilitare l'eliminazione temporanea del contenitore. I dati nei contenitori eliminati temporaneamente vengono fatturati alla stessa tariffa dei dati attivi.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare l'eliminazione temporanea del contenitore](soft-delete-container-enable.md)
- [Eliminazione temporanea per i BLOB](soft-delete-blob-overview.md)
- [Controllo delle versioni dei BLOB](versioning-overview.md)
