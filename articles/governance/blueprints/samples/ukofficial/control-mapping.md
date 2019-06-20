---
title: Mapping di controllo di esempio - ufficiale nel Regno Unito blueprint-
description: Mapping di controllo dell'esempio di progetto ufficiale nel Regno Unito.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/13/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 2eb6e62bc891a147a89107f5e3de7c2b605bbd09
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276977"
---
# <a name="control-mapping-of-the-uk-official-blueprint-sample"></a>Mapping di controllo dell'esempio di progetto ufficiale nel Regno Unito

L'articolo seguente illustra in dettaglio come nell'esempio di progetto ufficiale nel Regno Unito esegue il mapping ai controlli ufficiale nel Regno Unito.
Per altre informazioni sui controlli, vedere [ufficiale nel Regno Unito](https://www.gov.uk/government/publications/government-security-classifications).

I mapping seguenti sono per la **ufficiale nel Regno Unito** controlli. Usare la barra di spostamento a destra per passare direttamente a uno specifico mapping. Molti controlli mappati vengono implementati con un'iniziativa di [Criteri di Azure](../../../policy/overview.md). Per esaminare l'iniziativa completa, aprire **Criteri** nel portale di Azure e selezionare la pagina **Definizioni**. Quindi, individuare e selezionare il  **[anteprima] Audit ufficiale nel Regno Unito consente di controllare e distribuire estensioni di macchina virtuale specifiche per supportare i requisiti di controllo** iniziativa di criteri predefinite.

## <a name="1-data-in-transit-protection"></a>1-i dati nella protezione di transito

Questo progetto consente di assicurarsi che il trasferimento di informazioni con i servizi di Azure sia sicuro assegnando due definizioni di [Criteri di Azure](../../../policy/overview.md) per controllare le connessioni non sicure ad account di archiviazione e cache Redis.

- Devono essere abilitate solo connessioni protette per la Cache Redis
- Trasferimento sicuro per gli account di archiviazione deve essere abilitato

## <a name="23-data-at-rest-protection"></a>2.3 protezione dei dati inattivi

Questo progetto consente di applicare i criteri all'uso di controlli descritta assegnando 11 [criteri di Azure](../../../policy/overview.md) usano definizioni che applicano controlli specifici descritta e controllo delle impostazioni di crittografia debole.
Identificando le risorse di Azure le cui configurazioni di crittografia potrebbero non essere ottimali, è possibile adottare azioni correttive per assicurarsi che le risorse siano configurate in conformità ai criteri di sicurezza delle informazioni. In particolare, i criteri assegnati da questo progetto di richiedono la crittografia per gli account di archiviazione data lake; Richiedi crittografia trasparente dei dati nel database SQL. controllo della crittografia mancante in account di archiviazione, database SQL, dischi delle macchine virtuali e le variabili di account di automazione; Controllare le connessioni non sicure per gli account di archiviazione e Cache Redis. controllo della crittografia di password vulnerabili macchina virtuale; controlli e comunicazione non crittografata di Service Fabric.

- Monitorare i database SQL non crittografati nel Centro sicurezza di Azure
- Da applicare crittografia dischi di macchine virtuali
- Le variabili di account di automazione devono essere crittografate
- Trasferimento sicuro per gli account di archiviazione deve essere abilitato
- Trasferimento sicuro per gli account di archiviazione deve essere abilitato
- Cluster di Service Fabric devono avere la proprietà ClusterProtectionLevel impostato su EncryptAndSign
- Abilitare Transparent Data Encryption nei database SQL
- Distribuire la crittografia trasparente dei dati di database SQL
- Richiedi crittografia sugli account Data Lake Store
- Località consentite (è stato codificato per "Regno Unito MERIDIONALE" e "Regno Unito occidentale")
- Percorsi per i gruppi di risorse è consentita (è stato codificato per "Regno Unito MERIDIONALE" e "Regno Unito occidentale")

## <a name="52-vulnerability-management"></a>5.2 gestione delle vulnerabilità

Questo progetto consente di gestire le vulnerabilità del sistema di informazioni tramite l'assegnazione di cinque [criteri di Azure](../../../policy/overview.md) definizioni che consentono di monitorare senza endpoint protection, aggiornamenti di sistema, operano vulnerabilità del sistema, SQL mancanti le vulnerabilità e le vulnerabilità di macchina virtuale. Queste informazioni dettagliate forniscono dati in tempo reale sullo stato di sicurezza delle risorse distribuite e consentono di assegnare priorità alle azioni correttive.

- Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure
- Gli aggiornamenti del sistema devono essere installati nei computer
- Le vulnerabilità nella configurazione di sicurezza nei computer che devono essere corretti.
- Le vulnerabilità nei database SQL devono essere corretti.
- Le vulnerabilità devono essere corretti da una soluzione di valutazione della vulnerabilità

## <a name="53-protective-monitoring"></a>5.3 monitoraggio protettivo

Questo progetto consente di proteggere le risorse di sistema di informazioni tramite l'assegnazione di un [criteri di Azure](../../../policy/overview.md) illimitato di definizione che consente di monitorare gli account di archiviazione. Questo progetto assegna inoltre una definizione di criteri di Azure che monitora l'attività di elenco elementi consentiti.

- Controlla l'accesso di rete senza restrizioni agli account di archiviazione
- Controlli applicazione adattivi deve essere abilitati in macchine virtuali
- Distribuire il rilevamento delle minacce sui server SQL
- Distribuire l'estensione predefinita IaaS di Microsoft Anti-malware per Windows Server

## <a name="9-secure-user-management--10-identity-and-authentication"></a>Gestione degli utenti di proteggere 9 / 10 Authentication and Identity

Azure implementa l'accesso basato sui ruoli controllo degli accessi a consente di che gestire chi può accedere alle risorse in Azure. Usando il portale di Azure, è possibile verificare chi ha accesso alle risorse di Azure e le relative autorizzazioni. Questo progetto consente di limitare e controllare i diritti di accesso con privilegi tramite l'assegnazione di sei [criteri di Azure](../../../policy/overview.md) definizioni per controllare gli account esterni con proprietario e/o lettura/scrittura con il proprietario, account e autorizzazioni di lettura e/o scrittura autorizzazioni che non è abilitata l'autenticazione a più fattori.

- È necessario abilitare MFA per gli account con autorizzazioni di proprietario della sottoscrizione
- Autenticazione a più fattori deve essere abilitato gli account con autorizzazioni di scrittura per la sottoscrizione
- È necessario abilitare MFA per gli account con autorizzazioni di lettura per la sottoscrizione
- Controllare gli account esterni con autorizzazioni di proprietario su una sottoscrizione
- Controllare gli account esterni con autorizzazioni di scrittura su una sottoscrizione
- Controllare gli account esterni con autorizzazioni di lettura su una sottoscrizione

Questo progetto assegna due definizioni di criteri di Azure per controllare l'uso dell'autenticazione di Azure Active Directory per SQL Server e Service Fabric. L'uso dell'autenticazione di Azure Active Directory consente una gestione semplificata delle autorizzazioni e una gestione centralizzata delle identità degli utenti di database e di altri servizi Microsoft.

- Per istanze di SQL Server deve essere effettuato il provisioning di un amministratore di Azure Active Directory
- Cluster di Service Fabric devono usare solo Azure Active Directory per l'autenticazione client

Questo progetto assegna inoltre cinque le definizioni dei criteri di Azure per controllare gli account che devono avere una priorità per la revisione, inclusi gli account ammortizzati e gli account esterni. Se necessario, è possibile bloccare l'accesso degli account (oppure rimuoverli), rimuovendo immediatamente i diritti di accesso alle risorse di Azure. Questo progetto assegna due definizioni di criteri di Azure all'account controllo ammortizzato da considerare per la rimozione.

- Devono essere rimossi gli account deprecati dalla sottoscrizione
- Gli account deprecati con autorizzazioni di proprietario devono essere rimosso dalla sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimosso dalla sottoscrizione
- È consigliabile rimuovere dalla sottoscrizione gli account esterni con autorizzazioni di scrittura

Questo progetto assegna inoltre una definizione di criteri di Azure che controlla una VM Linux le autorizzazioni di file di password per un avviso se si impostano in modo non corretto. Questa progettazione consente di adottare misure correttive per garantire gli autenticatori non vengano compromessi.

- [Anteprima]: Audit Linux VM /etc/passwd file permissions are set to 0644

Questo progetto consente di imporre password complessa mediante l'assegnazione di 10 definizioni di criteri di Azure che controlla macchine virtuali di Windows che non applicano intensità minima e altri requisiti di password. Identificando le VM in violazione dei criteri di complessità delle password, è possibile adottare azioni correttive per assicurarsi che le password di tutti gli account utente delle VM siano conformi ai criteri.

- [Anteprima]: Deploy requirements to audit Windows VMs that do not have the password complexity setting enabled
- [Anteprima]: Deploy requirements to audit Windows VMs that do not have a maximum password age of 70 days
- [Anteprima]: Deploy requirements to audit Windows VMs that do not have a minimum password age of 1 day
- [Anteprima]: Deploy requirements to audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Anteprima]: Deploy requirements to audit Windows VMs that allow re-use of the previous 24 passwords
- [Anteprima]: Audit Windows VMs that do not have the password complexity setting enabled
- [Anteprima]: Audit Windows VMs that do not have a maximum password age of 70 days
- [Anteprima]: Audit Windows VMs that do not have a minimum password age of 1 day
- [Anteprima]: Audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Anteprima]: Audit Windows VMs that allow re-use of the previous 24 passwords

