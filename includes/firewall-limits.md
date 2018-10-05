---
title: File di inclusione
description: File di inclusione
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 7/30/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 0a4a66543b323b1dbe56158851dafcb1dfe695f0
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47185474"
---
| Risorsa | Limite predefinito |
| --- | --- |
| Dati elaborati |1000 TB/firewall/mese <sup>1</sup> |
|Regole|10.000 regole di applicazione, 10.000 regole di rete|
|Peering reti virtuali|Per le implementazioni hub-spoke, massimo 50 reti virtuali spoke.|
|Peering globale|Non supportati. È necessaria almeno una distribuzione del firewall per area.|
|Numero massimo di porte in una singola regola di rete|15<br>Un intervallo di porte (ad esempio: da 2 a 10) viene conteggiato come due.


<sup>1</sup> Contattare il supporto tecnico Azure nel caso in cui sia necessario aumentare questi limiti.
