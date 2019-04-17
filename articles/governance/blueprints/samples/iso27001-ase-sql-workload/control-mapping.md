---
title: Esempio - Progetto Carico di lavoro dell'ambiente del servizio app di Azure/SQL ISO 27001 - Mapping dei controlli
description: Mapping dei controlli del progetto di esempio Carico di lavoro dell'ambiente del servizio app/database SQL ISO 27001 in Criteri di Azure e RBAC.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 7a760bfe70fa2a83c43a0b41b77ba9bf45e809ca
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59258608"
---
# <a name="control-mapping-of-the-iso-27001-asesql-workload-blueprint-sample"></a>Mapping dei controlli del progetto di esempio Carico di lavoro dell'ambiente del servizio app/SQL ISO 27001

L'articolo seguente descrive il mapping dell'esempio di progetto Carico di lavoro ASE/SQL di ISO 27001 di Azure Blueprints ai controlli ISO 27001. Per altre informazioni sui controlli, vedere [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

I mapping seguenti fanno riferimento ai controlli **ISO 27001:2013**. Usare la barra di spostamento a destra per passare direttamente a uno specifico mapping. Molti controlli mappati vengono implementati con un'iniziativa di [Criteri di Azure](../../../policy/overview.md). Per esaminare l'iniziativa completa, aprire **Criteri** nel portale di Azure e selezionare la pagina **Definizioni**. Quindi, trovare e selezionare l'iniziativa dei criteri predefinita **[Anteprima]: Verifica controlli ISO 27001:2013 e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo**.

## <a name="a612-segregation-of-duties"></a>A.6.1.2 Separazione dei compiti

La presenza di un solo proprietario di sottoscrizioni di Azure non consente la ridondanza amministrativa. Al contrario, la presenza di troppi proprietari di sottoscrizioni di Azure può aumentare la possibilità che si verifichi una violazione tramite un account di proprietario compromesso. Questo progetto consente di mantenere un numero appropriato di proprietari della sottoscrizione assegnando due definizioni di [Criteri di Azure](../../../policy/overview.md) che controllano questo numero. La gestione delle autorizzazioni dei proprietari di sottoscrizioni consente di implementare la separazione appropriata dei compiti.

- [Anteprima]: Audit minimum number of owners for subscription
- [Anteprima]: Audit maximum number of owners for a subscription

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 Accesso a reti e servizi di rete

Azure implementa il [controllo degli accessi in base al ruolo](../../../../role-based-access-control/overview.md) per gestire chi ha accesso alle risorse di Azure. Questo progetto consente di controllare l'accesso alle risorse di Azure assegnando sette definizioni di [Criteri di Azure](../../../policy/overview.md). Questi criteri controllano l'uso dei tipi di risorse e le configurazioni che potrebbero consentire un accesso più permissivo alle risorse.
Identificando le risorse in violazione di questi criteri, è possibile adottare azioni correttive per restringere l'accesso alla risorse di Azure consentendolo solo agli utenti autorizzati.

- [Anteprima]: Deploy VM extension to audit Linux VM accounts with no passwords
- [Anteprima]: Deploy VM extension to audit Linux VM allowing remote connections from accounts with no
  passwords
- [Anteprima]: Audit Linux VM accounts with no passwords
- [Anteprima]: Audit Linux VM allowing remote connections from accounts with no passwords
- Controlla l'uso degli account di archiviazione della versione classica
- Controlla l'uso delle macchine virtuali della versione classica
- Controlla macchine virtuali che non usano dischi gestiti

## <a name="a922-user-access-provisioning"></a>A.9.2.2 Provisioning degli accessi utente

Azure implementa il [controllo degli accessi in base al ruolo](../../../../role-based-access-control/overview.md) per gestire chi ha accesso alle risorse di Azure. Questo progetto assegna tre definizioni di [Criteri di Azure](../../../policy/overview.md) per controllare l'uso dell'autenticazione di [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) per istanze di SQL Server e [Service Fabric](../../../../service-fabric/service-fabric-overview.md). L'uso dell'autenticazione di Azure Active Directory consente una gestione semplificata delle autorizzazioni e una gestione centralizzata delle identità degli utenti di database e di altri servizi Microsoft. Questo progetto assegna inoltre una definizione di Criteri di Azure per controllare l'uso delle regole del controllo degli accessi in base al ruolo. Identificando dove vengono implementate regole personalizzate del controllo degli accessi in base al ruolo, è possibile verificare l'esigenza e la corretta implementazione, perché tali regole sono soggette a errore.

- Controlla il provisioning di un amministratore di Azure Active Directory per SQL Server
- Controlla l'utilizzo di Azure Active Directory per l'autenticazione client in Service Fabric
- Controlla l'uso di ruoli di controllo degli accessi in base al ruolo personalizzati

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 Gestione dei diritti di accesso con privilegi

Questo progetto consente di restringere e controllare i diritti di accesso con privilegi assegnando quattro definizioni di [Criteri di Azure](../../../policy/overview.md) per controllare gli account esterni con autorizzazioni di proprietario e/o di scrittura e gli account con autorizzazioni di proprietario e/o di scrittura per cui non è abilitata l'autenticazione a più fattori.

- [Anteprima]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Anteprima]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [Anteprima]: Audit external accounts with owner permissions on a subscription
- [Anteprima]: Audit external accounts with write permissions on a subscription

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 Gestione delle informazioni di autenticazione degli utenti tramite segreto

