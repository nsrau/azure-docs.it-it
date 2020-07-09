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
ms.openlocfilehash: 88f92117dc07fc241ca714851956e386cd10d617
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135019"
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

  
## <a name="networking-requirements"></a>Requisiti di rete 

**Quali sono i vincoli Current NSG in ingresso/in uscita sulla subnet Istanza gestita?**

Le regole NSG e UDR obbligatorie sono documentate [qui](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)e impostate automaticamente dal servizio.
Si tenga presente che queste regole sono solo quelle necessarie per gestire il servizio. Per connettersi all'istanza gestita e utilizzare funzionalità diverse, è necessario impostare regole aggiuntive specifiche per le funzionalità, che è necessario gestire.

**Come è possibile impostare le regole di NSG in ingresso sulle porte di gestione?**

SQL Istanza gestita è responsabile dell'impostazione delle regole sulle porte di gestione. Questa operazione viene eseguita tramite la funzionalità denominata [configurazione della subnet assistita dal servizio](connectivity-architecture-overview.md#service-aided-subnet-configuration).
Ciò consente di garantire un flusso ininterrotto del traffico di gestione per soddisfare un contratto di contratto.

**È possibile ottenere gli intervalli IP di origine usati per il traffico di gestione in ingresso?**

Sì. È possibile analizzare il traffico proveniente dal gruppo di sicurezza di rete [configurando Network Watcher log dei flussi](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#analyze-traffic-to-or-from-a-network-security-group).

**È possibile impostare NSG per controllare l'accesso all'endpoint dati (porta 1433)?**

Sì. Dopo il provisioning di un Istanza gestita è possibile impostare NSG che controlla l'accesso in ingresso alla porta 1433. È consigliabile limitare il più possibile l'intervallo di indirizzi IP.

**È possibile impostare l'appliance virtuale di dispositivo o il firewall locale per filtrare il traffico di gestione in uscita in base ai nomi di dominio completi?**

No. Questa operazione non è supportata per diversi motivi:
-   Il routing del traffico che rappresenta la risposta alla richiesta di gestione in ingresso è asimmetrico e non può funzionare.
-   Il routing del traffico che passa all'archiviazione potrebbe essere influenzato da vincoli di velocità effettiva e latenza, in modo che non sia possibile fornire la qualità e la disponibilità dei servizi previsti.
-   In base all'esperienza, queste configurazioni sono soggette a errori e non sono supportate.

**È possibile impostare l'appliance virtuale di sistema o il firewall per il traffico non di gestione in uscita?**

Sì. Il modo più semplice per ottenere questo risultato consiste nell'aggiungere la regola 0/0 a un UDR associato alla subnet dell'istanza gestita per instradare il traffico attraverso l'appliance virtuale di dispositivo.
 
**Quanti indirizzi IP sono necessari per una Istanza gestita?**

La subnet deve avere un numero sufficiente di [indirizzi IP](connectivity-architecture-overview.md#network-requirements)disponibili. Per determinare le dimensioni della subnet VNet per SQL Istanza gestita, vedere [determinare le dimensioni e l'intervallo di subnet richiesti per istanza gestita](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-determine-size-vnet-subnet). 

**Cosa accade se non sono disponibili indirizzi IP sufficienti per eseguire l'operazione di aggiornamento dell'istanza?**

Se nella subnet non è disponibile un numero sufficiente di [indirizzi IP](connectivity-architecture-overview.md#network-requirements) in cui viene effettuato il provisioning dell'istanza gestita, sarà necessario creare una nuova subnet e una nuova istanza gestita al suo interno. Si consiglia inoltre di creare la nuova subnet con più indirizzi IP allocati, in modo che le operazioni di aggiornamento future eviteranno situazioni simili. Al termine del provisioning della nuova istanza, è possibile eseguire manualmente il backup e il ripristino dei dati tra le istanze precedenti e nuove o eseguire il [ripristino temporizzato](point-in-time-restore.md?tabs=azure-powershell)tra istanze.

**È necessaria una subnet vuota per creare un Istanza gestita?**

No. È possibile usare una subnet vuota o una subnet che contiene già Istanza gestita. 

**È possibile modificare l'intervallo di indirizzi della subnet?**

Non se sono presenti istanze gestite all'interno di. Si tratta di una limitazione dell'infrastruttura di rete di Azure. È possibile [aggiungere ulteriore spazio degli indirizzi a una subnet vuota](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet#change-subnet-settings). 

**È possibile spostare l'istanza gestita in un'altra subnet?**

No. Si tratta di una limitazione di progettazione Istanza gestita corrente. Tuttavia, è possibile effettuare il provisioning di una nuova istanza di in un'altra subnet e eseguire manualmente il backup e il ripristino dei dati tra la vecchia e la nuova istanza o eseguire il [ripristino temporizzato](point-in-time-restore.md?tabs=azure-powershell)tra istanze.

**È necessaria una rete virtuale vuota per creare un Istanza gestita?**

Questa operazione non è obbligatoria. È possibile [creare una rete virtuale per istanza gestita SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-vnet-subnet) o [configurare una rete virtuale esistente per istanza gestita SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vnet-subnet).

**È possibile inserire un Istanza gestita con altri servizi in una subnet?**

No. Attualmente non è supportata l'immissione di Istanza gestita in una subnet che contiene già altri tipi di risorse.

## <a name="connectivity"></a>Connettività 

**È possibile connettersi all'istanza gestita usando l'indirizzo IP?**

No, questa operazione non è supportata. Il nome host di un Istanza gestita viene mappato al servizio di bilanciamento del carico davanti al cluster virtuale del Istanza gestita. Poiché un cluster virtuale può ospitare più istanze gestite, non è possibile indirizzare una connessione al Istanza gestita appropriato senza specificarne il nome.
Per ulteriori informazioni sull'architettura di cluster virtuali SQL Istanza gestita, vedere [architettura della connettività del cluster virtuale](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**Un'istanza gestita può avere un indirizzo IP statico?**

Non supportato attualmente.

In casi rari ma necessari, potrebbe essere necessario eseguire una migrazione in linea di un'istanza gestita a un nuovo cluster virtuale. Se necessario, questa migrazione è dovuta a modifiche nello stack di tecnologie mirate a migliorare la sicurezza e l'affidabilità del servizio. La migrazione a un nuovo cluster virtuale comporta la modifica dell'indirizzo IP di cui è stato eseguito il mapping al nome host dell'istanza gestita. Il servizio istanza gestita non richiede il supporto di indirizzi IP statici e si riserva il diritto di modificarlo senza preavviso nell'ambito dei normali cicli di manutenzione.

Per questo motivo, è fortemente sconsigliabile basarsi sull'immutabilità dell'indirizzo IP perché potrebbe causare tempi di inattività superflui.

**Istanza gestita dispone di un endpoint pubblico?**

Sì. Istanza gestita dispone di un endpoint pubblico che viene usato per impostazione predefinita solo per la gestione dei servizi, ma un cliente può abilitarlo anche per l'accesso ai dati. Per informazioni dettagliate, vedere [usare istanza gestita SQL con endpoint pubblici](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-securely). Per configurare l'endpoint pubblico, passare a [Configura endpoint pubblico in SQL istanza gestita](public-endpoint-configure.md).

**In che modo Istanza gestita controlla l'accesso all'endpoint pubblico?**

Istanza gestita controlla l'accesso all'endpoint pubblico a livello di rete e di applicazione.

I servizi di gestione e distribuzione si connettono a un'istanza gestita usando un [endpoint di gestione](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-connectivity-architecture#management-endpoint) mappato a un servizio di bilanciamento del carico esterno. Il traffico viene indirizzato ai nodi solo se viene ricevuto su un set di porte predefinite usate esclusivamente dai componenti di gestione dell'istanza gestita. Per consentire il traffico solo da intervalli IP specifici di Microsoft, viene inoltre configurato un firewall incorporato nei nodi. Tutte le comunicazioni tra i componenti di gestione e il piano di gestione vengono autenticate reciprocamente tramite i certificati. Per ulteriori informazioni, vedere [architettura di connettività per SQL istanza gestita](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-connectivity-architecture#virtual-cluster-connectivity-architecture).

**È possibile usare l'endpoint pubblico per accedere ai dati in Istanza gestita database?**

Sì. Il cliente dovrà abilitare l'accesso ai dati dell'endpoint pubblico dal [portale di Azure](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal)  /  [PowerShell](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-using-powershell) /ARM e configurare NSG per bloccare l'accesso alla porta dati (numero di porta 3342). Per altre informazioni, vedere [configurare un endpoint pubblico in Azure sql istanza gestita](public-endpoint-configure.md) e [usare Azure SQL istanza gestita in modo sicuro con l'endpoint pubblico](public-endpoint-overview.md). 

**È possibile specificare una porta personalizzata per gli endpoint dati SQL?**

No, questa opzione non è disponibile.  Per l'endpoint di dati privati, Istanza gestita usa il numero di porta predefinito 1433 e per l'endpoint di dati pubblici Istanza gestita usa il numero di porta predefinito 3342.

**Qual è il modo consigliato per connettere le istanze gestite posizionate in aree diverse?**

Il peering del circuito Express route è il modo migliore per eseguire questa operazione. Questo non deve essere combinato con il peering di rete virtuale tra aree non supportato a causa di [vincoli](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)interni correlati al servizio di bilanciamento del carico.

Se il peering del circuito Express route non è possibile, l'unica altra opzione consiste nel creare una connessione VPN da sito a sito ([portale di Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal), [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell), l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli)).


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


## <a name="dns"></a>DNS

**È possibile configurare un DNS personalizzato per SQL Istanza gestita?**

Sì. Vedere [come configurare un DNS personalizzato per istanza gestita SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).

**È possibile eseguire l'aggiornamento DNS?**

Attualmente non è disponibile una funzionalità per aggiornare la configurazione del server DNS per SQL Istanza gestita.

Alla fine, la configurazione DNS verrà aggiornata:

- Quando il lease DHCP scade.
- Aggiornamento della piattaforma.

Per risolvere il problema, eseguire il downgrade di SQL Istanza gestita a 4 Vcore e aggiornarlo di nuovo in seguito. Questa operazione ha effetto collaterale sull'aggiornamento della configurazione DNS.


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

## <a name="purchasing-models-and-benefits"></a>Acquisto di modelli e vantaggi

**Quali modelli di acquisto sono disponibili per Istanza gestita SQL?**

SQL Istanza gestita offre un [modello di acquisto basato su vCore](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model).

**Quali vantaggi in termini di costi sono disponibili per Istanza gestita SQL?**

È possibile risparmiare sui costi con i vantaggi SQL di Azure nei modi seguenti:
-   Ottimizza gli investimenti esistenti in licenze locali e Risparmia fino al 55% con [vantaggio Azure Hybrid](https://docs.microsoft.com/azure/azure-sql/azure-hybrid-benefit?tabs=azure-powershell). 
-   Esegui il commit di una prenotazione per le risorse di calcolo e Risparmia fino al 33% con il [vantaggio dell'istanza riservata](https://docs.microsoft.com/azure/sql-database/sql-database-reserved-capacity). Combina questo vantaggio con il vantaggio Azure Hybrid per risparmiare fino al 82%. 
-   Risparmia fino al 55% rispetto ai prezzi di listino con il [vantaggio prezzi di sviluppo/test di Azure](https://azure.microsoft.com/pricing/dev-test/) che offre tariffe scontate per i tuoi carichi di lavoro di sviluppo e test in corso.

**Chi è idoneo per il vantaggio dell'istanza riservata?**

Per essere idoneo per il vantaggio dell'istanza riservata, il tipo di sottoscrizione deve essere un contratto Enterprise Agreement (numeri di offerta: MS-AZR-0017P o MS-AZR-0148P) o un contratto singolo con prezzi con pagamento in base al consumo (numeri di offerta: MS-AZR-0003P o MS-AZR-0023P). Per altre informazioni sulle prenotazioni, vedere [vantaggio dell'istanza riservata](https://docs.microsoft.com/azure/sql-database/sql-database-reserved-capacity). 

**È possibile annullare, scambiare o rimborsare le prenotazioni?**

È possibile annullare, scambiare o rimborsare prenotazioni con determinate limitazioni. Per altre informazioni, vedere [Scambi e rimborsi self-service per le prenotazioni di Azure](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations).

## <a name="billing-for-managed-instance-and-backup-storage"></a>Fatturazione per la Istanza gestita e l'archiviazione di backup

**Quali sono le opzioni di prezzo di SQL Istanza gestita?**

Per esplorare Istanza gestita opzioni di prezzo, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

**Come è possibile tenere traccia dei costi di fatturazione per l'istanza gestita?**

Questa operazione può essere eseguita usando la [soluzione Gestione costi di Azure](https://docs.microsoft.com/azure/cost-management-billing/). Passare a **sottoscrizioni** nella [portale di Azure](https://portal.azure.com) e selezionare **analisi dei costi**. 

Usare l'opzione **costi accumulati** , quindi filtrare in base al **tipo di risorsa** `microsoft.sql/managedinstances` .

**Qual è il costo dei backup automatici?**

Si ottiene la stessa quantità di spazio di archiviazione di backup libero come lo spazio di archiviazione dei dati riservato acquistato, indipendentemente dal periodo di conservazione dei backup impostato. Se il consumo di archiviazione di backup rientra nello spazio di archiviazione di backup libero allocato, i backup automatici sull'istanza gestita non avranno alcun costo aggiuntivo, pertanto saranno gratuiti. Il superamento dell'utilizzo dell'archiviazione di backup al di sopra dello spazio disponibile comporta costi pari a circa $0,20-$0,24 per GB/mese nelle aree degli Stati Uniti o per informazioni dettagliate sull'area, vedere la pagina relativa ai prezzi. Per informazioni dettagliate, vedere [utilizzo dell'archiviazione di backup illustrato](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923).

**Come è possibile monitorare I costi di fatturazione per il consumo di risorse di archiviazione di backup?**

È possibile monitorare i costi per l'archiviazione di backup tramite il portale di Azure. Per istruzioni, vedere [monitorare i costi per i backup automatici](https://docs.microsoft.com/azure/azure-sql/database/automated-backups-overview?tabs=managed-instance#monitor-costs). 

**Come è possibile ottimizzare i costi di archiviazione di backup nell'istanza gestita?**

Per ottimizzare i costi di archiviazione di backup, vedere [ottimizzazione del backup in SQL istanza gestita](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935).

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

## <a name="azure-feedback-and-support"></a>Feedback e supporto di Azure

**Dove è possibile lasciare le idee per i miglioramenti apportati a SQL Istanza gestita?**

Puoi votare per una nuova funzionalità di Istanza gestita o inserire una nuova idea di miglioramento per il voto sul [Forum dei commenti e suggerimenti su SQL istanza gestita](https://feedback.azure.com/forums/915676-sql-managed-instance). In questo modo è possibile contribuire allo sviluppo del prodotto e contribuire a classificare in ordine di priorità i potenziali miglioramenti.

**Come è possibile creare una richiesta di supporto di Azure?**

Per informazioni su come creare una richiesta di supporto di Azure, vedere [come creare una richiesta di supporto di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

