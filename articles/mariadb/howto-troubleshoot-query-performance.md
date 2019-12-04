---
title: Risolvere i problemi relativi alle prestazioni delle query-database di Azure per MariaDB
description: Informazioni su come usare EXPLAIN per risolvere i problemi relativi alle prestazioni delle query nel database di Azure per MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: troubleshooting
ms.date: 12/02/2019
ms.openlocfilehash: 36571cc1ac4fbdcd5c0c6a4007a6c43858c97193
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770986"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mariadb"></a>Come usare EXPLAIN per profilare le prestazioni delle query in Database di Azure per MariaDB
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

Come si può notare da questo esempio, il valore di *key* è NULL. Questo output indica che MariaDB non è in grado di trovare indici ottimizzati per la query ed esegue una scansione di tabella completa. Per ottimizzare la query, viene aggiunto un indice sulla colonna **ID**.

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

La nuova istruzione EXPLAIN mostra che MariaDB ora usa un indice per limitare il numero di righe a 1, con conseguente riduzione notevole del tempo di ricerca.
 
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

Come è possibile osservare dall'output, MariaDB non usa alcun indice perché non sono disponibili indici appropriati. Sono inoltre presenti le istruzioni *Using temporary; Using filesort*, che indicano che MariaDB crea una tabella temporanea per soddisfare la clausola **GROUP BY**.
 
La creazione di un indice solo sulla colonna **c2** non fa alcuna differenza e MariaDB deve comunque creare una tabella temporanea:

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

Come mostra l'istruzione EXPLAIN precedente, MariaDB usa ora l'indice di copertura evitando di creare una tabella temporanea. 

## <a name="combined-index"></a>Indice combinato
Un indice combinato è costituito da valori da più colonne e può essere considerato una matrice di righe ordinate concatenando i valori delle colonne indicizzate. Questo metodo può essere utile in un'istruzione **Group by** .

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

MariaDB esegue un'operazione di *ordinamento file* piuttosto lenta, in particolare quando deve ordinare molte righe. Per ottimizzare la query, è possibile creare un indice combinato su entrambe le colonne da ordinare.

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

L'istruzione EXPLAIN mostra ora che MariaDB è in grado di usare l'indice combinato per evitare ulteriori operazioni di ordinamento perché l'indice è già ordinato.
 
## <a name="conclusion"></a>Conclusione
 
L'uso di EXPLAIN e di diversi tipi di indici può aumentare sensibilmente le prestazioni. La presenza di un indice nella tabella non implica necessariamente che MariaDB possa utilizzarlo per le query. Verificare sempre i presupposti usando EXPLAIN e ottimizzare le query con gli indici.

## <a name="next-steps"></a>Passaggi successivi
- Per trovare risposte dai colleghi alle domande più pressanti o per pubblicare una nuova domanda o risposta, visitare il [forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDatabaseforMariadb) o [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mariadb).
