<properties 
   pageTitle="Esercitazioni per il ripristino di emergenza del database SQL" 
   description="Istruzioni e procedure consigliate relative all'uso del database SQL di Azure per eseguire esercitazioni per il ripristino di emergenza che consentono di mantenere la resilienza delle applicazioni aziendali cruciali in caso di errori e interruzioni del servizio." 
   services="sql-database" 
   documentationCenter="" 
   authors="mihaelablendea" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="07/15/2015"
   ms.author="mihaelab"/>

#Esercitazione per il ripristino di emergenza

È consigliabile verificare periodicamente la conformità delle applicazioni per il flusso di lavoro di ripristino. La verifica del comportamento di un'applicazione e delle implicazioni legate alla perdita di dati e/o all'interruzione del servizio che comporta il failover è una buona norma di progettazione. Inoltre, questa verifica è richiesta dalla maggior parte degli standard del settore come parte della certificazione di continuità aziendale.

L'esercitazione per il ripristino di emergenza prevede l'esecuzione delle attività seguenti:

- Simulazione dell'interruzione del livello dati
- Ripristino 
- Convalida dell'integrità dell'applicazione dopo il ripristino

A seconda della modalità di [progettazione per la continuità aziendale](sql-database-business-continuity.md), il flusso di lavoro dell'esercitazione può variare. Di seguito sono descritte le procedure consigliate per eseguire un'esercitazione per il ripristino di emergenza nel contesto del database SQL di Azure.

##Ripristino geografico

Per evitare il rischio di perdita di dati durante l'esecuzione di un'esercitazione per il ripristino di emergenza, è consigliabile usare un ambiente di test creando una copia dell'ambiente di produzione e usandolo per verificare il flusso di lavoro di failover dell'applicazione.
 
####Simulazione dell'interruzione del servizio

- Simulare l'interruzione del servizio eliminando o rinominando il database di origine e causare un errore di connettività dell'applicazione. In questo modo è possibile convalidare il rilevamento dell'interruzione e la generazione degli avvisi e misurare l'obiettivo del tempo di ripristino.

####Ripristino

- Eseguire il ripristino geografico del database in un server diverso, come descritto [qui](sql-database-disaster-recovery.md). 
- Modificare la configurazione dell'applicazione per connettersi ai database ripristinati e seguire la guida [Finalizzare un database ripristinato](sql-database-recovered-finalize.md) per completare il ripristino.

####Convalida

- Completare l'esercitazione verificando l'integrità dell'applicazione dopo il ripristino (ad esempio, stringhe di connessione, account di accesso, test di funzionalità di base o altre verifiche correlate alle procedure standard di convalida delle applicazioni).

##Replica geografica standard

Un database protetto mediante la replica geografica standard può avere un solo database secondario non leggibile. L'esercitazione comporterà la terminazione forzata del collegamento e questo punto il database non risulterà più protetto. Esiste inoltre il rischio di una perdita di dati. Si sconsiglia quindi di eseguire test di questo tipo su database di produzione. In alternativa, può essere opportuno creare una copia dell'ambiente di produzione e usarla per verificare il flusso di lavoro di failover dell'applicazione.

####Simulazione dell'interruzione del servizio

- Simulare il carico di lavoro sul database primario. Se il database primario è attivo al momento della terminazione, può verificarsi una perdita di dati, rendendo così più realistica l'esercitazione.
- Eliminare il database primario o [forzare la terminazione](sql-database-disaster-recovery.md) del collegamento sul lato database secondario.

####Ripristino

- Modificare la configurazione dell'applicazione in modo da impostare la connessione al database secondario precedentemente di sola lettura, che diventerà completamente accessibile e potrà essere usato dall'applicazione come nuovo database primario. 
- Seguire la guida [Finalizzare un database ripristinato](sql-database-recovered-finalize.md) per completare il ripristino.

####Convalida

- Completare l'esercitazione verificando l'integrità dell'applicazione dopo il ripristino (ad esempio, stringhe di connessione, account di accesso, test di funzionalità di base o altre verifiche correlate alle procedure standard di convalida delle applicazioni).

##Replica geografica attiva

L'esercitazione per il ripristino di emergenza verrà effettuata usando un server di destinazione parallelo e creando in tale server un altro set di database secondari di sola lettura. Usare una versione di test del livello applicazione per verificare l'integrità dei dati e dello stato dell'operazione tramite l'esecuzione di test su tale server dopo la terminazione forzata.

####Simulazione dell'interruzione del servizio

- [Creare un nuovo collegamento di replica geografica attiva](sql-database-business-continuity-design.md) dal database primario a un server di test secondario. Se il database primario è attivo al momento della terminazione, può verificarsi una perdita di dati, rendendo così più realistica l'esercitazione.
- [Forzare la terminazione](sql-database-disaster-recovery.md) del collegamento sul database secondario presente sul server di test.

####Ripristino

- Modificare la configurazione dell'applicazione in modo da impostare la connessione al database secondario precedentemente di sola lettura, che diventerà disponibile per operazioni di scrittura dopo la terminazione.
- Seguire la guida [Finalizzare un database ripristinato](sql-database-recovered-finalize.md) per completare il ripristino.

####Convalida

- Completare l'esercitazione verificando l'integrità dell'applicazione dopo il ripristino (ad esempio, stringhe di connessione, account di accesso, test di funzionalità di base o altre verifiche correlate alle procedure standard di convalida delle applicazioni).

<!---HONumber=Oct15_HO3-->