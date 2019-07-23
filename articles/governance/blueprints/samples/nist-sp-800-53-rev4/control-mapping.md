---
title: Esempio - Progetto NIST SP 800-53 R4 - Mapping dei controlli
description: Mapping dei controlli dell'esempio di progetto NIST SP 800-53 R4 in Criteri di Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 251dbc396aea5694c4bdec45c194439c9476238b
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226049"
---
# <a name="control-mapping-of-the-nist-sp-800-53-r4-blueprint-sample"></a>Mapping dei controlli dell'esempio di progetto NIST SP 800-53 R4

L'articolo seguente descrive in dettaglio il mapping dell'esempio di progetto NIST SP 800-53 R4 di Azure Blueprints ai controlli NIST SP 800-53 R4. Per altre informazioni sui controlli, vedere [NIST SP 800-53](https://nvd.nist.gov/800-53).

I mapping seguenti fanno riferimento ai controlli **NIST SP 800-53 (Rev. 4)** . Usare la barra di spostamento a destra per passare direttamente a uno specifico mapping. Molti controlli mappati vengono implementati con un'iniziativa di [Criteri di Azure](../../../policy/overview.md). Per esaminare l'iniziativa completa, aprire **Criteri** nel portale di Azure e selezionare la pagina **Definizioni**. Trovare e selezionare l'iniziativa dei criteri predefinita **\[Anteprima\]: Verifica controlli NIST SP 800-53 R4 e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo**.

## <a name="ac-2-account-management"></a>AC-2 Gestione degli account

Questo progetto consente di esaminare gli account che potrebbero non conformi ai requisiti di gestione degli account dell'organizzazione. Il progetto assegna cinque definizioni di Criteri di Azure che controllano gli account esterni con autorizzazioni di lettura, scrittura e proprietario per una sottoscrizione e gli account deprecati. Verificando gli account controllati da questi criteri, è possibile intervenire per assicurarsi che i requisiti di gestione degli account siano soddisfatti.

- \[Anteprima\]: Controllare account deprecati in una sottoscrizione
- \[Anteprima\]: Controllare gli account deprecati con autorizzazioni di proprietario in una sottoscrizione
- \[Anteprima\]: Controllare gli account esterni con autorizzazioni di proprietario su una sottoscrizione
- \[Anteprima\]: Controllare gli account esterni con autorizzazioni di lettura su una sottoscrizione
- \[Anteprima\]: Controllare gli account esterni con autorizzazioni di scrittura su una sottoscrizione

## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) Gestione degli account | Schemi basati sui ruoli

Azure implementa il [controllo degli accessi in base al ruolo](../../../../role-based-access-control/overview.md) per consentire di gestire chi ha accesso alle risorse di Azure. Usando il portale di Azure, è possibile verificare chi ha accesso alle risorse di Azure e le relative autorizzazioni. Questo progetto assegna due definizioni di [Criteri di Azure](../../../policy/overview.md) per controllare l'uso dell'autenticazione di Azure Active Directory per istanze di SQL Server e Service Fabric. L'uso dell'autenticazione di Azure Active Directory consente una gestione semplificata delle autorizzazioni e una gestione centralizzata delle identità degli utenti di database e di altri servizi Microsoft.

- Controlla il provisioning di un amministratore di Azure Active Directory per SQL Server
- Controlla l'utilizzo di Azure Active Directory per l'autenticazione client in Service Fabric

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>AC-2 (12) Gestione degli account | Monitoraggio/Utilizzo atipico degli account

L'accesso JIT alle macchine virtuali blocca il traffico in ingresso alle macchine virtuali di Azure, riducendo l'esposizione agli attacchi e al tempo stesso offrendo un facile accesso per connettersi alle macchine virtuali quando necessario. Tutte le richieste JIT di accesso alle macchine virtuali vengono registrate nel Log attività allo scopo di monitorare l'eventuale utilizzo atipico. Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di monitorare le macchine virtuali in grado di supportare l'accesso JIT ma che non sono state ancora configurate.

- \[Anteprima\]: Monitorare i possibili accessi JIT alla rete nel Centro sicurezza di Azure

## <a name="ac-4-information-flow-enforcement"></a>AC-4 Imposizione del controllo dei flussi di informazioni

Con la funzionalità Condivisione di risorse tra le origini (CORS) le risorse di Servizi app possono essere richieste da un dominio esterno. Microsoft consiglia di consentire solo ai domini richiesti di interagire con l'API, la funzione e le applicazioni Web. Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per consentire il monitoraggio delle limitazioni di accesso alle risorse CORS in Centro sicurezza di Azure.
La comprensione delle implementazioni CORS può essere utile per verificare l'implementazione dei controlli del flusso di informazioni.

- \[Anteprima\]: Controllare le restrizioni di accesso alle risorse CORS per un'applicazione Web

## <a name="ac-5-separation-of-duties"></a>AC-5 Separazione dei compiti

La presenza di un solo proprietario di sottoscrizioni di Azure non consente la ridondanza amministrativa. Al contrario, la presenza di troppi proprietari di sottoscrizioni di Azure può aumentare la probabilità che si verifichi una violazione tramite un account di proprietario compromesso. Questo progetto consente di mantenere un numero appropriato di proprietari di sottoscrizioni di Azure assegnando due definizioni di [Criteri di Azure](../../../policy/overview.md) che controllano questo numero. Questo progetto assegna anche quattro definizioni di Criteri di Azure che consentono di controllare l'appartenenza del gruppo Administrators nelle macchine virtuali Windows. La gestione delle autorizzazioni dei proprietari di sottoscrizioni e degli amministratori di macchine virtuali consente di implementare la separazione appropriata dei compiti.

- \[Anteprima\]: Controllare il numero massimo di proprietari per una sottoscrizione
- \[Anteprima\]: Controllare il numero minimo di proprietari per sottoscrizione
- Controllare che il gruppo Administrators all'interno di macchine virtuali Windows escluda i membri specificati
- Controllare che il gruppo Administrators all'interno di macchine virtuali Windows includa i membri specificati
- Distribuire l'estensione macchina virtuale per controllare che il gruppo Administrators all'interno di macchine virtuali Windows escluda i membri specificati
- Distribuire l'estensione macchina virtuale per controllare che il gruppo Administrators all'interno di macchine virtuali Windows includa i membri specificati

## <a name="ac-6-7-least-privilege--review-of-user-privileges"></a>AC-6 (7) Privilegi minimi | Revisione dei privilegi degli utenti

Azure implementa il [controllo degli accessi in base al ruolo](../../../../role-based-access-control/overview.md) per consentire di gestire chi ha accesso alle risorse di Azure. Usando il portale di Azure, è possibile verificare chi ha accesso alle risorse di Azure e le relative autorizzazioni. Questo progetto assegna sei definizioni di [Criteri di Azure](../../../policy/overview.md) per controllare gli account la cui verifica dovrebbe essere prioritaria. L'esame di questi indicatori di account è utile per verificare l'implementazione dei controlli dei privilegi minimi.

- \[Anteprima\]: Controllare il numero massimo di proprietari per una sottoscrizione
- \[Anteprima\]: Controllare il numero minimo di proprietari per sottoscrizione
- Controllare che il gruppo Administrators all'interno di macchine virtuali Windows escluda i membri specificati
- Controllare che il gruppo Administrators all'interno di macchine virtuali Windows includa i membri specificati
- Distribuire l'estensione macchina virtuale per controllare che il gruppo Administrators all'interno di macchine virtuali Windows escluda i membri specificati
- Distribuire l'estensione macchina virtuale per controllare che il gruppo Administrators all'interno di macchine virtuali Windows includa i membri specificati

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) Accesso remoto | Monitoraggio/Controllo automatico

