---
title: Pagare in anticipo le risorse di Azure Cosmos DB per risparmiare | Microsoft Docs
description: Informazioni su come acquistare capacità riservata di Azure Cosmos DB per risparmiare sui costi per i servizi di calcolo.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 79b3b84695bd547d6a4b12665aff822b21dfb781
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49354224"
---
# <a name="prepay-for-azure-cosmos-db-resources-with-reserved-capacity"></a>Pagare in anticipo le risorse di Azure Cosmos DB con la capacità riservata

La capacità riservata di Azure Cosmos DB permette di risparmiare grazie al pagamento anticipato delle risorse di Azure Cosmos DB per un anno o tre anni. Con la capacità riservata di Azure Cosmos DB, è possibile ottenere uno sconto sulla velocità effettiva di cui viene effettuato il provisioning per le risorse di Cosmos DB, ad esempio database e contenitori (tabelle, raccolte e grafi).

La capacità riservata di Azure Cosmos DB consente di ridurre in modo significativo i costi di Cosmos DB, fino al 65% rispetto ai prezzi normali, con un impegno anticipato di un anno o di tre anni. La capacità riservata permette di ottenere uno sconto a livello di fatturazione e non ha alcuna ripercussione sullo stato di runtime delle risorse di Azure Cosmos DB.

La capacità riservata di Azure Cosmos DB copre la velocità effettiva con provisioning relativa alle risorse. Non copre i costi di archiviazione e rete. Non appena si acquista una prenotazione, i costi per la velocità effettiva che corrispondono agli attributi della prenotazione non vengono più addebitati alle tariffe con pagamento in base al consumo. Per altre informazioni sulle prenotazioni, vedere l'articolo [Informazioni sulle prenotazioni di Azure](../billing/billing-save-compute-costs-reservations.md). 

