---
title: Distribuzione automatica di gruppi di dispositivi - Azure IoT Edge | Microsoft Docs
description: Usare le distribuzioni automatiche in Azure IoT Edge per gestire gruppi di dispositivi basati su tag condivisi
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8aaac6100ba980301ff3e85a3ac3959bfee89b49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76895973"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Informazioni sulle distribuzioni automatiche IoT Edge per singoli dispositivi o su vasta scala

Le distribuzioni automatiche e la distribuzione a più livelli consentono di gestire e configurare i moduli in un numero elevato di dispositivi IoT Edge.Automatic deployments and layered deployment help you manage and configure modules on large numbers of IoT Edge devices.

Azure IoT Edge offre due modi per configurare i moduli per l'esecuzione nei dispositivi Perimetrali IoT.Azure IoT Edge provides two ways to configure the modules to run on IoT Edge devices. Il primo metodo consiste nel distribuire i moduli in base al dispositivo. Creare un manifesto di distribuzione e quindi applicarlo a un particolare dispositivo in base al nome. Il secondo metodo consiste nel distribuire automaticamente i moduli in qualsiasi dispositivo registrato che soddisfi un set di condizioni definite. Creare un manifesto di distribuzione e quindi definire i dispositivi a cui si applica in base ai [tag](../iot-edge/how-to-deploy-monitor.md#identify-devices-using-tags) nel dispositivo gemello.

Questo articolo è incentrato sulla configurazione e il monitoraggio di flotte di dispositivi, definite collettivamente *distribuzioni automatiche di IoT Edge*.I passaggi di distribuzione di base sono i seguenti:The basic deployment steps are as follows:

1. Un operatore definisce una distribuzione che descrive un set di moduli e i dispositivi di destinazione.Per ogni distribuzione esiste un manifesto della distribuzione che riporta queste informazioni.
2. Il servizio Hub IoT comunica con tutti i dispositivi di destinazione per configurarli con i moduli dichiarati.
3. Il servizio Hub IoT recupera informazioni sullo stato dai dispositivi IoT Edge e li rende disponibili all’operatore.Ad esempio, un operatore può vedere quando un dispositivo Edge non è configurato correttamente o se un modulo non riesce durante il runtime.
4. In qualsiasi momento, i nuovi dispositivi IoT Edge che soddisfano le condizioni di destinazione vengono configurati per la distribuzione.

Questo articolo descrive ogni componente coinvolto nella configurazione e nel monitoraggio di una distribuzione. Per istruzioni dettagliate sulla creazione e l'aggiornamento di una distribuzione, vedere [Distribuire e monitorare i moduli di IoT Edge su larga scala](how-to-deploy-monitor.md).

## <a name="deployment"></a>Distribuzione

Una distribuzione automatica IoT Edge assegna immagini di moduli IoT Edge per l'esecuzione come istanze in un set di destinazione di dispositivi IoT Edge. Il funzionamento è basato sulla configurazione di un manifesto per la distribuzione IoT Edge, che include un elenco di moduli con i parametri di inizializzazione corrispondenti.Una distribuzione può essere assegnata a un singolo dispositivo (in base al relativo ID) o a un gruppo di dispositivi (in base ai tag).Quando un dispositivo IoT Edge riceve un manifesto di distribuzione, scarica e installa le immagini dei contenitori dai repository corrispondenti, quindi le configura come specificato.Dopo aver creato una distribuzione, un operatore può monitorare lo stato di distribuzione per verificare se i dispositivi di destinazione sono configurati correttamente.

Solo i dispositivi IoT Edge possono essere configurati con una distribuzione. Prima di poter ricevere la distribuzione, il dispositivo deve soddisfare i prerequisiti seguenti:

* Sistema operativo di base
* Sistema di gestione dei contenitori, come Moby o Docker
* Provisioning del runtime IoT Edge

### <a name="deployment-manifest"></a>Manifesto della distribuzione

Un manifesto di distribuzione è un documento JSON che descrive i moduli da configurare nei dispositivi IoT Edge di destinazione e contiene i metadati di configurazione per tutti i moduli, inclusi i moduli di sistema richiesti, in particolare l'agente IoT Edge e l'hub IoT Edge.  

I metadati di configurazione per ogni modulo includono:

* Versione
* Type
* Stato (ad esempio, in esecuzione o arrestato)
* Criterio di riavvio
* Registro di immagini e contenitori
* Route per l'input e l'output dei dati

Se l'immagine del modulo è archiviata in una registro contenitori privato, le credenziali del registro sono contenute nell'agente di IoT Edge.

### <a name="target-condition"></a>Condizione di destinazione

