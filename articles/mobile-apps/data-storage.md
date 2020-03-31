---
title: Archiviare, gestire e rendere persistenti i dati delle applicazioni nel cloud con Visual Studio App Center e i servizi di Azure
description: Informazioni su servizi come Visual Studio App Center che consentono di archiviare, gestire e rendere persistenti i dati delle applicazioni per dispositivi mobili nel cloud.
author: codemillmatt
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 166847325fa9094136f1c2a143f1751420f05f66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240902"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Archiviare, sincronizzare ed eseguire query sui dati delle applicazioni per dispositivi mobili dal cloud
Indipendentemente dal tipo di applicazione compilata, è probabile che si generino ed elaborino i dati. Gli utenti dell'applicazione hanno grandi aspettative. Vogliono che l'applicazione funzioni in modo rapido e senza soluzione di continuità, in tutte le circostanze. La maggior parte delle applicazioni funziona anche su più dispositivi. È possibile accedere all'applicazione da un desktop o un dispositivo mobile. Più utenti potrebbero utilizzare l'applicazione contemporaneamente e condividere i dati con l'aspettativa di ottenere l'accesso istantaneo e in tempo reale ai dati.

Gli utenti dell'applicazione non sempre dispongono di connettività Internet. Le applicazioni sono progettate e previste per funzionare con o senza una connessione Internet. Gli sviluppatori devono scegliere la soluzione giusta per l'archiviazione e la sincronizzazione dei dati nel cloud per offrire un'esperienza cliente eccezionale per la propria applicazione.

Microsoft offre una varietà di servizi che eliminano la necessità di spin up server, scegliere il database, o preoccuparsi di scala o sicurezza per fornire l'esperienza più ricca possibile. Questi servizi offrono un'esperienza di sviluppo eccezionale che consente di archiviare i dati dell'applicazione nel cloud usando le API SQL o NoSQL. È inoltre possibile sincronizzare i dati su tutti i dispositivi e consentire all'applicazione di lavorare con o senza una connessione di rete per creare applicazioni scalabili e affidabili.

Usa i servizi seguenti per gestire e archiviare i dati delle applicazioni mobili nel cloud.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Data](/appcenter/data/) è un servizio di gestione dei dati. Consente alle applicazioni di gestire, rendere persistenti e sincronizzare i dati delle applicazioni nel cloud tra dispositivi e piattaforme diversi in scenari online e offline. Basato su Azure Cosmos DB, App Center Data si adatta alla base di utenti e al numero di applicazioni cresce. Garantisce bassa latenza, disponibilità elevata e scalabilità elevata per tutti i dati.

**Caratteristiche principali**
- Facile provisioning in un nuovo database cosmos DB di Azure o in un database cosmos DB di Azure esistente dal portale di Visual Studio App Center.
- Supporto del database NoSQL per archiviare, sincronizzare ed eseguire facilmente query sui dati dell'applicazione.
- Basato su Azure Cosmos DB, questo servizio eredita la maggior parte delle funzionalità chiave offerte da Azure Cosmos DB e può essere scalato a livello globale per soddisfare le esigenze aziendali.
- Funzionalità di sincronizzazione online e offline per sincronizzare i dati tra dispositivi.
- SDK client mobili che è possibile utilizzare per gestire facilmente i dati privati delle applicazioni.
- Supporto della piattaforma per iOS, Android, Xamarin e React Native.

