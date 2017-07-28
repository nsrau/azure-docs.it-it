---
title: Creare un modulo Azure IoT Edge con Java | Microsoft Docs
description: "Questa esercitazione illustra come scrivere un modulo convertitore di dati BLE usando i pacchetti Maven di Azure IoT Edge più recenti."
services: iot-hub
author: junyi
manager: timlt
ms.service: iot-hub
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 06/28/2017
ms.author: junyi
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 682feb4d889ecd881abe1a70d36e0a5a4df3d910
ms.contentlocale: it-it
ms.lasthandoff: 07/04/2017

---
# <a name="create-an-azure-iot-edge-module-with-java"></a>Creare un modulo Azure IoT Edge con Java

Questa esercitazione illustra come è possibile creare un modulo per Azure IoT Edge in Java.

L'esercitazione illustra i passaggi per configurare l'ambiente e spiega come scrivere un modulo convertitore di dati [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) usando la versione più recente dei pacchetti Maven di Azure IoT Edge.

## <a name="prerequisites"></a>Prerequisiti

In questa sezione si configurerà l'ambiente per lo sviluppo del modulo IoT Edge. Si applica sia a *Windows a 64 bit* sia a *Linux a 64 bit (Ubuntu/Debian 8)*.

È richiesto il software seguente:

