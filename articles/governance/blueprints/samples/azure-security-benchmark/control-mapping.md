---
title: Controlli dell'esempio di progetto Azure Security Benchmark
description: Mapping dei controlli dell'esempio di progetto Azure Security Benchmark in Criteri di Azure.
ms.date: 04/16/2020
ms.topic: sample
ms.openlocfilehash: f80b72f06532adef28bf5e5afd1eb94c2e34ee2d
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691297"
---
# <a name="control-mapping-of-the-azure-security-benchmark-blueprint-sample"></a>Mapping dei controlli dell'esempio di progetto Azure Security Benchmark

L'articolo seguente illustra in dettaglio come viene eseguito il mapping dell'esempio di progetto Azure Security Benchmark di Azure Blueprints ai controlli di Azure Security Benchmark. Per altre informazioni sui controlli, vedere [Azure Security Benchmark](../../../../security/benchmarks/overview.md).

I mapping seguenti sono relativi ai controlli di **Azure Security Benchmark**. Usare la barra di spostamento a destra per passare direttamente a uno specifico mapping. I controlli mappati vengono implementati con un'iniziativa di [Criteri di Azure](../../../policy/overview.md). Per esaminare l'iniziativa completa, aprire **Criteri** nel portale di Azure e selezionare la pagina **Definizioni**. Trovare e selezionare l'iniziativa dei criteri predefinita **\[Anteprima\]: Controlla le raccomandazioni per Azure Security Benchmark e implementa le estensioni macchina virtuale di supporto specifiche**.

> [!IMPORTANT]
> Ogni controllo tra quelli riportati di seguito è associato a una o più definizioni di [Criteri di Azure](../../../policy/overview.md). Questi criteri possono aiutare a [valutare la conformità](../../../policy/how-to/get-compliance-data.md) con il controllo. In molti casi tuttavia non si tratta di una corrispondenza 1:1 o completa tra un controllo e uno o più criteri. Di per sé, **Conforme** in Criteri di Azure si riferisce solo ai criteri stessi e non garantisce che l'utente sia completamente conforme a tutti i requisiti di un controllo. Inoltre, in questo momento lo standard di conformità può includere controlli che non vengono gestiti da alcuna definizione di Criteri di Azure. La conformità in Criteri di Azure è quindi solo una visualizzazione parziale dello stato di conformità generale. Le associazioni tra i controlli e le definizioni di Criteri di Azure per questo esempio di progetto di conformità possono cambiare nel tempo. Per visualizzare la cronologia delle modifiche, vedere la [cronologia dei commit di GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/azure-security-benchmark/control-mapping.md).

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1 Proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale

- Le subnet devono essere associate a un gruppo di sicurezza di rete
- Le raccomandazioni di Protezione avanzata adattiva per la rete devono essere applicate alle macchine virtuali con connessione Internet
- Le macchine virtuali devono essere connesse a una rete virtuale approvata
- Le macchine virtuali con connessione Internet devono essere protette con i gruppi di sicurezza di rete
- Il bus di servizio deve usare un endpoint servizio di rete virtuale
- Il servizio app deve usare un endpoint servizio di rete virtuale
- I server SQL devono usare un endpoint servizio di rete virtuale
- Gli hub eventi devono usare un endpoint servizio di rete virtuale
- I database Cosmos DB devono usare un endpoint servizio di rete virtuale
- Gli insieme di credenziali delle chiavi devono usare un endpoint servizio di rete virtuale
- Controlla l'accesso di rete senza restrizioni agli account di archiviazione
- Gli account di archiviazione devono usare un endpoint servizio di rete virtuale
- I Registri Azure Container devono usare un endpoint servizio di rete virtuale
- Le reti virtuali devono usare il gateway di rete virtuale specificato
- Gli intervalli IP autorizzati devono essere definiti nei servizi Kubernetes
- \[Anteprima\]: L'inoltro IP nella macchina virtuale deve essere disabilitato
- Le macchine virtuali con connessione Internet devono essere protette con i gruppi di sicurezza di rete
- Alle macchine virtuali deve essere applicato il controllo di accesso alla rete JIT
- È consigliabile chiudere le porte di gestione nelle macchine virtuali

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2 Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

