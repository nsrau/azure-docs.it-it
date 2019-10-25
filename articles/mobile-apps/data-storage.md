---
title: Archiviazione, gestione e salvataggio permanente dei dati delle applicazioni nel cloud con Visual Studio App Center e i servizi di Azure
description: Informazioni sui servizi, ad esempio App Center che consente di archiviare, gestire e salvare in modo permanente i dati delle applicazioni per dispositivi mobili nel cloud.
author: elamalani
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: b7043c7e9bd944bfb3633397edfa3c35609bd8ec
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795575"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Archiviare, sincronizzare ed eseguire query sui dati dell'applicazione mobile dal cloud
Indipendentemente dal tipo di applicazione che si sta compilando, probabilmente verranno generati ed elaborati i dati. Le aspettative degli utenti sono elevate e vogliono che l'applicazione funzioni in modo rapido e facile, in tutte le circostanze. La maggior parte delle applicazioni funziona su più dispositivi. Pertanto, quando si accede all'applicazione, è possibile accedervi da un dispositivo desktop o mobile. È possibile che più utenti utilizzino l'applicazione allo stesso tempo e condividono i dati con la previsione di ottenere l'accesso immediato e in tempo reale ai dati.

Per gli utenti dell'applicazione non sarà sempre presente la connettività Internet. le applicazioni sono progettate e dovrebbero funzionare con o senza connessione Internet. Con tutte queste complessità crescenti, è molto importante per gli sviluppatori scegliere la soluzione più adatta per l'archiviazione e la sincronizzazione dei dati nel cloud per offrire un'esperienza utente ottimale per la propria applicazione.

Microsoft offre un'ampia gamma di servizi che elimina la necessità di creare server, selezionare il database o preoccuparsi della scalabilità o della sicurezza per offrire un'esperienza più completa possibile. Questi servizi offrono un'esperienza di sviluppo ottimale che consente di archiviare i dati delle applicazioni nel cloud usando le API SQL o NoSQL, sincronizzare i dati su tutti i dispositivi e consentire all'applicazione di usare o senza una connessione di rete per contribuire alla compilazione scalabile e affidabile applicazioni.

Usare i servizi seguenti per gestire e archiviare i dati delle applicazioni per dispositivi mobili nel cloud.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Data](/appcenter/data/) è un servizio di gestione dati che consente alle applicazioni di gestire, salvare in modo permanente e sincronizzare i dati dell'applicazione nel cloud tra diversi dispositivi e piattaforme in scenari online e offline. **Basato su Cosmos DB** servizio con scalabilità in base alla crescita della base utente e del numero di applicazioni, questo servizio garantisce una bassa latenza, disponibilità elevata e scalabilità elevata per tutti i dati.

**Funzionalità principali**
- Eseguire facilmente il **provisioning di un nuovo database di Cosmos DB** o **connettersi a un database di Cosmos DB esistente** dal portale di App Center.
- **Supporto del database NoSQL** per archiviare, sincronizzare ed eseguire query sui dati dell'applicazione in modo semplice.
- **Basato su Cosmos DB**, questo servizio eredita la maggior parte delle funzionalità chiave offerte da Azure Cosmos DB e può essere **ridimensionato a livello globale** per soddisfare le esigenze aziendali.
- Funzionalità di **sincronizzazione in linea e** non in linea per sincronizzare i dati tra dispositivi.
- **SDK per client** per dispositivi mobili che consentono di gestire facilmente i dati delle applicazioni private.
- **Supporto della piattaforma** : iOS, Android, Novell, React native.

**Riferimenti**
- [Iscriversi con App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Introduzione ai dati di App Center](/appcenter/data/getting-started)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) è il servizio di database multimodello distribuito a livello globale di Microsoft che consente di creare applicazioni su scala globale. Con un semplice clic su un pulsante, Cosmos DB garantisce la scalabilità elastica e indipendente della velocità effettiva e delle risorse di archiviazione tra un numero qualsiasi di aree di Azure del mondo. Puoi ridimensionare in modo elastico la velocità effettiva e lo spazio di archiviazione e sfruttare i vantaggi di un accesso ai dati rapido, a una sola cifra, usando le tue aree API preferite, tra cui SQL, MongoDB, Cassandra, tabelle o Gremlin. Cosmos DB fornisce in modo univoco contratti di servizio (SLA) completi per velocità effettiva, latenza, disponibilità e coerenza.

