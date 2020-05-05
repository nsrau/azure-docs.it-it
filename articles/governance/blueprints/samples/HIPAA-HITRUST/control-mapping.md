---
title: Controlli dell'esempio di progetto HIPAA HITRUST
description: Mapping dei controlli dell'esempio di progetto HIPAA HITRUST. Ogni controllo viene mappato a uno o più criteri di Azure che assistono nella valutazione.
ms.date: 11/26/2019
ms.topic: sample
ms.openlocfilehash: c6680055cfe41a2f893d46cba797783913d880ce
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75470746"
---
# <a name="control-mapping-of-the-hipaa-hitrust-blueprint-sample"></a>Mapping dei controlli dell'esempio di progetto HIPAA HITRUST

L'articolo seguente descrive in dettaglio il mapping dell'esempio di progetto HIPAA HITRUST di Azure Blueprints ai controlli HIPAA HITRUST. Per altre informazioni sui controlli, vedere [HIPAA HITRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

I mapping seguenti fanno riferimento ai controlli **HIPAA HITRUST**. Usare la barra di spostamento a destra per passare direttamente a uno specifico mapping. Molti controlli mappati vengono implementati con un'iniziativa di [Criteri di Azure](../../../policy/overview.md). Per esaminare l'iniziativa completa, aprire **Criteri** nel portale di Azure e selezionare la pagina **Definizioni**. Trovare e selezionare l'iniziativa dei criteri predefinita **\[Anteprima\]: Verifica controlli HITRUST HIPAA**.

> [!IMPORTANT]
> Ogni controllo tra quelli riportati di seguito è associato a una o più definizioni di [Criteri di Azure](../../../policy/overview.md). Questi criteri possono aiutare a [valutare la conformità](../../../policy/how-to/get-compliance-data.md) con il controllo. In molti casi tuttavia non si tratta di una corrispondenza 1:1 o completa tra un controllo e uno o più criteri. Di per sé, **Conforme** in Criteri di Azure si riferisce solo ai criteri stessi e non garantisce che l'utente sia completamente conforme a tutti i requisiti di un controllo. Inoltre, in questo momento lo standard di conformità include controlli che non vengono gestiti da alcuna definizione di Criteri di Azure. La conformità in Criteri di Azure è quindi solo una visualizzazione parziale dello stato di conformità generale. Le associazioni tra i controlli e le definizioni di Criteri di Azure per questo esempio di progetto di conformità possono cambiare nel tempo. Per visualizzare la cronologia delle modifiche, vedere la [cronologia dei commit di GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/HIPAA-HITRUST/control-mapping.md).

## <a name="control-against-malicious-code"></a>Controllo del codice dannoso

Questo progetto consente di gestire Endpoint Protection, inclusa la protezione dal malware, assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che monitorano la presenza di Endpoint Protection nelle macchine virtuali nel Centro sicurezza di Azure e impongono la soluzione antimalware Microsoft nelle macchine virtuali Windows.

- Distribuisci estensione IaaSAntimalware Microsoft predefinita per Windows Server
- È consigliabile abilitare i log di diagnostica negli account Batch
- Gli aggiornamenti di sistema devono essere installati nelle macchine


## <a name="management-of-removable-media"></a>Gestione dei supporti rimovibili

L'organizzazione, in base al livello di classificazione dei dati, registra i supporti (compresi i computer portatili) prima dell'utilizzo, impone restrizioni ragionevoli sulla modalità di utilizzo dei supporti e fornisce un livello appropriato di protezione fisica e logica (inclusa la crittografia) per i supporti contenenti le informazioni pertinenti fino a quando non vengono eliminati definitivamente o purificati in modo appropriato.

- Imponi crittografia per gli account Data Lake Store
- È consigliabile che la protezione TDE dell'istanza gestita di SQL sia crittografata con una chiave personalizzata
- La crittografia del disco deve essere applicata nelle macchine virtuali
- Transparent Data Encryption deve essere abilitata nei database SQL


## <a name="information-exchange-policies-and-procedures"></a>Criteri e procedure di scambio delle informazioni

I provider di servizi cloud usano una piattaforma di virtualizzazione riconosciuta dal settore e formati di virtualizzazione standard (ad esempio, Open Virtualization Format, OVF) per garantire l'interoperabilità e documentano le modifiche personalizzate apportate a qualsiasi hypervisor in uso e a tutti gli hook di virtualizzazione specifici della soluzione disponibili per l'analisi da parte dei clienti.

- Distribuisci i prerequisiti per controllare le macchine virtuali Windows in cui non sono installate le applicazioni specificate

## <a name="control-of-operational-software"></a>Controllo del software operativo 

L'organizzazione identifica un software non autorizzato nel sistema informatico, tra cui server, workstation e portatili, adotta criteri di tipo Allow-all, Deny-by-Exception per impedire l'esecuzione di software noto non autorizzato nel sistema informatico ed esamina e aggiorna periodicamente l'elenco di software non autorizzato, su base annuale.

- \[Anteprima\]: Mostra i risultati del controllo dalle configurazioni delle macchine virtuali Windows in "Opzioni di sicurezza - Controllo"
- \[Anteprima\]: Mostra i risultati del controllo dalle configurazioni delle macchine virtuali Windows in "Criteri di controllo sistema - Gestione account"

## <a name="change-control-procedures"></a>Procedure di controllo delle modifiche

L'integrità di tutte le immagini delle macchine virtuali è garantita in qualsiasi momento mediante la registrazione e la generazione di un avviso per le modifiche apportate alle immagini delle macchine virtuali, rendendo disponibili per i proprietari e/o i clienti aziendali tramite metodi elettronici, ad esempio portali o avvisi, i risultati di una modifica o spostamento e la successiva convalida dell'integrità dell'immagine.

- \[Anteprima\]: Mostra i risultati del controllo dalle configurazioni delle macchine virtuali Windows in 'Criteri di controllo sistema - Analisi dettagliata'

## <a name="inventory-of-assets"></a>Inventario degli asset 

Viene mantenuto un inventario di asset e servizi

- È consigliabile abilitare i log di diagnostica nei servizi di ricerca.
- \[Anteprima\]: Distribuisci i requisiti per controllare le configurazioni delle macchine virtuali Windows in "Opzioni di sicurezza - Server di rete Microsoft"
- \[Anteprima\]: Distribuisci i requisiti per controllare le configurazioni delle macchine virtuali Windows in "Modelli amministrativi - Rete"

## <a name="control-of-technical-vulnerabilities"></a>Controllo delle vulnerabilità tecniche 

Esiste uno standard di configurazione con protezione avanzata per tutti i componenti di sistema e di rete.

- Controlla macchine virtuali in cui non è configurato il ripristino di emergenza
- La valutazione delle vulnerabilità deve essere abilitata nelle istanze gestite di SQL
- È consigliabile correggere le vulnerabilità tramite una soluzione di valutazione della vulnerabilità

## <a name="segregation-in-networks"></a>Separazione nelle reti

I gateway di sicurezza dell'organizzazione, ad esempio i firewall, impongono i criteri di sicurezza e sono configurati per filtrare il traffico tra domini, bloccare l'accesso non autorizzato e vengono usati per mantenere la separazione tra segmenti di rete cablati interni, wireless interni ed esterni, ad esempio Internet, incluse le reti perimetrali, e impongono i criteri di controllo di accesso per ogni dominio.

- Provisioning automatico dell'agente di monitoraggio della sicurezza
- Distribuisci Network Watcher quando vengono create reti virtuali

## <a name="input-data-validation"></a>Convalida dei dati di input

Per tutte le applicazioni Web pubbliche, sono implementati firewall a livello dell'applicazione per controllare il traffico. Per le applicazioni pubbliche che non sono basate sul Web, l'organizzazione ha implementato un firewall basato su rete specifico per il tipo di applicazione. Se il traffico verso l'applicazione pubblica è crittografato, il dispositivo è protetto tramite crittografia o è in grado di decrittografare il traffico prima dell'analisi.

- \[Anteprima\]: Mostra i risultati del controllo dalle configurazioni delle macchine virtuali Windows in "Proprietà Windows Firewall"


## <a name="network-connection-control"></a>Controllo delle connessioni di rete

Il traffico di rete viene controllato in base ai criteri di controllo di accesso dell'organizzazione tramite firewall e altre restrizioni relative alla rete per ogni punto di accesso alla rete o interfaccia gestita del servizio di telecomunicazione esterno.

- È consigliabile limitare l'accesso tramite endpoint connessi a Internet
- È consigliabile che la protezione TDE dell'istanza gestita di SQL sia crittografata con una chiave personalizzata
- Il debug remoto deve essere disattivato per le app per le API

## <a name="network-controls"></a>Controlli di rete

L'organizzazione usa canali di comunicazione protetti e crittografati durante la migrazione di server fisici, applicazioni o dati a server virtualizzati.

- È consigliabile applicare la crittografia del disco nelle macchine virtuali
- È consigliabile che la protezione TDE di SQL Server sia crittografata con una chiave personalizzata
- \[Anteprima\]: Mostra i risultati del controllo dalle configurazioni delle macchine virtuali Windows in "Opzioni di sicurezza - Accesso di rete"
- Controlla l'accesso di rete senza restrizioni agli account di archiviazione
- \[Anteprima\]: Mostra i risultati del controllo dalle configurazioni delle macchine virtuali Windows in "Proprietà Windows Firewall"
- Distribuisci impostazioni di diagnostica per i gruppi di sicurezza di rete
- È consigliabile limitare l'accesso tramite endpoint connessi a Internet

## <a name="sensitive-system-isolation"></a>Isolamento delle risorse sensibili del sistema

Le risorse di sistema condivise, ad esempio, registri, memoria principale, risorse di archiviazione secondaria, vengono rilasciate di nuovo al sistema, protette dalla divulgazione ad altri sistemi/applicazioni/utenti e gli utenti non possono accedere intenzionalmente o accidentalmente tali informazioni.

- È consigliabile eseguire la migrazione delle macchine virtuali alle nuove risorse di Azure Resource Manager

## <a name="security-of-network-services"></a>Sicurezza dei servizi di rete

I servizi concordati offerti da un gestore/provider di servizi di rete vengono gestiti e monitorati in modo formale per assicurarsi che vengano forniti in modo sicuro.

- È consigliabile eseguire la migrazione delle macchine virtuali alle nuove risorse di Azure Resource Manager

## <a name="network-routing-control"></a>Controllo del routing di rete

I controlli di routing vengono implementati tramite gateway di sicurezza, ad esempio i firewall, usati tra reti interne ed esterne, come Internet e reti di terze parti.

- È consigliabile abilitare i controlli applicazioni adattivi nelle macchine virtuali

## <a name="information-exchange-policies-and-procedures"></a>Criteri e procedure di scambio delle informazioni

L'organizzazione limita l'uso di supporti di archiviazione portatili controllati dall'organizzazione da parte di singoli utenti autorizzati in sistemi informatici esterni.

- Controlla l'accesso di rete senza restrizioni agli account di archiviazione
- Il debug remoto deve essere disattivato per le applicazioni Web
- L'app per le API deve essere accessibile solo tramite HTTPS

## <a name="electronic-messaging"></a>Messaggistica elettronica

Le approvazioni vengono ottenute prima di usare servizi pubblici esterni, inclusa la messaggistica immediata o la condivisione di file.

- \[Anteprima\]: Mostra i risultati del controllo dalle macchine virtuali Linux in cui le autorizzazioni per il file passwd non sono impostate su 0644

## <a name="on-line-transactions"></a>Transazioni online

L'organizzazione richiede l'utilizzo della crittografia tra le parti coinvolte nella transazione e l'utilizzo di firme elettroniche da parte di ogni entità coinvolta nella transazione. L'organizzazione garantisce che l'archiviazione dei dettagli della transazione si trovi all'esterno degli ambienti accessibili pubblicamente, ad esempio in una piattaforma di archiviazione che risiede nella rete Intranet dell'organizzazione e non conservata ed esposta in un supporto di archiviazione direttamente accessibile da Internet. Quando viene usata un'autorità attendibile, ad esempio ai fini del rilascio e della gestione di firme digitali e/o certificati digitali, la sicurezza è integrata e incorporata nell'intero processo di gestione di certificati/firme end-to-end.

- È consigliabile applicare la crittografia del disco nelle macchine virtuali
- \[Anteprima\]: Mostra i risultati del controllo dalle macchine virtuali Windows che non contengono i certificati specificati nell'archivio certificati Autorità di certificazione radice disponibile nell'elenco locale

## <a name="password-management"></a>Gestione delle password

Le password vengono crittografate durante la trasmissione e l'archiviazione in tutti i componenti del sistema.

- \[Anteprima\]: Mostra i risultati del controllo dalle macchine virtuali Windows in cui non è abilitata l'impostazione relativa alla complessità della password

## <a name="user-authentication-for-external-connections"></a>Autenticazione utente per le connessioni esterne

Sono implementati metodi di autenticazione avanzata, ad esempio a più fattori, RADIUS o Kerberos (per l'accesso con privilegi) e CHAP (per la crittografia delle credenziali per i metodi di connessione remota) per tutte le connessioni esterne alla rete delle organizzazioni.

- L'autenticazione MFA deve essere abilitata per gli account con autorizzazioni di scrittura per la sottoscrizione
- Il controllo di accesso alla rete JIT deve essere applicato alle macchine virtuali

## <a name="user-identification-and-authentication"></a>Identificazione e autenticazione utente

Gli utenti che hanno eseguito funzioni con privilegi, ad esempio l'amministrazione del sistema, usano account distinti quando eseguono tali funzioni con privilegi. I metodi di autenticazione a più fattori vengono usati in base ai criteri dell'organizzazione, ad esempio per l'accesso remoto alla rete.

- L'autenticazione MFA deve essere abilitata per gli account con autorizzazioni di scrittura per la sottoscrizione
- Il controllo di accesso alla rete JIT deve essere applicato alle macchine virtuali

## <a name="privilege-management"></a>Gestione dei privilegi

L'accesso alle funzioni di gestione o alle console amministrative per i sistemi che ospitano sistemi virtualizzati è limitato al personale in base al principio del privilegio minimo e supportato tramite controlli tecnici.

- Il controllo di accesso alla rete JIT deve essere applicato alle macchine virtuali
- \[Anteprima\]: Nei servizi Kubernetes deve essere usato il controllo degli accessi in base al ruolo

## <a name="review-of-user-access-rights"></a>Revisione dei diritti di accesso utente

L'organizzazione gestisce un elenco documentato di utenti autorizzati di asset informativi.

- \[Anteprima\]: Mostra i risultati del controllo dalle configurazioni delle macchine virtuali Windows in "Opzioni di sicurezza - Account"

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Protezione delle porte di configurazione e diagnostica remota

Porte, servizi e applicazioni simili installate in un computer o in sistemi di rete e che non sono specificamente necessari per le funzionalità aziendali sono disabilitati o rimossi.

- È consigliabile chiudere le porte di gestione nelle macchine virtuali
- È consigliabile correggere le vulnerabilità nella configurazione di sicurezza nei set di scalabilità di macchine virtuali

## <a name="audit-logging"></a>Registrazione di controllo

I log dei messaggi inviati e ricevuti vengono conservati, inclusi la data, l'ora, l'origine e la destinazione del messaggio, ma non il relativo contenuto. Il controllo è sempre disponibile quando il sistema è attivo e tiene traccia degli eventi principali, dell'accesso ai dati con esito positivo o negativo, delle modifiche alla configurazione della sicurezza del sistema, dell'uso di privilegi o utilità, degli avvisi generati, dell'attivazione e della disattivazione dei sistemi di protezione, ad esempio antivirus e gestione delle identità, dell'attivazione e della disattivazione dei meccanismi di identificazione e autenticazione e della creazione e dell'eliminazione di oggetti a livello del sistema.

- È consigliabile abilitare i log di diagnostica in Hub eventi
- Gli aggiornamenti di sistema nei set di scalabilità di macchine virtuali devono essere installati

## <a name="monitoring-system-use"></a>Utilizzo del sistema di monitoraggio

I sistemi automatizzati distribuiti nell'ambiente dell'organizzazione vengono usati per monitorare gli eventi principali e le attività anomale e analizzare i log di sistema, i cui risultati vengono esaminati regolarmente. Il monitoraggio include operazioni con privilegi, tentativi di accesso autorizzato o non autorizzato, inclusi i tentativi di accesso ad account disattivati, e avvisi o errori del sistema.

- È consigliabile abilitare i log di diagnostica nei set di scalabilità di macchine virtuali

## <a name="segregation-of-duties"></a>Separazione dei compiti

La separazione dei compiti consente di limitare il rischio di modifiche non autorizzate o non intenzionali di informazioni e sistemi. Nessun utente singolo può accedere, modificare o usare i sistemi informatici senza autorizzazione o senza che venga rilevato. L'accesso per gli utenti responsabili dell'amministrazione e dei controlli di accesso è limitato al minimo necessario in base al ruolo e alle responsabilità di ogni utente e tali utenti non possono accedere alle funzioni di controllo correlate a tali controlli.

- L'autenticazione MFA deve essere abilitata per gli account con autorizzazioni di scrittura per la sottoscrizione

## <a name="administrator-and-operator-logs"></a>Log amministratore e operatore

L'organizzazione garantisce che sia abilitata la registrazione appropriata ai fini del controllo delle attività dell'amministratore ed esamina regolarmente i log degli operatori e degli amministratori di sistema.

- L'autenticazione MFA deve essere abilitata per gli account con autorizzazioni di scrittura per la sottoscrizione

## <a name="identification-of-risks-related-to-external-parties"></a>Identificazione dei rischi correlati a entità esterne

Le connessioni di accesso remoto tra l'organizzazione e le parti esterne sono crittografate

- È consigliabile applicare la crittografia del disco nelle macchine virtuali

## <a name="business-continuity-and-risk-assessment"></a>Continuità aziendale e valutazione del rischio

L'organizzazione identifica i processi aziendali critici e integra i requisiti di gestione della sicurezza delle informazioni per la continuità aziendale con altri requisiti di continuità relativi ad aspetti quali operazioni, personale, materiali, trasporto e strutture.

- \[Anteprima\]: Mostra i risultati del controllo dalle configurazioni delle macchine virtuali Windows in "Opzioni di sicurezza - Console di ripristino di emergenza"

> [!NOTE]
> La disponibilità di specifiche definizioni di Criteri di Azure può variare in Azure per enti pubblici e in altri cloud nazionali. 

## <a name="next-steps"></a>Passaggi successivi

È stato esaminato il mapping dei controlli dell'esempio di progetto HIPAA HITRUST. Vedere gli articoli seguenti per informazioni sulla panoramica e su come distribuire questo esempio:

> [!div class="next step action"]
> [Progetto HIPAA HITRUST - Panoramica](./control-mapping.md)
> [Progetto HIPAA HITRUST - Fasi di distribuzione](./deploy.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).
