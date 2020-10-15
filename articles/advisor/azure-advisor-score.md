---
title: Ottimizzare i carichi di lavoro di Azure usando il Punteggio di Advisor
description: Usa il Punteggio di Advisor per sfruttare al meglio Azure
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: c4c401a8b532c247806c5fe30728a1f4afd334a4
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074023"
---
# <a name="optimize-azure-workloads-using-advisor-score"></a>Ottimizzare i carichi di lavoro di Azure usando il Punteggio di Advisor

## <a name="introduction-to-advisor-score"></a>Introduzione al Punteggio di Advisor

Azure Advisor fornisce consigli sulle procedure consigliate per i carichi di lavoro. Questi consigli sono personalizzati e possono essere utili per eseguire le operazioni seguenti:
* Migliorare la postura dei carichi di lavoro e ottimizzare le distribuzioni di Azure
* Prevenire in modo proattivo i problemi principali seguendo le procedure consigliate
* Valutazione dei carichi di lavoro di Azure rispetto ai cinque pilastri del [Framework Microsoft Azure Well-Architected](/azure/architecture/framework/)

Come caratteristica principale di Advisor, il **Punteggio di Advisor** è concepito per aiutare a raggiungere questi obiettivi in modo efficace ed efficiente. 

Per ottenere il massimo da Azure, è fondamentale comprendere dove si trova il percorso di ottimizzazione del carico di lavoro, quali servizi/risorse vengono utilizzati bene e quali no. Inoltre, è necessario essere in grado di definire le priorità delle azioni, in base alle raccomandazioni, per massimizzare il risultato. È anche importante tenere traccia e segnalare lo stato di avanzamento in questo percorso di ottimizzazione. Con il **Punteggio di Advisor**, è possibile eseguire facilmente tutte queste operazioni con la nuova esperienza di gamification. Come consulente cloud personalizzato, Azure Advisor valuta continuamente la telemetria di utilizzo e la configurazione delle risorse per verificare la presenza di procedure consigliate per il settore. Advisor quindi aggrega i risultati in un unico punteggio, in modo da poter dare un'occhiata, se si esegue la procedura necessaria per creare soluzioni affidabili, sicure ed efficienti in termini di costi. Il Punteggio di Advisor è costituito da un punteggio complessivo, che può essere suddiviso ulteriormente in cinque punteggi di categoria, uno per ogni categoria di Azure Advisor che rappresenta i cinque pilastri della Well-Architected Framework. È possibile tenere traccia dello stato di avanzamento nel tempo visualizzando il punteggio complessivo e il Punteggio di categoria con la tendenza giornaliera, settimanale e mensile ed è possibile impostare i benchmark per consentire il raggiungimento degli obiettivi. 

 ![Esperienza di valutazione di Advisor](./media/advisor-score-1.png)

## <a name="how-to-consume-advisor-score"></a>Come utilizzare il Punteggio di Advisor
Advisor Visualizza il punteggio e la suddivisione complessiva di Advisor per le categorie di Advisor, in percentuale. Un punteggio pari al 100% in qualsiasi categoria indica che tutte le risorse valutate da Advisor seguono le procedure consigliate consigliate da Advisor. Nell'altra estremità dello spettro, un punteggio pari allo 0% indica che nessuna delle risorse valutate da Advisor segue le raccomandazioni di Advisor. Usando questi grani di punteggio è possibile ottenere facilmente il flusso seguente:
* **Punteggio di Advisor** che consente di ottenere informazioni di base sulle modalità di funzionamento del carico di lavoro/sottoscrizioni in base al Punteggio di Advisor. È anche possibile vedere le tendenze cronologiche per comprendere la tendenza.
* I **punteggi di categoria di Advisor** di ogni raccomandazione indicano quali raccomandazioni in attesa miglioreranno il punteggio. Questi valori riflettono sia il peso della raccomandazione che la semplicità di implementazione prevista per assicurarsi che sia possibile ottenere la maggior parte del valore con il tempo e che consenta l'assegnazione delle priorità
* **Valutazione del Punteggio di categoria** per ogni raccomandazione che consente di classificare in ordine di priorità le azioni correttive per ogni categoria

Il contributo di ogni raccomandazione per il punteggio della categoria viene visualizzato chiaramente nella pagina Score Advisor della portale di Azure. È possibile aumentare il Punteggio di ogni categoria in base al punto percentuale elencato nella colonna Categoria dell'effetto punteggio. Questo valore riflette sia il peso della raccomandazione all'interno della categoria che la semplicità di implementazione prevista per risolvere i possibili frutti a basso rischio. Concentrandosi sulle raccomandazioni con un maggiore effetto sui punteggi, sarà possibile ottenere il massimo avanzamento con il tempo.  

