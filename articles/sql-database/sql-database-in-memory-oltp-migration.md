<properties
	pageTitle="OLTP in memoria migliora le prestazioni delle transazioni SQL | Microsoft Azure"
	description="Adottare OLTP in memoria per migliorare le prestazioni transazionali in un database SQL esistente."
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jeffreyg"
	editor="MightyPen"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="11/10/2015"
	ms.author="jodebrui"/>


# Uso della tecnologia in memoria (anteprima) per migliorare le prestazioni delle applicazioni nel database SQL

Seguire questa procedura per ottimizzare le prestazioni transazionali del database SQL [ Premium[ di Azure esistente usando la funzionalità ](sql-database-service-tiers.md)In memoria](sql-database-in-memory.md).


## Passaggio 1: Verificare che il database Premium supporti la funzionalità in memoria

I database Premium creati nel novembre 2015 o versioni successive supportano la funzionalità in memoria. È possibile verificare se il database Premium supporta la funzionalità in memoria eseguendo l'istruzione Transact-SQL seguente. La funzionalità in memoria è supportata se il risultato restituito è 1 (non 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* è l'acronimo di *Extreme Transaction Processing*

Se è necessario trasferire il database esistente in un nuovo database V12 Premium, è possibile usare le tecniche seguenti per esportare e quindi importare i dati.

#### Procedura di esportazione

Esportare il database di produzione in un file bacpac usando:

