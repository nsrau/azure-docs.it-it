---
title: Controlli dell'esempio di progetto Media
description: Mapping dei controlli dell'esempio di progetto Media. Ogni controllo viene mappato a uno o più criteri di Azure che assistono nella valutazione.
ms.date: 02/24/2020
ms.topic: sample
ms.openlocfilehash: bdc4ef65c6d61a9d4217d7873bb29bd787842e71
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201738"
---
# <a name="control-mapping-of-the-media-blueprint-sample"></a>Mapping dei controlli dell'esempio di progetto Media

L'articolo seguente descrive il mapping dell'esempio di progetto Media di Azure Blueprints ai controlli Media. Per altre informazioni sui controlli, vedere [Media](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

I mapping seguenti fanno riferimento ai controlli **Media**. Usare la barra di spostamento a destra per passare direttamente a uno specifico mapping. Molti controlli mappati vengono implementati con un'iniziativa di [Criteri di Azure](../../../policy/overview.md). Per esaminare l'iniziativa completa, aprire **Criteri** nel portale di Azure e selezionare la pagina **Definizioni**. Trovare e selezionare l'iniziativa dei criteri predefinita **\[Anteprima\]: Verifica controlli Media**.

> [!IMPORTANT]
> Ogni controllo tra quelli riportati di seguito è associato a una o più definizioni di [Criteri di Azure](../../../policy/overview.md). Questi criteri possono aiutare a [valutare la conformità](../../../policy/how-to/get-compliance-data.md) con il controllo. In molti casi tuttavia non si tratta di una corrispondenza 1:1 o completa tra un controllo e uno o più criteri. Di per sé, **Conforme** in Criteri di Azure si riferisce solo ai criteri stessi e non garantisce che l'utente sia completamente conforme a tutti i requisiti di un controllo. Inoltre, in questo momento lo standard di conformità include controlli che non vengono gestiti da alcuna definizione di Criteri di Azure. La conformità in Criteri di Azure è quindi solo una visualizzazione parziale dello stato di conformità generale. Le associazioni tra i controlli e le definizioni di Criteri di Azure per questo esempio di progetto di conformità possono cambiare nel tempo. Per visualizzare la cronologia delle modifiche, vedere la [cronologia dei commit di GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/media/control-mapping.md).

## <a name="access-control"></a>Controllo dell’accesso

### <a name="ac-11--ensure-no-root-access-key-exists"></a>AC-1.1 - Assicurarsi che non esista una chiave di accesso radice

- \[Anteprima\]: Distribuisci i prerequisiti per controllare le macchine virtuali Windows che non contengono i certificati specificati nell'archivio certificati Autorità di certificazione radice disponibile nell'elenco locale

### <a name="ac-12---passwords-pins-and-tokens-must-be-protected"></a>AC-1.2 - Le password, i pin e i token devono essere protetti

- \[Anteprima\]: Distribuisci i prerequisiti per controllare le macchine virtuali Windows che non limitano la lunghezza minima della password a 14 caratteri

### <a name="ac-18---shared-account-access-is-prohibited"></a>AC-1.8 - L'accesso all'account condiviso non è consentito

- È consigliabile rimuovere tutte le regole di autorizzazione ad eccezione di RootManageSharedAccessKey dallo spazio dei nomi del bus di servizio

### <a name="ac-19--system-must-restrict-access-to-authorized-users"></a>AC-1.9 - Il sistema deve limitare l'accesso agli utenti autorizzati.

- Controlla l'accesso di rete senza restrizioni agli account di archiviazione

### <a name="ac--114--system-must-enforce-access-rights"></a>AC-1.14 - Il sistema deve applicare i diritti di accesso.

- \[Anteprima\]: Distribuisci i prerequisiti per controllare le configurazioni delle macchine virtuali Windows in 'Assegnazione diritti utente'

### <a name="ac--115--prevent-unauthorized-access-to-security-relevant-information-or-functions"></a>AC-1.15 - Impedire l'accesso non autorizzato alle informazioni o alle funzioni rilevanti per la sicurezza.

- \[Anteprima\]: Mostra i risultati del controllo dalle configurazioni delle macchine virtuali Windows in 'Opzioni di sicurezza - Impostazioni di sistema'

### <a name="ac-1-21---separation-of-duties-must-be-enforced-through-appropriate-assignment-of-role"></a>AC-1-21 - La separazione dei compiti deve essere applicata tramite l'assegnazione appropriata del ruolo.

- [Anteprima\]: il controllo degli accessi in base al ruolo deve essere usato con il servizio Azure Kubernetes

### <a name="ac-140--ensure-that-systems-are-not-connecting-trusted-network-and-untrusted-networks-at-the-same-time"></a>AC-1.40 - Assicurarsi che i sistemi non si connettano contemporaneamente alla rete attendibile e a reti non attendibili.

- \[Anteprima\]: Distribuisci i prerequisiti per controllare le configurazioni delle macchine virtuali Windows in 'Opzioni di sicurezza - Accesso di rete'

### <a name="ac-142--ac--143---remote-access-for-non-employees-must-be-restricted-to-allow-access-only-to-specifically-approved-information-systems"></a>AC-1.42 e AC-1.43 - L'accesso remoto per i non dipendenti deve essere limitato per consentire l'accesso solo a sistemi informativi specificamente approvati

- \[Anteprima\]: Mostra i risultati del controllo dalle macchine virtuali Linux che consentono connessioni remote da account senza password

### <a name="ac-150--log-security-related-events-for-all-information-system-components"></a>AC-1.50 - Registrare eventi correlati alla sicurezza per tutti i componenti dei sistemi informativi.

- È consigliabile abilitare i log di diagnostica in App per la logica

### <a name="ac-154--ensure-multi-factor-authentication-mfa-is-enabled-for-all-cloud-console-users"></a>AC-1.54- Assicurarsi che l'autenticazione a più fattori (MFA) sia abilitata per tutti gli utenti della console cloud.

- L'autenticazione MFA deve essere abilitata per gli account con autorizzazioni di scrittura per la sottoscrizione
- È necessario abilitare Multi-Factor Authentication (MFA) per tutti gli account della sottoscrizione con privilegi di scrittura per evitare una violazione di account o risorse.

## <a name="auditing--logging"></a>Controllo e registrazione

### <a name="al-21--successful-and-unsuccessful-events-must-be-logged"></a>AL-2.1: è necessario registrare gli eventi con esito positivo e negativo.

- È consigliabile abilitare i log di diagnostica nei servizi di ricerca

### <a name="al--216---network-devicesinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-network-deviceinstance-elbs-web-application-firewalls-etc"></a>AL -2.16 - I dispositivi/istanze di rete devono registrare qualsiasi evento classificato come evento di sicurezza critico da tale dispositivo/istanza di rete (ELB, web application firewalls e così via).

- \[Anteprima\]: Mostra i risultati del controllo dalle configurazioni delle macchine virtuali Windows in 'Opzioni di sicurezza - Account'

### <a name="al-217--serversinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-serverinstance"></a>AL-2.17 - I server/istanze devono registrare qualsiasi evento classificato come evento di sicurezza critico da tale server/istanza

- \[Anteprima\]: Mostra i risultati del controllo dalle configurazioni delle macchine virtuali Windows in 'Opzioni di sicurezza - Account'

### <a name="al-219---domain-events-must-log-any-event-classified-as-a-critical-or-high-security-event-by-the-domain-management-software"></a>AL-2.19 - Gli eventi di dominio devono registrare qualsiasi evento classificato come evento di sicurezza critico o con sicurezza elevata dal software di gestione del dominio

- \[Anteprima\]: Mostra i risultati del controllo dalle configurazioni delle macchine virtuali Windows in 'Opzioni di sicurezza - Account'
- \[Anteprima\]: Distribuisci i prerequisiti per controllare le configurazioni delle macchine virtuali Windows in 'Opzioni di sicurezza - Client di rete Microsoft'

### <a name="al-220--domain-events-must-log-any-event-classified-as-a-critical-security-event-by-domain-security-controls"></a>AL-2.20 - Gli eventi di dominio devono registrare qualsiasi evento classificato come evento di sicurezza critico dai controlli di sicurezza del dominio

- \[Anteprima\]: Mostra i risultati del controllo dalle configurazioni delle macchine virtuali Windows in 'Opzioni di sicurezza - Account'

### <a name="al-221--domain-events-must-log-any-access-or-changes-to-the-domain-log"></a>AL-2.21 - Gli eventi di dominio devono registrare eventuali accessi o modifiche al log di dominio

- \[Anteprima\]: Mostra i risultati del controllo dalle configurazioni delle macchine virtuali Windows in 'Opzioni di sicurezza - Console di ripristino di emergenza'

## <a name="cryptographic-controls"></a>Controlli crittografici

### <a name="cc-42--applications-and-systems-must-use-current-cryptographic-solutions-for-protecting-data"></a>CC-4.2 - Le applicazioni e i sistemi devono usare le soluzioni crittografiche correnti per la protezione dei dati.

- Transparent Data Encryption deve essere abilitata nei database SQL
- Abilitare Transparent Data Encryption per proteggere i dati inattivi e rispettare i requisiti relativi alla conformità

### <a name="cc-45--digital-certificates-must-be-signed-by-an-approved-certificate-authority"></a>CC-4.5 - I certificati digitali devono essere firmati da un'autorità di certificazione approvata.

- \[Anteprima\]: Mostra i risultati del controllo dalle macchine virtuali Windows che contengono certificati in scadenza entro il numero di giorni specificato

### <a name="cc-46--digital-certificates-must-be-uniquely-assigned-to-a-user-or-device"></a>CC-4.6- I certificati digitali devono essere assegnati in modo univoco a un utente o un dispositivo.

- \[Anteprima\]: Distribuisci i prerequisiti per controllare le macchine virtuali Windows che contengono certificati in scadenza entro il numero di giorni specificato

### <a name="cc-47--cryptographic-material-must-be-stored-to-enable-decryption-of-the-records-for-the-length-of-time-the-records-are-retained"></a>CC-4.7 - È necessario archiviare il materiale crittografico per consentire la decrittografia dei record per il periodo di tempo in cui i record vengono conservati.

- La crittografia del disco deve essere applicata nelle macchine virtuali
- Le macchine virtuali per cui non è abilitata la crittografia dei dischi verranno monitorate dal Centro sicurezza di Azure che invierà consigli

### <a name="cc-48--secret-and-private-keys-must-be-stored-securely"></a>CC-4.8 - I segreti e le chiavi private devono essere archiviati in modo sicuro.

- Transparent Data Encryption deve essere abilitata nei database SQL
- Abilitare Transparent Data Encryption per proteggere i dati inattivi e rispettare i requisiti relativi alla conformità

## <a name="change--config-management"></a>Gestione delle modifiche e della configurazione

### <a name="cm-52--only-authorized-users-may-implement-approved-changes-on-the-system"></a>CM-5.2- Solo gli utenti autorizzati possono implementare modifiche approvate sul sistema.

- Gli aggiornamenti di sistema devono essere installati nelle macchine
- Gli aggiornamenti di sistema per la sicurezza nei server verranno monitorati dal Centro sicurezza di Azure che invierà i consigli corrispondenti

### <a name="cm-512--maintain-an-up-to-date-complete-accurate-and-readily-available-baseline-configuration-of-the-information-system"></a>CM-5.12- Mantenere aggiornata, completa, accurata e immediatamente disponibile una configurazione di base del sistema informativo.

- Gli aggiornamenti di sistema devono essere installati nelle macchine
- Gli aggiornamenti di sistema per la sicurezza nei server verranno monitorati dal Centro sicurezza di Azure che invierà i consigli corrispondenti

### <a name="cm-513--employ-automated-tools-to-maintain-a-baseline-configuration-of-the-information-system"></a>CM-5.13 - Utilizzare strumenti automatizzati per mantenere una configurazione di base del sistema informativo.

- Gli aggiornamenti di sistema devono essere installati nelle macchine
- Gli aggiornamenti di sistema per la sicurezza nei server verranno monitorati dal Centro sicurezza di Azure che invierà i consigli corrispondenti

### <a name="cm-514--identify-and-disable-unnecessary-andor-non-secure-functions-ports-protocols-and-services"></a>CM-5.14- Identificare e disabilitare funzioni, porte, protocolli e servizi non necessari e/o non sicuri.

- Le interfacce di rete devono disabilitare l'inoltro IP
- \[Anteprima\]: L'inoltro IP nella macchina virtuale deve essere disabilitato

### <a name="cm-519--monitor-changes-to-the-security-configuration-settings"></a>CM-5.19 - Monitorare le modifiche apportate alle impostazioni di configurazione della sicurezza.

- Distribuire le impostazioni di diagnostica per i gruppi di sicurezza di rete

### <a name="cm-522--ensure-that-only-authorized-software-and-updates-are-installed-on-company-systems"></a>CM-5.22 - Assicurarsi che nei sistemi aziendali siano installati solo software e aggiornamenti autorizzati.

- Gli aggiornamenti di sistema devono essere installati nelle macchine
- Gli aggiornamenti di sistema per la sicurezza nei server verranno monitorati dal Centro sicurezza di Azure che invierà i consigli corrispondenti

## <a name="identity--authentication"></a>Identità e autenticazione

### <a name="ia-71--user-accounts-must-be-uniquely-assigned-to-individuals-for-access-to-information-that-is-not-classified-as-public-account-ids-must-be-constructed-using-a-standardized-logical-format"></a>IA-7.1- Gli account utente devono essere assegnati in modo univoco alle persone per l'accesso alle informazioni che non sono classificate come pubbliche. Gli ID account devono essere creati usando un formato logico standardizzato.

- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- È necessario rimuovere dalla sottoscrizione gli account esterni con autorizzazioni di proprietario in modo da evitare l'accesso non monitorato.

## <a name="network-security"></a>Sicurezza di rete

### <a name="ns-92--access-to-network-device-management-functionality-is-restricted-to-authorized-users"></a>NS-9.2- L'accesso alla funzionalità di gestione dei dispositivi di rete è limitato agli utenti autorizzati.

- \[Anteprima\]: Distribuisci i prerequisiti per controllare le configurazioni delle macchine virtuali Windows in 'Opzioni di sicurezza - Accesso di rete'

### <a name="ns-93--all-network-devices-must-be-configured-using-their-most-secure-configurations"></a>NS-9.3- Tutti i dispositivi di rete devono essere configurati usando le rispettive configurazioni più sicure.

- \[Anteprima\]: Distribuisci i prerequisiti per controllare le configurazioni delle macchine virtuali Windows in 'Opzioni di sicurezza - Accesso di rete'

### <a name="ns-95--all-network-connections-to-a-system-through-a-firewall-must-be-approved-and-audited-on-a-regular-basis"></a>NS-9.5 - Tutte le connessioni di rete a un sistema tramite un firewall devono essere approvate e controllate a intervalli regolari.

- \[Anteprima\]: Mostra i risultati del controllo dalle configurazioni delle macchine virtuali Windows in 'Proprietà Windows Firewall'

### <a name="ns-97--appropriate-controls-must-be-present-at-any-boundary-between-a-trusted-network-and-any-untrusted-or-public-network"></a>NS-9.7- Controlli appropriati devono essere presenti tra una rete attendibile e qualsiasi rete non attendibile o pubblica.

- \[Anteprima\]: Distribuisci i prerequisiti per controllare le configurazioni delle macchine virtuali Windows in 'Proprietà Windows Firewall'

## <a name="security-planning"></a>Pianificazione della sicurezza

### <a name="sp-113--threats-must-be-identified-that-could-negatively-impact-the-confidentiality-integrity-or-availability-of-company-information-and-content-along-with-the-likelihood-of-their-occurrence"></a>SP-11.3 - È necessario identificare le minacce che potrebbero influire negativamente sulla riservatezza, l'integrità o la disponibilità di informazioni e contenuti aziendali oltre alla probabilità che si verifichino.

- È consigliabile che i Tipi di Advanced Threat Protection siano impostati su "Tutti" nelle impostazioni di Sicurezza dei dati avanzata nell'istanza gestita di SQL

### <a name="security-continuity"></a>Continuità della sicurezza

## <a name="sc-125--data-in-long-term-storage-must-be-accessible-throughout-the-retention-period-and-protected-against-media-degradation-and-technology-changes"></a>SC-12.5 - I dati nell'archiviazione a lungo termine devono essere accessibili per tutto il periodo di conservazione e protetti dall'usura dei supporti e dai cambiamenti tecnologici.

- È consigliabile che i server SQL devono siano configurati con un periodo di conservazione dei dati di controllo superiore a 90 giorni.
- Controllare i server SQL configurati con un periodo di conservazione di controllo minore di 90 giorni.

## <a name="system-integrity"></a>Integrità del sistema

### <a name="si-143--only-authorized-personnel-may-monitor-network-and-user-activities"></a>SI-14.3 - Solo il personale autorizzato può monitorare le attività di reti e utenti.

- Le vulnerabilità dei database SQL devono essere risolte
- Consente di monitorare i consigli e i risultati dell'analisi della valutazione della vulnerabilità per informazioni su come correggere le vulnerabilità del database.

### <a name="si-144--internet-facing-systems-must-have-intrusion-detection"></a>SI-14.4 - I sistemi con connessione Internet devono avere il rilevamento delle intrusioni.

- Distribuisci Rilevamento minacce nelle istanze di SQL Server

### <a name="si-1413--standardized-centrally-managed-anti-malware-software-should-be-implemented-across-the-company"></a>SI-14.13- Implementare software anti-malware gestito centralmente e standardizzato in tutta l'azienda.

- Distribuisci estensione IaaSAntimalware Microsoft predefinita per Windows Server

### <a name="si-1414--anti-malware-software-must-scan-computers-and-media-weekly-at-a-minimum"></a>SI-14.14- Il software antimalware deve eseguire la scansione di computer e supporti almeno una volta alla settimana.

- Distribuisci estensione IaaSAntimalware Microsoft predefinita per Windows Server

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

### <a name="vm-154--ensure-that-applications-are-scanned-for-vulnerabilities-on-a-monthly-basis"></a>VM-15.4 - Assicurarsi che le applicazioni vengano analizzate per rilevarne le vulnerabilità su base mensile.

- Le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali devono essere risolte
- Controlla le vulnerabilità del sistema operativo nei set di scalabilità di macchine virtuali per proteggerli da attacchi.

### <a name="vm-155--ensure-that-vulnerabilities-are-identified-paired-to-threats-and-evaluated-for-risk"></a>VM-15.5 - Assicurarsi che le vulnerabilità siano identificate, associate a minacce e valutate per il rischio.

- Le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali devono essere risolte
- Controlla le vulnerabilità del sistema operativo nei set di scalabilità di macchine virtuali per proteggerli da attacchi.

### <a name="vm-156--ensure-that-identified-vulnerabilities-have-been-remediated-within-a-mutually-agreed-upon-timeline"></a>VM-15.6 - Assicurarsi che le vulnerabilità identificate siano state risolte in una sequenza temporale reciprocamente concordata.

- Le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali devono essere risolte
- Controlla le vulnerabilità del sistema operativo nei set di scalabilità di macchine virtuali per proteggerli da attacchi.

### <a name="vm-157--access-to-and-use-of-vulnerability-management-systems-must-be-restricted-to-authorized-personnel"></a>VM-15.7 - L'accesso e l'uso dei sistemi di gestione delle vulnerabilità devono essere limitati al personale autorizzato.

- Le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali devono essere risolte
- Controlla le vulnerabilità del sistema operativo nei set di scalabilità di macchine virtuali per proteggerli da attacchi.

> [!NOTE]
> La disponibilità di specifiche definizioni di Criteri di Azure può variare in Azure per enti pubblici e in altri cloud nazionali. 

## <a name="next-steps"></a>Passaggi successivi

È stato esaminato il mapping dei controlli dell'esempio di progetto Media. Vedere gli articoli seguenti per informazioni sulla panoramica e su come distribuire questo esempio:

> [!div class="next step action"]
> [Progetto Media - Panoramica](./control-mapping.md)
> [Progetto Media - Procedura di distribuzione](./deploy.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).
