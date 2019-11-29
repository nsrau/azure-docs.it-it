---
title: Definizioni di criteri di Azure monitorate nel Centro sicurezza di Azure | Microsoft Docs
description: Questo articolo fornisce un elenco di definizioni di criteri di Azure che è possibile monitorare nel centro sicurezza di Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 8/22/2019
ms.author: memildin
ms.openlocfilehash: 07d45f7cafa09ef6c5341acd5ffd0914cc56e4fd
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559253"
---
# <a name="azure-security-policies-monitored-by-security-center"></a>Criteri di sicurezza di Azure monitorati dal centro sicurezza
Questo articolo fornisce un elenco di definizioni di criteri di Azure che è possibile monitorare nel centro sicurezza di Azure. Per altre informazioni sui criteri di sicurezza, vedere [Utilizzo dei criteri di sicurezza](tutorial-security-policy.md).

## <a name="available-security-policies"></a>Criteri di sicurezza disponibili

Per informazioni sui criteri predefiniti monitorati dal centro sicurezza, vedere la tabella seguente:

| Policy | Operazioni eseguite dai criteri |
| --- | --- |
|I log di diagnostica nei set di scalabilità di macchine virtuali devono essere abilitati|È consigliabile abilitare i log in modo che una traccia attività sia disponibile per l'analisi dopo un evento imprevisto o un compromesso.|
|Tutte le regole di autorizzazione eccetto RootManageSharedAccessKey devono essere rimosse dallo spazio dei nomi dell'hub eventi|I client di hub eventi di Azure non devono usare criteri di accesso a livello di spazio dei nomi che consentono di accedere a tutte le code e gli argomenti in uno spazio dei nomi. Per l'allineamento con il modello di sicurezza con privilegi minimi, è necessario creare criteri di accesso a livello di entità per le code e gli argomenti per consentire l'accesso solo all'entità specifica.|
|È necessario definire le regole di autorizzazione nell'entità dell'hub eventi|Controllare l'esistenza di regole di autorizzazione sulle entità di hub eventi per concedere l'accesso con privilegi minimi.|
|L'accesso agli account di archiviazione con configurazioni del firewall e della rete virtuale deve essere limitato|Controllare l'accesso alla rete senza restrizioni nelle impostazioni del firewall dell'account di archiviazione. Configurare le regole di rete in modo che solo le applicazioni dalle reti consentite possano accedere all'account di archiviazione. Per consentire le connessioni da client Internet o locali specifici, concedere l'accesso al traffico da reti virtuali di Azure specifiche o a intervalli di indirizzi IP Internet pubblici.|
|Controlla l'uso di ruoli di controllo degli accessi in base al ruolo personalizzati|Controllare i ruoli predefiniti, ad esempio "proprietario, collaboratore, lettore" invece dei ruoli di controllo degli accessi in base al ruolo (RBAC) personalizzati, che sono soggetti a errori. L'utilizzo di ruoli personalizzati viene considerato come un'eccezione e richiede una rigorosa revisione e modellazione dei rischi.|
|I log di diagnostica in analisi di flusso di Azure devono essere abilitati|Controllare l'abilitazione dei log e mantenerli per un massimo di un anno. Questa operazione crea percorsi attività per l'analisi quando si verifica un evento imprevisto di sicurezza o la rete viene compromessa.|
|Il trasferimento sicuro negli account di archiviazione deve essere abilitato|Requisiti di controllo del trasferimento sicuro nell'account di archiviazione. Il trasferimento sicuro è un'opzione che impone all'account di archiviazione di accettare richieste solo da connessioni sicure (HTTPS). L'uso di HTTPS assicura l'autenticazione tra il server e il servizio. Protegge inoltre i dati in transito da attacchi a livello di rete, ad esempio Man-in-the-Middle, intercettazione e Hijack della sessione.|
|È necessario eseguire il provisioning di Azure AD amministratore di SQL Server|Controllare il provisioning di un amministratore di Azure Active Directory (Azure AD) per SQL Server per abilitare l'autenticazione Azure AD. Azure AD autenticazione supporta la gestione semplificata delle autorizzazioni e la gestione centralizzata delle identità degli utenti del database e di altri servizi Microsoft.|
|Tutte le regole di autorizzazione eccetto RootManageSharedAccessKey devono essere rimosse dallo spazio dei nomi del bus di servizio|I client del bus di servizio di Azure non devono usare criteri di accesso a livello di spazio dei nomi che consentono di accedere a tutte le code e gli argomenti in uno spazio dei nomi Per eseguire l'allineamento con il modello di sicurezza con privilegi minimi, creare criteri di accesso a livello di entità per le code e gli argomenti per consentire l'accesso solo all'entità specifica.|
|I log di diagnostica nel bus di servizio devono essere abilitati|Controllare l'abilitazione dei log e mantenerli per un anno. Questa operazione crea percorsi attività per l'analisi quando si verifica un evento imprevisto di sicurezza o la rete viene compromessa.|
|È necessario impostare la proprietà ClusterProtectionLevel su EncryptAndSign in Service Fabric|Service Fabric offre tre livelli di protezione per la comunicazione da nodo a nodo che usa un certificato del cluster primario: None, Sign e EncryptAndSign. Impostare il livello di protezione per garantire che tutti i messaggi da nodo a nodo vengano crittografati e firmati digitalmente.|
|L'autenticazione client deve usare Azure Active Directory|Controllare l'uso dell'autenticazione client solo tramite Azure AD in Service Fabric.|
|I log di diagnostica nei servizi di ricerca devono essere abilitati|Controllare l'abilitazione dei log e mantenerli per un massimo di un anno. Questa operazione crea percorsi attività per l'analisi quando si verifica un evento imprevisto di sicurezza o la rete viene compromessa.|
|Devono essere abilitate solo connessioni sicure alla Cache Redis|Controllare l'abilitazione solo delle connessioni tramite SSL alla cache di Azure per Redis. L'utilizzo di connessioni protette garantisce l'autenticazione tra il server e il servizio. Protegge inoltre i dati in transito da attacchi a livello di rete, ad esempio Man-in-the-Middle, intercettazione e Hijack della sessione.|
|I log di diagnostica nelle app per la logica devono essere abilitati|Controllare l'abilitazione dei log e mantenerli per un massimo di un anno. Questa operazione crea percorsi attività per l'analisi quando si verifica un evento imprevisto di sicurezza o la rete viene compromessa.|
|I log di diagnostica in Key Vault devono essere abilitati|Controllare l'abilitazione dei log e mantenerli per un massimo di un anno. Questa operazione crea percorsi attività per l'analisi quando si verifica un evento imprevisto di sicurezza o la rete viene compromessa.|
|I log di diagnostica nell'hub eventi devono essere abilitati|Controllare l'abilitazione dei log e mantenerli per un massimo di un anno. Questa operazione crea percorsi attività per l'analisi quando si verifica un evento imprevisto di sicurezza o la rete viene compromessa.|
|I log di diagnostica in Azure Data Lake Store devono essere abilitati|Controllare l'abilitazione dei log e mantenerli fino a un anno. Questa operazione crea percorsi attività per l'analisi quando si verifica un evento imprevisto di sicurezza o la rete viene compromessa.|
|I log di diagnostica in Data Lake Analytics devono essere abilitati|Controllare l'abilitazione dei log e mantenerli per un massimo di un anno. Questa operazione crea percorsi attività per l'analisi quando si verifica un evento imprevisto di sicurezza o la rete viene compromessa.|
|È necessario eseguire la migrazione degli account di archiviazione alle nuove risorse di AzureRM|Usare Azure Resource Manager per gli account di archiviazione per fornire miglioramenti alla sicurezza. Le aree includono: <br>-Controllo di accesso più sicuro (RBAC)<br>-Controllo migliore<br>-Distribuzione e governance basate su Azure Resource Manager<br>-Accesso alle identità gestite<br>-Accesso a Azure Key Vault per i segreti<br>-Autenticazione basata su Azure AD<br>-Supporto per i tag e i gruppi di risorse per semplificare la gestione della sicurezza|
|È necessario eseguire la migrazione delle macchine virtuali a nuove risorse AzureRM|Usare Azure Resource Manager per le macchine virtuali per fornire miglioramenti alla sicurezza. Le aree includono: <br>-Controllo di accesso più sicuro (RBAC)<br>-Controllo migliore<br>-Distribuzione e governance basate su Azure Resource Manager<br>-Accesso alle identità gestite<br>-Accesso a Azure Key Vault per i segreti<br>-Autenticazione basata su Azure AD<br>-Supporto per i tag e i gruppi di risorse per semplificare la gestione della sicurezza|
|Le regole di avviso delle metriche devono essere configurate negli account batch|Controllare la configurazione delle regole di avviso delle metriche sugli account Azure Batch per abilitare la metrica necessaria.|
|I log di diagnostica negli account batch devono essere abilitati|Controllare l'abilitazione dei log e mantenerli per un massimo di un anno. Questa operazione crea percorsi attività per l'analisi quando si verifica un evento imprevisto di sicurezza o la rete viene compromessa.|
|La crittografia deve essere abilitata nelle variabili dell'account di automazione|È importante abilitare la crittografia degli Asset variabili dell'account di automazione di Azure quando si archiviano dati sensibili.|
|I log di diagnostica nei servizi app devono essere abilitati|Controlla l'abilitazione dei log di diagnostica nell'app. Questa operazione crea percorsi attività per l'analisi quando si verifica un evento imprevisto di sicurezza o la rete viene compromessa.|
|Transparent Data Encryption deve essere abilitata nei database SQL|Controlla lo stato di Transparent Data Encryption per i database SQL.|
|Il controllo di SQL Server deve essere abilitato|Controllare l'esistenza del controllo SQL a livello di server.|
|\[Preview]: monitorare il database SQL non crittografato nel centro sicurezza di Azure|Il Centro sicurezza di Azure monitora i database o i server SQL non crittografati come consigliato.|
|Anteprima \[]: monitorare il database SQL non controllato nel centro sicurezza di Azure|Il Centro sicurezza di Azure monitora i server e i database SQL che non hanno attivato il controllo SQL come consigliato.|
|\[Preview]: gli aggiornamenti di sistema devono essere installati nei computer|Il Centro sicurezza di Azure monitora gli aggiornamenti dei sistemi di sicurezza mancanti nei server come consigliato.|
|\[Preview]: controlla la crittografia BLOB mancante per gli account di archiviazione|Controllare gli account di archiviazione che non usano la crittografia BLOB. Questo vale solo per i tipi di risorse di archiviazione Microsoft, non per l'archiviazione di altri provider. Il Centro sicurezza di Azure monitora il possibile accesso just-in-time alla rete come consigliato.|
|\[Preview]: il controllo degli accessi di rete just-in-time deve essere applicato alle macchine virtuali|Il Centro sicurezza di Azure monitora il possibile accesso just-in-time alla rete come consigliato.|
|\[Preview]: i controlli applicazione adattivi devono essere abilitati nelle macchine virtuali|Il Centro sicurezza di Azure monitora la possibile configurazione whitelist dell'applicazione.|
|\[Preview]: i gruppi di sicurezza di rete mancanti per le macchine virtuali devono essere configurati|Il Centro sicurezza di Azure monitora i gruppi di sicurezza di rete con regole troppo permissive, come consigliato.|
|\[Preview]: è necessario correggere le vulnerabilità nella configurazione della sicurezza nei computer|Il Centro sicurezza di Azure monitora i server che non soddisfano la baseline configurata come consigliato.| 
|\[Preview]: è necessario installare Endpoint Protection nelle macchine virtuali|Il Centro sicurezza di Azure monitora i server in cui non è installato Microsoft System Center Endpoint Protection Agent come consigliato.|
|\[Preview]: la crittografia del disco deve essere applicata alle macchine virtuali|Il Centro sicurezza di Azure monitora le macchine virtuali in cui non è abilitata la crittografia del disco come consigliato.|
|\[Preview]: le vulnerabilità devono essere risolte da una soluzione di valutazione della vulnerabilità|Monitorare le vulnerabilità rilevate dalla soluzione di valutazione della vulnerabilità e le macchine virtuali che non hanno una soluzione di valutazione della vulnerabilità nel centro sicurezza di Azure come consigliato.|
|\[Preview]: monitorare l'applicazione Web non protetta nel centro sicurezza di Azure|Il Centro sicurezza di Azure monitora le applicazioni Web che non dispongono di protezione web application firewall come consigliato.|
|\[Preview]: la soluzione Endpoint Protection deve essere installata nelle macchine virtuali|Il Centro sicurezza di Azure monitora gli endpoint di rete per i quali non è stata consigliata la protezione firewall di nuova generazione.|
|\[Preview]: è necessario correggere le vulnerabilità nei database SQL|Monitorare i risultati dell'analisi della valutazione della vulnerabilità e consigliare come correggere le vulnerabilità del database.|
|\[Preview]: è necessario designare un massimo di 3 proprietari per la sottoscrizione|Si consiglia di designare fino a tre proprietari di sottoscrizioni per ridurre il rischio di violazione da parte di un proprietario compromesso.|
|\[Preview]: la sottoscrizione deve essere assegnata a più di un proprietario|Si consiglia di designare più di un proprietario della sottoscrizione per garantire la ridondanza dell'accesso di amministratore.|
|\[Preview]: l'autenticazione a più fattori deve essere abilitata per gli account con autorizzazioni proprietario per la sottoscrizione |È necessario abilitare l'autenticazione a più fattori per tutti gli account di sottoscrizione che dispongono delle autorizzazioni di proprietario per impedire una violazione di account o risorse.|
|\[Preview]: l'autenticazione a più fattori deve essere abilitata per gli account di sottoscrizione con autorizzazioni di scrittura|È necessario abilitare l'autenticazione a più fattori per tutti gli account di sottoscrizione che dispongono di autorizzazioni di scrittura per impedire la violazione di account o risorse.|
|\[Preview]: l'autenticazione a più fattori deve essere abilitata per gli account di sottoscrizione con autorizzazioni di lettura|È necessario abilitare l'autenticazione a più fattori per tutti gli account di sottoscrizione che dispongono di autorizzazioni di lettura per impedire la violazione di account o risorse.|
|\[Preview]: gli account deprecati con autorizzazioni proprietarie devono essere rimossi dalla sottoscrizione|Gli account deprecati che dispongono di autorizzazioni proprietario devono essere rimossi dalla sottoscrizione. Gli account deprecati sono stati bloccati dall'accesso.|
|\[Preview]: gli account deprecati devono essere rimossi dalla sottoscrizione|È necessario rimuovere dalle sottoscrizioni gli account deprecati. Gli account deprecati sono stati bloccati dall'accesso.|
|\[Preview]: gli account esterni con autorizzazioni proprietarie devono essere rimossi dalla sottoscrizione|Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione per impedire l'accesso alle autorizzazioni.|
|\[Preview]: gli account esterni con autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione|Gli account esterni che dispongono di autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione per impedire l'accesso non monitorato.|
|\[Preview]: gli account esterni con autorizzazioni di lettura devono essere rimossi dalla sottoscrizione|Gli account esterni che dispongono di autorizzazioni di lettura devono essere rimossi dalla sottoscrizione per impedire l'accesso non monitorato.|




## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come configurare i criteri di sicurezza nel Centro sicurezza. Per altre informazioni sul centro sicurezza, vedere gli articoli seguenti.

* [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md): informazioni su come pianificare e comprendere le considerazioni di progettazione nel centro sicurezza di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestire e rispondere agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitorare le soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md): informazioni su come monitorare l'integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): risposte alle domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](https://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure.

Per altre informazioni sui criteri di Azure, vedere informazioni su [criteri di Azure](../governance/policy/overview.md).
