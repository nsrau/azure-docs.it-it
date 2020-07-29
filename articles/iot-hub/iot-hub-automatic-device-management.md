---
title: Gestione automatica dei dispositivi su larga scala con l'hub IoT di Azure | Microsoft Docs
description: Utilizzo delle configurazioni automatiche dell’hub IoT di Azure per gestire più dispositivi e moduli IoT
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 6d3661128008c13e5d4d459f6f8e7925aa18a9a4
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87322772"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-portal"></a>Gestione automatica di dispositivi e moduli IoT con il portale di Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

La gestione automatica dei dispositivi nell'hub IoT di Azure consente di automatizzare molte attività ripetitive e complesse di gestione di un numero elevato di dispositivi. La gestione automatica dei dispositivi consente di gestire un insieme di dispositivi in base alle proprietà, definire la configurazione desiderata e quindi permette all'hub IoT di aggiornare i dispositivi quando rientrano nell'ambito. Questo aggiornamento viene eseguito utilizzando una _configurazione automatica del dispositivo_ o una _configurazione automatica del modulo_, che consente di riepilogare il completamento e la conformità, gestire l'unione e i conflitti, nonché implementare le configurazioni con un approccio graduale.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

La gestione automatica dei dispositivi funziona aggiornando una serie di dispositivi gemelli o moduli gemelli con le proprietà desiderate e creando un riepilogo basato su proprietà gemellate riportate.  È stata introdotta una nuova classe e un documento JSON denominato *Configuration* che presenta tre parti:

* La **condizione di destinazione** definisce l'ambito dei dispositivi o dei moduli gemelli da aggiornare. La condizione di destinazione viene specificata come query sui tag e/o le proprietà segnalate gemelli.

* Il **contenuto di destinazione** definisce le proprietà desiderate da aggiungere o aggiornare nei moduli o nei dispositivi gemelli di destinazione. Il contenuto include un percorso della sezione delle proprietà desiderate da modificare.

* Le **metriche** definiscono i conteggi di riepilogo dei vari stati di configurazione, ad esempio **Success**, **In progress** ed **Error**. Le metriche personalizzate vengono specificate come query sulle proprietà segnalate dei gemelli.  Le metriche di sistema sono metriche predefinite che misurano lo stato di aggiornamento dei gemelli, ad esempio il numero di dispositivi gemelli di destinazione e di quelli che sono stati aggiornati.

Le configurazioni automatiche vengono eseguite per la prima volta poco dopo la creazione della configurazione e quindi a intervalli di cinque minuti. Le query per le metriche vengono eseguite ogni volta che viene eseguita la configurazione automatica.

## <a name="implement-twins"></a>Implementare i dispositivi o i moduli gemelli

Le configurazioni automatiche dei dispositivi richiedono l'uso di dispositivi gemelli per la sincronizzazione dello stato tra il cloud e i dispositivi.  Per altre informazioni, vedere [Comprendere e usare dispositivi gemelli nell'hub IoT](iot-hub-devguide-device-twins.md).

Le configurazioni automatiche dei dispositivi richiedono l'uso di moduli gemelli per la sincronizzazione dello stato tra il cloud e i moduli. Per altre informazioni, vedere [Comprendere e usare moduli gemelli nell'hub IoT](iot-hub-devguide-module-twins.md).

## <a name="use-tags-to-target-twins"></a>Usare i tag per individuare i gemelli

Prima di creare una configurazione, è necessario specificare i dispositivi o moduli a cui applicarla. L'hub IoT di Azure identifica i dispositivi, quindi usa i tag nel dispositivo gemello per identificare i moduli usando i tag nel modulo gemello. Ogni dispositivo o modulo può avere più tag ed è possibile definirli in qualsiasi modo risulti appropriato per una soluzione specifica. Ad esempio, se si gestiscono i dispositivi in posizioni diverse, aggiungere i tag seguenti a un dispositivo gemello:

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

3. Selezionare **Aggiungi configurazione dispositivo** o **Aggiungi configurazione modulo**.

   ![Aggiungere la configurazione del dispositivo o la configurazione del modulo](./media/iot-hub-automatic-device-management/create-automatic-configuration.png)

La creazione di una configurazione prevede cinque passaggi, illustrati nelle sezioni seguenti. 

### <a name="name-and-label"></a>Nome ed etichetta

1. Assegnare alla configurazione un nome univoco con un massimo di 128 lettere in minuscolo. Evitare gli spazi e i seguenti caratteri non validi: `& ^ [ ] { } \ | " < > /`.

