---
title: Distribuire automaticamente gli agenti per il Centro sicurezza di Azure | Microsoft Docs
description: Questo articolo descrive come configurare il provisioning automatico dell'agente di Log Analytics e di altri agenti usati dal Centro sicurezza di Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 11/15/2020
ms.author: memildin
ms.openlocfilehash: 7d96b03598f90b45b7ecf88027be7408d8f161ea
ms.sourcegitcommit: 18046170f21fa1e569a3be75267e791ca9eb67d0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2020
ms.locfileid: "94638768"
---
# <a name="auto-provisioning-agents-and-extensions-from-azure-security-center"></a>Provisioning automatico di agenti ed estensioni del Centro sicurezza di Azure

Il Centro sicurezza raccoglie i dati dalle macchine virtuali (VM) di Azure, dai set di scalabilità di macchine virtuali, dai contenitori IaaS e dai computer non Azure (inclusi quelli locali) per monitorare le vulnerabilità e le minacce per la sicurezza. 

La raccolta dei dati è fondamentale per ottenere visibilità sugli aggiornamenti mancanti, le impostazioni di sicurezza del sistema operativo non configurate correttamente, lo stato della protezione degli endpoint e la protezione dell'integrità e dalle minacce. La raccolta dei dati è necessaria solo per le risorse di calcolo (macchine virtuali, set di scalabilità di macchine virtuali, contenitori IaaS e computer non di Azure). È possibile usufruire dei vantaggi del Centro sicurezza di Azure anche se non si esegue il provisioning di agenti. La sicurezza sarà tuttavia limitata e le funzionalità sopra elencate non saranno supportate.  

I dati vengono raccolti tramite:

- L'**agente di Log Analytics**, che legge diverse configurazioni correlate alla sicurezza oltre ai log eventi del computer e copia i dati nell'area di lavoro per l'analisi. I dati raccolti sono ad esempio il tipo di sistema operativo e la versione, i log del sistema operativo (log eventi Windows), i processi in esecuzione, il nome del computer, gli indirizzi IP e l'utente connesso.
- **Estensioni di sicurezza**, come il [componente aggiuntivo Criteri di Azure per Kubernetes](../governance/policy/concepts/policy-for-kubernetes.md), che possono anche fornire dati al Centro sicurezza riguardo a specifici tipi di risorse.

> [!TIP]
> Con la crescita del Centro sicurezza, sono aumentati anche i tipi di risorse che è possibile monitorare, oltre al numero di estensioni. Il provisioning automatico è stato ampliato per supportare tipi di risorsa aggiuntivi sfruttando le funzionalità di Criteri di Azure.

:::image type="content" source="./media/security-center-enable-data-collection/auto-provisioning-options.png" alt-text="Pagina di impostazioni del provisioning automatico del Centro sicurezza":::