Questo progetto consente di monitorare e controllare l'accesso remoto assegnando tre definizioni di [Criteri di Azure](../../../policy/overview.md) per monitorare che il debug remoto per l'applicazione del Servizio app di Azure sia disattivato e due definizioni di criteri per controllare le macchine virtuali Linux che consentono connessioni remote da account senza password. Il progetto assegna anche una definizione di Criteri di Azure per il monitoraggio dell'accesso illimitato agli account di archiviazione. Il monitoraggio di questi indicatori è utile per verificare la conformità dei metodi di accesso remoto ai criteri di sicurezza.

- \[Anteprima\]: Controllare lo stato del debug remoto per un'app per le funzioni
- \[Anteprima\]: Controllare lo stato del debug remoto per un'applicazione Web
- \[Anteprima\]: Controllare lo stato del debug remoto per un'app per le API
- \[Anteprima\]: Controllare che le macchine virtuali Linux non consentano connessioni remote da account senza password
- \[Anteprima\]: Distribuire un'estensione VM per controllare che le macchine virtuali Linux non consentano connessioni remote da account senza password
- Controlla l'accesso di rete senza restrizioni agli account di archiviazione

## <a name="au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>AU-3 (2) Contenuto dei record di controllo | Gestione centralizzata del contenuto dei record di controllo pianificati