2. Aggiungere etichette per tenere traccia delle configurazioni. Le etichette sono coppie di **Nome** e **Valore** che descrivono la configurazione. Ad esempio, `HostPlatform, Linux` o `Version, 3.0.1`.

3. Selezionare **Avanti** per procedere al passaggio successivo. 

### <a name="specify-settings"></a>Specificare le impostazioni

Questa sezione specifica il contenuto da impostare nei dispositivi o moduli gemelli di destinazione. Esistono due input per ogni set di impostazioni. Il primo è il percorso del gemello, ovvero il percorso della sezione JSON all'interno delle proprietà desiderate del dispositivo gemello che verranno impostate.  Il secondo è il contenuto JSON da inserire in questa sezione. 

Ad esempio, è possibile impostare il percorso del gemello su `properties.desired.chiller-water` e quindi fornire il contenuto JSON seguente: 

```json
{
  "temperature": 66,
  "pressure": 28
}
```

![Impostare il percorso e il contenuto dei gemelli](./media/iot-hub-automatic-device-management/module-config-twin-settings.png)


È possibile anche definire singole impostazioni specificando il percorso completo del gemello e il valore senza parentesi quadre. Ad esempio, con il percorso gemello `properties.desired.chiller-water.temperature` impostare il contenuto su `66`. Quindi, creare una nuova impostazione del gemello per la proprietà pressure. 

Se due o più configurazioni assegnano come destinazione lo stesso percorso del gemello, verrà applicato il contenuto dalla configurazione con priorità più alta (la priorità viene definita nel passaggio 4).

Se si vuole rimuovere una proprietà esistente, specificare il valore della proprietà su `null`.

È possibile aggiungere altre impostazioni selezionando **Aggiungi impostazione dispositivo gemello** **Aggiungi impostazione modulo gemello**.

### <a name="specify-metrics-optional"></a>Specificare le metriche (facoltativo)

Le metriche forniscono i conteggi di riepilogo dei diversi stati che un dispositivo o un modulo possono segnalare dopo l'applicazione del contenuto della configurazione. Ad esempio è possibile creare una metrica per le modifiche alle impostazioni in sospeso, una metrica per gli errori e una metrica per le modifiche alle impostazioni con esito positivo.

Ogni configurazione può avere fino a cinque metriche personalizzate. 

1. Immettere un nome per **Nome della metrica**

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

Se si sta compilando una metrica per il report dei moduli configurati, selezionare `moduleId` da `devices.modules`. Ad esempio:

