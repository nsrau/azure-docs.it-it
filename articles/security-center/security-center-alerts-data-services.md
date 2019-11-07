---
title: Rilevamento delle minacce per i servizi dati nel centro sicurezza di Azure | Microsoft Docs
description: Questo argomento presenta gli avvisi di servizi dati disponibili nel centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: memildin
ms.openlocfilehash: c69352b8ff11edfce8bd800a0d3aad4584557572
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621335"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Rilevamento delle minacce per i servizi dati nel centro sicurezza di Azure

 Il Centro sicurezza di Azure analizza i log dei servizi di archiviazione dei dati e attiva gli avvisi quando rileva una minaccia per le risorse di dati. Questo argomento elenca gli avvisi generati dal centro sicurezza per i servizi seguenti:

* [Database SQL di Azure e SQL Data Warehouse di Azure](#data-sql)
* [Archiviazione di Azure](#azure-storage)
* [Azure Cosmos DB](#cosmos-db)

## Database SQL e SQL Data Warehouse<a name="data-sql"></a>

Rilevamento minacce SQL identifica le attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database. 

|Avviso|Descrizione|
|---|---|
|**Vulnerabilità a SQL injection**|Un'applicazione ha generato un'istruzione SQL non corretta nel database. Questo può indicare una possibile vulnerabilità agli attacchi intrusivi nel codice SQL. Esistono due possibili motivi per un'istruzione difettosa. Un difetto nel codice dell'applicazione potrebbe avere creato l'istruzione SQL non funzionante. In alternativa, il codice dell'applicazione o le stored procedure non hanno purificato l'input dell'utente durante la creazione dell'istruzione SQL difettosa, che può essere sfruttata per SQL injection.|
|**Potenziale attacco SQL injection**|Si è verificato un exploit attivo in un'applicazione identificata vulnerabile a SQL injection. Ciò significa che un utente malintenzionato sta provando a inserire istruzioni SQL dannose usando il codice dell'applicazione vulnerabile o le stored procedure.|
|**Accesso da una posizione insolita**|È stata apportata una modifica al modello di accesso per SQL Server, in cui qualcuno ha eseguito l'accesso al server da una posizione geografica insolita. In alcuni casi, l'avviso rileva un'azione legittima (una nuova applicazione o la manutenzione da parte dello sviluppatore). In altri casi, l'avviso rileva un'azione dannosa (un dipendente precedente o un utente malintenzionato esterno).|
|**Accesso da entità non note**|È stata apportata una modifica al modello di accesso per SQL Server. Un utente ha eseguito l'accesso al server usando un principale insolito (utente). In alcuni casi, l'avviso rileva un'azione legittima (una nuova applicazione o la manutenzione da parte dello sviluppatore). In altri casi, l'avviso rileva un'azione dannosa (un dipendente precedente o un utente malintenzionato esterno).|
|**Accesso da un'applicazione potenzialmente dannosa**|Per accedere al database è stata usata un'applicazione potenzialmente dannosa. In alcuni casi, l'avviso rileva test di penetrazione in corso. In altri casi, l'avviso rileva un attacco che usa strumenti comuni.|
|**Forza bruta credenziali SQL**|Si è verificato un numero anomalo elevato di accessi non riusciti con credenziali diverse. In alcuni casi, l'avviso rileva test di penetrazione in corso. In altri casi, l'avviso rileva un attacco di forza bruta.|

Per altre informazioni sugli avvisi di rilevamento delle minacce di SQL, vedere [rilevamento delle minacce nel database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview). In particolare, esaminare la sezione avvisi di rilevamento minacce. Vedere anche [come il Centro sicurezza di Azure consente di rivelare un cyberattack](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/) per visualizzare un esempio del modo in cui il Centro sicurezza ha usato il rilevamento di attività SQL dannose per individuare un attacco.

## Archiviazione di Azure<a name="azure-storage"></a>

>[!NOTE]
> Advanced Threat Protection per l'archiviazione è attualmente disponibile solo per l'archiviazione BLOB.

Advanced Threat Protection per l'archiviazione offre un ulteriore livello di intelligence per la sicurezza che rileva tentativi insoliti e potenzialmente dannosi di accedere o sfruttare gli account di archiviazione. Questo livello di protezione consente di risolvere le minacce senza richiedere l'intervento di un esperto di sicurezza e la gestione dei sistemi di monitoraggio della sicurezza.

Il Centro sicurezza analizza i log di diagnostica delle richieste di lettura, scrittura ed eliminazione nell'archivio BLOB per rilevare le minacce e attiva gli avvisi quando si verificano anomalie nelle attività. Per altre informazioni, vedere [configurare la registrazione analisi archiviazione](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging).

> [!div class="mx-tableFixed"]

|Avviso|Descrizione|
|---|---|
|**Anomalia di accesso al percorso insolito**|L'analisi del traffico di rete campionata ha rilevato una comunicazione in uscita Remote Desktop Protocol (RDP) anomala, originata da una risorsa nella distribuzione. Questa attività viene considerata anomala per questo ambiente. Può indicare che la risorsa è stata compromessa e viene ora usata per l'attacco di forza bruta a un endpoint RDP esterno. Questo tipo di attività potrebbe causare la segnalazione dell'indirizzo IP come dannoso da entità esterne.|
|**Anomalie di accesso all'applicazione**|Indica che l'account di archiviazione ha eseguito l'accesso a un'applicazione insolita. Una causa potenziale è che un utente malintenzionato ha eseguito l'accesso all'account di archiviazione utilizzando una nuova applicazione.|
|**Anomalia accesso anonimo**|Indica la presenza di una modifica nel modello di accesso a un account di archiviazione. Ad esempio, è stato eseguito l'accesso anonimo all'account (senza autenticazione), che è imprevisto rispetto al modello di accesso recente su questo account. Una possibile provocazione è che un utente malintenzionato ha sfruttato l'accesso in lettura pubblico a un contenitore che contiene l'archiviazione BLOB.|
|**Anomalia Tor**|Indica che l'accesso a questo account è stato eseguito correttamente da un indirizzo IP noto come nodo di uscita attivo di Tor (un proxy anonime). La gravità di questo avviso considera il tipo di autenticazione usato, se presente, e se questo è il primo caso di tale accesso. Le possibili cause possono essere un utente malintenzionato che ha eseguito l'accesso all'account di archiviazione usando Tor o un utente legittimo che ha eseguito l'accesso all'account di archiviazione usando Tor.|
|**Anomalia exfiltration dati**|Indica che è stata estratta una quantità insolitamente elevata di dati rispetto alle attività recenti sul contenitore di archiviazione. Una possibile provocazione è che un utente malintenzionato ha estratto una grande quantità di dati da un contenitore che contiene l'archiviazione BLOB.|
|**Anomalia di eliminazione imprevista**|Indica che una o più operazioni di eliminazione impreviste si sono verificate in un account di archiviazione, rispetto alle attività recenti di questo account. Una causa potenziale è che un utente malintenzionato ha eliminato i dati dall'account di archiviazione.|
|**Caricare il pacchetto di servizi cloud di Azure**|Indica che un pacchetto di servizi cloud di Azure (file con estensione cspkg) è stato caricato in un account di archiviazione in modo insolito, rispetto alle attività recenti di questo account. Una possibile causa è che un utente malintenzionato sta preparando la distribuzione di codice dannoso dall'account di archiviazione a un servizio cloud di Azure.|
|**Anomalie di accesso alle autorizzazioni**|Indica che le autorizzazioni di accesso di questo contenitore di archiviazione sono state modificate in modo insolito. È possibile che un utente malintenzionato abbia modificato le autorizzazioni del contenitore per indebolirne il comportamento di sicurezza o per ottenere la persistenza.|
|**Anomalia di accesso ispezione**|Indica che le autorizzazioni di accesso di un account di archiviazione sono state ispezionate in modo insolito, rispetto alle attività recenti di questo account. Una possibile provocazione è che un utente malintenzionato ha eseguito la ricognizione per un attacco futuro.|
|**Anomalia di esplorazione dei dati**|Indica che i BLOB o i contenitori in un account di archiviazione sono stati enumerati in modo anomalo, rispetto alle attività recenti di questo account. Una possibile provocazione è che un utente malintenzionato ha eseguito la ricognizione per un attacco futuro.|
|**Potenziale caricamento malware**|Indica che un BLOB contenente il potenziale malware è stato caricato in un account di archiviazione. Possibili cause possono includere un malware intenzionale caricato da un utente malintenzionato o un caricamento non intenzionale, di un BLOB potenzialmente dannoso, da un utente legittimo.|

>[!NOTE]
>Advanced Threat Protection per l'archiviazione non è attualmente disponibile in Azure per enti pubblici e aree cloud sovrane.

Per altre informazioni sugli avvisi per l'archiviazione, vedere [Advanced Threat Protection per archiviazione di Azure](../storage/common/storage-advanced-threat-protection.md). In particolare, esaminare la sezione "avvisi di protezione".

## Azure Cosmos DB<a name="cosmos-db"></a>

Gli avvisi seguenti vengono generati da tentativi insoliti e potenzialmente dannosi di accesso o exploit Azure Cosmos DB account:

|Avviso|Descrizione|
|---|---|
|**Accesso da una posizione insolita**|Indica che è stata apportata una modifica nel modello di accesso a un account Azure Cosmos DB. Un utente ha eseguito l'accesso a questo account da un indirizzo IP non noto, rispetto alle attività recenti. Un utente malintenzionato ha eseguito l'accesso all'account oppure un utente legittimo ha eseguito l'accesso a tale account da una posizione geografica nuova e insolita. Un esempio di quest'ultimo è la manutenzione remota di una nuova applicazione o di uno sviluppatore.|
|**Exfiltration dati insoliti**|Indica che è stata apportata una modifica al modello di estrazione dei dati da un account Azure Cosmos DB. Un utente ha estratto una quantità di dati insolita rispetto alle attività recenti. Un utente malintenzionato potrebbe avere estratto una grande quantità di dati da un database di Azure Cosmos DB (ad esempio, exfiltration o perdita di dati o un trasferimento non autorizzato di dati). In alternativa, un utente o un'applicazione legittima potrebbe avere estratto una quantità di dati insolita da un contenitore, ad esempio per attività di backup di manutenzione.|

Per ulteriori informazioni, vedere [Advanced Threat Protection per Azure Cosmos DB](../cosmos-db/cosmos-db-advanced-threat-protection.md).