Questo progetto assegna tre definizioni di [Criteri di Azure](../../../policy/overview.md) per controllare gli account per cui non è abilitata l'autenticazione a più fattori. L'autenticazione a più fattori consente di mantenere sicuri gli account anche se una parte delle informazioni di autenticazione viene compromessa. Monitorando gli account senza autenticazione a più fattori abilitata, è possibile identificare quelli che potrebbero venire compromessi con più probabilità. Questo progetto assegna anche due definizioni di Criteri di Azure che controllano le autorizzazioni del file delle password delle VM Linux per avvisare se non sono impostate correttamente. Questa configurazione consente di adottare azioni correttive per assicurare che gli autenticatori non vengano compromessi.

- [Anteprima]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Anteprima]: Audit accounts with read permissions who are not MFA enabled on a subscription
- [Anteprima]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [Anteprima]: Deploy VM extension to audit Linux VM passwd file permissions
- [Anteprima]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 Revisione dei diritti di accesso utente

Azure implementa il [controllo degli accessi in base al ruolo](../../../../role-based-access-control/overview.md) per consentire di gestire chi ha accesso alle risorse di Azure. Usando il portale di Azure, è possibile verificare chi ha accesso alle risorse di Azure e le relative autorizzazioni. Questo progetto assegna quattro definizioni di [Criteri di Azure](../../../policy/overview.md) per controllare gli account la cui revisione dovrebbe essere prioritaria, inclusi gli account deprecati e quelli con autorizzazioni elevate.

- [Anteprima]: Audit deprecated accounts on a subscription
- [Anteprima]: Audit deprecated accounts with owner permissions on a subscription
- [Anteprima]: Audit external accounts with owner permissions on a subscription
- [Anteprima]: Audit external accounts with write permissions on a subscription

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 Rimozione o modifica dei diritti di accesso

Azure implementa il [controllo degli accessi in base al ruolo](../../../../role-based-access-control/overview.md) per consentire di gestire chi ha accesso alle risorse di Azure. Usando [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) e il controllo degli accessi in base al ruolo, è possibile aggiornare i ruoli degli utenti in base alle modifiche organizzative. Se necessario, è possibile bloccare l'accesso degli account (oppure rimuovere gli account), rimuovendo immediatamente i diritti di accesso alle risorse di Azure. Questo progetto assegna due definizioni di [Criteri di Azure](../../../policy/overview.md) per controllare gli account deprecati che sarebbe preferibile rimuovere.

- [Anteprima]: Audit deprecated accounts on a subscription
- [Anteprima]: Audit deprecated accounts with owner permissions on a subscription

## <a name="a943-password-management-system"></a>A.9.4.3 Sistema di gestione delle password

Questo progetto consente di imporre password complesse assegnando 10 definizioni di [Criteri di Azure](../../../policy/overview.md) che controllano le VM Windows per cui non è applicato il requisito minimo di complessità o altri requisiti delle password. Identificando le VM in violazione dei criteri di complessità delle password, è possibile adottare azioni correttive per assicurarsi che le password di tutti gli account utente delle VM siano conformi ai criteri.

