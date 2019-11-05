---
title: Domande frequenti sull'istanza gestita di database SQL | Microsoft Docs
description: Domande frequenti sull'istanza gestita di database SQL
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 7e341cf8a4ff2a18e44e36d73ad5dbc642582802
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496292"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>Domande frequenti sull'istanza gestita di database SQL

Questo articolo contiene molte delle domande più comuni sull' [istanza gestita di database SQL](sql-database-managed-instance.md).

## <a name="where-can-i-find-a-list-of-features-supported-on-managed-instance"></a>Dove è possibile trovare un elenco delle funzionalità supportate nell'istanza gestita?

Per un elenco delle funzionalità supportate in istanza gestita, vedere [database SQL di Azure rispetto a SQL Server](sql-database-features.md).

Per le differenze di sintassi e comportamento tra istanza gestita di database SQL di Azure e SQL Server locali, vedere [differenze di T-SQL da SQL Server](sql-database-managed-instance-transact-sql-information.md).


## <a name="where-can-i-find-technical-characteristics-and-resource-limits-for-managed-instance"></a>Dove è possibile trovare le caratteristiche tecniche e I limiti delle risorse per l'istanza gestita?

Per le caratteristiche di generazione hardware disponibili, vedere [differenze tecniche nelle generazioni hardware](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
Per i livelli di servizio disponibili e le relative caratteristiche, vedere [differenze tecniche tra i livelli di servizio](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="where-can-i-find-known-issues-and-bugs"></a>Dove è possibile trovare I problemi noti e I bug?

Per i bug e i problemi noti, vedere [problemi noti](sql-database-managed-instance-transact-sql-information.md#Issues).

## <a name="where-can-i-find-latest-features-and-the-features-in-public-preview"></a>Dove è possibile trovare le funzionalità più recenti e le funzionalità disponibili in anteprima pubblica?

Per le funzionalità nuove e in anteprima, vedere [Note sulla versione](/azure/sql-database/sql-database-release-notes?tabs=managed-instance).

## <a name="how-much-time-takes-to-create-or-update-instance-or-to-restore-a-database"></a>Quanto tempo è necessario per creare o aggiornare un'istanza o per ripristinare un database?

Il tempo previsto per la creazione di una nuova istanza gestita o la modifica del livello di servizio (VCore, archiviazione) dipendono da diversi fattori. Esaminare le [operazioni di gestione](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 

## <a name="can-a-managed-instance-have-the-same-name-as-on-premises-sql-server"></a>Un'istanza gestita può avere lo stesso nome di SQL Server locali?

L'istanza gestita deve avere un nome che termina con *database.Windows.NET*. Per usare un'altra zona DNS anziché quella predefinita, ad esempio, **mi-Other-Name**. contoso.com: 
- Usare CliConfig per definire un alias. Lo strumento è solo un wrapper di impostazioni del registro di sistema, pertanto può essere eseguito anche tramite criteri di gruppo o script.
- Usare l'opzione *CNAME* con *TrustServerCertificate = true* .

## <a name="how-can-i-move-database-from-managed-instance-back-to-sql-server-or-azure-sql-database"></a>Come è possibile spostare il database da un'istanza gestita a SQL Server o a un database SQL di Azure?

È possibile [esportare il database in BacPac](sql-database-export.md) e quindi [importare il file BACPAC]( sql-database-import.md). Si tratta di un approccio consigliato se il database è inferiore a 100 GB.

La replica transazionale può essere utilizzata se tutte le tabelle del database includono chiavi primarie.

Non è possibile ripristinare i backup nativi di `COPY_ONLY` eseguiti dall'istanza gestita per SQL Server perché l'istanza gestita ha una versione del database superiore rispetto a SQL Server.

## <a name="how-can-i-migrate-my-instance-database-to-a-single-azure-sql-database"></a>Come è possibile eseguire la migrazione del database dell'istanza a un singolo database SQL di Azure?

Un'opzione consiste nell' [esportare il database in un BacPac](sql-database-export.md) e quindi [importare il file BACPAC](sql-database-import.md). 

Si tratta dell'approccio consigliato se il database è inferiore a 100 GB. La replica transazionale può essere utilizzata se tutte le tabelle del database includono chiavi primarie.

## <a name="how-do-i-choose-between-gen-4-and-gen-5-hardware-generation-for-managed-instance"></a>Ricerca per categorie scegliere tra la generazione di hardware gen 4 e gen 5 per istanza gestita?

Dipende dal carico di lavoro, perché una generazione hardware è migliore per determinati tipi di carichi di lavoro. Anche se l'oggetto delle prestazioni è piuttosto complesso da semplificare, le differenze seguenti tra le generazioni hardware influiscono sulle prestazioni del carico di lavoro:
- Generazione 4 offre un supporto di calcolo migliore in quanto si basa su processori fisici, rispetto alla generazione 5 basata sui processori vCore. Potrebbe essere più vantaggioso per i carichi di lavoro a elevato utilizzo di calcolo.
- Generazione 5 supporta la rete accelerata con una maggiore larghezza di banda di i/o per l'archiviazione remota. Potrebbe essere vantaggioso per i carichi di lavoro con utilizzo intensivo di i/o nei livelli di servizio per utilizzo generico. Generazione 5 usa dischi locali SSD più veloci rispetto alla generazione 4. Potrebbe essere vantaggioso per i carichi di lavoro con i/o intensivi nei livelli di servizio business-critical.

È consigliabile testare le prestazioni dei carichi di lavoro effettivi destinati alla produzione prima di iniziare a determinare quale generazione hardware funzionerà meglio in un caso specifico.

## <a name="can-i-switch-my-managed-instance-hardware-generation-between-gen-4-and-gen-5-online"></a>È possibile cambiare la generazione di hardware dell'istanza gestita tra gen 4 e gen 5 online? 

Il cambio automatico online tra le generazioni hardware è possibile se entrambe le generazioni hardware sono disponibili nell'area in cui viene effettuato il provisioning dell'istanza gestita. In questo caso, è possibile usare [lo script del post di Blog](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Change-hardware-generation-on-Managed-Instance/ba-p/699824) che spiega come passare da una generazione all'altra.

Si tratta di un'operazione a esecuzione prolungata perché verrà effettuato il provisioning di una nuova istanza gestita in background e i database trasferiti automaticamente tra la vecchia e la nuova istanza con un failover rapido alla fine del processo. 

Se entrambe le generazioni hardware non sono supportate nella stessa area, la modifica della generazione hardware è possibile, ma deve essere eseguita manualmente. A tale scopo, è necessario effettuare il provisioning di una nuova istanza nell'area in cui è disponibile la generazione hardware desiderata e eseguire manualmente il backup e il ripristino dei dati tra la vecchia e la nuova istanza.


## <a name="how-do-i-tune-performance-of-my-managed-instance"></a>Ricerca per categorie ottimizzare le prestazioni dell'istanza gestita? 

Per utilizzo generico istanza gestita utilizza l'archiviazione remota a causa delle dimensioni dei file di dati e di log importanti per le prestazioni. Per ulteriori informazioni, vedere [conseguenze delle dimensioni del file di registro in per utilizzo generico istanza gestita prestazioni](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

Per i carichi di lavoro con utilizzo intensivo i/o si consiglia di usare l'hardware di generazione 5, rispetto all'uso della generazione 4 per carichi di lavoro Per ulteriori informazioni, vedere [ricerca per categorie scegliere tra gen 4 e gen 5](#how-do-i-choose-between-gen-4-and-gen-5-hardware-generation-for-managed-instance).

Se il carico di lavoro è costituito da numerose transazioni di piccole dimensioni, provare a cambiare il tipo di connessione dal proxy alla modalità di reindirizzamento.

## <a name="what-is-the-maximum-storage-size-for-managed-instance"></a>Quali sono le dimensioni massime di archiviazione per l'istanza gestita? 

Le dimensioni di archiviazione per l'istanza gestita dipendono dal livello di servizio selezionato (per utilizzo generico o business critical). Per le limitazioni di archiviazione di questi livelli di servizio, vedere [caratteristica del livello di servizio](sql-database-service-tiers-general-purpose-business-critical.md).

## <a name="is-the-backup-storage-deducted-from-my-managed-instance-storage"></a>L'archivio di backup è stato sottratto dalla risorsa di archiviazione delle istanze gestite? 

No, l'archiviazione di backup non viene dedotta dallo spazio di archiviazione dell'istanza gestita. L'archivio di backup è indipendente dallo spazio di archiviazione dell'istanza e non ha dimensioni limitate. L'archiviazione di backup è limitata al periodo di tempo per cui conservare il backup dei database dell'istanza, configurabile da 7 a 35 giorni. Per informazioni dettagliate, vedere [backup automatici](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
  
## <a name="how-can-i-set-inbound-nsg-rules-on-management-ports"></a>Come è possibile impostare le regole di NSG in ingresso sulle porte di gestione?

La funzionalità firewall incorporata configura Windows Firewall in tutte le macchine virtuali del cluster per consentire le connessioni in ingresso dagli intervalli IP associati solo ai computer di gestione/distribuzione Microsoft e alle workstation di amministrazione sicure che impediscono efficacemente intrusioni attraverso il livello di rete.

Di seguito sono riportate le porte utilizzate per:

Le porte 9000 e 9003 vengono usate dall'infrastruttura Service Fabric. Service Fabric ruolo primario consiste nel lasciare integro il cluster virtuale mantenendo lo stato degli obiettivi in termini di numero di repliche di componenti.

Le porte 1438, 1440 e 1452 vengono utilizzate dall'agente del nodo. Node Agent è un'applicazione che viene eseguita all'interno del cluster e viene utilizzata dal piano di controllo per eseguire i comandi di gestione.

Oltre al firewall incorporato a livello di rete, la comunicazione è anche protetta con i certificati.
  
Per altre informazioni e per informazioni su come verificare il firewall incorporato, vedere [firewall predefinito istanza gestita di database SQL di Azure](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).


## <a name="how-can-i-mitigate-networking-risks"></a>Come è possibile attenuare I rischi di rete? 

Per attenuare i rischi di rete, è consigliabile applicare un set di impostazioni e controlli di sicurezza:

- Attivare Transparent Data Encryption (Transparent Data [Encryption)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) in tutti i database.
- Disattivare Common Language Runtime (CLR). Questa operazione è consigliata anche in locale.
- Usare solo l'autenticazione Azure Active Directory (AAD).
- Istanza di accesso con account DBA con privilegi limitati.
- Configurare l'accesso JumpBox JiT per l'account sysadmin.
- Attivare il [controllo SQL](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)e integrarlo con i meccanismi di avviso.
- Attivare il [rilevamento delle minacce](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) dalla Suite [Advanced Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) .


## <a name="where-can-i-find-use-cases-and-resulting-cost-savings-with-managed-instance"></a>Dove è possibile trovare I casi d'uso e I costi risultanti per l'istanza gestita?

Case study sull'istanza gestita:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
-   [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure) 
Per comprendere meglio i vantaggi, i costi e i rischi associati alla distribuzione dell'istanza gestita di database SQL di Azure, è disponibile anche uno studio di Forrester: [Total Economic Impact of mi](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="can-i-do-dns-refresh"></a>È possibile eseguire l'aggiornamento DNS? 
  
Attualmente non è disponibile una funzionalità per aggiornare la configurazione del server DNS per l'istanza gestita.

Alla fine, la configurazione DNS verrà aggiornata:

- Quando il lease DHCP scade.
- Aggiornamento della piattaforma.

Come soluzione alternativa, eseguire il downgrade dell'istanza gestita a 4 vCore e aggiornarla di nuovo in seguito. Questa operazione ha effetto collaterale sull'aggiornamento della configurazione DNS.


## <a name="can-a-managed-instance-have-a-static-ip-address"></a>Un'istanza gestita può avere un indirizzo IP statico?

In casi rari ma necessari, potrebbe essere necessario eseguire una migrazione in linea di un'istanza gestita a un nuovo cluster virtuale. Se necessario, questa migrazione è dovuta a modifiche nello stack di tecnologie mirate a migliorare la sicurezza e l'affidabilità del servizio. La migrazione a un nuovo cluster virtuale comporta la modifica dell'indirizzo IP di cui è stato eseguito il mapping al nome host dell'istanza gestita. Il servizio istanza gestita non richiede il supporto di indirizzi IP statici e si riserva il diritto di modificarlo senza preavviso nell'ambito dei normali cicli di manutenzione.

Per questo motivo, è fortemente sconsigliabile basarsi sull'immutabilità dell'indirizzo IP perché potrebbe causare tempi di inattività superflui.

## <a name="can-i-move-a-managed-instance-or-its-vnet-to-another-resource-group"></a>È possibile spostare un'istanza gestita o la relativa VNet in un altro gruppo di risorse?

No, si tratta di una limitazione della piattaforma corrente. Dopo la creazione di un'istanza gestita, lo stato di trasferimento dell'istanza gestita o VNet a un altro gruppo di risorse o a una sottoscrizione non è supportato.

## <a name="can-i-change-the-time-zone-for-an-existing-managed-instance"></a>È possibile modificare il fuso orario per un'istanza gestita esistente?

La configurazione del fuso orario può essere impostata quando viene eseguito il provisioning di un'istanza gestita per la prima volta. La modifica del fuso orario dell'istanza gestita esistente non è supportata. Per informazioni dettagliate, vedere [limitazioni del fuso orario](sql-database-managed-instance-timezone.md#limitations).

Le soluzioni alternative includono la creazione di una nuova istanza gestita con il fuso orario appropriato, quindi l'esecuzione di un backup manuale e il ripristino o quello che si consiglia di eseguire un [ripristino temporizzato tra istanze](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="how-do-i-resolve-performance-issues-with-my-managed-instance"></a>Ricerca per categorie risolvere i problemi relativi alle prestazioni con l'istanza gestita

Per un confronto delle prestazioni tra istanza gestita e SQL Server, un punto di partenza valido è la procedura consigliata [per il confronto delle prestazioni tra l'istanza gestita di SQL di Azure e SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210) articolo.

Il caricamento dei dati è spesso più lento nell'istanza gestita rispetto a SQL Server a causa del modello di recupero con registrazione completa obbligatoria e dei [limiti](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) sulla velocità effettiva di scrittura del log delle transazioni. A volte è possibile aggirare il problema caricando i dati temporanei in tempdb anziché nel database utente oppure usando columnstore cluster o tabelle ottimizzate per la memoria.


## <a name="can-i-restore-my-encrypted-database-to-managed-instance"></a>È possibile ripristinare il database crittografato nell'istanza gestita?

Sì, non è necessario decrittografare il database per poterlo ripristinare in un'istanza gestita. Per poter leggere i dati dal file di backup crittografato, è necessario fornire un certificato o una chiave utilizzata come protezione con chiave di crittografia nel sistema di origine all'istanza gestita. È possibile procedere in due modi:

- *Caricare il certificato-Protector nell'istanza gestita*. Questa operazione può essere eseguita solo tramite PowerShell. Lo [script di esempio](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) descrive l'intero processo.
- *Caricare la protezione con chiave asimmetrica per Azure Key Vault (AKV) e l'istanza gestita del punto*. Questo approccio è simile al caso di utilizzo di Transparent Data Encryption (BYOK) che usa anche l'integrazione AKV per archiviare la chiave di crittografia. Se non si vuole usare la chiave come protezione con chiave di crittografia e si vuole solo rendere disponibile la chiave per l'istanza gestita per ripristinare i database crittografati, seguire le istruzioni per la [configurazione di BYOK](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)Transparent Data Encryption e non selezionare la casella di controllo imposta *la chiave selezionata protezione*Transparent Data Encryption.

Dopo aver reso disponibile la protezione della crittografia per l'istanza gestita, è possibile procedere con la procedura di ripristino del database standard.

## <a name="how-can-i-migrate-from-azure-sql-database-single-or-elastic-pool-to-managed-instance"></a>Come è possibile eseguire la migrazione dal pool singolo o elastico del database SQL di Azure all'istanza gestita? 

Istanza gestita offre gli stessi livelli di prestazioni per calcolo e dimensioni di archiviazione delle altre opzioni di distribuzione del database SQL di Azure. Se si desidera consolidare i dati in una singola istanza oppure è sufficiente una funzionalità supportata esclusivamente in istanza gestita, è possibile eseguire la migrazione dei dati tramite la funzionalità di esportazione/importazione (BACPAC).