- È consigliabile abilitare Network Watcher

## <a name="13-protect-critical-web-applications"></a>1.3 Proteggere le applicazioni Web critiche

- Assicurarsi che l'opzione "Certificati client (certificati client in ingresso)" dell'app Web sia impostata su 'Sì'
- CORS non deve consentire a tutte le risorse di accedere alle applicazioni Web
- CORS non deve consentire a tutte le risorse di accedere alle app per le funzioni
- CORS non deve consentire a tutte le risorse di accedere all'app per le API dell'utente
- Il debug remoto deve essere disattivato per le applicazioni Web
- Il debug remoto deve essere disattivato per le app per le funzioni
- Il debug remoto deve essere disattivato per le app per le API

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 Negare le comunicazioni con indirizzi IP dannosi noti

- Lo standard di protezione DDoS deve essere abilitato
- Alle macchine virtuali deve essere applicato il controllo di accesso alla rete JIT
- Le raccomandazioni di Protezione avanzata adattiva per la rete devono essere applicate alle macchine virtuali con connessione Internet

## <a name="15-record-network-packets-and-flow-logs"></a>1.5 Registrare i pacchetti di rete e i log dei flussi

- È consigliabile abilitare Network Watcher

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 Usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

- Distribuisci i prerequisiti per controllare le configurazioni delle macchine virtuali Windows in 'Opzioni di sicurezza - Accesso di rete'
- Mostra i risultati del controllo dalle configurazioni delle macchine virtuali Windows in 'Opzioni di sicurezza - Client di rete Microsoft'
- Distribuisci i prerequisiti per controllare le configurazioni delle macchine virtuali Windows in 'Opzioni di sicurezza - Sicurezza di rete'
- Mostra i risultati del controllo dalle configurazioni delle macchine virtuali Windows in 'Opzioni di sicurezza - Sicurezza di rete'
- Distribuisci i prerequisiti per controllare le configurazioni delle macchine virtuali Windows in 'Opzioni di sicurezza - Server di rete Microsoft'
- Mostra i risultati del controllo dalle configurazioni delle macchine virtuali Windows in 'Opzioni di sicurezza - Server di rete Microsoft'
- Distribuisci i prerequisiti per controllare le configurazioni delle macchine virtuali Windows in 'Modelli amministrativi - Rete'
- Mostra i risultati del controllo dalle configurazioni delle macchine virtuali Windows in 'Modelli amministrativi - Rete'

## <a name="22-configure-central-security-log-management"></a>2.2 Configurare la gestione dei log di sicurezza centralizzata

- L'agente di Log Analytics deve essere installato nelle macchine virtuali
- L'agente di Log Analytics deve essere installato nei set di scalabilità di macchine virtuali
- Distribuisci i prerequisiti per controllare le macchine virtuali Windows in cui l'agente di Log Analytics non è connesso come previsto
- Mostra i risultati del controllo dalle macchine virtuali Windows in cui l'agente di Log Analytics non è connesso come previsto
- Il profilo di log di Monitoraggio di Azure deve raccogliere i log per le categorie 'scrittura', 'eliminazione' e 'azione'
- Monitoraggio di Azure deve raccogliere i log attività da tutte le aree
- È consigliabile abilitare il provisioning automatico dell'agente di monitoraggio di Log Analytics nella sottoscrizione

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 Abilitare la registrazione di controllo per le risorse di Azure

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

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4 Raccogliere i log di sicurezza dai sistemi operativi

- È consigliabile abilitare il provisioning automatico dell'agente di monitoraggio di Log Analytics nella sottoscrizione
- L'agente di Log Analytics deve essere installato nelle macchine virtuali
- L'agente di Log Analytics deve essere installato nei set di scalabilità di macchine virtuali
- Distribuisci i prerequisiti per controllare le macchine virtuali Windows in cui l'agente di Log Analytics non è connesso come previsto
- Mostra i risultati del controllo dalle macchine virtuali Windows in cui l'agente di Log Analytics non è connesso come previsto

