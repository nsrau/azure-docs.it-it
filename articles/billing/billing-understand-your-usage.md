---
title: Informazioni sull'utilizzo dettagliato e sugli addebiti | Microsoft Docs
description: Informazioni su come leggere e comprendere l'utilizzo dettagliato e gli addebiti
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
ms.openlocfilehash: 1842d32a838470d9b2af3a778c44c37464d32294
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "68954333"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Informazioni sui termini nel file dei dati di utilizzo e addebiti di Azure

Il file dettagliato relativo ai dati di utilizzo e agli addebiti contiene l'utilizzo giornaliero in base a tariffe negoziate, acquisti (ad esempio, prenotazioni, tariffe del Marketplace) e rimborsi per il periodo specificato.
Le tariffe non includono crediti, imposte o altri addebiti o sconti.
La tabella seguente illustra gli addebiti inclusi per ogni tipo di account.

Tipo di account | Utilizzo di Azure | Utilizzo del Marketplace | Acquisti | Rimborsi
--- | --- | --- | --- | ---
Contratto Enterprise Agreement (EA) | Sì | Sì | Sì | No
Contratto del cliente Microsoft | Sì | Sì | Sì | Sì
Pagamento in base al consumo | Sì | Sì | No | No

Per altre informazioni sugli ordini del Marketplace (noti anche come servizi esterni), vedere [Informazioni sugli addebiti per i servizi esterni di Azure](billing-understand-your-azure-marketplace-charges.md).

Per le istruzioni di download, vedere [Come ottenere la fattura e i dati di utilizzo giornalieri di Azure](billing-download-azure-invoice-daily-usage-date.md).
È possibile aprire il file CSV dei dati di utilizzo e addebiti in Microsoft Excel o in un'altra applicazione per fogli di calcolo.

## <a name="list-of-terms-and-descriptions"></a>Elenco di termini e descrizioni

La tabella seguente descrive i termini importanti usati nella versione più recente del file dei dati di utilizzo e addebiti di Azure.
L'elenco include gli account con pagamento in base al consumo, Contratto Enterprise e Contratto del cliente Microsoft.

