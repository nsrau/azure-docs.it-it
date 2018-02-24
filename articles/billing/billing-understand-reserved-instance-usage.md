---
title: Informazioni su Utilizzo istanze riservate di Azure per la sottoscrizione con pagamento in base al consumo | Microsoft Docs
description: Informazioni su come leggere l'utilizzo per comprendere l'applicazione di Istanza riservata per la sottoscrizione con pagamento in base al consumo
services: billing
documentationcenter: 
author: manish-shukla01
manager: manshuk
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: manshuk
ms.openlocfilehash: 29f153803d5eb74e2d287d97cf9436e81b2a3e20
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="understand-reserved-instance-usage-for-your-pay-as-you-go-subscription"></a>Informazioni su Utilizzo istanze riservate per la sottoscrizione con pagamento in base al consumo

Per informazioni sull'utilizzo di Istanza riservata, usare ReservationId della [pagina Prenotazione](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade ) e il file di utilizzo del [portale Account di Azure](https://account.azure.com).


>[!NOTE]
>Questo articolo non si applica ai clienti con Contratto Enterprise. I clienti con Contratto Enterprise possono vedere l'articolo [Informazioni su Utilizzo istanze riservate per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md). Questo articolo presuppone anche che la prenotazione venga applicata a una sottoscrizione singola. Se la prenotazione viene applicata a più di una sottoscrizione, il vantaggio della prenotazione può estendersi a più file CSV di utilizzo. 

Per la sezione seguente, si presupponga di eseguire una VM Windows Standard_DS1_v2 nell'area Stati Uniti orientali e che le informazioni sulla prenotazione siano simili a quelle della tabella seguente:

| Campo | Valore |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Quantità |1|
|SKU | Standard_DS1_v2|
|Region | eastus |

## <a name="reservation-application"></a>Applicazione della prenotazione

La parte hardware della VM è coperta perché la VM distribuita corrisponde agli attributi della prenotazione. Per sapere quale software Windows non è coperto da Istanza riservata, andare a [Costi del software Windows con le istanze di macchina virtuale riservate di Azure](billing-reserved-instance-windows-software-costs.md).

### <a name="statement-section-of-csv"></a>Sezione relativa al rendiconto nel CSV
Questa sezione del file CSV indica l'utilizzo totale della prenotazione. Nel campo Sottocategoria misuratore filtrare le voci contenenti "Istanze riservate di" per ottenere dati simili a quelli dello screenshot seguente: ![Prenotazione rendiconto diretto](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

La riga Istanze riservate di VM di base indica il numero totale di ore coperte dalla prenotazione. Questa riga contiene il valore $ 0,00 perché è coperta da Istanza riservata. La riga Istanze riservate di Windows Server (1 core) indica il costo del software Windows.

### <a name="daily-usage-section-of-csv"></a>Sezione Utilizzo giornaliero nel CSV
Filtrare per informazioni aggiuntive e tipo nell'ID prenotazione. Lo screenshot seguente illustra i campi correlati alla prenotazione. 

![Addebiti di utilizzo giornaliero](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. ReservationId nel campo Informazioni aggiuntive è la prenotazione usata per applicare il vantaggio alla VM.
2. ConsumptionMeter è l'ID contatore per la VM.
3. La riga Istanze riservate di VM di base in Sottocategoria misuratore rappresenta la riga del costo pari a $ 0 nella sezione del rendiconto. Il costo dell'esecuzione di questa VM è già coperto dalla prenotazione.
4. Questo è l'ID contatore per la prenotazione. Il costo di questo contatore è pari a $ 0. Le VM qualificate per Istanza riservata hanno questo ID contatore nel CSV per tenere conto del costo. 
5. Standard_DS1_v2 è una VM con vCPU che viene distribuita senza il vantaggio Azure Hybrid. Questo contatore copre quindi i costi aggiuntivi del software Windows. Vedere [Costi del software Windows per le istanze di macchina virtuale riservata di Azure](billing-reserved-instance-windows-software-costs.md) per trovare il contatore corrispondente alla VM con 1 core serie D. Se si usa il vantaggio Azure Hybrid, questo costo aggiuntivo non viene applicato. 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle istanze di macchine virtuali riservate, vedere gli articoli seguenti.

- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gestire le istanze di macchina virtuale riservate](billing-manage-reserved-vm-instance.md)
- [Risparmiare sui costi delle macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](billing-save-compute-costs-reservations.md)
- [Informazioni su come viene applicato lo sconto relativo alle istanze di macchine virtuali riservate](billing-understand-vm-reservation-charges.md)
- [Informazioni su Utilizzo istanze riservate per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Costi del software Windows non inclusi nelle istanze riservate](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.

Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.