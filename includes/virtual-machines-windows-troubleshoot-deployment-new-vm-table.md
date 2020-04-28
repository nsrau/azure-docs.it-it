---
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 14abae6f6f72d724fffb1ccaa12f56fb6976f7a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67180147"
---
La tabella seguente elenca le possibili combinazioni di caricamento e acquisizione di immagini del sistema operativo Windows generalizzate (gen.) e specializzate (spec.). Le combinazioni che vengono elaborate senza errori sono indicate da una S, mentre quelle che generano errori sono indicate da una N. Sotto la tabella sono riportate le cause e le soluzioni per i diversi errori che si verificheranno.

| OS | Caricamento spec. | Caricamento gen. | Acquisizione spec. | Acquisizione gen. |
| --- | --- | --- | --- | --- |
| Windows gen. |N<sup>1</sup> |S |N<sup>3</sup> |S |
| Windows spec. |S |N<sup>2</sup> |S |N<sup>4</sup> |

