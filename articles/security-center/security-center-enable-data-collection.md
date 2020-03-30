---
title: Raccolta dati nel Centro sicurezza di Azure | Microsoft Docs
description: In questo articolo viene descritto come installare un agente di Log Analytics e impostare un'area di lavoro di Log Analytics in cui archiviare i dati raccolti.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 5236fd89ed99ca14bb7fc24e40ef79e12e5177d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245499"
---
# <a name="data-collection-in-azure-security-center"></a>Raccolta dati nel Centro sicurezza di Azure
Il Centro sicurezza raccoglie dati dalle macchine virtuali di Azure, set di scalabilità di macchine virtuali, contenitori IaaS e computer non Azure (inclusi quelli locali) per monitorare le vulnerabilità e le minacce della sicurezza. I dati vengono raccolti tramite l'agente di Log Analytics, che legge varie configurazioni correlate alla sicurezza e i registri eventi dal computer e copia i dati nell'area di lavoro per l'analisi. I dati raccolti sono ad esempio il tipo di sistema operativo e la versione, i log del sistema operativo (log eventi Windows), i processi in esecuzione, il nome del computer, gli indirizzi IP e l'utente connesso. Log Analytics Agent copia anche i file di dump di arresto anomalo del sistema nell'area di lavoro.

La raccolta dei dati è necessaria per fornire visibilità sugli aggiornamenti mancanti, sulle impostazioni di sicurezza del sistema operativo non configurate correttamente, sullo stato di protezione degli endpoint e sulla protezione dell'integrità e delle minacce. 

In questo articolo viene descritto come installare un agente di Log Analytics e impostare un'area di lavoro di Log Analytics in cui archiviare i dati raccolti. Entrambe le operazioni sono necessarie per consentire la raccolta dei dati. 

> [!NOTE]
> - La raccolta dei dati è necessaria solo per le risorse di calcolo (VM, set di scalabilità di macchine virtuali, contenitori IaaS e computer non Azure). È possibile usufruire dei vantaggi del Centro sicurezza di Azure anche se non si esegue il provisioning di agenti. La sicurezza sarà tuttavia limitata e le funzionalità sopra elencate non saranno supportate.  
> - Per l'elenco delle piattaforme supportate, vedere [Supported platforms in Azure Security Center](security-center-os-coverage.md) (Piattaforme supportate nel Centro sicurezza di Azure).
> - L'archiviazione dei dati in Log Analytics, indipendentemente dal fatto che si utilizzi un'area di lavoro nuova o esistente, potrebbe comportare costi aggiuntivi per l'archiviazione dei dati. Per ulteriori informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-automatic-provisioning-of-the-log-analytics-agent"></a>Abilitare il provisioning automatico dell'agente di Log AnalyticsEnable automatic provisioning of the Log Analytics Agent<a name="auto-provision-mma"></a>

Per raccogliere i dati dai computer, è necessario che sia installato l'agente di Log Analytics.To collect the data from the machines, you should have the Log Analytics Agent installed. L'installazione dell'agente può essere eseguita automaticamente (scelta consigliata) oppure è possibile installarlo manualmente.  

>[!NOTE]
> Il provisioning automatico è disattivato per impostazione predefinita. Per fare in modo che il Centro sicurezza usi il provisioning automatico per impostazione predefinita, impostarlo su **Attivato**.
>

