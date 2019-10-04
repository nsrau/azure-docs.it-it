---
title: Pagamento anticipato per i piani Software-prenotazioni di Azure
description: Scopri in che modo puoi pagare in anticipo i piani software per risparmiare denaro rispetto ai costi con pagamento in base al consumo.
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: e4cab8963e3083d265d0254c81b78fbabb84a269
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091531"
---
# <a name="prepay-for-azure-software-plans"></a>Pagare in anticipo i piani software di Azure

Quando si usa il pagamento anticipato per l'utilizzo del software SUSE e RedHat in Azure, è possibile risparmiare sui costi con pagamento in base al consumo. Gli sconti si applicano solo ai contatori SUSE e RedHat e non all'utilizzo della macchina virtuale. È possibile acquistare separatamente le prenotazioni per le macchine virtuali per ottenere risparmi aggiuntivi.

È possibile acquistare piani software SUSE e RedHat nell'portale di Azure. Per acquistare un piano:

- È necessario avere il ruolo proprietario per almeno una sottoscrizione Enterprise o singola con prezzi con pagamento in base al consumo.
- Per le sottoscrizioni aziendali, l'opzione **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com/). Se l'impostazione è disabilitata, è necessario essere un amministratore EA per la sottoscrizione.
- Per il programma Cloud Solution Provider (CSP), gli agenti di amministrazione o gli agenti di vendita possono acquistare i piani software.

## <a name="buy-a-software-plan"></a>Acquistare un piano software

1. Accedere al portale di Azure e passare a [prenotazioni](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
2. Fare clic su **Aggiungi** e quindi selezionare il piano software che si desidera acquistare.
Compilare i campi obbligatori. Qualsiasi VM SUSE Linux o macchina virtuale RedHat che corrisponde agli attributi di ciò che si acquista ottiene lo sconto. Il numero effettivo di distribuzioni a cui viene applicato lo sconto dipende dall'ambito e dalla quantità selezionati.
3. Selezionare una sottoscrizione. Viene usato per pagare il piano.
Il metodo di pagamento della sottoscrizione viene addebitato ai costi iniziali per la prenotazione. Il tipo di sottoscrizione deve essere un Enterprise Agreement (numeri di offerta: MS-AZR-0017P o MS-AZR-0148P) o contratto singolo con prezzi con pagamento in base al consumo (numeri di offerta: MS-AZR-0003P o MS-AZR-0023P).
    - Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto.
    - Per una singola sottoscrizione con prezzi con pagamento in base al consumo, i costi vengono addebitati sul metodo di pagamento con carta di credito o fatturazione della sottoscrizione.
4. Selezionare un ambito. L'ambito può coprire una o più sottoscrizioni (ambito condiviso).
    - Singola sottoscrizione: lo sconto del piano viene applicato all'utilizzo corrispondente nella sottoscrizione.
    - Condiviso: lo sconto del piano viene applicato alle istanze corrispondenti in qualsiasi sottoscrizione nel contesto di fatturazione. Per i clienti aziendali, il contesto di fatturazione è la registrazione e include tutte le sottoscrizioni nella registrazione. Per i singoli piani con i clienti con pagamento in base al consumo, il contesto di fatturazione è costituito da tutti i singoli piani con le sottoscrizioni dei prezzi con pagamento in base al consumo create dall'amministratore account.
5. Selezionare un prodotto per scegliere le dimensioni della macchina virtuale e il tipo di immagine. Lo sconto si applica solo alle dimensioni della macchina virtuale selezionata.
6. Selezionare un termine di un anno o di tre anni.
7. Scegliere una quantità, ovvero il numero di istanze di VM prepagate che possono ottenere lo sconto per la fatturazione.
8. Aggiungere il prodotto al carrello, rivedere e acquistare.

Lo sconto relativo alla prenotazione viene applicato automaticamente al contatore del software che si sta prepagando. Gli addebiti per il calcolo della macchina virtuale non sono coperti dal piano. È possibile acquistare le prenotazioni della macchina virtuale separatamente.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>Lo sconto si applica a diverse dimensioni di VM SUSE

Come le istanze di VM riservate, i piani SUSE Linux offrono la flessibilità delle dimensioni delle istanze. Lo sconto si applica anche quando si distribuisce una macchina virtuale di dimensioni diverse dal piano SUSE acquistato. Per altre informazioni, vedere [comprendere come viene applicato lo sconto del piano software](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="redhat-plan-discount"></a>Sconto piano RedHat

I piani sono disponibili solo per Red Hat Enterprise Linux macchine virtuali. Lo sconto non si applica alle macchine virtuali RedHat Enterprise Linux SAP HANA o RedHat Enterprise Linux SAP Business App.

Gli sconti del piano RedHat si applicano solo alle dimensioni della macchina virtuale selezionate al momento dell'acquisto. Non è possibile rimborsare i piani RHEL o scambiarli dopo l'acquisto.


## <a name="cancellation-and-exchanges-not-allowed"></a>Annullamento e scambi non consentiti

Non è possibile annullare o scambiare un piano SUSE o RedHat acquistato. Controllare l'utilizzo per assicurarsi di acquistare il piano giusto. Per informazioni su come identificare gli elementi da acquistare, vedere [informazioni su come viene applicato lo sconto del piano software](../../billing/billing-understand-suse-reservation-charges.md).

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
