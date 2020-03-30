---
title: Domande frequenti sulle istanze gestite
description: Domande frequenti sull'istanza gestita del database SQLSQL Database managed instance frequently asked questions (FAQ)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 393d67b200a4f8d44cb001b3a7e2e491209e9d58
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80364158"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>Domande frequenti sull'istanza gestita del database SQLSQL Database managed instance frequently asked questions (FAQ)

In questo articolo contiene molte delle domande più comuni [sull'istanza gestita del database SQL.](sql-database-managed-instance.md)

## <a name="supported-features"></a>Caratteristiche supportate

**Dove è possibile trovare un elenco delle funzionalità supportate nell'istanza gestita?**

Per un elenco delle funzionalità supportate nell'istanza gestita, vedere Database SQL di [Azure rispetto a SQL Server](sql-database-features.md).

Per le differenze nella sintassi e nel comportamento tra l'istanza gestita del database SQL di Azure e SQL Server locale, vedere [Differenze T-SQL rispetto](sql-database-managed-instance-transact-sql-information.md)a SQL Server .


## <a name="tech-spec--resource-limits"></a>Limiti delle risorse tech spec &
 
**Dove è possibile trovare le caratteristiche tecniche e i limiti delle risorse per l'istanza gestita?**

Per le caratteristiche di generazione hardware disponibili, vedere [Differenze tecniche nelle generazioni di hardware](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
Per i livelli di servizio disponibili e le relative caratteristiche, vedere [Differenze tecniche tra i livelli di servizio](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="known-issues--bugs"></a>Problemi noti & bug

**Dove posso trovare problemi noti e bug?**

Per i bug e i problemi noti, vedere [problemi noti.](sql-database-release-notes.md#known-issues)

## <a name="new-features"></a>Nuove funzionalità

**Dove posso trovare le funzionalità più recenti e le funzionalità nell'anteprima pubblica?**

Per le funzionalità nuove e di anteprima, consultate [le note sulla versione.](sql-database-release-notes.md?tabs=managed-instance)

## <a name="deployment-times"></a>Tempi di distribuzione 

**Quanto tempo necessario per creare o aggiornare l'istanza o per ripristinare un database?**

Il tempo previsto per creare una nuova istanza gestita o modificare il livello di servizio (vCores, archiviazione) dipende da diversi fattori. Dai un'occhiata alle [operazioni di gestione](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 

## <a name="naming-convention"></a>Convenzione di denominazione

**Un'istanza gestita può avere lo stesso nome di SQL Server locale?**

La modifica del nome dell'istanza gestita non è supportata.

È possibile modificare la zona DNS predefinita dell'istanza gestita *.database.windows.net.* 

Per utilizzare un'altra zona DNS anziché quella predefinita, ad esempio *.contoso.com*: 
- Utilizzare CliConfig per definire un alias. Lo strumento è solo un wrapper di impostazioni del Registro di sistema, quindi potrebbe essere fatto utilizzando criteri di gruppo o script pure.
- Utilizzare *l'opzione CNAME* con *TrustServerCertificate.*

## <a name="move-db-from-mi"></a>Sposta DB da MI 

**Come è possibile spostare il database dall'istanza gestita a SQL ServerSQL Server o al database SQL di Azure?**

È possibile esportare il [database in BACPAC](sql-database-export.md) e quindi [importare il file BACPAC]( sql-database-import.md). Si tratta di un approccio consigliato se il database è inferiore a 100 GB.

La replica transazionale può essere utilizzata se tutte le tabelle del database dispongono di chiavi primarie.

I `COPY_ONLY` backup nativi eseguiti dall'istanza gestita non possono essere ripristinati in SQL ServerSQL Server perché l'istanza gestita ha una versione di database superiore rispetto a SQL ServerSQL Server.Native backups taken from managed instance cannot be restored to SQL ServerSQL Server because managed instance has a higher database version compared to SQL ServerSQL Server.

## <a name="migrate-instance-db"></a>Eseguire la migrazione del database dell'istanzaMigrate instance DB

**Come è possibile eseguire la migrazione del database dell'istanza in un singolo database SQL di Azure?**

Un'opzione consiste [nell'esportare il database in un BACPAC](sql-database-export.md) e quindi [importare il file BACPAC](sql-database-import.md). 

Questo è l'approccio consigliato se il database è inferiore a 100 GB. La replica transazionale può essere utilizzata se tutte le tabelle del database dispongono di chiavi primarie.

## <a name="switch-hardware-generation"></a>Cambia generazione hardware 

**È possibile passare dalla generazione hardware dell'istanza gestita alla generazione di hardware di tipo Gen 4 e Generazione 5?**

Il passaggio online automatizzato tra generazioni di hardware è possibile se entrambe le generazioni di hardware sono disponibili nell'area in cui viene eseguito il provisioning dell'istanza gestita. In questo caso, è possibile controllare la pagina di panoramica del [modello vCore](sql-database-service-tiers-vcore.md) che spiega come passare da una generazione di hardware all'altra.

Si tratta di un'operazione a esecuzione prolungata in quanto verrà eseguito il provisioning di una nuova istanza gestita in background e i database verranno trasferiti automaticamente tra l'istanza precedente e quella nuova con un failover rapido alla fine del processo. 

Se entrambe le generazioni di hardware non sono supportate nella stessa area, la modifica della generazione dell'hardware è possibile, ma deve essere eseguita manualmente. Ciò richiede il provisioning di una nuova istanza nell'area in cui è disponibile la generazione di hardware desiderato e il backup e il ripristino manuale dei dati tra l'istanza precedente e quella nuova.


## <a name="tune-performance"></a>Ottimizzare le prestazioni

**Come si ottimizzano le prestazioni dell'istanza gestita?**

L'istanza gestita General Purpose utilizza l'archiviazione remota a causa delle dimensioni dei file di dati e di log relative alle prestazioni. Per ulteriori informazioni, vedere [Impatto delle dimensioni del file di log sulle prestazioni dell'istanza gestita di uso generale](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

Se il carico di lavoro è costituito da molte transazioni di piccole dimensioni, è consigliabile passare il tipo di connessione dal proxy alla modalità di reindirizzamento.

## <a name="maximum-storage-size"></a>Dimensioni massime di archiviazione

**Qual è la dimensione massima di archiviazione per l'istanza gestita?**

Le dimensioni di archiviazione per l'istanza gestita dipendono dal livello di servizio selezionato (Uso generale o Business Critical). Per le limitazioni di archiviazione di questi livelli di servizio, vedere [Caratteristica del livello di servizio](sql-database-service-tiers-general-purpose-business-critical.md).

## <a name="back-up-storage-cost"></a>Eseguire il backup dei costi di archiviazione 

**L'archiviazione di backup viene dedotta dall'archivio dell'istanza gestita?**

No, l'archiviazione di backup non viene detratta dallo spazio di archiviazione dell'istanza gestita. L'archiviazione di backup è indipendente dallo spazio di archiviazione dell'istanza e non è di dimensioni limitate. L'archiviazione dei backup è limitata dal periodo di tempo per mantenere il backup dei database dell'istanza, configurabile da 7 a 35 giorni. Per informazioni [dettagliate,](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)vedere Backup automatici .

## <a name="track-billing"></a>Tenere traccia della fatturazione

**Esiste un modo per tenere traccia dei costi di fatturazione per l'istanza gestita?**

È possibile farlo usando la soluzione di gestione dei costi di [Azure.](/azure/cost-management/) Passare a **Sottoscrizioni** nel portale di [Azure](https://portal.azure.com) e selezionare **Analisi costi**. 

Utilizzare l'opzione **Costi accumulati** e `microsoft.sql/managedinstances`quindi filtrare in base al tipo di **risorsa** come . 
  
## <a name="inbound-nsg-rules"></a>Regole del gruppo di sicurezza di base in ingresso

**Come è possibile impostare le regole del gruppo di sicurezza di rete in ingresso per le porte di gestione?**

Il piano di controllo dell'istanza gestita gestisce le regole del gruppo di sicurezza di sicurezza di sicurezza di sicurezza di sicurezza di sicurezza di sicurezza di sicurezza di sicurezza che proteggono le porte di gestione.

Ecco a cosa servono le porte di gestione:

Le porte 9000 e 9003 vengono utilizzate dall'infrastruttura di Service Fabric. Il ruolo primario di Service Fabric consiste nel mantenere integro il cluster virtuale e mantenere lo stato dell'obiettivo in termini di numero di repliche dei componenti.

Le porte 1438, 1440 e 1452 vengono utilizzate dall'agente di nodo. L'agente di nodo è un'applicazione che viene eseguita all'interno del cluster e viene utilizzata dal piano di controllo per eseguire i comandi di gestione.

Oltre alle regole del gruppo di sicurezza di rete, il firewall incorporato protegge l'istanza a livello di rete. A livello di applicazione la comunicazione è protetta con i certificati.
  
Per altre informazioni e su come verificare il firewall incorporato, vedere [Firewall incorporato dell'istanza gestita del database SQL](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)di Azure.For more information and how to verify the built-in firewall, see Azure SQL Database managed instance built-in firewall .


## <a name="mitigate-data-exfiltration-risks"></a>Mitigare i rischi di esfiltrazione dei dati  

**Come posso mitigare i rischi di esfiltrazione dei dati?**

Per ridurre i rischi di esfiltrazione dei dati, si consiglia ai clienti di applicare una serie di impostazioni e controlli di sicurezza:

- Attivare [Transparent Data Encryption (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) in tutti i database.
- Disattivare Common Language Runtime (CLR). Questa è consigliata anche in locale.
- Usare solo l'autenticazione di Azure Active Directory (AAD).
- Accedere all'istanza con un account DBA con privilegi limitati.
- Configurare l'accesso JiT jumpbox per l'account sysadmin.
- Attivare il [controllo SQL](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)e integrarlo con meccanismi di avviso.
- Attivare il [rilevamento delle minacce](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) dalla suite [ADS (Advanced Data Security).](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)


## <a name="cost-saving-use-cases"></a>Casi d'uso per risparmiare sui costi

**Dove posso trovare i casi d'uso e i conseguenti risparmi sui costi con l'istanza gestita?**

Case study dell'istanza gestita:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [Kmd](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Script Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)  
Per comprendere meglio i vantaggi, i costi e i rischi associati alla distribuzione dell'istanza gestita del database SQL di Azure, è disponibile anche uno studio di Forrester: [Impatto economico totale di MI](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="dns-refresh"></a>Aggiornamento DNS 

**È possibile eseguire l'aggiornamento DNS?**

Attualmente, non è disponibile una funzionalità per aggiornare la configurazione del server DNS per l'istanza gestita.

La configurazione DNS viene infine aggiornata:

- Alla scadenza del lease DHCP.
- In base all'aggiornamento della piattaforma.

Come soluzione alternativa, eseguire il downgrade dell'istanza gestita a 4 vCore e aggiornarla di nuovo in un secondo momento. Ciò ha un effetto collaterale di aggiornare la configurazione DNS.


## <a name="ip-address"></a>Indirizzo IP

**È possibile connettersi all'istanza gestita utilizzando l'indirizzo IP?**

La connessione all'istanza gestita tramite indirizzo IP non è supportata. Il nome host dell'istanza gestita esegue il mapping al servizio di bilanciamento del carico davanti al cluster virtuale dell'istanza gestita. Poiché un cluster virtuale può ospitare più istanze gestite, la connessione non può essere instradata all'istanza gestita corretta senza specificarne il nome.

Per ulteriori informazioni sull'architettura del cluster virtuale dell'istanza gestita, vedere Architettura della [connettività del cluster virtuale](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture).

**Un'istanza gestita può avere un indirizzo IP statico?**

In situazioni rare ma necessarie, potrebbe essere necessario eseguire una migrazione online di un'istanza gestita in un nuovo cluster virtuale. Se necessario, questa migrazione è a causa di cambiamenti nel nostro stack di tecnologia volto a migliorare la sicurezza e l'affidabilità del servizio. La migrazione a un nuovo cluster virtuale comporta la modifica dell'indirizzo IP mappato al nome host dell'istanza gestita. Il servizio dell'istanza gestita non richiede il supporto degli indirizzi IP statici e si riserva il diritto di modificarlo senza preavviso come parte dei cicli di manutenzione regolari.

Per questo motivo, si sconsiglia vivamente di basarsi sull'immutabilità dell'indirizzo IP in quanto potrebbe causare tempi di inattività non necessari.

## <a name="change-time-zone"></a>Modificare il fuso orario

**È possibile modificare il fuso orario per un'istanza gestita esistente?**

La configurazione del fuso orario può essere impostata quando viene eseguito il provisioning di un'istanza gestita per la prima volta. La modifica del fuso orario dell'istanza gestita esistente non è supportata. Per informazioni [dettagliate, vedere Limitazioni relative ai fuso orario](sql-database-managed-instance-timezone.md#limitations).

Le soluzioni alternative includono la creazione di una nuova istanza gestita con il fuso orario appropriato e quindi l'esecuzione di un backup e ripristino manuale oppure di quanto consigliato, eseguire un [ripristino temporizzato tra istanze.](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)


## <a name="resolve-performance-issues"></a>Risolvere i problemi di prestazioni

**Come si risolvono i problemi di prestazioni con l'istanza gestita?**

Per un confronto delle prestazioni tra l'istanza gestita e SQL Server, un buon punto di partenza è [le procedure consigliate per il confronto delle prestazioni tra l'istanza gestita SQL](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210) di Azure e l'articolo di SQL Server.For a performance comparison between managed instance and SQL Server, a good starting point is Best practices for performance comparison between Azure SQL managed instance and SQL Server article.

Il caricamento dei dati è spesso più lento nell'istanza gestita rispetto a SQL ServerSQL Server a causa del modello di recupero con registrazione completa obbligatorio e dei [limiti](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) relativi alla velocità effettiva di scrittura del log delle transazioni. In alcuni stati, questo problema può essere risolto caricando i dati temporanei in tempdb anziché nel database utente o utilizzando columnstore cluster o tabelle ottimizzate per la memoria.


## <a name="restore-encrypted-backup"></a>Ripristinare il backup crittografato

**È possibile ripristinare il database crittografato nell'istanza gestita?**

Sì, non è necessario decrittografare il database per poterlo ripristinare nell'istanza gestita. È necessario fornire un certificato/chiave utilizzato come protezione della chiave di crittografia nel sistema di origine all'istanza gestita per poter leggere i dati dal file di backup crittografato. È possibile procedere in due modi:

- *Caricare la protezione certificati nell'istanza gestita.* Può essere eseguita solo con PowerShell.It can be done using PowerShell only. Lo [script di esempio](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) descrive l'intero processo.
- Caricare una protezione a *chiave asimmetrica in Azure Key Vault (AKV) e indirizzarvi l'istanza gestita.* Questo approccio è simile al caso di utilizzo di TDE bring-your-own-key (BYOK) che utilizza anche l'integrazione AKV per archiviare la chiave di crittografia. Se non si desidera utilizzare la chiave come protezione della chiave di crittografia e si desidera solo rendere disponibile la chiave per l'istanza gestita per ripristinare i database crittografati, seguire le istruzioni per [l'impostazione di BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)e non selezionare la casella di controllo *Imposta la chiave selezionata come protezione TDE predefinita*.

Una volta reso disponibile il protettore di crittografia per l'istanza gestita, è possibile procedere con la procedura standard di ripristino del database.

## <a name="migrate-from-single-db"></a>Eseguire la migrazione da un singolo databaseMigrate from single DB 

**Come è possibile eseguire la migrazione dal pool singolo o elastico del database SQL di Azure all'istanza gestita?**

L'istanza gestita offre gli stessi livelli di prestazioni per ogni dimensione di calcolo e archiviazione delle altre opzioni di distribuzione del database SQL di Azure.Managed instance offers the same performance levels per compute and storage size as other deployment options of Azure SQL Database. Se si desidera consolidare i dati in una singola istanza o semplicemente è necessaria una funzionalità supportata esclusivamente nell'istanza gestita, è possibile eseguire la migrazione dei dati utilizzando la funzionalità di esportazione/importazione (BACPAC).