- [Anteprima]: Deploy VM extension to audit Windows VM enforces password complexity requirements
- [Anteprima]: Deploy VM extension to audit Windows VM maximum password age 70 days
- [Anteprima]: Deploy VM extension to audit Windows VM minimum password age 1 day
- [Anteprima]: Deploy VM extension to audit Windows VM passwords must be at least 14 characters
- [Anteprima]: Deploy VM extension to audit Windows VM should not allow previous 24 passwords
- [Anteprima]: Audit Windows VM enforces password complexity requirements
- [Anteprima]: Audit Windows VM maximum password age 70 days
- [Anteprima]: Audit Windows VM minimum password age 1 day
- [Anteprima]: Audit Windows VM passwords must be at least 14 characters
- [Anteprima]: Audit Windows VM should not allow previous 24 passwords

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 Criteri sull'uso dei controlli crittografici

Questo progetto consente di applicare i criteri sull'uso dei controlli crittografici assegnando 13 definizioni di [Criteri di Azure](../../../policy/overview.md) che applicano specifici controlli crittografici e controllano l'uso di impostazioni di crittografia meno sicuri.
Identificando le risorse di Azure le cui configurazioni di crittografia potrebbero non essere ottimali, è possibile adottare azioni correttive per assicurarsi che le risorse siano configurate in conformità ai criteri di sicurezza delle informazioni. In particolare, i criteri assegnati da questo progetto richiedono la crittografia per gli account di archiviazione BLOB e gli account Data Lake Storage; richiedono la crittografia dei dati trasparente per i database SQL; controllano la crittografia mancante in account di archiviazione, database SQL, dischi di macchine virtuali e variabili degli account di automazione; controllano le connessioni non sicure ad account di archiviazione, app per le funzioni, app Web, app per le API e cache Redis; controllano la crittografia poco sicura delle password delle macchine virtuali e controllano le comunicazioni non crittografate con Service Fabric.

- [Anteprima]: Audit HTTPS only access for a Function App
- [Anteprima]: Audit HTTPS only access for a Web Application
- [Anteprima]: Audit HTTPS only access for an API App
- [Anteprima]: Audit missing blob encryption for storage accounts
- [Anteprima]: Deploy VM extension to audit Windows VM should not store passwords using reversible
  Crittografian
- [Anteprima]: Audit Windows VM should not store passwords using reversible encryption
- [Anteprima]: Monitor unencrypted SQL database in Azure Security Center
- [Anteprima]: Monitor unencrypted VM Disks in Azure Security Center
- Controlla l'abilitazione della crittografia delle variabili dell'account di Automazione
- Controlla l'abilitazione delle sole connessioni sicure alla cache Redis
- Controlla il trasferimento sicuro negli account di archiviazione
- Controlla l'impostazione della proprietà ClusterProtectionLevel su EncryptAndSign in Service Fabric
- Controllare lo stato di Transparent Data Encryption

## <a name="a1241-event-logging"></a>A.12.4.1 Registrazione di eventi

Questo progetto consente di assicurarsi che gli eventi di sistema vengano registrati assegnando sette definizioni di [Criteri di Azure](../../../policy/overview.md) che controllano le impostazioni dei log sulle risorse di Azure. Un criterio assegnato controlla inoltre se le macchine virtuali inviano i log a un'area di lavoro di analisi dei log specificata.

- [Anteprima]: Controlla la distribuzione di Dependency Agent - Immagine macchina virtuale (sistema operativo) non in elenco
- [Anteprima]: Controlla la distribuzione di Dependency Agent nei set di scalabilità di macchine virtuali - Immagine macchina virtuale (sistema operativo) non in elenco
- [Anteprima]: Controlla la distribuzione dell'agente di Log Analytics - Immagine macchina virtuale (sistema operativo) non in elenco
- [Anteprima]: Controlla la distribuzione dell'agente di Log Analytics nei set di scalabilità di macchine virtuali - Immagine macchina virtuale (sistema operativo) non in elenco
- [Anteprima]: Monitor unaudited SQL database in Azure Security Center
- Audit diagnostic setting (Controllare le impostazioni di diagnostica)
- Controlla le impostazioni di controllo a livello del server SQL

## <a name="a121-management-of-technical-vulnerabilities"></a>A.12.1 Gestione di vulnerabilità tecniche

