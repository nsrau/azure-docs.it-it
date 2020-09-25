---
title: Funzioni SQL-iperscala (CITUS)-database di Azure per PostgreSQL
description: Funzioni nell'API SQL con iperscalabilità (CITUS)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 16c3a45e0d88a0546772b3fdc855c90f2e450d14
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91250332"
---
# <a name="functions-in-the-hyperscale-citus-sql-api"></a>Funzioni nell'API SQL con iperscalabilità (CITUS)

Questa sezione contiene informazioni di riferimento per le funzioni definite dall'utente fornite da iperscale (CITUS). Queste funzioni consentono di fornire funzionalità distribuite aggiuntive a iperscalabilità (CITUS) diverse dai comandi SQL standard.

> [!NOTE]
>
> I gruppi di server iperscale (CITUS) che eseguono versioni precedenti del motore CITUS potrebbero non offrire tutte le funzioni elencate di seguito.

## <a name="table-and-shard-ddl"></a>DDL di tabella e partizione

### <a name="create_distributed_table"></a>Crea \_ tabella distribuita \_

La \_ funzione create Distributed \_ Table () viene usata per definire una tabella distribuita e creare le relative partizioni se si tratta di una tabella con distribuzione hash. Questa funzione accetta un nome di tabella, la colonna di distribuzione e un metodo di distribuzione facoltativo e inserisce i metadati appropriati per contrassegnare la tabella come distribuita. Se non viene specificato alcun metodo di distribuzione, per impostazione predefinita la funzione viene distribuita come distribuzione "hash". Se la tabella è con distribuzione hash, la funzione crea anche partizioni di lavoro in base ai valori di configurazione del numero di partizioni e del fattore di replica della partizione. Se la tabella contiene righe, queste vengono distribuite automaticamente ai nodi di lavoro.

Questa funzione sostituisce l'utilizzo della \_ tabella master create \_ Distributed \_ Table () seguita dalle partizioni master \_ Create \_ Worker \_ ().

#### <a name="arguments"></a>Argomenti

** \_ nome tabella:** nome della tabella che deve essere distribuita.

**colonna di distribuzione \_ :** la colonna in cui deve essere distribuita la tabella.

**tipo di distribuzione \_ :** (facoltativo) metodo in base al quale deve essere distribuita la tabella. I valori consentiti sono Append o hash e il valore predefinito è "hash".

**colocate \_ with:** (facoltativo) includere la tabella corrente nel gruppo di condivisione di un'altra tabella. Per impostazione predefinita, le tabelle sono condivise quando vengono distribuite da colonne dello stesso tipo, hanno lo stesso numero di partizioni e hanno lo stesso fattore di replica. I valori possibili per `colocate_with` sono `default` , `none` per avviare un nuovo gruppo di condivisione percorso o il nome di un'altra tabella da assegnare a tale tabella.  Vedere la condivisione [percorso tabella](concepts-hyperscale-colocation.md).

Tenere presente che il valore predefinito di è la condivisione `colocate_with` percorso implicita. La condivisione [percorso](concepts-hyperscale-colocation.md) può essere un'ottima cosa quando le tabelle sono correlate o verranno unite in join.  Tuttavia, quando due tabelle non sono correlate ma si usa lo stesso tipo di dati per le relative colonne di distribuzione, la condivisione accidentale può ridurre le prestazioni durante il [ribilanciamento](howto-hyperscale-scaling.md#rebalance-shards)delle partizioni.  Le partizioni della tabella verranno spostate insieme inutilmente in una \" catena.\"

Se una nuova tabella distribuita non è correlata ad altre tabelle, è preferibile specificare `colocate_with => 'none'` .

#### <a name="return-value"></a>Valore restituito

N/D

#### <a name="example"></a>Esempio

Questo esempio informa il database che la \_ tabella degli eventi GitHub deve essere distribuita tramite hash nella \_ colonna ID repository.

```postgresql
SELECT create_distributed_table('github_events', 'repo_id');

-- alternatively, to be more explicit:
SELECT create_distributed_table('github_events', 'repo_id',
                                colocate_with => 'github_repo');
```

### <a name="create_reference_table"></a>Crea \_ tabella di riferimento \_

La \_ funzione Create Reference \_ Table () viene utilizzata per definire un piccolo riferimento o una tabella delle dimensioni. Questa funzione accetta un nome di tabella e crea una tabella distribuita con una sola partizione, replicata in ogni nodo di lavoro.

#### <a name="arguments"></a>Argomenti

** \_ nome tabella:** nome della dimensione piccola o della tabella di riferimento che deve essere distribuita.

#### <a name="return-value"></a>Valore restituito

N/D

#### <a name="example"></a>Esempio

Questo esempio informa il database che la tabella Nation deve essere definita come tabella di riferimento

```postgresql
SELECT create_reference_table('nation');
```

### <a name="upgrade_to_reference_table"></a>eseguire l'aggiornamento \_ alla \_ tabella di riferimento \_

