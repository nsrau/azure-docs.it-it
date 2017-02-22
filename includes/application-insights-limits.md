Esistono alcuni limiti sul numero di metriche e eventi per applicazione (ovvero, per ogni chiave di strumentazione). I limiti dipendono dal [piano tariffario](https://azure.microsoft.com/pricing/details/application-insights/) scelto.

| **Risorsa** | **Limite predefinito** | **Nota**
| --- | --- | --- |
| Totale dati al giorno | 500 GB | È possibile ridurre i dati impostando un limite. Se sono necessarie altre informazioni, inviare un messaggio di posta elettronica all'indirizzo AIDataCap@microsoft.com.
| Dati gratuiti al mese<br/> (piano tariffario base) | 1 GB | Vengono applicati addebiti per ogni gigabyte di dati aggiuntivi.
| Limitazione | 32.000 eventi/secondo | Il limite viene misurato nell'arco di un minuto.
| Conservazione dei dati | 90 giorni | Questa risorsa è destinata a [Ricerca](../articles/application-insights/app-insights-diagnostic-search.md), [Analisi](../articles/application-insights/app-insights-analytics.md) e [Esplora metriche](../articles/application-insights/app-insights-metrics-explorer.md).
| Conservazione dei risultati dettagliati di [test di disponibilità in più passi](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests) | 90 giorni | Questa risorsa fornisce risultati dettagliati per ogni passaggio.
| Lunghezza nomi di proprietà e metriche | 150 |
| Lunghezza stringa valore di proprietà | 8.192 |
| Lunghezza messaggio di traccia e di eccezione | 10.000 |
| Numero di [test di disponibilità](../articles/application-insights/app-insights-monitor-web-app-availability.md) per app  | 10 |

Per altre informazioni, vedere [Informazioni su prezzi e quote in Application Insights](../articles/application-insights/app-insights-pricing.md).


<!--HONumber=Feb17_HO2-->


