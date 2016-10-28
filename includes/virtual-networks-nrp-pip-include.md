## Indirizzo IP pubblico
Una risorsa di indirizzo IP pubblico fornisce un indirizzo IP pubblico con connessione a internet riservato o dinamico. Sebbene sia possibile creare un indirizzo IP pubblico come oggetto autonomo, è necessario associarlo a un altro oggetto per utilizzare davvero l'indirizzo. È possibile associare un indirizzo IP pubblico a un bilanciamento del carico, a un gateway di applicazione o a una scheda di rete per fornire l'accesso a Internet a tali risorse.

|Proprietà|Descrizione|Valori di esempio|
|---|---|---|
|**publicIPAllocationMethod**|Definisce se l'indirizzo IP è *statico* o *dinamico*.|statico, dinamico|
|**idleTimeoutInMinutes**|Definisce il timeout di inattività, con un valore predefinito pari a 4 minuti. Se non vengono ricevuti ulteriori pacchetti per una determinata sessione entro questo intervallo di tempo, la sessione è terminata.|qualsiasi valore compreso tra 4 e 30.|
|**ipAddress**|Indirizzo IP assegnato all'oggetto. La proprietà è di sola lettura.|104\.42.233.77|

### Impostazioni DNS
Gli indirizzi IP pubblici dispongono di un oggetto figlio denominato **dnsSettings** contenente le proprietà seguenti:

|Proprietà|Descrizione|Valori di esempio|
|---|---|---|
|**domainNameLabel**|Host denominato utilizzato per la risoluzione dei nomi.|www, ftp, vm1|
|**fqdn**|Nome completo per l'indirizzo IP pubblico.|www.westus.cloudapp.azure.com|
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
			 "fqdn": "mylabel.westus.cloudapp.azure.com",
	         "reverseFqdn": "contoso.com."
	      }
	   }
	} 

### Risorse aggiuntive

- Ottenere ulteriori informazioni sugli [indirizzi IP pubblici](../articles/virtual-network/virtual-networks-reserved-public-ip.md).
- Informazioni sugli [indirizzi IP pubblici a livello di istanza](../articles/virtual-network/virtual-networks-instance-level-public-ip.md).
- Leggere [la documentazione di riferimento API REST](https://msdn.microsoft.com/library/azure/mt163638.aspx) per indirizzi IP pubblici.

<!---HONumber=AcomDC_0323_2016-->