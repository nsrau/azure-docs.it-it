---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d93de4ed758afb5e951bb5e19f4f7adb290e461c
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227486"
---
La tabella seguente include l'elenco delle possibili combinazioni di caricamento e acquisizione di immagini del sistema operativo Linux generalizzate e specializzate. Le combinazioni che vengono elaborate senza errori sono indicate da una S, mentre quelle che generano errori sono indicate da una N. Sotto la tabella sono riportate le cause e le soluzioni per i diversi errori che si verificheranno.

| SO | Caricamento spec. | Caricamento gen. | Acquisizione spec. | Acquisizione gen. |
| --- | --- | --- | --- | --- |
| Linux gen. |N<sup>1</sup> |S |N<sup>3</sup> |S |
| Linux spec. |S |N<sup>2</sup> |S |N<sup>4</sup> |

