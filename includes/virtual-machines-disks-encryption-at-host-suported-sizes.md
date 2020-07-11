---
title: includere il file
description: includere file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e5a811620de8336abd3e0df6d72db761ce18b2b6
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86230991"
---
Tutta la generazione più recente di dimensioni di VM supporta la crittografia nell'host:

|Tipo  |Non supportato  |Supportato  |
|---------|---------|---------|
|Scopo generico     | Dv3, Dav4, dv2, AV2        | B, DSv2, Dsv3, DC, DCv2, Dasv4        |
|Ottimizzate per il calcolo     |         | Fsv2        |
|Ottimizzate per la memoria     | EV3, Eav4        | DSv2, Esv3, M, Mv2, Easv4        |
|Ottimizzate per l'archiviazione     |         | LS, Lsv2 (dischi NVMe non crittografati)        |
|GPU     | NC, NV        | NCv2, NCv3, ND, NVv3, NVv4, NDv2 (anteprima)        |
|High Performance Computing (HPC)     | H        | HB, HC, HBv2        |
|Generazioni precedenti     | F, A, D, L, G        | DS, GS, FS, NVv2        |

L'aggiornamento delle dimensioni della macchina virtuale comporterà la convalida per verificare se le nuove dimensioni della macchina virtuale supportano la funzionalità EncryptionAtHost.
