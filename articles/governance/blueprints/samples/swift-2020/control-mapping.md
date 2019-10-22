---
title: Esempio - Progetto SWIFT CSP-CSCF v2020 - Mapping dei controlli
description: Mapping dei controlli dell'esempio di progetto SWIFT CSP-CSCF v2020. Ogni controllo viene mappato a uno o più criteri di Azure che assistono nella valutazione.
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/24/2019
ms.topic: sample
ms.service: blueprints
ms.openlocfilehash: a326a5b323999e97b4d015403e36eb97dfc8483f
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248816"
---
# <a name="control-mapping-of-the-swift-csp-cscf-v2020-blueprint-sample"></a>Mapping dei controlli dell'esempio di progetto SWIFT CSP-CSCF v2020

L'articolo seguente descrive il mapping del progetto Azure Blueprints SWIFT CSP-CSCF v2020 con i controlli SWIFT CSP-CSCF v2020. Per altre informazioni sui controlli, vedere [SWIFT CSP-CSCF v2020](https://www.swift.com/myswift/customer-security-programme-csp).

I mapping seguenti fanno riferimento ai controlli **SWIFT CSP-CSCF v2020**. Usare la barra di spostamento a destra per passare direttamente a uno specifico mapping. Molti controlli mappati vengono implementati con un'iniziativa di [Criteri di Azure](../../../policy/overview.md). Per esaminare l'iniziativa completa, aprire **Criteri** nel portale di Azure e selezionare la pagina **Definizioni**. Trovare e selezionare l'iniziativa dei criteri predefinita **\[Anteprima\]: Verifica controlli SWIFT CSP-CSCF v2020 e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo**.

> [!IMPORTANT]
> Ogni controllo tra quelli riportati di seguito è associato a una o più definizioni di [Criteri di Azure](../../../policy/overview.md). Questi criteri possono aiutare a [valutare la conformità](../../../policy/how-to/get-compliance-data.md) con il controllo. In molti casi tuttavia non si tratta di una corrispondenza 1:1 o completa tra un controllo e uno o più criteri. Di per sé, **Conforme** in Criteri di Azure si riferisce solo ai criteri stessi e non garantisce che l'utente sia completamente conforme a tutti i requisiti di un controllo. Inoltre, in questo momento lo standard di conformità include controlli che non vengono gestiti da alcuna definizione di Criteri di Azure. La conformità in Criteri di Azure è quindi solo una visualizzazione parziale dello stato di conformità generale. Le associazioni tra i controlli e le definizioni di Criteri di Azure per questo esempio di progetto di conformità possono cambiare nel tempo. Per visualizzare la cronologia delle modifiche, vedere la [cronologia dei commit di GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/swift-2020/control-mapping.md).

## <a name="12-and-51-account-management"></a>1.2 e 5.1 Gestione degli account

Questo progetto consente di esaminare gli account che potrebbero non conformi ai requisiti di gestione degli account dell'organizzazione. Il progetto assegna definizioni di [Criteri di Azure](../../../policy/overview.md) che controllano gli account esterni con autorizzazioni di lettura, scrittura e proprietario per una sottoscrizione e gli account deprecati. Verificando gli account controllati da questi criteri, è possibile intervenire per assicurarsi che i requisiti di gestione degli account siano soddisfatti.

- Gli account deprecati devono essere rimossi dalla sottoscrizione
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di lettura devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione

## <a name="26-51-64-and-65a-account-management--role-based-schemes"></a>2.6, 5.1, 6.4 e 6.5A Gestione degli account | Schemi basati sui ruoli

Azure implementa il [controllo degli accessi in base al ruolo](../../../../role-based-access-control/overview.md) per consentire di gestire chi ha accesso alle risorse di Azure. Usando il portale di Azure, è possibile verificare chi ha accesso alle risorse di Azure e le relative autorizzazioni. Questo progetto assegna inoltre definizioni di [Criteri di Azure](../../../policy/overview.md) per controllare l'uso dell'autenticazione di Azure Active Directory per istanze di SQL Server e Service Fabric. L'uso dell'autenticazione di Azure Active Directory consente una gestione semplificata delle autorizzazioni e una gestione centralizzata delle identità degli utenti di database e di altri servizi Microsoft. Questo progetto assegna inoltre una definizione di Criteri di Azure per controllare l'uso di regole personalizzate del controllo degli accessi in base al ruolo. Identificando dove vengono implementate regole personalizzate del controllo degli accessi in base al ruolo, è possibile verificare l'esigenza e la corretta implementazione, perché tali regole sono soggette a errore.

