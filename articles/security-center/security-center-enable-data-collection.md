---
title: Raccolta dati nel Centro sicurezza di Azure | Microsoft Docs
description: Questo articolo descrive come installare un agente di Log Analytics e impostare un'area di lavoro Log Analytics in cui archiviare i dati raccolti.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 10/08/2020
ms.author: memildin
ms.openlocfilehash: 68df6d6707ebe4f1a4b75a8005e746e2c1eba864
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341584"
---
# <a name="data-collection-in-azure-security-center"></a>Raccolta dati nel Centro sicurezza di Azure
Il Centro sicurezza raccoglie i dati dalle macchine virtuali (VM) di Azure, dai set di scalabilità di macchine virtuali, dai contenitori IaaS e dai computer non Azure (inclusi quelli locali) per monitorare le vulnerabilità e le minacce alla sicurezza. I dati vengono raccolti tramite l'agente di Log Analytics, che legge diverse configurazioni correlate alla sicurezza oltre ai log eventi del computer e copia i dati nell'area di lavoro per eseguire l'analisi. I dati raccolti sono ad esempio il tipo di sistema operativo e la versione, i log del sistema operativo (log eventi Windows), i processi in esecuzione, il nome del computer, gli indirizzi IP e l'utente connesso.

La raccolta dei dati è fondamentale per ottenere visibilità sugli aggiornamenti mancanti, le impostazioni di sicurezza del sistema operativo non configurate correttamente, lo stato della protezione degli endpoint e la protezione dell'integrità e dalle minacce. La raccolta dei dati è necessaria solo per le risorse di calcolo (macchine virtuali, set di scalabilità di macchine virtuali, contenitori IaaS e computer non di Azure). È possibile usufruire dei vantaggi del Centro sicurezza di Azure anche se non si esegue il provisioning di agenti. La sicurezza sarà tuttavia limitata e le funzionalità sopra elencate non saranno supportate.  

Questo articolo descrive come installare un agente di Log Analytics e impostare un'area di lavoro Log Analytics in cui archiviare i dati raccolti. Entrambe le operazioni sono necessarie per consentire la raccolta dei dati. L'archiviazione dei dati in Log Analytics, indipendentemente dal fatto che si usi un'area di lavoro nuova o esistente, può comportare costi aggiuntivi. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/).

> [!TIP]
> Per l'elenco delle piattaforme supportate, vedere [Supported platforms in Azure Security Center](security-center-os-coverage.md) (Piattaforme supportate nel Centro sicurezza di Azure).

## <a name="enable-automatic-provisioning-of-the-log-analytics-agent"></a>Abilitare il provisioning automatico dell'agente di Log Analytics <a name="auto-provision-mma"></a>

