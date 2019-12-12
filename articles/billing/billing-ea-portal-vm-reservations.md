---
title: Istanze riservate di VM per Azure EA
description: Questo articolo descrive come risparmiare sui costi della registrazione Enterprise con le prenotazioni di Azure di istanze riservate di VM.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/04/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: 3c3d95feed4f7cf47448c4fe62d8368fa5498dd7
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74841321"
---
# <a name="azure-ea-vm-reserved-instances"></a>Istanze riservate di VM per Azure EA

Questo articolo descrive come risparmiare sui costi della registrazione Enterprise con le prenotazioni di Azure di istanze riservate di VM. Per altre informazioni sulle prenotazioni, vedere [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md).

## <a name="reservation-exchanges-and-refunds"></a>Cambi e rimborsi per le prenotazioni

È possibile scambiare una prenotazione per un'altra dello stesso tipo. È anche possibile rimborsare una prenotazione, fino a 50.000 USD all'anno, se questa non è più necessaria. Per cambiare una prenotazione o richiedere un rimborso, è possibile usare il portale di Azure. Per altre informazioni, vedere [Scambi e rimborsi self-service per le prenotazioni di Azure](billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="reservation-costs-and-usage"></a>Costi e utilizzo delle prenotazioni

I clienti con contratto Enterprise Agreement possono visualizzare i dati relativi a costi e utilizzo nel portale di Azure e nelle API REST. Per i costi e l'utilizzo delle prenotazioni, è possibile:

- Ottenere i dati sugli acquisti di prenotazioni.
- Individuare la sottoscrizione, il gruppo di risorse o la risorsa che ha usato una prenotazione.
- Effettuare il chargeback per l'utilizzo delle prenotazioni.
- Calcolare il risparmio delle prenotazioni.
- Ottenere i dati di sottoutilizzo delle prenotazioni.
- Ammortizzare i costi delle prenotazioni.

Per altre informazioni sui costi e l'utilizzo delle prenotazioni, vedere [Ottenere i costi e l'utilizzo delle prenotazioni con contratto Enterprise Agreement](billing-understand-reserved-instance-usage-ea.md).

Per informazioni sui prezzi, vedere [Prezzi delle macchine virtuali Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) o [Prezzi delle macchine virtuali Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

## <a name="reserved-instances-api-support"></a>Supporto delle API per le istanze riservate

È possibile usare le API di Azure per ottenere informazioni per l'organizzazione a livello di codice sulle prenotazioni di software o servizi di Azure. Per altre informazioni, vedere [APIs for Azure reservation automation](billing-reservation-apis.md) (API per l'automazione della prenotazione in Azure).

## <a name="azure-reserved-virtual-machine-instances"></a>Istanze di macchine virtuali riservate di Azure

Le istanze riservate possono ridurre i costi delle macchine virtuali fino al 72% rispetto ai prezzi con pagamento in base al consumo in tutte le macchine virtuali o fino all'82% se le istanze vengono combinate con il vantaggio Azure Hybrid. È possibile classificare meglio in ordine di priorità i carichi di lavoro, il budget e le previsioni con il pagamento anticipato per uno o tre anni. È possibile anche scambiare o annullare prenotazioni in funzione delle esigenze di business.

### <a name="how-to-buy-reserved-virtual-machine-instances"></a>Come acquistare istanze di macchine virtuali riservate

Per acquistare un'istanza di macchina virtuale riservata di Azure, un amministratore delle registrazioni di Enterprise Azure deve abilitare l'opzione di acquisto _Reserve Instance_ (Riserva istanza) nella sezione _Dettagli registrazione_  della scheda _Registrazione_ all'interno del [portale di Azure EA](https://ea.azure.com/).

