---
title: Procedure consigliate per la sicurezza del Desktop virtuale Windows - Azure
description: Procedure consigliate per garantire la sicurezza dell'ambiente Desktop virtuale Windows.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 95f1027f4f5ace6963a38edf0dc028ddca351b7e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84736673"
---
# <a name="security-best-practices"></a>Procedure consigliate per la sicurezza

Desktop virtuale Windows è un servizio desktop virtuale gestito che include molte funzionalità di sicurezza per garantire la sicurezza dell'organizzazione. In una distribuzione di Desktop virtuale Windows, Microsoft gestisce parti dei servizi per conto del cliente. Il servizio dispone di molte funzionalità di sicurezza avanzate predefinite, ad esempio Reverse Connect, che riducono i rischi correlati alla possibilità di accedere a desktop remoti ovunque ci si trovi.

L'articolo descrive i passaggi aggiuntivi che possono essere eseguiti come amministratore per proteggere le distribuzioni di Desktop virtuale Windows dei clienti.

## <a name="security-responsibilities"></a>Responsabilità di sicurezza

Ciò che rende i servizi cloud diversi dalle tradizionali infrastrutture di desktop virtuale (VDI) locali è il modo in cui gestiscono le responsabilità di sicurezza. Ad esempio, in un VDI locale tradizionale, il cliente è responsabile di tutti gli aspetti di sicurezza. Tuttavia, nella maggior parte dei servizi cloud, queste responsabilità sono condivise tra il cliente e la società.

Quando si usa Desktop virtuale Windows, è importante comprendere che, sebbene alcuni componenti siano già protetti per l'ambiente in uso, sarà necessario configurare autonomamente altre aree in modo che si adattino alle esigenze di sicurezza dell'organizzazione.

Ecco le esigenze di sicurezza di cui si è responsabili nella distribuzione di Desktop virtuale Windows:

| Esigenza di sicurezza | Il cliente è responsabile di ciò? |
|---------------|:-------------------------:|
|Identità|Sì|
|Dispositivi utente (dispositivo mobile e PC)|Sì|
|Sicurezza delle app|Sì|
|Sistema operativo host di sessione|Sì|
|Configurazione della distribuzione|Sì|
|Controlli di rete|Sì|
|Piano di controllo della virtualizzazione|No|
|Host fisici|No|
|Rete fisica|No|
|Data center fisico|No|

Le esigenze di sicurezza di cui il cliente non è responsabile sono gestite da Microsoft.

## <a name="azure-security-best-practices"></a>Procedure consigliate per la sicurezza di Azure

Desktop virtuale Windows è un servizio di Azure. Per ottimizzare la sicurezza della distribuzione di Desktop virtuale Windows, occorre assicurarsi di proteggere anche l'infrastruttura di Azure e il piano di gestione circostanti. Per proteggere l'infrastruttura, valutare il modo in cui Desktop virtuale Windows si inserisce nel più ampio ecosistema di Azure. Per altre informazioni sull'ecosistema di Azure, vedere [Procedure consigliate e modelli per la sicurezza di Azure](../security/fundamentals/best-practices-and-patterns.md).

La sezione descrive le procedure consigliate per proteggere l'ecosistema di Azure.

### <a name="enable-azure-security-center"></a>Abilitare il Centro sicurezza di Azure

È consigliabile abilitare il Centro sicurezza di Azure Standard per le sottoscrizioni, le macchine virtuali, gli insiemi di credenziali delle chiavi e gli account di archiviazione.

Con il Centro sicurezza di Azure Standard è possibile:

* Gestire le vulnerabilità.
* Valutare la conformità con i framework comuni come PCI.
* Rafforzare la protezione complessiva dell'ambiente.

