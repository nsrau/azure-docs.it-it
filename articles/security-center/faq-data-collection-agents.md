---
title: Domande frequenti sul Centro sicurezza di Azure - Raccolta dati e archiviazione
description: Domande frequenti sulla raccolta dati, gli agenti e le aree di lavoro per il Centro sicurezza di Azure, un prodotto che aiuta a prevenire, rilevare e rispondere alle minacce
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
ms.openlocfilehash: 315183040515110a6a21afcd00e12d1b12313170
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341839"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>Domande frequenti sulla raccolta dati, gli agenti e le aree di lavoro

Il Centro sicurezza raccoglie i dati dalle macchine virtuali (VM) di Azure, dai set di scalabilità di macchine virtuali, dai contenitori IaaS e dai computer non Azure (incluse le macchine locali) per monitorare le vulnerabilità e le minacce alla sicurezza. I dati vengono raccolti tramite l'agente di Log Analytics, che legge diverse configurazioni correlate alla sicurezza oltre ai log eventi del computer e copia i dati nell'area di lavoro per eseguire l'analisi.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Vengono addebitati costi per i log di Monitoraggio di Azure nelle aree di lavoro create dal Centro sicurezza?

No. Le aree di lavoro create dal Centro sicurezza non comportano addebiti di Monitoraggio di Azure, benché siano configurate per la fatturazione di Monitoraggio di Azure per nodo. La fatturazione del Centro sicurezza è sempre basata sui criteri di sicurezza del Centro sicurezza e sulle soluzioni installate in un'area di lavoro:

- **Azure Defender off** : il Centro sicurezza Abilita la soluzione ' SecurityCenterFree ' nell'area di lavoro predefinita. Non verrà addebitato alcun costo se Azure Defender è disattivato.

- **Azure Defender on** : il Centro sicurezza Abilita la soluzione ' Security ' nell'area di lavoro predefinita.

