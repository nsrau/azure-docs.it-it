---
title: Esempio di progetto PCI-DSS v 3.2.1-mapping dei controlli
description: Mapping di controllo dell'esempio di progetto della carta di pagamento Industry Data Security Standard v 3.2.1 a criteri di Azure e RBAC.
ms.date: 06/24/2019
ms.topic: conceptual
ms.openlocfilehash: d3e72f923ea3d752d829731d1f741bda090ae9fd
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037274"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>Mapping di controllo dell'esempio di progetto PCI-DSS v 3.2.1

L'articolo seguente illustra in dettaglio come il progetto di esempio di Azure Blueprints PCI-DSS v 3.2.1 Blueprint esegue il mapping ai controlli PCI-DSS v 3.2.1. Per ulteriori informazioni sui controlli, vedere la pagina relativa a [PCI-DSS v 3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf).

I mapping seguenti sono ai controlli **PCI-DSS v 3.2.1:2018** . Usare la barra di spostamento a destra per passare direttamente a uno specifico mapping. Molti controlli mappati vengono implementati con un'iniziativa di [Criteri di Azure](../../../policy/overview.md). Per esaminare l'iniziativa completa, aprire **Criteri** nel portale di Azure e selezionare la pagina **Definizioni**. Individuare e selezionare i **controlli\[Preview\] audit PCI v 3.2.1:2018 e distribuire estensioni VM specifiche per supportare i requisiti di controllo** predefiniti Initiative.

> [!IMPORTANT]
> Ogni controllo tra quelli riportati di seguito è associato a una o più definizioni di [Criteri di Azure](../../../policy/overview.md). Questi criteri possono aiutare a [valutare la conformità](../../../policy/how-to/get-compliance-data.md) con il controllo. In molti casi tuttavia non si tratta di una corrispondenza 1:1 o completa tra un controllo e uno o più criteri. Di per sé, **Conforme** in Criteri di Azure si riferisce solo ai criteri stessi e non garantisce che l'utente sia completamente conforme a tutti i requisiti di un controllo. Inoltre, in questo momento lo standard di conformità include controlli che non vengono gestiti da alcuna definizione di Criteri di Azure. La conformità in Criteri di Azure è quindi solo una visualizzazione parziale dello stato di conformità generale. Le associazioni tra i controlli e le definizioni di Criteri di Azure per questo esempio di progetto di conformità possono cambiare nel tempo. Per visualizzare la cronologia delle modifiche, vedere la [cronologia dei commit di GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/pci-dss-3.2.1/control-mapping.md).

## <a name="132-and-134-boundary-protection"></a>per la protezione del limite 1.3.2 e 1.3.4

Questo progetto consente di gestire e controllare le reti assegnando definizioni di [criteri di Azure](../../../policy/overview.md) che monitorano i gruppi di sicurezza di rete con regole permissive. Le regole troppo permissive potrebbero consentire l'accesso non autorizzato alla rete e dovranno essere riviste. Questo progetto assegna una definizione di criteri di Azure che monitora gli endpoint, le applicazioni e gli account di archiviazione non protetti. Gli endpoint e le applicazioni non protetti da un firewall e gli account di archiviazione con accesso illimitato possono consentire l'accesso non autorizzato alle informazioni contenute nel sistema informativo.

- Controlla l'accesso di rete senza restrizioni agli account di archiviazione
- L'accesso tramite endpoint con connessione Internet deve essere limitato

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4. a, 4,1, 4.1. g, 4.1. h e 6.5.3 Cryptography Protection

Questo progetto consente di applicare i criteri con l'uso dei controlli di crittografia assegnando definizioni di [criteri di Azure](../../../policy/overview.md) che applicano controlli di crittografia specifici e controllano l'uso di impostazioni crittografiche vulnerabili. Identificando le risorse di Azure le cui configurazioni di crittografia potrebbero non essere ottimali, è possibile adottare azioni correttive per assicurarsi che le risorse siano configurate in conformità ai criteri di sicurezza delle informazioni. In particolare, i criteri assegnati da questo progetto richiedono la crittografia dei dati trasparente nei database SQL; controllare la crittografia mancante per gli account di archiviazione e le variabili dell'account di automazione. Esistono anche criteri che indirizzano le connessioni di controllo non sicure ad account di archiviazione, app per le funzioni, WebApp, app per le API e cache Redis e controllano la comunicazione Service Fabric non crittografata.

