<properties 
   pageTitle="Progettazione di soluzioni cloud per il ripristino di emergenza mediante la replica geografica del database SQL"
   description="Informazioni su come progettare la soluzione cloud per il ripristino di emergenza scegliendo il modello di failover appropriato."
   services="sql-database"
   documentationCenter="" 
   authors="sashan" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="10/07/2015"
   ms.author="sashan"/>

# Progettazione di applicazioni cloud per la continuità aziendale mediante la replica geografica

Esistono diversi modi per sfruttare la replica geografica in un database SQL quando si progetta l'applicazione per la continuità aziendale. I fattori di base della progettazione includono la topologia di distribuzione dell'applicazione, il contratto di servizio di destinazione, la latenza del traffico e i costi. In questo articolo verrà preso in esame il modello di applicazione comune e verranno illustrati i vantaggi e i compromessi di ogni opzione.

## Modello di progettazione 1: Distribuzione attiva/passiva per il ripristino di emergenza con database con percorso condiviso

Questa opzione è particolarmente indicata per le applicazioni con le caratteristiche seguenti:

+ istanza attiva in una singola area di Azure
+ dipendenza assoluta nell'accesso ai dati in lettura/scrittura 
+ la connettività in più aree tra la logica dell'applicazione e il database non è accettabile a causa della latenza e del costo del traffico    

In questo caso la topologia di distribuzione dell'applicazione viene ottimizzata per la gestione di situazioni di emergenza nelle aree interessate quando tutti i componenti dell'applicazione sono coinvolti e se ne rende necessario il failover come unità. Per la ridondanza geografica sia la logica dell'applicazione che il database vengono replicati in un'altra area, ma non sono usati per il carico di lavoro dell'applicazione in condizioni normali. L'applicazione nell'area secondaria dovrebbe essere configurata per l'uso di un stringa di connessione SQL al database secondario. Gestione traffico è configurato per l'uso del [metodo di routing di failover](traffic-manager-configure-failover-load-balancing.md).

> [AZURE.NOTE] [Azure traffic manager](traffic-manager-overview.md) viene usato in questo articolo solo a scopi illustrativi. È possibile usare qualsiasi soluzione di bilanciamento del carico che supporta il metodo di routing di failover.
 
Oltre alle istanze dell'applicazione principale, è consigliabile distribuire una piccola [applicazione ruolo di lavoro](cloud-services-choose-me.md#tellmecs) che monitora il database primario inviando regolarmente comandi di sola lettura T-SQL. È possibile usarla per attivare automaticamente il failover, per generare un avviso nella console di amministrazione dell'applicazione o per entrambi gli scopi. Per assicurarsi che il monitoraggio non venga compromesso da interruzioni che colpiscono l'intera area, è consigliabile distribuire le istanze dell'applicazione di monitoraggio in ogni area e connetterle al database nell'altra area, ma solo l'istanza nell'area secondaria deve essere attiva.

