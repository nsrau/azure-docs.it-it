---
title: Controlli degli esempi di progetti UK OFFICIAL e UK NHS
description: Mapping dei controlli degli esempi di progetti UK OFFICIAL e UK NHS. Ogni controllo viene mappato a uno o più criteri di Azure che assistono nella valutazione.
ms.date: 06/26/2019
ms.topic: sample
ms.openlocfilehash: 79c39ee058a74bc740e72b75fe85882a89f9cd85
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546440"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>Mapping dei controlli degli esempi di progetti UK OFFICIAL e UK NHS

L'articolo seguente illustra in dettaglio il mapping degli esempi di progetti UK OFFICIAL e UK NHS con i controlli UK OFFICIAL e UK NHS. Per altre informazioni sui controlli, vedere [UK OFFICIAL](https://www.gov.uk/government/publications/government-security-classifications).

I mapping seguenti fanno riferimento ai controlli **UK OFFICIAL** e **UK NHS**. Usare la barra di spostamento a destra per passare direttamente a uno specifico mapping. Molti controlli mappati vengono implementati con un'iniziativa di [Criteri di Azure](../../../policy/overview.md). Per esaminare l'iniziativa completa, aprire **Criteri** nel portale di Azure e selezionare la pagina **Definizioni**. Quindi, trovare e selezionare l'iniziativa dei criteri predefinita **\[Anteprima\] Verifica controlli UK OFFICIAL e UK NHS e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo**.

> [!IMPORTANT]
> Ogni controllo tra quelli riportati di seguito è associato a una o più definizioni di [Criteri di Azure](../../../policy/overview.md). Questi criteri possono aiutare a [valutare la conformità](../../../policy/how-to/get-compliance-data.md) con il controllo. In molti casi tuttavia non si tratta di una corrispondenza 1:1 o completa tra un controllo e uno o più criteri. Di per sé, **Conforme** in Criteri di Azure si riferisce solo ai criteri stessi e non garantisce che l'utente sia completamente conforme a tutti i requisiti di un controllo. Inoltre, in questo momento lo standard di conformità include controlli che non vengono gestiti da alcuna definizione di Criteri di Azure. La conformità in Criteri di Azure è quindi solo una visualizzazione parziale dello stato di conformità generale. Le associazioni tra i controlli e le definizioni di Criteri di Azure per questo esempio di progetto di conformità possono cambiare nel tempo. Per visualizzare la cronologia delle modifiche, vedere la [cronologia dei commit di GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ukofficial/control-mapping.md).

## <a name="1-data-in-transit-protection"></a>1 Protezione dei dati in transito

Il progetto consente di assicurarsi che il trasferimento di informazioni con i servizi di Azure sia sicuro assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che controllano le connessioni non sicure ad account di archiviazione e cache Redis.

- Devono essere abilitate solo connessioni sicure alla Cache Redis
- Il trasferimento sicuro negli account di archiviazione deve essere abilitato

## <a name="23-data-at-rest-protection"></a>2.3 Protezione dei dati inattivi

Questo progetto consente di applicare i criteri sull'uso dei controlli crittografici assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che applicano specifici controlli crittografici e controllano l'uso di impostazioni di crittografia meno sicure.
Identificando le risorse di Azure le cui configurazioni di crittografia potrebbero non essere ottimali, è possibile adottare azioni correttive per assicurarsi che le risorse siano configurate in conformità ai criteri di sicurezza delle informazioni. In particolare, i criteri assegnati da questo progetto richiedono la crittografia per gli account Data Lake Storage; richiedono l'applicazione di Transparent Data Encryption per i database SQL; controllano se manca la crittografia in account di archiviazione, database SQL, dischi di macchine virtuali e variabili degli account di automazione; controllano le connessioni non sicure ad account di archiviazione e cache Redis; controllano la crittografia poco sicura delle password delle macchine virtuali; infine, controllano le comunicazioni non crittografate con Service Fabric.

- Transparent Data Encryption deve essere abilitata nei database SQL
- La crittografia del disco deve essere applicata nelle macchine virtuali
- Le variabili dell'account di automazione devono essere crittografate
- Il trasferimento sicuro negli account di archiviazione deve essere abilitato
- La proprietà ClusterProtectionLevel dei cluster di Service Fabric dovrebbe essere impostata su EncryptAndSign
- Transparent Data Encryption deve essere abilitata nei database SQL
- Distribuisci Transparent Data Encryption nel database SQL
- Imponi crittografia per gli account Data Lake Store
- Località consentite (impostate come hardcoded su "REGNO UNITO MERIDIONALE" e "REGNO UNITO OCCIDENTALE")
- Località consentite per i gruppi di risorse (impostate come hardcoded su "REGNO UNITO MERIDIONALE" e "REGNO UNITO OCCIDENTALE")

## <a name="52-vulnerability-management"></a>5.2 Gestione delle vulnerabilità

