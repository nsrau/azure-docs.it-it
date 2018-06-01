---
title: Informazioni su Utilizzo istanze riservate di Azure per Enterprise - Fatturazione di Azure | Microsoft Docs
description: Informazioni su come leggere l'utilizzo per comprendere come viene applicata l'istanza di macchina virtuale riservata di Azure per l'iscrizione Enterprise.
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
ms.openlocfilehash: a92fce33b194c5cb7b763930e7fd11135f9fbd4f
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301315"
---
# <a name="understand-azure-reserved-instance-usage-for-your-enterprise-enrollment"></a>Informazioni su Utilizzo istanze riservate di Azure per l'iscrizione Enterprise
Per informazioni sull'utilizzo dell'istanza riservata, usare **ReservationId** della [pagina Prenotazione](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) e il file di utilizzo del [portale EA](https://ea.azure.com). È possibile anche visualizzare l'utilizzo delle istanze riservate nella sezione di riepilogo dell'utilizzo del [portale EA](https://ea.azure.com).

>[!NOTE]
>Se si è acquistata l'istanza riservata in un contesto di fatturazione con pagamento in base al consumo, vedere [Informazioni su Utilizzo istanze riservate per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md).

Per la sezione seguente, si presupponga di eseguire una VM Windows Standard_D1_v2 nell'area Stati Uniti orientali e che le informazioni sull'istanza riservata siano simili a quelle della tabella seguente:

| Campo | Valore |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Quantità |1|
|SKU | Standard_D1|
|Region | eastus |

## <a name="reserved-instance-application"></a>Applicazione dell'istanza riservata

La parte hardware della macchina virtuale è coperta perché la VM distribuita corrisponde agli attributi dell'istanza riservata. Per sapere quale software Windows non è coperto da Istanza riservata, andare a [Costi del software Windows con le istanze di macchina virtuale riservate di Azure](billing-reserved-instance-windows-software-costs.md).


### <a name="reserved-instance-usage-in-csv"></a>Utilizzo istanze riservate in formato CSV
È possibile scaricare il CSV sull'utilizzo di EA dal portale EA. Nel file CSV scaricato filtrare per informazioni aggiuntive e tipo in **ReservationID**. Lo screenshot seguente illustra i campi correlati all'istanza riservata:

![File in formato CSV del Contratto Enterprise (EA) per l'istanza riservata di Azure](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. **ReservationId** nel campo Informazioni aggiuntive rappresenta l'istanza riservata usata per applicare il vantaggio alla VM.
2. ConsumptionMeter è l'ID contatore per la VM.
3. Si tratta del valore ReservationMeter con costo pari a $ 0 perché il costo di esecuzione della VM è già coperto dall'istanza riservata. 
4. Standard_D1 è una VM con vCPU e la VM viene distribuita senza Vantaggio Azure Hybrid. Questo contatore copre quindi i costi aggiuntivi del software Windows. Vedere [Costi del software Windows per le istanze di macchina virtuale riservata di Azure](billing-reserved-instance-windows-software-costs.md) per trovare il contatore corrispondente alla VM con 1 core serie D. Se si usa il vantaggio Azure Hybrid, questo costo aggiuntivo non verrà applicato.

### <a name="reserved-instance-usage-in-usage-summary-page-in-ea-portal"></a>Utilizzo istanze riservate nella pagina di riepilogo dell'utilizzo nel portale EA

Utilizzo istanze riservate viene visualizzato anche nella sezione di riepilogo dell'utilizzo nel portale EA: ![Riepilogo dell'utilizzo Enterprise Agreement (EA)](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. Il componente hardware della VM non viene addebitato perché è coperto da Istanza riservata. 
2. Il software Windows viene addebitato perché Vantaggio Azure Hybrid non viene usato. 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle istanze riservate di Azure, vedere gli articoli seguenti:

- [Risparmiare sui costi delle macchine virtuali tramite le istanze riservate di Azure](billing-save-compute-costs-reservations.md)
- [Pagare in anticipo le macchine virtuali tramite le istanze riservate](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gestire le istanze riservate](billing-manage-reserved-vm-instance.md)
- [Informazioni su come viene applicato lo sconto relativo alle istanze riservate](billing-understand-vm-reservation-charges.md)
- [Informazioni su Utilizzo istanze riservate per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
- [Costi del software Windows non inclusi nelle istanze riservate](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.