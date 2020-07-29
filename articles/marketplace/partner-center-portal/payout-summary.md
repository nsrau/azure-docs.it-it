---
title: Report sui proventi - Marketplace commerciale di Microsoft
description: I report sui proventi consentono di visualizzare i dettagli relativi agli utili ottenuti con l'offerta, inclusi l'importo e la data dei pagamenti.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: mingshen-ms
ms.author: mingshen
ms.date: 04/24/2020
ms.openlocfilehash: dc690e29129f5be68456e6a9dc075ba72f11b121
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87318233"
---
# <a name="payout-summaries"></a>Riepiloghi proventi

Il riepilogo proventi offre informazioni dettagliate sugli utili realizzati con Microsoft. Consente anche di determinare quando si riceveranno i pagamenti e gli importi che verranno corrisposti.

Se si vendono prodotti in Azure Marketplace, nel riepilogo proventi verranno visualizzate anche informazioni sui pagamenti andati a buon fine. Per informazioni dettagliate, vedere [Criteri di partecipazione a Microsoft Azure Marketplace](https://docs.microsoft.com/legal/marketplace/participation-policy) e il [Contratto per editori di Microsoft Azure Marketplace](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt).

> [!NOTE]
> Per poter beneficiare del pagamento, gli introiti devono raggiungere la [soglia di pagamento](payment-thresholds-methods-timeframes.md) di 50 dollari statunitensi. Per informazioni dettagliate sulla soglia di pagamento, vedere questa pagina ed esaminare il contratto per sviluppatori di app.

> [!NOTE]
> Per supporto sui proventi, incluse informazioni su configurazione di account proventi, pagamenti mancanti o in attesa e altre informazioni, contattare il team di supporto [qui](https://developer.microsoft.com/windows/support).

## <a name="access-the-payout-summary-pages"></a>Accedere alle pagine di riepilogo proventi

Per aprire una delle pagine di riepilogo proventi:

1. Nell'angolo superiore destro fare clic sull'icona dei proventi.
2. Selezionare Cronologia transazioni, Pagamenti o Esportazione dati.

## <a name="transaction-history-page"></a>Pagina Cronologia transazioni

In questa pagina vengono visualizzati tutti gli utili, con informazioni quali data, tipo e importo per ogni voce. È possibile selezionare un periodo di tempo da visualizzare e filtrare in base a ID di iscrizione, programma, ID del pagamento, tipo di utili, leva e stato. I dati sono disponibili per l'anno fiscale corrente (1 luglio-30 giugno) e per i due anni fiscali precedenti.

Per visualizzare altri dettagli sugli utili, selezionare la freccia rivolta verso il basso sul lato destro della pagina. Verrà visualizzata la leva, l'importo dei ricavi e il prodotto. Se uno di questi tipi di dati non è disponibile ma è necessario accedervi, contattare il [supporto](https://developer.microsoft.com/windows/support). Se gli utili derivano da una rettifica e non da una transazione, i campi del prodotto non vengono visualizzati.

Per esportare i dati della transazione in questa pagina, utilizzare la pagina di **esportazione dati**.

## <a name="payments-page"></a>Pagina Pagamenti

I totali in questa pagina sono riferiti a tutti i programmi a cui si partecipa. È possibile filtrare in base a ID partecipante, programma, ID di pagamento e tipo di utili. Gli importi vengono specificati in dollari statunitensi. Il valore pagato viene inoltre visualizzato nella valuta di pagamento.

| Area                   | Descrizione                                                                                |
|------------------------|---------------------------------------------------------------------------------------------|
| Totale pagato questo anno   | Il totale combinato pagato nell'anno corrente, in dollari statunitensi, per tutti i programmi       |
| Pagamento successivo stimato | Il singolo pagamento successivo in previsione (anche se ne saranno presto disponibili altri), in dollari statunitensi |
| Ultimo pagamento           | La quantità (in dollari statunitensi), il nome del programma e il programma del pagamento più recente           |
| Pagamenti per origine     | Importo dei pagamenti (in dollari statunitensi) in base al programma negli ultimi 12 mesi           |
| Pagamenti               | Selezionare **Pagato** o **In sospeso** e quindi ordinare in base alle esigenze. Per ulteriori informazioni su uno specifico pagamento, selezionare **Visualizza**. Per scaricare una copia del rendiconto della rimessa di pagamento, selezionare **Scarica**. I dati della cronologia delle transazioni possono richiedere fino a 24 ore, pertanto è possibile che gli utili associati non vengano visualizzati immediatamente. |
|||

Per esportare i dati in questa pagina, selezionare **Esporta** e seguire le istruzioni visualizzate nella pagina di esportazione dati.

## <a name="transaction-history-page"></a>Pagina della cronologia transazioni

In questa pagina vengono visualizzati tutti gli utili individuali, con informazioni quali data, tipo e importo per ogni voce. È possibile selezionare un periodo di tempo da visualizzare e filtrare in base a ID di iscrizione, programma, ID del pagamento, tipo di utili, leva e stato. I dati sono disponibili per l'anno fiscale corrente (1 luglio-30 giugno) e per i due anni fiscali precedenti.

Per visualizzare altri dettagli sugli utili, selezionare la freccia rivolta verso il basso a destra nella pagina. Verranno visualizzati la leva, l'importo dei ricavi e il prodotto. Se uno di questi tipi di dati non è disponibile ma è necessario accedervi, contattare il [supporto](https://developer.microsoft.com/windows/support). Se gli utili derivano da una rettifica e non da una transazione, i campi del prodotto non vengono visualizzati.

Per esportare i dati delle transazioni in questa pagina, selezionare **Esporta** e seguire le istruzioni visualizzate nella pagina di esportazione dati. Nei file esportati dalla pagina della cronologia transazioni sono visualizzati i dati nella valuta di transazione, gli utili sia nella valuta di transazione sia in dollari statunitensi e il valore del pagamento in base alla valuta di pagamento.

## <a name="payment-status"></a>Stato dei pagamenti

| Stato degli utili           | Motivo                                                                                                                                      | È richiesta un'azione del partner?                                   |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------|
| Non elaborato              | Gli utili non sono idonei al pagamento. Rimane in questo stato per un periodo di sospensione come definito nella guida al programma per il programma Incentive. | No                                                         |
| In programma                 | Ordine di pagamento generato in attesa di revisioni interne prima dell'elaborazione del pagamento.                                                               | No                                                         |
| Pending tax invoice (Fattura fiscale in sospeso)      | La fattura fiscale è incompleta o non valida.                                                                                                  | È necessario aggiornare la fattura fiscale prima di poter essere pagati. |
| Rejected during review (Rifiutato durante la revisione)   | Il pagamento è stato rifiutato durante la revisione.                                                                                                     | Per informazioni dettagliate, contattare il [supporto tecnico Microsoft](https://developer.microsoft.com/windows/support).                      |
| Operazione non riuscita                   | Il pagamento non è riuscito a causa di un errore di sistema Microsoft.                                                                                         | Per informazioni dettagliate, contattare il [supporto tecnico Microsoft](https://developer.microsoft.com/windows/support).                      |
| In corso              | Il pagamento è in corso.                                                                                                                 | No                                                         |
| Pagamento non corretto        | È in corso il recupero del pagamento.                                                                                                       | No                                                         |
| Inviato                     | Il pagamento è stato inviato alla banca.                                                                                                     | No                                                         |
| Reprocessing (Rielaborazione)             | Durante il pagamento si è verificato un errore di sistema Microsoft e il pagamento è in fase di rielaborazione.                                                                  | No                                                         |
| Reversed                 | Il pagamento è stato stornato dalla banca e verrà inviato di nuovo al successivo ciclo di pagamento.                                                     | No                                                         |
| Tax invoice rejected (Fattura fiscale rifiutata)     | La fattura fiscale è stata rifiutata durante la revisione. Tutti i pagamenti in sospeso rimarranno in attesa fino al completamento della revisione della fattura fiscale.                 | Per informazioni dettagliate, contattare il [supporto tecnico Microsoft](https://developer.microsoft.com/windows/support).                      |
| Tax invoice under review (Fattura fiscale in revisione) | È in corso la revisione della fattura fiscale. Il pagamento verrà rilasciato una volta che la fattura fiscale è stata approvata.                                   | No                                                         |
| Rifiutato                 | Il pagamento è stato rifiutato dalla banca.                                                                                                      | Per informazioni dettagliate, contattare la banca.                             |
|||

## <a name="export-data-page"></a>Pagina di esportazione dati

Seguire queste istruzioni per esportare i dati.

La pagina di esportazione dati non viene aggiornata automaticamente. Per visualizzare i dati più recenti, potrebbe essere necessario aggiornare la pagina manualmente.

Il filtro può causare un errore di tipo **Non sono disponibili dati**. Ciò significa che probabilmente non è stato modificato il periodo di tempo predefinito di tre mesi, ma è stato selezionato un ID pagamento riferito a utili al di fuori di tale periodo. Espandere il periodo di tempo e riprovare.

## <a name="payments"></a>Pagamenti

![Esportazione dei pagamenti](./media/pc-export-payments.png)

Questa opzione consente di scaricare i pagamenti ricevuti in banca per un determinato programma, le imposte associate e la quantità aggregata di utili. Questo report viene utilizzato per molti programmi del Centro per i partner, pertanto alcune colonne potrebbero non essere applicabili al report. Queste colonne sono indicate di seguito.

| Nome colonna              | Descrizione                                                                                                                               |
|--------------------------|-----------------------------------------------------------------------------------------------------------------------------------------  |
| participantID            | Identità principale del partner che realizza un reddito nell'ambito del programma                                                                             |
| participantIDType        | ID programma per i programmi Incentive e ID venditore per programmi relativi allo Store                                                                |
| participantName          | Nome del partner beneficiario del reddito                                                                                                               |
| programName              | Nome del programma Incentive/Store                                                                                                              |
| earned                   | Importo guadagnato nella valuta di pagamento per l'ID programma/participante                                                                       |
| earnedUSD                | Importo guadagnato per l'ID programma/participante, in dollari statunitensi                                                                                      |
| withheldTax              | Importo delle imposte in valuta di pagamento per l'ID programma/participante                                                               |
| salesTax                 | Importo totale delle imposte sulle vendite in valuta di pagamento per l'ID programma/participante (applicabile solo per i programmi Incentive)                   |
| serviceFeeTax            | Quantità totale di serviceFeeTax in valuta di pagamento per l'ID programma/participante (applicabile solo per i programmi Store e Azure Marketplace) |
| totalPayment             | Pagamento totale in valuta locale, al netto di tutte ritenute e al lordo dell'imposta sulle vendite (se applicabile) per l'ID programma/participante   |
| currencyCode             | Codice della valuta di pagamento                                                                                                                      |
| paymentMethod            | Metodo utilizzato per pagare il partner, ad esempio, bonifico bancario o nota di credito                                                     |
| paymentID                | Identificatore univoco per il pagamento. Questo numero è in genere visibile nel rendiconto bancario (applicabile solo ai pagamenti SAP).              |
| paymentStatus            | Stato dei pagamenti                                                                                                                            |
| paymentStatusDescription | Descrizione descrittiva dello stato dei pagamenti                                                                                                    |
| paymentDate              | Data del pagamento inviato da Microsoft                                                                                                      |
|||

## <a name="transaction-history"></a>Cronologia delle transazioni

![Esportazione della cronologia delle transazioni](./media/pc-export-transaction.png)

Questa opzione consente di scaricare ogni voce della riga degli utili visualizzata nella pagina della cronologia transazioni con informazioni su tipo, data, importo transazione associato, cliente, prodotto e altri dettagli della transazione applicabili ai programmi.

| Nome colonna                    | Descrizione                                                                                                                              | Applicabilità per i programmi Incentive/Store/Azure Marketplace           |
|--------------------------------|------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------|
| earningId                      | Identificatore univoco per ogni reddito                                                                                                       | Tutti                                                            |
| participantId                  | Identità principale del partner che realizza un reddito nell'ambito del programma                                                                            | Tutti                                                            |
| participantIdType              | Principalmente ID programma per i programmi Incentive e ID venditore SE il programma è relativo allo Store o ad Azure Marketplace                                          | Tutti                                                            |
| participantName                | Nome del partner beneficiario del reddito                                                                                                              | Tutti                                                            |
| partnerCountryCode             | Località/paese/area del partner beneficiario del reddito                                                                                                  | Tutti                                                            |
| programName                    | Nome del programma Incentive/Store                                                                                                             | Tutti                                                            |
| transactionId                  | Identificatore univoco della transazione                                                                                                    | Tutti                                                            |
| transactionCurrency            | Valuta in cui è avvenuta la transazione originale del cliente (non si tratta della valuta locale del partner)                                     | Tutti                                                            |
| transactionDate                | Data della transazione. Utile per i programmi in cui un reddito è il risultato di più transazioni.                                           | Tutti                                                            |
| transactionExchangeRate        | Data del tasso di cambio usato per visualizzare l'importo in dollari statunitensi della transazione corrispondente                                                                 | Tutti                                                            |
| transactionAmount              | Importo della transazione nella valuta della transazione originale in base a cui vengono generati gli utili                                              | Tutti                                                            |
| transactionAmountUSD           | Importo della transazione in dollari statunitensi (USD)                                                                                                                | Tutti                                                            |
| lever                          | Indica la regola di business per il reddito                                                                                                  | Tutti                                                            |
| earningRate                    | Percentuale degli incentivi applicata all'importo della transazione per generare un reddito                                                                      | Tutti                                                            |
| quantity                       | Varia in base al programma. Indica la quantità fatturata per i programmi transazionali                                                            | Tutti                                                            |
| quantityType                   | Indica il tipo di quantità, ad esempio: importo fatturato o utenti attivi mensili (MAU)                                                                             | Tutti                                                            |
| earningType                    | Indica se si tratta di corrispettivi, sconti, co-op, vendite e così via                                                                                          | Tutti                                                            |
| earningAmount                  | Importo del reddito nella valuta della transazione originale                                                                                      | Tutti                                                            |
| earningAmountUSD               | Importo del reddito in dollari statunitensi                                                                                                                    | Tutti                                                            |
| earningDate                    | Data del reddito                                                                                                                      | Tutti                                                            |
| calculationDate                | Data di calcolo del reddito nel sistema                                                                                            | Tutti                                                            |
| earningExchangeRate            | Tasso di cambio usato per visualizzare l'importo corrispondente in dollari statunitensi                                                                                  | Tutti                                                            |
| exchangeRateDate               | Data del tasso di cambio usata per calcolare il valore in dollari statunitensi di EarningAmount                                                                                   | Tutti                                                            |
| paymentAmountWOTax             | Importo del reddito (al netto delle imposte) nella valuta di pagamento solo per i pagamenti inviati                                                                 | Tutti                                                            |
| paymentCurrency                | Valuta di pagamento scelta dal partner nel profilo di pagamento. Visualizzata solo per i pagamenti inviati                                                   | Tutti                                                            |
| paymentExchangeRate            | Tasso di cambio usato per calcolare paymentAmountWOTax nella valuta di pagamento con ExchangeRateDate                                            | Tutti                                                            |
| claimId                        | Identificatore univoco per l'attestazione                                                                                                              | Incentivi: solo alcuni programmi                                |
| planId                         | Identificatore univoco per il piano                                                                                                               | Incentivi: solo alcuni programmi                                |
| paymentId                      | Identificatore univoco per il pagamento. Questo numero è visibile nell'estratto conto bancario                                                 | Solo pagamenti SAP                                              |
| paymentStatus                  | Stato dei pagamenti                                                                                                                           | Tutti                                                            |
| paymentStatusDescription       | Descrizione dello stato dei pagamenti                                                                                                   | Tutti                                                            |
| customerId                     | È sempre vuoto                                                                                                                     | Solo per i programmi Incentive (eccezione: OEM) e Azure Marketplace |
| customerName                   | È sempre vuoto                                                                                                                     | Solo per i programmi Incentive (eccezione: OEM) e Azure Marketplace |
| partNumber                     | È sempre vuoto                                                                                                                     | Alcuni programmi Incentive, Store e Azure Marketplace        |
| productName                    | Nome del prodotto collegato alla transazione                                                                                                       | Tutti                                                            |
| productId                      | Identificatore univoco del prodotto                                                                                                                | Store e Azure Marketplace                                    |
| parentProductId                | Identificatore univoco del prodotto padre. Se non è presente un prodotto padre per la transazione, l'ID del prodotto padre coincide con l'ID del prodotto. | Store e Azure Marketplace                                    |
| parentProductName              | Nome del prodotto padre. Se non è presente un prodotto padre per la transazione, il nome del prodotto padre coincide con il nome del prodotto.   | Store e Azure Marketplace                                    |
| productType                    | Tipo di prodotto, ad esempio app, componente aggiuntivo o gioco                                                                                        | Store e Azure Marketplace                                    |
| invoiceNumber                  | Numero fattura (applicabile solo per EA)                                                                                                  | Incentive e Azure Marketplace: solo alcuni programmi           |
| subscriptionId                 | Identificatore della sottoscrizione associata al cliente                                                                                         | Incentive: solo alcuni programmi                                 |
| subscriptionStartDate          | Data di inizio della sottoscrizione                                                                                                                  | Incentive: solo alcuni programmi                                 |
| subscriptionEndDate            | Data di fine della sottoscrizione                                                                                                                    | Incentive: solo alcuni programmi                                 |
| resellerId                     | Identificatore del rivenditore                                                                                                                      | Incentive: solo alcuni programmi                                 |
| resellerName                   | Nome del rivenditore                                                                                                                            | Incentive: solo alcuni programmi                                 |
| distributorId                  | Identificatore del distributore                                                                                                                   | Incentive: solo alcuni programmi                                 |
| distributorName                | Nome del distributore                                                                                                                         | Incentive: solo alcuni programmi                                 |
| agreementNumber                | Numero del contratto                                                                                                                         | Incentive: solo alcuni programmi                                 |
| agreementStartDate             | Data di inizio del contratto                                                                                                                     | Incentive: solo alcuni programmi                                 |
| agreementEndDate               | Data di fine del contratto                                                                                                                       | Incentive: solo alcuni programmi                                 |
| workload                       | Carico di lavoro                                                                                                                                 | Incentive: solo alcuni programmi                                 |
| transactionType                | Tipo di transazione, ad esempio acquisto, rimborso, storno o rifiuto di addebito                                                               | Store e Azure Marketplace                                    |
| localProviderSeller            | Fornitore/gestore del sistema di pagamento locale                                                                                                          | Solo Store                                                     |
| taxRemitted                    | Importo delle imposte versate (vendite, utilizzo o IVA/GST)                                                                                   | Store e Azure Marketplace                                    |
| taxRemitModel                  | Parte responsabile del versamento delle imposte (vendite, utilizzo o IVA/GST)                                                                    | Solo Store                                                     |
| storeFee                       | Importo trattenuto da Microsoft come corrispettivo per rendere disponibile l'app o il componente aggiuntivo nello Store                                           | Solo Store                                                     |
| transactionPaymentMethod       | Strumento di pagamento del cliente usato per la transazione, ad esempio carta di credito, fatturazione operatore di telefonia mobile o PayPal                                | Store e Azure Marketplace                                    |
| tpan                           | Indica la rete pubblicitaria di terze parti                                                                                                     | Store: solo ADS                                               |
| customerCountry                | Paese/Area geografica del cliente                                                                                                                         | Store e Azure Marketplace                                    |
| customerCity                   | Città del cliente                                                                                                                            | Store e Azure Marketplace                                    |
| customerState                  | Stato/Provincia del cliente                                                                                                                           | Store e Azure Marketplace                                    |
| customerZip                    | CAP/Codice postale del cliente                                                                                                                 | Store e Azure Marketplace                                    |
| purchaseTypeCode               | È sempre vuoto                                                                                                                     | Programma Incentive: elemento del report personalizzato                                        |
| purchaseOrderType              | È sempre vuoto                                                                                                                     | Programma Incentive: elemento del report personalizzato                                        |
| purchaseOrderCoverageStartDate | È sempre vuoto                                                                                                                     | Programma Incentive: elemento del report personalizzato                                        |
| purchaseOrderCoverageEndDate   | È sempre vuoto                                                                                                                     | Programma Incentive: elemento del report personalizzato                                        |
| programOfferingLevel           |                                                                                                                                          | Programma Incentive: elemento del report personalizzato                                        |
| ID tenant                       |                                                                                                                                          | Programmi Incentive                                             |
| externalReferenceId            | Identificatore univoco per il programma                                                                                                        | Programmi di pagamento diretto (Incentive e Store)                      |
| externalReferenceIdLabel       | Etichetta identificatore univoco                                                                                                                  | Programmi di pagamento diretto (Incentive e Store)                      |
|||

## <a name="historical-statements"></a>Rendiconti cronologici

![Esportazione dei rendiconti cronologici](./media/pc-export-statements.png)

La cronologia delle transazioni antecedenti al 1 luglio 2019 luglio viene gestita separatamente. Le istruzioni utilizzano i campi seguenti anziché quelli correnti.

> [!NOTE]
> Nella cronologia delle transazioni legacy è presente una colonna denominata "Riservato" che corrisponde alla colonna "Utili" della cronologia attuale, ad eccezione del fatto che esclude tutti gli utili con stato "Pagamento inviato".

> [!NOTE]
> Filtri come 3M, 6M o 12M, non si applicano alla sezione dei **rendiconti cronologici**.

| Nome campo              | Descrizione                                                                                                                                                             |
|-------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Origine dei ricavi          | Origine dei ricavi in base a dove si è verificata la transazione, ad esempio Microsoft Store, Windows Phone Store, Windows 8 Store o Microsoft Advertising                  |
| ID dell'ordine                | Identificatore univoco dell'ordine. Questo ID consente di identificare le transazioni di acquisto con le rispettive transazioni non di acquisto, ad esempio rimborsi o chargeback. Entrambe avranno lo stesso ID ordine. Inoltre, nel caso di un addebito suddiviso in cui sono stati usati più metodi di pagamento per un singolo acquisto, è possibile collegare le transazioni di acquisto. |
| TransactionID          | Identificatore univoco della transazione.                                                                                                                                          |
| Data/ora della transazione   | Data e ora in cui si è verificata la transazione (UTC).                                                                                                                       |
| ID prodotto padre       | Identificatore univoco del prodotto padre. Se non è presente un prodotto padre per la transazione, l'ID del prodotto padre coincide con l'ID del prodotto.                                |
| Product ID              | Identificatore univoco del prodotto.                                                                                                                                              |
| Nome prodotto padre     | Nome del prodotto padre. Se non è presente un prodotto padre per la transazione, il nome del prodotto padre coincide con il nome del prodotto.                                  |
| Nome prodotto            | Nome del prodotto                                                                                                                                                    |
| Tipo prodotto            | Tipo di prodotto, ad esempio app, componente aggiuntivo o gioco                                                                                                                       |
| Quantità                | Quando l'origine dei ricavi è Microsoft Store per le aziende, la quantità corrisponde al numero di licenze acquistate. Per tutte le altre origini, la quantità è sempre pari a 1. Anche se una singola transazione viene suddivisa in due voci, perché sono stati utilizzati due metodi di pagamento diversi, ogni voce indica una quantità pari a 1. |
| Tipo di transazione        | Tipo di transazione, ad esempio acquisto, rimborso, storno o rifiuto di addebito                                                                                              |
| Metodo di pagamento          | Strumento di pagamento del cliente usato per la transazione, ad esempio carta di credito, fatturazione operatore di telefonia mobile o PayPal                                                               |
| Paese/Area        | Paese/Area geografica in cui risiedono i dati                                                                                                                          |
| Provider/venditore locale | Provider/venditore locale del record                                                                                                                                        |
| Valuta transazione    | Valuta della transazione                                                                                                                                            |
| Importo transazione      | Importo della transazione                                                                                                                                              |
| Imposte versate            | Importo delle imposte versate (vendite, utilizzo o IVA/GST)                                                                                                                  |
| Entrate nette            | Importo della transazione al netto delle imposte versate                                                                                                                                   |
| Corrispettivi per lo Store               | Percentuale delle entrate nette trattenuta da Microsoft come corrispettivo per rendere disponibile l'app o il componente aggiuntivo nello Store                                                      |
| Guadagno sull'app            | Entrate nette meno corrispettivi per lo Store                                                                                                                                       |
| Imposte trattenute          | Importo dell'imposta sul reddito trattenuta (non incluso nel file CSV **riservato**)                                                                                                |
| Payment                 | Guadagno sull'app al netto di qualsiasi ritenuta d'acconto applicabile (importo indicato nella valuta della transazione). Non incluso nel file CSV **riservato**.                               |
| Tasso di cambio                 | Tasso di cambio utilizzato per convertire la valuta della transazione in valuta di pagamento                                                                                         |
| Valuta del pagamento        | Valuta in cui viene effettuato il pagamento                                                                                                                                       |
| Pagamento convertito       | Importo del pagamento convertito in valuta di pagamento in base al tasso di cambio                                                                                                         |
| Modello versamento imposte         | Parte responsabile del versamento delle imposte (vendite, utilizzo o IVA/GST)                                                                                                   |
| Data/ora di idoneità   | La data e l'ora in cui i proventi della transazione diventano idonei per il pagamento (UTC). Quando viene creato un pagamento, vengono inclusi i proventi della transazione con data e ora di idoneità prima della data di creazione del pagamento (inclusa solo nel file CSV **riservato**). |
| Charges                 | Mostra una suddivisione di tutti i dettagli dell'addebito aggregati nella colonna Importo transazione (incluso solo per Azure Marketplace; non incluso nel file CSV **riservato**). |
|||

## <a name="next-step"></a>passaggio successivo

- [Dettagli dei criteri dei proventi](./payout-policy-details.md)