> [AZURE.NOTE]Se si usa la [replica geografica attiva](https://msdn.microsoft.com/library/azure/dn741339.aspx), è possibile mantenere attive entrambe le applicazioni di monitoraggio e analizzare il database sia primario che secondario. Quest'ultimo può essere usato per rilevare un errore nell'area secondaria e avvisare quando l'applicazione non è protetta.

Il diagramma seguente mostra questa configurazione prima di un'interruzione.

![Figura 1](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-1.png)

Dopo un'interruzione nell'area primaria l'applicazione di monitoraggio rileva che il database primario non è accessibile e registra un avviso. A seconda del contratto di servizio dell'applicazione, è possibile decidere quanti probe di monitoraggio consecutivi devono avere esito negativo prima di dichiarare un'interruzione del database. Per ottenere il failover coordinato dell'endpoint dell'applicazione e del database, è necessario che l'applicazione di monitoraggio esegua i passaggi seguenti:

1. [aggiornare lo stato dell'endpoint primario](https://msdn.microsoft.com/library/hh758250.aspx) per attivare il failover dell'endpoint.
2. chiamare il database secondario per [avviare il failover del database](https://msdn.microsoft.com/library/azure/dn509573.aspx) 

Dopo il failover, l'applicazione elaborerà le richieste degli utenti nell'area secondaria, ma rimarrà nello stesso percorso del database perché il database primario ora si trova nell'area secondaria, come è illustrato dal diagramma seguente. In tutti i diagrammi le linee continue indicano le connessioni attive, le linee con punti indicano le connessioni sospese e i segnali di stop indicano i trigger di azioni.


![Figura 2](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-2.png)

Se si verifica un'interruzione nell'area secondaria, il collegamento di replica tra il database primario e quello secondario verrà sospeso e l'applicazione di monitoraggio registrerà un avviso relativo all'esposizione del database primario. Ciò non influirà sulle prestazioni dell'applicazione, che però risulterà esposta e quindi sarà a rischio maggiore qualora entrambe le aree abbiano esito negativo una dopo l'altra.

> [AZURE.NOTE]Si consigliano solo configurazioni di distribuzione con un'unica area DR. Infatti la maggior parte delle geografie di Azure ha due aree. Queste configurazioni non proteggeranno l'applicazione da un errore irreparabile di entrambe le aree. Nell'improbabile eventualità di un errore di questo genere, è possibile recuperare i database in una terza area con un'[operazione di ripristino geografico](sql-database-disaster-recovery.md#recovery-using-geo-restore).
 
Una volta ridotta l'entità dell'interruzione, il database secondario verrà automaticamente sincronizzato con quello primario. Durante la sincronizzazione, le prestazioni di quello primario potrebbero essere leggermente rallentate a seconda della quantità di dati da sincronizzare. Il diagramma seguente illustra un'interruzione nell'area secondaria.

![Figura 3](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-3.png)


I **vantaggi** chiave di questo modello di progettazione sono:

+ la stringa di connessione SQL viene impostata durante la distribuzione dell'applicazione in ogni area e non viene modificata dopo il failover
+ le prestazioni dell'applicazione non vengono rallentate dal failover perché l'applicazione e il database sono sempre nello stesso percorso 

Il **compromesso** principale è che l'istanza dell'applicazione ridondante nell'area secondaria viene usata solo per il ripristino di emergenza.

## Modello di progettazione 2: Distribuzione attiva/attiva per il bilanciamento del carico dell'applicazione
Questa opzione è particolarmente indicata per le applicazioni con le caratteristiche seguenti:

+ rapporto elevato tra letture e scritture del database
+ la latenza di scrittura del database non influisce sull'esperienza utente finale  
+ la logica di sola lettura può essere separata dalla logica di lettura/scrittura usando una stringa di connessione diversa 
+ la logica di sola lettura non dipende dai dati che vengono completamente sincronizzati con gli ultimi aggiornamenti  

Se l'applicazione presenta queste caratteristiche, il bilanciamento del carico delle connessioni degli utenti finali in più istanze dell'applicazione in aree diverse può migliorare le prestazioni e l'esperienza utente finale. A questo scopo, ogni area dovrebbe avere un'istanza attiva dell'applicazione con la logica di lettura/scrittura connessa al database primario nell'area primaria. La logica di sola lettura dovrebbe essere connessa a un database secondario nella stessa area dell'istanza dell'applicazione. Gestione traffico dovrebbe essere configurato per l'uso del [routing round robin](traffic-manager-configure-round-robin-load-balancing.md) o del [routing delle prestazioni](traffic-manager-configure-performance-load-balancing.md) con il [monitoraggio dell'endpoint](traffic-manager-monitoring.md) abilitato per ogni istanza dell'applicazione.

È consigliabile distribuire un'applicazione di monitoraggio simile a quella del modello n. 1, che però, a differenza di quanto accade nel modello n. 1, non sarà responsabile dell'attivazione del failover dell'endpoint.

> [AZURE.NOTE]Anche se questo modello usa più di un database secondario, per il failover ne verrà usato solo uno per i motivi evidenziati in precedenza. Poiché questo modello richiede l'accesso in sola lettura al database secondario, è necessaria la [replica geografica attiva](https://msdn.microsoft.com/library/azure/dn741339.aspx).

Gestione traffico dovrebbe essere configurato per il routing delle prestazioni per poter indirizzare le connessioni utente all'istanza dell'applicazione più vicina alla posizione geografica dell'utente. Il diagramma seguente illustra questa configurazione prima di un'interruzione. ![Figura 4](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-1.png)

Se viene rilevata l'interruzione di un database nell'area primaria, avviare il failover del database primario in una delle aree secondarie per modificare la posizione del database primario. Gestione traffico escluderà automaticamente l'endpoint offline dalla tabella di routing, ma continuerà a instradare il traffico dell'utente finale alle rimanenti istanze online. Poiché il database primario ora si trova in un'area diversa, tutte le istanze online devono modificare la stringa di connessione SQL in lettura/scrittura per connettersi al nuovo database primario. È importante apportare questa modifica prima di avviare il failover del database. Le stringhe di connessione SQL di sola lettura dovrebbero restare invariate perché puntano sempre al database nella stessa area. I passaggi del failover sono:

1. modificare le stringhe di connessione SQL in lettura/scrittura in modo che puntino al nuovo database primario
2. chiamare il database secondario designato per [avviare il failover del database](https://msdn.microsoft.com/
3. /library/azure/dn509573.aspx) 

Il diagramma seguente illustra la nuova configurazione dopo il failover. ![Figura 5](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-2.png)

In caso di interruzione in una delle aree secondarie, Gestione traffico rimuoverà automaticamente l'endpoint offline in tale area dalla tabella di routing. Il canale di replica nel database secondario in tale area verrà sospeso. Poiché le aree rimanenti riceveranno traffico utente aggiuntivo, le prestazioni dell'applicazione potrebbero risultare rallentate durante l'interruzione. Una volta ridotta l'entità dell'interruzione, il database secondario nell'area interessata verrà immediatamente sincronizzato con quello primario. Durante la sincronizzazione, le prestazioni di quello primario potrebbero essere leggermente rallentate a seconda della quantità di dati da sincronizzare. Il diagramma seguente illustra un'interruzione in una delle aree secondarie.

![Figura 6](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-3.png)

Il **vantaggio** chiave di questo modello di progettazione è la scalabilità del carico di lavoro dell'applicazione in più database secondari per raggiungere le prestazioni utente ottimali. I **compromessi** di questa opzione sono:

+ le connessioni in lettura/scrittura tra le istanze dell'applicazione e il database hanno latenza e costi variabili
+ le prestazioni dell'applicazione vengono rallentate durante l'interruzione
+ le istanze dell'applicazione devono modificare in modo dinamico la stringa di connessione SQL dopo il failover del database.  

> [AZURE.NOTE]Un approccio simile può essere adottato per l'offload di carichi di lavoro specializzati, ad esempio processi di creazione di report, strumenti di business intelligence o backup. Poiché questi carichi di lavoro usano in genere una considerevole quantità di risorse del database, è consigliabile riservare loro uno dei database secondari con un livello di prestazioni corrispondente al carico di lavoro anticipato.

## Modello di progettazione 3: Distribuzione attiva/passiva per la conservazione dei dati  
Questa opzione è particolarmente indicata per le applicazioni con le caratteristiche seguenti:

+ se anche una perdita dei dati fosse un rischio elevato per l'attività, il failover del database può essere usato solo come ultima soluzione se l'interruzione è definitiva
+ l'applicazione può funzionare in "modalità sola lettura" per un determinato periodo di tempo 

In questo modello l'applicazione passa alla modalità di sola lettura quando viene connessa al database secondario. La logica dell'applicazione nell'area primaria si trova nello stesso percorso del database primario e funziona in modalità lettura/scrittura, la logica dell'applicazione nell'area secondaria si trova nello stesso percorso del database secondario ed è pronta a funzionare in modalità in sola lettura. Gestione traffico dovrebbe essere configurato per l'uso del [routing del failover](traffic-manager-configure-failover-load-balancing.md) con il [monitoraggio dell'endpoint](traffic-manager-monitoring.md) abilitato per entrambe le istanze dell'applicazione.

Il diagramma seguente illustra questa configurazione prima di un'interruzione. ![Figura 7](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-1.png)

Quando Gestione traffico rileva un errore di connettività nell'area primaria, passerà automaticamente il traffico utente all'istanza dell'applicazione nell'area secondaria. Con questo modello è importante **non** avviare il failover del database dopo avere rilevato l'interruzione. L'applicazione nell'area secondaria viene attivata e funziona in modalità sola lettura usando il database secondario. Tale condizione è illustrata dal diagramma seguente.

![Figura 8](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-2.png)

Una volta ridotta l'entità dell'interruzione nell'area primaria, Gestione traffico rileverà il ripristino della connettività nell'area primaria e ripasserà il traffico utente all'istanza dell'applicazione nell'area primaria. Tale istanza dell'applicazione viene riavviata e funziona in modalità lettura/scrittura usando il database primario.

> [AZURE.NOTE]Poiché questo modello richiede l'accesso in sola lettura al database secondario, è necessaria la [replica geografica attiva](https://msdn.microsoft.com/library/azure/dn741339.aspx).

In caso di interruzione nell'area secondaria, Gestione traffico contrassegnerà l'endpont dell'applicazione nell'area primaria come danneggiato e il canale di replica verrà sospeso. Tuttavia non avrà effetto sulle prestazioni dell'applicazione durante l'interruzione. Una volta ridotta l'entità dell'interruzione, il database secondario verrà immediatamente sincronizzato con quello primario. Durante la sincronizzazione, le prestazioni di quello primario potrebbero essere leggermente rallentate a seconda della quantità di dati da sincronizzare.

![Figura 8](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-3.png)

Questo modello di progettazione presenta diversi **vantaggi**:

+ impedisce la perdita dei dati durante le interruzioni temporanee
+ non richiede la distribuzione di un'applicazione di monitoraggio perché il ripristino viene attivato da Gestione traffico 
+ il tempo di inattività dipende solo dalla velocità con cui Gestione traffico rileva l'errore di connettività, che è configurabile

I **compromessi** sono:

+ l'applicazione deve poter funzionare in modalità sola lettura
+ è necessario passare all'applicazione in modo dinamico quando è connessa a un database di sola lettura
+ la ripresa delle operazioni di lettura/scrittura richiede il ripristino dell'area primaria, che comporta la possibile disabilitazione dell'accesso completo ai dati per ore o anche per giorni 

> [AZURE.NOTE]In caso in interruzione permanente del servizio nell'area, sarà necessario attivare manualmente il failover del database e accettare la perdita dei dati. L'applicazione sarà funzionale nell'area secondaria con l'accesso in lettura/scrittura al database.

## Riepilogo

La strategia di ripristino di emergenza specifica può combinare o estendere questi modelli per soddisfare al meglio le esigenze dell'applicazione. Come accennato in precedenza, la strategia scelta si baserà sul contratto di servizio che si vuole offrire ai clienti e sulla topologia di distribuzione dell'applicazione. Per facilitare la decisione, la tabella seguente confronta le scelte in base alla perdita dei dati stimata o all'obiettivo del punto di ripristino (RPO) e al tempo di recupero stimato (ERT).

| Modello | RPO | ERT 
| :--- |:--- | :--- 
| Distribuzione attiva/passiva per il ripristino di emergenza con accesso al database con percorso condiviso | Accesso in lettura/scrittura < 5 sec | Tempo di rilevamento errore + chiamata API di failover + test di verifica dell'applicazione 
| Distribuzione attiva/attiva per il bilanciamento del carico dell'applicazione | Accesso in lettura/scrittura < 5 sec | Tempo di rilevamento errore + chiamata API di failover + modifica della stringa di connessione SQL + test di verifica dell'applicazione
| Distribuzione attiva/passiva per la conservazione dei dati | Accesso di sola lettura < 5 sec Accesso di lettura/scrittura = zero | Accesso di sola lettura = tempo di rilevamento errore di connettività + test di verifica dell'applicazione <br>Accesso di lettura/scrittura = tempo di riduzione dell'entità dell'interruzione 

<!---HONumber=Oct15_HO2-->