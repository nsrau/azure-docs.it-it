---
title: Sincronizzare i dati (anteprima) | Microsoft Docs
description: "Questa panoramica è un'introduzione all'anteprima di sincronizzazione dati SQL di Azure."
services: sql-database
documentationcenter: 
author: douglaslms
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: load & move data
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: 34bc9588745eb24d8b8c2e81389a9e5144497b34
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL

La sincronizzazione dati SQL è un servizio basato sul database SQL di Azure che consente di sincronizzare i dati selezionati bidirezionalmente tra più database SQL e istanze di SQL Server.

La sincronizzazione dati si basa sul concetto di gruppo di sincronizzazione. Un gruppo di sincronizzazione è un gruppo di database che si vuole sincronizzare.

Di seguito sono elencate le proprietà di un gruppo di sincronizzazione:

-   Lo **schema di sincronizzazione** descrive i dati da sincronizzare.

-   La **direzione di sincronizzazione** può essere bidirezionale o unidirezionale. Ovvero, la direzione di sincronizzazione può essere *dall'hub al membro* o *dal membro all'hub* o entrambe.

-   L'**intervallo di sincronizzazione** è la frequenza con cui viene eseguita la sincronizzazione.

-   I **criteri di risoluzione dei conflitti** sono criteri a livello di gruppo e le impostazioni possono essere *Priorità hub* o *Priorità client*.

La sincronizzazione dati usa una topologia hub-spoke per sincronizzare i dati. Uno dei database nel gruppo viene definito come database hub. Il resto dei database sono database membri. La sincronizzazione si verifica solo tra l'hub e i singoli membri.
-   Il **database hub** deve essere un database SQL di Azure.
-   I **database membri** possono essere database SQL, database di SQL Server locali o istanze di SQL Server in macchine virtuali di Azure.
-   Il **database di sincronizzazione** contiene i metadati e il log per la sincronizzazione dati. Il database di sincronizzazione deve essere un database SQL di Azure posizionato nella stessa area del database hub. Il database di sincronizzazione viene creato dal cliente ed è di sua proprietà.

> [!NOTE]
> Se si usa un database locale, è necessario [configurare un agente locale.](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-get-started-sql-data-sync)

![Sincronizzare i dati tra database](media/sql-database-sync-data/sync-data-overview.png)

## <a name="when-to-use-data-sync"></a>Quando usare la sincronizzazione dati

La sincronizzazione dati è utile nei casi in cui i dati devono essere mantenuti aggiornati in diversi database SQL di Azure o database di SQL Server. Questi sono i principali casi d'uso per la sincronizzazione dati:

-   **Sincronizzazione dei dati ibrida:** con la sincronizzazione dei dati è possibile mantenere i dati sincronizzati tra database locali e database SQL di Azure per rendere possibili applicazioni ibride. Questa funzionalità può essere interessante per i clienti che stanno valutando il passaggio al cloud e vorrebbero trasferire alcune applicazioni in Azure.

-   **Applicazioni distribuite:** in molti casi è vantaggioso separare carichi di lavoro diversi in database differenti. Ad esempio, se si dispone di un database di produzione di grandi dimensioni, ma è anche necessario eseguire un carico di lavoro di report o analisi su tali dati, può essere utile avere un secondo database per questo carico di lavoro aggiuntivo. Questo approccio riduce al minimo l'impatto a livello di prestazioni sul carico di lavoro di produzione. È possibile usare la sincronizzazione dati per mantenere sincronizzati i due database.

-   **Applicazioni distribuite a livello globale:** molte aziende sono estese a più aree, a volte anche in paesi diversi. Per ridurre al minimo la latenza di rete, è consigliabile posizionare i dati in un'area vicina. Con sincronizzazione dati è possibile mantenere facilmente sincronizzati i database in aree in tutto il mondo.

Il servizio di sincronizzazione dati è sconsigliato per gli scenari seguenti:

-   Ripristino di emergenza

-   Scalabilità in lettura