```sql
SELECT deviceId, moduleId FROM devices.modules
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="target-devices"></a>Dispositivi di destinazione

Usare la proprietà tags dai gemelli per selezionare i dispositivi o moduli specifici che devono ricevere la configurazione. È possibile anche assegnare i gemelli di destinazione in base alle proprietà segnalate.

Le configurazioni automatiche dei dispositivi possono essere destinate solo ai tag del dispositivo gemello e le configurazioni automatiche dei moduli possono essere destinate solo ai tag del modulo gemello. 

Dato che più configurazioni possono avere come destinazione lo stesso dispositivo o modulo, è necessario assegnare a ogni configurazione un numero di priorità. In caso di conflitto, prevale la configurazione con la priorità più alta. 

1. Immettere un numero intero positivo in **Priority** (Priorità) per la configurazione. Il valore numerico più alto indica la priorità più alta. Se due configurazioni hanno lo stesso numero di priorità, prevale quella creata più di recente. 

2. Specificare una condizione in **Target condition** (Condizione di destinazione) per determinare i dispositivi o i moduli di destinazione di questa configurazione. La condizione è basata sui tag o sulle proprietà segnalate dei gemelli e deve corrispondere al formato di espressione. 

   Per la configurazione automatica del dispositivo, è possibile specificare solo il tag o la proprietà segnalati come destinazione. Ad esempio, `tags.environment='test'` o `properties.reported.chillerProperties.model='4000x'`. È possibile specificare `*` per assegnare la configurazione a tutti i dispositivi. 
   
   Per la configurazione automatica dei moduli, usare una query per specificare i tag o le proprietà segnalati dai moduli registrati nell'hub. Ad esempio, `from devices.modules where tags.environment='test'` o `from devices.modules where properties.reported.chillerProperties.model='4000x'`. Non è possibile usare il carattere jolly per specificare come destinazione tutti i moduli. 

3. Selezionare **Avanti** per procedere al passaggio finale.

### <a name="review-configuration"></a>Rivedere la configurazione

Rivedere le informazioni sulla configurazione e quindi selezionare **Submit** (Invia).

## <a name="monitor-a-configuration"></a>Monitorare una configurazione

Per visualizzare i dettagli di una configurazione e monitorare i dispositivi che la eseguono, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) passare all'hub IoT. 

2. Selezionare **IoT device configuration** (Configurazione del dispositivo IoT).

3. Esaminare l'elenco delle configurazioni. Per ogni configurazione è possibile visualizzare i dettagli seguenti:

   * **ID**: il nome della configurazione.

   * **Target condition** (Condizione di destinazione): la query usata per definire i dispositivi o moduli di destinazione.

   * **Priority** (Priorità): il numero di priorità assegnato alla configurazione.

   * **Creation time** (Data/ora di creazione): il timestamp di creazione della configurazione. Questo timestamp viene usato per stabilire la prevalenza quando due configurazioni hanno la stessa priorità. 

   * **System metrics** (Metriche sistema): le metriche che vengono calcolate dall'hub IoT e che non possono essere personalizzate dagli sviluppatori. Assegnato: specifica il numero di dispositivi gemelli che soddisfano la condizione di destinazione. Si applica: il numero di dispositivi gemelli che sono stati modificati dalla configurazione, che può includere modifiche parziali nel caso in cui anche una configurazione separata con priorità più alta abbia apportato modifiche. 

   * **Metriche personalizzate**: metriche specificate dallo sviluppatore come query sulle proprietà segnalate dei gemelli.  È possibile definire fino a cinque metriche personalizzate per ogni configurazione. 
   
4. Selezionare la configurazione che si vuole monitorare.  

5. Esaminare i dettagli della configurazione. È possibile usare le schede per visualizzare dettagli specifici sui dispositivi che hanno ricevuto la configurazione.

   * **Condizione di destinazione**: dispositivi o moduli che soddisfano la condizione di destinazione. 

   * **Metriche**: elenco delle metriche di sistema e personalizzate.  È possibile visualizzare un elenco di dispositivi o moduli che vengono conteggiati per ogni metrica selezionando la metrica nell'elenco a discesa e quindi selezionando **View Devices** (Visualizza dispositivi) o **View Modules** (Visualizza moduli).

   * **Device Twin Settings** (Impostazioni dispositivi gemelli) o **Module Twin Settings** (Impostazioni moduli gemelli): le impostazioni dei gemelli specificate dalla configurazione. 

   * **Configuration Labels** (Etichette configurazione): coppie chiave-valore usate per descrivere una configurazione.  Le etichette non impattano sulla funzionalità. 

## <a name="modify-a-configuration"></a>Modificare una configurazione

Quando si modifica una configurazione, le modifiche vengono replicate immediatamente in tutti i dispositivi o moduli di destinazione. 

Se si aggiorna la condizione di destinazione, vengono eseguiti gli aggiornamenti seguenti:

* Se un gemello non soddisfa la condizione di destinazione precedente, ma soddisfa la nuova condizione di destinazione e questa configurazione ha la priorità più alta per il gemello, viene applicata questa configurazione. 

* Se un gemello che attualmente esegue la configurazione non soddisfa più la condizione di destinazione, verranno rimosse le impostazioni dalla configurazione e il gemello verrà modificato dalla configurazione successiva con la priorità più alta. 

* Se un gemello che attualmente esegue questa configurazione non soddisfa più la condizione di destinazione e non soddisfa la condizione di destinazione delle altre configurazioni, le impostazioni vengono rimosse dalla configurazione e non verranno apportate altre modifiche al gemello. 

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

In questo articolo si è appreso come configurare e monitorare i dispositivi IoT su larga scala. Per ulteriori informazioni sulla gestione dell'hub IoT di Azure, consultare questi collegamenti:

* [Gestire in blocco le identità dei dispositivi dell'hub IoT](iot-hub-bulk-identity-mgmt.md)
* [Metriche di hub IoT](iot-hub-metrics.md)
* [Monitoraggio delle operazioni](iot-hub-operations-monitoring.md)

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md)
* [Distribuzione dell'intelligenza artificiale in dispositivi perimetrali con Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Per analizzare l'uso del servizio Device Provisioning dell'hub IoT per abilitare il provisioning automatico senza intervento umano, vedere: 

* [Servizio Device Provisioning in hub IoT di Azure](/azure/iot-dps)
