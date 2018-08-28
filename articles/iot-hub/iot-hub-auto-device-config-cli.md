---
title: Configurare e monitorare i dispositivi IoT su larga scala con l'hub IoT di Azure (interfaccia della riga di comando) | Microsoft Docs
description: Usare le configurazioni automatiche dei dispositivi dell'hub IoT di Azure per assegnare una configurazione a più dispositivi
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: chrisgre
ms.openlocfilehash: f81ef3c231874f314d6fe023ba247a0bcff61e90
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2018
ms.locfileid: "42144861"
---
# <a name="configure-and-monitor-iot-devices-at-scale-using-the-azure-cli"></a>Configurare e monitorare i dispositivi IoT su larga scala tramite l'interfaccia della riga di comando di Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

La gestione automatica dei dispositivi nell'hub IoT di Azure consente di automatizzare molte attività ripetitive e complesse di gestione di un numero elevato di dispositivi nel loro intero ciclo di vita. Con la gestione automatica dei dispositivi è possibile assegnare come destinazione un insieme di dispositivi in base alle proprietà, definire la configurazione desiderata e consentire all'hub IoT di aggiornare i dispositivi ogni volta che entrano nell'ambito.  Questo risultato viene ottenuto usando la configurazione automatica dei dispositivi, che consente anche di fornire un riepilogo del completamento e della conformità, di gestire l'unione e i conflitti e di distribuire le configurazioni in fasi.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Le configurazioni automatiche dei dispositivi funzionano aggiornando un insieme di dispositivi gemelli con le proprietà desiderate e segnalando un riepilogo in base alle proprietà segnalate dei dispositivi gemelli.  Introduce una nuova classe e un documento JSON denominato *Configuration* che presenta tre parti:

* La **condizione di destinazione** definisce l'ambito dei dispositivi gemelli da aggiornare. La condizione di destinazione viene specificata come query sui tag e/o le proprietà segnalate dei dispositivi gemelli.

* Il **contenuto di destinazione** definisce le proprietà desiderate da aggiungere o aggiornare nei dispositivi gemelli di destinazione. Il contenuto include un percorso della sezione delle proprietà desiderate da modificare.

* Le **metriche** definiscono i conteggi di riepilogo dei vari stati di configurazione, ad esempio **Success**, **In progress** ed **Error**. Le metriche personalizzate vengono specificate come query sulle proprietà segnalate dei dispositivi gemelli.  Le metriche di sistema sono metriche predefinite che misurano lo stato di aggiornamento dei dispositivi gemelli, ad esempio il numero di dispositivi gemelli di destinazione e di quelli che sono stati aggiornati. 

## <a name="cli-prerequisites"></a>Prerequisiti dell'interfaccia della riga di comando

* Un [hub IoT](../iot-hub/iot-hub-create-using-cli.md) nella sottoscrizione di Azure. 
* [Interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) nell'ambiente. La versione dell'interfaccia della riga di comando di Azure 2.0 deve essere 2.0.24 o successiva. Usare `az –-version` per la convalida. Questa versione supporta i comandi dell'estensione az e introduce il framework dei comandi Knack. 
* L'[estensione IoT per l'interfaccia della riga di comando di Azure 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="implement-device-twins-to-configure-devices"></a>Implementare i dispositivi gemelli per configurare i dispositivi