Dopo aver abilitato la registrazione di EA per l'aggiunta di istanze riservate, qualsiasi proprietario di account con una sottoscrizione attiva associata alla registrazione EA può acquistare un'istanza di macchina virtuale riservata nel [portale di Azure](https://aka.ms/reservations). Per altre informazioni, vedere [Pagare in anticipo le macchine virtuali e risparmiare sui costi tramite le istanze di macchina virtuale riservate](https://go.microsoft.com/fwlink/?linkid=861721).

### <a name="how-to-view-reserved-instance-purchase-details"></a>Come visualizzare i dettagli di acquisto di un'istanza riservata

È possibile visualizzare i dettagli di acquisto di un'istanza riservata tramite il menu _Prenotazioni_ sul lato sinistro del [portale di Azure](https://aka.ms/reservations) o dal [portale di Azure EA](https://ea.azure.com/). Selezionare **Report** nel menu a sinistra e scorrere verso il basso fino alla sezione _Charges by Services_ (Addebiti per servizi) della scheda _Riepilogo utilizzo_. Scorrere fino alla fine della sezione, dove vengono elencati gli acquisti di istanze riservate con relativo utilizzo, in base a quanto indicato dalla designazione "1 anno" o "3 anni" accanto al nome del servizio, ad esempio: Standard_DS1_v2 eastus 1 anno o Standard_D2s_v3 eastus2 3 anni.

### <a name="how-can-i-change-the-subscription-associated-with-reserved-instance-or-transfer-my-reserved-instance-benefits-to-a-subscription-under-the-same-account"></a>Come è possibile modificare la sottoscrizione associata a un'istanza riservata o trasferire i vantaggi di un'istanza riservata a una sottoscrizione con lo stesso account?

I vantaggi delle istanze riservate non possono essere conseguiti da più sottoscrizioni contemporaneamente. È possibile modificare la sottoscrizione che usufruisce dei vantaggi delle istanze riservate come segue:

- Accedere al [portale di Azure](https://aka.ms/reservations).
- Aggiornare l'ambito della sottoscrizione applicata associando una sottoscrizione diversa dello stesso account.

### <a name="how-to-view-reserved-instance-usage-details"></a>Come visualizzare i dettagli di utilizzo di un'istanza riservata

È possibile visualizzare i dettagli di utilizzo di un'istanza riservata nel [portale di Azure](https://aka.ms/reservations) o nel [portale di Azure EA](https://ea.azure.com/) (per i clienti di EA che hanno accesso alle informazioni di fatturazione) in _Report_ > _Riepilogo di utilizzo_ > _Charges by Services_ (Addebiti per servizi). Le istanze riservate possono essere identificate come nomi di servizio contenenti il termine "Reservation", ad esempio: Macchina virtuale Reservation-Base o macchine virtuali Reservation-Windows Svr (1 core).

Il file CSV di download con il report avanzato e i dettagli di utilizzo contiene informazioni aggiuntive sull'utilizzo delle istanze riservate. Il campo _Informazioni aggiuntive_ consente di identificare l'utilizzo effettivo di istanze riservate.

Se non è stato usato il vantaggio Azure Hybrid per l'acquisto di istanze di macchine virtuali riservate di Azure, le istanze riservate genereranno due contatori (hardware e software). Se invece è stato usato il vantaggio Azure Hybrid per l'acquisto di un'istanza riservata, nei dettagli di utilizzo dell'istanza riservata non verrà visualizzato il contatore software.

### <a name="reserved-instance-billing"></a>Fatturazione di un'istanza riservata

Per i clienti Enterprise, l'impegno monetario viene usato per acquistare le istanze di macchine virtuali riservate di Azure. Se la registrazione ha un saldo di impegno monetario sufficiente a coprire l'acquisto di un'istanza riservata, l'importo verrà sottratto dal saldo dell'impegno monetario personale e non si riceverà una fattura per l'acquisto.

Negli scenari in cui i clienti EA hanno usato tutto il rispettivo impegno monetario, è comunque possibile acquistare istanze riservate e tali acquisti verranno fatturati nella fattura successiva per l'eccedenza. Eventuali eccedenze di istanze riservate saranno inserite nella normale fattura per l'eccedenza.

### <a name="reserved-instance-expiration"></a>Scadenza di un'istanza riservata

Si riceveranno notifiche tramite posta elettronica 30 giorni prima della prenotazione e alla scadenza. Al termine della prenotazione, le macchine virtuali distribuite continueranno a essere eseguite e fatturate alla tariffa con pagamento in base al consumo. Per altre informazioni, vedere l'articolo relativo all'[offerta delle istanze di macchina virtuale riservate](https://azure.microsoft.com/pricing/reserved-vm-instances/).

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sulle prenotazioni di Azure, vedere [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md).
- Per altre informazioni sui costi e l'utilizzo delle prenotazioni Enterprise, vedere [Ottenere i costi e l'utilizzo delle prenotazioni con contratto Enterprise Agreement](billing-understand-reserved-instance-usage-ea.md).
- Per informazioni sui prezzi, vedere [Prezzi delle macchine virtuali Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) o [Prezzi delle macchine virtuali Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).
