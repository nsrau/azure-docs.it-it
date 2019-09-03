---
title: Esempio-blueprint ufficiale del Regno Unito e del SSN UK-mapping dei controlli
description: Controllare il mapping degli esempi di progetto ufficiale UK e del SSN UK.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: b53f293f6289377d85f4346933f7edb7369065cf
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231624"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>Controllo del mapping degli esempi di progetto ufficiale UK e del SSN UK

Nell'articolo seguente viene illustrato in dettaglio come vengono mappati gli esempi ufficiali del Regno Unito e del SSN UK per i controlli del Regno Unito e del Regno Unito. Per ulteriori informazioni sui controlli, vedere il [sito ufficiale UK](https://www.gov.uk/government/publications/government-security-classifications).

I mapping seguenti si riportano ai controlli del **Regno Unito ufficiale** e del **Regno Unito** . Usare la barra di spostamento a destra per passare direttamente a uno specifico mapping. Molti controlli mappati vengono implementati con un'iniziativa di [Criteri di Azure](../../../policy/overview.md). Per esaminare l'iniziativa completa, aprire **Criteri** nel portale di Azure e selezionare la pagina **Definizioni**. Quindi, trovare e selezionare i  **\[controlli\] di anteprima del Regno Unito e dei controlli NHS del Regno Unito e distribuire estensioni VM specifiche per supportare i requisiti di controllo** predefiniti Initiative.

## <a name="1-data-in-transit-protection"></a>1 dati nella protezione di transito

Il progetto consente di garantire la sicurezza del trasferimento delle informazioni con i servizi di Azure tramite l'assegnazione di definizioni di [criteri di Azure](../../../policy/overview.md) che controllano le connessioni non sicure agli account di archiviazione e alla cache Redis.

- Devono essere abilitate solo connessioni sicure alla Cache Redis
- Il trasferimento sicuro negli account di archiviazione deve essere abilitato

## <a name="23-data-at-rest-protection"></a>2,3 protezione dei dati inattivi

Questo progetto consente di applicare i criteri per l'uso dei controlli di crittografia assegnando definizioni di [criteri di Azure](../../../policy/overview.md) che applicano controlli di crittografia specifici e controllano l'uso di impostazioni crittografiche vulnerabili.
Identificando le risorse di Azure le cui configurazioni di crittografia potrebbero non essere ottimali, è possibile adottare azioni correttive per assicurarsi che le risorse siano configurate in conformità ai criteri di sicurezza delle informazioni. In particolare, i criteri assegnati da questo progetto richiedono la crittografia per gli account di archiviazione di data Lake; richiedere Transparent Data Encryption nei database SQL; controllare la crittografia mancante per gli account di archiviazione, i database SQL, i dischi delle macchine virtuali e le variabili dell'account di automazione. controllare le connessioni non sicure agli account di archiviazione e alla cache Redis; Controlla la crittografia della password della macchina virtuale vulnerabile; e controllano la comunicazione Service Fabric non crittografata.

- Transparent Data Encryption deve essere abilitata nei database SQL
- La crittografia del disco deve essere applicata nelle macchine virtuali
- Le variabili dell'account di automazione devono essere crittografate
- Il trasferimento sicuro negli account di archiviazione deve essere abilitato
- Per i cluster Service Fabric la proprietà ClusterProtectionLevel deve essere impostata su EncryptAndSign
- Transparent Data Encryption deve essere abilitata nei database SQL
- Distribuisci Transparent Data Encryption nel database SQL
- Imponi crittografia per gli account Data Lake Store
- Percorsi consentiti (è stato hardcoded in "UK Sud" e "UK WEST")
- Percorsi consentiti per i gruppi di risorse (è stato hardcoded in "Regno Unito meridionale" e "Regno Unito occidentale")

## <a name="52-vulnerability-management"></a>Gestione delle vulnerabilità 5,2

Questo progetto consente di gestire le vulnerabilità del sistema informativo assegnando le definizioni di [criteri di Azure](../../../policy/overview.md) che monitorano la protezione degli endpoint mancanti, gli aggiornamenti del sistema mancanti, le vulnerabilità del sistema operativo, le vulnerabilità SQL e virtuali vulnerabilità del computer. Queste informazioni dettagliate forniscono dati in tempo reale sullo stato di sicurezza delle risorse distribuite e consentono di assegnare priorità alle azioni correttive.

- Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure
- Gli aggiornamenti di sistema devono essere installati nelle macchine
- Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte
- Le vulnerabilità dei database SQL devono essere risolte
- Le vulnerabilità devono essere risolte tramite una soluzione di valutazione della vulnerabilità

## <a name="53-protective-monitoring"></a>5,3 monitoraggio protetto

Questo progetto consente di proteggere le risorse del sistema informativo assegnando le definizioni di [criteri di Azure](../../../policy/overview.md) che forniscono il monitoraggio protetto su accesso illimitato, attività whitelist e minacce.

- Controlla l'accesso di rete senza restrizioni agli account di archiviazione
- I controlli applicazioni adattivi devono essere abilitati nelle macchine virtuali
- Distribuisci Rilevamento minacce nelle istanze di SQL Server
- Distribuire l'estensione anti-malware Microsoft IaaS predefinita per Windows Server

