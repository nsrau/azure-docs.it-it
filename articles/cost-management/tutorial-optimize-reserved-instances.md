---
title: 'Esercitazione: Ottimizzare i costi delle istanze riservate con Gestione costi di Azure | Microsoft Docs'
description: In questa esercitazione si apprenderà come ottimizzare i costi delle istanze riservate per Azure e Amazon Web Services (AWS).
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/27/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 5e068f971c8b8996273210f385ca4f83df8e4efb
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
<!-- Intent: As a cloud-consuming administrator, I need to ensure that my reserved instances are optimized for cost and usage
-->

# <a name="tutorial-optimize-reserved-instances"></a>Esercitazione: Ottimizzare le istanze riservate

In questa esercitazione si apprenderà come Gestione costi può consentire di ottimizzare l'utilizzo e i costi delle istanze riservate per Azure e Amazon Web Services (AWS). Un'istanza riservata con un provider di servizi cloud è un impegno per un contratto a lungo termine in cui si esegue il commit anticipato per un uso futuro della macchina virtuale. Questa istanza può offrire potenzialmente un notevole risparmio rispetto a un modello di determinazione dei prezzi standard delle macchine virtuali in base al consumo. Il risparmio potenziale si realizza solo quando si usa completamente la capacità delle istanze riservate.

Questa esercitazione illustra come le istanze riservate di Azure e AWS vengono supportate da Gestione costi. Descrive anche come è possibile ottimizzare i costi delle istanze riservate. È prima di tutto necessario assicurarsi che le prenotazioni vengano utilizzate appieno. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Conoscere i costi delle istanze riservate di Azure
> * Ottenere informazioni sui vantaggi delle istanze riservate
> * Ottimizzare i costi delle istanze riservate di Azure
> * Visualizzare i costi delle istanze riservate
> * Valutare l'efficacia dei costi delle istanze riservate di Azure
> * Ottimizzare i costi delle istanze riservate di AWS
> * Acquistare istanze riservate consigliate
> * Modificare le prenotazioni inutilizzate

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>prerequisiti

- È necessario disporre di un account Azure.
- È necessario avere una registrazione di valutazione o una sottoscrizione a pagamento per Gestione costi di Azure.
- È necessario aver acquistato istanze riservate in Azure o AWS.

## <a name="understand-azure-ri-costs"></a>Conoscere i costi delle istanze riservate di Azure

Quando si acquistano le istanze di macchina virtuale riservate di Azure, si paga in anticipo per un uso futuro. Il pagamento anticipato copre il costo dell'uso futuro delle macchine virtuali:

- di un tipo specifico
- in un'area specifica
- per un periodo di uno o tre anni
- fino a una quantità di macchine virtuali acquistate.