I dati del log applicazioni raccolti da Monitoraggio di Azure vengono archiviati in un'area di lavoro Log Analytics per la centralizzazione della configurazione e della gestione. Questo progetto consente di assicurarsi che gli eventi di sistema vengano registrati assegnando sette definizioni di [Criteri di Azure](../../../policy/overview.md) che controllano e impongono la distribuzione dell'agente di Log Analytics nelle macchine virtuali di Azure.

- \[Anteprima\]: Controlla la distribuzione dell'agente di Log Analytics - Immagine macchina virtuale (sistema operativo) non in elenco
- \[Anteprima\]: Controlla la distribuzione dell'agente di Log Analytics nei set di scalabilità di macchine virtuali - Immagine macchina virtuale (sistema operativo) non in elenco
- \[Anteprima\]: Controlla area di lavoro Log Analytics per la macchina virtuale - Segnala mancata corrispondenza
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Linux
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Linux
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Windows
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Windows

## <a name="au-5-response-to-audit-processing-failures"></a>AU-5 Risposta a errori di elaborazione di controllo

Questo progetto assegna cinque definizioni di [Criteri di Azure](../../../policy/overview.md) che monitorano le configurazioni di controllo e registrazione degli eventi. Il monitoraggio di queste configurazioni è indicativo di errori o errate configurazioni del sistema di controllo ed è utile per adottare misure correttive.

- \[Anteprima\]: Monitorare i server SQL non controllati nel Centro sicurezza di Azure
- Audit diagnostic setting (Controllare le impostazioni di diagnostica)
- Controlla istanze gestite di SQL senza Sicurezza dei dati avanzata
- Controlla le impostazioni di controllo a livello del server SQL
- Controlla server SQL senza Sicurezza dei dati avanzata

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) Verifica, analisi e report di controllo | Verifica e analisi centralizzate

I dati del log raccolti da Monitoraggio di Azure vengono archiviati in un'area di lavoro Log Analytics per la centralizzazione della creazione di report e dell'analisi. Questo progetto consente di assicurarsi che gli eventi di sistema vengano registrati assegnando sette definizioni di [Criteri di Azure](../../../policy/overview.md) che controllano e impongono la distribuzione dell'agente di Log Analytics nelle macchine virtuali di Azure.

- \[Anteprima\]: Controlla la distribuzione dell'agente di Log Analytics - Immagine macchina virtuale (sistema operativo) non in elenco
- \[Anteprima\]: Controlla la distribuzione dell'agente di Log Analytics nei set di scalabilità di macchine virtuali - Immagine macchina virtuale (sistema operativo) non in elenco
- \[Anteprima\]: Controlla area di lavoro Log Analytics per la macchina virtuale - Segnala mancata corrispondenza
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Linux
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Linux
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Windows
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Windows

## <a name="au-12-audit-generation"></a>AU-12 Generazione di controlli

Questo progetto consente di assicurarsi che gli eventi di sistema vengano registrati assegnando 15 definizioni di [Criteri di Azure](../../../policy/overview.md) che controllano le impostazioni dei log nelle risorse di Azure. Queste definizioni di criteri controllano e impongono la distribuzione dell'agente di Log Analytics nelle macchine virtuali di Azure e la configurazione delle impostazioni di controllo per altri tipi di risorse di Azure. Controllano anche la configurazione dei log di diagnostica per fornire informazioni dettagliate sulle operazioni eseguite nelle risorse di Azure. Il controllo e la funzionalità Sicurezza dei dati avanzata vengono inoltre configurati nei server SQL.