Questo progetto consente di gestire le vulnerabilità dei sistemi informativi assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che monitorano la protezione di endpoint mancante, gli aggiornamenti di sistema mancanti, oltre alle vulnerabilità del sistema operativo, di SQL e delle macchine virtuali. Queste informazioni dettagliate forniscono dati in tempo reale sullo stato di sicurezza delle risorse distribuite e consentono di assegnare priorità alle azioni correttive.

- Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure
- Gli aggiornamenti di sistema devono essere installati nelle macchine
- Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte
- Le vulnerabilità dei database SQL devono essere risolte
- Le vulnerabilità devono essere risolte tramite una soluzione di valutazione della vulnerabilità

## <a name="53-protective-monitoring"></a>5.3 Monitoraggio protettivo

Questo progetto consente di proteggere gli asset dei sistemi informativi assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che forniscono il monitoraggio protettivo dell'accesso senza restrizioni, dell'attività di inserimento di elementi consentiti e delle minacce.

- Controlla l'accesso di rete senza restrizioni agli account di archiviazione
- I controlli applicazioni adattivi devono essere abilitati nelle macchine virtuali
- Distribuisci Rilevamento minacce nelle istanze di SQL Server
- Distribuisci estensione IaaS antimalware Microsoft predefinita per Windows Server

## <a name="9-secure-user-management--10-identity-and-authentication"></a>9 Gestione sicura degli utenti/10 Identità e autenticazione

Azure implementa il controllo degli accessi in base al ruolo per consentire di gestire chi ha accesso alle risorse di Azure. Usando il portale di Azure, è possibile verificare chi ha accesso alle risorse di Azure e le relative autorizzazioni. Questo progetto consente di restringere e controllare i diritti di accesso assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) per controllare gli account esterni con autorizzazioni di proprietario e/o di lettura/scrittura e gli account con autorizzazioni di proprietario, di lettura e/o di scrittura per cui non è abilitata l'autenticazione a più fattori.

- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione
- L'autenticazione MFA deve essere abilitata per gli account con autorizzazioni di scrittura per la sottoscrizione
- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di lettura per la sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di lettura devono essere rimossi dalla sottoscrizione

Questo progetto assegna definizioni di Criteri di Azure per controllare l'uso dell'autenticazione di Azure Active Directory per istanze di SQL Server e Service Fabric. L'uso dell'autenticazione di Azure Active Directory consente una gestione semplificata delle autorizzazioni e una gestione centralizzata delle identità degli utenti di database e di altri servizi Microsoft.

- È consigliabile effettuare il provisioning di un amministratore di Azure Active Directory per SQL Server
- I cluster di Service Fabric deve usare solo Azure Active Directory per l'autenticazione client

Questo progetto assegna inoltre definizioni di Criteri di Azure per controllare gli account la cui verifica dovrebbe essere prioritaria, inclusi gli account deprecati e quelli esterni. Se necessario, è possibile bloccare l'accesso degli account (oppure rimuoverli), rimuovendo immediatamente i diritti di accesso alle risorse di Azure. Questo progetto assegna due definizioni di Criteri di Azure per controllare gli account deprecati che sarebbe preferibile rimuovere.

- Gli account deprecati devono essere rimossi dalla sottoscrizione
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione

Questo progetto assegna inoltre una definizione di Criteri di Azure che controlla le autorizzazioni per il file delle password delle VM Linux per avvisare se non sono impostate correttamente. Questa configurazione consente di adottare azioni correttive per assicurare che gli autenticatori non vengano compromessi.

- \[Anteprima\]: Controllare che le autorizzazioni file etc/passwd della VM Linux siano impostate su 0644

Questo progetto consente di imporre password complesse assegnando definizioni di Criteri di Azure che controllano le VM Windows per cui non sono applicati i requisiti minimi di complessità o di altro tipo per le password. Identificando le VM in violazione dei criteri di complessità delle password, è possibile adottare azioni correttive per assicurarsi che le password di tutti gli account utente delle VM siano conformi ai criteri.

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

Questo progetto consente inoltre di controllare l'accesso alle risorse di Azure assegnando definizioni di Criteri di Azure. Questi criteri controllano l'uso dei tipi di risorse e le configurazioni che potrebbero consentire un accesso più permissivo alle risorse. Identificando le risorse in violazione di questi criteri, è possibile adottare azioni correttive per restringere l'accesso alla risorse di Azure consentendolo solo agli utenti autorizzati.

- \[Anteprima\]: Distribuisci i requisiti per controllare le macchine virtuali Linux in cui sono presenti account senza password
- \[Anteprima\]: Distribuisci requisiti per controllare le macchine virtuali Linux che consentono connessioni remote da account senza password
- \[Anteprima\]: Controlla le macchine virtuali Linux in cui sono presenti account senza password
- \[Anteprima\]: Controlla le macchine virtuali Linux che non consentono connessioni remote da account senza password
- È consigliabile eseguire la migrazione degli account di archiviazione alle nuove risorse di Azure Resource Manager
- È consigliabile eseguire la migrazione delle macchine virtuali alle nuove risorse di Azure Resource Manager
- Controlla macchine virtuali che non usano dischi gestiti