- È consigliabile effettuare il provisioning di un amministratore di Azure Active Directory per SQL Server
- Controlla l'uso di ruoli di controllo degli accessi in base al ruolo personalizzati
- I cluster di Service Fabric deve usare solo Azure Active Directory per l'autenticazione client

## <a name="29a--account-management--account-monitoring--atypical-usage"></a>2.9A Gestione degli account | Monitoraggio degli account/utilizzo atipico

L'accesso JIT alle macchine virtuali blocca il traffico in ingresso alle macchine virtuali di Azure, riducendo l'esposizione agli attacchi e al tempo stesso offrendo un facile accesso per connettersi alle macchine virtuali quando necessario. Tutte le richieste JIT di accesso alle macchine virtuali vengono registrate nel Log attività allo scopo di monitorare l'eventuale utilizzo atipico. Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di monitorare le macchine virtuali in grado di supportare l'accesso JIT ma che non sono state ancora configurate.

- Alle macchine virtuali deve essere applicato il controllo di accesso alla rete JIT

## <a name="13-51-and-64-separation-of-duties"></a>1.3, 5.1 e 6.4 Separazione dei compiti

La presenza di un solo proprietario di sottoscrizioni di Azure non consente la ridondanza amministrativa. Al contrario, la presenza di troppi proprietari di sottoscrizioni di Azure può aumentare la probabilità che si verifichi una violazione tramite un account di proprietario compromesso. Questo progetto consente di mantenere un numero appropriato di proprietari di sottoscrizioni di Azure assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che controllano questo numero. Questo progetto assegna anche definizioni di Criteri di Azure che consentono di controllare l'appartenenza del gruppo Administrators nelle macchine virtuali Windows. La gestione delle autorizzazioni dei proprietari di sottoscrizioni e degli amministratori di macchine virtuali consente di implementare la separazione appropriata dei compiti.

- Per la sottoscrizione devono essere designati al massimo 3 proprietari
- Mostra i risultati del controllo dalle macchine virtuali Windows in cui il gruppo Administrators non contiene tutti i membri specificati
- Distribuisci i prerequisiti per controllare le macchine virtuali Windows in cui il gruppo Administrators non contiene tutti i membri specificati
- Alla sottoscrizione deve essere assegnato più di un proprietario

## <a name="13-51-and-64-least-privilege--review-of-user-privileges"></a>1.3, 5.1 e 6.4 Privilegi minimi | Revisione dei privilegi degli utenti

Azure implementa il [controllo degli accessi in base al ruolo](../../../../role-based-access-control/overview.md) per consentire di gestire chi ha accesso alle risorse di Azure. Usando il portale di Azure, è possibile verificare chi ha accesso alle risorse di Azure e le relative autorizzazioni. Questo progetto assegna definizioni di [Criteri di Azure](../../../policy/overview.md) per controllare gli account la cui verifica dovrebbe essere prioritaria. L'esame di questi indicatori di account è utile per verificare l'implementazione dei controlli dei privilegi minimi.

- Per la sottoscrizione devono essere designati al massimo 3 proprietari
- Mostra i risultati del controllo dalle macchine virtuali Windows in cui il gruppo Administrators non contiene tutti i membri specificati
- Distribuisci i prerequisiti per controllare le macchine virtuali Windows in cui il gruppo Administrators non contiene tutti i membri specificati
- Alla sottoscrizione deve essere assegnato più di un proprietario

## <a name="22-and-27-security-attributes"></a>2.2 e 2.7 Attributi di sicurezza

