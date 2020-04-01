---
title: Domande frequenti sul Centro sicurezza di Azure- Raccolta dati e agentiAzure Security Center FAQ - data collection and agents
description: Domande frequenti sulla raccolta dei dati, gli agenti e le aree di lavoro per il Centro sicurezza di Azure, un prodotto che consente di prevenire, rilevare e rispondere alle minacce
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 0dbad1a94479430426dae47df7ca3a3ecd9dc980
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436188"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>Domande frequenti - Domande sulla raccolta di dati, gli agenti e le aree di lavoro

Il Centro sicurezza raccoglie dati dalle macchine virtuali di Azure, dai set di scalabilità delle macchine virtuali, dai contenitori IaaS e dai computer non Azure (inclusi i computer locali) per monitorare le vulnerabilità e le minacce alla sicurezza. I dati vengono raccolti utilizzando l'agente di Log Analytics, che legge varie configurazioni correlate alla sicurezza e i registri eventi dal computer e copia i dati nell'area di lavoro per l'analisi.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>I log di Monitoraggio di Azure vengono fatturati nelle aree di lavoro create dal Centro sicurezza?

No. Le aree di lavoro create dal Centro sicurezza, mentre sono configurate per i log di Monitoraggio di Azure per la fatturazione dei nodi, non comportano costi nei log di Monitoraggio di Azure.Workspaces created by Security Center, while configured for Azure Monitor logs per node billing, don't bebe Azure Monitor logs charges. La fatturazione del Centro sicurezza è sempre basata sui criteri di sicurezza del Centro sicurezza e sulle soluzioni installate in un'area di lavoro:

- **Livello Gratuito**: il Centro sicurezza abilita la soluzione 'SecurityCenterFree' nell'area di lavoro predefinita. Non ti verrà addebitato il livello Gratuito.

- **Livello Standard**: il Centro sicurezza abilita la soluzione 'Security' nell'area di lavoro predefinita.

