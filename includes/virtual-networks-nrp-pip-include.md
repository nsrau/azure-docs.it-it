## Indirizzo IP pubblico
Una risorsa di indirizzo IP pubblico fornisce un indirizzo IP pubblico riservato o dinamico. Sebbene sia possibile creare un indirizzo IP pubblico come oggetto autonomo, è necessario associarlo a un altro oggetto per utilizzare davvero l'indirizzo. È possibile associare un indirizzo IP pubblico a un bilanciamento del carico, a un gateway di applicazione o a una scheda di rete per fornire l'accesso a Internet a tali risorse.

|Proprietà|Descrizione|Valori di esempio|
|---|---|---|
|**publicIPAllocationMethod**|Definisce se l'indirizzo IP è *statico* o *dinamico*.|statico, dinamico|
|**idleTimeoutInMinutes**|Definisce il timeout di inattività.|qualsiasi valore compreso tra 4 e 30.|
|**ipAddress**|Indirizzo IP assegnato all'oggetto. La proprietà è di sola lettura.|104\.42.233.77|

### Impostazioni DNS
Gli indirizzi IP pubblici dispongono di un oggetto figlio denominato **dnsSettings** contenente le proprietà seguenti:

|Proprietà|Descrizione|Valori di esempio|
|---|---|---|
|**domainNameLabel**|Host denominato utilizzato per la risoluzione dei nomi.|www, ftp, vm1|
|**reverseFqdn**|Nome di dominio completo che viene risolto nell'indirizzo IP e viene registrato in DNS come un record PTR.|www.contoso.com|

Indirizzo IP pubblico di esempio in formato JSON:

	{
	   "name": "PIP01",
	   "location": "North US",
	   "tags": { "key": "value" },
	   "properties": {
	      "publicIPAllocationMethod": "Static",
	      "idleTimeoutInMinutes": 4,
		  "ipAddress": "104.42.233.77",
	      "dnsSettings": {
	         "domainNameLabel": "mylabel",
	         "reverseFqdn": "contoso.com."
	      }
	   }
	} 

<!---HONumber=Sept15_HO4-->