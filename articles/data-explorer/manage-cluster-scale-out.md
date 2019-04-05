---
title: Scalare orizzontalmente un cluster di Esplora dati di Azure per soddisfare la richiesta di modifica
description: Questo articolo descrive i passaggi per scalare in orizzontale e scalabilità in un cluster di Azure Data Explorer base alle variazioni della domanda.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: ab4ced6695e6066098dd5ff7348528deedfc0e1b
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044235"
---
# <a name="manage-cluster-scale-out-to-accommodate-changing-demand"></a>Gestire l'aumento del numero di istanze di un cluster per rispondere al cambiamento della domanda

Ridimensionare un cluster in modo appropriato è fondamentale per garantire le prestazioni di Esplora dati di Azure. Ma la domanda in un cluster non è possibile prevedere con accuratezza assoluto. Una dimensione del cluster statico può causare sottoutilizzo o di Sovrautilizzo, nessuno dei quali è ideale.

Un approccio migliore consiste nel *ridimensionare* il cluster, aumentandone o diminuendone la capacità in base al cambiamento della domanda. Esistono due flussi di lavoro per la scalabilità: scalabilità verticale e orizzontale. Questo articolo illustra il flusso di lavoro per l'aumento del numero di istanze.

Questo articolo illustra come gestire cluster di scalabilità orizzontale, detta anche scalabilità automatica. La scalabilità automatica consente di scalare orizzontalmente il numero di istanze automaticamente in base alle regole e pianificazioni predefinite. Specificare le impostazioni di scalabilità automatica per il cluster nel portale di Azure, come descritto in questo articolo.

Passare al cluster. Sotto **le impostazioni**, selezionare **scalare in orizzontale**. In **Configura** selezionare **Abilita scalabilità automatica**.

![Abilitare il ridimensionamento automatico](media/manage-cluster-scaling/enable-autoscale.png)

La figura seguente mostra il flusso dei passaggi successivi. Altri dettagli sono sotto il grafico.

![Regola di ridimensionamento](media/manage-cluster-scaling/scale-rule.png)

1. Nel **nome dell'impostazione di scalabilità automatica** immettere un nome, ad esempio *Scale-out: utilizzo di cache*.

1. Per la **modalità di ridimensionamento**, selezionare **scalabilità basata su una metrica**. Questa modalità assicura la scalabilità dinamica. È anche possibile selezionare **scala a un numero di istanze specifico**.

1. Selezionare **+ Aggiungi una regola**.

1. Nella sezione **Regola di ridimensionamento** sulla destra specificare i valori per ogni impostazione.

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
    | **Operazione** | Scegliere l'opzione appropriata per ridurre o aumentare il numero di istanze. |
    | **Numero di istanze** | Scegliere il numero di nodi o istanze da aggiungere o rimuovere quando viene soddisfatta una condizione di metrica. |
    | **Disattiva regole dopo (minuti)** | Scegliere un intervallo di tempo appropriato per l'attesa tra le operazioni di ridimensionamento. Iniziare con il valore predefinito di cinque minuti. |
    |  |  |

1. Selezionare **Aggiungi**.

1. Nella sezione **Limiti per le istanze** a sinistra specificare i valori per ogni impostazione.

    | Impostazione | Descrizione e valore |
    | --- | --- |
    | **Minima** | Numero di istanze al di sotto del quale non verrà effettuato il ridimensionamento del cluster, indipendentemente dall'utilizzo. |
    | **Massima** | Numero di istanze al di sopra del quale non verrà effettuato il ridimensionamento del cluster, indipendentemente dall'utilizzo. |
    | **Predefinito** | Il numero predefinito di istanze. Questa impostazione viene usata se si verificano problemi con la lettura delle metriche delle risorse. |
    |  |  |

1. Selezionare **Salva**.

È stata configurata un'operazione per aumentare il numero di istanze del cluster di Esplora dati di Azure. Aggiungere un'altra regola per configurare un'operazione per ridurne il numero. Questa configurazione consente il cluster per la scalabilità dinamicamente in base alle metriche che preferisci.

È anche possibile [gestire i cluster aumento](manage-cluster-scale-up.md) per il ridimensionamento appropriato di un cluster.

Se occorre assistenza per problemi di ridimensionamento del cluster [aprire una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) nel portale di Azure.