![Effetto Punteggio di Advisor](./media/advisor-score-2.png)

Nel caso in cui le raccomandazioni di Advisor non siano rilevanti per una singola risorsa, è possibile posticiparle o ignorarle e verranno escluse dal calcolo del punteggio dall'aggiornamento successivo. Advisor utilizzerà anche questo input come feedback aggiuntivo per migliorare il modello.

## <a name="how-is-advisor-score-calculated"></a>Come viene calcolato il Punteggio di Advisor?
Advisor Visualizza i punteggi di categoria e il Punteggio di Advisor complessivo come percentuali. Un punteggio pari al 100% in qualsiasi categoria indica che tutte le risorse, *valutate in base a Advisor*, seguono le procedure consigliate consigliate da Advisor. Nell'altra estremità dello spettro, un punteggio pari allo 0% indica che nessuna delle risorse, valutata da Advisor, segue le raccomandazioni di Advisor. 
**Ognuna delle cinque categorie ha un punteggio potenziale massimo di 100.** Il Punteggio complessivo di Advisor viene calcolato come somma di ogni punteggio di categoria applicabile, diviso per la somma del Punteggio potenziale più alto da tutte le categorie applicabili. Per la maggior parte delle sottoscrizioni, questo significa che Advisor somma il Punteggio di ogni categoria e divide per 500. Tuttavia, **ogni punteggio di categoria viene calcolato solo se si usano risorse valutate da Advisor.**

**Esempio di calcolo del Punteggio di Advisor**
* Singolo Punteggio di sottoscrizione: questa è la media semplice di tutti i punteggi di categoria di Advisor per la sottoscrizione. Se i punteggi della categoria Advisor sono As-cost = 73, reliabilit = 85, Operational excellence = 77, performance = 100; il **Punteggio di Advisor** è (73 + 85 + 77 + 100)/(4x100) = 0,84 o 84%.
* Punteggio per più sottoscrizioni: quando si selezionano più sottoscrizioni, il Punteggio di Advisor generale generato è l'aggregazione ponderata dei punteggi di categoria. In questo caso, ogni punteggio di categoria di Advisor viene aggregato in base alle risorse utilizzate dalle sottoscrizioni. Una volta ottenuti i punteggi di categoria aggregati ponderati, viene eseguita una semplice media per fornire il punteggio complessivo per le sottoscrizioni. 


### <a name="scoring-methodology"></a>Metodologia di assegnazione dei punteggi: 
Il calcolo del Punteggio di Advisor può essere riepilogato in quattro passaggi:
1. Advisor calcola il **costo al dettaglio delle risorse interessate**, che sono le risorse delle sottoscrizioni con almeno una raccomandazione in Advisor.
2. Advisor calcola il costo per la **vendita al dettaglio delle risorse valutate**, ovvero le risorse monitorate da Advisor, indipendentemente dal fatto che dispongano o meno di raccomandazioni. 
3. Per ogni tipo di raccomandazione, Advisor calcola il **rapporto di risorse integro**, ovvero il costo al dettaglio delle risorse interessate divise per il costo finale delle risorse valutate.
4. Advisor applica tre pesi aggiuntivi al rapporto di risorse integre in ogni categoria:
  * Le raccomandazioni con un impatto maggiore sono ponderate più pesanti rispetto a quelle con un impatto minore.
  * Le risorse con raccomandazioni di lunga durata vengono conteggiate più in base al punteggio.
  * Le risorse rimandate o ignorate in Advisor vengono rimosse dal calcolo del punteggio. 
    
