---
title: Istanze riservate di VM per Azure EA
description: Questo articolo descrive come risparmiare sui costi della registrazione Enterprise con le prenotazioni di Azure di istanze riservate di VM.
author: bandersmsft
ms.author: banders
ms.date: 08/14/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: 0d707d098eee91136d9e66a826705003e15c6f2a
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2020
ms.locfileid: "88244075"
---
# <a name="azure-ea-vm-reserved-instances"></a>Istanze riservate di VM per Azure EA

Questo articolo descrive come risparmiare sui costi della registrazione Enterprise con le prenotazioni di Azure di istanze riservate di VM. Per altre informazioni sulle prenotazioni, vedere [Informazioni sulle prenotazioni di Azure](../reservations/save-compute-costs-reservations.md).

## <a name="reservation-exchanges-and-refunds"></a>Cambi e rimborsi per le prenotazioni

È possibile scambiare una prenotazione per un'altra dello stesso tipo. È anche possibile rimborsare una prenotazione, fino a 50.000 USD all'anno, se questa non è più necessaria. Per cambiare una prenotazione o richiedere un rimborso, è possibile usare il portale di Azure. Per altre informazioni, vedere [Scambi e rimborsi self-service per le prenotazioni di Azure](../reservations/exchange-and-refund-azure-reservations.md).

## <a name="reservation-costs-and-usage"></a>Costi e utilizzo delle prenotazioni

I clienti con contratto Enterprise Agreement possono visualizzare i dati relativi a costi e utilizzo nel portale di Azure e nelle API REST. Per i costi e l'utilizzo delle prenotazioni, è possibile:

- Ottenere i dati sugli acquisti di prenotazioni.
- Individuare la sottoscrizione, il gruppo di risorse o la risorsa che ha usato una prenotazione.
- Chargeback per l'uso della prenotazione.
- Calcolare il risparmio delle prenotazioni.
- Ottenere i dati di sottoutilizzo delle prenotazioni.
- Ammortizzare i costi delle prenotazioni.

Per altre informazioni sui costi e l'utilizzo delle prenotazioni, vedere [Ottenere i costi e l'utilizzo delle prenotazioni con contratto Enterprise Agreement](../reservations/understand-reserved-instance-usage-ea.md).

