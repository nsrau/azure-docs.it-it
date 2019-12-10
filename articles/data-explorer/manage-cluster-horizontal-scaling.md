---
title: Gestire la scalabilità orizzontale del cluster (scale out) in Azure Esplora dati per soddisfare la richiesta di modifica
description: Questo articolo descrive i passaggi per la scalabilità orizzontale e la scalabilità in un cluster Esplora dati di Azure in base alla richiesta di modifica.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 52a9c0a13723361bbc93362cdd9e2c73ef0372f2
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74942240"
---
# <a name="manage-cluster-horizontal-scaling-scale-out-in-azure-data-explorer-to-accommodate-changing-demand"></a>Gestire la scalabilità orizzontale del cluster (scale out) in Azure Esplora dati per soddisfare la richiesta di modifica

Ridimensionare un cluster in modo appropriato è fondamentale per garantire le prestazioni di Esplora dati di Azure. La scelta di dimensioni statiche per un cluster può portare al suo sottoutilizzo o sovrautilizzo e nessuna delle due condizioni è ideale. Poiché la richiesta in un cluster non può essere prevista con una precisione assoluta, è preferibile *ridimensionare* un cluster, aggiungendo e rimuovendo la capacità e le risorse della CPU con la richiesta mutevole. 

Sono disponibili due flussi di lavoro per la scalabilità di un cluster Esplora dati di Azure: 
* Scalabilità orizzontale, detta anche scalabilità in uscita e in uscita.
* [Scalabilità verticale](manage-cluster-vertical-scaling.md), definita anche scalabilità verticale e verticale.
Questo articolo illustra il flusso di lavoro di ridimensionamento orizzontale.

## <a name="configure-horizontal-scaling"></a>Configurare la scalabilità orizzontale

Utilizzando il ridimensionamento orizzontale, è possibile ridimensionare automaticamente il numero di istanze in base a regole e pianificazioni predefinite. Per specificare le impostazioni di scalabilità automatica per il cluster:

1. Nella portale di Azure passare alla risorsa cluster di Azure Esplora dati. In **Impostazioni**selezionare **scale out (scalabilità orizzontale**). 

2. Nella finestra **scale out (scalabilità orizzontale** ) selezionare il metodo di ridimensionamento automatico desiderato: **scalabilità manuale**, **scalabilità automatica ottimizzata**o **scalabilità automatica personalizzata**.

### <a name="manual-scale"></a>Scalabilità manuale

La scalabilità manuale è l'impostazione predefinita durante la creazione del cluster. Il cluster ha una capacità statica che non cambia automaticamente. È possibile selezionare la capacità statica usando la barra di **conteggio delle istanze** . Il ridimensionamento del cluster rimane in tale impostazione fino a quando non si effettua un'altra modifica.

   ![Metodo di ridimensionamento manuale](media/manage-cluster-horizontal-scaling/manual-scale-method.png)

### <a name="optimized-autoscale-preview"></a>Scalabilità automatica ottimizzata (anteprima)

La scalabilità automatica ottimizzata è il metodo consigliato per la scalabilità automatica. Questo metodo consente di ottimizzare le prestazioni e i costi del cluster. Se il cluster si avvicina a uno stato di sottoutilizzo, verrà ridimensionato in. Questa azione riduce i costi ma mantiene il livello di prestazioni. Se il cluster si avvicina a uno stato di sovrautilizzo, viene scalato orizzontalmente per garantire prestazioni ottimali. Per configurare la scalabilità automatica ottimizzata:

1. Selezionare **scalabilità automatica ottimizzata**. 

1. Selezionare un numero minimo di istanze e un numero massimo di istanze. Gli intervalli di scalabilità automatica del cluster tra questi due numeri, in base al carico.

1. Selezionare **Salva**.

   ![Metodo di ridimensionamento automatico ottimizzato](media/manage-cluster-horizontal-scaling/optimized-autoscale-method.png)

La scalabilità automatica ottimizzata inizia a funzionare. Le azioni sono ora visibili nel log attività di Azure del cluster.

#### <a name="logic-of-optimized-autoscale"></a>Logica di scalabilità automatica ottimizzata 

**Aumentare il numero di istanze**

Quando il cluster si avvicina a uno stato di sovrautilizzo, applicare la scalabilità orizzontale per garantire prestazioni ottimali. La scalabilità orizzontale viene eseguita nei casi seguenti:
* Il numero di istanze del cluster è inferiore al numero massimo di istanze definite dall'utente.
* L'utilizzo della cache è elevato per più di un'ora.

