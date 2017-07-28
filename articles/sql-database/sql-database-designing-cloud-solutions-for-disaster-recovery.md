---
title: "Progettare un servizio a disponibilità elevata con database SQL di Azure | Documentazione Microsoft"
description: "Informazioni sulla progettazione di applicazioni per servizi a disponibilità elevata con database SQL di Azure."
keywords: "ripristino di emergenza cloud, soluzioni di ripristino di emergenza, backup dei dati delle app, replica geografica, pianificazione della continuità aziendale"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: e8a346ac-dd08-41e7-9685-46cebca04582
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 04/21/2017
ms.author: sashan
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 40fe0ae04eb94322356ed19773512e3bc383639c
ms.contentlocale: it-it
ms.lasthandoff: 05/18/2017


---
# <a name="designing-highly-available-services-using-azure-sql-database"></a>Progettazione di servizi a disponibilità elevata con database SQL di Azure

Durante la compilazione e distribuzione di servizi a disponibilità elevata in un database SQL Azure, usare [gruppi di failover e la replica geografica attiva](sql-database-geo-replication-overview.md) per ottenere la resilienza agli errori di area e alle interruzioni irreversibili e consentire un rapido ripristino sui database secondari. Questo articolo illustra i modelli di applicazione comuni e tratta i vantaggi e i compromessi di ogni opzione in base ai requisiti di distribuzione dell'applicazione, il contratto di servizio di destinazione, la latenza del traffico e i costi. Per informazioni sulla replica geografica attiva con i pool elastici, vedere [Strategie di ripristino di emergenza per applicazioni che usano il pool elastico del database SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

## <a name="design-pattern-1-active-passive-deployment-for-cloud-disaster-recovery-with-a-co-located-database"></a>Modello di progettazione 1: distribuzione attiva/passiva per il ripristino di emergenza cloud mediante un database con percorso condiviso
Questa opzione è particolarmente indicata per le applicazioni con le caratteristiche seguenti:

* Istanza attiva in una singola area di Azure
* Dipendenza assoluta nell'accesso ai dati in lettura/scrittura
* La connettività in più aree tra l'applicazione Web e il database non è accettabile a causa della latenza e del costo del traffico    

In questo caso la topologia di distribuzione dell'applicazione viene ottimizzata per la gestione di situazioni di emergenza nelle aree interessate quando tutti i componenti dell'applicazione sono coinvolti e se ne rende necessario il failover come unità. Per la ridondanza geografica, la logica dell'applicazione e il database vengono replicati in un'altra area, ma non vengono utilzizati per il carico di lavoro dell'applicazione in condizioni normali. L'applicazione nell'area secondaria dovrebbe essere configurata per l'uso di un stringa di connessione SQL al database secondario. Gestione traffico è configurato per l'uso del [metodo di routing di failover](../traffic-manager/traffic-manager-configure-failover-routing-method.md).  

> [!NOTE]
> [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md) viene usato in questo articolo solo a scopi illustrativi. È possibile usare qualsiasi soluzione di bilanciamento del carico che supporta il metodo di routing di failover.    
>

Il diagramma seguente mostra questa configurazione prima di un'interruzione.

![Configurazione della replica geografica del database SQL. Ripristino di emergenza cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-1.png)

Dopo un'interruzione del servizio nell'area primaria, il servizio di database SQL rileva che il database primario non è accessibile e attiva un failover nel database secondario in base ai parametri dei criteri di failover automatico. A seconda del contratto di servizio dell'applicazione, è possibile decidere di configurare un periodo di tolleranza tra il rilevamento dell'interruzione e il failover stesso. La configurazione di un periodo di tolleranza riduce il rischio di perdita di dati nei casi in cui l'interruzione del servizio è irreversibile e non è possibile ripristinare rapidamente la disponibilità nell'area. Se il failover dell'endpoint viene avviato da Gestione traffico prima che il gruppo di failover attivi il failover del database, l'applicazione Web non è in grado di riconnettersi al database. Il tentativo dell'applicazione di ristabilire la connessione ha esito positivo automaticamente non appena verrà completato il failover del database. 

> [!NOTE]
> Per ottenere il failover completamente coordinato di applicazione e database, si deve definire il proprio metodo di monitoraggio e usare il failover manuale degli endpoint dell'applicazione Web e dei database.
>

