---
title: Pagamento anticipato per i piani software - Prenotazioni di AzurePrepay for software plans - Azure Reservations
description: Scopri come puoi pagare in anticipo i piani software per risparmiare sui costi con pagamento in base al tuo pay-as-you-go.
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/06/2020
ms.author: banders
ms.openlocfilehash: 3e05920e495dd4aa14be6c849590a37a2bafa33f
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757223"
---
# <a name="prepay-for-azure-software-plans"></a>Pagare in anticipo i piani software di Azure

Quando si paga in anticipo l'utilizzo del software SUSE e RedHat in Azure, è possibile risparmiare sui costi con pagamento in base al consumo. Gli sconti si applicano solo ai contatori SUSE e RedHat e non all'utilizzo della macchina virtuale. È possibile acquistare le prenotazioni per le macchine virtuali separatamente per ulteriori risparmi.

È possibile acquistare piani software SUSE e RedHat nel portale di Azure.You can buy SUSE and RedHat software plans in the Azure portal. Per acquistare un piano:

- È necessario disporre del ruolo di proprietario per almeno un'enterprise o una singola sottoscrizione con prezzi con pagamento in base al consumo.
- Per le sottoscrizioni Enterprise, l'opzione **Aggiungi istanze riservate** deve essere abilitata in [EA Portal](https://ea.azure.com/). Se l'impostazione è disabilitata, è necessario essere un amministratore EA per la sottoscrizione.
- Per il programma Cloud Solution Provider (CSP), gli agenti di amministrazione o gli agenti di vendita possono acquistare i piani software.

## <a name="buy-a-software-plan"></a>Acquistare un piano software

1. Accedere al portale di Azure e passare a [Prenotazioni](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
2. Fare clic su **Aggiungi** e quindi selezionare il piano software che si desidera acquistare.
Compilare i campi obbligatori. Qualsiasi macchina virtuale SUSE Linux o RedHat che corrisponde agli attributi di ciò che si acquista ottiene lo sconto. Il numero effettivo di distribuzioni a cui viene applicato lo sconto dipende dall'ambito e dalla quantità selezionati.
3. Selezionare una sottoscrizione. È usato per pagare il piano.
Al metodo di pagamento dell'abbonamento vengono addebitati i costi iniziali per la prenotazione. Il tipo di abbonamento deve essere un contratto Enterprise Agreement (numeri di offerta: MS-A-R-0017P o MS-A-R-0148P) o un contratto individuale con prezzi con pagamento in base al consumo (numeri di offerta: MS-A-R-0003P o MS-A-R-0023P).
    - Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto.
    - Per un abbonamento individuale con prezzi con pagamento con pagamento con pagamento in base al consumo, gli addebiti vengono addebitati sulla carta di credito o sul metodo di pagamento della fattura dell'abbonamento.
4. Selezionare un ambito. L'ambito può coprire una o più sottoscrizioni (ambito condiviso).
    - Sottoscrizione singola: lo sconto del piano viene applicato all'utilizzo corrispondente nella sottoscrizione.
    - Condiviso: lo sconto del piano viene applicato alle istanze corrispondenti in qualsiasi sottoscrizione nel contesto di fatturazione. Per i clienti aziendali, il contesto di fatturazione è la registrazione e include tutte le sottoscrizioni nella registrazione. Per i singoli piani con prezzi con pagamento in base al consumo, il contesto di fatturazione è tutto un contesto di pianificazione individuale con sottoscrizioni di determinazione dei prezzi con pagamento in base al consumo create dall'amministratore dell'account.
    - Singolo gruppo di risorse: applica lo sconto prenotazione solo alle risorse corrispondenti nel gruppo di risorse selezionato.
5. Selezionare un prodotto per scegliere le dimensioni della macchina virtuale e il tipo di immagine. Lo sconto si applica solo alle dimensioni della macchina virtuale selezionata.
6. Selezionare un periodo di un anno o di tre anni.
7. Scegliere una quantità, ovvero il numero di istanze di macchine virtuali prepagate che possono ottenere lo sconto sulla fatturazione.
8. Aggiungi il prodotto al carrello, rivedi e acquista.

Lo sconto prenotazione viene applicato automaticamente al contatore software per il pagamento anticipato. Gli addebiti per il calcolo delle macchine virtuali non sono coperti dal piano. È possibile acquistare le prenotazioni della macchina virtuale separatamente.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>Lo sconto si applica alle diverse dimensioni delle macchine virtuali SUSE

Analogamente alle istanze di macchine virtuali riservate, i piani SUSE Linux offrono flessibilità in termini di dimensioni dell'istanza. Lo sconto si applica anche quando si distribuisce una macchina virtuale di dimensioni diverse da quelle del piano SUSE acquistato. Per ulteriori informazioni, vedere [Comprendere come viene applicato lo sconto del piano software](../../cost-management-billing/reservations/understand-suse-reservation-charges.md).

## <a name="redhat-plan-discount"></a>Sconto piano RedHat

I piani sono disponibili solo per le macchine virtuali Linux Red Hat Enterprise. Lo sconto non si applica alle macchine virtuali RedHat Enterprise Linux SAP HANA o Alle macchine virtuali RedHat Enterprise Linux SAP Business Apps.

Gli sconti piano RedHat si applicano solo alle dimensioni della macchina virtuale selezionate al momento dell'acquisto. I piani RHEL non possono essere rimborsati o scambiati dopo l'acquisto.


## <a name="cancellation-and-exchanges-not-allowed"></a>Annullamento e scambi non consentiti

Non è possibile annullare o scambiare un piano SUSE o RedHat acquistato. Controllare l'utilizzo per assicurarsi di acquistare il piano giusto. Per informazioni su come identificare gli elementi da acquistare, vedere [Comprendere come viene applicato lo sconto del piano software](../../cost-management-billing/reservations/understand-suse-reservation-charges.md).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come gestire una prenotazione, vedere Gestire le prenotazioni di Azure.To learn how to manage a reservation, see [Manage Azure reservations.](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)

Per altre informazioni, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Gestire le prenotazioni in AzureManage Reservations in Azure](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Understand how the SUSE reservation discount is applied](../../cost-management-billing/reservations/understand-suse-reservation-charges.md) (Informazioni su come viene applicato lo sconto sulla prenotazione SUSE)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
