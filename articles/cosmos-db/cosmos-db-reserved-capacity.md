---
title: Capacità riservata in Azure Cosmos DB per ottimizzare i costi
description: Informazioni su come acquistare capacità riservata di Azure Cosmos DB per risparmiare sui costi per i servizi di calcolo.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 16e8f770445218e10ab7e7645a81325d11be55da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505961"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Ottimizzare il costo con la capacità riservata in Azure Cosmos DB

La capacità riservata di Azure Cosmos DB consente di risparmiare grazie al pagamento anticipato delle risorse di Azure Cosmos DB per uno o tre anni. Con la capacità riservata di Azure Cosmos DB, è possibile ottenere uno sconto sulla velocità effettiva di cui viene effettuato il provisioning per le risorse di Cosmos DB, ad esempio database e contenitori (tabelle, raccolte e grafi).

La capacità riservata di Azure Cosmos DB consente di ridurre in modo significativo i costi di Cosmos DB, fino al 65% rispetto ai prezzi normali, con un impegno anticipato di un anno o di tre anni. La capacità riservata permette di ottenere uno sconto a livello di fatturazione e non ha alcuna ripercussione sullo stato di runtime delle risorse di Azure Cosmos DB.

La capacità riservata di Azure Cosmos DB copre la velocità effettiva con provisioning relativa alle risorse. Non copre i costi di archiviazione e rete. Non appena si acquista una prenotazione, i costi per la velocità effettiva che corrispondono agli attributi della prenotazione non vengono più addebitati alle tariffe con pagamento in base al consumo. Per altre informazioni sulle prenotazioni, vedere l'articolo [Informazioni sulle prenotazioni di Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md).

È possibile acquistare capacità riservata di Azure Cosmos DB dal [portale di Azure](https://portal.azure.com). Usare [pagamenti anticipati o mensili](../cost-management-billing/reservations/monthly-payments-reservations.md) per acquistare la prenotazione. Per acquistare capacità riservata:

* È necessario essere nel ruolo Proprietario per almeno un Enterprise o una singola sottoscrizione con tariffe con pagamento in base al costo.  
* Per le sottoscrizioni Enterprise, **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com). In alternativa, se tale impostazione è disabilitata, è necessario essere un amministratore della sottoscrizione con contratto Enterprise.
* Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare capacità riservata di Azure Cosmos DB.

## <a name="determine-the-required-throughput-before-purchase"></a>Determinare la velocità effettiva necessaria prima dell'acquisto

Le dimensioni dell'acquisto di capacità riservata devono essere basate sulla quantità totale di velocità effettiva che le risorse di Azure Cosmos DB esistenti o presto distribuite verranno usate su base oraria. Ad esempio: acquista 30.000 RU/s di capacità riservata se questo è il tuo modello di utilizzo orario coerente. In questo esempio, qualsiasi velocità effettiva di cui è stato eseguito il provisioning superiore a 30.000 RU/s verrà fatturata utilizzando la tariffa con pagamento in base al costo. Se la velocità effettiva di cui è stato eseguito il provisioning è inferiore a 30.000 RU/s in un'ora, la capacità riservata aggiuntiva per tale ora verrà sprecata.

Calcoliamo i consigli di acquisto in base al modello di utilizzo orario. È consigliabile analizzare l'utilizzo negli ultimi 7, 30 e 60 giorni e si consiglia l'acquisto di capacità riservata che massimizza i risparmi. È possibile visualizzare le dimensioni di prenotazione consigliate nel portale di Azure usando la procedura seguente:You can view recommended reservation sizes in the Azure portal using the following steps:

