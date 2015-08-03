
### redisCacheName

Nome della Cache Redis di Azure da creare.

    "redisCacheName": {
      "type": "string"
    }

### redisCacheSKU

Piano tariffario della nuova Cache Redis di Azure.

    "redisCacheSKU": {
      "type": "string",
      "allowedValues": [ "Basic", "Standard" ],
      "defaultValue": "Basic"
    }

Il modello definisce i valori consentiti per questo parametro (Basic o Standard) e assegna un valore predefinito (Basic) se non viene specificato alcun valore. Basic fornisce un singolo nodo con più dimensioni disponibili fino a 53 GB. Standard fornisce due nodi di replica/primario con più dimensioni disponibili fino a 53 GB e contratto di servizio con disponibilità del 99,9%.

### redisCacheFamily

Famiglia dello SKU.

    "redisCacheFamily": {
      "type": "string",
      "defaultValue": "C"
    }

### redisCacheCapacity

Dimensioni dell'istanza della nuova Cache Redis di Azure.

    "redisCacheCapacity": {
      "type": "int",
      "allowedValues": [ 0, 1, 2, 3, 4, 5, 6 ],
      "defaultValue": 0
    }

Il modello definisce i valori consentiti per questo parametro (0, 1, 2, 3, 4, 5 o 6) e assegna un valore predefinito (0) se non viene specificato alcun valore. Questi numeri corrispondono alle dimensioni della cache seguenti: 0 = 250 MB, 1 = 1 GB, 2 = 2,5 GB, 3 = 6 GB, 4 = 13 GB, 5 = 26 GB, 6 = 53 GB

### redisCacheVersion

Versione del server Redis della nuova cache.

    "redisCacheVersion": {
      "type": "string",
      "defaultValue": "2.8"
    }

<!---HONumber=July15_HO4-->