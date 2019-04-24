---
title: Informazioni sull'utilizzo di prenotazioni di Azure per Enterprise | Microsoft Docs
description: Informazioni su come leggere l'utilizzo per comprendere come viene applicata la prenotazione di Azure per l'iscrizione Enterprise.
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2019
ms.author: banders
ms.openlocfilehash: daa7f6a116578fa8d1f2b5bf825a6f4cd48f7f64
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370701"
---
# <a name="understand-azure-reservation-usage-for-your-enterprise-enrollment"></a>Informazioni sull'utilizzo della prenotazione di Azure per l'iscrizione Enterprise

Usare **ReservationId** della [pagina Prenotazioni](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) e il file relativo all'utilizzo del [portale EA](https://ea.azure.com) per valutare l'utilizzo della prenotazione. È anche possibile visualizzare l'utilizzo della prenotazione nella sezione di riepilogo dell'utilizzo del [portale EA](https://ea.azure.com).

Se la prenotazione è stata acquistata in un contesto di fatturazione con pagamento in base al consumo, vedere [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md).

## <a name="usage-for-reserved-virtual-machines-instances"></a>Utilizzo delle istanze di macchine virtuali riservate

Per le sezioni seguenti si presuppone di eseguire una macchina virtuale Windows Standard_D1_v2 nell'area Stati Uniti orientali e che le informazioni sulla prenotazione siano simili a quelle della tabella seguente:

| Campo | Value |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Quantità |1|
|SKU | Standard_D1|
|Region | eastus |

La parte hardware della VM è coperta perché la VM distribuita corrisponde agli attributi della prenotazione. Per informazioni sul software Windows non coperto dalla prenotazione, vedere [Costi del software Windows delle istanze di macchina virtuale riservate di Azure](billing-reserved-instance-windows-software-costs.md).

### <a name="usage-in-csv-file-for-reserved-vm-instances"></a>Utilizzo nel file CSV per le istanze di macchina virtuale riservate

È possibile scaricare il file CSV relativo all'utilizzo per la registrazione Enterprise da Enterprise Portal. Nel file CSV applicare un filtro in **Informazioni aggiuntive** e digitare il valore di **ReservationId**. Lo screenshot seguente illustra i campi correlati alla prenotazione:

![File CSV per il contratto Enterprise Agreement (EA) per la prenotazione di Azure](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. **ReservationId** nel campo **Informazioni aggiuntive** rappresenta la prenotazione applicata alla macchina virtuale.
2. **ConsumptionMeter** è l'ID contatore per la macchina virtuale.
3. **ID contatore** è il contatore della prenotazione con costo pari a $ 0. Il costo dell'esecuzione della macchina virtuale è coperto dall'istanza di macchina virtuale riservata.
4. Standard_D1 è una macchina virtuale vCPU e la macchina virtuale viene distribuita senza il Vantaggio Azure Hybrid. Questo contatore copre quindi il costo aggiuntivo del software Windows. Per trovare il contatore corrispondente alla macchina virtuale serie D con 1 core, vedere [Costi del software Windows delle istanze di macchina virtuale riservate di Azure](billing-reserved-instance-windows-software-costs.md).  Se si ha il Vantaggio Azure Hybrid, questo costo aggiuntivo non viene applicato.

## <a name="usage-for-sql-database--cosmos-db-reserved-capacity-reservations"></a>Utilizzo delle prenotazioni di capacità riservata del database SQL e Cosmos DB

Le sezioni seguenti usano il database SQL di Azure come esempio per descrivere il report di utilizzo. È possibile seguire questi stessi passaggi anche per l'utilizzo in Azure Cosmos DB.

Si presuppone che sia eseguito un database SQL di quarta generazione nell'area Stati Uniti orientali e che le informazioni sulla prenotazione siano simili a quelle della tabella seguente:

| Campo | Value |
|---| --- |
|ReservationId |8244e673-83e9-45ad-b54b-3f5295d37cae|
|Quantità |2|
|Prodotto| Database SQL Gen4 (2 core)|
|Region | eastus |

### <a name="usage-in-csv-file"></a>Utilizzo in un file con estensione csv

Applicare un filtro in **Informazioni aggiuntive**, digitare un valore in **ID prenotazione** e scegliere la **categoria del contatore** necessaria: database SQL di Azure o Azure Cosmos DB. Lo screenshot seguente illustra i campi correlati alla prenotazione.

![File CSV per il contratto Enterprise Agreement (EA) per la capacità riservata del database SQL](./media/billing-understand-reserved-instance-usage-ea/billing-ea-sql-db-reserved-capacity-csv.png)

1. **ReservationId** nel campo **Informazioni aggiuntive** rappresenta la prenotazione applicata alla risorsa database SQL.
2. **ConsumptionMeter** è l'ID contatore per la risorsa database SQL.
3. **ID contatore** è il contatore della prenotazione con costo pari a $ 0. Questo ID contatore è presente nel file CSV per qualsiasi risorsa database SQL idonea per la prenotazione.

## <a name="usage-summary-page-in-enterprise-portal"></a>Pagina di riepilogo dell'utilizzo in Enterprise Portal

L'utilizzo della prenotazione di Azure viene visualizzato anche nella sezione di riepilogo dell'utilizzo in Enterprise Portal: ![Riepilogo dell'utilizzo per il contratto Enterprise Agreement (EA)](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. Non vengono addebitati costi per il componente hardware della macchina virtuale, perché è coperto dalla prenotazione. Per una prenotazione del database SQL, viene visualizzata una riga con **Nome servizio** per l'utilizzo della capacità riservata del database SQL di Azure.
2. In questo esempio non si ha il Vantaggio Azure Hybrid, quindi il software Windows usato con la macchina virtuale viene addebitato.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Se si hanno domande o assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md)
- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pagare in anticipo le risorse di calcolo del database SQL con capacità riservata del database SQL di Azure](../sql-database/sql-database-reserved-capacity.md)
- [Gestire le prenotazioni di Azure](billing-manage-reserved-vm-instance.md)
- [Informazioni su come viene applicato lo sconto sulla prenotazione](billing-understand-vm-reservation-charges.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
- [Costi del software Windows non inclusi nelle prenotazioni](billing-reserved-instance-windows-software-costs.md)

