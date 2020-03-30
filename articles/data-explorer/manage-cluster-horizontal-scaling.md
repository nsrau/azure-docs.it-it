---
title: Gestire la scalabilità orizzontale del cluster (scalabilità orizzontale) per soddisfare la domanda in Azure Data ExplorerManage cluster horizontal scaling (scale out) to match demand in Azure Data Explorer
description: Questo articolo descrive i passaggi per la scalabilità orizzontale e la scalabilità orizzontale in un cluster di Azure Data Explorer in base alla modifica della domanda.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: ff7420619cffc2287ab8ff6332df605d56329549
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664134"
---
# <a name="manage-cluster-horizontal-scaling-scale-out-in-azure-data-explorer-to-accommodate-changing-demand"></a>Gestire la scalabilità orizzontale del cluster (scalabilità orizzontale) in Azure Data Explorer per soddisfare le mutevoli richiesteManage cluster horizontal scaling (scale out) in Azure Data Explorer to accommodate changing demand

Ridimensionare un cluster in modo appropriato è fondamentale per garantire le prestazioni di Esplora dati di Azure. La scelta di dimensioni statiche per un cluster può portare al suo sottoutilizzo o sovrautilizzo e nessuna delle due condizioni è ideale. Poiché la domanda in un cluster non può essere stimata con assoluta precisione, è preferibile *ridimensionare* un cluster, aggiungendo e rimuovendo risorse di capacità e CPU con la richiesta in continua evoluzione. 

Esistono due flussi di lavoro per la scalabilità di un cluster di Azure Data Explorer:There are two workflows for scaling an Azure Data Explorer cluster: 
* Ridimensionamento orizzontale, denominato anche ridimensionamento in orizzontale e in uscita.
* [Ridimensionamento verticale,](manage-cluster-vertical-scaling.md)denominato anche ridimensionamento verso l'alto e verso il basso.
Questo articolo illustra il flusso di lavoro di scalabilità orizzontale.

## <a name="configure-horizontal-scaling"></a>Configurare il ridimensionamento orizzontale

Utilizzando il ridimensionamento orizzontale, è possibile ridimensionare automaticamente il numero di istanze in base a regole e pianificazioni predefinite. Per specificare le impostazioni di scalabilità automatica per il cluster:

1. Nel portale di Azure passare alla risorsa cluster di Azure Data Explorer.In the Azure portal, go to your Azure Data Explorer cluster resource. In **Impostazioni**selezionare **Scalabilità orizzontale**. 

2. Nella finestra **Scalabilità orizzontale** selezionare il metodo di scalabilità automatica desiderato: **Scala manuale**, **Scala automatica ottimizzata**o **Scalabilità automatica personalizzata**.

### <a name="manual-scale"></a>Scalabilità manuale

La scalabilità manuale è l'impostazione predefinita durante la creazione del cluster. Il cluster ha una capacità statica che non cambia automaticamente. Selezionare la capacità statica utilizzando la barra **Conteggio istanze** . La scalabilità del cluster rimane in tale impostazione fino a quando non si apporta un'altra modifica.

   ![Metodo di scala manuale](media/manage-cluster-horizontal-scaling/manual-scale-method.png)

### <a name="optimized-autoscale-preview"></a>Scalabilità automatica ottimizzata (anteprima)Optimized autoscale (preview)

La scalabilità automatica ottimizzata è il metodo di scalabilità automatica consigliato. Questo metodo consente di ottimizzare le prestazioni e i costi del cluster. Se il cluster si avvicina a uno stato di sottoutilizzo, verrà ridimensionato. Questa azione riduce i costi ma mantiene il livello di prestazioni. Se il cluster si avvicina a uno stato di sovrautilizzo, verrà scalato orizzontalmente per mantenere prestazioni ottimali. Per configurare la scalabilità automatica ottimizzata:

1. Selezionare **Scalabilità automatica ottimizzata**. 

1. Selezionare un numero minimo di istanze e un numero massimo di istanze. Il ridimensionamento automatico del cluster varia tra questi due numeri, in base al carico.

1. Selezionare **Salva**.

   ![Metodo di scalabilità automatica ottimizzato](media/manage-cluster-horizontal-scaling/optimized-autoscale-method.png)

La scalabilità automatica ottimizzata inizia a funzionare. Le azioni sono ora visibili nel log attività di Azure del cluster.

#### <a name="logic-of-optimized-autoscale"></a>Logica di scalabilità automatica ottimizzata 

**Scalabilità orizzontale**

Quando il cluster si avvicina a uno stato di sovrautilizzo, scalabilità orizzontale per mantenere prestazioni ottimali. La scalabilità orizzontale si verifica quando:Scale out will occur when:
* Il numero di istanze del cluster è inferiore al numero massimo di istanze definite dall'utente.
* L'utilizzo della cache è elevato per oltre un'ora.
* La CPU è alta per oltre un'ora.
* L'utilizzo dell'inserimento è elevato per oltre un'ora.

