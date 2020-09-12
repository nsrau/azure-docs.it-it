---
title: Concetti-archiviazione
description: Informazioni sulle funzionalità di archiviazione chiavi in cloud privati in anteprima della soluzione VMware di Azure.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 8afd58c61f2f78eec2a92354be6d88178340912e
ms.sourcegitcommit: 70ee014d1706e903b7d1e346ba866f5e08b22761
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2020
ms.locfileid: "90024316"
---
# <a name="azure-vmware-solution-preview-storage-concepts"></a>Concetti relativi all'archiviazione anteprima della soluzione VMware di Azure

I cloud privati della soluzione VMware di Azure forniscono l'archiviazione nativa a livello di cluster con VMware rete VSAN. Tutte le archiviazioni locali da ogni host in un cluster vengono usate in un archivio dati rete VSAN e la crittografia dei dati inattivi è disponibile e abilitata per impostazione predefinita. È possibile usare le risorse di archiviazione di Azure per estendere le funzionalità di archiviazione dei cloud privati.

## <a name="vsan-clusters"></a>cluster rete VSAN

L'archiviazione locale in ogni host del cluster viene usata come parte di un archivio dati rete VSAN. Tutti diskgroups usano un livello di cache NVMe di 1,6 TB con la capacità basata su SSD, per host, di 15,4 TB. La dimensione del livello di capacità raw di un cluster è la capacità per host per il numero di host. Un cluster host, ad esempio, offre una capacità di 61,6 TB non elaborata nel livello di capacità rete VSAN.

L'archiviazione locale negli host del cluster viene usata nell'archivio dati rete VSAN a livello di cluster. Tutti gli archivi dati vengono creati come parte di una distribuzione di cloud privato e sono disponibili per l'uso immediato. L'utente cloudadmin e tutti gli utenti nel gruppo CloudAdmin possono gestire gli archivi dati con questi privilegi rete VSAN:
- Datastore.AllocateSpace
- Datastore.Browse
- Datastore.Config
- Archivio dati. DeleteFile
- Archivio dati. filemanagement
- Archivio dati. UpdateVirtualMachineMetadata

## <a name="data-at-rest-encryption"></a>Crittografia dei dati inattivi

per impostazione predefinita, i datastore rete VSAN usano la crittografia dei dati inattivi. La soluzione di crittografia è basata sul KMS e supporta le operazioni vCenter per la gestione delle chiavi. Le chiavi di crittografia della chiave vengono archiviate in una Azure Key Vault e quando un host viene rimosso da un cluster per qualsiasi motivo, i dati nelle unità SSD vengono invalidati immediatamente.

## <a name="scaling"></a>Scalabilità

La capacità di archiviazione del cluster nativa viene ridimensionata aggiungendo host a un cluster. Per i cluster che usano host, la capacità a livello di cluster non elaborata viene aumentata di 15,4 TB con ogni host aggiuntivo. I cluster compilati con gli host GP hanno una capacità non elaborata aumentata di 7,7 TB con ogni host aggiuntivo. In entrambi i tipi di cluster, gli host importano circa 10 minuti per essere aggiunti a un cluster. Per istruzioni sulla scalabilità dei cluster, vedere l' [esercitazione scalare un cloud privato][tutorial-scale-private-cloud] .

## <a name="azure-storage-integration"></a>Integrazione di archiviazione di Azure

È possibile usare i servizi di archiviazione di Azure sui carichi di lavoro in esecuzione nel cloud privato. I servizi di archiviazione di Azure includono gli account di archiviazione, l'archiviazione tabelle e l'archiviazione BLOB. La connessione dei carichi di lavoro ai servizi di archiviazione di Azure non attraversa Internet. Questa connettività offre maggiore sicurezza e consente di usare i servizi di archiviazione di Azure basati sul contratto di servizio nei carichi di lavoro del cloud privato.

## <a name="next-steps"></a>Passaggi successivi

Il passaggio successivo consiste nel comprendere i [concetti relativi alle identità del cloud privato][concepts-identity].

<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorial-scale-private-cloud]: ./tutorial-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md
