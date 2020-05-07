---
title: Panoramica di riepilogo pagamenti-Azure Marketplace
description: Il riepilogo dei pagamenti Mostra i dettagli relativi ai soldi ottenuti con l'offerta. Sono visualizzate anche indicazioni su quando riceverai i pagamenti e sulle modalità di pagamento.
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 48644f2f8148a7aa1974be0d7f761e9b3a55612d
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783513"
---
# <a name="payout-summary-overview"></a>Panoramica Riepilogo pagamenti

Il [Riepilogo dei pagamenti](./payout-summary.md) Mostra i dettagli relativi al denaro guadagnato con Microsoft. Consente inoltre di stabilire quando si riceveranno i pagamenti e la quantità di pagamento.

Se si vendono offerte in Azure Marketplace, verranno visualizzate anche le informazioni sui pagamenti riusciti nel riepilogo dei pagamenti. Per altre informazioni sui pagamenti di Azure Marketplace, vedere [criteri di partecipazione ad Azure Marketplace](https://docs.microsoft.com/legal/marketplace/participation-policy) e [contratto di Microsoft Azure Marketplace editore](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt).

> [!NOTE]
> Per essere idoneo per il versamento, i proseguimenti devono raggiungere la [soglia di pagamento](./payment-thresholds-methods-timeframes.md) di $50. Per informazioni dettagliate sulla soglia di pagamento, vedere il [contratto di Microsoft Azure Marketplace Publisher](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt).

Tutti i report e le informazioni dettagliate per l'opzione di pubblicazione delle transazioni sono disponibili nella sezione Analytics del centro per i partner, a cui si accede usando questa icona nell'angolo superiore destro del portale:

![Viene illustrata l'icona del payout nell'angolo superiore destro del portale del centro per i partner.](./media/payout-overview.png)

## <a name="roles-and-permissions"></a>Ruoli e autorizzazioni

Questi sono i ruoli e le autorizzazioni per accedere al report dei pagamenti:

| Report/pagine | Proprietario dell'account | Manager | Sviluppatore | Collaboratore aziendale | Collaboratore finanza | Addetto al marketing |
| --- | --- | --- | --- | --- | --- | --- |
| Report di acquisizione (inclusi i dati quasi in tempo reale) | Può visualizzare | Può visualizzare | Nessun accesso | Nessun accesso | Può visualizzare | Nessun accesso |
| Report Feedback/risposte | Può visualizzare e inviare feedback | Può visualizzare e inviare feedback | Può visualizzare e inviare feedback | Nessun accesso | Nessun accesso | Può visualizzare e inviare feedback |
| --- | --- | --- | --- | --- | --- | --- |
| Rapporto di stato (inclusi dati quasi in tempo reale) | Può visualizzare | Può visualizzare | Può visualizzare | Può visualizzare | Nessun accesso | Nessun accesso |
| Report Uso | Può visualizzare | Può visualizzare | Può visualizzare | Può visualizzare | Nessun accesso | Nessun accesso |
| Account per i proventi | Può aggiornare | Nessun accesso | Nessun accesso | Nessun accesso | Può aggiornare | Nessun accesso |
| Profilo fiscale | Può aggiornare | Nessun accesso | Nessun accesso | Nessun accesso | Può aggiornare | Nessun accesso |
| Riepilogo proventi | Può visualizzare | Nessun accesso | Nessun accesso | Nessun accesso | Può visualizzare | Nessun accesso  |
| | | | | | | |

## <a name="payout-report-differences"></a>Differenze tra i report di pagamento

Queste sono le differenze nel report dei pagamenti tra portale Cloud Partner (precedente) e centro per i partner (nuovo):

| Portale per Cloud Partner | Centro per i partner |
| --- | --- |
| **Collegamento**:https://cloudpartner.azure.com/ | **Collegamento**: https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory ehttps://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **Navigazione**: report sui pagamenti forniti in Insights payout | **Navigazione**: report di pagamento fornito nel centro per i partner-icona di pagamento |
| **Ambito**:<ul><li>La transazione per ogni elemento linea è visibile, per la raccolta in corso, raccolta e a pagamento.</li><li>Creazione di report: Mostra tutte le voci dopo la creazione dell'ordine di acquisto, incluse le raccolte in corso e la fatturazione in corso e lo stato della raccolta e le voci non ancora idonee per il pagamento.</li></ul> | **Ambito**:<ul><li>Mostra le voci dopo che sono state ritenute come guadagni idonei.</li><li>I clienti pagano prima di tutto Microsoft, quindi gli ISV possono visualizzare il report dei pagamenti a partire da.</li><li>Il report sui pagamenti non Mostra la raccolta in corso e la fatturazione in corso.</li></ul> |
| **Transazione non pronta per il pagamento**: fatturazione in corso | **Transazione non pronta per**il versamento: pagamento stimato successivo: lo stato del payout è nello stato non elaborato. |
| **Stato del pagamento**: n/a | **Stato del pagamento**:<ul><li>Non elaborata: il guadagno è idoneo per il pagamento.</li><li>Imminente: il guadagno verrà inviato all'editore nel successivo pagamento mensile.</li><li>Inviato: il pagamento è stato inviato alla banca.</li></ul> |
| | |

## <a name="payment-schedules"></a>Pianificazioni di pagamento

Per una descrizione delle pianificazioni dei pagamenti, inclusi i periodi di mantenimento, la visibilità del partner e quando il cliente utilizza una carta di credito o una fattura, vedere la sezione relativa alle [pianificazioni](./payout-policy-details.md#payment-schedules) dei pagamenti nell'argomento **Dettagli** del pagamento.

## <a name="transaction-history-download-export"></a>Esportazione Download cronologia transazioni

Questa opzione consente di scaricare ogni elemento della riga di guadagno visualizzato nella pagina Cronologia transazioni. Sono inclusi il tipo di acquisizione, la data, il numero di transazioni associato, il cliente, il prodotto e altri dettagli transazionali correlati al programma per gli incentivi.

| Nome colonna | Descrizione |
| --- | --- |
| earningId | Identificatore univoco per ogni guadagno |
| participantId | L'identità principale del partner che si guadagna sotto il programma |
| participantIdType | ID programma per programmi incentive e venditore se il programma è per i programmi dello Store e Azure Marketplace |
| operatore partecipante | Nome del partner di guadagno |
| partnerCountryCode | Località/paese del partner di guadagno |
| Nomeprogramma | Nome programma incentive/Store |
| transactionId | Identificatore univoco per la transazione |
| Transazione | Valuta in cui si è verificata la transazione del cliente originale (non si tratta della valuta della località del partner) |
| transactionDate | Data della transazione. Utile per i programmi in cui molte transazioni contribuiscono a un guadagno |
| transactionExchangeRate | Tasso di cambio utilizzato per visualizzare l'importo di transazione USD corrispondente |
| transactionAmount | Importo della transazione nella valuta di transazione originale in base al quale viene generato il guadagno |
| transactionAmountUSD | Importo transazione in dollari statunitensi (USD) |
| leva | Regola business per i guadagni |
| earningRate | Frequenza di incentivazione applicata alla quantità di transazioni per generare un guadagno |
| quantity | Quantità fatturata per i programmi transazionali. Varia in base al programma |
| quantityType | Tipo di quantità, ad esempio: importo fatturato, utenti attivi mensili (MAU) |
| earningType | Indica se è Fee, rebate, Coop, sell e così via |
| earningAmount | Accumulo di quantità nella valuta di transazione originale |
| earningAmountUSD | Guadagnare importo in USD |
| earningDate | Data del guadagno |
| calculationDate | Data di calcolo del guadagno nel sistema |
| earningExchangeRate | Tasso di cambio usato per visualizzare l'importo USD corrispondente |
| exchangeRateDate | Data del tasso di cambio usato per calcolare EarningAmount USD |
| paymentAmountWOTax | Guadagnare quantità (senza tassa) in pagamento alla valuta solo per &quot;i&quot; pagamenti inviati |
| paymentCurrency | Paga in valuta scelta dal partner nel profilo di pagamento. Visualizzato solo per i pagamenti inviati |
| paymentExchangeRate | Tasso di cambio usato per calcolare paymentAmountWOTax in valuta pagamento con ExchangeRateDate |
| paymentId | Identificatore univoco per il pagamento. Questo numero è visibile nell'istruzione Bank |
| paymentStatus | Stato pagamento |
| paymentStatusDescription | Descrizione dello stato di pagamento |
| customerId | Sarà sempre vuoto |
| customerName | Sarà sempre vuoto |
| partNumber | Sarà sempre vuoto |
| productName | Nome prodotto collegato alla transazione |
| productId | Identificatore univoco del prodotto |
| parentProductId | Identificatore univoco del prodotto padre. Se non è presente un prodotto padre per la transazione, ID prodotto padre = ID prodotto. |
| parentProductName | Nome del prodotto padre. Se non è presente un prodotto padre per la transazione, il nome del prodotto padre è il nome del prodotto. |
| productType | Tipo di prodotto (ad esempio app, componente aggiuntivo e gioco) |
| invoiceNumber | Numero fattura (solo per contratti Enterprise) |
| resellerId | Identificatore rivenditore |
| resellerName | Nome rivenditore |
| transactionType | Tipo di transazione, ad esempio acquisto, rimborso, inversione e chargeback. |
| localProviderSeller | Provider locale/venditore del record |
| taxRemitted | Importo delle imposte versate (imposte di vendita, di uso o IVA/GST). |
| taxRemitModel | Parte responsabile per il versamento delle imposte (imposte di vendita, di uso o IVA/GST). |
| storeFee | Importo mantenuto da Microsoft come tariffa per rendere disponibile l'app o il componente aggiuntivo nel Marketplace commerciale. |
| transactionPaymentMethod | Strumento di pagamento dei clienti usato per la transazione, ad esempio la carta, la fatturazione di mobile Carrier e PayPal |
| tpan | Rete ad di terze parti |
| customerCountry | Paese del cliente |
| customerCity | Città del cliente |
| customerState | Stato del cliente |
| customerZip | Codice postale del cliente |
| ID tenant | ID del tenant |
| externalReferenceId | Identificatore univoco per il programma |
| externalReferenceIdLabel | Etichetta identificatore univoco |
| transactionCountryCode | Codice paese in cui si è verificata la transazione |
| taxCountry | Paese del cliente |
| taxState | Stato del cliente |
| taxCity | Città del cliente |
| taxZipCode | Codice postale del cliente |
| LicensingProgramName | Nome del programma di licenza |
| Codice programma | Stringa da mappare con il nome del programma |
| earningAmountInLastPaymentCurrency | Acquisizione dell'importo nell'ultima valuta di pagamento (il campo sarà vuoto se non sono stati pagati pagamenti precedenti) |
| lastPaymentCurrency | Ultima valuta pagamenti (il campo sarà vuoto se non è stato pagato alcun pagamento precedente) |
| AssetId | Identificatore univoco per gli ordini dei clienti per il servizio Marketplace. Rappresenta le voci della riga di acquisto. Possono essere presenti più asset. |
| OrderId | Relazione con la fattura di un cliente |
| LineItemId | Riga singola nella fattura di un cliente |
| Customer Country | Nome del paese fornito dal cliente. Questa situazione potrebbe essere diversa da quella del paese nella sottoscrizione di Azure di un cliente. |
| EmailAddress cliente | Indirizzo di posta elettronica fornito dal cliente. Questo può essere diverso dall'indirizzo di posta elettronica nella sottoscrizione di Azure di un cliente. |
| SkuId | ID SKU come definito durante la pubblicazione. Un'offerta può includere molti SKU, ma uno SKU può essere associato solo a una singola offerta. |

> [!NOTE]
> Tutti i report e le informazioni dettagliate per l'opzione di pubblicazione transazioni sono disponibili nella sezione Analytics del centro per i partner.

## <a name="billing-questions-and-support"></a>Domande sulla fatturazione e supporto
Per il supporto della fatturazione, contattare il supporto per gli [editori](https://partner.microsoft.com/support/v2/?stage=1)del Marketplace commerciale.

## <a name="next-step"></a>passaggio successivo

- [Riepiloghi sui pagamenti](./payout-summary.md)
