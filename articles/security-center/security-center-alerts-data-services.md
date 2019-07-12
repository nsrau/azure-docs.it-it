---
title: Per i servizi dati nel Centro sicurezza Azure rilevamento delle minacce | Microsoft Docs
description: Questo argomento presenta avvisi di servizi di dati disponibili nel Centro sicurezza di Azure.
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
ms.author: monhaber
ms.openlocfilehash: 87cfd2769e473d26c2dcae1b7b418f6fb1739915
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626280"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Rilevamento delle minacce per i servizi dati nel Centro sicurezza di Azure

 Il Centro sicurezza analizza i log dei servizi di archiviazione di dati e Attiva avvisi quando rileva una minaccia alle risorse di dati. In questo argomento vengono elencati gli avvisi generati dal Centro sicurezza per i servizi seguenti:

* [Database SQL di Azure e SQL Data Warehouse](#data-sql)
* [Archiviazione di Azure](#azure-storage)

## Database SQL di Azure e SQL Data Warehouse <a name="data-sql"></a>

Tenta di minacce SQL rilevamento rileva attività anomale che indicano insoliti e potenzialmente dannosi di accesso o exploit dei database. Il Centro sicurezza analizza i log di controllo SQL e viene eseguito in modo nativo nel motore di SQL.

|Avviso|Descrizione|
|---|---|
|**Vulnerabilità agli attacchi SQL Injection**|Un'applicazione ha generato un'istruzione SQL non corretta nel database. Questo può indicare una possibile vulnerabilità ad attacchi SQL injection. Ci sono due possibili motivi per la generazione di un'istruzione non corretta: Un difetto nel codice dell'applicazione, costruita l'istruzione SQL non corretta. In alternativa, il codice dell'applicazione o stored procedure non correggono l'input utente quando si costruisce l'istruzione SQL non corretta, che può essere sfruttata per attacchi SQL Injection.|
|**Potenziale attacco SQL injection**|Si è verificato un exploit attivo rispetto a un'applicazione identificata vulnerabile agli attacchi SQL injection. Ciò significa che un utente malintenzionato sta cercando di inserire istruzioni SQL dannose usando codice dell'applicazione vulnerabile o stored procedure.|
|**Accesso da posizione insolita**|È stata apportata una modifica nel criterio di accesso a SQL server, in cui un utente ha effettuato l'accesso a SQL server da una posizione geografica insolita. In alcuni casi, l'avviso rileva un'azione legittima (una nuova applicazione o la manutenzione da parte dello sviluppatore). In altri casi, l'avviso rileva un'azione dannosa (da parte di un ex dipendente o un utente malintenzionato esterno).|
|**Accesso da entità di sicurezza sconosciuta**|È stata apportata una modifica nel criterio di accesso a SQL server: un utente ha effettuato l'accesso a SQL server usando un'entità di sicurezza insolita (utente SQL). In alcuni casi, l'avviso rileva un'azione legittima (nuova applicazione o manutenzione da parte dello sviluppatore). In altri casi, l'avviso rileva un'azione dannosa (da parte di un ex dipendente o un utente malintenzionato esterno).|
|**Accesso da un'applicazione potenzialmente dannosa**|Un'applicazione potenzialmente dannosa è stata utilizzata per accedere al database. In alcuni casi, l'avviso rileva test di penetrazione in corso. In altri casi, l'avviso rileva un attacco mediante strumenti comuni di attacco.|
|**Credenziali SQL attacchi di forza bruta**|Si è verificati un numero elevato anomalo di accessi non riusciti con credenziali diverse. In alcuni casi, l'avviso rileva test di penetrazione in corso. In altri casi, l'avviso rileva un attacco di forza bruta.|

Per altre informazioni su SQL threat detection avvisi, vedere[rilevamento minacce del Database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)ed esaminare la sezione avvisi di rilevamento delle minacce. Vedere anche [Centro sicurezza di Azure come consente di visualizzare un ad attacco informatico](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/) per visualizzare un esempio di come il Centro sicurezza consente di individuare un attacco rilevamento di attività SQL dannoso.

## Archiviazione di Azure<a name="azure-storage"></a>

>[!NOTE]
> Advanced Threat Protection per l'archiviazione di Azure è attualmente disponibile per l'archiviazione Blob solo. 

Advanced Threat Protection per Archiviazione di Azure offre un livello aggiuntivo di intelligence di sicurezza in grado di rilevare tentativi insoliti e potenzialmente dannosi di accesso o sfruttamento degli account di archiviazione. Questo livello di protezione dati consente di affrontare le minacce senza dover essere esperti della sicurezza e gestione dei sistemi di monitoraggio di sicurezza.

Il Centro sicurezza analizza i log di diagnostica di lettura, scrittura e le richieste di eliminazione nell'archivio Blob per rilevare le minacce e Attiva avvisi quando si verificano le anomalie nelle attività. Per altre informazioni, vedere [configurare la registrazione di archiviazione Analitica](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging) per altre informazioni.

> [!div class="mx-tableFixed"]

|Avviso|Descrizione|
|---|---|
|**Anomalie accesso posizione insolita**|Analisi del traffico di rete campionati visualizzano rilevato una comunicazione in uscita anomala Remote Desktop Protocol (RDP) che hanno origine da una risorsa nella distribuzione. Questa attività è considerata anomala per questo ambiente e potrebbe indicare che la risorsa sia stata compromessa e viene ora usato per l'endpoint RDP esterni di attacchi di forza bruta. Si noti che in seguito a questo tipo di attività l'IP può venire contrassegnato come dannoso dalle entità esterne.|
|**Anomalie di accesso dell'applicazione**|Indica che un'applicazione insolita è accedere a questo account di archiviazione. Una causa possibile è che un utente malintenzionato ha accesso l'account di archiviazione tramite una nuova applicazione.|
|**Anomalie l'accesso anonimo**|Indica che si verifica un cambiamento nel criterio di accesso a un account di archiviazione. Ad esempio, l'account ha avuto accesso in modo anonimo (senza alcuna autenticazione), che non è previsto confrontato con il modello di accesso recente su questo account. Una causa possibile è che un utente malintenzionato ha sfruttato l'accesso in lettura pubblico per un contenitore che contiene BLOB di archiviazione.|
|**Anomalie esfiltrazione dei dati**|Indica che una quantità insolitamente elevata di dati è stato estratto rispetto all'attività recente su questo contenitore di archiviazione. Una causa possibile è che un utente malintenzionato ha estratto una grande quantità di dati da un contenitore che contiene BLOB di archiviazione.|
|**Anomalie impreviste delete**|Indica che si è verificato uno o più operazioni di eliminazione imprevista in un account di archiviazione, rispetto all'attività recente su questo account. Una causa possibile è che un utente malintenzionato ha eliminato i dati dall'account di archiviazione.|
|**Carica pacchetto del servizio Cloud di Azure**|Indica che un pacchetto di servizio Cloud di Azure (file con estensione cspkg) sia stato caricato in un account di archiviazione in modo anomalo, rispetto all'attività recente su questo account. Una causa possibile è che un utente malintenzionato ha stato preparazione alla distribuzione di codice dannoso dall'account di archiviazione a un servizio cloud di Azure.|
|**Anomalie di accesso di autorizzazione**|Indica che le autorizzazioni di accesso di questo contenitore di archiviazione sono state modificate in modo anomalo. Possibile causa è che un utente malintenzionato ha modificato le autorizzazioni del contenitore per ridurre il livello relativo comportamento di sicurezza o per ottenere la persistenza.|
|**Anomalie di accesso di ispezione**|Indica che sono state controllate le autorizzazioni di accesso di un account di archiviazione in modo anomalo, rispetto all'attività recente su questo account. Una causa possibile è che un utente malintenzionato ha eseguito l'esplorazione per eventuali attacchi futuri.|
|**Anomalie di esplorazione dei dati**|Indica che i BLOB o contenitori in un account di archiviazione sono stati enumerati in modo anomalo, rispetto all'attività recente su questo account. Una causa possibile è che un utente malintenzionato ha eseguito l'esplorazione per eventuali attacchi futuri.|

>[!NOTE]
>Advanced Threat Protection per l'archiviazione di Azure non è attualmente disponibile in Azure per enti pubblici e aree del cloud con sovranità.

Per altre informazioni sugli avvisi per l'archiviazione, vedere la [Advanced Threat Protection per l'archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection) articolo ed esaminare la sezione avvisi di protezione.