Quando il provisioning automatico è attivato, il Centro sicurezza esegue il provisioning dell'agente di Analisi log in tutte le macchine virtuali di Azure supportate e in tutte le nuove macchine virtuali create. Il provisioning automatico è fortemente consigliato, ma è disponibile anche l'installazione manuale dell'agente. [Informazioni su come installare l'estensione Log Analytics Agent](#manual-agent).



Per abilitare il provisioning automatico dell'agente log Analytics:
1. Scegliere **Prezzi e impostazioni** dal menu principale del Centro sicurezza.
2. Fare clic sull'abbonamento applicabile

   ![Seleziona sottoscrizione][7]

3. Selezionare **Raccolta dati**.
4. In **Provisioning automatico** selezionare **Attivato** per abilitare il provisioning automatico.
5. Selezionare **Salva**.

   ![Abilitare il provisioning automatico][1]

>[!NOTE]
> - Per istruzioni su come effettuare il provisioning di un'installazione pre-esistente, vedere [Provisioning automatico nel caso di installazione di un agente preesistente](#preexisting).
> - Per istruzioni sul provisioning manuale, vedere [Installare manualmente l'estensione Log Analytics Agent](#manual-agent).
> - Per istruzioni su come disattivare il provisioning automatico, vedere [Disattivare il provisioning automatico](#offprovisioning).
> - Per istruzioni su come caricare il Centro sicurezza usando PowerShell, vedere [Automate onboarding of Azure Security Center using PowerShell](security-center-powershell-onboarding.md) (Automatizzare l'onboarding del Centro sicurezza di Azure usando PowerShell).
>

## <a name="workspace-configuration"></a>Configurazione dell'area di lavoro
I dati raccolti dal Centro sicurezza vengono archiviati nell'area di lavoro Log Analytics. È possibile scegliere di archiviare i dati raccolti dalle VM di Azure in aree di lavoro create dal Centro sicurezza o in un'area di lavoro esistente creata personalmente. 

La configurazione dell'area di lavoro è impostata in base alla sottoscrizione e più sottoscrizioni possono usare la stessa area di lavoro.

### <a name="using-a-workspace-created-by-security-center"></a>Uso di un'area di lavoro creata dal Centro sicurezza

Il Centro sicurezza può creare automaticamente un'area di lavoro predefinita in cui archiviare i dati. 

Per selezionare un'area di lavoro creata dal Centro sicurezza:

1. In **Configurazione dell'area di lavoro predefinita** selezionare l'opzione per usare un'altra area di lavoro creata dal Centro sicurezza.
   ![Selezionare un piano tariffario][10] 

1. Fare clic su **Salva**.<br>
    Il Centro sicurezza crea un nuovo gruppo di risorse e un'area di lavoro predefinita in corrispondenza della posizione geografica specificata e quindi connette l'agente all'area di lavoro. Ecco le convenzioni di denominazione per l'area di lavoro e il gruppo di risorse:<br>
   **Area di lavoro: DefaultWorkspace-[subscription-ID]-[geo]<br> Gruppo di risorse: DefaultResourceGroup-[geo]**

   Se una sottoscrizione contiene macchine virtuali da più aree geografiche, il Centro sicurezza crea più aree di lavoro. Vengono create più aree di lavoro per gestire le regole sulla privacy dei dati.
1. Il Centro sicurezza abiliterà automaticamente una soluzione del Centro sicurezza nell'area di lavoro in base al piano tariffario impostato per la sottoscrizione. 

> [!NOTE]
> Il piano tariffario di Log Analytics per le aree di lavoro create dal Centro sicurezza non influisce sulla fatturazione del Centro sicurezza. Questa è sempre basata sui criteri di sicurezza del Centro sicurezza e sulle soluzioni installate in un'area di lavoro. Per il livello Gratuito, il Centro sicurezza abilita la soluzione *SecurityCenterFree* nell'area di lavoro predefinita. Per il livello Standard, il Centro sicurezza abilita la soluzione *Security* nell'area di lavoro predefinita.
> L'archiviazione dei dati in Log Analytics potrebbe comportare costi aggiuntivi per l'archiviazione dei dati. Per ulteriori informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/).

Per ulteriori informazioni sugli account di analisi dei log esistenti, vedere [Clienti di analisi dei log esistenti.](./faq-azure-monitor-logs.md)

### <a name="using-an-existing-workspace"></a>Utilizzo di un'area di lavoro esistente

Se si dispone già di un'area di lavoro di Log Analytics esistente, è possibile usare la stessa area di lavoro.

Per usare l'area di lavoro Log Analytics esistente, è necessario disporre delle autorizzazioni di lettura e scrittura sull'area di lavoro.

> [!NOTE]
> Le soluzioni abilitate in questa area di lavoro verranno applicate alle macchine virtuali di Azure che sono ad essa connesse. Per le soluzioni a pagamento, ciò può comportare costi aggiuntivi. Per considerazioni sulla privacy dei dati, assicurarsi che l'area di lavoro selezionata si trovi nell'area geografica appropriata.
> L'archiviazione dei dati nell'analisi dei log potrebbe comportare costi aggiuntivi per l'archiviazione dei dati. Per ulteriori informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/).

Per selezionare l'area di lavoro Log Analytics esistente:

1. In **Configurazione dell'area di lavoro predefinita** selezionare **Usa un'altra area di lavoro**.

   ![Selezionare un'area di lavoro esistente][2]

2. Nel menu a discesa selezionare un'area di lavoro dove archiviare i dati raccolti.

   > [!NOTE]
   > Nel menu a discesa sono disponibile tutte le aree di lavoro delle sottoscrizioni. Per altre informazioni, vedere [Selezione di un'area di lavoro tra sottoscrizioni](security-center-enable-data-collection.md#cross-subscription-workspace-selection). È necessario disporre dell'autorizzazione per accedere all'area di lavoro.
   >
   >

3. Selezionare **Salva**.
4. Dopo aver selezionato **Salva**, verrà chiesto se si desidera riconfigurare le macchine virtuali monitorate che precedentemente erano connesse a un'area di lavoro predefinita.

   - Selezionare **No** se si desidera che le nuove impostazioni dell'area di lavoro si applichino solo alle nuove macchine virtuali. Le nuove impostazioni dell'area di lavoro si applicano solo alle nuove installazioni di agenti; macchine virtuali appena individuate in cui non è installato l'agente Log Analytics.
   - Selezionare **Sì** se si desidera applicare le nuove impostazioni dell'area di lavoro a tutte le macchine virtuali. In aggiunta, ogni macchina virtuale connessa a un'area di lavoro creata dal Centro sicurezza viene ricollegata alla nuova area di lavoro di destinazione.

   > [!NOTE]
   > Se si seleziona Sì, non è necessario eliminare le aree di lavoro create dal Centro sicurezza fino a quando tutte le macchine virtuali vengono ricollegate alla nuova area di lavoro di destinazione. Questa operazione non riesce se un'area di lavoro viene eliminata troppo presto.
   >
   >

   - Selezionare **Annulla** per annullare l'operazione.

     ![Selezionare un'area di lavoro esistente][3]

5. Selezionare il piano tariffario per l'area di lavoro desiderata che si desidera impostare l'agente log Analytics. <br>Per usare un'area di lavoro esistente, impostare il piano tariffario per l'area di lavoro. Verrà installata nell'area di lavoro una soluzione del Centro sicurezza, se non ne è già presente una.

    a.  Nel menu principale del Centro sicurezza selezionare **Impostazioni & prezzi**.
     
    b.  Selezionare l'area di lavoro desiderata a cui si intende connettere l'agente.
        ![Selezionare un'area di lavoro][7] c. Impostare il piano tariffario.
        ![Selezionare un piano tariffario][9]
   
   >[!NOTE]
   >Se l'area di lavoro dispone già di una soluzione **Security ** o **SecurityCenterFree** abilitata, il piano tariffario verrà impostato automaticamente. 

## <a name="cross-subscription-workspace-selection"></a>Selezione di un'area di lavoro tra sottoscrizioni
Quando si seleziona un'area di lavoro in cui archiviare i dati, sono disponibili tutte le aree di lavoro di tutte le sottoscrizioni. La selezione di un'area di lavoro tra sottoscrizioni consente di raccogliere i dati da macchine virtuali in esecuzione in sottoscrizioni diverse e di archiviarli nell'area di lavoro scelta. Questa selezione è utile se si usa un'area di lavoro centralizzata all'interno dell'organizzazione e si vuole usarla per la raccolta di dati sulla sicurezza. Per altre informazioni su come gestire le aree di lavoro, vedere [Gestire l'accesso alle aree di lavoro](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access).


## <a name="data-collection-tier"></a>Livello della raccolta dati
La selezione di un livello di raccolta dati nel Centro sicurezza di Azure avrà effetto soltanto sull'archiviazione degli eventi di sicurezza nell'area di lavoro Log Analytics. L'agente di Log Analytics continuerà a raccogliere e analizzare gli eventi di sicurezza necessari per la protezione dalle minacce del Centro sicurezza di Azure, indipendentemente dal livello di eventi di sicurezza che si sceglie di archiviare nell'area di lavoro di Log Analytics (se presente). La scelta di archiviare gli eventi di sicurezza nell'area di lavoro consentirà l'esecuzione di operazioni di analisi, ricerca e controllo di tali eventi nell'area di lavoro. 
> [!NOTE]
> L'archiviazione dei dati nell'analisi dei log potrebbe comportare costi aggiuntivi per l'archiviazione dei dati. Per ulteriori informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/).
> 
> È possibile scegliere i criteri di filtro adatti alle sottoscrizioni e alle aree di lavoro da quattro set di eventi da archiviare nell'area di lavoro: 

- **Nessuno**: disabilita l'archiviazione degli eventi di sicurezza. Si tratta dell'impostazione predefinita.
- **Minimi**: un insieme più piccolo di eventi per i clienti che desiderano ridurre al minimo il volume di eventi.
- **Comuni**: si tratta di un insieme di eventi che soddisfa la maggior parte dei clienti e consente loro di avere un audit trail completo.
- **Tutti gli eventi**: per i clienti che vogliono assicurarsi che tutti gli eventi vengano archiviati.


> [!NOTE]
> Questi set di eventi di sicurezza sono disponibili solo nel livello Standard del Centro sicurezza. Per altre informazioni sui piani tariffari di Centro sicurezza, vedere [Prezzi](security-center-pricing.md).
Questi insiemi sono stati progettati per soddisfare gli scenari tipici. Assicurarsi di valutare quale di questi si adatti alle proprie esigenze prima di implementarlo.
>
>

Per determinare gli eventi che apparterranno all'insieme di eventi **Comuni** e **Minimi** abbiamo collaborato con i clienti e usato gli standard del settore per conoscere la frequenza non filtrata di ogni evento e il loro uso. In questo processo sono state usate le linee guida seguenti:

- **Minimi**: assicurarsi che questo insieme includa solo gli eventi che potrebbero indicare una violazione riuscita ed eventi importanti con un volume molto basso. Ad esempio, questo set contiene l'accesso riuscito e non riuscito dell'utente (ID evento 4624, 4625), ma non contiene la disconnessione che è importante per il controllo ma non significativa per il rilevamento e ha un volume relativamente elevato. La maggior parte del volume di dati di questo insieme è composto da eventi di accesso e da eventi di creazione del processo (ID evento 4688).
- **Comuni**: fornire un audit trail completo degli utenti in questo insieme. Ad esempio, questo set contiene sia gli account di accesso utente che le disconzioni utente (ID evento 4634). Sono incluse azioni di controllo quali modifiche al gruppo di sicurezza, operazioni Kerberos del controller del dominio principale e altri eventi consigliati dalle organizzazioni del settore.

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
> - Se si usa l'oggetto Criteri di gruppo, è consigliabile abilitare i criteri di controllo della creazione del processo dell'evento 4688 e il campo *CommandLine* all'interno dell'evento 4688. Per altre informazioni sulla creazione del processo dell'evento 4688, vedere le [domande frequenti](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled) del Centro sicurezza. Per altre informazioni su questi criteri di controllo, vedere [Suggerimenti per i criteri di controllo](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Per abilitare la raccolta dei dati per i [controlli applicazione adattivi](security-center-adaptive-application.md), Centro sicurezza consente di configurare criteri AppLocker locali in modalità di controllo per consentire tutte le applicazioni. In questo modo AppLocker genererà eventi che vengono poi raccolti e sfruttati dal Centro sicurezza. È importante notare che questi criteri non saranno configurati nei computer in cui è già configurato un criterio AppLocker. 
> - Per raccogliere l'[ID evento 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156) della piattaforma filtri Windows, è necessario abilitare [Controlla Connessione a Piattaforma filtro Windows](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol /set /subcategory:"Connessione a Piattaforma filtro" /Success:Enable)
>

Per scegliere i criteri di filtraggio:
1. Nella pagina **Raccolta dati** selezionare i criteri di filtro in Eventi di **sicurezza**.
2. Selezionare **Salva**.

   ![Scegliere i criteri di filtraggio][5]

### <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>Provisioning automatico in caso di installazione di un agente preesistente<a name="preexisting"></a> 

I casi d'uso seguenti specificano il funzionamento del provisioning automatico nei casi in cui sia già installato un agente o un'estensione. 

- Log Analytics Agent è installato nel computer, ma non come estensione (agente diretto)<br>
Se l'agente di Log Analytics è installato direttamente nella macchina virtuale (non come estensione di Azure), il Centro sicurezza installerà l'estensione log Analytics Agent e potrebbe aggiornare l'agente Log Analytics alla versione più recente.
L'agente installato continuerà a segnalare le aree di lavoro già configurate e inoltre verrà segnalato all'area di lavoro configurata nel Centro sicurezza (multi-homing è supportato nei computer Windows).
Se l'area di lavoro configurata è un'area di lavoro utente (non l'area di lavoro predefinita del Centro sicurezza), sarà necessario installare la soluzione "security/"securityFree" su di essa affinché il Centro sicurezza inizi a elaborare gli eventi da macchine virtuali e computer che segnalano a tale area di lavoro.<br>
<br>
Per le macchine Linux, il multihoming dell'agente non è ancora supportato, pertanto, se viene rilevata un'installazione dell'agente esistente, il provisioning automatico non verrà eseguito e la configurazione della macchina non verrà modificata.
<br>
Per i computer esistenti nelle sottoscrizioni in servizio in servizio al Centro sicurezza prima del 2019-03-17, quando verrà rilevato un agente esistente, l'estensione Log Analytics Agent non verrà installata e il computer non verrà modificato. Per questi computer, vedere la raccomandazione "Risolvere i problemi di integrità dell'agente di monitoraggio nei computer" per risolvere i problemi di installazione dell'agente in tali computer.

  
- L'agente System Center Operations Manager è installato nel computer<br>
Il Centro sicurezza installerà l'estensione di Log Analytics Agent affiancata a Operations Manager esistente. L'agente Operations Manager esistente continuerà a segnalare normalmente al server Operations Manager. Si noti che l'agente Operations Manager e l'agente Log Analytics condividono librerie di runtime comuni, che verranno aggiornate alla versione più recente durante questo processo.
Nota: se è installata la versione 2012 dell'agente Operations Manager, **non** attivare il provisioning automatico.<br>

- È presente un'estensione di macchina virtuale preesistente<br>
    - Quando l'agente di monitoraggio viene installato come estensione, la configurazione dell'estensione consente di segnalare solo una singola area di lavoro. Il Centro sicurezza non esegue l'override delle connessioni esistenti alle aree di lavoro degli utenti. Il Centro sicurezza memorizzerà i dati di sicurezza della macchina virtuale nell'area di lavoro già connessa, a condizione che sia stata installata la soluzione "security" o "securityFree". Il Centro sicurezza può aggiornare la versione dell'estensione alla versione più recente di questo processo.  
    - Per vedere a quale area di lavoro l'estensione esistente invia i dati, eseguire il test per [convalidare la connettività con il Centro sicurezza di Azure](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). In alternativa, è possibile aprire le aree di lavoro di Log Analytics, selezionare un'area di lavoro, selezionare la macchina virtuale ed esaminare la connessione all'agente di Log Analytics.Alternatively, you can open Log Analytics workspaces, select a workspace, select the VM, and look at the Log Analytics agent connection. 
    - Se si dispone di un ambiente in cui l'agente di Log Analytics è installato nelle workstation client e si segnala un'area di lavoro di Log Analytics esistente, esaminare l'elenco dei [sistemi operativi supportati dal Centro sicurezza](security-center-os-coverage.md) di Azure per assicurarsi che il sistema operativo sia supportato. Per ulteriori informazioni, vedere [Clienti di analisi dei log esistenti](./faq-azure-monitor-logs.md).
 
### <a name="turn-off-automatic-provisioning"></a>Disattivare il provisioning automatico<a name="offprovisioning"></a>
È possibile disattivare il provisioning automatico dalle risorse in qualsiasi momento disattivando questa impostazione nei criteri di sicurezza. 


1. Tornare al menu principale del Centro sicurezza e selezionare Criteri di sicurezza.
2. Fare clic su **Modifica impostazioni** nella riga della sottoscrizione per la quale si desidera disabilitare il provisioning automatico.
3. Nel pannello **Criteri di sicurezza - Raccolta di dati**, in **Provisioning automatico** selezionare **Disattivato**.
4. Selezionare **Salva**.

   ![Disabilitare il provisioning automatico][6]

Quando il provisioning automatico è disabilitato (disattivato), la sezione di configurazione dell'area di lavoro predefinita non viene visualizzata.

Se si disattiva il provisioning automatico precedentemente attivato:
-   Non verrà eseguito il provisioning degli agenti nelle nuove macchine virtuali.
-   Il Centro sicurezza interromperà la raccolta dei dati dall'area di lavoro predefinita.
 
> [!NOTE]
>  La disabilitazione del provisioning automatico non comporta la rimozione dell'agente Log Analytics agent dalle macchine virtuali di Azure in cui è stato eseguito il provisioning dell'agente. Per informazioni sulla rimozione dell'estensione OMS, vedere [How do I remove OMS extensions installed by Security Center](faq-data-collection-agents.md#remove-oms) (Come si rimuovono le estensioni OMS installate dal Centro sicurezza).
>
    
## <a name="manual-agent-provisioning"></a>Provisioning manuale dell'agente <a name="manual-agent"></a>
 
Esistono diversi modi per installare manualmente l'agente Log Analytics. Quando si esegue l'installazione manualmente, assicurarsi di disabilitare il provisioning automatico.

### <a name="operations-management-suite-vm-extension-deployment"></a>Distribuzione di un'estensione di VM Operations Management Suite 

È possibile installare manualmente l'agente di Log Analytics, in modo che il Centro sicurezza possa raccogliere dati di sicurezza dalle macchine virtuali e fornire suggerimenti e avvisi.
1. Selezionare Provisioning automatico: Disattivato.
2. Creare un'area di lavoro e impostare il piano tariffario per l'area di lavoro che si intende impostare l'agente di Log Analytics:Create a workspace and set the pricing tier for the workspace you intend to set the Log Analytics Agent:

   a.  Nel menu principale del Centro sicurezza selezionare **Criteri di sicurezza**.
     
   b.  Selezionare l'area di lavoro a cui si intende connettere l'agente. Assicurarsi che l'area di lavoro si trovi nella stessa sottoscrizione che si usa nel Centro sicurezza e di disporre delle autorizzazioni di lettura/scrittura nell'area di lavoro.
       ![Selezionare l'area di lavoro][8]
3. Impostare il piano tariffario.
   ![Selezionare un piano tariffario][9] 
   >[!NOTE]
   >Se l'area di lavoro dispone già di una soluzione **Security ** o **SecurityCenterFree** abilitata, il piano tariffario verrà impostato automaticamente. 
   > 

4. Se si desidera distribuire gli agenti in nuove macchine virtuali usando un modello di Resource Manager, installare l'estensione di macchina virtuale OMS:

   a.  [Installare l'estensione di macchina virtuale OMS per Windows](../virtual-machines/extensions/oms-windows.md)
    
   b.  [Installare l'estensione di macchina virtuale OMS per Linux](../virtual-machines/extensions/oms-linux.md)
5. Per distribuire le estensioni in macchine virtuali esistenti, seguire le istruzioni riportate in [Raccogliere dati sulle macchine virtuali di Azure](../azure-monitor/learn/quick-collect-azurevm.md).

   > [!NOTE]
   > La sezione **Raccogliere dati su eventi e prestazioni** è facoltativa.
   >
6. Per distribuire l'estensione con PowerShell, usare l'esempio di PowerShell seguente:
   
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   1. Passare a **Log Analytics** e fare clic su **Impostazioni avanzate**.
    
      ![Impostare Log Analytics][11]

   2. Copiare i valori da **WorkspaceID** e **chiave primaria**.
  
      ![Copiare i valori][12]

   3. Compilare la configurazione pubblica e la configurazione privata con questi valori:
     
           $PublicConf = @{
               "workspaceId"= "<WorkspaceID value>"
           }
 
           $PrivateConf = @{
               "workspaceKey"= "<Primary key value>"
           }

      - Durante l'installazione in una macchina virtuale Windows:
        
            Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -settings $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
      - Durante l'installazione in una macchina virtuale Linux:
        
            Set-AzVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

> [!NOTE]
> Per istruzioni su come caricare il Centro sicurezza usando PowerShell, vedere [Automate onboarding of Azure Security Center using PowerShell](security-center-powershell-onboarding.md) (Automatizzare l'onboarding del Centro sicurezza di Azure usando PowerShell).

## <a name="troubleshooting"></a>Risoluzione dei problemi

-   Per identificare i problemi relativi all'installazione del provisioning automatico, vedere [Problemi di integrità di Microsoft Monitoring Agent](security-center-troubleshooting-guide.md#mon-agent).

-  Per identificare i requisiti di rete di Monitoring Agent, vedere [Risoluzione dei problemi di rete di Microsoft Monitoring Agent](security-center-troubleshooting-guide.md#mon-network-req).
-   Per identificare i problemi di onboarding manuale, vedere Come risolvere i problemi di [onboarding](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues)di Operations Management Suite .

- Per identificare i problemi relativi a macchine virtuali e computer non monitorati:

    Una macchina virtuale o un computer non è monitorata dal Centro sicurezza se non esegue l'estensione Microsoft Monitoring Agent. In un computer potrebbe essere già installato un agente locale, ad esempio l'agente diretto OMS o l'agente di System Center Operations Manager. Le macchine virtuali con questi agenti vengono identificate come non monitorate perché tali agenti non sono supportati completamente nel Centro sicurezza. Per sfruttare in modo ottimale i vantaggi di tutte le funzionalità del Centro sicurezza, è necessaria l'estensione Microsoft Monitoring Agent.

    Per ulteriori informazioni sui motivi per cui il Centro sicurezza non è in grado di monitorare correttamente le macchine virtuali e i computer inizializzati per il provisioning automatico, vedere Problemi di [integrità dell'agente](security-center-troubleshooting-guide.md#mon-agent)di monitoraggio .


## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato il funzionamento della raccolta dati e del provisioning automatico nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Domande frequenti sul Centro sicurezza di Azure](faq-general.md): domande frequenti sull'uso del servizio.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.



<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png
[7]: ./media/security-center-enable-data-collection/select-subscription.png
[8]: ./media/security-center-enable-data-collection/manual-provision.png
[9]: ./media/security-center-enable-data-collection/pricing-tier.png
[10]: ./media/security-center-enable-data-collection/workspace-selection.png
[11]: ./media/security-center-enable-data-collection/log-analytics.png
[12]: ./media/security-center-enable-data-collection/log-analytics2.png
