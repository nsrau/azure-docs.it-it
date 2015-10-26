Gestione risorse di Azure permette di definire i parametri per i valori da specificare durante la distribuzione del modello. Il modello include una sezione denominata Parametri che contiene tutti i valori dei parametri. È necessario definire un parametro per i valori che variano in base al progetto distribuito o all'ambiente in cui viene distribuito il progetto. Non definire i parametri per i valori che rimangono invariati. Ogni valore di parametro nel modello viene usato per definire le risorse distribuite.

Di seguito viene fornita la descrizione di ogni parametro del modello.

### gatewayName

Nome del gateway. Si tratta del gateway usato per la registrazione dell'app per le API.

    "gatewayName": {
      "type": "string"
    }

### apiAppName

Nome dell'app per le API da creare. Il nome deve contenere almeno 8 caratteri e non più di 50 caratteri.
    
    "apiAppName": {
      "type": "string"
    }

### apiAppSecret

Segreto dell'app per le API. Questo valore deve essere una stringa con codifica base64. Dovrebbe essere una stringa casuale con 64 caratteri costituita solo di numeri interi e caratteri minuscoli.

    "apiAppSecret": {
      "type": "securestring"
    }

### location

Percorso della nuova app per le API. È possibile ottenere i percorsi validi eseguendo il comando PowerShell`Get-AzureLocation`o il comando di Azure CLI`azure location list`.

    "location": {
      "type": "string"
    }

<!---HONumber=Oct15_HO3-->