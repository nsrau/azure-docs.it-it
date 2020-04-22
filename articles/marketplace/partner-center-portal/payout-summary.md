---
title: Riepilogo dei pagamenti del mercato commerciale Azure Marketplace
description: Il riepilogo dei proventi mostra i dettagli sui soldi che hai guadagnato con la tua offerta. Sono visualizzate anche indicazioni su quando riceverai i pagamenti e sulle modalità di pagamento.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 5a930dfb38007349155581424d03ee7b3e7a6b46
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730213"
---
# <a name="payout-reporting"></a>Segnalazioni sui proventi

Il [**riepilogo dei proventi**](https://docs.microsoft.com/windows/uwp/publish/payout-summary) mostra i dettagli sui soldi guadagnati con Microsoft. Sono visualizzate anche indicazioni su quando riceverai i pagamenti e sulle modalità di pagamento.

Se si vendono offerte in Azure Marketplace, verranno visualizzate anche informazioni sui pagamenti riusciti nel **riepilogo dei proventi.** Per altre informazioni sul pagamento di Azure Marketplace, vedere Criteri di [partecipazione](https://go.microsoft.com/fwlink/p/?LinkId=722436) di Microsoft Azure Marketplace e [Contratto di pubblicazione](https://go.microsoft.com/fwlink/p/?LinkID=699560)di Microsoft Azure Marketplace .

> [!NOTE]
> Per essere idoneo al pagamento, i tuoi proventi devono raggiungere la soglia di [pagamento](payment-thresholds-methods-timeframes.md) di 50 USD. Per informazioni dettagliate sulla soglia di pagamento, vedere questa pagina ed esaminare il [Contratto](https://go.microsoft.com/fwlink/p/?LinkID=699560)di pubblicazione di Microsoft Azure Marketplace .

- [Ruoli e autorizzazioni per accedere al rapporto di pagamento](#roles-and-permission-to-access-the-payout-report)
- [Rapporto sui proventi: differenza tra il portale per i partner cloud e il Centro per i partner](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [Tipi di clienti](#customer-types)
- [Corelation tra payout e utilizzo](#corelation-between-payout-and-usage)
- [Download della cronologia delle transazioni](#transaction-history-download-export)
- [Domande sulla fatturazione e supporto](#billing-questions-and-support)

## <a name="roles-and-permission-to-access-the-payout-report"></a>Ruoli e autorizzazioni per accedere al rapporto di pagamento

| Rapporti/Pagine    | Proprietario dell'account    | Manager  | Developer | Collaboratore aziendale |  Collaboratore finanziario | Addetto al marketing |
|------------------|------------------|----------|-----------|----|----|-----|
| Report di acquisizione (inclusi i dati quasi in tempo reale) | Può visualizzare | Può visualizzare | Nessun accesso | Nessun accesso | Può visualizzare | Nessun accesso |
| Report Feedback/risposte | Può visualizzare e inviare feedback | Può visualizzare e inviare feedback | Può visualizzare e inviare feedback | Nessun accesso | Nessun accesso | Può visualizzare e inviare feedback |
| Rapporto sull'integrità (inclusi i dati quasi in tempo reale) | Può visualizzare | Può visualizzare | Può visualizzare | Può visualizzare | Nessun accesso | Nessun accesso |
| Report Uso | Può visualizzare | Può visualizzare | Può visualizzare | Può visualizzare | Nessun accesso | Nessun accesso |
| Account per i proventi | Può aggiornare | Nessun accesso | Nessun accesso | Nessun accesso | Può aggiornare | Nessun accesso |
| Profilo fiscale | Può aggiornare | Nessun accesso | Nessun accesso | Nessun accesso | Può aggiornare | Nessun accesso |
| Riepilogo proventi | Può visualizzare | Nessun accesso | Nessun accesso | Nessun accesso | Può visualizzare | Nessun accesso |

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>Rapporto sui proventi: differenza tra il portale per i partner cloud e il Centro per i partner

| | Portale per Cloud Partner | Centro per i partner |
|---------|---------|---------|
| Collegamenti | [https://cloudpartner.azure.com/](https://cloudpartner.azure.com) | [https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory](https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory)E[https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
| Navigazione | Rapporti sui proventi forniti in Insights Payout | Report sui proventi forniti nel Centro per i partner - Icona dei proventi |
| Scope | <ul> <li>La transazione per elemento pubblicitario è visibile, per la riscossione in corso, raccolta e pagata </li> <li>Report: mostra tutte le voci una volta creato l'ordine fornitore, inclusa la raccolta in corso e la fatturazione in corso e lo stato della raccolta e gli elementi pubblicitari che non sono ancora idonei per essere pagati. </li> </ul> | <ul> <li>Mostra gli elementi pubblicitari una volta che sono stati considerati entrate idonee.</li> <li>I clienti pagano prima a Microsoft e quindi gli ISV possono visualizzare l'inizio del report di pagamento.</li> <li>Il rapporto sui proventi non mostrerà la riscossione in corso e la fatturazione in corso.  </li> </ul>  |
| Transazione non pronta per il pagamento | Fatturazione in corso | Pagamento stimato successivo: lo stato del pagamento è nello stato non elaborato.  |
| Stato di pagamento |  | Non elaborato: <br> Il guadagno è idoneo per il pagamento. Rimane in questo stato per un periodo di raffreddamento come definito nella guida del programma per il programma di incentivi. <br> <br> Prossimi: <br> Recensioni interne in sospeso generate dall'ordine di pagamento prima dell'elaborazione del pagamento. <br> <br> Inviato: <br> Il pagamento è stato inviato alla tua banca. |

## <a name="customer-types"></a>Tipi di clienti

### <a name="enterprise-agreement"></a>Contratto Enterprise

Per i clienti senza un contratto Enterprise Agreement appropriato le licenze del software del marketplace vengono fatturate mensilmente. Per i clienti con Enterprise Agreement viene emessa una fattura mensile inviata ogni trimestre.

### <a name="credit-cards-and-monthly-invoice"></a>Carte di credito e fattura mensile

I clienti possono anche pagare con una carta di credito e una fattura mensile. In questo caso, le spese di licenza software verranno fatturate mensilmente.

### <a name="csp-and-direct-pay-users"></a>Utenti CSP e Direct Pay

Ad esempio, se il cliente acquista utilizzando una carta di credito.

## <a name="corelation-between-payout-and-usage"></a>Corelation tra payout e utilizzo

|Descrizione    |    Data  | Ordini/Utilizzo  | Proventi |
|----------|----------|-----------|-------------|
|Periodo dell'ordine   | 15 agosto 2019 - 30 agosto 2019 | **Attributi di correlazione Ordini** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Uso** <br> <ul> <li>CustomerId </li> <li>Nome del cliente</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Addebito esteso stimato <br> Estimated Payout (PC) </li> </ul> |  |
|Fine periodo (mese)   | 30 agosto 2019 | | |
|Data di fatturazione | 1 settembre 2019 | | |
|Data pagamento cliente | 1 settembre 2019 | | |
|Periodo deposito (solo carte di credito, 30 giorni) | 1 settembre 2019 - 30 settembre 2019 | | **Ordini attributi di correlazione:** <br> <ul><li>AssetId</li> <li>ID cliente</li> <li> Nome del cliente</li> </ul> <br> **Uso** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nome del cliente</li> <li>OrderId</li> <li>LineItemId (Elemento LineItemId)</li> <li>importo transazione</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Stato pagamento:** Non elaborato |
|Inizio periodo riscossione | 1 settembre 2019 | | |
|Fine periodo riscossione (massimo 30 giorni) | 30 settembre 2019 | | |
|Data calcolo proventi (il giorno 15 di ogni mese) | 1o ottobre 2019 | | **Attributi di correlazione** <br> <ul><li>AssetId</li> <li>ID cliente</li> <li>Nome del cliente</li> </ul> <br> **Uso** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nome del cliente</li> <li>OrderId</li> <li>LineItemId (Elemento LineItemId)</li> <li>importo transazione</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Stato pagamento:** Prossimi |
|Payout Date | giovedì 15 ottobre 2019 | | **Attributi di correlazione** <br> <ul><li>AssetId</li> <li>ID cliente</li> <li> Nome del cliente</li> </ul> <br> **Uso** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nome del cliente</li> <li>OrderId</li> <li>LineItemId (Elemento LineItemId)</li> <li>importo transazione</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Stato pagamento:** Pagamento inviato |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>Contratto Enterprise (clienti trimestrali/mensili)

| Descrizione |    Data  | Uso | Proventi |
|----------|----------|---------|-----------|
|Periodo dell'ordine | 15 agosto 2019 - 30 agosto 2019 | **Attributi di correlazione ordini** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Report sull'utilizzo** <br> <ul> <li>CustomerId </li> <li>Nome del cliente</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Addebito esteso stimato <br> Estimated Payout (PC) </li> </ul> | |
|Fine periodo (trimestre) | 30 settembre 2019 | | |
|Data di fatturazione | giovedì 15 ottobre 2019 | | |
|Periodo deposito (solo carte di credito, 30 giorni) | n/d | | |
|Inizio periodo riscossione | giovedì 15 ottobre 2019 | | |
|Solo carte di credito, 30 giorni | Novembre 1, 2019 - 30 novembre 2019 | | |
|Fine periodo riscossione (massimo 90 giorni) | 15 gennaio 2020 | | |
|Data pagamento cliente | dicembre 30, 2019 | | |
|Calcolo dei proventi | 15 gennaio 2020 | | |
|Payout Date | 15 feb 2020 | | **Per i clienti trimestrali** <br> <br> **Report Ordini** <br> <ul><li>AssetId</li> <li>ID cliente</li> <li> Nome del cliente</li> </ul> <br> **Uso** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nome del cliente</li> <li>OrderId</li> <li>LineItemId (Elemento LineItemId)</li> <li>importo transazione</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Stato pagamento:** inviato |

## <a name="transaction-history-download-export"></a>Esportazione download cronologia transazioni

Questa opzione fornisce un download di ogni elemento pubblicitario di reddito visualizzato nella pagina Cronologia transazioni, tipo di guadagno, data, importo della transazione associato, cliente, prodotto e altri dettagli transazionali applicabili al programma Incentivi.

| Nome colonna     | Descrizione    |
|-------------|-------------------------------|
| earningId                      | Identificatore univoco per ogni guadagno                                                                                                       |
| IDpartecipante                  | L'identità primaria del partner che guadagna nell'ambito del programma                                                                            |
| participantIdType (IdType di partecipante)              | Per lo più ID programma per i programmi di incentivi e Venditore SE per i programmi Store e Azure Marketplace                                          |
| participantName                | Nome del partner                                                                                                              |
| partnerCountryCode             | Ubicazione/paese del partner                                                                                                  |
| nomeprogramma                    | Nome programma incentivo/archivio                                                                                                             |
| transactionId                  | Identificatore univoco della transazione                                                                                                    |
| transactionValuta            | Valuta in cui si è verificata la transazione cliente originale (non è la valuta di ubicazione del partner)                                     |
| transactionDate (DataTransazione)                | Data della transazione. Utile per i programmi in cui molte transazioni contribuiscono                                           |
| transactionExchangeRate (tasso di transazione)        | Tasso di cambio utilizzato per visualizzare l'importo in USD della transazione corrispondente                                                                 |
| importo transazione              | Importo della transazione nella valuta della transazione originale in base al quale viene generato il reddito                                              |
| transactionAmountUSD           | Importo della transazione in USD                                                                                                                |
| Leva                          | Indica la regola business per                                                                                                  |
| earningRate                    | Tasso di incentivo applicato alla transazione per generare un                                                                      |
| quantity                       | Varia in base al programma. Indica la quantità fatturata per i programmi transazionali                                                            |
| quantityTipo                   | Indica il tipo di quantità, ad esempio: Quantità fatturata, MAU                                                                                     |
| earningType                    | Indica se è a pagamento, sconto, coop, vendere ecc.                                                                                          |
| earningAmount                  | Importo guadagno nella valuta della transazione originale                                                                                      |
| earningAmountUSD               | Importo degli guadagnati in USD                                                                                                                    |
| earningDate                    | Data del guadagno                                                                                                                      |
| calculationDate                | Data in cui il guadagno è stato calcolato nel sistema                                                                                            |
| earningExchangeRate (tasso di cambio di reddito)            | Tasso di cambio utilizzato per mostrare l'importo in USD corrispondente                                                                                  |
| exchangeRateDate               | Data del tasso di cambio utilizzata per calcolare EarningAmount USD                                                                                   |
| paymentAmountWOTax             | Importo del guadagno (senza IVA) nella valuta Di pay To solo per i pagamenti "Inviati"                                                                 |
| pagamentoValuta                | Pagare alla valuta scelta dal partner nel profilo di pagamento. Mostrato solo per i pagamenti inviati                                                   |
| pagamentoExchangeRate            | Tasso di cambio utilizzato per calcolare paymentAmountWOTax nella valuta di pagamento utilizzando ExchangeRateDate                                            |
| paymentId (id pagamento)            | Identificatore univoco del pagamento. Questo numero è visibile nell'estratto conto                                            |
| paymentStatus (Statopagamento)            | Stato del pagamento                                            |
| paymentStatusDescription (descrizione pagamento)            | Descrizione amichevole dello stato del pagamento                                            |
| customerId                     | Sarà sempre vuoto                                                                                                                     |
| customerName                   | Sarà sempre vuoto                                                                                                                     |
| partNumber                     | Sarà sempre vuoto                                                                                                                     |
| Productname                    | Nome del prodotto collegato alla transazione                                                                                                       |
| productId                      | Identificatore univoco del prodotto                                                                                                                |
| parentProductId                | Identificatore univoco del prodotto padre. Nota: se non è presente un prodotto padre per la transazione, l'ID prodotto padre è l'ID prodotto. |
| parentProductName (NomeProdotto)              | Nome del prodotto padre. Nota: se non è presente un prodotto padre per la transazione, allora nome prodotto padre - Nome prodotto.   |
| productType                    | Tipo di prodotto (ad esempio App, Add-on, Gioco, ecc.)                                                                                        |
| invoiceNumber (numerodi fattura)                  | Numero di fattura (applicabile solo per EA)                                                                                                  |
| resellerId                     | Identificatore rivenditore                                                                                                                      |
| resellerName                   | Nome rivenditore                                                                                                                            |
| Transactiontype                | Tipo di transazione (ad esempio, acquisto, rimborso, storno, rifiuto di addebito e così via)                                                               |
| localProviderSeller (fornitore locale)            | Fornitore locale/venditore di record                                                                                                          |
| tassaRemesso                    | Importo delle imposte versate (imposte di vendita, di uso o IVA/GST).                                                                                   |
| taxRemitModello                  | Parte responsabile per il versamento delle imposte (imposte di vendita, di uso o IVA/GST).                                                                    |
| StoreFee                       | Importo trattenuto da Microsoft come costo per rendere disponibile l'app o il componente aggiuntivo nello Store.                                            |
| TransactionPaymentMethod (Metodo di pagamento)       | Strumento di pagamento usato dal cliente per la transazione, ad esempio, carta di credito, operatore di telefonia mobile, PayPal e così via.                                |
| tpan                           | Indica la rete pubblicitaria di terze parti                                                                                                     |
| customerPaese                | Paese cliente                                                                                                                         |
| customerCity                   | Città del cliente                                                                                                                            |
| customerState (Statocliente)                  | Stato cliente                                                                                                                           |
| customer-ip (informazioni in cravatta                    | Codice postale del cliente                                                                                                                 |
| ID tenant                       |                                                                                                                                          |
| externalReferenceId (idRiferimento esterno)            | Identificatore univoco del programma                                                                                                        |
| externalReferenceIdLabel (etichettatura externalReferenceIdLabel)       | Etichetta identificatore univoco                                                                                                                  |
| transactionCountryCode       | Codice paese in cui si è verificata la transazione                                                                                                                  |
| taxCountry       | Venduto al paese del cliente                                                                                                                  |
| TaxState       | Venduto allo Stato cliente                                                                                                                  |
| taxCity       | Venduto alla città del cliente                                                                                                                  |
| codice taxzip       | Venduto al cliente zip                                                                                                                  |
| Nome Programma licenze       |                                                                                                                   |
| Codice programma       | Stringa di cui eseguire il mapping con il nome del programma                                                                                                                   |
| earningAmountInLastPaymentCurrency       | Importo del guadagno nell'ultima valuta di pagamento (campo sarà vuoto, se non sono stati pagati pagamenti precedenti)                                                                                                                   |
| lastPaymentCurrency (valutadiadilasta)       | Valuta ultimo pagamento (campo sarà vuoto, se non è stato pagato alcun pagamento precedente)                                                                                                                   |
| AssetId       | Identificatore univoco degli ordini cliente per il servizio di marketplace.  Rappresenta gli elementi pubblicitari di acquisto transazionali. Possono essere presenti più risorse.                                                                                                                   |
| OrderId       | si riferisce alla fattura di un cliente                                                                                                                   |
| LineItemId (Elemento LineItemId)       | singola riga nella fattura di un cliente                                                                                                                   |
| Customer Country       | Nome del paese fornito dal cliente.  Questo potrebbe essere diverso dal paese nella sottoscrizione di Azure di un cliente.                                                                                                                   |
| E-mail del clienteIndirizzo       | Indirizzo e-mail fornito dal cliente finale.  Questo potrebbe essere diverso dall'indirizzo di posta elettronica nella sottoscrizione di Azure di un cliente.                                                                                                                   |
| SkuId (informazioni in lingua lingua inlingua)       | ID SKU come definito durante la pubblicazione. Un'offerta può includere molti SKU, ma uno SKU può essere associato solo a una singola offerta.                                                                                                                   |

>[!Note]
>Tutti i report e le informazioni dettagliate per l'opzione di pubblicazione delle transazioni sono disponibili tramite la sezione Insights del portale per i partner cloud o della sezione Analisi del Centro per i partner.

## <a name="billing-questions-and-support"></a>Domande sulla fatturazione e supporto

Per ottenere assistenza per le domande sulla fatturazione, contattare il [supporto editore del marketplace commerciale.](https://partner.microsoft.com/support/v2/?stage=1)
