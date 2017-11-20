Gli aggiornamenti dell'agente Sincronizzazione file di Azure verranno rilasciati a intervalli regolari per aggiungere nuove funzionalità e risolvere eventuali problemi riscontrati. Si consiglia di configurare Microsoft Update per ricevere tutti gli aggiornamenti dell'agente Sincronizzazione file di Azure non appena vengono rilasciati. È comprensibile che alcune organizzazioni vogliano controllare e testare accuratamente gli aggiornamenti. 

Per le distribuzioni che usano versioni precedenti dell'agente Sincronizzazione file di Azure:

- Il servizio di sincronizzazione archiviazione rimane conforme alla versione principale precedente per tre mesi dopo il rilascio iniziale di una nuova versione principale. Ad esempio, il servizio di sincronizzazione archiviazione supporta la versione 1.\* per tre mesi dopo il rilascio della versione 2.\*.
- Allo scadere dei tre mesi, il servizio di sincronizzazione archiviazione inizia a impedire ai server registrati che usano la versione scaduta di sincronizzarsi con i propri gruppi di sincronizzazione.
- Nei tre mesi di validità della versione principale precedente, tutte le correzioni di bug vengono aggiunte solo alla versione principale corrente.

> [!Note]  
> Si riceverà una notifica di tipo avviso popup nel portale di Azure se si usa una versione di Sincronizzazione file di Azure che scadrà entro i prossimi tre mesi.