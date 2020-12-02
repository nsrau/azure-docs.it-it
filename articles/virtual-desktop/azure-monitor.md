---
title: Usare monitor Windows Virtual Desktop Monitor Preview-Azure
description: Come usare monitoraggio di Azure per desktop virtuale di Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e0be6decf28fcbb2edacd5019f567d26403b1f31
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467729"
---
# <a name="use-azure-monitor-for-windows-virtual-desktop-to-monitor-your-deployment-preview"></a>Usare monitoraggio di Azure per desktop virtuale Windows per monitorare la distribuzione (anteprima)

>[!IMPORTANT]
>Monitoraggio di Azure per desktop virtuale Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Monitoraggio di Azure per desktop virtuale Windows (anteprima) è un dashboard basato sulle cartelle di lavoro di monitoraggio di Azure che consente ai professionisti IT di comprendere gli ambienti desktop virtuali Windows. Questo argomento descrive come configurare monitoraggio di Azure per desktop virtuale Windows per monitorare gli ambienti desktop virtuali Windows.

## <a name="requirements"></a>Requisiti

Prima di iniziare a usare monitoraggio di Azure per desktop virtuale Windows, è necessario configurare gli elementi seguenti:

- Tutti gli ambienti desktop virtuali Windows monitorati devono essere basati sulla versione più recente di desktop virtuale di Windows compatibile con Azure Resource Manager.

- Almeno un'area di lavoro configurata Log Analytics.

- Abilitare la raccolta dei dati per gli elementi seguenti nell'area di lavoro Log Analytics:
    - Tutti i contatori delle prestazioni necessari
    - Tutti i contatori delle prestazioni o gli eventi usati in monitoraggio di Azure per desktop virtuale Windows
    - Dati dello strumento di diagnostica per tutti gli oggetti nell'ambiente che verrà monitorato.
    - Macchine virtuali (VM) nell'ambiente da monitorare.

Chiunque stia monitorando monitoraggio di Azure per desktop virtuale Windows per l'ambiente richiederà anche le seguenti autorizzazioni di accesso in lettura:

- Accesso in lettura al gruppo di risorse in cui si trovano le risorse dell'ambiente.

- Accesso in lettura al gruppo o ai gruppi di risorse in cui si trovano gli host di sessione dell'ambiente

>[!NOTE]
> L'accesso in lettura consente agli amministratori di visualizzare i dati. Sono necessarie autorizzazioni diverse per gestire le risorse nel portale di desktop virtuale di Windows.

## <a name="open-azure-monitor-for-windows-virtual-desktop"></a>Apri monitoraggio di Azure per desktop virtuale Windows

È possibile aprire Monitoraggio di Azure per desktop virtuale Windows con uno dei metodi seguenti:

- Passare a [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks).

- Cercare e selezionare **desktop virtuale di Windows** dal portale di Azure, quindi selezionare **Insights (informazioni dettagliate**).

- Cercare e selezionare **monitoraggio di Azure** dalla portale di Azure. Selezionare **hub informazioni dettagliate** in **informazioni dettagliate** e in **altro** selezionare **desktop virtuale Windows** per aprire il dashboard nella pagina Monitoraggio di Azure.

Dopo aver aperto monitoraggio di Azure per desktop virtuale Windows, selezionare una o più caselle di controllo con etichetta **sottoscrizione**, **gruppo di risorse**, **pool host** e intervallo di **tempo** in base all'ambiente che si desidera monitorare.

>[!NOTE]
>Desktop virtuale di Windows attualmente supporta solo il monitoraggio di una sottoscrizione, di un gruppo di risorse e di un pool host alla volta. Se non è possibile trovare l'ambiente che si vuole monitorare, vedere [la documentazione relativa alla risoluzione dei](troubleshoot-azure-monitor.md) problemi per richieste di funzionalità note e problemi.

## <a name="set-up-with-the-configuration-workbook"></a>Configurare con la cartella di lavoro di configurazione

Se è la prima volta che si apre Monitoraggio di Azure per desktop virtuale Windows, è necessario configurare monitoraggio di Azure per le risorse del desktop virtuale di Windows. Per configurare le risorse:

1. Aprire la cartella di lavoro nella portale di Azure.
2. Selezionare **Apri la cartella di lavoro configurazione**.

La cartella di lavoro configurazione configura l'ambiente di monitoraggio e consente di controllare la configurazione al termine del processo di installazione. È importante controllare la configurazione se gli elementi nel dashboard non vengono visualizzati correttamente oppure quando il gruppo di prodotti pubblica gli aggiornamenti che richiedono punti dati aggiuntivi.

## <a name="host-pool-diagnostic-settings"></a>Impostazioni di diagnostica del pool di host

È necessario abilitare le impostazioni di diagnostica di monitoraggio di Azure in tutti gli oggetti all'interno dell'ambiente desktop virtuale Windows che supportano questa funzionalità.

1. Aprire Monitoraggio di Azure per desktop virtuale Windows in [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks), quindi selezionare **cartella di lavoro configurazione**.

