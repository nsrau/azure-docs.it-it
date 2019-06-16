---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 610f5fcf80db8ffa0c7207d459b98087cbb4773b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66455174"
---
Nella tabella seguente sono elencate le limitazioni che si applicano alle risorse di Azure IoT Hub servizio Device Provisioning.

| Resource | Limite |
| --- | --- |
| Massimo servizi device provisioning per ogni sottoscrizione di Azure | 10 |
| Numero massimo di iscrizioni | 1\.000.000 |
| Numero massimo di registrazioni | 1\.000.000 |
| Numero massimo di gruppi di registrazione | 100 |
| Numero massimo di autorità di certificazione | 25 |
| Dimensione massima del messaggio | 96 KB|

> [!NOTE]
> Per aumentare il numero di istanze nella sottoscrizione, contattare [supporto tecnico Microsoft](https://azure.microsoft.com/support/options/).

> [!NOTE]
> Per aumentare il numero di registrazioni e le registrazioni sul servizio di provisioning, contattare [supporto tecnico Microsoft](https://azure.microsoft.com/support/options/).

Il servizio Device Provisioning limita le richieste quando vengono superate le quote seguenti.

| Limitazione | Valore per unità |
| --- | --- |
| Operazioni | 200/min/servizio |
| Registrazioni del dispositivo | 200/min/servizio |
| Operazione di polling di dispositivo | 5/10 sec/dispositivo |