> [!NOTE]
> La logica di scalabilità orizzontale non considera attualmente la metrica di utilizzo dell'inserimento. Se questa metrica è importante per il caso d'uso, usare [la scalabilità automatica personalizzata.](#custom-autoscale)

**Ridurre il numero di istanze**

Quando il cluster si avvicina a uno stato di sottoutilizzo, scalare in modo da ridurre i costi ma mantenere le prestazioni. Vengono usate più metriche per verificare che la scalabilità nel cluster sia sicura. Le regole seguenti vengono valutate ogni ora per 6 ore prima dell'esecuzione della scalabilità verticale:
* Il numero di istanze è superiore a 2 e superiore al numero minimo di istanze definito.
* Per assicurarsi che non vi sia alcun sovraccarico delle risorse, è necessario verificare le metriche seguenti prima dell'esecuzione della scalabilità verticale:To ensure that there's no overloading of resources, the following metrics must be verified before scale-in is performed: 
    * L'utilizzo della cache non è elevato
    * La CPU è inferiore alla media 
    * L'utilizzo dell'ingestione è inferiore alla media 
    * L'utilizzo dell'inserimento in streaming (se viene utilizzato l'inserimento in streaming) non è elevato
    * Gli eventi Keep Live superano un minimo definito, vengono elaborati correttamente e in tempo.
    * Nessuna limitazione delle query 
    * Il numero di query non riuscite è inferiore a un minimo definito.

> [!NOTE]
> La scalabilità nella logica richiede attualmente una valutazione di 7 giorni prima dell'implementazione della scalabilità ottimizzata. Questa valutazione avviene una volta ogni 24 ore. Se è necessaria una modifica rapida, utilizzare [la scala manuale](#manual-scale).

### <a name="custom-autoscale"></a>Scalabilità automatica personalizzata

Usando la scalabilità automatica personalizzata, è possibile ridimensionare il cluster in modo dinamico in base alle metriche specificate. Nella figura seguente vengono illustrati il flusso e i passaggi per configurare la scalabilità automatica personalizzata. Maggiori dettagli seguono l'immagine.

1. Nella casella **Nome impostazione scalabilità automatica** immettere un nome, ad esempio *Scalabilità orizzontale: utilizzo della cache*. 

   ![Regola di ridimensionamento](media/manage-cluster-horizontal-scaling/custom-autoscale-method.png)

2. Per **Modalità scala**, selezionare Scala in base a **una metrica**. Questa modalità fornisce il ridimensionamento dinamico. È inoltre possibile selezionare Scala a un numero di **istanze specifico.**

3. Selezionare **: Aggiungi una regola**.

4. Nella sezione **Regola di scala** a destra immettere i valori per ogni impostazione.

    **Criteri**

    | Impostazione | Descrizione e valore |
    | --- | --- |
    | **Aggregazione temporale** | Selezionare un criterio di aggregazione, ad esempio **Media**. |
    | **Nome metrica** | Selezionare la metrica su cui si vuole basare l'operazione di ridimensionamento, ad esempio **Utilizzo della cache**. |
    | **Statistica intervallo di tempo** | Scegliere tra **Medio**, **Minimo**, **Massimo** e **Somma**. |
    | **Operatore** | Scegliere l'opzione appropriata, ad esempio **Maggiore o uguale a**. |
    | **Soglia** | Scegliere un valore appropriato. Ad esempio, per l'utilizzo della cache, l'80% è un buon punto di partenza. |
    | **Durata (in minuti)** | Scegliere un periodo di tempo appropriato da considerare per il calcolo delle metriche. Iniziare con il valore predefinito di 10 minuti. |
    |  |  |

    **Azione**

    | Impostazione | Descrizione e valore |
    | --- | --- |
    | **Operazione** | Scegliere l'opzione appropriata per ridurre o aumentare il numero di istanze. |
    | **Conteggio istanze** | Scegliere il numero di nodi o istanze da aggiungere o rimuovere quando viene soddisfatta una condizione di metrica. |
    | **Raffreddare (minuti)** | Scegliere un intervallo di tempo appropriato per l'attesa tra le operazioni di ridimensionamento. Iniziare con il valore predefinito di cinque minuti. |
    |  |  |

5. Selezionare **Aggiungi**.

6. Nella sezione **Limiti istanza** a sinistra immettere i valori per ogni impostazione.

    | Impostazione | Descrizione e valore |
    | --- | --- |
    | **Minimi** | Numero di istanze al di sotto del quale non verrà effettuato il ridimensionamento del cluster, indipendentemente dall'utilizzo. |
    | **Massimo** | Numero di istanze al di sopra del quale non verrà effettuato il ridimensionamento del cluster, indipendentemente dall'utilizzo. |
    | **Predefinito** | Numero predefinito di istanze. Questa impostazione viene utilizzata in caso di problemi con la lettura delle metriche delle risorse. |
    |  |  |

7. Selezionare **Salva**.

È stato configurato la scalabilità orizzontale per il cluster Azure Data Explorer.You've now configured horizontal scaling for your Azure Data Explorer cluster. Aggiungere un'altra regola per il ridimensionamento verticale. Per assistenza sui problemi di scalabilità del cluster, aprire una richiesta di supporto nel portale di Azure.If you need assistance with cluster-scaling issues, [open a support request](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) in the Azure portal.

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare le prestazioni, l'integrità e l'utilizzo di Azure Data Explorer con le metricheMonitor Azure Data Explorer performance, health, and usage with metrics](using-metrics.md)
* [Gestire la scalabilità verticale del cluster](manage-cluster-vertical-scaling.md) per il ridimensionamento appropriato di un cluster.