## <a name="27-enable-alerts-for-anomalous-activity"></a>2.7 Abilitare gli avvisi per le attività anomale

- È consigliabile selezionare il piano tariffario Standard del Centro sicurezza
- Sicurezza dei dati avanzata deve essere abilitata nei server SQL
- La sicurezza dei dati avanzata deve essere abilitata nelle istanze gestite di SQL
- È consigliabile che i Tipi di Advanced Threat Protection siano impostati su "Tutti" nelle impostazioni di Sicurezza dei dati avanzata in SQL Server
- È consigliabile che i Tipi di Advanced Threat Protection siano impostati su "Tutti" nelle impostazioni di Sicurezza dei dati avanzata nell'istanza gestita di SQL

## <a name="28-centralize-anti-malware-logging"></a>2.8 Centralizzare la registrazione antimalware

- È consigliabile configurare Microsoft Antimalware per Azure per aggiornare automaticamente le firme di protezione
- Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure
- La soluzione Endpoint Protection deve essere installata nei set di scalabilità di macchine virtuali

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 Gestire un inventario degli account amministrativi

- Per la sottoscrizione devono essere designati al massimo 3 proprietari
- Alla sottoscrizione deve essere assegnato più di un proprietario
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

## <a name="33-use-dedicated-administrative-accounts"></a>3.3. Usare account amministrativi dedicati

- Distribuisci i prerequisiti per controllare le macchine virtuali Windows in cui il gruppo Administrators non contiene solo i membri specificati
- Mostra i risultati del controllo dalle macchine virtuali Windows in cui il gruppo Administrators non contiene solo i membri specificati
- Distribuisci i prerequisiti per controllare le macchine virtuali Windows in cui il gruppo Administrators non contiene tutti i membri specificati
- Mostra i risultati del controllo dalle macchine virtuali Windows in cui il gruppo Administrators non contiene tutti i membri specificati
- Distribuisci i prerequisiti per controllare le macchine virtuali Windows in cui il gruppo Administrators contiene uno qualsiasi dei membri specificati
- Mostra i risultati del controllo dalle macchine virtuali Windows in cui il gruppo Administrators contiene uno qualsiasi dei membri specificati
- Per la sottoscrizione devono essere designati al massimo 3 proprietari
- Alla sottoscrizione deve essere assegnato più di un proprietario

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 Usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione
- L'autenticazione MFA deve essere abilitata per gli account con autorizzazioni di scrittura per la sottoscrizione
- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di lettura per la sottoscrizione

## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7 Registrare e inviare avvisi per le attività sospette dagli account amministrativi

- È consigliabile selezionare il piano tariffario Standard del Centro sicurezza

## <a name="39-use-azure-active-directory"></a>3.9 Usare Azure Active Directory

- È consigliabile effettuare il provisioning di un amministratore di Azure Active Directory per SQL Server
- I cluster di Service Fabric deve usare solo Azure Active Directory per l'autenticazione client
- Assicurarsi che l'opzione Registra con Azure Active Directory sia abilitata nell'app per le API
- Assicurarsi che l'opzione Registra con Azure Active Directory sia abilitata nell'app per le Web
- Assicurarsi che l'opzione Registra con Azure Active Directory sia abilitata nell'app per le funzioni

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 Controllare e riconciliare regolarmente l'accesso utente

- Gli account deprecati devono essere rimossi dalla sottoscrizione
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di lettura devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 Gestire un inventario delle informazioni riservate

- È consigliabile configurare come riservati i dati sensibili nei database SQL

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 Crittografare tutte le informazioni riservate in transito

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

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 Usare uno strumento di individuazione attivo per identificare i dati sensibili

- È consigliabile configurare come riservati i dati sensibili nei database SQL
- Sicurezza dei dati avanzata deve essere abilitata nei server SQL
- La sicurezza dei dati avanzata deve essere abilitata nelle istanze gestite di SQL

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6 Usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse

- il controllo degli accessi in base al ruolo deve essere usato con il servizio Azure Kubernetes
- Controlla l'uso di ruoli di controllo degli accessi in base al ruolo personalizzati

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 Crittografare le informazioni riservate inattive

