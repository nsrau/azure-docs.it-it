---
title: Gestione automatica dei dispositivi su larga scala con l'hub Azure (CLI) | Microsoft Docs
description: Usare le configurazioni automatiche dell'hub Azure per gestire più dispositivi o moduli
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.openlocfilehash: 60d0ef30a1c7d948a9e837a8bc37c76ace415545
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82024966"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-cli"></a>Gestione automatica di dispositivi IoT e moduli con l'interfaccia della riga di comando di Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

La gestione automatica dei dispositivi nell'hub IoT di Azure consente di automatizzare molte attività ripetitive e complesse di gestione di un numero elevato di dispositivi. La gestione automatica dei dispositivi consente di gestire un insieme di dispositivi in base alle proprietà, definire la configurazione desiderata e quindi permette all'hub IoT di aggiornare i dispositivi quando rientrano nell'ambito. Questo aggiornamento viene eseguito utilizzando una _configurazione automatica del dispositivo_ o una _configurazione automatica del modulo_, che consente di riepilogare il completamento e la conformità, gestire l'unione e i conflitti, nonché implementare le configurazioni con un approccio graduale.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

La gestione automatica dei dispositivi funziona aggiornando una serie di dispositivi gemelli o moduli gemelli con le proprietà desiderate e creando un riepilogo basato su proprietà gemellate riportate.  È stata introdotta una nuova classe e un documento JSON denominato *Configuration* che presenta tre parti:

* La **condizione di destinazione** definisce l'ambito dei dispositivi o dei moduli gemelli da aggiornare. La condizione di destinazione viene specificata come query sui tag e/o le proprietà segnalate dei dispositivi gemelli.

* Il **contenuto di destinazione** definisce le proprietà desiderate da aggiungere o aggiornare nei moduli o nei dispositivi gemelli di destinazione. Il contenuto include un percorso della sezione delle proprietà desiderate da modificare.

* Le **metriche** definiscono i conteggi di riepilogo dei vari stati di configurazione, ad esempio **Success**, **In progress** ed **Error**. Le metriche personalizzate vengono specificate come query sulle proprietà segnalate dei gemelli.  Le metriche di sistema sono metriche predefinite che misurano lo stato di aggiornamento dei gemelli, ad esempio il numero di dispositivi gemelli di destinazione e di quelli che sono stati aggiornati.

Le configurazioni automatiche vengono eseguite per la prima volta poco dopo la creazione della configurazione e quindi a intervalli di cinque minuti. Le query per le metriche vengono eseguite ogni volta che viene eseguita la configurazione automatica.

## <a name="cli-prerequisites"></a>Prerequisiti dell'interfaccia della riga di comando

* Un [hub IoT](../iot-hub/iot-hub-create-using-cli.md) nella sottoscrizione di Azure. 

* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) nell'ambiente in uso. La versione dell'interfaccia della riga di comando di Azure deve essere 2.0.70 o successiva. Usare il comando `az –-version` per verificare. Questa versione supporta i comandi dell'estensione az e introduce il framework dei comandi Knack. 

