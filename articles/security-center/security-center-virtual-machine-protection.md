---
title: Protezione delle macchine virtuali nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento illustra le raccomandazioni presenti nel Centro sicurezza che facilitano la protezione delle macchine virtuali, dei computer, delle app Web e degli ambienti di Servizio app di Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: monhaber
ms.openlocfilehash: fa664952f3eb7d6f9e611fb87a9e484e97f388a2
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403834"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>Protezione delle macchine virtuali nel Centro sicurezza di Azure
Centro sicurezza di Azure analizza lo stato di sicurezza delle risorse di Azure, il server non di Azure e macchine virtuali. Quando il Centro sicurezza identifica potenziali vulnerabilità della sicurezza, crea raccomandazioni utili per definire il processo di configurazione dei controlli necessari. Le raccomandazioni sono applicabili a diversi tipi di risorse di Azure, ovvero macchine virtuali (VM) e computer, applicazioni, risorse di rete, SQL, identità e accesso.

Questo articolo illustra le raccomandazioni relative alle macchine e alle applicazioni.

## <a name="monitoring-security-health"></a>Monitoraggio dello stato di sicurezza
È possibile monitorare lo stato di sicurezza delle risorse nel dashboard **Centro sicurezza - Panoramica**. La sezione **Risorse** segnala il numero di problemi identificati e lo stato di sicurezza per ogni tipo di risorsa.

È possibile visualizzare l'elenco di tutti i problemi selezionando **Consigli**. Per altre informazioni su come applicare le raccomandazioni, leggere l'articolo [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md).

Per l'elenco completo delle raccomandazioni relative ai servizi di calcolo e app, vedere [Raccomandazioni](security-center-virtual-machine-recommendations.md).

Per continuare, selezionare **Calcolo e app** in **Risorse** o dal menu principale del Centro sicurezza.
![Dashboard Centro sicurezza](./media/security-center-virtual-machine-recommendations/overview.png)

## <a name="monitor-compute-and-app-services"></a>Monitoraggio dei servizi di calcolo e app
In **Calcolo e app** sono disponibili le schede seguenti:

- **Panoramica**: monitoraggio e raccomandazioni identificati dal Centro sicurezza.
- **VM e computer**: elenco di VM, computer e il relativo stato di sicurezza.
- **Servizi cloud**: elenco dei ruoli Web e di lavoro monitorati dal Centro sicurezza.
- **Servizi app**: elenco di ambienti del Servizio app e relativo stato di sicurezza corrente.
- **Contenitori (anteprima)**: elenco dei contenitori ospitati in computer IaaS Linux e valutazione della sicurezza delle relative configurazioni Docker.
- **Risorse di calcolo (anteprima)**: elenco di raccomandazioni per le risorse di calcolo, come cluster di Service Fabric e hub eventi.

Per continuare, selezionare **Risorse di calcolo e app** in **Protezione della sicurezza delle risorse**.

![Calcolo](./media/security-center-virtual-machine-recommendations/compute.png)

In ogni scheda possono essere presenti più sezioni ed è possibile selezionare in ogni sezione una singola opzione per visualizzare altri dettagli sulla procedura consigliata per risolvere il problema.

### Macchine virtuali e computer non monitorati <a name="unmonitored-vms-and-computers"></a>
Una macchina virtuale o un computer non è monitorata dal Centro sicurezza se non esegue l'estensione Microsoft Monitoring Agent. Un computer potrebbe avere già installato un agente locale, ad esempio OMS diretto agente o dell'agente System Center Operations Manager. Le macchine virtuali con questi agenti vengono identificate come non monitorate perché tali agenti non sono supportati completamente nel Centro sicurezza. Per sfruttare in modo ottimale i vantaggi di tutte le funzionalità del Centro sicurezza, è necessaria l'estensione Microsoft Monitoring Agent.

È possibile installare l'estensione nella VM o del computer non monitorata oltre all'agente locale già installato. Configurare gli agenti allo stesso modo, connettendoli alla stessa area di lavoro. Ciò consente al Centro sicurezza di interagire con l'estensione Microsoft Monitoring Agent e raccogliere dati. Vedere [Abilitare l'estensione macchina virtuale](../azure-monitor/learn/quick-collect-azurevm.md) per istruzioni su come installare l'estensione Microsoft Monitoring Agent.