- Transparent Data Encryption deve essere abilitata nei database SQL
- La crittografia del disco deve essere applicata nelle macchine virtuali
- È consigliabile crittografare i dischi non collegati
- È consigliabile che la protezione TDE di SQL Server sia crittografata con una chiave personalizzata
- È consigliabile che la protezione TDE dell'istanza gestita di SQL sia crittografata con una chiave personalizzata
- Le variabili dell'account di automazione devono essere crittografate
- La proprietà ClusterProtectionLevel dei cluster di Service Fabric dovrebbe essere impostata su EncryptAndSign

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 Registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

- Monitoraggio di Azure deve raccogliere i log attività da tutte le aree

## <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 Eseguire strumenti di analisi della vulnerabilità automatizzati

- La valutazione delle vulnerabilità deve essere abilitata nei server SQL
- La valutazione delle vulnerabilità deve essere abilitata nelle istanze gestite di SQL
- \[Anteprima\] La soluzione Valutazione della vulnerabilità deve essere abilitata nelle macchine virtuali
- Le impostazioni di Valutazione della vulnerabilità per SQL Server devono contenere un indirizzo di posta elettronica per ricevere i report di analisi

## <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2 Distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

- Gli aggiornamenti di sistema devono essere installati nelle macchine
- Gli aggiornamenti di sistema nei set di scalabilità di macchine virtuali devono essere installati
- Assicurarsi che la versione di '.NET Framework' sia la più recente, se usata come parte dell'app per le funzioni
- Assicurarsi che la versione di '.NET Framework' sia la più recente, se usata come parte dell'app Web
- Assicurarsi che la versione di '.NET Framework' sia la più recente, se usata come parte dell'app per le API

## <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3 Distribuire una soluzione di gestione delle patch automatizzata per il software di terze parti

- Assicurarsi che la 'versione di PHP' sia la più recente, se usata come parte dell'app per le API
- Assicurarsi che la 'versione di PHP' sia la più recente, se usata come parte dell'app Web
- Assicurarsi che la 'versione di PHP' sia la più recente, se usata come parte dell'app per le funzioni
- Assicurarsi che la 'versione di Java' sia la più recente, se usata come parte dell'app Web
- Assicurarsi che la 'versione di Java' sia la più recente, se usata come parte dell'app per le funzioni
- Assicurarsi che la 'versione di Java' sia la più recente, se usata come parte dell'app per le API
- Assicurarsi che la 'versione di Python' sia la più recente, se usata come parte dell'app Web
- Assicurarsi che la 'versione di Python' sia la più recente, se usata come parte dell'app per le funzioni
- Assicurarsi che la 'versione di Python' sia la più recente, se usata come parte dell'app per le API
- I servizi Kubernetes devono essere aggiornati a una versione di Kubernetes non vulnerabile

## <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 Usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

- Le vulnerabilità devono essere risolte tramite una soluzione di valutazione della vulnerabilità
- Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte
- Le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali devono essere risolte
- È consigliabile correggere le vulnerabilità nelle configurazioni della sicurezza dei contenitori
- Le vulnerabilità dei database SQL devono essere risolte

## <a name="68-use-only-approved-applications"></a>6.8 Usare solo applicazioni approvate

- È consigliabile selezionare il piano tariffario Standard del Centro sicurezza
- I controlli applicazioni adattivi devono essere abilitati nelle macchine virtuali

## <a name="69-use-only-approved-azure-services"></a>6.9 Usare solo servizi di Azure approvati

- È consigliabile eseguire la migrazione delle macchine virtuali alle nuove risorse di Azure Resource Manager
- È consigliabile eseguire la migrazione degli account di archiviazione alle nuove risorse di Azure Resource Manager

## <a name="610-implement-approved-application-list"></a>6.10 Implementare l'elenco di applicazioni approvate

- È consigliabile selezionare il piano tariffario Standard del Centro sicurezza
- I controlli applicazioni adattivi devono essere abilitati nelle macchine virtuali

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 Garantire la sicurezza delle configurazioni delle risorse di Azure