È possibile acquistare capacità riservata di Azure Cosmos DB dal [portale di Azure](https://portal.azure.com). Per acquistare capacità riservata:

* È necessario avere il ruolo di Proprietario per almeno una sottoscrizione Enterprise o con pagamento in base al consumo.  
* Per le sottoscrizioni Enterprise, gli acquisti di prenotazioni di Azure devono essere abilitati nel [portale EA](https://ea.azure.com/).  
* Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare capacità riservata di Azure Cosmos DB.

## <a name="determine-the-required-throughput-before-purchase"></a>Determinare la velocità effettiva necessaria prima dell'acquisto

Le dimensioni della prenotazione devono essere basate sulla quantità totale di velocità effettiva che verrà usata dalle risorse di Azure Cosmos DB esistenti o che verranno distribuite a breve. È possibile determinare la velocità effettiva necessaria nei modi seguenti:

* Ottenere i dati cronologici per la velocità effettiva totale sottoposta a provisioning negli account, nei database e nelle raccolte di Azure Cosmos DB in tutte le aree. È ad esempio possibile valutare la velocità effettiva giornaliera media sottoposta a provisioning scaricando il rendiconto sull'utilizzo giornaliero da `https://account.azure.com`.

* I clienti con contratto Enterprise possono scaricare il file di utilizzo per ottenere i dettagli sulla velocità effettiva di Azure Cosmos DB. Fare riferimento al valore di **Tipo di servizio** nella sezione **Informazioni aggiuntive** del file di utilizzo.

* È possibile sommare la velocità effettiva media per tutti i carichi di lavoro negli account di Azure Cosmos DB che si prevede di eseguire nell'anno o nei tre anni successivi. È quindi possibile usare tale quantità per la prenotazione.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Acquistare capacità riservata di Azure Cosmos DB

1. Accedere al [portale di Azure](https://portal.azure.com).  

2. Selezionare **Tutti i servizi** > **Prenotazioni** > **Aggiungi**.  

3. Nel riquadro **Selezionare il tipo di prodotto** scegliere **Azure Cosmos DB** > **Seleziona** per acquistare una nuova prenotazione.  

4. Compilare i campi obbligatori come descritto nella tabella seguente:

   ![Compilare il modulo per la capacità riservata](./media/cosmos-db-reserved-capacity/fill_reserved_capacity_form.png) 

   |Campo  |DESCRIZIONE  |
   |---------|---------|
   |NOME   |    Nome della prenotazione. Questo campo viene popolato automaticamente con `CosmosDB_Reservation_<timeStamp>`. È possibile immettere un nome diverso durante la creazione della prenotazione o rinominare la prenotazione dopo la creazione.      |
   |Sottoscrizione  |   Sottoscrizione usata per pagare la capacità riservata di Azure Cosmos DB. Per l'addebito dei costi iniziali, viene usata la modalità di pagamento della sottoscrizione selezionata. Il tipo di sottoscrizione deve essere uno dei seguenti: <br/><br/>  [Contratto Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) (numero offerta: MS-AZR-0017P): per una sottoscrizione Enterprise, il costo viene detratto dal saldo dell'impegno monetario della registrazione oppure viene addebitato come eccedenza. <br/><br/> [Con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) (numero offerta: MS-AZR-0003P): per le sottoscrizioni con pagamento in base al consumo, il costo viene addebitato sulla carta di credito o sulla modalità di pagamento tramite fattura nella sottoscrizione.    |
   |Scope   |   Opzione che controlla quante sottoscrizioni possono usare il vantaggio di fatturazione associato alla prenotazione. Controlla anche il modo in cui la prenotazione viene applicata alle sottoscrizioni specifiche.   <br/><br/>  Se si seleziona **Sottoscrizione singola**, lo sconto per la prenotazione viene applicato alle istanze di Azure Cosmos DB nella sottoscrizione selezionata. <br/><br/>  Se si seleziona **Condiviso**, lo sconto per la prenotazione viene applicato alle istanze di Azure Cosmos DB in esecuzione in qualsiasi sottoscrizione nel contesto di fatturazione. Il contesto di fatturazione si basa sul modo in cui è stata effettuata l'iscrizione ad Azure. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni (eccetto le sottoscrizioni di sviluppo/test) all'interno della registrazione. Per i clienti con pagamento in base al consumo, l'ambito condiviso copre tutte le sottoscrizioni con pagamento in base al consumo create dall'amministratore dell'account.  <br/><br/> È possibile modificare l'ambito della prenotazione dopo l'acquisto della capacità riservata.  |
   |Tipo di capacità riservata   |  Velocità effettiva di cui viene effettuato il provisioning come unità di richieste.|
   |Unità di capacità riservate  |      Quantità di velocità effettiva da riservare. È possibile calcolare questo valore determinando la velocità effettiva necessaria per tutte le risorse di Cosmos DB (ad esempio, database o contenitori) per ogni area. Lo si moltiplica quindi per il numero di aree da associare al database di Cosmos DB.  <br/><br/> Se ad esempio si hanno cinque aree con 1 milione di unità richieste/sec in ogni area, selezionare 5 milioni di unità richieste/sec per l'acquisto di capacità di prenotazione.    |
   |Termine  |   Un anno o tre anni.   |

5. Esaminare lo sconto e il prezzo della prenotazione nella sezione **Costi**. Il prezzo della prenotazione si applica alle risorse di Azure Cosmos DB con velocità effettiva di cui è stato effettuato il provisioning in tutte le aree.  

6. Selezionare **Acquisto**. Se l'acquisto ha esito positivo, viene visualizzata la pagina seguente: 

   ![Compilare il modulo per la capacità riservata](./media/cosmos-db-reserved-capacity/reserved_capacity_successful.png) 

Dopo l'acquisto, una prenotazione viene applicata immediatamente a tutte le risorse esistenti di Azure Cosmos DB che corrispondono ai termini della prenotazione. Se non ci sono risorse di Azure Cosmos DB esistenti, la prenotazione verrà applicata quando si distribuisce una nuova istanza di Cosmos DB che corrisponde ai termini della prenotazione. In entrambi i casi, il periodo della prenotazione inizia immediatamente dopo l'acquisto. 

Quando la prenotazione scade, le istanze di Azure Cosmos DB continuano a essere eseguite e vengono fatturate alle normali tariffe con pagamento in base al consumo.

## <a name="next-steps"></a>Passaggi successivi

Lo sconto per la prenotazione si applica automaticamente alle risorse di Azure Cosmos DB che corrispondono agli attributi e all'ambito della prenotazione. È possibile aggiornare l'ambito della prenotazione tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure o l'API.

*  Per informazioni su come vengono applicati gli sconti per la capacità riservata in Azure Cosmos DB, vedere [Informazioni su come viene applicato lo sconto per la prenotazione ad Azure Cosmos DB](../billing/billing-understand-cosmosdb-reservation-charges.md).

* Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

   * [Cosa sono le prenotazioni di Azure?](../billing/billing-save-compute-costs-reservations.md)  
   * [Gestire le prenotazioni di Azure](../billing/billing-manage-reserved-vm-instance.md)  
   * [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md)  
   * [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](../billing/billing-understand-reserved-instance-usage.md)
   * [Prenotazioni di Azure nel programma CSP del Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

