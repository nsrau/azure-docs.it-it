---
title: Condizioni nel file Utilizzo e addebiti di Azure per un Contratto del cliente Microsoft
description: Informazioni su come leggere e comprendere le sezioni del file CSV Utilizzo e addebiti di Azure per il proprio profilo di fatturazione.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c0c43c779affb4edca78def95906f5adfcc6fac4
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709457"
---
# <a name="terms-in-the-azure-usage-and-charges-file-for-a-microsoft-customer-agreement"></a>Condizioni nel file Utilizzo e addebiti di Azure per un Contratto del cliente Microsoft

Questo articolo si applica a un account di fatturazione per un Contratto del cliente Microsoft. [Verificare di avere accesso a un Contratto del cliente Microsoft](#check-access-to-a-microsoft-customer-agreement).

Il file CSV Utilizzo e addebiti di Azure contiene gli addebiti sia a livello giornaliero sia a livello di contatore per il periodo di fatturazione corrente.

Per ottenere il file Utilizzo e addebiti di Azure, vedere [Visualizzare e scaricare utilizzo e addebiti di Azure per il Contratto del cliente di Microsoft](billing-download-azure-daily-usage.md). È disponibile in un formato di file CSV (valori separati dalla virgola) che è possibile aprire in un foglio di calcolo.

Gli addebiti in base all'utilizzo corrispondono agli addebiti **mensili** totali per una sottoscrizione e non tengono in considerazione eventuali accrediti o sconti applicabili.

## <a name="changes-from-azure-ea-usage-and-charges"></a>Modifiche dell'utilizzo e degli addebiti del Contratto Enterprise di Azure

Se si è un cliente con contratto Enterprise, si noterà che le condizioni nel file CSV di utilizzo del profilo di fatturazione di Azure sono diversi da quelli del file CSV di utilizzo del contratto Enterprise di Azure. Di seguito vengono confrontate le condizioni d'utilizzo del contratto Enterprise con le condizioni d'utilizzo del profilo di fatturazione:

| File CSV di utilizzo del contratto Enterprise di Azure | File CSV Utilizzo e addebiti di Azure del contratto del cliente Microsoft |
| --- | --- |
| Data | date |
| Mese| date |
| Giorno | date |
| Year | date |
| Prodotto | product |
| ID contatore | meterID |
| MeterCategory | meterCategory |
| MeterSubCategory | meterSubCategory |
| MeterRegion | meterRegion |
| MeterName | meterName |
| ConsumedQuantity | quantity |
| ResourceRate | effectivePrice |
| ExtendedCost | cost |
| ResourceLocation | resourceLocation |
| ConsumedService | consumedService |
| InstanceId | instanceId |
| ServiceInfo1 | serviceInfo1 |
| ServiceInfo2 | serviceInfo2 |
| AdditionalInfo | additionalInfo |
| Tag | tags |
| StoreServiceIdentifier | N/D |
| DepartmentName | invoiceSection |
| CostCenter | costCenter |
| UnitOfMeasure | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible |

## <a name="detailed-terms-and-descriptions"></a>Dettagli sui termini e sulle descrizioni

I termini seguenti sono riportati nel file Utilizzo e addebiti di Azure.

Termine | DESCRIZIONE
--- | ---
invoiceId | ID univoco del documento elencato nella fattura in formato PDF
previousInvoiceId | Riferimento a una fattura originale se questa voce è un rimborso
billingAccountName | Nome dell'account di fatturazione
billingAccountId | Identificatore univoco per l'account di fatturazione radice
billingProfileId | Nome del profilo di fatturazione che determina un incremento dei costi fatturati
billingProfileName | Identificatore univoco per il profilo di fatturazione che determina un incremento dei costi fatturati
invoiceSectionId | Identificatore univoco per la sezione della fattura
invoiceSectionName | Nome della sezione della fattura
costCenter | Centro di costo definito nella sottoscrizione per tenere traccia dei costi (disponibile solo nei periodi di fatturazione aperti)
billingPeriodStartDate | Data di inizio del periodo di fatturazione per il quale viene generata la fattura
billingPeriodEndDate | Data di fine del periodo di fatturazione per il quale viene generata la fattura
servicePeriodStartDate | Data di inizio del periodo di valutazione che ha definito e bloccato i prezzi per il servizio utilizzato o acquistato
servicePeriodStartDate | Data di fine del periodo di valutazione che ha definito e bloccato i prezzi per il servizio utilizzato o acquistato
date | Per gli addebiti basati sull'utilizzo di Azure e Marketplace, questa è la data di valutazione. Per gli acquisti una tantum (prenotazioni, Marketplace) o gli addebiti periodici fissi (offerte di supporto), si tratta della data di acquisto.
serviceFamily | Famiglia di servizi a cui appartiene il servizio
productOrderId | Identificatore univoco per l'ordine del prodotto
productOrderName | Nome univoco per l'ordine del prodotto
consumedService | Nome del servizio utilizzato
meterId | Identificatore univoco del contatore
meterName | Nome del contatore
meterCategory | Nome della categoria di classificazione per il contatore. Ad esempio, *Servizi cloud*, *Rete*, ecc.
meterSubCategory | Nome della categoria di sottoclassificazione del contatore
meterRegion | Nome dell'area in cui è disponibile il contatore per il servizio. Identifica la posizione del datacenter per determinati servizi il cui prezzo dipende dalla posizione stessa.
offer | Nome dell'offerta acquistata
productId | Identificatore univoco per il prodotto che determina un incremento dei costi
product | Nome del prodotto che determina un incremento dei costi
ID sottoscrizione | Identificatore univoco per la sottoscrizione che determina un incremento dei costi
subscriptionName | Nome della sottoscrizione che determina un incremento dei costi
reservationId | Identificatore univoco per l'istanza di prenotazione acquistata
reservationName | Nome dell'istanza di prenotazione acquistata
publisherType | Tipo di server di pubblicazione (valori: firstParty, thirdPartyReseller, thirdPartyAgency)
publisherName | Server di pubblicazione per servizi del Marketplace
resourceGroupId | Identificatore univoco per il gruppo di risorse associato alla risorsa
resourceGroupName | Nome del gruppo di risorse associato alla risorsa
resourceId | Identificatore univoco per l'istanza della risorsa
resourceType | Tipo di istanza della risorsa
resourceLocation | Identifica la posizione del data center in cui è in esecuzione la risorsa.
location | Posizione normalizzata della risorsa se sono configurate posizioni di risorse diverse per le stesse aree
quantity | Numero di unità acquistate o utilizzate
unitOfMeasure | Unità di misura per la fatturazione del servizio. I servizi di calcolo, ad esempio, vengono fatturati all'ora.
chargeType | Tipo di addebito. Valori: <ul><li>AsCharged-Usage: Addebiti accumulati in base all'utilizzo di un servizio di Azure. Questo include l'utilizzo delle macchine virtuali che non vengono addebitate a causa di istanze riservate.</li><li>AsCharged-PurchaseMarketplace: Addebiti periodici una tantum o fissi dagli acquisti su Marketplace</li><li>AsCharged-UsageMarketplace: Addebiti per i servizi del Marketplace addebitati in base alle unità di utilizzo</li></ul>
isAzureCreditEligible | Flag che indica se l'addebito per il servizio è idoneo a essere pagato per l'uso di crediti Azure (valori: True, False)
serviceInfo1 | Metadati specifici del servizio
serviceInfo2 | Campo legacy che acquisisce i metadati specifici del servizio facoltativo
additionalInfo | Metadati specifici del servizio aggiuntivi.
tags | Tag assegnati alla risorsa

### <a name="make-sure-that-charges-are-correct"></a>Assicurarsi che gli addebiti siano corretti

Per assicurarsi che gli addebiti nel file di utilizzo dettagliato siano corretti, è possibile verificarli. Vedere [Informazioni sugli addebiti nella fattura del profilo di fatturazione](billing-mca-understand-your-bill.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un Contratto del cliente Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare e scaricare la fattura di Microsoft Azure](billing-download-azure-invoice.md)
- [Visualizzare e scaricare l'utilizzo e gli addebiti di Microsoft Azure](billing-download-azure-daily-usage.md)
