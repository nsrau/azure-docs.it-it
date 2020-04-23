---
title: Gestione automatica dei dispositivi su larga scala con l'hub Di Azure IoT (CLI) Documenti Microsoft
description: Usare le configurazioni automatiche dell'hub IoT di Azure per gestire più dispositivi o moduli IoTUse Azure IoT Hub automatic configurations to manage multiple IoT devices or modules
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.openlocfilehash: 60d0ef30a1c7d948a9e837a8bc37c76ace415545
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024966"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-cli"></a>Gestione automatica dei dispositivi e dei moduli IoT tramite l'interfaccia della riga di comando di AzureAutomatic IoT device and module management using the Azure CLI

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

La gestione automatica dei dispositivi nell'hub IoT di Azure automatizza molte delle attività ripetitive e complesse della gestione di flotte di dispositivi di grandi dimensioni. Con la gestione automatica dei dispositivi, è possibile scegliere come destinazione un set di dispositivi in base alle relative proprietà, definire una configurazione desiderata e quindi consentire all'hub IoT di aggiornare i dispositivi quando entrano nell'ambito. Questo aggiornamento viene eseguito utilizzando una _configurazione automatica_ del dispositivo o una configurazione automatica del _modulo,_ che consente di riepilogare il completamento e la conformità, gestire l'unione e i conflitti e implementare le configurazioni in un approccio graduale.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

La gestione automatica dei dispositivi funziona aggiornando un set di dispositivi gemelli o gemelli modulo con le proprietà desiderate e segnalando un riepilogo basato su proprietà segnalate gemelle.  Introduce una nuova classe e documento JSON denominato *una configurazione* che ha tre parti:It introduces a new class and JSON document called a Configuration that has three parts:

* La condizione di **destinazione** definisce l'ambito dei dispositivi gemelli o dei gemelli modulo da aggiornare. La condizione di destinazione viene specificata come query sui tag e/o le proprietà segnalate dei dispositivi gemelli.

* Il contenuto di destinazione definisce le proprietà desiderate da aggiungere o aggiornare nei dispositivi gemelli o nei due gemelli del modulo di **destinazione.** Il contenuto include un percorso della sezione delle proprietà desiderate da modificare.

* Le **metriche** definiscono i conteggi di riepilogo dei vari stati di configurazione, ad esempio **Success**, **In progress** ed **Error**. Le metriche personalizzate vengono specificate come query sulle proprietà segnalate gemelle.  Le metriche di sistema sono le metriche predefinite che misurano lo stato di aggiornamento gemello, ad esempio il numero di gemelli di destinazione e il numero di gemelli che sono stati aggiornati correttamente.

Le configurazioni automatiche vengono eseguite per la prima volta subito dopo la creazione della configurazione e quindi a intervalli di cinque minuti. Le query metriche vengono eseguite ogni volta che viene eseguita la configurazione automatica.

## <a name="cli-prerequisites"></a>Prerequisiti dell'interfaccia della riga di comando

* Un hub IoT nella sottoscrizione di Azure.An [IoT hub](../iot-hub/iot-hub-create-using-cli.md) in your Azure subscription. 

* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) nell'ambiente in uso. Come minimo, la versione dell'interfaccia della riga di comando di Azure deve essere 2.0.70 o superiore. Usare il comando `az –-version` per verificare. Questa versione supporta i comandi dell'estensione az e introduce il framework dei comandi Knack. 