Questo progetto consente di gestire le vulnerabilità dei sistemi informativi assegnando cinque definizioni di [Criteri di Azure](../../../policy/overview.md) che monitorano gli aggiornamenti di sistema mancanti, le vulnerabilità del sistema operativo, le vulnerabilità di SQL e le vulnerabilità delle macchine virtuali. Queste informazioni dettagliate forniscono dati in tempo reale sullo stato di sicurezza delle risorse distribuite e consentono di assegnare priorità alle azioni correttive.

- [Anteprima]: Monitor missing Endpoint Protection in Azure Security Center
- [Anteprima]: Monitor missing system updates in Azure Security Center
- [Anteprima]: Monitor OS vulnerabilities in Azure Security Center
- [Anteprima]: Monitor SQL vulnerability assessment results in Azure Security Center
- [Anteprima]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 Restrizioni sull'installazione di software

Il controllo applicazione adattivo è una soluzione di Centro sicurezza di Azure che consente di controllare quali applicazioni possono essere eseguite nelle VM situate in Azure. Questo progetto assegna una definizione di Criteri di Azure che monitora le modifiche apportate al set di applicazioni consentite. Le restrizioni sull'installazione di software consentono di ridurre la probabilità che vengano introdotte vulnerabilità software.

- [Anteprima]: Monitor possible app Whitelisting in Azure Security Center

## <a name="a1311-network-controls"></a>A.13.1.1 Controlli di rete

Questo progetto consente di gestire e controllare le reti assegnando una definizione di [Criteri di Azure](../../../policy/overview.md) che monitora i gruppi di sicurezza di rete con regole permissive. Le regole troppo permissive potrebbero consentire l'accesso non autorizzato alla rete e dovranno essere riviste.

- [Anteprima]: Monitor permissive network access in Azure Security Center

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 Criteri e procedure di trasferimento delle informazioni

Questo progetto consente di assicurarsi che il trasferimento di informazioni con i servizi di Azure sia sicuro assegnando due definizioni di [Criteri di Azure](../../../policy/overview.md) per controllare le connessioni non sicure ad account di archiviazione e cache Redis.

- Controlla l'abilitazione delle sole connessioni sicure alla cache Redis
- Controlla il trasferimento sicuro negli account di archiviazione

## <a name="a1413-protecting-application-services-transactions"></a>A.14.1.3 Protezione delle transazioni dei servizi per le applicazioni

Questo progetto consente di proteggere gli asset del sistema informativo assegnando tre definizioni di [Criteri di Azure](../../../policy/overview.md) che monitorano endpoint, applicazioni e account di archiviazione non protetti. Gli endpoint e le applicazioni non protetti da un firewall e gli account di archiviazione con accesso illimitato possono consentire l'accesso non autorizzato alle informazioni contenute nel sistema informativo.

- [Anteprima]: Monitor unprotected network endpoints in Azure Security Center
- [Anteprima]: Monitor unprotected web application in Azure Security Center
- Controlla l'accesso di rete senza restrizioni agli account di archiviazione

## <a name="a1613-reporting-information-security-weaknesses"></a>A.16.1.3 Report sulle debolezze della sicurezza delle informazioni

Questo progetto consente di rimanere sempre informati sulle vulnerabilità del sistema assegnando cinque definizioni di [Criteri di Azure](../../../policy/overview.md) che monitorano le vulnerabilità, lo stato delle patch e gli avvisi su malware in Centro sicurezza di Azure. Centro sicurezza di Azure fornisce funzionalità di report che consentono di ricevere informazioni dettagliate in tempo reale sullo stato di sicurezza delle risorse di Azure distribuite.

- [Anteprima]: Monitor missing Endpoint Protection in Azure Security Center
- [Anteprima]: Monitor missing system updates in Azure Security Center
- [Anteprima]: Monitor OS vulnerabilities in Azure Security Center
- [Anteprima]: Monitor SQL vulnerability assessment results in Azure Security Center
- [Anteprima]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="next-steps"></a>Passaggi successivi

Dopo aver esaminato il mapping dei controlli dell'esempio di progetto Carico di lavoro dell'ambiente del servizio app/database SQL ISO 27001, vedere gli articoli seguenti per informazioni sull'architettura e su come distribuire questo esempio:

> [!div class="nextstepaction"]
> [Progetto Carico di lavoro dell'ambiente del servizio app/database SQL ISO 27001 - Panoramica](./index.md)
> [Progetto Carico di lavoro dell'ambiente del servizio app/database SQL ISO 27001 - Procedura per la distribuzione](./deploy.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).