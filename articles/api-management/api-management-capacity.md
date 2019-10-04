---
title: Capacità di un'istanza di Gestione API di Azure | Microsoft Docs
description: Questo articolo spiega che cosa sono le metriche di capacità e come prendere decisioni informate sull'opportunità di ridimensionare un'istanza di Gestione API di Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: apimpm
ms.custom: fasttrack-edit
ms.openlocfilehash: a585ab059319b15be1f2a86bf10b7dc58da72494
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299454"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Capacità di un'istanza di Gestione API di Azure

La **capacità** è la [metrica di monitoraggio di Azure](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) più importante per prendere decisioni informate se ridimensionare un'istanza di gestione API per gestire un carico maggiore. La costruzione è complessa e impone determinati comportamenti.

Questo articolo descrive che cos'è la **capacità** e il relativo comportamento. Illustra come accedere alle metriche di **capacità** nel portale di Azure e suggerisce quando prendere in considerazione il ridimensionamento o l'aggiornamento dell'istanza di Gestione API.

> [!IMPORTANT]
> Questo articolo illustra come è possibile monitorare e ridimensionare l'istanza di gestione API di Azure in base alla metrica della capacità. Tuttavia, è ugualmente importante capire cosa accade quando una singola istanza di gestione API ha effettivamente *raggiunto* la capacità. Gestione API di Azure non applica alcuna limitazione a livello di servizio per impedire un sovraccarico fisico delle istanze. Quando un'istanza raggiunge la capacità fisica, si comporterà in modo analogo a qualsiasi server Web di overload che non è in grado di elaborare le richieste in ingresso: la latenza aumenterà, le connessioni verranno eliminate, si verificheranno errori di timeout e così via. Ciò significa che i client API devono essere preparati a gestire questa possibilità simile a qualsiasi altro servizio esterno, ad esempio applicando criteri di ripetizione dei tentativi.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire i passaggi in questo articolo è necessario quanto segue:

+ Una sottoscrizione di Azure attiva.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Un'istanza di Gestione API. Per altre informazioni, vedere [Create an Azure API Management instance](get-started-create-service-instance.md) (Creare un'istanza di Gestione API di Azure).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>Che cos'è la capacità

![Metriche di capacità](./media/api-management-capacity/capacity-ingredients.png)

La **capacità** è un indicatore del carico su un'istanza di gestione API. che indica l'utilizzo delle risorse (CPU, memoria) e le lunghezze delle code di rete. L'utilizzo di CPU e memoria indicano l'utilizzo delle risorse per:

+ Servizi del piano dati di gestione API, ad esempio l'elaborazione della richiesta, che può includere richieste di invio o l'esecuzione di un criterio.
+ Servizi del piano di gestione gestione API, ad esempio le azioni di gestione applicate tramite il portale di Azure o ARM, oppure il carico proveniente dal [portale per sviluppatori](api-management-howto-developer-portal.md).
+ Processi del sistema operativo selezionati, inclusi i processi che comportano il costo di handshake SSL sulle nuove connessioni.

La **capacità** totale è una media dei valori per ogni unità di un'istanza di Gestione API.

Sebbene la **metrica della capacità** sia progettata per la superficie di problemi con l'istanza di gestione API, esistono casi in cui i problemi non si riflettono nelle modifiche apportate alla **metrica della capacità**.

## <a name="capacity-metric-behavior"></a>Comportamento delle metriche di capacità

A causa di come è costruita, nel mondo reale sono molte le variabili che possono influire sulla **capacità**, ad esempio:

+ Modelli di connessione (nuova connessione su richiesta o riutilizzo della connessione esistente)
+ Dimensioni di richiesta e risposta
+ Criteri configurati per ogni API o numero di client che inviano richieste.

A una maggiore complessità delle operazioni sulle richieste corrisponde un utilizzo maggiore della **capacità**. Ad esempio, i criteri di trasformazione complessi usano molta più CPU rispetto a un semplice inoltro di richieste. La capacità aumenterà anche in presenza di risposte lente dal servizio back-end.

> [!IMPORTANT]
> La **capacità** non è una misurazione diretta del numero di richieste elaborate.

![Picchi delle metriche di capacità](./media/api-management-capacity/capacity-spikes.png)

I picchi di **capacità** possono anche verificarsi a intermittenza o essere maggiori di zero, anche in assenza di richieste in fase di elaborazione. Ciò si verifica a causa di azioni specifiche del sistema o della piattaforma e non devono essere presi in considerazione quando si decide se ridimensionare un'istanza.

La **metrica a capacità** ridotta non significa necessariamente che l'istanza di gestione API non stia riscontrando alcun problema.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Usare il portale di Azure per esaminare la capacità
  
![Metriche di capacità](./media/api-management-capacity/capacity-metric.png)  

1. Nel [portale di Azure](https://portal.azure.com/) passare all'istanza di Gestione API.
2. Selezionare **Metriche**.
3. Dalla sezione viola selezionare la metrica**Capacità** tra le metriche disponibili e lasciare l'aggregazione predefinita **Media**.

    > [!TIP]
    > Per evitare interpretazioni errate, è sempre consigliabile esaminare una suddivisione delle metriche di **capacità** in base alla località.

4. Dalla sezione verde selezionare **Località** per la suddivisione delle metriche in base alla dimensione.
5. Selezionare un intervallo di tempo desiderato dalla barra superiore della sezione.

    È anche possibile impostare un avviso collegato alle metriche che si attivi se si verifica qualcosa di imprevisto, Ottenere, ad esempio, notifiche quando l'istanza di gestione API supera la capacità di picco prevista per più di 20 minuti.

    >[!TIP]
    > È possibile configurare avvisi per ricevere una segnalazione quando il servizio sta per esaurire la capacità oppure usare la funzionalità di ridimensionamento automatico di Monitoraggio di Azure per aggiungere automaticamente un'unità Gestione API di Azure. L'operazione di ridimensionamento può richiedere circa 30 minuti, pertanto è consigliabile pianificare le regole di conseguenza.  
    > È consentito solo il ridimensionamento della località master.

## <a name="use-capacity-for-scaling-decisions"></a>Usare la capacità per prendere decisioni per la scalabilità

La **capacità** è la metrica per prendere decisioni sull'opportunità di ridimensionare un'istanza di Gestione API per gestire un carico maggiore. Prendere in considerazione:

+ L'individuazione di tendenze e medie a lungo termine.
+ La possibilità di ignorare i picchi più probabilmente non correlati ad aumenti del carico (vedere la sezione "Comportamento delle metriche di capacità" per una spiegazione).
+ L'aggiornamento o il ridimensionamento dell'istanza, quando il valore di **capacità** supera il 60% o 70% per un periodo di tempo prolungato, ad esempio 30 minuti. Valori diversi possono essere più adatti a servizi o scenari specifici.

>[!TIP]  
> Se si è in grado di stimare il traffico in anticipo, testare l'istanza di Gestione API sui carichi di lavoro previsti. È possibile aumentare il carico delle richieste nel tenant gradualmente e monitorare il valore delle metriche di capacità corrispondenti al carico di picco. Seguire i passaggi della sezione precedente per usare il portale di Azure per ottenere maggiori informazioni sulla quantità di capacità usata in qualsiasi momento.

## <a name="next-steps"></a>Passaggi successivi

[Aggiornare e ridimensionare un'istanza di Gestione API](upgrade-and-scale.md)