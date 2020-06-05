---
title: Archiviare, gestire e rendere persistenti i dati delle applicazioni nel cloud con Visual Studio App Center e i servizi di Azure
description: Informazioni sui servizi, come Visual Studio App Center, che consentono di archiviare, gestire e rendere persistenti i dati delle applicazioni per dispositivi mobili nel cloud.
author: codemillmatt
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: cb76151a3f952f932cc533b771283b5d45382366
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873457"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Archiviare, sincronizzare ed eseguire query sui dati delle applicazioni per dispositivi mobili dal cloud
Indipendentemente dal tipo di applicazione che si compila, è probabile che vengano generati ed elaborati dati. Gli utenti delle applicazioni hanno aspettative elevate. Vogliono che le applicazioni siano veloci e che funzionino senza interruzioni, in tutte le circostanze. La maggior parte delle applicazioni funziona anche in più dispositivi. È possibile accedervi da un dispositivo desktop o mobile. Più utenti possono usare le applicazioni contemporaneamente e condividere i dati con l'aspettativa di accedervi istantaneamente e in tempo reale.

Gli utenti delle applicazioni non sempre hanno connettività Internet. Le applicazioni vengono progettate e dovrebbero funzionare con o senza connessione Internet. Gli sviluppatori devono scegliere la soluzione più adatta per l'archiviazione e la sincronizzazione dei dati nel cloud in modo da offrire un'esperienza ottimale agli utenti delle loro applicazioni.

Microsoft offre un'ampia varietà di servizi che eliminano la necessità di attivare server, selezionare il database o preoccuparsi di aspetti come la scalabilità e la sicurezza per offrire un'esperienza più completa possibile. Questi servizi assicurano un'esperienza di sviluppo ottimale che consente di archiviare i dati delle applicazioni nel cloud tramite API SQL o NoSQL. È anche possibile sincronizzare i dati in tutti i dispositivi e consentire il funzionamento delle applicazioni con o senza connessione Internet per garantire scalabilità e affidabilità.

Usare i servizi seguenti per gestire e archiviare i dati delle applicazioni per dispositivi mobili nel cloud.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) è un servizio di database multimodello e distribuito a livello globale. È possibile usare questo servizio per creare un'applicazione su scala planetaria. Azure Cosmos DB garantisce la scalabilità elastica e indipendente della velocità effettiva e dello spazio di archiviazione tra un numero qualsiasi di aree di Azure a livello mondiale. È possibile sfruttare i vantaggi di un accesso ai dati rapido, nell'ordine dei millisecondi a una cifra, usando le superfici delle API preferite. Queste superfici includono SQL, MongoDB, Cassandra, Tabella o Gremlin. Azure Cosmos DB fornisce in esclusiva contratti di servizio completi per velocità effettiva, latenza, disponibilità e coerenza.

**Funzionalità principali**
- Supporto di un'ampia gamma di API, tra cui SQL (Core), Cassandra, MongoDB, Gremlin e Tabella.
- Distribuzione globale e chiavi in mano con replica dei dati ovunque si trovino gli utenti. Gli utenti possono interagire con una replica dei dati più vicina alla loro sede.
- Nessuna gestione di schemi o indici, perché il motore di database è completamente indipendente dallo schema.
- Presenza ubiquitaria a livello di area, perché Azure Cosmos DB è disponibile in tutte le aree di Azure in tutto il mondo, con più di 54 aree nel cloud pubblico.
- Più opzioni di coerenza definite con precisione, perché il protocollo di replica multimaster di Azure Cosmos DB viene progettato con attenzione per offrire cinque opzioni di coerenza ben definite, ovvero coerenza assoluta, decadimento ristretto, coerenza di sessione, coerenza del prefisso e coerenza finale.
- 99,999% di disponibilità per le operazioni di lettura e scrittura.
- Richiamo a livello di codice (o tramite il portale di Azure) del failover a livello di area dell'account Azure Cosmos DB per assicurarsi che l'applicazione sia progettata per resistere a emergenze locali.
- Bassa latenza garantita al 99° percentile, in tutto il mondo.

**Riferimenti**
- [Azure portal](https://portal.azure.com) 
- [Documentazione di Azure Cosmos DB](/azure/cosmos-db/introduction)

## <a name="azure-sql-database"></a>database SQL di Azure
 [Database SQL di Azure](https://azure.microsoft.com/services/sql-database/) è un servizio gestito di database relazionale per utilizzo generico. È possibile usarlo per creare un livello di archiviazione dati con disponibilità e prestazioni elevate per applicazioni e soluzioni nel cloud di Azure.

**Funzionalità principali**
- **Strumenti e modelli di database elastici:** con un database elastico, gli sviluppatori possono raggruppare le risorse tra un gruppo di database per la scalabilità. Per gestire queste risorse a livello amministrativo, inviare uno script come processo. Il database SQL esegue quindi lo script tra i database.
- **Prestazioni elevate:** le applicazioni con velocità effettiva elevata possono sfruttare la versione più recente. Offre il 25% in più di potenza del database premium.
- **Backup, replica e disponibilità elevata:** la replica predefinita e un contratto di servizio supportato da Microsoft a livello di database assicurano continuità delle applicazioni e protezione da eventi catastrofici. La replica geografica attiva consente di configurare il failover e il ripristino self-service, per un controllo completo del ripristino in seguito a eventi imprevisti. È possibile ripristinare i dati da un totale di 35 giorni di backup.
- **Manutenzione quasi inesistente:** il software automatico fa parte del servizio. Le repliche di sistema predefinite assicurano protezione intrinseca dei dati, tempo di esecuzione del database e stabilità del sistema. Le repliche di sistema vengono spostate automaticamente nei nuovi computer. In caso di errore di una replica, viene automaticamente effettuato il provisioning di una nuova.
- **Sicurezza:** Database SQL di Azure offre un portfolio di funzionalità di sicurezza per soddisfare i criteri di conformità dell'organizzazione o imposti dal settore:
    - Il controllo offre agli sviluppatori la possibilità di eseguire attività correlate alla conformità e di acquisire informazioni sulle attività.
    - Gli sviluppatori e i reparti IT possono implementare i criteri a livello di database per limitare l'accesso a dati sensibili con misure di sicurezza a livello di riga, mascheramento dinamico dei dati e Transparent Data Encryption per Database SQL di Azure.
    - Database SQL di Azure viene verificato dai principali revisori del cloud nell'ambito delle certificazioni e delle approvazioni di conformità di Azure, ad esempio HIPAA BAA, ISO/IEC 27001:2005, FedRAMP e Clausole modello UE.

**Riferimenti**
- [Azure portal](https://portal.azure.com) 
- [Documentazione di Database SQL di Azure](/azure/sql-database/) 