## <a name="11-external-interface-protection"></a>11 Protezione dell'interfaccia esterna

Invece di usare più di 25 criteri per la gestione appropriata e sicura degli utenti, questo progetto consente di proteggere le interfacce del servizio da accessi non autorizzati assegnando una definizione di [Criteri di Azure](../../../policy/overview.md) che monitora gli account di archiviazione senza restrizioni. Gli account di archiviazione con accesso senza restrizioni possono consentire l'accesso non autorizzato alle informazioni contenute nel sistema informativo. Questo progetto assegna inoltre un criterio che abilita i controlli delle applicazioni adattivi nelle macchine virtuali.

- Controlla l'accesso di rete senza restrizioni agli account di archiviazione
- I controlli applicazioni adattivi devono essere abilitati nelle macchine virtuali

## <a name="12-secure-service-administration"></a>12 Amministrazione sicura dei servizi

Azure implementa il controllo degli accessi in base al ruolo per consentire di gestire chi ha accesso alle risorse di Azure. Usando il portale di Azure, è possibile verificare chi ha accesso alle risorse di Azure e le relative autorizzazioni. Questo progetto consente di restringere e controllare i diritti di accesso con privilegi assegnando cinque definizioni di [Criteri di Azure](../../../policy/overview.md) per controllare gli account esterni con autorizzazioni di proprietario e/o di scrittura e gli account con autorizzazioni di proprietario e/o di scrittura per cui non è abilitata l'autenticazione a più fattori.

Ai sistemi usati per l'amministrazione di un servizio cloud viene concesso l'accesso al servizio con privilegi elevati. Un'eventuale violazione di questi sistemi avrebbe quindi conseguenze piuttosto gravi, come la possibilità di aggirare i controlli di sicurezza e rubare o modificare grandi volumi di dati. I metodi usati dagli amministratori dei provider di servizi per gestire il servizio operativo dovrebbero essere progettati per mitigare l'eventuale rischio di sfruttamento che potrebbe compromettere la sicurezza del servizio. Se questo principio non viene implementato, un utente malintenzionato potrebbe avere i mezzi per ignorare i controlli di sicurezza e rubare o manipolare grandi volumi di dati.

- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione
- L'autenticazione MFA deve essere abilitata per gli account con autorizzazioni di scrittura per la sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione

Questo progetto assegna definizioni di Criteri di Azure per controllare l'uso dell'autenticazione di Azure Active Directory per istanze di SQL Server e Service Fabric. L'uso dell'autenticazione di Azure Active Directory consente una gestione semplificata delle autorizzazioni e una gestione centralizzata delle identità degli utenti di database e di altri servizi Microsoft.

- È consigliabile effettuare il provisioning di un amministratore di Azure Active Directory per SQL Server
- I cluster di Service Fabric deve usare solo Azure Active Directory per l'autenticazione client

Questo progetto assegna inoltre definizioni di Criteri di Azure per controllare gli account la cui verifica dovrebbe essere prioritaria, inclusi gli account deprecati e quelli esterni con autorizzazioni elevate. Se necessario, è possibile bloccare l'accesso degli account (oppure rimuoverli), rimuovendo immediatamente i diritti di accesso alle risorse di Azure. Questo progetto assegna due definizioni di Criteri di Azure per controllare gli account deprecati che sarebbe preferibile rimuovere.

- Gli account deprecati devono essere rimossi dalla sottoscrizione
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione

Questo progetto assegna inoltre una definizione di Criteri di Azure che controlla le autorizzazioni per il file delle password delle VM Linux per avvisare se non sono impostate correttamente. Questa configurazione consente di adottare azioni correttive per assicurare che gli autenticatori non vengano compromessi.

- \[Anteprima\]: Controllare che le autorizzazioni file etc/passwd della VM Linux siano impostate su 0644

## <a name="13-audit-information-for-users"></a>13 Informazioni di controllo per gli utenti

Questo progetto consente di assicurarsi che gli eventi di sistema vengano registrati assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che controllano le impostazioni dei log nelle risorse di Azure. Un criterio assegnato controlla inoltre se le macchine virtuali non inviano i log a un'area di lavoro di analisi dei log specificata.

- È consigliabile abilitare il controllo nelle impostazioni di Sicurezza dei dati avanzata in SQL Server
- Audit diagnostic setting (Controllare le impostazioni di diagnostica)
- Controlla le impostazioni di controllo a livello del server SQL
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Linux
- \[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Windows
- Distribuisci Network Watcher quando vengono create reti virtuali

## <a name="next-steps"></a>Passaggi successivi

Dopo aver esaminato il mapping dei controlli dei progetti UK OFFICIAL e UK NHS, vedere gli articoli seguenti per informazioni generali e su come distribuire questo esempio:

> [!div class="nextstepaction"]
> [Progetti UK OFFICIAL e UK NHS - Panoramica](./index.md)
> [Progetti UK OFFICIAL e UK NHS - Procedura di distribuzione](./deploy.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).