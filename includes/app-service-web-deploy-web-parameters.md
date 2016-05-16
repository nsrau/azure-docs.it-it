Gestione risorse di Azure permette di definire i parametri per i valori da specificare durante la distribuzione del modello. Il modello include una sezione denominata Parametri che contiene tutti i valori dei parametri. È necessario definire un parametro per i valori che variano in base al progetto distribuito o all'ambiente in cui viene distribuito il progetto. Non definire i parametri per i valori che rimangono invariati. Ogni valore di parametro nel modello viene usato per definire le risorse distribuite.

Durante la definizione dei parametri, usare il campo **allowedValues** per specificare i valori che l'utente può fornire durante la distribuzione. Usare il campo **defaultValue** per assegnare un valore al parametro, se non viene specificato alcun valore durante la distribuzione.

Di seguito è fornita la descrizione di ogni parametro del modello.

### siteName

Il nome dell'app Web che si desidera creare.

    "siteName":{
      "type":"string"
    }

### hostingPlanName

Il nome del piano di servizio app da usare per l'hosting dell'app Web.
    
    "hostingPlanName":{
      "type":"string"
    }

### sku

Il piano tariffario del piano di hosting.

    "sku": {
      "type": "string",
      "allowedValues": [
        "F1",
        "D1",
        "B1",
        "B2",
        "B3",
        "S1",
        "S2",
        "S3",
        "P1",
        "P2",
        "P3",
        "P4"
      ],
      "defaultValue": "S1",
      "metadata": {
        "description": "The pricing tier for the hosting plan."
      }
    }

Il modello definisce i valori consentiti per questo parametro e assegna un valore predefinito (S1) se non viene specificato alcun valore.

### workerSize

Le dimensioni delle istanze del piano di hosting (piccole, medie o grandi dimensioni).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }
    
Il modello definisce i valori consentiti per questo parametro (0, 1 o 2) e assegna un valore predefinito (0) nel caso in cui non viene specificato alcun valore. I valori corrispondono a piccole, medie e grandi dimensioni.

