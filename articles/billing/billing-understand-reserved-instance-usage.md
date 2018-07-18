---
title: Informazioni su Utilizzo istanze riservate di Azure per la sottoscrizione con pagamento in base al consumo | Microsoft Docs
description: Informazioni su come leggere l'utilizzo per comprendere come viene applicata l'istanza di macchina virtuale riservata di Azure per la sottoscrizione con pagamento in base al consumo.
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
ms.date: 05/09/2018
ms.author: manshuk
ms.openlocfilehash: 7e303f3e5ce0e618d941be4190f6fadb40f2e09d
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064493"
---
# <a name="understand-reserved-instance-usage-for-your-pay-as-you-go-subscription"></a>Informazioni su Utilizzo istanze riservate per la sottoscrizione con pagamento in base al consumo

Per informazioni sull'utilizzo di un'istanza di macchina virtuale riservata di Azure, usare ReservationId della [pagina Prenotazione](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) e il file di utilizzo del [portale degli account di Azure](https://account.azure.com).


>[!NOTE]
>Questo articolo non si applica ai clienti con Contratto Enterprise. I clienti con Contratto Enterprise possono vedere l'articolo [Informazioni su Utilizzo istanze riservate per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md). In questo articolo si presuppone anche che l'istanza riservata venga applicata a una sottoscrizione singola. Se l'istanza riservata viene applicata a più di una sottoscrizione, il vantaggio dell'istanza riservata può estendersi a più file CSV di utilizzo. 

Per la sezione seguente, si presupponga di eseguire una macchina virtuale Windows Standard_DS1_v2 nell'area Stati Uniti orientali e che le informazioni sull'istanza riservata siano simili a quelle della tabella seguente:

| Campo | Valore |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Quantità |1|
|SKU | Standard_DS1_v2|
|Region | eastus |

## <a name="reserved-instance-application"></a>Applicazione dell'istanza riservata

La parte hardware della macchina virtuale è coperta perché la VM distribuita corrisponde agli attributi dell'istanza riservata. Per sapere quale software Windows non è coperto da Istanza riservata, andare a [Costi del software Windows con le istanze di macchina virtuale riservate di Azure](billing-reserved-instance-windows-software-costs.md).

### <a name="statement-section-of-csv"></a>Sezione relativa al rendiconto nel CSV
Questa sezione del file CSV indica l'utilizzo totale per l'istanza riservata. Nel campo Sottocategoria misuratore filtrare le voci contenenti "Istanze riservate di" per ottenere dati simili a quelli nello screenshot seguente: ![Screenshot dei dettagli sull'utilizzo e gli addebiti per l'istanza riservata filtrata](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

La riga Istanze riservate di VM di base indica il numero totale di ore coperte dall'istanza riservata. Questa riga contiene il valore $ 0,00 perché è coperta dall'istanza riservata. La riga Istanze riservate di Windows Server (1 core) indica il costo del software Windows.

### <a name="daily-usage-section-of-csv"></a>Sezione Utilizzo giornaliero nel CSV
Filtrare per informazioni aggiuntive e tipo nell'**ID prenotazione**. Lo screenshot seguente illustra i campi correlati all'istanza riservata. 

![Screenshot dei dettagli di utilizzo giornaliero e degli addebiti](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. **ReservationId** nel campo Informazioni aggiuntive è l'istanza riservata usata per applicare il vantaggio alla macchina virtuale.
2. ConsumptionMeter è l'ID contatore per la VM.
3. La riga Istanze riservate di VM di base in Sottocategoria misuratore rappresenta la riga del costo pari a $ 0 nella sezione del rendiconto. Il costo dell'esecuzione di questa VM è già coperto dall'istanza riservata.
4. Questo è l'ID contatore per l'istanza riservata. Il costo di questo contatore è pari a $ 0. Le VM qualificate per l'istanza riservata hanno questo ID contatore nel CSV per tenere conto del costo. 
5. Standard_DS1_v2 è una VM con vCPU che viene distribuita senza il vantaggio Azure Hybrid. Questo contatore copre quindi i costi aggiuntivi del software Windows. Vedere [Costi del software Windows per le istanze di macchina virtuale riservata di Azure](billing-reserved-instance-windows-software-costs.md) per trovare il contatore corrispondente alla VM con 1 core serie D. Se si usa il vantaggio Azure Hybrid, questo costo aggiuntivo non viene applicato. 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle istanze riservate, vedere gli articoli seguenti:

- [Cosa sono le istanze di macchina virtuale riservate di Azure?](billing-save-compute-costs-reservations.md)
- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gestire le istanze riservate in Azure](billing-manage-reserved-vm-instance.md)
- [Informazioni su come viene applicato lo sconto per le istanze riservate](billing-understand-vm-reservation-charges.md)
- [Informazioni su Utilizzo istanze riservate per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Windows software costs not included with reserved instances](billing-reserved-instance-windows-software-costs.md) (Costi del software Windows non inclusi nelle istanze riservate)

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