Al termine dell'esecuzione del failover degli endpoint dell'applicazione e del database, l'applicazione riavvierà l'elaborazione delle richieste utente nell'area B e rimarrà condivisa con il database perché il database primario è ora nell'area B. Questo scenario è illustrato nel diagramma seguente. In tutti i diagrammi le linee continue indicano le connessioni attive, le linee con punti indicano le connessioni sospese e i segnali di stop indicano i trigger di azioni.

![Replica geografica: failover al database secondario. Backup dei dati delle app.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-2.png)

Se si verifica un'interruzione nell'area secondaria, il collegamento di replica tra il database primario e quello secondario è sospeso, ma il failover non viene attivato perché il database primario non è interessato. In questo caso la disponibilità dell'applicazione non viene modificata, ma l'applicazione risulta esposta a un rischio maggiore se entrambe le aree hanno esito negativo una dopo l'altra.

> [!NOTE]
> Per il ripristino di emergenza è consigliabile la configurazione con la distribuzione di applicazioni limitata a due aree. Infatti la maggior parte delle geografie di Azure ha solo due aree. Questa configurazione non proteggerà l'applicazione da un errore simultaneo irreparabile di entrambe le aree.  Nell'improbabile eventualità di un errore di questo genere, è possibile recuperare i database in una terza area con un'[operazione di ripristino geografico](sql-database-disaster-recovery.md#recover-using-geo-restore).
>

Dopo che è stata ridotta l'entità dell'interruzione, il database secondario viene automaticamente risincronizzato con quello primario. Durante la sincronizzazione, le prestazioni di quello primario potrebbero essere leggermente rallentate a seconda della quantità di dati da sincronizzare. Il diagramma seguente illustra un'interruzione nell'area secondaria.

![Database secondario sincronizzato con il database primario. Ripristino di emergenza cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-3.png)

I **vantaggi** chiave di questo modello di progettazione sono:

* La stessa applicazione Web viene distribuita in entrambe le aree senza alcuna configurazione specifica dell'area e nessuna logica aggiuntiva per rispondere al failover. 
* Le prestazioni dell'applicazione non vengono rallentate dal failover, perché l'applicazione Web e il database condividono sempre lo stesso percorso.

Il **compromesso** principale è che l'istanza dell'applicazione ridondante nell'area secondaria viene usata solo per il ripristino di emergenza.

## <a name="design-pattern-2-active-active-deployment-for-application-load-balancing"></a>Modello di progettazione 2: Distribuzione attiva/attiva per il bilanciamento del carico dell'applicazione
Questa opzione di ripristino di emergenza cloud è particolarmente indicata per le applicazioni con le caratteristiche seguenti:

* Rapporto elevato tra letture e scritture del database
* La latenza di lettura del database è più critica per l'esperienza utente finale rispetto alla latenza di scrittura 
* Logica di sola lettura che può essere separata dalla logica di lettura/scrittura usando una stringa di connessione diversa
* Logica di sola lettura che non dipende dai dati che vengono completamente sincronizzati con gli ultimi aggiornamenti  