-   ETL (da OLTP a OLAP)

-   Migrazione da SQL Server locale al database SQL di Azure

## <a name="how-does-data-sync-work"></a>Come funziona la sincronizzazione dati? 

-   **Rilevamento delle modifiche ai dati:** sincronizzazione dati tiene traccia delle modifiche tramite trigger di inserimento, aggiornamento ed eliminazione. Le modifiche vengono registrate in una tabella laterale nel database utente.

-   **Sincronizzazione dei dati:** il servizio di sincronizzazione dati è progettato in base a un modello hub-spoke. L'hub sincronizza singolarmente ogni membro. Le modifiche dall'hub vengono scaricate nel membro e quindi le modifiche dal membro vengono caricate nell'hub.

-   **Risoluzione dei conflitti:** sincronizzazione dati offre due opzioni per la risoluzione dei conflitti, ovvero *Priorità hub* o *Priorità client*.
    -   Se si seleziona *Priorità hub*, le modifiche nell'hub sovrascrivono sempre le modifiche nel membro.
    -   Se si seleziona *Priorità client*, le modifiche nel membro sovrascrivono sempre le modifiche nell'hub. In presenza di più di un membro, il valore finale dipende dal membro sincronizzato per primo.

## <a name="limitations-and-considerations"></a>Limitazioni e considerazioni

### <a name="performance-impact"></a>Impatto sulle prestazioni
La sincronizzazione dati usa trigger di inserimento, aggiornamento ed eliminazione per il rilevamento delle modifiche e crea tabelle laterali nel database utente per il rilevamento delle modifiche. Queste attività di rilevamento delle modifiche hanno un impatto sul carico di lavoro del database. Valutare il livello di servizio e aggiornare se necessario.

### <a name="eventual-consistency"></a>Coerenza finale
Dato che la sincronizzazione dati è basata su trigger, la coerenza delle transazioni non è garantita. Microsoft garantisce che tutte le modifiche vengono apportate alla fine e che la sincronizzazione dati non causi perdite di dati.

### <a name="unsupported-data-types"></a>Tipi di dati non supportati

-   FileStream

-   Tipo definito dall'utente (UDT) SQL/CLR

-   XMLSchemaCollection (supportato da XML)

-   Cursor, Timestamp, Hierarchyid

### <a name="requirements"></a>Requisiti

-   Ogni tabella deve avere una chiave primaria. Non modificare il valore della chiave primaria in alcuna riga. Se questa modifica è necessaria, eliminare la riga e ricrearla con il nuovo valore di chiave primaria. 

-   Una tabella non può includere colonne Identity che non sono la chiave primaria.

-   I nomi degli oggetti (database, tabelle e colonne) non possono contenere i caratteri stampabili punto (.), parentesi quadra aperta ([) o parentesi quadra chiusa (]).

-   L'isolamento dello snapshot deve essere abilitato. Per altre informazioni, vedere [Isolamento dello snapshot in SQL Server](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="limitations-on-service-and-database-dimensions"></a>Limitazioni alle dimensioni del servizio e del database

|                                                                 |                        |                             |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| **Dimensioni**                                                      | **Limite**              | **Soluzione alternativa**              |
| Numero massimo di gruppi di sincronizzazione a cui può appartenere qualsiasi database.       | 5                      |                             |
| Numero massimo di endpoint in un singolo gruppo di sincronizzazione              | 30                     | Creare più gruppi di sincronizzazione |
| Numero massimo di endpoint locali in un singolo gruppo di sincronizzazione. | 5                      | Creare più gruppi di sincronizzazione |
| Nomi di database, tabella, schema e colonna                       | 50 caratteri per nome |                             |
| Tabelle in un gruppo di sincronizzazione                                          | 500                    | Creare più gruppi di sincronizzazione |
| Colonne in una tabella in un gruppo di sincronizzazione                              | 1000                   |                             |
| Dimensioni delle righe di dati in una tabella                                        | 24 MB                  |                             |
| Intervallo minimo di sincronizzazione                                           | 5 minuti              |                             |

