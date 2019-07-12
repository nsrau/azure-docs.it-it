---
title: Scalare orizzontalmente un cluster di Esplora dati di Azure
description: Questo articolo descrive i passaggi per scalare in orizzontale e scalabilità in un cluster di Azure Data Explorer base alle variazioni della domanda.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 29bfcc42462a667850f0b2e1bbda3d29cd1597ab
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571517"
---
# <a name="manage-cluster-horizontal-scaling-to-accommodate-changing-demand"></a>Gestire cluster la scalabilità orizzontale per soddisfare la richiesta di modifica

Ridimensionare un cluster in modo appropriato è fondamentale per garantire le prestazioni di Esplora dati di Azure. Ma la domanda in un cluster non è possibile prevedere con accuratezza assoluto. Una dimensione del cluster statico può causare sottoutilizzo o di Sovrautilizzo, nessuno dei quali è ideale.

Un approccio migliore consiste nel *ridimensionare* il cluster, aumentandone o diminuendone la capacità in base al cambiamento della domanda. Esistono due flussi di lavoro per la scalabilità: 
* Scalabilità orizzontale, detta anche scalabilità orizzontale.
* Scalabilità verticale, detta anche scalabilità verticale.

Questo articolo illustra il flusso di lavoro di scalabilità orizzontale.

Scalabilità orizzontale consente di aumentare il numero di istanze automaticamente in base alle regole e pianificazioni predefinite. Specificare le impostazioni di scalabilità automatica per il cluster nel portale di Azure, come descritto in questo articolo.

## <a name="steps-to-configure-horizontal-scaling"></a>Passaggi per configurare la scalabilità orizzontale

Nel portale di Azure, passare alla risorsa cluster di Esplora dati. Sotto il **le impostazioni** titolo, selezionare **scalare in orizzontale**. 

Selezionare il metodo di ridimensionamento automatico desiderato: **Scalabilità manuale**, **ottimizzato per la scalabilità automatica** oppure **scalabilità automatica personalizzati**.

### <a name="manual-scale"></a>Scalabilità manuale

Scalabilità manuale è l'impostazione predefinita con la creazione del cluster. Indica che il cluster hanno una capacità di cluster statici che non verrà modificato automaticamente. È possibile scegliere la capacità statica usando la barra e non cambia finché non successivo si modificherà impostazione di aumento del cluster.

   ![Metodo scalabilità manuale](media/manage-cluster-horizontal-scaling/manual-scale-method.png)

### <a name="optimized-autoscale"></a>Con ottimizzazione per la scalabilità automatica

Con ottimizzazione per la scalabilità automatica è il metodo consigliato di scalabilità automatica. Passaggi per configurare la scalabilità automatica ottimizzato:

1. Selezionata con ottimizzazione per la scalabilità automatica opzione e scegliere un limite inferiore e un limite superiore alla quantità di istanze del cluster, la scalabilità automatica verrà eseguita tra tali limiti.
2. Fare clic su Salva.

   ![Con ottimizzazione per la scalabilità automatica (metodo)](media/manage-cluster-horizontal-scaling/optimized-autoscale-method.png)

Dopo aver facendo clic su Salva ottimizzato meccanismo di scalabilità automatica inizierà a funzionare ed è azioni saranno visibili nel log attività del cluster. Questo metodo di scalabilità automatica è ottimizzare le prestazioni del cluster e i costi: se è possibile avviare il cluster ottenere uno stato di sottoutilizzo verrà essere ridimensionati in cui lasciare le prestazioni della stessi e inferiore dei costi e se è possibile avviare il cluster ottenere uno stato di Sovrautilizzo, sarà con scalabilità orizzontale per assicurarsi che stia funzionando correttamente

### <a name="custom-autoscale"></a>Scalabilità automatica personalizzata

Metodo di scalabilità automatica personalizzati consente di ridimensionare il cluster dinamicamente in base alle metriche che preferisci. La figura seguente illustra il flusso e passaggi per configurare la scalabilità automatica personalizzati. Altri dettagli seguono nel grafico.

1. Nel **nome dell'impostazione di scalabilità automatica** immettere un nome, ad esempio *Scale-out: utilizzo di cache*. 

   ![Regola di ridimensionamento](media/manage-cluster-horizontal-scaling/custom-autoscale-method.png)

2. Per la **modalità di ridimensionamento**, selezionare **scalabilità basata su una metrica**. Questa modalità assicura la scalabilità dinamica. È anche possibile selezionare **scala a un numero di istanze specifico**.

3. Selezionare **+ Aggiungi una regola**.

4. Nella sezione **Regola di ridimensionamento** sulla destra specificare i valori per ogni impostazione.

    **Criteri**

    | Impostazione | Descrizione e valore |
    | --- | --- |
    | **Aggregazione temporale** | Selezionare un criterio di aggregazione, ad esempio **Media**. |
    | **Nome metrica** | Selezionare la metrica su cui si vuole basare l'operazione di ridimensionamento, ad esempio **Utilizzo della cache**. |
    | **Statistica intervallo di tempo** | Scegliere tra **Medio**, **Minimo**, **Massimo** e **Somma**. |
    | **Operatore** | Scegliere l'opzione appropriata, ad esempio **Maggiore o uguale a**. |
    | **Soglia** | Scegliere un valore appropriato. Ad esempio, per l'uso della cache, pari all'80% è un buon punto di partenza. |
    | **Durata (in minuti)** | Scegliere un periodo di tempo appropriato da considerare per il calcolo delle metriche. Iniziare con il valore predefinito di 10 minuti. |
    |  |  |

    **Azione**

    | Impostazione | Descrizione e valore |
    | --- | --- |
    | **operazione** | Scegliere l'opzione appropriata per ridurre o aumentare il numero di istanze. |
    | **Numero di istanze** | Scegliere il numero di nodi o istanze da aggiungere o rimuovere quando viene soddisfatta una condizione di metrica. |
    | **Disattiva regole dopo (minuti)** | Scegliere un intervallo di tempo appropriato per l'attesa tra le operazioni di ridimensionamento. Iniziare con il valore predefinito di cinque minuti. |
    |  |  |

5. Selezionare **Aggiungi**.

6. Nella sezione **Limiti per le istanze** a sinistra specificare i valori per ogni impostazione.

    | Impostazione | Descrizione e valore |
    | --- | --- |
    | **Minimi** | Numero di istanze al di sotto del quale non verrà effettuato il ridimensionamento del cluster, indipendentemente dall'utilizzo. |
    | **Massimo** | Numero di istanze al di sopra del quale non verrà effettuato il ridimensionamento del cluster, indipendentemente dall'utilizzo. |
    | **Default** | Il numero predefinito di istanze. Questa impostazione viene usata se si verificano problemi con la lettura delle metriche delle risorse. |
    |  |  |

7. Selezionare **Salva**.

È stata configurata un'operazione per aumentare il numero di istanze del cluster di Esplora dati di Azure. Aggiungere un'altra regola per configurare un'operazione per ridurne il numero. Se occorre assistenza per problemi di ridimensionamento del cluster [aprire una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) nel portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare le prestazioni, integrità e sull'utilizzo con le metriche di Esplora dati di Azure](using-metrics.md)
* [Gestire il ridimensionamento verticale cluster](manage-cluster-vertical-scaling.md) per il ridimensionamento appropriato di un cluster.
