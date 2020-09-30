---
title: Capacità riservata in Azure Cosmos DB per ottimizzare i costi
description: Informazioni su come acquistare capacità riservata di Azure Cosmos DB per risparmiare sui costi per i servizi di calcolo.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 90a4e86360916ba4b3dace0861fd1c6f7cd9b459
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91567112"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Ottimizzare il costo con la capacità riservata in Azure Cosmos DB

La capacità riservata di Azure Cosmos DB consente di risparmiare grazie al pagamento anticipato delle risorse di Azure Cosmos DB per uno o tre anni. Con la capacità riservata di Azure Cosmos DB, è possibile ottenere uno sconto sulla velocità effettiva di cui viene effettuato il provisioning per le risorse di Cosmos DB, ad esempio database e contenitori (tabelle, raccolte e grafi).

La capacità riservata di Azure Cosmos DB consente di ridurre in modo significativo i costi di Cosmos DB, fino al 65% rispetto ai prezzi normali, con un impegno anticipato di un anno o di tre anni. La capacità riservata permette di ottenere uno sconto a livello di fatturazione e non ha alcuna ripercussione sullo stato di runtime delle risorse di Azure Cosmos DB.

La capacità riservata di Azure Cosmos DB copre la velocità effettiva con provisioning relativa alle risorse. Non copre i costi di archiviazione e rete. Non appena si acquista una prenotazione, i costi per la velocità effettiva che corrispondono agli attributi della prenotazione non vengono più addebitati alle tariffe con pagamento in base al consumo. Per altre informazioni sulle prenotazioni, vedere l'articolo [Informazioni sulle prenotazioni di Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md).

È possibile acquistare capacità riservata di Azure Cosmos DB dal [portale di Azure](https://portal.azure.com). Usare [pagamenti anticipati o mensili](../cost-management-billing/reservations/monthly-payments-reservations.md) per acquistare la prenotazione. Per acquistare capacità riservata:

* È necessario avere il ruolo di proprietario per almeno una sottoscrizione Enterprise o singola con tariffe con pagamento in base al consumo.  
* Per le sottoscrizioni Enterprise, **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com). In alternativa, se tale impostazione è disabilitata, è necessario essere un amministratore della sottoscrizione con contratto Enterprise.
* Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare capacità riservata di Azure Cosmos DB.

## <a name="determine-the-required-throughput-before-purchase"></a>Determinare la velocità effettiva necessaria prima dell'acquisto

La dimensione dell'acquisto di capacità riservata deve essere basata sulla quantità totale di velocità effettiva che verrà usata dalle risorse Azure Cosmos DB esistenti o presto distribuite per la distribuzione su base oraria. Ad esempio: acquistare 30.000 UR/s capacità riservata se questo è il modello di utilizzo orario coerente. In questo esempio, la velocità effettiva con provisioning superiore a 30.000 UR/sec verrà fatturata in base alla tariffa con pagamento in base al consumo. Se la velocità effettiva con provisioning è inferiore a 30.000 UR/sec in un'ora, la capacità riservata aggiuntiva per quell'ora verrà sprecata.

Vengono calcolate le raccomandazioni per l'acquisto in base al modello di utilizzo orario. L'utilizzo negli ultimi 7, 30 e 60 giorni viene analizzato e l'acquisto di capacità riservata che massimizza i risparmi è consigliato. È possibile visualizzare le dimensioni di prenotazione consigliate nel portale di Azure attenendosi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com).  

2. Selezionare **tutti i servizi**  >  **prenotazioni**  >  **Aggiungi**.

3. Dal riquadro **prenotazioni acquisti** scegliere **Azure Cosmos DB**.

4. Selezionare la scheda **consigliata** per visualizzare le prenotazioni consigliate:

È possibile filtrare le raccomandazioni in base ai seguenti attributi:

- **Termini** (1 anno o 3 anni)
- **Frequenza di fatturazione** (mensile o iniziale)
- **Tipo di velocità effettiva** (UR/s e ur/sec scrittura in più aree)

Inoltre, è possibile definire l'ambito delle raccomandazioni all'interno di un singolo gruppo di risorse, una singola sottoscrizione o l'intera registrazione di Azure. 

Ecco un esempio di Raccomandazione:

:::image type="content" source="./media/cosmos-db-reserved-capacity/reserved-capacity-recommendation.png" alt-text="Raccomandazioni sulla capacità riservata":::

Questa raccomandazione per l'acquisto di una prenotazione di 30.000 UR/sec indica che, tra le prenotazioni di 3 anni, una dimensione di prenotazione di 30.000 UR/s consente di ottimizzare il risparmio. In questo caso, la raccomandazione viene calcolata in base agli ultimi 30 giorni di utilizzo del Azure Cosmos DB. Se il cliente prevede che gli ultimi 30 giorni di utilizzo di Azure Cosmos DB siano rappresentativi di un uso futuro, si otterrebbe un miglioramento del risparmio acquistando una prenotazione di 30.000 UR/s.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Acquistare capacità riservata di Azure Cosmos DB