Le funzionalità di individuazione e classificazione dei dati offerte da Sicurezza dei dati avanzata di Database SQL di Azure includono funzionalità per l'individuazione, la classificazione, l'applicazione di etichette e la protezione dei dati sensibili presenti nei database. Consente di visualizzare lo stato di classificazione del database e di tenere traccia dell'accesso ai dati sensibili all'interno del database e all'esterno di questo ambito. Sicurezza dei dati avanzata consente di assicurarsi che le informazioni siano associate agli attributi di sicurezza appropriati per l'organizzazione. Questo progetto assegna definizioni di [Criteri di Azure](../../../policy/overview.md) per monitorare e imporre l'uso di Sicurezza dei dati avanzata nei server SQL. 

- Sicurezza dei dati avanzata deve essere abilitata nei server SQL
- Distribuisci Sicurezza dei dati avanzata nei server SQL

## <a name="22-27-41-and-61-remote-access--automated-monitoring--control"></a>2.2, 2.7, 4.1 e 6.1 Accesso remoto | Monitoraggio automatizzato/controllo

Questo progetto consente di monitorare e controllare l'accesso remoto assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) per verificare che il debug remoto per l'applicazione del Servizio app di Azure sia disattivato e altre definizioni di criteri per controllare le macchine virtuali Linux che consentono connessioni remote da account senza password. Il progetto assegna anche una definizione di Criteri di Azure per il monitoraggio dell'accesso illimitato agli account di archiviazione. Il monitoraggio di questi indicatori è utile per verificare la conformità dei metodi di accesso remoto ai criteri di sicurezza.

- \[Anteprima\]: Mostra i risultati del controllo dalle macchine virtuali Linux che consentono connessioni remote da account senza password
- \[Anteprima\]: Distribuisci prerequisiti per controllare le macchine virtuali Linux che consentono connessioni remote da account senza password
- Controlla l'accesso di rete senza restrizioni agli account di archiviazione
- Il debug remoto deve essere disattivato per l'app per le API
- Il debug remoto deve essere disattivato per l'app per le funzioni
- Il debug remoto deve essere disattivato per l'applicazione Web

## <a name="13-and-64-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>1.3 e 6.4 Contenuto dei record di controllo | Gestione centralizzata del contenuto dei record di controllo pianificati

I dati del log applicazioni raccolti da Monitoraggio di Azure vengono archiviati in un'area di lavoro Log Analytics per la centralizzazione della configurazione e della gestione. Questo progetto consente di assicurarsi che gli eventi vengano registrati assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che controllano e impongono la distribuzione dell'agente di Log Analytics nelle macchine virtuali di Azure.

- \[Anteprima\]: Controlla la distribuzione dell'agente di Log Analytics - Immagine macchina virtuale (sistema operativo) non in elenco
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Linux
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Linux
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Windows
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Windows

## <a name="22-27-and-64-response-to-audit-processing-failures"></a>2.2, 2.7 e 6.4 Risposta a errori di elaborazione di controllo

Questo progetto assegna definizioni di [Criteri di Azure](../../../policy/overview.md) che monitorano le configurazioni di controllo e registrazione degli eventi. Il monitoraggio di queste configurazioni è indicativo di errori o errate configurazioni del sistema di controllo ed è utile per adottare misure correttive.

- Sicurezza dei dati avanzata deve essere abilitata nei server SQL
- Audit diagnostic setting (Controllare le impostazioni di diagnostica)
- Distribuire il controllo in SQL Server

## <a name="13-and-64-audit-review-analysis-and-reporting--central-review-and-analysis"></a>1.3 e 6.4 Revisione, analisi e report di controllo | Revisione e analisi centralizzate

I dati del log raccolti da Monitoraggio di Azure vengono archiviati in un'area di lavoro Log Analytics per la centralizzazione della creazione di report e dell'analisi. Questo progetto consente di assicurarsi che gli eventi vengano registrati assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che controllano e impongono la distribuzione dell'agente di Log Analytics nelle macchine virtuali di Azure.

- \[Anteprima\]: Controlla la distribuzione dell'agente di Log Analytics - Immagine macchina virtuale (sistema operativo) non in elenco
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Linux
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Linux
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Windows
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Windows

