---
title: Errori di connettività temporanei-database di Azure per MySQL
description: Informazioni su come gestire gli errori di connettività temporanei e connettersi in modo efficiente al database di Azure per MySQL.
keywords: connessione MySQL, stringa di connessione, problemi di connettività, errore temporaneo, errore di connessione, connessione efficiente
author: jan-eng
ms.author: janeng
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: b598fafbbfce9a2c0a824dd6530d07a5933a2873
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74765207"
---
# <a name="handle-transient-errors-and-connect-efficiently-to-azure-database-for-mysql"></a>Gestire gli errori temporanei e connettersi in modo efficiente al database di Azure per MySQL

Questo articolo descrive come gestire gli errori temporanei e connettersi in modo efficiente al database di Azure per MySQL.

## <a name="transient-errors"></a>Errori temporanei

Un errore temporaneo è un errore che si risolve in modo autonomo. Questi errori generalmente si manifestano come una connessione al server di database che viene interrotta. Inoltre non è possibile aprire nuove connessioni a un server. Possono verificarsi errori temporanei, ad esempio nel caso di un errore hardware o di rete. Un altro motivo potrebbe essere una nuova versione di un servizio PaaS che viene implementato. La maggior parte di questi eventi viene automaticamente attenuata dal sistema in meno di 60 secondi. Una procedura consigliata per la progettazione e lo sviluppo di applicazioni nel cloud è la previsione di errori temporanei, presupponendo che possano accadere in qualsiasi componente e in qualunque momento e che si disponga della logica appropriata per affrontare tali situazioni.

## <a name="handling-transient-errors"></a>Gestione degli errori temporanei

Gli errori temporanei devono essere gestiti usando la logica di ripetizione dei tentativi. Situazioni da considerare:

* Si verifica un errore quando si tenta di aprire una connessione
* Una connessione inattiva viene eliminata sul lato server. Se si prova a eseguire un comando non si riesce a eseguirlo
* Una connessione attiva che sta attualmente eseguendo un comando viene eliminata.

Il primo e il secondo caso sono abbastanza semplici da gestire. Provare ad aprire di nuovo la connessione. Quando l'operazione riesce, l'errore temporaneo è stato attenuato dal sistema. ed è possibile usare nuovamente il Database di Azure per MySQL. È consigliabile attendere prima di riprovare a effettuare la connessione. Interrompere temporaneamente l'operazione se i tentativi iniziali hanno esito negativo. In questo modo il sistema può usare tutte le risorse disponibili per superare la situazione di errore. È consigliabile seguire questa procedura:

* Attendere 5 secondi prima di riprovare.
* Per ogni tentativo successivo, aumentare l'attesa in modo esponenziale, fino a 60 secondi.
* Impostare un numero massimo di tentativi nel punto in cui l'applicazione considera l'operazione non riuscita.

Quando una connessione con una transazione attiva ha esito negativo, la gestione corretta del ripristino è più difficile. Possono presentarsi due casi. Se la transazione era di sola lettura, è opportuno riaprire la connessione e riprovare la transazione. Se tuttavia la transazione è stata anche scritta nel database, è necessario determinare se è stato eseguito il rollback della transazione o se l'errore è stato completato prima dell'errore temporaneo. In tal caso, è possibile che non sia stato ricevuto il riconoscimento del commit dal server di database.

Un modo per superare il problema consiste nel generare un ID univoco sul client usato per tutti i tentativi. Passare questo ID univoco come parte della transazione al server e archiviarlo in una colonna con un vincolo univoco. In questo modo è possibile riprovare a eseguire la transazione in tutta sicurezza. L'operazione avrà esito positivo se è stato eseguito il rollback della transazione precedente e l'ID univoco generato dal client non esiste ancora nel sistema. Il tentativo ha esito negativo quando indica una violazione della chiave duplicata se l'ID univoco è stato già archiviato, perché la transazione precedente è stata completata correttamente.

Se il programma comunica con il Database di Azure per MySQL tramite middleware di terze parti, chiedere al fornitore se il middleware include la logica di ripetizione dei tentativi per errori temporanei.

Verificare di testare la logica di ripetizione dei tentativi. Ad esempio, provare a eseguire il codice durante il ridimensionamento delle risorse di calcolo del database di Azure per il server MySQL. L'applicazione deve gestire senza problemi il breve tempo di inattività che si verifica durante questa operazione.

## <a name="connect-efficiently-to-azure-database-for-mysql"></a>Connetti in modo efficiente al database di Azure per MySQL

Le connessioni di database sono una risorsa limitata, pertanto l'uso efficace del pool di connessioni per accedere a database di Azure per MySQL ottimizza le prestazioni. La sezione seguente illustra come usare il pool di connessioni o connessioni permanenti per accedere in modo più efficace a database di Azure per MySQL.

## <a name="access-databases-by-using-connection-pooling-recommended"></a>Accedere ai database usando il pool di connessioni (scelta consigliata)

La gestione delle connessioni di database può avere un impatto significativo sulle prestazioni dell'applicazione nel suo insieme. Per ottimizzare le prestazioni dell'applicazione, l'obiettivo consiste nel ridurre il numero di volte in cui vengono stabilite le connessioni e il tempo per stabilire le connessioni nei percorsi di codice chiave. Per la connessione a database di Azure per MySQL è consigliabile usare il pool di connessioni di database o le connessioni permanenti. Il pool di connessioni di database gestisce la creazione, la gestione e l'allocazione delle connessioni al database. Quando un programma richiede una connessione al database, viene assegnata la priorità all'allocazione delle connessioni al database inattive esistenti anziché alla creazione di una nuova connessione. Al termine dell'utilizzo della connessione al database da parte del programma, la connessione viene ripristinata in preparazione per un ulteriore utilizzo, anziché semplicemente essere chiusa.

