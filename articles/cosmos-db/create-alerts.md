---
title: Creare avvisi per Azure Cosmos DB usando monitoraggio di Azure
description: Informazioni su come configurare gli avvisi per Azure Cosmos DB usando monitoraggio di Azure.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: how-to
ms.date: 07/16/2020
ms.openlocfilehash: 1042638dc622e6675c997bc6db8df1d072824816
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099913"
---
# <a name="create-alerts-for-azure-cosmos-db-using-azure-monitor"></a>Creare avvisi per Azure Cosmos DB usando monitoraggio di Azure
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Gli avvisi vengono usati per configurare test ricorrenti per monitorare la disponibilità e la velocità di risposta delle risorse Azure Cosmos DB. Gli avvisi possono inviare una notifica sotto forma di messaggio di posta elettronica oppure eseguire una funzione di Azure quando una delle metriche raggiunge la soglia o se viene registrato un evento specifico nel log attività.

È possibile ricevere un avviso basato sulle metriche o sugli eventi del log attività nell'account Azure Cosmos:

* **Metrica** : l'avviso si attiva quando il valore di una metrica specificata supera una soglia assegnata. Ad esempio, quando le unità di richiesta totali utilizzate superano 1000 ur/s. Questo avviso viene generato quando la condizione viene soddisfatta per la prima volta e successivamente quando tale condizione non viene più soddisfatta. Vedere l'articolo di [riferimento sui dati di monitoraggio](monitor-cosmos-db-reference.md#metrics) per le diverse metriche disponibili in Azure Cosmos DB.

* **Eventi del log attività** : questo avviso viene attivato quando si verifica un determinato evento. Ad esempio, quando si accede o si aggiornano le chiavi dell'account Azure Cosmos.

È possibile configurare gli avvisi dal riquadro Azure Cosmos DB o dal servizio monitoraggio di Azure nel portale di Azure. Entrambe le interfacce offrono le stesse opzioni. Questo articolo illustra come configurare gli avvisi per Azure Cosmos DB usando monitoraggio di Azure.

## <a name="create-an-alert-rule"></a>Creare una regola di avviso

Questa sezione illustra come creare un avviso quando si riceve un codice di stato HTTP 429, che viene ricevuto quando la frequenza delle richieste è limitata. Per gli esempi, è possibile che si desideri ricevere un avviso quando sono presenti 100 o più richieste con frequenza limitata. Questo articolo illustra come configurare un avviso per tale scenario usando il codice di stato HTTP. È possibile usare i passaggi simili per configurare anche altri tipi di avvisi, ma è sufficiente scegliere una condizione diversa in base alle esigenze.

1. Accedere al [portale di Azure.](https://portal.azure.com/)

1. Selezionare **monitoraggio** nella barra di spostamento a sinistra e selezionare **avvisi** .

1. Selezionare il pulsante nuova regola di avviso per aprire il riquadro Crea regola di avviso.  

1. Compilare la sezione **ambito** :

   * Aprire il riquadro **Seleziona risorsa** e configurare quanto segue:

   * Scegliere il nome della **sottoscrizione** .

   * Selezionare **Azure Cosmos DB account** per il **tipo di risorsa** .

   * Il **percorso** dell'account Azure Cosmos.

   * Dopo aver compilato i dettagli, viene visualizzato un elenco di account Azure Cosmos nell'ambito selezionato. Scegliere quella per cui si desidera configurare gli avvisi e selezionare **fine** .

1. Compilare la sezione **condizione** :

   * Aprire il riquadro **Seleziona condizione** per aprire la pagina **Configura logica del segnale** e configurare quanto segue:

   * Selezionare un segnale, Il **tipo di segnale** può essere una **metrica** o un **log attività** . Scegliere le **metriche** per questo scenario. Poiché si desidera ricevere un avviso quando si verificano problemi di limitazione della frequenza per la metrica totale unità richiesta.

   * Seleziona **tutto** per il **servizio di monitoraggio**

   * Scegliere un **nome di segnale** . Per ricevere un avviso per i codici di stato HTTP, scegliere il segnale **totale delle unità richiesta** .

   * Nella scheda successiva è possibile definire la logica per l'attivazione di un avviso e usare il grafico per visualizzare le tendenze dell'account Azure Cosmos. La metrica **totale unità richiesta** supporta le dimensioni. Queste dimensioni consentono di filtrare la metrica. Se non si seleziona alcuna dimensione, questo valore viene ignorato.

   * Scegliere **statusCode** come **nome della dimensione** . Selezionare **Aggiungi valore personalizzato** e impostare il codice di stato su 429.

   * Nella **logica di avviso** impostare la **soglia** su **static** . La soglia statica usa un valore soglia definito dall'utente per valutare la regola, mentre le soglie dinamiche usano gli algoritmi di apprendimento automatico incorporati per apprendere continuamente il modello di comportamento della metrica e calcolare automaticamente le soglie.

   * Impostare l' **operatore** su un valore **maggiore di** , il **tipo di aggregazione** su **Total** e il **valore soglia** su **100** . Con questa logica, se il client rileva più di 100 richieste con un codice di stato 429, viene attivato l'avviso. È anche possibile configurare il tipo di aggregazione, la granularità delle aggregazioni e la frequenza di valutazione in base ai requisiti.

   * Dopo aver compilato il modulo, selezionare **fine** . La schermata seguente mostra i dettagli della logica di avviso:

     :::image type="content" source="./media/create-alerts/configure-alert-logic.png" alt-text="Configurare la logica per la ricezione di avvisi per richieste con frequenza limitata/429":::

1. Compilare la sezione del **gruppo di azioni** :

   * Nel riquadro **Crea regola** selezionare un gruppo di azioni esistente o creare un nuovo gruppo di azioni. Un gruppo di azione consente di definire l'azione da intraprendere quando si verifica una condizione di avviso. Per questo esempio, creare un nuovo gruppo di azioni per ricevere una notifica tramite posta elettronica quando viene generato l'avviso. Aprire il riquadro **Aggiungi gruppo di azione** e compilare i dettagli seguenti:

   * **Nome gruppo di azioni** : il nome del gruppo di azioni deve essere univoco all'interno di un gruppo di risorse.

   * **Nome breve** : nome breve del gruppo di azioni, questo valore è incluso nelle notifiche di posta elettronica e SMS per identificare il gruppo di azione che rappresenta l'origine della notifica.

   * Scegliere la sottoscrizione e il gruppo di risorse in cui verrà creato il gruppo di azione.  

   * Specificare un nome per l'azione e selezionare **messaggio di posta elettronica/SMS/push/Voice** come **tipo di azione** . La schermata seguente mostra i dettagli del tipo di azione:

     :::image type="content" source="./media/create-alerts/configure-alert-action-type.png" alt-text="Configurare la logica per la ricezione di avvisi per richieste con frequenza limitata/429":::

1. Compilare la sezione dei **Dettagli della regola di avviso** :

   * Definire un nome per la regola, fornire una descrizione facoltativa, il livello di gravità dell'avviso, scegliere se abilitare la regola al momento della creazione della regola, quindi selezionare **Crea avviso regola** per creare l'avviso della regola metrica.

Dopo la creazione, l'avviso sarà attivo entro 10 minuti.

## <a name="common-alerting-scenarios"></a>Scenari comuni di avviso

Di seguito sono riportati alcuni scenari in cui è possibile utilizzare gli avvisi:

* Quando vengono aggiornate le chiavi di un account Azure Cosmos.
* Quando l'utilizzo dei dati o dell'indice di un contenitore, di un database o di un'area supera un determinato numero di byte.
* Quando il consumo di Ur/s normalizzato è maggiore di una determinata percentuale. La metrica di consumo ur normalizzato fornisce l'utilizzo massimo della velocità effettiva in un set di repliche. Per informazioni, vedere l'articolo [come monitorare le UR/sec normalizzate](monitor-normalized-request-units.md) .  
* Quando un'area viene aggiunta, rimossa o se passa alla modalità offline.
* Quando un database o un contenitore viene creato, eliminato o aggiornato.
* Quando la velocità effettiva del database o del contenitore viene modificata.

## <a name="next-steps"></a>Passaggi successivi

* Come [monitorare la metrica ur/sec normalizzata](monitor-normalized-request-units.md) nel contenitore Azure Cosmos.
* Come [monitorare la velocità effettiva o l'utilizzo delle unità richiesta](monitor-request-unit-usage.md) di un'operazione in Azure Cosmos DB.