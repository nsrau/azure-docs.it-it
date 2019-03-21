---
title: Funzionalità di richiesta dati dei clienti
description: Riepilogo delle funzionalità di richiesta dei dati dei clienti
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: e9cc16d659e220dbe72cc2b2e22f71a817b36de2
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011314"
---
# <a name="summary-of-customer-data-request-features"></a>Riepilogo delle funzionalità di richiesta dei dati dei clienti

L'Hub IoT di Azure è un servizio cloud basato su REST API destinato ai clienti aziendali che consente comunicazioni protette e bidirezionali tra milioni di dispositivi e un servizio di Azure partizionato.

[!INCLUDE [gdpr-related guidance](../../includes/gdpr-intro-sentence.md)]

Ai singoli dispositivi viene assegnato un identificatore del dispositivo e (ID dispositivo) da un amministratore del tenant. I dati del dispositivo si basano sull'ID dispositivo assegnato. Microsoft non gestisce alcuna informazione e non dispone dell'accesso ai dati che permetterebbero l'ID dispositivo di avere una correlazione con l'utente.

Molti dei dispositivi gestiti in hub IoT di Azure non sono dispositivi personali, ad esempio un termostato o un robot industriale. I clienti possono, tuttavia, decidere di rendere personalmente identificabili alcuni dispositivi e, a loro discrezione, possono gestire i propri asset o i propri metodi di verifica dell'inventario che collegano i dispositivi agli individui. Hub IoT di Azure gestisce e archivia tutti i dati associati dispositivi come se fossero dati personali.

Gli amministratori del tenant possono usare il portale di Azure o le API REST del servizio per soddisfare le richieste di informazioni mediante l'esportazione o l'eliminazione dei dati associati a un ID dispositivo.

Se si usa la funzionalità di routing del servizio Hub IoT di Azure per l'inoltro di messaggi da dispositivo ad altri servizi, allora le richieste di dati devono essere eseguite dall'amministratore tenant per ogni endpoint di routing al fine di completare una richiesta completa per un determinato dispositivo. Per altri dettagli, vedere la documentazione di riferimento di ogni endpoint. Per altre informazioni sugli endpoint supportati, vedere [Riferimenti - Endpoint dell'hub IoT](iot-hub-devguide-endpoints.md).

Se si usa la funzionalità di integrazione di Griglia di eventi di Azure del servizio Hub IoT di Azure, allora le richieste di dati devono essere eseguite da amministratore tenant per ogni sottoscrittore di questi eventi. Per altre informazioni, vedere [Rispondere agli eventi dell'hub IoT usando Griglia di eventi](iot-hub-event-grid.md).

Se si usa la funzionalità di integrazione di Monitoraggio di Azure del servizio Hub IoT di Azure per creare log di diagnostica, allora le richieste di dati devono essere eseguite da amministratore tenant dai log archiviati. Per altre informazioni, vedere [Monitorare l'integrità dell'hub IoT di Azure](iot-hub-monitor-resource-health.md).

## <a name="deleting-customer-data"></a>Eliminazione dei dati del cliente

Gli amministratori tenant possono usare il pannello di dispositivi IoT dell'estensione dell'Hub IoT di Azure nel portale di Azure per eliminare un dispositivo, che elimina i dati associati al dispositivo.

È anche possibile eseguire le operazioni di eliminazione per i dispositivi tramite le API REST. Per altre informazioni, vedere [Service - Delete Device](/rest/api/iothub/service/deletedevice) (Servizio - Delete Device).

## <a name="exporting-customer-data"></a>Esportazione dei dati dei clienti

Gli amministratori tenant possono utilizzare copia e incolla all'interno del pannello di dispositivi IoT dell'estensione dell'Hub IoT di Azure nel portale di Azure per esportare i dati associati a un dispositivo.

È anche possibile eseguire le operazioni di esportazione per i dispositivi tramite le API REST. Per altre informazioni, vedere [Service - Delete Device](/rest/api/iothub/service/getdevice) (Servizio - Get Device).

> [!NOTE]
> Quando si usano i servizi enterprise di Microsoft, Microsoft genera alcune informazioni note come log generati dal sistema. Alcuni log generati dal sistema dell'hub IoT di Azure non sono accessibili o esportabili dagli amministratori del tenant. Questi log costituiscono azioni pratiche eseguite nel servizio e i dati diagnostici relativi ai singoli dispositivi.

## <a name="links-to-additional-documentation"></a>Collegamenti a documentazione aggiuntiva

La documentazione completa per le API del servizio Hub IoT di Azure si trova in [IoT Hub Service APIs](https://docs.microsoft.com/rest/api/iothub/service) (API del servizio Hub IoT).
