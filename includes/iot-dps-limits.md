---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 4bd890adcaa4982c52366faed0f2975729290360
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73612094"
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
> Per aumentare il numero di istanze nella sottoscrizione, contattare il [supporto tecnico Microsoft](https://azure.microsoft.com/support/options/).

> [!NOTE]
> Per aumentare il numero di registrazioni al servizio di provisioning, contattare il [supporto tecnico Microsoft](https://azure.microsoft.com/support/options/).

Il servizio Device Provisioning limita le richieste quando vengono superate le quote seguenti.

| Limitazione | Valore per unità |
| --- | --- |
| Operazioni | 200/min/servizio |
| Registrazioni del dispositivo | 200/min/servizio |
| Operazione di polling di dispositivo | 5/10 sec/dispositivo |
