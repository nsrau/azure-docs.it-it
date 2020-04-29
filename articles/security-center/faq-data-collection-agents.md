---
title: Domande frequenti sul centro sicurezza di Azure-raccolta dati e agenti
description: Domande frequenti sulla raccolta dati, gli agenti e le aree di lavoro per il Centro sicurezza di Azure, un prodotto che consente di prevenire, rilevare e rispondere alle minacce
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80436188"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>Domande frequenti sulla raccolta dati, gli agenti e le aree di lavoro

Il Centro sicurezza raccoglie i dati dalle macchine virtuali (VM) di Azure, dai set di scalabilità di macchine virtuali, dai contenitori IaaS e dai computer non Azure (incluse le macchine locali) per monitorare le vulnerabilità e le minacce alla sicurezza. I dati vengono raccolti tramite l'agente di Log Analytics, che legge diverse configurazioni correlate alla sicurezza oltre ai log eventi del computer e copia i dati nell'area di lavoro per eseguire l'analisi.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Vengono fatturati i log di monitoraggio di Azure nelle aree di lavoro create dal centro sicurezza?

No. Le aree di lavoro create dal centro sicurezza, configurate per la fatturazione dei log di monitoraggio di Azure per nodo, non comportano costi di log di monitoraggio di Azure La fatturazione del Centro sicurezza è sempre basata sui criteri di sicurezza del Centro sicurezza e sulle soluzioni installate in un'area di lavoro:

- **Livello Gratuito**: il Centro sicurezza abilita la soluzione 'SecurityCenterFree' nell'area di lavoro predefinita. Non verrà addebitato alcun costo per il livello gratuito.

- **Livello Standard**: il Centro sicurezza abilita la soluzione 'Security' nell'area di lavoro predefinita.

