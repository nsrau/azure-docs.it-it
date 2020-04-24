---
title: Archiviazione, gestione e salvataggio permanente dei dati delle applicazioni nel cloud con Visual Studio App Center e i servizi di Azure
description: Informazioni sui servizi, ad esempio Visual Studio App Center che consentono di archiviare, gestire e salvare in modo permanente i dati delle applicazioni per dispositivi mobili nel cloud.
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
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Archiviare, sincronizzare ed eseguire query sui dati dell'applicazione mobile dal cloud
Indipendentemente dal tipo di applicazione che si compila, è probabile che vengano generati ed elaborati i dati. Gli utenti dell'applicazione hanno aspettative elevate. Desiderano che l'applicazione funzioni velocemente e facilmente, in tutte le circostanze. La maggior parte delle applicazioni funziona anche tra più dispositivi. È possibile accedere all'applicazione da un dispositivo desktop o mobile. Più utenti possono utilizzare l'applicazione contemporaneamente e condividere i dati con la previsione di ottenere l'accesso immediato e in tempo reale ai dati.

Gli utenti dell'applicazione non avranno sempre connettività Internet. Le applicazioni sono progettate e dovrebbero funzionare con o senza una connessione Internet. Gli sviluppatori devono scegliere la soluzione più adatta per l'archiviazione e la sincronizzazione dei dati nel cloud per offrire un'esperienza utente ottimale per la propria applicazione.

Microsoft offre un'ampia gamma di servizi che eliminano la necessità di creare server, selezionare il database o preoccuparsi della scalabilità o della sicurezza per offrire un'esperienza più completa possibile. Questi servizi offrono un'esperienza di sviluppo ottimale che consente di archiviare i dati delle applicazioni nel cloud usando le API SQL o NoSQL. È anche possibile sincronizzare i dati su tutti i dispositivi e consentire all'applicazione di usare o senza una connessione di rete per consentire la compilazione di applicazioni scalabili e affidabili.

Usare i servizi seguenti per gestire e archiviare i dati delle applicazioni per dispositivi mobili nel cloud.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center dati](/appcenter/data/) è un servizio di gestione dati. Consente alle applicazioni di gestire, salvare in modo permanente e sincronizzare i dati dell'applicazione nel cloud tra diversi dispositivi e piattaforme in scenari online e offline. Basati su Azure Cosmos DB, App Center scalabilità dei dati in base alla crescita dell'utente e al numero di applicazioni. Garantisce bassa latenza, disponibilità elevata e scalabilità elevata per tutti i dati.

**Funzionalità principali**
- Provisioning semplice in un nuovo database di Azure Cosmos DB o in un database Azure Cosmos DB esistente dal portale di Visual Studio App Center.
- Supporto del database NoSQL per archiviare, sincronizzare ed eseguire query sui dati dell'applicazione in modo semplice.
- Basato su Azure Cosmos DB, questo servizio eredita la maggior parte delle funzionalità chiave offerte da Azure Cosmos DB e può essere ridimensionato a livello globale per soddisfare le esigenze aziendali.
- Funzionalità di sincronizzazione in linea e non in linea per sincronizzare i dati tra dispositivi.
- SDK per client per dispositivi mobili che è possibile usare per gestire facilmente i dati dell'applicazione privata.
- Supporto della piattaforma per iOS, Android, Novell e React native.

**Riferimenti**
- [Iscriversi con Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Introduzione ai dati di App Center](/appcenter/data/getting-started)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) è un servizio di database multimodello distribuito a livello globale. È possibile usarlo per creare applicazioni su scala globale. Con Azure Cosmos DB è possibile ridimensionare in modo elastico e indipendente la velocità effettiva e l'archiviazione in qualsiasi numero di aree di Azure in tutto il mondo. Puoi sfruttare i vantaggi di un accesso ai dati rapido e a una sola cifra in millisecondi usando le tue superfici dell'API preferite. Queste superfici includono SQL, MongoDB, Cassandra, tabelle o Gremlin. Azure Cosmos DB fornisce in modo univoco contratti di servizio (SLA) completi per velocità effettiva, latenza, disponibilità e coerenza.

