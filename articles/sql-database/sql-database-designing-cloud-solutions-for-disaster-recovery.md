---
title: Soluzioni di ripristino di emergenza cloud - Replica geografica attiva del database SQL | Microsoft Docs
description: Informazioni su come progettare soluzioni di ripristino di emergenza cloud per la pianificazione della continuità aziendale usando la replica geografica per il backup di dati delle app con il database SQL di Azure.
keywords: ripristino di emergenza cloud, soluzioni di ripristino di emergenza, backup dei dati delle app, replica geografica, pianificazione della continuità aziendale
services: sql-database
documentationcenter: ''
author: anosov1960
manager: jhubbard
editor: monicar

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 07/20/2016
ms.author: sashan

---
# Progettare un'applicazione per il ripristino di emergenza cloud mediante la replica geografica attiva nel database SQL
> [!NOTE]
> [Active Geo-Replication](sql-database-geo-replication-overview.md) è ora disponibile per tutti i database in tutti i livelli.
> 
> 

Informazioni su come utilizzare la [replica geografica attiva](sql-database-geo-replication-overview.md) nel database SQL per progettare applicazioni di database resilienti a guasti a livello di area e interruzioni irreversibili. Per la pianificazione della continuità aziendale, è necessario prendere in considerazione la topologia di distribuzione dell'applicazione, il contratto di servizio di destinazione, la latenza del traffico e i costi. Questo articolo esamina i modelli di applicazione comuni e illustra i vantaggi e gli svantaggi di ogni opzione. Per informazioni sulla replica geografica attiva con i pool elastici, vedere [Strategie di ripristino di emergenza per applicazioni che usano il pool elastico del database SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

## Modello di progettazione 1: distribuzione attiva/passiva per il ripristino di emergenza cloud mediante un database con percorso condiviso
Questa opzione è particolarmente indicata per le applicazioni con le caratteristiche seguenti:

* Istanza attiva in una singola area di Azure
* Dipendenza assoluta nell'accesso ai dati in lettura/scrittura
* Connettività in più aree tra la logica dell'applicazione e il database non accettabile a causa della latenza e del costo del traffico

In questo caso la topologia di distribuzione dell'applicazione viene ottimizzata per la gestione di situazioni di emergenza nelle aree interessate quando tutti i componenti dell'applicazione sono coinvolti e se ne rende necessario il failover come unità. Per la ridondanza geografica, la logica dell'applicazione e il database vengono replicati in un'altra area, ma non vengono utilzizati per il carico di lavoro dell'applicazione in condizioni normali. L'applicazione nell'area secondaria dovrebbe essere configurata per l'uso di un stringa di connessione SQL al database secondario. Gestione traffico è configurato per l'uso del [metodo di routing di failover](../traffic-manager/traffic-manager-configure-failover-routing-method.md).

> [!NOTE]
> [Azure traffic manager](../traffic-manager/traffic-manager-overview.md) viene usato in questo articolo solo a scopi illustrativi. È possibile usare qualsiasi soluzione di bilanciamento del carico che supporta il metodo di routing di failover.
> 
> 

Oltre alle istanze dell'applicazione principale, è consigliabile distribuire una piccola [applicazione ruolo di lavoro](../cloud-services/cloud-services-choose-me.md#tellmecs) che garantisca il monitoraggio del database primario inviando regolarmente comandi T-SQL di sola lettura. È possibile usarla per attivare automaticamente il failover, per generare un avviso nella console di amministrazione dell'applicazione o per entrambi gli scopi. Per assicurarsi che il monitoraggio non venga compromesso da interruzioni che colpiscono l'intera area, è opportuno distribuire le istanze dell'applicazione di monitoraggio in ogni area e connetterle al database primario nell'area primaria e al database secondario nell'area locale.

> [!NOTE]
> Entrambe le applicazioni di monitoraggio devono essere attive e operare sia sul database primario che su quello secondario. Quest'ultimo può essere usato per rilevare un errore nell'area secondaria e avvisare quando l'applicazione non è protetta.
> 
> 

Il diagramma seguente mostra questa configurazione prima di un'interruzione.

![Configurazione della replica geografica del database SQL. Ripristino di emergenza cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-1.png)

Dopo un'interruzione nell'area primaria l'applicazione di monitoraggio rileva che il database primario non è accessibile e registra un avviso. A seconda del contratto di servizio dell'applicazione, è possibile decidere quanti probe di monitoraggio consecutivi devono avere esito negativo prima di dichiarare un'interruzione del database. Per ottenere il failover coordinato dell'endpoint dell'applicazione e del database, è necessario che l'applicazione di monitoraggio esegua i passaggi seguenti:

1. [Aggiornare lo stato dell'endpoint primario](https://msdn.microsoft.com/library/hh758250.aspx) per attivare il failover dell'endpoint.
2. Chiamare il database secondario per [avviare il failover del database](sql-database-geo-replication-portal.md).

Dopo il failover l'applicazione elabora le richieste degli utenti nell'area secondaria, ma continua a condividere il percorso con il database perché il database primario ora si trova nell'area secondaria, secondo lo scenario illustrato dal diagramma seguente. In tutti i diagrammi le linee continue indicano le connessioni attive, le linee con punti indicano le connessioni sospese e i segnali di stop indicano i trigger di azioni.

![Replica geografica: failover al database secondario. Backup dei dati delle app.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-2.png)

Se si verifica un'interruzione nell'area secondaria, il collegamento di replica tra il database primario e quello secondario è sospeso e l'applicazione di monitoraggio registra un avviso relativo all'esposizione del database primario. Non si registrano effetti sulle prestazioni dell'applicazione, che però risulta esposta a un rischio maggiore se entrambe le aree abbiano esito negativo una dopo l'altra.

> [!NOTE]
> Si consigliano solo configurazioni di distribuzione con un'unica area DR. Infatti la maggior parte delle geografie di Azure ha due aree. Queste configurazioni non proteggeranno l'applicazione da un errore irreparabile di entrambe le aree. Nell'improbabile eventualità di un errore di questo genere, è possibile recuperare i database in una terza area con un'[operazione di ripristino geografico](sql-database-disaster-recovery.md#recovery-using-geo-restore).
> 
> 

Una volta ridotta l'entità dell'interruzione, il database secondario viene automaticamente sincronizzato con quello primario. Durante la sincronizzazione, le prestazioni di quello primario potrebbero essere leggermente rallentate a seconda della quantità di dati da sincronizzare. Il diagramma seguente illustra un'interruzione nell'area secondaria.

![Database secondario sincronizzato con il database primario. Ripristino di emergenza cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-3.png)

I **vantaggi** chiave di questo modello di progettazione sono:

* La stringa di connessione SQL viene impostata durante la distribuzione dell'applicazione in ogni area e non viene modificata dopo il failover.
* Le prestazioni dell'applicazione non vengono rallentate dal failover, perché l'applicazione e il database condividono sempre lo stesso percorso.

Il **compromesso** principale è che l'istanza dell'applicazione ridondante nell'area secondaria viene usata solo per il ripristino di emergenza.

## Modello di progettazione 2: Distribuzione attiva/attiva per il bilanciamento del carico dell'applicazione
Questa opzione di ripristino di emergenza cloud è particolarmente indicata per le applicazioni con le caratteristiche seguenti:

* Rapporto elevato tra letture e scritture del database
* Latenza di scrittura del database che non influisce sull'esperienza utente finale
* Logica di sola lettura che può essere separata dalla logica di lettura/scrittura usando una stringa di connessione diversa
* Logica di sola lettura che non dipende dai dati che vengono completamente sincronizzati con gli ultimi aggiornamenti

Se l'applicazione presenta queste caratteristiche, il bilanciamento del carico delle connessioni degli utenti finali in più istanze dell'applicazione in aree diverse può migliorare le prestazioni e l'esperienza dell'utente finale. Per implementare il servizio di bilanciamento del carico, ogni area deve avere un'istanza attiva dell'applicazione con la logica di lettura/scrittura connessa al database primario nell'area primaria. La logica di sola lettura dovrebbe essere connessa a un database secondario nella stessa area dell'istanza dell'applicazione. Gestione traffico dovrebbe essere configurato per l'uso del [routing round robin](../traffic-manager/traffic-manager-configure-round-robin-routing-method.md) o del [routing delle prestazioni](../traffic-manager/traffic-manager-configure-performance-routing-method.md) con il [monitoraggio dell'endpoint](../traffic-manager/traffic-manager-monitoring.md) abilitato per ogni istanza dell'applicazione.

È consigliabile distribuire un'applicazione di monitoraggio simile a quella del modello n. 1, che però, a differenza di quanto accade nel modello n. 1, non sarà responsabile dell'attivazione del failover dell'endpoint.

> [!NOTE]
> Anche se questo modello usa più di un database secondario, per il failover ne verrà usato solo uno per i motivi evidenziati in precedenza. Poiché questo modello richiede l'accesso in sola lettura al database secondario, è necessaria la replica geografica attiva.
> 
> 

Gestione traffico dovrebbe essere configurato per il routing delle prestazioni per poter indirizzare le connessioni utente all'istanza dell'applicazione più vicina alla posizione geografica dell'utente. Il diagramma seguente illustra questa configurazione prima di un'interruzione. ![Nessuna interruzione: routing delle prestazioni all'applicazione più vicina. Replica geografica.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-1.png)

Se viene rilevata l'interruzione di un database nell'area primaria, avviare il failover del database primario in una delle aree secondarie, modificndo la posizione del database primario. Gestione traffico esclude automaticamente l'endpoint offline dalla tabella di routing, ma continua a reindirizzare il traffico dell'utente finale alle rimanenti istanze online. Poiché il database primario ora si trova in un'area diversa, tutte le istanze online devono modificare la stringa di connessione SQL in lettura/scrittura per connettersi al nuovo database primario. È importante apportare questa modifica prima di avviare il failover del database. Le stringhe di connessione SQL di sola lettura dovrebbero restare invariate perché puntano sempre al database nella stessa area. I passaggi del failover sono:

1. Modificare le stringhe di connessione SQL in lettura/scrittura in modo che puntino al nuovo database primario.
2. Chiamare il database secondario designato per [avviare il failover del database](sql-database-geo-replication-portal.md).

Il diagramma seguente illustra la nuova configurazione dopo il failover. ![Configurazione dopo il failover. Ripristino di emergenza cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-2.png)

In caso di interruzione in una delle aree secondarie, Gestione traffico rimuove automaticamente l'endpoint offline in tale area dalla tabella di routing. Il canale di replica nel database secondario in tale area viene sospeso. Poiché le aree rimanenti ricevono il traffico dell'utente aggiuntivo in questo scenario, le prestazioni dell'applicazione potrebbero risultare rallentate durante l'interruzione. Una volta ridotta l'entità dell'interruzione, il database secondario nell'area interessata viene immediatamente sincronizzato con quello primario. Durante la sincronizzazione, le prestazioni di quello primario potrebbero essere leggermente rallentate a seconda della quantità di dati da sincronizzare. Il diagramma seguente illustra un'interruzione in una delle aree secondarie.

![Interruzione nell'area secondaria. Ripristino di emergenza cloud - Replica geografica.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-3.png)

Il **vantaggio** chiave di questo modello di progettazione è la scalabilità del carico di lavoro dell'applicazione in più database secondari per raggiungere le prestazioni utente ottimali. I **compromessi** di questa opzione sono:

* Le connessioni in lettura/scrittura tra le istanze dell'applicazione e il database hanno latenza e costi variabili
* Le prestazioni dell'applicazione vengono rallentate durante l'interruzione
* Le istanze dell'applicazione devono modificare in modo dinamico la stringa di connessione SQL dopo il failover del database.

> [!NOTE]
> Un approccio simile può essere adottato per l'offload di carichi di lavoro specializzati, ad esempio processi di creazione di report, strumenti di business intelligence o backup. Poiché questi carichi di lavoro usano in genere una considerevole quantità di risorse del database, è consigliabile riservare loro uno dei database secondari con un livello di prestazioni corrispondente al carico di lavoro anticipato.
> 
> 

## Modello di progettazione 3: Distribuzione attiva/passiva per la conservazione dei dati
Questa opzione è particolarmente indicata per le applicazioni con le caratteristiche seguenti:

* Qualsiasi perdita di dati rappresenta un rischio aziendale elevato. Il failover del database può essere usato solo come ultima soluzione se l'interruzione è definitiva.
* L'applicazione può funzionare in "modalità sola lettura" per un determinato periodo di tempo.

In questo modello l'applicazione passa alla modalità di sola lettura quando viene connessa al database secondario. La logica dell'applicazione nell'area primaria viene memorizzata con il database primario e opera in modalità di lettura-scrittura (RW). La logica dell'applicazione nell'area secondaria viene memorizzata con il database secondario ed è pronta a operare in modalità di sola lettura (RO). Gestione traffico dovrebbe essere configurato per l'uso del [routing del failover](../traffic-manager/traffic-manager-configure-failover-routing-method.md) con il [monitoraggio dell'endpoint](../traffic-manager/traffic-manager-monitoring.md) abilitato per entrambe le istanze dell'applicazione.

Il diagramma seguente illustra questa configurazione prima di un'interruzione. ![Distribuzione attiva-passiva prima del failover. Ripristino di emergenza cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-1.png)

Quando Gestione traffico rileva un errore di connettività nell'area primaria, passa automaticamente il traffico utente all'istanza dell'applicazione nell'area secondaria. Con questo modello è importante **non** avviare il failover del database dopo avere rilevato l'interruzione. L'applicazione nell'area secondaria viene attivata e funziona in modalità sola lettura usando il database secondario. Tale condizione è illustrata dal diagramma seguente.

![Interruzione: applicazione in modalità di sola lettura. Ripristino di emergenza cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-2.png)

Una volta ridotta l'entità dell'interruzione nell'area primaria, Gestione traffico rileva il ripristino della connettività nell'area primaria e ripassa il traffico utente all'istanza dell'applicazione nell'area primaria. Tale istanza dell'applicazione viene riavviata e funziona in modalità lettura/scrittura usando il database primario.

> [!NOTE]
> Poiché questo modello richiede l'accesso in sola lettura al database secondario, è necessaria la replica geografica attiva.
> 
> 

In caso di interruzione nell'area secondaria, Gestione traffico contrassegna l'endpoint dell'applicazione nell'area primaria come danneggiato e il canale di replica viene sospeso. Tuttavia, l'interruzione non avrà effetto sulle prestazioni dell'applicazione. Una volta ridotta l'entità dell'interruzione, il database secondario viene immediatamente sincronizzato con quello primario. Durante la sincronizzazione, le prestazioni di quello primario potrebbero essere leggermente rallentate a seconda della quantità di dati da sincronizzare.

![Interruzione: database secondario. Ripristino di emergenza cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-3.png)

Questo modello di progettazione presenta diversi **vantaggi**:

* Impedisce la perdita dei dati durante le interruzioni temporanee.
* Non richiede la distribuzione di un'applicazione di monitoraggio perché il ripristino viene attivato da Gestione traffico.
* Il tempo di inattività dipende solo dalla velocità con cui Gestione traffico rileva l'errore di connettività, che è configurabile.

I **compromessi** sono:

* L'applicazione deve poter funzionare in modalità di sola lettura.
* È necessario passare all'applicazione in modo dinamico quando è connessa a un database di sola lettura.
* La ripresa delle operazioni di lettura/scrittura richiede il ripristino dell'area primaria, che comporta la possibile disabilitazione dell'accesso completo ai dati per ore o addirittura giorni.

> [!NOTE]
> In caso in interruzione permanente del servizio nell'area, è necessario attivare manualmente il failover del database e accettare la perdita dei dati. L'applicazione sarà funzionale nell'area secondaria con l'accesso in lettura/scrittura al database.
> 
> 

## Pianificazione della continuità aziendale: Scegliere una progettazione di applicazioni per il ripristino di emergenza cloud
La strategia di ripristino di emergenza cloud specifica può combinare o estendere questi modelli di progettazione per soddisfare al meglio le esigenze dell'applicazione. Come accennato in precedenza, la strategia scelta si basa sul contratto di servizio che si vuole offrire ai clienti e sulla topologia di distribuzione dell'applicazione. Per facilitare la decisione, la tabella seguente confronta le scelte in base alla perdita dei dati stimata o all'obiettivo del punto di ripristino (RPO) e al tempo di recupero stimato (ERT).

| Modello | RPO | ERT |
|:--- |:--- |:--- |
| Distribuzione attiva/passiva per il ripristino di emergenza con accesso al database con percorso condiviso |Accesso in lettura/scrittura < 5 sec |Tempo di rilevamento errore + chiamata API di failover + test di verifica dell'applicazione |
| Distribuzione attiva/attiva per il bilanciamento del carico dell'applicazione |Accesso in lettura/scrittura < 5 sec |Tempo di rilevamento errore + chiamata API di failover + modifica della stringa di connessione SQL + test di verifica dell'applicazione |
| Distribuzione attiva/passiva per la conservazione dei dati |Accesso di sola lettura < 5 sec Accesso di lettura/scrittura = zero |Accesso di sola lettura = tempo di rilevamento errore di connettività + test di verifica dell'applicazione <br>Accesso di lettura/scrittura = tempo di riduzione dell'entità dell'interruzione |

## Passaggi successivi
* Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md)
* Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)
* Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md)
* Per altre informazioni sulle opzioni di ripristino più veloci, vedere [Replica geografica attiva](sql-database-geo-replication-overview.md)
* Per altre informazioni sull'uso di backup automatici per l'archiviazione, vedere [Copia del database](sql-database-copy.md)
* Per informazioni sulla replica geografica attiva con i pool elastici, vedere [Strategie di ripristino di emergenza per applicazioni che usano il pool elastico del database SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

<!---HONumber=AcomDC_0817_2016-->