2. Selezionare un ambiente da monitorare in **sottoscrizione**, **gruppo di risorse** e **pool di host**.

3. In **impostazioni di diagnostica del pool host** verificare se la diagnostica del desktop virtuale di Windows è abilitata per il pool host. In caso affermativo, verrà visualizzato un messaggio di errore che indica che non è stata trovata alcuna configurazione di diagnostica esistente per il pool host selezionato. 
   
   È necessario abilitare le tabelle seguenti:

    - Checkpoint
    - Errore
    - Gestione
    - Connessioni
    - HostRegistration

    >[!NOTE]
    > Se il messaggio di errore non viene visualizzato, non è necessario eseguire il passaggio 4.

4. Selezionare **Configura pool host**.

5. Selezionare **Distribuisci**.

6. Aggiornare la cartella di lavoro.

Per ulteriori informazioni su come abilitare la diagnostica in tutti gli oggetti nell'ambiente desktop virtuale di Windows o accedere all'area di lavoro Log Analytics, vedere l'articolo relativo all' [invio di diagnostica desktop virtuali Windows a log Analytics](diagnostics-log-analytics.md).

## <a name="configure-log-analytics"></a>Configurare Log Analytics

Per iniziare a usare monitoraggio di Azure per desktop virtuale di Windows, è necessario disporre di almeno un'area di lavoro Log Analytics per raccogliere i dati dall'ambiente che si intende monitorare e fornirli alla cartella di lavoro. Se è già stata configurata una configurazione, andare avanti per [configurare i contatori delle prestazioni](#set-up-performance-counters). Per configurare una nuova area di lavoro Log Analytics per la sottoscrizione di Azure che contiene l'ambiente di desktop virtuale Windows, vedere [creare un'area di lavoro log Analytics nel portale di Azure](../azure-monitor/learn/quick-create-workspace.md).

>[!NOTE]
>Verranno applicati gli addebiti per l'archiviazione dei dati standard per Log Analytics. Per iniziare, è consigliabile scegliere il modello con pagamento in base al consumo e adattarlo quando si ridimensiona la distribuzione e si accettano più dati. Per altre informazioni, vedere [prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="set-up-performance-counters"></a>Set up performance counters (Impostare i contatori delle prestazioni)

È necessario abilitare specifici contatori delle prestazioni per la raccolta in corrispondenza dell'intervallo di campionamento corrispondente nell'area di lavoro Log Analytics. Questi contatori delle prestazioni sono gli unici contatori necessari per monitorare il desktop virtuale di Windows. È possibile disabilitare tutte le altre per ridurre i costi.

Se sono già stati abilitati contatori delle prestazioni e si desidera rimuoverli, seguire le istruzioni riportate in [configurazione dei contatori delle](../azure-monitor/platform/data-sources-performance-counters.md) prestazioni per riconfigurare i contatori delle prestazioni. Mentre l'articolo descrive come aggiungere i contatori, è anche possibile rimuoverli nella stessa posizione.

Se non sono già stati configurati contatori delle prestazioni, di seguito viene illustrato come configurarli per monitoraggio di Azure per desktop virtuale di Windows:

1. Passare a [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks), quindi selezionare la **cartella di lavoro di configurazione** in basso nella finestra.

2. In **log Analytics configurazione** selezionare l'area di lavoro configurata per la sottoscrizione.

3. Nei **contatori delle prestazioni dell'area di lavoro** verrà visualizzato l'elenco dei contatori necessari per il monitoraggio. Sul lato destro dell'elenco, controllare gli elementi nell'elenco dei **contatori mancanti** per abilitare i contatori necessari per avviare il monitoraggio dell'area di lavoro.

4. Selezionare **configura contatori delle prestazioni**.

5. Selezionare **Applica configurazione**.

6. Aggiornare la cartella di lavoro di configurazione e continuare a configurare l'ambiente.

È anche possibile aggiungere nuovi contatori delle prestazioni dopo la configurazione iniziale ogni volta che il servizio viene aggiornato e richiede nuovi strumenti di monitoraggio. È possibile verificare che tutti i contatori necessari siano abilitati selezionandola nell'elenco dei **contatori mancanti** .

>[!NOTE]
>I contatori delle prestazioni di ritardo di input sono compatibili solo con Windows 10 RS5 e versioni successive o Windows Server 2019 e versioni successive.

Per ulteriori informazioni su come aggiungere manualmente i contatori delle prestazioni che non sono già abilitati per la raccolta, vedere [configurazione dei contatori delle prestazioni](../azure-monitor/platform/data-sources-performance-counters.md).

### <a name="set-up-windows-events"></a>Configurare gli eventi di Windows

Successivamente, è necessario abilitare eventi specifici di Windows per la raccolta nell'area di lavoro Log Analytics. Gli eventi descritti in questa sezione sono gli unici requisiti di monitoraggio di Azure per desktop virtuali Windows. È possibile disabilitare tutte le altre per ridurre i costi.

Per configurare gli eventi di Windows:

1. Se sono già stati abilitati gli eventi di Windows e si desidera rimuoverli, rimuovere gli eventi non desiderati prima di utilizzare la cartella di lavoro di configurazione per abilitare il set necessario per il monitoraggio.

2. Passare a monitoraggio di Azure per desktop virtuale Windows in [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks), quindi selezionare **cartella di lavoro configurazione** nella parte inferiore della finestra.

3. Nella **configurazione degli eventi di Windows** è disponibile un elenco di eventi di Windows necessari per il monitoraggio. Sul lato destro dell'elenco è presente l'elenco degli **eventi mancanti** , in cui sono disponibili i nomi di evento e i tipi di evento richiesti che non sono attualmente abilitati per l'area di lavoro. Registrare ognuno di questi nomi per un momento successivo.

4. Selezionare **Apri area di lavoro configurazione**.

5. Selezionare **i dati**.

6. Selezionare **registri eventi di Windows**.

7. Aggiungere i nomi degli eventi mancanti dal passaggio 3 e il tipo di evento richiesto per ciascuno.

8. Aggiornare la cartella di lavoro di configurazione e continuare a configurare l'ambiente.

È possibile aggiungere nuovi eventi di Windows dopo la configurazione iniziale se gli aggiornamenti degli strumenti di monitoraggio richiedono l'abilitazione di nuovi eventi. Per assicurarsi di avere tutti gli eventi necessari abilitati, tornare all'elenco **degli eventi mancanti** e abilitare gli eventuali eventi mancanti.

## <a name="install-the-log-analytics-agent-on-all-hosts"></a>Installare l'agente di Log Analytics in tutti gli host

Infine, è necessario installare l'agente di Log Analytics in tutti gli host nel pool di host per inviare i dati dagli host all'area di lavoro selezionata.

Per installare l'agente di Log Analytics:

1. Passare a monitoraggio di Azure per desktop virtuale Windows in [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks), quindi selezionare **cartella di lavoro configurazione** nella parte inferiore della finestra.

2. Se Log Analytics non è configurato per tutti gli host nel pool host, verrà visualizzato un errore nella parte inferiore della sezione relativa alla configurazione Log Analytics con il messaggio "alcuni host nel pool host non inviano dati all'area di lavoro Log Analytics selezionata". Selezionare **Aggiungi host all'area di lavoro** per aggiungere gli host selezionati. Se il messaggio di errore non viene visualizzato, fermarsi qui.

3. Aggiornare la cartella di lavoro configurazione.

>[!NOTE]
>Per installare l'estensione Log Analytics, è necessario che il computer host sia in esecuzione. Se la distribuzione automatica non riesce in un host, è sempre possibile installare manualmente l'estensione in un host. Per informazioni su come installare l'estensione manualmente, vedere [log Analytics estensione della macchina virtuale per Windows](../virtual-machines/extensions/oms-windows.md).

## <a name="optional-configure-alerts"></a>Facoltativo: configurare gli avvisi

È possibile configurare monitoraggio di Azure per desktop virtuale Windows per ricevere una notifica se si verificano avvisi di monitoraggio di Azure gravi all'interno della sottoscrizione selezionata. A tale scopo, seguire le istruzioni riportate in [rispondere agli eventi con gli avvisi di monitoraggio di Azure](../azure-monitor/learn/tutorial-response.md).

## <a name="diagnostic-and-usage-data"></a>Dati di diagnostica e di utilizzo

Microsoft raccoglie automaticamente i dati relativi a utilizzo e prestazioni tramite l'uso del servizio Monitoraggio di Azure. Microsoft usa questi dati per migliorare la qualità, la sicurezza e l'integrità del servizio.

Per fornire funzionalità di risoluzione dei problemi accurate ed efficienti, i dati raccolti includono l'ID di sessione del portale, Azure Active Directory ID utente e il nome della scheda del portale in cui si è verificato l'evento. Microsoft non raccoglie nomi, indirizzi o altre informazioni di contatto.

Per altre informazioni sulla raccolta e sull'uso dei dati , vedere l'[Informativa sulla privacy Servizi online Microsoft ](https://privacy.microsoft.com/privacystatement).

>[!NOTE]
>Per informazioni sulla visualizzazione o l'eliminazione dei dati personali raccolti dal servizio, vedere [richieste di soggetto dati di Azure per GDPR](/microsoft-365/compliance/gdpr-dsr-azure). Per ulteriori informazioni su GDPR, vedere [la sezione GDPR del portale di attendibilità del servizio](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato configurato il desktop virtuale di Windows portale di Azure, di seguito sono riportate alcune risorse che possono essere utili:

- Consulta il [Glossario](azure-monitor-glossary.md) per scoprire di più su termini e concetti correlati a monitoraggio di Azure per desktop virtuale di Windows.
- Se si verifica un problema, consultare la [Guida alla risoluzione dei problemi](troubleshoot-azure-monitor.md) per assistenza.