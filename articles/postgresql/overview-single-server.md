---
title: Server singolo di Database di Azure per PostgreSQL
description: Offre una panoramica del server singolo di Database di Azure per PostgreSQL.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: 469235957ebe26dd44cc6ce464a68167629099ab
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944648"
---
# <a name="azure-database-for-postgresql-single-server"></a>Server singolo di Database di Azure per PostgreSQL

[Database di Azure per PostgreSQL](./overview.md) basato su PostgreSQL Community Edition è disponibile in tre modalità di distribuzione:

- Server unico
- Server flessibile (anteprima)
- Hyperscale (Citus)

In questo articolo verranno fornite una panoramica e un'introduzione ai concetti di base del modello di distribuzione server singolo. Per informazioni sulla modalità di distribuzione server flessibile, vedere rispettivamente la [panoramica del server flessibile](./flexible-server/overview.md) e la panoramica di Hyperscale (Citus).

## <a name="overview"></a>Panoramica

Server singolo è un servizio di database completamente gestito con requisiti minimi per le personalizzazioni del database. La piattaforma server singolo è progettata per gestire la maggior parte delle funzioni di gestione di database, ad esempio l'applicazione di patch, i backup, la disponibilità elevata e la sicurezza con configurazione e controllo minimi dell'utente. L'architettura è ottimizzata per offrire una disponibilità del 99,99% in una singola zona di disponibilità. Supporta attualmente la versione community di PostgreSQL 9.5, 9.6, 10 e 11. Il servizio è attualmente disponibile a livello generale in un'ampia gamma di [aree di Azure](https://azure.microsoft.com/global-infrastructure/services/).

I server singoli sono ideali per applicazioni native per il cloud progettate per gestire l'applicazione automatizzata di patch senza la necessità di un controllo granulare sulla pianificazione dell'applicazione di patch e sulle impostazioni di configurazione di PostgreSQL personalizzate.

## <a name="high-availability"></a>Disponibilità elevata

Il modello di distribuzione server singolo è ottimizzato per disponibilità elevata predefinita ed elasticità a costi ridotti. L'architettura separa il calcolo e l'archiviazione. Il motore di database viene eseguito in un contenitore di calcolo proprietario, mentre i file di dati risiedono in Archiviazione di Azure. L'archiviazione gestisce localmente tre copie sincrone con ridondanza dei file di database garantendo la durabilità dei dati.

Durante gli eventi di failover pianificati o non pianificati, se il server diventa inattivo, il servizio mantiene la disponibilità elevata dei server usando la procedura automatizzata seguente:

1. Viene eseguito il provisioning di un nuovo contenitore di calcolo.
2. Viene eseguito il mapping tra la risorsa di archiviazione con file di dati e il nuovo contenitore.
3. Il motore di database PostgreSQL viene portato online nel nuovo contenitore di calcolo.
4. Il servizio Gateway garantisce un failover trasparente che non richiede modifiche sul lato applicazione.
   
 :::image type="content" source="./media/overview/overview-azure-postgres-single-server.png" alt-text="Database di Azure per PostgreSQL - Server singolo":::

Il tempo di failover tipico è compreso tra 60 e 120 secondi. La progettazione nativa per il cloud del servizio server singolo consente di supportare il 99,99% della disponibilità eliminando il costo di hot standby passivo.

Il Contratto di servizio per la disponibilità del 99,99% leader del settore di Azure, fornito da una rete globale di datacenter gestiti da Microsoft, consente di mantenere le applicazioni in esecuzione 24 ore su 24, 7 giorni su 7.

## <a name="automated-patching"></a>Applicazione automatica delle patch

Il servizio esegue l'applicazione automatica di patch dell'hardware, del sistema operativo e del motore di database sottostanti. L'applicazione di patch include aggiornamenti di sicurezza e software. Per il motore PostgreSQL gli aggiornamenti della versione secondaria sono automatici e sono inclusi come parte del ciclo di applicazione delle patch. Non è richiesto alcun intervento da parte dell'utente o alcuna impostazione di configurazione per l'applicazione di patch. La frequenza dell'applicazione delle patch è gestita dal servizio in base alla criticità del payload. Il servizio segue in genere la pianificazione di rilascio mensile come parte del processo continuo di integrazione e rilascio. Gli utenti possono sottoscrivere la [notifica per la manutenzione pianificata]() per ricevere una notifica relativa alla manutenzione imminente 72 ore prima dell'evento.

## <a name="automatic-backups"></a>Backup automatici

Il servizio server singolo crea automaticamente backup del server e li archivia nell'archiviazione con ridondanza locale o geografica configurata dall'utente. I backup possono essere usati per ripristinare il server in modo temporizzato entro il periodo di conservazione. Il periodo di conservazione dei backup predefinito è di sette giorni. La conservazione può essere configurata facoltativamente fino a 35 giorni. Tutti i backup vengono crittografati con crittografia AES a 256 bit. Per informazioni dettagliate, vedere [Backup](./concepts-backup.md).

## <a name="adjust-performance-and-scale-within-seconds"></a>Regolare prestazioni e scalabilità in pochi secondi

Il servizio server singolo è disponibile in tre livelli di SKU: piani Basic, Per utilizzo generico e Con ottimizzazione per la memoria. Il livello Basic è ideale per lo sviluppo a basso costo e per carichi di lavoro di concorrenza ridotti. Per utilizzo generico e Con ottimizzazione per la memoria sono più adatti per i carichi di lavoro di produzione che richiedono concorrenza elevata, scalabilità e prevedibilità delle prestazioni. È possibile compilare la prima app in un database di piccole dimensioni con un costo mensile minimo, quindi regolare la scalabilità in base alle esigenze della soluzione. Il ridimensionamento dello spazio di archiviazione è online e supporta l'aumento automatico dello spazio di archiviazione. La scalabilità dinamica consente al database di rispondere in modo trasparente ai requisiti delle risorse soggetti a rapidi cambiamenti. Verranno addebitati i costi solo per le risorse usate. Per informazioni dettagliate, vedere [Piani tariffari]().

## <a name="enterprise-grade-security-compliance-and-governance"></a>Sicurezza, conformità e governance di livello aziendale

Il servizio server singolo usa il modulo crittografico convalidato FIPS 140-2 per la crittografia di archiviazione dei dati inattivi. I dati, inclusi i backup e i file temporanei creati durante l'esecuzione di query, vengono crittografati. Il servizio usa la crittografia AES a 256 bit inclusa nella crittografia di archiviazione di Azure e le chiavi possono essere gestite dal sistema (predefinito) o dal [cliente](). Il servizio crittografa i dati in transito con il protocollo Transport Layer Security (SSL/TLS) applicato per impostazione predefinita. Il servizio supporta le versioni 1.2, 1.1 e 1.0 di TLS con la possibilità di applicare la [versione TLS minima]().

Il servizio consente l'accesso privato ai server tramite collegamento privato e fornisce la funzionalità Advanced Threat Protection. Advanced Threat Protection rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

Oltre all'autenticazione nativa, il servizio server singolo supporta l'autenticazione Azure Active Directory. L'autenticazione Azure AD è un meccanismo di connessione ai server PostgreSQL tramite identità definite e gestite in Azure AD. Con l'autenticazione Azure AD è possibile centralizzare la gestione delle identità degli utenti del database e di altri servizi Azure semplificando e centralizzando il controllo di accesso.

La [registrazione di controllo]() (in anteprima) è disponibile per tenere traccia di tutte le attività a livello di database.

Il servizio server singolo è conforme a tutte le certificazioni leader del settore, ad esempio FedRAMP, HIPAA e PCI DSS. Visitare il [Centro protezione di Azure]() per informazioni sulla sicurezza della piattaforma Azure.

Per altre informazioni sulle funzionalità di sicurezza di Database di Azure per PostgreSQL, vedere la [panoramica sulla sicurezza]().

## <a name="monitoring-and-alerting"></a>Monitoraggio e avviso

Il servizio server singolo è dotato di funzionalità predefinite di monitoraggio delle prestazioni e di avviso. Tutte le metriche di Azure hanno una frequenza di un minuto e offrono una cronologia di 30 giorni. È possibile configurare avvisi in base alle metriche. Il servizio consente la configurazione di log di query lente ed è dotato di una funzionalità [Archivio query](./concepts-query-store.md) differenziata. Query Store semplifica la risoluzione dei problemi di prestazioni consentendo di trovare rapidamente le query con il tempo di esecuzione più lungo e il più elevato utilizzo di risorse. Grazie a questi strumenti, è possibile ottimizzare rapidamente i carichi di lavoro e configurare il server per ottenere prestazioni ottimali. Per informazioni dettagliate, vedere [Monitoraggio](./concepts-monitoring.md).

## <a name="migration"></a>Migrazione

Il servizio esegue la versione community di PostgreSQL. Questo consente la compatibilità completa delle applicazioni e richiede un costo di refactoring minimo per eseguire la migrazione di un'applicazione esistente sviluppata sul motore PostgreSQL a un servizio server singolo. Per eseguire la migrazione al server singolo, è possibile usare una delle opzioni seguenti:

- **Dump e ripristino**: per le migrazioni offline, in cui gli utenti possono permettersi un certo tempo di inattività, l'esecuzione del dump e del ripristino usando strumenti della community come Pg_dump e Pg_restore può fornire una soluzione più rapida per la migrazione. Per i dettagli, vedere [Eseguire la migrazione con dump e ripristino](https://docs.microsoft.com/azure/postgresql/howto-migrate-using-dump-and-restore).
- **Servizio Migrazione del database di Azure**: per migrazioni semplici e facilitate verso server singoli con tempi di inattività minimi, è possibile sfruttare Servizio Migrazione del database di Azure. Vedere [Servizio Migrazione del database tramite portale](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online-portal) e [Servizio Migrazione del database tramite interfaccia della riga di comando](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).

## <a name="contacts"></a>Contatti

Per eventuali domande o suggerimenti sull'uso di Database di Azure per PostgreSQL, inviare un messaggio di posta elettronica al team di Database di Azure per PostgreSQL ([@Ask Azure DB per PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Questo indirizzo di posta elettronica non è un alias del supporto tecnico.

Inoltre, prendere in considerazione i seguenti punti di contatto in base alle specifiche esigenze:

- Per contattare il supporto di Azure, [creare un ticket dal portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Per risolvere un problema relativo all'account, inviare una [richiesta di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) nel portale di Azure.
- Per fornire commenti e suggerimenti o richiedere nuove funzionalità, creare una richiesta tramite [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver letto un'introduzione alla modalità di distribuzione server singolo di Database di Azure per PostgreSQL, si è pronti per:
- Creare il primo server.
  