- \[Anteprima\]: Controlla la distribuzione dell'agente di Log Analytics - Immagine macchina virtuale (sistema operativo) non in elenco
- \[Anteprima\]: Controlla la distribuzione dell'agente di Log Analytics nei set di scalabilità di macchine virtuali - Immagine macchina virtuale (sistema operativo) non in elenco
- \[Anteprima\]: Controlla area di lavoro Log Analytics per la macchina virtuale - Segnala mancata corrispondenza
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Linux
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Linux
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Windows
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Windows
- \[Anteprima\]: Monitorare i server SQL non controllati nel Centro sicurezza di Azure
- Applica impostazioni di diagnostica per i gruppi di sicurezza di rete
- Audit diagnostic setting (Controllare le impostazioni di diagnostica)
- Controlla istanze gestite di SQL senza Sicurezza dei dati avanzata
- Controlla le impostazioni di controllo a livello del server SQL
- Controlla server SQL senza Sicurezza dei dati avanzata
- Distribuisci Sicurezza dei dati avanzata nei server SQL
- Distribuisci il controllo nei server SQL

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>CM-7 (2) Funzionalità minima | Impedire l'esecuzione di programmi

Il controllo delle applicazioni adattivo in Centro sicurezza di Azure è una soluzione per l'inserimento delle applicazioni nell'elenco elementi consentiti end-to-end, intelligente e automatizzata in grado di bloccare o impedire l'esecuzione di software specifico nelle macchine virtuali. Il controllo delle applicazioni può essere eseguito in una modalità di imposizione che impedisce l'esecuzione di applicazioni non approvate. Questo progetto assegna una definizione di Criteri di Azure che consente di monitorare le macchine virtuali quando è consigliato un elenco elementi consentiti che però non è stato ancora configurato.

- \[Anteprima\]: Monitorare il possibile elenco elementi consentiti dell'app nel Centro sicurezza di Azure

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) Funzionalità minima | Software autorizzato/elenco elementi consentiti

Il controllo delle applicazioni adattivo in Centro sicurezza di Azure è una soluzione per l'inserimento delle applicazioni nell'elenco elementi consentiti end-to-end, intelligente e automatizzata in grado di bloccare o impedire l'esecuzione di software specifico nelle macchine virtuali. Il controllo delle applicazioni consente di creare elenchi di applicazioni approvate per le macchine virtuali. Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di monitorare le macchine virtuali quando è consigliato un elenco elementi consentiti che però non è stato ancora configurato.

- \[Anteprima\]: Monitorare il possibile elenco elementi consentiti dell'app nel Centro sicurezza di Azure

## <a name="cm-11-user-installed-software"></a>CM-11 Software installato dall'utente

Il controllo delle applicazioni adattivo in Centro sicurezza di Azure è una soluzione per l'inserimento delle applicazioni nell'elenco elementi consentiti end-to-end, intelligente e automatizzata in grado di bloccare o impedire l'esecuzione di software specifico nelle macchine virtuali. Il controllo delle applicazioni consente di imporre e monitorare la conformità ai criteri di restrizione software. Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di monitorare le macchine virtuali quando è consigliato un elenco elementi consentiti che però non è stato ancora configurato.

- \[Anteprima\]: Monitorare il possibile elenco elementi consentiti dell'app nel Centro sicurezza di Azure

## <a name="cp-7-alternate-processing-site"></a>CP-7 Sito di elaborazione alternativo

Azure Site Recovery consente di replicare carichi di lavoro in esecuzione in macchine da una località primaria a una località secondaria. Se si verifica un'interruzione nel sito primario, viene effettuato il failover del carico di lavoro nella località secondaria. Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per controllare le macchine virtuali senza aver configurato il ripristino di emergenza. Il monitoraggio di questo indicatore è utile per verificare che siano già predisposti i controlli di emergenza necessari.