> [!NOTE]
> Utenti di Azure Sentinel: si noti che la raccolta di eventi di sicurezza nel contesto di una singola area di lavoro può essere configurata nel Centro sicurezza di Azure o in Azure Sentinel, ma non in entrambi. Se si prevede di aggiungere Azure Sentinel a un'area di lavoro che riceve già gli avvisi di Azure Defender dal Centro sicurezza di Azure ed è impostata per la raccolta di eventi di sicurezza, sono disponibili due opzioni:
> - Lasciare la raccolta di eventi di sicurezza nel Centro sicurezza di Azure così com'è. Sarà possibile eseguire query e analizzare questi eventi in Azure Sentinel oltre che in Azure Defender. Non sarà tuttavia possibile monitorare lo stato di connettività del connettore o cambiarne la configurazione in Azure Sentinel. Se queste operazioni sono importanti, considerare la seconda opzione.
>
> - [Disabilitare la raccolta di eventi di sicurezza](#data-collection-tier) nel Centro sicurezza di Azure e solo a quel punto aggiungere il connettore degli eventi di sicurezza in Azure Sentinel. Come per la prima opzione, sarà possibile eseguire query e analizzare gli eventi sia in Azure Sentinel che in Azure Defender/Centro sicurezza di Azure, ma ora sarà possibile monitorare lo stato di connettività del connettore o cambiarne la configurazione in Azure Sentinel (e solo in Azure Sentinel).


Per raccogliere i dati dai computer, è consigliabile avere installato l'agente di Log Analytics. L'installazione dell'agente può essere eseguita automaticamente (scelta consigliata) o manualmente. Per impostazione predefinita, il provisioning automatico è disattivato.

Dopo aver attivato il provisioning automatico, il Centro sicurezza distribuisce l'agente di Log Analytics in tutte le macchine virtuali di Azure supportate e nelle nuove macchine create. È consigliabile eseguire il provisioning automatico, ma è possibile installare l'agente manualmente, se necessario (vedere [Installazione manuale dell'agente di Log Analytics](#manual-agent)).

Una volta distribuito l'agente nei computer, il Centro sicurezza può offrire altri suggerimenti correlati allo stato di aggiornamento del sistema, alle configurazioni di sicurezza del sistema operativo e alla protezione di endpoint, nonché generare avvisi di sicurezza aggiuntivi.

Per abilitare il provisioning automatico dell'agente di Log Analytics:

1. Scegliere **Prezzi e impostazioni** dal menu del Centro sicurezza.
1. Selezionare la sottoscrizione pertinente.
1. Nella pagina **Raccolta dati** impostare **Provisioning automatico** su **Sì**.
1. Selezionare **Salva**.

    :::image type="content" source="./media/security-center-enable-data-collection/enable-automatic-provisioning.png" alt-text="Abilitare il provisioning automatico dell'agente di Log Analytics":::

    >[!TIP]
    > Se è necessario eseguire il provisioning di un'area di lavoro, l'installazione dell'agente potrebbe richiedere fino a 25 minuti.


## <a name="workspace-configuration"></a>Configurazione dell'area di lavoro
I dati raccolti dal Centro sicurezza vengono archiviati nell'area di lavoro Log Analytics. È possibile archiviare i dati raccolti dalle macchine virtuali di Azure nelle aree di lavoro create dal Centro sicurezza o in un'area di lavoro esistente creata dall'utente. 

La configurazione dell'area di lavoro è impostata in base alla sottoscrizione e più sottoscrizioni possono usare la stessa area di lavoro.

### <a name="using-a-workspace-created-by-security-center"></a>Uso di un'area di lavoro creata dal Centro sicurezza

Il Centro sicurezza può creare automaticamente un'area di lavoro predefinita in cui archiviare i dati. 

Per selezionare un'area di lavoro creata dal Centro sicurezza:

1. In **Configurazione dell'area di lavoro predefinita** selezionare l'opzione per usare un'altra area di lavoro creata dal Centro sicurezza.
    :::image type="content" source="./media/security-center-enable-data-collection/workspace-selection.png" alt-text="Abilitare il provisioning automatico dell'agente di Log Analytics"::: 

1. Fare clic su **Salva**.<br>
    Il Centro sicurezza crea un nuovo gruppo di risorse e un'area di lavoro predefinita in corrispondenza della posizione geografica specificata e quindi connette l'agente all'area di lavoro. Ecco le convenzioni di denominazione per l'area di lavoro e il gruppo di risorse:<br>
   **Area di lavoro: DefaultWorkspace-[subscription-ID]-[geo]<br> Gruppo di risorse: DefaultResourceGroup-[geo]**

   Se una sottoscrizione contiene macchine virtuali da più aree geografiche, il Centro sicurezza crea più aree di lavoro. Vengono create più aree di lavoro per gestire le regole sulla privacy dei dati.
1. Il Centro sicurezza abiliterà automaticamente una soluzione del Centro sicurezza nell'area di lavoro in base al piano tariffario impostato per la sottoscrizione. 

> [!NOTE]
> Il piano tariffario di Log Analytics per le aree di lavoro create dal Centro sicurezza non influisce sulla fatturazione del Centro sicurezza. Questa è sempre basata sui criteri di sicurezza del Centro sicurezza e sulle soluzioni installate in un'area di lavoro. Per le sottoscrizioni senza Azure Defender, il Centro sicurezza offre la soluzione *SecurityCenterFree* nell'area di lavoro predefinita. Per le sottoscrizioni con Azure Defender, il Centro sicurezza offre la soluzione *Security* nell'area di lavoro predefinita.
> L'archiviazione di dati in Log Analytics può comportare costi aggiuntivi. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/).

Per altre informazioni sugli account di Log Analytics esistenti, vedere [Clienti di Log Analytics esistenti](./faq-azure-monitor-logs.md).

### <a name="using-an-existing-workspace"></a>Utilizzo di un'area di lavoro esistente

Se si dispone già di un'area di lavoro Log Analytics esistente, è possibile che si voglia usare la stessa area di lavoro.

Per usare l'area di lavoro Log Analytics esistente, è necessario disporre delle autorizzazioni di lettura e scrittura sull'area di lavoro.

> [!NOTE]
> Le soluzioni abilitate in questa area di lavoro verranno applicate alle macchine virtuali di Azure che sono ad essa connesse. Per le soluzioni a pagamento, ciò può comportare costi aggiuntivi. Per considerazioni sulla privacy dei dati, assicurarsi che l'area di lavoro selezionata si trovi nell'area geografica appropriata.
> L'archiviazione di dati in Log Analytics può comportare costi aggiuntivi. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/).

Per selezionare l'area di lavoro Log Analytics esistente:

1. In **Configurazione dell'area di lavoro predefinita** selezionare **Usa un'altra area di lavoro**.
    :::image type="content" source="./media/security-center-enable-data-collection/use-another-workspace.png" alt-text="Abilitare il provisioning automatico dell'agente di Log Analytics"::: 

2. Nel menu a discesa selezionare un'area di lavoro dove archiviare i dati raccolti.

   > [!NOTE]
   > Nel menu a discesa sono disponibile tutte le aree di lavoro delle sottoscrizioni. Per altre informazioni, vedere [Selezione di un'area di lavoro tra sottoscrizioni](security-center-enable-data-collection.md#cross-subscription-workspace-selection). È necessario disporre dell'autorizzazione per accedere all'area di lavoro.
   >
   >

3. Selezionare **Salva**.
4. Dopo aver selezionato **Salva**, verrà chiesto se si desidera riconfigurare le macchine virtuali monitorate che precedentemente erano connesse a un'area di lavoro predefinita.

   - Selezionare **No** se si desidera che le nuove impostazioni dell'area di lavoro si applichino solo alle nuove macchine virtuali. Le nuove impostazioni dell'area di lavoro si applicano solo alle nuove installazioni dell'agente, ovvero alle macchine virtuali appena individuate in cui non è installato l'agente di Log Analytics.
   - Selezionare **Sì** se si desidera che le nuove impostazioni dell'area di lavoro si applichino a tutte le macchine virtuali. In aggiunta, ogni macchina virtuale connessa a un'area di lavoro creata dal Centro sicurezza viene ricollegata alla nuova area di lavoro di destinazione.

   > [!NOTE]
   > Se si seleziona Sì, non è necessario eliminare le aree di lavoro create dal Centro sicurezza fino a quando tutte le macchine virtuali vengono ricollegate alla nuova area di lavoro di destinazione. Questa operazione non riesce se un'area di lavoro viene eliminata troppo presto.
   >
   >

   - Selezionare **Annulla** per annullare l'operazione.

     ![Rivedere le opzioni per riconfigurare le VM monitorate][3]

5. Selezionare se la soluzione Azure Defender sarà abilitata o meno per l'area di lavoro.

    Per usare un'area di lavoro esistente, impostare il piano tariffario per l'area di lavoro. Verrà installata nell'area di lavoro una soluzione del Centro sicurezza, se non ne è già presente una.

    1. Scegliere **Prezzi e impostazioni** dal menu principale del Centro sicurezza.
     
    1. Selezionare l'area di lavoro a cui connettere l'agente.

    1. Selezionare **Azure Defender - On** o **Azure Defender - Off**.

   
   >[!NOTE]
   >Se l'area di lavoro dispone già di una soluzione **Security**  o **SecurityCenterFree** abilitata, il piano tariffario verrà impostato automaticamente. 


## <a name="cross-subscription-workspace-selection"></a>Selezione di un'area di lavoro tra sottoscrizioni
Quando si seleziona un'area di lavoro in cui archiviare i dati, sono disponibili tutte le aree di lavoro di tutte le sottoscrizioni. La selezione di un'area di lavoro tra sottoscrizioni consente di raccogliere i dati da macchine virtuali in esecuzione in sottoscrizioni diverse e di archiviarli nell'area di lavoro scelta. Questa selezione è utile se si usa un'area di lavoro centralizzata all'interno dell'organizzazione e si vuole usarla per la raccolta di dati sulla sicurezza. Per altre informazioni su come gestire le aree di lavoro, vedere [Gestire l'accesso alle aree di lavoro](../azure-monitor/platform/manage-access.md).



## <a name="data-collection-tier"></a>Livello della raccolta dati
La selezione di un livello di raccolta dati nel Centro sicurezza di Azure avrà effetto soltanto sull'archiviazione degli eventi di sicurezza nell'area di lavoro Log Analytics. L'agente di Log Analytics continuerà a raccogliere e analizzare gli eventi di sicurezza necessari ai fini della protezione dalle minacce del Centro sicurezza di Azure, indipendentemente dal livello scelto per l'archiviazione degli eventi di sicurezza nell'area di lavoro Log Analytics (se presente). La scelta di archiviare gli eventi di sicurezza nell'area di lavoro consentirà l'esecuzione di operazioni di analisi, ricerca e controllo di tali eventi nell'area di lavoro. 
> [!NOTE]
> L'archiviazione di dati in Log Analytics può comportare costi aggiuntivi. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/).

È possibile scegliere i criteri di filtro adatti alle sottoscrizioni e alle aree di lavoro da quattro set di eventi da archiviare nell'area di lavoro: 
- **Nessuno**: disabilita l'archiviazione degli eventi di sicurezza. Si tratta dell'impostazione predefinita.
- **Minimi**: un insieme più piccolo di eventi per i clienti che desiderano ridurre al minimo il volume di eventi.
- **Comuni**: si tratta di un insieme di eventi che soddisfa la maggior parte dei clienti e consente loro di avere un audit trail completo.
- **Tutti gli eventi**: per i clienti che vogliono assicurarsi che tutti gli eventi vengano archiviati.

Questi set di eventi di sicurezza sono disponibili solo con Azure Defender. Per altre informazioni sui piani tariffari di Centro sicurezza, vedere [Prezzi](security-center-pricing.md).

Questi insiemi sono stati progettati per soddisfare gli scenari tipici. Assicurarsi di valutare quale di questi si adatti alle proprie esigenze prima di implementarlo.

Per determinare gli eventi che apparterranno all'insieme di eventi **Comuni** e **Minimi** abbiamo collaborato con i clienti e usato gli standard del settore per conoscere la frequenza non filtrata di ogni evento e il loro uso. In questo processo sono state usate le linee guida seguenti:

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

Per scegliere i criteri di filtraggio:
1. Nella pagina **Raccolta dati** selezionare il criterio di filtro in **Archivia altri dati non elaborati - Eventi di sicurezza di Windows**.
 
1. Selezionare **Salva**.
    :::image type="content" source="./media/security-center-enable-data-collection/data-collection-tiers.png" alt-text="Abilitare il provisioning automatico dell'agente di Log Analytics":::

### <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>Provisioning automatico nel caso di installazione di un agente preesistente <a name="preexisting"></a> 

I casi d'uso seguenti specificano il funzionamento del provisioning automatico nei casi in cui sia già installato un agente o un'estensione. 

- L'agente di Log Analytics è installato nel computer, ma non come estensione (agente diretto)<br>
Se l'agente di Log Analytics viene installato direttamente nella macchina virtuale e non come estensione di Azure, il Centro sicurezza installerà l'estensione dell'agente di Log Analytics e potrà aggiornare l'agente Log Analytics all'ultima versione.
L'agente installato continuerà a generare report per le aree di lavoro già configurate e in più genererà report per l'area di lavoro configurata nel Centro sicurezza (il multihoming è supportato nei computer Windows).
Se l'area di lavoro configurata è un'area di lavoro utente e non l'area di lavoro predefinita del Centro sicurezza, sarà necessario installare la soluzione "security" o "securityFree" su di essa in modo che il Centro sicurezza avvii l'elaborazione degli eventi provenienti dalle macchine virtuali e dai computer che inviano report a tale area di lavoro.<br>
<br>
Per i computer Linux, il multihoming dell'agente non è ancora supportato. Di conseguenza, se viene rilevata un'installazione di un agente esistente, il provisioning automatico non verrà eseguito e la configurazione del computer non verrà modificata.
<br>
Per i computer esistenti in sottoscrizioni di cui è stato eseguito l'onboarding nel Centro sicurezza prima del 17 marzo 2019, quando verrà rilevato un agente esistente, l'estensione agente di Log Analytics non verrà installata e il computer non verrà modificato. Per questi computer, vedere la raccomandazione "Risolvere i problemi di integrità dell'agente di monitoraggio nei computer" per risolvere i problemi di installazione dell'agente.

  
- L'agente di System Center Operations Manager è installato nel computer<br>
Il Centro sicurezza installerà l'estensione dell'agente di Log Analytics in modalità affiancata con l'agente di Operations Manager esistente. L'agente di Operations Manager esistente continuerà a inviare report normalmente al server Operations Manager. L'agente di Operations Manager e l'agente di Log Analytics condividono librerie di runtime comuni, che durante questo processo verranno aggiornate all'ultima versione. Se è installato l'agente Operations Manager versione 2012, **non** abilitare il provisioning automatico.<br>

- È presente un'estensione di macchina virtuale preesistente<br>
    - Quando l'agente di monitoraggio viene installato come estensione, la configurazione dell'estensione consente il reporting a una sola area di lavoro. Il Centro sicurezza non esegue l'override delle connessioni esistenti alle aree di lavoro degli utenti. Il Centro sicurezza archivierà i dati di sicurezza dalla macchina virtuale nell'area di lavoro già connessa, a condizione che vi sia installata la soluzione "security" o "securityFree". Durante questo processo, il Centro sicurezza può aggiornare l'estensione all'ultima versione.  
    - Per vedere a quale area di lavoro l'estensione esistente invia i dati, eseguire il test per [convalidare la connettività con il Centro sicurezza di Azure](/archive/blogs/yuridiogenes/validating-connectivity-with-azure-security-center). In alternativa, è possibile aprire le aree di lavoro Log Analytics, selezionare un'area di lavoro, selezionare la macchina virtuale ed esaminare la connessione dell'agente di Log Analytics. 
    - Se si dispone di un ambiente in cui l'agente di Log Analytics è installato in workstation client e invia report a un'area di lavoro Log Analytics esistente, esaminare l'elenco di [sistemi operativi supportati dal Centro sicurezza di Azure](security-center-os-coverage.md) per assicurarsi che il sistema operativo in uso sia supportato. Per altre informazioni, vedere [Clienti di Log Analytics esistenti](./faq-azure-monitor-logs.md).
 
### <a name="turn-off-automatic-provisioning"></a>Disattivare il provisioning automatico<a name="offprovisioning"></a>
Per disattivare il provisioning automatico dell'agente di Log Analytics:

1. Dal menu del Centro sicurezza nel portale selezionare **Prezzi e impostazioni**.
2. Selezionare la sottoscrizione pertinente.

    :::image type="content" source="./media/security-center-enable-data-collection/select-subscription.png" alt-text="Abilitare il provisioning automatico dell'agente di Log Analytics":::

3. Selezionare **Raccolta di dati**.
4. In **Provisioning automatico** selezionare **No** per disabilitare il provisioning automatico.
5. Selezionare **Salva**. 


Quando il provisioning automatico è disabilitato (disattivato), la sezione di configurazione dell'area di lavoro predefinita non viene visualizzata.

Se si disattiva il provisioning automatico precedentemente attivato, non verrà effettuato il provisioning degli agenti nelle nuove macchine virtuali.

 
> [!NOTE]
>  La disabilitazione del provisioning automatico non implica la rimozione dell'agente di Log Analytics dalle macchine virtuali di Azure in cui è stato eseguito il provisioning dell'agente. Per informazioni sulla rimozione dell'estensione OMS, vedere [How do I remove OMS extensions installed by Security Center](faq-data-collection-agents.md#remove-oms) (Come si rimuovono le estensioni OMS installate dal Centro sicurezza).
>
    
## <a name="manual-agent-provisioning"></a>Provisioning manuale dell'agente <a name="manual-agent"></a>
 
Esistono diversi modi per installare manualmente l'agente di Log Analytics. Quando si esegue l'installazione manualmente, assicurarsi di disabilitare il provisioning automatico.

### <a name="operations-management-suite-vm-extension-deployment"></a>Distribuzione di un'estensione di VM Operations Management Suite 

È possibile installare manualmente l'agente di Log Analytics in modo che il Centro sicurezza possa raccogliere i dati sulla sicurezza dalle macchine virtuali e fornire raccomandazioni e avvisi.

1. Disabilitare il provisioning automatico.

1. Facoltativamente, creare un'area di lavoro.

1. Abilitare Azure Defender nell'area di lavoro in cui si installa l'agente di Log Analytics:

    1. Scegliere **Prezzi e impostazioni** dal menu del Centro sicurezza.

    1. Impostare l'area di lavoro in cui si installa l'agente. Assicurarsi che l'area di lavoro si trovi nella stessa sottoscrizione che si usa nel Centro sicurezza e di disporre delle autorizzazioni di lettura/scrittura nell'area di lavoro.

    1. Attivare Azure Defender, quindi selezionare **Salva**.

       >[!NOTE]
       >Se l'area di lavoro dispone già di una soluzione **Security**  o **SecurityCenterFree** abilitata, il piano tariffario verrà impostato automaticamente. 

1. Per distribuire gli agenti in nuove VM con un modello di Resource Manager, installare l'agente di Log Analytics:

   - [Installare l'agente di Log Analytics per Windows](../virtual-machines/extensions/oms-windows.md)
   - [Installare l'agente di Log Analytics per Linux](../virtual-machines/extensions/oms-linux.md)

1. Per distribuire le estensioni in macchine virtuali esistenti, seguire le istruzioni riportate in [Raccogliere dati sulle macchine virtuali di Azure](../azure-monitor/learn/quick-collect-azurevm.md).

   > [!NOTE]
   > La sezione **Raccogliere dati su eventi e prestazioni** è facoltativa.
   >

1. Per usare PowerShell per distribuire l'estensione, seguire le istruzioni della documentazione relativa alle macchine virtuali:

    - [Per macchine Windows](../virtual-machines/extensions/oms-windows.md?toc=%252fazure%252fazure-monitor%252ftoc.json#powershell-deployment)
    - [Per computer Linux](../virtual-machines/extensions/oms-linux.md?toc=%252fazure%252fazure-monitor%252ftoc.json#azure-cli-deployment)



> [!NOTE]
> Per istruzioni su come caricare il Centro sicurezza usando PowerShell, vedere [Automate onboarding of Azure Security Center using PowerShell](security-center-powershell-onboarding.md) (Automatizzare l'onboarding del Centro sicurezza di Azure usando PowerShell).

## <a name="troubleshooting"></a>Risoluzione dei problemi

-   Per identificare i problemi relativi all'installazione del provisioning automatico, vedere [Problemi di integrità di Microsoft Monitoring Agent](security-center-troubleshooting-guide.md#mon-agent).

-  Per identificare i requisiti di rete di Monitoring Agent, vedere [Risoluzione dei problemi di rete di Microsoft Monitoring Agent](security-center-troubleshooting-guide.md#mon-network-req).
-   Per identificare i problemi di onboarding manuale, vedere [How to troubleshoot Operations Management Suite onboarding issues](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues) (Come risolvere i problemi di onboarding di Operations Management Suite).

- Per identificare i problemi relativi a macchine virtuali e computer non monitorati:

    Una macchina virtuale o un computer non è monitorato dal Centro sicurezza se non esegue l'estensione dell'agente di Log Analytics. È possibile che in una macchina sia già installato un agente locale, ad esempio l'agente diretto di OMS o l'agente di System Center Operations Manager. Le macchine virtuali con questi agenti vengono identificate come non monitorate perché tali agenti non sono supportati completamente nel Centro sicurezza. Per sfruttare in modo ottimale i vantaggi di tutte le funzionalità del Centro sicurezza, è necessaria l'estensione dell'agente di Log Analytics.

    Per altre informazioni sui motivi per cui il Centro sicurezza non è in grado di monitorare correttamente macchine virtuali e computer inizializzati per il provisioning automatico, vedere [Problemi di integrità dell'agente di monitoraggio](security-center-troubleshooting-guide.md#mon-agent).


## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato il funzionamento della raccolta dati e del provisioning automatico nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere le pagine seguenti:

- [Domande frequenti sul Centro sicurezza di Azure](faq-general.md): domande frequenti sull'uso del servizio.
- [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.



<!--Image references-->
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[9]: ./media/security-center-enable-data-collection/pricing-tier.png
[11]: ./media/security-center-enable-data-collection/log-analytics.png
[12]: ./media/security-center-enable-data-collection/log-analytics2.png