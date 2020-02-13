---
title: Controllare la proprietà ora ultima sincronizzazione per un account di archiviazione
titleSuffix: Azure Storage
description: Informazioni su come controllare la proprietà dell' **ora dell'ultima sincronizzazione** per un account di archiviazione con replica geografica. La proprietà **ultima sincronizzazione ora** indica l'ultima volta in cui tutte le Scritture dall'area primaria sono state scritte correttamente nell'area secondaria.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/16/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 3a406ce6db060b9ff5be7bcadecb6c7ff7e65a1f
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165488"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>Controllare la proprietà ora ultima sincronizzazione per un account di archiviazione

Quando si configura un account di archiviazione, è possibile specificare che i dati vengano copiati in un'area secondaria a centinaia di chilometri dall'area primaria. La replica geografica offre durabilità per i dati in caso di un'interruzione significativa nell'area primaria, ad esempio una calamità naturale. Se si abilita anche l'accesso in lettura all'area secondaria, i dati restano disponibili per le operazioni di lettura se l'area primaria non è più disponibile. È possibile progettare l'applicazione in modo da passare facilmente alla lettura dall'area secondaria se l'area primaria non risponde.

Archiviazione con ridondanza geografica (GRS) e archiviazione con ridondanza della zona geografica (GZRS) (anteprima) consentono di replicare i dati in modo asincrono in un'area secondaria. Per l'accesso in lettura all'area secondaria, abilitare l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) o l'archiviazione con ridondanza geografica e accesso in lettura (RA-GZRS). Per altre informazioni sulle diverse opzioni di ridondanza offerte da archiviazione di Azure, vedere [ridondanza di archiviazione di Azure](storage-redundancy.md).

Questo articolo descrive come controllare la proprietà **ora ultima sincronizzazione** per l'account di archiviazione in modo da poter valutare eventuali discrepanze tra le aree primarie e secondarie.

## <a name="about-the-last-sync-time-property"></a>Informazioni sulla proprietà ora ultima sincronizzazione

Poiché la replica geografica è asincrona, è possibile che i dati scritti nell'area primaria non siano ancora stati scritti nell'area secondaria nel momento in cui si verifica un'interruzione. La proprietà **ultima sincronizzazione ora** indica l'ultima volta in cui i dati dell'area primaria sono stati scritti correttamente nell'area secondaria. Tutte le scritture effettuate nell'area primaria prima dell'ora dell'ultima sincronizzazione sono disponibili per la lettura dalla posizione secondaria. Le scritture effettuate nell'area primaria dopo la proprietà dell'ora dell'ultima sincronizzazione possono essere ancora disponibili per le letture.

La proprietà **ora ultima sincronizzazione** è un valore di data/ora GMT.

## <a name="get-the-last-sync-time-property"></a>Ottenere la proprietà dell'ora dell'ultima sincronizzazione

È possibile usare PowerShell o l'interfaccia della riga di comando di Azure per recuperare il valore della proprietà **ora ultima sincronizzazione** .

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Per ottenere l'ora dell'ultima sincronizzazione per l'account di archiviazione con PowerShell, installare un modulo di anteprima di archiviazione di Azure che supporta il recupero delle statistiche di replica geografica. Per esempio:

```powershell
Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
```

Quindi controllare la proprietà **GeoReplicationStats. LastSyncTime** dell'account di archiviazione. Ricordarsi di sostituire i valori segnaposto con i propri valori:

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per ottenere l'ora dell'ultima sincronizzazione dell'account di archiviazione con l'interfaccia della riga di comando di Azure, controllare la proprietà **geoReplicationStats. LastSyncTime** dell'account di archiviazione. Usare il parametro `--expand` per restituire i valori per le proprietà nidificate in **geoReplicationStats**. Ricordarsi di sostituire i valori segnaposto con i propri valori:

```azurecli-interactive
$lastSyncTime=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --expand geoReplicationStats \
    --query geoReplicationStats.lastSyncTime \
    --output tsv)
```

---

## <a name="see-also"></a>Vedere anche

- [Azure Storage redundancy](storage-redundancy.md) (Ridondanza di Archiviazione di Azure)
- [Modificare l'opzione di ridondanza per un account di archiviazione](redundancy-migration.md)
- [Progettazione di applicazioni a disponibilità elevata tramite l'archiviazione con ridondanza geografica e accesso in lettura](storage-designing-ha-apps-with-ragrs.md)