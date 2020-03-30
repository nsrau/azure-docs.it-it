---
title: Controllare la proprietà Ora ultima sincronizzazione per un account di archiviazioneCheck the Last Sync Time property for a storage account
titleSuffix: Azure Storage
description: Informazioni su come controllare la proprietà **Ultima sincronizzazione** per un account di archiviazione con replica geografica. La proprietà **Ora ultima sincronizzazione** indica l'ultima volta in cui tutte le scritture dall'area primaria sono state scritte correttamente nell'area secondaria.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/16/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 3a406ce6db060b9ff5be7bcadecb6c7ff7e65a1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77165488"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>Controllare la proprietà Ora ultima sincronizzazione per un account di archiviazioneCheck the Last Sync Time property for a storage account

Quando si configura un account di archiviazione, è possibile specificare che i dati vengano copiati in un'area secondaria a centinaia di chilometri dall'area primaria. La replica geografica offre durabilità per i dati in caso di un'interruzione significativa nell'area primaria, ad esempio un'emergenza naturale. Se si abilita inoltre l'accesso in lettura all'area secondaria, i dati rimangono disponibili per le operazioni di lettura se l'area primaria non è più disponibile. È possibile progettare l'applicazione in modo che passi senza problemi alla lettura dall'area secondaria se l'area primaria non risponde.

L'archiviazione con ridondanza geografica (GRS) e l'archiviazione con ridondanza geografica (G-RS) (anteprima) replicano entrambi i dati in modo asincrono in un'area secondaria. Per l'accesso in lettura all'area secondaria, abilitare l'archiviazione con ridondanza geografica di accesso in lettura (RA-GRS) o l'archiviazione con ridondanza di zona geografica ad accesso in lettura (RA-G-RS). Per altre informazioni sulle varie opzioni per la ridondanza offerte da Archiviazione di Azure, vedere [Ridondanza](storage-redundancy.md)di Archiviazione di Azure.For more information about the various options for redundancy offered by Azure Storage, see Azure Storage redundancy .

In questo articolo viene descritto come controllare la proprietà **Ora ultima sincronizzazione** per l'account di archiviazione in modo da poter valutare qualsiasi discrepanza tra le aree primaria e secondaria.

## <a name="about-the-last-sync-time-property"></a>Informazioni sulla proprietà Ora ultima sincronizzazione

Poiché la replica geografica è asincrona, è possibile che i dati scritti nell'area primaria non siano ancora stati scritti nell'area secondaria al momento in cui si verifica un'interruzione. La proprietà **Ora ultima sincronizzazione** indica l'ultima volta che i dati dell'area primaria sono stati scritti correttamente nell'area secondaria. Tutte le scritture eseguite nell'area primaria prima dell'ora dell'ultima sincronizzazione sono disponibili per essere lette dalla posizione secondaria. Le scritture effettuate nell'area primaria dopo l'ultima proprietà dell'ora di sincronizzazione possono essere ancora disponibili per le letture.

La proprietà **Ora ultima sincronizzazione** è un valore di data/ora GMT.

## <a name="get-the-last-sync-time-property"></a>Ottenere la proprietà Ora ultima sincronizzazioneGet the Last Sync Time property

È possibile usare PowerShell o l'interfaccia della riga di comando di Azure per recuperare il valore della proprietà **Ora ultima sincronizzazione.**

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Per ottenere l'ora dell'ultima sincronizzazione per l'account di archiviazione con PowerShell, installare un modulo di anteprima di Archiviazione di Azure che supporti il recupero delle statistiche di replica geografica. Per esempio:

```powershell
Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
```

Controllare quindi la proprietà **GeoReplicationStats.LastSyncTime** dell'account di archiviazione. Ricordarsi di sostituire i valori segnaposto con valori personalizzati:

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

Per ottenere l'ora dell'ultima sincronizzazione per l'account di archiviazione con l'interfaccia della riga di comando di Azure, controllare la proprietà **geoReplicationStats.lastSyncTime** dell'account di archiviazione. Utilizzare `--expand` il parametro per restituire i valori per le proprietà annidate in **geoReplicationStats**. Ricordarsi di sostituire i valori segnaposto con valori personalizzati:

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

- [Ridondanza di Archiviazione di AzureAzure Storage redundancy](storage-redundancy.md)
- [Modificare l'opzione di ridondanza per un account di archiviazioneChange the redundancy option for a storage account](redundancy-migration.md)
- [Progettazione di applicazioni a disponibilità elevata utilizzando l'archiviazione con ridondanza geografica ad accesso in lettura](storage-designing-ha-apps-with-ragrs.md)