- Controlla macchine virtuali in cui non è configurato il ripristino di emergenza

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) Identificazione e autenticazione (utenti dell'organizzazione) | Accesso alla rete per gli account con privilegi

Questo progetto consente di limitare e controllare l'accesso con privilegi assegnando due definizioni di [Criteri di Azure](../../../policy/overview.md) per controllare gli account con autorizzazioni di proprietario e/o di scrittura per cui non è abilitata l'autenticazione a più fattori. L'autenticazione a più fattori consente di mantenere sicuri gli account anche se una parte delle informazioni di autenticazione viene compromessa. Monitorando gli account senza autenticazione a più fattori abilitata, è possibile identificare quelli che potrebbero venire compromessi con più probabilità.

- \[Anteprima\]: Controllare gli account con autorizzazioni di proprietario non abilitati per MFA in una sottoscrizione
- \[Anteprima\]: Controllare gli account con autorizzazioni di scrittura non abilitati per MFA in una sottoscrizione

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>IA-2 (2) Identificazione e autenticazione (utenti dell'organizzazione) | Accesso alla rete per gli account senza privilegi

Questo progetto consente di limitare e controllare l'accesso assegnando una definizione di [Criteri di Azure](../../../policy/overview.md) per controllare gli account con autorizzazioni di lettura per cui non è abilitata l'autenticazione a più fattori. L'autenticazione a più fattori consente di mantenere sicuri gli account anche se una parte delle informazioni di autenticazione viene compromessa. Monitorando gli account senza autenticazione a più fattori abilitata, è possibile identificare quelli che potrebbero venire compromessi con più probabilità.

- \[Anteprima\]: Controllare gli account con autorizzazioni di lettura non abilitati per MFA in una sottoscrizione

## <a name="ia-5-authenticator-management"></a>IA-5 Gestione degli autenticatori

Questo progetto assegna cinque definizioni di [Criteri di Azure](../../../policy/overview.md) per controllare le macchine virtuali Linux che consentono connessioni remote da account senza password e/o per cui sono impostate autorizzazioni errate nel file passwd. Questo progetto assegna anche una definizione di criteri che controlla la compatibilità del tipo di crittografia delle password per le macchine virtuali Windows. Il monitoraggio di questi indicatori è utile per verificare che gli autenticatori di sistema siano conformi ai criteri di identificazione e autenticazione dell'organizzazione.

- \[Anteprima\]: Controllare le macchine virtuali Linux in cui sono presenti account senza password
- \[Anteprima\]: Distribuire un'estensione VM per controllare che nelle macchine virtuali Linux non siano presenti account senza password
- \[Anteprima\]: Controllare che nelle macchine virtuali Linux le autorizzazioni per il file passwd siano impostate su 0644
- \[Anteprima\]: Controllare che le macchine virtuali Windows archivino le password usando la crittografia reversibile
- \[Anteprima\]: Distribuire un'estensione VM per controllare che nelle macchine virtuali Linux le autorizzazioni per il file passwd siano impostate su 0644

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) Gestione autenticatori | Autenticazione basata su password

Questo progetto consente di imporre password complesse assegnando 12 definizioni di [Criteri di Azure](../../../policy/overview.md) per controllare le macchine virtuali Windows che non impongono il requisito minimo di complessità o altri requisiti delle password. Identificando le macchine virtuali che violano i criteri di complessità delle password, è possibile adottare azioni correttive per assicurarsi che le password di tutti gli account utente delle macchine virtuali siano conformi ai criteri delle password dell'organizzazione.

- \[Anteprima\]: Controllare le macchine virtuali Windows non possano riutilizzare le 24 password precedenti
- \[Anteprima\]: Controllare che la validità massima della password della macchina virtuale Windows sia di 70 giorni
- \[Anteprima\]: Controllare che la validità minima della password della macchina virtuale Windows sia di 1 giorno
- \[Anteprima\]: Controllare che nelle macchine virtuali Windows sia abilitata l'impostazione relativa alla complessità della password
- \[Anteprima\]: Controllare che le macchine virtuali Windows limitino la lunghezza minima della password a 14 caratteri
- \[Anteprima\]: Controllare che le macchine virtuali Windows archivino le password usando la crittografia reversibile
- \[Anteprima\]: Distribuire un'estensione VM per controllare le macchine virtuali Windows non possano riutilizzare le 24 password precedenti
- \[Anteprima\]: Distribuire un'estensione VM per controllare che la validità massima della password della macchina virtuale Windows sia di 70 giorni
- \[Anteprima\]: Distribuire un'estensione VM per controllare che la validità minima della password della macchina virtuale Windows sia di 1 giorno
- \[Anteprima\]: Distribuire un'estensione VM per controllare che nelle macchine virtuali Windows sia abilitata l'impostazione relativa alla complessità della password
- \[Anteprima\]: Distribuire un'estensione VM per controllare che le macchine virtuali Windows limitino la lunghezza minima della password a 14 caratteri
- \[Anteprima\]: Distribuire l'estensione VM per controllare che le macchine virtuali Windows archivino le password usando la crittografia reversibile

## <a name="ra-5-vulnerability-scanning"></a>RA-5 Analisi delle vulnerabilità

Questo progetto consente di gestire le vulnerabilità dei sistemi informativi assegnando quattro definizioni di [Criteri di Azure](../../../policy/overview.md) che monitorano le vulnerabilità del sistema operativo, nonché di SQL e delle macchine virtuali nel Centro sicurezza di Azure. Centro sicurezza di Azure fornisce funzionalità di report che consentono di ricevere informazioni dettagliate in tempo reale sullo stato di sicurezza delle risorse di Azure distribuite. Questo progetto assegna anche tre definizioni di criteri che controllano e impongono Sicurezza dei dati avanzata nei server SQL. Sicurezza dei dati avanzata include le funzionalità Valutazione della vulnerabilità e Advanced Threat Protection che consentono di comprendere le vulnerabilità nelle risorse distribuite.

- Controlla istanze gestite di SQL senza Sicurezza dei dati avanzata
- Controlla server SQL senza Sicurezza dei dati avanzata
- Distribuisci Sicurezza dei dati avanzata nei server SQL
- \[Anteprima\]: Controllare le vulnerabilità del sistema operativo dei set di scalabilità di macchine virtuali nel Centro sicurezza di Azure
- \[Anteprima\]: Monitorare le vulnerabilità del sistema operativo nel Centro sicurezza di Azure
- \[Anteprima\]: Monitorare i risultati della valutazione della vulnerabilità di SQL nel Centro sicurezza di Azure
- \[Anteprima\]: Monitorare le vulnerabilità delle macchine virtuali nel Centro sicurezza di Azure

## <a name="sc-5-denial-of-service-protection"></a>SC-5 Protezione da attacchi Denial of Service

Il livello Standard per gli attacchi Distributed Denial of Service (DDoS) di Azure offre funzionalità aggiuntive e di mitigazione dei rischi rispetto al livello di servizio Basic. Queste funzionalità aggiuntive includono l'integrazione di Monitoraggio di Azure e la possibilità di esaminar ei report di mitigazione dei rischi dopo gli attacchi. Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per controllare l'abilitazione del livello Standard per gli attacchi Distributed Denial of Service. La comprensione delle differenze nelle funzionalità dei livelli di servizio consente di selezionare la soluzione più adatta per rispondere ad attacchi Denial of Service per l'ambiente Azure.

- \[Anteprima\]: Controllare che il livello Standard della protezione DDoS sia abilitato per una rete virtuale

## <a name="sc-7-boundary-protection"></a>SC-7 Protezione dei limiti

Questo progetto consente di gestire e controllare i limiti di sistema assegnando una definizione di [Criteri di Azure](../../../policy/overview.md) che monitora i gruppi di sicurezza di rete con regole permissive. Le regole troppo permissive potrebbero consentire l'accesso non autorizzato alla rete e dovranno essere riviste. Questo progetto assegna anche una definizione di criteri per monitorare le raccomandazioni di protezione avanzata dei gruppi di sicurezza di rete in Centro sicurezza di Azure. Centro sicurezza di Azure analizza i criteri relativi al traffico di macchine virtuali connesse a Internet e fornisce raccomandazioni sulle regole dei gruppi di sicurezza di rete per ridurre la potenziale superficie di attacco.
Questo progetto assegna inoltre tre definizioni di criteri che monitorano endpoint, applicazioni e account di archiviazione non protetti. Gli endpoint e le applicazioni non protetti da un firewall e gli account di archiviazione con accesso illimitato possono consentire l'accesso non autorizzato alle informazioni contenute nel sistema informativo.

- \[Anteprima\]: Monitorare le macchine virtuali con connessione Internet per le raccomandazioni sulla protezione avanzata del traffico del gruppo di sicurezza di rete
- \[Anteprima\]: Monitorare gli accessi di rete permissivi nel Centro sicurezza di Azure
- \[Anteprima\]: Monitorare gli endpoint di rete non protetti nel Centro sicurezza di Azure
- \[Anteprima\]: Monitorare le applicazioni Web non protette nel Centro sicurezza di Azure
- Controlla l'accesso di rete senza restrizioni agli account di archiviazione

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) Protezione dei limiti | Punti di accesso