* [Client Git](https://git-scm.com/downloads).
* [**x64** JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* [Maven](https://maven.apache.org/install.html).

Aprire una finestra del terminale della riga di comando e clonare il repository seguente:

1. `git clone https://github.com/Azure-Samples/iot-edge-samples.git`.
2. `cd iot-edge-samples/java/simulated_ble`

## <a name="overall-architecture"></a>Architettura complessiva

La piattaforma Azure IoT Edge usa frequentemente l'[architettura Von Neumann](https://en.wikipedia.org/wiki/Von_Neumann_architecture). L'intera architettura di Azure IoT Edge è quindi un sistema che elabora input e produce output e ogni singolo modulo è anche un piccolo sottosistema di input/output. In questa esercitazione verranno presentati i due moduli seguenti:

1. Un modulo che riceve un segnale [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) simulato e lo converte in un messaggio [JSON](https://en.wikipedia.org/wiki/JSON) formattato.
2. Un modulo che stampa il messaggio [JSON](https://en.wikipedia.org/wiki/JSON) ricevuto.

L'immagine seguente mostra il tipico flusso di dati end-to-end per il progetto:

![Flusso di dati tra tre moduli](media/iot-hub-iot-edge-create-module/dataflow.png "Input: modulo BLE simulato; Processore: modulo convertitore; Output: modulo stampante")

## <a name="understanding-the-code"></a>Informazioni sul codice

### <a name="maven-project-structure"></a>Struttura di progetto Maven

Poiché i pacchetti Azure IoT Edge sono basati su Maven, è necessario creare una tipica struttura di progetto Maven, che contiene un file `pom.xml`.

Il modello POM eredita dal pacchetto `com.microsoft.azure.gateway.gateway-module-base`, che dichiara tutte le dipendenze necessarie per un progetto di modulo contenente i file binari di runtime, il percorso del file di configurazione del gateway e il comportamento di esecuzione. Questo consente di risparmiare molto tempo ed elimina la necessità di scrivere e riscrivere ripetutamente centinaia di righe di codice.

È necessario aggiornare il file pom.xml dichiarando i plug-in o le dipendenze necessarie e il nome del file di configurazione che deve essere usato da questo modulo, come illustrato nel frammento di codice seguente.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <!-- Inherit from parent -->
  <parent>
    <groupId>com.microsoft.azure.gateway</groupId>
    <artifactId>gateway-module-base</artifactId>
    <version>1.0.1</version>
  </parent>
  
  <groupId>com.microsoft.azure.gateway</groupId>
  <artifactId>ble-converter</artifactId>
  <version>1.0</version>
  <packaging>jar</packaging>

  <!-- Set the filename of the Azure IoT Edge configuration located
       under ./src/main/resources/gateway/ which is used in parent -->
  <properties>
    <gw.config.fileName>gw-config.json</gw.config.fileName>
  </properties>

  <!-- Re-declare dependencies used in parent -->
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.gateway</groupId>
      <artifactId>gateway-java-binding</artifactId>
    </dependency>
    <dependency>
      <groupId>${dependency.runtime.group}</groupId>
      <artifactId>${dependency.runtime.name}</artifactId>
    </dependency>
  </dependencies>

  <!-- Re-declare plugins used in parent -->
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-dependency-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-resources-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
```

### <a name="basic-understanding-of-an-azure-iot-edge-module"></a>Conoscenza di base di un modulo Azure IoT Edge

È possibile gestire il modulo Azure IoT Edge come elaboratore di dati il cui compito è di ricevere l'input, elaborarlo e produrre l'output.

L'input potrebbe essere costituito da dati dell'hardware (ad esempio un rilevatore di movimento), un messaggio da altri moduli o qualsiasi altra informazione (ad esempio un numero casuale generato periodicamente da un timer).

L'output è simile all'input. Può attivare il comportamento dell'hardware, ad esempio il lampeggiamento di un LED, generare un messaggio ad altri moduli o qualsiasi altra azione, ad esempio la stampa nella console.

I moduli comunicano reciprocamente usando la classe `com.microsoft.azure.gateway.messaging.Message`. Il **contenuto** di un `Message` è una matrice di byte in grado di rappresentare qualsiasi genere di dati. Nel `Message` sono presenti anche **proprietà** che rappresentano semplicemente il mapping stringa-a-stringa. Si può pensare alle **proprietà** come alle intestazioni in una richiesta HTTP o ai metadati di un file.

Per sviluppare un modulo Azure IoT Edge in Java, è necessario creare una nuova classe di moduli che eredita da `com.microsoft.azure.gateway.core.GatewayModule` e implementare i metodi astratti necessari `receive()` e `destroy()`. A questo punto, è possibile anche implementare i metodi `start()` e `create()` facoltativi. Il frammento di codice seguente illustra come iniziare la creazione di un modulo Azure IoT Edge.

```java
import com.microsoft.azure.gateway.core.Broker;
import com.microsoft.azure.gateway.core.GatewayModule;
import com.microsoft.azure.gateway.messaging.Message;

public class MyEdgeModule extends GatewayModule {
  public MyEdgeModule(long address, Broker broker, String configuration) {
    /* Let the GatewayModule do the dirty work of initialization. It's also
       a good time to parse your own configuration defined in Azure IoT Edge
       configuration file (typically ./src/main/resources/gateway/gw-config.json) */
    super(address, broker, configuration);
  }

  @Override
  public void start() {
    /* Acquire the resources you need. If you don't
       need any resources, you may omit this method. */
  }

  @Override
  public void destroy() {
    /* It's time to release all resources. This method is required. */
  }

  @Override
  public void receive(Message message) {
    /* Logic to process the input message. This method is required. */
    // ...
    /* Use publish() method to do the output. You are
       allowed to publish your new Message instance. */
    this.publish(message);
  }
}
```

### <a name="converter-module"></a>Modulo convertitore

| Input                    | Processore                              | Output                 | File di origine            |
| ------------------------ | -------------------------------------- | ---------------------- | ---------------------- |
| Messaggio sui dati della temperatura | Analizza e crea un nuovo messaggio JSON | Messaggio JSON strutturato | `ConverterModule.java` |

Questo è un tipico modulo Azure IoT Edge. Accetta messaggi sulla temperatura da altri moduli, ad esempio un modulo hardware o in questo caso il modulo BLE simulato. Normalizza quindi il messaggio sulla temperatura in un messaggio JSON strutturato, includendo l'aggiunta dell'ID di messaggio, l'impostazione della proprietà che indica se è necessario attivare l'avviso di temperatura e così via.

```java
@Override
public void receive(Message message) {
  try {
    JSONObject messageFromBle = new JSONObject(new String(message.getContent()));
    double temperature = messageFromBle.getDouble("temperature");
    Map<String, String> inputProperties = message.getProperties();

    HashMap<String, String> properties = new HashMap<>();
    properties.put("source", inputProperties.get("source"));
    properties.put("macAddress", inputProperties.get("macAddress"));
    properties.put("temperatureAlert", temperature > 30 ? "true" : "false");

    String content = String.format(
        "{ \"deviceId\": \"Intel NUC Gateway\", \"messageId\": %d, \"temperature\": %f }",
        ++this.messageCount, temperature);

    this.publish(new Message(content.getBytes(), properties));
  } catch (Exception ex) {
    ex.printStackTrace();
  }
}
```

### <a name="printer-module"></a>Modulo della stampante

| Input                          | Processore | Output                     | File di origine          |
| ------------------------------ | --------- | -------------------------- | -------------------- |
| Qualsiasi messaggio da altri moduli | N/D       | Registra il messaggio nella console | `PrinterModule.java` |

Questo modulo semplice e di chiara interpretazione restituisce i messaggi ricevuti alla finestra Terminal.

```java
@Override
public void receive(Message message) {
  System.out.println(message.toString());
}
```

### <a name="azure-iot-edge-configuration"></a>Configurazione di Azure IoT Edge

Il passaggio finale prima di eseguire i moduli consiste nel configurare Azure IoT Edge e stabilire le connessioni tra i moduli.

Dal momento che Azure IoT Edge supporta caricatori di varie lingue, occorre prima dichiarare il caricatore Java specifico, a cui si potrebbe fare riferimento in base al `name` nelle sezioni successive.

```json
"loaders": [{
  "type": "java",
  "name": "java",
  "configuration": {
    "jvm.options": {
      "library.path": "./"
    }
  }
}]
```

Dopo avere dichiarato i caricatori, sarà necessario dichiarare anche i moduli. Come avviene nella dichiarazione dei caricatori, anche ai moduli è possibile fare riferimento tramite il relativo attributo `name`. Quando si dichiara un modulo, è necessario specificare il caricatore che il modulo deve usare (che dovrebbe essere quello che è stato definito prima) e il punto di ingresso (che dovrebbe essere il nome della classe normalizzata del modulo) per ogni modulo. Il modulo `simulated_device` è un modulo nativo che è incluso nel pacchetto di runtime principale di Azure IoT Edge. È opportuno includere sempre `args` nel file JSON, anche se è `null`.

```json
"modules": [
  {
    "name": "simulated_device",
    "loader": {
      "name": "native",
      "entrypoint": {
        "module.path": "simulated_device"
      }
    },
    "args": {
      "macAddress": "01:02:03:03:02:01",
      "messagePeriod": 500
    }
  },
  {
    "name": "converter",
    "loader": {
      "name": "java",
      "entrypoint": {
        "class.name": "com/microsoft/azure/gateway/ConverterModule",
        "class.path": "./ble-converter-1.0-with-deps.jar"
      }
    },
    "args": null
  },
  {
    "name": "print",
    "loader": {
      "name": "java",
      "entrypoint": {
        "class.name": "com/microsoft/azure/gateway/PrinterModule",
        "class.path": "./ble-converter-1.0-with-deps.jar"
      }
    },
    "args": null
  }
]
```

Al termine della configurazione occorre stabilire le connessioni. Ogni connessione è espressa da `source` e `sink`. Entrambi devono fare riferimento a un modulo predefinito. Il messaggio di output del modulo `source` viene inoltrato all'input del modulo `sink`.

```json
"links": [
  {
    "source": "simulated_device",
    "sink": "converter"
  },
  {
    "source": "converter",
    "sink": "print"
  }
]
```

## <a name="running-the-modules"></a>Esecuzione dei moduli

Usare `mvn package` per compilare tutti gli elementi nella cartella `target/`. Per eseguire una compilazione pulita, è consigliato anche `mvn clean package`.

Usare `mvn exec:exec` per eseguire Azure IoT Edge; è importante osservare che i dati sulla temperatura e tutte le proprietà vengono stampate nella console a una tariffa fissa.

Se si vuole terminare l'applicazione, premere `<Enter>`.

> [!IMPORTANT]
> Non è consigliabile usare Ctrl + C per terminare l'applicazione gateway IoT Edge, poiché questa azione può determinare un arresto anomalo del processo.


