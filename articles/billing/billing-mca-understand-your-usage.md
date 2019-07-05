---
title: Condizioni nel file di utilizzo e costi di Azure per un contratto di Microsoft dal cliente
description: Informazioni su come leggere e comprendere le sezioni dell'utilizzo di Azure e gli addebiti CSV per il profilo di fatturazione.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: d11e31366ea5aa15cf7a790eaee800fa2ea6dabe
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490619"
---
# <a name="terms-in-the-azure-usage-and-charges-file-for-a-microsoft-customer-agreement"></a>Condizioni nel file di utilizzo e costi di Azure per un contratto di Microsoft dal cliente

Questo articolo si applica a un account di fatturazione per un contratto di clienti Microsoft. [Controllare se si ha accesso a un contratto di Microsoft dal cliente](#check-access-to-a-microsoft-customer-agreement).

File CSV sull'utilizzo e costi di Azure contiene i costi di utilizzo giornalieri e a livello di contatore per il periodo di fatturazione corrente.

Per ottenere il file di utilizzo e costi di Azure, vedere [visualizzazione e download di utilizzo di Azure e gli addebiti per il contratto del cliente Microsoft](billing-download-azure-daily-usage.md). È disponibile in un formato di file CSV (valori separati dalla virgola) che è possibile aprire in un foglio di calcolo.

Gli addebiti in base all'utilizzo corrispondono agli addebiti **mensili** totali per una sottoscrizione e non tengono in considerazione eventuali accrediti o sconti applicabili.

## <a name="changes-from-azure-ea-usage-and-charges"></a>Modifiche apportate dall'utilizzo di Azure con contratto Enterprise Agreement e negli addebiti

Se si eseguisse un cliente con contratto Enterprise, si noterà che i termini del Azure fatturazione dell'utilizzo CSV file del profilo sono diversi dalle condizioni nel file CSV di utilizzo di contratti Enterprise di Azure. Di seguito è riportato il mapping delle condizioni di utilizzo di tale contratto condizioni di utilizzo del profilo di fatturazione:

| Utilizzo di Azure con contratto Enterprise Agreement CSV | Utilizzo di Microsoft Azure per i contratti dei clienti e negli addebiti CSV |
| --- | --- |
| Date | date |
| Mese| date |
| Giorno | date |
| Year | date |
| Prodotto | product |
| ID contatore | ID contatore |
| meterCategory | meterCategory |
| MeterSubCategory | meterSubCategory |
| meterRegion | meterRegion |
| nome del contatore | meterName |
| ConsumedQuantity | quantity |
| ResourceRate | effectivePrice |
| ExtendedCost | cost |
| ResourceLocation | resourceLocation |
| ConsumedService | consumedService |
| InstanceId | instanceId |
| ServiceInfo1 | serviceInfo1 |
| ServiceInfo2 | serviceInfo2 |
| AdditionalInfo | additionalInfo |
| `Tags` | tags |
| StoreServiceIdentifier | N/D |
| DepartmentName | invoiceSection |
| CostCenter | costCenter |
| UnitOfMeasure | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible |

## <a name="detailed-terms-and-descriptions"></a>Le descrizioni e informazioni dettagliate sulle condizioni

I termini seguenti vengono visualizzati nel file di utilizzo e costi di Azure.

Nome | Descrizione
--- | ---
invoiceId | L'ID univoco del documento indicato sulla fattura PDF
previousInvoiceId | Riferimento a una fattura originale se questa voce è un rimborso
billingAccountName | Nome dell'account di fatturazione
billingAccountId | Identificatore univoco per la radice di account di fatturazione
billingProfileId | Nome del profilo di fatturazione accumulerà addebiti fatturati
billingProfileName | Identificatore univoco per il profilo di fatturazione accumulerà addebiti fatturati
invoiceSectionId | Identificatore univoco per la sezione della fattura
invoiceSectionName | Nome della sezione della fattura
costCenter | Il centro di costo definito per la sottoscrizione per tenere traccia dei costi (disponibili solo nei periodi di fatturazione aperti)
billingPeriodStartDate | La data di inizio del periodo di fatturazione per il quale viene generata la fattura
billingPeriodEndDate | La data di fine del periodo di fatturazione per il quale viene generata la fattura
servicePeriodStartDate | La data di inizio del periodo di valutazione che ha definito e bloccato ai prezzi per il servizio acquistato o utilizzato
servicePeriodEndDate | La data di fine del periodo di valutazione che ha definito e bloccato ai prezzi per il servizio acquistato o utilizzato
date | Per Azure Marketplace e basata sull'utilizzo costi e, questa è la data di classificazione. Per gli acquisti una tantum (prenotazioni, Marketplace) o addebiti periodici fissi (offerte di supporto), questa è la data di acquisto.
serviceFamily | Famiglia di servizio a cui appartiene il servizio
productOrderId | Identificatore univoco per l'ordine di prodotto
productOrderName | Nome univoco per l'ordine di prodotto
consumedService | Nome del servizio utilizzato
meterId | L'identificatore univoco per il contatore
meterName | Il nome del contatore
meterCategory | Nome della categoria di classificazione per il contatore. Ad esempio, *servizi Cloud*, *Networking*e così via.
meterSubCategory | Nome della categoria misuratore sottoclassificazione
meterRegion | Nome dell'area in cui il contatore per il servizio è disponibile. Identifica la posizione del data center per determinati servizi il cui prezzo basato su posizione del data center.
offer | Nome dell'offerta acquistata
productId | Identificatore univoco per il prodotto di addebiti dei
product | Nome del prodotto di addebiti dei
ID sottoscrizione | Identificatore univoco per la sottoscrizione di addebiti dei
subscriptionName | Nome della sottoscrizione di addebiti dei
reservationId | Identificatore univoco per l'istanza di prenotazione acquistata
reservationName | Nome dell'istanza di prenotazione acquistata
publisherType | Tipo del server di pubblicazione (i valori: firstParty, thirdPartyReseller, thirdPartyAgency)
publisherName | Server di pubblicazione per i servizi di Marketplace
resourceGroupId | Identificatore univoco per il gruppo di risorse associato alla risorsa
resourceGroupName | Nome del gruppo di risorse associato alla risorsa
resourceId | Identificatore univoco per l'istanza della risorsa
resourceType | Tipo di istanza di risorsa
resourceLocation | Identifica la posizione del data center in cui la risorsa è in esecuzione.
location | Posizione normalizzata della risorsa se i percorsi di risorse diversi vengono configurati per le stesse aree
quantity | Il numero di unità acquistate o utilizzate
unitOfMeasure | L'unità di misura per la fatturazione per il servizio. Ad esempio, i servizi di calcolo vengono fatturati per ora.
chargeType | Tipo di addebito. Valori: <ul><li>AsCharged-Usage: Costi accumulati in base all'utilizzo di un servizio di Azure. Ciò include l'utilizzo nelle macchine virtuali che non vengono addebitati a causa di istanze riservate.</li><li>AsCharged-PurchaseMarketplace: Una tantum o fissi addebiti periodici da acquisti del Marketplace</li><li>AsCharged-UsageMarketplace: Gli addebiti per i servizi Marketplace vengono addebitati in base alle unità di consumo</li></ul>
isAzureCreditEligible | Flag che indica se l'addebito per il servizio è idoneo a essere a pagamento per l'utilizzo di crediti di Azure (i valori: True, False)
serviceInfo1 | Metadati specifici del servizio
serviceInfo2 | Campo legacy che acquisisce i metadati specifici del servizio facoltativo
additionalInfo | Altri metadati specifici del servizio.
tags | Tag che è assegnato alla risorsa

### <a name="make-sure-that-charges-are-correct"></a>Assicurarsi che gli addebiti siano corretti

Se si desidera assicurarsi che gli addebiti nel file di dati di utilizzo dettagliato siano corretti, è possibile verificare tali. Vedere [comprendere gli addebiti nella fattura del proprio profilo di fatturazione](billing-mca-understand-your-bill.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un contratto di Microsoft dal cliente
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare e scaricare la fattura di Microsoft Azure](billing-download-azure-invoice.md)
- [Visualizzare e scaricare l'utilizzo di Microsoft Azure e negli addebiti](billing-download-azure-daily-usage.md)