Per altre informazioni sui prezzi, vedere [Prezzi di Centro sicurezza](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> Il piano tariffario di log Analytics per le aree di lavoro create dal centro sicurezza non influisce sulla fatturazione del Centro sicurezza.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-log-analytics-agent-installation"></a>Quale qualifica una macchina virtuale per il provisioning automatico dell'installazione dell'agente di Log Analytics?

Le macchine virtuali IaaS Windows o Linux hanno diritto al provisioning automatico se:

- L'estensione agente Log Analytics non è attualmente installata nella macchina virtuale.
- La macchina virtuale è nello stato di esecuzione.
- L' [agente di macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) per Windows o Linux è installato.
- La macchina virtuale non viene usata come appliance, ad esempio web application firewall o firewall di nuova generazione.


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>È possibile eliminare le aree di lavoro predefinite create dal Centro sicurezza?

**L'eliminazione dell'area di lavoro predefinita non è consigliata.** Il Centro sicurezza usa le aree di lavoro predefinite per archiviare i dati di sicurezza dalle macchine virtuali. Se si elimina un'area di lavoro, il Centro sicurezza non potrà raccogliere tali dati e alcune raccomandazioni e alcuni avvisi di sicurezza non saranno disponibili.

Per eseguire il ripristino, rimuovere l'agente di Log Analytics nelle VM connesse all'area di lavoro eliminata. Il Centro sicurezza reinstalla l'agente e crea nuove aree di lavoro predefinite.

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

1. Selezionare **Salva**. Verrà chiesto se si vuole riconfigurare le macchine virtuali monitorate.

    - Selezionare **No** se si desidera che le nuove impostazioni dell'area di lavoro si **applichino solo alle nuove macchine virtuali**. Le nuove impostazioni dell'area di lavoro si applicano solo alle nuove installazioni degli agenti. nuove macchine virtuali individuate in cui non è installato l'agente Log Analytics.
    - Selezionare **Sì** se si desidera che le nuove impostazioni dell'area di lavoro si **applichino a tutte le macchine virtuali**. In aggiunta, ogni macchina virtuale connessa a un'area di lavoro creata dal Centro sicurezza viene ricollegata alla nuova area di lavoro di destinazione.

    > [!NOTE]
    > Se si seleziona **Sì**, non eliminare le aree di lavoro create dal centro sicurezza finché tutte le VM non sono state riconnesse alla nuova area di lavoro di destinazione. Questa operazione non riesce se un'area di lavoro viene eliminata troppo presto.

    - Per annullare l'operazione, selezionare **Annulla**.

## <a name="what-if-the-log-analytics-agent-was-already-installed-as-an-extension-on-the-vm"></a>Cosa accade se l'agente di Log Analytics è già stato installato come estensione nella VM?<a name="mmaextensioninstalled"></a>

Quando l'agente di monitoraggio viene installato come estensione, la configurazione dell'estensione consente la segnalazione solo a una singola area di lavoro. Il Centro sicurezza non esegue l'override delle connessioni esistenti alle aree di lavoro degli utenti. Il Centro sicurezza archivia i dati di sicurezza da una macchina virtuale in un'area di lavoro già connessa, a condizione che sia stata installata la soluzione "Security" o "SecurityCenterFree". Il Centro sicurezza può aggiornare la versione dell'estensione alla versione più recente in questo processo.

Per ulteriori informazioni, vedere [provisioning automatico nei casi di installazione di un agente preesistente](security-center-enable-data-collection.md#preexisting).



## <a name="what-if-a-log-analytics-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>Cosa accade se un agente Log Analytics è installato direttamente nel computer, ma non come estensione (agente diretto)?<a name="directagentinstalled"></a>

Se l'agente di Log Analytics viene installato direttamente nella macchina virtuale (non come estensione di Azure), il Centro sicurezza installerà l'estensione agente Log Analytics e potrà aggiornare l'agente Log Analytics alla versione più recente.

L'agente installato continuerà a segnalare le aree di lavoro già configurate e verrà inoltre segnalato all'area di lavoro configurata nel centro sicurezza (il multihoming è supportato nei computer Windows).

Se l'area di lavoro configurata è un'area di lavoro dell'utente (non l'area di lavoro predefinita del Centro sicurezza), è necessario installare la soluzione "Security/" SecurityCenterFree "in modo che il Centro sicurezza avvii l'elaborazione degli eventi da macchine virtuali e computer che inviano report all'area di lavoro.

Per i computer Linux, il multihosting di Agent non è ancora supportato. di conseguenza, se viene rilevata un'installazione di un agente esistente, il provisioning automatico non verrà eseguito e la configurazione del computer non verrà modificata.

Per i computer esistenti nelle sottoscrizioni caricate nel centro sicurezza prima del 17 2019 marzo, quando verrà rilevato un agente esistente, l'estensione dell'agente Log Analytics non verrà installata e il computer non sarà interessato. Per questi computer, vedere la raccomandazione "risolvere i problemi di integrità dell'agente di monitoraggio nei computer" per risolvere i problemi di installazione dell'agente in questi computer

Per altre informazioni, vedere la sezione successiva [che cosa accade se un agente System Center Operations Manager o OMS Direct Agent è già installato nella macchina virtuale?](#scomomsinstalled)

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>Cosa accade se un agente System Center Operations Manager è già installato nella macchina virtuale?<a name="scomomsinstalled"></a>

Il Centro sicurezza installerà l'estensione Log Analytics Agent side-by-side con l'agente di System Center Operations Manager esistente. L'agente esistente continuerà a segnalare normalmente al server System Center Operations Manager. Si noti che l'agente di Operations Manager e l'agente di Log Analytics condividono librerie di runtime comuni, che verranno aggiornate alla versione più recente durante questo processo. Nota: se è installata la versione 2012 dell'agente di Operations Manager, non attivare il provisioning automatico (le funzionalità di gestibilità possono andare perse quando anche il server Operations Manager è la versione 2012).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>Qual è l'impatto della rimozione delle estensioni?

Se si rimuove l'estensione Microsoft Monitoring, il Centro sicurezza non potrà raccogliere i dati di sicurezza dalla VM e alcune raccomandazioni e alcuni avvisi di sicurezza non saranno disponibili. Entro 24 ore il Centro sicurezza determina che nella VM non è presente l'estensione e la reinstalla.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Come si interrompono l'installazione automatica dell'agente e la creazione automatica dell'area di lavoro?

È possibile disattivare il provisioning automatico per le sottoscrizioni nei criteri di sicurezza, ma questa opzione non è consigliata. La disattivazione del provisioning automatico limita le raccomandazioni e gli avvisi del Centro sicurezza. Per disabilitare il provisioning automatico:

1. Se la sottoscrizione è configurata per il livello Standard, aprire i criteri di sicurezza per tale sottoscrizione e selezionare il livello **Gratuito**.

   ![Piano tariffario][1]

1. Disabilitare quindi il provisioning automatico selezionando **disattivato** nella pagina **criteri di sicurezza-raccolta dati** .
   ![Raccolta di dati][2]


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>È consigliabile rifiutare esplicitamente l'installazione automatica dell'agente e la creazione automatica dell'area di lavoro?

> [!NOTE]
> Assicurarsi di aver letto con attenzione le sezioni [Quali sono le implicazioni del rifiuto esplicito?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) e [Procedure consigliate quando si rifiuta esplicitamente](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) se si sceglie di rifiutare esplicitamente il provisioning automatico.

È possibile rifiutare esplicitamente il provisioning automatico se si applicano le condizioni seguenti:

- L'installazione automatica dell'agente da parte del Centro sicurezza si applica all'intera sottoscrizione. Non è possibile applicare l'installazione automatica a un sottoinsieme di macchine virtuali. Se sono presenti macchine virtuali critiche che non possono essere installate con l'agente di Log Analytics, è consigliabile rifiutare esplicitamente il provisioning automatico.
- L'installazione dell'estensione dell'agente di Log Analytics aggiorna la versione dell'agente. Questa condizione si applica a un agente diretto e a un agente di System Center Operations Manager (nel secondo caso, le Operations Manager e Log Analytics Agent condividono librerie di runtime comuni, che verranno aggiornate nel processo). Se l'agente di Operations Manager installato è la versione 2012 e viene aggiornato, le funzionalità di gestibilità possono andare perse quando anche il server di Operations Manager è la versione 2012. Si consiglia di rifiutare esplicitamente il provisioning automatico se l'agente di Operations Manager installato è la versione 2012.
- Se si dispone di un'area di lavoro personalizzata esterna alla sottoscrizione (un'area di lavoro centralizzata), è consigliabile rifiutare esplicitamente il provisioning automatico. È possibile installare manualmente l'estensione dell'agente Log Analytics e connetterla all'area di lavoro senza eseguire l'override della connessione.
- Se si vuole evitare di creare più aree di lavoro per sottoscrizione e si dispone di un'area di lavoro personalizzata all'interno della sottoscrizione, sono disponibili due opzioni:

   1. È possibile rifiutare esplicitamente il provisioning automatico. Dopo la migrazione, configurare le impostazioni dell'area di lavoro predefinita come descritto in [Come usare l'area di lavoro Log Analytics esistente](#how-can-i-use-my-existing-log-analytics-workspace).

   1. In alternativa, è possibile consentire il completamento della migrazione, l'agente di Log Analytics da installare nelle macchine virtuali e le VM connesse all'area di lavoro creata. Quindi, selezionare l'area di lavoro personalizzata configurando l'impostazione dell'area di lavoro predefinita in modo da acconsentire esplicitamente alla riconfigurazione degli agenti già installati. Per altre informazioni, vedere [Come usare l'area di lavoro Log Analytics esistente](#how-can-i-use-my-existing-log-analytics-workspace).


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Quali sono le implicazioni del rifiuto esplicito del provisioning automatico?

Al termine della migrazione, il Centro sicurezza non è in grado di raccogliere i dati di sicurezza dalla macchina virtuale e alcuni avvisi e raccomandazioni sulla sicurezza non sono disponibili. Se si rifiuta esplicitamente, installare l'agente Log Analytics manualmente. Vedere [Procedure consigliate quando si rifiuta esplicitamente](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Quali sono le procedure consigliate quando si rifiuta esplicitamente il provisioning automatico?

Installare manualmente l'estensione dell'agente Log Analytics in modo che il Centro sicurezza possa raccogliere i dati di sicurezza dalle macchine virtuali e fornire consigli e avvisi. Vedere [installazione dell'agente per macchine virtuali Windows](../virtual-machines/extensions/oms-windows.md) oppure [installazione dell'agente per macchine virtuali Linux](../virtual-machines/extensions/oms-linux.md) per indicazioni sull'installazione.

È possibile connettere l'agente a qualsiasi area di lavoro personalizzata esistente o all'area di lavoro creata dal Centro sicurezza. Se per un'area di lavoro personalizzata non sono abilitate le soluzioni ' Security ' o ' SecurityCenterFree ', sarà necessario applicare una soluzione. Per applicare, selezionare l'area di lavoro o la sottoscrizione personalizzata e applicare un piano tariffario tramite la pagina **criteri di sicurezza-piano tariffario** .

   ![Piano tariffario][1]

Il Centro sicurezza abiliterà la corretta soluzione nell'area di lavoro in base al piano tariffario selezionato.


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Come si rimuovono le estensioni di OMS installate dal Centro sicurezza?<a name="remove-oms"></a>

È possibile rimuovere manualmente l'agente di Log Analytics. Questa operazione non è consigliata perché limita le raccomandazioni e gli avvisi del Centro sicurezza.

> [!NOTE]
> Se la raccolta di dati è abilitata, il Centro sicurezza reinstallerà l'agente dopo la rimozione.  È necessario disabilitare la raccolta di dati prima di rimuovere manualmente l'agente. Consultare "Come si interrompono l'installazione automatica dell'agente e la creazione automatica dell'area di lavoro?" per istruzioni sulla disabilitazione della raccolta di dati.

Per rimuovere manualmente l'agente:

1.    Nel portale aprire **Log Analytics**.

1.    Nella pagina Log Analytics selezionare un'area di lavoro:

1.    Selezionare le macchine virtuali che non si vuole monitorare e selezionare **Disconnetti**.

   ![Rimuovere l'agente][3]

> [!NOTE]
> Se una macchina virtuale Linux dispone già di un agente OMS senza estensione, la rimozione dell'estensione rimuove anche l'agente ed è necessario reinstallarlo.


## <a name="how-do-i-disable-data-collection"></a>Come si disabilita la raccolta dati?

Il provisioning automatico è disattivato per impostazione predefinita. È possibile disabilitare il provisioning automatico nelle risorse in qualsiasi momento disattivando questa impostazione nei criteri di sicurezza. Il provisioning automatico è fortemente consigliato per ottenere avvisi di sicurezza e consigli sugli aggiornamenti del sistema, sulle vulnerabilità del sistema operativo e su Endpoint Protection.

Per disabilitare la raccolta di dati, [accedere al portale di Azure](https://portal.azure.com), selezionare **Sfoglia**, **Centro sicurezza**, quindi selezionare**Selezione criteri**. Selezionare la sottoscrizione per cui si desidera disabilitare il provisioning automatico. Quando si seleziona una sottoscrizione, si apre **Criteri di sicurezza - Raccolta dati**. In **Auto provisioning**, (Provisioning automatico) selezionare **Off**.


## <a name="how-do-i-enable-data-collection"></a>Come si abilita la raccolta dati?

È possibile abilitare la raccolta dei dati per la sottoscrizione nei criteri di sicurezza. Per abilitare la raccolta di dati. [Accedere al portale di Azure](https://portal.azure.com), selezionare **Sfoglia**, quindi **Centro sicurezza** e infine **Criteri di sicurezza**. Selezionare la sottoscrizione per cui si desidera abilitare il provisioning automatico. Quando si seleziona una sottoscrizione, si apre **Criteri di sicurezza - Raccolta dati**. In **Auto provisioning**, (Provisioning automatico) selezionare **On**.


## <a name="what-happens-when-data-collection-is-enabled"></a>Cosa accade quando si abilita la raccolta dati?

Quando è abilitato il provisioning automatico, il Centro sicurezza effettua il provisioning dell'agente di Log Analytics in tutte le VM di Azure supportate e in quelle nuove che vengono create. È consigliabile eseguire il provisioning automatico, ma è disponibile anche l'installazione manuale dell'agente. [Informazioni su come installare l'estensione dell'agente log Analytics](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

L'agente abilita l'evento di creazione di processi 4688 e il campo *CommandLine* all'interno dell'evento 4688. I nuovi processi creati nella VM vengono registrati da EventLog e monitorati dai servizi di rilevamento del Centro sicurezza. Per ulteriori informazioni sui dettagli registrati per ogni nuovo processo, vedere la pagina relativa ai [campi Descrizione in 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). L'agente inoltre raccoglie gli eventi 4688 creati nella macchina virtuale e li archivia nella ricerca.

L'agente abilita anche la raccolta di dati per i [controlli applicazione adattivi](security-center-adaptive-application.md). Centro sicurezza consente di configurare criteri AppLocker locali in modalità di controllo per consentire tutte le applicazioni. Questo criterio provocherà la generazione di eventi da AppLocker, che vengono quindi raccolti e utilizzati dal centro sicurezza. È importante notare che questi criteri non saranno configurati nei computer in cui è già configurato un criterio AppLocker. 

Quando il Centro sicurezza rileva attività sospette nella macchina virtuale, il cliente riceve una notifica tramite posta elettronica se sono state fornite le [informazioni sul contatto di sicurezza](security-center-provide-security-contact-details.md). Un avviso è visibile anche nel dashboard degli avvisi di sicurezza del Centro sicurezza.


## <a name="will-security-center-work-using-an-oms-gateway"></a>Il Centro sicurezza funziona usando un gateway OMS?

Sì. Il Centro sicurezza di Azure USA monitoraggio di Azure per raccogliere i dati da macchine virtuali e server di Azure, usando l'agente Log Analytics.
Per raccogliere i dati, ogni macchina virtuale e il server devono connettersi a Internet tramite HTTPS. La connessione può essere diretta, tramite un proxy o tramite il [gateway OMS](../azure-monitor/platform/gateway.md).


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>L'agente di monitoraggio compromettere le prestazioni dei server?

L'agente e usa una quantità nominale delle risorse di sistema e dovrebbe avere un impatto minimo sulle prestazioni. Per altre informazioni sull'agente, sull'estensione e sull'impatto sulle prestazioni, vedere la [guida alla pianificazione e alla gestione](security-center-planning-and-operations-guide.md#data-collection-and-storage).


## <a name="where-is-my-data-stored"></a>Dove vengono archiviati i dati?

I dati raccolti dall'agente vengono archiviati in un'area di lavoro Log Analytics esistente associata alla sottoscrizione o in una nuova area di lavoro. Per altre informazioni, vedere [Sicurezza dei dati](security-center-data-security.md).


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png
