Esistono alcuni limiti sul numero di metriche e eventi per applicazione (ovvero, per ogni chiave di strumentazione).

I limiti dipendono dal [livello di prezzo](https://azure.microsoft.com/pricing/details/application-insights/) scelto.

**Risorsa** | **Limite predefinito** | **Limite massimo**
-------- | ------------- | -------------
Punti dati di sessione<sup>1</sup> al mese | senza limiti | 
Altri punti dati al mese | 5 milioni | 50 milioni<sup>2</sup>
Velocità dati [di traccia o di log](../articles/application-insights/app-insights-search-diagnostic-logs.md) | 200 dp/s | 500 dp/s
Velocità dati [di eccezione](../articles/application-insights/app-insights-asp-net-exceptions.md) | 50 dp/s | 50 dp/s
Altre velocità dati di telemetria | 200 dp/s | 500 dp/s
Conservazione [dati non elaborati](../articles/application-insights/app-insights-diagnostic-search.md) | 7 giorni
Conservazione [dati aggregati](../articles/application-insights/app-insights-metrics-explorer.md) | 90 giorni
Numero nomi di [proprietà](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) | 100 |
Lunghezza nome proprietà | 100 | 
Lunghezza valore proprietà | 1000 | 
Lunghezza messaggio di traccia e di eccezione | 10000 |
Numero nomi di [metriche](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) | 100 |
Lunghezza nome di metrica | 100 | 
[Test della disponibilità](../articles/application-insights/app-insights-monitor-web-app-availability.md) | 10 | 

<sup>1</sup> Un punto dati è un evento o un valore di metrica singolo, con proprietà e misurazioni associate.

<sup>2</sup> È possibile acquistare capacità aggiuntiva per oltre 50 milioni.
 
[Informazioni su prezzi e quote in Application Insights](../articles/application-insights/app-insights-pricing.md)

<!---HONumber=AcomDC_0427_2016-->