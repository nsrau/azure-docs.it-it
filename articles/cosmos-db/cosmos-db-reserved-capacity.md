---
title: Ottimizzare il costo delle risorse di Azure Cosmos DB con la capacità riservata
description: Informazioni su come acquistare capacità riservata di Azure Cosmos DB per risparmiare sui costi per i servizi di calcolo.
author: bandersmsft
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: e4d33756f1e09abedbf828deb447fbf14c6bfed7
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616911"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Ottimizzare il costo con la capacità riservata in Azure Cosmos DB

La capacità riservata di Azure Cosmos DB permette di risparmiare grazie al pagamento anticipato delle risorse di Azure Cosmos DB per un anno o tre anni. Con la capacità riservata di Azure Cosmos DB, è possibile ottenere uno sconto sulla velocità effettiva di cui viene effettuato il provisioning per le risorse di Cosmos DB, ad esempio database e contenitori (tabelle, raccolte e grafi).

La capacità riservata di Azure Cosmos DB consente di ridurre in modo significativo i costi di Cosmos DB, fino al 65% rispetto ai prezzi normali, con un impegno anticipato di un anno o di tre anni. La capacità riservata permette di ottenere uno sconto a livello di fatturazione e non ha alcuna ripercussione sullo stato di runtime delle risorse di Azure Cosmos DB.

La capacità riservata di Azure Cosmos DB copre la velocità effettiva con provisioning relativa alle risorse. Non copre i costi di archiviazione e rete. Non appena si acquista una prenotazione, i costi per la velocità effettiva che corrispondono agli attributi della prenotazione non vengono più addebitati alle tariffe con pagamento in base al consumo. Per altre informazioni sulle prenotazioni, vedere l'articolo [Informazioni sulle prenotazioni di Azure](../billing/billing-save-compute-costs-reservations.md).

È possibile acquistare capacità riservata di Azure Cosmos DB dal [portale di Azure](https://portal.azure.com). Per acquistare capacità riservata:

* È necessario avere il ruolo di proprietario per almeno una sottoscrizione Enterprise o singola con tariffe con pagamento in base al consumo.  
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

3. Dal riquadro **prenotazioni acquisti** scegliere **Azure Cosmos DB** per acquistare una nuova prenotazione.  

4. Compilare i campi obbligatori come descritto nella tabella seguente:

   ![Compilare il modulo per la capacità riservata](./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png)

   |Campo  |Descrizione  |
   |---------|---------|
   |Ambito   |   Opzione che controlla quante sottoscrizioni possono usare il vantaggio di fatturazione associato alla prenotazione. Controlla anche il modo in cui la prenotazione viene applicata alle sottoscrizioni specifiche. <br/><br/>  Se si seleziona **Condiviso**, lo sconto per la prenotazione viene applicato alle istanze di Azure Cosmos DB in esecuzione in qualsiasi sottoscrizione nel contesto di fatturazione. Il contesto di fatturazione si basa sul modo in cui è stata effettuata l'iscrizione ad Azure. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni all'interno della registrazione. Per i clienti con pagamento in base al consumo, l'ambito condiviso è costituito da tutte le singole sottoscrizioni con tariffe con pagamento in base al consumo create dall'amministratore account.  <br/><br/>  Se si seleziona **Sottoscrizione singola**, lo sconto per la prenotazione viene applicato alle istanze di Azure Cosmos DB nella sottoscrizione selezionata. <br/><br/> Se si seleziona **gruppo di risorse singolo**, lo sconto relativo alla prenotazione viene applicato alle istanze Azure Cosmos DB nella sottoscrizione selezionata e al gruppo di risorse selezionato all'interno di tale sottoscrizione. <br/><br/> È possibile modificare l'ambito della prenotazione dopo l'acquisto della capacità riservata.  |
   |Sottoscrizione  |   Sottoscrizione usata per pagare la capacità riservata di Azure Cosmos DB. Per l'addebito dei costi iniziali, viene usata la modalità di pagamento della sottoscrizione selezionata. La sottoscrizione deve essere di uno dei seguenti tipi: <br/><br/>  Contratto Enterprise (numero offerta: MS-AZR-0017P o MS-AZR-0148P): per una sottoscrizione Enterprise, il costo viene detratto dal saldo dell'impegno monetario della registrazione oppure viene addebitato come eccedenza. <br/><br/> Sottoscrizione singola con tariffe con pagamento in base al consumo (numeri di offerta: MS-AZR-0003P o MS-AZR-0023P): Per una singola sottoscrizione con tariffe con pagamento in base al consumo, i costi vengono addebitati sulla carta di credito o sul metodo di pagamento della fattura per la sottoscrizione.    |
   | Gruppo di risorse | Gruppo di risorse a cui viene applicato lo sconto per la capacità riservata. |
   |Termine  |   Un anno o tre anni.   |
   |Tipo di velocità effettiva   |  Il provisioning della velocità effettiva viene effettuato come unità richiesta. È possibile acquistare una prenotazione per la velocità effettiva di cui è stato effettuato il provisioning per entrambe le operazioni di scrittura: singole aree e scritture in più aree. Il tipo di velocità effettiva può scegliere tra due valori: 100 ur/s all'ora e 100 ur/sec multimaster all'ora.|
   | Unità di capacità riservata| Quantità di velocità effettiva da riservare. È possibile calcolare questo valore determinando la velocità effettiva necessaria per tutte le risorse di Cosmos DB (ad esempio, database o contenitori) per ogni area. Viene quindi moltiplicato per il numero di aree associate al database Cosmos. Ad esempio:  se si hanno cinque aree con 1 milione di unità richieste/sec in ogni area, selezionare 5 milioni di unità richieste/sec per l'acquisto di capacità di prenotazione. |


5. Una volta compilato il modulo, viene calcolato il prezzo necessario per acquistare la capacità riservata. L'output Mostra anche la percentuale di sconto ottenibile con le opzioni selezionate. Fare clic su **Seleziona**

6. Nel riquadro **prenotazioni acquisti** esaminare lo sconto e il prezzo della prenotazione. Il prezzo della prenotazione si applica alle risorse di Azure Cosmos DB con velocità effettiva di cui è stato effettuato il provisioning in tutte le aree.  

   ![Riepilogo capacità riservata](./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png)

7. Selezionare **Review + Buy** , quindi **acquistare ora**. Se l'acquisto ha esito positivo, viene visualizzata la pagina seguente:

Dopo l'acquisto, una prenotazione viene applicata immediatamente a tutte le risorse esistenti di Azure Cosmos DB che corrispondono ai termini della prenotazione. Se non ci sono risorse di Azure Cosmos DB esistenti, la prenotazione verrà applicata quando si distribuisce una nuova istanza di Cosmos DB che corrisponde ai termini della prenotazione. In entrambi i casi, il periodo della prenotazione inizia immediatamente dopo l'acquisto.

Quando la prenotazione scade, le istanze di Azure Cosmos DB continuano a essere eseguite e vengono fatturate alle normali tariffe con pagamento in base al consumo.

## <a name="cancel-exchange-or-refund-reservations"></a>Prenotazioni per annullamento, scambio o rimborso

Per informazioni sull'identificazione della capacità riservata corretta, vedere [informazioni su come viene applicato lo sconto per la prenotazione ai Azure Cosmos DB](../billing/billing-understand-cosmosdb-reservation-charges.md).

È possibile annullare, scambiare o rimborsare le prenotazioni con determinate limitazioni. Per altre informazioni, vedere [scambi e rimborsi self-service per le prenotazioni di Azure](../billing/billing-azure-reservations-self-service-exchange-and-refund.md).

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
