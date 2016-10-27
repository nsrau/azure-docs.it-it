## <a name="load-balancer-differences"></a>Differenze del servizio di bilanciamento del carico

Sono disponibili diverse opzioni per distribuire il traffico di rete tramite Microsoft Azure. Queste opzioni funzionano in modo diverso, vantano un set di funzionalità differenti e supportano diversi scenari. Possono essere utilizzate singolarmente o in combinazione.

- **Azure Load Balancer** funziona al livello trasporto (livello 4 nello stack di riferimento di rete OSI). Assicura la distribuzione del traffico a livello di rete tra le istanze di un'applicazione in esecuzione nello stesso data center di Azure.

- **gateway applicazione** funziona a livello di applicazione (livello 7 nello stack di riferimento di rete OSI). Agisce come un servizio di proxy inverso, terminando la connessione di client e inoltrando richieste a endpoint di back-end.

- **servizio Gestione traffico** funziona a livello DNS.  Usa le risposte DNS per indirizzare il traffico degli utenti finali agli endpoint distribuiti a livello globale. I client si connettono quindi direttamente a questi endpoint.

La tabella seguente riepiloga le funzionalità offerte da ogni servizio:

| Service | Azure Load Balancer | gateway applicazione | servizio Gestione traffico |
|---|---|---|---|
|Tecnologia| Livello trasporto (livello 4) | Livello applicazione (livello 7) | Livello DNS |
| Protocolli di applicazioni supportati | Qualsiasi | HTTP e HTTPS |  Qualsiasi (è richiesto un endpoint HTTP per il monitoraggio degli endpoint) |
| Endpoint | Istanze del ruolo VM e Servizi cloud di Azure | Qualsiasi indirizzo IP interno di Azure o indirizzo IP Internet pubblico | VM di Azure, Servizi Cloud, App Web di Azure ed endpoint esterni |
| Supporto della rete virtuale | Può essere utilizzato sia per applicazioni con connessione Internet sia per applicazioni interne (rete virtuale) | Può essere utilizzato sia per applicazioni con connessione Internet sia per applicazioni interne (rete virtuale) |    Supporta solo applicazioni con connessione Internet |
Monitoraggio degli endpoint | supportato tramite probe | supportato tramite probe | supportato tramite HTTP/HTTPS GET | 

Azure Load Balancer e il gateway applicazione di Azure indirizzano il traffico di rete agli endpoint, ma presentano diversi scenari di uso in base al traffico da gestire. La tabella seguente consente di comprendere la differenza tra i due servizi di bilanciamento del carico:

| Tipo | Azure Load Balancer | gateway applicazione |
|---|---|---|
| Protocolli | UDP/TCP | HTTP/HTTPS |
| Prenotazione IP | Supportato | Non supportate | 
| Modalità di bilanciamento del carico | 5 tuple (IP di origine, porta di origine, IP di destinazione, porta di destinazione, tipo di protocollo) | Round robin<br>Routing basato su URL | 
| Modalità di bilanciamento del carico (IP di origine / sessioni permanenti) |  2 tuple (IP di origine e IP di destinazione), 3 tuple (IP di origine, IP di destinazione e porta). Possono aumentare o diminuire in base al numero di macchine virtuali | Affinità basata sui cookie<br>Routing basato su URL |
| Probe di integrità | Predefinito: intervallo di probe - 15 secondi. Esclusione dalla rotazione: 2 errori ripetuti. Supporta le probe definite dall'utente | Inattivo: intervallo di probe - 30 secondi. Esclusione dopo 5 errori consecutivi di traffico live o un errore di probe singolo in modalità di inattività. Supporta le probe definite dall'utente | 
| Offload SSL | Non supportate | Supportato | 
  

<!--HONumber=Oct16_HO2-->