**Riferimenti**
- [Iscriversi a Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Introduzione ai dati dell'App Center](/appcenter/data/getting-started)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) è un servizio di database multimodello distribuito a livello globale. È possibile utilizzarlo per costruire applicazioni su scala di pianeta. Con Azure Cosmos DB, è possibile scalare in modo elastico e indipendente la velocità effettiva e l'archiviazione in un numero qualsiasi di aree di Azure in tutto il mondo. Puoi sfruttare l'accesso rapido ai dati a una cifra al millisecondo usando le tue superfici API preferite. Queste superfici includono SQL, MongoDB, Cassandra, Tables o Gremlin. Azure Cosmos DB offre in modo univoco contratti di servizio completi per velocità effettiva, latenza, disponibilità e coerenza.

**Caratteristiche principali**
- Supporta un'ampia gamma di API, che include l'API SQL (Core), l'API Cassandra, l'API MongoDB, l'API Gremlin e l'API Table.
- La distribuzione globale con chiavi in mano replica i dati ovunque si trovino gli utenti. Gli utenti possono interagire con una replica dei dati più vicini.
- Nessuna gestione dello schema o dell'indice perché il motore di database è completamente indipendente dallo schema.
- Presenza regionale ubiquitosa perché Azure Cosmos DB è disponibile in tutte le aree di Azure in tutto il mondo, inclusa più di 54 aree nel cloud pubblico.
- Scelte di coerenza multiple definite con precisione perché il protocollo di replica multimaster di Azure Cosmos DB è attentamente progettato per offrire cinque scelte di coerenza ben definite. Queste cinque scelte sono forte, stantio limitato, sessione, prefisso coerente ed eventuale.
- Disponibilità del 99,999% sia per le letture che per le scritture.
- A livello di codice (o tramite il portale di Azure) richiamare il failover regionale dell'account di database Cosmos di Azure per assicurarsi che l'applicazione sia progettata per resistere a un'emergenza a livello regionale.
- Bassa latenza garantita al 99esimo percentile in tutto il mondo.

**Riferimenti**
- [Portale di Azure](https://portal.azure.com) 
- [Documentazione di Azure Cosmos DB](/azure/cosmos-db/introduction)

## <a name="azure-sql-database"></a>database SQL di Azure
 [Il database SQL](https://azure.microsoft.com/services/sql-database/) di Azure è un servizio gestito di database relazionale generico. È possibile usarlo per creare un livello di archiviazione dati a disponibilità elevata e ad alte prestazioni per applicazioni e soluzioni nel cloud di Azure.You can use it to create a highly available and high-performance data storage layer for applications and solutions in the Azure cloud.

**Caratteristiche principali**
- **Modelli e strumenti per database elastici:** Con un database elastico, gli sviluppatori possono raggruppare le risorse in un gruppo di database per la scalabilità. Per gestire a livello amministrativo queste risorse, si invia uno script come processo. Il database SQL esegue quindi lo script tra i database.
- **Prestazioni elevate:** Le applicazioni ad alta velocità effettiva possono sfruttare la versione più recente. Offre il 25% di potenza premium del database.
- **Backup, replica e disponibilità elevata:** La replica integrata e un contratto di servizio supportato da Microsoft a livello di database forniscono continuità e protezione dell'applicazione contro eventi catastrofici. La replica geografica attiva consente di configurare il failover e il ripristino self-service, che forniscono il controllo completo sul "ripristino delle oops". Il ripristino dei dati è disponibile da backup dei dati fino a 35 giorni.
- **Manutenzione quasi zero:** Il software automatico fa parte del servizio. Le repliche di sistema integrate consentono di garantire la protezione dei dati intrinseca, i tempi di attività del database e la stabilità del sistema. Le repliche di sistema vengono spostate automaticamente nei nuovi computer. Sono sottoposti a provisioning al volo mentre quelli vecchi falliscono.
- **Sicurezza:** Il database SQL di Azure offre un portfolio di funzionalità di sicurezza per soddisfare i criteri di conformità obbligatori dell'organizzazione o del settore:Azure SQL Database offers a portfolio of security features to meet organizational or industry-mandated compliance policies:
    - Il controllo offre agli sviluppatori la possibilità di eseguire attività correlate alla conformità e di acquisire informazioni sulle attività.
    - Gli sviluppatori e l'IT possono implementare criteri a livello di database per limitare l'accesso ai dati sensibili con sicurezza a livello di riga, mascheramento dinamico dei dati e crittografia dei dati trasparente per il database SQL di Azure.Developers and IT can implement policies at the database level to help limit access to sensitive data with row-level security, dynamic data masking, and transparent data encryption for Azure SQL Database.
    - Il database SQL di Azure viene verificato dai principali revisori cloud come parte dell'ambito delle certificazioni e delle approvazioni chiave di conformità di Azure, ad esempio le clausole del modello HIPAA BAA, ISO/IEC 27001:2005, FedRAMP e EU Model.

**Riferimenti**
- [Portale di Azure](https://portal.azure.com) 
- [Documentazione del database SQL di AzureAzure SQL Database documentation](/azure/sql-database/) 
