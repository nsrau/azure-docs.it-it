---
title: Esempio di progetto Carico di lavoro dell'ambiente del servizio app di Azure/SQL ISO 27001 - Mapping dei controlli
description: Mapping dei controlli del progetto di esempio Carico di lavoro dell'ambiente del servizio app/database SQL ISO 27001 in Criteri di Azure e RBAC.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
ms.openlocfilehash: aa60305e6733b18aa563b77c19ca81bfc6eced57
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72297654"
---
# <a name="control-mapping-of-the-iso-27001-asesql-workload-blueprint-sample"></a>Mapping dei controlli del progetto di esempio Carico di lavoro dell'ambiente del servizio app/SQL ISO 27001

L'articolo seguente descrive il mapping dell'esempio di progetto Carico di lavoro ASE/SQL di ISO 27001 di Azure Blueprints ai controlli ISO 27001. Per altre informazioni sui controlli, vedere [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

I mapping seguenti fanno riferimento ai controlli **ISO 27001:2013**. Usare la barra di spostamento a destra per passare direttamente a uno specifico mapping. Molti controlli mappati vengono implementati con un'iniziativa di [Criteri di Azure](../../../policy/overview.md). Per esaminare l'iniziativa completa, aprire **Criteri** nel portale di Azure e selezionare la pagina **Definizioni**. Quindi, trovare e selezionare l'iniziativa dei criteri predefinita **\[Anteprima\]: Verifica controlli ISO 27001:2013 e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo**.

> [!IMPORTANT]
> Ogni controllo tra quelli riportati di seguito è associato a una o più definizioni di [Criteri di Azure](../../../policy/overview.md). Questi criteri possono aiutare a [valutare la conformità](../../../policy/how-to/get-compliance-data.md) con il controllo. In molti casi tuttavia non si tratta di una corrispondenza 1:1 o completa tra un controllo e uno o più criteri. Di per sé, **Conforme** in Criteri di Azure si riferisce solo ai criteri stessi e non garantisce che l'utente sia completamente conforme a tutti i requisiti di un controllo. Inoltre, in questo momento lo standard di conformità include controlli che non vengono gestiti da alcuna definizione di Criteri di Azure. La conformità in Criteri di Azure è quindi solo una visualizzazione parziale dello stato di conformità generale. Le associazioni tra i controlli e le definizioni di Criteri di Azure per questo esempio di progetto di conformità possono cambiare nel tempo. Per visualizzare la cronologia delle modifiche, vedere la [cronologia dei commit di GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001-ase-sql-workload/control-mapping.md).

## <a name="a612-segregation-of-duties"></a>A.6.1.2 Separazione dei compiti

La presenza di un solo proprietario di sottoscrizioni di Azure non consente la ridondanza amministrativa. Al contrario, la presenza di troppi proprietari di sottoscrizioni di Azure può aumentare la probabilità che si verifichi una violazione tramite un account di proprietario compromesso. Questo progetto consente di mantenere un numero appropriato di proprietari di sottoscrizioni di Azure assegnando due definizioni di [Criteri di Azure](../../../policy/overview.md) che controllano questo numero. La gestione delle autorizzazioni dei proprietari di sottoscrizioni consente di implementare la separazione appropriata dei compiti.

- \[Anteprima\]: Controllare il numero minimo di proprietari per sottoscrizione
- \[Anteprima\]: Controllare il numero massimo di proprietari per una sottoscrizione

## <a name="a821-classification-of-information"></a>A.8.2.1 Classificazione delle informazioni

Il [Servizio Valutazione della vulnerabilità di SQL](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) può aiutare a individuare i dati sensibili archiviati nei database e include suggerimenti per la loro classificazione. Questo progetto assegna una definizione dei [Criteri di Azure](../../../policy/overview.md) per controllare che le vulnerabilità identificate durante l'analisi della Valutazione della vulnerabilità di SQL vengano risolte.

- \[Anteprima\]: Monitorare i risultati della valutazione della vulnerabilità di SQL nel Centro sicurezza di Azure

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 Accesso a reti e servizi di rete

Azure implementa il [controllo degli accessi in base al ruolo](../../../../role-based-access-control/overview.md) per gestire chi ha accesso alle risorse di Azure. Questo progetto consente di controllare l'accesso alle risorse di Azure assegnando sette definizioni di [Criteri di Azure](../../../policy/overview.md). Questi criteri controllano l'uso dei tipi di risorse e le configurazioni che potrebbero consentire un accesso più permissivo alle risorse.
Identificando le risorse in violazione di questi criteri, è possibile adottare azioni correttive per restringere l'accesso alla risorse di Azure consentendolo solo agli utenti autorizzati.

- \[Anteprima\]: Distribuire l'estensione VM per controllare gli account della VM Linux senza password
- \[Anteprima\]: Distribuire l'estensione VM per controllare la VM Linux per consentire le connessioni remote dagli account senza password
- \[Anteprima\]: Account della VM Linux senza password
- \[Anteprima\]: Controllare la VM Linux per consentire le connessioni remote dagli account senza password
- Controlla l'uso degli account di archiviazione della versione classica
- Controlla l'uso delle macchine virtuali della versione classica
- Controlla macchine virtuali che non usano dischi gestiti

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 Gestione dei diritti di accesso con privilegi

Questo progetto consente di restringere e controllare i diritti di accesso con privilegi assegnando quattro definizioni di [Criteri di Azure](../../../policy/overview.md) per controllare gli account esterni con autorizzazioni di proprietario e/o di scrittura e gli account con autorizzazioni di proprietario e/o di scrittura per cui non è abilitata l'autenticazione a più fattori. Azure implementa il controllo degli accessi in base al ruolo per gestire chi ha accesso alle risorse di Azure. Questo progetto assegna tre definizioni di Criteri di Azure per controllare l'uso dell'autenticazione di Azure Active Directory per istanze di SQL Server e Service Fabric. L'uso dell'autenticazione di Azure Active Directory consente una gestione semplificata delle autorizzazioni e una gestione centralizzata delle identità degli utenti di database e di altri servizi Microsoft. Questo progetto assegna inoltre una definizione di Criteri di Azure per controllare l'uso di regole personalizzate del controllo degli accessi in base al ruolo. Identificando dove vengono implementate regole personalizzate del controllo degli accessi in base al ruolo, è possibile verificare l'esigenza e la corretta implementazione, perché tali regole sono soggette a errore.

- \[Anteprima\]: Controllare gli account con autorizzazioni di proprietario non abilitati per MFA in una sottoscrizione
- \[Anteprima\]: Controllare gli account con autorizzazioni di scrittura non abilitati per MFA in una sottoscrizione
- \[Anteprima\]: Controllare gli account esterni con autorizzazioni di proprietario su una sottoscrizione
- \[Anteprima\]: Controllare gli account esterni con autorizzazioni di scrittura su una sottoscrizione
- Controlla il provisioning di un amministratore di Azure Active Directory per SQL Server
- Controlla l'utilizzo di Azure Active Directory per l'autenticazione client in Service Fabric
- Controlla l'uso di ruoli di controllo degli accessi in base al ruolo personalizzati

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 Gestione delle informazioni di autenticazione degli utenti tramite segreto

Questo progetto assegna tre definizioni di [Criteri di Azure](../../../policy/overview.md) per controllare gli account per cui non è abilitata l'autenticazione a più fattori. L'autenticazione a più fattori consente di mantenere sicuri gli account anche se una parte delle informazioni di autenticazione viene compromessa. Monitorando gli account senza autenticazione a più fattori abilitata, è possibile identificare quelli che potrebbero venire compromessi con più probabilità. Questo progetto assegna anche due definizioni di Criteri di Azure che controllano le autorizzazioni del file delle password delle VM Linux per avvisare se non sono impostate correttamente. Questa configurazione consente di adottare azioni correttive per assicurare che gli autenticatori non vengano compromessi.

- \[Anteprima\]: Controllare gli account con autorizzazioni di proprietario non abilitati per MFA in una sottoscrizione
- \[Anteprima\]: Controllare gli account con autorizzazioni di lettura non abilitati per MFA in una sottoscrizione
- \[Anteprima\]: Controllare gli account con autorizzazioni di scrittura non abilitati per MFA in una sottoscrizione
- \[Anteprima\]: Distribuire l'estensione VM per controllare le autorizzazioni file passwd della VM Linux
- \[Anteprima\]: Controllare che le autorizzazioni file etc/passwd della VM Linux siano impostate su 0644

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 Revisione dei diritti di accesso utente

Azure implementa il [controllo degli accessi in base al ruolo](../../../../role-based-access-control/overview.md) per consentire di gestire chi ha accesso alle risorse di Azure. Usando il portale di Azure, è possibile verificare chi ha accesso alle risorse di Azure e le relative autorizzazioni. Questo progetto assegna quattro definizioni di [Criteri di Azure](../../../policy/overview.md) per controllare gli account la cui verifica dovrebbe essere prioritaria, inclusi gli account deprecati e quelli con autorizzazioni elevate.

- \[Anteprima\]: Controllare account deprecati in una sottoscrizione
- \[Anteprima\]: Controllare gli account deprecati con autorizzazioni di proprietario in una sottoscrizione
- \[Anteprima\]: Controllare gli account esterni con autorizzazioni di proprietario su una sottoscrizione
- \[Anteprima\]: Controllare gli account esterni con autorizzazioni di scrittura su una sottoscrizione

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 Rimozione o modifica dei diritti di accesso

Azure implementa il [controllo degli accessi in base al ruolo](../../../../role-based-access-control/overview.md) per consentire di gestire chi ha accesso alle risorse di Azure. Usando [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) e il controllo degli accessi in base al ruolo, è possibile aggiornare i ruoli degli utenti in base alle modifiche organizzative. Se necessario, è possibile bloccare l'accesso degli account (oppure rimuoverli), rimuovendo immediatamente i diritti di accesso alle risorse di Azure. Questo progetto assegna due definizioni di [Criteri di Azure](../../../policy/overview.md) per controllare gli account deprecati che sarebbe preferibile rimuovere.

- \[Anteprima\]: Controllare account deprecati in una sottoscrizione
- \[Anteprima\]: Controllare gli account deprecati con autorizzazioni di proprietario in una sottoscrizione

## <a name="a942-secure-log-on-procedures"></a>A.9.4.2 Procedure di accesso sicure

Questo progetto assegna tre definizioni di Criteri di Azure per controllare gli account per cui non è abilitata l'autenticazione a più fattori. Azure Multi-Factor Authentication offre ulteriore sicurezza richiedendo una seconda forma di autenticazione nonché un'autenticazione avanzata. Monitorando gli account senza autenticazione a più fattori abilitata, è possibile identificare quelli che potrebbero venire compromessi con più probabilità.

- \[Anteprima\]: Controllare gli account con autorizzazioni di proprietario non abilitati per MFA in una sottoscrizione
- \[Anteprima\]: Controllare gli account con autorizzazioni di lettura non abilitati per MFA in una sottoscrizione
- \[Anteprima\]: Controllare gli account con autorizzazioni di scrittura non abilitati per MFA in una sottoscrizione

## <a name="a943-password-management-system"></a>A.9.4.3 Sistema di gestione delle password

Questo progetto consente di imporre password complesse assegnando 10 definizioni di [Criteri di Azure](../../../policy/overview.md) che controllano le VM Windows per cui non è applicato il requisito minimo di complessità o altri requisiti delle password. Identificando le VM in violazione dei criteri di complessità delle password, è possibile adottare azioni correttive per assicurarsi che le password di tutti gli account utente delle VM siano conformi ai criteri.

- \[Anteprima\]: Distribuire l'estensione VM per controllare che la macchina virtuale Windows applichi i requisiti di complessità delle password
- \[Anteprima\]: Distribuire l'estensione VM per controllare che la validità massima della password della macchina virtuale Windows sia di 70 giorni
- \[Anteprima\]: Distribuire l'estensione VM per controllare che la validità minima della password della macchina virtuale Windows sia di 1 giorno
- \[Anteprima\]: Distribuire l'estensione VM per controllare che la password della macchina virtuale Windows contenga almeno 14 caratteri
- \[Anteprima\]: Distribuire l'estensione VM per controllare che la macchina virtuale Windows non autorizzi le 24 password precedenti
- \[Anteprima\]: Controllare che la macchina virtuale Windows applichi i requisiti di complessità delle password
- \[Anteprima\]: Controllare che la validità massima della password della macchina virtuale Windows sia di 70 giorni
- \[Anteprima\]: Controllare che la validità minima della password della macchina virtuale Windows sia di 1 giorno
- \[Anteprima\]: Controllare che la password della macchina virtuale Windows contenga almeno 14 caratteri
- \[Anteprima\]: Controllare che la macchina virtuale Windows non autorizzi le 24 password precedenti

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 Criteri sull'uso dei controlli crittografici

Questo progetto consente di applicare i criteri sull'uso dei controlli crittografici assegnando 13 definizioni di [Criteri di Azure](../../../policy/overview.md) che applicano specifici controlli crittografici e controllano l'uso di impostazioni di crittografia meno sicure.
Identificando le risorse di Azure le cui configurazioni di crittografia potrebbero non essere ottimali, è possibile adottare azioni correttive per assicurarsi che le risorse siano configurate in conformità ai criteri di sicurezza delle informazioni. In particolare, i criteri assegnati da questo progetto richiedono la crittografia per gli account di archiviazione BLOB e gli account Data Lake Storage; richiedono l'applicazione di Transparent Data Encryption per i database SQL; controllano se manca la crittografia in account di archiviazione, database SQL, dischi di macchine virtuali e variabili degli account di automazione; controllano le connessioni non sicure ad account di archiviazione, app per le funzioni, app Web, app per le API e cache Redis; controllano la crittografia poco sicura delle password delle macchine virtuali; infine, controllano le comunicazioni non crittografate con Service Fabric.

- \[Anteprima\]: Controllare l'accesso Solo HTTPS per un'app per le funzioni
- \[Anteprima\]: Controllare l'accesso Solo HTTPS per un'applicazione Web
- \[Anteprima\]: Controllare l'accesso Solo HTTPS per un'app per le API
- \[Anteprima\]: Controllare crittografia BLOB mancante per gli account di archiviazione
- \[Anteprima\]: Distribuire l'estensione VM per controllare che le macchine virtuali Windows non archivino le password usando la crittografia reversibile
- \[Anteprima\]: Controllare che le macchine virtuali Windows non archivino le password usando la crittografia reversibile
- \[Anteprima\]: Monitorare i dischi di macchine virtuali non crittografati nel Centro sicurezza di Azure
- Controlla l'abilitazione della crittografia delle variabili dell'account di Automazione
- Controlla l'abilitazione delle sole connessioni sicure alla cache Redis
- Controlla il trasferimento sicuro negli account di archiviazione
- Controlla l'impostazione della proprietà ClusterProtectionLevel su EncryptAndSign in Service Fabric
- Controllare lo stato di Transparent Data Encryption
- Transparent Data Encryption deve essere abilitata nei database SQL

## <a name="a1241-event-logging"></a>A.12.4.1 Registrazione di eventi

Questo progetto consente di assicurarsi che gli eventi di sistema vengano registrati assegnando sette definizioni di [Criteri di Azure](../../../policy/overview.md) che controllano le impostazioni dei log sulle risorse di Azure.
I log di diagnostica forniscono informazioni dettagliate sulle operazioni eseguite nelle risorse di Azure.

- \[Anteprima\]: Controlla la distribuzione di Dependency Agent - Immagine macchina virtuale (sistema operativo) non in elenco
- \[Anteprima\]: Controlla la distribuzione di Dependency Agent nei set di scalabilità di macchine virtuali - Immagine macchina virtuale (sistema operativo) non in elenco
- \[Anteprima\]: Controlla la distribuzione dell'agente di Log Analytics - Immagine macchina virtuale (sistema operativo) non in elenco
- \[Anteprima\]: Controlla la distribuzione dell'agente di Log Analytics nei set di scalabilità di macchine virtuali - Immagine macchina virtuale (sistema operativo) non in elenco
- Audit diagnostic setting (Controllare le impostazioni di diagnostica)
- Controlla le impostazioni di controllo a livello del server SQL
- È consigliabile abilitare il controllo nelle impostazioni di Sicurezza dei dati avanzata in SQL Server

## <a name="a1243-administrator-and-operator-logs"></a>A.12.4.3 Log amministratore e operatore

Questo progetto consente di assicurarsi che gli eventi di sistema vengano registrati assegnando sette definizioni di Criteri di Azure che controllano le impostazioni dei log sulle risorse di Azure. I log di diagnostica forniscono informazioni dettagliate sulle operazioni eseguite nelle risorse di Azure.

- \[Anteprima\]: Controlla la distribuzione di Dependency Agent - Immagine macchina virtuale (sistema operativo) non in elenco
- \[Anteprima\]: Controlla la distribuzione di Dependency Agent nei set di scalabilità di macchine virtuali - Immagine macchina virtuale (sistema operativo) non in elenco
- \[Anteprima\]: Controlla la distribuzione dell'agente di Log Analytics - Immagine macchina virtuale (sistema operativo) non in elenco
- \[Anteprima\]: Controlla la distribuzione dell'agente di Log Analytics nei set di scalabilità di macchine virtuali - Immagine macchina virtuale (sistema operativo) non in elenco
- Audit diagnostic setting (Controllare le impostazioni di diagnostica)
- Controlla le impostazioni di controllo a livello del server SQL
- È consigliabile abilitare il controllo nelle impostazioni di Sicurezza dei dati avanzata in SQL Server

## <a name="a1244-clock-synchronization"></a>A.12.4.4 Sincronizzazione dell'orologio

Questo progetto consente di assicurarsi che gli eventi di sistema vengano registrati assegnando sette definizioni di Criteri di Azure che controllano le impostazioni dei log sulle risorse di Azure. I log di Azure si basano sulla sincronizzazione degli orologi interni per creare un record correlato al tempo degli eventi nelle risorse.

- \[Anteprima\]: Controlla la distribuzione di Dependency Agent - Immagine macchina virtuale (sistema operativo) non in elenco
- \[Anteprima\]: Controlla la distribuzione di Dependency Agent nei set di scalabilità di macchine virtuali - Immagine macchina virtuale (sistema operativo) non in elenco
- \[Anteprima\]: Controlla la distribuzione dell'agente di Log Analytics - Immagine macchina virtuale (sistema operativo) non in elenco
- \[Anteprima\]: Controlla la distribuzione dell'agente di Log Analytics nei set di scalabilità di macchine virtuali - Immagine macchina virtuale (sistema operativo) non in elenco
- Audit diagnostic setting (Controllare le impostazioni di diagnostica)
- Controlla le impostazioni di controllo a livello del server SQL
- È consigliabile abilitare il controllo nelle impostazioni di Sicurezza dei dati avanzata in SQL Server

## <a name="a1251-installation-of-software-on-operational-systems"></a>A.12.5.1 Installazione del software nei sistemi operativi

Il controllo applicazione adattivo è una soluzione di Centro sicurezza di Azure che consente di controllare quali applicazioni possono essere eseguite nelle VM situate in Azure. Questo progetto assegna una definizione di Criteri di Azure che monitora le modifiche apportate al set di applicazioni consentite. Questa funzionalità consente di controllare l'installazione del software e delle applicazioni nelle macchine virtuali di Azure.

- \[Anteprima\]: Monitorare il possibile elenco elementi consentiti dell'app nel Centro sicurezza di Azure

## <a name="a1261-management-of-technical-vulnerabilities"></a>A.12.6.1 Gestione di vulnerabilità tecniche

Questo progetto consente di gestire le vulnerabilità dei sistemi informativi assegnando cinque definizioni di [Criteri di Azure](../../../policy/overview.md) che monitorano gli aggiornamenti di sistema mancanti, oltre alle vulnerabilità del sistema operativo, di SQL e delle macchine virtuali nel Centro sicurezza di Azure. Centro sicurezza di Azure fornisce funzionalità di report che consentono di ricevere informazioni dettagliate in tempo reale sullo stato di sicurezza delle risorse di Azure distribuite.

- \[Anteprima\]: Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure
- \[Anteprima\]: Monitorare gli aggiornamenti di sistema mancanti nel Centro sicurezza di Azure
- \[Anteprima\]: Monitorare le vulnerabilità del sistema operativo nel Centro sicurezza di Azure
- \[Anteprima\]: Monitorare i risultati della valutazione della vulnerabilità di SQL nel Centro sicurezza di Azure
- \[Anteprima\]: Monitorare le vulnerabilità delle macchine virtuali nel Centro sicurezza di Azure

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 Restrizioni sull'installazione di software

Il controllo applicazione adattivo è una soluzione di Centro sicurezza di Azure che consente di controllare quali applicazioni possono essere eseguite nelle VM situate in Azure. Questo progetto assegna una definizione di Criteri di Azure che monitora le modifiche apportate al set di applicazioni consentite. Le restrizioni sull'installazione di software consentono di ridurre la probabilità che vengano introdotte vulnerabilità software.

- \[Anteprima\]: Monitorare il possibile elenco elementi consentiti dell'app nel Centro sicurezza di Azure

## <a name="a1311-network-controls"></a>A.13.1.1 Controlli di rete

Questo progetto consente di gestire e controllare le reti assegnando una definizione di [Criteri di Azure](../../../policy/overview.md) che monitora i gruppi di sicurezza di rete con regole permissive. Le regole troppo permissive potrebbero consentire l'accesso non autorizzato alla rete e dovranno essere riviste. Questo progetto assegna anche tre definizioni di Criteri di Azure che monitorano endpoint, applicazioni e account di archiviazione non protetti. Gli endpoint e le applicazioni non protetti da un firewall e gli account di archiviazione con accesso illimitato possono consentire l'accesso non autorizzato alle informazioni contenute nel sistema informativo.

- \[Anteprima\]: Monitorare gli accessi di rete permissivi nel Centro sicurezza di Azure
- \[Anteprima\]: Monitorare gli endpoint di rete non protetti nel Centro sicurezza di Azure
- \[Anteprima\]: Monitorare le applicazioni Web non protette nel Centro sicurezza di Azure
- Controlla l'accesso di rete senza restrizioni agli account di archiviazione

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 Criteri e procedure di trasferimento delle informazioni

Questo progetto consente di assicurarsi che il trasferimento di informazioni con i servizi di Azure sia sicuro assegnando due definizioni di [Criteri di Azure](../../../policy/overview.md) per controllare le connessioni non sicure ad account di archiviazione e cache Redis.

- Controlla l'abilitazione delle sole connessioni sicure alla cache Redis
- Controlla il trasferimento sicuro negli account di archiviazione

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