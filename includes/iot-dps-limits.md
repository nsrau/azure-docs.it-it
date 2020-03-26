---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2b8d60da507aa1eb312d1a8505f756ff337085ff
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77592425"
---
La tabella seguente elenca i limiti applicabili alle risorse del servizio Device Provisioning in hub IoT di Azure.

| Risorsa | Limite |
| --- | --- |
| Numero massimo di servizi di provisioning di dispositivi per sottoscrizione di Azure | 10 |
| Numero massimo di iscrizioni | 1\.000.000 |
| Numero massimo di registrazioni | 1\.000.000 |
| Numero massimo di gruppi di registrazione | 100 |
| Numero massimo di autorità di certificazione | 25 |
| Numero massimo di hub IoT collegati | 50 |
| Dimensioni massime del messaggio | 96 KB|

> [!NOTE]
> Per aumentare il numero di registrazioni al servizio di provisioning, contattare il [supporto tecnico Microsoft](https://azure.microsoft.com/support/options/).

> [!NOTE]
> L'aumento del numero massimo di CA non è supportato.

Il servizio Device Provisioning limita le richieste quando vengono superate le quote seguenti.

| Limitazione | Valore per unità |
| --- | --- |
| Operazioni | 200/min/servizio |
| Registrazioni del dispositivo | 200/min/servizio |
| Operazione di polling di dispositivo | 5/10 sec/dispositivo |