* [L'estensione IoT per l'interfaccia della riga di comando](https://github.com/Azure/azure-cli)di Azure.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

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

## <a name="define-the-target-content-and-metrics"></a>Definire il contenuto di destinazione e le metriche

Le query di contenuto e metrica di destinazione vengono specificate come documenti JSON che descrivono le proprietà desiderate del dispositivo gemello o del modulo gemello per impostare e le proprietà segnalate da misurare.  Per creare una configurazione automatica usando l'interfaccia della riga di comando di Azure, salvare il contenuto e le metriche di destinazione in locale come file con estensione txt. Utilizzare i percorsi di file in una sezione successiva quando si esegue il comando per applicare la configurazione al dispositivo.

Ecco un esempio di contenuto di destinazione di base per una configurazione automatica del dispositivo:Here's a basic target content sample for an automatic device configuration:

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

Le configurazioni automatiche dei moduli `moduleContent` si `deviceContent`comportano in modo molto simile, ma si ha come destinazione invece di .

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

Anche le query di metrica per i moduli `moduleId` `devices.modules`sono simili alle query per i dispositivi, ma si seleziona per from . Ad esempio: 

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

* --**condizione-destinazione:** immettere una condizione di destinazione per determinare quali dispositivi o moduli saranno destinati a questa configurazione.Per la configurazione automatica del dispositivo, la condizione si basa sui tag del dispositivo gemello o sulle proprietà desiderate del dispositivo gemello e deve corrispondere al formato dell'espressione.Ad esempio, `tags.environment='test'` o `properties.desired.devicemodel='4000x'`.Per la configurazione automatica del modulo, la condizione si basa sui tag gemelli del modulo o sulle proprietà desiderate del modulo gemello. Ad esempio, `from devices.modules where tags.environment='test'` o `from devices.modules where properties.reported.chillerProperties.model='4000x'`.

* --**priority**: numero intero positivo. Nel caso in cui due o più configurazioni siano destinate allo stesso dispositivo o modulo, verrà applicata la configurazione con il valore numerico più alto per Priorità.

* --**metrics**: percorso del file per le query sulle metriche. Le metriche forniscono conteggi riepilogativi dei vari stati che un dispositivo o un modulo può segnalare dopo l'applicazione del contenuto di configurazione. Ad esempio è possibile creare una metrica per le modifiche alle impostazioni in sospeso, una metrica per gli errori e una metrica per le modifiche alle impostazioni con esito positivo. 

## <a name="monitor-a-configuration"></a>Monitorare una configurazione

Per visualizzare il contenuto di una configurazione, usare il comando seguente:

```azurecli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id**: il nome della configurazione che si trova nell'hub IoT.

* --**hub-name**: nome dell'hub IoT in cui si trova la configurazione. L'hub deve trovarsi nella sottoscrizione corrente. Per passare alla sottoscrizione desiderata, usare il comando `az account set -s [subscription name]`.

Esaminare la configurazione nella finestra di comando.La proprietà **metrics** visualizza un conteggio per ogni metrica valutata da ciascun hub:

* **targetedCount:** metrica di sistema che specifica il numero di dispositivi gemelli o gemelli del modulo nell'hub IoT che corrispondono alla condizione di targeting.

* **appliedCount:** una metrica di sistema specifica il numero di dispositivi o moduli a cui è stato applicato il contenuto di destinazione.appliedCount - A system metric specifies the number of devices or modules that have the target content applied.

* **Metriche personalizzate:** tutte le metriche definite sono metriche utente.

È possibile visualizzare un elenco di ID dispositivo, ID modulo o oggetti per ognuna delle metriche utilizzando il comando seguente:

```azurecli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id**: nome della distribuzione esistente nell'hub IoT.

* --**metric-id** - Il nome della metrica per la quale si desidera visualizzare l'elenco `appliedCount`degli ID dispositivo o degli ID modulo, ad esempio .

* --**hub-name**: nome dell'hub IoT in cui si trova la distribuzione. L'hub deve trovarsi nella sottoscrizione corrente. Per passare alla sottoscrizione desiderata, usare il comando `az account set -s [subscription name]`.

* --**metric-type**: il tipo di metrica può essere `system` o `user`.  Le metriche di sistema sono `targetedCount` e `appliedCount`. Tutte le altre metriche sono metriche dell'utente.

## <a name="modify-a-configuration"></a>Modificare una configurazione

Quando si modifica una configurazione, le modifiche vengono replicate immediatamente in tutti i dispositivi di destinazione. 

Se si aggiorna la condizione di destinazione, vengono eseguiti gli aggiornamenti seguenti:

* Se un gemello non soddisfa la condizione di destinazione precedente, ma soddisfa la nuova condizione di destinazione e questa configurazione è la priorità più alta per tale gemello, viene applicata questa configurazione. 

* Se un gemello che esegue attualmente questa configurazione non soddisfa più la condizione di destinazione, le impostazioni della configurazione verranno rimosse e il gemello verrà modificato dalla successiva configurazione con priorità più alta. 

* Se un gemello che esegue attualmente questa configurazione non soddisfa più la condizione di destinazione e non soddisfa la condizione di destinazione di altre configurazioni, le impostazioni della configurazione verranno rimosse e non verranno apportate altre modifiche al server gemello. 

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

Quando si elimina una configurazione, tutti i dispositivi gemelli o i gemelli del modulo assumono la successiva configurazione con la priorità più alta. Se i gemelli non soddisfano la condizione di destinazione di qualsiasi altra configurazione, non vengono applicate altre impostazioni. 

Per eliminare una configurazione, usare il comando seguente:

```azurecli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```

* --**config-id**: il nome della configurazione che si trova nell'hub IoT.

* --**hub-name**: nome dell'hub IoT in cui si trova la configurazione. L'hub deve trovarsi nella sottoscrizione corrente. Per passare alla sottoscrizione desiderata, usare il comando `az account set -s [subscription name]`.

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