In questo progetto anche consente di controllare l'accesso alle risorse di Azure tramite l'assegnazione di sette le definizioni dei criteri di Azure. Questi criteri controllano l'uso dei tipi di risorse e le configurazioni che potrebbero consentire un accesso più permissivo alle risorse. Identificando le risorse in violazione di questi criteri, è possibile adottare azioni correttive per restringere l'accesso alla risorse di Azure consentendolo solo agli utenti autorizzati.

- [Anteprima]: Deploy requirements to audit Linux VMs that have accounts without passwords
- [Anteprima]: Deploy requirements to audit Linux VMs that allow remote connections from accounts without passwords
- [Anteprima]: Audit Linux VMs that have accounts without passwords
- [Anteprima]: Audit Linux VMs that allow remote connections from accounts without passwords
- Gli account di archiviazione devono essere migrati a nuove risorse di Azure Resource Manager
- Le macchine virtuali devono essere migrate a nuove risorse di Azure Resource Manager
- Controlla macchine virtuali che non usano dischi gestiti

## <a name="11-external-interface-protection"></a>Protezione dell'interfaccia esterna 11

Diverso da usando i criteri di più di 25 per la gestione appropriata sicura degli utenti, questo progetto consente di proteggere interfacce del servizio dall'accesso non autorizzato tramite l'assegnazione di un [criteri di Azure](../../../policy/overview.md) definizione di monitoraggi illimitati account di archiviazione. Gli account di archiviazione con accesso senza restrizioni possono consentire l'accesso non autorizzato alle informazioni contenute all'interno del sistema informativo. Questo progetto assegna anche un criterio che consente ai controlli applicazione adattivi sulle macchine virtuali.

