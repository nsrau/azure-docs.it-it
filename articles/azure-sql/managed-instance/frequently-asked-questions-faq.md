---
title: Domande frequenti
titleSuffix: Azure SQL Managed Instance
description: Domande frequenti su Azure SQL Istanza gestita
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 9295c6e1daaad6346581b959a9b94a7ab74da44c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708859"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Domande frequenti su Azure SQL Istanza gestita
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Questo articolo contiene le domande più comuni su [Azure SQL istanza gestita](sql-managed-instance-paas-overview.md).

## <a name="supported-features"></a>Caratteristiche supportate

**Dove è possibile trovare un elenco delle funzionalità supportate in SQL Istanza gestita?**

Per un elenco delle funzionalità supportate in SQL Istanza gestita, vedere [funzionalità di istanza gestita SQL di Azure](../database/features-comparison.md).

Per le differenze di sintassi e comportamento tra Istanza gestita SQL di Azure e SQL Server, vedere [differenze di T-SQL da SQL Server](transact-sql-tsql-differences-sql-server.md).


## <a name="tech-spec--resource-limits"></a>Specifiche tecniche & limiti delle risorse
 
**Dove è possibile trovare le caratteristiche tecniche e I limiti delle risorse per Istanza gestita SQL?**

Per le caratteristiche di generazione hardware disponibili, vedere [differenze tecniche nelle generazioni hardware](resource-limits.md#hardware-generation-characteristics).
Per i livelli di servizio disponibili e le relative caratteristiche, vedere [differenze tecniche tra i livelli di servizio](resource-limits.md#service-tier-characteristics).

## <a name="known-issues--bugs"></a>Problemi noti & bug

**Dove è possibile trovare I problemi noti e I bug?**

Per i bug e i problemi noti, vedere [problemi noti](../database/doc-changes-updates-release-notes.md#known-issues).

## <a name="new-features"></a>Nuove funzionalità

**Dove è possibile trovare le funzionalità più recenti e le funzionalità disponibili in anteprima pubblica?**

Per le funzionalità nuove e in anteprima, vedere [Note sulla versione](../database/doc-changes-updates-release-notes.md?tabs=managed-instance).

## <a name="deployment-times"></a>Tempi di distribuzione 

**Quanto tempo è necessario per creare o aggiornare un'istanza o per ripristinare un database?**

Il tempo previsto per la creazione di un'istanza gestita o la modifica del livello di servizio (VCore, Storage) dipende da diversi fattori. Esaminare le [operazioni di gestione](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations). 

## <a name="naming-conventions"></a>Convenzioni di denominazione

**Un'istanza gestita può avere lo stesso nome di un'istanza di SQL Server locale?**

La modifica di un nome di istanza gestita non è supportata.

È possibile modificare la zona DNS predefinita *. database.Windows.NET* per un'istanza gestita. 

Per usare un'altra zona DNS anziché quella predefinita, ad esempio, *. contoso.com*: 
- Usare CliConfig per definire un alias. Lo strumento è semplicemente un wrapper di impostazioni del registro di sistema, pertanto può essere eseguito anche tramite criteri di gruppo o uno script.
- Usare *CNAME* con l'opzione *TrustServerCertificate = true* .

## <a name="move-a-database-from-sql-managed-instance"></a>Spostare un database da SQL Istanza gestita 

**Come è possibile spostare un database da SQL Istanza gestita alla SQL Server o al database SQL di Azure?**

È possibile [esportare un database in BacPac](../database/database-export.md) e quindi [importare il file BACPAC](../database/database-import.md). Si tratta dell'approccio consigliato se il database è inferiore a 100 GB.

La replica transazionale può essere utilizzata se tutte le tabelle del database includono chiavi primarie.

`COPY_ONLY`Non è possibile ripristinare i backup nativi eseguiti da sql istanza gestita per SQL Server perché SQL istanza gestita ha una versione del database superiore rispetto a SQL Server.

## <a name="migrate-an-instance-database"></a>Eseguire la migrazione di un database dell'istanza

**Come è possibile eseguire la migrazione del database dell'istanza al database SQL di Azure?**

Un'opzione consiste nell' [esportare il database in un BacPac](../database/database-export.md) e quindi [importare il file BACPAC](../database/database-import.md). 

Si tratta dell'approccio consigliato se il database è inferiore a 100 GB. La replica transazionale può essere utilizzata se tutte le tabelle del database includono chiavi primarie.

## <a name="switch-hardware-generation"></a>Cambia generazione hardware 

**È possibile cambiare la generazione di hardware Istanza gestita SQL tra gen 4 e gen 5 online?**

Il cambio automatico online tra le generazioni hardware è possibile se entrambe le generazioni hardware sono disponibili nell'area in cui viene effettuato il provisioning di SQL Istanza gestita. In questo caso, è possibile controllare la [pagina Panoramica del modello vCore](../database/service-tiers-vcore.md), che illustra come passare da una generazione all'altra.

Si tratta di un'operazione a esecuzione prolungata, in quanto verrà effettuato il provisioning di una nuova istanza gestita in background e i database trasferiti automaticamente tra le istanze precedenti e quelle nuove, con un failover rapido alla fine del processo. 

**Cosa accade se entrambe le generazioni hardware non sono supportate nella stessa area?**

Se entrambe le generazioni hardware non sono supportate nella stessa area, la modifica della generazione hardware è possibile, ma deve essere eseguita manualmente. A tale scopo, è necessario effettuare il provisioning di una nuova istanza nell'area in cui è disponibile la generazione hardware desiderata e eseguire manualmente il backup e il ripristino dei dati tra le istanze precedenti e nuove.

**Cosa accade se non sono disponibili indirizzi IP sufficienti per eseguire l'operazione di aggiornamento?**

Se nella subnet non è disponibile un numero sufficiente di indirizzi IP in cui viene effettuato il provisioning dell'istanza gestita, sarà necessario creare una nuova subnet e una nuova istanza gestita al suo interno. Si consiglia inoltre di creare la nuova subnet con più indirizzi IP allocati, in modo che le operazioni di aggiornamento future eviteranno situazioni simili. Per le dimensioni corrette della subnet, controllare [come determinare le dimensioni di una subnet VNet](vnet-subnet-determine-size.md). Al termine del provisioning della nuova istanza, è possibile eseguire manualmente il backup e il ripristino dei dati tra le istanze precedenti e nuove o eseguire il [ripristino temporizzato](point-in-time-restore.md?tabs=azure-powershell)tra istanze. 


## <a name="tune-performance"></a>Ottimizzare le prestazioni

**Ricerca per categorie ottimizzare le prestazioni di SQL Istanza gestita?**

Il Istanza gestita SQL nel livello per utilizzo generico usa l'archiviazione remota, quindi le dimensioni dei file di dati e di log sono importanti per le prestazioni. Per ulteriori informazioni, vedere [conseguenze delle dimensioni del file di registro in per utilizzo generico prestazioni di SQL istanza gestita](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

Se il carico di lavoro è costituito da numerose transazioni di piccole dimensioni, provare a cambiare il tipo di connessione dal proxy alla modalità di reindirizzamento.

## <a name="maximum-storage-size"></a>Dimensioni massime di archiviazione

**Quali sono le dimensioni massime di archiviazione per SQL Istanza gestita?**

Le dimensioni di archiviazione per SQL Istanza gestita variano a seconda del livello di servizio selezionato (per utilizzo generico o business critical). Per le limitazioni di archiviazione di questi livelli di servizio, vedere [caratteristiche del livello di servizio](../database/service-tiers-general-purpose-business-critical.md).

## <a name="backup-storage-cost"></a>Costo dell'archiviazione dei backup 

**L'archivio di backup è stato sottratto dalla risorsa di archiviazione di SQL Istanza gestita?**

No, l'archiviazione di backup non viene dedotta dallo spazio di archiviazione di SQL Istanza gestita. L'archivio di backup è indipendente dallo spazio di archiviazione dell'istanza e non ha dimensioni limitate. L'archiviazione di backup è limitata al periodo di tempo per cui conservare il backup dei database dell'istanza, configurabile da 7 a 35 giorni. Per informazioni dettagliate, vedere [backup automatici](../database/automated-backups-overview.md).

## <a name="track-billing"></a>Tenere traccia della fatturazione

**Esiste un modo per tenere traccia dei costi di fatturazione per SQL Istanza gestita?**

Questa operazione può essere eseguita usando la [soluzione Gestione costi di Azure](/azure/cost-management/). Passare a **sottoscrizioni** nella [portale di Azure](https://portal.azure.com) e selezionare **analisi dei costi**. 

Usare l'opzione **costi accumulati** , quindi filtrare in base al **tipo di risorsa** `microsoft.sql/managedinstances` . 
  
## <a name="inbound-nsg-rules"></a>Regole NSG in ingresso

**Come è possibile impostare le regole di NSG in ingresso sulle porte di gestione?**

Il piano di controllo di SQL Istanza gestita gestisce le regole NSG che proteggono le porte di gestione.

Di seguito sono riportate le porte di gestione utilizzate per:

Le porte 9000 e 9003 vengono usate dall'infrastruttura di Azure Service Fabric. Il Service Fabric ruolo primario consiste nel lasciare integro il cluster virtuale mantenendo lo stato obiettivo in termini di numero di repliche di componenti.

Le porte 1438, 1440 e 1452 vengono utilizzate dall'agente del nodo. Node Agent è un'applicazione che viene eseguita all'interno del cluster e viene utilizzata dal piano di controllo per eseguire i comandi di gestione.

Oltre alle regole NSG, il firewall incorporato protegge l'istanza a livello di rete. A livello di applicazione, la comunicazione è protetta con i certificati.

Per ulteriori informazioni e per informazioni su come verificare il firewall incorporato, vedere [Azure SQL istanza gestita firewall incorporato](management-endpoint-verify-built-in-firewall.md).


## <a name="mitigate-data-exfiltration-risks"></a>Attenuazione dei rischi exfiltration  

**Come è possibile attenuare i rischi exfiltration?**

Per attenuare i rischi exfiltration di dati, è consigliabile applicare un set di impostazioni e controlli di sicurezza:

- Attivare Transparent Data Encryption (Transparent Data [Encryption)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) in tutti i database.
- Disattivare Common Language Runtime (CLR). Questa operazione è consigliata anche in locale.
- Usare solo l'autenticazione Azure Active Directory (Azure AD).
- Accedere all'istanza con un account DBA con privilegi limitati.
- Configurare l'accesso JumpBox JIT per l'account sysadmin.
- Attivare il [controllo SQL](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)e integrarlo con i meccanismi di avviso.
- Attivare il [rilevamento delle minacce](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) dalla Suite [Advanced Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) .


## <a name="cost-saving-use-cases"></a>Casi d'uso di risparmio sui costi

**Dove è possibile trovare I casi d'uso e I costi derivanti con SQL Istanza gestita?**

Case Study di SQL Istanza gestita:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

Per comprendere meglio i vantaggi, i costi e i rischi associati alla distribuzione di Istanza gestita SQL di Azure, è disponibile anche uno studio Forrester: [l'effetto economico totale di database SQL di Microsoft Azure istanza gestita](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="dns-refresh"></a>Aggiornamento DNS 

**È possibile eseguire l'aggiornamento DNS?**

Attualmente non è disponibile una funzionalità per aggiornare la configurazione del server DNS per SQL Istanza gestita.

Alla fine, la configurazione DNS verrà aggiornata:

- Quando il lease DHCP scade.
- Aggiornamento della piattaforma.

Per risolvere il problema, eseguire il downgrade di SQL Istanza gestita a 4 Vcore e aggiornarlo di nuovo in seguito. Questa operazione ha effetto collaterale sull'aggiornamento della configurazione DNS.


## <a name="ip-address"></a>Indirizzo IP

**È possibile connettersi a SQL Istanza gestita usando un indirizzo IP?**

La connessione a SQL Istanza gestita tramite un indirizzo IP non è supportata. Il nome host di SQL Istanza gestita viene mappato a un servizio di bilanciamento del carico davanti al cluster virtuale di SQL Istanza gestita. Poiché un cluster virtuale può ospitare più istanze gestite, le connessioni non possono essere indirizzate all'istanza gestita corretta senza specificare il nome in modo esplicito.

Per ulteriori informazioni sull'architettura di cluster virtuali SQL Istanza gestita, vedere [architettura della connettività del cluster virtuale](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**È possibile che SQL Istanza gestita disponga di un indirizzo IP statico?**

In casi rari ma necessari, potrebbe essere necessario eseguire una migrazione in linea di SQL Istanza gestita a un nuovo cluster virtuale. Se necessario, questa migrazione è dovuta a modifiche nello stack di tecnologie mirate a migliorare la sicurezza e l'affidabilità del servizio. La migrazione a un nuovo cluster virtuale comporta la modifica dell'indirizzo IP di cui è stato eseguito il mapping al nome host di SQL Istanza gestita. Il servizio SQL Istanza gestita non richiede il supporto di indirizzi IP statici e si riserva il diritto di modificarlo senza preavviso nell'ambito dei normali cicli di manutenzione.

Per questo motivo, è fortemente sconsigliabile basarsi sull'immutabilità dell'indirizzo IP perché potrebbe causare tempi di inattività superflui.

## <a name="change-time-zone"></a>Modificare il fuso orario

**È possibile modificare il fuso orario per un'istanza gestita esistente?**

La configurazione del fuso orario può essere impostata quando viene eseguito il provisioning di un'istanza gestita per la prima volta. La modifica del fuso orario di un'istanza gestita esistente non è supportata. Per informazioni dettagliate, vedere [limitazioni del fuso orario](timezones-overview.md#limitations).

Le soluzioni alternative includono la creazione di una nuova istanza gestita con il fuso orario appropriato e quindi l'esecuzione di un backup manuale e il ripristino o quello che si consiglia di eseguire un [ripristino temporizzato tra istanze](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="resolve-performance-issues"></a>Risoluzione dei problemi di prestazioni

**Ricerca per categorie risolvere i problemi relativi alle prestazioni con SQL Istanza gestita?**

Per un confronto delle prestazioni tra SQL Istanza gestita e SQL Server, un punto di partenza ottimale è la procedura consigliata [per il confronto delle prestazioni tra istanza gestita SQL di Azure e SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).

Il caricamento dei dati è spesso più lento in SQL Istanza gestita rispetto a SQL Server a causa del modello di recupero con registrazione completa obbligatoria e dei [limiti](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) sulla velocità effettiva di scrittura del log delle transazioni. A volte è possibile aggirare il problema caricando i dati temporanei in tempdb anziché nel database utente oppure usando columnstore cluster o tabelle ottimizzate per la memoria.


## <a name="restore-encrypted-backup"></a>Ripristino del backup crittografato

**È possibile ripristinare il database crittografato in SQL Istanza gestita?**

Sì, non è necessario decrittografare il database per ripristinarlo in SQL Istanza gestita. Per poter leggere i dati dal file di backup crittografato, è necessario fornire un certificato o una chiave usati come protezione con chiave di crittografia nel sistema di origine a SQL Istanza gestita. È possibile procedere in due modi:

- *Caricare la protezione da certificato a SQL istanza gestita*. Questa operazione può essere eseguita solo tramite PowerShell. Lo [script di esempio](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) descrive l'intero processo.
- *Caricare la protezione con chiave asimmetrica per Azure Key Vault e puntare SQL istanza gestita*. Questo approccio è simile al caso di utilizzo di Transparent Data Encryption (BYOK) che usa anche l'integrazione Key Vault per archiviare la chiave di crittografia. Se non si vuole usare la chiave come protezione con chiave di crittografia e si vuole solo rendere disponibile la chiave per SQL Istanza gestita per ripristinare i database crittografati, seguire le istruzioni per la [configurazione di BYOK](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)Transparent Data Encryption e non selezionare la casella di controllo imposta la **chiave selezionata come protezione**Transparent Data Encryption.

Dopo aver reso disponibile la protezione della crittografia per SQL Istanza gestita, è possibile procedere con la procedura di ripristino del database standard.

## <a name="migrate-from-sql-database"></a>Eseguire la migrazione dal database SQL 

**Come è possibile eseguire la migrazione dal database SQL di Azure a SQL Istanza gestita?**

SQL Istanza gestita offre gli stessi livelli di prestazioni per calcolo e dimensioni di archiviazione del database SQL di Azure. Se si desidera consolidare i dati in una singola istanza oppure è sufficiente una funzionalità supportata esclusivamente in SQL Istanza gestita, è possibile eseguire la migrazione dei dati tramite la funzionalità di esportazione/importazione (BACPAC).

## <a name="password-policy"></a>Criteri password 

**Quali criteri password vengono applicati per gli account di accesso SQL Istanza gestita SQL?**

I criteri password SQL Istanza gestita per gli account di accesso SQL ereditano i criteri della piattaforma Azure applicati alle macchine virtuali che formano il cluster virtuale che contiene l'istanza gestita. Al momento non è possibile modificare nessuna di queste impostazioni poiché queste impostazioni sono definite da Azure e ereditate dall'istanza gestita.

 > [!IMPORTANT]
 > La piattaforma Azure può modificare i requisiti dei criteri senza inviare notifiche ai servizi che si basano su tali criteri.

**Che cosa sono i criteri correnti della piattaforma Azure?**

Ogni account di accesso deve impostare la relativa password al momento dell'accesso e modificare la password dopo che ha raggiunto la validità massima.

| **Criteri** | **Impostazione di sicurezza** |
| --- | --- |
| Validità massima password | 42 giorni |
| Validità minima password | 1 giorno |
| Lunghezza minima password | 10 caratteri |
| Le password devono essere conformi ai requisiti di complessità | Abilitato |

**È possibile disabilitare la complessità e la scadenza delle password in SQL Istanza gestita al livello di accesso?**

Sì, è possibile controllare CHECK_POLICY e CHECK_EXPIRATION campi a livello di accesso. È possibile controllare le impostazioni correnti eseguendo il comando T-SQL seguente:

```sql
SELECT *
FROM sys.sql_logins
```

Successivamente, è possibile modificare le impostazioni di accesso specificate eseguendo:

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

(sostituire ' test ' con il nome di accesso desiderato e modificare i criteri e i valori di scadenza)