**Funzionalità principali**
- Supporta un'ampia gamma di API, che include l'API SQL (Core), API Cassandra, l'API MongoDB, l'API Gremlin e API Tabella.
- La distribuzione globale chiavi in mano replica i dati ovunque si trovino gli utenti. Gli utenti possono interagire con una replica dei dati più vicini.
- Nessuna gestione di schemi o indici perché il motore di database è completamente indipendente dallo schema.
- Presenza a livello di area diffusa perché Azure Cosmos DB è disponibile in tutte le aree di Azure in tutto il mondo, che include più di 54 aree nel cloud pubblico.
- Sono state definite con precisione più opzioni di coerenza, poiché il protocollo di replica multimaster di Azure Cosmos DB viene progettato con attenzione per offrire cinque opzioni di coerenza ben definite. Queste cinque scelte sono forte, con decadimento ristretto, sessione, prefisso coerente e finale.
- disponibilità del 99,999% per letture e scritture.
- A livello di codice (o tramite il portale di Azure) richiamare il failover a livello di area dell'account Azure Cosmos DB per assicurarsi che l'applicazione sia progettata per sostenere un'emergenza a livello di area.
- Bassa latenza garantita al 99 ° percentile in tutto il mondo.

**Riferimenti**
- [Azure portal](https://portal.azure.com) 
- [Documentazione di Azure Cosmos DB](/azure/cosmos-db/introduction)

## <a name="azure-sql-database"></a>Database SQL di Azure
 Il [database SQL di Azure](https://azure.microsoft.com/services/sql-database/) è un servizio gestito di database relazionale per utilizzo generico. È possibile usarlo per creare un livello di archiviazione dati a disponibilità elevata e a elevate prestazioni per le applicazioni e le soluzioni nel cloud di Azure.

**Funzionalità principali**
- **Strumenti e modelli di database elastici:** Con un database elastico, gli sviluppatori possono raggruppare risorse tra un gruppo di database per la scalabilità. Per gestire queste risorse in modo amministrativo, è necessario inviare uno script come processo. Il database SQL esegue quindi lo script nei database.
- **Prestazioni elevate:** Le applicazioni con velocità effettiva elevata possono sfruttare la versione più recente. Offre una potenza del database Premium del 25% superiore.
- **Backup, replica e disponibilità elevata:** La replica incorporata e un contratto di contratto con supporto Microsoft a livello di database garantiscono la continuità e la protezione delle applicazioni da eventi irreversibili. La replica geografica attiva consente di configurare il failover e il ripristino self-service, che forniscono il controllo completo sul ripristino degli Oops. Il ripristino dei dati è disponibile dai backup dei dati fino a 35 giorni.
- **Manutenzione quasi zero:** Il software automatico fa parte del servizio. Le repliche di sistema predefinite consentono di offrire protezione dei dati intrinseca, tempo di esecuzione del database e stabilità del sistema. Le repliche di sistema vengono spostate automaticamente nei nuovi computer. Viene eseguito il provisioning in tempo reale quando i vecchi hanno esito negativo.
- **Sicurezza:** Il database SQL di Azure offre un portfolio di funzionalità di sicurezza per soddisfare i criteri di conformità dell'organizzazione o richiesti dal settore:
    - Il controllo offre agli sviluppatori la possibilità di eseguire attività correlate alla conformità e di acquisire informazioni sulle attività.
    - Gli sviluppatori possono implementare i criteri a livello di database per limitare l'accesso ai dati sensibili con sicurezza a livello di riga, maschera dati dinamica e Transparent Data Encryption per il database SQL di Azure.
    - Il database SQL di Azure viene verificato dai revisori principali del cloud come parte dell'ambito delle certificazioni di conformità e delle approvazioni chiave di Azure, ad esempio HIPAA BAA, ISO/IEC 27001:2005, FedRAMP e clausole del modello UE.

**Riferimenti**
- [Azure portal](https://portal.azure.com) 
- [Documentazione sul database SQL di Azure](/azure/sql-database/) 
