---
title: Errori di connettività temporanei - Database di Azure per MySQLTransient connectivity errors - Azure Database for MySQL
description: Informazioni su come gestire gli errori di connettività temporanei e connettersi in modo efficiente al database di Azure per MySQL.Learn how to handle transient connectivity errors and connect efficiently to Azure Database for MySQL.
keywords: connessione mysql,stringa di connessione,problemi di connettività,errore temporaneo,errore di connessione,connessione in modo efficiente
author: jan-eng
ms.author: janeng
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 79c5c7e485cc9cb03757b8a981cef92d79b81c3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537177"
---
# <a name="handle-transient-errors-and-connect-efficiently-to-azure-database-for-mysql"></a>Gestire gli errori temporanei e connettersi in modo efficiente al database di Azure per MySQL

Questo articolo descrive come gestire gli errori temporanei e connettersi in modo efficiente al database di Azure per MySQL.This article describes how to handle transient errors and connect efficiently to Azure Database for MySQL.

## <a name="transient-errors"></a>Errori temporanei

Un errore temporaneo è un errore che si risolve in modo autonomo. Questi errori generalmente si manifestano come una connessione al server di database che viene interrotta. Inoltre non è possibile aprire nuove connessioni a un server. Possono verificarsi errori temporanei, ad esempio nel caso di un errore hardware o di rete. Un altro motivo potrebbe essere una nuova versione di un servizio PaaS che è in fase di implementazione. La maggior parte di questi eventi vengono attenuati automaticamente dal sistema in meno di 60 secondi. Una procedura consigliata per la progettazione e lo sviluppo di applicazioni nel cloud è la previsione di errori temporanei, presupponendo che possano accadere in qualsiasi componente e in qualunque momento e che si disponga della logica appropriata per affrontare tali situazioni.

## <a name="handling-transient-errors"></a>Gestione degli errori temporanei

Gli errori temporanei devono essere gestiti usando la logica di ripetizione dei tentativi. Situazioni da considerare:

* Si verifica un errore quando si tenta di aprire una connessione
* Una connessione inattiva viene eliminata sul lato server. Se si prova a eseguire un comando non si riesce a eseguirlo
* Una connessione attiva che sta attualmente eseguendo un comando viene eliminata.

Il primo e il secondo caso sono abbastanza semplici da gestire. Provare ad aprire di nuovo la connessione. Quando l'operazione riesce, l'errore temporaneo è stato attenuato dal sistema. ed è possibile usare nuovamente il Database di Azure per MySQL. È consigliabile attendere prima di riprovare a effettuare la connessione. Interrompere temporaneamente l'operazione se i tentativi iniziali hanno esito negativo. In questo modo il sistema può usare tutte le risorse disponibili per superare la situazione di errore. È consigliabile seguire questa procedura:

* Attendere 5 secondi prima di riprovare.
* Per ogni tentativo successivo, aumentare l'attesa in modo esponenziale, fino a 60 secondi.
* Impostare un numero massimo di tentativi nel punto in cui l'applicazione considera l'operazione non riuscita.

Quando una connessione con una transazione attiva ha esito negativo, la gestione corretta del ripristino è più difficile. Possono presentarsi due casi. Se la transazione era di sola lettura, è opportuno riaprire la connessione e riprovare la transazione. Se tuttavia la transazione scriveva anche nel database, è necessario determinare se è stato eseguito il rollback della transazione o se ha avuto esito positivo prima che si verificasse l'errore temporaneo. In tal caso, è possibile che non sia stato ricevuto il riconoscimento del commit dal server di database.

Un modo per superare il problema consiste nel generare un ID univoco sul client usato per tutti i tentativi. Passare questo ID univoco come parte della transazione al server e archiviarlo in una colonna con un vincolo univoco. In questo modo è possibile riprovare a eseguire la transazione in tutta sicurezza. Avrà esito positivo se è stato eseguito il rollback della transazione precedente e l'ID univoco generato dal client non esiste ancora nel sistema. Il tentativo ha esito negativo quando indica una violazione della chiave duplicata se l'ID univoco è stato già archiviato, perché la transazione precedente è stata completata correttamente.

Se il programma comunica con il Database di Azure per MySQL tramite middleware di terze parti, chiedere al fornitore se il middleware include la logica di ripetizione dei tentativi per errori temporanei.

Verificare di testare la logica di ripetizione dei tentativi. Ad esempio, provare a eseguire il codice durante la scalabilità verticale o verso il basso delle risorse di calcolo del database di Azure per il server MySQL.For example, try to execute your code while scaling up or down the compute resources of your Azure Database for MySQL server. L'applicazione deve gestire senza problemi il breve tempo di inattività che si verifica durante questa operazione.

## <a name="connect-efficiently-to-azure-database-for-mysql"></a>Connettiti in modo efficiente al database di Azure per MySQL