Per una migliore illustrazione, questo articolo fornisce [un codice di esempio](./sample-scripts-java-connection-pooling.md) che usa Java come esempio. Per altre informazioni, vedere [Apache Common DBCP](http://commons.apache.org/proper/commons-dbcp/).

> [!NOTE]
> Il server configura un meccanismo di timeout per chiudere una connessione che si trova in uno stato di inattività per un certo periodo di tempo per liberare risorse. Assicurarsi di configurare il sistema di verifica per garantire l'efficacia delle connessioni permanenti quando vengono usate. Per altre informazioni, vedere [configurare i sistemi di verifica sul lato client per garantire l'efficacia delle connessioni permanenti](concepts-connectivity.md#configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections).

## <a name="access-databases-by-using-persistent-connections-recommended"></a>Accedere ai database usando connessioni permanenti (scelta consigliata)

Il concetto di connessioni permanenti è simile a quello del pool di connessioni. La sostituzione di connessioni brevi con connessioni permanenti richiede solo modifiche minime al codice, ma ha un effetto significativo in termini di miglioramento delle prestazioni in molti scenari di applicazione tipici.

## <a name="access-databases-by-using-wait-and-retry-mechanism-with-short-connections"></a>Accedere ai database usando il meccanismo wait and retry con connessioni brevi

In caso di limitazioni delle risorse, è consigliabile utilizzare il pool di database o le connessioni permanenti per accedere ai database. Se l'applicazione usa connessioni brevi e si verificano errori di connessione quando si affronta il limite superiore per il numero di connessioni simultanee, è possibile provare il meccanismo di attesa e di ripetizione dei tentativi. È possibile impostare un tempo di attesa appropriato, con un tempo di attesa più breve dopo il primo tentativo. Successivamente, è possibile provare ad attendere più volte gli eventi.

## <a name="configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections"></a>Configurare i meccanismi di verifica nei client per verificare l'efficacia delle connessioni permanenti

Il server configura un meccanismo di timeout per chiudere una connessione che si trova in uno stato di inattività per un certo periodo di tempo per liberare risorse. Quando il client accede nuovamente al database, equivale a creare una nuova richiesta di connessione tra il client e il server. Per garantire l'efficacia delle connessioni durante il processo di utilizzo, configurare un meccanismo di verifica nel client. Come illustrato nell'esempio seguente, è possibile usare il pool di connessioni JDBC per Tomcat per configurare questo meccanismo di verifica.

Impostando il parametro TestOnBorrow, quando è presente una nuova richiesta, il pool di connessioni verifica automaticamente l'efficacia di tutte le connessioni inattive disponibili. Se tale connessione è efficace, il pool di connessioni restituito direttamente viene ritirato dalla connessione. Il pool di connessioni crea quindi una nuova connessione valida e la restituisce. Questo processo assicura che l'accesso al database avvenga in modo efficiente. 

Per informazioni sulle impostazioni specifiche, vedere il [documento di introduzione ufficiale del pool di connessioni JDBC](https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html#Common_Attributes). È necessario principalmente impostare i tre parametri seguenti: TestOnBorrow (impostato su true), ValidationQuery (impostato su SELECT 1) e ValidationQueryTimeout (impostato su 1). Il codice di esempio specifico è illustrato di seguito:

```java
public class SimpleTestOnBorrowExample {
      public static void main(String[] args) throws Exception {
          PoolProperties p = new PoolProperties();
          p.setUrl("jdbc:mysql://localhost:3306/mysql");
          p.setDriverClassName("com.mysql.jdbc.Driver");
          p.setUsername("root");
          p.setPassword("password");
            // The indication of whether objects will be validated by the idle object evictor (if any). 
            // If an object fails to validate, it will be dropped from the pool. 
            // NOTE - for a true value to have any effect, the validationQuery or validatorClassName parameter must be set to a non-null string. 
          p.setTestOnBorrow(true); 

            // The SQL query that will be used to validate connections from this pool before returning them to the caller.
            // If specified, this query does not have to return any data, it just can't throw a SQLException.
          p.setValidationQuery("SELECT 1");

            // The timeout in seconds before a connection validation queries fail. 
            // This works by calling java.sql.Statement.setQueryTimeout(seconds) on the statement that executes the validationQuery. 
            // The pool itself doesn't timeout the query, it is still up to the JDBC driver to enforce query timeouts. 
            // A value less than or equal to zero will disable this feature.
          p.setValidationQueryTimeout(1);
            // set other usefull pool properties.
          DataSource datasource = new DataSource();
          datasource.setPoolProperties(p);

          Connection con = null;
          try {
            con = datasource.getConnection();
            // execute your query here
          } finally {
            if (con!=null) try {con.close();}catch (Exception ignore) {}
          }
      }
  }
```

## <a name="next-steps"></a>Passaggi successivi

* [Troubleshoot connection issues to Azure Database for MySQL](howto-troubleshoot-common-connection-issues.md) (Risolvere i problemi di connessione a database di Azure per MySQL)
