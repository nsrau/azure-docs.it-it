---
title: Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo | Microsoft Docs
description: Informazioni su come leggere l'utilizzo per identificare come viene applicata la prenotazione di Azure per la sottoscrizione con pagamento in base al consumo.
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
ms.date: 09/28/2018
ms.author: banders
ms.openlocfilehash: d3da7e73569d7860c7e4328b11ecc3f20cf648fc
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2019
ms.locfileid: "54903622"
---
# <a name="understand-azure-reservation-usage-for-your-pay-as-you-go-subscription"></a>Informazioni sull'utilizzo della prenotazione di Azure per la sottoscrizione con pagamento in base al consumo

Usare ReservationId della [pagina Prenotazioni](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) e il file relativo all'utilizzo dal [portale degli account di Azure](https://account.azure.com) per valutare l'utilizzo della prenotazione.

I clienti con Contratto Enterprise possono vedere l'articolo [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md).

Questo articolo presuppone che la prenotazione venga applicata a una sottoscrizione singola. Se la prenotazione viene applicata a più di una sottoscrizione, il vantaggio della prenotazione può estendersi a più file CSV di utilizzo.

## <a name="usage-for-reserved-virtual-machine-instances"></a>Utilizzo delle istanze di macchina virtuale riservate

Per le sezioni seguenti si presuppone di eseguire una macchina virtuale Windows Standard_DS1_v2 nell'area Stati Uniti orientali e che le informazioni sull'istanza riservata siano simili a quelle della tabella seguente:

| Campo | Valore |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Quantità |1|
|SKU | Standard_DS1_v2|
|Region | eastus |

La parte hardware della VM è coperta perché la VM distribuita corrisponde agli attributi della prenotazione. Per identificare quale software Windows non è coperto dall'istanza di macchina virtuale riservata, vedere [Costi del software Windows per le istanze di macchina virtuale riservate di Azure](billing-reserved-instance-windows-software-costs.md)

### <a name="statement-section-of-csv-file-for-vms"></a>Sezione relativa al rendiconto nel file CSV per le macchine virtuali

Questa sezione del file CSV indica l'utilizzo totale della prenotazione. Nel campo **Sottocategoria contatore** applicare il filtro che contiene **"Istanze riservate"**. Il contenuto visualizzato sarà simile allo screenshot seguente:

![Screenshot dei dettagli filtrati sull'utilizzo della prenotazione e addebiti](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

La riga **Istanze riservate di VM di base** indica il numero totale di ore coperte dalla prenotazione. Questa riga contiene il valore $ 0,00 perché è coperta dalla prenotazione. La riga **Istanze riservate di Windows Server (1 core)** indica il costo del software Windows.

### <a name="daily-usage-section-of-csv-file"></a>Sezione relativa all'utilizzo giornaliero nel file CSV

Applicare un filtro in **Informazioni aggiuntive** e digitare l'**ID prenotazione**. Lo screenshot seguente illustra i campi correlati alla prenotazione.

![Screenshot dei dettagli di utilizzo giornaliero e degli addebiti](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. **ReservationId** nel campo **Informazioni aggiuntive** rappresenta la prenotazione applicata alla macchina virtuale.
2. **ConsumptionMeter** è l'ID contatore per la macchina virtuale.
3. La riga **Istanze riservate di VM di base** in **Sottocategoria contatore** rappresenta la riga del costo con valore $ 0 nella sezione del rendiconto. Il costo dell'esecuzione di questa macchina virtuale è già coperto dalla prenotazione.
4. **ID contatore** è l'ID contatore della prenotazione. Il costo di questo contatore è $ 0. Questo ID contatore è visualizzato per qualsiasi macchina virtuale idonea per lo sconto sulla prenotazione.
5. Standard_DS1_v2 è una VM con vCPU che viene distribuita senza il vantaggio Azure Hybrid. Di conseguenza, questo contatore copre il costo aggiuntivo del software Windows. Per trovare il contatore corrispondente alla macchina virtuale serie D con 1 core, vedere [Costi del software Windows delle istanze di macchina virtuale riservate di Azure](billing-reserved-instance-windows-software-costs.md). Se si ha il Vantaggio Azure Hybrid, questo costo aggiuntivo non viene applicato.

## <a name="usage-for-sql-database--cosmos-db-reserved-capacity-reservations"></a>Utilizzo delle prenotazioni di capacità riservata del database SQL e Cosmos DB

Le sezioni seguenti usano il database SQL di Azure come esempio per descrivere il report di utilizzo. È possibile seguire questi stessi passaggi anche per l'utilizzo in Azure Cosmos DB. 

Si presuppone che sia eseguito un database SQL di quarta generazione nell'area Stati Uniti orientali e che le informazioni sulla prenotazione siano simili a quelle della tabella seguente:

| Campo | Valore |
|---| --- |
|ReservationId |446ec809-423d-467c-8c5c-bbd5d22906b1|
|Quantità |2|
|Prodotto| Database SQL Gen4 (2 core)|
|Region | eastus |

### <a name="statement-section-of-csv-file"></a>Sezione relativa al rendiconto nel file CSV

Filtrare in base al nome di misuratore **Utilizzo istanze riservate** e quindi scegliere **Categoria misuratore** - Database SQL di Azure o Azure Cosmos DB. Il contenuto visualizzato sarà simile allo screenshot seguente:

![File CSV per la capacità riservata del database SQL](./media/billing-understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-statements.png)

La riga **Utilizzo istanze riservate** include il numero totale di ore core coperte dalla prenotazione. La tariffa per questa riga è $ 0, perché la prenotazione copre tutti i costi.

### <a name="detail-section-of-csv-file"></a>Sezione relativa ai dettagli nel file CSV

Applicare un filtro in **Informazioni aggiuntive** e digitare l'**ID prenotazione**. Lo screenshot mostra i campi correlati alla prenotazione di capacità riservata per il database SQL.

![File CSV per la capacità riservata del database SQL](./media/billing-understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-details.png)

1. **ReservationId** nel campo **Informazioni aggiuntive** rappresenta la prenotazione di capacità riservata del database SQL applicata alla risorsa database SQL.
2. **ConsumptionMeter** è l'ID contatore per la risorsa database SQL.
3. **ID contatore** è il contatore della prenotazione. Il costo di questo contatore è $ 0. Questo ID contatore è presente nel file CSV per qualsiasi risorsa database SQL idonea per lo sconto sulla prenotazione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md)
- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pagare in anticipo le risorse di calcolo del database SQL con capacità riservata del database SQL di Azure](../sql-database/sql-database-reserved-capacity.md)
- [Gestire le prenotazioni di Azure](billing-manage-reserved-vm-instance.md)
- [Informazioni su come viene applicato lo sconto sulla prenotazione](billing-understand-vm-reservation-charges.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Costi del software Windows non inclusi nelle prenotazioni](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).