L'accesso JIT alle macchine virtuali blocca il traffico in ingresso alle macchine virtuali di Azure, riducendo l'esposizione agli attacchi e al tempo stesso offrendo un facile accesso per connettersi alle macchine virtuali quando necessario. L'accesso JIT alle macchine virtuali consente di limitare il numero di connessioni esterne alle risorse in Azure. Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di monitorare le macchine virtuali in grado di supportare l'accesso JIT ma che non sono state ancora configurate.

- \[Anteprima\]: Monitorare i possibili accessi JIT alla rete nel Centro sicurezza di Azure

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) Protezione dei limiti | Servizi di telecomunicazione esterni

L'accesso JIT alle macchine virtuali blocca il traffico in ingresso alle macchine virtuali di Azure, riducendo l'esposizione agli attacchi e al tempo stesso offrendo un facile accesso per connettersi alle macchine virtuali quando necessario. L'accesso JIT alle macchine virtuali consente di gestire le eccezioni ai criteri di flusso del traffico agevolando i processi di richiesta e approvazione degli accessi. Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di monitorare le macchine virtuali in grado di supportare l'accesso JIT ma che non sono state ancora configurate.

- \[Anteprima\]: Monitorare i possibili accessi JIT alla rete nel Centro sicurezza di Azure

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>SC-28 (1) Protezione delle informazioni inattive | Protezione crittografica

