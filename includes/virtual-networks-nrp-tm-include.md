## Profilo di Gestione traffico
Gestione traffico e la relativa risorsa endpoint figlio consentono la distribuzione del traffico agli endpoint all'interno e all'esterno di Azure. Questa distribuzione del traffico è regolata dai criteri. Gestione traffico consente anche il monitoraggio dell'integrità dell'endpoint e la corretta deviazione del traffico in base all'integrità di un endpoint.

Le proprietà principali di un profilo di Gestione traffico includono:

- **Metodo di routing del traffico**: i valori possibili sono *Prestazioni*, *Valore ponderato* e *Priorità*.
- **Configurazione DNS**: FQDN per il profilo.
- **Protocollo**: protocollo di monitoraggio. I valori possibili sono *HTTP* e *HTTPS*.
- **Porta**: porta di monitoraggio. 
- **Percorso**: percorso di monitoraggio.
- **Endpoint**: contenitore per le risorse endpoint.

### Endpoint 
Un endpoint è una risorsa figlio di un profilo di Gestione traffico. Rappresenta un endpoint di servizio o Web al quale viene distribuito il traffico in base ai criteri configurati nella risorsa del profilo di Gestione traffico.

Le proprietà principali di un endpoint includono:
 
- **Tipo**: tipo di endpoint. I valori possibili sono *Endpoint di Azure*, *Endpoint esterno* ed *Endpoint annidato*. 
- **ID risorsa di destinazione**: indirizzo IP pubblico di un endpoint di servizio o Web. Può trattarsi di un endpoint di Azure o esterno.
- **Peso**: peso dell'endpoint usato nella gestione del traffico. 
- **Priorità**: priorità dell'endpoint, usata per definire un'azione di failover. 

<!---HONumber=Oct15_HO3-->