## <a name="9-secure-user-management--10-identity-and-authentication"></a>9 gestione sicura degli utenti/10 identità e autenticazione

Azure implementa il controllo degli accessi in base al ruolo (RBAC) per semplificare la gestione degli utenti che hanno accesso alle risorse in Azure. Usando il portale di Azure, è possibile verificare chi ha accesso alle risorse di Azure e le relative autorizzazioni. Questo progetto consente di limitare e controllare i diritti di accesso assegnando le definizioni di [criteri di Azure](../../../policy/overview.md) per controllare gli account esterni con autorizzazioni di proprietario e/o lettura/scrittura e account con autorizzazioni proprietario, lettura e/o scrittura che non dispongono di più fattori autenticazione abilitata.

- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione
- L'autenticazione a più fattori deve essere abilitata per gli account con autorizzazioni di scrittura sulla sottoscrizione
- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di lettura per la sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di lettura devono essere rimossi dalla sottoscrizione

Questo progetto assegna le definizioni di criteri di Azure per controllare l'uso dell'autenticazione Azure Active Directory per SQL Server e Service Fabric. L'uso dell'autenticazione di Azure Active Directory consente una gestione semplificata delle autorizzazioni e una gestione centralizzata delle identità degli utenti di database e di altri servizi Microsoft.

- È consigliabile effettuare il provisioning di un amministratore di Azure Active Directory per SQL Server
- I cluster di Service Fabric deve usare solo Azure Active Directory per l'autenticazione client

Questo progetto assegna anche le definizioni di criteri di Azure agli account di controllo che devono avere priorità per la revisione, inclusi gli account ammortizzati e gli account esterni. Se necessario, è possibile bloccare l'accesso degli account (oppure rimuoverli), rimuovendo immediatamente i diritti di accesso alle risorse di Azure. Questo progetto assegna due definizioni di criteri di Azure per controllare l'account ammortizzato da considerare per la rimozione.

- Gli account deprecati devono essere rimossi dalla sottoscrizione
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione

Questo progetto assegna anche una definizione di criteri di Azure che controlla le autorizzazioni del file di password della VM Linux per avvisare se sono impostate in modo errato. Questa progettazione consente di intraprendere azioni correttive per garantire che gli autenticatori non siano compromessi.

- \[Anteprima\]: Controllare che le autorizzazioni file etc/passwd della VM Linux siano impostate su 0644

Questo progetto consente di applicare password complesse assegnando definizioni di criteri di Azure che controllano le macchine virtuali Windows che non applicano la forza minima e altri requisiti di password. Identificando le VM in violazione dei criteri di complessità delle password, è possibile adottare azioni correttive per assicurarsi che le password di tutti gli account utente delle VM siano conformi ai criteri.

- \[Anteprima\]: Distribuisci i requisiti per controllare le macchine virtuali Windows in cui non è abilitata l'impostazione relativa alla complessità della password
- \[Anteprima\]: Distribuisci i requisiti per controllare le macchine virtuali Windows in cui la validità massima della password non è impostata su 70 giorni
- \[Anteprima\]: Distribuisci i requisiti per controllare le macchine virtuali Windows in cui la validità minima della password non è impostata su 1 giorno
- \[Anteprima\]: Distribuisci i requisiti per controllare le macchine virtuali Windows che non limitano la lunghezza minima della password a 14 caratteri
- \[Anteprima\]: Distribuisci i requisiti per controllare le macchine virtuali Windows che consentono il riutilizzo delle 24 password precedenti
- \[Anteprima\]: Controlla le macchine virtuali Windows in cui non è abilitata l'impostazione relativa alla complessità della password
- \[Anteprima\]: Controlla le macchine virtuali Windows in cui la validità minima della password non è impostata su 70 giorni
- \[Anteprima\]: Controlla le macchine virtuali Windows in cui la validità minima della password non è impostata su 1 giorno
- \[Anteprima\]: Controlla le macchine virtuali Windows in cui la lunghezza minima della password non è limitata a 14 caratteri
- \[Anteprima\]: Controlla le macchine virtuali Windows che consentono il riutilizzo delle 24 password precedenti

Questo progetto consente anche di controllare l'accesso alle risorse di Azure assegnando le definizioni di criteri di Azure. Questi criteri controllano l'uso dei tipi di risorse e le configurazioni che potrebbero consentire un accesso più permissivo alle risorse. Identificando le risorse in violazione di questi criteri, è possibile adottare azioni correttive per restringere l'accesso alla risorse di Azure consentendolo solo agli utenti autorizzati.

- \[Anteprima\]: Distribuisci i requisiti per controllare le macchine virtuali Linux in cui sono presenti account senza password
- \[Anteprima\]: Distribuisci requisiti per controllare le macchine virtuali Linux che consentono connessioni remote da account senza password
- \[Anteprima\]: Controlla le macchine virtuali Linux in cui sono presenti account senza password
- \[Anteprima\]: Controlla le macchine virtuali Linux che non consentono connessioni remote da account senza password
- È necessario eseguire la migrazione degli account di archiviazione alle nuove risorse Azure Resource Manager
- È necessario eseguire la migrazione delle macchine virtuali a nuove risorse Azure Resource Manager
- Controlla macchine virtuali che non usano dischi gestiti