Vedere [Problemi di integrità dell'agente di monitoraggio](security-center-troubleshooting-guide.md#mon-agent) per altre informazioni sui motivi per cui il Centro sicurezza non è in grado di monitorare correttamente VM e computer inizializzati per il provisioning automatico.

### <a name="recommendations"></a>Consigli
Questa sezione include una serie di raccomandazioni per ogni VM e computer, ruoli di lavoro e Web, app Web del servizio app di Azure e ambiente del Servizio app di Azure monitorate dal Centro sicurezza. La prima colonna elenca la raccomandazione. La seconda colonna mostra il numero totale di risorse interessate dal consiglio. La terza colonna indica la gravità del problema.

Ogni raccomandazione è inoltre associata a una serie di azioni che è possibile eseguire dopo averla selezionata. Se ad esempio si seleziona **Aggiornamenti di sistema mancanti**, verrà visualizzato il numero di macchine virtuali e computer senza patch e il livello di gravità per l'aggiornamento mancante.

**Applicare gli aggiornamenti di sistema** include un riepilogo degli aggiornamenti critici in un formato grafico, uno per Windows e uno per Linux. La seconda parte visualizza una tabella con le informazioni seguenti:

- **NOME**: il nome dell'aggiornamento mancante.
- **N. DI MACCHINE VIRTUALI E COMPUTER**: il numero totale di macchine virtuali e computer a cui manca l'aggiornamento.
- **GRAVITÀ**: descrive il livello di gravità della raccomandazione:

    - **Critica**: esiste una vulnerabilità associata a una risorsa significativa, ad esempio applicazione, macchina virtuale, gruppo di sicurezza di rete, che richiede attenzione.
    - **Importante**: sono necessari passaggi aggiuntivi, o non critici, per completare un processo o eliminare una vulnerabilità.
    - **Moderata**: vulnerabilità che è opportuno risolvere, ma non richiede attenzione immediata. Per impostazione predefinita, le raccomandazioni con gravità bassa non appaiono, ma è possibile visualizzarle applicando il filtro corrispondente.


- **STATO**: stato attuale della raccomandazione:

    - **Aperta**: la raccomandazione non è ancora stata risolta.
    - **In corso**: la raccomandazione è stata applicata alle risorse e non è richiesta alcuna azione da parte dell'utente.
    - **Risolta**: la raccomandazione è stata già completata. Quando il problema è stato risolto, la voce viene visualizzata in grigio.

Per visualizzare i dettagli delle raccomandazioni, fare clic sul nome dell'aggiornamento mancante nell'elenco.


> [!NOTE]
> Le raccomandazioni sulla sicurezza contenute qui sono identiche a quelle nel file **Raccomandazioni**. Per altre informazioni su come gestire correttamente le raccomandazioni, consultare le [Implementare le raccomandazioni sulla sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md).
>
>

### <a name="vms-and-computers"></a>VM e computer
La sezione relativa alle macchine virtuali e ai computer offre una panoramica di tutte le raccomandazioni per macchine virtuali e computer. Ogni colonna rappresenta un set di raccomandazioni.

![Consigli per VM e computer](./media/security-center-virtual-machine-recommendations/vm-computers.png)

Esistono quattro tipi di icone rappresentate in questo elenco:

![Computer non Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Computer non Azure.

![VM di Azure Resource Manager](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) VM di Azure Resource Manager.

![VM classica di Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) VM classica di Azure.


![VM identificate dall'area di lavoro](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) VM identificate solo dall'area di lavoro che fa parte della sottoscrizione visualizzata. Ciò include le macchine virtuali da altre sottoscrizioni report all'area di lavoro in questa sottoscrizione e le macchine virtuali che sono state installate con Operations Manager agente diretto e non dispone di alcun ID di risorsa.

L'icona visualizzata sotto ogni raccomandazione consente di identificare rapidamente le macchine virtuali e i computer che richiedono attenzione e il tipo di raccomandazione. È anche possibile usare i filtri per cercare all'interno dell'elenco in base al **tipo di risorsa** e alla **gravità**.

Per eseguire il drill down delle raccomandazioni di sicurezza per ogni macchina virtuale, fare clic sulla macchina virtuale.
Qui sono visualizzasti i dettagli sulla sicurezza relativi alla macchina virtuale o al computer. Nella parte inferiore è possibile vedere l'azione consigliata e il livello di gravità di ogni problema.
![Servizi cloud](./media/security-center-virtual-machine-recommendations/recommendation-list.png)

### <a name="cloud-services"></a>Servizi cloud
Per i servizi cloud viene creata una raccomandazione quando la versione del sistema operativo non è aggiornata.

![Servizi cloud](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

In uno scenario in cui è presente una raccomandazione, a differenza dell'esempio precedente, è necessario seguire la procedura illustrata nella raccomandazione per aggiornare la versione del sistema operativo. Quando è disponibile un aggiornamento, verrà visualizzato un avviso di tipo rosso o arancione, in base alla gravità del problema. Quando si seleziona questo avviso in un ruolo Web1 (che esegue Windows Server con l'app Web distribuita automaticamente in IIS) o un ruolo di lavoro1 (che esegue Windows Server con l'app Web distribuita automaticamente in IIS), verranno visualizzati altri dettagli su questa raccomandazione.

Per visualizzare una spiegazione più esaustiva riguardante questa raccomandazione, fare clic su **Aggiorna la versione del sistema operativo** nella colonna **DESCRIZIONE**.



![Aggiornare la versione sistema operativo](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png)

### <a name="app-services"></a>Servizi app
È necessario abilitare il servizio app nella sottoscrizione per visualizzare le informazioni del servizio stesso. Per istruzioni su come abilitare questa funzionalità, vedere [Proteggere il servizio app con il Centro sicurezza di Azure](security-center-app-services.md).
[!NOTE]
> La funzionalità di monitoraggio del Servizio app è disponibile in anteprima e solo per il livello Standard del Centro sicurezza.


Nella sezione **Servizi app**, è riportato un elenco degli ambienti del Servizio app e il riepilogo dell'integrità eseguita sulla base della valutazione del Centro sicurezza.

![Servizi app](./media/security-center-virtual-machine-recommendations/app-services.png)

Esistono tre tipi di icone rappresentate in questo elenco:

![Ambiente dei Servizi app](./media/security-center-virtual-machine-recommendations/ase.png) Ambiente dei Servizi app.

![Applicazione Web](./media/security-center-virtual-machine-recommendations/web-app.png) Applicazione Web.

![Applicazione di funzione](./media/security-center-virtual-machine-recommendations/function-app.png) Applicazione di funzione.

1. Selezionare un'applicazione Web. Viene aperta una visualizzazione di riepilogo con tre schede:

   - **Consigli**: in base alle valutazioni non riuscite eseguite dal Centro sicurezza.
   - **Valutazioni superate**: elenco di valutazioni superate eseguite dal Centro sicurezza.
   - **Valutazioni non disponibili**: elenco di valutazioni non eseguito a causa di un errore o poiché la raccomandazione non è rilevante per il Servizio app specifico

   Nella sezione **Raccomandazioni** è presente un elenco di consigli per l'applicazione selezionata, con l'indicazione del livello di gravità per ogni consiglio.

   ![Consigli per Servizi app](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

2. Selezionare una raccomandazione per ottenerne la descrizione e l'elenco delle risorse non integre, integre e non analizzate.

   - Nella colonna **Valutazioni superate** è presente un elenco di valutazioni superate.  Il livello di gravità di tali valutazioni è sempre di colore verde.

   - Selezionare una valutazione superata dall'elenco per visualizzarne la descrizione e l'elenco di risorse non integre, integre e non analizzate. È presente una scheda per le risorse di tipo non integro, ma tale elenco è sempre vuoto perché la valutazione è superata.

     ![Correzione del Servizio app di Azure](./media/security-center-virtual-machine-recommendations/app-service-remediation.png)

## <a name="virtual-machine-scale-sets"></a>set di scalabilità di macchine virtuali
Il Centro sicurezza individua automaticamente se si dispone di set di scalabilità e consiglia di installare Microsoft Monitoring Agent in questi set di scalabilità. 

Per installare Microsoft Monitoring Agent: 

1. Selezionare la raccomandazione **Install the monitoring agent on virtual machine scale set.**  (Installare l'agente di monitoraggio nel set di scalabilità di macchine virtuali). Si otterrà un elenco dei set di scalabilità non monitorati.
2. Selezionare un set di scalabilità non integro. Seguire le istruzioni per installare l'agente di monitoraggio mediante un'area di lavoro popolata esistente o crearne una nuova. Assicurarsi di impostare il [piano tariffario](security-center-pricing.md)dell'area di lavoro se non è impostato.

   ![Installare Microsoft Monitoring Agent](./media/security-center-virtual-machine-recommendations/install-mms.png)

Se si desidera impostare nuovi set di scalabilità affinché installino automaticamente Microsoft Monitoring Agent:
1. Passare a Criteri di Azure e fare clic su **Definizioni**.
2. Cercare il criterio **agente di distribuzione Log Analitica per set di scalabilità di macchine virtuali Windows** e fare clic su di esso.
3. Fare clic su **Assegna**.
4. Impostare l'**ambito** e l'**area di lavoro Log Analytics** e fare clic su **Assegna**.

Se si desidera impostare tutti i set di scalabilità esistenti affinché installino Microsoft Monitoring Agent, in Criteri di Azure passare a **Correzione** e applicare i criteri esistenti ai set di scalabilità esistenti.


## <a name="compute-and-app-recommendations"></a>Consigli per le app e il calcolo
|Tipo di risorsa|Punteggio di sicurezza|Recommendation|DESCRIZIONE|
|----|----|----|----|
|Servizio app|20|L'applicazione Web deve essere accessibile solo tramite HTTPS|Limitare l'accesso delle applicazioni Web solo tramite HTTPS.|
|Servizio app|20|L'app per le funzioni deve essere accessibile solo tramite HTTPS|Limitare l'accesso delle app per le funzioni solo tramite HTTPS.|
|Servizio app|5|Abilitare i log di diagnostica nel servizio app|Abilitare i log e conservarli fino a un anno. Ciò consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. |
|Servizio app|10|Il debug remoto deve essere disattivato per l'applicazione Web|Disattivare il debug per le applicazioni Web se non è più necessario. Il debug remoto richiede che vengano aperte le porte in ingresso in un'app per le funzioni.|
|Servizio app|10|Il debug remoto deve essere disattivato per l'app per le funzioni|Disattivare il debug per l'app per le funzioni se non è più necessario. Il debug remoto richiede che vengano aperte le porte in ingresso in un'app per le funzioni.|
|Servizio app|10|Configurare le restrizioni IP per l'applicazione Web|Definire un elenco di indirizzi IP che possono accedere all'applicazione. L'uso di restrizioni IP protegge un'applicazione Web dagli attacchi comuni.|
|Servizio app|10|Do not allow all ('*') resources to access your application (Non consentire a tutte le risorse ('*') di accedere all'applicazione)| Non consentire l'impostazione del parametro WEBSITE_LOAD_CERTIFICATES su "". L'impostazione del parametro su " comporta che tutti i certificati verranno caricati nell'archivio certificati personale delle applicazioni Web. Ciò può causare un abuso del principio dei privilegi minimi poiché è improbabile che il sito necessiti dell'accesso a tutti i certificati in fase di esecuzione.|
|Servizio app|20|CORS non deve consentire a tutte le risorse di accedere alle applicazioni Web|Consentire solo ai domini richiesti di interagire con l'applicazione Web. Condivisione di risorse tra le origini (CORS) non deve consentire a tutti i domini di accedere all'applicazione Web.|
|Servizio app|20|Condivisione risorse tra le origini non deve consentire a tutte le risorse di accedere all'app per le funzioni dell'utente| Consentire solo ai domini richiesti di interagire con l'app per le funzioni. Condivisione di risorse tra le origini (CORS) non deve consentire a tutti i domini di accedere all'applicazione per le funzioni.|
|Risorse di calcolo (Batch)|1|Configurare regole di avviso relative alle metriche nell'account Batch|Configurare regole di avviso relative alle metriche nell'account Batch e abilitare le metriche Eventi eliminazione pool completati ed Eventi eliminazione pool avviati|
|Risorse di calcolo (Service Fabric)|10|Usare Azure Active Directory per l'autenticazione client in Service Fabric|Eseguire l'autenticazione client solo tramite Azure Active Directory in Service Fabric.|
|Risorse di calcolo (account di automazione)|5| Abilitare la crittografia dell'account di Automazione|Abilitare la crittografia degli asset della variabile dell'account di Automazione quando si archiviano dati sensibili.|
|Calcolo delle risorse (Load Balancer)|5|Abilitare i log di diagnostica in Load Balancer|Abilitare i log e conservarli fino a un anno. Questo consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. |
|Risorse di calcolo (Ricerca)|5|Abilitare i log di diagnostica nel servizio Ricerca|Abilitare i log e conservarli fino a un anno. Questo consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. |
|Risorse di calcolo (bus di servizio)|5|Abilitare i log di diagnostica nel bus di servizio|Abilitare i log e conservarli fino a un anno. Questo consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. |
|Calcolo delle risorse (Analisi di flusso)|5|Abilitare i log di diagnostica in Analisi di flusso di Azure|Abilitare i log e conservarli fino a un anno. Questo consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. |
|Risorse di calcolo (Service Fabric)|5|Abilitare i log di diagnostica in Service Fabric|Abilitare i log e conservarli fino a un anno. Questo consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. |
|Risorse di calcolo (Batch)|5|Abilitare i log di diagnostica negli account Batch|Abilitare i log e conservarli fino a un anno. Questo consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. |
|Risorse di calcolo (Hub eventi)|5|Abilitare i log di diagnostica in Hub eventi|Abilitare i log e conservarli fino a un anno. Questo consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. |
|Risorse di calcolo (App per la logica)|5|Abilitare i log di diagnostica in App per la logica|Abilitare i log e conservarli fino a un anno. Questo consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. |
|Risorse di calcolo (Service Fabric)|15|Impostare la proprietà ClusterProtectionLevel su EncryptAndSign in Service Fabric|Service Fabric offre tre livelli di protezione (None, Sign ed EncryptAndSign) per la comunicazione da nodo a nodo mediante un certificato cluster primario.  Impostare il livello di protezione per garantire che tutti i messaggi da nodo a nodo vengano crittografati e firmati digitalmente. |
|Risorse di calcolo (bus di servizio)|1|Rimuovere tutte le regole di autorizzazione ad eccezione di RootManageSharedAccessKey dallo spazio dei nomi del bus di servizio |I client del bus di servizio non devono usare un criterio di accesso a livello di spazio dei nomi che assicura l'accesso a tutte le code e gli argomenti in uno spazio dei nomi. Per allinearsi al modello di sicurezza con privilegi minimi, è opportuno creare criteri di accesso a livello di entità per code e argomenti per assicurare l'accesso solo all'entità specifica.|
|Risorse di calcolo (Hub eventi)|1|Rimuovere tutte le regole di autorizzazione ad eccezione di RootManageSharedAccessKey dallo spazio dei nomi di Hub eventi |I client di Hub eventi non devono usare un criterio di accesso a livello di spazio dei nomi che assicura l'accesso a tutte le code e gli argomenti in uno spazio dei nomi. Per allinearsi al modello di sicurezza con privilegi minimi, è opportuno creare criteri di accesso a livello di entità per code e argomenti per assicurare l'accesso solo all'entità specifica.|
|Risorse di calcolo (Hub eventi)|5|Definire le regole di autorizzazione nell'entità di Hub eventi|Controllare regole di autorizzazione nell'entità di Hub eventi per concedere l'accesso con privilegi minimi.|
|Machine|50|Installare l'agente di monitoraggio nei computer|Installare l'agente di monitoraggio per abilitare la raccolta di dati, la ricerca di aggiornamenti, l'analisi della baseline e la protezione degli endpoint in ogni computer.|
|Machine|50|Abilitare il provisioning automatico e la raccolta dei dati per le sottoscrizioni |Abilitare il provisioning automatico e la raccolta dei dati per i computer nelle sottoscrizioni per abilitare la raccolta dei dati, la ricerca di aggiornamenti, l'analisi della baseline e la protezione degli endpoint in ogni computer aggiunto alle sottoscrizioni.|
|Machine|40|Risolvere i problemi di integrità dell'agente di monitoraggio nei computer|Per la protezione completa del Centro sicurezza, risolvere i problemi dell'agente di monitoraggio nei computer seguendo le istruzioni nella Guida alla risoluzione dei problemi| 
|Machine|40|Risolvere i problemi di integrità della protezione degli endpoint nei computer|Per la protezione completa del Centro sicurezza, risolvere i problemi dell'agente di monitoraggio nei computer seguendo le istruzioni nella Guida alla risoluzione dei problemi.|
|Machine|40|Risolvere il problema dei dati di analisi mancanti nei computer|Risolvere il problema dei dati di analisi mancanti nelle macchine virtuali e nei computer. L'assenza di dati di analisi nei computer determina l'impossibilità di usufruire di valutazioni sulla sicurezza, ad esempio la ricerca di aggiornamenti, l'analisi della baseline e la ricerca di soluzioni di protezione degli endpoint.|
|Machine|40|Installare gli aggiornamenti del sistema nei computer|Installare gli aggiornamenti mancanti per la sicurezza del sistema e critici per proteggere i computer e le macchine virtuali Windows e Linux
|Machine|15|Aggiungere un Web Application Firewall| Aggiungere una soluzione WAF (web application firewall) per proteggere le applicazioni Web. |
|Machine|40|Aggiornare la versione del sistema operativo per i ruoli del servizio cloud|Aggiornare la versione del sistema operativo per i ruoli del servizio cloud alla versione più recente disponibile per la famiglia del sistema operativo.|
|Machine|35|Correggere le vulnerabilità nella configurazione della sicurezza nei computer|Risolvere le vulnerabilità nella configurazione della sicurezza dei computer per proteggerli da attacchi. |
|Machine|35|Risolvere le vulnerabilità nella configurazione della sicurezza nei contenitori|Risolvere le vulnerabilità nella configurazione della sicurezza nei computer in cui è installato Docker per proteggerli da attacchi.|
|Machine|25|Abilitare i controlli applicazioni adattivi|Abilitare il controllo delle applicazioni per controllare quali applicazioni possono essere eseguite nelle macchine virtuali in Azure. Sarà così possibile rafforzare la protezione delle macchine virtuali dai malware. Il Centro sicurezza usa l'apprendimento automatico per analizzare le applicazioni in esecuzione in ogni macchina virtuale e, grazie a questa funzionalità intelligente, consente di applicare regole di autorizzazione. Questa funzionalità semplifica il processo di configurazione e gestione delle regole di autorizzazione delle applicazioni.|
|Machine|20|Installare la soluzione di protezione degli endpoint nei computer|Installare una soluzione di protezione degli endpoint nelle macchine virtuali per proteggerli da minacce e vulnerabilità.|
|Machine|20|Riavviare i computer per applicare gli aggiornamenti del sistema|Riavviare i computer per applicare gli aggiornamenti del sistema e proteggere i computer dalle vulnerabilità.|
|Machine|15|Applicare Crittografia dischi alle macchine virtuali|Crittografare i dischi delle macchine virtuali con Crittografia dischi di Azure per macchine virtuali Windows e Linux. Crittografia dischi di Azure sfrutta la funzionalità standard di settore BitLocker di Windows e la funzionalità DM-Crypt di Linux per offrire la crittografia del sistema operativo e del disco dati per proteggere e salvaguardare i dati e rispettare gli impegni in termini di sicurezza e conformità dell'organizzazione nell'insieme di credenziali delle chiavi di Azure del cliente. Se il requisito di conformità e sicurezza prevede la crittografia dei dati end-to-end tramite le chiavi di crittografia, tra cui la crittografia del disco temporaneo collegato in locale, usare Crittografia dischi di Azure. In alternativa, per impostazione predefinita, il servizio Managed Disks viene crittografato con dischi inattivi per impostazione predefinita usando la crittografia del servizio di archiviazione di Azure in cui le chiavi di crittografia sono chiavi gestite da Microsoft in Azure. Se questa condizione soddisfa i requisiti di sicurezza e conformità, è possibile usare la crittografia di Managed Disks predefinita.|
|Machine|30|Installare una soluzione di valutazione della vulnerabilità nelle macchine virtuali|Installare una soluzione di valutazione della vulnerabilità nelle macchine virtuali|
|Machine|15|Aggiungere un Web Application Firewall| Aggiungere una soluzione WAF (web application firewall) per proteggere le applicazioni Web. |
|Machine|30|Risolvere le vulnerabilità tramite una soluzione di valutazione della vulnerabilità|Le macchine virtuali per cui viene distribuita una soluzione di terze parti per la valutazione della vulnerabilità vengono costantemente controllate per individuare vulnerabilità del sistema operativo e delle applicazioni. Ogni volta che vengono individuate queste vulnerabilità, sono disponibili altre informazioni nell'ambito dell'indicazione.|
|Machine|30|Installare una soluzione di valutazione della vulnerabilità nelle macchine virtuali|Installare una soluzione di valutazione della vulnerabilità nelle macchine virtuali|
|Machine|1|Eseguire la migrazione delle macchine virtuali alle nuove risorse di Azure Resource Manager|Usare Azure Resource Manager per le macchine virtuali per fornire funzionalità di sicurezza migliorate quali controllo di accesso (Controllo degli accessi in base al ruolo) più avanzato, controllo migliore, distribuzione e governance basate su Resource Manager, accesso alle identità gestite, accesso all'insieme di credenziali delle chiavi per i segreti, autenticazione basata su Azure AD e supporto di tag e gruppi di risorse per una gestione della sicurezza semplificata. |
|Machine|30|Risolvere le vulnerabilità tramite una soluzione di valutazione della vulnerabilità|Le macchine virtuali per cui viene distribuita una soluzione di terze parti per la valutazione della vulnerabilità vengono costantemente controllate per individuare vulnerabilità del sistema operativo e delle applicazioni. Ogni volta che vengono individuate queste vulnerabilità, sono disponibili altre informazioni nell'ambito dell'indicazione.|
|Set di scalabilità di macchine virtuali |4|Abilitare i log di diagnostica nei set di scalabilità di macchine virtuali|Abilitare i log e conservarli per un periodo massimo di un anno. In questo modo è possibile ricreare la traccia delle attività per scopi di analisi. Ciò è utile quando si verifica un evento imprevisto di sicurezza o la rete è compromessa.|
|Set di scalabilità di macchine virtuali|35|Risolvere le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali|Risolvere le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali per proteggerli da attacchi. |
|Set di scalabilità di macchine virtuali|5|Risolvere i problemi di integrità della protezione degli endpoint nei set di scalabilità di macchine virtuali|Risolvere i problemi di integrità della protezione degli endpoint nei set di scalabilità di macchine virtuali per proteggerli da minacce e vulnerabilità. |
|Set di scalabilità di macchine virtuali|10|Installare la soluzione di protezione degli endpoint nei set di scalabilità di macchine virtuali|Installare una soluzione di protezione degli endpoint nei set di scalabilità di macchine virtuali per proteggerli da minacce e vulnerabilità. |
|Set di scalabilità di macchine virtuali|40|Installare gli aggiornamenti del sistema nei set di scalabilità di macchine virtuali|Installare gli aggiornamenti mancanti per la sicurezza del sistema e critici per proteggere i set di scalabilità di macchine virtuali Windows e Linux. |
 





## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle raccomandazioni applicabili ad altri tipi di risorse di Azure, vedere gli argomenti seguenti:


* [Comprendere le raccomandazioni del Centro sicurezza di Azure per le macchine virtuali](security-center-virtual-machine-recommendations.md)
* [Monitoraggio dell'identità e dell'accesso nel Centro sicurezza di Azure](security-center-identity-access.md)
* [Protezione della rete nel Centro sicurezza di Azure](security-center-network-recommendations.md)
* [Protezione del servizio SQL di Azure nel Centro sicurezza di Azure](security-center-sql-service-recommendations.md)

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](tutorial-security-policy.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.