## <a name="13-22-27-64-and-65a-audit-generation"></a>1.3, 2.2, 2.7, 6.4 e 6.5A Generazione di controlli

Questo progetto consente di assicurarsi che gli eventi di sistema vengano registrati assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che controllano le impostazioni dei log nelle risorse di Azure. Queste definizioni di criteri controllano e impongono la distribuzione dell'agente di Log Analytics nelle macchine virtuali di Azure e la configurazione delle impostazioni di controllo per altri tipi di risorse di Azure. Controllano anche la configurazione dei log di diagnostica per fornire informazioni dettagliate sulle operazioni eseguite nelle risorse di Azure. Il controllo e la funzionalità Sicurezza dei dati avanzata vengono inoltre configurati nei server SQL.

- \[Anteprima\]: Controlla la distribuzione dell'agente di Log Analytics - Immagine macchina virtuale (sistema operativo) non in elenco
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Linux
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Linux
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Windows
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Windows
- Audit diagnostic setting (Controllare le impostazioni di diagnostica)
- Controlla le impostazioni di controllo a livello del server SQL
- Sicurezza dei dati avanzata deve essere abilitata nei server SQL
- Distribuisci Sicurezza dei dati avanzata nei server SQL
- Distribuisci il controllo nei server SQL
- Distribuire le impostazioni di diagnostica per i gruppi di sicurezza di rete

## <a name="11-least-functionality--prevent-program-execution"></a>1.1 Funzionalità minima | Impedire l'esecuzione di programmi

Il controllo delle applicazioni adattivo in Centro sicurezza di Azure è una soluzione per l'inserimento delle applicazioni nell'elenco elementi consentiti end-to-end, intelligente e automatizzata in grado di bloccare o impedire l'esecuzione di software specifico nelle macchine virtuali. Il controllo delle applicazioni può essere eseguito in una modalità di imposizione che impedisce l'esecuzione di applicazioni non approvate. Questo progetto assegna una definizione di Criteri di Azure che consente di monitorare le macchine virtuali quando è consigliato un elenco elementi consentiti che però non è stato ancora configurato.

- I controlli applicazioni adattivi devono essere abilitati nelle macchine virtuali

## <a name="11-least-functionality--authorized-software--whitelisting"></a>1.1 Funzionalità minima | Software autorizzato/elenco elementi consentiti

Il controllo delle applicazioni adattivo in Centro sicurezza di Azure è una soluzione per l'inserimento delle applicazioni nell'elenco elementi consentiti end-to-end, intelligente e automatizzata in grado di bloccare o impedire l'esecuzione di software specifico nelle macchine virtuali. Il controllo delle applicazioni consente di creare elenchi di applicazioni approvate per le macchine virtuali. Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di monitorare le macchine virtuali quando è consigliato un elenco elementi consentiti che però non è stato ancora configurato.

- I controlli applicazioni adattivi devono essere abilitati nelle macchine virtuali

## <a name="11-user-installed-software"></a>1.1 Software installato dall'utente

Il controllo delle applicazioni adattivo in Centro sicurezza di Azure è una soluzione per l'inserimento delle applicazioni nell'elenco elementi consentiti end-to-end, intelligente e automatizzata in grado di bloccare o impedire l'esecuzione di software specifico nelle macchine virtuali. Il controllo delle applicazioni consente di imporre e monitorare la conformità ai criteri di restrizione software. Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di monitorare le macchine virtuali quando è consigliato un elenco elementi consentiti che però non è stato ancora configurato.

- I controlli applicazioni adattivi devono essere abilitati nelle macchine virtuali

## <a name="42-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>4.2 Identificazione e autenticazione (utenti dell'organizzazione) | Accesso alla rete per gli account con privilegi

Questo progetto consente di limitare e controllare l'accesso con privilegi assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che controllano gli account con autorizzazioni di proprietario e/o di scrittura per cui non è abilitata l'autenticazione a più fattori. L'autenticazione a più fattori consente di mantenere sicuri gli account anche se una parte delle informazioni di autenticazione viene compromessa. Monitorando gli account senza autenticazione a più fattori abilitata, è possibile identificare quelli che potrebbero venire compromessi con più probabilità.

- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione
- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di scrittura per la sottoscrizione

## <a name="42-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>4.2 Identificazione e autenticazione (utenti dell'organizzazione) | Accesso alla rete per gli account senza privilegi

Questo progetto consente di limitare e controllare l'accesso assegnando una definizione di [Criteri di Azure](../../../policy/overview.md) per controllare gli account con autorizzazioni di lettura per cui non è abilitata l'autenticazione a più fattori. L'autenticazione a più fattori consente di mantenere sicuri gli account anche se una parte delle informazioni di autenticazione viene compromessa. Monitorando gli account senza autenticazione a più fattori abilitata, è possibile identificare quelli che potrebbero venire compromessi con più probabilità.

- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di lettura per la sottoscrizione

## <a name="23-and-41-authenticator-management"></a>2.3 e 4.1 Gestione autenticatori

Questo progetto assegna definizioni di [Criteri di Azure](../../../policy/overview.md) che controllano le macchine virtuali Linux che consentono connessioni remote da account senza password e/o per cui sono impostate autorizzazioni errate nel file passwd. Questo progetto assegna anche definizioni di criteri che controllano la configurazione del tipo di crittografia delle password per le macchine virtuali Windows. Il monitoraggio di questi indicatori è utile per verificare che gli autenticatori di sistema siano conformi ai criteri di identificazione e autenticazione dell'organizzazione.

- \[Anteprima\]: Mostra i risultati del controllo dalle macchine virtuali Linux in cui le autorizzazioni per il file passwd non sono impostate su 0644
- \[Anteprima\]: Distribuisci i requisiti per controllare le macchine virtuali Linux in cui le autorizzazioni per il file passwd non sono impostate su 0644
- \[Anteprima\]: Mostra i risultati del controllo dalle macchine virtuali Linux in cui sono presenti account senza password
- \[Anteprima\]: Distribuisci i requisiti per controllare le macchine virtuali Linux in cui sono presenti account senza password
- \[Anteprima\]: Mostra i risultati del controllo dalle macchine virtuali Windows che non archiviano le password usando la crittografia reversibile
- \[Anteprima\]: Distribuisci i requisiti per controllare le macchine virtuali Windows che non archiviano le password usando la crittografia reversibile

## <a name="23-and-41-authenticator-management--password-based-authentication"></a>2.3 e 4.1 Gestione autenticatori | Autenticazione basata su password

Questo progetto consente di imporre password complesse assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che controllano le macchine virtuali Windows che non impongono il requisito minimo di complessità o altri requisiti delle password. Identificando le macchine virtuali che violano i criteri di complessità delle password, è possibile adottare azioni correttive per assicurarsi che le password di tutti gli account utente delle macchine virtuali siano conformi ai criteri delle password dell'organizzazione.

- \[Anteprima\]: Mostra i risultati del controllo dalle macchine virtuali Windows che consentono il riutilizzo delle 24 password precedenti
- \[Anteprima\]: Mostra i risultati del controllo dalle macchine virtuali Windows in cui la validità minima della password non è impostata su 70 giorni
- \[Anteprima\]: Mostra i risultati del controllo dalle macchine virtuali Windows in cui la validità minima della password non è impostata su 1 giorno
- \[Anteprima\]: Mostra i risultati del controllo dalle macchine virtuali Windows in cui non è abilitata l'impostazione relativa alla complessità della password
- \[Anteprima\]: Mostra i risultati del controllo dalle macchine virtuali Windows in cui la lunghezza minima della password non è limitata a 14 caratteri
- \[Anteprima\]: Mostra i risultati del controllo dalle macchine virtuali Windows che non archiviano le password usando la crittografia reversibile
- \[Anteprima\]: Distribuisci i prerequisiti per controllare le macchine virtuali Windows che consentono il riutilizzo delle 24 password precedenti
- \[Anteprima\]: Distribuisci i prerequisiti per controllare le macchine virtuali Windows in cui la validità massima della password non è impostata su 70 giorni
- \[Anteprima\]: Distribuisci i prerequisiti per controllare le macchine virtuali Windows in cui la validità minima della password non è impostata su 1 giorno
- \[Anteprima\]: Distribuisci i prerequisiti per controllare le macchine virtuali Windows in cui non è abilitata l'impostazione relativa alla complessità della password
- \[Anteprima\]: Distribuisci i prerequisiti per controllare le macchine virtuali Windows che non limitano la lunghezza minima della password a 14 caratteri
- \[Anteprima\]: Distribuisci i prerequisiti per controllare le macchine virtuali Windows che non archiviano le password usando la crittografia reversibile

