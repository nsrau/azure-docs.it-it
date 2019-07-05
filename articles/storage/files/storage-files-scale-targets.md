---
title: Obiettivi di ridimensionamento e prestazioni di File di Azure | Microsoft Docs
description: Informazioni sugli obiettivi di scalabilità e prestazioni di File di Azure, incluse la capacità, la velocità di richiesta e la larghezza di banda in entrata e in uscita.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 5/5/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 91ec65e17b77ccb3864fce45e30729ff420a48b6
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542664"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Obiettivi di scalabilità e prestazioni per File di Azure

[File di Azure](storage-files-introduction.md) offre condivisioni file completamente gestite nel cloud, accessibili tramite il protocollo SMB standard di settore. Questo articolo descrive gli obiettivi di scalabilità e prestazioni per File di Azure e Sincronizzazione file di Azure.

Gli obiettivi di scalabilità e prestazioni elencati di seguito sono di fascia alta, ma possono dipendere da altre variabili nella distribuzione. Ad esempio, la velocità effettiva per un file potrebbe essere limitata anche dalla larghezza di banda di rete disponibile, non solo dai server che ospitano il servizio File di Azure. È consigliabile eseguire il test del criterio di utilizzo per determinare se la scalabilità e le prestazioni di File di Azure soddisfano i requisiti. Microsoft è impegnata ad aumentare i limiti gradualmente. Fornire commenti e suggerimenti sui limiti che si desidera vengano incrementati nella sezione dedicata di seguito o in [UserVoice per File di Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).

## <a name="azure-storage-account-scale-targets"></a>Obiettivi di scalabilità per gli account di archiviazione di Azure

La risorsa padre per una condivisione file di Azure è un account di archiviazione di Azure. Un account di archiviazione rappresenta un pool di archiviazione in Azure che può essere usato da più servizi di archiviazione, incluso File di Azure, per archiviare i dati. Altri servizi che archiviano i dati negli account di archiviazione sono archiviazione BLOB di Azure, archiviazione code di Azure e archiviazione tabelle di Azure. Le destinazioni seguenti si applicano a tutti i servizi di archiviazione che archiviano i dati in un account di archiviazione:

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Utilizzo di account di archiviazione generico da altri servizi di archiviazione interessa le condivisioni file di Azure nell'account di archiviazione. Ad esempio, se si raggiunge la capacità massima dell'account di archiviazione con Archiviazione BLOB di Azure, non sarà più possibile creare nuovi file nella condivisione file di Azure, anche se questa non ha ancora raggiunto la dimensione massima.

## <a name="azure-files-scale-targets"></a>Obiettivi di scalabilità di File di Azure

Esistono tre categorie di limitazioni da considerare per i file di Azure: account di archiviazione, condivisioni e file.

Ad esempio: Con le condivisioni file premium, una singola condivisione può raggiungere 100.000 IOPS e un singolo file possono aumentare fino a 5.000 IOPS. Pertanto, se si dispone di tre file in una condivisione, il numero massimo di IOPS è possibile ottenere da tale condivisione è 15.000.

### <a name="standard-storage-account-limits"></a>Limiti dell'account di archiviazione standard