Per altre informazioni sui prezzi, vedere [Prezzi di Centro sicurezza](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> Il piano tariffario di analisi dei log delle aree di lavoro create dal Centro sicurezza non influisce sulla fatturazione del Centro sicurezza.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-log-analytics-agent-installation"></a>Che cosa qualifica una macchina virtuale per il provisioning automatico dell'installazione dell'agente di Log Analytics?

Le macchine virtuali IaaS Windows o Linux hanno diritto al provisioning automatico se:

- L'estensione dell'agente di Log Analytics non è attualmente installata nella macchina virtuale.
- La macchina virtuale è nello stato di esecuzione.
- È installato Windows o Linux [Azure Virtual Machine Agent.](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)
- La macchina virtuale non viene usata come appliance, ad esempio web application firewall o firewall di nuova generazione.


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>È possibile eliminare le aree di lavoro predefinite create dal Centro sicurezza?

**L'eliminazione dell'area di lavoro predefinita non è consigliata.** Il Centro sicurezza usa le aree di lavoro predefinite per archiviare i dati di sicurezza dalle macchine virtuali. Se si elimina un'area di lavoro, il Centro sicurezza non potrà raccogliere tali dati e alcune raccomandazioni e alcuni avvisi di sicurezza non saranno disponibili.

Per eseguire il ripristino, rimuovere l'agente di Log Analytics nelle macchine virtuali connesse all'area di lavoro eliminata. Il Centro sicurezza reinstalla l'agente e crea nuove aree di lavoro predefinite.

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Come usare l'area di lavoro Log Analytics esistente

È possibile selezionare un'area di lavoro Log Analytics esistente per archiviare i dati raccolti dal Centro sicurezza. Per usare l'area di lavoro Log Analytics esistente:

- L'area di lavoro deve essere associata alla sottoscrizione di Azure selezionata.
- È necessario avere almeno le autorizzazioni di lettura per accedere all'area di lavoro.

Per selezionare l'area di lavoro Log Analytics esistente:

1. In **Security policy – Data Collection** (Criteri di sicurezza - Raccolta dati) selezionare **Use another workspace** (Usare un'altra area di lavoro).

    ![Usare un'altra area di lavoro][4]

1. Nel menu a discesa selezionare un'area di lavoro dove archiviare i dati raccolti.

    > [!NOTE]
    > Nel menu a discesa vengono visualizzate solo le aree di lavoro a cui si ha accesso e che si trovano nella sottoscrizione di Azure.

1. Selezionare **Salva**. Verrà chiesto se si desidera riconfigurare le macchine virtuali monitorate.

    - Selezionare **No** se si desidera applicare le nuove impostazioni dell'area di lavoro **solo alle nuove macchine virtuali.** Le nuove impostazioni dell'area di lavoro si applicano solo alle nuove installazioni di agenti; macchine virtuali appena individuate in cui non è installato l'agente di Log Analytics.
    - Selezionare **Sì** se si desidera che le nuove impostazioni dell'area di lavoro si **applichino a tutte le macchine virtuali**. In aggiunta, ogni macchina virtuale connessa a un'area di lavoro creata dal Centro sicurezza viene ricollegata alla nuova area di lavoro di destinazione.

    > [!NOTE]
    > Se si seleziona **Sì**, non eliminare le aree di lavoro create dal Centro sicurezza finché tutte le macchine virtuali non sono state riconnesse nella nuova area di lavoro di destinazione. Questa operazione non riesce se un'area di lavoro viene eliminata troppo presto.

    - Per annullare l'operazione, selezionare **Annulla**.

## <a name="what-if-the-log-analytics-agent-was-already-installed-as-an-extension-on-the-vm"></a>Cosa succede se l'agente di Log Analytics è già stato installato come estensione nella macchina virtuale?<a name="mmaextensioninstalled"></a>

Quando l'agente di monitoraggio viene installato come estensione, la configurazione dell'estensione consente di segnalare solo una singola area di lavoro. Il Centro sicurezza non esegue l'override delle connessioni esistenti alle aree di lavoro degli utenti. Centro sicurezza archivierà i dati di sicurezza da una macchina virtuale in un'area di lavoro già connessa, a condizione che la soluzione "Security" o "SecurityCenterFree" sia stata installata su di essa. Il Centro sicurezza può aggiornare la versione dell'estensione alla versione più recente di questo processo.

Per ulteriori informazioni, consultate [Provisioning automatico in caso di installazione di un agente preesistente.](security-center-enable-data-collection.md#preexisting)



## <a name="what-if-a-log-analytics-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>Cosa succede se un agente di Log Analytics è installato direttamente nel computer ma non come estensione (agente diretto)?<a name="directagentinstalled"></a>

Se l'agente di Log Analytics è installato direttamente nella macchina virtuale (non come estensione di Azure), il Centro sicurezza installerà l'estensione dell'agente di Log Analytics e potrebbe aggiornare l'agente di Log Analytics alla versione più recente.

L'agente installato continuerà a eseguire report sulle aree di lavoro già configurate e inoltre verrà segnalato all'area di lavoro configurata nel Centro sicurezza (multihoming è supportato nei computer Windows).

Se l'area di lavoro configurata è un'area di lavoro utente (non l'area di lavoro predefinita del Centro sicurezza), sarà necessario installare la soluzione "Security/"SecurityCenterFree" in tale area di lavoro per avviare l'elaborazione di eventi da macchine virtuali e computer che segnalano a tale area di lavoro.

Per le macchine Linux, il multihoming dell'agente non è ancora supportato, pertanto, se viene rilevata un'installazione dell'agente esistente, il provisioning automatico non verrà eseguito e la configurazione della macchina non verrà modificata.

Per i computer esistenti nelle sottoscrizioni in servizio in servizio al Centro sicurezza prima del 17 marzo 2019, quando verrà rilevato un agente esistente, l'estensione dell'agente Log Analytics non verrà installata e il computer non verrà modificato. Per questi computer, vedere la raccomandazione "Risolvere i problemi di integrità dell'agente di monitoraggio nei computer" per risolvere i problemi di installazione dell'agente in tali computer

Per altre informazioni, vedere la sezione successiva [Cosa succede se nella macchina virtuale è già installato un agente diretto di System Center Operations Manager o OMS?](#scomomsinstalled)

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>Cosa succede se un agente Di System Center Operations Manager è già installato nella macchina virtuale?<a name="scomomsinstalled"></a>

Il Centro sicurezza installerà l'estensione dell'agente di Log Analytics affiancata all'agente System Center Operations Manager esistente. L'agente esistente continuerà a segnalare normalmente il server System Center Operations Manager. Si noti che l'agente Operations Manager e l'agente log Analytics condividono librerie di runtime comuni, che verranno aggiornate alla versione più recente durante questo processo. Nota: se è installata la versione 2012 dell'agente Operations Manager, non attivare il provisioning automatico (le funzionalità di gestione possono andare perse quando il server Operations Manager è anche la versione 2012).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>Qual è l'impatto della rimozione delle estensioni?

Se si rimuove l'estensione Microsoft Monitoring, il Centro sicurezza non potrà raccogliere i dati di sicurezza dalla VM e alcune raccomandazioni e alcuni avvisi di sicurezza non saranno disponibili. Entro 24 ore il Centro sicurezza determina che nella VM non è presente l'estensione e la reinstalla.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Come si interrompono l'installazione automatica dell'agente e la creazione automatica dell'area di lavoro?

È possibile disattivare il provisioning automatico per le sottoscrizioni nei criteri di sicurezza, ma questa opzione non è consigliata. La disattivazione del provisioning automatico limita le raccomandazioni e gli avvisi del Centro sicurezza. Per disabilitare il provisioning automatico:

1. Se la sottoscrizione è configurata per il livello Standard, aprire i criteri di sicurezza per tale sottoscrizione e selezionare il livello **Gratuito**.

   ![Piano tariffario][1]

1. Disattivare quindi il provisioning automatico selezionando **Disattivato** nella pagina **Criteri di sicurezza - Raccolta dati.**
   ![Raccolta di dati][2]


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>È consigliabile rifiutare esplicitamente l'installazione automatica dell'agente e la creazione automatica dell'area di lavoro?

> [!NOTE]
> Assicurarsi di aver letto con attenzione le sezioni [Quali sono le implicazioni del rifiuto esplicito?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) e [Procedure consigliate quando si rifiuta esplicitamente](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) se si sceglie di rifiutare esplicitamente il provisioning automatico.

È possibile rifiutare esplicitamente il provisioning automatico se si applicano le condizioni seguenti:

- L'installazione automatica dell'agente da parte del Centro sicurezza si applica all'intera sottoscrizione. Non è possibile applicare l'installazione automatica a un sottoinsieme di macchine virtuali. Se sono presenti macchine virtuali critiche che non possono essere installate con l'agente di Log Analytics, è consigliabile rifiutare esplicitamente il provisioning automatico.
- L'installazione dell'estensione dell'agente log Analytics aggiorna la versione dell'agente. Questo vale per un agente diretto e un agente di System Center Operations Manager (in quest'ultimo, l'agente Operations Manager e Log Analytics condividono le librerie di runtime comuni, che verranno aggiornate nel processo). Se l'agente Operations Manager installato è la versione 2012 e viene aggiornato, le funzionalità di gestibilità possono andare perse quando anche il server Operations Manager è la versione 2012. Se l'agente Operations Manager installato è la versione 2012, è consigliabile disattivare il provisioning automatico.
- Se si dispone di un'area di lavoro personalizzata esterna alla sottoscrizione (un'area di lavoro centralizzata), è consigliabile rifiutare esplicitamente il provisioning automatico. È possibile installare manualmente l'estensione dell'agente log Analytics e connetterla all'area di lavoro senza che Il Centro sicurezza esembri la connessione.
- Se si vuole evitare di creare più aree di lavoro per sottoscrizione e si dispone di un'area di lavoro personalizzata all'interno della sottoscrizione, sono disponibili due opzioni:

   1. È possibile rifiutare esplicitamente il provisioning automatico. Dopo la migrazione, configurare le impostazioni dell'area di lavoro predefinita come descritto in [Come usare l'area di lavoro Log Analytics esistente](#how-can-i-use-my-existing-log-analytics-workspace).

   1. In alternativa, è possibile consentire il completamento della migrazione, l'agente di Log Analytics da installare nelle macchine virtuali e le macchine virtuali connesse all'area di lavoro creata. Quindi, selezionare l'area di lavoro personalizzata configurando l'impostazione dell'area di lavoro predefinita in modo da acconsentire esplicitamente alla riconfigurazione degli agenti già installati. Per altre informazioni, vedere [Come usare l'area di lavoro Log Analytics esistente](#how-can-i-use-my-existing-log-analytics-workspace).


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Quali sono le implicazioni del rifiuto esplicito del provisioning automatico?

Al termine della migrazione, il Centro sicurezza non è in grado di raccogliere dati di sicurezza dalla macchina virtuale e alcuni avvisi e consigli sulla sicurezza non sono disponibili. Se si rifiuta esplicitamente, installare manualmente l'agente di Log Analytics. Vedere [Procedure consigliate quando si rifiuta esplicitamente](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Quali sono le procedure consigliate quando si rifiuta esplicitamente il provisioning automatico?

Installare manualmente l'estensione dell'agente log Analytics in modo che il Centro sicurezza possa raccogliere dati di sicurezza dalle macchine virtuali e fornire suggerimenti e avvisi. Vedere [installazione dell'agente per macchine virtuali Windows](../virtual-machines/extensions/oms-windows.md) oppure [installazione dell'agente per macchine virtuali Linux](../virtual-machines/extensions/oms-linux.md) per indicazioni sull'installazione.

È possibile connettere l'agente a qualsiasi area di lavoro personalizzata esistente o all'area di lavoro creata dal Centro sicurezza. Se in un'area di lavoro personalizzata non sono abilitate le soluzioni 'Security' o 'SecurityCenterFree', sarà necessario applicare una soluzione. Per applicare, selezionare l'area di lavoro o la sottoscrizione personalizzata e applicare un piano tariffario tramite la pagina Criteri di **sicurezza - Piano tariffario.**

   ![Piano tariffario][1]

Il Centro sicurezza abiliterà la corretta soluzione nell'area di lavoro in base al piano tariffario selezionato.


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Come si rimuovono le estensioni di OMS installate dal Centro sicurezza?<a name="remove-oms"></a>

È possibile rimuovere manualmente l'agente di Log Analytics.You can manually remove the Log Analytics agent. Questa operazione non è consigliata perché limita le raccomandazioni e gli avvisi del Centro sicurezza.

> [!NOTE]
> Se la raccolta di dati è abilitata, il Centro sicurezza reinstallerà l'agente dopo la rimozione.  È necessario disabilitare la raccolta di dati prima di rimuovere manualmente l'agente. Consultare "Come si interrompono l'installazione automatica dell'agente e la creazione automatica dell'area di lavoro?" per istruzioni sulla disabilitazione della raccolta di dati.

Per rimuovere manualmente l'agente:

1.    Nel portale aprire **Log Analytics**.

1.    Nella pagina Log Analytics selezionare un'area di lavoro:On the Log Analytics page, select a workspace:

1.    Selezionare le macchine virtuali che non si desidera monitorare e selezionare **Disconnetti**.

   ![Rimuovere l'agente][3]

> [!NOTE]
> Se una macchina virtuale Linux dispone già di un agente OMS non di estensione, la rimozione dell'estensione rimuove anche l'agente e sarà necessario reinstallarlo.


## <a name="how-do-i-disable-data-collection"></a>Come si disabilita la raccolta dati?

Il provisioning automatico è disattivato per impostazione predefinita. È possibile disabilitare il provisioning automatico nelle risorse in qualsiasi momento disattivando questa impostazione nei criteri di sicurezza. Il provisioning automatico è altamente consigliato per ricevere avvisi di sicurezza e suggerimenti sugli aggiornamenti del sistema, le vulnerabilità del sistema operativo e la protezione degli endpoint.

Per disabilitare la raccolta di dati, [accedere al portale di Azure](https://portal.azure.com), selezionare **Sfoglia**, **Centro sicurezza**, quindi selezionare**Selezione criteri**. Selezionare la sottoscrizione per cui si desidera disabilitare il provisioning automatico. Quando si seleziona una sottoscrizione, si apre **Criteri di sicurezza - Raccolta dati**. In **Auto provisioning**, (Provisioning automatico) selezionare **Off**.


## <a name="how-do-i-enable-data-collection"></a>Come si abilita la raccolta dati?

È possibile abilitare la raccolta dei dati per la sottoscrizione nei criteri di sicurezza. Per abilitare la raccolta di dati. [Accedere al portale di Azure](https://portal.azure.com), selezionare **Sfoglia**, quindi **Centro sicurezza** e infine **Criteri di sicurezza**. Selezionare la sottoscrizione per cui si desidera abilitare il provisioning automatico. Quando si seleziona una sottoscrizione, si apre **Criteri di sicurezza - Raccolta dati**. In **Auto provisioning**, (Provisioning automatico) selezionare **On**.


## <a name="what-happens-when-data-collection-is-enabled"></a>Cosa accade quando si abilita la raccolta dati?

Quando il provisioning automatico è abilitato, il Centro sicurezza esegue il provisioning dell'agente di Log Analytics in tutte le macchine virtuali di Azure supportate e in tutte le nuove macchine virtuali create. È consigliabile eseguire il provisioning automatico, ma è disponibile anche l'installazione manuale dell'agente. [Informazioni su come installare l'estensione dell'agente di Log Analytics.](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) 

L'agente abilita l'evento di creazione di processi 4688 e il campo *CommandLine* all'interno dell'evento 4688. I nuovi processi creati nella macchina virtuale vengono registrati da EventLog e monitorati dai servizi di rilevamento del Centro sicurezza. Per ulteriori informazioni sui dettagli registrati per ogni nuovo processo, vedere [i campi di descrizione in 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). L'agente inoltre raccoglie gli eventi 4688 creati nella macchina virtuale e li archivia nella ricerca.

L'agente abilita anche la raccolta di dati per i [controlli applicazione adattivi](security-center-adaptive-application.md). Centro sicurezza consente di configurare criteri AppLocker locali in modalità di controllo per consentire tutte le applicazioni. Questo criterio farà sì che AppLocker generi eventi, che vengono quindi raccolti e sfruttati dal Centro sicurezza. È importante notare che questi criteri non saranno configurati nei computer in cui è già configurato un criterio AppLocker. 

Quando il Centro sicurezza rileva attività sospette nella macchina virtuale, il cliente riceve una notifica tramite posta elettronica se sono state fornite le [informazioni sul contatto di sicurezza](security-center-provide-security-contact-details.md). Un avviso è visibile anche nel dashboard degli avvisi di sicurezza del Centro sicurezza.


## <a name="will-security-center-work-using-an-oms-gateway"></a>Il Centro sicurezza funziona con un gateway OMS?

Sì. Il Centro sicurezza di Azure sfrutta Monitoraggio di Azure per raccogliere dati da macchine virtuali e server di Azure usando l'agente log Analytics.Azure Security Center leverages Azure Monitor to collect data from Azure VMs and servers, using the Log Analytics agent.
Per raccogliere i dati, ogni macchina virtuale e server deve connettersi a Internet tramite HTTPS. La connessione può essere diretta, tramite un proxy o tramite il [gateway OMS](../azure-monitor/platform/gateway.md).


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>L'agente di monitoraggio compromettere le prestazioni dei server?

L'agente e usa una quantità nominale delle risorse di sistema e dovrebbe avere un impatto minimo sulle prestazioni. Per altre informazioni sull'agente, sull'estensione e sull'impatto sulle prestazioni, vedere la [guida alla pianificazione e alla gestione](security-center-planning-and-operations-guide.md#data-collection-and-storage).


## <a name="where-is-my-data-stored"></a>Dove vengono archiviati i dati?

I dati raccolti dall'agente vengono archiviati in un'area di lavoro Log Analytics esistente associata alla sottoscrizione o in una nuova area di lavoro. Per altre informazioni, vedere [Sicurezza dei dati](security-center-data-security.md).


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png