## <a name="22-and-27-vulnerability-scanning"></a>2.2 e 2.7 Analisi delle vulnerabilità

Questo progetto consente di gestire le vulnerabilità dei sistemi informativi assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che monitorano le vulnerabilità del sistema operativo, nonché di SQL e delle macchine virtuali nel Centro sicurezza di Azure. Centro sicurezza di Azure fornisce funzionalità di report che consentono di ricevere informazioni dettagliate in tempo reale sullo stato di sicurezza delle risorse di Azure distribuite. Questo progetto assegna anche definizioni di criteri che controllano e impongono l'uso di Sicurezza dei dati avanzata nei server SQL. Sicurezza dei dati avanzata include le funzionalità Valutazione della vulnerabilità e Advanced Threat Protection che consentono di comprendere le vulnerabilità nelle risorse distribuite.

- Sicurezza dei dati avanzata deve essere abilitata nei server SQL
- Distribuisci Sicurezza dei dati avanzata nei server SQL
- Le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali devono essere risolte
- Le vulnerabilità dei database SQL devono essere risolte 
- Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte

## <a name="13-denial-of-service-protection"></a>1.3 Protezione da attacchi Denial of Service

Il livello Standard per gli attacchi Distributed Denial of Service (DDoS) di Azure offre funzionalità aggiuntive e di mitigazione dei rischi rispetto al livello di servizio Basic. Queste funzionalità aggiuntive includono l'integrazione di Monitoraggio di Azure e la possibilità di esaminar ei report di mitigazione dei rischi dopo gli attacchi. Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per controllare l'abilitazione del livello Standard per gli attacchi Distributed Denial of Service. La comprensione delle differenze nelle funzionalità dei livelli di servizio consente di selezionare la soluzione più adatta per rispondere ad attacchi Denial of Service per l'ambiente Azure.

- Lo standard di protezione DDoS deve essere abilitato

## <a name="11-and-61-boundary-protection"></a>1.1 e 6.1 Protezione dei limiti

Questo progetto consente di gestire e controllare i limiti di sistema assegnando una definizione di [Criteri di Azure](../../../policy/overview.md) che monitora l'applicazione delle raccomandazioni sulla protezione avanzata per i gruppi di sicurezza di rete nel Centro sicurezza di Azure. Centro sicurezza di Azure analizza i criteri relativi al traffico di macchine virtuali connesse a Internet e fornisce raccomandazioni sulle regole dei gruppi di sicurezza di rete per ridurre la potenziale superficie di attacco.
Questo progetto assegna inoltre definizioni di criteri che monitorano endpoint, applicazioni e account di archiviazione non protetti. Gli endpoint e le applicazioni non protetti da un firewall e gli account di archiviazione con accesso illimitato possono consentire l'accesso non autorizzato alle informazioni contenute nel sistema informativo.

- Le regole per i gruppi di sicurezza di rete delle macchine virtuali con connessione Internet devono essere rafforzate
- L'accesso tramite endpoint con connessione Internet deve essere limitato
- Controlla l'accesso di rete senza restrizioni agli account di archiviazione

## <a name="29a-boundary-protection--access-points"></a>2.9A Protezione dei limiti | Punti di accesso

L'accesso JIT alle macchine virtuali blocca il traffico in ingresso alle macchine virtuali di Azure, riducendo l'esposizione agli attacchi e al tempo stesso offrendo un facile accesso per connettersi alle macchine virtuali quando necessario. L'accesso JIT alle macchine virtuali consente di limitare il numero di connessioni esterne alle risorse in Azure. Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di monitorare le macchine virtuali in grado di supportare l'accesso JIT ma che non sono state ancora configurate.

