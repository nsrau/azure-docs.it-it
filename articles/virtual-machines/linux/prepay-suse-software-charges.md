---
title: Pagare in anticipo per i piani software - prenotazioni di Azure
description: Informazioni su come è possibile pagare in anticipo per i piani software a risparmiare denaro rispetto ai costi con pagamento a consumo.
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: c8e9d07bf01536d7865bd79f667a937037d96837
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67489891"
---
# <a name="prepay-for-azure-software-plans"></a>Pagare in anticipo i piani software di Azure

Quando il pagamento anticipato per l'utilizzo del software SUSE e RedHat in Azure, è possibile risparmiare denaro tramite i costi con pagamento a consumo. Sconti si applicano solo ai contatori di SUSE e RedHat e non sull'utilizzo della macchina virtuale. È possibile acquistare prenotazioni per le macchine virtuali separatamente per ottenere risparmi aggiuntivi.

È possibile acquistare piani software SUSE e RedHat nel portale di Azure. Per acquistare un piano:

- È necessario il ruolo di proprietario per almeno un Enterprise o singola sottoscrizione con pagamento a consumo.
- Per le sottoscrizioni Enterprise, il **aggiungere le istanze riservate** opzione deve essere abilitata nel [portale EA](https://ea.azure.com/). Se l'impostazione è disabilitata, è necessario essere un amministratore EA per la sottoscrizione.
- Per il programma Cloud Solution Provider (CSP), è possono acquistare i piani di software di agenti di amministrazione o gli agenti di vendita.

## <a name="buy-a-software-plan"></a>Acquista un piano software

1. Accedere al portale di Azure e passare a [prenotazioni](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
2. Fare clic su **Add** e quindi selezionare il piano software che si vuole acquistare.
Compilare i campi obbligatori. Qualsiasi VM SUSE Linux o VM RedHat che corrisponde agli attributi dei quali si acquistano Ottiene lo sconto. Il numero effettivo di distribuzioni a cui viene applicato lo sconto dipende dall'ambito e dalla quantità selezionati.
3. Selezionare una sottoscrizione. Si è usato per pagare il piano.
Il metodo di pagamento della sottoscrizione viene addebitato il costo iniziale per la prenotazione. Il tipo di sottoscrizione deve essere un contratto Enterprise Agreement (numeri dell'offerta: MS-AZR-0017P o MS-AZR - 0148p) o un singolo contratto prezzi con pagamento a consumo (numeri dell'offerta: MS-AZR-0003P o MS-AZR-0023P).
    - Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto.
    - Un singolo abbonamento con prezzi con pagamento a consumo, il costo viene addebitato al metodo di pagamento della fattura o carta di credito della sottoscrizione.
4. Selezionare un ambito. L'ambito può coprire una o più sottoscrizioni (ambito condiviso).
    - Sottoscrizione singola: lo sconto di piano viene applicata ai corrispondenti sull'utilizzo della sottoscrizione.
    - Condiviso - viene applicato lo sconto di piano per far corrispondere le istanze in tutte le sottoscrizioni nel contesto di fatturazione. Per i clienti aziendali, il contesto di fatturazione è la registrazione e include tutte le sottoscrizioni nell'ambito della registrazione. Per i clienti dei prezzi con pagamento a consumo per singolo piano, il contesto di fatturazione è tutti i piani di singole con le sottoscrizioni di prezzi con pagamento a consumo create dall'amministratore dell'account.
5. Selezionare un prodotto per scegliere le dimensioni VM e il tipo di immagine. Lo sconto si applica solo le dimensioni della macchina virtuale selezionata.
6. Selezionare un termine di un anno o tre anni.
7. Scegliere una quantità, ovvero il numero di istanze di macchina virtuale prepagate che è possibile ottenere lo sconto sulla fatturazione.
8. Aggiungere il prodotto al carrello, la revisione e acquisto.

Lo sconto della prenotazione viene applicato automaticamente per la misurazione del software che effettua un pagamento anticipato per. I costi di calcolo di macchine Virtuali non sono coperti dal piano. È possibile acquistare separatamente le prenotazioni di macchina virtuale.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>Viene applicato uno sconto per diverse dimensioni delle VM SUSE

Ad esempio istanze di VM riservate, piani di SUSE Linux offrono flessibilità le dimensioni dell'istanza. Lo sconto si applica anche quando si distribuisce una macchina virtuale che è una dimensione diversa dal piano di SUSE acquistate. Per altre informazioni, vedere [informazioni su come viene applicato lo sconto relativo piano software](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="redhat-plan-discount"></a>Sconto del piano di RedHat

I piani sono disponibili solo per le macchine virtuali Red Hat Enterprise Linux. Lo sconto non si applica a macchine virtuali Red Hat Enterprise Linux SAP HANA o macchine virtuali di Red Hat Enterprise Linux SAP Business Apps.

Gli sconti piano RedHat valgono solo per le dimensioni VM scelto al momento dell'acquisto. I piani di RHEL non possono essere rimborsati o scambiati dopo l'acquisto.


## <a name="cancellation-and-exchanges-not-allowed"></a>Annullamento e scambi non consentiti

Non è possibile annullare o lo scambio di un piano di SUSE o Red Hat acquistate. Controllare l'utilizzo per assicurarsi di acquistare il piano giusto. Per altre informazioni identificare gli elementi da acquistare, vedere [informazioni su come viene applicato lo sconto relativo piano software](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come gestire una prenotazione, vedere [Gestire le prenotazioni di Azure](../../billing/billing-manage-reserved-vm-instance.md).

Per altre informazioni, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](../../billing/billing-save-compute-costs-reservations.md)
- [Gestire le prenotazioni in Azure](../../billing/billing-manage-reserved-vm-instance.md)
- [Understand how the SUSE reservation discount is applied](../../billing/billing-understand-suse-reservation-charges.md) (Informazioni su come viene applicato lo sconto sulla prenotazione SUSE)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](../../billing/billing-understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](../../billing/billing-understand-reserved-instance-usage-ea.md)
