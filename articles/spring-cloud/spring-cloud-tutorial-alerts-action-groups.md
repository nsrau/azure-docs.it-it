---
title: 'Esercitazione: Monitorare le risorse di Azure Spring Cloud tramite avvisi e gruppi di azioni | Microsoft Docs'
description: Informazioni su come usare gli avvisi di Spring cloud.
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 12/29/2019
ms.openlocfilehash: 0d3fcbbb317ef70687c83ff21e2ab817d89051a6
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142458"
---
# <a name="tutorial-how-to-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>Esercitazione: Come monitorare le risorse di Spring Cloud tramite avvisi e gruppi di azioni.

Gli avvisi di Azure Spring Cloud supportano il monitoraggio delle risorse in base a condizioni quali spazio di archiviazione disponibile, frequenza delle richieste o utilizzo dei dati. Un avviso invia una notifica quando le frequenze o le condizioni soddisfano le specifiche definite.

La procedura per la configurazione di una pipeline di avvisi, prevede due passaggi: 
1. Configurare un gruppo di azioni con le azioni da intraprendere quando viene attivato un avviso, ad esempio messaggio di posta elettronica, SMS, runbook o webhook. I gruppi di azioni possono essere riutilizzati tra avvisi diversi.
2. Configurare le regole di avviso. Le regole associano i modelli delle metriche ai gruppi di azioni in base alla risorsa, alla metrica, alla condizione, all'aggregazione temporale di destinazione e così via.

## <a name="prerequisites"></a>Prerequisiti

Oltre ai requisiti di Azure Spring Cloud, le procedure descritte in questa esercitazione si basano su un'istanza di Azure Spring Cloud distribuita.  Per iniziare, seguire una [guida di avvio rapido](spring-cloud-quickstart-launch-app-cli.md).

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

1. Nella pagina **Crea regola** specificare **RISORSA**.

1. L'impostazione **CONDIZIONE** offre molte opzioni per il monitoraggio delle risorse **Spring Cloud**.  Fare clic su **Aggiungi** per aprire il riquadro **Configura logica dei segnali**.

1. Selezionare una condizione. Questo esempio usa la **percentuale di utilizzo della CPU di sistema**.

   ![Screenshot dell'opzione Nuova regola di avviso nel portale](media/alerts-action-groups/alerts-3-1.png)

1. Nel riquadro **Configura logica dei segnali** scorrere verso il basso per impostare **Valore soglia** per il monitoraggio.

   ![Screenshot dell'opzione Nuova regola di avviso nel portale](media/alerts-action-groups/alerts-3-2.png)

1. Fare clic su **Done**.

Per informazioni dettagliate sulle condizioni disponibili per il monitoraggio, vedere [Opzioni delle metriche del portale utenti](spring-cloud-concept-metrics.md#user-metrics-options).

 In **AZIONI** fare clic su **Seleziona gruppo di azioni**. Dal riquadro **AZIONI** selezionare il **Gruppo di azioni** definito in precedenza.

   ![Screenshot dell'opzione Nuova regola di avviso nel portale](media/alerts-action-groups/alerts-3-3.png) 

1. Scorrere verso il basso e in **DETTAGLI AVVISO** assegnare un nome alla regola di avviso.

1. Impostare il livello di **Gravità**.

1. Fare clic su **Crea regola di avviso**.

   ![Screenshot dell'opzione Nuova regola di avviso nel portale](media/alerts-action-groups/alerts-3-4.png)

Verificare che la nuova regola di avviso sia abilitata.

   ![Screenshot dell'opzione Nuova regola di avviso nel portale](media/alerts-action-groups/alerts-4.png)

È possibile creare una regola anche usando la pagina **Metriche**:

   ![Screenshot dell'opzione Nuova regola di avviso nel portale](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come configurare avvisi e gruppi di azioni per un'applicazione Azure Spring Cloud. Per altre informazioni sui gruppi di azioni, vedere:

> [!div class="nextstepaction"]
> [Creare e gestire gruppi di azione nel portale di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

> [!div class="nextstepaction"]
> [Comportamento degli avvisi SMS nei gruppi di azioni](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-sms-behavior)