- Alle macchine virtuali deve essere applicato il controllo di accesso alla rete JIT

## <a name="29a-boundary-protection--external-telecommunications-services"></a>2.9A Protezione dei limiti | Servizi di telecomunicazione esterni

L'accesso JIT alle macchine virtuali blocca il traffico in ingresso alle macchine virtuali di Azure, riducendo l'esposizione agli attacchi e al tempo stesso offrendo un facile accesso per connettersi alle macchine virtuali quando necessario. L'accesso JIT alle macchine virtuali consente di gestire le eccezioni ai criteri di flusso del traffico agevolando i processi di richiesta e approvazione degli accessi. Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di monitorare le macchine virtuali in grado di supportare l'accesso JIT ma che non sono state ancora configurate.

- Alle macchine virtuali deve essere applicato il controllo di accesso alla rete JIT

## <a name="21-24-24a-25a-and-26-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>2.1, 2.4, 2.4A, 2.5A e 2.6 Riservatezza e integrità delle trasmissioni | Protezione crittografica o fisica in alternativa

Questo progetto consente di proteggere la riservatezza e l'integrità delle informazioni trasmesse assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che consentono di monitorare il meccanismo di crittografia implementato per i protocolli di comunicazione. Assicurandosi che le comunicazioni vengano correttamente crittografate, è possibile soddisfare i requisiti dell'organizzazione per la protezione delle informazioni da divulgazione e modifiche non autorizzate.

- L'app per le API deve essere accessibile solo tramite HTTPS
- Mostra i risultati del controllo dai server Web Windows che non usano protocolli di comunicazione sicuri
- Distribuisci i prerequisiti per controllare i server Web Windows che non usano protocolli di comunicazione sicuri
- L'app per le funzioni deve essere accessibile solo tramite HTTPS
- Devono essere abilitate solo connessioni sicure alla Cache Redis
- Il trasferimento sicuro negli account di archiviazione deve essere abilitato
- L'applicazione Web deve essere accessibile solo tramite HTTPS

## <a name="22-23-25-41-and-27-protection-of-information-at-rest--cryptographic-protection"></a>2.2, 2.3, 2.5, 4.1 e 2.7 Protezione delle informazioni inattive | Protezione crittografica

Questo progetto consente di imporre i criteri sull'uso dei controlli crittografici per la protezione delle informazioni inattive assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che impongono controlli crittografici specifici e controllano l'uso di impostazioni di crittografia meno sicure. Identificando le risorse di Azure le cui configurazioni di crittografia potrebbero non essere ottimali, è possibile adottare azioni correttive per assicurarsi che le risorse siano configurate in conformità ai criteri di sicurezza delle informazioni. In particolare, le definizioni di criteri assegnate da questo progetto richiedono la crittografia per gli account Data Lake Storage, richiedono l'applicazione di Transparent Data Encryption per i database SQL e controllano se manca la crittografia in database SQL, dischi di macchine virtuali e variabili degli account di automazione.

- Sicurezza dei dati avanzata deve essere abilitata nei server SQL
- Distribuisci Sicurezza dei dati avanzata nei server SQL
- Distribuisci Transparent Data Encryption nel database SQL
- Transparent Data Encryption deve essere abilitata nei database SQL

## <a name="13-22-and-27-flaw-remediation"></a>1.3, 2.2 e 2.7 Correzione degli errori

Questo progetto consente di gestire le vulnerabilità dei sistemi informativi assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che monitorano gli aggiornamenti di sistema mancanti, oltre alle vulnerabilità del sistema operativo, di SQL e delle macchine virtuali nel Centro sicurezza di Azure. Centro sicurezza di Azure fornisce funzionalità di report che consentono di ricevere informazioni dettagliate in tempo reale sullo stato di sicurezza delle risorse di Azure distribuite. Questo progetto assegna anche una definizione di criteri che assicura la distribuzione automatica di patch del sistema operativo per i set di scalabilità di macchine virtuali.

