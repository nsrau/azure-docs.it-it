---
title: Pagamento anticipato per il calcolo con capacità riservata-cache di Azure per Redis
description: Pagamento anticipato per la cache di Azure per le risorse di calcolo Redis con capacità riservata
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: c4ea8db464c7cae57b80837ced9f8886a8b3cf4a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87093018"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>Pagamento anticipato per la cache di Azure per le risorse di calcolo Redis con capacità riservata

Cache di Azure per Redis consente ora di risparmiare denaro prepagando le risorse di calcolo rispetto ai prezzi con pagamento in base al consumo. Con la cache di Azure per la capacità riservata di redis, è possibile creare un impegno iniziale nella cache per un periodo di uno o tre anni per ottenere uno sconto significativo sui costi di calcolo. Per acquistare cache di Azure per la capacità riservata di redis, è necessario specificare l'area di Azure, il livello di servizio e il termine.

Non è necessario assegnare la prenotazione a una cache di Azure specifica per le istanze di Redis. Una cache di Azure già in esecuzione per Redis o uno appena distribuito otterrà automaticamente il vantaggio dei prezzi riservati, fino alla dimensione della cache riservata. Tramite l'acquisto di una prenotazione, si pagano in anticipo i costi di calcolo per un periodo di uno o tre anni. Non appena si acquista una prenotazione, la cache di Azure per i costi di calcolo di redis che corrispondono agli attributi di prenotazione non viene più addebitata in base alle tariffe a consumo. Una prenotazione non copre i prezzi di rete o di archiviazione associati alla cache. Al termine del periodo di prenotazione, il vantaggio di fatturazione scade e la cache di Azure per Redis viene fatturata in base al prezzo con pagamento in base al consumo. Le prenotazioni non vengono rinnovate automaticamente. Per informazioni sui prezzi, vedere l' [offerta di capacità riservata di cache di Azure per Redis](https://azure.microsoft.com/pricing/details/cache).

È possibile acquistare cache di Azure per la capacità riservata di redis nella [portale di Azure](https://portal.azure.com/). Per acquistare la capacità riservata:

* È necessario avere il ruolo di proprietario per almeno una sottoscrizione Enterprise o singola con tariffe con pagamento in base al consumo.
* Per le sottoscrizioni Enterprise, **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com/). In alternativa, se tale impostazione è disabilitata, è necessario essere un amministratore della sottoscrizione con contratto Enterprise.
* Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare cache di Azure per la capacità riservata di Redis.

Per informazioni dettagliate sul modo in cui i clienti aziendali e i clienti con pagamento in base al consumo vengono addebitati sugli acquisti di prenotazioni, vedere informazioni [sull'utilizzo della prenotazione di Azure per la registrazione Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) e [informazioni sull'utilizzo della prenotazione di Azure per la sottoscrizione con pagamento in base al consumo](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage).


## <a name="determine-the-right-cache-size-before-purchase"></a>Determinare le dimensioni corrette della cache prima dell'acquisto

Le dimensioni della prenotazione devono essere basate sulla quantità totale di memoria utilizzata dalla cache esistente o presto distribuita in un'area specifica e utilizzando lo stesso livello di servizio.

Si supponga, ad esempio, di eseguire due cache, una a 13 GB e l'altra a 26 GB. Sono necessari entrambi per almeno un anno. Si supponga inoltre di voler pianificare la scalabilità delle cache da 13 GB esistenti a 26 GB per un mese per soddisfare la richiesta stagionale, quindi eseguire il ridimensionamento. In questo caso, è possibile acquistare una cache P2 e una cache di 1 P3 o 3 P2 in una prenotazione di un anno per ottimizzare il risparmio. Si riceverà uno sconto sulla quantità totale di memoria cache riservata, indipendentemente dal modo in cui tale quantità viene allocata tra le cache.


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>Acquistare la cache di Azure per la capacità riservata di redis

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare **Tutti i servizi** > **Prenotazioni**.
3. Selezionare **Aggiungi** e quindi nel riquadro prenotazioni acquisti selezionare cache di **Azure per Redis** per acquistare una nuova prenotazione per le cache.
4. Compilare i campi obbligatori. I database nuovi o esistenti che corrispondono agli attributi selezionati sono idonei per ottenere lo sconto per la capacità riservata. Il numero effettivo della cache di Azure per le istanze di redis che ottengono lo sconto dipendono dall'ambito e dalla quantità selezionati.


![Panoramica dei prezzi riservati](media/cache-reserved-pricing/cache-reserved-price.png)


Nella tabella seguente vengono descritti i campi obbligatori.

| Campo | Descrizione |
| :------------ | :------- |
| Subscription   | Sottoscrizione usata per pagare la riserva della capacità riservata di Azure per Redis. Il metodo di pagamento per la sottoscrizione viene addebitato ai costi iniziali per la prenotazione della capacità riservata di cache di Azure per Redis. Il tipo di sottoscrizione deve essere un contratto Enterprise Agreement (numero offerta: MS-AZR-0017P o MS-AZR-0148P) o un contratto singolo con prezzi con pagamento in base al consumo (numeri di offerta: MS-AZR-0003P o MS-AZR-0023P). Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto. Per una singola sottoscrizione con prezzi con pagamento in base al consumo, i costi vengono addebitati sulla carta di credito o sul metodo di pagamento della fattura per la sottoscrizione.
| Scope | L'ambito della prenotazione può coprire una o più sottoscrizioni (ambito condiviso). Se si seleziona: </br></br> **Condiviso**, lo sconto relativo alla prenotazione viene applicato alla cache di Azure per le istanze di redis in esecuzione in tutte le sottoscrizioni all'interno del contesto di fatturazione. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni all'interno della registrazione. Per i clienti con pagamento in base al consumo, l'ambito condiviso copre tutte le sottoscrizioni con pagamento in base al consumo create dall'amministratore dell'account.</br></br> **Sottoscrizione singola**, lo sconto relativo alla prenotazione viene applicato alla cache di Azure per le istanze di redis in questa sottoscrizione. </br></br> **Gruppo di risorse singolo**, lo sconto relativo alla prenotazione viene applicato alla cache di Azure per le istanze di redis nella sottoscrizione selezionata e nel gruppo di risorse selezionato all'interno di tale sottoscrizione.
| Region | Area di Azure coperta dalla cache di Azure per la prenotazione di capacità riservata di Redis.
| Piano tariffario | Livello di servizio per la cache di Azure per i server Redis.
| Termine | Un anno o tre anni
| Quantità | Quantità di risorse di calcolo acquistate nella cache di Azure per la prenotazione di capacità riservata di Redis. La quantità è un numero di cache nell'area di Azure selezionata e il livello di servizio che vengono riservati e otterranno lo sconto per la fatturazione. Se ad esempio si esegue o si prevede di eseguire una cache di Azure per i server Redis con la capacità totale della cache di 26 GB nell'area Stati Uniti orientali, è necessario specificare la quantità che offre l'equivalente di 26 GB per massimizzare il vantaggio per tutte le cache. Potrebbe trattarsi di una cache di 1 P3 o 2 P2.

## <a name="cancel-exchange-or-refund-reservations"></a>Annullare o scambiare le prenotazioni oppure chiedere il rimborso

È possibile annullare o scambiare le prenotazioni oppure chiederne il rimborso con determinate limitazioni. Per altre informazioni, vedere [Scambi e rimborsi self-service per le prenotazioni di Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="cache-size-flexibility"></a>Flessibilità della dimensione della cache

La flessibilità della dimensione della cache consente di aumentare o ridurre le prestazioni all'interno di un livello di servizio e di un'area, senza perdere il vantaggio della capacità riservata.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

Lo sconto relativo alla prenotazione viene applicato automaticamente alla cache di Azure per le istanze di redis che corrispondono agli attributi e all'ambito di prenotazione. È possibile aggiornare l'ambito della prenotazione tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure o l'API.

*  Per informazioni sul modo in cui vengono applicati gli sconti sulla capacità riservata alla cache di Azure per Redis, vedere [informazioni sullo sconto per la prenotazione di Azure](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md)

* Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

    * [Informazioni sulle prenotazioni di Azure](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
    * [Gestire le prenotazioni di Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
    * [Comprendere lo sconto sulle prenotazioni di Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
    * [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
    * [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
    * [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) del Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations)