- Controlla l'accesso di rete senza restrizioni agli account di archiviazione
- Controlli applicazione adattivi deve essere abilitati in macchine virtuali

## <a name="12-secure-service-administration"></a>Amministrazione del servizio protetta 12

Azure implementa l'accesso basato sui ruoli controllo degli accessi a consente di che gestire chi può accedere alle risorse in Azure. Usando il portale di Azure, è possibile verificare chi ha accesso alle risorse di Azure e le relative autorizzazioni. Questo progetto consente di limitare e controllare i diritti di accesso con privilegi tramite l'assegnazione di cinque [criteri di Azure](../../../policy/overview.md) definizioni per controllare gli account esterni con proprietario e/o account e autorizzazioni di scrittura con il proprietario e/o le autorizzazioni di scrittura che non è abilitata l'autenticazione a più fattori.

Ai sistemi usati per l'amministrazione di un servizio cloud viene concesso l'accesso al servizio con privilegi elevati. Un'eventuale violazione di questi sistemi avrebbe quindi conseguenze piuttosto gravi, come la possibilità di aggirare i controlli di sicurezza e rubare o modificare grandi volumi di dati. I metodi utilizzati dagli amministratori del provider di servizi per gestire il servizio operational devono essere progettati per ridurre qualsiasi rischio di sfruttamento che potrebbe compromettere la sicurezza del servizio. Se non viene implementato questo principio, un utente malintenzionato abbia la possibilità di ignorare i controlli di sicurezza e rubare o modificare grandi volumi di dati.