Questo progetto consente di imporre i criteri sull'uso dei controlli crittografici per la protezione delle informazioni inattive assegnando nove definizioni di [Criteri di Azure](../../../policy/overview.md) che impongono controlli crittografici specifici e controllano l'uso di impostazioni di crittografia meno sicure. Identificando le risorse di Azure le cui configurazioni di crittografia potrebbero non essere ottimali, è possibile adottare azioni correttive per assicurarsi che le risorse siano configurate in conformità ai criteri di sicurezza delle informazioni. In particolare, le definizioni di criteri assegnate da questo progetto richiedono la crittografia per gli account Data Lake Storage, richiedono l'applicazione di Transparent Data Encryption per i database SQL e controllano se manca la crittografia in database SQL, dischi di macchine virtuali e variabili degli account di automazione.

- \[Anteprima\]: Monitorare i database SQL non crittografati nel Centro sicurezza di Azure
- \[Anteprima\]: Monitorare i dischi di macchine virtuali non crittografati nel Centro sicurezza di Azure
- Controlla il trasferimento sicuro negli account di archiviazione
- Controlla istanze gestite di SQL senza Sicurezza dei dati avanzata
- Controlla server SQL senza Sicurezza dei dati avanzata
- Controllare lo stato di Transparent Data Encryption
- Distribuisci Sicurezza dei dati avanzata nei server SQL
- Distribuisci Transparent Data Encryption nel database SQL
- Imponi crittografia per gli account Data Lake Store

## <a name="si-2-flaw-remediation"></a>SI-2 Correzione degli errori

Questo progetto consente di gestire gli errori dei sistemi informativi assegnando sei definizioni di [Criteri di Azure](../../../policy/overview.md) che monitorano gli aggiornamenti di sistema mancanti, oltre alle vulnerabilità del sistema operativo, di SQL e delle macchine virtuali nel Centro sicurezza di Azure. Centro sicurezza di Azure fornisce funzionalità di report che consentono di ricevere informazioni dettagliate in tempo reale sullo stato di sicurezza delle risorse di Azure distribuite. Questo progetto assegna anche una definizione di criteri che assicura l'aggiornamento automatico del sistema operativo per i set di scalabilità di macchine virtuali.

- \[Anteprima\]: Controllare gli aggiornamenti del sistema mancanti nei set di scalabilità di macchine virtuali nel Centro sicurezza di Azure
- \[Anteprima\]: Controllare le vulnerabilità del sistema operativo dei set di scalabilità di macchine virtuali nel Centro sicurezza di Azure
- \[Anteprima\]: Monitorare gli aggiornamenti di sistema mancanti nel Centro sicurezza di Azure
- \[Anteprima\]: Monitorare le vulnerabilità del sistema operativo nel Centro sicurezza di Azure
- \[Anteprima\]: Monitorare i risultati della valutazione della vulnerabilità di SQL nel Centro sicurezza di Azure
- \[Anteprima\]: Monitorare le vulnerabilità delle macchine virtuali nel Centro sicurezza di Azure
- Imponi aggiornamento automatico del sistema operativo con controlli di integrità delle app per i set di scalabilità di macchine virtuali

