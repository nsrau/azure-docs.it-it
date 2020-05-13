---
title: Note sulla versione del Centro sicurezza di Azure
description: Descrizione delle novità e delle modifiche apportate al centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2020
ms.author: memildin
ms.openlocfilehash: bfe1e5d6a0c4171a262b36387f02be356fb1d72d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210896"
---
# <a name="whats-new-in-azure-security-center"></a>Novità del Centro sicurezza di Azure

La sicurezza di Azure è in fase di sviluppo attivo e riceve miglioramenti su base continuativa. Per rimanere sempre aggiornati sugli sviluppi più recenti, in questa pagina vengono fornite informazioni su:

- Nuove funzionalità
- Correzioni di bug
- Funzionalità deprecate

Questa pagina viene aggiornata regolarmente, quindi viene rivisitata spesso. Se si stanno cercando elementi più vecchi di sei mesi, saranno disponibili nell' [Archivio per le novità del Centro sicurezza di Azure](release-notes-archive.md).


## <a name="may-2020"></a>Maggio 2020

### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Modifiche all'accesso alle macchine virtuali JIT (just-in-Time)

Il Centro sicurezza include una funzionalità facoltativa per proteggere le porte di gestione delle macchine virtuali. Ciò offre una difesa contro la forma più comune di attacchi di forza bruta.

Questo aggiornamento apporta le seguenti modifiche a questa funzionalità:

- La raccomandazione che consiglia di abilitare JIT in una macchina virtuale è stata rinominata. In precedenza, il controllo di accesso alla rete JIT dovrebbe essere applicato alle macchine virtuali. ora è possibile che le porte di gestione delle macchine virtuali siano protette con il controllo di accesso di rete JIT.

- La raccomandazione è stata impostata per essere attivata solo se sono presenti porte di gestione aperte.