Le configurazioni automatiche dei dispositivi richiedono l'uso di dispositivi gemelli per la sincronizzazione dello stato tra il cloud e i dispositivi.  Per indicazioni sull'uso di dispositivi gemelli, vedere [Comprendere e usare dispositivi gemelli nell'hub IoT](iot-hub-devguide-device-twins.md).

## <a name="identify-devices-using-tags"></a>Identificare i dispositivi tramite tag

Prima di poter creare una configurazione, è necessario specificare i dispositivi a cui si vuole applicare. L'hub IoT di Azure identifica i dispositivi usando i tag nel dispositivo gemello. Ogni dispositivo può avere più tag ed è possibile definirli in qualsiasi modo risulti appropriato per una soluzione specifica. Ad esempio, se si gestiscono i dispositivi in posizioni diverse, è possibile aggiungere i tag seguenti a un dispositivo gemello:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>Definire il contenuto di destinazione e le metriche

Il contenuto di destinazione e le query sulle metriche vengono specificati come documenti JSON che descrivono le proprietà desiderate del dispositivo gemello da impostare e le proprietà restituite da misurare.  Per creare una configurazione automatica del dispositivo con l'interfaccia della riga di comando di Azure 2.0, salvare il contenuto di destinazione e le metriche in locale come file con estensione txt. I percorsi dei file verranno usati in una sezione successiva quando si esegue il comando per applicare la configurazione al dispositivo. 

Ecco un esempio di contenuto di destinazione di base:

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

Ecco alcuni esempi di query sulle metriche:

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

## <a name="create-a-configuration"></a>Creare una configurazione

Per configurare i dispositivi di destinazione si crea una configurazione costituita da contenuto di destinazione e metriche. 

Usare il comando seguente per creare una configurazione:

```cli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-id**: il nome della configurazione che verrà creata nell'hub IoT. Assegnare alla configurazione un nome univoco con un massimo di 128 lettere in minuscolo. Evitare gli spazi e i seguenti caratteri non validi: `& ^ [ ] { } \ | " < > /`.

* --**labels** : aggiungere etichette per tenere traccia della configurazione. Le etichette sono coppie nome-valore che descrivono la distribuzione. Ad esempio, `HostPlatform, Linux` o `Version, 3.0.1`

* --**content**: JSON inline o percorso del file per il contenuto di destinazione da impostare come proprietà desiderate del gemello. 

* --**hub-name**: nome dell'hub IoT in cui verrà creata la configurazione. L'hub deve trovarsi nella sottoscrizione corrente. Per passare alla sottoscrizione desiderata, usare il comando `az account set -s [subscription name]`.

* --**target-condition**: immettere una condizione di destinazione per determinare i dispositivi di destinazione di questa configurazione. La condizione è basata sui tag o sulle proprietà desiderate dei dispositivi gemelli e deve corrispondere al formato dell'espressione. Ad esempio, `tags.environment='test'` o `properties.desired.devicemodel='4000x'`. 

* --**priority**: numero intero positivo. Nel caso in cui due o più configurazioni abbiano lo stesso dispositivo di destinazione, verrà applicata quella con valore di priorità più alto.

* --**metrics**: percorso del file per le query sulle metriche. Le metriche forniscono i conteggi di riepilogo dei diversi stati che un dispositivo può segnalare dopo l'applicazione del contenuto della configurazione. Ad esempio è possibile creare una metrica per le modifiche alle impostazioni in sospeso, una metrica per gli errori e una metrica per le modifiche alle impostazioni con esito positivo. 

## <a name="monitor-a-configuration"></a>Monitorare una configurazione

Per visualizzare il contenuto di una configurazione, usare il comando seguente:

```cli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id**: il nome della configurazione che si trova nell'hub IoT.

* --**hub-name**: nome dell'hub IoT in cui si trova la configurazione. L'hub deve trovarsi nella sottoscrizione corrente. Per passare alla sottoscrizione desiderata, usare il comando `az account set -s [subscription name]`.

Esaminare la configurazione nella finestra di comando. La proprietà **metrics** visualizza un conteggio per ogni metrica valutata da ciascun hub:

* **targetedCount**: metrica di sistema che specifica il numero di dispositivi gemelli presenti nell'hub IoT che corrispondono alla condizione di destinazione.

* **appliedCount**: metrica di sistema che specifica il numero di dispositivi a cui è stato applicato il contenuto di destinazione.

* **metrica personalizzata**: qualsiasi metrica definita in modo personalizzato verrà considerata metrica dell'utente.

È possibile visualizzare un elenco di oggetti o ID dispositivo per ognuna delle metriche tramite il comando seguente:

```cli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id**: nome della distribuzione esistente nell'hub IoT.

* --**metric-id**: nome della metrica per la quale si vuole visualizzare l'elenco di ID dispositivo, ad esempio `appliedCount`.

* --**hub-name**: nome dell'hub IoT in cui si trova la distribuzione. L'hub deve trovarsi nella sottoscrizione corrente. Per passare alla sottoscrizione desiderata, usare il comando `az account set -s [subscription name]`.

* --**metric-type**: il tipo di metrica può essere `system` o `user`.  Le metriche di sistema sono `targetedCount` e `appliedCount`. Tutte le altre metriche sono metriche dell'utente.

## <a name="modify-a-configuration"></a>Modificare una configurazione

Quando si modifica una configurazione, le modifiche vengono replicate immediatamente in tutti i dispositivi di destinazione. 

Se si aggiorna la condizione di destinazione, vengono eseguiti gli aggiornamenti seguenti:

* Se un dispositivo gemello non soddisfa la condizione di destinazione precedente, ma soddisfa la nuova condizione di destinazione e questa configurazione ha la priorità più alta per il dispositivo gemello, questa configurazione viene applicata al dispositivo. 

* Se un dispositivo gemello non soddisfa più la condizione di destinazione, verranno rimosse le impostazioni dalla configurazione e il dispositivo gemello verrà modificato dalla configurazione successiva con la priorità più alta. 

* Se un dispositivo gemello che attualmente esegue questa configurazione non soddisfa più la condizione di destinazione e non soddisfa la condizione di destinazione delle altre configurazioni, le impostazioni vengono rimosse dalla configurazione e non verranno apportate altre modifiche al dispositivo gemello. 

Per aggiornare una configurazione, usare il comando seguente:

```cli
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

Quando si elimina una configurazione, gli eventuali dispositivi gemelli ricevono la configurazione successiva con la priorità più alta. Se i dispositivi gemelli non soddisfano la condizione di destinazione di qualsiasi altra configurazione, non vengono applicate altre impostazioni. 

Per eliminare una configurazione, usare il comando seguente:

```cli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```
* --**config-id**: il nome della configurazione che si trova nell'hub IoT.

* --**hub-name**: nome dell'hub IoT in cui si trova la configurazione. L'hub deve trovarsi nella sottoscrizione corrente. Per passare alla sottoscrizione desiderata, usare il comando `az account set -s [subscription name]`.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato appreso come configurare e monitorare i dispositivi IoT su larga scala. Per ulteriori informazioni sulla gestione dell'hub IoT di Azure, consultare questi collegamenti:

* [Gestire in blocco le identità dei dispositivi dell'hub IoT](iot-hub-bulk-identity-mgmt.md)
* [Metriche di Hub IoT](iot-hub-metrics.md)
* [Monitoraggio delle operazioni](iot-hub-operations-monitoring.md)

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md)
* [Distribuzione dell'intelligenza artificiale in dispositivi perimetrali con Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Per analizzare l'uso del servizio Device Provisioning dell'hub IoT per abilitare il provisioning automatico senza intervento umano, vedere: 

* [Servizio Device Provisioning in hub IoT di Azure](/azure/iot-dps)
