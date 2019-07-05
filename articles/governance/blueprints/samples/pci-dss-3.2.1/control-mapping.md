---
title: Mapping di controllo di esempio - progetto v3.2.1 per PCI-DSS-
description: Mapping di controllo dell'esempio di progetto v3.2.1 Payment Card Industry Data Security Standard per RBAC e criteri di Azure.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 38b1cc6249da98e11167416c8e18d06de1645679
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540943"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>Mapping di controllo dell'esempio PCI-DSS v3.2.1 blueprint

L'articolo seguente illustra in dettaglio come nell'esempio di progetto Azure linee guida per PCI-DSS v3.2.1 esegue il mapping ai controlli v3.2.1 PCI-DSS. Per altre informazioni sui controlli, vedere [PCI-DSS v3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf).

I mapping seguenti sono per la **PCI-DSS v3.2.1:2018** controlli. Usare la barra di spostamento a destra per passare direttamente a uno specifico mapping. Molti controlli mappati vengono implementati con un'iniziativa di [Criteri di Azure](../../../policy/overview.md). Per esaminare l'iniziativa completa, aprire **Criteri** nel portale di Azure e selezionare la pagina **Definizioni**. Quindi, individuare e selezionare il  **[anteprima] v3.2.1:2018 PCI Audit consente di controllare e distribuire estensioni di macchina virtuale specifiche per supportare i requisiti di controllo** iniziativa di criteri predefinite.

## <a name="132-and-134-boundary-protection"></a>1.3.2 e 1.3.4 protezione dei limiti

Questo progetto consente di gestire e controllare le reti assegnando [criteri di Azure](../../../policy/overview.md) definizioni che consente di monitorare gruppi di sicurezza di rete con regole permissive. Le regole troppo permissive potrebbero consentire l'accesso non autorizzato alla rete e dovranno essere riviste. Questo progetto viene assegnato uno definizioni dei criteri di Azure che monitora gli endpoint non protetti, applicazioni e gli account di archiviazione. Gli endpoint e le applicazioni non protetti da un firewall e gli account di archiviazione con accesso illimitato possono consentire l'accesso non autorizzato alle informazioni contenute nel sistema informativo.

- Controlla l'accesso di rete senza restrizioni agli account di archiviazione
- Accesso tramite endpoint con connessione Internet deve essere limitato

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4.a, 4.1, 4.1.g, 4.1.h e 6.5.3 protezione crittografica

Questo progetto consente di applicare i criteri con l'uso di controlli descritta assegnando [criteri di Azure](../../../policy/overview.md) usano definizioni che applicano controlli specifici descritta e controllo delle impostazioni di crittografia debole. Identificando le risorse di Azure le cui configurazioni di crittografia potrebbero non essere ottimali, è possibile adottare azioni correttive per assicurarsi che le risorse siano configurate in conformità ai criteri di sicurezza delle informazioni. In particolare, i criteri assegnati da questo progetto richiedono transparent data encryption nei database SQL. controllo della crittografia mancante in account di archiviazione e le variabili di account di automazione. Esistono anche criteri quale indirizzo connessioni non sicure per gli account di archiviazione, App per le funzioni, App Web, App per le API e Redis Cache di controllo e controllo comunicazione non crittografata di Service Fabric.

- L'app per le funzioni deve essere accessibile solo tramite HTTPS
- L'applicazione Web deve essere accessibile solo tramite HTTPS
- App per le API devono essere accessibili solo tramite HTTPS
- Monitorare i database SQL non crittografati nel Centro sicurezza di Azure
- Da applicare crittografia dischi di macchine virtuali
- Le variabili di account di automazione devono essere crittografate
- Devono essere abilitate solo connessioni protette per la Cache Redis
- Trasferimento sicuro per gli account di archiviazione deve essere abilitato
- Cluster di Service Fabric devono avere la proprietà ClusterProtectionLevel impostato su EncryptAndSign
- Abilitare Transparent Data Encryption nei database SQL
- Distribuire la crittografia trasparente dei dati di database SQL

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1, 6.2, 6,6 e 11.2.1 analisi delle vulnerabilità e gli aggiornamenti del sistema

Questo progetto consente di gestire vulnerabilità del sistema informatico assegnando [criteri di Azure](../../../policy/overview.md) definizioni che consentono di monitorare gli aggiornamenti del sistema mancanti, vulnerabilità del sistema operativo, le vulnerabilità SQL e macchina virtuale vulnerabilità nel Centro sicurezza di Azure. Centro sicurezza di Azure fornisce funzionalità di report che consentono di ricevere informazioni dettagliate in tempo reale sullo stato di sicurezza delle risorse di Azure distribuite.

- Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure
- Distribuisci estensione IaaSAntimalware Microsoft predefinita per Windows Server
- Distribuire il rilevamento delle minacce sui server SQL
- Gli aggiornamenti del sistema devono essere installati nei computer
- Le vulnerabilità nella configurazione di sicurezza nei computer che devono essere corretti.
- Le vulnerabilità nei database SQL devono essere corretti.
- Le vulnerabilità devono essere corretti da una soluzione di valutazione della vulnerabilità

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.3 7.1.2 e versioni precedenti la separazione dei compiti

La presenza di un solo proprietario di sottoscrizioni di Azure non consente la ridondanza amministrativa. Al contrario, la presenza di troppi proprietari di sottoscrizioni di Azure può aumentare la probabilità che si verifichi una violazione tramite un account di proprietario compromesso. Questo progetto consente di gestire un numero appropriato di proprietari di sottoscrizioni Azure assegnando [criteri di Azure](../../../policy/overview.md) definizioni che controlla il numero di proprietari per le sottoscrizioni di Azure. La gestione delle autorizzazioni dei proprietari di sottoscrizioni consente di implementare la separazione appropriata dei compiti.

- Deve essere presente più di un proprietario assegnato alla sottoscrizione
- Un massimo di 3 proprietari deve essere designato per la sottoscrizione 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2, 7.2.1, 8.3.1.a e 8.3.1.b la gestione dei diritti di accesso con privilegi

Questo progetto consente di limitare e controllare i diritti di accesso con privilegi assegnando [criteri di Azure](../../../policy/overview.md) definizioni per controllare gli account esterni con il proprietario, scrivere e/o leggere le autorizzazioni e gli account dei dipendenti con proprietario e/o scrittura autorizzazioni che non sono abilitata l'autenticazione a più fattori. Azure implementa il controllo degli accessi in base al ruolo per gestire chi ha accesso alle risorse di Azure. Identificando dove vengono implementate regole personalizzate del controllo degli accessi in base al ruolo, è possibile verificare l'esigenza e la corretta implementazione, perché tali regole sono soggette a errore. In questo progetto anche assegna [criteri di Azure](../../../policy/overview.md) definizioni controllare usano di autenticazione di Azure Active Directory per i server SQL. Tramite Azure Active Directory authentication semplifica la gestione delle autorizzazioni e consente di centralizzare la gestione delle identità di utenti del database e altri Microsoft  
Servizi.
 
- Gli account esterni con autorizzazioni di proprietario devono essere rimosso dalla sottoscrizione
- È consigliabile rimuovere dalla sottoscrizione gli account esterni con autorizzazioni di scrittura
- Gli account esterni con autorizzazioni di lettura devono essere rimossi dalla sottoscrizione
- È necessario abilitare MFA per gli account con autorizzazioni di proprietario della sottoscrizione
- Autenticazione a più fattori deve essere abilitato gli account con autorizzazioni di scrittura per la sottoscrizione
- È necessario abilitare MFA per gli account con autorizzazioni di lettura per la sottoscrizione
- Per istanze di SQL Server deve essere effettuato il provisioning di un amministratore di Azure Active Directory
- Controlla l'uso di ruoli di controllo degli accessi in base al ruolo personalizzati

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 e 8.1.5 privilegio minimo e revisione dell'utente i diritti di accesso

Azure implementa l'accesso basato sui ruoli controllo degli accessi a consente di che gestire chi può accedere alle risorse in Azure. Usando il portale di Azure, è possibile verificare chi ha accesso alle risorse di Azure e le relative autorizzazioni. Questo progetto assegna [criteri di Azure](../../../policy/overview.md) definizioni per controllare gli account che devono avere una priorità per la revisione, inclusi gli account ammortizzati e gli account esterni con autorizzazioni elevate.

- Devono essere rimossi gli account deprecati dalla sottoscrizione
- Gli account deprecati con autorizzazioni di proprietario devono essere rimosso dalla sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimosso dalla sottoscrizione
- È consigliabile rimuovere dalla sottoscrizione gli account esterni con autorizzazioni di scrittura
- Gli account esterni con autorizzazioni di lettura devono essere rimossi dalla sottoscrizione

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 rimozione o rettifica dei diritti di accesso

Azure implementa il controllo di accesso basato sui ruoli (RBAC) che consentono di gestire chi ha accesso alle risorse in Azure. Usando Azure Active Directory e RBAC, è possibile aggiornare i ruoli utente per riflettere le modifiche dell'organizzazione. Se necessario, è possibile bloccare l'accesso degli account (oppure rimuoverli), rimuovendo immediatamente i diritti di accesso alle risorse di Azure. Questo progetto assegna [criteri di Azure](../../../policy/overview.md) definizioni a ammortizzato account che devono essere considerati per la rimozione di controllo.

- Devono essere rimossi gli account deprecati dalla sottoscrizione
- Gli account deprecati con autorizzazioni di proprietario devono essere rimosso dalla sottoscrizione

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3.a,b, 8.2.4.a,b e 8.2.5 l'autenticazione basata su Password

Questo progetto consente di imporre password complessa assegnando [criteri di Azure](../../../policy/overview.md) definizioni di controllo delle macchine virtuali Windows che non applicano intensità minima e altri requisiti di password. Identificando le VM in violazione dei criteri di complessità delle password, è possibile adottare azioni correttive per assicurarsi che le password di tutti gli account utente delle VM siano conformi ai criteri.

- [Anteprima]: Audit Windows VMs that do not have a maximum password age of 70 days
- [Anteprima]: Deploy requirements to audit Windows VMs that do not have a maximum password age of 70 days
- [Anteprima]: Audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Anteprima]: Deploy requirements to audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Anteprima]: Audit Windows VMs that allow re-use of the previous 24 passwords
- [Anteprima]: Deploy requirements to audit Windows VMs that allow re-use of the previous 24 passwords

## <a name="103-and-1054-audit-generation"></a>10.3 e 10.5.4 di generazione di controllo

Questo progetto consente di verificare che gli eventi di sistema vengono registrati tramite l'assegnazione [criteri di Azure](../../../policy/overview.md) definizioni che controlla le impostazioni del log in risorse di Azure.
I log di diagnostica forniscono informazioni dettagliate sulle operazioni eseguite nelle risorse di Azure. I log di Azure si basano sulla sincronizzazione degli orologi interni per creare un record correlato al tempo degli eventi nelle risorse.

- Monitorare i server SQL non controllati in Centro sicurezza di Azure
- Audit diagnostic setting (Controllare le impostazioni di diagnostica)
- Controlla le impostazioni di controllo a livello del server SQL
- Distribuire il controllo sui server SQL
- Gli account di archiviazione devono essere migrati a nuove risorse di Azure Resource Manager
- Le macchine virtuali devono essere migrate a nuove risorse di Azure Resource Manager

## <a name="1236-and-1237-information-security"></a>12.3.6 e 12.3.7 di sicurezza delle informazioni

Questo progetto consente di gestire e controllare la rete assegnando [criteri di Azure](../../../policy/overview.md) definizioni di controllare i percorsi di rete accettabili e i prodotti aziendali approvate consentito per l'ambiente. Questi sono personalizzabili per ogni azienda tramite i parametri dei criteri all'interno di ognuno di questi criteri.

- Percorsi consentiti
- Percorsi per i gruppi di risorse consentiti

## <a name="next-steps"></a>Passaggi successivi

Dopo aver esaminato il mapping di controllo del progetto v3.2.1 PCI-DSS, vedere gli articoli seguenti per apprendere la panoramica e la modalità di distribuzione in questo esempio:

> [!div class="nextstepaction"]
> [Progetto v3.2.1 per PCI-DSS - Panoramica](./index.md)
> [PCI-DSS v3.2.1 blueprint - distribuzione passaggi](./deploy.md)

## <a name="addition-articles-about-blueprints-and-how-to-use-them"></a>Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).