La condizione di destinazione viene valutata continuamente per tutta la durata della distribuzione. Vengono inclusi tutti i nuovi dispositivi che soddisfano i requisiti e i dispositivi che non li soddisfano più vengono rimossi. Se il servizio rileva qualsiasi variazione della condizione di destinazione, la distribuzione viene riattivata.

Ad esempio, si dispone di una distribuzione con una condizione di destinazione tags.environment - 'prod'. Quando viene avviata la distribuzione, sono disponibili 10 dispositivi di produzione. I moduli vengono installati correttamente in questi 10 dispositivi. Lo stato dell'agente IoT Edge mostra 10 dispositivi totali, 10 risposte riuscite, 0 risposte di errore e 0 risposte in sospeso. Si aggiungono ora altri cinque dispositivi con tags.environment = 'prod'. Il servizio rileva la modifica e lo stato dell'agente IoT Edge diventa 15 dispositivi totali, 10 risposte riuscite, 0 risposte di errore e 5 risposte in sospeso durante la distribuzione nei cinque nuovi dispositivi.

Usare qualsiasi condizione booleana nei tag del dispositivo gemello, nelle proprietà segnalate del dispositivo gemello o nel deviceId per selezionare i dispositivi di destinazione. Se si intende usare una condizione con tag, è necessario aggiungere la sezione "tags"{} nel dispositivo gemello allo stesso livello delle proprietà. [Altre informazioni sui tag nel dispositivo gemello](../iot-hub/iot-hub-devguide-device-twins.md)

Esempi di condizioni di destinazione:

* deviceId ='linuxprod1'
* tags.environment ='prod'
* tags.environment = 'prod' AND tags.location = 'westus'
* tags.environment = 'prod' OR tags.location = 'westus'
* tags.operator = 'John' AND tags.environment = 'prod' NOT deviceId = 'linuxprod1'
* properties.reported.devicemodel - '4000x'

Considerare questi vincoli quando si costruisce una condizione di destinazione:Consider these constraints when you construct a target condition:

* Nel dispositivo gemello, è possibile compilare una condizione di destinazione solo utilizzando tag, proprietà segnalate o deviceId.
* Le virgolette doppie non sono consentite in qualsiasi parte della condizione di destinazione. Usare le virgolette singole.
* Le virgolette singole rappresentano i valori della condizione di destinazione. Pertanto, è necessario eseguire l'escape della virgoletta singola con un'altra virgoletta singola se fa parte del nome del dispositivo. Ad esempio, per un dispositivo chiamato `operator'sDevice`, scrivere `deviceId='operator''sDevice'`.
* Nei valori della condizione di destinazione sono consentiti i numeri, le lettere e i caratteri seguenti: `-:.+%_#*?!(),=@;$`.

### <a name="priority"></a>Priorità

Una priorità definisce se un tipo di distribuzione deve essere applicato a un dispositivo di destinazione rispetto ad altre distribuzioni. La priorità di distribuzione è un numero intero positivo crescente per indicare una priorità più alta. Se un dispositivo IoT Edge è la destinazione di più di una distribuzione, si applica la distribuzione con priorità più alta.Le distribuzioni con priorità inferiore non vengono applicate, né unite.Se due distribuzioni con uguale priorità vengono assegnate a un dispositivo, si applica la distribuzione creata più di recente, in base al timestamp di creazione.

### <a name="labels"></a>Etichette

Le etichette sono coppie chiave/valore stringa che è possibile utilizzare per filtrare e raggruppare le distribuzioni.Una distribuzione può avere più etichette. Le etichette sono facoltative e non influiscono sulla configurazione effettiva dei dispositivi IoT Edge.

### <a name="metrics"></a>Metriche

Per impostazione predefinita, tutte le distribuzioni segnalano quattro metriche:By default, all deployments report on four metrics:

* **Targeted** Mostra i dispositivi IoT Edge che corrispondono alla condizione di destinazione della distribuzione.
* **Applicato** mostra i dispositivi IoT Edge di destinazione che non sono destinati da un'altra distribuzione con priorità più alta.
* **Segnalazione riuscita** mostra i dispositivi IoT Edge che hanno segnalato che i moduli sono stati distribuiti correttamente.
* **Segnalazione errori** mostra i dispositivi IoT Edge che hanno segnalato che uno o più moduli non sono stati distribuiti correttamente. Per esaminare ulteriormente questo errore, connettersi in remoto a tali dispositivi e visualizzare i file di log.

Inoltre, è possibile definire metriche personalizzate per monitorare e gestire la distribuzione.