- È necessario abilitare MFA per gli account con autorizzazioni di proprietario della sottoscrizione
- Autenticazione a più fattori deve essere abilitato gli account con autorizzazioni di scrittura per la sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimosso dalla sottoscrizione
- È consigliabile rimuovere dalla sottoscrizione gli account esterni con autorizzazioni di scrittura

Questo progetto assegna due definizioni di criteri di Azure per controllare l'uso dell'autenticazione di Azure Active Directory per SQL Server e Service Fabric. L'uso dell'autenticazione di Azure Active Directory consente una gestione semplificata delle autorizzazioni e una gestione centralizzata delle identità degli utenti di database e di altri servizi Microsoft.

- Per istanze di SQL Server deve essere effettuato il provisioning di un amministratore di Azure Active Directory
- Cluster di Service Fabric devono usare solo Azure Active Directory per l'autenticazione client

Questo progetto anche assegna quattro le definizioni dei criteri di Azure per controllare gli account che devono avere una priorità per la revisione, inclusi gli account ammortizzati e gli account esterni con autorizzazioni elevate. Se necessario, è possibile bloccare l'accesso degli account (oppure rimuoverli), rimuovendo immediatamente i diritti di accesso alle risorse di Azure. Questo progetto assegna due definizioni di criteri di Azure all'account controllo ammortizzato da considerare per la rimozione.

- Devono essere rimossi gli account deprecati dalla sottoscrizione
- Gli account deprecati con autorizzazioni di proprietario devono essere rimosso dalla sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimosso dalla sottoscrizione
- È consigliabile rimuovere dalla sottoscrizione gli account esterni con autorizzazioni di scrittura

Questo progetto assegna inoltre una definizione di criteri di Azure che controlla una VM Linux le autorizzazioni di file di password per un avviso se si impostano in modo non corretto. Questa progettazione consente di adottare misure correttive per garantire gli autenticatori non vengano compromessi.

- [Anteprima]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="13-audit-information-for-users"></a>Informazioni di controllo 13 per gli utenti

Questo progetto consente di verificare che gli eventi di sistema vengono registrati tramite l'assegnazione di 6 [criteri di Azure](../../../policy/overview.md) definizioni che controlla le impostazioni del log in risorse di Azure. Un criterio assegnato controlla inoltre se le macchine virtuali non inviano i log a un'area di lavoro di analisi dei log specificata.

- Monitorare i server SQL non controllati in Centro sicurezza di Azure
- Audit diagnostic setting (Controllare le impostazioni di diagnostica)
- Controlla le impostazioni di controllo a livello del server SQL
- [Anteprima]: Deploy Log Analytics Agent for Linux VMs
- [Anteprima]: Deploy Log Analytics Agent for Windows VMs
- Distribuisci network watcher quando vengono create le reti virtuali

## <a name="next-steps"></a>Passaggi successivi

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).
