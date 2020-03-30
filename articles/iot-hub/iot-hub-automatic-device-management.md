---
title: Gestione automatica dei dispositivi su larga scala con l'hub Di Azure IoT Documenti Microsoft
description: Usare le configurazioni automatiche dell'hub IoT di Azure per gestire più dispositivi e moduli IoTUse Azure IoT Hub automatic configurations to manage multiple IoT devices and modules
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: chrisgre
ms.openlocfilehash: 75c6b7d89e7ae540e7428afde127281aa3f15fc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271304"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-portal"></a>Gestione automatica di dispositivi e moduli IoT tramite il portale di AzureAutomatic IoT device and module management using the Azure portal

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

La gestione automatica dei dispositivi nell'hub IoT di Azure automatizza molte delle attività ripetitive e complesse della gestione di flotte di dispositivi di grandi dimensioni. Con la gestione automatica dei dispositivi, è possibile scegliere come destinazione un set di dispositivi in base alle relative proprietà, definire una configurazione desiderata e quindi consentire all'hub IoT di aggiornare i dispositivi quando entrano nell'ambito. Questo aggiornamento viene eseguito utilizzando una _configurazione automatica_ del dispositivo o una configurazione automatica del _modulo,_ che consente di riepilogare il completamento e la conformità, gestire l'unione e i conflitti e implementare le configurazioni in un approccio graduale.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

La gestione automatica dei dispositivi funziona aggiornando un set di dispositivi gemelli o gemelli modulo con le proprietà desiderate e segnalando un riepilogo basato su proprietà segnalate gemelle.  Introduce una nuova classe e documento JSON denominato *una configurazione* che ha tre parti:It introduces a new class and JSON document called a Configuration that has three parts:

* La condizione di **destinazione** definisce l'ambito dei dispositivi gemelli o dei gemelli modulo da aggiornare. La condizione di destinazione viene specificata come query sui tag gemelli e/o sulle proprietà segnalate.

* Il contenuto di destinazione definisce le proprietà desiderate da aggiungere o aggiornare nei dispositivi gemelli o nei due gemelli del modulo di **destinazione.** Il contenuto include un percorso della sezione delle proprietà desiderate da modificare.

* Le **metriche** definiscono i conteggi di riepilogo dei vari stati di configurazione, ad esempio **Success**, **In progress** ed **Error**. Le metriche personalizzate vengono specificate come query sulle proprietà segnalate gemelle.  Le metriche di sistema sono le metriche predefinite che misurano lo stato di aggiornamento gemello, ad esempio il numero di gemelli di destinazione e il numero di gemelli che sono stati aggiornati correttamente.

Le configurazioni automatiche vengono eseguite per la prima volta subito dopo la creazione della configurazione e quindi a intervalli di cinque minuti. Le query metriche vengono eseguite ogni volta che viene eseguita la configurazione automatica.

## <a name="implement-twins"></a>Implementare i gemelli

Le configurazioni automatiche dei dispositivi richiedono l'uso di dispositivi gemelli per la sincronizzazione dello stato tra il cloud e i dispositivi.  Per ulteriori informazioni, consultate [Comprendere e usare i dispositivi gemelli nell'hub IoT.](iot-hub-devguide-device-twins.md)

Le configurazioni automatiche dei moduli richiedono l'uso di moduli gemelli per sincronizzare lo stato tra il cloud e i moduli. Per ulteriori informazioni, consultate Comprendere e usare i gemelli modulo [nell'hub IoT.](iot-hub-devguide-module-twins.md)

## <a name="use-tags-to-target-twins"></a>Utilizzare i tag per indirizzare i gemelli