Le connessioni al database sono una risorsa limitata, pertanto l'uso efficace del pool di connessioni per accedere al database di Azure per MySQL consente di ottimizzare le prestazioni. Nella sezione seguente viene illustrato come usare il pool di connessioni o le connessioni permanenti per accedere in modo più efficace al database di Azure per MySQL.The below section explains how to use connection pooling or persistent connections to more effectively access Azure Database for MySQL.

## <a name="access-databases-by-using-connection-pooling-recommended"></a>Accedere ai database tramite il pool di connessioni (scelta consigliata)

La gestione delle connessioni al database può avere un impatto significativo sulle prestazioni dell'applicazione nel suo complesso. Per ottimizzare le prestazioni dell'applicazione, l'obiettivo deve essere quello di ridurre il numero di volte in cui vengono stabilite le connessioni e il tempo necessario per stabilire connessioni nei percorsi di codice chiave. È consigliabile usare il pool di connessioni al database o connessioni permanenti per connettersi al database di Azure per MySQL.We strongly recommend using database connection pooling or persistent connections to connect to Azure Database for MySQL. Il pool di connessioni al database gestisce la creazione, la gestione e l'allocazione delle connessioni al database. Quando un programma richiede una connessione al database, assegna la priorità all'allocazione delle connessioni di database inattive esistenti, anziché alla creazione di una nuova connessione. Dopo che il programma ha terminato di utilizzare la connessione al database, la connessione viene ripristinata in preparazione per un ulteriore utilizzo, anziché semplicemente essere chiusa.

Per una migliore illustrazione, questo articolo fornisce [una parte di codice di esempio](./sample-scripts-java-connection-pooling.md) che usa JAVA come esempio. Per ulteriori informazioni, vedere [Apache common DBCP](https://commons.apache.org/proper/commons-dbcp/).

> [!NOTE]
> Il server configura un meccanismo di timeout per chiudere una connessione che si è instato di inattività per qualche tempo per liberare risorse. Assicurarsi di impostare il sistema di verifica per garantire l'efficacia delle connessioni permanenti quando vengono in uso. Per ulteriori informazioni, vedere Configurare i sistemi di [verifica sul lato client per garantire l'efficacia delle connessioni permanenti.](concepts-connectivity.md#configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections)

## <a name="access-databases-by-using-persistent-connections-recommended"></a>Accedere ai database utilizzando connessioni permanenti (scelta consigliata)

Il concetto di connessioni permanenti è simile a quello del pool di connessioni. La sostituzione di connessioni brevi con connessioni permanenti richiede solo piccole modifiche al codice, ma ha un effetto importante in termini di miglioramento delle prestazioni in molti scenari di applicazione tipici.

## <a name="access-databases-by-using-wait-and-retry-mechanism-with-short-connections"></a>Accedere ai database utilizzando il meccanismo di attesa e ripetizione dei tentativi con connessioni brevi

In caso di limitazioni delle risorse, è consigliabile utilizzare il pool ingoidaggio dei database o connessioni permanenti per accedere ai database. Se l'applicazione utilizza connessioni brevi e si verificano errori di connessione quando si avvicina il limite superiore per il numero di connessioni simultanee, è possibile provare a attendere e ripetere il meccanismo. È possibile impostare un tempo di attesa appropriato, con un tempo di attesa più breve dopo il primo tentativo. Successivamente, è possibile provare ad attendere gli eventi più volte.

## <a name="configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections"></a>Configurare i meccanismi di verifica nei client per verificare l'efficacia delle connessioni permanenti

Il server configura un meccanismo di timeout per chiudere una connessione che è stata in uno stato di inattività per qualche tempo per liberare risorse. Quando il client accede nuovamente al database, equivale a creare una nuova richiesta di connessione tra il client e il server. Per garantire l'efficacia delle connessioni durante il processo di utilizzo, configurare un meccanismo di verifica sul client. Come illustrato nell'esempio seguente, è possibile utilizzare il pool di connessioni Tomcat JDBC per configurare questo meccanismo di verifica.

Impostando il parametro TestOnBorrow, quando è presente una nuova richiesta, il pool di connessioni verifica automaticamente l'efficacia di tutte le connessioni inattive disponibili. Se tale connessione è effettiva, viene restituito direttamente il pool di connessioni ritira la connessione. Il pool di connessioni crea quindi una nuova connessione efficace e la restituisce. Questo processo garantisce l'accesso efficiente all'accesso al database. 

Per informazioni sulle impostazioni specifiche, vedere il documento introduttivo ufficiale del pool di [connessioni JDBC](https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html#Common_Attributes). È principalmente necessario impostare i tre parametri seguenti: TestOnBorrow (impostato su true), ValidationQuery (impostato su SELECT 1) e ValidationQueryTimeout (impostato su 1). Il codice di esempio specifico è illustrato di seguito:The specific sample code is shown below:

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
            // set other useful pool properties.
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

* [Risolvere i problemi di connessione a Database di Azure per MySQL](howto-troubleshoot-common-connection-issues.md)
