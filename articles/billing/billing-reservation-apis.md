---
title: API per l'automazione delle prenotazioni di Azure | Microsoft Docs
description: Informazioni sulle API di Azure utilizzabili per ottenere informazioni sulle prenotazioni a livello di codice.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/10/2018
ms.author: banders
ms.openlocfilehash: a71c456f017e80da85f27437738aa963ec967a3c
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2019
ms.locfileid: "54904158"
---
# <a name="apis-for-azure-reservation-automation"></a>API per l'automazione delle prenotazioni di Azure

Usare le API di Azure per ottenere informazioni per l'organizzazione a livello di codice sulle prenotazioni di software o servizi di Azure.

## <a name="find-reservation-plans-to-buy"></a>Trovare piani di prenotazione da acquistare

Usare l'API Raccomandazioni di prenotazione per ottenere consigli su quale piano di prenotazione acquistare in base all'utilizzo della propria organizzazione. Per altre informazioni, vedere [Get reservation recommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) (Ottenere raccomandazioni di prenotazione).

È anche possibile analizzare l'utilizzo delle risorse usando l'API per il consumo per dettagli sull'uso. Per altre informazioni, vedere [Usage Details - List For Billing Period By Billing Account](/rest/api/consumption/usagedetails/listforbillingperiodbybillingaccount) (Dettagli sull'uso - Elenco per periodo di fatturazione in base all'account di fatturazione). Le risorse di Azure che si usano regolarmente sono in genere la scelta ottimale per una prenotazione.

## <a name="buy-a-reservation"></a>Acquistare una prenotazione

Attualmente non è possibile acquistare una prenotazione a livello di codice. Per acquistare una prenotazione, vedere gli articoli seguenti:

Piani di servizio:
- [Macchina virtuale](../virtual-machines/windows/prepay-reserved-vm-instances.md?toc=/azure/billing/TOC.json)
-  [Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md?toc=/azure/billing/TOC.json)
- [Database SQL](../sql-database/sql-database-reserved-capacity.md?toc=/azure/billing/TOC.json)

Piani software:
- [Software SUSE Linux](../virtual-machines/linux/prepay-suse-software-charges.md?toc=/azure/billing/TOC.json)

## <a name="get-reservations"></a>Ottenere le prenotazioni

I clienti di Azure che dispongono di un contratto Enterprise (clienti con contratto Enterprise) possono ottenere le prenotazioni acquistate dall'organizzazione usando l'[API per ottenere le tariffe di transazione di istanza riservata](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). Per altre sottoscrizioni, ottenere l'elenco delle prenotazioni acquistate e l'autorizzazione a visualizzarle usando l'API [Reservation Order - List](/rest/api/reserved-vm-instances/reservationorder/list). Per impostazione predefinita, il proprietario dell'account o l'utente che ha acquistato la prenotazione dispone delle autorizzazioni per visualizzare la prenotazione.

## <a name="see-reservation-usage"></a>Visualizzare l'utilizzo della prenotazione