Termine | Tipo di account | DESCRIZIONE
--- | --- | ---
AccountName | Contratto Enterprise, Con pagamento in base al consumo | Nome visualizzato dell'account di registrazione EA o dell'account di fatturazione con pagamento in base al consumo.
AccountOwnerId<sup>1</sup> | Contratto Enterprise, Con pagamento in base al consumo | Identificatore univoco dell'account di registrazione EA o dell'account di fatturazione con pagamento in base al consumo.
AdditionalInfo | Tutti | Metadati specifici del servizio. Ad esempio un tipo di immagine per una macchina virtuale.
BillingAccountId<sup>1</sup> | Tutti | Identificatore univoco per l'account di fatturazione radice.
BillingAccountName | Tutti | Nome dell'account di fatturazione.
BillingCurrency | Tutti | Valuta associata all'account di fatturazione.
BillingPeriod | Contratto Enterprise, Con pagamento in base al consumo | Periodo di fatturazione dell'addebito.
BillingPeriodEndDate | Tutti | Data di fine del periodo di fatturazione.
BillingPeriodStartDate | Tutti | Data di inizio del periodo di fatturazione.
BillingProfileId<sup>1</sup> | Tutti | Identificatore univoco della registrazione EA, della sottoscrizione con pagamento in base al consumo, del profilo di fatturazione del Contratto del cliente Microsoft o account AWS consolidato.
BillingProfileName | Tutti | Nome della registrazione EA, della sottoscrizione con pagamento in base al consumo, del profilo di fatturazione del Contratto del cliente Microsoft o account AWS consolidato.
ChargeType | Tutti | Indica se l'addebito rappresenta l'utilizzo (**Utilizzo**), un acquisto (**Acquisto**) o un rimborso (**Rimborso**).
ConsumedService | Tutti | Nome del servizio a cui è associato l'addebito.
CostCenter<sup>1</sup> | EA, Contratto del cliente Microsoft | Centro di costo definito per la sottoscrizione per tenere traccia dei costi (disponibile solo nei periodi di fatturazione aperti per gli account del Contratto del cliente Microsoft).
Costi | Contratto Enterprise, Con pagamento in base al consumo | Vedere CostInBillingCurrency.
CostInBillingCurrency | Contratto del cliente Microsoft | Costo dell'addebito nella valuta di fatturazione al lordo di crediti o imposte.
CostInPricingCurrency | Contratto del cliente Microsoft | Costo dell'addebito nella valuta dei prezzi al lordo di crediti o imposte.
Valuta | Contratto Enterprise, Con pagamento in base al consumo | Vedere BillingCurrency.
Date<sup>1</sup> | Tutti | Data di utilizzo o di acquisto dell'addebito.
EffectivePrice | Tutti | Prezzo unitario combinato per il periodo. I prezzi combinati calcolano la media di eventuali fluttuazioni nel prezzo unitario, ad esempio la suddivisione in livelli graduale, che riduce il prezzo man mano che la quantità aumenta nel tempo.
ExchangeRateDate | Contratto del cliente Microsoft | Data di determinazione del tasso di cambio.
ExchangeRatePricingToBilling | Contratto del cliente Microsoft | Tasso di cambio usato per convertire il costo della valuta dei prezzi nella valuta di fatturazione.
Frequenza | Tutti | Indica se è prevista la ripetizione di un addebito. Gli addebiti possono avvenire una sola volta (**OneTime**), ripetersi su base mensile o annuale (**Recurring**) o essere basati sull'utilizzo (**UsageBased**).
InvoiceId | Con pagamento in base al consumo, Contratto del cliente Microsoft | ID univoco del documento elencato nella fattura in formato PDF.
InvoiceSection | Contratto del cliente Microsoft | Vedere InvoiceSectionName.
InvoiceSectionId<sup>1</sup> | EA, Contratto del cliente Microsoft | Identificatore univoco per reparto EA o sezione della fattura del Contratto del cliente Microsoft.
InvoiceSectionName | EA, Contratto del cliente Microsoft | Nome reparto EA o sezione della fattura del Contratto del cliente Microsoft.
IsAzureCreditEligible | Tutti | Indica se l'addebito è idoneo a essere pagato per l'uso di crediti Azure (valori: True, False).
Location | Contratto del cliente Microsoft | Località del data center in cui viene eseguita la risorsa.
MeterCategory | Tutti | Nome della categoria di classificazione per il contatore. Ad esempio, *Servizi cloud* e *Rete*.
MeterId<sup>1</sup> | Tutti | Identificatore univoco del contatore.
MeterName | Tutti | Nome del contatore.
MeterRegion | Tutti | Nome della località del data center per i servizi addebitati in base alla località. Vedere Location.
MeterSubCategory | Tutti | Nome della categoria di sottoclassificazione del contatore.
OfferId<sup>1</sup> | Tutti | Nome dell'offerta acquistata.
PartNumber<sup>1</sup> | Contratto Enterprise, Con pagamento in base al consumo | Identificatore usato per ottenere i prezzi specifici del contatore.
PlanName | Contratto Enterprise, Con pagamento in base al consumo | Nome del piano del Marketplace.
PreviousInvoiceId | Contratto del cliente Microsoft | Riferimento a una fattura originale se questa voce è un rimborso.
PricingCurrency | Contratto del cliente Microsoft | Valuta usata per la classificazione in base ai prezzi negoziati.
Prodotto | Tutti | Nome del prodotto.
ProductId<sup>1</sup> | Contratto del cliente Microsoft | Identificatore univoco per il prodotto.
ProductOrderId | Tutti | Identificatore univoco per l'ordine del prodotto.
ProductOrderName | Tutti | Nome univoco per l'ordine del prodotto.
PublisherName | Tutti | Server di pubblicazione per servizi del Marketplace.
PublisherType | Tutti | Tipo di server di pubblicazione (valori: **Azure**, **AWS**, **Marketplace**).
Quantità | Tutti | Numero di unità acquistate o utilizzate.
ReservationId | EA, Contratto del cliente Microsoft | Identificatore univoco per l'istanza di prenotazione acquistata.
ReservationName | EA, Contratto del cliente Microsoft | Nome dell'istanza di prenotazione acquistata.
ResourceGroup | Tutti | Nome del [gruppo di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) in cui si trova la risorsa.
ResourceId<sup>1</sup> | Tutti | Identificatore univoco della risorsa di [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources).
ResourceLocation | Tutti | Località del data center in cui viene eseguita la risorsa. Vedere Location.
ResourceName | Contratto Enterprise, Con pagamento in base al consumo | Nome della risorsa.
ResourceType | Contratto del cliente Microsoft | Tipo di istanza della risorsa.
ServiceFamily | Contratto del cliente Microsoft | Famiglia di servizi a cui appartiene il servizio.
ServiceInfo1 | Tutti | Metadati specifici del servizio.
ServiceInfo2 | Tutti | Campo legacy con metadati specifici del servizio facoltativo.
ServicePeriodEndDate | Contratto del cliente Microsoft | Data di fine del periodo di valutazione che ha definito e bloccato i prezzi per il servizio utilizzato o acquistato.
ServicePeriodStartDate | Contratto del cliente Microsoft | Data di inizio del periodo di valutazione che ha definito e bloccato i prezzi per il servizio utilizzato o acquistato.
SubscriptionId<sup>1</sup> | Tutti | Identificatore univoco per la sottoscrizione di Azure.
SubscriptionName | Tutti | Nome della sottoscrizione di Azure.
Tags<sup>1</sup> | Tutti | Tag assegnati alla risorsa. Non include i tag del gruppo di risorse. Può essere usato per raggruppare o distribuire i costi per il chargeback interno. Per altre informazioni, vedere [Organize your Azure resources with tags](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) (Organizzare le risorse di Azure con i tag).
Termine | Tutti | Visualizza il termine della validità dell'offerta. Ad esempio:  Nel caso di istanze riservate visualizza 12 mesi come termine. Per gli acquisti una tantum o gli acquisti periodici, il termine è 1 mese (SaaS, supporto per il Marketplace). Non è applicabile per il consumo di Azure.
UnitOfMeasure | Tutti | Unità di misura per la fatturazione del servizio. I servizi di calcolo, ad esempio, vengono fatturati all'ora.
UnitPrice | Contratto Enterprise, Con pagamento in base al consumo | Prezzo unitario per l'addebito.

