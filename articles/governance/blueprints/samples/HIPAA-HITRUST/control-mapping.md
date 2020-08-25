---
title: Controlli dell'esempio di progetto HIPAA HITRUST
description: Mapping dei controlli dell'esempio di progetto HIPAA HITRUST. Ogni controllo viene mappato a uno o più criteri di Azure che assistono nella valutazione.
ms.date: 08/03/2020
ms.topic: sample
ms.openlocfilehash: 10b771e3cfb18a28bd720332a26e13bb1d1f6022
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88209417"
---
# <a name="control-mapping-of-the-hipaa-hitrust-blueprint-sample"></a>Mapping dei controlli dell'esempio di progetto HIPAA HITRUST

L'articolo seguente descrive in dettaglio il mapping dell'esempio di progetto HIPAA HITRUST di Azure Blueprints ai controlli HIPAA HITRUST. Per altre informazioni sui controlli, vedere [HIPAA HITRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

I mapping seguenti fanno riferimento ai controlli **HIPAA HITRUST**. Usare la barra di spostamento a destra per passare direttamente a uno specifico mapping. Molti controlli mappati vengono implementati con un'iniziativa di [Criteri di Azure](../../../policy/overview.md). Per esaminare l'iniziativa completa, aprire **Criteri** nel portale di Azure e selezionare la pagina **Definizioni**. Trovare e selezionare l'iniziativa dei criteri predefinita **\[Anteprima\]: Verifica controlli HITRUST HIPAA**.

> [!IMPORTANT]
> Ogni controllo tra quelli riportati di seguito è associato a una o più definizioni di [Criteri di Azure](../../../policy/overview.md). Questi criteri possono aiutare a [valutare la conformità](../../../policy/how-to/get-compliance-data.md) con il controllo. In molti casi tuttavia non si tratta di una corrispondenza 1:1 o completa tra un controllo e uno o più criteri. Di per sé, **Conforme** in Criteri di Azure si riferisce solo ai criteri stessi e non garantisce che l'utente sia completamente conforme a tutti i requisiti di un controllo. Inoltre, in questo momento lo standard di conformità include controlli che non vengono gestiti da alcuna definizione di Criteri di Azure. La conformità in Criteri di Azure è quindi solo una visualizzazione parziale dello stato di conformità generale. Le associazioni tra i controlli e le definizioni di Criteri di Azure per questo esempio di progetto di conformità possono cambiare nel tempo. Per visualizzare la cronologia delle modifiche, vedere la [cronologia dei commit di GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/HIPAA-HITRUST/control-mapping.md).

## <a name="control-against-malicious-code"></a>Controllo del codice dannoso

Questo progetto consente di gestire Endpoint Protection, inclusa la protezione dal malware, assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che monitorano la presenza di Endpoint Protection nelle macchine virtuali nel Centro sicurezza di Azure e impongono la soluzione antimalware Microsoft nelle macchine virtuali Windows.

- È consigliabile configurare Microsoft Antimalware per Azure per aggiornare automaticamente le firme di protezione
- Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure
- La soluzione Endpoint Protection deve essere installata nei set di scalabilità di macchine virtuali
- I controlli applicazioni adattivi devono essere abilitati nelle macchine virtuali


## <a name="management-of-removable-media"></a>Gestione dei supporti rimovibili

L'organizzazione, in base al livello di classificazione dei dati, registra i supporti (compresi i computer portatili) prima dell'utilizzo, impone restrizioni ragionevoli sulla modalità di utilizzo dei supporti e fornisce un livello appropriato di protezione fisica e logica (inclusa la crittografia) per i supporti contenenti le informazioni pertinenti fino a quando non vengono eliminati definitivamente o purificati in modo appropriato.

- Transparent Data Encryption deve essere abilitata nei database SQL
- La crittografia del disco deve essere applicata nelle macchine virtuali
- È consigliabile crittografare i dischi non collegati
- Imponi crittografia per gli account Data Lake Store
- È consigliabile che la protezione TDE di SQL Server sia crittografata con una chiave personalizzata
- È consigliabile che la protezione TDE dell'istanza gestita di SQL sia crittografata con una chiave personalizzata

## <a name="control-of-operational-software"></a>Controllo del software operativo 

L'organizzazione identifica un software non autorizzato nel sistema informatico, tra cui server, workstation e portatili, adotta criteri di tipo Allow-all, Deny-by-Exception per impedire l'esecuzione di software noto non autorizzato nel sistema informatico ed esamina e aggiorna periodicamente l'elenco di software non autorizzato, su base annuale.

- Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte
- È consigliabile correggere le vulnerabilità nelle configurazioni della sicurezza dei contenitori
- Le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali devono essere risolte
- I controlli applicazioni adattivi devono essere abilitati nelle macchine virtuali

## <a name="change-control-procedures"></a>Procedure di controllo delle modifiche

L'integrità di tutte le immagini delle macchine virtuali è garantita in qualsiasi momento mediante la registrazione e la generazione di un avviso per le modifiche apportate alle immagini delle macchine virtuali, rendendo disponibili per i proprietari e/o i clienti aziendali tramite metodi elettronici, ad esempio portali o avvisi, i risultati di una modifica o spostamento e la successiva convalida dell'integrità dell'immagine.

- \[Anteprima\]: Mostra i risultati del controllo dalle configurazioni delle macchine virtuali Windows in 'Criteri di controllo sistema - Analisi dettagliata'
- \[Anteprima\]: Mostra i risultati del controllo dalle configurazioni delle macchine virtuali Windows in 'Criteri di controllo sistema - Analisi dettagliata'

## <a name="control-of-technical-vulnerabilities"></a>Controllo delle vulnerabilità tecniche 

Esiste uno standard di configurazione con protezione avanzata per tutti i componenti di sistema e di rete.

- La valutazione delle vulnerabilità deve essere abilitata nei server SQL
- La valutazione delle vulnerabilità deve essere abilitata nelle istanze gestite di SQL
- \[Anteprima\] La soluzione Valutazione della vulnerabilità deve essere abilitata nelle macchine virtuali
- Le vulnerabilità devono essere risolte tramite una soluzione di valutazione della vulnerabilità
- Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte
- Le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali devono essere risolte
- È consigliabile correggere le vulnerabilità nelle configurazioni della sicurezza dei contenitori
- Le vulnerabilità dei database SQL devono essere risolte
- \[Anteprima\]: I criteri di sicurezza pod devono essere definiti nei servizi Kubernetes

## <a name="segregation-in-networks"></a>Separazione nelle reti

I gateway di sicurezza dell'organizzazione, ad esempio i firewall, impongono i criteri di sicurezza e sono configurati per filtrare il traffico tra domini, bloccare l'accesso non autorizzato e vengono usati per mantenere la separazione tra segmenti di rete cablati interni, wireless interni ed esterni, ad esempio Internet, incluse le reti perimetrali, e impongono i criteri di controllo di accesso per ogni dominio.

- Le subnet devono essere associate a un gruppo di sicurezza di rete
- Le macchine virtuali devono essere connesse a una rete virtuale approvata
- Le macchine virtuali devono essere associate a un gruppo di sicurezza di rete
- Il bus di servizio deve usare un endpoint servizio di rete virtuale
- Il servizio app deve usare un endpoint servizio di rete virtuale
- I server SQL devono usare un endpoint servizio di rete virtuale
- Gli hub eventi devono usare un endpoint servizio di rete virtuale
- I database Cosmos DB devono usare un endpoint servizio di rete virtuale
- Gli insieme di credenziali delle chiavi devono usare un endpoint servizio di rete virtuale
- Le subnet del gateway non devono essere configurate con un gruppo di sicurezza di rete
- Gli account di archiviazione devono usare un endpoint servizio di rete virtuale
- \[Anteprima\]: I Registri Azure Container devono usare un endpoint servizio di rete virtuale
- Le raccomandazioni di Protezione avanzata adattiva per la rete devono essere applicate alle macchine virtuali interne

## <a name="network-connection-control"></a>Controllo delle connessioni di rete

Il traffico di rete viene controllato in base ai criteri di controllo di accesso dell'organizzazione tramite firewall e altre restrizioni relative alla rete per ogni punto di accesso alla rete o interfaccia gestita del servizio di telecomunicazione esterno.

- Il trasferimento sicuro negli account di archiviazione deve essere abilitato
- Nell'app per le API è necessario usare la versione più recente di TLS
- Nell'app Web è necessario usare la versione più recente di TLS
- Nell'app per le funzioni è necessario usare la versione più recente di TLS
- L'app per le funzioni deve essere accessibile solo tramite HTTPS
- L'applicazione Web deve essere accessibile solo tramite HTTPS
- L'app per le API deve essere accessibile solo tramite HTTPS
- Il criterio Imponi connessione SSL deve essere abilitato per i server di database MySQL
- Il criterio Imponi connessione SSL deve essere abilitato per i server di database PostgreSQL
- Devono essere abilitate solo connessioni sicure alla Cache Redis
- Le subnet devono essere associate a un gruppo di sicurezza di rete
- Le regole per i gruppi di sicurezza di rete delle applicazioni Web in IaaS devono essere rafforzate
- Le regole per i gruppi di sicurezza di rete delle macchine virtuali con connessione Internet devono essere rafforzate
- Le macchine virtuali devono essere connesse a una rete virtuale approvata
- Le macchine virtuali devono essere associate a un gruppo di sicurezza di rete

## <a name="network-controls"></a>Controlli di rete

L'organizzazione usa canali di comunicazione protetti e crittografati durante la migrazione di server fisici, applicazioni o dati a server virtualizzati.

- Alle macchine virtuali deve essere applicato il controllo di accesso alla rete JIT
- Le raccomandazioni di Protezione avanzata adattiva per la rete devono essere applicate alle macchine virtuali interne
- Il bus di servizio deve usare un endpoint servizio di rete virtuale
- Il servizio app deve usare un endpoint servizio di rete virtuale
- I server SQL devono usare un endpoint servizio di rete virtuale
- Gli hub eventi devono usare un endpoint servizio di rete virtuale
- I database Cosmos DB devono usare un endpoint servizio di rete virtuale
- Gli insieme di credenziali delle chiavi devono usare un endpoint servizio di rete virtuale
- Controlla l'accesso di rete senza restrizioni agli account di archiviazione
- Gli account di archiviazione devono usare un endpoint servizio di rete virtuale
- \[Anteprima\]: I Registri Azure Container devono usare un endpoint servizio di rete virtuale

## <a name="security-of-network-services"></a>Sicurezza dei servizi di rete

I servizi concordati offerti da un gestore/provider di servizi di rete vengono gestiti e monitorati in modo formale per assicurarsi che vengano forniti in modo sicuro.

- \[Anteprima\]: L'agente di raccolta dati sul traffico di rete deve essere installato nelle macchine virtuali Windows
- \[Anteprima\]: L'agente di raccolta dati sul traffico di rete deve essere installato nelle macchine virtuali Linux ed è consigliabile abilitare Network Watcher

## <a name="information-exchange-policies-and-procedures"></a>Criteri e procedure di scambio delle informazioni

L'organizzazione limita l'uso di supporti di archiviazione portatili controllati dall'organizzazione da parte di singoli utenti autorizzati in sistemi informatici esterni.

- Assicurarsi che l'opzione "Certificati client (certificati client in ingresso)" dell'app Web sia impostata su 'Sì'
- CORS non deve consentire a tutte le risorse di accedere alle applicazioni Web
- CORS non deve consentire a tutte le risorse di accedere alle app per le funzioni
- CORS non deve consentire a tutte le risorse di accedere all'app per le API dell'utente
- Il debug remoto deve essere disattivato per le applicazioni Web
- Il debug remoto deve essere disattivato per le app per le funzioni
- Il debug remoto deve essere disattivato per le app per le API

## <a name="on-line-transactions"></a>Transazioni online

L'organizzazione richiede l'utilizzo della crittografia tra le parti coinvolte nella transazione e l'utilizzo di firme elettroniche da parte di ogni entità coinvolta nella transazione. L'organizzazione garantisce che l'archiviazione dei dettagli della transazione si trovi all'esterno degli ambienti accessibili pubblicamente, ad esempio in una piattaforma di archiviazione che risiede nella rete Intranet dell'organizzazione e non conservata ed esposta in un supporto di archiviazione direttamente accessibile da Internet. Quando viene usata un'autorità attendibile, ad esempio ai fini del rilascio e della gestione di firme digitali e/o certificati digitali, la sicurezza è integrata e incorporata nell'intero processo di gestione di certificati/firme end-to-end.

- Il trasferimento sicuro negli account di archiviazione deve essere abilitato
- Nell'app per le API è necessario usare la versione più recente di TLS
- Nell'app Web è necessario usare la versione più recente di TLS
- Nell'app per le funzioni è necessario usare la versione più recente di TLS
- L'app per le funzioni deve essere accessibile solo tramite HTTPS
- L'applicazione Web deve essere accessibile solo tramite HTTPS
- L'app per le API deve essere accessibile solo tramite HTTPS
- Il criterio Imponi connessione SSL deve essere abilitato per i server di database MySQL
- Il criterio Imponi connessione SSL deve essere abilitato per i server di database PostgreSQL
- Devono essere abilitate solo connessioni sicure alla Cache Redis
- \[Anteprima\]: Distribuisci i prerequisiti per controllare le macchine virtuali Windows che non contengono i certificati specificati nell'archivio certificati Autorità di certificazione radice disponibile nell'elenco locale
- \[Anteprima\]: Mostra i risultati del controllo dalle macchine virtuali Windows che non contengono i certificati specificati nell'archivio certificati Autorità di certificazione radice disponibile nell'elenco locale

## <a name="user-password-management"></a>Gestione delle password utente

Le password vengono crittografate durante la trasmissione e l'archiviazione in tutti i componenti del sistema.

- \[Anteprima\]: Controlla le macchine virtuali con impostazioni di sicurezza delle password non sicure

## <a name="user-authentication-for-external-connections"></a>Autenticazione utente per le connessioni esterne

Sono implementati metodi di autenticazione avanzata, ad esempio a più fattori, RADIUS o Kerberos (per l'accesso con privilegi) e CHAP (per la crittografia delle credenziali per i metodi di connessione remota) per tutte le connessioni esterne alla rete delle organizzazioni.

- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione
- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di scrittura per la sottoscrizione
- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di lettura per la sottoscrizione
- Alle macchine virtuali deve essere applicato il controllo di accesso alla rete JIT

## <a name="user-identification-and-authentication"></a>Identificazione e autenticazione utente

Gli utenti che hanno eseguito funzioni con privilegi, ad esempio l'amministrazione del sistema, usano account distinti quando eseguono tali funzioni con privilegi. I metodi di autenticazione a più fattori vengono usati in base ai criteri dell'organizzazione, ad esempio per l'accesso remoto alla rete.

- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione
- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di scrittura per la sottoscrizione
- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di lettura per la sottoscrizione
- Per la sottoscrizione devono essere designati al massimo 3 proprietari
- Alla sottoscrizione deve essere assegnato più di un proprietario
- Distribuisci i prerequisiti per controllare le macchine virtuali Windows in cui il gruppo Administrators contiene uno qualsiasi dei membri specificati
- Mostra i risultati del controllo dalle macchine virtuali Windows in cui il gruppo Administrators contiene uno qualsiasi dei membri specificati
- Distribuisci i prerequisiti per controllare le macchine virtuali Windows in cui il gruppo Administrators non contiene tutti i membri specificati
- Mostra i risultati del controllo dalle macchine virtuali Windows in cui il gruppo Administrators non contiene tutti i membri specificati
- Distribuisci i prerequisiti per controllare le macchine virtuali Windows in cui il gruppo Administrators non contiene solo i membri specificati
- Mostra i risultati del controllo dalle macchine virtuali Windows in cui il gruppo Administrators non contiene solo i membri specificati

## <a name="privilege-management"></a>Gestione dei privilegi

L'accesso alle funzioni di gestione o alle console amministrative per i sistemi che ospitano sistemi virtualizzati è limitato al personale in base al principio del privilegio minimo e supportato tramite controlli tecnici.

- Alle macchine virtuali deve essere applicato il controllo di accesso alla rete JIT
- È consigliabile chiudere le porte di gestione nelle macchine virtuali
- Per la sottoscrizione devono essere designati al massimo 3 proprietari
- Alla sottoscrizione deve essere assegnato più di un proprietario
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Controlla l'uso di ruoli di controllo degli accessi in base al ruolo personalizzati
- il controllo degli accessi in base al ruolo deve essere usato con il servizio Azure Kubernetes

## <a name="review-of-user-access-rights"></a>Revisione dei diritti di accesso utente

L'organizzazione gestisce un elenco documentato di utenti autorizzati di asset informativi.

- Controlla l'uso di ruoli di controllo degli accessi in base al ruolo personalizzati

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Protezione delle porte di configurazione e diagnostica remota

Porte, servizi e applicazioni simili installate in un computer o in sistemi di rete e che non sono specificamente necessari per le funzionalità aziendali sono disabilitati o rimossi.

- Alle macchine virtuali deve essere applicato il controllo di accesso alla rete JIT
- È consigliabile chiudere le porte di gestione nelle macchine virtuali
- Il debug remoto deve essere disattivato per le applicazioni Web
- Il debug remoto deve essere disattivato per le app per le funzioni
- Il debug remoto deve essere disattivato per le app per le API
- I controlli applicazioni adattivi devono essere abilitati nelle macchine virtuali

## <a name="audit-logging"></a>Registrazione di controllo

I log dei messaggi inviati e ricevuti vengono conservati, inclusi la data, l'ora, l'origine e la destinazione del messaggio, ma non il relativo contenuto. Il controllo è sempre disponibile quando il sistema è attivo e tiene traccia degli eventi principali, dell'accesso ai dati con esito positivo o negativo, delle modifiche alla configurazione della sicurezza del sistema, dell'uso di privilegi o utilità, degli avvisi generati, dell'attivazione e della disattivazione dei sistemi di protezione, ad esempio antivirus e gestione delle identità, dell'attivazione e della disattivazione dei meccanismi di identificazione e autenticazione e della creazione e dell'eliminazione di oggetti a livello del sistema.

- È consigliabile abilitare i log di diagnostica in Azure Data Lake Store
- È consigliabile abilitare i log di diagnostica in App per la logica 
- È consigliabile abilitare i log di diagnostica nell'hub IoT 
- È consigliabile abilitare i log di diagnostica negli account Batch 
- È consigliabile abilitare i log di diagnostica nei set di scalabilità di macchine virtuali 
- È consigliabile abilitare i log di diagnostica in Hub eventi 
- È consigliabile abilitare i log di diagnostica nei servizi di ricerca 
- I log di diagnostica devono essere abilitati in Servizi app 
- È consigliabile abilitare i log di diagnostica Data Lake Analytics 
- I log di diagnostica in Key Vault devono essere abilitati 
- È consigliabile abilitare i log di diagnostica nel bus di servizio
- I log di diagnostica devono essere abilitati in Analisi di flusso di Azure
- È consigliabile abilitare il controllo in SQL Server
- Audit diagnostic setting (Controllare le impostazioni di diagnostica)
- Monitoraggio di Azure deve raccogliere i log attività da tutte le aree

## <a name="monitoring-system-use"></a>Utilizzo del sistema di monitoraggio

I sistemi automatizzati distribuiti nell'ambiente dell'organizzazione vengono usati per monitorare gli eventi principali e le attività anomale e analizzare i log di sistema, i cui risultati vengono esaminati regolarmente. Il monitoraggio include operazioni con privilegi, tentativi di accesso autorizzato o non autorizzato, inclusi i tentativi di accesso ad account disattivati, e avvisi o errori del sistema.

- Monitoraggio di Azure deve raccogliere i log attività da tutte le aree
- L'agente di Log Analytics deve essere installato nelle macchine virtuali
- L'agente di Log Analytics deve essere installato nei set di scalabilità di macchine virtuali
- \[Anteprima\]: Distribuisci i prerequisiti per controllare le macchine virtuali Windows in cui l'agente di Log Analytics non è connesso come previsto
- \[Anteprima\]: Mostra i risultati del controllo dalle macchine virtuali Windows in cui l'agente di Log Analytics non è connesso come previsto
- Il profilo di log di Monitoraggio di Azure deve raccogliere i log per le categorie 'scrittura', 'eliminazione' e 'azione'
- È consigliabile abilitare il provisioning automatico dell'agente di monitoraggio di Log Analytics nella sottoscrizione

## <a name="segregation-of-duties"></a>Separazione dei compiti

La separazione dei compiti consente di limitare il rischio di modifiche non autorizzate o non intenzionali di informazioni e sistemi. Nessun utente singolo può accedere, modificare o usare i sistemi informatici senza autorizzazione o senza che venga rilevato. L'accesso per gli utenti responsabili dell'amministrazione e dei controlli di accesso è limitato al minimo necessario in base al ruolo e alle responsabilità di ogni utente e tali utenti non possono accedere alle funzioni di controllo correlate a tali controlli.

- il controllo degli accessi in base al ruolo deve essere usato con il servizio Azure Kubernetes
- Controlla l'uso di ruoli di controllo degli accessi in base al ruolo personalizzati
- \[Anteprima\]: Distribuisci i prerequisiti per controllare le configurazioni delle macchine virtuali Windows in 'Assegnazione diritti utente'
- \[Anteprima\]: Mostra i risultati del controllo dalle configurazioni delle macchine virtuali Windows in 'Assegnazione diritti utente'
- \[Anteprima\]: Distribuisci i prerequisiti per controllare le configurazioni delle macchine virtuali Windows in 'Opzioni di sicurezza - Controllo account utente'
- \[Anteprima\]: Mostra i risultati del controllo dalle configurazioni delle macchine virtuali Windows in 'Opzioni di sicurezza - Controllo account utente'
- Non devono esistere ruoli di proprietario della sottoscrizione personalizzati

## <a name="administrator-and-operator-logs"></a>Log amministratore e operatore

L'organizzazione garantisce che sia abilitata la registrazione appropriata ai fini del controllo delle attività dell'amministratore ed esamina regolarmente i log degli operatori e degli amministratori di sistema.

- Per operazioni amministrative specifiche deve esistere un avviso del log attività

## <a name="identification-of-risks-related-to-external-parties"></a>Identificazione dei rischi correlati a entità esterne

Le connessioni di accesso remoto tra l'organizzazione e le parti esterne sono crittografate

- Il trasferimento sicuro negli account di archiviazione deve essere abilitato
- L'app per le funzioni deve essere accessibile solo tramite HTTPS
- L'applicazione Web deve essere accessibile solo tramite HTTPS
- L'app per le API deve essere accessibile solo tramite HTTPS
- Il criterio Imponi connessione SSL deve essere abilitato per i server di database MySQL
- Il criterio Imponi connessione SSL deve essere abilitato per i server di database PostgreSQL
- Devono essere abilitate solo connessioni sicure alla Cache Redis

## <a name="business-continuity-and-risk-assessment"></a>Continuità aziendale e valutazione del rischio

L'organizzazione identifica i processi aziendali critici e integra i requisiti di gestione della sicurezza delle informazioni per la continuità aziendale con altri requisiti di continuità relativi ad aspetti quali operazioni, personale, materiali, trasporto e strutture.

- Controlla macchine virtuali in cui non è configurato il ripristino di emergenza
- Gli oggetti Key Vault devono essere recuperabili
- \[Anteprima\]: Distribuisci i prerequisiti per controllare le configurazioni delle macchine virtuali Windows in 'Opzioni di sicurezza - Console di ripristino di emergenza'
- \[Anteprima\]: Mostra i risultati del controllo dalle configurazioni delle macchine virtuali Windows in "Opzioni di sicurezza - Console di ripristino di emergenza"

## <a name="back-up"></a>Backup

Questo progetto assegna le definizioni di Criteri di Azure che controllano in modalità elettronica le informazioni di backup del sistema dell'organizzazione nel sito di archiviazione alternativo. Per la spedizione fisica dei metadati di archiviazione, valutare l'utilizzo di Azure Data Box.

- Il backup con ridondanza geografica a lungo termine deve essere abilitato per i database SQL di Azure
- Il backup con ridondanza geografica deve essere abilitato per i database di Azure per MySQL
- Il backup con ridondanza geografica deve essere abilitato per i database di Azure per PostgreSQL
- Il backup con ridondanza geografica deve essere abilitato per i database di Azure per MariaDB
- La soluzione Backup di Azure deve essere abilitata per le macchine virtuali

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
