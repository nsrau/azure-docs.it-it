---
title: Definizioni di criteri di Azure monitorate nel Centro sicurezza di Azure | Microsoft Docs
description: Definizioni di criteri di Azure monitorate nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: c89cb1aa-74e8-4ed1-980a-02a7a25c1a2f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: 9d9369afd36f64c27cd2222cab0de5912aa913de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60909197"
---
# <a name="azure-security-policies-monitored-by-security-center"></a>Criteri di sicurezza di Azure monitorati dal Centro sicurezza
Questo articolo fornisce un elenco di definizioni di criteri di Azure che è possibile monitorare nel Centro sicurezza di Azure. Per altre informazioni sui criteri di sicurezza, vedere [Utilizzo dei criteri di sicurezza](tutorial-security-policy.md).

## <a name="available-security-policies"></a>Criteri di sicurezza disponibili

Per altre informazioni sui criteri predefiniti che vengono monitorati dal Centro sicurezza, vedere la tabella seguente:

| Policy | Operazioni eseguite dai criteri |
| --- | --- |
|Controllo abilitazione di diagnostica di log in Azure Service Fabric e set di scalabilità di macchine virtuali|È consigliabile abilitare i log in modo che un itinerario di attività è disponibile per l'analisi dopo un evento imprevisto o compromissione.|
|Le regole di autorizzazione negli spazi dei nomi di hub eventi di controllo|I client di Azure hub eventi non devono usare un criterio di accesso a livello di spazio dei nomi che fornisce l'accesso a tutte le code e argomenti in uno spazio dei nomi. Per allineare con il modello di sicurezza con privilegi minimi, è necessario creare i criteri di accesso a livello di entità per code e argomenti per fornire l'accesso solo all'entità specifiche.|
|Controllo esistenza di regole di autorizzazione in entità di hub eventi|Controllare l'esistenza delle regole di autorizzazione sulle entità di hub eventi per concedere l'accesso con privilegi minimi.|
|Controlla l'accesso di rete senza restrizioni agli account di archiviazione|Controllare l'accesso alla rete senza restrizioni nelle impostazioni del firewall dell'account di archiviazione. Configurare le regole di rete in modo che solo le applicazioni delle reti consentite possono accedere all'account di archiviazione. Per consentire le connessioni da internet specifici o ai client in locale, concedere l'accesso per il traffico da specifiche reti virtuali di Azure o a internet pubblico indirizzo IP compreso.|
|Controlla l'uso di ruoli di controllo degli accessi in base al ruolo personalizzati|Ruoli predefiniti, ad esempio "Proprietario, collaboratore, lettore" invece di ruoli controllo degli accessi basata sui ruoli di accesso personalizzato, che sono soggette a errori di controllo. Utilizzo dei ruoli personalizzati viene considerata come un'eccezione e richiede revisione rigorosa e la modellazione delle minacce.|
|Controlla l'abilitazione dei log di diagnostica in Analisi di flusso di Azure|Abilitazione dei log di controllo e mantenerli per un massimo di un anno. Ciò consente di creare gli itinerari di attività per l'analisi quando si verifica un evento imprevisto della sicurezza o di rete è compromessa.|
|Controlla il trasferimento sicuro negli account di archiviazione|Requisiti di trasferimento sicuro nell'account di archiviazione di controllo. Il trasferimento sicuro è un'opzione che impone all'account di archiviazione di accettare richieste solo da connessioni sicure (HTTPS). Uso di HTTPS assicura l'autenticazione tra il server e il servizio. Inoltre protegge i dati in transito da attacchi di livello di rete, ad esempio man-in-the-middle, intercettazione e Hijack della sessione.|
|Il provisioning di un amministratore di Azure Active Directory per SQL Server di controllo|Controllo di provisioning di un amministratore di Azure Active Directory (Azure AD) per SQL Server abilitare l'autenticazione di Azure AD. Autenticazione di Azure AD supporta la gestione semplificata delle autorizzazioni e gestione centralizzata delle identità degli utenti di database e altri servizi Microsoft.|
|Controlla le regole di autorizzazione negli spazi dei nomi del bus di servizio|I client del Bus di servizio di Azure non devono usare un criterio di accesso a livello di spazio dei nomi che fornisce l'accesso a tutte le code e argomenti in uno spazio dei nomi. Per allineare con il modello di sicurezza con privilegi minimi, creare criteri di accesso a livello di entità per code e argomenti per fornire l'accesso solo all'entità specifiche.|
|Controlla l'abilitazione dei log di diagnostica nel bus di servizio|Abilitazione dei log di controllo e mantenerli iscrizione a un anno. Ciò consente di creare gli itinerari di attività per l'analisi quando si verifica un evento imprevisto della sicurezza o di rete è compromessa.|
|Controlla l'impostazione della proprietà ClusterProtectionLevel su EncryptAndSign in Service Fabric|Service Fabric offre tre livelli di protezione per la comunicazione da nodo a nodo che usa un certificato cluster primario: None, Sign ed EncryptAndSign. Impostare il livello di protezione per garantire che tutti i messaggi da nodo a nodo vengano crittografati e firmati digitalmente.|
|Controlla l'utilizzo di Azure Active Directory per l'autenticazione client in Service Fabric|Controllare l'uso dell'autenticazione del client solo tramite Azure AD in Service Fabric.|
|Controlla l'abilitazione dei log di diagnostica per il servizio di ricerca|Abilitazione dei log di controllo e mantenerli per un massimo di un anno. Ciò consente di creare gli itinerari di attività per l'analisi quando si verifica un evento imprevisto della sicurezza o di rete è compromessa.|
|Controllo abilitazione di solo connessioni protette da memorizzare nella Cache di Azure per Redis|Controllo attivazione solo di connessioni tramite protocollo SSL per Cache di Azure per Redis. Uso di connessioni protette assicura l'autenticazione tra il server e il servizio. Inoltre protegge i dati in transito da attacchi di livello di rete, ad esempio man-in-the-middle, intercettazione e Hijack della sessione.|
|Controlla l'abilitazione dei log di diagnostica nelle app per la logica|Abilitazione dei log di controllo e mantenerli per un massimo di un anno. Ciò consente di creare gli itinerari di attività per l'analisi quando si verifica un evento imprevisto della sicurezza o di rete è compromessa.|
|Controlla l'abilitazione dei log di diagnostica in Key Vault|Abilitazione dei log di controllo e mantenerli per un massimo di un anno. Ciò consente di creare gli itinerari di attività per l'analisi quando si verifica un evento imprevisto della sicurezza o di rete è compromessa.|
|Abilitazione dei log di diagnostica in hub eventi di controllo|Abilitazione dei log di controllo e mantenerli per un massimo di un anno. Ciò consente di creare gli itinerari di attività per l'analisi quando si verifica un evento imprevisto della sicurezza o di rete è compromessa.|
|Controlla l'abilitazione dei log di diagnostica in Azure Data Lake Store|Abilitazione dei log di controllo e le mantiene fino a un anno. Ciò consente di creare gli itinerari di attività per l'analisi quando si verifica un evento imprevisto della sicurezza o di rete è compromessa.|
|Controlla l'abilitazione dei log di diagnostica in Data Lake Analytics|Abilitazione dei log di controllo e mantenerli per un massimo di un anno. Ciò consente di creare gli itinerari di attività per l'analisi quando si verifica un evento imprevisto della sicurezza o di rete è compromessa.|
|Controlla l'uso degli account di archiviazione della versione classica|Usare Azure Resource Manager per gli account di archiviazione per fornire miglioramenti della sicurezza. incluse le seguenti: <br>-Controllo di accesso più avanzato (RBAC)<br>-Meglio il controllo<br>-Governance e distribuzione basata su Resource Manager azure<br>-Accesso alle identità gestite<br>-L'accesso ad Azure Key Vault per i segreti<br>-Autenticazione basata su Active Directory azure<br>-Supporto per tag e i gruppi di risorse per facilitare la gestione della protezione|
|Controlla l'uso delle macchine virtuali della versione classica|Usare Azure Resource Manager per le macchine virtuali per fornire miglioramenti della sicurezza.  incluse le seguenti: <br>-Controllo di accesso più avanzato (RBAC)<br>-Meglio il controllo<br>-Governance e distribuzione basata su Resource Manager azure<br>-Accesso alle identità gestite<br>-L'accesso ad Azure Key Vault per i segreti<br>-Autenticazione basata su Active Directory azure<br>-Supporto per tag e i gruppi di risorse per facilitare la gestione della protezione|
|Controlla la configurazione delle regole di avviso delle metriche per gli account Batch|Controllare la configurazione delle regole di avviso delle metriche per gli account Azure Batch per abilitare la metrica obbligatoria.|
|Controlla l'abilitazione dei log di diagnostica negli account Batch|Abilitazione dei log di controllo e mantenerli per un massimo di un anno. Ciò consente di creare gli itinerari di attività per l'analisi quando si verifica un evento imprevisto della sicurezza o di rete è compromessa.|
|Controlla l'abilitazione della crittografia delle variabili di account di automazione|È importante abilitare la crittografia dell'asset variabile dell'account di automazione di Azure quando si archiviano dati sensibili.|
|Abilitazione dei log di diagnostica nel servizio App di controllo|Controlla l'abilitazione dei log di diagnostica nell'app. Ciò consente di creare gli itinerari di attività per l'analisi quando si verifica un evento imprevisto della sicurezza o di rete è compromessa.|
|Controllare lo stato di Transparent Data Encryption|Controlla lo stato di Transparent Data Encryption per i database SQL.|
|Controllare le impostazioni di controllo a livello di Server SQL|Controllare l'esistenza di controllo a livello di server SQL.|
|\[Anteprima]: Monitorare i database SQL non crittografati nel Centro sicurezza di Azure|Centro sicurezza di Azure esegue il monitoraggio non crittografati istanze di SQL Server o database, come consigliato.|
|\[Anteprima]: Monitorare i database SQL non controllati nel Centro sicurezza di Azure|Centro sicurezza di Azure consente di monitorare SQL Server e database che non hanno il controllo SQL abilitato come consigliato.|
|\[Anteprima]: Monitorare gli aggiornamenti di sistema mancanti nel Centro sicurezza di Azure|Centro sicurezza di Azure consente di monitorare aggiornamenti di sistema di sicurezza mancanti nei server come consigliato.|
|\[Anteprima]: Controllare crittografia BLOB mancante per gli account di archiviazione|Controllare gli account di archiviazione che non usano la crittografia blob. Questo vale solo per tipi di risorse di archiviazione Microsoft e archiviazione non da altri provider. Centro sicurezza di Azure consente di monitorare l'accesso just-in-time possibili rete come consigliato.|
|\[Anteprima]: Monitorare l'accesso just-in-time di rete possibili nel Centro sicurezza di Azure|Centro sicurezza di Azure consente di monitorare l'accesso just-in-time possibili rete come consigliato.|
|\[Anteprima]: Monitorare il possibile elenco elementi consentiti dell'app nel Centro sicurezza di Azure|Centro sicurezza di Azure monitora configurazione elenco elementi consentiti le applicazioni.|
|\[Anteprima]: Monitorare gli accessi di rete permissivi nel Centro sicurezza di Azure|Monitoraggi di Centro sicurezza di rete i gruppi di sicurezza che dispongono di regole troppo permissive, come consigliato.|
|\[Anteprima]: Monitorare le vulnerabilità del sistema operativo nel Centro sicurezza di Azure|Centro sicurezza di Azure monitora i server che non soddisfano la baseline configurata come consigliato.| 
|\[Anteprima]: Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure|Centro sicurezza di Azure monitora i server che non hanno un agente di Microsoft System Center Endpoint Protection installato come consigliato.|
|\[Anteprima]: Monitorare i dischi delle macchine Virtuali non crittografati nel Centro sicurezza di Azure|Centro sicurezza di Azure consente di monitorare le macchine virtuali che non dispone di crittografia del disco abilitata come consigliato.|
|\[Anteprima]: Monitorare le vulnerabilità di macchine Virtuali nel Centro sicurezza di Azure|Monitorare le vulnerabilità rilevate dalla soluzione di valutazione della vulnerabilità e le macchine virtuali che non hanno una soluzione di valutazione della vulnerabilità nel Centro sicurezza di Azure, come consigliato.|
|\[Anteprima]: Monitorare le applicazioni Web non protette nel Centro sicurezza di Azure|I monitoraggi di Centro sicurezza di Azure le applicazioni web che non dispongono di protezione con firewall applicazione web, come consigliato.|
|\[Anteprima]: Monitorare gli endpoint di rete non protetti nel Centro sicurezza di Azure|Monitoraggi di Centro sicurezza di rete endpoint che non hanno la protezione tramite firewall di prossima generazione, come consigliato.|
|\[Anteprima]: Monitorare i risultati della valutazione della vulnerabilità di SQL nel Centro sicurezza di Azure|Valutazione della vulnerabilità di monitoraggio analizzare i risultati e consiglia di risolvere le vulnerabilità dei database.|
|\[Anteprima]: Controllare il numero massimo di proprietari per una sottoscrizione|È consigliabile designare fino a tre i proprietari di sottoscrizioni per ridurre il rischio di violazione da un proprietario compromesso.|
|\[Anteprima]: Controllare il numero minimo di proprietari per sottoscrizione|È consigliabile designare più di un proprietario della sottoscrizione per garantire l'accesso amministratore ridondanza.|
|\[Anteprima]: Controllare gli account con autorizzazioni di proprietario non abilitati per MFA in una sottoscrizione|Multi-factor authentication (MFA) deve essere abilitata per tutti gli account di sottoscrizione che dispone delle autorizzazioni di proprietario per evitare una violazione di account o risorse.|
|\[Anteprima]: Controllare gli account con autorizzazioni di scrittura non abilitati per MFA in una sottoscrizione|Multi-factor authentication deve essere abilitata per tutti gli account di sottoscrizione che dispongono delle autorizzazioni di scrittura per evitare una violazione di account o risorse.|
|\[Anteprima]: Controllare gli account con autorizzazioni di lettura non abilitati per MFA in una sottoscrizione|Multi-factor authentication deve essere abilitata per tutti gli account di sottoscrizione che hanno autorizzazioni di lettura per evitare una violazione di account o risorse.|
|\[Anteprima]: Controllare gli account deprecati con autorizzazioni di proprietario in una sottoscrizione|Gli account deprecati che dispongono delle autorizzazioni di proprietario devono essere rimosso dalla sottoscrizione. Gli account deprecati sono stati bloccati dall'accesso.|
|\[Anteprima]: Controllare account deprecati in una sottoscrizione|È necessario rimuovere dalle sottoscrizioni gli account deprecati. Gli account deprecati sono stati bloccati dall'accesso.|
|\[Anteprima]: Controllare gli account esterni con autorizzazioni di proprietario su una sottoscrizione|Gli account esterni con autorizzazioni di proprietario devono essere rimosso dalla sottoscrizione per impedire l'accesso le autorizzazioni.|
|\[Anteprima]: Controllare gli account esterni con autorizzazioni di scrittura su una sottoscrizione|Gli account esterni dotati di scrivere le autorizzazioni devono essere rimossi dalla sottoscrizione per impedire l'accesso non monitorato.|
|\[Anteprima]: Controllare gli account esterni con autorizzazioni di lettura su una sottoscrizione|Gli account esterni che dispongano delle autorizzazioni di lettura devono essere rimossi dalla sottoscrizione per impedire l'accesso non monitorato.|




## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come configurare i criteri di sicurezza nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli articoli seguenti.

* [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md): Informazioni su come pianificare e comprendere le considerazioni di progettazione nel Centro sicurezza di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): Informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md): Informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): Risposte alle domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](https://blogs.msdn.com/b/azuresecurity/): Post di blog sulla sicurezza e sulla conformità di Azure.

Per altre informazioni sui criteri di Azure, vedere [quali sono i criteri di Azure?](../governance/policy/overview.md).