Advisor applica questo modello a livello di categoria Advisor (sicurezza usa il modello di [Punteggio sicuro](../security-center/secure-score-security-controls.md#introduction-to-secure-score) ), assegnando il Punteggio di Advisor per ogni categoria e una media più semplice produce il punteggio finale di Advisor.


## <a name="advisor-score-faq"></a>Domande frequenti sul punteggio di Advisor
* **Con quale frequenza viene aggiornato il Punteggio?**
Il Punteggio viene aggiornato almeno una volta al giorno. 
* **Perché alcune raccomandazioni hanno un valore "-" vuoto nella colonna Categoria Impact Score?** Advisor non include immediatamente nuove raccomandazioni o quelle con modifiche recenti al modello di assegnazione dei punteggi. Dopo un breve periodo di valutazione, in genere alcune settimane, verranno incluse nel punteggio. 
* **Perché il Punteggio di costo ha un impatto maggiore per alcune raccomandazioni anche se hanno un minor risparmio potenziale?**
Il Punteggio di costo riflette il potenziale risparmio di risorse sottoutilizzate e la semplicità prevista per l'implementazione di tali raccomandazioni. Ad esempio, viene applicato un peso aggiuntivo alle risorse interessate che sono inattive per un periodo di tempo più lungo, anche se il risparmio potenziale è inferiore. 
* **Perché non si dispone di un punteggio per una o più categorie o sottoscrizioni?**
Advisor genererà un punteggio solo per le categorie e le sottoscrizioni con risorse valutate da Advisor.
* **Che cosa accade se una raccomandazione non è pertinente?**
Se si ignora un Consiglio di Advisor, questo verrà omesso dal calcolo del punteggio. Le raccomandazioni di chiusura consentono inoltre a Advisor di migliorare la qualità delle raccomandazioni.
* **Perché il punteggio è stato modificato?** Il Punteggio può variare se si aggiornano le risorse interessate adottando le procedure consigliate consigliate da Advisor. Se gli utenti con le autorizzazioni per la sottoscrizione hanno modificato o creato nuove risorse, è possibile che si riscontrino fluttuazioni nel punteggio perché il punteggio è basato su un rapporto tra le risorse con impatto sui costi rispetto al costo totale di tutte le risorse.
* **In che modo Advisor calcola il costo al dettaglio delle risorse in una sottoscrizione?**
Advisor usa le tariffe con pagamento in base al consumo pubblicate nella pagina dei prezzi di Azure.com, che non riflette gli sconti applicabili, moltiplicato per la quantità di utilizzo nell'ultimo giorno in cui la risorsa è stata allocata. L'omissione di sconti dal calcolo del costo delle risorse rende il Punteggio di Advisor confrontabile tra sottoscrizioni, tenant e registrazioni in cui gli sconti possono variare. 
* **È necessario visualizzare le raccomandazioni in Advisor per ottenere i punti per il Punteggio?** No. Il Punteggio indica se adottare le procedure consigliate consigliate da Advisor, anche se si adottano tali procedure consigliate in modo proattivo e non si visualizzano mai le raccomandazioni in Advisor.
* **La metodologia di assegnazione dei punteggi distingue tra i carichi di lavoro di produzione e di sviluppo e test?**
No, non per ora, ma è possibile ignorare le raccomandazioni sulle singole risorse se tali risorse vengono usate per lo sviluppo e il test e la raccomandazione non è applicabile.
* **È possibile confrontare I punteggi tra una sottoscrizione con risorse 100 e una sottoscrizione con risorse 100.000?**
La metodologia di assegnazione dei punteggi è progettata per controllare il numero di risorse in una sottoscrizione e una combinazione di servizi, pertanto le sottoscrizioni con un minor numero di risorse possono avere punteggi superiori o inferiori rispetto alle sottoscrizioni con più risorse. 
* **Cosa significa quando viene visualizzato "Coming Soon" nella colonna Score Impact?**
Questo significa che si tratta di una nuova raccomandazione che è ancora in uso nel modello di Punteggio di Advisor. Una volta che questa nuova raccomandazione viene considerata nel calcolo dei punteggi, il valore dell'effetto del punteggio per la raccomandazione verrà visualizzato.  
* **Il mio punteggio dipende da quanto spendo in Azure?**
No, il punteggio non è necessariamente un riflesso della spesa e le spese superflue determineranno un punteggio di costo inferiore.

## <a name="how-to-access-advisor"></a>Come accedere a Advisor
Il Punteggio di Advisor è in anteprima pubblica in portale di Azure. È necessario passare alla sezione Advisor per trovare il Punteggio di Advisor come seconda voce di menu nel menu di spostamento a sinistra. 

![Punto di ingresso Punteggio di Advisor](./media/advisor-score-3.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:
* [Introduction to Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [Introduzione ad Advisor](advisor-get-started.md)
* [Raccomandazioni sui costi di Advisor](advisor-cost-recommendations.md)
* [Raccomandazioni sulle prestazioni di Advisor](advisor-performance-recommendations.md)
* [Raccomandazioni sulla sicurezza di Advisor](advisor-security-recommendations.md)
* [Raccomandazioni sull'eccellenza operativa di Advisor](advisor-operational-excellence-recommendations.md)