1. Accedere al [portale di Azure](https://portal.azure.com).  

2. Selezionare **tutti i servizi**  >  **prenotazioni**  >  **Aggiungi**.  

3. Dal riquadro **prenotazioni acquisti** scegliere **Azure Cosmos DB** per acquistare una nuova prenotazione.  

4. Compilare i campi obbligatori come descritto nella tabella seguente:

   :::image type="content" source="./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png" alt-text="Raccomandazioni sulla capacità riservata":::

   |Campo  |Descrizione  |
   |---------|---------|
   |Ambito   |   Opzione che controlla quante sottoscrizioni possono usare il vantaggio di fatturazione associato alla prenotazione. Controlla anche il modo in cui la prenotazione viene applicata alle sottoscrizioni specifiche. <br/><br/>  Se si seleziona **Condiviso**, lo sconto per la prenotazione viene applicato alle istanze di Azure Cosmos DB in esecuzione in qualsiasi sottoscrizione nel contesto di fatturazione. Il contesto di fatturazione si basa sul modo in cui è stata effettuata l'iscrizione ad Azure. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni all'interno della registrazione. Per i clienti con pagamento in base al consumo, l'ambito condiviso è costituito da tutte le singole sottoscrizioni con tariffe con pagamento in base al consumo create dall'amministratore account.  <br/><br/>  Se si seleziona **Sottoscrizione singola**, lo sconto per la prenotazione viene applicato alle istanze di Azure Cosmos DB nella sottoscrizione selezionata. <br/><br/> Se si seleziona **gruppo di risorse singolo**, lo sconto relativo alla prenotazione viene applicato alle istanze Azure Cosmos DB nella sottoscrizione selezionata e al gruppo di risorse selezionato all'interno di tale sottoscrizione. <br/><br/> È possibile modificare l'ambito della prenotazione dopo l'acquisto della capacità riservata.  |
   |Sottoscrizione  |   Sottoscrizione usata per pagare la capacità riservata di Azure Cosmos DB. Il metodo di pagamento per la sottoscrizione selezionata viene utilizzato per l'addebito dei costi. La sottoscrizione deve essere di uno dei seguenti tipi: <br/><br/>  Enterprise Agreement (numeri di offerta: MS-AZR-0017P o MS-AZR-0148P): per una sottoscrizione Enterprise, gli addebiti vengono dedotti dal saldo dell'impegno monetario di registrazione o addebitato come eccedenza. <br/><br/> Sottoscrizione singola con tariffe con pagamento in base al consumo (numeri di offerta: MS-AZR-0003P o MS-AZR-0023P): per una sottoscrizione singola con tariffe con pagamento in base al consumo, i costi vengono addebitati sul metodo di pagamento con carta di credito o fatturazione per la sottoscrizione.    |
   | Gruppo di risorse | Gruppo di risorse a cui viene applicato lo sconto per la capacità riservata. |
   |Termine  |   Un anno o tre anni.   |
   |Tipo di velocità effettiva   |  Il provisioning della velocità effettiva viene effettuato come unità richiesta. È possibile acquistare una prenotazione per la velocità effettiva di cui è stato effettuato il provisioning per entrambe le operazioni di scrittura: singole aree e scritture in più aree. Il tipo di velocità effettiva è costituito da due valori da scegliere: 100 ur/sec all'ora e 100 in più aree Scritture ur/sec all'ora.|
   | Unità di capacità riservata| Quantità di velocità effettiva da riservare. È possibile calcolare questo valore determinando la velocità effettiva necessaria per tutte le risorse di Cosmos DB (ad esempio, database o contenitori) per ogni area. Viene quindi moltiplicato per il numero di aree associate al database Cosmos. Se ad esempio si hanno cinque aree con 1 milione di unità richieste/sec in ogni area, selezionare 5 milioni di unità richieste/sec per l'acquisto di capacità di prenotazione. |


5. Una volta compilato il modulo, viene calcolato il prezzo necessario per acquistare la capacità riservata. L'output Mostra anche la percentuale di sconto ottenibile con le opzioni selezionate. Fare clic su **Seleziona**

6. Nel riquadro **prenotazioni acquisti** esaminare lo sconto e il prezzo della prenotazione. Il prezzo della prenotazione si applica alle risorse di Azure Cosmos DB con velocità effettiva di cui è stato effettuato il provisioning in tutte le aree.  

   :::image type="content" source="./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png" alt-text="Raccomandazioni sulla capacità riservata":::

7. Selezionare **Review + Buy** , quindi **acquistare ora**. Se l'acquisto ha esito positivo, viene visualizzata la pagina seguente:

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

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
