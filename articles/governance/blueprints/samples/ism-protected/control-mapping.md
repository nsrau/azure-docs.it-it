---
title: Panoramica di esempio del progetto Australian Government ISM PROTECTED
description: Panoramica dell'esempio di progetto Australian Government ISM PROTECTED. Questo esempio di progetto consente ai clienti di valutare specifici controlli ISM PROTECTED.
ms.date: 08/03/2020
ms.topic: sample
ms.openlocfilehash: 750cc5551f0bbf05e0da6ad41d46729231243f72
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760431"
---
# <a name="control-mapping-of-the-australian-government-ism-protected-blueprint-sample"></a>Mapping dei controlli dell'esempio di progetto Australian Government ISM PROTECTED

L'articolo seguente illustra in dettaglio il mapping dell'esempio di progetto Australian Government ISM PROTECTED di Azure Blueprints ai controlli ISM PROTECTED. Per altre informazioni sui controlli, vedere [ISM PROTECTED](https://www.cyber.gov.au/ism).

I mapping seguenti fanno riferimento ai controlli **ISM PROTECTED**. Usare la barra di spostamento a destra per passare direttamente a uno specifico mapping. Molti controlli mappati vengono implementati con un'iniziativa di [Criteri di Azure](../../../policy/overview.md). Per esaminare l'iniziativa completa, aprire **Criteri** nel portale di Azure e selezionare la pagina **Definizioni**. Trovare e selezionare l'iniziativa dei criteri predefinita **\[Anteprima\]: Verifica controlli Australian Government ISM PROTECTED e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di proteggere controllo**.

> [!IMPORTANT]
> Ogni controllo tra quelli riportati di seguito è associato a una o più definizioni di [Criteri di Azure](../../../policy/overview.md). Questi criteri possono aiutare a [valutare la conformità](../../../policy/how-to/get-compliance-data.md) con il controllo. In molti casi tuttavia non si tratta di una corrispondenza 1:1 o completa tra un controllo e uno o più criteri. Di per sé, **Conforme** in Criteri di Azure si riferisce solo ai criteri stessi e non garantisce che l'utente sia completamente conforme a tutti i requisiti di un controllo. Inoltre, in questo momento lo standard di conformità include controlli che non vengono gestiti da alcuna definizione di Criteri di Azure. La conformità in Criteri di Azure è quindi solo una visualizzazione parziale dello stato di conformità generale. Le associazioni tra i controlli e le definizioni di Criteri di Azure per questo esempio di progetto di conformità possono cambiare nel tempo.
> Per visualizzare la cronologia delle modifiche, vedere la [cronologia dei commit di GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ism-protected/control-mapping.md).


## <a name="location-constraints"></a>Vincoli delle località

Questo progetto consente di limitare la località per la distribuzione di tutte le risorse e dei gruppi di risorse a "Australia centrale" e "Australia centrale 2", "Australia orientale" e "Australia sud-orientale" assegnando le definizioni di Criteri di Azure seguenti:

- Località consentite (impostate tramite hardcode su "Australia centrale", "Australia centrale 2", "Australia orientale" e "Australia sud-orientale")
- Località consentite per i gruppi di risorse (impostate tramite hardcode su "Australia centrale", "Australia centrale 2", "Australia orientale" e "Australia sud-orientale")

## <a name="guidelines-for-personnel-security---access-to-systems-and-their-resources"></a>Linee guida per la sicurezza del personale - Accesso ai sistemi e alle relative risorse

### <a name="0414-personnel-granted-access-to-a-system-and-its-resources-are-uniquely-identifiable"></a>0414 Il personale a cui viene concesso l'accesso a un sistema e alle relative risorse è identificabile in modo univoco

- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione
- L'autenticazione MFA deve essere abilitata per gli account con autorizzazioni di scrittura per la sottoscrizione
- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di lettura per la sottoscrizione

### <a name="1503-standard-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1503 L'accesso standard a sistemi, applicazioni e repository di dati è limitato a quanto richiesto dal personale per svolgere le proprie mansioni

- Per la sottoscrizione devono essere designati al massimo 3 proprietari
- Alla sottoscrizione deve essere assegnato più di un proprietario
- Mostra i risultati del controllo dalle macchine virtuali Windows in cui il gruppo Administrators contiene uno qualsiasi dei membri specificati
- Distribuisci i prerequisiti per controllare le macchine virtuali Windows in cui il gruppo Administrators contiene uno qualsiasi dei membri specificati

### <a name="1507-privileged-access-to-systems-applications-and-data-repositories-is-validated-when-first-requested-and-revalidated-on-an-annual-or-more-frequent-basis"></a>1507 L'accesso con privilegi a sistemi, applicazioni e repository di dati viene convalidato alla prima richiesta e riconvalidato su base annuale o con una maggiore frequenza

- Mostra i risultati del controllo dalle macchine virtuali Windows in cui il gruppo Administrators contiene uno qualsiasi dei membri specificati
- Distribuisci i prerequisiti per controllare le macchine virtuali Windows in cui il gruppo Administrators contiene uno qualsiasi dei membri specificati

### <a name="1508-privileged-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1508 L'accesso con privilegi a sistemi, applicazioni e repository di dati è limitato a quanto richiesto dal personale per svolgere le proprie mansioni

- Per la sottoscrizione devono essere designati al massimo 3 proprietari
- Alla sottoscrizione deve essere assegnato più di un proprietario
- Mostra i risultati del controllo dalle macchine virtuali Windows in cui il gruppo Administrators contiene uno qualsiasi dei membri specificati
- Distribuisci i prerequisiti per controllare le macchine virtuali Windows in cui il gruppo Administrators contiene uno qualsiasi dei membri specificati
- Alle macchine virtuali deve essere applicato il controllo di accesso alla rete JIT

### <a name="0415-the-use-of-shared-user-accounts-is-strictly-controlled-and-personnel-using-such-accounts-are-uniquely-identifiable"></a>0415 L'uso di account utente condivisi è strettamente controllato e il personale che usa tali account è identificabile in modo univoco

- Mostra i risultati del controllo dalle macchine virtuali Windows in cui il gruppo Administrators contiene uno qualsiasi dei membri specificati
- Distribuisci i prerequisiti per controllare le macchine virtuali Windows in cui il gruppo Administrators contiene uno qualsiasi dei membri specificati

### <a name="0445-privileged-users-are-assigned-a-dedicated-privileged-account-to-be-used-solely-for-tasks-requiring-privileged-access"></a>0445 Agli utenti con privilegi viene assegnato un account con privilegi dedicato da usare esclusivamente per le attività che richiedono l'accesso con privilegi

- Mostra i risultati del controllo dalle macchine virtuali Windows in cui il gruppo Administrators contiene uno qualsiasi dei membri specificati
- Distribuisci i prerequisiti per controllare le macchine virtuali Windows in cui il gruppo Administrators contiene uno qualsiasi dei membri specificati

### <a name="0430-access-to-systems-applications-and-data-repositories-is-removed-or-suspended-on-the-same-day-personnel-no-longer-have-a-legitimate-requirement-for-access"></a>0430 L'accesso a sistemi, applicazioni e repository di dati viene rimosso o sospeso nello stesso giorno in cui il personale non ha più un requisito legittimo per l'accesso

- Gli account deprecati devono essere rimossi dalla sottoscrizione
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

### <a name="0441-when-personnel-are-granted-temporary-access-to-a-system-effective-security-controls-are-put-in-place-to-restrict-their-access-to-only-information-required-for-them-to-undertake-their-duties"></a>0441 Quando al personale viene concesso l'accesso temporaneo a un sistema, vengono applicati i controlli di sicurezza effettivi per limitare l'accesso alle sole informazioni necessarie per svolgere le loro mansioni

- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione
- Gli account deprecati devono essere rimossi dalla sottoscrizione
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

## <a name="guidelines-for-system-hardening---operating-system-hardening"></a>Linee guida per la protezione avanzata dei sistemi - Protezione avanzata del sistema operativo

### <a name="1407-the-latest-version-n-or-n-1-version-of-an-operating-system-is-used-for-standard-operating-environments-soes"></a>1407 Per gli ambienti operativi standard, viene usata la versione più recente (N) o la versione N-1 di un sistema operativo

- Gli aggiornamenti di sistema devono essere installati nelle macchine
- Gli aggiornamenti di sistema nei set di scalabilità di macchine virtuali devono essere installati

### <a name="0380-unneeded-operating-system-accounts-software-components-services-and-functionality-are-removed-or-disabled"></a>0380 Gli account del sistema operativo, il software, i componenti, i servizi e le funzionalità non necessari vengono rimossi o disabilitati

- Gli account deprecati devono essere rimossi dalla sottoscrizione
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

### <a name="1490-an-application-whitelisting-solution-is-implemented-on-all-servers-to-restrict-the-execution-of-executables-software-libraries-scripts-and-installers-to-an-approved-set"></a>1490 In tutti i server viene implementata una soluzione di elenco di applicazioni consentite per limitare l'esecuzione di file eseguibili, librerie software, script e programmi di installazione a un set approvato

- I controlli applicazioni adattivi devono essere abilitati nelle macchine virtuali

### <a name="1417-antivirus-software-is-implemented-on-workstations-and-servers-and-configured-with-signature-based-detection-enabled-and-set-to-a-high-level-heuristic-based-detection-enabled-and-set-to-a-high-level-detection-signatures-checked-for-currency-and-updated-on-at-least-a-daily-basis-automatic-and-regular-scanning-configured-for-all-fixed-disks-and-removable-media"></a>1417 Il software antivirus viene implementato in workstation e server e configurato con: rilevamento basato su firma abilitato e impostato su un livello elevato, rilevamento basato su euristica abilitato e impostato su un livello elevato, firme di rilevamento con verifica dell'aggiornamento e aggiornate almeno su base quotidiana, analisi automatica e regolare configurata per tutti i dischi fissi e i supporti rimovibili

- È consigliabile distribuire l'estensione Microsoft IaaSAntimalware nei server Windows
- La soluzione Endpoint Protection deve essere installata nei set di scalabilità di macchine virtuali
- Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure

## <a name="guidelines-for-system-hardening---authentication-hardening"></a>Linee guida per la protezione avanzata dei sistemi - Protezione avanzata dell'autenticazione

### <a name="1546-users-are-authenticated-before-they-are-granted-access-to-a-system-and-its-resources"></a>1546 Gli utenti vengono autenticati prima che venga loro concesso l'accesso a un sistema e alle relative risorse

- Controlla l'accesso di rete senza restrizioni agli account di archiviazione
- I cluster di Service Fabric deve usare solo Azure Active Directory per l'autenticazione client
- Mostra i risultati del controllo dalle macchine virtuali Linux che consentono connessioni remote da account senza password
- Distribuisci prerequisiti per controllare le macchine virtuali Linux che consentono connessioni remote da account senza password
- Mostra i risultati del controllo dalle macchine virtuali Linux in cui sono presenti account senza password
- Distribuisci i prerequisiti per controllare le macchine virtuali Linux in cui sono presenti account senza password

### <a name="0974-multi-factor-authentication-is-used-to-authenticate-standard-users"></a>0974 Per autenticare gli utenti standard viene usata l'autenticazione a più fattori

- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di lettura per la sottoscrizione

### <a name="1173-multi-factor-authentication-is-used-to-authenticate-all-privileged-users-and-any-other-positions-of-trust"></a>1173 Per autenticare tutti gli utenti con privilegi e qualsiasi altra posizione di fiducia, viene usata l'autenticazione a più fattori

- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione
- L'autenticazione MFA deve essere abilitata per gli account con autorizzazioni di scrittura per la sottoscrizione

### <a name="0421-passphrases-used-for-single-factor-authentication-are-a-minimum-of-14-characters-with-complexity-ideally-as-4-random-words"></a>0421 Le passphrase usate per l'autenticazione a fattore singolo sono costituite da un minimo di 14 caratteri con complessità, idealmente come 4 parole casuali

- Mostra i risultati del controllo dalle configurazioni delle macchine virtuali Windows in 'Impostazioni di sicurezza - Criteri account'
- Distribuisci i prerequisiti per controllare le configurazioni delle macchine virtuali Windows in 'Impostazioni di sicurezza - Criteri account'

## <a name="guidelines-for-system-management---system-administration"></a>Linee guida per la gestione di sistemi - Amministrazione dei sistemi

### <a name="1384-multi-factor-authentication-is-used-to-authenticate-users-each-time-they-perform-privileged-actions"></a>1384 Per autenticare gli utenti ogni volta che eseguono azioni con privilegi, viene usata l'autenticazione a più fattori

- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione
- L'autenticazione MFA deve essere abilitata per gli account con autorizzazioni di scrittura per la sottoscrizione
- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di lettura per la sottoscrizione

### <a name="1386-management-traffic-is-only-allowed-to-originate-from-network-zones-that-are-used-to-administer-systems-and-applications"></a>1386 Il traffico di gestione può provenire solo dalle zone di rete usate per amministrare sistemi e applicazioni

- Alle macchine virtuali deve essere applicato il controllo di accesso alla rete JIT
- Il debug remoto deve essere disattivato per le app per le API
- Il debug remoto deve essere disattivato per le app per le funzioni
- Il debug remoto deve essere disattivato per le applicazioni Web

## <a name="guidelines-for-system-management---system-patching"></a>Linee guida per la gestione di sistemi - Distribuzione di patch nei sistemi

### <a name="1144-security-vulnerabilities-in-applications-and-drivers-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1144 Le vulnerabilità della sicurezza in applicazioni e driver valutate come rischi estremi vengono sottoposte a patch, aggiornate o mitigate entro 48 ore dall'identificazione da parte di fornitori, terze parti indipendenti, responsabili di sistemi o utenti

- Le vulnerabilità dei database SQL devono essere risolte
- La valutazione delle vulnerabilità deve essere abilitata nei server SQL
- La valutazione delle vulnerabilità deve essere abilitata nelle istanze gestite di SQL
- La valutazione delle vulnerabilità deve essere abilitata nei computer
- Le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali devono essere risolte
- Le vulnerabilità devono essere risolte tramite una soluzione di valutazione della vulnerabilità
- Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte
- È consigliabile correggere le vulnerabilità nelle configurazioni della sicurezza dei contenitori
- Le impostazioni di Valutazione della vulnerabilità per SQL Server devono contenere un indirizzo di posta elettronica per ricevere i report di analisi

### <a name="0940-security-vulnerabilities-in-applications-and-drivers-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>0940 Le vulnerabilità della sicurezza in applicazioni e driver valutate come rischi elevati vengono sottoposte a patch, aggiornate o mitigate entro due settimane dall'identificazione da parte di fornitori, terze parti indipendenti, responsabili di sistemi o utenti

- Le vulnerabilità dei database SQL devono essere risolte
- La valutazione delle vulnerabilità deve essere abilitata nei server SQL
- La valutazione delle vulnerabilità deve essere abilitata nelle istanze gestite di SQL
- La soluzione Valutazione della vulnerabilità deve essere abilitata nelle macchine virtuali
- Le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali devono essere risolte
- Le vulnerabilità devono essere risolte tramite una soluzione di valutazione della vulnerabilità
- Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte
- È consigliabile correggere le vulnerabilità nelle configurazioni della sicurezza dei contenitori
- Le impostazioni di Valutazione della vulnerabilità per SQL Server devono contenere un indirizzo di posta elettronica per ricevere i report di analisi

### <a name="1472-security-vulnerabilities-in-applications-and-drivers-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1472 Le vulnerabilità della sicurezza in applicazioni e driver valutate come rischi elevati vengono sottoposte a patch, aggiornate o mitigate entro un mese dall'identificazione da parte di fornitori, terze parti indipendenti, responsabili di sistemi o utenti

- Le vulnerabilità dei database SQL devono essere risolte
- La valutazione delle vulnerabilità deve essere abilitata nei server SQL
- La valutazione delle vulnerabilità deve essere abilitata nelle istanze gestite di SQL
- La soluzione Valutazione della vulnerabilità deve essere abilitata nelle macchine virtuali
- Le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali devono essere risolte
- Le vulnerabilità devono essere risolte tramite una soluzione di valutazione della vulnerabilità
- Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte
- È consigliabile correggere le vulnerabilità nelle configurazioni della sicurezza dei contenitori
- Le impostazioni di Valutazione della vulnerabilità per SQL Server devono contenere un indirizzo di posta elettronica per ricevere i report di analisi

### <a name="1494-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1494 Le vulnerabilità della sicurezza in sistemi operativi e firmware valutate come rischi estremi vengono sottoposte a patch, aggiornate o mitigate entro 48 ore dall'identificazione da parte di fornitori, terze parti indipendenti, responsabili di sistemi o utenti

- Le vulnerabilità dei database SQL devono essere risolte
- La valutazione delle vulnerabilità deve essere abilitata nei server SQL
- La valutazione delle vulnerabilità deve essere abilitata nelle istanze gestite di SQL
- La soluzione Valutazione della vulnerabilità deve essere abilitata nelle macchine virtuali
- Le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali devono essere risolte
- Le vulnerabilità devono essere risolte tramite una soluzione di valutazione della vulnerabilità
- Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte
- È consigliabile correggere le vulnerabilità nelle configurazioni della sicurezza dei contenitori
- Le impostazioni di Valutazione della vulnerabilità per SQL Server devono contenere un indirizzo di posta elettronica per ricevere i report di analisi

### <a name="1495-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1495 Le vulnerabilità della sicurezza in sistemi operativi e firmware valutate come rischi elevati vengono sottoposte a patch, aggiornate o mitigate entro due settimane dall'identificazione da parte di fornitori, terze parti indipendenti, responsabili di sistemi o utenti

- Le vulnerabilità dei database SQL devono essere risolte
- La valutazione delle vulnerabilità deve essere abilitata nei server SQL
- La valutazione delle vulnerabilità deve essere abilitata nelle istanze gestite di SQL
- La soluzione Valutazione della vulnerabilità deve essere abilitata nelle macchine virtuali
- Le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali devono essere risolte
- Le vulnerabilità devono essere risolte tramite una soluzione di valutazione della vulnerabilità
- Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte
- È consigliabile correggere le vulnerabilità nelle configurazioni della sicurezza dei contenitori
- Le impostazioni di Valutazione della vulnerabilità per SQL Server devono contenere un indirizzo di posta elettronica per ricevere i report di analisi

### <a name="1496-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1496 Le vulnerabilità della sicurezza in sistemi operativi e firmware valutate come rischi estremi bassi o moderati vengono sottoposte a patch, aggiornate o mitigate entro un mese dall'identificazione da parte di fornitori, terze parti indipendenti, responsabili di sistemi o utenti

- Le vulnerabilità dei database SQL devono essere risolte
- La valutazione delle vulnerabilità deve essere abilitata nei server SQL
- La valutazione delle vulnerabilità deve essere abilitata nelle istanze gestite di SQL
- La soluzione Valutazione della vulnerabilità deve essere abilitata nelle macchine virtuali
- Le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali devono essere risolte
- Le vulnerabilità devono essere risolte tramite una soluzione di valutazione della vulnerabilità
- Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte
- È consigliabile correggere le vulnerabilità nelle configurazioni della sicurezza dei contenitori
- Le impostazioni di Valutazione della vulnerabilità per SQL Server devono contenere un indirizzo di posta elettronica per ricevere i report di analisi

## <a name="guidelines-for-system-management---data-backup-and-restoration"></a>Linee guida per la gestione dei sistemi - Backup e ripristino dei dati

### <a name="1511-backups-of-important-information-software-and-configuration-settings-are-performed-at-least-daily"></a>1511 I backup di informazioni importanti, software e impostazioni di configurazione vengono eseguiti almeno ogni giorno

- Controlla macchine virtuali in cui non è configurato il ripristino di emergenza

## <a name="guidelines-for-system-monitoring---event-logging-and-auditing"></a>Linee guida per il monitoraggio dei sistemi - Registrazione e controllo degli eventi

### <a name="1405-a-centralised-logging-facility-is-implemented-and-systems-are-configured-to-save-event-logs-to-the-centralised-logging-facility-as-soon-as-possible-after-each-event-occurs"></a>1405 Viene implementata una struttura di registrazione centralizzata e i sistemi vengono configurati per salvare i log eventi in tale struttura non appena possibile dopo il verificarsi di ogni evento

- Le sottoscrizioni di Azure devono avere un profilo di log per il log attività

### <a name="0582-the-following-events-are-logged-for-operating-systems-access-to-important-data-and-processes-application-crashes-and-any-error-messages-attempts-to-use-special-privileges-changes-to-accounts-changes-to-security-policy-changes-to-system-configurations-domain-name-system-dns-and-hypertext-transfer-protocol-http-requests-failed-attempts-to-access-data-and-system-resources-service-failures-and-restarts-system-startup-and-shutdown-transfer-of-data-to-external-media-user-or-group-management-use-of-special-privileges"></a>0582 Per i sistemi operativi vengono registrati gli eventi seguenti: accesso a dati e processi importanti, arresti anomali delle applicazioni ed eventuali messaggi di errore, tentativi di usare privilegi speciali, modifiche degli account, modifiche dei criteri di sicurezza, modifiche delle configurazioni dei sistemi, richieste DNS (Domain Name System) e HTTP (Hypertext Transfer Protocol), tentativi non riusciti di accedere a dati e risorse di sistema, errori e riavvi dei servizi, avvio e arresto dei sistemi, trasferimento di dati a supporti esterni, gestione di utenti o gruppi, uso di privilegi speciali

- \[Anteprima\]: Controlla la distribuzione dell'agente di Log Analytics - Immagine macchina virtuale (sistema operativo) non in elenco
- Controlla la distribuzione dell'agente di Log Analytics nei set di scalabilità di macchine virtuali - Immagine macchina virtuale (sistema operativo) non in elenco
- Controlla area di lavoro Log Analytics per la macchina virtuale - Segnala mancata corrispondenza
- Audit diagnostic setting (Controllare le impostazioni di diagnostica)

### <a name="1537-the-following-events-are-logged-for-databases-access-to-particularly-important-information-addition-of-new-users-especially-privileged-users-any-query-containing-comments-any-query-containing-multiple-embedded-queries-any-query-or-database-alerts-or-failures-attempts-to-elevate-privileges-attempted-access-that-is-successful-or-unsuccessful-changes-to-the-database-structure-changes-to-user-roles-or-database-permissions-database-administrator-actions-database-logons-and-logoffs-modifications-to-data-use-of-executable-commands"></a>1537 Per i database vengono registrati gli eventi seguenti: accesso a informazioni particolarmente importanti, aggiunta di nuovi utenti, in particolare utenti con privilegi, qualsiasi query contenente commenti, qualsiasi query contenente più query incorporate, eventuali avvisi o errori di query o database, tentativi di elevare i privilegi, tentativi di accesso riusciti o non riusciti, modifiche della struttura del database, modifiche di ruoli utente o autorizzazioni per il database, azioni dell'amministratore del database, accessi e disconnessioni dal database, modifiche dei dati, usi di comandi eseguibili

- Sicurezza dei dati avanzata deve essere abilitata nei server SQL
- Audit diagnostic setting (Controllare le impostazioni di diagnostica)
- La sicurezza dei dati avanzata deve essere abilitata nelle istanze gestite di SQL

## <a name="guidelines-for-system-monitoring---vulnerability-management"></a>Linee guida per il monitoraggio dei sistemi - Gestione delle vulnerabilità

### <a name="0911-vulnerability-assessments-and-penetration-tests-are-conducted-by-suitably-skilled-personnel-before-a-system-is-deployed-after-a-significant-change-to-a-system-and-at-least-annually-or-as-specified-by-the-system-owner"></a>0911 Le valutazioni delle vulnerabilità e i test di penetrazione vengono eseguiti da personale adeguatamente qualificato prima della distribuzione di un sistema, dopo una modifica significativa apportata a un sistema e almeno ogni anno o come specificato dal proprietario del sistema

- Le vulnerabilità dei database SQL devono essere risolte
- La valutazione delle vulnerabilità deve essere abilitata nei server SQL
- La valutazione delle vulnerabilità deve essere abilitata nelle istanze gestite di SQL
- La soluzione Valutazione della vulnerabilità deve essere abilitata nelle macchine virtuali
- Le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali devono essere risolte
- Le vulnerabilità devono essere risolte tramite una soluzione di valutazione della vulnerabilità
- Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte
- È consigliabile correggere le vulnerabilità nelle configurazioni della sicurezza dei contenitori

## <a name="guidelines-for-database-systems-management---database-servers"></a>Linee guida per la gestione dei sistemi di database - Server di database

### <a name="1425-hard-disks-of-database-servers-are-encrypted-using-full-disk-encryption"></a>1425 I dischi rigidi dei server di database vengono crittografati con crittografia completa

- La crittografia del disco deve essere applicata nelle macchine virtuali
- Transparent Data Encryption deve essere abilitata nei database SQL

### <a name="1277-information-communicated-between-database-servers-and-web-applications-is-encrypted"></a>1277 Le informazioni comunicate tra i server di database e le applicazioni Web sono crittografate

- Devono essere abilitate solo connessioni sicure alla Cache Redis
- Il trasferimento sicuro negli account di archiviazione deve essere abilitato
- Mostra i risultati del controllo dai server Web Windows che non usano protocolli di comunicazione sicuri
- Distribuisci i prerequisiti per controllare i server Web Windows che non usano protocolli di comunicazione sicuri

## <a name="guidelines-for-database-systems-management---database-management-system-software"></a>Linee guida per la gestione dei sistemi di database - Software del sistema di gestione di database

### <a name="1260-default-database-administrator-accounts-are-disabled-renamed-or-have-their-passphrases-changed"></a>1260 Gli account amministratore di database predefiniti vengono disabilitati, rinominati o ne viene cambiata la passphrase

- È consigliabile effettuare il provisioning di un amministratore di Azure Active Directory per SQL Server

### <a name="1262-database-administrators-have-unique-and-identifiable-accounts"></a>1262 Gli amministratori di database hanno account univoci e identificabili

- È consigliabile effettuare il provisioning di un amministratore di Azure Active Directory per SQL Server

### <a name="1261-database-administrator-accounts-are-not-shared-across-different-databases"></a>1261 Gli account amministratore di database non vengono condivisi tra database diversi

- È consigliabile effettuare il provisioning di un amministratore di Azure Active Directory per SQL Server

### <a name="1263-database-administrator-accounts-are-used-exclusively-for-administrative-tasks-with-standard-database-accounts-used-for-general-purpose-interactions-with-database"></a>1263 Gli account amministratore di database vengono usati esclusivamente per attività amministrative, con account di database standard usati per le interazioni per utilizzo generico con il database

- È consigliabile effettuare il provisioning di un amministratore di Azure Active Directory per SQL Server

### <a name="1264-database-administrator-access-is-restricted-to-defined-roles-rather-than-accounts-with-default-administrative-permissions-or-all-permissions"></a>1264 L'accesso dell'amministratore di database è limitato ai ruoli definiti anziché agli account con autorizzazioni amministrative predefinite o a tutte le autorizzazioni

- È consigliabile effettuare il provisioning di un amministratore di Azure Active Directory per SQL Server

## <a name="guidelines-for-using-cryptography---cryptographic-fundamentals"></a>Linee guida per l'uso della crittografia - Informazioni di base sulla crittografia

### <a name="0459-encryption-software-used-for-data-at-rest-implements-full-disk-encryption-or-partial-encryption-where-access-controls-will-only-allow-writing-to-the-encrypted-partition"></a>0459 Il software di crittografia usato per i dati inattivi implementa la crittografia completa del disco o la crittografia parziale in cui i controlli di accesso consentiranno la scrittura solo nella partizione crittografata

- La crittografia del disco deve essere applicata nelle macchine virtuali

## <a name="guidelines-for-using-cryptography---transport-layer-security"></a>Linee guida per l'uso della crittografia - Transport Layer Security

### <a name="1139-only-the-latest-version-of-tls-is-used"></a>1139 Viene usata solo l'ultima versione di TLS

- Nell'app per le API è necessario usare la versione più recente di TLS
- Nell'app Web è necessario usare la versione più recente di TLS
- Nell'app per le funzioni è necessario usare la versione più recente di TLS
- Distribuisci i prerequisiti per controllare i server Web Windows che non usano protocolli di comunicazione sicuri
- Mostra i risultati del controllo dai server Web Windows che non usano protocolli di comunicazione sicuri

## <a name="guidelines-for-data-transfers-and-content-filtering---content-filtering"></a>Linee guida per i trasferimenti di dati e il filtro del contenuto - Filtro del contenuto

### <a name="1288-antivirus-scanning-using-multiple-different-scanning-engines-is-performed-on-all-content"></a>1288 L'analisi antivirus, che usa più motori di analisi diversi, viene eseguita su tutto il contenuto

- È consigliabile distribuire l'estensione Microsoft IaaSAntimalware nei server Windows
- La soluzione Endpoint Protection deve essere installata nei set di scalabilità di macchine virtuali
- Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure

## <a name="guidelines-for-data-transfers-and-content-filtering---web-application-development"></a>Linee guida per i trasferimenti di dati e il filtro del contenuto - Sviluppo di applicazioni Web

### <a name="1552-all-web-application-content-is-offered-exclusively-using-https"></a>1552 Tutto il contenuto delle applicazioni Web viene offerto esclusivamente tramite HTTPS

- L'app per le funzioni deve essere accessibile solo tramite HTTPS
- L'app per le API deve essere accessibile solo tramite HTTPS
- L'applicazione Web deve essere accessibile solo tramite HTTPS
- Devono essere abilitate solo connessioni sicure alla Cache Redis

### <a name="1424-web-browser-based-security-controls-are-implemented-for-web-applications-in-order-to-help-protect-both-web-applications-and-their-users"></a>1424 Per le applicazioni Web vengono implementati controlli di sicurezza basati su Web browser per proteggere sia le applicazioni Web che i relativi utenti

- CORS non deve consentire a tutte le risorse di accedere alle applicazioni Web

## <a name="guidelines-for-network-management---network-design-and-configuration"></a>Linee guida per la gestione della rete - Progettazione e configurazione della rete

### <a name="0520-network-access-controls-are-implemented-on-networks-to-prevent-the-connection-of-unauthorised-network-devices"></a>0520 Nelle reti vengono implementati controlli di accesso per impedire la connessione di dispositivi di rete non autorizzati

- Controlla l'accesso di rete senza restrizioni agli account di archiviazione

### <a name="1182-network-access-controls-are-implemented-to-limit-traffic-within-and-between-network-segments-to-only-those-that-are-required-for-business-purposes"></a>1182 Vengono implementati controllo di accesso alla rete per limitare il traffico all'interno e tra segmenti di rete solo a quello necessario per scopi aziendali

- Le macchine virtuali con connessione Internet devono essere protette con i gruppi di sicurezza di rete
- Controlla l'accesso di rete senza restrizioni agli account di archiviazione
- Le raccomandazioni di Protezione avanzata adattiva per la rete devono essere applicate alle macchine virtuali con connessione Internet

## <a name="guidelines-for-network-management---service-continuity-for-online-services"></a>Linee guida per la gestione della rete - Continuità del servizio per servizi online

### <a name="1431-denial-of-service-attack-prevention-and-mitigation-strategies-are-discussed-with-service-providers-specifically-their-capacity-to-withstand-denial-of-service-attacks-any-costs-likely-to-be-incurred-by-customers-resulting-from-denial-of-service-attacks-thresholds-for-notifying-customers-or-turning-off-their-online-services-during-denial-of-service-attacks-pre-approved-actions-that-can-be-undertaken-during-denial-of-service-attacks-denial-of-service-attack-prevention-arrangements-with-upstream-providers-to-block-malicious-traffic-as-far-upstream-as-possible"></a>1431 Le strategie di prevenzione e mitigazione degli attacchi Denial of Service vengono discusse con i provider di servizio, in particolare: la loro capacità di resistere ad attacchi Denial of Service, gli eventuali costi in cui potrebbero incorrere i clienti come conseguenza di attacchi Denial of Service, le sogli per l'invio di notifiche ai clienti o la disattivazione dei servizi online durante attacchi Denial of Service, le azioni pre-approvate da intraprendere durante gli attacchi Denial of Service, le disposizioni di prevenzione degli attacchi Denial of Service stabilite con i provider upstream per bloccare il traffico dannoso quanto più upstream possibile

- Lo standard di protezione DDoS deve essere abilitato


> [!NOTE]
> La disponibilità di specifiche definizioni di Criteri di Azure può variare in Azure per enti pubblici e in altri cloud nazionali. 

## <a name="next-steps"></a>Passaggi successivi

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).
