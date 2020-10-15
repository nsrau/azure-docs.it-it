---
title: Distribuire moduli su larga scala in portale di Azure-Azure IoT Edge
description: Usare il portale di Azure per creare distribuzioni automatiche per gruppi di dispositivi IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9d03b6f4a512c22564480405ec0f0e0c0e62a958
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048424"
---
# <a name="deploy-iot-edge-modules-at-scale-using-the-azure-portal"></a>Distribuisci moduli IoT Edge su larga scala usando il portale di Azure

Creare una **IOT Edge distribuzione automatica** nel portale di Azure per gestire contemporaneamente le distribuzioni in corso per molti dispositivi. Le distribuzioni automatiche per IoT Edge rientrano nella funzionalità di [gestione automatica dei dispositivi](../iot-hub/iot-hub-automatic-device-management.md) dell'hub IoT. Le distribuzioni sono processi dinamici che consentono di distribuire più moduli in più dispositivi, di tenere traccia dello stato e dell'integrità dei moduli, nonché di apportare modifiche all'occorrenza.

Per altre informazioni, vedere [Informazioni sulle distribuzioni automatiche di IoT Edge per singoli dispositivi o su vasta scala](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identificare i dispositivi tramite tag

Prima di poter creare una distribuzione, è necessario essere in grado di specificare i dispositivi a cui la si vuole applicare. Azure IoT Edge identifica i dispositivi tramite **tag** nel dispositivo gemello. Ogni dispositivo può avere più tag, che è possibile definire in qualsiasi modo risulti appropriato per una soluzione specifica.

Se ad esempio si gestisce un campus di Smart Build, è possibile aggiungere tag di posizione, tipo di stanza e ambiente a un dispositivo:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Per altre informazioni sui dispositivi gemelli e i tag, vedere [Comprendere e usare dispositivi gemelli nell'hub IoT](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Creare una distribuzione

IoT Edge fornisce due tipi diversi di distribuzioni automatiche che è possibile usare per personalizzare lo scenario. È possibile creare una *distribuzione*standard, che include i moduli di runtime di sistema e gli eventuali moduli e route aggiuntivi. Ogni dispositivo può applicare solo una distribuzione. In alternativa, è possibile creare una distribuzione a più *livelli*, che include solo moduli e Route personalizzati, non il runtime di sistema. Molte distribuzioni su più livelli possono essere combinate in un dispositivo, oltre a una distribuzione standard. Per altre informazioni sul funzionamento combinato dei due tipi di distribuzioni automatiche, vedere informazioni sulle [distribuzioni automatiche IOT Edge per singoli dispositivi o su larga scala](module-deployment-monitoring.md).

I passaggi per la creazione di una distribuzione e una distribuzione a più livelli sono molto simili. Le eventuali differenze sono riportate nei passaggi seguenti.

1. Nella [portale di Azure](https://portal.azure.com)passare all'hub Internet.
1. Nel menu nel riquadro a sinistra selezionare **IOT Edge** in **gestione automatica dei dispositivi**.
1. Sulla barra superiore selezionare **Crea distribuzione** o **Crea distribuzione**su più livelli.

La creazione di una distribuzione prevede cinque passaggi, illustrati nelle sezioni seguenti.

>[!NOTE]
>I passaggi descritti in questo articolo riflettono la versione dello schema più recente dell'agente e dell'hub IoT Edge. La versione dello schema 1,1 è stata rilasciata insieme a IoT Edge versione 1.0.10 e Abilita le funzionalità per l'ordine di avvio e la definizione delle priorità del modulo.
>
>Se si esegue la distribuzione in un dispositivo che esegue la versione 1.0.9 o precedente, modificare le **impostazioni di runtime** nel passaggio **moduli** della procedura guidata per usare la versione dello schema 1,0.

### <a name="step-1-name-and-label"></a>Passaggio 1: nome ed etichetta

1. Assegnare alla distribuzione un nome univoco contenente al massimo 128 lettere minuscole. Evitare gli spazi e i seguenti caratteri non validi: `& ^ [ ] { } \ | " < > /`.
1. È possibile aggiungere etichette come coppie chiave-valore per tenere traccia delle distribuzioni. Ad esempio, **PiattaformaHost** e **Linux** oppure **Versione** e **3.0.1**.
1. Selezionare **Avanti: moduli** da spostare al passaggio 2.

### <a name="step-2-modules"></a>Passaggio 2: moduli

È possibile aggiungere fino a 50 moduli a una distribuzione. Se si crea una distribuzione senza moduli, verranno rimossi tutti i moduli correnti dai dispositivi di destinazione.

Nelle distribuzioni è possibile gestire le impostazioni per i moduli agente IoT Edge e hub IoT Edge. Selezionare **le impostazioni di runtime** per configurare i due moduli di Runtime. In una distribuzione a più livelli i moduli di runtime non sono inclusi e pertanto non possono essere configurati.

Per aggiungere codice personalizzato come modulo o aggiungere manualmente un modulo per un servizio di Azure, seguire questa procedura:

1. Nella sezione **impostazioni container Registry** della pagina fornire le credenziali per accedere ai registri di contenitori privati contenenti le immagini del modulo.
1. Nella sezione **moduli IOT Edge** della pagina selezionare **Aggiungi**.
1. Scegliere uno dei tre tipi di moduli dal menu a discesa:

   * **Modulo IOT Edge** : specificare il nome del modulo e l'URI dell'immagine del contenitore. Ad esempio, l'URI dell'immagine per il modulo SimulatedTemperatureSensor di esempio è `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0` . Se l'immagine del modulo è archiviata in un registro contenitori privato, aggiungere le credenziali in questa pagina per accedere all'immagine.
   * **Modulo Marketplace** : moduli ospitati in Azure Marketplace. Alcuni moduli del Marketplace richiedono una configurazione aggiuntiva, quindi esaminare i dettagli del modulo nell'elenco dei [moduli IOT Edge di Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) .
   * **Modulo di analisi di flusso di Azure** : moduli generati da un carico di lavoro di analisi di flusso di Azure.

1. Se necessario, ripetere i passaggi 2 e 3 per aggiungere ulteriori moduli alla distribuzione.

Dopo aver aggiunto un modulo a una distribuzione, è possibile selezionarne il nome per aprire la pagina **aggiorna IOT Edge modulo** . In questa pagina è possibile modificare le impostazioni del modulo, le variabili di ambiente, le opzioni di creazione, l'ordine di avvio e il modulo gemello. Se è stato aggiunto un modulo dal Marketplace, è possibile che alcuni di questi parametri siano già stati compilati. Per ulteriori informazioni sulle impostazioni dei moduli disponibili, vedere [configurazione e gestione dei moduli](module-composition.md#module-configuration-and-management).

Se si sta creando una distribuzione a più livelli, è possibile che si stia configurando un modulo presente in altre distribuzioni destinate agli stessi dispositivi. Per aggiornare il modulo gemello senza sovrascrivere altre versioni, aprire la scheda **impostazioni del modulo gemello** . Creare una nuova **proprietà del modulo gemello** con un nome univoco per una sottosezione nelle proprietà desiderate del modulo gemello, ad esempio `properties.desired.settings` . Se si definiscono proprietà solo nel `properties.desired` campo, le proprietà desiderate per il modulo definite in tutte le distribuzioni con priorità inferiore vengono sovrascritte.

![Imposta la proprietà del modulo gemello per la distribuzione su più livelli](./media/how-to-deploy-monitor/module-twin-property.png)

Per altre informazioni sulla configurazione dei moduli gemelli nelle distribuzioni a più livelli, vedere distribuzione a più [livelli](module-deployment-monitoring.md#layered-deployment).

Una volta configurati tutti i moduli per una distribuzione, fare clic su **Next: routes** to Move to Step 3.

### <a name="step-3-routes"></a>Passaggio 3: Route

Nella scheda **Route** è possibile definire come vengono passati i messaggi tra i moduli e l'hub IoT. I messaggi vengono costruiti mediante coppie nome/valore.

Ad esempio, una route con una **Route** di nome e un valore **da/messages/a \* $upstream** otterrebbe tutti i messaggi restituiti da qualsiasi modulo e li invierà all'hub Internet.  

I parametri **Priority** e **time to Live** sono parametri facoltativi che è possibile includere in una definizione di route. Il parametro Priority consente di scegliere le route di cui devono essere elaborati i messaggi prima o le route da elaborare per ultime. La priorità viene determinata impostando un numero 0-9, dove 0 è la priorità più alta. Il parametro time to Live consente di dichiarare per quanto tempo i messaggi in tale route devono essere conservati fino a quando non vengono elaborati o rimossi dalla coda.

Per altre informazioni su come creare route, vedere [dichiarare le route](module-composition.md#declare-routes).

Selezionare **Avanti: metrica**.

### <a name="step-4-metrics"></a>Passaggio 4: metriche

Le metriche forniscono i conteggi di riepilogo dei diversi stati che un dispositivo può segnalare dopo l'applicazione del contenuto della configurazione.

1. Immettere un nome per **Nome della metrica**

1. Immettere una query per **Metric Criteria** (Criteri metrica). La query è basata sulle [proprietà segnalate](module-edgeagent-edgehub.md#edgehub-reported-properties) del modulo gemello hub di IoT Edge. La metrica rappresenta il numero di righe restituite dalla query.

   Ad esempio:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

Selezionare **Avanti: dispositivi di destinazione**.

### <a name="step-5-target-devices"></a>Passaggio 5: dispositivi di destinazione

Usare la proprietà tags dai dispositivi per selezionare i dispositivi specifici che devono ricevere la distribuzione.

Dato che più distribuzioni potrebbero avere come destinazione lo stesso dispositivo, è necessario assegnare a ogni distribuzione un numero di priorità. Se si verifica un conflitto, la distribuzione con la priorità più alta (valori maggiori indica una priorità più alta) prevale. Se due distribuzioni hanno lo stesso numero di priorità, prevale quella creata più di recente.

Se più distribuzioni hanno come destinazione lo stesso dispositivo, viene applicato solo quello con la priorità più alta. Se più distribuzioni a più livelli hanno come destinazione lo stesso dispositivo, vengono applicate tutte. Tuttavia, se le proprietà vengono duplicate, ad esempio se sono presenti due route con lo stesso nome, quella della distribuzione a più livelli con priorità superiore sovrascrive il resto.

Tutte le distribuzioni a più livelli destinate a un dispositivo devono avere una priorità più elevata rispetto alla distribuzione di base per l'applicazione.

1. Immettere un numero intero positivo in **Priority** (Priorità) per la distribuzione.
1. Specificare una condizione in **Target condition** (Condizione di destinazione) per determinare i dispositivi di destinazione di questa distribuzione. La condizione è basata sui tag o sulle proprietà segnalate dei dispositivi gemelli e deve corrispondere al formato di espressione.Ad esempio, `tags.environment='test'` o `properties.reported.devicemodel='4000x'`.

Selezionare **Avanti: rivedere + crea** per passare al passaggio finale.

### <a name="step-6-review-and-create"></a>Passaggio 6: esaminare e creare

Esaminare le informazioni di distribuzione e quindi selezionare **Crea**.

Per monitorare la distribuzione, vedere [monitorare IOT Edge distribuzioni](how-to-monitor-iot-edge-deployments.md).

## <a name="modify-a-deployment"></a>Modificare una distribuzione

Quando si modifica una distribuzione, le modifiche vengono replicate immediatamente in tutti i dispositivi di destinazione. È possibile modificare le impostazioni e le funzionalità seguenti per una distribuzione esistente:

* Condizioni di destinazione
* Metriche personalizzate
* Etichette
* Tag
* Proprietà desiderate

### <a name="modify-target-conditions-custom-metrics-and-labels"></a>Modificare le condizioni di destinazione, le metriche personalizzate e le etichette

1. Nell'hub Internet delle cose selezionare **IOT Edge** dal menu del riquadro sinistro.
1. Selezionare la scheda **distribuzioni IOT Edge** , quindi selezionare la distribuzione che si desidera configurare.
1. Selezionare la scheda **condizione di destinazione** . Modificare la **condizione di destinazione** per individuare i dispositivi desiderati. È anche possibile modificare la **priorità**.  Selezionare **Salva**.

    Se si aggiorna la condizione di destinazione, vengono eseguiti gli aggiornamenti seguenti:

    * Se un dispositivo non soddisfa la condizione di destinazione precedente, ma soddisfa la nuova condizione di destinazione e questa distribuzione ha la priorità più alta per il dispositivo, la distribuzione viene applicata al dispositivo.
    * Se un dispositivo che esegue la distribuzione non soddisfa più la condizione di destinazione, disinstalla questa distribuzione e riceve la distribuzione successiva nell'ordine di priorità.
    * Se un dispositivo che esegue la distribuzione non soddisfa più la condizione di destinazione e non soddisfa la condizione di destinazione di tutte le altre distribuzioni, nel dispositivo non viene apportata alcuna modifica. Il dispositivo continua a eseguire i moduli corrente nello stato corrente, ma non è più gestito come parte di questa distribuzione. Quando soddisfa la condizione di destinazione di qualsiasi altra distribuzione, disinstalla questa distribuzione e riceve quella nuova.

1. Selezionare la scheda **metriche** , quindi fare clic sul pulsante **modifica metriche** . Aggiungere o modificare metriche personalizzate, usando la sintassi di esempio come guida. Selezionare **Salva**.

    ![Modificare le metriche personalizzate in una distribuzione](./media/how-to-deploy-monitor/metric-list.png)

1. Selezionare la scheda **etichette** e apportare le modifiche desiderate e selezionare **Salva**.

## <a name="delete-a-deployment"></a>Eliminare una distribuzione

Quando si elimina una distribuzione, tutti i dispositivi distribuiti accettano la successiva distribuzione con priorità più elevata. Se i dispositivi non soddisfano la condizione di destinazione di qualsiasi altra distribuzione, i moduli non vengono rimossi in seguito all'eliminazione della distribuzione.

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.
1. Selezionare **IoT Edge**.
1. Selezionare la scheda **distribuzioni IOT Edge** .

   ![Visualizzare le distribuzioni IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Usare la casella di controllo per selezionare la distribuzione che si vuole eliminare.
1. Selezionare **Elimina**.
1. Un messaggio indicherà che questa azione comporta l'eliminazione della distribuzione e il ripristino dello stato precedente per tutti i dispositivi.Viene applicata una distribuzione con una priorità più bassa.Se non viene assegnata alcun'altra distribuzione, i moduli non verranno rimossi. Per rimuovere tutti i moduli dai dispositivi, creare una distribuzione senza moduli e assegnarla agli stessi dispositivi.Selezionare **Sì** per continuare.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [distribuzione di moduli nei dispositivi IoT Edge](module-deployment-monitoring.md).