1. Accedere al [portale](https://portal.azure.com)di Azure .  

2. Selezionare **Tutti i servizi** > **Prenotazioni** > **Aggiungi**.

3. Nel riquadro **Prenotazioni acquisti** scegliere **Database cosmo**di Azure .

4. Selezionare la scheda **Consigliato** per visualizzare le prenotazioni consigliate:

È possibile filtrare i suggerimenti in base agli attributi seguenti:You can filter recommendations by the following attributes:

- **Durata** (1 anno o 3 anni)
- **Frequenza di fatturazione** (mensile o anticipata)
- **Tipo di velocità effettiva** (RU e RU multimaster)

Inoltre, è possibile definire l'ambito dei consigli per trovarsi all'interno di un singolo gruppo di risorse, di una singola sottoscrizione o dell'intera registrazione di Azure.Additionally, you can scope recommendations to be within a single resource group, single subscription, or your entire Azure enrollment. 

Ecco un esempio di raccomandazione:Here's an example recommendation:

![Consigli per la capacità riservata](./media/cosmos-db-reserved-capacity/reserved-capacity-recommendation.png)

Questa raccomandazione per l'acquisto di una prenotazione di 30.000 RU/s indica che, tra le prenotazioni di 3 anni, una dimensione di prenotazione di 30.000 RU/s massimizzerà il risparmio. In questo caso, la raccomandazione viene calcolata in base ai 30 giorni di utilizzo di Azure Cosmos DB. Se il cliente prevede che gli ultimi 30 giorni di utilizzo di Azure Cosmos DB siano rappresentativi dell'utilizzo futuro, massimizzerebbe i risparmi acquistando una prenotazione di 30.000 RU/s.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Acquistare capacità riservata di Azure Cosmos DB

1. Accedere al [portale](https://portal.azure.com)di Azure .  

2. Selezionare **Tutti i servizi** > **Prenotazioni** > **Aggiungi**.  

3. Nel riquadro **Prenotazioni acquisti** scegliere **Database cosmo di Azure** per acquistare una nuova prenotazione.  

4. Compilare i campi obbligatori come descritto nella tabella seguente:

   ![Compilare il modulo per la capacità riservata](./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png)

   |Campo  |Descrizione  |
   |---------|---------|
   |Scope   |   Opzione che controlla quante sottoscrizioni possono usare il vantaggio di fatturazione associato alla prenotazione. Controlla anche il modo in cui la prenotazione viene applicata alle sottoscrizioni specifiche. <br/><br/>  Se si seleziona **Condiviso**, lo sconto per la prenotazione viene applicato alle istanze di Azure Cosmos DB in esecuzione in qualsiasi sottoscrizione nel contesto di fatturazione. Il contesto di fatturazione si basa sul modo in cui è stata effettuata l'iscrizione ad Azure. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni all'interno della registrazione. Per i clienti con pagamento in base al cliente, l'ambito condiviso è rappresentato da tutte le singole sottoscrizioni con tariffe con pagamento in base al cliente creata dall'amministratore dell'account.  <br/><br/>  Se si seleziona **Sottoscrizione singola**, lo sconto per la prenotazione viene applicato alle istanze di Azure Cosmos DB nella sottoscrizione selezionata. <br/><br/> Se si seleziona Gruppo di risorse **singolo,** lo sconto sulla prenotazione viene applicato alle istanze di Azure Cosmos DB nella sottoscrizione selezionata e al gruppo di risorse selezionato all'interno della sottoscrizione. <br/><br/> È possibile modificare l'ambito della prenotazione dopo l'acquisto della capacità riservata.  |
   |Subscription  |   Sottoscrizione usata per pagare la capacità riservata di Azure Cosmos DB. Il metodo di pagamento sull'abbonamento selezionato viene utilizzato per addebitare i costi. La sottoscrizione deve essere di uno dei seguenti tipi: <br/><br/>  Contratto Enterprise Agreement (numeri di offerta: MS-A-R-0017P o MS-A-R-0148P): per una sottoscrizione Enterprise, gli addebiti vengono detratti dal saldo dell'impegno monetario dell'iscrizione o addebitati come eccessivamente. <br/><br/> Abbonamento individuale con tariffe con pagamento in base al costo (numeri di offerta: MS-A-R-0003P o MS-A-R-0023P): per un abbonamento individuale con tariffe con pagamento in base al costo, gli addebiti vengono addebitati sulla carta di credito o sul metodo di pagamento della fattura sull'abbonamento.    |
   | Gruppo di risorse | Gruppo di risorse a cui viene applicato lo sconto capacità prenotata. |
   |Termine  |   Un anno o tre anni.   |
   |Tipo di velocità effettiva   |  Il provisioning della velocità effettiva viene eseguito come unità richiesta. È possibile acquistare una prenotazione per la velocità effettiva di cui è stato eseguito il provisioning per entrambe le configurazioni: scritture in aree singole e scritture di più aree. Il tipo di velocità effettiva ha due valori tra cui scegliere: 100 RU/s all'ora e 100 RU/s multimaster all'ora.|
   | Unità di capacità riservate| Quantità di velocità effettiva da riservare. È possibile calcolare questo valore determinando la velocità effettiva necessaria per tutte le risorse di Cosmos DB (ad esempio, database o contenitori) per ogni area. Quindi lo moltiplichi per il numero di regioni che associerai al tuo database Cosmos. Se ad esempio si hanno cinque aree con 1 milione di unità richieste/sec in ogni area, selezionare 5 milioni di unità richieste/sec per l'acquisto di capacità di prenotazione. |


5. Dopo aver compilato il modulo, viene calcolato il prezzo necessario per l'acquisto della capacità prenotata. L'output mostra anche la percentuale di sconto ottenuta con le opzioni scelte. Avanti fare clic su **Seleziona**

6. Nel riquadro **Prenotazioni acquisti** esaminare lo sconto e il prezzo della prenotazione. Il prezzo della prenotazione si applica alle risorse di Azure Cosmos DB con velocità effettiva di cui è stato effettuato il provisioning in tutte le aree.  

   ![Riepilogo capacità riservata](./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png)

7. Selezionare **Revisione : acquista** e quindi acquista **ora**. Se l'acquisto ha esito positivo, viene visualizzata la pagina seguente:

Dopo l'acquisto, una prenotazione viene applicata immediatamente a tutte le risorse esistenti di Azure Cosmos DB che corrispondono ai termini della prenotazione. Se non ci sono risorse di Azure Cosmos DB esistenti, la prenotazione verrà applicata quando si distribuisce una nuova istanza di Cosmos DB che corrisponde ai termini della prenotazione. In entrambi i casi, il periodo della prenotazione inizia immediatamente dopo l'acquisto.

Quando la prenotazione scade, le istanze di Azure Cosmos DB continuano a essere eseguite e vengono fatturate alle normali tariffe con pagamento in base al consumo.

## <a name="cancel-exchange-or-refund-reservations"></a>Annullare o scambiare le prenotazioni oppure chiedere il rimborso

È possibile annullare o scambiare le prenotazioni oppure chiederne il rimborso con determinate limitazioni. Per altre informazioni, vedere [Scambi e rimborsi self-service per le prenotazioni di Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="next-steps"></a>Passaggi successivi

Lo sconto per la prenotazione si applica automaticamente alle risorse di Azure Cosmos DB che corrispondono agli attributi e all'ambito della prenotazione. È possibile aggiornare l'ambito della prenotazione tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure o l'API.

*  Per informazioni su come vengono applicati gli sconti per la capacità riservata in Azure Cosmos DB, vedere [Informazioni su come viene applicato lo sconto per la prenotazione ad Azure Cosmos DB](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md).

* Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

   * [Che cosa sono le prenotazioni di Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)  
   * [Gestire le prenotazioni di Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)  
   * [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)  
   * [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
   * [Prenotazioni di Azure nel programma CSP del Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Se hai domande o hai bisogno di aiuto, [crea una richiesta](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)di supporto .
