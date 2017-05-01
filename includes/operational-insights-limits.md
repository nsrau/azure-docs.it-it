
>[!NOTE]
>Log Analytics era noto in precedenza come Operational Insights.
>
>

I limiti seguenti si applicano alle risorse di Log Analytics per ogni sottoscrizione:

| Risorsa | Limite predefinito | Commenti
| --- | --- | --- |
| Numero di aree di lavoro gratuite per sottoscrizione | 10 | Non è possibile aumentare questo limite |
| Numero di aree di lavoro a pagamento per sottoscrizione | N/D | Il limite è dato dal numero di risorse all'interno di un gruppo di risorse e dal numero di gruppi di risorse per ogni sottoscrizione | 


I limiti seguenti si applicano a ogni area di lavoro di Log Analytics:

|  | Gratuito | Standard | Premium | Autonoma | OMS |
| --- | --- | --- | --- | --- | --- |
| Volume di dati raccolti ogni giorno |500 MB<sup>1</sup> |None |None | None | Nessuno
| Periodo di conservazione dei dati |7 giorni |1 mese |12 mesi | 1 mese<sup>2</sup> | 1 mese <sup>2</sup>|

<sup>1</sup> Quando i clienti raggiungono il limite giornaliero di trasferimento dati di 500 MB, l'analisi dei dati si interrompe e riprende all'inizio del giorno successivo. Un giorno si basa su UTC.

<sup>2</sup> Il periodo di conservazione dei dati per i piani tariffari Autonomo e OMS può essere aumentato a 730 giorni.

| Categoria | Limiti | Commenti
| --- | --- | --- |
| API dell'Agente di raccolta dati | La dimensione massima per un singolo post è di 30 MB<br>La dimensione massima per i valori dei campi è di 32 KB | Dividere i volumi più grandi in più post<br>I campi con una lunghezza superiore a 32 KB vengono troncati. |
| API di ricerca | 5000 record restituiti per i dati non aggregati<br>500000 record per i dati aggregati | I dati aggregati sono una ricerca che include il comando `measure`
 