Per altre informazioni sui prezzi, vedere [Prezzi di Centro sicurezza](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> Il piano tariffario di Log Analytics per le aree di lavoro create dal Centro sicurezza non influisce sulla fatturazione del Centro sicurezza.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-is-the-log-analytics-agent"></a>Che cos'è il Log Analytics Agent?

Per monitorare vulnerabilità e minacce per la sicurezza, il Centro sicurezza di Azure dipende dall' [agente di log Analytics](../azure-monitor/platform/log-analytics-agent.md) , ovvero lo stesso agente usato dal servizio monitoraggio di Azure. 

L'agente viene talvolta definito Microsoft Monitoring Agent (o "MMA"). 

L'agente raccoglie diversi dettagli di configurazione relativi alla sicurezza e registri eventi da computer connessi, quindi copia i dati nell'area di lavoro di Log Analytics per un'ulteriore analisi. I dati raccolti sono ad esempio il tipo di sistema operativo e la versione, i log del sistema operativo (log eventi Windows), i processi in esecuzione, il nome del computer, gli indirizzi IP e l'utente connesso.

Verificare che nei computer sia in esecuzione uno dei sistemi operativi supportati per l'agente, come descritto nelle pagine seguenti:

* [Agente di Log Analytics per i sistemi operativi Windows supportati](../azure-monitor/platform/agents-overview.md#supported-operating-systems)

* [Agente di Log Analytics per i sistemi operativi Linux supportati](../azure-monitor/platform/agents-overview.md#supported-operating-systems)

Altre informazioni sui [dati raccolti dall'agente log Analytics](security-center-enable-data-collection.md).




## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-log-analytics-agent-installation"></a>Che cosa determina il diritto di una macchina virtuale al provisioning automatico dell'installazione dell'agente di Log Analytics?

Le macchine virtuali IaaS Windows o Linux hanno diritto al provisioning automatico se:

- L'estensione agente di Log Analytics non è attualmente installata nella macchina virtuale.
- La macchina virtuale è nello stato di esecuzione.
- L'[agente di macchine virtuali di Azure](../virtual-machines/extensions/agent-windows.md) Linux è installato.
- La macchina virtuale non viene usata come appliance, ad esempio web application firewall o firewall di nuova generazione.


## <a name="where-is-the-default-log-analytics-workspace-created"></a>Dove viene creata l'area di lavoro predefinita di Log Analytics?

La località dell'area di lavoro predefinita dipende dall'area di Azure:

- Per le macchine virtuali in Stati Uniti e Brasile, la località dell'area di lavoro è Stati Uniti
- Per le macchine virtuali in Canada, la località dell'area di lavoro è Canada
- Per le macchine virtuali in Europa, la località dell'area di lavoro è Europa
- Per le macchine virtuali nel Regno Unito, la località dell'area di lavoro è Regno Unito
- Per le macchine virtuali in Asia orientale e Asia sud-orientale, la località dell'area di lavoro è Asia
- Per le macchine virtuali in Corea del Sud, la località dell'area di lavoro è Corea del Sud
- Per le macchine virtuali in India, la località dell'area di lavoro è India
- Per le macchine virtuali in Giappone, la località dell'area di lavoro è Giappone
- Per le macchine virtuali in Cina, la località dell'area di lavoro è Cina
- Per le macchine virtuali in Australia, la località dell'area di lavoro è Australia


## <a name="what-data-is-collected-by-the-log-analytics-agent"></a>Quali dati vengono raccolti dall'agente di Log Analytics?

Per un elenco completo delle applicazioni e dei servizi monitorati dall'agente, vedere [elementi monitorati da monitoraggio di Azure](../azure-monitor/monitor-reference.md#azure-services).

> [!IMPORTANT]
> Si noti che per alcuni servizi, ad esempio Azure firewall, se è stata abilitata la registrazione e si è scelta una risorsa loquace da registrare, ad esempio impostando il log su *verbose*, è possibile che si verifichino effetti significativi sulle esigenze di archiviazione dell'area di lavoro log Analytics. 


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>È possibile eliminare le aree di lavoro predefinite create dal Centro sicurezza?

**L'eliminazione dell'area di lavoro predefinita non è consigliata.** Il Centro sicurezza usa le aree di lavoro predefinite per archiviare i dati di sicurezza dalle macchine virtuali. Se si elimina un'area di lavoro, il Centro sicurezza non potrà raccogliere tali dati e alcune raccomandazioni e alcuni avvisi di sicurezza non saranno disponibili.

Per il ripristino, rimuovere l'agente di Log Analytics dalle macchine virtuali connesse all'area di lavoro eliminata. Il Centro sicurezza reinstalla l'agente e crea nuove aree di lavoro predefinite.

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

1. Selezionare **Salva**. Verrà chiesto se si vogliono riconfigurare le macchine virtuali monitorate.

    - Selezionare **No** se si desidera che le nuove impostazioni dell'area di lavoro si **applichino solo alle nuove macchine virtuali**. Le nuove impostazioni dell'area di lavoro si applicano solo alle nuove installazioni dell'agente, ovvero alle macchine virtuali appena individuate in cui non è installato l'agente di Log Analytics.
    - Selezionare **Sì** se si desidera che le nuove impostazioni dell'area di lavoro si **applichino a tutte le macchine virtuali**. In aggiunta, ogni macchina virtuale connessa a un'area di lavoro creata dal Centro sicurezza viene ricollegata alla nuova area di lavoro di destinazione.

    > [!NOTE]
    > Se si seleziona **Sì**, non eliminare aree di lavoro create dal Centro sicurezza finché tutte le macchine virtuali non vengono ricollegate alla nuova area di lavoro di destinazione. Questa operazione non riesce se un'area di lavoro viene eliminata troppo presto.

    - Selezionare **Annulla** per annullare l'operazione.

## <a name="what-if-the-log-analytics-agent-was-already-installed-as-an-extension-on-the-vm"></a>Cosa accade se l'agente di Log Analytics è già stato installato come estensione nella macchina virtuale?<a name="mmaextensioninstalled"></a>

Quando l'agente di monitoraggio viene installato come estensione, la configurazione dell'estensione consente il reporting a una sola area di lavoro. Il Centro sicurezza non esegue l'override delle connessioni esistenti alle aree di lavoro degli utenti. Il Centro sicurezza archivierà i dati di sicurezza da una macchina virtuale in un'area di lavoro già connessa, a condizione che vi sia installata la soluzione "Security" o "SecurityCenterFree". Durante questo processo, il Centro sicurezza può aggiornare la versione dell'estensione all'ultima versione.

Per altre informazioni, vedere [Provisioning automatico nel caso di installazione di un agente preesistente](security-center-enable-data-collection.md#preexisting).



## <a name="what-if-a-log-analytics-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>Cosa accade se un agente di Log Analytics è installato direttamente nel computer, ma non come estensione (agente diretto)?<a name="directagentinstalled"></a>

Se l'agente di Log Analytics viene installato direttamente nella macchina virtuale e non come estensione di Azure, il Centro sicurezza installerà l'estensione agente di Log Analytics e potrà aggiornare l'agente Log Analytics all'ultima versione.

L'agente installato continuerà a generare report per le aree di lavoro già configurate e in più genererà report per l'area di lavoro configurata nel Centro sicurezza (il multihoming è supportato nei computer Windows).

Se l'area di lavoro configurata è un'area di lavoro utente e non l'area di lavoro predefinita del Centro sicurezza, sarà necessario installare la soluzione "Security/"SecurityCenterFree" su di essa in modo che il Centro sicurezza avvii l'elaborazione degli eventi provenienti dalle macchine virtuali e dai computer che inviano report a tale area di lavoro.

Per i computer Linux, il multihoming dell'agente non è ancora supportato. Di conseguenza, se viene rilevata un'installazione di un agente esistente, il provisioning automatico non verrà eseguito e la configurazione del computer non verrà modificata.

Per i computer esistenti in sottoscrizioni di cui è stato eseguito l'onboarding nel Centro sicurezza prima del 17 marzo 2019, quando verrà rilevato un agente esistente, l'estensione agente di Log Analytics non verrà installata e il computer non verrà modificato. Per questi computer, vedere la raccomandazione "Risolvere i problemi di integrità dell'agente di monitoraggio nei computer" per risolvere i problemi di installazione dell'agente

Per altre informazioni, vedere la sezione successiva [Cosa accade se un agente diretto di System Center Operations Manager o OMS è già installato nella macchina virtuale?](#scomomsinstalled)

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>Cosa accade se un agente di System Center Operations Manager è già installato nella macchina virtuale?<a name="scomomsinstalled"></a>

Il Centro sicurezza installerà l'estensione agente di Log Analytics in modalità affiancata con l'agente di System Center Operations Manager esistente. L'agente esistente continuerà a inviare report normalmente al server System Center Operations Manager. Si noti che l'agente di Operations Manager e l'agente di Log Analytics condividono librerie di runtime comuni, che durante questo processo verranno aggiornate all'ultima versione. Nota: se è installata la versione 2012 dell'agente di Operations Manager, non attivare il provisioning automatico. Quando anche il server di Operations Manager è nella versione 2012, si potrebbero perdere funzionalità per la gestibilità.


## <a name="what-is-the-impact-of-removing-these-extensions"></a>Qual è l'impatto della rimozione delle estensioni?

Se si rimuove l'estensione Microsoft Monitoring, il Centro sicurezza non potrà raccogliere i dati di sicurezza dalla VM e alcune raccomandazioni e alcuni avvisi di sicurezza non saranno disponibili. Entro 24 ore il Centro sicurezza determina che nella VM non è presente l'estensione e la reinstalla.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Come si interrompono l'installazione automatica dell'agente e la creazione automatica dell'area di lavoro?

È possibile disattivare il provisioning automatico per le sottoscrizioni nei criteri di sicurezza, ma questa opzione non è consigliata. La disattivazione del provisioning automatico limita le raccomandazioni e gli avvisi del Centro sicurezza. Per disabilitare il provisioning automatico:

1. Se per la sottoscrizione è abilitata la funzionalità Azure Defender, aprire i criteri di sicurezza per la sottoscrizione e selezionare **Azure Defender off**.

    :::image type="content" source="./media/security-center-platform-migration-faq/pricing-tier.png" alt-text="Abilitare o disabilitare Azure Defender":::

1. Disattivare quindi il provisioning automatico selezionando **Disattivato** nella pagina **Criteri di sicurezza - Raccolta dati**.
   ![Raccolta di dati][2]


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>È consigliabile rifiutare esplicitamente l'installazione automatica dell'agente e la creazione automatica dell'area di lavoro?

> [!NOTE]
> Assicurarsi di aver letto con attenzione le sezioni [Quali sono le implicazioni del rifiuto esplicito?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) e [Procedure consigliate quando si rifiuta esplicitamente](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) se si sceglie di rifiutare esplicitamente il provisioning automatico.

È possibile rifiutare esplicitamente il provisioning automatico se si applicano le condizioni seguenti:

- L'installazione automatica dell'agente da parte del Centro sicurezza si applica all'intera sottoscrizione. Non è possibile applicare l'installazione automatica a un sottoinsieme di macchine virtuali. Se sono presenti macchine virtuali critiche in cui l'agente di Log Analytics non può essere installato, sarà necessario rifiutare esplicitamente il provisioning automatico.
- L'installazione dell'estensione agente di Log Analytics aggiorna la versione dell'agente. Questa condizione si applica a un agente diretto e a un agente di System Center Operations Manager. In quest'ultimo, l'agente di Operations Manager e l'agente di Log Analytics condividono librerie di runtime comuni, che verranno aggiornate durante il processo. Se la versione dell'agente di Operations Manager installato è la 2012 e l'agente viene aggiornato, le capacità di gestibilità potrebbero andare perse se anche la versione del server di Operations Manager è la 2012. Valutare l'opportunità di rifiutare esplicitamente il provisioning automatico se la versione dell'agente di Operations Manager installato è la 2012.
- Se si ha un'area di lavoro personalizzata esterna alla sottoscrizione (un'area di lavoro centralizzata), è consigliabile rifiutare esplicitamente il provisioning automatico. È possibile installare manualmente l'estensione agente di Log Analytics e connetterla all'area di lavoro senza che il Centro sicurezza esegua l'override della connessione.
- Se si vuole evitare di creare più aree di lavoro per sottoscrizione e si dispone di un'area di lavoro personalizzata all'interno della sottoscrizione, sono disponibili due opzioni:

   1. È possibile rifiutare esplicitamente il provisioning automatico. Dopo la migrazione, configurare le impostazioni dell'area di lavoro predefinita come descritto in [Come usare l'area di lavoro Log Analytics esistente](#how-can-i-use-my-existing-log-analytics-workspace).

   1. In alternativa, è possibile consentire il completamento della migrazione, l'installazione dell'agente Log Analytics nelle macchine virtuali e la connessione delle macchine virtuali all'area di lavoro creata. Quindi, selezionare l'area di lavoro personalizzata configurando l'impostazione dell'area di lavoro predefinita in modo da acconsentire esplicitamente alla riconfigurazione degli agenti già installati. Per altre informazioni, vedere [Come usare l'area di lavoro Log Analytics esistente](#how-can-i-use-my-existing-log-analytics-workspace).


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Quali sono le implicazioni del rifiuto esplicito del provisioning automatico?

Al termine della migrazione, il Centro sicurezza non potrà raccogliere dati sulla sicurezza dalla macchina virtuale e alcune raccomandazioni e alcuni avvisi di sicurezza non saranno disponibili. Se si rifiuta esplicitamente, installare l'agente di Log Analytics manualmente. Vedere [Procedure consigliate quando si rifiuta esplicitamente](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Quali sono le procedure consigliate quando si rifiuta esplicitamente il provisioning automatico?

Installare manualmente l'estensione agente Log Analytics in modo che il Centro sicurezza possa raccogliere i dati sulla sicurezza dalle macchine virtuali e fornire raccomandazioni e avvisi. Vedere [installazione dell'agente per macchine virtuali Windows](../virtual-machines/extensions/oms-windows.md) oppure [installazione dell'agente per macchine virtuali Linux](../virtual-machines/extensions/oms-linux.md) per indicazioni sull'installazione.

È possibile connettere l'agente a qualsiasi area di lavoro personalizzata esistente o all'area di lavoro creata dal Centro sicurezza. Se per un'area di lavoro personalizzata non è abilitata la soluzione "Security" o "SecurityCenterFree", sarà necessario applicare una soluzione. A tale scopo, selezionare l'area di lavoro personalizzata o la sottoscrizione e applicare un piano tariffario tramite la pagina **Criteri di sicurezza - Piano tariffario**.

:::image type="content" source="./media/security-center-platform-migration-faq/pricing-tier.png" alt-text="Abilitare o disabilitare Azure Defender":::

Il Centro sicurezza abiliterà la corretta soluzione nell'area di lavoro in base al piano tariffario selezionato.


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Come si rimuovono le estensioni di OMS installate dal Centro sicurezza?<a name="remove-oms"></a>

È possibile rimuovere manualmente l'agente di Log Analytics. Questa operazione non è consigliata perché limita le raccomandazioni e gli avvisi del Centro sicurezza.

> [!NOTE]
> Se la raccolta di dati è abilitata, il Centro sicurezza reinstallerà l'agente dopo la rimozione.  È necessario disabilitare la raccolta di dati prima di rimuovere manualmente l'agente. Consultare "Come si interrompono l'installazione automatica dell'agente e la creazione automatica dell'area di lavoro?" per istruzioni sulla disabilitazione della raccolta di dati.

Per rimuovere manualmente l'agente:

1.    Nel portale aprire **Log Analytics**.

1.    Nella pagina Log Analytics selezionare un'area di lavoro:

1.    Selezionare le macchine virtuali da escludere dal monitoraggio, quindi selezionare **Disconnetti**.

   ![Rimuovere l'agente][3]

> [!NOTE]
> Se una macchina virtuale Linux include già un agente OMS non dell'estensione, la rimozione dell'estensione comporta anche la rimozione dell'agente e sarà necessario reinstallarlo.


## <a name="how-do-i-disable-data-collection"></a>Come si disabilita la raccolta dati?

Il provisioning automatico è fortemente consigliato per ottenere gli avvisi di sicurezza e le raccomandazioni sugli aggiornamenti del sistema, le vulnerabilità del sistema operativo e la protezione degli endpoint. Per impostazione predefinita, il provisioning automatico è disabilitato.

Se è stata abilitata, ma ora si vuole disabilitarla:

1. Dal [portale di Azure aprire il](https://portal.azure.com) **Centro sicurezza** e selezionare **criteri di sicurezza**.

1. Selezionare la sottoscrizione in cui si vuole disabilitare il provisioning automatico.

    **Criteri di sicurezza:** viene visualizzata la raccolta dati.

1. In **Auto provisioning**, (Provisioning automatico) selezionare **Off**.


## <a name="how-do-i-enable-data-collection"></a>Come si abilita la raccolta dati?

È possibile abilitare la raccolta dei dati per la sottoscrizione nei criteri di sicurezza. Per abilitare la raccolta di dati. [Accedere al portale di Azure](https://portal.azure.com), selezionare **Sfoglia**, quindi **Centro sicurezza** e infine **Criteri di sicurezza**. Selezionare la sottoscrizione per cui si desidera abilitare il provisioning automatico. Quando si seleziona una sottoscrizione, si apre **Criteri di sicurezza - Raccolta dati**. In **Auto provisioning**, (Provisioning automatico) selezionare **On**.


## <a name="what-happens-when-data-collection-is-enabled"></a>Cosa accade quando si abilita la raccolta dati?

Dopo aver abilitato il provisioning automatico, il Centro sicurezza effettua il provisioning dell'agente di Log Analytics in tutte le macchine virtuali di Azure supportate e nelle nuove macchine create. Il provisioning automatico è consigliato, ma è disponibile anche l'installazione manuale dell'agente. [Informazioni su come installare l'estensione agente di Log Analytics](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

L'agente abilita l'evento di creazione di processi 4688 e il campo *CommandLine* all'interno dell'evento 4688. I nuovi processi creati nella macchina virtuale vengono registrati da EventLog e monitorati dai servizi di rilevamento del Centro sicurezza. Per altre informazioni sui dettagli registrati per ogni nuovo processo, vedere i [campi descrizione in 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). L'agente inoltre raccoglie gli eventi 4688 creati nella macchina virtuale e li archivia nella ricerca.

L'agente abilita anche la raccolta di dati per i [controlli applicazione adattivi](security-center-adaptive-application.md). Centro sicurezza consente di configurare criteri AppLocker locali in modalità di controllo per consentire tutte le applicazioni. In base a questi criteri, AppLocker genererà eventi che vengono poi raccolti e sfruttati dal Centro sicurezza. È importante notare che questi criteri non saranno configurati nei computer in cui è già configurato un criterio AppLocker. 

Quando il Centro sicurezza rileva attività sospette nella macchina virtuale, il cliente riceve una notifica tramite posta elettronica se sono state fornite le [informazioni sul contatto di sicurezza](security-center-provide-security-contact-details.md). Viene visualizzato un avviso anche nel dashboard degli avvisi di sicurezza del Centro sicurezza.


## <a name="will-security-center-work-using-an-oms-gateway"></a>Il Centro sicurezza funziona usando un gateway OMS?

Sì. Il Centro sicurezza di Azure usa Monitoraggio di Azure per raccogliere dati da macchine virtuali e server di Azure usando l'agente di Log Analytics.
Per raccogliere i dati, ogni macchina virtuale e server deve connettersi a Internet tramite HTTPS. La connessione può essere diretta, tramite proxy o tramite il [gateway OMS](../azure-monitor/platform/gateway.md).


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>L'agente di monitoraggio compromettere le prestazioni dei server?

L'agente e usa una quantità nominale delle risorse di sistema e dovrebbe avere un impatto minimo sulle prestazioni. Per altre informazioni sull'agente, sull'estensione e sull'impatto sulle prestazioni, vedere la [guida alla pianificazione e alla gestione](security-center-planning-and-operations-guide.md#data-collection-and-storage).




<!--Image references-->
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png