## <a name="why-use-auto-provisioning"></a>Perché usare il provisioning automatico?
Le estensioni e gli agenti descritti in questa pagina *possono* essere installati manualmente. Vedere [Installazione manuale dell'agente di Log Analytics](#manual-agent). Tuttavia, il **provisioning automatico** riduce il sovraccarico di gestione installando tutti gli agenti necessari e le estensioni nei computer nuovi ed esistenti per garantire una copertura di sicurezza più rapida per tutte le risorse supportate. 

È consigliabile abilitare il provisioning automatico, anche se è disabilitato per impostazione predefinita.

## <a name="how-does-auto-provisioning-work"></a>Come funziona il provisioning automatico?
Le impostazioni del provisioning automatico del Centro sicurezza includono un interruttore per ogni tipo di estensione supportata. Quando si abilita il provisioning automatico di un'estensione, si assegna il criterio **DeployIfNotExists** appropriato per assicurarsi che il provisioning dell'estensione venga effettuato in tutte le risorse attuali e future di quel tipo.

> [!TIP]
> Per altre informazioni sugli effetti di Criteri di Azure, incluso DeployIfNotExists, vedere [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md).

## <a name="enable-auto-provisioning-of-the-log-analytics-agent"></a>Abilitare il provisioning automatico dell'agente di Log Analytics <a name="auto-provision-mma"></a>
Quando si attiva il provisioning automatico per l'agente di Log Analytics, il Centro sicurezza lo distribuisce in tutte le VM di Azure supportate e in tutte quelle nuove create. Per l'elenco delle piattaforme supportate, vedere [Supported platforms in Azure Security Center](security-center-os-coverage.md) (Piattaforme supportate nel Centro sicurezza di Azure).

Per abilitare il provisioning automatico dell'agente di Log Analytics:

1. Scegliere **Prezzi e impostazioni** dal menu del Centro sicurezza.
1. Selezionare la sottoscrizione pertinente.
1. Nella pagina **Provisioning automatico** impostare lo stato dell'agente su **Attivato**.
1. Nel riquadro delle opzioni di configurazione definire l'area di lavoro da usare.

    :::image type="content" source="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png" alt-text="Opzioni di configurazione per il provisioning automatico degli agenti di Log Analytics nelle VM" lightbox="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png":::

    - **Connect Azure VMs to the default workspace(s) created by Security Center** (Connetti le VM di Azure a una o più aree di lavoro predefinite create dal Centro sicurezza): il Centro sicurezza crea un nuovo gruppo di risorse e un'area di lavoro predefinita nella stessa area geografica e connette l'agente a tale area di lavoro. Se una sottoscrizione contiene VM di più aree geografiche, il Centro sicurezza crea più aree di lavoro per garantire la conformità ai requisiti di privacy dei dati.

        Ecco le convenzioni di denominazione per l'area di lavoro e il gruppo di risorse: 
        - Area di lavoro: DefaultWorkspace-[subscription-ID]-[geo] 
        - Gruppo di risorse: DefaultResourceGroup-[geo] 

        Nell'area di lavoro viene automaticamente abilitata una soluzione del Centro sicurezza in base al piano tariffario impostato per la sottoscrizione. 

        > [!TIP]
        > Per eventuali domande sulle aree di lavoro predefinite, vedere:
        >
        > - [Vengono addebitati costi per i log di Monitoraggio di Azure nelle aree di lavoro create dal Centro sicurezza?](faq-data-collection-agents.md#am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center)
        > - [Dove viene creata l'area di lavoro predefinita di Log Analytics?](faq-data-collection-agents.md#where-is-the-default-log-analytics-workspace-created)
        > - [È possibile eliminare le aree di lavoro predefinite create dal Centro sicurezza?](faq-data-collection-agents.md#can-i-delete-the-default-workspaces-created-by-security-center)

    - **Connect Azure VMs to a different workspace** (Connetti le VM di Azure a un'area di lavoro diversa): nell'elenco a discesa selezionare l'area di lavoro in cui archiviare i dati raccolti. L'elenco a discesa include tutte le aree di lavoro di tutte le sottoscrizioni. È possibile usare questa opzione per raccogliere i dati dalle macchine virtuali in esecuzione in sottoscrizioni diverse e archiviarli tutti nell'area di lavoro selezionata.  

        Se è già disponibile un'area di lavoro Log Analytics, si può scegliere di usare la stessa (sono necessari autorizzazioni di lettura e scrittura per l'area di lavoro). Questa opzione è utile se nell'organizzazione è disponibile un'area di lavoro centralizzata che si vuole usare per la raccolta dati sulla sicurezza. Per altre informazioni, vedere [Gestire l'accesso ai dati di log e alle aree di lavoro in Monitoraggio di Azure](../azure-monitor/platform/manage-access.md).

        Se l'area di lavoro selezionata include già una soluzione Security o SecurityCenterFree abilitata, il piano tariffario verrà impostato automaticamente. In caso contrario, installare una soluzione del Centro sicurezza nell'area di lavoro:

        1. Scegliere **Prezzi e impostazioni** nel menu del Centro sicurezza.
        1. Selezionare l'area di lavoro a cui connettere gli agenti.
        1. Selezionare **Azure Defender - On** o **Azure Defender - Off**.

1. Nella configurazione **Eventi di sicurezza di Windows** selezionare la quantità di dati non elaborati sugli eventi da archiviare:
    - **Nessuno**: disabilita l'archiviazione degli eventi di sicurezza. Si tratta dell'impostazione predefinita.
    - **Minimi**: un piccolo set di eventi nel caso i cui si voglia ridurne al minimo il volume.
    - **Comuni**: un set di eventi che soddisfa la maggior parte dei clienti e fornisce un audit trail completo.
    - **Tutti gli eventi**: per i clienti che vogliono assicurarsi che tutti gli eventi vengano archiviati.

    > [!TIP]
    > Per impostare queste opzioni a livello di area di lavoro, vedere [Impostazione dell'opzione per gli eventi di sicurezza a livello di area di lavoro](#setting-the-security-event-option-at-the-workspace-level).
    > 
    > Per altre informazioni su queste opzioni, vedere [Opzioni degli eventi di sicurezza di Windows per l'agente di Log Analytics](#data-collection-tier).

1. Selezionare **Applica** nel riquadro di configurazione.

1. Selezionare **Salva**. Se è necessario eseguire il provisioning di un'area di lavoro, l'installazione dell'agente potrebbe richiedere fino a 25 minuti.

1. Verrà chiesto se si vogliono riconfigurare le VM monitorate che in precedenza erano connesse a un'area di lavoro predefinita:

    :::image type="content" source="./media/security-center-enable-data-collection/reconfigure-monitored-vm.png" alt-text="Rivedere le opzioni per riconfigurare le VM monitorate":::

    - **No**: le impostazioni della nuova area di lavoro si applicano solo alle nuove VM individuate in cui non è installato l'agente di Log Analytics.
    - **Sì**: le impostazioni della nuova area di lavoro verranno applicate a tutte le VM e ogni VM attualmente connessa a un'area di lavoro creata dal Centro sicurezza verrà riconnessa alla nuova area di lavoro di destinazione.

   > [!NOTE]
   > Se si seleziona **Sì**, non eliminare le aree di lavoro create dal Centro sicurezza finché tutte le macchine virtuali non vengono riconnesse alla nuova area di lavoro di destinazione. Questa operazione non riesce se un'area di lavoro viene eliminata troppo presto.


## <a name="enable-auto-provisioning-of-extensions"></a>Abilitare il provisioning automatico delle estensioni

Per abilitare il provisioning automatico di un'estensione diversa dall'agente di Log Analytics: 

1. Nel menu del Centro sicurezza nel portale di Azure selezionare **Prezzi e impostazioni**.
1. Selezionare la sottoscrizione pertinente.
1. Selezionare **Provisioning automatico**.
1. Se si abilita il provisioning automatico per Microsoft Dependency Agent, assicurarsi che anche l'agente di Log Analytics sia impostato per la distribuzione automatica. 
1. Impostare lo stato su **Attivato** per l'estensione appropriata.

    :::image type="content" source="./media/security-center-enable-data-collection/toggle-kubernetes-add-on.png" alt-text="Interruttore per abilitare il provisioning automatico per il componente aggiuntivo Criteri per Kubernetes":::

1. Selezionare **Salva**. Il criterio di Azure viene assegnato e viene creata un'attività di correzione.

    |Estensione  |Policy  |
    |---------|---------|
    |Componente aggiuntivo Criteri per Kubernetes|[Distribuire il componente aggiuntivo Criteri di Azure nei cluster del servizio Azure Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fa8eff44f-8c92-45c3-a3fb-9880802d67a7)|
    |Microsoft Dependency Agent (anteprima) (VM Windows)|[Distribuisci Dependency Agent per le macchine virtuali Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f1c210e94-a481-4beb-95fa-1571b434fb04)         |
    |Microsoft Dependency Agent (anteprima) (VM Linux)|[Distribuisci Dependency Agent per le macchine virtuali Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da21710-ce6f-4e06-8cdb-5cc4c93ffbee)|



## <a name="windows-security-event-options-for-the-log-analytics-agent"></a>Opzioni degli eventi di sicurezza di Windows per l'agente di Log Analytics <a name="data-collection-tier"></a> 

La selezione di un livello di raccolta dati nel Centro sicurezza di Azure ha effetto solo sull'*archiviazione* degli eventi di sicurezza nell'area di lavoro Log Analytics. L'agente di Log Analytics continuerà a raccogliere e analizzare gli eventi di sicurezza necessari ai fini della protezione dalle minacce del Centro sicurezza di Azure, indipendentemente dal livello scelto per l'archiviazione nell'area di lavoro. La scelta di archiviare gli eventi di sicurezza consente l'esecuzione di attività di analisi, ricerca e controllo di tali eventi nell'area di lavoro.

### <a name="requirements"></a>Requisiti 
Per archiviare i dati degli eventi di sicurezza di Windows, è necessario Azure Defender. Vedere[altre informazioni su Azure Defender](azure-defender.md).

L'archiviazione di dati in Log Analytics può comportare costi aggiuntivi. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/).

### <a name="information-for-azure-sentinel-users"></a>Informazioni per gli utenti di Azure Sentinel 
Utenti di Azure Sentinel: si noti che la raccolta di eventi di sicurezza nel contesto di una singola area di lavoro può essere configurata nel Centro sicurezza di Azure o in Azure Sentinel, ma non in entrambi. Se si prevede di aggiungere Azure Sentinel a un'area di lavoro che riceve già gli avvisi di Azure Defender dal Centro sicurezza di Azure ed è impostata per la raccolta di eventi di sicurezza, sono disponibili due opzioni:
- Lasciare la raccolta di eventi di sicurezza nel Centro sicurezza di Azure così com'è. Sarà possibile eseguire query e analizzare questi eventi in Azure Sentinel oltre che in Azure Defender. Non sarà tuttavia possibile monitorare lo stato di connettività del connettore o cambiarne la configurazione in Azure Sentinel. Se queste operazioni sono importanti, considerare la seconda opzione.
- Disabilitare la raccolta di eventi di sicurezza nel Centro sicurezza di Azure impostando **Eventi di sicurezza di Windows** su **Nessuno** nella configurazione dell'agente di Log Analytics. Aggiungere quindi il connettore Eventi di sicurezza in Azure Sentinel. Come per la prima opzione, sarà possibile eseguire query e analizzare gli eventi sia in Azure Sentinel che in Azure Defender/Centro sicurezza di Azure, ma ora sarà possibile monitorare lo stato di connettività del connettore o cambiarne la configurazione in Azure Sentinel (e solo in Azure Sentinel).

### <a name="what-event-types-are-stored-for-common-and-minimal"></a>Quali tipi di eventi vengono archiviati per le opzioni "Comuni" e "Minimi"?
Questi insiemi sono stati progettati per soddisfare gli scenari tipici. Assicurarsi di valutare quale di questi si adatti alle proprie esigenze prima di implementarlo.

Per determinare gli eventi associati alle opzioni **Comuni** e **Minimi**, abbiamo collaborato con i clienti e usato standard di settore per identificare la frequenza non filtrata di ogni evento e il relativo utilizzo. In questo processo sono state usate le linee guida seguenti:

- **Minimi**: assicurarsi che questo insieme includa solo gli eventi che potrebbero indicare una violazione riuscita ed eventi importanti con un volume molto basso. Ad esempio, questo insieme contiene gli accessi utente riusciti e non (ID evento 4624, 4625), ma non contiene le disconnessioni (importanti per il controllo ma non per il rilevamento) e ha un volume relativamente alto. La maggior parte del volume di dati di questo insieme è composto da eventi di accesso e da eventi di creazione del processo (ID evento 4688).
- **Comuni**: fornire un audit trail completo degli utenti in questo insieme. Ad esempio, questo insieme contiene sia gli accessi utente che le disconnessioni dell'utente (ID evento 4634). Sono incluse azioni di controllo quali modifiche al gruppo di sicurezza, operazioni Kerberos del controller del dominio principale e altri eventi consigliati dalle organizzazioni del settore.

Gli eventi che hanno un volume molto basso sono stati inclusi nell'insieme Comuni. Questo insieme è stato scelto perché l'obiettivo è quello di ridurre il volume, non di filtrare eventi specifici.

Di seguito è riportata una suddivisione completa degli ID di eventi di sicurezza e App Locker per ogni insieme:

| Livello dati | Indicatori di eventi raccolti |
| --- | --- |
| Minime | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Comuni | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - Se si usa l'oggetto Criteri di gruppo, è consigliabile abilitare i criteri di controllo della creazione del processo dell'evento 4688 e il campo *CommandLine* all'interno dell'evento 4688. Per altre informazioni sulla creazione del processo dell'evento 4688, vedere le [domande frequenti](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled) del Centro sicurezza. Per altre informazioni su questi criteri di controllo, vedere [Suggerimenti per i criteri di controllo](/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Per abilitare la raccolta dei dati per i [controlli applicazione adattivi](security-center-adaptive-application.md), Centro sicurezza consente di configurare criteri AppLocker locali in modalità di controllo per consentire tutte le applicazioni. In questo modo AppLocker genererà eventi che vengono poi raccolti e sfruttati dal Centro sicurezza. È importante notare che questi criteri non saranno configurati nei computer in cui è già configurato un criterio AppLocker. 
> - Per raccogliere l'[ID evento 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156) della piattaforma filtri Windows, è necessario abilitare [Controlla Connessione a Piattaforma filtro Windows](/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol /set /subcategory:"Connessione a Piattaforma filtro" /Success:Enable)
>

### <a name="setting-the-security-event-option-at-the-workspace-level"></a>Impostazione dell'opzione per gli eventi di sicurezza a livello di area di lavoro

È possibile definire il livello dei dati sugli eventi di sicurezza da archiviare a livello di area di lavoro.

1. Nel menu del Centro sicurezza nel portale di Azure selezionare **Prezzi e impostazioni**.
1. Selezionare l'area di lavoro appropriata. Gli unici eventi della raccolta dati per un'area di lavoro sono gli eventi di sicurezza di Windows descritti in questa pagina.

    :::image type="content" source="media/security-center-enable-data-collection/event-collection-workspace.png" alt-text="Impostazione degli dati sugli eventi di sicurezza da archiviare in un'area di lavoro":::

1. Selezionare la quantità di dati non elaborati sugli eventi da archiviare, quindi selezionare **Salva**.

## <a name="manual-agent-provisioning"></a>Provisioning manuale dell'agente <a name="manual-agent"></a>
 
Per installare manualmente l'agente di Log Analytics:

1. Disabilitare il provisioning automatico.

1. Facoltativamente, creare un'area di lavoro.

1. Abilitare Azure Defender nell'area di lavoro in cui si installa l'agente di Log Analytics:

    1. Scegliere **Prezzi e impostazioni** dal menu del Centro sicurezza.

    1. Impostare l'area di lavoro in cui si installa l'agente. Assicurarsi che l'area di lavoro sia inclusa nella stessa sottoscrizione che si usa nel Centro sicurezza e di avere le autorizzazioni di lettura/scrittura per l'area di lavoro.

    1. Selezionare **Azure Defender - On** e quindi **Salva**.

       >[!NOTE]
       >Se l'area di lavoro dispone già di una soluzione **Security**  o **SecurityCenterFree** abilitata, il piano tariffario verrà impostato automaticamente. 

1. Per distribuire gli agenti in nuove VM con un modello di Resource Manager, installare l'agente di Log Analytics:

   - [Installare l'agente di Log Analytics per Windows](../virtual-machines/extensions/oms-windows.md)
   - [Installare l'agente di Log Analytics per Linux](../virtual-machines/extensions/oms-linux.md)

1. Per distribuire gli agenti nelle VM esistenti, seguire le istruzioni riportate in [Raccogliere dati sulle macchine virtuali di Azure](../azure-monitor/learn/quick-collect-azurevm.md) (la sezione **Raccogliere dati su eventi e prestazioni** è facoltativa).

1. Per usare PowerShell per distribuire l'estensione, seguire le istruzioni indicate nella documentazione delle macchine virtuali:

    - [Per macchine Windows](../virtual-machines/extensions/oms-windows.md?toc=%252fazure%252fazure-monitor%252ftoc.json#powershell-deployment)
    - [Per computer Linux](../virtual-machines/extensions/oms-linux.md?toc=%252fazure%252fazure-monitor%252ftoc.json#azure-cli-deployment)

> [!TIP]
> Per istruzioni su come caricare il Centro sicurezza usando PowerShell, vedere [Automate onboarding of Azure Security Center using PowerShell](security-center-powershell-onboarding.md) (Automatizzare l'onboarding del Centro sicurezza di Azure usando PowerShell).


## <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>Provisioning automatico nel caso di installazione di un agente preesistente <a name="preexisting"></a> 

I casi d'uso seguenti specificano il funzionamento del provisioning automatico nei casi in cui sia già installato un agente o un'estensione. 

- **L'agente di Log Analytics è installato nel computer, ma non come estensione (agente diretto)** : se l'agente di Log Analytics viene installato direttamente nella VM e non come estensione di Azure, il Centro sicurezza installerà l'estensione dell'agente di Log Analytics e potrebbe aggiornare l'agente all'ultima versione.
L'agente installato continuerà a generare report per le aree di lavoro già configurate e in più genererà report per l'area di lavoro configurata nel Centro sicurezza (il multihoming è supportato nei computer Windows).
Se l'area di lavoro configurata è un'area di lavoro utente e non l'area di lavoro predefinita del Centro sicurezza, sarà necessario installare la soluzione "security" o "securityFree" su di essa in modo che il Centro sicurezza avvii l'elaborazione degli eventi provenienti dalle macchine virtuali e dai computer che inviano report a tale area di lavoro.

    Per i computer Linux, il multihoming dell'agente non è ancora supportato. Di conseguenza, se viene rilevata un'installazione di un agente esistente, il provisioning automatico non verrà eseguito e la configurazione del computer non verrà modificata.

    Per i computer esistenti in sottoscrizioni di cui è stato eseguito l'onboarding nel Centro sicurezza prima del 17 marzo 2019, quando verrà rilevato un agente esistente, l'estensione dell'agente di Log Analytics non verrà installata e il computer non verrà modificato. Per questi computer, vedere la raccomandazione "Risolvere i problemi di integrità dell'agente di monitoraggio nei computer" per risolvere i problemi di installazione dell'agente.
  
- **L'agente di System Center Operations Manager è installato nel computer**: il Centro sicurezza installerà l'estensione dell'agente di Log Analytics in modalità affiancata con l'istanza esistente di Operations Manager. L'agente di Operations Manager esistente continuerà a inviare report normalmente al server Operations Manager. L'agente di Operations Manager e l'agente di Log Analytics condividono librerie di runtime comuni, che durante questo processo verranno aggiornate all'ultima versione. Se è installato l'agente Operations Manager versione 2012, **non** abilitare il provisioning automatico.

- **È presente un'estensione di VM preesistente**:
    - Quando l'agente di monitoraggio viene installato come estensione, la configurazione dell'estensione consente il reporting a una sola area di lavoro. Il Centro sicurezza non esegue l'override delle connessioni esistenti alle aree di lavoro degli utenti. Il Centro sicurezza archivierà i dati di sicurezza dalla macchina virtuale nell'area di lavoro già connessa, a condizione che vi sia installata la soluzione "security" o "securityFree". Durante questo processo, il Centro sicurezza può aggiornare l'estensione all'ultima versione.  
    - Per vedere a quale area di lavoro l'estensione esistente invia i dati, eseguire il test per [convalidare la connettività con il Centro sicurezza di Azure](/archive/blogs/yuridiogenes/validating-connectivity-with-azure-security-center). In alternativa, è possibile aprire le aree di lavoro Log Analytics, selezionare un'area di lavoro, selezionare la macchina virtuale ed esaminare la connessione dell'agente di Log Analytics. 
    - Se si dispone di un ambiente in cui l'agente di Log Analytics è installato in workstation client e invia report a un'area di lavoro Log Analytics esistente, esaminare l'elenco di [sistemi operativi supportati dal Centro sicurezza di Azure](security-center-os-coverage.md) per assicurarsi che il sistema operativo in uso sia supportato. Per altre informazioni, vedere [Clienti di Log Analytics esistenti](./faq-azure-monitor-logs.md).
 

## <a name="disable-auto-provisioning"></a>Disabilitare il provisioning automatico <a name="offprovisioning"></a>

Se si disabilita il provisioning automatico, il provisioning degli agenti non verrà effettuato nelle nuove VM.

Per disattivare il provisioning automatico di un agente:

1. Dal menu del Centro sicurezza nel portale selezionare **Prezzi e impostazioni**.
1. Selezionare la sottoscrizione pertinente.
1. Selezionare **Provisioning automatico**.
1. Impostare lo stato su **Disattivato** per l'agente appropriato.

    :::image type="content" source="./media/security-center-enable-data-collection/agent-toggles.png" alt-text="Interruttori per disabilitare il provisioning automatico in base al tipo di agente":::

1. Selezionare **Salva**. Quando il provisioning automatico è disabilitato, la sezione di configurazione dell'area di lavoro predefinita non viene visualizzata:

    :::image type="content" source="./media/security-center-enable-data-collection/empty-configuration-column.png" alt-text="Se il provisioning automatico è disabilitato, la cella di configurazione è vuota":::


> [!NOTE]
>  La disabilitazione del provisioning automatico non implica la rimozione dell'agente di Log Analytics dalle macchine virtuali di Azure in cui è stato eseguito il provisioning dell'agente. Per informazioni sulla rimozione dell'estensione OMS, vedere [How do I remove OMS extensions installed by Security Center](faq-data-collection-agents.md#remove-oms) (Come si rimuovono le estensioni OMS installate dal Centro sicurezza).
>


## <a name="troubleshooting"></a>Risoluzione dei problemi

-   Per identificare i problemi relativi all'installazione del provisioning automatico, vedere [Problemi di integrità di Microsoft Monitoring Agent](security-center-troubleshooting-guide.md#mon-agent).

-  Per identificare i requisiti di rete di Monitoring Agent, vedere [Risoluzione dei problemi di rete di Microsoft Monitoring Agent](security-center-troubleshooting-guide.md#mon-network-req).
-   Per identificare i problemi di onboarding manuale, vedere [How to troubleshoot Operations Management Suite onboarding issues](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues) (Come risolvere i problemi di onboarding di Operations Management Suite).

- Per identificare le VM non monitorate e i problemi dei computer:

    Una macchina virtuale o un computer non è monitorato dal Centro sicurezza se non esegue l'estensione dell'agente di Log Analytics. È possibile che in una macchina sia già installato un agente locale, ad esempio l'agente diretto di OMS o l'agente di System Center Operations Manager. Le macchine virtuali con questi agenti vengono identificate come non monitorate perché tali agenti non sono supportati completamente nel Centro sicurezza. Per sfruttare in modo ottimale i vantaggi di tutte le funzionalità del Centro sicurezza, è necessaria l'estensione dell'agente di Log Analytics.

    Per altre informazioni sui motivi per cui il Centro sicurezza non è in grado di monitorare correttamente macchine virtuali e computer inizializzati per il provisioning automatico, vedere [Problemi di integrità dell'agente di monitoraggio](security-center-troubleshooting-guide.md#mon-agent).




## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato il funzionamento della raccolta dati e del provisioning automatico nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere le pagine seguenti:

- [Domande frequenti sul Centro sicurezza di Azure](faq-general.md): domande frequenti sull'uso del servizio.
- [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.

Questo articolo descrive come installare un agente di Log Analytics e impostare un'area di lavoro Log Analytics in cui archiviare i dati raccolti. Entrambe le operazioni sono necessarie per consentire la raccolta dei dati. L'archiviazione dei dati in Log Analytics, indipendentemente dal fatto che si usi un'area di lavoro nuova o esistente, può comportare costi aggiuntivi. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/).

