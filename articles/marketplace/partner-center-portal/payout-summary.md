---
title: Riepilogo pagamenti del Marketplace commerciale | Azure Marketplace
description: Il riepilogo dei pagamenti Mostra i dettagli relativi ai soldi ottenuti con l'offerta. Sono visualizzate anche indicazioni su quando riceverai i pagamenti e sulle modalità di pagamento.
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 997014263f0d12e7cc16a761152870c2ee63d6ec
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979690"
---
# <a name="payout-reporting"></a>Report sui proventi

Il [**Riepilogo dei pagamenti**](https://docs.microsoft.com/windows/uwp/publish/payout-summary) Mostra i dettagli relativi al denaro guadagnato con Microsoft. Sono visualizzate anche indicazioni su quando riceverai i pagamenti e sulle modalità di pagamento.

Se si vendono offerte in Azure Marketplace, verranno visualizzate anche le informazioni sui pagamenti riusciti nel **Riepilogo dei pagamenti**. Per ulteriori informazioni sui pagamenti di Azure Marketplace, vedere le [politiche di partecipazione Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/p/?LinkId=722436) e il [contratto di Microsoft Azure Marketplace Publisher](https://go.microsoft.com/fwlink/p/?LinkID=699560).

> [!NOTE]
> Per essere idoneo per il versamento, i proseguimenti devono raggiungere la [soglia di pagamento](payment-thresholds-methods-timeframes.md) di $50. Per informazioni dettagliate sulla soglia di pagamento, vedere questa pagina ed esaminare il [contratto di Microsoft Azure Marketplace Publisher](https://go.microsoft.com/fwlink/p/?LinkID=699560).

- [Ruoli e autorizzazione per accedere al report dei pagamenti](#roles-and-permission-to-access-the-payout-report)
- [Report sui pagamenti: differenza tra portale Cloud Partner e centro per i partner](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [Tipi di cliente](#customer-types)
- [Corelazione tra pagamenti e utilizzo](#corelation-between-payout-and-usage)
- [Download cronologia transazioni](#transaction-history-download-export)
- [Domande e supporto per la fatturazione](#billing-questions-and-support)

## <a name="roles-and-permission-to-access-the-payout-report"></a>Ruoli e autorizzazione per accedere al report dei pagamenti

| Report/pagine    | Proprietario dell'account    | Manager  | Sviluppatore | Collaboratore aziendale |  Collaboratore finanza | Addetto al marketing |
|------------------|------------------|----------|-----------|----|----|-----|
| Report di acquisizione (inclusi i dati quasi in tempo reale) | Può visualizzare | Può visualizzare | Nessun accesso | Nessun accesso | Può visualizzare | Nessun accesso |
| Report Feedback/risposte | Può visualizzare e inviare feedback | Può visualizzare e inviare feedback | Può visualizzare e inviare feedback | Nessun accesso | Nessun accesso | Può visualizzare e inviare feedback |
| Rapporto di stato (inclusi dati quasi in tempo reale) | Può visualizzare | Può visualizzare | Può visualizzare | Può visualizzare | Nessun accesso | Nessun accesso |
| Report Uso | Può visualizzare | Può visualizzare | Può visualizzare | Può visualizzare | Nessun accesso | Nessun accesso |
| Account per i proventi | Può aggiornare | Nessun accesso | Nessun accesso | Nessun accesso | Può aggiornare | Nessun accesso |
| Profilo fiscale | Può aggiornare | Nessun accesso | Nessun accesso | Nessun accesso | Può aggiornare | Nessun accesso |
| Riepilogo proventi | Può visualizzare | Nessun accesso | Nessun accesso | Nessun accesso | Può visualizzare | Nessun accesso |

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>Report sui pagamenti: differenza tra portale Cloud Partner e centro per i partner

| | Portale per Cloud Partner | Centro per i partner |
|---------|---------|---------|
| Collegamenti | https://cloudpartner.azure.com/ | https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory e https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| Spostamento | Report sui pagamenti forniti in Insights payout | Report sui pagamenti forniti nel centro per i partner-icona di pagamento |
| Ambito | <ul> <li>La transazione per ogni elemento linea è visibile, per la raccolta in corso, raccolta e a pagamento </li> <li>Creazione di report: Mostra tutte le voci dopo la creazione dell'ordine di acquisto, incluse le raccolte in corso e la fatturazione in corso e lo stato della raccolta e le voci non ancora idonee per il pagamento. </li> </ul> | <ul> <li>Mostra le voci quando vengono ritenute idonee per i guadagni.</li> <li>I clienti pagano prima di tutto Microsoft, quindi gli ISV possono visualizzare il report dei pagamenti a partire da.</li> <li>Il report sui pagamenti non visualizzerà la raccolta in corso e la fatturazione in corso.  </li> </ul>  |
| Transazione non pronta per il pagamento | Fatturazione in corso | Successivo pagamento stimato: lo stato del pagamento è nello stato non elaborato.  |
| Stato del pagamento |  | Non elaborati <br> Il guadagno è idoneo per il pagamento. Rimane in questo stato per un periodo di raffreddamento come definito nella guida del programma per il programma incentive. <br> <br> Imminente <br> Ordine di pagamento-verifiche interne in sospeso generate prima dell'elaborazione del pagamento. <br> <br> Inviato: <br> Il pagamento è stato inviato alla banca. |

## <a name="customer-types"></a>Tipi di cliente

### <a name="enterprise-agreement"></a>Contratto Enterprise Agreement

Per i clienti senza un contratto Enterprise Agreement appropriato le licenze del software del marketplace vengono fatturate mensilmente. Per i clienti con Enterprise Agreement viene emessa una fattura mensile inviata ogni trimestre.

### <a name="credit-cards-and-monthly-invoice"></a>Carte di credito e fattura mensile

I clienti possono anche pagare con una carta di credito e una fattura mensile. In questo caso, i costi di licenza software verranno fatturati mensilmente.

### <a name="csp-and-direct-pay-users"></a>Utenti con pagamento diretto e CSP

Ad esempio, se il cliente acquista utilizzando una carta di credito.

## <a name="corelation-between-payout-and-usage"></a>Corelazione tra pagamenti e utilizzo

|Description    |    Data  | Ordini/utilizzo  | Pagamento |
|----------|----------|-----------|-------------|
|Periodo dell'ordine   | 15 agosto, 2019-30 agosto, 2019 | **Ordini degli attributi di correlazione** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Utilizzo** <br> <ul> <li>CustomerId </li> <li>Nome del cliente</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Costo esteso stimato <br> Estimated Payout (PC) </li> </ul> |  |
|Fine periodo (mese)   | 30 agosto 2019 | | |
|Data di fatturazione | 1 settembre 2019 | | |
|Data pagamento cliente | 1 settembre 2019 | | |
|Periodo deposito (solo carte di credito, 30 giorni) | 1 settembre 2019-30 settembre, 2019 | | **Ordini degli attributi di correlazione:** <br> <ul><li>AssetId</li> <li>ID cliente</li> <li> Nome del cliente</li> </ul> <br> **Utilizzo** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nome del cliente</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>earningAmountInLastPaymentCurrency</li> </ul> <br> **Stato del pagamento:** Non elaborati |
|Inizio periodo riscossione | 1 settembre 2019 | | |
|Fine periodo riscossione (massimo 30 giorni) | 30 settembre, 2019 | | |
|Data calcolo proventi (il giorno 15 di ogni mese) | 1 ° ottobre 2019 | | **Attributi di correlazione** <br> <ul><li>AssetId</li> <li>ID cliente</li> <li>Nome del cliente</li> </ul> <br> **Utilizzo** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nome del cliente</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>earningAmountInLastPaymentCurrency</li> </ul> <br> **Stato del pagamento:** Imminente |
|Payout Date | 15 ottobre 2019 | | **Attributi di correlazione** <br> <ul><li>AssetId</li> <li>ID cliente</li> <li> Nome del cliente</li> </ul> <br> **Utilizzo** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nome del cliente</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>earningAmountInLastPaymentCurrency</li> </ul> <br> **Stato del pagamento:** Pagamento inviato |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>Contratto Enterprise Agreement (clienti trimestrali/mensili)

| Description |    Data  | Uso | Pagamento |
|----------|----------|---------|-----------|
|Periodo dell'ordine | 15 agosto, 2019-30 agosto, 2019 | **Ordini degli attributi di correlazione** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Report Uso** <br> <ul> <li>CustomerId </li> <li>Nome del cliente</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Costo esteso stimato <br> Estimated Payout (PC) </li> </ul> | |
|Fine periodo (trimestre) | 30 settembre, 2019 | | |
|Data di fatturazione | 15 ottobre 2019 | | |
|Periodo deposito (solo carte di credito, 30 giorni) | N/D | | |
|Inizio periodo riscossione | 15 ottobre 2019 | | |
|Solo carte di credito, 30 giorni | 1 Nov 2019-30 nov, 2019 | | |
|Fine periodo riscossione (massimo 90 giorni) | 15 gennaio 2020 | | |
|Data pagamento cliente | 30 dicembre 2019 | | |
|Calcolo pagamenti | 15 gennaio 2020 | | |
|Payout Date | 15 febbraio 2020 | | **Per i clienti basati su trimestre** <br> <br> **Report ordini** <br> <ul><li>AssetId</li> <li>ID cliente</li> <li> Nome del cliente</li> </ul> <br> **Utilizzo** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nome del cliente</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>earningAmountInLastPaymentCurrency</li> </ul> <br> **Stato del pagamento:** inviato |

## <a name="transaction-history-download-export"></a>Esportazione Download cronologia transazioni

Questa opzione consente di scaricare ogni elemento della riga di acquisizione visualizzato nella pagina Cronologia transazioni, il tipo di dati, la data, il numero di transazioni associato, il cliente, il prodotto e altri dettagli transazionali applicabili al programma incentives.

| Nome colonna     | Description    |
|-------------|-------------------------------|
| earningId                      | Identificatore univoco per ogni guadagno                                                                                                       |
| participantId                  | L'identità principale del partner che si guadagna sotto il programma                                                                            |
| participantIdType              | Per la maggior parte dell'ID programma per incentivare programmi e venditori se per i programmi di Store e Azure Marketplace                                          |
| operatore partecipante                | Nome del partner di guadagno                                                                                                              |
| partnerCountryCode             | Località/paese del partner di guadagno                                                                                                  |
| Nomeprogramma                    | Nome programma incentive/Store                                                                                                             |
| transactionId                  | Identificatore univoco per la transazione                                                                                                    |
| Transazione            | Valuta in cui si è verificata la transazione del cliente originale (non si tratta della valuta della località del partner)                                     |
| transactionDate                | Data della transazione. Utile per i programmi in cui molte transazioni contribuiscono a un guadagno                                           |
| transactionExchangeRate        | Tasso di cambio utilizzato per visualizzare l'importo di transazione USD corrispondente                                                                 |
| transactionAmount              | Importo della transazione nella valuta di transazione originale in base al quale viene generato il guadagno                                              |
| transactionAmountUSD           | Importo transazione in USD                                                                                                                |
| leva                          | Indica la regola business per il guadagno                                                                                                  |
| earningRate                    | Frequenza di incentivazione applicata alla quantità di transazioni per generare un guadagno                                                                      |
| quantity                       | Varia in base al programma. Indica la quantità fatturata per i programmi transazionali                                                            |
| quantityType                   | Indica il tipo di quantità, ad esempio: importo fatturato, MAU                                                                                     |
| earningType                    | Indica se è Fee, rebate, Coop, sell e così via.                                                                                          |
| earningAmount                  | Accumulo di quantità nella valuta di transazione originale                                                                                      |
| earningAmountUSD               | Guadagnare importo in USD                                                                                                                    |
| earningDate                    | Data del guadagno                                                                                                                      |
| calculationDate                | Data di calcolo del guadagno nel sistema                                                                                            |
| earningExchangeRate            | Tasso di cambio usato per visualizzare l'importo USD corrispondente                                                                                  |
| exchangeRateDate               | Data del tasso di cambio usato per calcolare EarningAmount USD                                                                                   |
| paymentAmountWOTax             | Guadagnare quantità (senza tassa) in pagamento alla valuta per i soli pagamenti "inviati"                                                                 |
| paymentCurrency                | Paga in valuta scelta dal partner nel profilo di pagamento. Visualizzato solo per i pagamenti inviati                                                   |
| paymentExchangeRate            | Tasso di cambio usato per calcolare paymentAmountWOTax in valuta pagamento con ExchangeRateDate                                            |
| paymentId            | Identificatore univoco per il pagamento. Questo numero è visibile nell'istruzione Bank                                            |
| paymentStatus            | Stato del pagamento                                            |
| paymentStatusDescription            | Descrizione descrittiva dello stato dei pagamenti                                            |
| customerId                     | Sarà sempre vuoto                                                                                                                     |
| customerName                   | Sarà sempre vuoto                                                                                                                     |
| partNumber                     | Sarà sempre vuoto                                                                                                                     |
| productName                    | Nome prodotto collegato alla transazione                                                                                                       |
| productId                      | Identificatore univoco del prodotto                                                                                                                |
| parentProductId                | Identificatore univoco del prodotto padre. Nota: se non è presente un prodotto padre per la transazione, ID prodotto padre = ID prodotto. |
| parentProductName              | Nome del prodotto padre. Nota: se non è presente un prodotto padre per la transazione, il nome del prodotto padre è il nome del prodotto.   |
| productType                    | Tipo di prodotto (ad esempio app, componente aggiuntivo, gioco, ecc.)                                                                                        |
| invoiceNumber                  | Numero fattura (applicabile solo per EA)                                                                                                  |
| resellerId                     | Identificatore rivenditore                                                                                                                      |
| resellerName                   | Nome rivenditore                                                                                                                            |
| transactionType                | Tipo di transazione (ad esempio, acquisto, rimborso, storno, rifiuto di addebito e così via)                                                               |
| localProviderSeller            | Provider locale/venditore del record                                                                                                          |
| taxRemitted                    | Importo delle imposte versate (imposte di vendita, di uso o IVA/GST).                                                                                   |
| taxRemitModel                  | Parte responsabile per il versamento delle imposte (imposte di vendita, di uso o IVA/GST).                                                                    |
| storeFee                       | Importo mantenuto da Microsoft come tariffa per rendere disponibile l'app o il componente aggiuntivo nello Store.                                            |
| transactionPaymentMethod       | Strumento di pagamento usato dal cliente per la transazione, ad esempio, carta di credito, operatore di telefonia mobile, PayPal e così via.                                |
| tpan                           | Indica la rete ad di terze parti                                                                                                     |
| customerCountry                | Paese del cliente                                                                                                                         |
| customerCity                   | Città del cliente                                                                                                                            |
| customerState                  | Stato del cliente                                                                                                                           |
| customerZip                    | Codice postale del cliente                                                                                                                 |
| ID tenant                       |                                                                                                                                          |
| externalReferenceId            | Identificatore univoco per il programma                                                                                                        |
| externalReferenceIdLabel       | Etichetta identificatore univoco                                                                                                                  |
| transactionCountryCode       | Codice paese in cui si è verificata la transazione                                                                                                                  |
| taxCountry       | Venduto al paese del cliente                                                                                                                  |
| taxState       | Venduto allo stato del cliente                                                                                                                  |
| taxCity       | Venduto alla città del cliente                                                                                                                  |
| taxZipCode       | Venduto al file zip del cliente                                                                                                                  |
| LicensingProgramName       |                                                                                                                   |
| Codice programma       | Stringa da mappare con il nome del programma                                                                                                                   |
| earningAmountInLastPaymentCurrency       | Acquisizione dell'importo nell'ultima valuta di pagamento (il campo sarà vuoto, se non sono stati pagati pagamenti precedenti)                                                                                                                   |
| lastPaymentCurrency       | Ultima valuta pagamenti (il campo sarà vuoto, se non è stato pagato alcun pagamento precedente)                                                                                                                   |
| AssetId       | Identificatore univoco per gli ordini dei clienti per il servizio Marketplace.  Rappresenta gli elementi delle righe di acquisto transazionali. Possono essere presenti più asset.                                                                                                                   |
| OrderId       | relazione con la fattura di un cliente                                                                                                                   |
| LineItemId       | riga singola nella fattura di un cliente                                                                                                                   |
| Customer Country       | Nome del paese fornito dal cliente.  Questa situazione potrebbe essere diversa da quella del paese nella sottoscrizione di Azure di un cliente.                                                                                                                   |
| EmailAddress cliente       | Indirizzo di posta elettronica fornito dal cliente finale.  Questo può essere diverso dall'indirizzo di posta elettronica nella sottoscrizione di Azure di un cliente.                                                                                                                   |
| SkuId       | ID SKU come definito durante la pubblicazione. Un'offerta può avere molti SKU, ma uno SKU può essere associato solo a una singola offerta.                                                                                                                   |

>[!Note]
>Tutti i report e le informazioni dettagliate per l'opzione di pubblicazione delle transazioni sono disponibili tramite la sezione Insights della sezione portale Cloud Partner o Analytics del centro per i partner.

## <a name="billing-questions-and-support"></a>Domande sulla fatturazione e supporto

Per ottenere assistenza sulle domande di fatturazione, contattare il [supporto tecnico per l'editore del Marketplace commerciale](https://aka.ms/marketplacepublishersupport).
