---
title: Definizioni di criteri di Azure monitorate nel Centro sicurezza di Azure | Microsoft Docs
description: Definizioni di criteri di Azure monitorate nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: c89cb1aa-74e8-4ed1-980a-02a7a25c1a2f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2018
ms.author: rkarlin
ms.openlocfilehash: e76464e294a3378bf3a275ec4fe7ccbd87c09475
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53011641"
---
# <a name="azure-security-policies-monitored-by-azure-security-center"></a>Criteri di sicurezza di Azure monitorati dal Centro sicurezza di Azure
Questo articolo offre un elenco di definizioni di criteri di Azure che possono essere monitorate nel Centro sicurezza.

## <a name="available-security-policies"></a>Criteri di sicurezza disponibili

Per informazioni sui criteri predefiniti che vengono monitorati dal Centro sicurezza, vedere la tabella seguente:

| Criterio | Operazioni eseguite dai criteri |
| --- | --- |
|Abilitazione dell'audit dei log di diagnostica in Service Fabric e nei set di scalabilità di macchine virtuali|È consigliabile abilitare i log per poter ricreare la traccia delle attività quando sono necessarie indagini in caso di evento imprevisto o compromissione.|
|Controlla le regole di autorizzazione negli spazi dei nomi di Hub eventi|I client di Hub eventi non devono usare un criterio di accesso a livello di spazio dei nomi che assicura l'accesso a tutte le code e gli argomenti in uno spazio dei nomi. Per allinearsi al modello di sicurezza con privilegi minimi, è opportuno creare criteri di accesso a livello di entità per code e argomenti per assicurare l'accesso solo all'entità specifica.|
|Controlla l'esistenza di regole di autorizzazione nelle entità di Hub eventi|Controlla l'esistenza di regole di autorizzazione nelle entità di Hub eventi per concedere l'accesso con privilegi minimi.|
|Controlla l'accesso di rete senza restrizioni agli account di archiviazione|Controllare l'accesso alla rete senza restrizioni nelle impostazioni del firewall dell'account di archiviazione. Configurare invece regole di rete in modo che l'account di archiviazione sia accessibile solo alle applicazioni provenienti da reti consentite. Per consentire connessioni da specifici client Internet o locali, è possibile concedere l'accesso al traffico proveniente da determinate reti virtuali di Azure o diretto verso intervalli di indirizzi IP Internet pubblici.|
|Controlla l'uso di ruoli di controllo degli accessi in base al ruolo personalizzati|Controlla i ruoli predefiniti, ad esempio "Proprietario, Collaboratore, Lettore" invece che i ruoli Controllo degli accessi in base al ruolo personalizzati, che sono soggetti a errori. L'uso di ruoli personalizzati è considerato un'eccezione e richiede una revisione rigorosa e la modellazione delle minacce.|
|Controlla l'abilitazione dei log di diagnostica in Analisi di flusso di Azure|Controlla l'abilitazione dei log e li conserva per un periodo massimo di un anno. Ciò consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa.|
|Controlla il trasferimento sicuro negli account di archiviazione|Requisito di controllo del trasferimento sicuro nell'account di archiviazione. Il trasferimento sicuro è un'opzione che impone all'account di archiviazione di accettare richieste solo da connessioni sicure (HTTPS). L'uso di HTTPS garantisce l'autenticazione tra il server e il servizio e protegge i dati in transito dagli attacchi a livello rete, come attacchi man-in-the-middle, eavesdropping e hijack della sessione.|
|Controlla il provisioning di un amministratore di Azure Active Directory per SQL Server|Controlla il provisioning di un amministratore di Azure Active Directory per il server SQL per abilitare l'autenticazione di Azure AD. Questo tipo di autenticazione consente una gestione semplificata delle autorizzazioni e una gestione centralizzata delle identità degli utenti di database e di altri servizi Microsoft.|
|Controlla le regole di autorizzazione negli spazi dei nomi del bus di servizio|I client del bus di servizio non devono usare un criterio di accesso a livello di spazio dei nomi che assicura l'accesso a tutte le code e gli argomenti in uno spazio dei nomi. Per allinearsi al modello di sicurezza con privilegi minimi, è opportuno creare criteri di accesso a livello di entità per code e argomenti per assicurare l'accesso solo all'entità specifica.|
|Controlla l'abilitazione dei log di diagnostica nel bus di servizio|Controlla l'abilitazione dei log e li conserva per un periodo massimo di un anno. Ciò consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa.|
|Controlla l'impostazione della proprietà ClusterProtectionLevel su EncryptAndSign in Service Fabric|Service Fabric offre tre livelli di protezione (None, Sign ed EncryptAndSign) per la comunicazione da nodo a nodo mediante un certificato cluster primario. Impostare il livello di protezione per garantire che tutti i messaggi da nodo a nodo vengano crittografati e firmati digitalmente.| 
|Controlla l'utilizzo di Azure Active Directory per l'autenticazione client in Service Fabric|Controlla l'utilizzo dell'autenticazione client solo tramite Azure Active Directory in Service Fabric| 
|Controlla l'abilitazione dei log di diagnostica per il servizio di ricerca|Controlla l'abilitazione dei log e li conserva per un periodo massimo di un anno. Ciò consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa.| 
|Controllare l'abilitazione delle sole connessioni sicure a Cache Redis|Controllare l'abilitazione delle sole connessioni tramite SSL a Cache Redis. L'uso di connessioni sicure garantisce l'autenticazione tra il server e il servizio e protegge i dati in transito dagli attacchi a livello rete, come attacchi man-in-the-middle, eavesdropping e hijack della sessione| 
|Controlla l'abilitazione dei log di diagnostica nelle app per la logica|Controlla l'abilitazione dei log e li conserva per un periodo massimo di un anno. Ciò consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa.| 
|Controlla l'abilitazione dei log di diagnostica in Key Vault|Controlla l'abilitazione dei log e li conserva per un periodo massimo di un anno. Ciò consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa.|
|Controlla l'abilitazione dei log di diagnostica in Hub eventi|Controlla l'abilitazione dei log e li conserva per un periodo massimo di un anno. Ciò consente la ricreazione della traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa.| 
|Controlla l'abilitazione dei log di diagnostica in Azure Data Lake Store|Controlla l'abilitazione dei log e li conserva per un periodo massimo di un anno. Ciò consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa.| 
|Controlla l'abilitazione dei log di diagnostica in Data Lake Analytics|Controlla l'abilitazione dei log e li conserva per un periodo massimo di un anno. Ciò consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa.| 
|Controlla l'uso degli account di archiviazione della versione classica|Usare la versione di Azure Resource Manager per gli account di archiviazione per fornire funzionalità di sicurezza migliorate quali controllo di accesso (Controllo degli accessi in base al ruolo) più avanzato, controllo migliore, distribuzione e governance basate su Azure Resource Manager, accesso alle identità gestite, accesso all'insieme di credenziali delle chiavi per i segreti, autenticazione basata su Azure AD e supporto di tag e gruppi di risorse per una gestione della sicurezza semplificata.| 
|Controlla l'uso delle macchine virtuali della versione classica|Usare la versione di Azure Resource Manager per le macchine virtuali per fornire funzionalità di sicurezza migliorate quali controllo di accesso (Controllo degli accessi in base al ruolo) più avanzato, controllo migliore, distribuzione e governance basate su Azure Resource Manager, accesso alle identità gestite, accesso all'insieme di credenziali delle chiavi per i segreti, autenticazione basata su Azure AD e supporto di tag e gruppi di risorse per una gestione della sicurezza semplificata.| 
|Controlla la configurazione delle regole di avviso delle metriche per gli account Batch|Controlla la configurazione delle regole di avviso delle metriche per l'account Batch per abilitare la metrica richiesta.| 
|Controlla la configurazione delle regole di avviso delle metriche per gli account Batch|Controlla la configurazione delle regole di avviso delle metriche per l'account Batch per abilitare la metrica richiesta.| 
|Controlla l'abilitazione dei log di diagnostica negli account Batch|Controlla l'abilitazione dei log e li conserva per un periodo massimo di un anno. Ciò consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa.| 
|Controlla l'abilitazione della crittografia delle variabili dell'account di Automazione|È importante abilitare la crittografia degli asset della variabile dell'account di Automazione quando si archiviano dati sensibili.| 
|Controlla l'abilitazione dei log di diagnostica in Servizi app|Controlla l'abilitazione dei log di diagnostica nell'app. Ciò consente di ricreare la traccia delle attività per scopi di analisi se si verifica un evento imprevisto della sicurezza o la rete viene compromessa.| 
|Controllare lo stato di Transparent Data Encryption|Controlla lo stato di Transparent Data Encryption per i database SQL.| 
|Controlla le impostazioni di controllo a livello del server SQL|Controlla l'esistenza di Controllo SQL a livello del server.| 
|[Anteprima]: Monitorare i database SQL non crittografati nel Centro sicurezza di Azure|I database o i server SQL non crittografati verranno monitorati dal Centro sicurezza di Azure che invierà i consigli corrispondenti.| 
|[Anteprima]: Monitorare i database SQL non controllati nel Centro sicurezza di Azure|I server e i database in cui non è abilitato il servizio di controllo SQL verranno monitorati dal Centro sicurezza di Azure che invierà i consigli corrispondenti.| 
|[Anteprima]: Monitorare gli aggiornamenti di sistema mancanti nel Centro sicurezza di Azure|Gli aggiornamenti di sistema per la sicurezza nei server verranno monitorati dal Centro sicurezza di Azure che invierà i consigli corrispondenti.| 
|[Anteprima]: Controllare crittografia BLOB mancante per gli account di archiviazione|Questo criterio controlla gli account di archiviazione senza crittografia BLOB. Si applica solo ai tipi di risorsa Microsoft.Storage, non agli altri provider di archiviazione. I possibili accessi JIT alla rete verranno monitorati dal Centro sicurezza di Azure che invierà i consigli corrispondenti.| 
|[Anteprima]: Monitorare i possibili accessi JIT alla rete nel Centro sicurezza di Azure|I possibili accessi JIT alla rete verranno monitorati dal Centro sicurezza di Azure che invierà i consigli corrispondenti.| 
|[Anteprima]: Monitorare il possibile elenco elementi consentiti dell'app nel Centro sicurezza di Azure|La possibile configurazione dell'elenco elementi consentiti dell'applicazione verrà monitorata dal Centro sicurezza di Azure.| 
|[Anteprima]: Monitorare gli accessi di rete permissivi nel Centro sicurezza di Azure|I gruppi di sicurezza di rete con regole troppo permissive verranno monitorati dal Centro sicurezza di Azure che invierà i consigli corrispondenti.| 
|[Anteprima]: Monitorare le vulnerabilità del sistema operativo nel Centro sicurezza di Azure|I server che non soddisfano la baseline configurata verranno monitorati dal Centro sicurezza di Azure che invierà i consigli corrispondenti.| 
|[Anteprima]: Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure|I server in cui non è installato un agente di Endpoint Protection verranno monitorati dal Centro sicurezza di Azure che invierà i consigli corrispondenti.| 
|[Anteprima]: Monitorare i dischi di macchine virtuali non crittografati nel Centro sicurezza di Azure|Le macchine virtuali per cui non è abilitata la crittografia dei dischi verranno monitorate dal Centro sicurezza di Azure che invierà consigli.| 
|[Anteprima]: Monitorare le vulnerabilità delle macchine virtuali nel Centro sicurezza di Azure|Monitora le vulnerabilità rilevate dalla soluzione Valutazione della vulnerabilità e le macchine virtuali senza una soluzione Valutazione della vulnerabilità nel Centro sicurezza di Azure che invierà i consigli corrispondenti.| 
|[Anteprima]: Monitorare le applicazioni Web non protette nel Centro sicurezza di Azure|Le applicazioni Web senza la protezione di un web application firewall verranno monitorate dal Centro sicurezza di Azure che invierà i consigli corrispondenti.| 
|[Anteprima]: Monitorare gli endpoint di rete non protetti nel Centro sicurezza di Azure|Gli endpoint rete senza la protezione di un firewall di nuova generazione verranno monitorati dal Centro sicurezza di Azure che invierà i consigli corrispondenti.| 
|[Anteprima]: Monitorare i risultati della valutazione della vulnerabilità di SQL nel Centro sicurezza di Azure|Consente di monitorare i consigli e i risultati dell'analisi della valutazione della vulnerabilità per informazioni su come correggere le vulnerabilità del database.| 
|[Anteprima]: Controllare il numero massimo di proprietari per una sottoscrizione|È consigliabile designare fino a 3 proprietari della sottoscrizione in modo da ridurre la probabilità di violazione da parte di un proprietario compromesso.| 
|[Anteprima]: Controllare il numero minimo di proprietari per sottoscrizione|È consigliabile designare più di un proprietario di sottoscrizione per assicurare la ridondanza dell'accesso amministratore.| 
|[Anteprima]: Controllare gli account con autorizzazioni di proprietario non abilitati per MFA in una sottoscrizione|È necessario abilitare Multi-Factor Authentication (MFA) per tutti gli account della sottoscrizione con autorizzazioni di proprietario per evitare una violazione di account o risorse.| 
|[Anteprima]: Controllare gli account con autorizzazioni di scrittura non abilitati per MFA in una sottoscrizione|È necessario abilitare Multi-Factor Authentication (MFA) per tutti gli account della sottoscrizione con privilegi di scrittura per evitare una violazione di account o risorse.| 
|[Anteprima]: Controllare gli account con autorizzazioni di lettura non abilitati per MFA in una sottoscrizione|È necessario abilitare Multi-Factor Authentication (MFA) per tutti gli account della sottoscrizione con privilegi di lettura per evitare una violazione di account o risorse.| 
|[Anteprima]: Controllare gli account deprecati con autorizzazioni di proprietario in una sottoscrizione|Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione. Gli account deprecati sono account a cui è stato bloccato l'accesso.| 
|[Anteprima]: Controllare account deprecati in una sottoscrizione|È necessario rimuovere dalle sottoscrizioni gli account deprecati. Gli account deprecati sono account a cui è stato bloccato l'accesso.| 
|[Anteprima]: Controllare gli account esterni con autorizzazioni di proprietario su una sottoscrizione|È necessario rimuovere dalla sottoscrizione gli account esterni con autorizzazioni di proprietario in modo da evitare l'accesso non monitorato.| 
|[Anteprima]: Controllare gli account esterni con autorizzazioni di scrittura su una sottoscrizione|È necessario rimuovere dalla sottoscrizione gli account esterni con privilegi di scrittura in modo da evitare l'accesso non monitorato.| 
|[Anteprima]: Controllare gli account esterni con autorizzazioni di lettura su una sottoscrizione|È necessario rimuovere dalla sottoscrizione gli account esterni con privilegi di lettura in modo da evitare l'accesso non monitorato.| 




## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come configurare i criteri di sicurezza nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli articoli seguenti:

* [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md): Informazioni sulla pianificazione e considerazioni di progettazione sul Centro sicurezza di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): Informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md): Informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): Risposte alle domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): Post di blog sulla sicurezza e sulla conformità di Azure.

Per altre informazioni su Criteri di Azure, vedere [Informazioni su Criteri di Azure](../azure-policy/azure-policy-introduction.md).