Le metriche forniscono conteggi riepilogativi dei vari stati che i dispositivi possono segnalare come risultato dell'applicazione di una configurazione di distribuzione. Le metriche possono eseguire query [sulle proprietà segnalate del modulo edgeHub gemello,](module-edgeagent-edgehub.md#edgehub-reported-properties)ad esempio *lastDesiredStatus* o *lastConnectTime*. Ad esempio:

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

L'aggiunta di metriche personalizzate è facoltativa e non influisce sulla configurazione effettiva dei dispositivi IoT Edge.Adding your own metrics is optional, and doesn't impact the actual configuration of IoT Edge devices.

## <a name="layered-deployment"></a>Distribuzione a più livelli

Le distribuzioni a più livelli sono distribuzioni automatiche che possono essere combinate insieme per ridurre il numero di distribuzioni univoche da creare. Le distribuzioni a più livelli sono utili negli scenari in cui gli stessi moduli vengono riutilizzati in combinazioni diverse in molte distribuzioni automatiche.

Le distribuzioni a più livelli hanno gli stessi componenti di base di qualsiasi distribuzione automatica. Hanno come destinazione i dispositivi in base ai tag nei dispositivi gemelli e forniscono la stessa funzionalità per le etichette, le metriche e la creazione di rapporti di stato. Alle distribuzioni a più livelli sono assegnate anche priorità, ma invece di usare la priorità per determinare quale distribuzione viene applicata a un dispositivo, la priorità determina la classificazione di più distribuzioni in un dispositivo. Ad esempio, se due distribuzioni a più livelli hanno un modulo o una route con lo stesso nome, la distribuzione a più livelli con la priorità più alta verrà applicata mentre la priorità più bassa viene sovrascritta.

I moduli di runtime del sistema, edgeAgent e edgeHub, non sono configurati come parte di una distribuzione a più livelli. Qualsiasi dispositivo IoT Edge di destinazione di una distribuzione a più livelli richiede prima una distribuzione automatica standard applicata. La distribuzione automatica fornisce la base su cui è possibile aggiungere distribuzioni a più livelli.

Un dispositivo IoT Edge può applicare una sola distribuzione automatica standard, ma può applicare più distribuzioni automatiche a più livelli. Tutte le distribuzioni sovrapposte destinate a un dispositivo devono avere una priorità più alta rispetto alla distribuzione automatica per tale dispositivo.

Si consideri, ad esempio, il seguente scenario di una società che gestisce gli edifici. Hanno sviluppato moduli IoT Edge per la raccolta di dati da telecamere di sicurezza, sensori di movimento e ascensori. Tuttavia, non tutti i loro edifici possono utilizzare tutti e tre i moduli. Con le distribuzioni automatiche standard, l'azienda deve creare singole distribuzioni per tutte le combinazioni di moduli necessarie per gli edifici.

![Le distribuzioni automatiche standard devono soddisfare ogni combinazione di moduli](./media/module-deployment-monitoring/standard-deployment.png)

Tuttavia, una volta che l'azienda passa a distribuzioni automatiche a più livelli, ritiene di poter creare le stesse combinazioni di moduli per i propri edifici con un numero inferiore di distribuzioni da gestire. Ogni modulo ha una propria distribuzione a più livelli e i tag del dispositivo identificano quali moduli vengono aggiunti a ogni edificio.

![Le distribuzioni automatiche a più livelli semplificano gli scenari in cui gli stessi moduli vengono combinati in modi diversi](./media/module-deployment-monitoring/layered-deployment.png)

### <a name="module-twin-configuration"></a>Configurazione del modulo gemello

Quando si utilizzano distribuzioni a più livelli, è possibile che siano disponibili due distribuzioni con lo stesso modulo destinato a un dispositivo. In questi casi, è possibile decidere se la distribuzione con priorità più alta deve sovrascrivere il modulo gemello o aggiungervi. Ad esempio, si potrebbe avere una distribuzione che applica lo stesso modulo a 100 dispositivi diversi. Tuttavia, 10 di questi dispositivi si trovano in strutture sicure e necessitano di una configurazione aggiuntiva per comunicare tramite server proxy. È possibile usare una distribuzione a più livelli per aggiungere proprietà del modulo gemello che consentono a questi 10 dispositivi di comunicare in modo sicuro senza sovrascrivere le informazioni gemelle del modulo esistente dalla distribuzione di base.

È possibile aggiungere le proprietà desiderate del modulo gemello nel manifesto di distribuzione. Dove in una distribuzione standard è necessario aggiungere proprietà nella sezione **properties.desired** del modulo gemello, in una distribuzione a più livelli è possibile dichiarare un nuovo sottoinsieme di proprietà desiderate.

Ad esempio, in una distribuzione standard è possibile aggiungere il modulo sensore di temperatura simulato con le seguenti proprietà desiderate che indicano di inviare dati a intervalli di 5 secondi:

