---
title: File di inclusione
description: File di inclusione
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 11/26/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 8b236bc1f0089b89aca90e7c69e9b445b01a374b
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440222"
---
| Risorsa | Limite predefinito |
| --- | --- |
| Dati elaborati |1000 TB/firewall/mese <sup>1</sup> |
|Regole|10.000 - tutti i tipi di regole combinati|
|Peering globale|Non supportati. È necessaria almeno una distribuzione del firewall per area.|
|Numero massimo di porte in una singola regola di rete|15<br>Un intervallo di porte (ad esempio: da 2 a 10) viene conteggiato come due.
|Dimensioni minime di AzureFirewallSubnet |/26


<sup>1</sup> Contattare il supporto tecnico Azure nel caso in cui sia necessario aumentare questi limiti.
