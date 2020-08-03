---
title: Panoramica della replica di oggetti (anteprima)
titleSuffix: Azure Storage
description: La replica di oggetti (anteprima) copia in modo asincrono i BLOB in blocchi tra un account di archiviazione di origine e uno di destinazione. Usare la replica di oggetti per ridurre al minimo la latenza sulle richieste di lettura, per aumentare l'efficienza per i carichi di lavoro di calcolo, per ottimizzare la distribuzione dei dati e per ridurre al minimo i costi.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 097b3c71b01a8ad0e930d7aa0d7be46a1e835e1a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87495166"
---
# <a name="object-replication-for-block-blobs-preview"></a>Replica di oggetti per i BLOB in blocchi (anteprima)

La replica di oggetti (anteprima) copia in modo asincrono i BLOB in blocchi tra un account di archiviazione di origine e uno di destinazione. Alcuni scenari supportati dalla replica di oggetti includono:

- **Riduzione al minimo della latenza.** La replica di oggetti può ridurre la latenza per le richieste di lettura consentendo ai client di usare i dati di un'area con prossimità fisica più stretta.
- **Aumento dell'efficienza per i carichi di lavoro di calcolo.** Con la replica di oggetti, i carichi di lavoro di calcolo possono elaborare gli stessi set di BLOB in blocchi in aree diverse.
- **Ottimizzazione della distribuzione dei dati.** È possibile elaborare o analizzare i dati in un'unica posizione e quindi replicare solo i risultati in aree aggiuntive.
- **Ottimizzazione dei costi.** Una volta che i dati sono stati replicati, è possibile ridurre i costi spostandoli nel livello archivio usando i criteri di gestione del ciclo di vita.

Il diagramma seguente illustra in che modo la replica di oggetti replica i BLOB in blocchi da un account di archiviazione di origine in un'area ad account di destinazione in due aree diverse.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="Diagramma che illustra il funzionamento della replica di oggetti":::

Per informazioni su come configurare la replica di oggetti, vedere [Configurare la replica di oggetti (anteprima)](object-replication-configure.md).

## <a name="object-replication-policies-and-rules"></a>Regole e criteri di replica di oggetti

Quando si configura la replica di oggetti, si crea un criterio di replica che specifica l'account di archiviazione di origine e l'account di destinazione. Un criterio di replica include una o più regole che specificano un contenitore di origine e un contenitore di destinazione e indicano i BLOB in blocchi del contenitore di origine che verranno replicati.

Dopo aver configurato la replica di oggetti, Archiviazione di Azure controlla periodicamente il feed di modifiche per l'account di origine e replica in modo asincrono le operazioni di scrittura o eliminazione nell'account di destinazione. La latenza di replica dipende dalla dimensione del BLOB in blocchi da replicare.

> [!IMPORTANT]
> Poiché i dati BLOB in blocchi vengono replicati in modo asincrono, l'account di origine e l'account di destinazione non sono immediatamente sincronizzati. Al momento non esiste alcun contratto di servizio per il tempo necessario per replicare i dati nell'account di destinazione.

### <a name="replications-policies"></a>Criteri di replica

Quando si configura la replica di oggetti, viene creato un criterio di replica sia nell'account di origine che nell'account di destinazione tramite il provider di risorse di Archiviazione di Azure. Il criterio di replica è identificato da un ID criterio. Il criterio negli account di origine e di destinazione deve avere lo stesso ID criterio per poter eseguire la replica.

Un account di archiviazione può fungere da account di origine per un massimo di due account di destinazione. E un account di destinazione non può avere più di due account di origine. Gli account di origine e di destinazione possono trovarsi in aree diverse. È possibile configurare criteri di replica distinti per replicare i dati in ogni account di destinazione.

### <a name="replication-rules"></a>Regole di replica

Le regole di replica specificano in che modo Archiviazione di Azure replica i BLOB da un contenitore di origine a un contenitore di destinazione. È possibile specificare fino a 10 regole di replica per ogni criterio di replica. Ogni regola definisce un singolo contenitore di origine e di destinazione e ogni contenitore di origine e di destinazione può essere usato in una sola regola.

Quando si crea una regola di replica, per impostazione predefinita vengono copiati solo i nuovi BLOB in blocchi aggiunti successivamente al contenitore di origine. È anche possibile specificare che vengano copiati sia i BLOB in blocchi nuovi che quelli esistenti, oppure è possibile definire un ambito di copia personalizzato che copia i BLOB in blocchi creati a partire da un determinato momento.

È anche possibile specificare uno o più filtri come parte di una regola di replica per filtrare i BLOB in blocchi per prefisso. Quando si specifica un prefisso, solo i BLOB corrispondenti al prefisso nel contenitore di origine verranno copiati nel contenitore di destinazione.

