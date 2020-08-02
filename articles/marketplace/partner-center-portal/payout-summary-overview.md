---
title: Panoramica del riepilogo proventi - Azure Marketplace
description: Il riepilogo proventi presenta informazioni dettagliate sulle entrate realizzate con l'offerta. Consente anche di determinare quando si riceveranno i pagamenti e gli importi che verranno corrisposti.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 2e7658e965931dd78a6db0e2c9fee36edffff98b
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87479813"
---
# <a name="payout-summary-overview"></a>Panoramica del riepilogo proventi

Il [riepilogo proventi](./payout-summary.md) presenta informazioni dettagliate sulle entrate realizzate con Microsoft. Consente anche di determinare quando si riceveranno i pagamenti e gli importi che verranno corrisposti.

Se si vendono offerte in Azure Marketplace, nel riepilogo proventi verranno visualizzate anche informazioni sui pagamenti andati a buon fine. Per altre informazioni sui pagamenti di Azure Marketplace, vedere [Criteri di partecipazione ad Azure Marketplace](https://docs.microsoft.com/legal/marketplace/participation-policy) e [Contratto per editori di Microsoft Azure Marketplace](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx).

> [!NOTE]
> Per poter beneficiare del pagamento, gli introiti devono raggiungere la [soglia di pagamento](./payment-thresholds-methods-timeframes.md) di 50 dollari statunitensi. Per informazioni dettagliate sulla soglia di pagamento, vedere il [Contratto per editori di Microsoft Azure Marketplace](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx).

Per visualizzare i dettagli relativi ai pagamenti, accedere al centro per i [partner](https://partner.microsoft.com/dashboard/home) e selezionare l'icona del payout nell'angolo superiore destro della schermata:

![Illustra l'icona Proventi nell'angolo superiore destro del portale del Centro per i partner.](./media/payout-overview.png)

## <a name="roles-and-permissions"></a>Ruoli e autorizzazioni

Di seguito sono indicati i ruoli e le autorizzazioni per accedere al report sui proventi:

| Report/pagine | Proprietario dell'account | Manager | Developer | Collaboratore aziendale | Collaboratore finanza | Addetto al marketing |
| --- | --- | --- | --- | --- | --- | --- |
| Report Acquisizioni (inclusi dati quasi in tempo reale) | Può visualizzare | Può visualizzare | Nessun accesso | Nessun accesso | Può visualizzare | Nessun accesso |
| Report commenti e suggerimenti/risposte | Può visualizzare e inviare commenti e suggerimenti | Può visualizzare e inviare commenti e suggerimenti | Può visualizzare e inviare commenti e suggerimenti | Nessun accesso | Nessun accesso | Può visualizzare e inviare commenti e suggerimenti |
| --- | --- | --- | --- | --- | --- | --- |
| Report sull'integrità (inclusi dati quasi in tempo reale) | Può visualizzare | Può visualizzare | Può visualizzare | Può visualizzare | Nessun accesso | Nessun accesso |
| Report sull'utilizzo | Può visualizzare | Può visualizzare | Può visualizzare | Può visualizzare | Nessun accesso | Nessun accesso |
| Account proventi | Può aggiornare | Nessun accesso | Nessun accesso | Nessun accesso | Può aggiornare | Nessun accesso |
| Profilo fiscale | Può aggiornare | Nessun accesso | Nessun accesso | Nessun accesso | Può aggiornare | Nessun accesso |
| Riepilogo proventi | Può visualizzare | Nessun accesso | Nessun accesso | Nessun accesso | Può visualizzare | Nessun accesso  |
| | | | | | | |

## <a name="payment-schedules"></a>Pianificazioni dei pagamenti

Per una descrizione delle pianificazioni dei pagamenti, inclusi i periodi di detenzione, la visibilità dei partner e quando il cliente usa una carta di credito o una fattura, vedere la sezione [Pianificazioni dei pagamenti](./payout-policy-details.md#payment-schedules) dell'argomento **Dettagli dei pagamenti**.

## <a name="transaction-history-download-export"></a>Esportazione del download della cronologia delle transazioni

Questa opzione consente di scaricare ogni voce di reddito visualizzata nella pagina della cronologia delle transazioni. Sono inclusi il tipo di reddito, la data, l'importo della transazione associata, il cliente, il prodotto e altri dettagli della transazione relativi al programma Incentivi.

| Nome colonna | Descrizione |
| --- | --- |
| earningId | Identificatore univoco per ogni reddito |
| participantId | Identità principale del partner che realizza un reddito nell'ambito del programma |
| participantIdType | ID programma per i programmi Incentivi e Venditore se il programma è relativo allo Store e Azure Marketplace |
| participantName | Nome del partner beneficiario del reddito |
| partnerCountryCode | Località/paese/area del partner beneficiario del reddito |
| programName | Nome del programma Incentivi/Store |
| transactionId | Identificatore univoco della transazione |
| transactionCurrency | Valuta in cui è avvenuta la transazione del cliente originale (non si tratta della valuta locale del partner) |
| transactionDate | Data della transazione. Utile per i programmi in cui un reddito è il risultato di più transazioni |
| transactionExchangeRate | Tasso di cambio usato per visualizzare l'importo in dollari statunitensi della transazione corrispondente |
| transactionAmount | Importo della transazione nella valuta della transazione originale in base a cui viene generato il reddito |
| transactionAmountUSD | Importo della transazione in dollari statunitensi (USD) |
| lever | Regola di business per il reddito |
| earningRate | Percentuale degli incentivi applicata all'importo della transazione per generare un reddito |
| quantity | Quantità fatturata per i programmi transazionali. Varia in base al programma |
| quantityType | Tipo di quantità, ad esempio: quantità fatturata, utenti attivi mensili |
| earningType | Indica se si tratta di corrispettivi, sconti, co-op, vendite e così via |
| earningAmount | Importo del reddito nella valuta della transazione originale |
| earningAmountUSD | Importo del reddito in dollari statunitensi |
| earningDate | Data del reddito |
| calculationDate | Data di calcolo del reddito nel sistema |
| earningExchangeRate | Tasso di cambio usato per visualizzare l'importo in dollari statunitensi corrispondente |
| exchangeRateDate | Data del tasso di cambio usata per calcolare il valore in dollari statunitensi di EarningAmount |
| paymentAmountWOTax | Importo del reddito (al netto di tasse) nella valuta di pagamento solo per i pagamenti &quot;inviati&quot; |
| paymentCurrency | Valuta di pagamento scelta dal partner nel profilo di pagamento. Visualizzata solo per i pagamenti inviati |
| paymentExchangeRate | Tasso di cambio usato per calcolare paymentAmountWOTax nella valuta di pagamento con ExchangeRateDate |
| paymentId | Identificatore univoco per il pagamento. Questo numero è visibile nell'estratto conto della banca |
| paymentStatus | Stato dei pagamenti |
| paymentStatusDescription | Descrizione dello stato del pagamento |
| customerId | È sempre vuoto |
| customerName | È sempre vuoto |
| partNumber | È sempre vuoto |
| productName | Nome del prodotto correlato alla transazione |
| productId | Identificatore univoco del prodotto |
| parentProductId | Identificatore univoco del prodotto padre. Se non è presente un prodotto padre per la transazione, l'ID del prodotto padre coincide con l'ID del prodotto. |
| parentProductName | Nome del prodotto padre. Se non è presente un prodotto padre per la transazione, il nome del prodotto padre coincide con il nome del prodotto. |
| productType | Tipo di prodotto (ad esempio app, componente aggiuntivo e gioco) |
| invoiceNumber | Numero di fattura (solo per i contratti Enterprise) |
| resellerId | Identificatore del rivenditore |
| resellerName | Nome del rivenditore |
| transactionType | Tipo di transazione, ad esempio acquisto, rimborso, storno e rifiuto di addebito |
| localProviderSeller | Fornitore/gestore del sistema di pagamento locale |
| taxRemitted | Importo delle imposte versate (vendite, utilizzo o IVA/GST). |
| taxRemitModel | Parte responsabile del versamento delle imposte (vendite, utilizzo o IVA/GST). |
| storeFee | Importo trattenuto da Microsoft come corrispettivo per rendere disponibile l'app o il componente aggiuntivo nel marketplace commerciale. |
| transactionPaymentMethod | Strumento di pagamento del cliente usato per la transazione, ad esempio carta di credito, fatturazione operatore di telefonia mobile e PayPal |
| tpan | Rete pubblicitaria di terze parti |
| customerCountry | Paese/Area geografica del cliente |
| customerCity | Città del cliente |
| customerState | Stato/Provincia del cliente |
| customerZip | CAP/Codice postale del cliente |
| ID tenant | ID del tenant |
| externalReferenceId | Identificatore univoco per il programma |
| externalReferenceIdLabel | Etichetta identificatore univoco |
| transactionCountryCode | Codice paese/area geografica in cui è avvenuta la transazione |
| taxCountry | Paese/Area geografica del cliente |
| taxState | Stato/Provincia del cliente |
| taxCity | Città del cliente |
| taxZipCode | CAP/Codice postale del cliente |
| LicensingProgramName | Nome del programma di licenza |
| Codice programma | Stringa da associare al nome del programma |
| earningAmountInLastPaymentCurrency | Importo del reddito nell'ultima valuta di pagamento. Il campo sarà vuoto se non sono stati corrisposti pagamenti precedenti. |
| lastPaymentCurrency | Ultima valuta di pagamento. Il campo sarà vuoto se non sono stati corrisposti pagamenti precedenti. |
| AssetId | Identificatore univoco per gli ordini dei clienti per il servizio Marketplace. Rappresenta le voci di acquisto. Possono essere presenti più asset. |
| OrderId | Si riferisce alla fattura di un cliente |
| LineItemId | Singola voce nella fattura di un cliente |
| Paese/Area geografica del cliente | Nome del paese o dell'area geografica specificato dal cliente. Potrebbe essere diverso da quello del paese/area geografica della sottoscrizione di Azure di un cliente. |
| Customer EmailAddress | Indirizzo e-mail specificato dal cliente. Potrebbe essere diverso dall'indirizzo e-mail della sottoscrizione di Azure di un cliente. |
| SkuId | ID SKU come definito durante la pubblicazione. Un'offerta può includere molti SKU, ma uno SKU può essere associato solo a una singola offerta. |

> [!NOTE]
> Tutti i report e le informazioni dettagliate per l'opzione di pubblicazione delle transazioni sono disponibili nella sezione di analisi del Centro per i partner.

## <a name="billing-questions-and-support"></a>Domande sulla fatturazione e supporto
Per il supporto sulla fatturazione, contattare il [supporto per gli editori](https://partner.microsoft.com/support/v2/?stage=1) del marketplace commerciale.

## <a name="next-step"></a>passaggio successivo

- [Riepiloghi proventi](./payout-summary.md)
