---
title: Ottimizzare i carichi di lavoro di Azure usando il Punteggio di Advisor
description: Usa il Punteggio di Advisor per sfruttare al meglio Azure
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: 720a2b358e35d776a7233452eee2bd69b521654f
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90056235"
---
# <a name="optimize-azure-workloads-using-advisor-score"></a>Ottimizzare i carichi di lavoro di Azure usando il Punteggio di Advisor

## <a name="introduction-to-advisor-score"></a>Introduzione al Punteggio di Advisor

Azure Advisor fornisce consigli sulle procedure consigliate per i carichi di lavoro. Questi consigli sono personalizzati e possono essere utili per eseguire le operazioni seguenti:
* Migliorare la postura dei carichi di lavoro e ottimizzare le distribuzioni di Azure
* Prevenire in modo proattivo i problemi principali seguendo le procedure consigliate
* Valutazione dei carichi di lavoro di Azure rispetto ai cinque pilastri della [Microsoft Azure Framework ben progettato](https://docs.microsoft.com/azure/architecture/framework/)

Come caratteristica principale di Advisor, il **Punteggio di Advisor** è concepito per aiutare a raggiungere questi obiettivi in modo efficace ed efficiente. 

Per ottenere il massimo da Azure, è fondamentale comprendere dove si trova il percorso di ottimizzazione del carico di lavoro, quali servizi/risorse vengono utilizzati bene e quali no. Inoltre, è necessario essere in grado di definire le priorità delle azioni, in base alle raccomandazioni, per massimizzare il risultato. È anche importante tenere traccia e segnalare lo stato di avanzamento in questo percorso di ottimizzazione. Con il **Punteggio di Advisor**, è possibile eseguire facilmente tutte queste operazioni con la nuova esperienza di gamification. Come consulente cloud personalizzato, Azure Advisor valuta continuamente la telemetria di utilizzo e la configurazione delle risorse per verificare la presenza di procedure consigliate per il settore. Advisor quindi aggrega i risultati in un unico punteggio, in modo da poter dare un'occhiata, se si esegue la procedura necessaria per creare soluzioni affidabili, sicure ed efficienti in termini di costi. Il Punteggio di Advisor è costituito da un punteggio complessivo, che può essere suddiviso ulteriormente in cinque punteggi di categoria, uno per ogni categoria di Azure Advisor che rappresenta i cinque pilastri del Framework ben architettato. È possibile tenere traccia dello stato di avanzamento nel tempo visualizzando il punteggio complessivo e il Punteggio di categoria con la tendenza giornaliera, settimanale e mensile ed è possibile impostare i benchmark per consentire il raggiungimento degli obiettivi. 

 ![Esperienza di valutazione di Advisor](./media/advisor-score-1.png)

## <a name="how-to-consume-advisor-score"></a>Come utilizzare il Punteggio di Advisor
Advisor Visualizza il punteggio e la suddivisione complessiva di Advisor per le categorie di Advisor, in percentuale. Un punteggio pari al 100% in qualsiasi categoria indica che tutte le risorse valutate da Advisor seguono le procedure consigliate consigliate da Advisor. Nell'altra estremità dello spettro, un punteggio pari allo 0% indica che nessuna delle risorse valutate da Advisor segue le raccomandazioni di Advisor. Usando questi grani di punteggio è possibile ottenere facilmente il flusso seguente:
* **Punteggio di Advisor** che consente di ottenere informazioni di base sulle modalità di funzionamento del carico di lavoro/sottoscrizioni in base al Punteggio di Advisor. È anche possibile vedere le tendenze cronologiche per comprendere la tendenza.
* **Punteggi della categoria Advisor** che consentono di individuare le categorie che richiedono maggiore attenzione e consentono di definire le priorità
* **Potenziale aumento del Punteggio** di ogni raccomandazione che consente di assegnare una priorità alle azioni correttive per ogni categoria

Il contributo di ogni raccomandazione per il punteggio è chiaramente visualizzato nella pagina Panoramica del portale di Azure. È possibile aumentare il Punteggio adottando le procedure consigliate ed è possibile classificare in ordine di priorità le raccomandazioni che hanno un **aumento del Punteggio potenziale** maggiore per ottenere lo stato di avanzamento più rapido con il tempo necessario.  

![Effetto Punteggio di Advisor](./media/advisor-score-2.png)

Dal momento che la metodologia di assegnazione dei punteggi di Advisor applica un peso aggiuntivo a risorse più costose con raccomandazioni di lunga durata, è possibile migliorare lo stato di avanzamento monitorando e aggiornando le risorse con il costo finale più elevato. Se una delle raccomandazioni di Advisor non è pertinente per una singola risorsa, è possibile ignorare tali raccomandazioni per escluderle dal calcolo dei punteggi e inviare commenti e suggerimenti a Advisor per migliorare le raccomandazioni. 

## <a name="how-is-advisor-score-calculated"></a>Come viene calcolato il Punteggio di Advisor?
Advisor Visualizza i punteggi di categoria e il Punteggio di Advisor complessivo come percentuali. Un punteggio pari al 100% in qualsiasi categoria indica che tutte le risorse, *valutate in base a Advisor*, seguono le procedure consigliate consigliate da Advisor. Nell'altra estremità dello spettro, un punteggio pari allo 0% indica che nessuna delle risorse, valutata da Advisor, segue le raccomandazioni di Advisor. 
**Ognuna delle cinque categorie ha un punteggio potenziale massimo di 100.** Il Punteggio complessivo di Advisor viene calcolato come somma di ogni punteggio di categoria applicabile, diviso per la somma del Punteggio potenziale più alto da tutte le categorie applicabili. Per la maggior parte delle sottoscrizioni, questo significa che Advisor somma il Punteggio di ogni categoria e divide per 500. Tuttavia, **ogni punteggio di categoria viene calcolato solo se si usano risorse valutate da Advisor.**

### <a name="scoring-methodology"></a>Metodologia di assegnazione dei punteggi: 
Il calcolo del Punteggio di Advisor può essere riepilogato in quattro passaggi:
1. Advisor calcola il **costo al dettaglio giornaliero delle risorse interessate**, che sono le risorse per le sottoscrizioni con almeno una raccomandazione in Advisor.
2. Advisor calcola il costo per la **vendita al dettaglio giornaliero delle risorse valutate**, ovvero le risorse monitorate da Advisor, indipendentemente dal fatto che siano presenti raccomandazioni o meno. 
3. Per ogni tipo di raccomandazione, Advisor calcola il **rapporto di risorse integro**, ovvero il costo delle risorse interessate divise per il costo delle risorse valutate.
4. Advisor applica tre pesi aggiuntivi al rapporto di risorse integre in ogni categoria:
* Le raccomandazioni con un impatto maggiore sono ponderate più pesanti rispetto a quelle con un impatto minore.
* Le risorse con raccomandazioni di lunga durata vengono conteggiate più in base al punteggio.
* Le risorse ignorate in Advisor vengono rimosse dal calcolo del punteggio. 
    
Advisor applica questo modello a livello di categoria Advisor (sicurezza usa il modello di [Punteggio sicuro](https://docs.microsoft.com/azure/security-center/secure-score-security-controls#introduction-to-secure-score) ), assegnando il Punteggio di Advisor per ogni categoria e una media più semplice produce il punteggio finale di Advisor.


## <a name="advisor-score-faq"></a>Domande frequenti sul punteggio di Advisor
* **Con quale frequenza viene aggiornato il Punteggio?**
Il Punteggio viene aggiornato almeno una volta al giorno. 
* **È necessario visualizzare le raccomandazioni in Advisor per ottenere un punto per il Punteggio?**
No. Il Punteggio indica se adottare le procedure consigliate consigliate da Advisor, anche se non si visualizzano mai tali raccomandazioni in Advisor e si adottano in modo proattivo le procedure consigliate.  
* **In che modo Advisor calcola il costo al dettaglio giornaliero per le risorse di una sottoscrizione?**
Advisor usa le tariffe *con pagamento in base* al consumo pubblicate nella pagina dei prezzi di Azure.com, che non riflette gli sconti applicabili, moltiplicato per la quantità di utilizzo nell'ultimo giorno in cui la risorsa è stata allocata. L'omissione di sconti dal calcolo del costo delle risorse rende il Punteggio di Advisor confrontabile tra sottoscrizioni, tenant e registrazioni in cui gli sconti possono variare. 
* **Che cosa accade se una raccomandazione non è pertinente?**
Se si ignora un Consiglio di Advisor, questo verrà omesso dal calcolo del punteggio. Le raccomandazioni di chiusura consentono inoltre a Advisor di migliorare la qualità delle raccomandazioni.
* **Perché il punteggio è stato modificato?** Il Punteggio può variare se si aggiornano le risorse interessate adottando le procedure consigliate consigliate da Advisor. Se gli utenti con le autorizzazioni per la sottoscrizione hanno modificato o creato nuove risorse, è possibile che si riscontrino fluttuazioni nel punteggio perché il punteggio è basato su un rapporto tra le risorse con impatto sui costi rispetto al costo totale di tutte le risorse.
* **Il mio punteggio dipende da quanto spendo in Azure?**
No. Il Punteggio è progettato per controllare la dimensione di una sottoscrizione e una combinazione di servizi. 
* **La metodologia di assegnazione dei punteggi distingue tra i carichi di lavoro di produzione e di sviluppo e test?**
No, non per ora, ma è possibile ignorare le raccomandazioni sulle singole risorse se tali risorse vengono usate per lo sviluppo e il test e la raccomandazione non è applicabile.
* **È possibile confrontare I punteggi tra una sottoscrizione con risorse 100 e una sottoscrizione con risorse 100.000?**
La metodologia di assegnazione dei punteggi è progettata per controllare il numero di risorse in una sottoscrizione e una combinazione di servizi, pertanto le sottoscrizioni con un minor numero di risorse possono avere punteggi superiori o inferiori rispetto alle sottoscrizioni con più risorse. 

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