- Imponi l'applicazione automatica di patch alle immagini del sistema operativo nei set di scalabilità di macchine virtuali
- Gli aggiornamenti di sistema nei set di scalabilità di macchine virtuali devono essere installati
- Gli aggiornamenti di sistema devono essere installati nelle macchine virtuali
- Le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali devono essere risolte
- Le vulnerabilità nella configurazione di sicurezza delle macchine virtuali devono essere risolte
- Le vulnerabilità dei database SQL devono essere risolte

## <a name="61-malicious-code-protection"></a>6.1 Protezione dal malware

Questo progetto consente di gestire Endpoint Protection, inclusa la protezione dal malware, assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che monitorano la presenza di Endpoint Protection nelle macchine virtuali nel Centro sicurezza di Azure e impongono la soluzione antimalware Microsoft nelle macchine virtuali Windows.

- Distribuisci estensione IaaSAntimalware Microsoft predefinita per Windows Server
- La soluzione Endpoint Protection deve essere installata nei set di scalabilità di macchine virtuali
- Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure

## <a name="61-malicious-code-protection--central-management"></a>6.1 Protezione dal malware | Gestione centrale

Questo progetto consente di gestire Endpoint Protection, inclusa la protezione dal malware, assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che monitorano la presenza di Endpoint Protection nelle macchine virtuali nel Centro sicurezza di Azure. Centro sicurezza di Azure fornisce funzionalità centralizzate di gestione e creazione di report che consentono di ricevere informazioni dettagliate in tempo reale sullo stato di sicurezza delle risorse di Azure distribuite.

- La soluzione Endpoint Protection deve essere installata nei set di scalabilità di macchine virtuali
- Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure

## <a name="11-13-22-27-28-and-64-information-system-monitoring"></a>1.1, 1.3, 2.2, 2.7, 2.8 e 6.4 Monitoraggio dei sistemi informativi

Questo progetto consente di monitorare il sistema controllando e imponendo la registrazione e la sicurezza dei dati nelle risorse di Azure. In particolare, i criteri assegnati controllano e impongono la distribuzione dell'agente di Log Analytics, nonché impostazioni di sicurezza avanzata per database SQL, account di archiviazione e risorse di rete. Queste funzionalità consentono di rilevare comportamenti anomali e indicatori di attacchi in modo che sia possibile adottare le misure appropriate.

- \[Anteprima\]: Controlla la distribuzione dell'agente di Log Analytics - Immagine macchina virtuale (sistema operativo) non in elenco
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Linux
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Linux
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Windows
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Windows
- Sicurezza dei dati avanzata deve essere abilitata nei server SQL
- Le impostazioni avanzate di sicurezza dei dati per SQL Server devono contenere un indirizzo di posta elettronica a cui ricevere gli avvisi di sicurezza
- I log di diagnostica devono essere abilitati in Analisi di flusso di Azure
- Distribuisci Sicurezza dei dati avanzata nei server SQL
- Distribuisci il controllo nei server SQL
- Distribuisci Network Watcher quando vengono create reti virtuali
- Distribuisci Rilevamento minacce nelle istanze di SQL Server

## <a name="22-and-28-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>2.2 e 2.8 Monitoraggio dei sistemi informativi | Analisi del traffico/esfiltrazioni nascoste

Advanced Threat Protection per Archiviazione di Azure rileva tentativi insoliti e potenzialmente dannosi di accesso o sfruttamento degli account di archiviazione. Gli avvisi di protezione includono modelli di accesso anomalo, estrazioni/caricamenti anomali e attività sospette. Questi indicatori possono essere utili per rilevare l'esfiltrazione occulta delle informazioni.

- Distribuisci Rilevamento minacce nelle istanze di SQL Server

> [!NOTE]
> La disponibilità di specifiche definizioni di Criteri di Azure può variare in Azure per enti pubblici e in altri cloud nazionali.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver esaminato il mapping dei controlli del progetto SWIFT CSP-CSCF v2020, vedere gli articoli seguenti per informazioni sul progetto e su come distribuire questo esempio:

> [!div class="nextstepaction"]
> [Progetto SWIFT CSP-CSCF v2020 - Panoramica](./index.md)
> [Progetto SWIFT CSP-CSCF v2020 - Procedura di distribuzione](./deploy.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).
