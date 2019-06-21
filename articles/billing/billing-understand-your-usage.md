---
title: Comprendere i dettagli di utilizzo e costi | Microsoft Docs
description: Informazioni su come leggere e comprendere i dettagli di utilizzo e costi
author: bandersmsft
manager: micflan
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2019
ms.author: banders
ms.openlocfilehash: 2eb9f8e19be2a7b6220bc34bf4ce0c72c4ac0b4f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275063"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Comprendere i termini nel file di utilizzo e costi di Azure

Il file di utilizzo e costi dettagliato contiene dati di utilizzo più votati giornalieri basata negoziati frequenze, gli acquisti (ad esempio, le prenotazioni, tariffa di Marketplace) e rimborsi per il periodo specificato.
Tariffe non includono i crediti, imposte, o altri costi o sconti.
La tabella seguenti viene illustrano quali gli addebiti sono inclusi per ogni tipo di conto.

Tipo di account | Utilizzo di Azure | Utilizzo di Marketplace | Acquisti | Rimborsi
--- | --- | --- | --- | ---
Contratto Enterprise Agreement (EA) | Yes | Sì | Sì | No
Contratto del cliente Microsoft | Yes | Sì | Sì | Yes
Pagamento in base al consumo | Yes | No | No | No

Per altre informazioni sugli ordini Marketplace (noto anche come servizi esterni), vedere [informazioni sugli addebiti di esterni Azure servizio](billing-understand-your-azure-marketplace-charges.md).

Visualizzare [come ottenere la fattura e utilizzo i dati giornalieri di Azure](billing-download-azure-invoice-daily-usage-date.md) per le istruzioni di download.
Il file di utilizzo e costi è disponibile in un formato di file con valori delimitati da virgole (CSV), che è possibile aprire in un'applicazione foglio di calcolo.

## <a name="list-of-terms-and-descriptions"></a>Elenco di termini e descrizioni

La tabella seguente descrive i termini importanti usati nella versione più recente del file di utilizzo e costi di Azure.
L'elenco copre pagamento a consumo (PAYG), Enterprise Agreement (EA) e account di Microsoft dal cliente contratto MCA ().