Vedere le [obiettivi di scalabilità di account di archiviazione di Azure](#azure-storage-account-scale-targets) sezione per questi limiti.

### <a name="premium-filestorage-account-limits"></a>Limiti dell'account FileStorage Premium

[!INCLUDE [azure-storage-limits-filestorage](../../../includes/azure-storage-limits-filestorage.md)]

> [!IMPORTANT]
> Limiti dell'account di archiviazione si applicano a tutte le condivisioni. Scalabilità per il numero massimo per gli account FileStorage solo è realizzabile se è presente solo una condivisione per ogni account FileStorage.

### <a name="file-share-and-file-scale-targets"></a>Obiettivi di scalabilità di file e condivisione file

> [!NOTE]
> Le condivisioni di file standard di dimensioni superiori a 5 TiB sono disponibili in anteprima e presentano alcune limitazioni.
> Per un elenco di limitazioni e per eseguire l'onboarding per l'anteprima di queste dimensioni di condivisione file più grandi, vedere la [condivisioni di file Standard](storage-files-planning.md#standard-file-shares) sezione della Guida alla pianificazione.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Obiettivi di scalabilità di Sincronizzazione file di Azure

Sincronizzazione file di Azure è stato progettato per supportare un utilizzo senza limiti, cosa però non sempre possibile. La tabella seguente indica i limiti dei test Microsoft e anche le destinazioni con limiti rigidi:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Metriche delle prestazioni di Sincronizzazione file di Azure

Poiché l'agente Sincronizzazione file di Azure viene eseguito su un computer Windows Server che si connette alle condivisioni file di Azure, le prestazioni di sincronizzazione effettive dipendono da una serie di fattori dell'infrastruttura: Windows Server e la configurazione dei dischi sottostanti, larghezza di banda di rete tra il server e l'archiviazione di Azure, dimensioni del file, dimensione totale di set di dati e l'attività nel set di dati. Poiché Sincronizzazione file di Azure opera a livello di file, le caratteristiche in termini di prestazioni di una soluzione basata su Sincronizzazione file di Azure possono essere misurate meglio in base al numero di oggetti (file e directory) elaborati al secondo.

Per Sincronizzazione file di Azure, le prestazioni sono critiche in due fasi:

1. **Provisioning monouso iniziale**: per ottimizzare le prestazioni in fase di provisioning iniziale, fare riferimento a [Onboarding con Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) per informazioni dettagliate sulla distribuzione ottimale.
2. **Sincronizzazione continua**: dopo il seeding iniziale dei dati nelle condivisioni file di Azure, Sincronizzazione file di Azure mantiene sincronizzati più endpoint.

Per semplificare la pianificazione della distribuzione per ognuna delle fasi, di seguito vengono presentati i risultati osservati durante i test interni su un sistema con una configurazione specifica

| Configurazione del sistema |  |
|-|-|
| CPU | 64 core virtuali con cache L3 da 64 MiB |
| Memoria | 128 GiB |
| Disco | Dischi SAS con RAID 10 con cache supportata da batteria |
| Rete | Rete a 1 Gbps |
| Carico di lavoro | File server per utilizzo generico|

| Provisioning monouso iniziale  |  |
|-|-|
| Numero di oggetti | 25 milioni di oggetti |
| Dimensioni del set di dati| ~4.7 TiB |
| Dimensioni medie dei file | ~ 200 KiB (File più grande: 100 GiB) |
| Velocità effettiva di caricamento | 20 oggetti al secondo |
| Velocità effettiva di download dello spazio dei nomi* | 400 oggetti al secondo |

\* Quando viene creato un nuovo endpoint del server, l'agente di Sincronizzazione file di Azure non scarica il contenuto di alcun file. Sincronizza prima di tutto lo spazio dei nomi completo e quindi attiva il richiamo in background per scaricare i file, interamente o, se è abilitato il cloud a più livelli, in base ai criteri di suddivisione in livelli cloud impostati nell'endpoint del server.

| Sincronizzazione continua  |   |
|-|--|
| Numero di oggetti sincronizzati| 125.000 oggetti (circa 1% di varianza) |
| Dimensioni del set di dati| 50 GiB |
| Dimensioni medie dei file | ~500 KiB |
| Velocità effettiva di caricamento | 30 oggetti al secondo |
| Velocità effettiva per il download completo* | 60 oggetti al secondo |

*Se è abilitato il cloud a più livelli, è probabile che si osserveranno prestazioni migliori, in quanto vengono scaricati solo alcuni dei dati dei file. Sincronizzazione file di Azure scarica solo i dati dei file memorizzati nella cache quando vengono modificati in uno degli endpoint. Per tutti i file a più livelli o appena creati, l'agente non scarica i dati dei file e sincronizza invece solo lo spazio dei nomi per tutti gli endpoint del server. L'agente supporta anche download parziali di file a più livelli man mano che vi accedono gli utenti. 

> [!Note]  
> I numeri forniti sopra non sono un'indicazione delle prestazioni che si riscontreranno. Le prestazioni effettive dipenderanno da diversi fattori, come descritto all'inizio di questa sezione.

Come indicazione generale per la distribuzione, è necessario tenere presenti alcuni aspetti:

- La velocità effettiva degli oggetti cambia all'incirca in misura proporzionale al numero di gruppi di sincronizzazione nel server. La suddivisione dei dati in più gruppi di sincronizzazione in un server produce una maggiore velocità effettiva, che è limitata anche dal server e dalla rete.
- La velocità effettiva degli oggetti è inversamente proporzionale alla velocità effettiva in MiB al secondo. Per i file più piccoli, si riscontrerà una velocità effettiva maggiore per quanto riguarda il numero di oggetti elaborati al secondo, ma con una minore velocità effettiva in MiB al secondo. Al contrario, per i file di dimensioni maggiori, si otterrà un numero minore di oggetti elaborati al secondo, ma con una maggiore velocità effettiva in MiB al secondo. La velocità effettiva in MiB al secondo è limitata dagli obiettivi di scalabilità di File di Azure.

## <a name="see-also"></a>Vedere anche

- [Pianificazione per la distribuzione di File di Azure](storage-files-planning.md)
- [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md)
- [Obiettivi di scalabilità e prestazioni per altri servizi di archiviazione](../common/storage-scalability-targets.md)
