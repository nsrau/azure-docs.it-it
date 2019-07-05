---
title: Ottimizzare il costo delle risorse di Azure Cosmos DB con la capacità riservata
description: Informazioni su come acquistare capacità riservata di Azure Cosmos DB per risparmiare sui costi per i servizi di calcolo.
author: bandersmsft
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: b74fcc2e08f02be7adeeab4cfee5f36d5194392c
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508613"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Ottimizzare il costo con la capacità riservata in Azure Cosmos DB

La capacità riservata di Azure Cosmos DB permette di risparmiare grazie al pagamento anticipato delle risorse di Azure Cosmos DB per un anno o tre anni. Con la capacità riservata di Azure Cosmos DB, è possibile ottenere uno sconto sulla velocità effettiva di cui viene effettuato il provisioning per le risorse di Cosmos DB, ad esempio database e contenitori (tabelle, raccolte e grafi).

La capacità riservata di Azure Cosmos DB consente di ridurre in modo significativo i costi di Cosmos DB, fino al 65% rispetto ai prezzi normali, con un impegno anticipato di un anno o di tre anni. La capacità riservata permette di ottenere uno sconto a livello di fatturazione e non ha alcuna ripercussione sullo stato di runtime delle risorse di Azure Cosmos DB.

La capacità riservata di Azure Cosmos DB copre la velocità effettiva con provisioning relativa alle risorse. Non copre i costi di archiviazione e rete. Non appena si acquista una prenotazione, i costi per la velocità effettiva che corrispondono agli attributi della prenotazione non vengono più addebitati alle tariffe con pagamento in base al consumo. Per altre informazioni sulle prenotazioni, vedere l'articolo [Informazioni sulle prenotazioni di Azure](../billing/billing-save-compute-costs-reservations.md).

È possibile acquistare capacità riservata di Azure Cosmos DB dal [portale di Azure](https://portal.azure.com). Per acquistare capacità riservata:

* È necessario essere il ruolo di proprietario per almeno un Enterprise o singola sottoscrizione con tariffe a consumo.  
* Per le sottoscrizioni Enterprise, **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com). In alternativa, se tale impostazione è disabilitata, è necessario essere un amministratore della sottoscrizione con contratto Enterprise.
* Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare capacità riservata di Azure Cosmos DB.

## <a name="determine-the-required-throughput-before-purchase"></a>Determinare la velocità effettiva necessaria prima dell'acquisto

Le dimensioni della prenotazione devono essere basate sulla quantità totale di velocità effettiva che verrà usata dalle risorse di Azure Cosmos DB esistenti o che verranno distribuite a breve. È possibile determinare la velocità effettiva necessaria nei modi seguenti:

* Ottenere i dati cronologici per la velocità effettiva totale sottoposta a provisioning negli account, nei database e nelle raccolte di Azure Cosmos DB in tutte le aree. È ad esempio possibile valutare la velocità effettiva giornaliera media sottoposta a provisioning scaricando il rendiconto sull'utilizzo giornaliero da `https://account.azure.com`.

* I clienti con contratto Enterprise possono scaricare il file di utilizzo per ottenere i dettagli sulla velocità effettiva di Azure Cosmos DB. Fare riferimento al valore di **Tipo di servizio** nella sezione **Informazioni aggiuntive** del file di utilizzo.

* È possibile sommare la velocità effettiva media per tutti i carichi di lavoro negli account di Azure Cosmos DB che si prevede di eseguire nell'anno o nei tre anni successivi. È quindi possibile usare tale quantità per la prenotazione.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Acquistare capacità riservata di Azure Cosmos DB

