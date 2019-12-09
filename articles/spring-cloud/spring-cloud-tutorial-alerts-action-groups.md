---
title: 'Esercitazione: Monitorare le risorse di Azure Spring Cloud tramite avvisi e gruppi di azioni | Microsoft Docs'
description: Informazioni su come usare gli avvisi di Spring cloud.
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/18/2019
ms.openlocfilehash: 2be21b20c394ae8505ad18f2c411db7aab06215f
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689566"
---
# <a name="tutorial-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>Esercitazione: Monitorare le risorse di Spring Cloud tramite avvisi e gruppi di azioni

Gli avvisi di Azure Spring Cloud supportano il monitoraggio delle risorse in base a condizioni quali spazio di archiviazione disponibile, frequenza delle richieste o utilizzo dei dati. Un avviso invia una notifica quando le frequenze o le condizioni soddisfano le specifiche definite.

La procedura per la configurazione di una pipeline di avvisi, prevede due passaggi: 
1. Configurare un gruppo di azioni con le azioni da intraprendere quando viene attivato un avviso, ad esempio messaggio di posta elettronica, SMS, runbook o webhook. I gruppi di azioni possono essere riutilizzati tra avvisi diversi.
2. Configurare le regole di avviso. Le regole associano i modelli delle metriche ai gruppi di azioni in base alla risorsa, alla metrica, alla condizione, all'aggregazione temporale di destinazione e così via.

## <a name="prerequisites"></a>Prerequisiti
Oltre ai requisiti di Azure Spring, questa esercitazione dipende dalle risorse seguenti.

* Un'istanza di Azure Spring Cloud distribuita.  Per iniziare, seguire la [guida dell'avvio rapido](spring-cloud-quickstart-launch-app-cli.md).

* Una risorsa di Azure da monitorare, ad esempio il database implementato in questo articolo: [Come usare l'API Apache Cassandra per Spring Data con Azure Cosmos DB](https://docs.microsoft.com/azure/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
 
Le procedure seguenti consentono di inizializzare sia il **gruppo di azioni** sia l'**avviso** dall'opzione **Avvisi** nel riquadro di spostamento a sinistra di un'istanza di Spring Cloud. La procedura può anche essere avviata dalla pagina **Panoramica di Monitoraggio** del portale di Azure. 

Passare da un gruppo di risorse all'istanza di Spring Cloud. Nel riquadro sinistro selezionare **Avvisi** e quindi **Gestisci azioni**:

![Screenshot della pagina del gruppo di risorse nel portale](media/alerts-action-groups/action-1-a.png)

## <a name="set-up-action-group"></a>Configurare il gruppo di azioni

Per iniziare la procedura di inizializzazione di un nuovo **Gruppo di azioni**, selezionare **+ Aggiungi gruppo di azioni**.

![Screenshot dell'opzione Aggiungi gruppo di azioni nel portale](media/alerts-action-groups/action-1.png)

Nella pagina **Aggiungi gruppo di azioni**:

 1. Specificare il **Nome gruppo di azioni** e il **Nome breve**.

 1. Specificare la **Sottoscrizione** e il **Gruppo di risorse**.

 1. Specificare il **Nome azione**.

 1. Selezionare il **Tipo di azione**.  Verrà aperto un altro riquadro a destra per definire l'azione che verrà eseguita all'attivazione.

 1. Definire l'azione usando le opzioni nel riquadro destro.  In questo caso verrà usata la notifica di posta elettronica.

 1. Fare clic su **OK** nel riquadro dell'azione a destra.

 1. Fare clic su **OK** nella finestra di dialogo **Aggiungi gruppo di azioni**. 

  ![Screenshot della definizione dell'azione nel portale](media/alerts-action-groups/action-2.png)

## <a name="set-up-alert"></a>Configurare l'avviso 

Nei passaggi precedenti è stato creato un **Gruppo di azioni** che usa la posta elettronica. È anche possibile usare la notifica tramite telefono, i webhook, le funzioni di Azure e così via.  

Per configurare un **Avviso**, tornare alla pagina **Avvisi** e fare clic su **Gestisci regole di avviso**.

  ![Screenshot della definizione dell'avviso nel portale](media/alerts-action-groups/alerts-2.png)

1. Selezionare la **Risorsa** per l'avviso.

1. Fare clic su **+ Nuova regola di avviso**.

  ![Screenshot dell'opzione Nuova regola di avviso nel portale](media/alerts-action-groups/alerts-3.png)

1. Nella pagina **Crea regola** specificare **RISORSA**, **CONDIZIONE** e **AZIONI**.  Dal riquadro **AZIONI** selezionare il **Gruppo di azioni** definito in precedenza.

1. In **DETTAGLI AVVISO** assegnare un nome alla regola di avviso.

1. Fare clic su **Crea regola di avviso**.

  ![Screenshot dell'opzione Nuova regola di avviso nel portale](media/alerts-action-groups/alerts-4.png)

Verificare che la nuova regola di avviso sia abilitata.

  ![Screenshot dell'opzione Nuova regola di avviso nel portale](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>Passaggi successivi
* [Creare e gestire gruppi di azione nel portale di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* [Comportamento degli avvisi SMS nei gruppi di azioni](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-sms-behavior)
* [Esercitazione: ](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)Uso di Traccia distribuita con Azure Spring Cloud