Se l'applicazione presenta queste caratteristiche, il bilanciamento del carico delle connessioni degli utenti finali in più istanze dell'applicazione in aree diverse può migliorare notevolmente l'esperienza complessiva dell'utente finale. Due delle aree devono essere selezionate come coppia di ripristino di emergenza e il gruppo di failover deve includere i database in queste aree. Per implementare il bilanciamento del carico, ogni area deve disporre di un'istanza attiva dell'applicazione con la logica di lettura/scrittura connessa all'endpoint del listener di lettura/scrittura del gruppo di failover. Ciò garantisce che il failover venga avviato automaticamente se il database primario è stato interessato da un'interruzione del servizio. La logica di sola lettura (RO) nell'applicazione Web deve connettersi direttamente al database in tale area. Gestione traffico dovrebbe essere configurato per l'uso del [routing delle prestazioni](../traffic-manager/traffic-manager-configure-performance-routing-method.md) con il [monitoraggio dell'endpoint](../traffic-manager/traffic-manager-monitoring.md) abilitato per ogni istanza dell'applicazione.

È consigliabile distribuire un'applicazione di monitoraggio simile a quella del modello n. 1, che però, a differenza di quanto accade nel modello n. 1, non sarà responsabile dell'attivazione del failover dell'endpoint.

> [!NOTE]
> Anche se questo modello usa più di un database secondario, solo il database secondario nell'area B viene usato per il failover e deve far parte del gruppo di failover.
>

Gestione traffico dovrebbe essere configurato per il routing delle prestazioni per poter indirizzare le connessioni utente all'istanza dell'applicazione più vicina alla posizione geografica dell'utente. Il diagramma seguente illustra questa configurazione prima di un'interruzione.

![Nessuna interruzione: routing delle prestazioni all'applicazione più vicina. Replica geografica.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-1.png)

Se viene rilevata un'interruzione del database nell'area A, il gruppo di failover avvierà automaticamente il failover del database primario nell'area A per il database secondario nell'area B. Aggiornerà automaticamente anche l'endpoint del listener di lettura-scrittura per l'area B in modo che le connessioni di lettura/scrittura nell'applicazione Web non vengano interessate. Gestione traffico esclude l'endpoint offline dalla tabella di routing, ma continua a reindirizzare il traffico dell'utente finale alle rimanenti istanze online. Le stringhe di connessione SQL di sola lettura non vengono interessate perché puntano sempre al database nella stessa area. 

Il diagramma seguente illustra la nuova configurazione dopo il failover.

![Configurazione dopo il failover. Ripristino di emergenza cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-2.png)

In caso di interruzione in una delle aree secondarie, Gestione traffico rimuove automaticamente l'endpoint offline in tale area dalla tabella di routing. Il canale di replica nel database secondario in tale area viene sospeso. Poiché le aree rimanenti ricevono il traffico dell'utente aggiuntivo in questo scenario, le prestazioni dell'applicazione potrebbero risultare rallentate durante l'interruzione. Dopo che l'entità dell'interruzione è stata ridotta, il database secondario nell'area interessata viene immediatamente sincronizzato con quello primario. Durante la sincronizzazione, le prestazioni del database primario potrebbero essere leggermente rallentate a seconda della quantità di dati da sincronizzare. Il diagramma seguente illustra un'interruzione nell'area B.

![Interruzione nell'area secondaria. Ripristino di emergenza cloud - Replica geografica.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-3.png)

Il **vantaggio** chiave di questo modello di progettazione è la scalabilità del carico di lavoro dell'applicazione in più database secondari per raggiungere le prestazioni utente ottimali. I **compromessi** di questa opzione sono:

* Le connessioni in lettura/scrittura tra le istanze dell'applicazione e il database hanno latenza e costi variabili
* Le prestazioni dell'applicazione vengono rallentate durante l'interruzione

> [!NOTE]
> Un approccio simile può essere adottato per l'offload di carichi di lavoro specializzati, ad esempio processi di creazione di report, strumenti di business intelligence o backup. Poiché questi carichi di lavoro usano in genere una considerevole quantità di risorse del database, è consigliabile riservare loro uno dei database secondari con un livello di prestazioni corrispondente al carico di lavoro anticipato.
>

## <a name="design-pattern-3-active-passive-deployment-for-data-preservation"></a>Modello di progettazione 3: Distribuzione attiva/passiva per la conservazione dei dati
Questa opzione è particolarmente indicata per le applicazioni con le caratteristiche seguenti:

* Qualsiasi perdita di dati rappresenta un rischio aziendale elevato. Il failover del database può essere usato solo come ultima soluzione se l'interruzione è irreversibile.
* L'applicazione supporta le modalità di sola lettura e lettura/scrittura delle operazioni e può funzionare in "modalità di sola lettura" per un periodo di tempo.

In questo modello l'applicazione passa alla modalità di sola lettura quando le connessioni di lettura-scrittura iniziano a dare errori di timeout. L'applicazione Web viene distribuita in entrambe le aree e include una connessione all'endpoint del listener di lettura/scrittura e una diversa connessione all'endpoint del listener di sola lettura. Gestione traffico dovrebbe essere configurato per l'uso del [routing del failover](../traffic-manager/traffic-manager-configure-failover-routing-method.md) con il [monitoraggio dell'endpoint](../traffic-manager/traffic-manager-monitoring.md) abilitato per l'endpoint dell'applicazione in ogni area.

Il diagramma seguente illustra questa configurazione prima di un'interruzione.

![Distribuzione attiva-passiva prima del failover. Ripristino di emergenza cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-1.png)

Quando Gestione traffico rileva un errore di connettività nell'area A, automaticamente passa il traffico degli utenti all'istanza dell'applicazione nell'area B. Con questo modello, è importante impostare il periodo di tolleranza con perdita dei dati su un valore sufficientemente elevato, ad esempio 24 ore. Ciò garantisce che la perdita di dati venga impedita se l'interruzione del servizio viene risolta entro tale periodo di tempo. Quando l'applicazione Web nell'area B viene attivata, le operazioni di lettura-scrittura iniziano ad avere esito negativo. A questo punto, si passa alla modalità di sola lettura. In questa modalità le richieste vengono indirizzate automaticamente al database secondario. In caso di errore irreversibile non è possibile risolvere l'interruzione del servizio entro il periodo di tolleranza e il gruppo di failover attiva il failover. Dopo di che il listener di lettura-scrittura diventa disponibile e le chiamate smettono di avere esito negativo. Tale condizione è illustrata dal diagramma seguente.

![Interruzione: applicazione in modalità di sola lettura. Ripristino di emergenza cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-2.png)

Se l'interruzione nell'area primaria viene risolta entro il periodo di tolleranza, Gestione traffico rileva il ripristino della connettività nell'area primaria e ripassa il traffico utente all'istanza dell'applicazione nell'area A. Tale istanza dell'applicazione riprende e opera in modalità di sola lettura usando il database primario nell'area A.

In caso di interruzione nell'area B, Gestione traffico rileva l'errore dell'endpoint dell'applicazione nell'area B e il gruppo di failover passa il listener di sola lettura all'area A. Questa interruzione non influisce sull'esperienza dell'utente finale, ma il database primario verrà esposto durante l'interruzione del servizio. Tale condizione è illustrata dal diagramma seguente.

![Interruzione: database secondario. Ripristino di emergenza cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-3.png)

Dopo che l'interruzione del servizio è stata risolta, il database secondario viene immediatamente sincronizzato con quello primario e il listener di sola lettura viene passato nuovamente al database secondario nell'area B. Durante la sincronizzazione, le prestazioni del database primario potrebbero essere leggermente influenzate a seconda della quantità di dati che deve essere sincronizzata.

Questo modello di progettazione presenta diversi **vantaggi**:

* Impedisce la perdita dei dati durante le interruzioni temporanee.
* Il tempo di inattività dipende solo dalla velocità con cui Gestione traffico rileva l'errore di connettività, che è configurabile.

Il **compromesso** è:

* L'applicazione deve poter funzionare in modalità di sola lettura.

> [!NOTE]
> In caso in interruzione permanente del servizio nell'area, attivare manualmente il failover del database e accettare la perdita dei dati. L'applicazione sarà funzionale nell'area secondaria con l'accesso in lettura/scrittura al database.
>

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Pianificazione della continuità aziendale: Scegliere una progettazione di applicazioni per il ripristino di emergenza cloud
La strategia di ripristino di emergenza cloud specifica può combinare o estendere questi modelli di progettazione per soddisfare al meglio le esigenze dell'applicazione.  Come accennato in precedenza, la strategia scelta si basa sul contratto di servizio che si vuole offrire ai clienti e sulla topologia di distribuzione dell'applicazione. Per facilitare la decisione, la tabella seguente confronta le scelte in base alla perdita dei dati stimata o all'obiettivo del punto di ripristino (RPO) e al tempo di recupero stimato (ERT).

| Modello | RPO | ERT |
|:--- |:--- |:--- |
| Distribuzione attiva/passiva per il ripristino di emergenza con accesso al database con percorso condiviso |Accesso in lettura/scrittura < 5 sec |Ora di rilevamento dell'errore + DNS TTL |
| Distribuzione attiva/attiva per il bilanciamento del carico dell'applicazione |Accesso in lettura/scrittura < 5 sec |Ora di rilevamento dell'errore + DNS TTL |
| Distribuzione attiva/passiva per la conservazione dei dati |Accesso in sola lettura < 5 sec | Accesso in sola lettura = 0 |
||Accesso in lettura/scrittura = zero | Accesso in lettura-scrittura = ora di rilevamento dell'errore + periodo di tolleranza con perdita di dati |
|||

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sui backup automatici del database SQL di Azure, vedere [Panoramica: Backup automatici del database SQL](sql-database-automated-backups.md)
* Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)
* Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md)
* Per altre informazioni sulle opzioni di ripristino più veloci, vedere [Panoramica: Replica geografica attiva per il database SQL di Azure](sql-database-geo-replication-overview.md).  
* Per altre informazioni sull'uso di backup automatici per l'archiviazione, vedere [Copia del database](sql-database-copy.md)
* Per informazioni sulla replica geografica attiva con i pool elastici, vedere [Strategie di ripristino di emergenza per applicazioni che usano il pool elastico del database SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