- L'app per le funzioni deve essere accessibile solo tramite HTTPS
- L'applicazione Web deve essere accessibile solo tramite HTTPS
- L'app per le API deve essere accessibile solo tramite HTTPS
- Transparent Data Encryption deve essere abilitata nei database SQL
- La crittografia del disco deve essere applicata nelle macchine virtuali
- Le variabili dell'account di automazione devono essere crittografate
- Devono essere abilitate solo connessioni sicure alla Cache Redis
- Il trasferimento sicuro negli account di archiviazione deve essere abilitato
- Per i cluster Service Fabric la proprietà ClusterProtectionLevel deve essere impostata su EncryptAndSign
- Transparent Data Encryption deve essere abilitata nei database SQL
- Distribuisci Transparent Data Encryption nel database SQL

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5,1, 6,2, 6,6 e 11.2.1 di analisi delle vulnerabilità e aggiornamenti del sistema

Questo progetto consente di gestire le vulnerabilità del sistema informativo assegnando definizioni di [criteri di Azure](../../../policy/overview.md) che monitorano gli aggiornamenti del sistema mancanti, le vulnerabilità del sistema operativo, le vulnerabilità SQL e le vulnerabilità delle macchine virtuali nel centro sicurezza di Azure. Centro sicurezza di Azure fornisce funzionalità di report che consentono di ricevere informazioni dettagliate in tempo reale sullo stato di sicurezza delle risorse di Azure distribuite.

- Monitora server senza Endpoint Protection nel Centro sicurezza di Azure
- Distribuisci estensione IaaSAntimalware Microsoft predefinita per Windows Server
- Distribuzione del rilevamento delle minacce nei server SQL
- Gli aggiornamenti di sistema devono essere installati nelle macchine
- Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte
- Le vulnerabilità dei database SQL devono essere risolte
- Le vulnerabilità devono essere risolte tramite una soluzione di valutazione della vulnerabilità

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2 e 7.1.3 separazione dei compiti

La presenza di un solo proprietario di sottoscrizioni di Azure non consente la ridondanza amministrativa. Al contrario, la presenza di troppi proprietari di sottoscrizioni di Azure può aumentare la probabilità che si verifichi una violazione tramite un account di proprietario compromesso. Questo progetto consente di mantenere un numero appropriato di proprietari di sottoscrizioni di Azure assegnando le definizioni di [criteri di Azure](../../../policy/overview.md) che controllano il numero di proprietari per le sottoscrizioni di Azure. La gestione delle autorizzazioni dei proprietari di sottoscrizioni consente di implementare la separazione appropriata dei compiti.

- Alla sottoscrizione deve essere assegnato più di un proprietario
- Per la sottoscrizione devono essere designati al massimo 3 proprietari 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3,2, 7.2.1, 8.3.1. a e 8.3.1. b gestione dei diritti di accesso con privilegi

Questo progetto consente di limitare e controllare i diritti di accesso con privilegi assegnando le definizioni di [criteri di Azure](../../../policy/overview.md) per controllare gli account esterni con autorizzazioni proprietario, scrittura e/o lettura e account dipendente con autorizzazioni di proprietario e/o scrittura per le quali non è abilitata l'autenticazione a più fattori. Azure implementa il controllo degli accessi in base al ruolo per gestire chi ha accesso alle risorse di Azure. Identificando dove vengono implementate regole personalizzate del controllo degli accessi in base al ruolo, è possibile verificare l'esigenza e la corretta implementazione, perché tali regole sono soggette a errore. Questo progetto assegna anche le definizioni di [criteri di Azure](../../../policy/overview.md) per controllare l'uso dell'autenticazione Azure Active Directory per SQL Server. L'uso dell'autenticazione Azure Active Directory semplifica la gestione delle autorizzazioni e centralizza la gestione delle identità degli utenti del database e di altri Microsoft  
Servizi.
 
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di lettura devono essere rimossi dalla sottoscrizione
- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione
- L'autenticazione a più fattori deve essere abilitata per gli account con autorizzazioni di scrittura sulla sottoscrizione
- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di lettura per la sottoscrizione
- È consigliabile effettuare il provisioning di un amministratore di Azure Active Directory per SQL Server
- Controlla l'uso di ruoli di controllo degli accessi in base al ruolo personalizzati

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 e 8.1.5 con privilegi minimi e revisione dei diritti di accesso degli utenti

Azure implementa il controllo degli accessi in base al ruolo (RBAC) per semplificare la gestione degli utenti che hanno accesso alle risorse in Azure. Usando il portale di Azure, è possibile verificare chi ha accesso alle risorse di Azure e le relative autorizzazioni. Questo progetto assegna le definizioni di [criteri di Azure](../../../policy/overview.md) agli account di controllo che devono avere priorità per la revisione, inclusi gli account ammortizzati e gli account esterni con autorizzazioni elevate.

- Gli account deprecati devono essere rimossi dalla sottoscrizione
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di lettura devono essere rimossi dalla sottoscrizione

## <a name="813-removal-or-adjustment-of-access-rights"></a>rimozione o modifica 8.1.3 dei diritti di accesso

Azure implementa il controllo degli accessi in base al ruolo (RBAC) per semplificare la gestione degli utenti che hanno accesso alle risorse in Azure. Utilizzando Azure Active Directory e RBAC, è possibile aggiornare i ruoli utente in modo da riflettere le modifiche organizzative. Se necessario, è possibile bloccare l'accesso degli account (oppure rimuoverli), rimuovendo immediatamente i diritti di accesso alle risorse di Azure. Questo progetto assegna le definizioni di [criteri di Azure](../../../policy/overview.md) per controllare l'account ammortizzato da considerare per la rimozione.

- Gli account deprecati devono essere rimossi dalla sottoscrizione
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3. a, b, 8.2.4. a, b e 8.2.5 autenticazione basata su password

Questo progetto consente di applicare password complesse assegnando definizioni di [criteri di Azure](../../../policy/overview.md) che controllano le macchine virtuali Windows che non applicano la forza minima e altri requisiti di password. Identificando le VM in violazione dei criteri di complessità delle password, è possibile adottare azioni correttive per assicurarsi che le password di tutti gli account utente delle VM siano conformi ai criteri.

- \[Preview\]: controlla le VM Windows che non hanno una validità massima della password di 70 giorni
- \[Preview\]: distribuire i requisiti per controllare le VM Windows che non hanno una validità massima della password di 70 giorni
- \[Preview\]: controlla le VM Windows che non limitano la lunghezza minima della password a 14 caratteri
- \[Preview\]: distribuire i requisiti per controllare le macchine virtuali Windows che non limitano la lunghezza minima della password a 14 caratteri
- \[Preview\]: controllare le VM Windows che consentono di riutilizzare le 24 password precedenti
- \[Preview\]: distribuire i requisiti per controllare le VM Windows che consentono di riutilizzare le 24 password precedenti

## <a name="103-and-1054-audit-generation"></a>Generazione controllo 10,3 e 10.5.4

Questo progetto consente di assicurarsi che gli eventi di sistema vengano registrati assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che controllano le impostazioni dei log nelle risorse di Azure.
I log di diagnostica forniscono informazioni dettagliate sulle operazioni eseguite nelle risorse di Azure. I log di Azure si basano sulla sincronizzazione degli orologi interni per creare un record correlato al tempo degli eventi nelle risorse.

- È consigliabile abilitare il controllo nelle impostazioni di Sicurezza dei dati avanzata in SQL Server
- Audit diagnostic setting (Controllare le impostazioni di diagnostica)
- Controlla le impostazioni di controllo a livello del server SQL
- Distribuisci il controllo nei server SQL
- È necessario eseguire la migrazione degli account di archiviazione alle nuove risorse Azure Resource Manager
- È necessario eseguire la migrazione delle macchine virtuali a nuove risorse Azure Resource Manager

## <a name="1236-and-1237-information-security"></a>Sicurezza delle informazioni 12.3.6 e 12.3.7

Questo progetto consente di gestire e controllare la rete assegnando le definizioni di [criteri di Azure](../../../policy/overview.md) che controllano i percorsi di rete accettabili e i prodotti aziendali approvati consentiti per l'ambiente. Sono personalizzabili da ogni azienda tramite i parametri dei criteri all'interno di ognuno di questi criteri.

- Percorsi consentiti
- Percorsi consentiti per i gruppi di risorse

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato esaminato il mapping dei controlli del modello PCI-DSS v 3.2.1, vedere gli articoli seguenti per informazioni sulla panoramica e su come distribuire questo esempio:

> [!div class="nextstepaction"]
> [PCI-DSS v 3.2.1 Blueprint-panoramica](./index.md)
> [PCI-DSS v 3.2.1 Blueprint-procedura di distribuzione](./deploy.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).