> [!NOTE]
> La logica di scalabilità orizzontale non considera attualmente le metriche di utilizzo dell'inserimento e della CPU. Se queste metriche sono importanti per il caso d'uso, usare la [scalabilità automatica personalizzata](#custom-autoscale).

**Ridimensiona in**

Quando il cluster si avvicina a uno stato di sottoutilizzo, applicare la scalabilità a costi ridotti ma mantenere le prestazioni. Vengono usate più metriche per verificare la scalabilità sicura del cluster. Le regole seguenti vengono valutate giornalmente per 7 giorni prima che venga eseguita la scalabilità in:
* Il numero di istanze è superiore a 2 e superiore al numero minimo di istanze definite.
* Per assicurarsi che non vi sia alcun sovraccarico delle risorse, è necessario verificare le metriche seguenti prima di eseguire la scalabilità in: 
    * L'utilizzo della cache non è elevato
    * La CPU è al di sotto della media 
    * L'utilizzo dell'inserimento è inferiore alla media 
    * L'utilizzo del flusso di inserimento (se si usa l'inserimento di flussi) non è elevato
    * Gli eventi Keep Alive sono superiori a un valore minimo definito, elaborati correttamente e in tempo.
    * Nessuna limitazione query 
    * Il numero di query non riuscite è inferiore a un valore minimo definito.

> [!NOTE]
> La scala nella logica richiede attualmente una valutazione di 7 giorni prima dell'implementazione della scala ottimizzata in. Questa valutazione viene eseguita ogni 24 ore. Se è necessaria una modifica rapida, usare la [scalabilità manuale](#manual-scale).

### <a name="custom-autoscale"></a>Scalabilità automatica personalizzata

Con la scalabilità automatica personalizzata è possibile ridimensionare il cluster in modo dinamico in base alle metriche specificate. Il grafico seguente illustra il flusso e i passaggi per configurare la scalabilità automatica personalizzata. Altre informazioni seguono il grafico.

1. Nella casella **Nome impostazione di scalabilità** automatica immettere un nome, ad esempio *scale-out: utilizzo cache*. 

   ![Regola di ridimensionamento](media/manage-cluster-horizontal-scaling/custom-autoscale-method.png)

2. Per la **modalità scala**, selezionare **Ridimensiona in base a una metrica**. Questa modalità consente la scalabilità dinamica. È anche possibile selezionare **la scalabilità a un numero di istanze specifico**.

3. Selezionare **+ Aggiungi una regola**.

4. Nella sezione **regola di scalabilità** a destra, immettere i valori per ogni impostazione.

    **Criteri**

    | Impostazione | Descrizione e valore |
    | --- | --- |
    | **Aggregazione temporale** | Selezionare un criterio di aggregazione, ad esempio **Media**. |
    | **Nome metrica** | Selezionare la metrica su cui si vuole basare l'operazione di ridimensionamento, ad esempio **Utilizzo della cache**. |
    | **Statistica intervallo di tempo** | Scegliere tra **Medio**, **Minimo**, **Massimo** e **Somma**. |
    | **Operatore** | Scegliere l'opzione appropriata, ad esempio **Maggiore o uguale a**. |
    | **Soglia** | Scegliere un valore appropriato. Ad esempio, per l'utilizzo della cache, il 80% è un punto di partenza valido. |
    | **Durata (in minuti)** | Scegliere un periodo di tempo appropriato da considerare per il calcolo delle metriche. Iniziare con il valore predefinito di 10 minuti. |
    |  |  |

    **Azione**

    | Impostazione | Descrizione e valore |
    | --- | --- |
    | **Operazione** | Scegliere l'opzione appropriata per ridurre o aumentare il numero di istanze. |
    | **Numero di istanze** | Scegliere il numero di nodi o istanze da aggiungere o rimuovere quando viene soddisfatta una condizione di metrica. |
    | **Disattiva regole dopo (minuti)** | Scegliere un intervallo di tempo appropriato per l'attesa tra le operazioni di ridimensionamento. Iniziare con il valore predefinito di cinque minuti. |
    |  |  |

5. Selezionare **Aggiungi**.

6. Nella sezione **limiti dell'istanza** a sinistra immettere i valori per ogni impostazione.

    | Impostazione | Descrizione e valore |
    | --- | --- |
    | **Minimi** | Numero di istanze al di sotto del quale non verrà effettuato il ridimensionamento del cluster, indipendentemente dall'utilizzo. |
    | **Massimo** | Numero di istanze al di sopra del quale non verrà effettuato il ridimensionamento del cluster, indipendentemente dall'utilizzo. |
    | **Default** | Numero predefinito di istanze di. Questa impostazione viene usata in caso di problemi con la lettura delle metriche delle risorse. |
    |  |  |

7. Selezionare **Salva**.

A questo punto è stata configurata la scalabilità orizzontale per il cluster Esplora dati di Azure. Aggiungere un'altra regola per la scalabilità verticale. Se è necessaria assistenza per i problemi di scalabilità del cluster, [aprire una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) nella portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

* [Monitora le prestazioni, l'integrità e l'utilizzo di Azure Esplora dati con le metriche](using-metrics.md)
* [Gestire la scalabilità verticale del cluster](manage-cluster-vertical-scaling.md) per il dimensionamento appropriato di un cluster.
