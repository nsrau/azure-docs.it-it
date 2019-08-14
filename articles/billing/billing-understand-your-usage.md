---
title: Comprendere l'utilizzo e gli addebiti dettagliati | Microsoft Docs
description: Informazioni su come leggere e comprendere l'utilizzo e gli addebiti dettagliati
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
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954333"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Informazioni sui termini del file di utilizzo e degli addebiti di Azure

Il file dettagliato relativo all'utilizzo e agli addebiti contiene l'utilizzo valutato giornaliero in base a tariffe negoziate, acquisti (ad esempio, prenotazioni, tariffe del Marketplace) e rimborsi per il periodo specificato.
Le tariffe non includono crediti, imposte o altri costi o sconti.
La tabella seguente illustra gli addebiti inclusi per ogni tipo di conto.

Tipo di account | Utilizzo di Azure | Utilizzo del Marketplace | Acquisti | Rimborsi
--- | --- | --- | --- | ---
Contratto Enterprise Agreement (EA) | Yes | Sì | Sì | No
Contratto del cliente Microsoft | Yes | Sì | Sì | Yes
Pagamento in base al consumo | Sì | Sì | No | No

Per altre informazioni sugli ordini del Marketplace (noti anche come servizi esterni), vedere informazioni sugli addebiti per [i servizi esterni di Azure](billing-understand-your-azure-marketplace-charges.md).

Per istruzioni sul download, vedere [come ottenere la fattura e i dati di utilizzo giornalieri di Azure](billing-download-azure-invoice-daily-usage-date.md).
È possibile aprire l'utilizzo e addebitare il file CSV in Microsoft Excel o in un'altra applicazione del foglio di calcolo.

## <a name="list-of-terms-and-descriptions"></a>Elenco di termini e descrizioni

La tabella seguente descrive i termini importanti usati nella versione più recente del file di utilizzo e degli addebiti di Azure.
L'elenco include gli account con pagamento in base al consumo (PAYG), Enterprise Agreement (EA) e Microsoft Customer Agreement (MCA).

