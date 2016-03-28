Data Factory è un servizio multi-tenant che presenta i seguenti limiti predefiniti in modo che le sottoscrizioni dei clienti siano protette da ogni altro carico di lavoro. Molti dei limiti possono essere facilmente aumentati per la sottoscrizione fino al limite massimo contattando il supporto tecnico.

**Risorsa** | **Limite predefinito** | **Limite massimo**
-------- | ------------- | -------------
pipeline all'interno di una factory di dati | 100 | 2500
set di dati all'interno di una factory di dati | 500 | 5000
sezioni simultanee per ogni set di dati | 10 | 10
byte per oggetto per gli oggetti pipeline<sup>1</sup> | 200 KB | 2000 KB
byte per oggetto per oggetti set di dati e linkedservice<sup>1</sup> | 30 KB | 2000 KB
campi per oggetto | 100 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
byte per nome di campo o identificatore | 2 KB | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
byte per campo | 30 KB | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
Memorie centrali del cluster HDInsight su richiesta con una sottoscrizione<sup>2</sup> | 48 | [Contattare il supporto tecnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
Numero di tentativi delle esecuzioni di attività pipeline | 1000 | MaxInt (32 bit)

<sup>1</sup>pipeline, set di dati e oggetti servizio collegato rappresentano un raggruppamento logico del carico di lavoro. I limiti per questi oggetti non riguardano le quantità di dati che è possibile spostare ed elaborare con il servizio di Azure Data Factory. Data Factory è progettato per la scalabilità e la gestione di petabyte di dati.

<sup>2</sup>memorie centrali HDInsight su richiesta vengono distribuite al di fuori della sottoscrizione che contiene la factory di dati. Di conseguenza, il limite massimo è il limite principale imposto dalla Data Factory per le memorie centrali HDInsight su richiesta e che è diverso dal limite principale associato alla sottoscrizione Azure.


**Risorsa** | **Limite inferiore predefinito** | **Limite minimo**
-------- | ------------------- | -------------
Intervallo di pianificazione | 15 minuti | 15 minuti
Intervallo tra tentativi | 1 secondo | 1 secondo
Valore di timeout del tentativo | 1 secondo | 1 secondo


### Limiti di chiamata del servizio Web

Gestione risorse di Azure presenta limiti per le chiamate API. È possibile effettuare chiamate API a una velocità all'interno di[limiti API di gestione risorse di Azure](../articles/azure-subscription-service-limits/#resource-group-limits).

<!---HONumber=AcomDC_0316_2016-->