## <a name="11-external-interface-protection"></a>11 protezione interfaccia esterna

Oltre a usare più di 25 criteri per una gestione sicura degli utenti appropriata, questo progetto consente di proteggere le interfacce di servizio da accessi non autorizzati assegnando una definizione di [criteri di Azure](../../../policy/overview.md) che monitora gli account di archiviazione senza restrizioni. Gli account di archiviazione con accesso illimitato possono consentire l'accesso non intenzionale alle informazioni contenute all'interno del sistema informativo. Questo progetto assegna anche un criterio che Abilita i controlli delle applicazioni adattivi nelle macchine virtuali.

- Controlla l'accesso di rete senza restrizioni agli account di archiviazione
- I controlli applicazioni adattivi devono essere abilitati nelle macchine virtuali

## <a name="12-secure-service-administration"></a>12 amministrazione sicura del servizio

Azure implementa il controllo degli accessi in base al ruolo (RBAC) per semplificare la gestione degli utenti che hanno accesso alle risorse in Azure. Usando il portale di Azure, è possibile verificare chi ha accesso alle risorse di Azure e le relative autorizzazioni. Questo progetto consente di limitare e controllare i diritti di accesso con privilegi assegnando cinque definizioni di [criteri di Azure](../../../policy/overview.md) per controllare gli account esterni con autorizzazioni di proprietario e/o scrittura e account con proprietario e/o autorizzazioni di scrittura senza autenticazione a più fattori abilitata.

Ai sistemi usati per l'amministrazione di un servizio cloud viene concesso l'accesso al servizio con privilegi elevati. Un'eventuale violazione di questi sistemi avrebbe quindi conseguenze piuttosto gravi, come la possibilità di aggirare i controlli di sicurezza e rubare o modificare grandi volumi di dati. I metodi utilizzati dagli amministratori del provider di servizi per gestire il servizio operativo devono essere progettati per mitigare il rischio di sfruttamento che può compromettere la sicurezza del servizio. Se questo principio non è implementato, un utente malintenzionato potrebbe avere i mezzi per ignorare i controlli di sicurezza e rubare o manipolare grandi volumi di dati.

- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione
- L'autenticazione a più fattori deve essere abilitata per gli account con autorizzazioni di scrittura sulla sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione

Questo progetto assegna le definizioni di criteri di Azure per controllare l'uso dell'autenticazione Azure Active Directory per SQL Server e Service Fabric. L'uso dell'autenticazione di Azure Active Directory consente una gestione semplificata delle autorizzazioni e una gestione centralizzata delle identità degli utenti di database e di altri servizi Microsoft.

- È consigliabile effettuare il provisioning di un amministratore di Azure Active Directory per SQL Server
- I cluster di Service Fabric deve usare solo Azure Active Directory per l'autenticazione client

Questo progetto assegna anche le definizioni di criteri di Azure agli account di controllo che devono avere priorità per la revisione, inclusi gli account ammortizzati e gli account esterni con autorizzazioni elevate. Se necessario, è possibile bloccare l'accesso degli account (oppure rimuoverli), rimuovendo immediatamente i diritti di accesso alle risorse di Azure. Questo progetto assegna due definizioni di criteri di Azure per controllare l'account ammortizzato da considerare per la rimozione.

- Gli account deprecati devono essere rimossi dalla sottoscrizione
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione

Questo progetto assegna anche una definizione di criteri di Azure che controlla le autorizzazioni del file di password della VM Linux per avvisare se sono impostate in modo errato. Questa progettazione consente di intraprendere azioni correttive per garantire che gli autenticatori non siano compromessi.

- \[Anteprima\]: Controllare che le autorizzazioni file etc/passwd della VM Linux siano impostate su 0644

## <a name="13-audit-information-for-users"></a>13 informazioni di controllo per gli utenti

Questo progetto consente di assicurarsi che gli eventi di sistema vengano registrati assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che controllano le impostazioni dei log nelle risorse di Azure. Un criterio assegnato controlla inoltre se le macchine virtuali non inviano i log a un'area di lavoro di analisi dei log specificata.

- È consigliabile abilitare il controllo nelle impostazioni di Sicurezza dei dati avanzata in SQL Server
- Controlla impostazione di diagnostica
- Controlla le impostazioni di controllo a livello del server SQL
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Linux
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Windows
- Distribuisci Network Watcher quando vengono create reti virtuali

## <a name="next-steps"></a>Passaggi successivi

Ora che è stata esaminata la mappatura dei controlli del Regno Unito e dei progetti del SSN UK, vedere gli articoli seguenti per informazioni sulla panoramica e su come distribuire questo esempio:

> [!div class="nextstepaction"]
> [Ufficiale del Regno Unito e piani di NHS UK-Panoramica](./index.md)
> [ufficiale UK e progetti NHS UK-passaggi di distribuzione](./deploy.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).
