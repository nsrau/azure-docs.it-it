---
title: includere file
description: includere file
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 4770ac0181c64ef800aa02ba87284c8add357e36
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518066"
---
Questo articolo consente di chiarire le prestazioni del disco e il suo funzionamento quando si combinano macchine virtuali di Azure e dischi di Azure. Viene inoltre descritto come è possibile diagnosticare i colli di bottiglia per l'IO del disco e le modifiche che è possibile apportare per ottimizzare le prestazioni.

## <a name="how-does-disk-performance-work"></a>Come funzionano le prestazioni del disco?
Le macchine virtuali di Azure dispongono di operazioni di input/output al secondo (IOPS) e limiti di prestazioni della velocità effettiva in base al tipo e alle dimensioni della macchina virtuale. I dischi del sistema operativo e i dischi dati possono essere collegati alle macchine virtuali. I dischi hanno limiti di velocità effettiva e IOPS.

Le prestazioni dell'applicazione vengono limitate quando richiede più IOPS o velocità effettiva rispetto a quelle assegnate per le macchine virtuali o i dischi collegati. Quando il limite è limitato, le prestazioni dell'applicazione sono non ottimali. Questo può causare conseguenze negative, ad esempio un aumento della latenza. Di seguito vengono illustrati alcuni esempi per chiarire questo concetto. Per semplificare il following di questi esempi, verranno esaminate solo le operazioni di IOPS. Tuttavia, la stessa logica si applica alla velocità effettiva.

## <a name="disk-io-capping"></a>Limitazione IO disco

**Installazione**

- Standard_D8s_v3
  - IOPS non memorizzati nella cache: 12.800
- Disco del sistema operativo E30
  - IOPS: 500
- Due dischi dati E30 × 2
  - IOPS: 500

![Diagramma che mostra la limitazione a livello di disco.](media/vm-disk-performance/disk-level-throttling.jpg)

L'applicazione in esecuzione nella macchina virtuale effettua una richiesta che richiede 10.000 IOPS alla macchina virtuale. Tutti i quali sono consentiti dalla VM perché la macchina virtuale Standard_D8s_v3 può eseguire fino a 12.800 IOPS.

Le richieste di IOPS 10.000 vengono suddivise in tre richieste diverse per i diversi dischi:

- 1.000 IOPS vengono richiesti al disco del sistema operativo.
- 4.500 IOPS vengono richiesti a ogni disco dati.

Tutti i dischi collegati sono dischi E30 e sono in grado di gestire solo 500 IOPS. Quindi, rispondono con 500 IOPS ciascuno. Le prestazioni dell'applicazione sono limitate dai dischi collegati e possono solo elaborare 1.500 IOPS. L'applicazione potrebbe funzionare con prestazioni ottimali con 10.000 IOPS se vengono usati dischi con prestazioni migliori, ad esempio SSD Premium dischi P30.

## <a name="virtual-machine-io-capping"></a>Limitazione delle operazioni di i/o della macchina virtuale

**Installazione**

- Standard_D8s_v3
  - IOPS non memorizzati nella cache: 12.800
- Disco del sistema operativo P30
  - IOPS: 5.000
- Due dischi dati P30 × 2
  - IOPS: 5.000

![Diagramma che mostra la limitazione a livello di macchina virtuale.](media/vm-disk-performance/vm-level-throttling.jpg)

L'applicazione in esecuzione nella macchina virtuale effettua una richiesta che richiede 15.000 IOPS. Sfortunatamente, viene eseguito il provisioning della macchina virtuale Standard_D8s_v3 per gestire 12.800 IOPS. L'applicazione è limitata dai limiti della macchina virtuale ed è necessario allocare gli IOPS 12.800 assegnati.

Questi 12.800 IOPS richiesti sono suddivisi in tre richieste diverse per i diversi dischi:

- 4.267 IOPS vengono richiesti al disco del sistema operativo.
- 4.266 IOPS vengono richiesti a ogni disco dati.

Tutti i dischi collegati sono dischi P30 in grado di gestire 5.000 IOPS. Quindi, rispondono con gli importi richiesti.
