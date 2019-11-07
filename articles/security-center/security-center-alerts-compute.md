---
title: Rilevamento delle minacce per il cloud native Computing nel centro sicurezza di Azure | Microsoft Docs
description: Questo articolo presenta gli avvisi di calcolo nativi cloud disponibili nel centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/05/2019
ms.author: memildin
ms.openlocfilehash: d705174f46ba38f37ae8cce9839c2d1f63ab6bf8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73664264"
---
# <a name="threat-detection-for-cloud-native-computing-in-azure-security-center"></a>Rilevamento delle minacce per il cloud native Computing nel centro sicurezza di Azure

Come soluzione nativa, il Centro sicurezza di Azure offre una visibilità univoca dei log interni per l'identificazione della metodologia di attacco in più destinazioni. Questo articolo presenta gli avvisi disponibili per i servizi di Azure seguenti:

* [servizio app di Azure](#app-services)
* [Contenitori di Azure](#azure-containers) 

> [!NOTE]
> Questi servizi non sono attualmente disponibili in Azure per enti pubblici e aree cloud sovrane.

## Servizio app Azure<a name="app-services"></a>

Il Centro sicurezza usa la scalabilità del cloud per identificare gli attacchi destinati alle applicazioni in esecuzione sul servizio app. Gli utenti malintenzionati Probe applicazioni Web per individuare e sfruttare i punti deboli. Prima di essere indirizzati ad ambienti specifici, le richieste alle applicazioni in esecuzione in Azure passano attraverso diversi gateway, dove vengono ispezionate e registrate. Questi dati vengono quindi usati per identificare gli exploit e gli utenti malintenzionati e per apprendere nuovi modelli che verranno usati in un secondo momento.

Grazie alla visibilità di Azure come provider di servizi cloud, il Centro sicurezza analizza i log interni del servizio app per identificare la metodologia di attacco in più destinazioni. La metodologia, ad esempio, include analisi generalizzate e attacchi distribuiti. Questo tipo di attacco in genere deriva da un piccolo subset di indirizzi IP e Mostra i modelli di ricerca per indicizzazione in endpoint simili su più host. Gli attacchi stanno cercando una pagina o un plug-in vulnerabile e non possono essere identificati dal punto di vista di un singolo host.

Il Centro sicurezza ha anche accesso ai sandbox e alle VM sottostanti. Insieme alle analisi della memoria, l'infrastruttura può raccontare la storia, da un nuovo attacco che circola in natura a compromessi nei computer dei clienti. Pertanto, anche se il Centro sicurezza viene distribuito dopo che un'app Web è stata sfruttata, potrebbe essere in grado di rilevare attacchi continui.

> [!div class="mx-tableFixed"]

|Avviso|Descrizione|
|---|---|
|**Rilevata chiamata del tema WordPress sospetta**|Il log attività del servizio app indica una possibile attività di inserimento di codice nella risorsa del servizio app.<br/> Questa attività sospetta assomiglia all'attività che manipola un tema WordPress per supportare l'esecuzione sul lato server del codice, seguito da una richiesta Web diretta per richiamare il file di tema modificato. Questo tipo di attività può essere parte di una campagna di attacco rispetto a WordPress.|
|**Connessione alla pagina Web da un indirizzo IP anomalo rilevato**|Il log attività del servizio app indica una connessione a una pagina Web sensibile da un indirizzo di origine che non si è mai connesso prima. Questa connessione potrebbe indicare che un utente sta tentando un attacco di forza bruta nelle pagine di amministrazione dell'app Web. Potrebbe anche essere il risultato di un utente legittimo che usa un nuovo indirizzo IP.|
|**Nell'Intelligence per le minacce è stato trovato un indirizzo IP connesso all'interfaccia FTP del servizio app Azure**|L'analisi dei log FTP del servizio app ha rilevato una connessione da un indirizzo di origine trovato nel feed di intelligence per le minacce. Durante questa connessione, un utente ha eseguito l'accesso alle pagine elencate.|
|**Impronta digitale web rilevata**|Il log attività del servizio app indica una possibile attività di impronta digitale Web nella risorsa del servizio app. <br/>Questa attività sospetta è associata a uno strumento denominato Elephant cieco. Lo strumento consente di eseguire l'impronta digitale dei server Web e tenta di rilevare le applicazioni installate e le relative versioni. Spesso gli utenti malintenzionati usano questo strumento per eseguire il probe delle applicazioni Web per individuare le vulnerabilità.|
|**È stato rilevato un accesso sospetto a una pagina Web potenzialmente vulnerabile**|Il log attività del servizio app indica che è stato eseguito l'accesso a una pagina Web che sembra essere sensibile. <br/>Questa attività sospetta ha origine da un indirizzo di origine il cui modello di accesso è simile a quello di uno scanner Web. Questo tipo di attività è spesso associato a un tentativo da un utente malintenzionato di analizzare la rete per tentare di accedere alle pagine Web sensibili o vulnerabili.|
|**File PHP nella cartella di caricamento**|Il log attività del servizio app indica che è stato eseguito l'accesso a una pagina PHP sospetta che si trova nella cartella di caricamento. <br/>Questo tipo di cartella non contiene in genere file PHP. L'esistenza di questo tipo di file potrebbe indicare un exploit che sfrutta le vulnerabilità di caricamento arbitrario dei file.|
|**Tentativo di eseguire i comandi di Linux in un servizio app di Windows**|L'analisi dei processi del servizio app ha rilevato un tentativo di esecuzione di un comando Linux in un servizio app di Windows. Questa azione è stata eseguita dall'applicazione Web. Questo comportamento è spesso visibile durante le campagne che sfruttano una vulnerabilità in un'applicazione Web comune.|
|**È stata rilevata un'esecuzione PHP sospetta**|Log del computer indica che è in esecuzione un processo PHP sospetto. L'azione includeva un tentativo di eseguire comandi del sistema operativo o codice PHP dalla riga di comando tramite il processo PHP. Sebbene questo comportamento possa essere legittimo, nelle applicazioni Web questo comportamento potrebbe indicare attività dannose, ad esempio tentativi di infettare i siti Web con le shell Web.|
|**Elabora esecuzione da cartella temporanea**|L'analisi dei processi del servizio app ha rilevato un'esecuzione di un processo dalla cartella temporanea dell'app. Sebbene questo comportamento possa essere legittimo, nelle applicazioni Web questo comportamento potrebbe indicare attività dannose.|
|**Il tentativo di eseguire il comando con privilegi elevati è stato rilevato**|L'analisi dei processi del servizio app ha rilevato un tentativo di eseguire un comando che richiede privilegi elevati. Il comando è stato eseguito nel contesto dell'applicazione Web. Sebbene questo comportamento possa essere legittimo, nelle applicazioni Web questo comportamento potrebbe indicare attività dannose.|

## Contenitori di Azure<a name="azure-containers"></a>

Il Centro sicurezza fornisce il rilevamento delle minacce in tempo reale per gli ambienti in contenitori e genera avvisi per le attività sospette. È possibile usare queste informazioni per risolvere rapidamente i problemi di sicurezza e migliorare la sicurezza dei contenitori.

Le minacce vengono rilevate a livelli diversi: 

* **Livello host** : l'agente del Centro sicurezza (disponibile nel livello standard, vedere [prezzi](security-center-pricing.md) per i dettagli) monitora Linux per le attività sospette. L'agente attiva avvisi per le attività sospette provenienti dal nodo o da un contenitore in esecuzione su di esso. Esempi di attività di questo tipo includono rilevamento della shell Web e connessione con indirizzi IP sospetti noti.

    Per informazioni più dettagliate sulla sicurezza dell'ambiente in contenitori, l'agente monitora le analisi specifiche del contenitore. Attiverà gli avvisi per eventi quali la creazione di contenitori con privilegi, l'accesso sospetto ai server API e i server Secure Shell (SSH) in esecuzione all'interno di un contenitore docker.

    >[!NOTE]
    > Se si sceglie di non installare gli agenti negli host, si riceverà solo un subset dei vantaggi e degli avvisi di rilevamento delle minacce. Verranno comunque ricevuti gli avvisi relativi all'analisi di rete e alle comunicazioni con server dannosi.

* Per il **livello del cluster AKS**, il monitoraggio del rilevamento delle minacce è basato sull'analisi dei log di controllo di Kubernetes. Per abilitare questo monitoraggio senza **agente** , aggiungere l'opzione Kubernetes alla sottoscrizione dalla pagina **Impostazioni & prezzi** (vedere i [prezzi](security-center-pricing.md)). Per generare avvisi a questo livello, il Centro sicurezza monitora i servizi gestiti da AKS usando i log recuperati da AKS. Esempi di eventi a questo livello includono dashboard Kubernetes esposti, creazione di ruoli con privilegi elevati e creazione di montaggi sensibili. 

    >[!NOTE]
    > Il Centro sicurezza genera avvisi di rilevamento per le azioni e le distribuzioni del servizio Kubernetes di Azure che si verificano dopo che l'opzione Kubernetes è stata abilitata nelle impostazioni della sottoscrizione. 

Inoltre, il team globale dei ricercatori di sicurezza monitora costantemente il panorama delle minacce. Aggiungono avvisi e vulnerabilità specifici del contenitore Man mano che vengono individuati.


### <a name="aks-cluster-level-alerts"></a>Avvisi a livello di cluster AKS

> [!div class="mx-tableFixed"]

|Avviso|Descrizione|
|---|---|
|**Anteprima-associazione ruolo al ruolo di amministratore del cluster rilevato**|L'analisi del log di controllo di Kubernetes ha rilevato una nuova associazione al ruolo di amministratore del cluster, con privilegi di amministratore. La fornitura inutilmente di privilegi di amministratore potrebbe causare problemi di escalation dei privilegi nel cluster.|
|**ANTEPRIMA: è stato rilevato un dashboard Kubernetes esposto**|L'analisi del log di controllo di Kubernetes ha rilevato l'esposizione del dashboard Kubernetes da parte di un servizio LoadBalancer. I dashboard esposti consentono l'accesso non autenticato alla gestione del cluster e costituiscono una minaccia per la sicurezza.|
|**ANTEPRIMA: rilevato nuovo ruolo con privilegi elevati**|L'analisi del log di controllo di Kubernetes ha rilevato un nuovo ruolo con privilegi elevati. Un'associazione a un ruolo con privilegi elevati consente di privilegi elevati di utenti/gruppi nel cluster. La fornitura inutilmente di privilegi elevati può causare problemi di escalation dei privilegi nel cluster.|
|**ANTEPRIMA-nuovo contenitore nello spazio dei nomi Kube-System rilevato**|L'analisi del log di controllo di Kubernetes ha rilevato un nuovo contenitore nello spazio dei nomi Kube-System che non è tra i contenitori che in genere vengono eseguiti in questo spazio dei nomi. Gli spazi dei nomi Kube-System non devono contenere risorse utente. Gli utenti malintenzionati possono utilizzare questo spazio dei nomi per nascondere i componenti dannosi.|
|**ANTEPRIMA-contenitore di data mining Digital Currency rilevato**|L'analisi del log di controllo di Kubernetes ha rilevato un contenitore con un'immagine associata a uno strumento di data mining digitale.|
|**ANTEPRIMA-contenitore con privilegi rilevato**|L'analisi del log di controllo di Kubernetes ha rilevato un nuovo contenitore con privilegi. Un contenitore con privilegi ha accesso alle risorse del nodo e interrompe l'isolamento tra i contenitori. Se compromesso, un utente malintenzionato può usare il contenitore con privilegi per ottenere l'accesso al nodo.|
|**ANTEPRIMA-contenitore con un montaggio del volume sensibile rilevato**|L'analisi del log di controllo di Kubernetes ha rilevato un nuovo contenitore con un montaggio sensibile del volume. Il volume rilevato è un tipo hostPath che consente di montare un file o una cartella sensibile dal nodo al contenitore. Se il contenitore viene compromesso, l'utente malintenzionato può usare questa installazione per accedere al nodo.|



### <a name="host-level-alerts"></a>Avvisi a livello di host

> [!div class="mx-tableFixed"]

|Avviso|Descrizione|
|---|---|
|**Rilevato contenitore con privilegi**|Log del computer indica che è in esecuzione un contenitore Docker con privilegi. Un contenitore con privilegi dispone dell'accesso completo alle risorse dell'host. Se compromesso, un utente malintenzionato può usare il contenitore con privilegi per ottenere l'accesso al computer host.|
|**Esecuzione comando con privilegi nel contenitore**|I log del computer indicano che un comando con privilegi è stato eseguito in un contenitore docker. Un comando con privilegi ha privilegi estesi sul computer host.|
|**Rilevato daemon Docker esposto**|Log del computer indica che il daemon Docker (dockerd) espone un socket TCP. Per impostazione predefinita, la configurazione di Docker non usa la crittografia o l'autenticazione quando è abilitato un socket TCP. Chiunque abbia accesso alla porta pertinente può quindi ottenere l'accesso completo al daemon docker.|
|**Il server SSH viene eseguito all'interno di un contenitore**|I log del computer indicano che un server SSH viene eseguito all'interno di un contenitore docker. Sebbene questo comportamento possa essere intenzionale, spesso indica che un contenitore non è configurato correttamente o è stato violato.|
|**Rilevato contenitore con un'immagine Miner**|I log del computer indicano l'esecuzione di un contenitore Docker che esegue un'immagine associata a Digital Currency mining. Questo comportamento può eventualmente indicare che le risorse sono state usate.|
|**Richiesta sospetta all'API Kubernetes**|I log del computer indicano che è stata apportata una richiesta sospetta all'API Kubernetes. La richiesta è stata inviata da un nodo Kubernetes, probabilmente da uno dei contenitori in esecuzione nel nodo. Sebbene questo comportamento possa essere intenzionale, potrebbe indicare che il nodo esegue un contenitore compromesso.|
|**Richiesta sospetta al dashboard di Kubernetes**|I log del computer indicano che è stata apportata una richiesta sospetta al dashboard di Kubernetes. La richiesta è stata inviata da un nodo Kubernetes, probabilmente da uno dei contenitori in esecuzione nel nodo. Sebbene questo comportamento possa essere intenzionale, potrebbe indicare che il nodo esegue un contenitore compromesso.|