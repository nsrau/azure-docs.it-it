---
title: Domande frequenti su istanza gestita di Database SQL | Microsoft Docs
description: Istanza gestita di Database SQL di domande frequenti (FAQ)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 07/08/2019
ms.openlocfilehash: c3a070eb7e1435055b47b39985cf8cb0b182a514
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798073"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>Istanza gestita di Database SQL di domande frequenti (FAQ)

Questo articolo contiene molte delle domande più comuni sulle [istanza gestita di Database SQL](sql-database-managed-instance.md).

## <a name="where-can-i-find-a-list-of-features-that-are-supported-on-managed-instance"></a>Dove trovare un elenco delle funzionalità supportate in istanza gestita?

Per un elenco delle funzionalità supportate in istanza gestita, vedere [Database SQL di Azure e SQL Server](sql-database-features.md).

Le differenze nella sintassi e il comportamento tra istanza gestita di Database di Azure SQL e SQL Server in locale, vedere [differenze di T-SQL da SQL Server](sql-database-managed-instance-transact-sql-information.md).


## <a name="where-can-i-find-technical-characteristics-and-resource-limits-for-managed-instance"></a>Dove posso trovare caratteristiche tecniche e i limiti delle risorse per l'istanza gestita?

Per le caratteristiche di generazione di hardware disponibili, vedere [differenze tecniche in generazioni di hardware](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
Per i livelli di servizio disponibile e le relative caratteristiche, vedere [differenze tecniche tra i livelli di servizio](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="where-can-i-find-known-issues-and-bugs"></a>Dove posso trovare i problemi noti e bug?

Per i bug e problemi noti, vedere [modifiche del comportamento](sql-database-managed-instance-transact-sql-information.md#Changes) e [problemi noti](sql-database-managed-instance-transact-sql-information.md#Issues).


## <a name="can-a-managed-instance-have-the-same-name-as-on-premises-sql-server"></a>Un'istanza gestita può avere lo stesso nome di Server SQL in locale?

Istanza gestita deve avere un nome che termina con *database.windows.net*. Per usare un'altra zona DNS anziché il valore predefinito, ad esempio, **istanza gestita a un altro nome**. contoso.com: 
- Usare CliConfig per definire un alias (lo strumento è solo un wrapper di impostazioni del Registro di sistema, in modo che questa operazione può anche essere eseguita tramite criteri di gruppo o script).
- Uso *CNAME* con *TrustServerCertificate = true* opzione.


## <a name="how-can-i-move-database-from-managed-instance-back-to-sql-server-or-azure-sql-database"></a>Come è possibile spostare database dall'istanza gestita di tornare a SQL Server o Database SQL di Azure?

È possibile [Esporta database in file bacpac](sql-database-export.md) e quindi [importare il file bacpac]( sql-database-import.md). Questo approccio è consigliato se il database è inferiore a 100 GB.

Replica transazionale può essere usata se tutte le tabelle del database dispongono di chiavi primarie.

Nativo `COPY_ONLY` i backup eseguiti da istanza gestita non è possibile ripristinare in SQL Server perché istanza gestita ha una versione del database superiore rispetto a SQL Server.

## <a name="how-can-i-migrate-my-instance-database-to-a-single-azure-sql-database"></a>Come è possibile migrare il database dell'istanza di un singolo Database SQL di Azure?

È possibile [esportare il database in un file bacpac](sql-database-export.md) e quindi [importare il file bacpac]( sql-database-import.md). 

Questo è l'approccio consigliato se il database è inferiore a 100 GB. Replica transazionale può essere usata se tutte le tabelle del database dispongono di chiavi primarie.

## <a name="how-do-i-choose-between-gen-4-and-gen-5-hardware-generation-for-managed-instance"></a>Come scegliere tra generazione 4 e generazione di hardware di generazione 5 per l'istanza gestita?

Ciò varia a seconda del carico di lavoro come alcune generazione di hardware è migliore per determinati tipi di carichi di lavoro di altro. Anche se l'oggetto delle prestazioni è piuttosto complessi per semplificare, quanto segue le differenze tra generazioni di hardware impatto sulle prestazioni del carico di lavoro:
- Generazione 4 fornisce un supporto migliore di calcolo che si basa su processori fisici, e generazione 5 basato su vCore processori. Ciò potrebbe essere più vantaggioso per carichi di lavoro con utilizzo intensivo di calcolo.
- Supporta la generazione 5 accelerated networking generando una migliore larghezza di banda dei / o in un archivio remoto. Ciò potrebbe essere vantaggioso per carichi di lavoro con utilizzo intensivo dei / o nei livelli di servizio utilizzo generico. Generazione 5 utilizza dischi locali più veloci unità SSD rispetto alla generazione 4. Ciò potrebbe essere vantaggioso per carichi di lavoro con utilizzo intensivo dei / o nei livelli di servizio critici di business.

I clienti sono invitati a testare le prestazioni dei carichi di lavoro effettivi destinati alla produzione prima di essere pubblicati per determinare quale componente hardware generazione funzionerà meglio nel caso specifico.

## <a name="can-i-switch-my-managed-instance-hardware-generation-between-gen-4-and-gen-5-online"></a>È possibile passare l'istanza gestita hardware generazione tra generazione 4 e generazione 5 online? 

Il passaggio online automatizzati tra generazioni di hardware è possibile se entrambe le generazioni di hardware sono disponibili nella stessa area in cui viene eseguito il provisioning di istanza gestita. In questo caso, è possibile nella sezione dei prezzi a livelli nel portale di Azure per passare tra generazioni di hardware.

Si tratta di una lunga esecuzione operazione come la nuova istanza gestita viene eseguito il provisioning nel server di back-end e database automaticamente trasferite tra l'istanza di vecchio e nuovo. Questo processo è trasparente per i clienti.

Se entrambe le generazioni di hardware non sono supportate nella stessa area, modificare la generazione di hardware è possibile ma deve essere eseguito manualmente. Ciò richiede di effettuare il provisioning di una nuova istanza della regione in cui è disponibile, la generazione di hardware desiderate e backup manuale e il ripristino dei dati tra l'istanza di vecchio e nuovo.


## <a name="how-do-i-tune-performance-of-my-managed-instance"></a>Come è possibile ottimizzare le prestazioni della mia istanza gestita? 

Istanza gestita generico Usa l'archiviazione remota a causa della quale le dimensioni del file di log e dati importanti per le prestazioni. Per ottimizzare le prestazioni del livello servizio generico, seguire le istruzioni nel post di blog.

Per i carichi di lavoro con utilizzo intensivo dei / o è consigliabile usare hardware di generazione 5, invece di generazione 4 per carichi di lavoro con utilizzo intensivo di calcolo. Per altre informazioni, vedere la sezione Domande frequenti sulla scelta tra generazioni di hardware.

Se il carico di lavoro è costituito da un numero elevato di transazioni di dimensioni ridotte, valutare se passare il tipo di connessione dal proxy per la modalità di reindirizzamento.

## <a name="what-is-the-maximum-storage-size-for-managed-instance"></a>Qual è la dimensione massima di memoria per l'istanza gestita? 

Dimensioni di archiviazione per l'istanza gestita varia il livello di servizio selezionati (utilizzo generico o Business Critical). Per le limitazioni di archiviazione di questi livelli di servizio, vedere [caratteristica di livello servizio](sql-database-service-tiers-general-purpose-business-critical.md).

## <a name="is-the-backup-storage-deducted-from-my-managed-instance-storage"></a>L'archiviazione dei backup verrà dedotti dal mio archiviazione a istanza gestita? 

No, archivio di backup non viene dedotto dallo spazio di archiviazione di istanza gestita. L'archiviazione dei backup è indipendente dallo spazio di archiviazione dell'istanza e non esistono limitato di dimensioni. Archiviazione dei backup è limitata dal tempo conservare il backup dei database, istanza può essere configurati da 7 a 35 giorni. Per informazioni dettagliate, vedere [backup automatici](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
  
## <a name="how-can-i-set-inbound-nsg-rules-on-management-ports"></a>Come è possibile impostare le regole di sicurezza di rete in entrata sulle porte di gestione?

La funzionalità incorporata firewall configura Windows firewall su tutte le macchine virtuali del cluster per consentire connessioni in ingresso da intervalli di indirizzi IP associati solo a Microsoft management/distribuzione le macchine virtuali e impedendo così che secure admin workstation intrusioni attraverso il livello di rete.

Ecco quali porte vengono usate per:

Le porte 9000 e 9003 vengono utilizzate dall'infrastruttura di Service Fabric. Ruolo primario di Service Fabric consiste nel mantenere integro il cluster virtuale e di mantenere lo stato di obiettivo in termini di numero di repliche di componente.

Porte 1438 1440 e 1452 vengono usate dall'agente del nodo. Agente del nodo è un'applicazione che viene eseguito all'interno del cluster e viene usata per il piano di controllo per eseguire i comandi di gestione.

Oltre il firewall incorporato nel livello di rete, comunicazione è protetto anche con i certificati.
  
Per altre informazioni e come verificare il firewall incorporato, vedere [Database SQL di Azure gestiti firewall incorporato istanza](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).


## <a name="how-can-i-mitigate-networking-risks"></a>In che modo è possibile ridurre i rischi di rete? 

Per attenuare i rischi di rete, i clienti sono consigliati per applicare un set di impostazioni di sicurezza e controlli:

- Attivare tutti i database su Transparent Data Encryption (TDE).
- Turn off Common Language Runtime (CLR). Questo è consigliato in locale anche.
- Usare solo account di Azure AD.
- Accesso istanza Gestita di SQL con account di amministratore di database con privilegi limitati.
- Configurare l'accesso jumpbox JiT per account sysadmin.
- Attivare il controllo SQL e integrarla con meccanismi di avviso.
- Attivare il rilevamento delle minacce da suite ATS.


## <a name="where-can-i-find-use-cases-and-resulting-cost-savings-with-managed-instance"></a>Dove posso trovare casi d'uso e risparmi sui costi risultante con istanza gestita?

Istanza gestita case study:

- [Komatsu](http://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [powerdetails](http://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](http://customers.microsoft.com/story/allscripts-partner-professional-services-azure)   
Per ottenere una migliore comprensione dei vantaggi, costi e rischi associati alla distribuzione di istanza gestita di Database SQL di Azure, è inoltre disponibile uno studio di Forrester: [Total Economic Impact of MI](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="can-i-do-dns-refresh"></a>È possibile eseguire l'aggiornamento DNS? 
  
Attualmente, non forniamo una funzionalità per aggiornare la configurazione del server DNS per l'istanza gestita.

Configurazione DNS viene aggiornata alla fine:

- Scadenza lease DHCP.
- In seguito all'aggiornamento della piattaforma.

In alternativa, effettua il downgrade l'istanza gestita a 4 vCore e aggiornarla nuovamente in un secondo momento. Ciò ha un effetto collaterale di aggiornamento della configurazione DNS.


## <a name="can-a-managed-instance-have-a-static-ip-address"></a>Un'istanza gestita può avere un indirizzo IP statico?

In casi rari ma necessarie, potrebbe essere necessario eseguire una migrazione online di un'istanza gestita a un nuovo cluster virtuale. Se necessario, questa migrazione è a causa delle modifiche nel nostro stack di tecnologie volte a migliorare la sicurezza e affidabilità del servizio. La migrazione a un nuovo cluster virtuale come risultato la modifica dell'indirizzo IP che viene eseguito il mapping per il nome host dell'istanza gestita. Il servizio istanza gestita non richiesta il supporto di indirizzi IP statico e si riserva il diritto di modificarla senza preavviso come parte di cicli di manutenzione regolare.

Per questo motivo, è fortemente sconsigliato basarsi sulla non modificabilità dell'indirizzo IP, poiché potrebbe causare tempi di inattività non necessari.


## <a name="can-i-change-the-time-zone-for-an-existing-managed-instance"></a>È possibile modificare il fuso orario per un'istanza gestita esistente?

Configurazione del fuso orario può essere impostato quando viene eseguito il provisioning di un'istanza gestita per la prima volta. Modifica del fuso orario dell'istanza gestita esistente non è supportata. Per informazioni dettagliate, vedere [fuso orario limitazioni](sql-database-managed-instance-timezone.md#limitations).

Le soluzioni alternative includono la creazione di una nuova istanza gestita con il fuso orario corretti e quindi eseguire un backup manuale e ripristino, o ciò che è consigliabile eseguire un' [restore point-in-time tra istanze](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="how-do-i-resolve-performance-issues-with-my-managed-instance"></a>Come si risolvono i problemi di prestazioni con lo stato dell'istanza gestita

Per un confronto delle prestazioni tra istanza gestita e SQL Server, è un buon punto di partenza [procedure consigliate per il confronto delle prestazioni tra istanza gestita SQL di Azure e SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).

Caricamento dei dati è spesso più lento in istanza gestita di SQL Server a causa di un modello di recupero con registrazione completa obbligatorio e [limiti](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) sulla velocità effettiva di scrittura del log delle transazioni. In alcuni casi, ciò può essere evitato tramite columnstore cluster o le tabelle ottimizzate per la memoria o il caricamento di dati temporanei in tempdb invece del database utente.


## <a name="can-i-restore-my-encrypted-database-to-managed-instance"></a>È possibile ripristinare il database crittografato in istanza gestita?

Sì, non è necessario decrittografare il database per poter ripristinare il server a istanza gestita. È necessario fornire una certificato/chiave usata come una protezione con chiave di crittografia nel sistema di origine all'istanza gestita per essere in grado di leggere i dati dal file di backup crittografato. Esistono due modi possibili per eseguire questa operazione:

- Carica certificato di protezione per l'istanza gestita. Questa operazione può essere eseguita solo con PowerShell. Lo script di esempio descrive l'intero processo.
- Caricare protezione con chiave asimmetrica per Azure Key Vault (AKV) e scegliere istanza gestita. Questo approccio è simile al caso d'uso di bring-your-own-key (BYOK) TDE che usa anche l'integrazione di AKV per archiviare la chiave di crittografia. Se si desidera semplicemente la chiave caricata in Azure Key Vault disponibili per istanza gestita per il ripristino dei database crittografati senza effettivamente usando la chiave come una protezione con chiave di crittografia, seguire le istruzioni per configurare TDE BYOK e non verificare la chiave selezionata la casella di controllo di protezione TDE predefinita.

Dopo aver effettuato la crittografia del programma di protezione disponibili per istanza gestita, è possibile procedere con la procedura di ripristino di database standard.

## <a name="how-can-i-migrate-from-azure-sql-database-single-or-elastic-pool-to-managed-instance"></a>Come è possibile migrare dal pool di Database SQL di Azure singolo o elastico in istanza gestita? 

Gestito istanza offre gli stessi livelli di prestazioni per ogni dimensione di calcolo e archiviazione come altre opzioni di distribuzione del Database SQL di Azure. Se si desidera consolidare i dati in una singola istanza o è sufficiente creare una funzionalità supportata esclusivamente in istanza gestita, è possibile migrare i dati utilizzando la funzionalità importazione/esportazione (BACPAC).