## <a name="si-3-malicious-code-protection"></a>SI-3 Protezione dal malware

Questo progetto consente di gestire Endpoint Protection, inclusa la protezione dal malware, assegnando tre definizioni di [Criteri di Azure](../../../policy/overview.md) per monitorare la presenza di Endpoint Protection nelle macchine virtuali in Centro sicurezza di Azure e imporre la soluzione antimalware Microsoft nelle macchine virtuali Windows.

- \[Anteprima\]: Controllare la soluzione Endpoint Protection nei set di scalabilità di macchine virtuali nel Centro sicurezza di Azure
- \[Anteprima\]: Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure
- Distribuisci estensione IaaSAntimalware Microsoft predefinita per Windows Server

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) Protezione dal malware | Gestione centrale

Questo progetto consente di gestire Endpoint Protection, inclusa la protezione dal malware, assegnando due definizioni di [Criteri di Azure](../../../policy/overview.md) per monitorare la presenza di Endpoint Protection nelle macchine virtuali in Centro sicurezza di Azure. Centro sicurezza di Azure fornisce funzionalità centralizzate di gestione e creazione di report che consentono di ricevere informazioni dettagliate in tempo reale sullo stato di sicurezza delle risorse di Azure distribuite.

- \[Anteprima\]: Controllare la soluzione Endpoint Protection nei set di scalabilità di macchine virtuali nel Centro sicurezza di Azure
- \[Anteprima\]: Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure

## <a name="si-4-information-system-monitoring"></a>SI-4 Monitoraggio dei sistemi informativi

Questo progetto consente di monitorare il sistema controllando e imponendo la registrazione e la sicurezza dei dati nelle risorse di Azure. In particolare, i criteri assegnati controllano e impongono la distribuzione dell'agente di Log Analytics, nonché impostazioni di sicurezza avanzata per database SQL, account di archiviazione e risorse di rete. Queste funzionalità consentono di rilevare comportamenti anomali e indicatori di attacchi in modo che sia possibile adottare le misure appropriate.

- \[Anteprima\]: Controlla la distribuzione dell'agente di Log Analytics - Immagine macchina virtuale (sistema operativo) non in elenco
- \[Anteprima\]: Controlla la distribuzione dell'agente di Log Analytics nei set di scalabilità di macchine virtuali - Immagine macchina virtuale (sistema operativo) non in elenco
- \[Anteprima\]: Controlla area di lavoro Log Analytics per la macchina virtuale - Segnala mancata corrispondenza
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Linux
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Linux
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Windows
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Windows
- Controlla istanze gestite di SQL senza Sicurezza dei dati avanzata
- Controlla server SQL senza Sicurezza dei dati avanzata
- Distribuisci Sicurezza dei dati avanzata nei server SQL
- Distribuisci Advanced Threat Protection negli account di archiviazione
- Distribuisci il controllo nei server SQL
- Distribuisci Network Watcher quando vengono create reti virtuali
- Distribuisci Rilevamento minacce nelle istanze di SQL Server

## <a name="si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>SI-4 (18) Monitoraggio dei sistemi informativi | Analizzare il traffico/esfiltrazioni nascoste

Advanced Threat Protection per Archiviazione di Azure rileva tentativi insoliti e potenzialmente dannosi di accesso o sfruttamento degli account di archiviazione. Gli avvisi di protezione includono modelli di accesso anomalo, estrazioni/caricamenti anomali e attività sospette. Questi indicatori possono essere utili per rilevare l'esfiltrazione occulta delle informazioni.

- Distribuisci Advanced Threat Protection negli account di archiviazione

## <a name="next-steps"></a>Passaggi successivi

Dopo aver esaminato il mapping dei controlli del progetto NIST SP 800-53 R4, vedere gli articoli seguenti per informazioni sul progetto e su come distribuire questo esempio:

> [!div class="nextstepaction"]
> [Progetto NIST SP 800-53 R4 - Panoramica](./index.md)
> [Progetto NIST SP 800-53 R4 - Passaggi per la distribuzione](./deploy.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).
