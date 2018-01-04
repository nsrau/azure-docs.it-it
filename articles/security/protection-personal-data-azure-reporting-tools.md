---
title: Documentare la protezione dei dati personali con gli strumenti di creazione di report di Azure | Microsoft Docs
description: Come usare i servizi e le tecnologie di creazione di report di Azure per proteggere la privacy dei dati personali.
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.openlocfilehash: 7ce6297d7d6b61ac95df58db3fa1a2a0a123a64e
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="document-protection-of-personal-data-with-azure-reporting-tools"></a>Documentare la protezione dei dati personali con gli strumenti di creazione di report di Azure

Questo articolo illustra come usare i servizi e le tecnologie di creazione di report di Azure per proteggere la privacy dei dati personali.

## <a name="scenario"></a>Scenario

Un'importante compagnia di viaggi in crociera, con sede negli Stati Uniti, sta espandendo le proprie operazioni per offrire itinerari nel Mar Mediterraneo e nel Mar Baltico, nonché nelle isole britanniche. Per supportare tale iniziativa, ha acquistato diverse linee minori con sede in Italia, Germania, Danimarca e Regno Unito.

L'azienda usa Microsoft Azure per l'elaborazione e l'archiviazione dei dati aziendali. Questi includono informazioni personali come nomi, indirizzi, numeri di telefono e dati delle carte di credito della base clienti globale, nonché informazioni tradizionali del reparto risorse umane come indirizzi, numeri di telefono, codici fiscali e altri dati dei dipendenti aziendali in tutte le sedi. La linea di crociere gestisce anche un database di grandi dimensioni dei membri dei programmi fedeltà e premi, che include informazioni personali per tenere traccia delle relazioni con i clienti attuali e del passato.

I dipendenti aziendali accedono alla rete dagli uffici remoti della società, mentre agenti di viaggio in tutto il mondo hanno accesso ad alcune risorse aziendali.

## <a name="problem-statement"></a>Presentazione del problema

L'azienda deve proteggere i dati personali e la privacy di clienti e dipendenti attraverso una strategia di sicurezza a più livelli che fa uso delle funzionalità di sicurezza e gestione di Azure per imporre rigidi controlli sull'accesso e l'elaborazione dei dati personali. Inoltre, deve poter dimostrare a revisori interni ed esterni le misure di protezione attuate.

## <a name="company-goal"></a>Obiettivo dell'azienda

Come parte di una strategia di difesa avanzata, uno degli obiettivi dell'azienda è tenere traccia di tutti gli accessi e delle elaborazioni di dati personali e poter documentare che sono state adottate e attuate misure adeguate di protezione della privacy per i dati personali.

## <a name="solutions"></a>Soluzioni

Microsoft Azure offre strumenti completi di monitoraggio, registrazione e diagnostica per tenere traccia e registrare attività ed eventi associati all'accesso e all'elaborazione di dati personali, flusso geografico di dati e accesso di terze parti a dati personali. La sicurezza dei dati personali nel cloud è una responsabilità condivisa e, tra le altre cose, Microsoft fornisce ai clienti:

- Informazioni dettagliate sull'elaborazione dei dati dei clienti da parte di Microsoft

- Misure di sicurezza gestite da Microsoft

- Informazioni sull'uso e la destinazione dei dati dei clienti

- Informazioni dettagliate sul processo di revisione della privacy di Microsoft

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) è un servizio Microsoft per la gestione delle identità e delle directory multi-tenant, basato sul cloud. Le funzionalità di creazione di report su accesso e controllo offrono informazioni dettagliate sulle attività di accesso e utilizzo dell'applicazione, che consentono di monitorare e garantire l'accesso appropriato ai dati personali di clienti e dipendenti.

Esistono due tipi di report attività:

- I [log/report delle attività di controllo](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) offrono un resoconto dettagliato delle attività di sistema

- Il [log/report delle attività di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) mostra chi ha eseguito ogni attività elencata nel report di controllo

Se usati insieme, i due report permettono di tenere traccia della cronologia di ogni attività eseguita e degli utenti che l'hanno eseguita. Entrambi i tipi di report sono personalizzabili e possono essere filtrati.

#### <a name="how-do-i-access-the-audit-and-security-logs"></a>Come accedere ai log di controllo e sicurezza