Termine | Tipo di account | Descrizione
--- | --- | ---
AccountName | EA, PAYG | Nome visualizzato dell'account di registrazione EA o dell'account di fatturazione PAYG.
AccountOwnerId<sup>1</sup> | EA, PAYG | Identificatore univoco per l'account di registrazione EA o l'account di fatturazione PAYG.
Informazioni aggiuntive | Tutti | Metadati specifici del servizio. Ad esempio un tipo di immagine per una macchina virtuale.
BillingAccountId<sup>1</sup> | Tutti | Identificatore univoco per l'account di fatturazione radice.
BillingAccountName | Tutti | Nome dell'account di fatturazione.
BillingCurrency | Tutti | Valuta associata all'account di fatturazione.
BillingPeriod | EA, PAYG | Periodo di fatturazione dell'addebito.
BillingPeriodEndDate | Tutti | Data di fine del periodo di fatturazione.
BillingPeriodStartDate | Tutti | Data di inizio del periodo di fatturazione.
BillingProfileId<sup>1</sup> | Tutti | Identificatore univoco della registrazione EA, sottoscrizione PAYG, profilo di fatturazione MCA o account consolidato AWS.
BillingProfileName | Tutti | Nome della registrazione EA, sottoscrizione PAYG, profilo di fatturazione MCA o account consolidato AWS.
ChargeType | Tutti | Indica se l'addebito rappresenta l'utilizzo (**utilizzo**), un acquisto (**acquisto**) o un rimborso (**rimborso**).
ConsumedService | Tutti | Nome del servizio a cui è associato il costo.
CostCenter<sup>1</sup> | EA, MCA | Centro di costo definito per la sottoscrizione per tenere traccia dei costi (disponibile solo nei periodi di fatturazione aperti per gli account MCA).
Costi | EA, PAYG | Vedere CostInBillingCurrency.
CostInBillingCurrency | MCA | Costo dell'addebito nella valuta di fatturazione prima di crediti o imposte.
CostInPricingCurrency | MCA | Costo dell'addebito nella valuta dei prezzi prima di crediti o imposte.
Currency | EA, PAYG | Vedere BillingCurrency.
Data<sup>1</sup> | Tutti | Data di utilizzo o di acquisto dell'addebito.
EffectivePrice | Tutti | Prezzo unitario misto per il periodo. I prezzi combinati comportano la media di eventuali fluttuazioni nel prezzo unitario, ad esempio la suddivisione in livelli graduale, che riduce il prezzo come aumento della quantità nel tempo.
ExchangeRateDate | MCA | Data di creazione del tasso di cambio.
ExchangeRatePricingToBilling | MCA | Tasso di cambio utilizzato per convertire il costo della valuta per la fatturazione.
Frequenza | Tutti | Indica se è previsto un addebito per la ripetizione. Gli addebiti possono essereeffettuati una sola volta (un periodo), ripetuti su base mensile o annuale (**ricorrente**) o in base all'utilizzo (**UsageBased**).
InvoiceId | PAYG, MCA | ID univoco del documento elencato nel PDF della fattura.
InvoiceSection | MCA | Vedere InvoiceSectionName.
InvoiceSectionId<sup>1</sup> | EA, MCA | Identificatore univoco per la sezione del reparto EA o della fattura MCA.
InvoiceSectionName | EA, MCA | Nome della sezione del reparto EA o della fattura MCA.
IsAzureCreditEligible | Tutti | Indica se l'addebito è idoneo a essere pagato per l'uso di crediti Azure (valori: True, false).
Location | MCA | Località del Data Center in cui è in esecuzione la risorsa.
Categoria del contatore | Tutti | Nome della categoria di classificazione per il contatore. Ad esempio *servizi cloud* e *rete*.
ID contatore<sup>1</sup> | Tutti | Identificatore univoco del contatore.
Nome del contatore | Tutti | Nome del contatore.
Area del contatore | Tutti | Nome della località del Data Center per i servizi prezzi in base alla località. Vedere location.
Sottocategoria del contatore | Tutti | Nome della categoria di sottoclassificazione del contatore.
Idofferta<sup>1</sup> | Tutti | Nome dell'offerta acquistata.
NumeroArticolo<sup>1</sup> | EA, PAYG | Identificatore usato per ottenere i prezzi specifici del contatore.
PlanName | EA, PAYG | Nome del piano del Marketplace.
PreviousInvoiceId | MCA | Riferimento a una fattura originale se questa voce è un rimborso.
PricingCurrency | MCA | Valuta utilizzata per la classificazione in base ai prezzi negoziati.
Prodotto | Tutti | Nome del prodotto.
ProductId<sup>1</sup> | MCA | Identificatore univoco per il prodotto.
ProductOrderId | Tutti | Identificatore univoco per l'ordine del prodotto.
ProductOrderName | Tutti | Nome univoco per l'ordine del prodotto.
PublisherName | Tutti | Editore per i servizi del Marketplace.
PublisherType | Tutti | Tipo di server di pubblicazione (valori: **Azure**, **AWS**, **Marketplace**).
Quantità | Tutti | Numero di unità acquistate o utilizzate.
ReservationId | EA, MCA | Identificatore univoco per l'istanza di prenotazione acquistata.
Reservationname | EA, MCA | Nome dell'istanza di prenotazione acquistata.
ResourceGroup | Tutti | Nome del [gruppo di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) in cui si trova la risorsa.
ResourceId<sup>1</sup> | Tutti | Identificatore univoco della risorsa [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources) .
Posizione della risorsa | Tutti | Località del Data Center in cui è in esecuzione la risorsa. Vedere location.
ResourceName | EA, PAYG | Nome della risorsa.
ResourceType | MCA | Tipo di istanza della risorsa.
ServiceFamily | MCA | Famiglia di servizi a cui appartiene il servizio.
ServiceInfo1 | Tutti | Metadati specifici del servizio.
Informazioni sul servizio 2 | Tutti | Campo legacy con metadati facoltativi specifici del servizio.
ServicePeriodEndDate | MCA | Data di fine del periodo di valutazione che ha definito e bloccato i prezzi per il servizio utilizzato o acquistato.
ServicePeriodStartDate | MCA | Data di inizio del periodo di valutazione che ha definito e bloccato i prezzi per il servizio utilizzato o acquistato.
SubscriptionId<sup>1</sup> | Tutti | Identificatore univoco per la sottoscrizione di Azure.
Nome della sottoscrizione | Tutti | Nome della sottoscrizione di Azure.
Tag<sup>1</sup> | Tutti | Tag assegnati alla risorsa. Non include i tag del gruppo di risorse. Può essere usato per raggruppare o distribuire i costi per il chargeback interno. Per altre informazioni, vedere [Organize your Azure resources with tags](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) (Organizzare le risorse di Azure con i tag).
Termine | Tutti | Visualizza il termine per la validità dell'offerta. Ad esempio:  Nel caso di istanze riservate, Visualizza 12 mesi come termine. Per gli acquisti monouso o gli acquisti periodici, il termine è 1 mese (SaaS, supporto del Marketplace). Questa operazione non è applicabile per il consumo di Azure.
Unità di misura | Tutti | Unità di misura per la fatturazione per il servizio. I servizi di calcolo, ad esempio, vengono fatturati all'ora.
PrezzoUnitario | EA, PAYG | Prezzo per unità per l'addebito.

_<sup>**1**</sup> campi utilizzati per compilare un ID univoco per un singolo record di costi._

Nota Alcuni campi possono variare in maiuscolo e minuscolo e spaziatura tra i tipi di conto.
Le versioni precedenti dei file di utilizzo con pagamento in base al consumo hanno sezioni separate per l'utilizzo giornaliero e l'istruzione.

### <a name="list-of-terms-from-older-apis"></a>Elenco di termini delle API precedenti
Nella tabella seguente viene eseguito il mapping dei termini usati nelle API precedenti ai nuovi termini. Per tali descrizioni, fare riferimento alla tabella precedente.

Termine precedente | Nuovo termine
--- | ---
Quantità utilizzata | Quantità
IncludedQuantity | N/D
InstanceId | ResourceId
Tariffa | EffectivePrice
Unità | Unità di misura
UsageDate | Date
UsageEnd | Date
UsageStart | Date


## <a name="ensure-charges-are-correct"></a>Verificare che gli addebiti siano corretti

Per ulteriori informazioni sull'utilizzo e sugli addebiti dettagliati, leggere le informazioni su come comprendere la fattura con [pagamento in base](./billing-understand-your-bill.md) al consumo o la fattura [del contratto clienti Microsoft](billing-mca-understand-your-bill.md) .

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- [Visualizza e Scarica la fattura Microsoft Azure](billing-download-azure-invoice.md)
- [Visualizza e Scarica i Microsoft Azure utilizzo e gli addebiti](billing-download-azure-daily-usage.md)
