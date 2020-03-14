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
ms.openlocfilehash: 1f094cd78e6708fec4bda1e5510379b11df14dcd
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299178"
---
Gli snapshot incrementali sono backup temporizzati per i dischi gestiti che, quando vengono eseguiti, sono costituiti solo da tutte le modifiche apportate dopo l'ultimo snapshot. Quando si tenta di scaricare o utilizzare in altro modo uno snapshot incrementale, viene utilizzato il VHD completo. Questa nuova funzionalità per gli snapshot dei dischi gestiti può potenzialmente essere più conveniente, dal momento che, a meno che non si scelga di, non è necessario archiviare l'intero disco con ogni singolo snapshot. Analogamente agli snapshot regolari, è possibile usare gli snapshot incrementali per creare un disco gestito completo o per eseguire uno snapshot regolare.

Esistono alcune differenze tra uno snapshot incrementale e uno snapshot regolare. Gli snapshot incrementali utilizzeranno sempre l'archiviazione HDD standard, indipendentemente dal tipo di archiviazione del disco, mentre gli snapshot regolari possono usare unità SSD Premium. Se si usano snapshot regolari nell'archiviazione Premium per la scalabilità verticale delle distribuzioni di macchine virtuali, è consigliabile usare immagini personalizzate nell'archiviazione standard della [raccolta immagini condivise](../articles/virtual-machines/linux/shared-image-galleries.md). Consente di ottenere una scalabilità più massiccia con costi ridotti. Inoltre, gli snapshot incrementali offrono potenzialmente una migliore affidabilità con l' [archiviazione con ridondanza della zona](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Se ZRS è disponibile nell'area selezionata, verrà usato automaticamente ZRS per uno snapshot incrementale. Se ZRS non è disponibile nell'area, lo snapshot utilizzerà per impostazione predefinita l' [archiviazione con ridondanza locale](../articles/storage/common/storage-redundancy-lrs.md) (con ridondanza locale). È possibile eseguire l'override di questo comportamento e selezionarne uno manualmente, ma non è consigliabile.

Gli snapshot incrementali offrono inoltre una funzionalità differenziale, disponibile solo per i dischi gestiti. Consentono di ottenere le modifiche tra due snapshot incrementali degli stessi dischi gestiti, fino al livello di blocco. È possibile usare questa funzionalità per ridurre il footprint dei dati durante la copia degli snapshot tra le aree.