**Funzionalità principali**
- Supporta un' **ampia gamma di API** include API SQL (Core), API Cassandra, API MONGODB, API Gremlin e API tabella.
- La **distribuzione globale chiavi in mano** replica i dati ovunque si trovino gli utenti, in modo che gli utenti possano interagire con una replica dei dati più vicina.
- **Nessun schema o gestione degli indici** come il motore di database è completamente indipendente dallo schema.
- La **presenza a livello** di area diffusa come Cosmos DB è disponibile in tutte le aree di Azure in tutto il mondo, tra cui 54 più aree nel cloud pubblico.
- Con **precisione, più opzioni di coerenza** come il protocollo di replica multimaster di Cosmos DB sono progettate con attenzione per offrire cinque opzioni di coerenza ben definite, ovvero forte, con decadimento ristretto, sessione, prefisso coerente e finale.
- **disponibilità del 99,999%** per letture e scritture.
- A **livello di codice (o tramite il portale) richiamare il failover a livello** di area dell'account Cosmos per assicurarsi che l'applicazione sia progettata per resistere a un'emergenza a livello di area.
- **Bassa latenza garantita al 99 ° percentile**, in tutto il mondo.

**Riferimenti**
- [Azure portal](https://portal.azure.com) 
- [Documentazione](/azure/cosmos-db/introduction)   

## <a name="azure-sql-database"></a>database SQL di Azure
 Il [database SQL di Azure](https://azure.microsoft.com/services/sql-database/) è un servizio gestito di database relazionale per utilizzo generico che consente di creare un livello di archiviazione dei dati a disponibilità elevata e a elevate prestazioni per applicazioni e soluzioni nel cloud di Azure.

**Funzionalità principali**
- **Modelli e strumenti di database elastici** : un database elastico offre agli sviluppatori la possibilità di creare pool di risorse da usare tra un gruppo di database per il ridimensionamento, che può essere gestito in modo amministrativo inviando uno script come processo e il database SQL esegue il script tra i database.
- **Prestazioni elevate** : le applicazioni con velocità effettiva elevata possono sfruttare la versione più recente, che offre una potenza del database Premium del 25% superiore.
- **Backup, replica e disponibilità elevata** : la replica incorporata e un contratto di contratto con supporto Microsoft a livello di database garantiscono continuità e protezione delle applicazioni da eventi irreversibili. Replica geografica attiva consente di configurare il failover e il ripristino self-service, che forniscono il controllo completo sul ripristino degli Oops (ripristino dei dati dai backup dei dati disponibili fino a 35 giorni).
- **Manutenzione quasi zero** : il software automatico fa parte del servizio e le repliche di sistema predefinite contribuiscono a garantire la protezione intrinseca dei dati, il tempo di esecuzione del database e la stabilità del sistema. Le repliche di sistema vengono automaticamente spostate su nuovi computer, di cui viene effettuato immediatamente il provisioning in caso di errore nei vecchi computer.
- **Sicurezza** : il database SQL offre un portfolio di funzionalità di sicurezza per soddisfare i criteri di conformità dell'organizzazione o richiesti dal settore
    - Il controllo offre agli sviluppatori la possibilità di eseguire attività correlate alla conformità e di acquisire informazioni sulle attività.
    - Gli sviluppatori possono implementare i criteri a livello di database per limitare l'accesso ai dati sensibili con sicurezza a livello di riga, maschera dati dinamica e Transparent Data Encryption per il database SQL di Azure.
    - Il database SQL viene verificato dai revisori principali del cloud come parte dell'ambito delle certificazioni di conformità e delle approvazioni chiave di Azure, ad esempio HIPAA BAA, ISO/IEC 27001:2005, FedRAMP e clausole del modello UE.

**Riferimenti**
- [Azure portal](https://portal.azure.com) 
- [Documentazione](/azure/sql-database/)
   