* [Estensione IoT per l'interfaccia della riga di comando di Azure](https://github.com/Azure/azure-cli).

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

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

## <a name="define-the-target-content-and-metrics"></a>Definire il contenuto di destinazione e le metriche

Il contenuto di destinazione e le query sulle metriche vengono specificati come documenti JSON che descrivono le proprietà desiderate del dispositivo gemello o del modulo gemello per impostare e segnalare le proprietà da misurare.  Per creare una configurazione automatica usando l'interfaccia della riga di comando di Azure, salvare il contenuto e le metriche di destinazione localmente come file con estensione txt. Usare i percorsi di file in una sezione successiva quando si esegue il comando per applicare la configurazione al dispositivo.

Di seguito è riportato un esempio di contenuto di base di destinazione per la configurazione automatica del dispositivo:

```json
{
  "content": {
    "deviceContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

Le configurazioni automatiche dei moduli si comportano in modo molto simile, ma è possibile usare `moduleContent` anziché `deviceContent` .

```json
{
  "content": {
    "moduleContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

Ecco alcuni esempi di query sulle metriche:

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

Anche le query di metrica per i moduli sono simili alle query per i dispositivi, ma si seleziona per `moduleId` da `devices.modules` . Ad esempio: 

```json
{
  "queries": {
    "Compliant": "select deviceId, moduleId from devices.module where configurations.[[chillermodulesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'"
  }
}
```

## <a name="create-a-configuration"></a>Creare una configurazione

Per configurare i dispositivi di destinazione si crea una configurazione costituita da contenuto di destinazione e metriche. 

Usare il comando seguente per creare una configurazione:

```azurecli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-id**: il nome della configurazione che verrà creata nell'hub IoT. Assegnare alla configurazione un nome univoco con un massimo di 128 lettere in minuscolo. Evitare gli spazi e i seguenti caratteri non validi: `& ^ [ ] { } \ | " < > /`.

* --**labels** : aggiungere etichette per tenere traccia della configurazione. Le etichette sono coppie nome-valore che descrivono la distribuzione. Ad esempio, `HostPlatform, Linux` o `Version, 3.0.1`

* --**content**: JSON inline o percorso del file per il contenuto di destinazione da impostare come proprietà desiderate del gemello. 

* --**hub-name**: nome dell'hub IoT in cui verrà creata la configurazione. L'hub deve trovarsi nella sottoscrizione corrente. Per passare alla sottoscrizione desiderata, usare il comando `az account set -s [subscription name]`.

* --**target-condition** : immettere una condizione di destinazione per determinare quali dispositivi o moduli verranno assegnati a questa configurazione.Per la configurazione automatica del dispositivo, la condizione è basata sui tag del dispositivo gemello o sulle proprietà desiderate del dispositivo gemello e deve corrispondere al formato dell'espressione.Ad esempio, `tags.environment='test'` o `properties.desired.devicemodel='4000x'`.Per la configurazione automatica dei moduli, la condizione è basata sui tag del modulo gemello o sulle proprietà desiderate del modulo gemello. Ad esempio, `from devices.modules where tags.environment='test'` o `from devices.modules where properties.reported.chillerProperties.model='4000x'`.

* --**Priority** : numero intero positivo. Nel caso in cui due o più configurazioni siano destinate allo stesso dispositivo o modulo, verrà applicata la configurazione con il valore numerico più elevato per Priority.

* --**metrics**: percorso del file per le query sulle metriche. Le metriche forniscono i conteggi di riepilogo dei diversi stati che un dispositivo o un modulo possono segnalare dopo l'applicazione del contenuto della configurazione. Ad esempio è possibile creare una metrica per le modifiche alle impostazioni in sospeso, una metrica per gli errori e una metrica per le modifiche alle impostazioni con esito positivo. 

## <a name="monitor-a-configuration"></a>Monitorare una configurazione

Per visualizzare il contenuto di una configurazione, usare il comando seguente:

```azurecli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id**: il nome della configurazione che si trova nell'hub IoT.

* --**hub-name**: nome dell'hub IoT in cui si trova la configurazione. L'hub deve trovarsi nella sottoscrizione corrente. Per passare alla sottoscrizione desiderata, usare il comando `az account set -s [subscription name]`.

Esaminare la configurazione nella finestra di comando. La proprietà **metrics** visualizza un conteggio per ogni metrica valutata da ciascun hub:

* **targetedCount** : metrica di sistema che specifica il numero di dispositivi gemelli o moduli gemelli nell'hub degli elementi che soddisfano la condizione di destinazione.

* **appliedCount** : una metrica di sistema specifica il numero di dispositivi o moduli a cui è stato applicato il contenuto di destinazione.

* Metrica **personalizzata** : le metriche definite sono metriche utente.

È possibile visualizzare un elenco di ID dispositivo, ID modulo o oggetti per ogni metrica usando il comando seguente:

```azurecli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id**: nome della distribuzione esistente nell'hub IoT.

* --**metrica-ID** : nome della metrica per cui si vuole visualizzare l'elenco di ID dispositivo o ID modulo, ad esempio `appliedCount` .

* --**nome-Hub** -nome dell'hub Internet delle cose in cui è presente la distribuzione. L'hub deve trovarsi nella sottoscrizione corrente. Per passare alla sottoscrizione desiderata, usare il comando `az account set -s [subscription name]`.

* --**metric-type**: il tipo di metrica può essere `system` o `user`.  Le metriche di sistema sono `targetedCount` e `appliedCount`. Tutte le altre metriche sono metriche dell'utente.

## <a name="modify-a-configuration"></a>Modificare una configurazione

Quando si modifica una configurazione, le modifiche vengono replicate immediatamente in tutti i dispositivi di destinazione. 

Se si aggiorna la condizione di destinazione, vengono eseguiti gli aggiornamenti seguenti:

* Se un gemello non soddisfa la condizione di destinazione precedente, ma soddisfa la nuova condizione di destinazione e questa configurazione ha la priorità più alta per il gemello, viene applicata questa configurazione. 

* Se un gemello che attualmente esegue la configurazione non soddisfa più la condizione di destinazione, verranno rimosse le impostazioni dalla configurazione e il gemello verrà modificato dalla configurazione successiva con la priorità più alta. 

* Se un gemello che attualmente esegue questa configurazione non soddisfa più la condizione di destinazione e non soddisfa la condizione di destinazione delle altre configurazioni, le impostazioni vengono rimosse dalla configurazione e non verranno apportate altre modifiche al gemello. 

Per aggiornare una configurazione, usare il comando seguente:

```azurecli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-id**: il nome della configurazione che si trova nell'hub IoT.

* --**hub-name**: nome dell'hub IoT in cui si trova la configurazione. L'hub deve trovarsi nella sottoscrizione corrente. Per passare alla sottoscrizione desiderata, usare il comando `az account set -s [subscription name]`.

* --**set**: aggiornare una proprietà nella configurazione. È possibile aggiornare le proprietà seguenti:

    * targetCondition, ad esempio `targetCondition=tags.location.state='Oregon'`

    * Etichette 

    * priority

## <a name="delete-a-configuration"></a>Eliminare una configurazione

Quando si elimina una configurazione, i dispositivi gemelli o i moduli gemelli accettano la successiva configurazione con priorità più elevata. Se i dispositivi gemelli non soddisfano la condizione di destinazione di qualsiasi altra configurazione, non vengono applicate altre impostazioni. 

Per eliminare una configurazione, usare il comando seguente:

```azurecli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```

* --**config-id**: il nome della configurazione che si trova nell'hub IoT.

* --**hub-name**: nome dell'hub IoT in cui si trova la configurazione. L'hub deve trovarsi nella sottoscrizione corrente. Per passare alla sottoscrizione desiderata, usare il comando `az account set -s [subscription name]`.

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