- \[Anteprima\]: I criteri di sicurezza pod devono essere definiti nei servizi Kubernetes

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4 Garantire la sicurezza delle configurazioni del sistema operativo

- Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte
- È consigliabile correggere le vulnerabilità nelle configurazioni della sicurezza dei contenitori
- Le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali devono essere risolte

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9 Implementare il monitoraggio automatizzato della configurazione per i servizi di Azure

- \[Anteprima\]: I criteri di sicurezza pod devono essere definiti nei servizi Kubernetes

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10 Implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

- Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte
- È consigliabile correggere le vulnerabilità nelle configurazioni della sicurezza dei contenitori
- Le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali devono essere risolte

## <a name="711-manage-azure-secrets-securely"></a>7.11 Gestire i segreti di Azure in modo sicuro

- Gli oggetti Key Vault devono essere recuperabili

## <a name="712-manage-identities-securely-and-automatically"></a>7.12 Gestire le identità in modo sicuro e automatico

- Nell'app per le funzioni è necessario usare un'identità gestita
- Nell'app Web è necessario usare un'identità gestita
- Nell'app per le API è necessario usare un'identità gestita

## <a name="81-use-centrally-managed-anti-malware-software"></a>8.1 Usare software antimalware gestito in modo centralizzato

- Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure
- La soluzione Endpoint Protection deve essere installata nei set di scalabilità di macchine virtuali

## <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 Eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

- È consigliabile selezionare il piano tariffario Standard del Centro sicurezza

## <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3 Assicurarsi che le firme e il software antimalware siano aggiornati

- È consigliabile configurare Microsoft Antimalware per Azure per aggiornare automaticamente le firme di protezione

## <a name="91-ensure-regular-automated-back-ups"></a>9.1 Garantire l'esecuzione regolare di backup automatizzati

- Il backup con ridondanza geografica a lungo termine deve essere abilitato per i database SQL di Azure
- Il backup con ridondanza geografica deve essere abilitato per i database di Azure per MySQL
- Il backup con ridondanza geografica deve essere abilitato per i database di Azure per PostgreSQL
- Il backup con ridondanza geografica deve essere abilitato per i database di Azure per MariaDB
- La soluzione Backup di Azure deve essere abilitata per le macchine virtuali

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 Eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

- Il backup con ridondanza geografica a lungo termine deve essere abilitato per i database SQL di Azure
- Il backup con ridondanza geografica deve essere abilitato per i database di Azure per MySQL
- Il backup con ridondanza geografica deve essere abilitato per i database di Azure per PostgreSQL
- Il backup con ridondanza geografica deve essere abilitato per i database di Azure per MariaDB
- La soluzione Backup di Azure deve essere abilitata per le macchine virtuali

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 Garantire la protezione dei backup e delle chiavi gestite dal cliente

- Gli oggetti Key Vault devono essere recuperabili

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 Creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

- È consigliabile selezionare il piano tariffario Standard del Centro sicurezza

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4 Specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

- È consigliabile fornire un indirizzo di posta elettronica dei contatti di sicurezza per la sottoscrizione
- È consigliabile fornire il numero di telefono dei contatti di sicurezza per la sottoscrizione
- Le impostazioni avanzate di sicurezza dei dati per SQL Server devono contenere un indirizzo di posta elettronica a cui ricevere gli avvisi di sicurezza
- Le impostazioni avanzate di sicurezza dei dati per l'istanza gestita di SQL devono contenere un indirizzo di posta elettronica a cui ricevere gli avvisi di sicurezza
- Le notifiche tramite posta elettronica agli amministratori e ai proprietari della sottoscrizione devono essere abilitate nelle impostazioni di Sicurezza dei dati avanzata del server SQL
- Le notifiche tramite posta elettronica agli amministratori e ai proprietari della sottoscrizione devono essere abilitate nelle impostazioni di Sicurezza dei dati avanzata dell'istanza gestita di SQL

## <a name="next-steps"></a>Passaggi successivi

Dopo aver esaminato il mapping dei controlli del progetto Azure Security Benchmark, vedere Criteri di Azure nella portale di Azure per assegnare l'iniziativa:

> [!div class="nextstepaction"]
> [Criteri di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).