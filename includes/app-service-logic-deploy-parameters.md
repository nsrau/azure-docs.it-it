Gestione risorse di Azure permette di definire i parametri per i valori da specificare durante la distribuzione del modello. Il modello include una sezione denominata Parametri che contiene tutti i valori dei parametri. È necessario definire un parametro per i valori che variano in base al progetto distribuito o all'ambiente in cui viene distribuito il progetto. Non definire i parametri per i valori che rimangono invariati. Ogni valore di parametro nel modello viene usato per definire le risorse distribuite.

Durante la definizione dei parametri, usare il campo **allowedValues** per specificare i valori che l'utente può fornire durante la distribuzione. Usare il campo **defaultValue** per assegnare un valore al parametro, se non viene specificato alcun valore durante la distribuzione.

Di seguito viene fornita la descrizione di ogni parametro del modello.

### logicAppName

Nome dell'app per la logica da creare.

    "logicAppName": {
        "type": "string"
    }

### svcPlanName

Nome del piano di servizio app da creare per l'hosting dell'app per la logica.
    
    "svcPlanName": {
        "type": "string"
    }

### sku

Piano tariffario dell'app per la logica.

    "sku": {
        "type": "string",
        "defaultValue": "Standard",
        "allowedValues": [
            "Free",
            "Basic",
            "Standard",
            "Premium"
        ]
    }

Il modello definisce i valori consentiti per il parametro (Gratuito, Basic, Standard o Premium) e assegna un valore predefinito (Standard) nel caso in cui non viene specificato alcun valore.

### svcPlanSize

Dimensioni dell'istanza dell'app.

    "svcPlanSize": {
        "defaultValue": "0",
        "type": "string",
        "allowedValues": [
            "0",
            "1",
            "2"
        ]
    }

Il modello definisce i valori consentiti per questo parametro (0, 1 o 2) e assegna un valore predefinito (0) nel caso in cui non viene specificato alcun valore. I valori corrispondono a piccola, media e grande.

<!---HONumber=62-->