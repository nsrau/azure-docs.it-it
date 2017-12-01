---
title: Informazioni su Utilizzo istanze riservate di Azure per Enterprise | Microsoft Docs
description: Informazioni su come leggere l'utilizzo per comprendere l'applicazione di Istanza riservata per l'iscrizione Enterprise.
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
ms.openlocfilehash: 7ef601033b36ee968cb766d40a0a6b05afa9a1a4
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="understand--reserved-instance-usage-for-your-enterprise-enrollment"></a>Informazioni su Utilizzo istanze riservate per l'iscrizione Enterprise
Per informazioni sull'utilizzo di Istanza riservata, usare ReservationId della [pagina Prenotazione](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade ) e il file di utilizzo del [portale EA](https://ea.azure.com). È anche possibile visualizzare l'utilizzo della prenotazione nella sezione di riepilogo dell'utilizzo del [portale EA](https://ea.azure.com).

>[!NOTE]
>Se si è acquistata la prenotazione in un contesto di fatturazione con pagamento in base al consumo, vedere [Informazioni su Utilizzo istanze riservate per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md).

Per la sezione seguente, si presupponga di eseguire una VM Windows Standard_D1_v2 nell'area Stati Uniti orientali e che le informazioni sulla prenotazione siano simili a quelle della tabella seguente:

| Campo | Valore |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Quantità |1|
|SKU | Standard_D1|
|Region | eastus |

## <a name="reservation-application"></a>Applicazione della prenotazione

La parte hardware della VM è coperta perché la VM distribuita corrisponde agli attributi della prenotazione. Per sapere quale software Windows non è coperto da Istanza riservata, andare a [Costi del software Windows con le istanze di macchina virtuale riservate di Azure](billing-reserved-instance-windows-software-costs.md).


### <a name="reservation-usage-in-csv"></a>Utilizzo della prenotazione nel CSV
È possibile scaricare il CSV sull'utilizzo di EA dal portale EA. Nel file CSV scaricato filtrare per informazioni aggiuntive e tipo nell'ID prenotazione. Lo screenshot seguente illustra i campi correlati alla prenotazione:

![CSV EA per Istanza riservata](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. ReservationId nel campo Informazioni aggiuntive rappresenta la prenotazione usata per applicare il vantaggio alla VM.
2. ConsumptionMeter è l'ID contatore per la VM.
3. Si tratta del valore ReservationMeter con costo pari a $ 0 perché il costo di esecuzione della VM è già coperto dalla prenotazione. 
4. Standard_D1 è una VM con vCPU e la VM viene distribuita senza Vantaggio Azure Hybrid. Questo contatore copre quindi i costi aggiuntivi del software Windows. Vedere [Costi del software Windows per le istanze di macchina virtuale riservata di Azure](billing-reserved-instance-windows-software-costs.md) per trovare il contatore corrispondente alla VM con 1 core serie D. Se si usa il vantaggio Azure Hybrid, questo costo aggiuntivo non verrà applicato.

### <a name="reservation-usage-in-usage-summary-page-in-ea-portal"></a>Utilizzo della prenotazione nella pagina di riepilogo dell'utilizzo nel portale EA

Utilizzo istanze riservate viene visualizzato anche nella sezione di riepilogo dell'utilizzo del portale EA: ![Riepilogo dell'utilizzo EA](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. Il componente hardware della VM non viene addebitato perché è coperto da Istanza riservata. 
2. Il software Windows viene addebitato perché Vantaggio Azure Hybrid non viene usato. 

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.

Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.