È possibile visualizzare le istanze di macchina virtuale riservate di Azure acquistate nel portale di Azure in [Prenotazioni](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

Il termine _istanza di macchina virtuale riservata di Azure_ si applica solo a un modello di determinazione dei prezzi. Non modifica assolutamente le macchine virtuali in esecuzione. Il termine è specifico di Azure e più in generale viene indicato come _istanza riservata_ o _prenotazione_. Le istanze riservate acquistate non si applicano a macchine virtuali specifiche, ma a tutte le macchine virtuali corrispondenti. Ad esempio, una prenotazione per un tipo di macchina virtuale eseguita in un'area scelta per la prenotazione acquistata.

Le istanze riservate acquistate si applicano solo all'hardware di base. Non coprono le licenze software di una macchina virtuale. Ad esempio, è possibile riservare un'istanza e avere una macchina virtuale corrispondente che esegue Windows. Le istanze riservate coprono solo il costo base della macchina virtuale. In questo esempio, si paga il prezzo intero di una licenza di Windows necessaria. Per ottenere uno sconto sul sistema operativo o un altro software in esecuzione nelle macchine virtuali, è consigliabile usare il [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit). Il Vantaggio Azure Hybrid offre un tipo di sconto per le licenze software simile a quello offerto dalle istanze riservate per le macchine virtuali di base.

L'utilizzo delle istanze riservate non influisce direttamente sul costo. In altre parole, eseguire una macchina virtuale con un utilizzo della CPU pari al 100% o allo 0% ha lo stesso effetto: si paga in anticipo per l'allocazione della macchina virtuale, non per l'utilizzo effettivo.

Nella figura seguente viene illustrata la correlazione tra utilizzo standard di macchine virtuali on demand e costi in relazione alle istanze riservate:

![Costi on demand rispetto ai costi di istanze riservate](./media/tutorial-optimize-reserved-instances/azure01.png)



Le barre rosse mostrano il costo complessivo dell'acquisto di istanze riservate. Si effettua solo un pagamento una tantum. L'utilizzo della macchina virtuale è gratuito. Le barre blu mostrano il costo complessivo della stessa macchina virtuale in esecuzione con il modello di determinazione dei prezzi in base al consumo o on-demand. Tra il settimo e l'ottavo mese di utilizzo della macchina virtuale si può notare un *punto di pareggio*. In questo esempio il risparmio inizia a partire dall'ottavo mese.

## <a name="benefits-of-ris"></a>Vantaggi delle istanze riservate

Ogni acquisto di un'istanza riservata si applica a una macchina virtuale di dimensioni e località specifiche. Ad esempio, D2s\_v3 in esecuzione nella località Stati Uniti occidentali, come illustrato nella figura seguente:

![Dettagli dell'istanza riservata di Azure](./media/tutorial-optimize-reserved-instances/azure02.png)

L'acquisto di un'istanza riservata diventa utile quando una macchina virtuale viene eseguita per un numero sufficiente di ore da raggiungere il punto di pareggio della prenotazione. La macchina virtuale deve corrispondere alle dimensioni e a una località dell'istanza riservata. Ad esempio, nel grafico precedente il punto di pareggio si raggiunge alla metà del settimo mese. L'acquisto è quindi utile quando la macchina virtuale corrispondente alla prenotazione viene eseguita per almeno 7,5 mesi \* 30 giorni \* 24 ore = 5.400 ore. Se la macchina virtuale corrispondente viene eseguita meno di 5.400 ore, la prenotazione è più costosa rispetto al pagamento in base al consumo.

Il punto di pareggio potrebbe essere diverso a seconda delle dimensioni della macchina virtuale e della località. Dipende anche dal prezzo concordato per il pagamento in base al consumo della macchina virtuale. Prima di effettuare un acquisto, è consigliabile controllare il punto di pareggio applicabile al caso in questione.

Un altro aspetto da considerare quando si acquista la prenotazione è l'ambito dell'istanza riservata. L'ambito determina se il vantaggio della prenotazione è condiviso o se si applica a una sottoscrizione specifica. Le istanze riservate condivise vengono applicate in modo casuale in tutte le sottoscrizioni alle macchine virtuali corrispondenti trovate per prime.

L'ambito di acquisto condiviso è il più flessibile ed è consigliabile laddove possibile. Le possibilità di utilizzo di tutte le istanze riservate sono notevolmente superiori con l'ambito condiviso. Tuttavia, quando il proprietario di una sottoscrizione acquista l'istanza riservata, è possibile che l'unica scelta disponibile sia quella con l'ambito della sottoscrizione singola.

## <a name="optimize-azure-ri-costs"></a>Ottimizzare i costi delle istanze riservate di Azure

Gestione costi di Azure supporta le istanze riservate e il Vantaggio Azure Hybrid per eseguire le operazioni seguenti:

- Mostrare i costi associati ai modelli di determinazione dei prezzi
- Tenere traccia dell'utilizzo delle istanze riservate
- Valutare l'impatto delle istanze riservate
- Allocare i costi delle istanze riservate in base ai criteri dell'utente

La prima azione da intraprendere prima di acquistare un'istanza riservata è valutare l'impatto dell'acquisto di istanze riservate:

- Quale sarà il costo?
- Quale sarà il risparmio?
- Cos'è il punto di pareggio?

Il report di impatto dell'acquisto di istanze riservate consente di rispondere a tali domande.

## <a name="assess-azure-ri-cost-effectiveness"></a>Valutare l'efficacia dei costi delle istanze riservate di Azure

Nel portale di Cloudyn passare a **Optimizer** (Utilità di ottimizzazione) > **RI Comparison** (Confronto istanze riservate) e quindi selezionare **Reserved Instance Purchase Impact** (Impatto dell'acquisto di istanze riservate).

Nel report di impatto dell'acquisto di istanze riservate selezionare le dimensioni della macchina virtuale (tipo di istanza), la località (area), il periodo di prenotazione, la quantità e il runtime previsto. È quindi possibile valutare se l'acquisto produrrà un risparmio.

Ad esempio, se si acquista una prenotazione per una macchina virtuale di tipo DS1\_v2 negli Stati Uniti orientali che viene eseguita 24x7 per un intero anno, si otterrà un risparmio annuale di $369,48. Il punto di pareggio è al quinto mese. Vedere l'immagine seguente:

![Punto di pareggio dell'istanza riservata di Azure](./media/tutorial-optimize-reserved-instances/azure03.png)

Tuttavia, se viene eseguita solo il 50% del tempo, il punto di pareggio sarà al 10 mese e il risparmio annuale sarà solo di $49,74. In questo esempio si potrebbe non trarre alcun vantaggio dall'acquisto della prenotazione per tale tipo di istanza. Vedere l'immagine seguente:

![Punto di pareggio in Azure](./media/tutorial-optimize-reserved-instances/azure04.png)

## <a name="view-ri-costs"></a>Visualizzare i costi delle istanze riservate

Quando si acquista una prenotazione, si effettua solo un pagamento una tantum. Esistono due modi per visualizzare il pagamento in Gestione costi:

- Costo effettivo
- Costo ammortizzato

### <a name="actual-reserved-instance-cost"></a>Costo effettivo delle istanze riservate

I report di analisi dei costi effettivi e di analisi nel tempo mostrano l'intero importo corrisposto per la prenotazione, a partire dal mese di acquisto. Consentono di visualizzare la spesa effettiva per un determinato periodo.

Passare a **Cost** (Costo) > **Cost Analysis** (Analisi dei costi) nel portale di Cloudyn e quindi fare clic su **Actual Cost Analysis** (Analisi dei costi effettivi) o **Actual Cost Over Time** (Costo effettivo nel tempo). Successivamente, impostare i filtri. Ad esempio, filtrare solo per servizio di Azure/macchina virtuale e raggruppare per tipo di risorsa e modello di prezzo. Vedere l'immagine seguente:

![Costo effettivo dell'istanza riservata](./media/tutorial-optimize-reserved-instances/azure05.png)

È possibile filtrare per servizio, **Azure/macchina virtuale** in questo esempio, e raggruppare per **tipo di risorsa** e **modello di prezzo** come illustrato nella figura seguente:

![Filtri e gruppi del report del costo effettivo](./media/tutorial-optimize-reserved-instances/azure06.png)

È anche possibile analizzare il tipo di pagamento effettuato, ad esempio pagamento una tantum, spese di utilizzo e spese di licenza.

### <a name="amortized-reserved-instance-cost"></a>Costo ammortizzato delle istanze riservate

È possibile pagare una tariffa in anticipo visibile nel mese dell'acquisto di un'istanza riservata. Non è visibile nelle fatture successive. Pertanto, esaminare l'utilizzo mensile può risultare fuorviante. Il costo di un mese è pari effettivamente all'utilizzo mensile più la parte proporzionale (ammortizzata) del pagamento una tantum effettuato in precedenza. Il report del costo ammortizzato consente di avere il quadro reale della situazione.

Il costo ammortizzato delle istanze riservate viene calcolato prendendo il costo una tantum della prenotazione e ammortizzandolo per il periodo di prenotazione. Nei report del costo effettivo il costo una tantum è visibile nel mese dell'acquisto della prenotazione. La spesa giornaliera e mensile non viene visualizzata nel costo effettivo della distribuzione. I report del costo ammortizzato mostrano il costo effettivo della distribuzione nel tempo.  Il report del costo ammortizzato è l'unico modo per visualizzare le tendenze dei costi effettive. È anche l'unico modo per fare una previsione della spesa futura.

Nel report del costo effettivo si può notare un picco per l'acquisto di un'istanza riservata effettuato il 16 novembre di $747. Nel report del costo ammortizzato (vedere la figura seguente) al 16 novembre viene riportato un costo giornaliero parziale. A partire dal 17 novembre verrà visualizzato il costo ammortizzato dell'istanza riservata di $747/365 = $2,05. Tra l'altro, si può notare anche che la prenotazione acquistata è inutilizzata, pertanto è possibile ottimizzarla passando a una macchina virtuale di dimensioni diverse.

Per visualizzare questo report, passare a **Cost** (Costo) > **Cost Analysis** (Analisi dei costi) e quindi selezionare **Amortized Cost Analysis** (Analisi dei costi ammortizzati) o **Amortized Cost Over Time** (Costo ammortizzato nel tempo).

![Costo ammortizzato delle istanze riservate](./media/tutorial-optimize-reserved-instances/azure07.png)

## <a name="optimize-aws-ri-costs"></a>Ottimizzare i costi delle istanze riservate di AWS

Le istanze riservate sono un impegno aperto. Sono utili quando si ha un utilizzo sostenuto delle macchine virtuali perché le istanze riservate sono meno costose delle istanze on demand. Tuttavia, devono essere sufficientemente usate. L'impegno consiste nell'usare le risorse, in genere le macchine virtuali, per un periodo di tempo definito: uno o tre anni. Quando si effettua l'impegno di acquisto, si paga in anticipo per le risorse con una prenotazione. Tuttavia, si potrebbe non sempre usare completamente quanto previsto dall'impegno di acquisto con la prenotazione.

Ad esempio, si potrebbe valutare l'ambiente e stabilire che nell'ultimo anno sono state eseguite costantemente 20 istanze D2 standard. Si potrebbe quindi acquistare una prenotazione per tali istanze con un notevole risparmio. In un altro esempio l'impegno di acquisto potrebbe prevedere l'uso di dieci istanza MA4 per un anno, mentre finora potrebbero esserne state usate solo cinque. Entrambi gli esempi illustrano un utilizzo inefficiente delle istanze riservate. Esistono due modi per ottimizzare i costi per le istanze riservate con i report di ottimizzazione di Cloudyn:

- Analizzare i consigli per gli acquisti in base all'utilizzo cronologico
- Modificare le prenotazioni inutilizzate

Usare i report dei _consigli per gli acquisti di istanze riservate EC2_ e delle _prenotazioni attualmente inutilizzate EC2_ per migliorare l'utilizzo delle istanze riservate e i costi.

## <a name="buy-recommended-ris"></a>Acquistare istanze riservate consigliate

Cloudyn confronta l'utilizzo delle istanze on demand e lo confronta con le istanze riservate potenziali. Quando viene rilevato un possibile risparmio, vengono visualizzati i consigli nel relativo report.

Nel menu dei report nella parte superiore del portale fare clic su **Optimizer (Utilità di ottimizzazione)** > **Pricing Optimization (Ottimizzazione prezzi)**  > **EC2 RI Buying Recommendations** (Consigli per gli acquisti di istanze riservate EC2).

L'immagine seguente mostra i consigli per gli acquisti presenti nel report.

![Consigli per gli acquisti](./media/tutorial-optimize-reserved-instances/aws01.png)

In questo esempio l'account Cloudyn\_A ha 32 consigli per gli acquisti di istanze riservate. Se si seguono tutti i consigli per gli acquisti, è possibile risparmiare annualmente $137.770. Tenere presente che i consigli per gli acquisti forniti da Cloudyn presuppongono che l'utilizzo dei carichi di lavoro in esecuzione rimanga costante.

Per visualizzare i dettagli che spiegano il motivo per cui è consigliato ogni acquisto, fare clic sul segno più ( **+** ) in **Justifications** (Motivazioni). Di seguito è riportato un esempio per il primo consiglio nell'elenco.

![Motivazioni di acquisto](./media/tutorial-optimize-reserved-instances/aws02.png)

L'esempio precedente mostra che il costo annuale dell'esecuzione del carico di lavoro on demand sarebbe di $90.456. Tuttavia, se si acquista in anticipo la prenotazione, lo stesso carico di lavoro costerebbe $56.592 con un risparmio annuale di $33.864.

Fare clic sul segno più accanto a **EC2 RI Purchase Impact** (Impatto dell'acquisto di istanze riservate EC2) per visualizzare il punto di pareggio in un anno e vedere quando viene realizzato approssimativamente l'investimento dell'acquisto. L'esempio seguente mostra come otto mesi circa dopo aver effettuato l'acquisto, il costo complessivo dell'istanza on demand inizia a superare il costo complessivo dell'istanza riservata:

![Impatto dell'acquisito](./media/tutorial-optimize-reserved-instances/aws03.png)

Il risparmio inizia in quel preciso momento.

È possibile esaminare le **istanze nel tempo** per verificare l'accuratezza del consiglio per gli acquisiti suggerito. In questo esempio, si può notare che in media sono state usate sei istanze per il carico di lavoro negli ultimi 30 giorni.

![Istanze nel tempo](./media/tutorial-optimize-reserved-instances/aws04.png)

## <a name="modify-unused-reservations"></a>Modificare le prenotazioni inutilizzate

Le prenotazioni inutilizzate sono comuni in molti ambienti di elaborazione di consumer di risorse cloud. Garantire che le prenotazioni inutilizzate vengano usate completamente può consentire un risparmio quando si modificano le prenotazioni per soddisfare le esigenze correnti. Ad esempio, si potrebbe avere una sottoscrizione che contiene istanze D3 standard in esecuzione in Linux. Se non si usa completamente la prenotazione, è possibile modificare il tipo di istanza. In alternativa, è possibile spostare le risorse non utilizzate in una prenotazione diversa o in un account diverso.

AWS vende istanze riservate per aree e zone di disponibilità specifiche. Se sono state acquistate istanze riservate per una zona di disponibilità specifica, è possibile spostare le prenotazioni tra le zone. Tuttavia, è possibile spostare facilmente le istanze riservate regionali tra le zone usando il report **EC2 Currently Unused Reservations** (Prenotazioni attualmente inutilizzate EC2). In alternativa, è possibile modificarle scegliendo un ambito regionale in modo che le istanze corrispondenti vengano applicate in tutte le zone di disponibilità.

Nel menu dei report nella parte superiore del portale fare clic su **Optimizer (Utilità di ottimizzazione)** > **Inefficiencies (Inefficienze)**  > **EC2 Currently Unused Reservations** (Prenotazioni attualmente inutilizzate EC2).

Le immagini seguenti mostrano il report con le istanze riservate inutilizzate.

![Prenotazioni inutilizzate](./media/tutorial-optimize-reserved-instances/unused-ri01.png)

Fare clic sul segno più in **Details** (Dettagli) per visualizzare i dettagli di una prenotazione specifica.

![Dettagli delle prenotazioni inutilizzate](./media/tutorial-optimize-reserved-instances/unused-ri02.png)

Nell'esempio precedente sono presenti 77 prenotazioni inutilizzate totali in varie zone di disponibilità. La prima prenotazione ha 51 istanze inutilizzate. Guardando più in basso nell'elenco, è possibile apportare alcune modifiche all'istanza riservata scegliendo il tipo di istanza **m3.2xlarge** nella zona di disponibilità **us-east-1c**.

Fare clic su **Modify** (Modifica) per la prima prenotazione nell'elenco per aprire la pagina **Modify RI** (Modifica istanza riservata) in cui vengono visualizzati i dati sulla prenotazione.

![Modify RI (Modifica istanza riservata)](./media/tutorial-optimize-reserved-instances/unused-ri03.png)

Vengono elencate le istanze riservate che è possibile modificare. Nella figura dell'esempio seguente esistono 51 prenotazioni inutilizzate che è possibile modificare con 54 istanze necessarie tra le due prenotazioni. Se si modificano le prenotazioni inutilizzate per usarle tutte, quattro istanze continueranno a essere eseguite on demand. Per questo esempio, dividere le prenotazioni inutilizzate in modo che la prima prenotazione userà 30 istanze e la seconda prenotazione ne userà 21.

Fare clic sul segno più per la prima prenotazione e impostare la **quantità di prenotazione** su **30**. Per la seconda voce, impostare la quantità di prenotazione su **21** e quindi fare clic su **Apply** (Applica).

![Modifica della quantità di prenotazione](./media/tutorial-optimize-reserved-instances/unused-ri04.png)

Tutte le istanze inutilizzate per la prenotazione vengono usate appieno e 51 istanze non sono più in esecuzione on demand. In questo esempio l'organizzazione ha avuto un notevole risparmio riducendo l'utilizzo on demand e usando le prenotazioni già pagate.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state eseguite le attività seguenti:

> [!div class="checklist"]
> * Conoscere i costi delle istanze riservate di Azure
> * Ottenere informazioni sui vantaggi delle istanze riservate
> * Ottimizzare i costi delle istanze riservate di Azure
> * Visualizzare i costi delle istanze riservate
> * Valutare l'efficacia dei costi delle istanze riservate di Azure
> * Ottimizzare i costi delle istanze riservate di AWS
> * Acquistare istanze riservate consigliate
> * Modificare le prenotazioni inutilizzate


Passare all'esercitazione successiva per apprendere come controllare l'accesso ai dati.

> [!div class="nextstepaction"]
> [Controllo dell'accesso ai dati](tutorial-user-access.md)
