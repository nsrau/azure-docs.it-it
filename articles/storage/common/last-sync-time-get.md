---
title: Controllare la proprietà Ora ultima sincronizzazione per un account di archiviazione
titleSuffix: Azure Storage
description: Informazioni su come controllare la proprietà Ora ultima sincronizzazione per un account di archiviazione con replica geografica. La proprietà Ora ultima sincronizzazione indica l'ultima volta in cui tutte le scritture dall'area primaria sono state scritte correttamente nell'area secondaria.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 242700c05053aa9d07e3a561a21986c8451a46c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91612445"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>Controllare la proprietà Ora ultima sincronizzazione per un account di archiviazione

Quando si configura un account di archiviazione, è possibile specificare che i dati vengano copiati in un'area secondaria a centinaia di chilometri dall'area primaria. La replica geografica garantisce la durabilità dei dati in caso di un'interruzione significativa nell'area primaria, ad esempio nel caso di una calamità naturale. Se si abilita anche l'accesso in lettura all'area secondaria, i dati restano disponibili per le operazioni di lettura nel caso in cui l'area primaria non sia disponibile. È possibile progettare l'applicazione in modo che passi in modo trasparente alla lettura dei dati nell'area secondaria se l'area primaria non risponde.

L'archiviazione con ridondanza geografica e l'archiviazione con ridondanza geografica della zona consentono entrambe di replicare i dati in modo asincrono in un'area secondaria. Per l'accesso in lettura all'area secondaria, abilitare l'archiviazione con ridondanza geografica e accesso in lettura o l'archiviazione con ridondanza geografica della zona e accesso in lettura. Per altre informazioni sulle diverse opzioni di ridondanza offerte da Archiviazione di Azure, vedere [Ridondanza di Archiviazione di Azure](storage-redundancy.md).

Questo articolo descrive come controllare la proprietà **Ora ultima sincronizzazione** per l'account di archiviazione in modo da poter valutare eventuali discrepanze tra le aree primaria e secondaria.

## <a name="about-the-last-sync-time-property"></a>Informazioni sulla proprietà Ora ultima sincronizzazione

Poiché la replica geografica è asincrona, è possibile che nel momento in cui si verifica un'interruzione i dati scritti nell'area primaria non siano ancora stati scritti nell'area secondaria. La proprietà **Ora ultima sincronizzazione** indica l'ultima volta in cui i dati provenienti dall'area primaria sono stati scritti correttamente nell'area secondaria. Tutte le scritture eseguite nell'area primaria prima dell'ora dell'ultima sincronizzazione sono disponibili per la lettura dalla posizione secondaria. Le scritture eseguite nell'area primaria dopo l'ora dell'ultima sincronizzazione potrebbero non essere ancora disponibili per la lettura.

La proprietà **Ora ultima sincronizzazione** è un valore di data/ora GMT.

## <a name="get-the-last-sync-time-property"></a>Ottenere la proprietà Ora ultima sincronizzazione

Per recuperare il valore della proprietà **Ora ultima sincronizzazione**, è possibile usare PowerShell o l'interfaccia della riga di comando di Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per ottenere l'ora dell'ultima sincronizzazione per l'account di archiviazione con PowerShell, installare la versione 1.11.0 o successiva del modulo [AZ. storage](https://www.powershellgallery.com/packages/Az.Storage) . Controllare quindi la proprietà **GeoReplicationStats.LastSyncTime** dell'account di archiviazione. Ricordarsi di sostituire i valori segnaposto con valori personalizzati:

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per ottenere l'ora dell'ultima sincronizzazione dell'account di archiviazione con l'interfaccia della riga di comando di Azure, controllare la proprietà **geoReplicationStats.lastSyncTime** dell'account di archiviazione. Usare il parametro `--expand` per restituire i valori per le proprietà annidate in **geoReplicationStats**. Ricordarsi di sostituire i valori segnaposto con valori personalizzati:

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
- [Usare la ridondanza geografica per progettare applicazioni a disponibilità elevata](geo-redundant-design.md)