Nome | Tipo di account | Descrizione
--- | --- | ---
AccountName | Contratto Enterprise | Nome visualizzato dell'account di registrazione.
AccountOwnerId | Contratto Enterprise | Identificatore univoco per l'account di registrazione.
AdditionalInfo | Tutti | Metadati specifici del servizio. Ad esempio un tipo di immagine per una macchina virtuale.
BillingAccountId | EA, MCA | Identificatore univoco per la radice di account di fatturazione.
BillingAccountName | EA, MCA | Nome dell'account di fatturazione.
BillingCurrency | EA, MCA | Valuta associata con l'account di fatturazione.
BillingPeriod | Contratto Enterprise | Il periodo di fatturazione degli addebiti.
BillingPeriodEndDate | EA, MCA | Data di fine del periodo di fatturazione.
BillingPeriodStartDate | EA, MCA | La data di inizio del periodo di fatturazione.
BillingProfileId | EA, MCA | Identificatore univoco della registrazione EA o MCA profilo di fatturazione.
BillingProfileName | EA, MCA | Nome della registrazione EA o MCA profilo di fatturazione.
ChargeType | EA, MCA | Indica se il costo rappresenta utilizzo (**utilizzo**), un acquisto (**acquistare**), o un rimborso (**rimborso**).
ConsumedQuantity | IN BASE AL CONSUMO | Vedere Quantity.
ConsumedService | Tutti | Nome del servizio l'addebito è associato.
Costi | Contratto Enterprise | Vedere CostInBillingCurrency.
CostCenter | EA, MCA | Il centro di costo definito per la sottoscrizione per tenere traccia dei costi (disponibili solo nei periodi di fatturazione aperti per gli account MCA).
CostInBillingCurrency | MCA | Costo degli addebiti nella fatturazione valuta prima crediti o imposte.
CostInPricingCurrency | MCA | Costo dell'addebito nella valuta del prezzo prima crediti o imposte.
Currency | IN BASE AL CONSUMO | Vedere BillingCurrency.
Date | EA, MCA | La data di utilizzo o l'acquisto di addebito.
ExchangeRateDate | MCA | È stato stabilito il tasso di cambio Data.
ExchangeRatePricingToBilling | MCA | Tasso di cambio utilizzato per convertire i costi nella valuta del prezzo per la valuta di fatturazione.
Frequenza | EA, MCA | Indica se è previsto un addebito per ripetere. Gli addebiti per può essere completato una volta (**OneTime**), ripetere l'operazione su base mensile o annua (**ricorrente**), o essere basata sull'utilizzo (**UsageBased**).
IncludedQuantity | IN BASE AL CONSUMO | La quantità riportata dal contatore inclusa senza addebiti nel periodo di fatturazione corrente.
InstanceId | PAGY | Vedere ID risorsa.
InvoiceId | EA, MCA | ID univoco del documento indicato sulla fattura PDF.
InvoiceSection | MCA | Vedere InvoiceSectionName.
InvoiceSectionId | EA, MCA | Identificatore univoco per il reparto con contratto Enterprise Agreement o di una sezione della fattura MCA.
InvoiceSectionName | EA, MCA | Nome della sezione della fattura MCA o reparto con contratto Enterprise.
IsAzureCreditEligible | EA, MCA | Indica se l'addebito è idoneo a essere a pagamento per l'utilizzo di crediti di Azure (i valori: True, False).
IsEstimated | Tutti | Indica se il periodo di fatturazione è chiuso o completato. I dati di utilizzo è possono modificare in tutto il periodo di fatturazione fino a quando non viene generata la fattura. Questi record vengono contrassegnati come "prevista".
Località | EA, MCA | Posizione del Data Center in cui la risorsa è in esecuzione.
meterCategory | Tutti | Nome della categoria di classificazione per il contatore. Ad esempio, *servizi Cloud* e *Networking*.
ID contatore | Tutti | Identificatore univoco per il contatore.
nome del contatore | Tutti | Il nome del contatore.
meterRegion | Tutti | Nome della posizione dei Data Center per i servizi di prezzo basato sul percorso. Vedere posizione.
MeterSubCategory | Tutti | Nome della categoria sottoclassificazione del misuratore.
OfferId | EA, MCA | Nome dell'offerta acquistata.
Operativo{0 | Contratto Enterprise | Identificatore utilizzato per ottenere prezzi contatore specifico.
PlanName | Contratto Enterprise | Nome del piano di Marketplace.
PreviousInvoiceId | MCA | Riferimento a una fattura originale se questa voce è un rimborso.
pricingCurrency | MCA | Valuta usata per classificazione in base al prezzo negoziato.
Prodotto | MCA | ProductName, vedere.
ProductId | EA, MCA | Identificatore univoco per il prodotto.
ProductName | Contratto Enterprise | Nome del prodotto.
ProductOrderId | EA, MCA | Identificatore univoco per l'ordine del prodotto.
ProductOrderName | EA, MCA | Nome univoco per l'ordine del prodotto.
PublisherName | EA, MCA | Server di pubblicazione per i servizi di Marketplace.
PublisherType | EA, MCA | Tipo del server di pubblicazione (i valori: firstParty, thirdPartyReseller, thirdPartyAgency).
Quantità | EA, MCA | Il numero di unità acquistate o utilizzate.
Tariffa | IN BASE AL CONSUMO | Vedere UnitPrice.
ReservationId | EA, MCA | Identificatore univoco per l'istanza di prenotazione acquistata.
ReservationName | EA, MCA | Nome dell'istanza di prenotazione acquistata.
ResourceGroupId | EA, MCA | Identificatore univoco per il [gruppo di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) la risorsa si trova.
ResourceGroupName | EA, MCA | Nome del [gruppo di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) la risorsa si trova.
ResourceId | EA, MCA | Identificatore univoco del [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources) risorsa.
ResourceLocation | EA, MCA | Posizione del Data Center in cui la risorsa è in esecuzione. Vedere posizione.
ResourceName | Contratto Enterprise | Nome della risorsa.
ResourceType | MCA | Tipo di istanza di risorsa.
ServiceFamily | EA, MCA | Famiglia di servizio a cui appartiene il servizio.
ServiceInfo1 | Tutti | Metadati specifici del servizio.
ServiceInfo2 | Tutti | Campo legacy con i metadati specifici del servizio facoltativo.
ServicePeriodEndDate | MCA | La data di fine del periodo di valutazione che definito e bloccato prezzi del servizio utilizzato o acquistato.
ServicePeriodStartDate | MCA | La data di inizio del periodo di valutazione che definito e bloccato prezzi del servizio utilizzato o acquistato.
SubscriptionId | Tutti | Identificatore univoco per la sottoscrizione.
SubscriptionName | Tutti | Nome della sottoscrizione.
`Tags` | Tutti | Tag assegnato alla risorsa. Non includere tag dei gruppi di risorse. Può essere utilizzato per raggruppare o distribuire i costi per il chargeback interno. Per altre informazioni, vedere [Organize your Azure resources with tags](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) (Organizzare le risorse di Azure con i tag).
Unità | IN BASE AL CONSUMO | Vedere UnitOfMeasure.
UnitOfMeasure | Tutti | L'unità di misura per la fatturazione per il servizio. Ad esempio, i servizi di calcolo vengono fatturati per ora.
prezzo unitario | Contratto Enterprise | Il prezzo unitario per le spese.
UsageDate | IN BASE AL CONSUMO | Visualizzare data.

Tenere presente che alcuni campi possono differire in maiuscole e minuscole e la spaziatura tra i tipi di account.
Le versioni precedenti di file di utilizzo a consumo sono sezioni separate per l'istruzione e sull'utilizzo giornaliero.

## <a name="ensure-that-your-charges-are-correct"></a>Verificare che gli addebiti siano corretti

Per altre informazioni sui dettagli di utilizzo e costi, ottenere informazioni su come comprendere le [pagamento a consumo](./billing-understand-your-bill.md) o [contratto di Microsoft dal cliente](billing-mca-understand-your-bill.md) fattura.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare e scaricare la fattura di Microsoft Azure](billing-download-azure-invoice.md)
- [Visualizzare e scaricare l'utilizzo di Microsoft Azure e negli addebiti](billing-download-azure-daily-usage.md)
