---
title: Scalare orizzontalmente un cluster di Esplora dati di Azure
description: Questo articolo descrive i passaggi per scalare in orizzontale e scalabilità in un cluster di Azure Data Explorer base alle variazioni della domanda.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: 24bbddd28943adc929fbaea456eeae8165db290c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60758665"
---
# <a name="manage-cluster-scale-out-to-accommodate-changing-demand"></a>Gestire l'aumento del numero di istanze di un cluster per rispondere al cambiamento della domanda

Ridimensionare un cluster in modo appropriato è fondamentale per garantire le prestazioni di Esplora dati di Azure. Ma la domanda in un cluster non è possibile prevedere con accuratezza assoluto. Una dimensione del cluster statico può causare sottoutilizzo o di Sovrautilizzo, nessuno dei quali è ideale.

Un approccio migliore consiste nel *ridimensionare* il cluster, aumentandone o diminuendone la capacità in base al cambiamento della domanda. Esistono due flussi di lavoro per la scalabilità: scalabilità verticale e orizzontale. Questo articolo illustra il flusso di lavoro per l'aumento del numero di istanze.

Questo articolo illustra come gestire cluster di scalabilità orizzontale, detta anche scalabilità automatica. La scalabilità automatica consente di scalare orizzontalmente il numero di istanze automaticamente in base alle regole e pianificazioni predefinite. Specificare le impostazioni di scalabilità automatica per il cluster nel portale di Azure, come descritto in questo articolo.

## <a name="steps-to-configure-autoscale"></a>Passaggi per configurare la scalabilità automatica

Nel portale di Azure, passare alla risorsa cluster di Esplora dati. Sotto il **le impostazioni** titolo, selezionare **scalare in orizzontale**. Nel **Configure** scheda, seleziona **Abilita scalabilità automatica**.

   ![Abilitare il ridimensionamento automatico](media/manage-cluster-scaling/enable-autoscale.png)

La figura seguente mostra il flusso dei passaggi successivi. Altri dettagli seguono nel grafico.

1. Nel **nome dell'impostazione di scalabilità automatica** immettere un nome, ad esempio *Scale-out: utilizzo di cache*. 

   ![Regola di ridimensionamento](media/manage-cluster-scaling/scale-rule.png)

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

È stata configurata un'operazione per aumentare il numero di istanze del cluster di Esplora dati di Azure. Aggiungere un'altra regola per configurare un'operazione per ridurne il numero. Questa configurazione consente il cluster per la scalabilità dinamicamente in base alle metriche che preferisci.

È anche possibile [gestire i cluster aumento](manage-cluster-scale-up.md) per il ridimensionamento appropriato di un cluster.

Se occorre assistenza per problemi di ridimensionamento del cluster [aprire una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) nel portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

[Monitorare le prestazioni, integrità e sull'utilizzo con le metriche di Esplora dati di Azure](using-metrics.md)
