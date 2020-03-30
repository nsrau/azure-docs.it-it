---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4d2c0a02a48c0e04b501f136f66c28b3f532e2a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79486025"
---
Gli snapshot incrementali sono backup temporizzato per i dischi gestiti che, una volta acquisiti, sono costituiti solo da tutte le modifiche apportate dall'ultimo snapshot. Quando si tenta di scaricare o utilizzare in altro modo uno snapshot incrementale, viene utilizzato il disco rigido virtuale completo. Questa nuova funzionalità per gli snapshot del disco gestito consente potenzialmente di essere più conveniente, poiché, a meno che non si scelga di archiviare l'intero disco con ogni singolo snapshot. Analogamente agli snapshot normali, gli snapshot incrementali possono essere usati per creare un disco gestito completo o per creare uno snapshot normale.

Esistono alcune differenze tra uno snapshot incrementale e uno snapshot normale. Gli snapshot incrementali utilizzeranno sempre l'archiviazione standard dei dischi rigidi, indipendentemente dal tipo di archiviazione del disco, mentre le istantanee regolari possono usare unità SSD premium. Se si usano snapshot regolari in Archiviazione Premium per aumentare le distribuzioni di macchine virtuali, è consigliabile usare immagini personalizzate nell'archiviazione standard nella [raccolta immagini condivise.](../articles/virtual-machines/linux/shared-image-galleries.md) Vi aiuterà a raggiungere una scala più massiccia con costi inferiori. Inoltre, gli snapshot incrementali offrono potenzialmente una migliore affidabilità con [l'archiviazione con ridondanza](../articles/storage/common/storage-redundancy-zrs.md) di zona. Se nell'area selezionata è disponibile l'opzione . Se nell'area non è disponibile il sistema di archiviazione con [ridondanza](../articles/storage/common/storage-redundancy-lrs.md) locale ( LRS). È possibile eseguire l'override di questo comportamento e selezionarne uno manualmente, ma non è consigliabile.

Gli snapshot incrementali offrono anche una funzionalità differenziale, disponibile solo per i dischi gestiti. Consentono di ottenere le modifiche tra due snapshot incrementali degli stessi dischi gestiti, fino al livello di blocco. È possibile utilizzare questa funzionalità per ridurre il footprint dei dati durante la copia di istantanee tra aree.  Ad esempio, è possibile scaricare il primo snapshot incrementale come BLOB di base in un'altra area. Per gli snapshot incrementali successivi, è possibile copiare solo le modifiche dall'ultimo snapshot nel BLOB di base. Dopo aver copiato le modifiche, è possibile creare snapshot nel BLOB di base che rappresentano il backup temporale del disco in un'altra area. È possibile ripristinare il disco dal BLOB di base o da uno snapshot nel BLOB di base in un'altra area.

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="Diagramma che illustra gli snapshot incrementali copiati tra le aree. Gli snapshot effettuano varie chiamate API fino a formare blob di pagine per ogni snapshot.":::