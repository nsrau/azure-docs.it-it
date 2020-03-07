---
title: Distribuire moduli su larga scala in portale di Azure-Azure IoT Edge
description: Usare il portale di Azure per creare distribuzioni automatiche per gruppi di dispositivi IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0a20ea4236683e26c51bc75309435c65e24271d7
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396759"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Distribuire e monitorare i moduli di IoT Edge su larga scala tramite il portale di Azure

Creare una **IOT Edge distribuzione automatica** nel portale di Azure per gestire contemporaneamente le distribuzioni in corso per molti dispositivi. Le distribuzioni automatiche per IoT Edge fanno parte della funzionalità di [gestione automatica dei dispositivi](/azure/iot-hub/iot-hub-automatic-device-management) dell'hub Internet. Le distribuzioni sono processi dinamici che consentono di distribuire più moduli a più dispositivi, tenere traccia dello stato e dell'integrità dei moduli e apportare modifiche quando necessario.

Per altre informazioni, vedere [comprendere IOT Edge distribuzioni automatiche per singoli dispositivi o su larga scala](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identificare i dispositivi tramite tag

Prima di poter creare una distribuzione, è necessario essere in grado di specificare i dispositivi a cui la si vuole applicare. Azure IoT Edge identifica i dispositivi tramite **tag** nel dispositivo gemello. Ogni dispositivo può avere più tag definiti in qualsiasi modo appropriato per la soluzione.

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

### <a name="step-1-name-and-label"></a>Passaggio 1: nome ed etichetta

1. Assegnare alla distribuzione un nome univoco contenente al massimo 128 lettere minuscole. Evitare gli spazi e i seguenti caratteri non validi: `& ^ [ ] { } \ | " < > /`.
1. È possibile aggiungere etichette come coppie chiave-valore per tenere traccia delle distribuzioni. Ad esempio, **PiattaformaHost** e **Linux** oppure **Versione** e **3.0.1**.
1. Selezionare **Avanti: moduli** da spostare al passaggio 2.

### <a name="step-2-modules"></a>Passaggio 2: moduli

È possibile aggiungere fino a 20 moduli a una distribuzione. Se si crea una distribuzione senza moduli, verranno rimossi tutti i moduli correnti dai dispositivi di destinazione.

Nelle distribuzioni è possibile gestire le impostazioni per i moduli agente IoT Edge e hub IoT Edge. Selezionare **le impostazioni di runtime** per configurare i due moduli di Runtime. In una distribuzione a più livelli i moduli di runtime non sono inclusi e pertanto non possono essere configurati.

È possibile aggiungere tre tipi di moduli:

* Modulo IoT Edge
* Modulo Marketplace
* Modulo di analisi di flusso di Azure

#### <a name="add-an-iot-edge-module"></a>Aggiungere un modulo IoT Edge

Per aggiungere codice personalizzato come modulo o aggiungere manualmente un modulo per un servizio di Azure, seguire questa procedura:

1. Nella sezione **credenziali container Registry** della pagina fornire i nomi e le credenziali per tutti i registri di contenitori privati contenenti le immagini del modulo per la distribuzione. L'agente di IoT Edge segnalerà l'errore 500 se non riesce a trovare le credenziali del registro contenitori per un'immagine docker.
1. Nella sezione **Moduli IoT Edge** della pagina fare clic su **Aggiungi**.
1. Selezionare **IOT Edge modulo** dal menu a discesa.
1. Assegnare al modulo un **nome di modulo IOT Edge**.
1. Nel campo **URI immagine** immettere l'immagine del contenitore per il modulo.
1. Usare il menu a discesa per selezionare **Restart policy** (Criteri di riavvio). È possibile scegliere tra le opzioni seguenti:
   * **sempre** : il modulo viene sempre riavviato se viene arrestato per qualsiasi motivo.
   * **mai** : il modulo non viene mai riavviato se viene arrestato per qualsiasi motivo.
   * **in caso di errore** : il modulo viene riavviato in caso di arresto anomalo, ma non se viene arrestato in modo corretto.
   * **in-non integro** : il modulo viene riavviato in caso di arresto anomalo o restituisce uno stato non integro. Ogni modulo deve implementare la funzione di stato di integrità.
1. Usare il menu a discesa per selezionare lo **stato desiderato** per il modulo. È possibile scegliere tra le opzioni seguenti:
   * **Running** -running è l'opzione predefinita. Il modulo verrà avviato immediatamente dopo la distribuzione.
   * **arrestato** : dopo la distribuzione, il modulo rimarrà inattivo fino a quando non viene chiamato per iniziare dall'utente o da un altro modulo.
1. Specificare le eventuali **Container Create Options** (Opzioni di creazione container) da passare al contenitore. Per altre informazioni, vedere [docker create](https://docs.docker.com/engine/reference/commandline/create/).
1. Selezionare **modulo dispositivi gemelli** se si vuole aggiungere tag o altre proprietà al modulo gemello.
1. Immettere **Variabili di ambiente** per il modulo. Le variabili di ambiente forniscono informazioni di configurazione a un modulo.
1. Selezionare **Aggiungi** per aggiungere il modulo alla distribuzione.

#### <a name="add-a-module-from-the-marketplace"></a>Aggiungere un modulo dal Marketplace

Per aggiungere un modulo da Azure Marketplace, seguire questa procedura:

1. Nella sezione **Moduli IoT Edge** della pagina fare clic su **Aggiungi**.
1. Selezionare **modulo Marketplace** dal menu a discesa.
1. Scegliere un modulo dalla pagina del **Marketplace del modulo IOT Edge** . Il modulo selezionato viene configurato automaticamente per la sottoscrizione, il gruppo di risorse e il dispositivo. Viene quindi visualizzato nell'elenco dei moduli IoT Edge. Alcuni moduli possono richiedere una configurazione aggiuntiva. Per altre informazioni, vedere [distribuire moduli da Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace).

#### <a name="add-a-stream-analytics-module"></a>Aggiungere un modulo di analisi di flusso

Per aggiungere un modulo da Analisi di flusso di Azure, seguire questa procedura:

1. Nella sezione **Moduli IoT Edge** della pagina fare clic su **Aggiungi**.
1. Selezionare il **modulo analisi di flusso di Azure** dal menu a discesa.
1. Nel riquadro destro scegliere la **sottoscrizione**.
1. Scegli il tuo **processo Edge**.
1. Selezionare **Save** (Salva) per aggiungere il modulo alla distribuzione.

#### <a name="configure-module-settings"></a>Configurare le impostazioni del modulo

Dopo aver aggiunto un modulo a una distribuzione, è possibile selezionarne il nome per aprire la pagina **aggiorna IOT Edge modulo** . In questa pagina è possibile modificare le impostazioni del modulo, le variabili di ambiente, le opzioni di creazione e il modulo gemello. Se è stato aggiunto un modulo dal Marketplace, è possibile che alcuni di questi parametri siano già stati compilati.

Se si sta creando una distribuzione a più livelli, è possibile che si stia configurando un modulo presente in altre distribuzioni destinate agli stessi dispositivi. Per aggiornare il modulo gemello senza sovrascrivere altre versioni, aprire la scheda **delle impostazioni del modulo gemello** . creare una nuova **proprietà del modulo gemello** con un nome univoco per una sottosezione nelle proprietà desiderate del modulo gemello, ad esempio `properties.desired.settings`. Se si definiscono proprietà solo nel campo `properties.desired`, le proprietà desiderate per il modulo definite in tutte le distribuzioni con priorità inferiore vengono sovrascritte.

![Imposta la proprietà del modulo gemello per la distribuzione su più livelli](./media/how-to-deploy-monitor/module-twin-property.png)

Per altre informazioni sulla configurazione dei moduli gemelli nelle distribuzioni a più livelli, vedere distribuzione a più [livelli](module-deployment-monitoring.md#layered-deployment).

Una volta configurati tutti i moduli per una distribuzione, fare clic su **Next: routes** to Move to Step 3.

### <a name="step-3-routes"></a>Passaggio 3: Route

Le route definiscono le modalità di comunicazione tra i moduli in una distribuzione. Per impostazione predefinita, la procedura guidata fornisce una route denominata **upstream** e definita **da/messages/\* in $upstream**, il che significa che tutti i messaggi restituiti da qualsiasi modulo vengono inviati all'hub Internet.  

Aggiungere o aggiornare le route con le informazioni riportate in [Dichiarare le route](module-composition.md#declare-routes) e quindi scegliere **Avanti** per proseguire con la sezione di verifica.

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
1. Specificare una condizione in **Target condition** (Condizione di destinazione) per determinare i dispositivi di destinazione di questa distribuzione. La condizione è basata sui tag del dispositivo gemello o sulle proprietà segnalate dal dispositivo gemello e deve corrispondere al formato dell'espressione. Ad esempio, `tags.environment='test'` o `properties.reported.devicemodel='4000x'`.

Selezionare **Avanti: rivedere + crea** per passare al passaggio finale.

### <a name="step-6-review-and-create"></a>Passaggio 6: esaminare e creare

Esaminare le informazioni di distribuzione e quindi selezionare **Crea**.

## <a name="monitor-a-deployment"></a>Monitorare una distribuzione

Per visualizzare i dettagli di una distribuzione e monitorare i dispositivi che la eseguono, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.
1. Selezionare **IoT Edge**.
1. Selezionare la scheda **distribuzioni IOT Edge** .

   ![Visualizzare le distribuzioni IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Esaminare l'elenco delle distribuzioni. Per ogni distribuzione, è possibile visualizzare i dettagli seguenti:
   * **ID**: nome della distribuzione.
   * **Tipo** : il tipo di distribuzione **, ovvero la distribuzione o la** distribuzione a più **livelli**.
   * **Condizione di destinazione** : il tag usato per definire i dispositivi di destinazione.
   * **Priority** (Priorità): numero di priorità assegnato alla distribuzione.
   * **System metrics** -  (Metriche di sistema): **Targeted** (Assegnati) specifica il numero di dispositivi gemelli nell'hub IoT che corrispondono alla condizione di destinazione, mentre **Applied** (Applicati) specifica il numero di dispositivi il cui contenuto di distribuzione è stato applicato ai rispettivi dispositivi gemelli nell'hub IoT.
   * **Metriche del dispositivo** : numero di dispositivi IOT Edge nella distribuzione che segnalano l'esito positivo o negativo della IOT Edge runtime del client.
   * **Metriche personalizzate** : il numero di dispositivi IOT Edge nei dati di Reporting della distribuzione per le metriche definite per la distribuzione.
   * **Data di creazione** : timestamp del momento in cui è stata creata la distribuzione. Questo timestamp viene usato per stabilire la prevalenza quando due distribuzioni hanno la stessa priorità.
1. Selezionare la distribuzione che si vuole monitorare.  
1. Esaminare i dettagli della distribuzione. Per esaminare i dettagli della distribuzione sono disponibili schede.

## <a name="modify-a-deployment"></a>Modificare una distribuzione

Quando si modifica una distribuzione, le modifiche vengono replicate immediatamente in tutti i dispositivi di destinazione.

Se si aggiorna la condizione di destinazione, vengono eseguiti gli aggiornamenti seguenti:

* Se un dispositivo non soddisfa la condizione di destinazione precedente, ma soddisfa la nuova condizione di destinazione e questa distribuzione ha la priorità più alta per il dispositivo, la distribuzione viene applicata al dispositivo.
* Se un dispositivo che esegue la distribuzione non soddisfa più la condizione di destinazione, disinstalla questa distribuzione e riceve la distribuzione successiva nell'ordine di priorità.
* Se un dispositivo che esegue la distribuzione non soddisfa più la condizione di destinazione e non soddisfa la condizione di destinazione di tutte le altre distribuzioni, nel dispositivo non viene apportata alcuna modifica. Il dispositivo continua a eseguire i moduli corrente nello stato corrente, ma non è più gestito come parte di questa distribuzione. Quando soddisfa la condizione di destinazione di qualsiasi altra distribuzione, disinstalla questa distribuzione e riceve quella nuova.

Per modificare una distribuzione, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.
1. Selezionare **IoT Edge**.
1. Selezionare la scheda **distribuzioni IOT Edge** .

   ![Visualizzare le distribuzioni IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Selezionare la distribuzione che si vuole modificare.
1. Eseguire gli aggiornamenti nelle schede seguenti:
   * **Condizione di destinazione**
   * **Metriche** : è possibile modificare o eliminare le metriche definite o aggiungerne di nuove.
   * **Etichette**
   * **moduli**
   * **Route**
   * **Distribuzione**

1. Selezionare **Salva**.
1. Seguire i passaggi in [Monitorare una distribuzione](#monitor-a-deployment) per controllare la distribuzione delle modifiche.

## <a name="delete-a-deployment"></a>Eliminare una distribuzione

Quando si elimina una distribuzione, tutti i dispositivi distribuiti accettano la successiva distribuzione con priorità più elevata. Se i dispositivi non soddisfano la condizione di destinazione di qualsiasi altra distribuzione, i moduli non vengono rimossi in seguito all'eliminazione della distribuzione.

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.
1. Selezionare **IoT Edge**.
1. Selezionare la scheda **distribuzioni IOT Edge** .

   ![Visualizzare le distribuzioni IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Usare la casella di controllo per selezionare la distribuzione che si vuole eliminare.
1. Selezionare **Elimina**.
1. Un messaggio indicherà che questa azione comporta l'eliminazione della distribuzione e il ripristino dello stato precedente per tutti i dispositivi.  Ciò significa che verrà applicata una distribuzione con una priorità più bassa. Se non sono destinate altre distribuzioni, non verranno rimossi moduli. Per rimuovere tutti i moduli dai dispositivi, creare una distribuzione senza moduli e assegnarla agli stessi dispositivi. Selezionare **Sì** per continuare.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [distribuzione di moduli per IOT Edge dispositivi](module-deployment-monitoring.md).