Per altre informazioni, vedere [Eseguire l'onboarding della sottoscrizione di Azure al Centro di sicurezza di livello Standard](../security-center/security-center-get-started.md).

### <a name="improve-your-secure-score"></a>Migliorare il punteggio di sicurezza

Il Punteggio di sicurezza fornisce raccomandazioni e consigli sulle procedure consigliate per migliorare la sicurezza complessiva. Queste raccomandazioni sono classificate in ordine di priorità per facilitare la scelta di quelle più importanti e le opzioni di correzione rapida consentono di risolvere rapidamente le potenziali vulnerabilità. Inoltre, queste raccomandazioni vengono aggiornate nel tempo, mantenendo aggiornati i modi migliori per gestire la sicurezza dell'ambiente. Per altre informazioni, vedere [Migliorare il punteggio di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-secure-score.md).

## <a name="windows-virtual-desktop-security-best-practices"></a>Procedure consigliate per la sicurezza del Desktop virtuale Windows

Desktop virtuale Windows include molti controlli di sicurezza integrati. In questa sezione verranno illustrati i controlli di sicurezza che si possono usare per proteggere gli utenti e i dati.

### <a name="require-multi-factor-authentication"></a>Richiedi autenticazione a più fattori

La richiesta dell'autenticazione a più fattori per tutti gli utenti e gli amministratori in Desktop virtuale Windows migliora la sicurezza dell'intera distribuzione. Per altre informazioni, vedere [Abilitare l'autenticazione a più fattori di Azure per Desktop virtuale Windows](set-up-mfa.md).

### <a name="enable-conditional-access"></a>Abilitare l'accesso condizionale

L'abilitazione dell'[accesso condizionale](../active-directory/conditional-access/best-practices.md) consente di gestire i rischi prima di concedere agli utenti l'accesso all'ambiente Desktop virtuale Windows. Quando si decide a quali utenti concedere l'accesso, è consigliabile considerare anche chi è l'utente, il modo in cui accede e il dispositivo in uso.

### <a name="collect-audit-logs"></a>Raccogliere log di controllo

L'abilitazione della raccolta dei log di controllo consente di visualizzare l'attività di utenti e amministratori correlata a Desktop virtuale Windows. Ecco alcuni esempi di log di controllo principali:

-   [Log attività di Azure](../azure-monitor/platform/activity-log-collect.md)
-   [Log attività di Azure Active Directory](../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)
-   [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)
-   [Host di sessione](../azure-monitor/platform/agent-windows.md)
-   [Log di diagnostica di Desktop virtuale Windows](../virtual-desktop/diagnostics-log-analytics.md)
-   [Log di insiemi di credenziali delle chiavi](../key-vault/general/logging.md)

### <a name="use-remoteapps"></a>Usare RemoteApps

Quando si sceglie un modello di distribuzione, è possibile consentire agli utenti remoti di accedere a interi desktop virtuali o solo selezionare le applicazioni. Le applicazioni remote, o RemoteApps, offrono all'utente un'esperienza lineare quando si lavora con le app sul proprio desktop virtuale. RemoteApps riduce il rischio semplicemente consentendo all'utente di usare con un subset del computer remoto esposto dall'applicazione.

### <a name="monitor-usage-with-azure-monitor"></a>Monitorare l'uso con Monitoraggio di Azure

Monitorare l'uso e la disponibilità del servizio Desktop virtuale Windows con [Monitoraggio di Azure](https://azure.microsoft.com/services/monitor/). Prendere in considerazione la creazione di [avvisi di integrità dei servizi](../service-health/alerts-activity-log-service-notifications.md) per il servizio Desktop virtuale Windows per ricevere notifiche ogni volta che si verifica un evento che ha un impatto sui servizi.

## <a name="session-host-security-best-practices"></a>Procedure consigliate per la sicurezza dell'host di sessione

Gli host di sessione sono macchine virtuali che vengono eseguite all'interno di una sottoscrizione di Azure e di una rete virtuale. La sicurezza complessiva della distribuzione di Desktop virtuale Windows dipende dai controlli di sicurezza associati agli host di sessione. Questa sezione descrive le procedure consigliate per proteggere gli host di sessione.

### <a name="enable-endpoint-protection"></a>Abilita la protezione dell'endpoint

Per proteggere la distribuzione da software dannosi noti, è consigliabile abilitare la protezione dell'endpoint in tutti gli host di sessione. È possibile usare Windows Defender antivirus o un programma di terze parti. Per altre informazioni, vedere [Guida alla distribuzione per Windows Defender Antivirus in un ambiente VDI](/windows/security/threat-protection/windows-defender-antivirus/deployment-vdi-windows-defender-antivirus). 

Per le soluzioni di profili come FSLogix o altre soluzioni che montano file VHD, è consigliabile escludere le estensioni di file VHD.

### <a name="install-an-endpoint-detection-and-response-product"></a>Installare un prodotto di rilevamento di endpoint e risposta

È consigliabile installare un prodotto di rilevamento di endpoint e risposta (EDR) per fornire funzionalità avanzate di rilevamento e risposta. Per i sistemi operativi server con [Centro sicurezza di Azure](../security-center/security-center-services.md) abilitato, l'installazione di un prodotto EDR distribuirà Defender ATP. Per i sistemi operativi client, è possibile distribuire [Defender ATP](/windows/security/threat-protection/microsoft-defender-atp/onboarding) o un prodotto di terze parti a tali endpoint.

### <a name="enable-threat-and-vulnerability-management-assessments"></a>Abilitare le valutazioni della gestione delle minacce e delle vulnerabilità

Identificare le vulnerabilità software presenti nei sistemi operativi e nelle applicazioni è essenziale per garantire la protezione dell'ambiente. Il Centro sicurezza di Azure aiuta a identificare le aree problematiche tramite valutazioni della vulnerabilità per i sistemi operativi server. È anche possibile usare Defender ATP, che fornisce la gestione delle minacce e delle vulnerabilità per i sistemi operativi desktop. È anche possibile usare prodotti di terze parti se si è particolarmente propensi, anche se è consigliabile usare il Centro sicurezza di Azure e Defender ATP.

### <a name="patch-software-vulnerabilities-in-your-environment"></a>Applicare patch alle vulnerabilità nel proprio ambiente

Una volta identificata una vulnerabilità, è necessario applicarvi una patch. Questo vale anche per gli ambienti virtuali, inclusi i sistemi operativi in esecuzione, le applicazioni distribuite all'interno di essi e le immagini da cui vengono creati nuovi computer. Seguire le comunicazioni delle notifiche sulle patch del fornitore e applicare le patch in maniera tempestiva. Si consiglia di applicare le patch alle immagini di base mensilmente per assicurarsi che i computer appena distribuiti siano più protetti possibile.

### <a name="establish-maximum-inactive-time-and-disconnection-policies"></a>Stabilire il tempo massimo di inattività e i criteri di disconnessione

La disconnessione degli utenti quando sono inattivi mantiene le risorse e impedisce l'accesso da parte di utenti non autorizzati. È consigliabile che i timeout bilancino la produttività degli utenti e l'uso delle risorse. Per gli utenti che interagiscono con le applicazioni senza stato, prendere in considerazione criteri più aggressivi che spengano i computer e mantengano le risorse. Disconnettere le applicazioni a esecuzione prolungata che continuano a essere eseguite se un utente è inattivo, ad esempio una simulazione o un rendering CAD, può interrompere il lavoro dell'utente e potrebbe anche richiedere il riavvio del computer.

### <a name="set-up-screen-locks-for-idle-sessions"></a>Configurare i blocchi schermo per le sessioni inattive

È possibile impedire l'accesso indesiderato al sistema configurando Desktop virtuale Windows affinché blocchi lo schermo di un computer durante il tempo di inattività e richieda l'autenticazione per sbloccarlo.

### <a name="establish-tiered-admin-access"></a>Stabilire l'accesso amministratore a livelli

Si consiglia di non concedere all'amministratore degli utenti l'accesso ai desktop virtuali. Se sono necessari pacchetti software, è consigliabile renderli disponibili tramite utilità di gestione della configurazione come Microsoft Endpoint Manager. In un ambiente multisessione, è consigliabile non consentire agli utenti di installare direttamente il software.

### <a name="consider-which-users-should-access-which-resources"></a>Considerare quali utenti hanno accesso a quali risorse

Considerare gli host di sessione come un'estensione della distribuzione desktop esistente. Si consiglia di controllare l'accesso alle risorse di rete in modo analogo ad altri desktop nell'ambiente, ad esempio usando la segmentazione e il filtro di rete. Per impostazione predefinita, gli host di sessione possono connettersi a qualsiasi risorsa in Internet. Esistono diversi modi per limitare il traffico, tra cui l'uso del Firewall di Azure, di appliance virtuali di rete o di proxy. Se è necessario limitare il traffico, assicurarsi di aggiungere le regole appropriate in modo che Desktop virtuale Windows possa funzionare correttamente.

### <a name="manage-office-pro-plus-security"></a>Gestire la sicurezza di Office Pro Plus

Oltre a proteggere gli host di sessione, è importante proteggere anche le applicazioni in esecuzione all'interno di essi. Office Pro Plus è una delle applicazioni più comuni distribuite negli host di sessione. Per migliorare la sicurezza della distribuzione di Office, è consigliabile usare l' [Advisor di criteri di sicurezza](/DeployOffice/overview-of-security-policy-advisor) per le app Microsoft 365 per Enterprise. Questo strumento identifica i criteri che è possibile applicare alla distribuzione per una maggiore sicurezza. L'Assistente criteri di sicurezza consiglia inoltre i criteri in base al loro impatto sulla sicurezza e la produttività.

### <a name="other-security-tips-for-session-hosts"></a>Altri suggerimenti per la sicurezza degli host di sessione

Limitando le funzionalità del sistema operativo, è possibile rafforzare la sicurezza degli host di sessione. Ecco alcune operazioni eseguibili:

- Controllare il reindirizzamento dei dispositivi reindirizzando le unità, le stampanti e i dispositivi USB al dispositivo locale di un utente in una sessione desktop remoto. Si consiglia di valutare i requisiti di sicurezza e verificare se queste funzionalità devono essere disabilitate o meno.

- Limitare l'accesso a Esplora risorse nascondendo i mapping delle unità locali e remote. In questo modo si impedisce agli utenti di individuare informazioni indesiderate sulla configurazione del sistema e sugli utenti.

- Evitare l'accesso RDP diretto agli host di sessione nell'ambiente in uso. Se è necessario l'accesso RDP diretto per l'amministrazione o la risoluzione dei problemi, abilitare [accesso just-in-time](../security-center/security-center-just-in-time.md) per limitare la superficie di attacco potenziale in un host di sessione.

- Concedere agli utenti autorizzazioni limitate quando accedono a file system locali e remoti. È possibile limitare le autorizzazioni assicurandosi che i file system locali e remoti usino gli elenchi di controllo di accesso con privilegi minimi. In questo modo, gli utenti possono accedere solo a ciò di cui hanno bisogno e non possono modificare o eliminare le risorse critiche.

- Impedire l'esecuzione di software indesiderati negli host di sessione. È possibile abilitare Blocco dell'app per la sicurezza aggiuntiva sugli host di sessione, assicurando che solo le app consentite possano essere eseguite nell'host.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come abilitare l'autenticazione a più fattori, vedere [Configurare la funzionalità di autenticazione a più fattori](set-up-mfa.md).