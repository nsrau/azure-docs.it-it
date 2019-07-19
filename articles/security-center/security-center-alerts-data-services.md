---
title: Rilevamento delle minacce per i servizi dati nel centro sicurezza di Azure | Microsoft Docs
description: Questo argomento presenta gli avvisi di servizi dati disponibili nel centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 1cafd8a3c766e57aed67634d7da8498c9a6ee120
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295831"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Rilevamento delle minacce per i servizi dati nel centro sicurezza di Azure

 Il Centro sicurezza analizza i log dei servizi di archiviazione dei dati e attiva gli avvisi quando rileva una minaccia per le risorse di dati. Questo argomento elenca gli avvisi generati dal centro sicurezza per i servizi seguenti:

* [Database SQL di Azure e SQL Data Warehouse](#data-sql)
* [Archiviazione di Azure](#azure-storage)

## Database SQL di Azure e SQL Data Warehouse<a name="data-sql"></a>

Il rilevamento delle minacce SQL rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database. Il Centro sicurezza analizza i log di controllo SQL ed esegue in modalità nativa nel motore di SQL.

|Avviso|Descrizione|
|---|---|
|**Vulnerabilità a SQL injection**|Un'applicazione ha generato un'istruzione SQL non corretta nel database. Questo può indicare una possibile vulnerabilità agli attacchi intrusivi nel codice SQL. Ci sono due possibili motivi per la generazione di un'istruzione non corretta: Un difetto nel codice dell'applicazione ha creato un'istruzione SQL non funzionante. In alternativa, il codice dell'applicazione o le stored procedure non hanno purificato l'input dell'utente durante la creazione dell'istruzione SQL difettosa, che può essere sfruttata per SQL injection.|
|**Potenziale attacco SQL injection**|Si è verificato un exploit attivo in un'applicazione identificata vulnerabile a SQL injection. Ciò significa che un utente malintenzionato sta provando a inserire istruzioni SQL dannose usando il codice dell'applicazione vulnerabile o le stored procedure.|
|**Accesso da una posizione insolita**|È stata apportata una modifica al modello di accesso a SQL Server, in cui un utente ha effettuato l'accesso a SQL Server da una posizione geografica insolita. In alcuni casi, l'avviso rileva un'azione legittima (una nuova applicazione o la manutenzione da parte dello sviluppatore). In altri casi, l'avviso rileva un'azione dannosa (da parte di un ex dipendente o un utente malintenzionato esterno).|
|**Accesso da entità non note**|È stata apportata una modifica al modello di accesso a SQL Server: un utente ha effettuato l'accesso a SQL Server usando un'entità insolita (utente SQL). In alcuni casi, l'avviso rileva un'azione legittima (nuova applicazione o manutenzione da parte dello sviluppatore). In altri casi, l'avviso rileva un'azione dannosa (da parte di un ex dipendente o un utente malintenzionato esterno).|
|**Accesso da un'applicazione potenzialmente dannosa**|Per accedere al database è stata usata un'applicazione potenzialmente dannosa. In alcuni casi, l'avviso rileva test di penetrazione in corso. In altri casi, l'avviso rileva un attacco mediante strumenti comuni di attacco.|
|**Forza bruta credenziali SQL**|Si è verificato un numero elevato anomalo di accessi non riusciti con credenziali diverse. In alcuni casi, l'avviso rileva test di penetrazione in corso. In altri casi, l'avviso rileva un attacco di forza bruta.|

Per altre informazioni sugli avvisi di rilevamento delle minacce di SQL, vedere[rilevamento delle minacce nel database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)ed esaminare la sezione avvisi di rilevamento minacce. Vedere anche [come il Centro sicurezza di Azure consente di rivelare un cyberattack](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/) per visualizzare un esempio del modo in cui il Centro sicurezza ha usato il rilevamento di attività SQL dannose per individuare un attacco.

## Archiviazione di Azure<a name="azure-storage"></a>

>[!NOTE]
> Advanced Threat Protection per archiviazione di Azure è attualmente disponibile solo per l'archiviazione BLOB. 

Advanced Threat Protection per Archiviazione di Azure offre un livello aggiuntivo di intelligence di sicurezza in grado di rilevare tentativi insoliti e potenzialmente dannosi di accesso o sfruttamento degli account di archiviazione. Questo livello di protezione consente di risolvere le minacce senza richiedere l'intervento di un esperto di sicurezza e gestire i sistemi di monitoraggio della sicurezza.

Il Centro sicurezza analizza i log di diagnostica delle richieste di lettura, scrittura ed eliminazione nell'archivio BLOB per rilevare le minacce e attiva gli avvisi quando si verificano anomalie nelle attività. Per ulteriori informazioni, vedere per [configurare analisi archiviazione la registrazione](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging) per ulteriori informazioni.

> [!div class="mx-tableFixed"]

|Avviso|Descrizione|
|---|---|
|**Anomalia di accesso al percorso insolito**|L'analisi del traffico di rete campionata ha rilevato una comunicazione in uscita Remote Desktop Protocol (RDP) anomala originata da una risorsa nella distribuzione. Questa attività viene considerata anomala per questo ambiente e può indicare che la risorsa è stata compromessa e viene ora utilizzata per la forza bruta dell'endpoint RDP esterno. Si noti che in seguito a questo tipo di attività l'IP può venire contrassegnato come dannoso dalle entità esterne.|
|**Anomalie di accesso all'applicazione**|Indica che l'account di archiviazione ha eseguito l'accesso a un'applicazione insolita. Una causa potenziale è che un utente malintenzionato ha eseguito l'accesso all'account di archiviazione utilizzando una nuova applicazione.|
|**Anomalia accesso anonimo**|Indica la presenza di una modifica nel modello di accesso a un account di archiviazione. Ad esempio, è stato eseguito l'accesso anonimo all'account (senza autenticazione), che è imprevisto rispetto al modello di accesso recente su questo account. Una possibile provocazione è che un utente malintenzionato ha sfruttato l'accesso in lettura pubblico a un contenitore che contiene l'archiviazione BLOB (s).|
|**Anomalia exfiltration dati**|Indica che è stata estratta una quantità insolitamente elevata di dati rispetto alle attività recenti sul contenitore di archiviazione. Una possibile provocazione è che un utente malintenzionato ha estratto una grande quantità di dati da un contenitore che contiene l'archiviazione BLOB.|
|**Anomalia di eliminazione imprevista**|Indica che una o più operazioni di eliminazione impreviste si sono verificate in un account di archiviazione, rispetto alle attività recenti di questo account. Una causa potenziale è che un utente malintenzionato ha eliminato i dati dall'account di archiviazione.|
|**Caricare il pacchetto del servizio cloud di Azure**|Indica che un pacchetto del servizio cloud di Azure (file con estensione cspkg) è stato caricato in un account di archiviazione in modo insolito, rispetto alle attività recenti di questo account. Una possibile causa è che un utente malintenzionato sta preparando la distribuzione di codice dannoso dall'account di archiviazione a un servizio cloud di Azure.|
|**Anomalie di accesso alle autorizzazioni**|Indica che le autorizzazioni di accesso di questo contenitore di archiviazione sono state modificate in modo insolito. È possibile che un utente malintenzionato abbia modificato le autorizzazioni del contenitore per indebolirne il comportamento di sicurezza o per ottenere la persistenza.|
|**Anomalia di accesso ispezione**|Indica che le autorizzazioni di accesso di un account di archiviazione sono state ispezionate in modo insolito, rispetto alle attività recenti di questo account. Una possibile provocazione è che un utente malintenzionato ha eseguito la ricognizione per un attacco futuro.|
|**Anomalia di esplorazione dei dati**|Indica che i BLOB o i contenitori in un account di archiviazione sono stati enumerati in modo anomalo, rispetto alle attività recenti di questo account. Una possibile provocazione è che un utente malintenzionato ha eseguito la ricognizione per un attacco futuro.|

>[!NOTE]
>Advanced Threat Protection per archiviazione di Azure non è attualmente disponibile in Azure per enti pubblici e aree cloud sovrane.

Per altre informazioni sugli avvisi per l'archiviazione, vedere l'articolo [Advanced Threat Protection per archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection) ed esaminare la sezione avvisi di protezione.
