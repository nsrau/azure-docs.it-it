---
title: HBase hbck restituisce incoerenze in Azure HDInsight
description: HBase hbck restituisce incoerenze in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/08/2019
ms.openlocfilehash: 6bf136d943f230eb02df62cbaa732e28d6b359a4
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968462"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>Scenario: `hbase hbck` il comando restituisce incoerenze in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue-region-is-not-in-hbasemeta"></a>Problema: L'area non è in`hbase:meta`

Region xxx in HDFS, ma non elencato in `hbase:meta` o distribuito in un server di area.

### <a name="cause"></a>Causa

Varia.

### <a name="resolution"></a>Risoluzione

1. Correggere la meta tabella eseguendo:

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. Assegnare le aree a RegionServers eseguendo:

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>Problema: Area offline

L'area xxx non è distribuita in alcun RegionServer. Ciò significa che l'area è `hbase:meta`in, ma offline.

### <a name="cause"></a>Causa

Varia.

### <a name="resolution"></a>Risoluzione

Portare online le aree eseguendo:

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>Problema: Le aree hanno le stesse chiavi di inizio/fine

### <a name="cause"></a>Causa

Varia.

### <a name="resolution"></a>Risoluzione

Unire manualmente le aree sovrapposte. Passare alla sezione tabella dell'interfaccia utente Web HBase HMaster, selezionare il collegamento alla tabella, che presenta il problema. Viene visualizzata la chiave di inizio/fine di ogni area che appartiene a tale tabella. Unire quindi le aree sovrapposte. In HBase Shell, fare `merge_region 'xxxxxxxx','yyyyyyy', true`. Ad esempio:

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

In questo scenario è necessario unire regiona e RegionC e ottenere l'area con lo stesso intervallo di chiavi di RegionB, quindi unire RegionB e regiond. xxxxxxx e YYYYYY sono la stringa hash alla fine di ogni nome di area. Prestare attenzione a non unire due aree discontinue. Dopo ogni Unione, ad esempio merge A e C, HBase avvierà una compattazione nell'area. Attendere il completamento della compattazione prima di eseguire un'altra operazione di merge con l'area geografica. È possibile trovare lo stato di compattazione nella pagina del server di area nell'interfaccia utente di HBase HMaster.

---

## <a name="issue-cant-load-regioninfo"></a>Problema: Non è possibile caricare`.regioninfo`

Non è `.regioninfo` possibile caricare `/hbase/data/default/tablex/regiony`per l'area.

### <a name="cause"></a>Causa

Questa operazione è probabilmente dovuta all'eliminazione parziale dell'area quando si verifica un arresto anomalo di RegionServer o il riavvio della macchina virtuale. Attualmente, archiviazione di Azure è un BLOB flat file system e alcune operazioni sui file non sono atomiche.

### <a name="resolution"></a>Risoluzione

Pulire manualmente i file e le cartelle rimanenti:

1. Eseguire `hdfs dfs -ls /hbase/data/default/tablex/regiony` per verificare quali cartelle/file sono ancora al di sotto di esso.

1. Esegui `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez` per eliminare tutti i file o le cartelle figlio

1. Eseguire `hdfs dfs -rmr /hbase/data/default/tablex/regiony` per eliminare la cartella Region.

---

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