1. Accedere al [portale di Azure](https://portal.azure.com).  

2. Selezionare **Tutti i servizi** > **Prenotazioni** > **Aggiungi**.  

3. Dal **acquistare prenotazioni** riquadro, scegliere **Azure Cosmos DB** acquistare una nuova prenotazione.  

4. Compilare i campi obbligatori come descritto nella tabella seguente:

   ![Compilare il modulo per la capacità riservata](./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png)

   |Campo  |Descrizione  |
   |---------|---------|
   |`Scope`   |   Opzione che controlla quante sottoscrizioni possono usare il vantaggio di fatturazione associato alla prenotazione. Controlla anche il modo in cui la prenotazione viene applicata alle sottoscrizioni specifiche. <br/><br/>  Se si seleziona **Condiviso**, lo sconto per la prenotazione viene applicato alle istanze di Azure Cosmos DB in esecuzione in qualsiasi sottoscrizione nel contesto di fatturazione. Il contesto di fatturazione si basa sul modo in cui è stata effettuata l'iscrizione ad Azure. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni all'interno della registrazione. Per i clienti con pagamento a consumo, l'ambito condiviso è tutte le sottoscrizioni singole con tariffe a consumo create dall'amministratore dell'account.  <br/><br/>  Se si seleziona **Sottoscrizione singola**, lo sconto per la prenotazione viene applicato alle istanze di Azure Cosmos DB nella sottoscrizione selezionata. <br/><br/> Se si seleziona **singolo gruppo di risorse**, lo sconto della prenotazione viene applicato alle istanze di Azure Cosmos DB nella sottoscrizione selezionata e il gruppo di risorse selezionato all'interno della sottoscrizione. <br/><br/> È possibile modificare l'ambito della prenotazione dopo l'acquisto della capacità riservata.  |
   |Sottoscrizione  |   Sottoscrizione usata per pagare la capacità riservata di Azure Cosmos DB. Per l'addebito dei costi iniziali, viene usata la modalità di pagamento della sottoscrizione selezionata. Il tipo di sottoscrizione deve essere uno dei seguenti: <br/><br/>  Contratto Enterprise (numero offerta: MS-AZR-0017P o MS-AZR-0148P): per una sottoscrizione Enterprise, il costo viene detratto dal saldo dell'impegno monetario della registrazione oppure viene addebitato come eccedenza. <br/><br/> Singola sottoscrizione con tariffe a consumo (numeri dell'offerta: MS-AZR-0003P o MS-AZR-0023P): Per una singola sottoscrizione con tariffe a consumo, il costo viene addebitato al metodo di pagamento della fattura o carta di credito alla sottoscrizione.    |
   | Gruppo di risorse | Gruppo di risorse a cui viene applicato lo sconto relativo alla capacità riservata. |
   |Nome  |   Un anno o tre anni.   |
   |Tipo di velocità effettiva   |  Viene eseguito il provisioning della velocità effettiva come unità di richiesta. È possibile acquistare una prenotazione per la velocità effettiva con provisioning per entrambe le configurazioni - in una singola area scrive anche come più scritture di aree. Il tipo di velocità effettiva ha due valori da selezionare: 100 UR/sec per ogni ora e di 100 UR/sec multimaster ogni ora.|
   | Unità riservate di capacità| Quantità di velocità effettiva da riservare. È possibile calcolare questo valore determinando la velocità effettiva necessaria per tutte le risorse di Cosmos DB (ad esempio, database o contenitori) per ogni area. Lo si moltiplica quindi per il numero di aree da associare al database di Cosmos DB. Ad esempio: se si hanno cinque aree con 1 milione di unità richieste/sec in ogni area, selezionare 5 milioni di unità richieste/sec per l'acquisto di capacità di prenotazione. |
 

5. Dopo che riempire il form, viene calcolato il prezzo è necessario acquistare capacità riservata. Inoltre, l'output mostra la percentuale di sconto che si ottiene con opzioni scelte. Successivamente fare clic su **selezionare**

6. Nel **acquistare prenotazioni** riquadro, esaminare lo sconto relativo e il prezzo della prenotazione. Il prezzo della prenotazione si applica alle risorse di Azure Cosmos DB con velocità effettiva di cui è stato effettuato il provisioning in tutte le aree.  

   ![Riepilogo di capacità riservata](./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png)

7. Selezionare **revisione + buy** e quindi **Acquista subito**. Se l'acquisto ha esito positivo, viene visualizzata la pagina seguente:

Dopo l'acquisto, una prenotazione viene applicata immediatamente a tutte le risorse esistenti di Azure Cosmos DB che corrispondono ai termini della prenotazione. Se non ci sono risorse di Azure Cosmos DB esistenti, la prenotazione verrà applicata quando si distribuisce una nuova istanza di Cosmos DB che corrisponde ai termini della prenotazione. In entrambi i casi, il periodo della prenotazione inizia immediatamente dopo l'acquisto.

Quando la prenotazione scade, le istanze di Azure Cosmos DB continuano a essere eseguite e vengono fatturate alle normali tariffe con pagamento in base al consumo.

## <a name="cancellation-and-exchanges"></a>L'annullamento e scambi

Per informazioni su come identificare la capacità riservata a destra, vedere [informazioni su come viene applicato lo sconto di prenotazione ad Azure Cosmos DB](../billing/billing-understand-cosmosdb-reservation-charges.md). Se è necessario annullare o scambiare una prenotazione di Azure Cosmos DB, vedere [scambi di prenotazione e rimborsi](../billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="next-steps"></a>Passaggi successivi

Lo sconto per la prenotazione si applica automaticamente alle risorse di Azure Cosmos DB che corrispondono agli attributi e all'ambito della prenotazione. È possibile aggiornare l'ambito della prenotazione tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure o l'API.

*  Per informazioni su come vengono applicati gli sconti per la capacità riservata in Azure Cosmos DB, vedere [Informazioni su come viene applicato lo sconto per la prenotazione ad Azure Cosmos DB](../billing/billing-understand-cosmosdb-reservation-charges.md).

* Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

   * [Cosa sono le prenotazioni di Azure?](../billing/billing-save-compute-costs-reservations.md)  
   * [Gestire le prenotazioni di Azure](../billing/billing-manage-reserved-vm-instance.md)  
   * [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md)  
   * [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](../billing/billing-understand-reserved-instance-usage.md)
   * [Prenotazioni di Azure nel programma CSP del Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