Devono esistere sia i contenitori di origine che di destinazione prima di poterli specificare in una regola. Dopo aver creato i criteri di replica, il contenitore di destinazione diventa di sola lettura. Qualsiasi tentativo di scrittura nel contenitore di destinazione non riesce e viene restituito il codice errore 409 (conflitto). Tuttavia, è possibile chiamare l'operazione di [impostazione del livello BLOB](/rest/api/storageservices/set-blob-tier) su un BLOB nel contenitore di destinazione per spostarla nel livello archivio. Per altre informazioni sul livello archivio, vedere [archiviazione BLOB di Azure: livelli di accesso ad accesso frequente, ad accesso sporadico e archivio](storage-blob-storage-tiers.md#archive-access-tier).

## <a name="about-the-preview"></a>Informazioni sulla versione di anteprima

La replica di oggetti è supportata solo per gli account di archiviazione per uso generico v2. La replica di oggetti è attualmente disponibile nelle aree seguenti in anteprima:

- Francia centrale
- Canada orientale
- Canada centrale
- Stati Uniti orientali 2
- Stati Uniti centrali

Sia gli account di origine che di destinazione devono trovarsi in una di queste aree per poter usare la replica di oggetti. Gli account possono trovarsi in due aree diverse.

Durante l'anteprima, non sono previsti costi aggiuntivi associati alla replica di dati tra account di archiviazione.

> [!IMPORTANT]
> La versione di anteprima della replica di oggetti è destinata solo all'uso in ambienti non di produzione. I contratti di servizio (SLA) di produzione non sono al momento disponibili.

### <a name="prerequisites-for-object-replication"></a>Prerequisiti per la replica di oggetti

Per la replica di oggetti è necessario che siano abilitate le funzionalità di Archiviazione di Azure seguenti: 
- [Feed delle modifiche](storage-blob-change-feed.md)
- [Controllo delle versioni](versioning-overview.md)

Prima di configurare la replica di oggetti, abilitare i relativi prerequisiti. Il feed di modifiche deve essere abilitato nell'account di origine e il controllo delle versioni dei BLOB deve essere abilitato sia nell'account di origine che in quello di destinazione. Per altre informazioni su come abilitare queste funzionalità, vedere gli articoli seguenti:

- [Abilitare e disabilitare il feed di modifiche](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Abilitare e gestire il controllo delle versioni dei BLOB](versioning-enable.md)

Assicurarsi di eseguire la registrazione per le anteprime del feed di modifiche e del controllo delle versioni dei BLOB prima di abilitarli.

L'abilitazione del feed di modifiche e del controllo delle versioni dei BLOB può comportare costi aggiuntivi. Per informazioni più dettagliate, fare riferimento alla pagina [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

### <a name="register-for-the-preview"></a>Registrarsi per l'anteprima

È possibile eseguire la registrazione per l'anteprima della replica di oggetti usando PowerShell o l'interfaccia della riga di comando di Azure. Se non è già stato fatto, assicurarsi di registrarsi anche per le anteprime del feed di modifiche e del controllo delle versioni dei BLOB.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per eseguire la registrazione per l'anteprima con PowerShell, usare i comandi seguenti:

```powershell
# Register for the object replication preview
Register-AzProviderFeature -FeatureName AllowObjectReplication -ProviderNamespace Microsoft.Storage

# Register for change feed (preview)
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage

# Register for blob versioning (preview)
Register-AzProviderFeature -FeatureName Versioning -ProviderNamespace Microsoft.Storage

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per eseguire la registrazione per l'anteprima con l'interfaccia della riga di comando di Azure, usare i comandi seguenti:

```azurecli
az feature register --namespace Microsoft.Storage --name AllowObjectReplication
az feature register --namespace Microsoft.Storage --name Changefeed
az feature register --namespace Microsoft.Storage --name Versioning
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-registration-status"></a>Controllare lo stato della registrazione

È possibile controllare lo stato delle richieste di registrazione usando PowerShell o l'interfaccia della riga di comando di Azure.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per controllare lo stato delle richieste di registrazione usando PowerShell, eseguire i comandi seguenti:

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowObjectReplication

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Changefeed

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per controllare lo stato delle richieste di registrazione usando l'interfaccia della riga di comando di Azure, eseguire i comandi seguenti:

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowObjectReplication')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Changefeed')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Versioning')].{Name:name,State:properties.state}"
```

---

## <a name="ask-questions-or-provide-feedback"></a>Porre domande o inviare commenti

Per porre domande sull'anteprima della replica di oggetti o per inviare commenti, contattare Microsoft all'indirizzo AzureStorageFeedback@microsoft.com. Le idee e i suggerimenti su Archiviazione di Azure sono sempre molto apprezzati nel [forum dei commenti su Archiviazione di Azure](https://feedback.azure.com/forums/217298-storage).

## <a name="next-steps"></a>Passaggi successivi

- [Configurare la replica di oggetti (anteprima)](object-replication-configure.md)
- [Supporto del feed di modifiche in Archiviazione BLOB di Azure (anteprima)](storage-blob-change-feed.md)
- [Abilitare e gestire il controllo delle versioni dei BLOB](versioning-enable.md)
