---
author: rothja
ms.service: iot-dps
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: bbe928084a758335e5fa3b0531726c77a6d599b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85838868"
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