[Altre informazioni sulla funzionalità di accesso JIT](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Le raccomandazioni personalizzate sono state spostate in un controllo di sicurezza separato

Uno dei controlli di sicurezza introdotti con il Punteggio sicuro migliorato è stato "implementare le procedure consigliate per la sicurezza". Eventuali raccomandazioni personalizzate create per le sottoscrizioni sono state inserite automaticamente nel controllo. 

Per semplificare l'individuazione delle raccomandazioni personalizzate, sono state spostate in un controllo di sicurezza dedicato, "raccomandazioni personalizzate". Questo controllo non ha alcun effetto sul punteggio sicuro.

Altre informazioni sui controlli di sicurezza in un [Punteggio sicuro avanzato (anteprima) nel centro sicurezza di Azure](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Interruttore aggiunto per visualizzare le raccomandazioni nei controlli o come elenco semplice

I controlli di sicurezza sono gruppi logici di raccomandazioni correlate alla sicurezza. Riflettono le superfici di attacco vulnerabili. Un controllo è un set di raccomandazioni sulla sicurezza, con istruzioni che consentono di implementare tali raccomandazioni.

Per verificare immediatamente in che modo l'organizzazione protegga ogni singola superficie di attacco, esaminare i punteggi per ogni controllo di sicurezza.

Per impostazione predefinita, le raccomandazioni vengono visualizzate nei controlli di sicurezza. Da questo aggiornamento è inoltre possibile visualizzarli come elenco. Per visualizzarli come semplici elenchi ordinati in base allo stato di integrità delle risorse interessate, usare il nuovo elemento/Nascondi ' raggruppa per controlli '. L'interruttore si trova sopra l'elenco nel portale.

I controlli di sicurezza, e questo interruttore, fanno parte della nuova esperienza di assegnazione dei punteggi sicura. Ricordarsi di inviare commenti e suggerimenti dall'interno del portale.

Altre informazioni sui controlli di sicurezza in un [Punteggio sicuro avanzato (anteprima) nel centro sicurezza di Azure](secure-score-security-controls.md).


### <a name="account-security-recommendations-moved-to-security-best-practices-security-control"></a>Raccomandazioni sulla sicurezza dell'account spostate nel controllo di sicurezza "procedure consigliate per la sicurezza"

Uno dei controlli di sicurezza introdotti con il Punteggio sicuro migliorato è "procedure consigliate per la sicurezza". Quando una raccomandazione si trova in questo controllo, non ha alcun effetto sul punteggio sicuro. 

Con questo aggiornamento, tre raccomandazioni sono state spostate all'esterno dei controlli in cui erano originariamente posizionate e in questo controllo delle procedure consigliate. Questa operazione è stata eseguita perché è stato rilevato che il rischio di queste tre raccomandazioni è inferiore a quello previsto inizialmente.

Sono disponibili le indicazioni seguenti:

- L'autenticazione a più fattori deve essere abilitata per gli account con autorizzazioni di lettura per la sottoscrizione (originariamente nel controllo "Enable multi-factor authentication")
- Gli account esterni con autorizzazioni di lettura devono essere rimossi dalla sottoscrizione (originariamente nel controllo "Gestisci accesso e autorizzazioni")
- È necessario designare un massimo di 3 proprietari per la sottoscrizione (originariamente nel controllo "Gestisci accesso e autorizzazioni")

Altre informazioni sui controlli di sicurezza in un [Punteggio sicuro avanzato (anteprima) nel centro sicurezza di Azure](secure-score-security-controls.md).


### <a name="custom-policies-with-custom-metadata-generally-available"></a>Criteri personalizzati con metadati personalizzati disponibili a livello generale

I criteri personalizzati fanno ora parte dell'esperienza consigliata del Centro sicurezza, del Punteggio sicuro e del dashboard degli standard di conformità normativi. Questa funzionalità è ora disponibile a livello generale e consente di estendere la copertura della valutazione della sicurezza dell'organizzazione nel centro sicurezza. 

Creare un'iniziativa personalizzata in criteri di Azure, aggiungervi criteri e caricarla nel centro sicurezza di Azure e visualizzarla come consigli.

A questo punto è stata aggiunta anche l'opzione per modificare i metadati delle raccomandazioni personalizzate. Le opzioni dei metadati includono gravità, passaggi di correzione, informazioni sulle minacce e altro ancora.  

[Scopri di più su come migliorare le tue raccomandazioni personalizzate con informazioni dettagliate](custom-security-policies.md#enhancing-your-custom-recommendations-with-detailed-information).


## <a name="april-2020"></a>Aprile 2020

### <a name="dynamic-compliance-packages-now-generally-available"></a>Pacchetti di conformità dinamici ora disponibili a livello generale

Il dashboard di conformità normativa del Centro sicurezza di Azure include ora **pacchetti di conformità dinamici** (ora disponibili a livello generale) per tenere traccia di altri standard di settore e normativi.

È possibile aggiungere pacchetti di conformità dinamici alla sottoscrizione o al gruppo di gestione dalla pagina dei criteri di sicurezza del Centro sicurezza. Quando è stato caricato uno standard o un benchmark, lo standard viene visualizzato nel dashboard conformità normativa con tutti i dati di conformità associati mappati come valutazioni. Un report di riepilogo per gli standard che sono stati caricati sarà disponibile per il download.

A questo punto, è possibile aggiungere standard, ad esempio:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-V2020**
- **Ufficiale del Regno Unito e NHS del Regno Unito**
- **Canada Federal PBMM**
- **Azure cis 1.1.0 (nuovo)** (una rappresentazione più completa di Azure cis 1.1.0)

È stato aggiunto di recente il benchmark di **sicurezza di Azure**, ovvero le linee guida specifiche di Azure create da Microsoft per le procedure consigliate per la sicurezza e la conformità basate su Framework di conformità comuni. Gli standard aggiuntivi saranno supportati nel dashboard Man mano che diventano disponibili.  
 
[Altre informazioni sulla personalizzazione del set di standard nel dashboard conformità normativa](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Raccomandazioni per l'identità ora incluse nel livello gratuito del Centro sicurezza di Azure

Sono ora disponibili a livello generale le raccomandazioni sulla sicurezza per l'identità e l'accesso nel livello gratuito del Centro sicurezza di Azure. Questa operazione fa parte del lavoro necessario per rendere disponibili le funzionalità CSPM (Cloud Security Posture Management). Fino ad ora, queste raccomandazioni erano disponibili solo nel piano tariffario standard.

Di seguito sono riportati alcuni esempi di raccomandazioni di identità e accesso:

- "L'autenticazione a più fattori deve essere abilitata per gli account con autorizzazioni proprietario per la sottoscrizione."
- "Per la sottoscrizione è necessario designare un massimo di tre proprietari".
- "Gli account deprecati devono essere rimossi dalla sottoscrizione".

Se si dispone di sottoscrizioni per il piano tariffario gratuito, i punteggi sicuri saranno interessati da questa modifica perché non sono mai stati valutati per la sicurezza dell'accesso e delle identità.

[Altre informazioni sulle raccomandazioni di identità e accesso](recommendations-reference.md#recs-identity).
[Altre informazioni sul monitoraggio dell'identità e dell'accesso](security-center-identity-access.md).


## <a name="march-2020"></a>Marzo 2020

### <a name="workflow-automation-is-now-generally-available"></a>Automazione del flusso di lavoro ora disponibile a livello generale

La funzionalità di automazione del flusso di lavoro del Centro sicurezza di Azure è ora disponibile a livello generale. Usarlo per attivare automaticamente app per la logica in avvisi di sicurezza e consigli. Sono inoltre disponibili trigger manuali per gli avvisi e tutte le raccomandazioni per le quali è disponibile l'opzione correzione rapida.

Ogni programma di sicurezza include più flussi di lavoro per la risposta agli eventi imprevisti. Questi processi possono includere la notifica a stakeholder pertinenti, l'avvio di un processo di gestione delle modifiche e l'applicazione di procedure di correzione specifiche. Gli esperti di sicurezza raccomandano di automatizzare tutti i passaggi di queste procedure come possibile. L'automazione riduce il sovraccarico e può migliorare la sicurezza garantendo che i passaggi del processo vengano eseguiti rapidamente, in modo coerente e in base ai requisiti predefiniti.

Per ulteriori informazioni sulle funzionalità del Centro sicurezza automatico e manuale per l'esecuzione dei flussi di lavoro, vedere [automazione dei flussi di lavoro](workflow-automation.md).

Per informazioni sulla creazione di app per la logica, vedere app per la [logica di Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Integrazione del Centro sicurezza di Azure con l'interfaccia di amministrazione di Windows

È ora possibile spostare i server Windows locali dall'interfaccia di amministrazione di Windows direttamente nel centro sicurezza di Azure. Il Centro sicurezza diventa quindi il singolo riquadro di vetro per visualizzare le informazioni di sicurezza per tutte le risorse dell'interfaccia di amministrazione di Windows, inclusi i server locali, le macchine virtuali e i carichi di lavoro PaaS aggiuntivi.

Dopo aver spostato un server dal centro di amministrazione di Windows al centro sicurezza di Azure, sarà possibile:

- Visualizzare gli avvisi di sicurezza e le raccomandazioni nell'estensione del Centro sicurezza dell'interfaccia di amministrazione di Windows.
- Visualizzare il comportamento di sicurezza e recuperare informazioni dettagliate aggiuntive dei server gestiti dall'interfaccia di amministrazione di Windows nel centro sicurezza all'interno del portale di Azure (o tramite un'API).

Altre informazioni su come [integrare il Centro sicurezza di Azure con l'interfaccia di amministrazione di Windows](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Protezione per il servizio Azure Kubernetes

Il Centro sicurezza di Azure espande le funzionalità di sicurezza dei contenitori per proteggere il servizio Azure Kubernetes (AKS).

La diffusa piattaforma open source Kubernetes è stata adottata in modo da essere ora uno standard di settore per l'orchestrazione dei contenitori. Nonostante questa implementazione diffusa, c'è ancora la mancanza di informazioni su come proteggere un ambiente Kubernetes. Per difendere le aree di attacco di un'applicazione in contenitori, è necessario avere una certa esperienza per garantire che l'infrastruttura sia configurata in modo sicuro e costantemente monitorato per potenziali minacce.

La difesa del Centro sicurezza include:

- **Individuazione e visibilità** : individuazione continua delle istanze di AKS gestite all'interno delle sottoscrizioni registrate nel centro sicurezza.
- **Raccomandazioni sulla sicurezza** : raccomandazioni utili per la conformità alle procedure consigliate per la sicurezza per AKS. Queste raccomandazioni sono incluse nel punteggio sicuro per assicurarsi che siano visualizzate come parte del comportamento di sicurezza dell'organizzazione. Un esempio di raccomandazione relativa a AKS potrebbe essere il "controllo degli accessi in base al ruolo da usare per limitare l'accesso a un cluster del servizio Kubernetes".
- **Protezione dalle minacce** : tramite l'analisi continua della distribuzione di AKS, il Centro sicurezza segnala le minacce e le attività dannose rilevate a livello di host e del cluster AKS.

[Scopri di più sull'integrazione dei servizi Kubernetes di Azure con il Centro sicurezza](azure-kubernetes-service-integration.md).
[Altre informazioni sulle funzionalità di sicurezza del contenitore nel centro sicurezza](container-security.md).


### <a name="improved-just-in-time-experience"></a>Esperienza JIT migliorata

Le funzionalità, l'operazione e l'interfaccia utente per gli strumenti JIT del Centro sicurezza di Azure che proteggono le porte di gestione sono state migliorate come segue: 

- **Campo giustificazione** : quando si richiede l'accesso a una macchina virtuale (VM) tramite la pagina jit del portale di Azure, è disponibile un nuovo campo facoltativo per immettere una giustificazione per la richiesta. Le informazioni immesse in questo campo possono essere rilevate nel log attività. 
- **Pulizia automatica di regole JIT (just-in-Time) ridondanti** : ogni volta che si aggiorna un criterio JIT, viene eseguito automaticamente uno strumento di pulizia per verificare la validità dell'intero set di regole. Lo strumento Cerca le mancate corrispondenze tra le regole nei criteri e le regole del NSG. Se lo strumento di pulizia rileva una mancata corrispondenza, determina la cause e, quando è sicuro, rimuove le regole incorporate che non sono più necessarie. Il Pulisci non elimina mai le regole create. 

[Altre informazioni sulla funzionalità di accesso JIT](security-center-just-in-time.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Due raccomandazioni sulla sicurezza per le applicazioni Web deprecate

Due raccomandazioni sulla sicurezza correlate alle applicazioni Web sono deprecate: 

- Le regole per le applicazioni Web in IaaS gruppi devono essere finalizzate.
    (Criterio correlato: le regole gruppi per le applicazioni Web in IaaS devono essere finalizzate)

- È necessario limitare l'accesso ai servizi app.
    (Criteri correlati: è necessario limitare l'accesso ai servizi app [Anteprima])

Queste raccomandazioni non verranno più visualizzate nell'elenco di raccomandazioni del Centro sicurezza. I criteri correlati non verranno più inclusi nell'iniziativa denominata "Centro sicurezza predefinito".

[Altre informazioni sulle raccomandazioni sulla sicurezza](recommendations-reference.md).

## <a name="february-2020"></a>Febbraio 2020

### <a name="fileless-attack-detection-for-linux-is-now-in-preview"></a>Il rilevamento di attacchi non file per Linux è ora disponibile in anteprima

Poiché gli utenti malintenzionati che aumentano l'uso di metodi stealthier per evitare il rilevamento, il Centro sicurezza di Azure sta estendendo il rilevamento di attacchi non file per Linux, oltre a Windows Gli attacchi informatici sfruttano le vulnerabilità del software, introducono payload dannosi in processi di sistema benigni e nascondono memoria. Queste tecniche:

- ridurre o eliminare tracce di malware sul disco
- ridurre notevolmente le probabilità di rilevamento da parte di soluzioni di analisi di malware basate su disco

Per contrastare questa minaccia, il Centro sicurezza di Azure ha rilasciato il rilevamento di attacchi senza file per Windows nell'ottobre 2018 e ora ha esteso il rilevamento di attacchi senza file su Linux. 


## <a name="january-2020"></a>Gennaio 2020

### <a name="enhanced-secure-score"></a>Punteggio sicuro migliorato

Una versione migliorata della funzionalità di Punteggio sicuro del Centro sicurezza di Azure è ora disponibile in anteprima. In questa versione, più raccomandazioni sono raggruppate in controlli di sicurezza che riflettono meglio le superfici di attacco vulnerabili, ad esempio, limitano l'accesso alle porte di gestione.

Acquisire familiarità con le modifiche del Punteggio sicuro durante la fase di anteprima e determinare altre correzioni che consentiranno di proteggere ulteriormente l'ambiente.

Altre informazioni sono disponibili in [Punteggio sicuro avanzato (anteprima) nel centro sicurezza di Azure](secure-score-security-controls.md).