_<sup>**1**</sup> Campi usati per creare un ID univoco per un singolo record di costi._

Alcuni campi possono variare per l'utilizzo di maiuscole e minuscole e per la spaziatura tra i tipi di account.
Le versioni precedenti dei file di dati di utilizzo con pagamento in base al consumo hanno sezioni separate per il rendiconto e l'utilizzo giornaliero.

### <a name="list-of-terms-from-older-apis"></a>Elenco di termini delle API precedenti
La tabella seguente associa i termini usati nelle API precedenti ai nuovi termini. Per le descrizioni, fare riferimento alla tabella precedente.

Termine precedente | Nuovo termine
--- | ---
ConsumedQuantity | Quantità
IncludedQuantity | N/D
InstanceId | ResourceId
Tariffa | EffectivePrice
Unità | UnitOfMeasure
UsageDate | Data
UsageEnd | Data
UsageStart | Data


## <a name="ensure-charges-are-correct"></a>Verificare che gli addebiti siano corretti

Per altre informazioni sui dati di utilizzo dettagliati e sugli addebiti, vedere le informazioni sulla fattura [con pagamento in base al consumo](./billing-understand-your-bill.md) o sulla fattura del [contratto del cliente Microsoft](billing-mca-understand-your-bill.md).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare e scaricare la fattura di Microsoft Azure](billing-download-azure-invoice.md)
- [Visualizzare e scaricare l'utilizzo e gli addebiti di Microsoft Azure](billing-download-azure-daily-usage.md)
