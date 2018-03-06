---
title: Risoluzione dei problemi relativi alle prestazioni delle query in Database di Azure per MySQL
description: Questo articolo descrive come usare EXPLAIN per risolvere i problemi di prestazioni delle query in Database di Azure per MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 3af6ad347cec171132ddfbec21137775c0f71245
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mysql"></a>Come usare EXPLAIN per profilare le prestazioni delle query in Database di Azure per MySQL
**EXPLAIN** è uno strumento comodo per ottimizzare le query. L'istruzione EXPLAIN può essere usata per ottenere informazioni sulla modalità di esecuzione delle istruzioni SQL. L'output seguente mostra un esempio di esecuzione di un'istruzione EXPLAIN.

```sql
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 10.00
        Extra: Using where
```

Come si può notare da questo esempio, il valore di *key* è NULL. Questo output indica che MySQL non è in grado di trovare indici ottimizzati per la query ed esegue una scansione di tabella completa. Per ottimizzare la query, viene aggiunto un indice sulla colonna **ID**.

```sql
mysql> ALTER TABLE tb1 ADD KEY (id);
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ref
possible_keys: id
          key: id
      key_len: 4
          ref: const
         rows: 1
     filtered: 100.00
        Extra: NULL
```

La nuova istruzione EXPLAIN mostra che MySQL ora usa un indice per limitare il numero di righe a 1, con conseguente riduzione notevole del tempo di ricerca.
 
## <a name="covering-index"></a>Indice di copertura
Un indice di copertura è costituito dall'inclusione di tutte le colonne di una query nell'indice per limitare il recupero di valori dalle tabelle dati. L'istruzione **GROUP BY** seguente illustra questo scenario.
 
```sql
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

Come è possibile osservare dall'output, MySQL non usa alcun indice perché non sono disponibili indici appropriati. Sono inoltre presenti le istruzioni *Using temporary; Using filesort*, che indicano che MySQL crea una tabella temporanea per soddisfare la clausola **GROUP BY**.
 
La creazione di un indice solo sulla colonna **c2** non fa alcuna differenza e MySQL deve comunque creare una tabella temporanea:

```sql 
mysql> ALTER TABLE tb1 ADD KEY (c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

In questo caso, è possibile creare un **indice di copertura** sia su **c1** che su **c2**, aggiungendo il valore di **c2** direttamente nell'indice per evitare ulteriori ricerche di dati.

```sql 
mysql> ALTER TABLE tb1 ADD KEY covered(c1,c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: covered
          key: covered
      key_len: 108
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using index
```

Come mostra l'istruzione EXPLAIN precedente, MySQL usa ora l'indice di copertura evitando di creare una tabella temporanea. 

## <a name="combined-index"></a>Indice combinato
Un indice combinato è costituito da valori da più colonne e può essere considerato una matrice di righe ordinate concatenando i valori delle colonne indicizzate. Questo metodo può essere utile in un'istruzione **GROUP BY**.

```sql
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using filesort
```

MySQL esegue un'operazione di *ordinamento file* piuttosto lenta, in particolare quando deve ordinare molte righe. Per ottimizzare la query, è possibile creare un indice combinato su entrambe le colonne da ordinare.

```sql 
mysql> ALTER TABLE tb1 ADD KEY my_sort2 (c1, c2);
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: NULL
          key: my_sort2
      key_len: 108
          ref: NULL
         rows: 10
     filtered: 11.11
        Extra: Using where; Using index
```

L'istruzione EXPLAIN mostra ora che MySQL è in grado di usare l'indice combinato per evitare ulteriori operazioni di ordinamento perché l'indice è già ordinato.
 
## <a name="conclusion"></a>Conclusioni
 
L'uso di EXPLAIN e di diversi tipi di indici può aumentare sensibilmente le prestazioni. Il fatto che sia presente un indice sulla tabella non significa necessariamente che MySQL sia in grado di usarlo per le query. Verificare sempre i presupposti usando EXPLAIN e ottimizzare le query con gli indici.


## <a name="next-steps"></a>Passaggi successivi
- Per trovare risposte dai colleghi alle domande più pressanti o per pubblicare una nuova domanda o risposta, visitare il [forum MSDN](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) o [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