Per accedere ai log di controllo e sicurezza dal portale di Active Directory, è possibile procedere in tre modi diversi: tramite la sezione **Attività**, selezionando **Log di controllo** o **Accessi**, oppure da **Utenti e gruppi** o **Applicazioni aziendali** in **Gestione** in Active Directory. È anche possibile accedere ai report tramite l'API di creazione report di Azure Active Directory. 

1. Nel portale di Azure selezionare **Azure Active Directory**.

2. Nella sezione **Attività** selezionare **Log di controllo**.

    ![](media/protection-personal-data-azure-reporting-tools/image001.png)

3. Per personalizzare la visualizzazione elenco, fare clic su **Colonne** nella barra degli strumenti.

4.  Selezionare un elemento nella visualizzazione elenco per visualizzare tutti i relativi dettagli disponibili.

    ![](media/protection-personal-data-azure-reporting-tools/image003.png)

La creazione di report di Azure Active Directory include anche due tipi di report di sicurezza, **Utenti contrassegnati per il rischio** e **Accessi a rischio**, che consentono di monitorare i rischi potenziali nell'ambiente di Azure.

Per altre informazioni sul servizio di creazione di report, vedere [Creazione di report in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

Per informazioni più specifiche sui report disponibili in Azure Active Directory, vedere la sezione relativa ai [report attività di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal#activity-reports). Questa pagina offre informazioni più dettagliate su come accedere e usare i [report delle attività dei log di controllo](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) e i [report delle attività di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) nel portale. Include anche informazioni relative ai report sulla sicurezza [Utenti contrassegnati per il rischio](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-user-at-risk) e [Accessi a rischio](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-risky-sign-ins).

Per altre informazioni su come connettersi alla creazione di report di Azure Directory a livello di codice, vedere [Informazioni di riferimento sull'API di controllo di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference).

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/) può [raccogliere dati da Monitoraggio di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) per metterli in relazione con altri dati e offrire un'analisi ulteriore. Monitoraggio di Azure raccoglie e analizza i dati di monitoraggio per l'ambiente di Azure. 

Gli strumenti di analisi in Log Analytics, come le ricerche nei log, le visualizzazioni e le soluzioni, vengono applicati a tutti i dati raccolti per offrire un'analisi centralizzata dell'intero ambiente. Log Analytics permette di aggregare e analizzare log di IIS, registri eventi di Windows e SysLog, consentendo così di rilevare potenziali violazioni della sicurezza dei dati personali che potrebbero esporre tali dati a utenti non autorizzati.

#### <a name="how-do-i-use-log-analytics"></a>Uso di Log Analytics

È possibile accedere a Log Analytics tramite il portale di OMS o il portale di Azure, da qualsiasi Web browser. Log Analytics include un linguaggio di query che permette di recuperare e consolidare rapidamente i dati nel repository. È possibile creare e salvare ricerche log per analizzare direttamente i dati nel portale.

Per creare un'area di lavoro di Log Analytics nel portale di Azure, seguire questa procedura:

1. Nell'elenco dei servizi in Marketplace selezionare **Log Analytics**.

2. Selezionare **Crea** e quindi specificare il nome dell'area di lavoro di OMS, selezionare la sottoscrizione, il gruppo di risorse, la località e il piano tariffario.

3. Fare clic su **OK** per visualizzare un elenco delle aree di lavoro.

4. Selezionare un'area di lavoro per visualizzarne i dettagli.

    ![](media/protection-personal-data-azure-reporting-tools/image004.png)

Per altre informazioni sul servizio, vedere la [documentazione di Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

Per creare un'area di lavoro di valutazione e apprendere i concetti base dell'uso del servizio, vedere l'esercitazione [Introduzione a un'area di lavoro di Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started).

Per informazioni più specifiche su come connettersi per usare Log Analytics con i log descritti sopra, vedere le pagine Web seguenti:

[Origini dei dati del registro eventi di Windows in Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events)

[Log di IIS in Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs)

[Origini dati Syslog in Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-syslog)

### <a name="azure-monitorazure-activity-log"></a>Monitoraggio di Azure/Log attività di Azure 

Attualmente [Monitoraggio di Azure](https://azure.microsoft.com/services/monitor/) offre log e metrica dell'infrastruttura di livello base per la maggior parte dei servizi in Microsoft Azure.
Il monitoraggio permette di ottenere informazioni dettagliate complete sulle applicazioni Azure. Monitoraggio di Azure si basa sull'estensione Diagnostica di Azure (Windows o Linux) per raccogliere la maggior parte delle metriche e dei log a livello di applicazione. Il [Log attività di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) è una delle risorse che è possibile visualizzare con Monitoraggio di Azure. Tiene traccia di ogni chiamata API e fornisce numerose informazioni sulle attività eseguite in [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
Nei log attività, in precedenza chiamati log operativi o di controllo, è possibile cercare informazioni relative alla risorsa così come visualizzate dall'infrastruttura di Azure. 

Anche se molte delle informazioni registrate nel log attività sono relative alle prestazioni e all'integrità del servizio, sono incluse anche informazioni relative alla protezione dei dati. L'uso del log attività permette di acquisire informazioni dettagliate su qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sulle risorse nella sottoscrizione di Azure.

Ad esempio, restituisce un record quando un amministratore elimina un gruppo di sicurezza di rete, perché ciò può influire sulla protezione dei dati personali. Le voci del log attività vengono archiviate in Monitoraggio di Azure per 90 giorni.

#### <a name="how-do-i-use-the-data-collected-by-azure-monitor"></a>Come usare i dati raccolti da Monitoraggio di Azure

È possibile usare i dati del log attività e delle altre risorse di Monitoraggio di Azure in diversi modi.

- È possibile trasmettere i dati ad altre località in tempo reale.

- È possibile archiviare i dati per periodi di tempo più lunghi rispetto a quelli predefiniti, usando un [account di archiviazione Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction) e impostando i criteri di conservazione.

- È possibile visualizzare i dati in grafici usando il [portale di Azure](https://azure.microsoft.com/features/azure-portal/), [Azure Application Insights](https://azure.microsoft.com/services/application-insights/), [Microsoft Power BI](https://powerbi.microsoft.com/) o strumenti di visualizzazione di terze parti.

- È possibile eseguire query sui dati usando l'API REST di Monitoraggio di Azure, i comandi dell'interfaccia della riga di comando, i cmdlet di [PowerShell](https://docs.microsoft.com/powershell/) o .NET SDK.

Per iniziare a usare Monitoraggio di Azure, selezionare **Altri servizi** nel portale di Azure.

1. Nella sezione di **monitoraggio e gestione** scorrere fino a **Monitoraggio**.

    ![](media/protection-personal-data-azure-reporting-tools/image005.png)

2.  Monitoraggio di Azure viene aperto nella visualizzazione **Log attività**.

    ![](media/protection-personal-data-azure-reporting-tools/image007.png)

È possibile creare e salvare le query per i filtri comuni, quindi aggiungere le query più importanti a un dashboard del portale, così da sapere sempre se si sono verificati eventi che soddisfano i criteri configurati.

1. È possibile filtrare la visualizzazione per gruppo di risorse, intervallo di tempo e categoria di eventi.

    ![](media/protection-personal-data-azure-reporting-tools/image008.png)

2. È quindi possibile aggiungere query a un dashboard del portale facendo clic sul pulsante **Aggiungi**. Ciò consente di creare un'unica fonte di informazioni per i dati operativi nei servizi. Il nome della query e il numero di risultati verranno visualizzati nel dashboard.

È anche possibile usare Monitoraggio di Azure per visualizzare la metrica per tutte le risorse di Azure, configurare gli avvisi e le impostazioni di diagnostica ed eseguire ricerche nel log. Per altre informazioni su come usare Monitoraggio di Azure e il log attività, vedere [Introduzione a Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started).

### <a name="azure-diagnostics"></a>Diagnostica Azure 

La funzionalità di diagnostica in Azure consente di raccogliere dati da diverse origini. I registri eventi di Windows, tra cui il log di sicurezza, possono essere particolarmente utili per rilevare e documentare la protezione dei dati personali. Il log di sicurezza tiene traccia di eventi di accesso riuscito e non riuscito, nonché di modifiche alle autorizzazioni, modelli indicativi di determinati tipi di attacchi, modifiche ai criteri di sicurezza, modifiche all'appartenenza al gruppo di sicurezza e molto altro.

Ad esempio, l'ID evento 4695 segnala il tentativo di rimuovere la protezione di dati protetti controllabili. Si riferisce a Data Protection API (DPAPI), che consente di proteggere dati quali chiavi private, credenziali archiviate e altre informazioni riservate.

#### <a name="how-do-i-enable-the-diagnostics-extension-for-windows-vms"></a>Come abilitare l'estensione di diagnostica per le macchine virtuali Windows

È possibile usare PowerShell per abilitare l'estensione di diagnostica per una macchina virtuale Windows e raccogliere dati di log. La procedura varia a seconda del modello di distribuzione usato, Resource Manager o classica. Per abilitare l'estensione di diagnostica in una macchina virtuale esistente creata con il modello di distribuzione Resource Manager, è possibile usare il cmdlet di PowerShell [Set-AzureRMVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmdiagnosticsextension?view=azurermps-4.3.1).

   ![](media/protection-personal-data-azure-reporting-tools/image009.png)

*\$diagnosticsconfig_path* è il percorso del file contenente la configurazione di diagnostica in formato XML. Per istruzioni più dettagliate su come abilitare la diagnostica di Azure in un macchina virtuale, vedere [Usare PowerShell per abilitare Diagnostica di Azure in una macchina virtuale Windows](https://docs.microsoft.com/azure/virtual-machines/windows/ps-extensions-diagnostics).

L'estensione di diagnostica di Azure consente di trasferire i dati raccolti in un account di archiviazione di Azure o di inviarli a servizi come Application Insights. È quindi possibile usare i dati per il controllo.

#### <a name="how-do-i-store-and-view-diagnostic-data"></a>Come archiviare e visualizzare dati di diagnostica

È importante ricordare che i dati di diagnostica non vengono archiviati definitivamente a meno che non vengano trasferiti nell'Emulatore di archiviazione di Microsoft Azure o in Archiviazione di Azure. Per archiviare e visualizzare i dati di diagnostica in Archiviazione di Azure, seguire questa procedura:

1. Specificare un account di archiviazione nel file ServiceConfiguration.cscfg. Diagnostica di Azure può usare il servizio BLOB o il servizio tabelle, a seconda del tipo di dati. I registri eventi di Windows vengono archiviati in formato tabella.

2. Trasferire i dati. È possibile richiedere il trasferimento dei dati di diagnostica mediante il file di configurazione. Per SDK 2.4 e versioni precedenti, è anche possibile impostare la richiesta a livello di codice.

3. Visualizzare i dati con [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer), [Esplora Server](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) in Visual Studio o [Azure Diagnostics Manager](https://www.cerebrata.com/products/azure-diagnostics-manager) in Azure Management Studio.

Per altre informazioni su come eseguire queste operazioni, vedere [Archiviare e visualizzare i dati di diagnostica in Archiviazione di Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics-storage).

### <a name="azure-storage-analytics"></a>Analisi archiviazione di Azure 

Analisi archiviazione registra informazioni dettagliate sulle richieste riuscite e non a un servizio di archiviazione. È possibile usare queste informazioni per monitorare singole richieste e contribuire così a documentare l'accesso ai dati personali archiviati nel servizio. Tuttavia, la registrazione di Analisi archiviazione non è abilitata per impostazione predefinita per l'account di archiviazione. È possibile abilitarla nel portale di Azure.

#### <a name="how-do-i-configure-monitoring-for-a-storage-account"></a>Come configurare il monitoraggio per un account di archiviazione

Per configurare il monitoraggio per un account di archiviazione, seguire questa procedura:

1. Selezionare **Account di archiviazione** nel portale di Azure, quindi selezionare il nome dell'account da monitorare.

    ![](media/protection-personal-data-azure-reporting-tools/image011.png)

2. Selezionare **Diagnostica** nella sezione **Monitoraggio**.

3.  Selezionare il **tipo** di dati di metrica da monitorare per ogni servizio (BLOB, tabelle, file). Per impostare Archiviazione di Azure in modo da salvare i log di diagnostica per le richieste di lettura, scrittura ed eliminazione per i servizi BLOB, tabelle e di accodamento, selezionare **Log dei BLOB, Log delle tabelle** e **Log delle code**.

    ![](media/protection-personal-data-azure-reporting-tools/image013.png)

4. Usare il dispositivo di scorrimento nella parte inferiore per impostare i criteri di **conservazione** in giorni, scegliendo un valore compreso tra 1 e 365. L'impostazione predefinita è sette giorni.

5. Selezionare **salvare** per applicare le impostazioni di configurazione.

Le voci del log di registrazione archiviazione contengono le informazioni seguenti sulle singole richieste:

- Informazioni sui tempi quali l'ora di inizio, la latenza end-to-end e la latenza del server.

- Dettagli dell'operazione di archiviazione, come il tipo di operazione, la chiave dell'oggetto di archiviazione a cui accede il client, l'esito positivo o negativo e il codice di stato HTTP restituito al client.

- Dettagli dell'autenticazione, come il tipo di autenticazione usato dal client.

- Informazioni sulla concorrenza, ad esempio il valore ETag e il timestamp dell'ultima modifica.

- Dimensioni dei messaggi di richiesta e di risposta.

Per istruzioni più dettagliate su come abilitare la registrazione di Analisi archiviazione, vedere [Monitor a storage account in the Azure portal](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account) (Monitorare un account di archiviazione nel portale di Azure).

### <a name="azure-security-center"></a>Centro sicurezza di Azure 

[Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) monitora lo stato della sicurezza delle risorse di Azure per prevenire e rilevare le minacce e suggerire come rispondere. Offre diversi modi per documentare le misure di sicurezza attuate per proteggere la privacy dei dati personali.

Il monitoraggio dell'integrità della sicurezza consente di garantire la conformità ai criteri di sicurezza. Il monitoraggio della sicurezza è una strategia attiva per il controllo delle risorse che permette di identificare i sistemi non conformi agli standard o alle procedure consigliate dell'organizzazione. È possibile monitorare lo stato della sicurezza delle risorse seguenti:

- Calcolo: macchine virtuali e servizi cloud

- Rete: reti virtuali

- Archiviazione e dati: controllo di server e database, rilevamento delle minacce e Transparent Data Encryption

- Applicazioni: problemi di sicurezza potenziali

Eventuali problemi di sicurezza in una qualsiasi di queste categorie possono costituire una minaccia per la privacy dei dati personali.

#### <a name="how-do-i-view-the-security-state-of-my-azure-resources"></a>Come visualizzare lo stato della sicurezza delle risorse di Azure

Il Centro sicurezza analizza periodicamente lo stato di sicurezza delle risorse di Azure. Le potenziali vulnerabilità di sicurezza identificate vengono visualizzate nella sezione **Prevenzione** del dashboard.

   ![](media/protection-personal-data-azure-reporting-tools/image014.png)

1. Selezionare il riquadro **Calcolo** nella sezione **Prevenzione**. Qui è disponibile una **panoramica**, un elenco di tutte le **macchine virtuali** e dei relativi stati della sicurezza, oltre all'elenco **Servizi cloud** dei ruoli Web e di lavoro monitorati dal Centro sicurezza.

2. Nella scheda **Panoramica** accettare una raccomandazione di visualizzare altre informazioni.

3. Selezionare una macchina virtuale nella scheda **Macchine virtuali** per visualizzare altri dettagli.

Dopo aver abilitato la raccolta dati nel Centro sicurezza di Azure, viene eseguito il provisioning automatico di Microsoft Monitoring Agent in tutte le macchine virtuali supportate distribuite, nuove ed esistenti. I dati raccolti dall'agente vengono archiviati in un'area di lavoro di [Log Analytics](https://azure.microsoft.com/services/log-analytics/) esistente associata alla sottoscrizione o in una nuova area di lavoro.

Il Centro sicurezza di Azure genera [report di intelligence sulle minacce](https://docs.microsoft.com/azure/security-center/security-center-threat-report). Questi contengono informazioni che permettono di identificare l'autore dell'attacco, i relativi obiettivi, le campagne di attacco presenti e passate, le tattiche, gli strumenti e le procedure usate. Includono anche informazioni sulla mitigazione dei rischi e la correzione.

Lo scopo principale dei report sulle minacce è consentire di reagire in modo efficace alla minaccia immediata e adottare misure per mitigare il problema in un secondo momento. Le informazioni nei report possono essere utili anche per documentare la risposta agli eventi imprevisti a fini di controllo e creazione di report.

I report di intelligence sulle minacce vengono presentati in formato PDF e sono accessibili tramite un collegamento nel campo **Report** del pannello **Suspicious process executed**  (Processo sospetto eseguito) per ogni avviso di sicurezza nel Centro sicurezza di Azure.

Per altre informazioni su come visualizzare e usare il report di intelligence sulle minacce, vedere [Report di intelligence per le minacce generato dal Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-threat-report).

## <a name="next-steps"></a>Passaggi successivi:

[Introduzione all'API di creazione report di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

[Che cos'è Log Analytics?](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)

[Panoramica del monitoraggio in Microsoft Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

[Introduzione al Log attività di Azure (video)](https://azure.microsoft.com/resources/videos/intro-activity-log/)