```json
"SimulatedTemperatureSensor": {
  "properties.desired": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

In una distribuzione a più livelli destinata ad alcuni o a tutti gli stessi dispositivi, è possibile aggiungere una proprietà che indichi al sensore simulato di inviare 1000 messaggi e quindi arrestarli. Non si desidera sovrascrivere le proprietà esistenti, pertanto si crea `layeredProperties`una nuova sezione all'interno delle proprietà desiderate , che contiene la nuova proprietà:

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties": {
    "StopAfterCount": 1000
  }
}
```

Un dispositivo a cui sono state applicate entrambe le distribuzioni rifletterà le seguenti proprietà nel modulo gemello per il sensore di temperatura simulato:

```json
"properties": {
  "desired": {
    "SendData": true,
    "SendInterval": 5,
    "layeredProperties": {
      "StopAfterCount": 1000
    }
  }
}
```

Se si imposta `properties.desired` il campo del modulo gemello in una distribuzione a più livelli, sovrascriverà le proprietà desiderate per tale modulo in tutte le distribuzioni con priorità più bassa.

## <a name="phased-rollout"></a>Implementazione graduale

Un'implementazione graduale è un processo in base al quale un operatore distribuisce le modifiche a un set più ampio di dispositivi IoT Edge. L'obiettivo è apportare le modifiche gradualmente per ridurre il rischio di introdurre modifiche che causano disservizi su larga scala. Le distribuzioni automatiche consentono di gestire implementazioni in più fasi in un'flotta di dispositivi IoT Edge.Automatic deployments help manage phased rollouts across a fleet of IoT Edge devices.

Un'implementazione graduale viene eseguita in base alle fasi e ai passaggi seguenti:

1. Definire un ambiente di test di dispositivi IoT Edge eseguendone il provisioning e impostando un tag di dispositivo gemello come `tag.environment='test'`.L'ambiente di test deve rispecchiare l'ambiente di produzione a cui sarà infine destinata la distribuzione.
2. Creare una distribuzione, inclusi i moduli e le configurazioni desiderate. La condizione di destinazione deve essere destinata all'ambiente di test dei dispositivi IoT Edge.
3. Convalidare la nuova configurazione dei moduli nell'ambiente di test.
4. Aggiornare la distribuzione per includere un sottoinsieme di dispositivi IoT Edge di produzione, aggiungendo un nuovo tag alla condizione di destinazione. Assicurarsi inoltre che la priorità per la distribuzione sia superiore rispetto alle altre distribuzioni attualmente destinate ai dispositivi.
5. Verificare che la distribuzione venga applicata correttamente nei dispositivi IoT di destinazione visualizzando lo stato di distribuzione.
6. Aggiornare la distribuzione per destinarla a tutti i dispositivi IoT Edge di produzione rimanenti.

## <a name="rollback"></a>Rollback

È possibile eseguire il rollback delle distribuzioni in caso di errori o di problemi di configurazione.Dato che una distribuzione definisce la configurazione assoluta dei moduli per un dispositivo IoT Edge, è necessario destinare una distribuzione aggiuntiva allo stesso dispositivo con una priorità inferiore, anche se l'obiettivo è quello di rimuovere tutti i moduli.  

L'eliminazione di una distribuzione non rimuove i moduli dai dispositivi di destinazione. Deve essere presente un'altra distribuzione che definisce una nuova configurazione per i dispositivi, anche se si tratta di una distribuzione vuota.

Eseguire i rollback con la sequenza seguente:

1. Verificare che esista una seconda distribuzione destinata allo stesso set di dispositivi. Se l'obiettivo del rollback è rimuovere tutti i moduli, la seconda distribuzione non deve includere alcun modulo.
2. Modificare o rimuovere l'espressione della condizione di destinazione della distribuzione di cui si vuole eseguire il rollback in modo che i dispositivi non soddisfino più la condizione di destinazione.
3. Verificare che il rollback sia stato eseguito correttamente visualizzando lo stato di distribuzione.
   * La distribuzione di cui viene eseguito il rollback non deve più visualizzare lo stato per i dispositivi inclusi nel rollback.
   * La seconda distribuzione dovrebbe ora includere lo stato di distribuzione per i dispositivi di cui è stato eseguito il rollback.

## <a name="next-steps"></a>Passaggi successivi

* Eseguire le procedure per creare, aggiornare o eliminare una distribuzione in [Distribuire e monitorare i moduli di IoT Edge su larga scala](how-to-deploy-monitor.md).
* Ulteriori informazioni su altri concetti di IoT Edge come il [runtime di IoT Edge](iot-edge-runtime.md) e i [moduli di IoT Edge](iot-edge-modules.md).
