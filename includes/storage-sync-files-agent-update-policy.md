L'agente Sincronizzazione file di Azure viene aggiornato a intervalli regolari per aggiungere nuove funzionalità e risolvere eventuali problemi. È consigliabile configurare Microsoft Update per ricevere gli aggiornamenti dell'agente Sincronizzazione file di Azure non appena vengono rilasciati. È comprensibile che alcune organizzazioni vogliano controllare e testare accuratamente gli aggiornamenti.

Per le distribuzioni che usano versioni precedenti dell'agente Sincronizzazione file di Azure:

- Dopo il rilascio iniziale di una nuova versione principale, il servizio di sincronizzazione archiviazione supporta la versione principale precedente per tre mesi. Ad esempio, il servizio di sincronizzazione archiviazione supporta la versione 1.\* per tre mesi dopo il rilascio della versione 2.\*.
- Allo scadere dei tre mesi, il servizio di sincronizzazione archiviazione impedirà ai server registrati che usano la versione scaduta di sincronizzarsi con i propri gruppi di sincronizzazione.
- Nei tre mesi di supporto della versione principale precedente, tutte le correzioni di bug verranno aggiunte solo alla versione principale corrente (nuova).

> [!Note]  
> Si riceverà una notifica di tipo avviso popup nel portale di Azure se la versione di Sincronizzazione file di Azure scadrà entro i tre mesi successivi.
