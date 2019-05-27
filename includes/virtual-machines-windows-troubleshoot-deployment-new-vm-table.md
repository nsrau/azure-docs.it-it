---
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 14abae6f6f72d724fffb1ccaa12f56fb6976f7a1
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66160228"
---
La tabella seguente elenca le possibili combinazioni di caricamento e acquisizione di immagini del sistema operativo Windows generalizzate (gen.) e specializzate (spec.). Le combinazioni che vengono elaborate senza errori sono indicate da una S, mentre quelle che generano errori sono indicate da una N. Sotto la tabella sono riportate le cause e le soluzioni per i diversi errori che si verificheranno.

| SO | Caricamento spec. | Caricamento gen. | Acquisizione spec. | Acquisizione gen. |
| --- | --- | --- | --- | --- |
| Windows gen. |N<sup>1</sup> |S |N<sup>3</sup> |S |
| Windows spec. |S |N<sup>2</sup> |S |N<sup>4</sup> |

