---
title: Ottimizzare i costi di Azure Databricks con un preacquisto
description: Informazioni su come è possibile risparmiare pagando in anticipo gli addebiti di Azure Databricks con capacità riservata.
services: billing
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 0165da8adbb28b97d524f1a07c725716b69277d4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223645"
---
# <a name="optimize-azure-databricks-costs-with-a-pre-purchase"></a>Ottimizzare i costi di Azure Databricks con un preacquisto

È possibile risparmiare sui costi delle unità Azure Databricks preacquistando unità di impegno in Azure Databricks per uno o tre anni. Le unità di impegno in Databricks preacquistate possono essere usate in qualsiasi momento durante il periodo dell'acquisto. A differenza delle VM, le unità preacquistate non hanno scadenza oraria e sono utilizzabili in qualsiasi momento nel corso del periodo dell'acquisto.

Qualsiasi uso di Azure Databricks viene detratto automaticamente dalle unità Databricks preacquistate. Non è necessario ridistribuire o assegnare un piano preacquistato alle aree di lavoro di Azure Databricks per applicare gli sconti del preacquisto all'utilizzo delle unità Databricks.

Lo sconto del preacquisto si applica solo all'utilizzo di unità Databricks. Gli altri costi, ad esempio per il calcolo, l'elaborazione e la rete, vengono addebitati separatamente.

## <a name="determine-the-right-size-to-buy"></a>Determinare le dimensioni corrette da acquistare

Il preacquisto di Databricks si applica a tutti i carichi di lavoro e i livelli di Databricks. Il preacquisto può essere considerato come un pool di unità di impegno in Databricks prepagate. L'utilizzo viene detratto dal pool, indipendentemente dal carico di lavoro o dal livello, nel rapporto seguente:

| **Carico di lavoro** | **Rapporto di applicazione per unità Databricks - Livello Standard** | **Rapporto di applicazione per unità Databricks - Livello Premium** |
| --- | --- | --- |
| Analisi dei dati | 0,4 | 0,55 |
| Ingegneria dei dati | 0,15 | 0,30 |
| Ingegneria dei dati - Light | 0,07 | 0,22 |

Quando viene utilizzata una quantità di Analisi dei dati nel livello Standard, ad esempio, dalle unità di impegno in Databricks preacquistate vengono detratte 0,4 unità.

Prima dell'acquisto, calcolare la quantità totale di unità Databricks utilizzata per i diversi carichi di lavoro e livelli. Usare i rapporti sopra indicati per la normalizzazione in unità di impegno in Databricks e quindi eseguire una proiezione dell'utilizzo totale nei prossimi uno o tre anni.

## <a name="purchase-databricks-commit-units"></a>Acquistare unità di impegno in Databricks

È possibile acquistare piani di Databricks nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D). Per acquistare capacità riservata, è necessario avere il ruolo di proprietario per almeno una sottoscrizione Enterprise.

- Il preacquisto è attualmente disponibile solo per i clienti con contratto Enterprise Agreement.
- È necessario avere un ruolo di proprietario per almeno una sottoscrizione Enterprise.
- Per le sottoscrizioni Enterprise, **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com/). In alternativa, se tale impostazione è disabilitata, è necessario essere un amministratore del contratto EA della sottoscrizione.

**Per effettuare l'acquisto:**

1. Accedere al [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D).
1. Selezionare una sottoscrizione. Usare l'elenco **Sottoscrizione** per selezionare la sottoscrizione usata per pagare la capacità riservata. I costi iniziali relativi alla capacità riservata vengono addebitati in base al metodo di pagamento della sottoscrizione. I costi vengono detratti dal saldo dell'impegno monetario della registrazione oppure addebitati come eccedenza.
1. Selezionare un ambito. Usare l'elenco **Ambito** per selezionare un ambito della sottoscrizione.
    - **Gruppo di risorse singolo**: lo sconto della prenotazione viene applicato solo alle risorse corrispondenti incluse nel gruppo di risorse selezionato.
    - **Sottoscrizione singola**: lo sconto della prenotazione viene applicato alle risorse corrispondenti incluse nella sottoscrizione selezionata.
    - **Condiviso**: lo sconto della prenotazione viene applicato alle risorse corrispondenti nelle sottoscrizioni idonee incluse nel contesto di fatturazione. Per i clienti con contratto Enterprise Agreement, il contesto di fatturazione è la registrazione.
1. Selezionare il numero di unità di impegno in Azure Databricks che si vuole acquistare e completare l'acquisto.


![Esempio che mostra l'acquisto di Azure Databricks nel portale di Azure](./media/billing-prepay-databricks-reserved-capacity/data-bricks-pre-purchase.png)

## <a name="change-scope-and-ownership"></a>Modificare ambito e proprietà

Dopo l'acquisto, a una prenotazione è possibile apportare i tipi di modifiche seguenti:

- Aggiornare l'ambito della prenotazione
- Accessi in base al ruolo

Non è possibile dividere o unire il preacquisto di unità di impegno in Databricks. Per altre informazioni sulla gestione delle prenotazioni, vedere [Gestire le prenotazioni dopo l'acquisto](billing-manage-reserved-vm-instance.md).

## <a name="cancellations-and-exchanges"></a>Annullamenti e scambi

Per i piani di preacquisto di Databricks non sono supportati l'annullamento e lo scambio. Tutti gli acquisti sono definitivi.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:
  - [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md)
  - [Informazioni sulle modalità di applicazione di uno sconto di preacquisto per unità di impegno in Azure Databricks](billing-reservation-discount-databricks.md)
  - [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