- La funzionalità [Esporta](sql-database-export.md) nel [portale](https://portal.azure.com/).

- La funzionalità **Esporta l'applicazione livello dati** in [up-to-date SSMS.exe](http://msdn.microsoft.com/library/mt238290.aspx) (SQL Server Management Studio).
 1. In **Esplora oggetti ** espandere il nodo **Database**.
 2. Fare clic con il pulsante destro del mouse sul nodo del database.
 3. Fare clic su **Attività** > **Esporta l'applicazione livello dati**.
 4. Usare la finestra della procedura guidata visualizzata.


#### Procedura di importazione

Importare il file bacpac in un nuovo database Premium.

1. Nel [portale](http://portal.azure.com/) di Azure:
 - Passare al server.
 - Selezionare l'opzione [Importa database](sql-database-import.md).
 - Selezionare un piano tariffario Premium.

2. Usare SSMS per importare il file bacpac:
 - In **Esplora oggetti ** fare clic con il pulsante destro del mouse sul nodo **Database**.
 - Fare clic su **Importa applicazione livello dati**.
 - Usare la finestra della procedura guidata visualizzata.


## Passaggio 2: Identificare gli oggetti per eseguire la migrazione a OLTP In memoria

SSMS include un report **Panoramica analisi delle prestazioni per le transazioni** che è possibile eseguire su un database con un carico di lavoro attivo. Il report identifica le tabelle e le stored procedure candidate per la migrazione a OLTP in memoria.

In SSMS per generare il report: in **Esplora oggetti** fare clic con il pulsante destro del mouse sul nodo del database. Fare clic su **Report** > **Report standard** > **Panoramica analisi delle prestazioni per le transazioni**.

Per altre informazioni, vedere [Determinare se una tabella o una stored procedure deve essere trasferita a OLTP in memoria](http://msdn.microsoft.com/library/dn205133.aspx).


## Passaggio 3: Creare un database di prova confrontabile

Si supponga che il report indichi che il database include una tabella che può trarre vantaggio dalla conversione in una tabella con ottimizzazione per la memoria. È consigliabile verificare prima di tutto l'indicazione eseguendo il test.

È necessaria una copia di test del database di produzione. Il database di test deve avere lo stesso livello di servizio del database di produzione.

Per semplificare il test, perfezionare il database di test come segue:

1. Connettersi al database di test tramite SSMS.

2. Per evitare di dover usare l'opzione WITH (SNAPSHOT) nelle query, impostare l'opzione di database come illustrato nell'istruzione T-SQL seguente: ```
ALTER DATABASE CURRENT
	SET
		MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
```


## Passaggio 4: Eseguire la migrazione delle tabelle

È necessario creare e popolare una copia con ottimizzazione per la memoria della tabella che si vuole testare. È possibile crearla usando:

- La pratica Ottimizzazione guidata per la memoria di SSMS.
- T-SQL manuale.


#### Ottimizzazione guidata per la memoria di SSMS

Per usare questa opzione di migrazione:

1. Connettersi al database di test tramite SSMS.

2. In **Esplora oggetti** fare clic con il pulsante destro del mouse sulla tabella e quindi fare clic su **Ottimizzazione guidata per la memoria**.
 - Verrà visualizzata l'**Ottimizzazione guidata per la memoria della tabella**.

3. Nella procedura guidata fare clic su **Convalida della migrazione** o sul pulsante **Avanti** per verificare se la tabella include eventuali funzionalità non supportate nelle tabelle con ottimizzazione per la memoria. Per altre informazioni, vedere:
 - *Elenco di controllo relativo all'ottimizzazione per la memoria* in [Ottimizzazione guidata per la memoria](http://msdn.microsoft.com/library/dn284308.aspx).
 - [Costrutti transact-SQL non supportati da OLTP in memoria](http://msdn.microsoft.com/library/dn246937.aspx).
 - [Migrazione a OLTP in memoria](http://msdn.microsoft.com/library/dn247639.aspx).

4. Se la tabella non include funzionalità non supportate, la procedura guidata può eseguire automaticamente la migrazione effettiva dello schema e dei dati.


#### T-SQL manuale

Per usare questa opzione di migrazione:

1. Connettersi al database di test tramite SSMS o un'utilità analoga.

2. Ottenere lo script T-SQL completo per la tabella e i relativi indici.
 - In SSMS fare clic con il pulsante destro del mouse sul nodo della tabella.
 - Fare clic su **Crea script per tabella** > **CREATE in** > **Nuova finestra Query**.

3. Nella finestra dello script aggiungere WITH (MEMORY\_OPTIMIZED = ON) all'istruzione CREATE TABLE.

4. Se esiste un indice CLUSTERED, modificarlo in NONCLUSTERED.

5. Rinominare la tabella esistente in SP\_RENAME.

6. Creare la nuova copia della tabella con ottimizzazione per la memoria eseguendo lo script modificato CREATE TABLE.

7. Copiare i dati nella tabella con ottimizzazione per la memoria usando l'istruzione INSERT...SELECT * INTO:
	
```
INSERT INTO <new_memory_optimized_table>
		SELECT * FROM <old_disk_based_table>;
```


## Passaggio 5 (facoltativo): Eseguire la migrazione di stored procedure

La funzionalità in memoria può anche modificare una stored procedure per migliorare le prestazioni.


### Considerazioni sulle stored procedure compilate in modo nativo

Una stored procedure compilata in modo nativo deve avere le opzioni seguenti nella relativa clausola WITH T-SQL:

- NATIVE\_COMPILATION

- SCHEMABINDING: indica le tabelle in cui la stored procedure non può modificare le definizioni di colonna in alcun modo che abbia effetto sulla stored procedure, a meno che non si rimuova la stored procedure.


Un modulo nativo deve usare [blocchi ATOMICI](http://msdn.microsoft.com/library/dn452281.aspx) di grandi dimensioni per la gestione delle transazioni. Non esiste un ruolo per un'istruzione BEGIN TRANSACTION esplicita.


### CREATE PROCEDURE tipica per le stored procedure compilate in modo nativo

In genere l'istruzione T-SQL per creare una stored procedure compilata in modo nativo è simile al modello seguente:

```
CREATE PROCEDURE schemaname.procedurename
	@param1 type1, …
	WITH NATIVE_COMPILATION, SCHEMABINDING
	AS
		BEGIN ATOMIC WITH
			(TRANSACTION ISOLATION LEVEL = SNAPSHOT,
			LANGUAGE = N'your_language__see_sys.languages'
			)
		…
		END;
```

- Per TRANSACTION\_ISOLATION\_LEVEL, SNAPSHOT è il valore più comune per la stored procedure compilata in modo nativo. Tuttavia, è supportato anche un subset degli altri valori:
 - REPEATABLE READ
 - SERIALIZABLE


- Il valore LANGUAGE deve essere presente nella vista sys.languages.


### Come eseguire la migrazione di una stored procedure

Ecco i passaggi della migrazione:


1. Ottenere lo script CREATE PROCEDURE per la stored procedure interpretata regolare.

2. Riscrivere l'intestazione in modo che corrisponda al modello precedente.

3. Verificare se il codice T-SQL della stored procedure usa funzionalità non supportate per le stored procedure compilate in modo nativo. Se necessario, implementare le soluzioni alternative.
 - Per altre informazioni vedere [Problemi di migrazione relativi alle stored procedure compilate in modo nativo](http://msdn.microsoft.com/library/dn296678.aspx).

4. Rinominare la stored procedure precedente tramite SP\_RENAME. In alternativa usare semplicemente DROP.

5. Eseguire lo script CREATE PROCEDURE T-SQL modificato.


## Passaggio 6: Eseguire il carico di lavoro nel test

Eseguire un carico di lavoro nel database di test simile al carico di lavoro eseguito nel database di produzione. Dovrebbe indicare il miglioramento delle prestazioni ottenuto con l'uso della funzionalità in memoria per tabelle e stored procedure.

Gli attributi principali del carico di lavoro sono i seguenti:

- Numero di connessioni simultanee.

- Rapporto di lettura/scrittura.


Per personalizzare ed eseguire il carico di lavoro di test, si consiglia di usare il pratico strumento ostress.exe, illustrato [qui](sql-database-in-memory.md).


Per ridurre al minimo la latenza di rete, eseguire il test nella stessa area geografica di Azure in cui è disponibile il database.


## Passaggio 7: Monitoraggio post-implementazione

Tenere sotto controllo gli effetti sulle prestazioni delle implementazioni in memoria nell'ambiente di produzione:

- [Monitorare l'archiviazione in memoria](https://azure.microsoft.com/documentation/articles/sql-database-in-memory-oltp-monitoring/).

- [Monitoraggio del database SQL di Azure tramite le visualizzazioni di gestione dinamica](sql-database-monitoring-with-dmvs.md)


## Collegamenti correlati

- [OLTP in memoria (ottimizzazione in memoria)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Stored procedure compilate in modo nativo](http://msdn.microsoft.com/library/dn133184.aspx)

- [Ottimizzazione guidata per la memoria](http://msdn.microsoft.com/library/dn284308.aspx)

<!---HONumber=Nov15_HO3-->