La \_ funzione di aggiornamento alla \_ tabella di riferimento \_ () accetta una tabella distribuita esistente con un numero di partizioni pari a uno e la aggiorna in modo che sia una tabella di riferimento riconosciuta. Dopo la chiamata a questa funzione, la tabella sarà come se fosse stata creata con [create_reference_table](#create_reference_table).

#### <a name="arguments"></a>Argomenti

** \_ nome tabella:** nome della tabella distribuita (con numero di partizioni = 1) che verrà distribuito come tabella di riferimento.

#### <a name="return-value"></a>Valore restituito

N/D

#### <a name="example"></a>Esempio

Questo esempio informa il database che la tabella Nation deve essere definita come tabella di riferimento

```postgresql
SELECT upgrade_to_reference_table('nation');
```

### <a name="mark_tables_colocated"></a>contrassegnare le tabelle con percorso condiviso \_ \_

La \_ funzione Mark Tables \_ () accetta una tabella distribuita (l'origine) e un elenco di altre (destinazioni) e inserisce le destinazioni nello stesso gruppo di condivisione percorso dell'origine. Se l'origine non è ancora in un gruppo, questa funzione ne crea una e assegna l'origine e le destinazioni.

Le tabelle di condivisione percorso devono essere eseguite in fase di distribuzione della tabella tramite il `colocate_with` parametro di [create_distributed_table](#create_distributed_table), ma possono essere gestite in un secondo momento, `mark_tables_colocated` se necessario.

#### <a name="arguments"></a>Argomenti

** \_ \_ nome tabella di origine:** nome della tabella distribuita di cui verranno assegnate le destinazioni a cui viene assegnato il gruppo di condivisione percorso.

**nomi di tabella di destinazione \_ \_ :** la matrice dei nomi delle tabelle di destinazione distribuite deve essere non vuota. Queste tabelle distribuite devono corrispondere alla tabella di origine in:

> -   distribution (metodo)
> -   tipo di colonna di distribuzione
> -   tipo di replica
> -   numero di partizioni

Se non viene applicato nessuno dei suddetti, iperscale (CITUS) genererà un errore. Ad esempio, il tentativo di trovare una colocazione di tabelle `apples` e `oranges` i cui tipi di colonne di distribuzione differiscono in:

```
ERROR:  XX000: cannot colocate tables apples and oranges
DETAIL:  Distribution column types don't match for apples and oranges.
```

#### <a name="return-value"></a>Valore restituito

N/D

#### <a name="example"></a>Esempio

Questo esempio inserisce `products` e `line_items` nello stesso gruppo di condivisione percorso di `stores` . Nell'esempio si presuppone che queste tabelle siano tutte distribuite in una colonna con tipo corrispondente, probabilmente un \" ID di archivio.\"

```postgresql
SELECT mark_tables_colocated('stores', ARRAY['products', 'line_items']);
```

### <a name="create_distributed_function"></a>Crea \_ funzione distribuita \_

Propaga una funzione dal nodo coordinatore ai ruoli di lavoro e la contrassegna per l'esecuzione distribuita. Quando una funzione distribuita viene chiamata sul coordinatore, iperscale (CITUS) usa il valore dell' \" argomento Distribution \" per selezionare un nodo di lavoro per eseguire la funzione. L'esecuzione della funzione sui ruoli di lavoro aumenta il parallelismo e può avvicinare il codice ai dati nelle partizioni per una latenza più bassa.

Il percorso di ricerca Postgres non viene propagato dal coordinatore ai ruoli di lavoro durante l'esecuzione della funzione distribuita, quindi il codice della funzione distribuita deve specificare in modo completo i nomi degli oggetti di database. Inoltre, le notifiche emesse dalle funzioni non verranno visualizzate all'utente.

#### <a name="arguments"></a>Argomenti

** \_ nome funzione:** nome della funzione da distribuire. Il nome deve includere i tipi di parametro della funzione tra parentesi, perché più funzioni possono avere lo stesso nome in PostgreSQL. Ad esempio, `'foo(int)'` è diverso da `'foo(int, text)'` .

** \_ \_ nome arg di distribuzione:** (facoltativo) nome dell'argomento da distribuire. Per praticità (o se gli argomenti della funzione non hanno nomi), è consentito un segnaposto posizionale, ad esempio `'$1'` . Se questo parametro non viene specificato, la funzione denominata da `function_name` viene semplicemente creata nei ruoli di lavoro. Se i nodi di lavoro vengono aggiunti in futuro, la funzione verrà creata automaticamente.

**colocate \_ with:** (facoltativo) quando la funzione distribuita legge o scrive in una tabella distribuita (o, più in generale, gruppo di condivisione percorso), assicurarsi di assegnare un nome alla tabella usando il `colocate_with` parametro. Ogni chiamata della funzione verrà quindi eseguita nel nodo del ruolo di lavoro contenente le partizioni pertinenti.

#### <a name="return-value"></a>Valore restituito

N/D

#### <a name="example"></a>Esempio

```postgresql
-- an example function which updates a hypothetical
-- event_responses table which itself is distributed by event_id
CREATE OR REPLACE FUNCTION
  register_for_event(p_event_id int, p_user_id int)
RETURNS void LANGUAGE plpgsql AS $fn$
BEGIN
  INSERT INTO event_responses VALUES ($1, $2, 'yes')
  ON CONFLICT (event_id, user_id)
  DO UPDATE SET response = EXCLUDED.response;
END;
$fn$;

-- distribute the function to workers, using the p_event_id argument
-- to determine which shard each invocation affects, and explicitly
-- colocating with event_responses which the function updates
SELECT create_distributed_function(
  'register_for_event(int, int)', 'p_event_id',
  colocate_with := 'event_responses'
);
```

## <a name="metadata--configuration-information"></a>Metadati/informazioni di configurazione

### <a name="master_get_table_metadata"></a>\_metadati della \_ tabella master Get \_

La \_ funzione Master Get \_ Table \_ Metadata () può essere utilizzata per restituire i metadati relativi alla distribuzione per una tabella distribuita. Questi metadati includono l'ID relazione, il tipo di archiviazione, il metodo di distribuzione, la colonna di distribuzione, il numero di repliche, la dimensione massima della partizione e i criteri di posizionamento delle partizioni per la tabella. Dietro le quinte, questa funzione esegue una query sulle tabelle di metadati iperscale (CITUS) per ottenere le informazioni necessarie e la concatena in una tupla prima di restituirla all'utente.

#### <a name="arguments"></a>Argomenti

** \_ nome tabella:** nome della tabella distribuita per cui si desidera recuperare i metadati.

#### <a name="return-value"></a>Valore restituito

Tupla contenente le informazioni seguenti:

** \_ relid logico:** OID della tabella distribuita. Fa riferimento alla colonna relfilenode nella tabella PG \_ Class System Catalog.

** \_ tipo di archiviazione parti \_ :** tipo di archiviazione usato per la tabella. Potrebbe essere ' T'(tabella standard),' f ' (tabella esterna) o ' c'(tabella a colonne).

** \_ Metodo Part:** metodo di distribuzione utilizzato per la tabella. Può essere ' a' (Append) o ' h ' (hash).

**chiave della parte \_ :** colonna di distribuzione per la tabella.

** \_ \_ numero di repliche della parte:** conteggio della replica della partizione corrente.

** \_ \_ dimensione massima della parte:** dimensioni massime correnti della partizione in byte.

**criteri di posizionamento delle parti \_ \_ :** criteri di posizionamento delle partizioni usati per inserire le partizioni della tabella. Può essere 1 (locale-nodo-primo) o 2 (Round Robin).

#### <a name="example"></a>Esempio

Nell'esempio seguente vengono recuperati e visualizzati i metadati della tabella per la \_ tabella degli eventi di GitHub.

```postgresql
SELECT * from master_get_table_metadata('github_events');
 logical_relid | part_storage_type | part_method | part_key | part_replica_count | part_max_size | part_placement_policy 
---------------+-------------------+-------------+----------+--------------------+---------------+-----------------------
         24180 | t                 | h           | repo_id  |                  2 |    1073741824 |                     2
(1 row)
```

### <a name="get_shard_id_for_distribution_column"></a>ottenere \_ l' \_ ID \_ di partizione per la \_ colonna di distribuzione \_

Iperscale (CITUS) assegna ogni riga di una tabella distribuita a una partizione in base al valore della colonna di distribuzione della riga e al metodo di distribuzione della tabella. Nella maggior parte dei casi, il mapping preciso è un dettaglio di basso livello che l'amministratore del database può ignorare. Può tuttavia essere utile determinare la partizione di una riga, per le attività di manutenzione manuale del database o solo per soddisfare le curiosità. La `get_shard_id_for_distribution_column` funzione fornisce queste informazioni per le tabelle con distribuzione hash e con intervallo e per le tabelle di riferimento. Non funziona per la distribuzione di Accodamento.

#### <a name="arguments"></a>Argomenti

** \_ nome tabella:** la tabella distribuita.

**valore di distribuzione \_ :** valore della colonna di distribuzione.

#### <a name="return-value"></a>Valore restituito

L'ID di partizione (CITUS) viene associato al valore della colonna di distribuzione per la tabella specificata.

#### <a name="example"></a>Esempio

```postgresql
SELECT get_shard_id_for_distribution_column('my_table', 4);

 get_shard_id_for_distribution_column
--------------------------------------
                               540007
(1 row)
```

### <a name="column_to_column_name"></a>\_nome colonna in \_ colonna \_

Converte la `partkey` colonna di `pg_dist_partition` in un nome di colonna testuale. La traduzione è utile per determinare la colonna di distribuzione di una tabella distribuita.

Per una descrizione più dettagliata, vedere [scelta di una colonna di distribuzione](concepts-hyperscale-choose-distribution-column.md).

#### <a name="arguments"></a>Argomenti

** \_ nome tabella:** la tabella distribuita.

**testo della colonna \_ var \_ :** valore di `partkey` nella `pg_dist_partition` tabella.

#### <a name="return-value"></a>Valore restituito

Nome della `table_name` colonna di distribuzione di.

#### <a name="example"></a>Esempio

```postgresql
-- get distribution column name for products table

SELECT column_to_column_name(logicalrelid, partkey) AS dist_col_name
  FROM pg_dist_partition
 WHERE logicalrelid='products'::regclass;
```

Output:

```
┌───────────────┐
│ dist_col_name │
├───────────────┤
│ company_id    │
└───────────────┘
```

### <a name="citus_relation_size"></a>\_dimensioni della relazione CITUS \_

Ottenere lo spazio su disco utilizzato da tutte le partizioni della tabella distribuita specificata.
Lo spazio su disco include le dimensioni del \" fork principale, \" ma esclude la mappa di visibilità e la mappa dello spazio disponibile per le partizioni.

#### <a name="arguments"></a>Argomenti

**logicalrelid:** nome di una tabella distribuita.

#### <a name="return-value"></a>Valore restituito

Dimensioni in byte come bigint.

#### <a name="example"></a>Esempio

```postgresql
SELECT pg_size_pretty(citus_relation_size('github_events'));
```

```
pg_size_pretty
--------------
23 MB
```

### <a name="citus_table_size"></a>\_Dimensioni tabella \_ CITUS

Ottenere lo spazio su disco utilizzato da tutte le partizioni della tabella distribuita specificata, esclusi gli indici, inclusi i popup, la mappa dello spazio libero e la mappa di visibilità.

#### <a name="arguments"></a>Argomenti

**logicalrelid:** nome di una tabella distribuita.

#### <a name="return-value"></a>Valore restituito

Dimensioni in byte come bigint.

#### <a name="example"></a>Esempio

```postgresql
SELECT pg_size_pretty(citus_table_size('github_events'));
```

```
pg_size_pretty
--------------
37 MB
```

### <a name="citus_total_relation_size"></a>\_dimensioni totali \_ relazione \_ CITUS

Ottenere lo spazio totale su disco utilizzato da tutte le partizioni della tabella distribuita specificata, inclusi tutti gli indici e i dati di tipo avviso popup.

#### <a name="arguments"></a>Argomenti

**logicalrelid:** nome di una tabella distribuita.

#### <a name="return-value"></a>Valore restituito

Dimensioni in byte come bigint.

#### <a name="example"></a>Esempio

```postgresql
SELECT pg_size_pretty(citus_total_relation_size('github_events'));
```

```
pg_size_pretty
--------------
73 MB
```

### <a name="citus_stat_statements_reset"></a>\_reimpostazione \_ istruzioni stat CITUS \_

Rimuove tutte le righe dal [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table).
Questa funzione funziona indipendentemente da `pg_stat_statements_reset()` . Per reimpostare tutte le statistiche, chiamare entrambe le funzioni.

#### <a name="arguments"></a>Argomenti

N/D

#### <a name="return-value"></a>Valore restituito

nessuno

## <a name="server-group-management-and-repair"></a>Gestione e ripristino dei gruppi di server

### <a name="master_copy_shard_placement"></a>\_ \_ \_ selezione host partizione principale

Se non è possibile aggiornare la posizione di una partizione durante un comando di modifica o un'operazione DDL, viene contrassegnata come inattiva. \_ \_ \_ È quindi possibile chiamare la funzione di posizionamento delle partizioni di copia master per ripristinare una posizione di partizionamento inattiva usando i dati di una posizione integra.

Per ripristinare una partizione, la funzione rilascia prima di tutto il posizionamento della partizione non integro e lo ricrea usando lo schema sul coordinatore. Una volta creato il posizionamento della partizione, la funzione copia i dati dal posizionamento integro e aggiorna i metadati per contrassegnare il nuovo posizionamento della partizione come integro. Questa funzione garantisce che la partizione venga protetta da eventuali modifiche simultanee durante il ripristino.

#### <a name="arguments"></a>Argomenti

** \_ ID partizione:** ID della partizione da ripristinare.

**nome del nodo di origine \_ \_ :** nome DNS del nodo in cui è presente la posizione di partizionamento integro ( \" nodo di origine \" ).

**porta del nodo di origine \_ \_ :** porta sul nodo del ruolo di lavoro di origine su cui è in ascolto il server di database.

**nome del nodo di destinazione \_ \_ :** nome DNS del nodo in cui è presente la posizione di partizionamento non valida ( \" nodo di destinazione \" ).

**porta del nodo di destinazione \_ \_ :** porta sul nodo del ruolo di lavoro di destinazione su cui è in ascolto il server di database.

#### <a name="return-value"></a>Valore restituito

N/D

#### <a name="example"></a>Esempio

Nell'esempio seguente viene ripristinato un posizionamento di partizionamento inattivo della partizione 12345, presente sul server di database in esecuzione su "host non valido \_ " sulla porta 5432. Per ripristinarlo, utilizzerà i dati di una posizione di partizionamento integra presente sul server in esecuzione su " \_ host valido" sulla porta
5432.

```postgresql
SELECT master_copy_shard_placement(12345, 'good_host', 5432, 'bad_host', 5432);
```

### <a name="master_move_shard_placement"></a>posizione principale di \_ spostamento della \_ partizione \_

Questa funzione sposta una determinata partizione (e le partizioni con percorso condiviso) da un nodo a un altro. Viene in genere usato indirettamente durante il ribilanciamento delle partizioni anziché essere chiamato direttamente da un amministratore di database.

Esistono due modi per spostare i dati: blocco o non blocco. L'approccio di blocco significa che durante lo spostamento tutte le modifiche apportate alla partizione vengono sospese.
Il secondo modo, che evita di bloccare le scritture di partizionamento, si basa sulla replica logica Postgres 10.

Dopo un'operazione di spostamento riuscita, le partizioni nel nodo di origine vengono eliminate. Se lo spostamento ha esito negativo in qualsiasi momento, questa funzione genera un errore e lascia invariati i nodi di origine e di destinazione.

#### <a name="arguments"></a>Argomenti

** \_ ID partizione:** ID della partizione da spostare.

**nome del nodo di origine \_ \_ :** nome DNS del nodo in cui è presente la posizione di partizionamento integro ( \" nodo di origine \" ).

**porta del nodo di origine \_ \_ :** porta sul nodo del ruolo di lavoro di origine su cui è in ascolto il server di database.

**nome del nodo di destinazione \_ \_ :** nome DNS del nodo in cui è presente la posizione di partizionamento non valida ( \" nodo di destinazione \" ).

**porta del nodo di destinazione \_ \_ :** porta sul nodo del ruolo di lavoro di destinazione su cui è in ascolto il server di database.

** \_ modalità di trasferimento partizioni \_ :** (facoltativo) specificare il metodo di replica, se usare la replica logica PostgreSQL o un comando di copia tra i thread di lavoro. I valori possibili sono:

> -   `auto`: Richiedere l'identità della replica se è possibile la replica logica. in caso contrario, usare il comportamento legacy, ad esempio per il ripristino della partizione, PostgreSQL 9,6. Si tratta del valore predefinito.
> -   `force_logical`: Usare la replica logica anche se la tabella non ha un'identità di replica. Eventuali istruzioni Update/Delete simultanee della tabella avranno esito negativo durante la replica.
> -   `block_writes`: Usare copia (scritture di blocco) per le tabelle prive di chiave primaria o identità di replica.

#### <a name="return-value"></a>Valore restituito

N/D

#### <a name="example"></a>Esempio

```postgresql
SELECT master_move_shard_placement(12345, 'from_host', 5432, 'to_host', 5432);
```

### <a name="rebalance_table_shards"></a>ribilanciare le partizioni della \_ tabella \_

La funzione ribilanciare le partizioni \_ \_ della tabella () sposta le partizioni della tabella specificata in modo da renderle distribuite uniformemente tra i ruoli di lavoro. La funzione calcola innanzitutto l'elenco di spostamenti che deve eseguire per garantire che il gruppo di server sia bilanciato entro la soglia specificata. Sposta quindi i posizionamenti della partizione uno alla volta dal nodo di origine al nodo di destinazione e aggiorna i metadati della partizione corrispondenti in modo da riflettere lo spostamento.

A ogni partizione viene assegnato un costo quando si determina se le partizioni sono \" distribuite in modo uniforme. \" Per impostazione predefinita, ogni partizione ha lo stesso costo (un valore pari a 1), quindi la distribuzione per l'equalizzazione del costo tra i thread di lavoro è uguale all'equalizzazione del numero di partizioni in ogni partizione. La strategia di costo costante viene chiamata \" dal numero di partizioni \_ \_ \" ed è la strategia di ribilanciamento predefinita.

La strategia predefinita è appropriata nei casi seguenti:

*  Le partizioni hanno approssimativamente le stesse dimensioni
*  Le partizioni ottengono approssimativamente la stessa quantità di traffico
*  I nodi del ruolo di lavoro hanno le stesse dimensioni/tipo
*  Le partizioni non sono state aggiunte a ruoli di lavoro specifici

Se uno di questi presupposti non è presente, il ribilanciamento predefinito può causare un piano non valido. In questo caso è possibile personalizzare la strategia, usando il `rebalance_strategy` parametro.

È consigliabile chiamare [get_rebalance_table_shards_plan](#get_rebalance_table_shards_plan) prima di eseguire il ribilanciamento \_ \_ delle partizioni della tabella per visualizzare e verificare le azioni da eseguire.

#### <a name="arguments"></a>Argomenti

** \_ nome tabella:** (facoltativo) nome della tabella di cui è necessario ribilanciare le partizioni. Se NULL, ribilanciare tutti i gruppi di condivisione percorso esistenti.

**Threshold:** (facoltativo) un numero float compreso tra 0,0 e 1,0 che indica il rapporto tra la differenza massima di utilizzo del nodo e l'utilizzo medio. Se ad esempio si specifica 0,1, il ribilanciamento delle partizioni tenterà di bilanciare tutti i nodi in modo da mantenere lo stesso numero di partizioni pari a ± 10%.
In particolare, il ribilanciamento delle partizioni tenterà di convergere l'utilizzo di tutti i nodi del ruolo di lavoro per l'utilizzo medio (soglia 1) \* \_ \. . (1
+ soglia) \* \_ intervallo di utilizzo medio.

**massimo spostamento \_ partizione \_ :** (facoltativo) numero massimo di partizioni da spostare.

**Elenco partizioni escluse \_ \_ :** (facoltativo) identificatori di partizioni che non devono essere spostati durante l'operazione di ribilanciamento.

** \_ modalità di trasferimento partizioni \_ :** (facoltativo) specificare il metodo di replica, se usare la replica logica PostgreSQL o un comando di copia tra i thread di lavoro. I valori possibili sono:

> -   `auto`: Richiedere l'identità della replica se è possibile la replica logica. in caso contrario, usare il comportamento legacy, ad esempio per il ripristino della partizione, PostgreSQL 9,6. Si tratta del valore predefinito.
> -   `force_logical`: Usare la replica logica anche se la tabella non ha un'identità di replica. Eventuali istruzioni Update/Delete simultanee della tabella avranno esito negativo durante la replica.
> -   `block_writes`: Usare copia (scritture di blocco) per le tabelle prive di chiave primaria o identità di replica.

** \_ solo svuotamento:** (facoltativo) se il valore è true, spostare le partizioni dai nodi del ruolo di lavoro che hanno `shouldhaveshards` impostato su false in [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table); non spostare altre partizioni.

**strategia di ribilanciamento \_ :** (facoltativo) nome di una strategia in [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table).
Se questo argomento viene omesso, la funzione sceglie la strategia predefinita, come indicato nella tabella.

#### <a name="return-value"></a>Valore restituito

N/D

#### <a name="example"></a>Esempio

Nell'esempio seguente si tenterà di ribilanciare le partizioni della tabella degli \_ eventi di GitHub entro la soglia predefinita.

```postgresql
SELECT rebalance_table_shards('github_events');
```

Questo utilizzo di esempio tenterà di ribilanciare la \_ tabella degli eventi di GitHub senza trasferire le partizioni con ID 1 e 2.

```postgresql
SELECT rebalance_table_shards('github_events', excluded_shard_list:='{1,2}');
```

### <a name="get_rebalance_table_shards_plan"></a>ottenere il \_ piano di ribilanciamento delle partizioni della \_ tabella \_ \_

Output dei movimenti partizionati pianificati di [rebalance_table_shards](#rebalance_table_shards) senza eseguirli.
Sebbene sia improbabile, il \_ piano di ribilanciamento delle partizioni della \_ tabella \_ \_ può restituire un piano leggermente diverso rispetto a quello che verrà eseguita da una ribilanciamento delle partizioni \_ \_ della tabella con gli stessi argomenti. Non vengono eseguite allo stesso tempo, quindi i fatti del gruppo di server, \- ad esempio lo spazio su disco, \- potrebbero essere diversi tra le chiamate.

#### <a name="arguments"></a>Argomenti

Gli stessi argomenti del ribilanciamento delle partizioni della \_ tabella \_ : relazione, soglia, numero massimo di partizioni \_ \_ , Elenco partizioni escluse \_ \_ e \_ solo svuotamento. Vedere la documentazione di tale funzione per il significato degli argomenti.

#### <a name="return-value"></a>Valore restituito

Tuple contenenti le colonne seguenti:

-   ** \_ nome tabella**: tabella di cui si sposteranno le partizioni
-   **shardid**: partizione in questione
-   ** \_ Dimensioni partizione**: dimensioni in byte
-   **SourceName**: nome host del nodo di origine
-   **sourceport**: porta del nodo di origine
-   **TargetName**: nome host del nodo di destinazione
-   **TARGETPORT**: porta del nodo di destinazione

### <a name="get_rebalance_progress"></a>ottenere lo \_ stato di ribilanciamento \_

Una volta avviato il ribilanciamento di una partizione, la `get_rebalance_progress()` funzione elenca lo stato di ogni partizione. Monitora gli spostamenti pianificati ed eseguiti da `rebalance_table_shards()` .

#### <a name="arguments"></a>Argomenti

N/D

#### <a name="return-value"></a>Valore restituito

Tuple contenenti le colonne seguenti:

-   **SessionID**: PID Postgres del monitoraggio del ribilanciamento
-   ** \_ nome tabella**: tabella di cui si stanno muovendo le partizioni
-   **shardid**: partizione in questione
-   ** \_ Dimensioni partizione**: dimensioni in byte
-   **SourceName**: nome host del nodo di origine
-   **sourceport**: porta del nodo di origine
-   **TargetName**: nome host del nodo di destinazione
-   **TARGETPORT**: porta del nodo di destinazione
-   **stato**: 0 = in attesa di spostamento; 1 = spostato; 2 = completato

#### <a name="example"></a>Esempio

```sql
SELECT * FROM get_rebalance_progress();
```

```
┌───────────┬────────────┬─────────┬────────────┬───────────────┬────────────┬───────────────┬────────────┬──────────┐
│ sessionid │ table_name │ shardid │ shard_size │  sourcename   │ sourceport │  targetname   │ targetport │ progress │
├───────────┼────────────┼─────────┼────────────┼───────────────┼────────────┼───────────────┼────────────┼──────────┤
│      7083 │ foo        │  102008 │    1204224 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102009 │    1802240 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102018 │     614400 │ n2.foobar.com │       5432 │ n4.foobar.com │       5432 │        1 │
│      7083 │ foo        │  102019 │       8192 │ n3.foobar.com │       5432 │ n4.foobar.com │       5432 │        2 │
└───────────┴────────────┴─────────┴────────────┴───────────────┴────────────┴───────────────┴────────────┴──────────┘
```

### <a name="citus_add_rebalance_strategy"></a>CITUS \_ aggiungere la \_ strategia di ribilanciamento \_

Accoda una riga a [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table) .

#### <a name="arguments"></a>Argomenti

Per ulteriori informazioni su questi argomenti, vedere i valori di colonna corrispondenti in `pg_dist_rebalance_strategy` .

**Nome:** identificatore per la nuova strategia

**funzione di costo della partizione \_ \_ :** identifica la funzione usata per determinare il \" costo \" di ogni partizione

** \_ funzione node Capacity \_ :** identifica la funzione per misurare la capacità del nodo

**partizione \_ consentita \_ nella \_ \_ funzione node:** identifica la funzione che determina quali partizioni possono essere posizionate sui nodi

** \_ soglia predefinita:** soglia a virgola mobile che ottimizza la precisione con cui bilanciare il costo della partizione cumulativa tra i nodi

** \_ soglia minima:** (facoltativo) colonna di salvaguardia che include il valore minimo consentito per l'argomento soglia delle partizioni di tabella di ribilanciamento \_ \_ (). Il valore predefinito è 0

#### <a name="return-value"></a>Valore restituito

N/D

### <a name="citus_set_default_rebalance_strategy"></a>CITUS \_ impostare \_ la \_ strategia di ribilanciamento predefinita \_

Aggiornare la tabella [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table) , modificando la strategia denominata dall'argomento in modo che sia il valore predefinito scelto durante il ribilanciamento delle partizioni.

#### <a name="arguments"></a>Argomenti

**Nome:** il nome della strategia nella strategia di \_ \_ ribilanciamento dei dists di PG \_

#### <a name="return-value"></a>Valore restituito

N/D

#### <a name="example"></a>Esempio

```postgresql
SELECT citus_set_default_rebalance_strategy('by_disk_size');
```

### <a name="citus_remote_connection_stats"></a>\_ \_ statistiche connessione remota \_ CITUS

La \_ funzione CITUS Remote \_ Connection \_ stats () Mostra il numero di connessioni attive a ogni nodo remoto.

#### <a name="arguments"></a>Argomenti

N/D

#### <a name="example"></a>Esempio

```postgresql
SELECT * from citus_remote_connection_stats();
```

```
    hostname    | port | database_name | connection_count_to_node
----------------+------+---------------+--------------------------
 citus_worker_1 | 5432 | postgres      |                        3
(1 row)
```

### <a name="master_drain_node"></a>\_nodo di svuotamento principale \_

La \_ funzione del \_ nodo di svuotamento principale () sposta le partizioni dal nodo designato e dagli altri nodi che hanno `shouldhaveshards` impostato su true in [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table). Chiamare la funzione prima di rimuovere un nodo dal gruppo di server e spegnere il server fisico del nodo.

#### <a name="arguments"></a>Argomenti

**NodeName:** Nome del nome host del nodo da svuotare.

Deport **:** Numero di porta del nodo da svuotare.

** \_ modalità di trasferimento partizioni \_ :** (facoltativo) specificare il metodo di replica, se usare la replica logica PostgreSQL o un comando di copia tra i thread di lavoro. I valori possibili sono:

> -   `auto`: Richiedere l'identità della replica se è possibile la replica logica. in caso contrario, usare il comportamento legacy, ad esempio per il ripristino della partizione, PostgreSQL 9,6. Si tratta del valore predefinito.
> -   `force_logical`: Usare la replica logica anche se la tabella non ha un'identità di replica. Eventuali istruzioni Update/Delete simultanee della tabella avranno esito negativo durante la replica.
> -   `block_writes`: Usare copia (scritture di blocco) per le tabelle prive di chiave primaria o identità di replica.

**strategia di ribilanciamento \_ :** (facoltativo) nome di una strategia in [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table).
Se questo argomento viene omesso, la funzione sceglie la strategia predefinita, come indicato nella tabella.

#### <a name="return-value"></a>Valore restituito

N/D

#### <a name="example"></a>Esempio

Ecco i passaggi tipici per rimuovere un singolo nodo (ad esempio "10.0.0.1" in una porta PostgreSQL standard):

1.  Svuotare il nodo.

    ```postgresql
    SELECT * from master_drain_node('10.0.0.1', 5432);
    ```

2.  Attendere il completamento del comando

3.  Rimuovere il nodo

Quando si svuotano più nodi, è consigliabile usare invece [rebalance_table_shards](#rebalance_table_shards) . In questo modo, è possibile pianificare in anticipo l'iperscalabilità (CITUS) e spostare le partizioni il numero minimo di volte.

1.  Eseguire per ogni nodo che si desidera rimuovere:

    ```postgresql
    SELECT * FROM master_set_node_property(node_hostname, node_port, 'shouldhaveshards', false);
    ```

2.  Svuotarli tutti contemporaneamente con [rebalance_table_shards](#rebalance_table_shards)

    ```postgresql
    SELECT * FROM rebalance_table_shards(drain_only := true);
    ```

3.  Attendere il completamento del ribilanciamento dello svuotamento

4.  Rimuovere i nodi

### <a name="replicate_table_shards"></a>replicare le partizioni di \_ tabella \_

La funzione replicate \_ Table \_ partizionas () replica le partizioni sottoreplicate della tabella specificata. La funzione calcola innanzitutto l'elenco delle partizioni e dei percorsi sottoreplicati da cui è possibile recuperare per la replica. La funzione copia quindi le partizioni e aggiorna i metadati della partizione corrispondenti in modo da riflettere la copia.

#### <a name="arguments"></a>Argomenti

** \_ nome tabella:** il nome della tabella di cui è necessario replicare le partizioni.

** \_ fattore di replica partizione \_ :** (facoltativo) il fattore di replica desiderato da ottenere per ogni partizione.

numero massimo di ** \_ copie di partizione \_ :** (facoltativo) numero massimo di partizioni da copiare per raggiungere il fattore di replica desiderato.

**Elenco partizioni escluse \_ \_ :** (facoltativo) identificatori delle partizioni che non devono essere copiate durante l'operazione di replica.

#### <a name="return-value"></a>Valore restituito

N/D

#### <a name="examples"></a>Esempi

Nell'esempio seguente viene effettuato un tentativo di replicare le partizioni della \_ tabella degli eventi GitHub in un fattore di replica di partizionamento \_ \_ .

```postgresql
SELECT replicate_table_shards('github_events');
```

Questo esempio tenterà di riportare le \_ partizioni della tabella eventi github al fattore di replica desiderato con un massimo di 10 copie di partizionamento. Tramite il ribilanciamento verrà copiato un massimo di 10 partizioni nel tentativo di raggiungere il fattore di replica desiderato.

```postgresql
SELECT replicate_table_shards('github_events', max_shard_copies:=10);
```

### <a name="isolate_tenant_to_new_shard"></a>isolare il \_ tenant \_ in una \_ nuova \_ partizione

Questa funzione crea una nuova partizione per mantenere le righe con un valore singolo specifico nella colonna di distribuzione. È particolarmente utile per il caso d'uso di iperscalabilità (CITUS) multi-tenant, in cui un tenant di grandi dimensioni può essere inserito da solo nella propria partizione e infine nel proprio nodo fisico.

#### <a name="arguments"></a>Argomenti

** \_ nome tabella:** il nome della tabella per ottenere una nuova partizione.

** \_ ID tenant:** valore della colonna di distribuzione che verrà assegnato alla nuova partizione.

** \_ opzione CASCADE:** (facoltativo) se impostata su \" CASCADE, \" isola anche una partizione da tutte le tabelle del [gruppo](concepts-hyperscale-colocation.md)di condivisione della tabella corrente.

#### <a name="return-value"></a>Valore restituito

** \_ ID partizione:** la funzione restituisce l'ID univoco assegnato alla partizione appena creata.

#### <a name="examples"></a>Esempi

Creare una nuova partizione per l'LineItems del tenant 135:

```postgresql
SELECT isolate_tenant_to_new_shard('lineitem', 135);
```

```
┌─────────────────────────────┐
│ isolate_tenant_to_new_shard │
├─────────────────────────────┤
│                      102240 │
└─────────────────────────────┘
```

## <a name="next-steps"></a>Passaggi successivi

* Molte delle funzioni in questo articolo modificano le [tabelle dei metadati](reference-hyperscale-metadata.md) di sistema
* [Parametri del server](reference-hyperscale-parameters.md) personalizzare il comportamento di alcune funzioni
