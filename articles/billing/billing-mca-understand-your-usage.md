---
title: Informazioni sull'utilizzo di Azure e gli addebiti CSV condizioni per un contratto di Microsoft dei clienti | Microsoft Docs
description: Informazioni su come leggere e comprendere le sezioni dell'utilizzo di Azure e gli addebiti CSV per il profilo di fatturazione
services: ''
documentationcenter: ''
author: bandersmsft
manager: alherz
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: banders
ms.openlocfilehash: 8f71f42386ce49d4d7178cb03d28d74edacd7e39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371309"
---
# <a name="understand-terms-on-your-azure-usage-and-charges-csv-for-a-microsoft-customer-agreement"></a>Informazioni sull'utilizzo di Azure e gli addebiti CSV condizioni per un contratto di Microsoft dal cliente

Questo articolo si applica a un account di fatturazione per un contratto di clienti Microsoft. [Controllare se si ha accesso a un contratto di Microsoft dal cliente](#check-access-to-a-microsoft-customer-agreement).

File CSV sull'utilizzo e costi di Azure contiene i costi di utilizzo a livello giornaliero e contatore per il periodo di fatturazione corrente.

Per ottenere il file di utilizzo e costi di Azure, vedere [visualizzazione e download di utilizzo di Azure e gli addebiti per il contratto del cliente Microsoft](billing-download-azure-daily-usage.md).
È disponibile in un formato di file CSV (valori separati dalla virgola) che è possibile aprire in un foglio di calcolo.

Gli addebiti in base all'utilizzo corrispondono agli addebiti **mensili** totali per una sottoscrizione e non tengono in considerazione eventuali accrediti o sconti applicabili.

## <a name="changes-in-the-enterprise-agreement-azure-usage-and-charges-csv"></a>Modifica nell'utilizzo di Azure per i contratti Enterprise e gli addebiti per CSV

Se si eseguisse un cliente con contratto Enterprise, si noterà che i termini nel profilo di fatturazione file CSV di utilizzo di Azure sono diversi rispetto i termini nel file CSV di utilizzo di Azure con contratto Enterprise. Di seguito è riportato il mapping delle condizioni di utilizzo di tale contratto condizioni di utilizzo del profilo di fatturazione:

| CSV di utilizzo di Azure con contratto Enterprise | Utilizzo di Microsoft Azure per i contratti dei clienti e negli addebiti CSV |
| --- | --- |
| Data | date |
| Mese| date |
| Giorno | date |
| Year | date |
| Prodotto | product |
| ID contatore | meterID |
| Categoria del contatore | meterCategory |
| Sottocategoria del contatore | meterSubCategory |
| Area del contatore | meterRegion |
| Nome del contatore | meterName |
| ConsumedQuantity | quantity |
| Tariffa per la risorsa | effectivePrice | <!-- this was highlighted -->
| Costo esteso | cost |
| Posizione della risorsa | resourceLocation |
| Servizio utilizzato | consumedService |
| InstanceId | instanceId |
| Informazioni sul servizio 1 | serviceInfo1 |
| Informazioni sul servizio 2 | serviceInfo2 |
| Informazioni aggiuntive | additionalInfo |
| Tag | tags |
| Identificatore del servizio Store | N/D |
| DepartmentName | invoiceSection | <!-- this was highlighted -->
| CostCenter | costCenter |
| Unità di misura | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible | <!-- this was highlighted -->

<!-- TO DO: Marketplace CSV? -->

## <a name="detailed-terms-and-descriptions-in-your-azure-usage-and-charges-file"></a>Dettagli sui termini e le descrizioni nel file di utilizzo e costi di Azure

La sezione seguente descrive i termini più importanti presenti nel file di utilizzo e costi di Azure.

Termine | DESCRIZIONE
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

### <a name="how-do-i-make-sure-that-the-charges-in-my-azure-usage-and-charges-file-are-correct"></a>Come assicurarmi che gli addebiti nel file di utilizzo e costi di Azure siano corretti?

Se è previsto un addebito per il file di dati di utilizzo dettagliato che si vogliono ricevere altre informazioni, vedere [comprendere gli addebiti nella fattura del proprio profilo di fatturazione](billing-mca-understand-your-bill.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un contratto di Microsoft dal cliente
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Se si hanno domande o assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare e scaricare la fattura di Microsoft Azure](billing-download-azure-invoice.md)
- [Visualizzare e scaricare l'utilizzo di Microsoft Azure e negli addebiti](billing-download-azure-daily-usage.md)
