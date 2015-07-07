Gestione risorse di Azure permette di definire i parametri per i valori da specificare durante la distribuzione del modello. Il modello include una sezione denominata Parametri che contiene tutti i valori dei parametri. È necessario definire un parametro per i valori che variano in base al progetto distribuito o all'ambiente in cui viene distribuito il progetto. Non definire i parametri per i valori che rimangono invariati. Ogni valore di parametro nel modello viene usato per definire le risorse distribuite.

Di seguito viene fornita la descrizione di ogni parametro del modello.

### gatewayName

Nome del gateway da creare. Si tratta del gateway usato per la registrazione dell'app per le API.

    "gatewayName": {
      "type": "string"
    }

### apiAppName

Nome dell'app per le API da creare.
    
    "apiAppName": {
      "type": "string"
    }

### apiAppSecret

Segreto dell'app per le API. Questo valore deve essere una stringa con codifica base64.

    "apiAppSecret": {
      "type": "securestring"
    }

### location

Percorso della nuova app per le API.

    "location": {
      "type": "string"
    }

<!---HONumber=62-->