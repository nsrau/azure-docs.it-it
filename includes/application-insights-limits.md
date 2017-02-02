Esistono alcuni limiti sul numero di metriche e eventi per applicazione (ovvero, per ogni chiave di strumentazione). 

I limiti dipendono dal [piano tariffario](https://azure.microsoft.com/pricing/details/application-insights/) scelto.

| **Risorsa** | **Limite predefinito** | **Nota**
| --- | --- | --- |
| Totale dati al giorno | 500 GB | È possibile ridurre impostando un limite. Se sono necessarie altre informazioni, inviare un messaggio di posta elettronica all'indirizzo AIDataCap@microsoft.com 
| Dati gratuiti al mese<br/> (piano tariffario base) | 1 GB | Dati aggiuntivi addebitati per GB
| Limitazione | 16.000 eventi/secondo | Misurazione nell'arco di un minuto. 
| Conservazione dei dati | 90 giorni | per [ricerca](../articles/application-insights/app-insights-diagnostic-search.md), [analisi](../articles/application-insights/app-insights-analytics.md) ed [Esplora metriche](../articles/application-insights/app-insights-metrics-explorer.md)
| Conservazione dei risultati dettagliati di [test di disponibilità in più passi](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests) | 90 giorni | Risultati dettagliati di ogni passo
| Lunghezza nomi di proprietà e metriche | 150 |
| Lunghezza stringa valore di proprietà | 8192 |
| Lunghezza messaggio di traccia e di eccezione | 10000 |
| Numero di [test di disponibilità](../articles/application-insights/app-insights-monitor-web-app-availability.md) per app  | 10 |

1. Tutti i numeri si riferiscono a ogni chiave di strumentazione.

[Informazioni su prezzi e quote in Application Insights](../articles/application-insights/app-insights-pricing.md)


<!--HONumber=Jan17_HO4-->