Prima di creare una configurazione, è necessario specificare i dispositivi o i moduli su cui si desidera intervenire. L'hub IoT di Azure identifica i dispositivi e usa i tag nel dispositivo gemello e identifica i moduli usando i tag nel modulo gemello. Ogni dispositivo o modulo può avere più tag ed è possibile definirli in qualsiasi modo appropriato per la soluzione. Ad esempio, se gestisci i dispositivi in posizioni diverse, aggiungi i seguenti tag a un dispositivo gemello:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="create-a-configuration"></a>Creare una configurazione

1. Nel [portale di Azure](https://portal.azure.com) passare all'hub IoT. 

2. Selezionare **IoT device configuration** (Configurazione del dispositivo IoT).

3. Selezionare **Aggiungi configurazione dispositivo** o Aggiungi configurazione **modulo**.

   ![Aggiungere la configurazione del dispositivo o del moduloAdd device configuration or module configuration](./media/iot-hub-automatic-device-management/create-automatic-configuration.png)

La creazione di una configurazione prevede cinque passaggi, illustrati nelle sezioni seguenti. 

### <a name="name-and-label"></a>Nome ed etichetta

1. Assegnare alla configurazione un nome univoco con un massimo di 128 lettere in minuscolo. Evitare gli spazi e i seguenti caratteri non validi: `& ^ [ ] { } \ | " < > /`.

2. Aggiungere etichette per tenere traccia delle configurazioni. Le etichette sono coppie di **Nome** e **Valore** che descrivono la configurazione. Ad esempio, `HostPlatform, Linux` o `Version, 3.0.1`.

3. Selezionare **Avanti** per procedere al passaggio successivo. 

### <a name="specify-settings"></a>Specificare le impostazioni

Questa sezione definisce il contenuto da impostare nel dispositivo di destinazione o nei gemelli del modulo. Esistono due input per ogni set di impostazioni. Il primo è il percorso gemello, ovvero il percorso della sezione JSON all'interno delle proprietà desiderate gemelle che verranno impostate.  Il secondo è il contenuto JSON da inserire in questa sezione. 

Ad esempio, è possibile impostare il percorso gemello su e quindi fornire il contenuto JSON seguente:For example, you could set the twin path to `properties.desired.chiller-water` and then provide the following JSON content: 

```json
{
  "temperature": 66,
  "pressure": 28
}
```

![Impostare il percorso gemello e il contenuto](./media/iot-hub-automatic-device-management/module-config-twin-settings.png)


È inoltre possibile impostare singole impostazioni specificando l'intero percorso gemello e fornendo il valore senza parentesi quadre. Ad esempio, con `properties.desired.chiller-water.temperature`il percorso gemello , impostare il contenuto su `66`. Creare quindi una nuova impostazione gemella per la proprietà pressure. 

Se due o più configurazioni hanno come destinazione lo stesso percorso gemello, verrà applicato il contenuto della configurazione con priorità più alta (la priorità è definita nel passaggio 4).

Se si desidera rimuovere una proprietà esistente, `null`specificare il valore della proprietà su .

È possibile aggiungere ulteriori impostazioni selezionando **Aggiungi impostazione dispositivo gemello** o **Aggiungi modulo gemello impostazione**.

### <a name="specify-metrics-optional"></a>Specificare le metriche (facoltativo)

Le metriche forniscono conteggi riepilogativi dei vari stati che un dispositivo o un modulo può segnalare dopo l'applicazione del contenuto di configurazione. Ad esempio è possibile creare una metrica per le modifiche alle impostazioni in sospeso, una metrica per gli errori e una metrica per le modifiche alle impostazioni con esito positivo.

Ogni configurazione può avere fino a cinque metriche personalizzate. 

1. Immettere un nome per **Nome metrica**.

2. Immettere una query per **Metric Criteria** (Criteri metrica).  La query è basata sulle proprietà segnalate dei dispositivi gemelli.  La metrica rappresenta il numero di righe restituite dalla query.

Ad esempio:

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

È possibile includere una clausola per indicare che la configurazione è stata applicata, ad esempio: 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

Se si sta creando una metrica da `moduleId` `devices.modules`creare per creare report sui moduli configurati, selezionare da . Ad esempio:

```sql
SELECT deviceId, moduleId FROM devices.modules
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="target-devices"></a>Dispositivi di destinazione

Utilizzare la proprietà tags dei gemelli per scegliere come destinazione i dispositivi o i moduli specifici che devono ricevere questa configurazione. È inoltre possibile impostare come destinazione le proprietà segnalate di due gemelle.

Le configurazioni automatiche dei dispositivi possono essere destinate solo ai tag di dispositivo gemello e le configurazioni automatiche dei moduli possono essere destinate solo ai tag gemelli del modulo gemello. 

Poiché più configurazioni possono essere destinate allo stesso dispositivo o modulo, ogni configurazione richiede un numero di priorità. In caso di conflitto, prevale la configurazione con la priorità più alta. 

1. Immettere un numero intero positivo in **Priority** (Priorità) per la configurazione. Il valore numerico più alto indica la priorità più alta. Se due configurazioni hanno lo stesso numero di priorità, prevale quella creata più di recente. 

2. Immettere una **condizione di destinazione** per determinare quali dispositivi o moduli saranno destinati a questa configurazione. La condizione è basata su tag gemelli o proprietà riportanti gemelle e deve corrispondere al formato dell'espressione. 

   Per la configurazione automatica del dispositivo, è possibile specificare solo il tag o la proprietà segnalata di destinazione. Ad esempio, `tags.environment='test'` o `properties.reported.chillerProperties.model='4000x'`. È possibile specificare `*` per assegnare la configurazione a tutti i dispositivi. 
   
   Per la configurazione automatica dei moduli, usare una query per specificare i tag o le proprietà segnalate dai moduli registrati nell'hub IoT.For automatic module configuration, use a query to specify tags or reported properties from the modules registered to the IoT hub. Ad esempio, `from devices.modules where tags.environment='test'` o `from devices.modules where properties.reported.chillerProperties.model='4000x'`. Il carattere jolly non può essere utilizzato per tutti i moduli. 

3. Selezionare **Avanti** per procedere al passaggio finale.

### <a name="review-configuration"></a>Rivedere la configurazione

Rivedere le informazioni sulla configurazione e quindi selezionare **Submit** (Invia).

## <a name="monitor-a-configuration"></a>Monitorare una configurazione

Per visualizzare i dettagli di una configurazione e monitorare i dispositivi che la eseguono, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) passare all'hub IoT. 

2. Selezionare **IoT device configuration** (Configurazione del dispositivo IoT).

3. Esaminare l'elenco delle configurazioni. Per ogni configurazione è possibile visualizzare i dettagli seguenti:

   * **ID**: il nome della configurazione.

   * **Condizione di destinazione:** la query utilizzata per definire dispositivi o moduli di destinazione.

   * **Priority** (Priorità): il numero di priorità assegnato alla configurazione.

   * **Creation time** (Data/ora di creazione): il timestamp di creazione della configurazione. Questo timestamp viene usato per stabilire la prevalenza quando due configurazioni hanno la stessa priorità. 

   * **System metrics** (Metriche sistema): le metriche che vengono calcolate dall'hub IoT e che non possono essere personalizzate dagli sviluppatori. Assegnato: specifica il numero di dispositivi gemelli che soddisfano la condizione di destinazione. Si applica: il numero di dispositivi gemelli che sono stati modificati dalla configurazione, che può includere modifiche parziali nel caso in cui anche una configurazione separata con priorità più alta abbia apportato modifiche. 

   * **Metriche personalizzate:** metriche specificate dallo sviluppatore come query sulle proprietà segnalate gemelle.  È possibile definire fino a cinque metriche personalizzate per ogni configurazione. 
   
4. Selezionare la configurazione che si vuole monitorare.  

5. Esaminare i dettagli della configurazione. È possibile usare le schede per visualizzare dettagli specifici sui dispositivi che hanno ricevuto la configurazione.

   * **Condizione di destinazione:** i dispositivi o i moduli che corrispondono alla condizione di destinazione. 

   * **Metriche**: elenco delle metriche di sistema e personalizzate.  È possibile visualizzare un elenco di dispositivi o moduli conteggiati per ogni metrica selezionando la metrica nell'elenco a discesa e quindi selezionando **Visualizza dispositivi** o **Visualizza moduli**.

   * **Impostazioni Dispositivo Gemello** o **Impostazioni doppio modulo** - le impostazioni gemelle che vengono impostate dalla configurazione. 

   * **Configuration Labels** (Etichette configurazione): coppie chiave-valore usate per descrivere una configurazione.  Le etichette non impattano sulla funzionalità. 

## <a name="modify-a-configuration"></a>Modificare una configurazione

Quando si modifica una configurazione, le modifiche vengono replicate immediatamente in tutti i dispositivi o moduli di destinazione. 

Se si aggiorna la condizione di destinazione, vengono eseguiti gli aggiornamenti seguenti:

* Se un gemello non soddisfa la condizione di destinazione precedente, ma soddisfa la nuova condizione di destinazione e questa configurazione è la priorità più alta per tale gemello, viene applicata questa configurazione. 

* Se un gemello che esegue attualmente questa configurazione non soddisfa più la condizione di destinazione, le impostazioni della configurazione verranno rimosse e il gemello verrà modificato dalla successiva configurazione con priorità più alta. 

* Se un gemello che esegue attualmente questa configurazione non soddisfa più la condizione di destinazione e non soddisfa la condizione di destinazione di altre configurazioni, le impostazioni della configurazione verranno rimosse e non verranno apportate altre modifiche al server gemello. 

Per modificare una configurazione, seguire questa procedura: 

1. Nel [portale di Azure](https://portal.azure.com) passare all'hub IoT. 

2. Selezionare **IoT device configuration** (Configurazione del dispositivo IoT). 

3. Selezionare la configurazione che si vuole modificare. 

4. Apportare modifiche nei campi seguenti: 

   * Condizione di destinazione 
   * Etichette 
   * Priorità 
   * Metriche

4. Selezionare **Salva**.

5. Seguire la procedura descritta in [Monitorare una configurazione](#monitor-a-configuration) per controllare la distribuzione delle modifiche. 

## <a name="delete-a-configuration"></a>Eliminare una configurazione

Quando si elimina una configurazione, gli eventuali dispositivi gemelli ricevono la configurazione successiva con la priorità più alta. Se i dispositivi gemelli non soddisfano la condizione di destinazione di qualsiasi altra configurazione, non vengono applicate altre impostazioni. 

1. Nel [portale di Azure](https://portal.azure.com) passare all'hub IoT. 

2. Selezionare **IoT device configuration** (Configurazione del dispositivo IoT). 

3. Usare la casella di controllo per selezionare la configurazione che si vuole eliminare. 

4. Selezionare **Elimina**.

5. Verrà visualizzato un messaggio che richiede la conferma.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come configurare e monitorare i dispositivi IoT su larga scala. Per ulteriori informazioni sulla gestione dell'hub IoT di Azure, consultare questi collegamenti:

* [Gestire in blocco le identità dei dispositivi dell'hub IoT](iot-hub-bulk-identity-mgmt.md)
* [Metriche di Hub IoT](iot-hub-metrics.md)
* [Monitoraggio delle operazioni](iot-hub-operations-monitoring.md)

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per gli sviluppatori dell'hub IoT](iot-hub-devguide.md)
* [Distribuzione dell'intelligenza artificiale in dispositivi perimetrali con Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Per analizzare l'uso del servizio Device Provisioning dell'hub IoT per abilitare il provisioning automatico senza intervento umano, vedere: 

* [Servizio Device Provisioning in hub IoT di Azure](/azure/iot-dps)
