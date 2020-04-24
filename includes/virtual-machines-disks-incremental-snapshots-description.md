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
ms.openlocfilehash: 83c391c0d92f8d4a0ed4b44bc3a90273db51b412
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81539392"
---
Gli snapshot incrementali sono backup temporizzati per i dischi gestiti che, quando vengono eseguiti, sono costituiti solo da tutte le modifiche apportate dopo l'ultimo snapshot. Quando si tenta di scaricare o utilizzare in altro modo uno snapshot incrementale, viene utilizzato il VHD completo. Questa nuova funzionalità per gli snapshot dei dischi gestiti può potenzialmente essere più conveniente, dal momento che, a meno che non si scelga di, non è necessario archiviare l'intero disco con ogni singolo snapshot. Analogamente agli snapshot regolari, è possibile usare gli snapshot incrementali per creare un disco gestito completo o per eseguire uno snapshot regolare.

Esistono alcune differenze tra uno snapshot incrementale e uno snapshot regolare. Gli snapshot incrementali utilizzeranno sempre l'archiviazione HDD standard, indipendentemente dal tipo di archiviazione del disco, mentre gli snapshot regolari possono usare unità SSD Premium. Se si usano snapshot regolari nell'archiviazione Premium per la scalabilità verticale delle distribuzioni di macchine virtuali, è consigliabile usare immagini personalizzate nell'archiviazione standard della [raccolta immagini condivise](../articles/virtual-machines/linux/shared-image-galleries.md). Consente di ottenere una scalabilità più massiccia con costi ridotti. Inoltre, gli snapshot incrementali offrono potenzialmente una migliore affidabilità con l' [archiviazione con ridondanza della zona](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Se ZRS è disponibile nell'area selezionata, verrà usato automaticamente ZRS per uno snapshot incrementale. Se ZRS non è disponibile nell'area, lo snapshot utilizzerà per impostazione predefinita l' [archiviazione con ridondanza locale](../articles/storage/common/storage-redundancy-lrs.md) (con ridondanza locale). È possibile eseguire l'override di questo comportamento e selezionarne uno manualmente, ma non è consigliabile.

Gli snapshot incrementali offrono inoltre una funzionalità differenziale, disponibile solo per i dischi gestiti. Consentono di ottenere le modifiche tra due snapshot incrementali degli stessi dischi gestiti, fino al livello di blocco. È possibile usare questa funzionalità per ridurre il footprint dei dati durante la copia degli snapshot tra le aree.  Ad esempio, è possibile scaricare il primo snapshot incrementale come BLOB di base in un'altra area. Per gli snapshot incrementali successivi, è possibile copiare solo le modifiche apportate dall'ultimo snapshot al BLOB di base. Dopo aver copiato le modifiche, è possibile creare snapshot nel BLOB di base che rappresentano il backup temporizzato del disco in un'altra area. È possibile ripristinare il disco dal BLOB di base o da uno snapshot nel BLOB di base in un'altra area.

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="Diagramma che illustra gli snapshot incrementali copiati tra le aree. Gli snapshot effettuano varie chiamate API fino alla creazione di BLOB di pagine per ogni snapshot.":::

È possibile visualizzare le dimensioni usate degli snapshot esaminando il [report sull'utilizzo di Azure](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). Se ad esempio le dimensioni dei dati usati di uno snapshot sono 10 GiB, il report sull'utilizzo **giornaliero** mostrerà 10 GiB/(31 giorni) = 0,3226 come quantità utilizzata.