I clienti con contratto Enterprise possono visualizzare a livello di codice come vengono usate le prenotazioni all'interno dell'organizzazione. Per altre informazioni, vedere [Get Reserved Instance usage for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) (Ottenere i dati sull'utilizzo di istanza riservata per i clienti Enterprise). Per altre sottoscrizioni, usare l'API per i [riepiloghi delle prenotazioni - Elenco per ordine di prenotazione e prenotazione](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

Se si ritiene che le prenotazioni dell'organizzazione siano sottoutilizzate:

- Assicurarsi che le macchine virtuali create dall'organizzazione corrispondano alle dimensioni delle macchine virtuali riportate nella prenotazione.
- Assicurarsi che la flessibilità delle dimensioni istanza sia attiva. Per altre informazioni, vedere [Gestire le prenotazioni - Modificare l'impostazione di ottimizzazione per le istanze di macchina virtuale riservate](billing-manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).
- Modificare l'ambito della prenotazione impostandolo su condiviso in modo che venga applicato più su larga scala. Per altre informazioni, vedere [Gestire le prenotazioni - Modificare l'ambito di una prenotazione](billing-manage-reserved-vm-instance.md#change-the-scope-for-a-reservation).
- Scambiare la quantità inutilizzata. Per altre informazioni, vedere [Gestire le prenotazioni - Annullamenti e scambi](billing-manage-reserved-vm-instance.md#cancellations-and-exchanges).

## <a name="give-access-to-reservations"></a>Concedere l'accesso alle prenotazioni

Ottenere l'elenco di tutte le prenotazioni a cui un utente può accedere usando l'[API Reservation - Operation - List](/rest/api/reserved-vm-instances/reservationorder/list). Per consentire l'accesso a una prenotazione a livello di codice, vedere uno degli articoli seguenti:

- [Gestire l'accesso tramite il controllo degli accessi in base al ruolo e l'API REST](../role-based-access-control/role-assignments-rest.md)
- [Gestire l'accesso tramite il controllo degli accessi in base al ruolo e Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Gestire l'accesso tramite il controllo degli accessi in base al ruolo e l'interfaccia della riga di comando di Azure](../role-based-access-control/role-assignments-cli.md)

## <a name="split-or-merge-reservation"></a>Dividere o unire una prenotazione

Dopo aver acquistato più di un'istanza di risorsa all'interno di una prenotazione, è possibile assegnare le istanze all'interno di tale prenotazione a sottoscrizioni diverse. È possibile modificare l'ambito della prenotazione in modo che sia applicabile a tutte le sottoscrizioni all'interno dello stesso contesto di fatturazione. Tuttavia, per motivi di gestione dei costi o di definizione del budget, è possibile mantenere l'ambito come "sottoscrizione singola" e assegnare le istanze di prenotazione a una sottoscrizione specifica. 

Per dividere una prenotazione, usare l'API [Reservation - Split](/rest/api/reserved-vm-instances/reservation/split). È anche possibile dividere una prenotazione tramite PowerShell. Per altre informazioni, vedere [Gestire le prenotazioni - Dividere una prenotazione singola in due prenotazioni](billing-manage-reserved-vm-instance.md#split-a-single-reservation-into-two-reservations).

Per unire due prenotazioni in una prenotazione, usare l'API [Reservation - Merge](/rest/api/reserved-vm-instances/reservation/merge).

## <a name="change-scope-for-a-reservation"></a>Modificare l'ambito di una prenotazione

L'ambito di una prenotazione può fare riferimento a una singola sottoscrizione o a tutte le sottoscrizioni nel contesto di fatturazione. Se si imposta l'ambito su una singola sottoscrizione, la prenotazione corrisponde alle risorse in esecuzione nella sottoscrizione selezionata. Se si imposta l'ambito su un ambito condiviso, Azure individua la prenotazione corrispondente relativa alle risorse in esecuzione in tutte le sottoscrizioni all'interno del contesto di fatturazione. Il contesto di fatturazione dipende dalla sottoscrizione usata per acquistare la prenotazione. Per altre informazioni, vedere [Gestire le prenotazioni - Modificare l'ambito](billing-manage-reserved-vm-instance.md#change-the-scope-for-a-reservation).

Per modificare l'ambito a livello di codice, usare l'API [Reservation - Update](/rest/api/reserved-vm-instances/reservation/update).

## <a name="learn-more"></a>Altre informazioni

- [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md)
- [Informazioni su come viene applicato lo sconto sulla prenotazione di macchine virtuali](billing-understand-vm-reservation-charges.md)
- [Informazioni su come viene applicato lo sconto relativo al piano software SUSE Linux Enterprise](billing-understand-suse-reservation-charges.md)
- [Informazioni su come vengono applicati gli altri sconti sulla prenotazione](billing-understand-reservation-charges.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Costi del software Windows non inclusi nelle prenotazioni](billing-reserved-instance-windows-software-costs.md)
- [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) del Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations)