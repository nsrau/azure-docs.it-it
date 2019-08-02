---
title: Backup e ripristino di emergenza in Azure Australia
description: Backup e ripristino di emergenza in Microsoft Azure per agenzie governative australiane in relazione all'ASD Essential 8
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 078918cbfeffb1ff5f3626b5add96bc6622a90a7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571523"
---
# <a name="backup-and-disaster-recovery-in-azure-australia"></a>Backup e ripristino di emergenza in Azure Australia

La presenza di piani di backup e ripristino di emergenza con l'infrastruttura di supporto sul posto è fondamentale per tutte le organizzazioni. La necessità di disporre di una soluzione di backup è evidenziata dalla sua inclusione in [Australian Cyber Security Center ' s Essential 8](https://acsc.gov.au/publications/protect/essential-eight-explained.htm).

Microsoft Azure offre due servizi che consentono la resilienza: Backup di Azure e Azure Site Recovery. Questi servizi consentono di proteggere i dati, sia in locale che nel cloud, per un'ampia gamma di scenari di progettazione. Backup di Azure e Azure Site Recovery usano entrambe una risorsa di archiviazione e gestione comune, ovvero l'insieme di credenziali di servizi di ripristino di Azure. Questo insieme di credenziali viene usato per gestire, monitorare e separare i dati di backup di Azure e Azure Site Recovery.

Questo articolo illustra in dettaglio gli elementi di progettazione principali per l'implementazione di backup di Azure e Azure Site Recovery in linea con i [controlli di ISM (Information Security Manual) della direzione di sicurezza dei segnali australiani](https://acsc.gov.au/infosec/ism/index.htm).

## <a name="azure-backup"></a>Backup di Azure

![Backup di Azure](media/backup-overview.png)

Backup di Azure è simile a una soluzione di backup locale tradizionale e offre la possibilità di eseguire il backup dei dati locali e ospitati in Azure. Backup di Azure può essere usato per eseguire il backup dei tipi di dati seguenti in Azure:

* File e cartelle
* Sistemi operativi Windows e Linux supportati ospitati in:
  * Hypervisor Hyper-V e VMWare
  * Hardware fisico
* Applicazioni Microsoft supportate

### <a name="azure-site-recovery"></a>Azure Site Recovery

![Azure Site Recovery](media/asr-overview.png)

Azure Site Recovery replica i carichi di lavoro costituiti da una singola macchina virtuale o da applicazioni multilivello. La replica è supportata dall'ambiente locale in Azure, tra aree di Azure o tra percorsi locali orchestrati da Azure Site Recovery. Le macchine virtuali locali possono essere replicate in Azure o in un hypervisor locale supportato. Una volta configurata, Azure Site Recovery orchestra la replica, il failover e il failback.

## <a name="key-design-considerations"></a>Considerazioni di progettazione principali

Quando si implementa una soluzione di backup o ripristino di emergenza, la soluzione proposta deve prendere in considerazione quanto segue:

* Ambito e volume dei dati da acquisire
* Per quanto tempo i dati verranno conservati
* Modalità di archiviazione e gestione sicure dei dati
* Le posizioni geografiche in cui sono archiviati i dati
* Routine di test del sistema routine

ISM fornisce indicazioni sulle considerazioni relative alla sicurezza da effettuare quando si progetta una soluzione. Microsoft Azure fornisce i mezzi per risolvere queste considerazioni sulla sicurezza.

### <a name="data-sovereignty"></a>Sovranità dei dati

Le organizzazioni devono garantire che la sovranità dei dati venga mantenuta quando si utilizzano i percorsi di archiviazione basati sul cloud. Criteri di Azure fornisce i mezzi per limitare le posizioni consentite in cui è possibile creare una risorsa di Azure. I criteri di Azure predefiniti "percorsi consentiti" vengono usati per garantire che tutte le risorse di Azure create nell'ambito di un criterio di Azure assegnato possano essere create solo nelle posizioni geografiche candidate.

Gli elementi criteri di Azure per la restrizione geografica per le risorse di Azure sono:

* allowedLocations
* allowedSingleLocation

Questi criteri consentono agli amministratori di Azure di limitare la creazione a un elenco di località candidate o anche a una singola posizione geografica.

### <a name="redundant-and-geographically-dispersed-storage"></a>Archiviazione ridondante e dislocata geograficamente

I dati archiviati nell'insieme di credenziali del servizio di ripristino di Azure vengono sempre archiviati in un archivio ridondante. Per impostazione predefinita, l'insieme di credenziali del servizio di ripristino usa l'archiviazione con ridondanza geografica (GRS) di Azure. I dati archiviati con GRS vengono replicati in altri Data Center di Azure nell' [area abbinata](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)dell'insieme di credenziali dei servizi di ripristino. I dati replicati vengono archiviati come di sola lettura ed è possibile renderli scrivibili solo se è presente un evento di failover di Azure. All'interno del Data Center di Azure, i dati vengono replicati tra domini di errore e domini di aggiornamento distinti per ridurre al minimo le probabilità di interruzione dell'hardware o di manutenzione. Il livello GRS garantisce almeno una disponibilità del 99,99999999999999% annuale.

L'insieme di credenziali di servizi di ripristino di Azure può essere configurato per l'uso dell'archiviazione con ridondanza locale (con ridondanza locale). CON ridondanza locale è un'opzione di archiviazione a basso costo con il compromesso della disponibilità ridotta. Questo modello di ridondanza utilizza la stessa replica tra domini di errore e domini di aggiornamento separati ma non viene replicata tra le aree geografiche. I dati che si trovano nell'archiviazione con ridondanza locale, anche se non sono resilienti come GRS, offrono ancora una disponibilità di almeno il 99,999999999% ogni anno.

A differenza delle tecnologie di archiviazione fuori sede tradizionali, come i supporti su nastro, le copie aggiuntive dei dati vengono create automaticamente e non richiedono un sovraccarico amministrativo aggiuntivo.

### <a name="restricted-access-and-activity-monitoring"></a>Accesso limitato e monitoraggio delle attività

I dati di backup devono essere protetti da danneggiamenti, modifiche e eliminazioni non approvate. Backup di Azure e Azure Site Recovery usano l'infrastruttura di gestione di Azure comune. Questa infrastruttura fornisce controllo dettagliato, registrazione e controllo degli accessi in base al ruolo (RBAC) alle risorse che si trovano all'interno di Azure. L'accesso ai dati di backup può essere limitato al personale amministrativo selezionato e tutte le azioni che interessano i dati di backup possono essere registrate e controllate.

Backup di Azure e Azure Site Recovery dispongono di funzionalità di registrazione e creazione di report predefinite. Tutti i problemi che si verificano durante il backup o la replica vengono segnalati agli amministratori tramite l'infrastruttura di gestione di Azure.

L'insieme di credenziali di servizi di ripristino di Azure include anche le misure di sicurezza dei dati aggiuntive seguenti:

* I dati di backup vengono conservati per 14 giorni dopo l'esecuzione di un'operazione di eliminazione
* Avvisi e notifiche per operazioni critiche, ad esempio "Interrompi backup con Elimina dati"
* Requisiti del PIN di sicurezza per le operazioni critiche
* Sono disponibili i controlli del periodo di mantenimento dati minimo

Questi controlli del periodo di mantenimento dati minimo includono:

* Per la conservazione giornaliera, un minimo di sette giorni di conservazione
* Per la conservazione settimanale, almeno quattro settimane di conservazione
* Per la conservazione mensile, un minimo di tre mesi di conservazione
* Per la conservazione annuale, almeno un anno di conservazione

Tutti i dati di backup archiviati in Azure vengono crittografati a riposo usando la crittografia del servizio di archiviazione di Azure (SSE). Questa opzione è abilitata per tutti gli account di archiviazione nuovi ed esistenti per impostazione predefinita e non può essere disabilitata. I dati crittografati vengono decrittografati automaticamente durante il recupero. Per impostazione predefinita, i dati crittografati con SSE vengono crittografati usando una chiave fornita da e gestita da Microsoft. Le organizzazioni possono scegliere di fornire e gestire la propria chiave di crittografia per l'uso con SSE. In questo modo viene fornito un ulteriore livello di sicurezza facoltativo per i dati crittografati. Questa chiave può essere archiviata dal cliente locale o in modo sicuro all'interno dell'insieme di credenziali delle chiavi di Azure.

### <a name="secure-data-transport"></a>Trasporto dati protetto

Dati di backup di Azure crittografati in transito con AES 256. Questi dati vengono protetti tramite l'utilizzo di una passphrase creata dal personale amministrativo quando il backup viene configurato per la prima volta. Microsoft non ha accesso a questa passphrase, il che significa che il cliente deve assicurarsi che la passphrase venga archiviata in modo sicuro. Il trasferimento dei dati si verifica quindi tra l'ambiente locale e l'insieme di credenziali di servizi di ripristino di Azure tramite una connessione HTTPS sicura.  I dati all'interno dell'insieme di credenziali di servizi di ripristino vengono quindi crittografati a riposo usando la crittografia di Azure.

Anche i dati Azure Site Recovery vengono sempre crittografati in transito. Tutti i dati replicati vengono trasportati in modo sicuro in Azure usando HTTPS e TLS. Quando un cliente di Azure si connette ad Azure tramite una connessione ExpressRoute, i dati Azure Site Recovery vengono inviati tramite questa connessione privata.  Quando un cliente di Azure si connette ad Azure tramite una connessione VPN, i dati vengono replicati tra l'insieme di credenziali locale e l'insieme di credenziali di servizi di ripristino in modo sicuro tramite Internet.

Questo trasferimento dei dati di rete sicuro elimina i requisiti di protezione e di mitigazione per la gestione di soluzioni di archiviazione di backup fuori sede tradizionali, ad esempio i supporti a nastro.

### <a name="data-retention-periods"></a>Periodi di conservazione dei dati

È consigliabile un periodo di conservazione dei backup minimo di tre mesi, ma i periodi di conservazione più lunghi sono comunemente necessari. Backup di Azure è in grado di fornire fino a 9999 copie di un backup. Se un singolo backup di Azure di un'istanza protetta è stato effettuato quotidianamente, ciò consentirebbe la conservazione di 27 anni di backup giornalieri. I singoli backup mensili di un'istanza protetta consentono 833 anni di conservazione. Poiché i dati di backup sono obsoleti e i backup meno granulari vengono conservati nel tempo, l'intervallo di conservazione totale per i dati di backup aumenta.  Azure non limita il periodo di tempo in cui i dati possono rimanere in un insieme di credenziali di servizi di ripristino di Azure, ma solo il numero totale di backup per ogni istanza. Inoltre, non esiste alcuna differenza di prestazioni tra il ripristino da backup obsoleti o nuovi, a ogni ripristino viene impiegata la stessa quantità di tempo.

Nell'insieme di credenziali di servizi di ripristino di Azure sono presenti diversi criteri predefiniti di backup e conservazione.  Il personale amministrativo può inoltre creare criteri personalizzati di backup e conservazione.

![Criteri di backup di Azure](media/create-policy.png)

Quando si configurano i criteri di conservazione e backup di Azure, è necessario trovare un equilibrio tra la frequenza di backup e i requisiti di conservazione a lungo termine.

### <a name="backup-and-restore-testing"></a>Test di backup e ripristino

ISM consiglia di testare i dati di backup per assicurarsi che i dati protetti siano validi quando è necessario un ripristino o un failover. Backup di Azure e Azure Site Recovery offrono inoltre la possibilità di testare i dati protetti una volta che è stato eseguito il backup o la replica. I dati gestiti da backup di Azure possono essere ripristinati in una posizione candidata e la coerenza dei dati può quindi essere convalidata.

Azure Site Recovery dispone di funzionalità incorporate per eseguire i test di failover. I carichi di lavoro replicati nell'insieme di credenziali di servizi di ripristino possono essere ripristinati in un ambiente di Azure designato. L'ambiente di ripristino di destinazione può essere completamente isolato da qualsiasi ambiente di produzione per garantire che non vi sia alcun impatto sui sistemi di produzione durante l'esecuzione di un test. Una volta completato il test, è possibile eliminare immediatamente l'ambiente di test e tutte le risorse per ridurre i costi operativi.

Il test e la convalida del failover possono essere automatizzati tramite il servizio automazione di Azure incorporato nella piattaforma Azure. In questo modo è possibile pianificare automaticamente il test di failover per garantire che i dati vengano replicati correttamente in Azure.

## <a name="next-steps"></a>Passaggi successivi

Vedere l'articolo su come [garantire la sicurezza con criteri di Azure](azure-policy.md).
