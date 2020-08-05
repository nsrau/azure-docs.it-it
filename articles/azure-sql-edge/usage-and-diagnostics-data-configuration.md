---
title: Configurazione dei dati di utilizzo e diagnostica di Azure SQL Edge
description: Informazioni su come configurare i dati di utilizzo e diagnostica in Azure SQL Edge.
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 08/04/2020
ms.openlocfilehash: 1f6624c454364ca19c8ce112cb1cbbef134f162d
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87567977"
---
# <a name="azure-sql-edge-usage-and-diagnostics-data-configuration"></a>Configurazione dei dati di utilizzo e diagnostica di Azure SQL Edge

Per impostazione predefinita, Azure SQL Edge raccoglie informazioni sul modo in cui i clienti usano l'applicazione. In particolare, Azure SQL Edge raccoglie informazioni sull'esperienza, l'utilizzo e le prestazioni della distribuzione. Queste informazioni consentono a Microsoft di migliorare il prodotto per meglio soddisfare le esigenze dei clienti. Ad esempio, Microsoft raccoglie informazioni sui tipi di codici di errore riscontrati dai clienti, in modo da poter correggere i bug correlati, migliorare la documentazione su come usare Azure SQL Edge e determinare se è necessario aggiungere funzionalità al prodotto per offrire al meglio i clienti.

In particolare, Microsoft non invia alcuna informazione dei tipi seguenti tramite questo meccanismo:

- Tutti i valori delle tabelle utente all'interno di.
- Eventuali credenziali di accesso o altre informazioni di autenticazione.
- Dati personali o del cliente.

Lo scenario di esempio seguente include informazioni sull'utilizzo di funzionalità che consentono di migliorare il prodotto.

Di seguito è riportata una query di esempio delle query usate per la raccolta dei dati di utilizzo e di diagnostica. La query identifica il numero e i tipi di origini dati di flusso diverse usate in Azure SQL Edge. Questi dati consentono a Microsoft di identificare quali origini dati di streaming vengono utilizzate in genere in modo che Microsoft possa migliorare le prestazioni e l'esperienza utente associate a tali origini dati. 

```sql
select 
count(*) as [count], sum(inputs) as inputs, sum(outputs) as outputs, sum(linked_to_job) 
as linked_to_job, data_source_type  
from ( 
select isnull(value,'unknown') as data_source_type, inputs, outputs, linked_to_job  
from 
    ( 
        select 
        convert(sysname, lower(substring(ds.location, 0, charindex('://', ds.location))), 1) as data_source_type, 
        isnull(inputs, 0) as inputs, isnull(outputs, 0) as outputs, isnull(js.stream_id/js.stream_id, 0) as linked_to_job 
        from sys.external_streams es              
        join sys.external_data_sources ds 
             on es.data_source_id = ds.data_source_id             
        left join 
            ( 
            select stream_id, max(cast(is_input as int)) inputs, max(cast(is_output as int)) outputs 
            from sys.external_job_streams group by stream_id 
            ) js                
             on js.stream_id = es.object_id 
    ) ds            
left join 
    (
        select value from string_split('edgehub,sqlserver,kafka', ',')) as known_ep on data_source_type = value 
    ) known_ds        
group by data_source_type
```

## <a name="disable-usage-and-diagnostic-data-collection"></a>Disabilitare la raccolta dei dati di utilizzo e di diagnostica

La raccolta dei dati di utilizzo e di diagnostica in Azure SQL Edge può essere disabilitata usando uno dei metodi seguenti.

### <a name="disable-usage-and-diagnostics-using-environment-variables"></a>Disabilitare l'utilizzo e la diagnostica usando le variabili di ambiente

Per disabilitare la raccolta dei dati di utilizzo e diagnostica in Azure SQL Edge, aggiungere la variabile di ambiente seguente e impostarne il valore su `*False*` . Per altre informazioni sulla configurazione di Azure SQL Edge con le variabili di ambiente, vedere [configurare usando le variabili di ambiente](configure.md#configure-by-using-environment-variables).

`MSSQL_TELEMETRY_ENABLED = TRUE | FALSE`

- TRUE: Abilita la raccolta dei dati di utilizzo e di diagnostica. Questa è la configurazione predefinita.
- FALSE-Disabilita la raccolta dei dati di utilizzo e di diagnostica

### <a name="disable-usage-and-diagnostics-using-mssqlconf-file"></a>Disabilitare l'utilizzo e la diagnostica tramite il file MSSQL. conf

Per disabilitare la raccolta dei dati di utilizzo e diagnostica in Azure SQL Edge, aggiungere i file seguenti nel file MSSQL. conf nell'unità di archiviazione persistente di cui è stato eseguito il mapping alla cartella/var/opt/MSSQL/nel modulo SQL Edge. Per altre informazioni sulla configurazione di Azure SQL Edge tramite il file MSSQL. conf, vedere [configurare usando il file MSSQL. conf](configure.md#configure-by-using-an-mssqlconf-file).

```ini
[telemetry]
customerfeedback = false
```

## <a name="local-audit-of-usage-and-diagnostic-data-collection"></a>Controllo locale della raccolta dei dati di utilizzo e di diagnostica

Il componente di controllo locale della raccolta di dati di diagnostica e utilizzo di Azure SQL Edge può scrivere i dati raccolti dal servizio in una cartella specificata, che rappresenta i dati (log) che verranno inviati a Microsoft. Lo scopo del controllo locale è quello di consentire ai clienti di visualizzare tutti i dati che Microsoft raccoglie con questa funzionalità, per motivi di conformità alle normative o rispetto della privacy.

### <a name="enable-local-audit-of-usage-and-diagnostics-data"></a>Abilitare il controllo locale dei dati di utilizzo e di diagnostica

Per abilitare i dati di diagnostica e di utilizzo del controllo locale in Azure SQL Edge

1. Creare una directory di destinazione per il nuovo archivio dei log di controllo locale. Questa directory di destinazione deve essere creata nello stesso volume di montaggio mappato al percorso/var/opt/MSSQL/su SQL Edge.

   ```bash
   sudo mkdir <host mount path>/audit
   ```

2. Configurare il controllo dei dati di utilizzo e di diagnostica usando le variabili di ambiente o il file MSSQL. conf.

   - Uso delle variabili di ambiente: aggiungere la variabile di ambiente seguente alla distribuzione di SQL Edge.
   
     `*MSSQL_TELEMETRY_DIR = /var/opt/mssql/audit*`
   
   - Con il file MSSQL. conf aggiungere le righe seguenti nel file MSSQL. conf.
       ```ini
       [telemetry]
       userrequestedlocalauditdirectory  = /var/opt/mssql/audit
       ```  

## <a name="next-steps"></a>Passaggi successivi

- [Connettersi a SQL Edge di Azure](connect.md)
- [Creare una soluzione IoT end-to-end con SQL Edge](tutorial-deploy-azure-resources.md)