Per informazioni sui prezzi, vedere [Prezzi delle macchine virtuali Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) o [Prezzi delle macchine virtuali Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

## <a name="reserved-instances-api-support"></a>Supporto delle API per le istanze riservate

Usare le API di Azure per ottenere informazioni per l'organizzazione a livello di codice sulle prenotazioni di software o servizi di Azure. È ad esempio possibile usare le API per:

- Trovare prenotazioni da acquistare
- Acquistare una prenotazione
- Visualizzare le prenotazioni acquistate
- Visualizzare e gestire l'accesso alle prenotazioni
- Dividere o unire le prenotazioni
- Cambiare l'ambito delle prenotazioni

Per altre informazioni, vedere [APIs for Azure reservation automation](../reservations/reservation-apis.md) (API per l'automazione della prenotazione in Azure).

## <a name="azure-reserved-virtual-machine-instances"></a>Istanze di macchine virtuali riservate di Azure

Le istanze riservate possono ridurre i costi delle macchine virtuali fino al 72% rispetto ai prezzi con pagamento in base al consumo in tutte le macchine virtuali o fino all'82% se le istanze vengono combinate con il Vantaggio Azure Hybrid. Le istanze riservate contribuiscono migliorare la gestione di carichi di lavoro, budget e previsioni con un pagamento anticipato per un anno o tre anni. È possibile anche scambiare o annullare prenotazioni in funzione delle esigenze di business.

### <a name="how-to-buy-reserved-virtual-machine-instances"></a>Come acquistare istanze di macchine virtuali riservate

Per acquistare un'istanza di macchina virtuale riservata di Azure, un amministratore delle registrazioni Enterprise di Azure deve abilitare l'opzione di acquisto _Reserve Instance_ (Prenota istanza). L'opzione è disponibile nella sezione _Dettagli registrazione_ nella scheda _Registrazione_ di [Azure EA Portal](https://ea.azure.com/).

Dopo aver abilitato la registrazione di EA per l'aggiunta di istanze riservate, qualsiasi proprietario di account con una sottoscrizione attiva associata alla registrazione EA può acquistare un'istanza di macchina virtuale riservata nel [portale di Azure](https://aka.ms/reservations). Per altre informazioni, vedere [Pagare in anticipo le macchine virtuali e risparmiare sui costi tramite le istanze di macchina virtuale riservate](https://go.microsoft.com/fwlink/?linkid=861721).

### <a name="how-to-view-reserved-instance-purchase-details"></a>Come visualizzare i dettagli di acquisto di un'istanza riservata

È possibile visualizzare i dettagli di acquisto di un'istanza riservata tramite il menu _Prenotazioni_ sul lato sinistro del [portale di Azure](https://aka.ms/reservations) o dal [portale di Azure EA](https://ea.azure.com/). Selezionare **Report** nel menu a sinistra e scorrere verso il basso fino alla sezione _Charges by Services_ (Addebiti per servizi) della scheda _Riepilogo utilizzo_. Scorrere fino alla fine della sezione, dove vengono elencati gli acquisti di istanze riservate con relativo utilizzo, in base a quanto indicato dalla designazione `1 year` o `3 years` accanto al nome del servizio, ad esempio: `Standard_DS1_v2 eastus 1 year` o `Standard_D2s_v3 eastus2 3 years`.

### <a name="how-can-i-change-the-subscription-associated-with-reserved-instance-or-transfer-my-reserved-instance-benefits-to-a-subscription-under-the-same-account"></a>Come è possibile modificare la sottoscrizione associata a un'istanza riservata o trasferire i vantaggi di un'istanza riservata a una sottoscrizione con lo stesso account?

È possibile modificare la sottoscrizione che usufruisce dei vantaggi delle istanze riservate come segue:

- Accedere al [portale di Azure](https://aka.ms/reservations).
- Aggiornare l'ambito della sottoscrizione applicato associando una sottoscrizione diversa dello stesso account.

Per altre informazioni sulla modifica dell'ambito di una prenotazione, vedere [Cambiare l'ambito della prenotazione](../reservations/manage-reserved-vm-instance.md#change-the-reservation-scope).

### <a name="how-to-view-reserved-instance-usage-details"></a>Come visualizzare i dettagli di utilizzo di un'istanza riservata

È possibile visualizzare i dettagli di utilizzo di un'istanza riservata nel [portale di Azure](https://aka.ms/reservations) o nel [portale di Azure EA](https://ea.azure.com/) (per i clienti di EA che hanno accesso alle informazioni di fatturazione) in _Report_ > _Riepilogo di utilizzo_ > _Charges by Services_ (Addebiti per servizi). Le istanze riservate possono essere identificate come nomi di servizio contenenti il termine "Reservation", ad esempio: `Reservation-Base VM or Virtual Machines Reservation-Windows Svr (1 Core)`.

Il file CSV di download con il report avanzato e i dettagli di utilizzo contiene informazioni aggiuntive sull'utilizzo delle istanze riservate. Il campo _Informazioni aggiuntive_ consente di identificare l'utilizzo effettivo di istanze riservate.

Se non è stato usato il Vantaggio Azure Hybrid per l'acquisto di istanze di macchine virtuali riservate di Azure, le istanze riservate genereranno due contatori (hardware e software). Quando si usa il Vantaggio Azure Hybrid per l'acquisto di un'istanza riservata, nei dettagli di utilizzo dell'istanza riservata non verrà visualizzato il contatore software.

### <a name="reserved-instance-billing"></a>Fatturazione di un'istanza riservata

Per i clienti Enterprise, il pagamento anticipato di Azure si usa per acquistare istanze riservate di VM di Azure. Se la registrazione ha un saldo di pagamento anticipato di Azure sufficiente a coprire l'acquisto di un'istanza riservata, l'importo verrà sottratto da tale saldo e non si riceverà una fattura per l'acquisto.

Negli scenari in cui i clienti con contratto Enterprise di Azure hanno usato tutto l'importo del pagamento anticipato, è comunque possibile acquistare istanze riservate e tali acquisti verranno addebitati nella fattura successiva per l'eccedenza. Eventuali eccedenze di istanze riservate saranno inserite nella normale fattura per l'eccedenza.

### <a name="reserved-instance-expiration"></a>Scadenza di un'istanza riservata

Si riceveranno notifiche tramite posta elettronica 30 giorni prima della prenotazione e alla scadenza. Al termine della prenotazione, le macchine virtuali distribuite continueranno a essere eseguite e fatturate alla tariffa con pagamento in base al consumo. Per altre informazioni, vedere l'articolo relativo all'[offerta delle istanze di macchina virtuale riservate](https://azure.microsoft.com/pricing/reserved-vm-instances/).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle prenotazioni di Azure, vedere [Informazioni sulle prenotazioni di Azure](../reservations/save-compute-costs-reservations.md).
- Per altre informazioni sui costi e l'utilizzo delle prenotazioni Enterprise, vedere [Ottenere i costi e l'utilizzo delle prenotazioni con contratto Enterprise Agreement](../reservations/understand-reserved-instance-usage-ea.md).
- Per informazioni sui prezzi, vedere [Prezzi delle macchine virtuali Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) o [Prezzi delle macchine virtuali Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).