## <a name="common-questions"></a>Domande frequenti

### <a name="how-frequently-can-data-sync-synchronize-my-data"></a>Con quale frequenza sincronizzazione dati sincronizza i dati? 
La frequenza minima è ogni 5 minuti.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>È possibile usare la sincronizzazione dati solo tra database SQL Server locali? 
Non direttamente. Tuttavia, è possibile sincronizzare in maniera indiretta tra i database SQL Server locali creando un database hub in Azure e aggiungendo i database locali al gruppo di sincronizzazione.
   
### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-keep-them-synchronized"></a>È possibile usare la sincronizzazione dati per effettuare il seeding dei dati da un database di produzione a un database vuoto, mantenendoli poi sincronizzati? 
Sì. Creare manualmente lo schema nel nuovo database effettuando lo scripting dall'originale. Dopo aver creato lo schema, aggiungere le tabelle a un gruppo di sincronizzazione per copiare i dati e mantenerli sincronizzati.

### <a name="why-do-i-see-tables-that-i-did-not-create"></a>Perché vengono visualizzate tabelle che non risultano essere state create?  
Sincronizzazione dati crea tabelle laterali nel database utente per il rilevamento delle modifiche. Non eliminarle in quanto sono richieste per il funzionamento di sincronizzazione dati.
   
### <a name="i-got-an-error-message-that-said-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-the-error"></a>Viene visualizzato un messaggio di errore: "Impossibile inserire il valore NULL nella colonna \<colonna\>. La colonna non ammette valori Null." Cosa significa e come è possibile correggere l'errore? 
Questo messaggio di errore indica uno dei due problemi seguenti:
1.  Potrebbe essere presente una tabella priva di chiave primaria. Per risolvere il problema, aggiungere una chiave primaria a tutte le tabelle da sincronizzare.
2.  Potrebbe essere presente una clausola WHERE nell'istruzione CREATE INDEX. La sincronizzazione non è in grado di gestire questa condizione. Per risolvere il problema, rimuovere la clausola WHERE o apportare manualmente le modifiche a tutti i database. 
 
### <a name="how-does-data-sync-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a>In che modo la sincronizzazione dati gestisce i riferimenti circolari? In altre parole, quando vengono sincronizzati gli stessi dati in più gruppi di sincronizzazione e pertanto continuano a cambiare?
La sincronizzazione dati non gestisce i riferimenti circolari. Si consiglia di evitarli. 

### <a name="how-can-i-export-and-import-a-database-with-data-sync"></a>Come si esporta e importa un database con la sincronizzazione dei dati?
Dopo aver esportato un database come file con estensione bacpac e averlo importato per creare un nuovo database, è necessario eseguire le due operazioni seguenti per usare la sincronizzazione dei dati nel nuovo database:
1.  Pulire le tabelle e gli oggetti di sincronizzazione dei dati nel **nuovo database** usando [questo script](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/clean_up_data_sync_objects.sql). Questo script elimina tutti gli oggetti di sincronizzazione dei dati dal database.
2.  Creare di nuovo il gruppo di sincronizzazione con il nuovo database. Se il gruppo di sincronizzazione precedente non è più necessario, eliminarlo.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla sincronizzazione dati SQL, vedere:

-   [Introduzione alla sincronizzazione dati SQL](sql-database-get-started-sql-data-sync.md)

-   Esempi di PowerShell completi che illustrano come configurare la sincronizzazione dati SQL:
    -   [Usare PowerShell per sincronizzare più database SQL di Azure](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Usare PowerShell per la sincronizzazione tra un database SQL di Azure e un database locale di SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Scaricare la documentazione dell'API REST di sincronizzazione dati SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Per altre informazioni sul database SQL, vedere:

-   [Panoramica del database SQL](sql-database-technical-overview.md)

-   [Gestione del ciclo di vita del database](https://msdn.microsoft.com/library/jj907294.aspx)
