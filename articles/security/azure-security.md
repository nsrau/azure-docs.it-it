---
title: Introduzione alla sicurezza in Azure | Microsoft Docs
description: Informazioni sulla sicurezza in Azure, sui relativi servizi e sul funzionamento.
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 68bba95e177fa8d0261b84f51b0f5285c7fb7417
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="introduction-to-azure-security"></a>Introduzione alla sicurezza in Azure
## <a name="overview"></a>Panoramica
La sicurezza è la priorità principale nel cloud ed è importante che l'utente trovi informazioni accurate e tempestive sulla sicurezza di Azure. Uno dei motivi migliori per usare Azure per le proprie applicazioni e i propri servizi consiste nella possibilità di sfruttare una vasta gamma di strumenti e funzionalità per la sicurezza. Questi strumenti e queste funzionalità consentono di creare soluzioni sicure sulla piattaforma Azure protetta. Microsoft Azure garantisce la riservatezza, l'integrità e la disponibilità dei dati dei clienti, assicurando anche al tempo stesso una rendicontazione trasparente.

Per consentire una migliore comprensione della raccolta di controlli di sicurezza implementati in Microsoft Azure dal punto di vista sia del cliente sia delle operazioni Microsoft, questo white paper, "Introduzione alla sicurezza in Azure", offre una panoramica completa delle funzionalità di sicurezza disponibili in Microsoft Azure.

### <a name="azure-platform"></a>Piattaforma Azure
Azure è una piattaforma di servizi cloud pubblici che supporta un'ampia gamma di sistemi operativi, linguaggi di programmazione, framework, strumenti, database e dispositivi. Può eseguire contenitori Linux con integrazione con Docker, compilare app con JavaScript, Python, .NET, PHP, Java e Node.js e creare back-end per dispositivi iOS, Android e Windows.

I servizi cloud pubblici di Azure supportano le stesse tecnologie già considerate affidabili e usate da milioni di sviluppatori e professionisti IT. Creando asset IT o eseguendone la migrazione in un provider di servizi cloud pubblici, si dipende dalla capacità di tale organizzazione di proteggere le applicazioni e i dati con i servizi e i controlli offerti per gestire la sicurezza degli asset basati sul cloud.

L'infrastruttura di Azure è stata progettata, dalla struttura fino alle applicazioni, per ospitare simultaneamente milioni di clienti e garantisce alle aziende una solida base per poterne soddisfare i requisiti di sicurezza.

Azure, inoltre, offre una vasta gamma di opzioni di sicurezza configurabili e la possibilità di controllarle in modo da personalizzare la sicurezza in base ai requisiti univoci delle distribuzioni della propria organizzazione. Questo documento consente di comprendere come le funzionalità di sicurezza di Azure consentono di soddisfare tali requisiti.

> [!Note]
> L'argomento principale di questo documento sono i controlli destinati ai clienti che è possibile usare per personalizzare e aumentare la sicurezza delle proprie applicazioni e dei propri servizi.
>
> Le informazioni offerte costituiscono una panoramica. Per informazioni dettagliate sul modo in cui Microsoft protegge la piattaforma Azure, vedere le informazioni disponibili nel sito [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx).

### <a name="abstract"></a>Sunto
Inizialmente, le migrazioni a cloud pubblici erano motivate dalla riduzione dei costi e dalla flessibilità di innovazione. Per un certo periodo, la sicurezza è stata un'importante fonte di preoccupazione e anche un deterrente alla migrazione a cloud pubblici. La sicurezza dei cloud pubblici, tuttavia, si è trasformata da fonte di preoccupazione a fattore determinante in favore della migrazione cloud. La motivazione di base è la superiore capacità dei grandi provider di servizi cloud pubblici di proteggere le applicazioni e i dati degli asset basati sul cloud.

L'infrastruttura di Azure è stata progettata, dalla struttura fino alle applicazioni, per ospitare milioni di clienti contemporaneamente e fornisce alle aziende una solida base per poter soddisfare le esigenze di sicurezza. Azure, inoltre, offre una vasta gamma di opzioni di sicurezza configurabili e la possibilità di controllarle in modo da personalizzare la sicurezza in base ai requisiti univoci delle distribuzioni per rispettare i criteri di controllo del reparto IT e i regolamenti esterni.

Questo documento illustra l'approccio di Microsoft alla sicurezza all'interno della piattaforma cloud Microsoft Azure:
* Funzionalità di sicurezza implementate da Microsoft per proteggere l'infrastruttura di Azure, i dati dei clienti e le applicazioni.
* Servizi e funzionalità di sicurezza di Azure a disposizione degli utenti per gestire la sicurezza dei servizi e dei dati all'interno delle sottoscrizioni di Azure.

## <a name="summary-azure-security-capabilities"></a>Riepilogo delle funzionalità di sicurezza di Azure
La tabella seguente offre una breve descrizione delle funzionalità di sicurezza implementate da Microsoft per proteggere l'infrastruttura di Azure, i dati dei clienti e le applicazioni.
### <a name="security-features-implemented-to-secure-the-azure-platform"></a>Funzionalità di sicurezza implementate per proteggere la piattaforma Azure:
Di seguito sono elencate le funzionalità che è possibile esaminare per assicurarsi che la piattaforma Azure venga gestita in modo sicuro. I collegamenti disponibili consentono di approfondire come Microsoft affronta gli aspetti della protezione dei clienti in quattro aree: sicurezza della piattaforma, privacy e controlli, conformità e trasparenza.


| [Sicurezza della piattaforma](https://www.microsoft.com/en-us/trustcenter/Security/default.aspx)  | [Privacy e controlli](https://www.microsoft.com/en-us/trustcenter/Privacy/default.aspx)  |[Conformità](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)   | [Trasparenza](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- |
| [Ciclo di sviluppo per la sicurezza](https://www.microsoft.com/en-us/sdl/), controlli interni | [Gestione costante dei propri dati](https://www.microsoft.com/en-us/trustcenter/Privacy/You-own-your-data) | [Centro protezione](https://www.microsoft.com/en-us/trustcenter/default.aspx) |[Come vengono protetti da Microsoft i dati dei clienti nei servizi di Azure](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| [Formazione sulla sicurezza obbligatoria, controlli del background](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=2&cad=rja&uact=8&ved=0ahUKEwiwsOCpganRAhWqxVQKHUdiDsMQFghAMAE&url=https%3A%2F%2Fdownloads.cloudsecurityalliance.org%2Fstar%2Fself-assessment%2FStandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx&usg=AFQjCNEYvBky4zNeDQPN6YJGPFRZA7eeZg&sig2=2kkw1lOCP_kzLzgE9RS2Tg&bvm=bv.142059868,d.amc) |  [Controllo sulla posizione dei dati](https://www.microsoft.com/en-us/trustcenter/Privacy/Where-your-data-is-located) |  [Common Controls Hub](https://www.microsoft.com/en-us/trustcenter/Common-Controls-Hub) |[Come viene gestita da Microsoft la posizione dei dati nei servizi di Azure](http://azuredatacentermap.azurewebsites.net/)|
| [Test di penetrazione](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=2&cad=rja&uact=8&ved=0ahUKEwiwsOCpganRAhWqxVQKHUdiDsMQFghAMAE&url=https%3A%2F%2Fdownloads.cloudsecurityalliance.org%2Fstar%2Fself-assessment%2FStandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx&usg=AFQjCNEYvBky4zNeDQPN6YJGPFRZA7eeZg&sig2=2kkw1lOCP_kzLzgE9RS2Tg&bvm=bv.142059868,d.amc), [rilevamento delle intrusioni, DDoS](https://www.microsoft.com/en-us/trustcenter/Security/ThreatManagement), [controlli e registrazione](https://www.microsoft.com/en-us/trustcenter/Security/AuditingAndLogging) | [Accesso ai dati in base ai propri termini](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [Elenco di controllo della due diligence per i servizi cloud](https://www.microsoft.com/en-us/trustcenter/Compliance/Due-Diligence-Checklist) |[Personale Microsoft che ha accesso ai dati e in quali termini](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [Data center all'avanguardia](https://www.microsoft.com/en-us/cloud-platform/global-datacenters), sicurezza fisica, [rete protetta](https://docs.microsoft.com/azure/security/security-network-overview) | [Risposta alle richieste delle forze dell'ordine](https://www.microsoft.com/en-us/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data) |  [Conformità in base al servizio, alla località e al settore](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx) |[Come vengono protetti da Microsoft i dati dei clienti nei servizi di Azure](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx)|
|  [Risposta a eventi imprevisti di sicurezza](http://aka.ms/SecurityResponsepaper), [responsabilità condivisa](http://aka.ms/sharedresponsibility) |[Standard di privacy rigorosi](https://www.microsoft.com/en-us/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [Certificazione di verifica per i servizi di Azure, hub per la trasparenza](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)|



### <a name="security-features-offered-by-azure-to-secure-data-and-application"></a>Funzionalità di sicurezza offerte da Azure per proteggere i dati e l'applicazione
A seconda del modello di servizio cloud, la responsabilità per le persone incaricate della gestione della sicurezza per l'applicazione o il servizio può variare. Nella piattaforma Azure sono disponibili funzionalità che consentono di adempiere a tali responsabilità con caratteristiche predefinite e tramite soluzioni di partner che possono essere distribuite in una sottoscrizione di Azure.

Le funzionalità predefinite sono organizzate in sei (6) aree funzionali: operazioni, applicazioni, archiviazione, rete, calcolo e identità. Di seguito sono riportate informazioni di riepilogo che offrono altri dettagli sulle funzionalità e sulle caratteristiche disponibili nella piattaforma Azure in queste sei (6) aree.

## <a name="operations"></a>Operazioni
Questa sezione contiene informazioni aggiuntive sulle caratteristiche principali per le operazioni di sicurezza e informazioni di riepilogo su tali funzionalità.

### <a name="operations-management-suite-security-and-audit-dashboard"></a>Dashboard Sicurezza e controllo di Operations Management Suite
La [soluzione Sicurezza e controllo di OMS](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) consente di ottenere un quadro completo dello stato della sicurezza IT dell'organizzazione con [query di ricerca predefinite](https://blogs.technet.microsoft.com/msoms/2016/01/21/easy-microsoft-operations-management-suite-search-queries/) per i problemi rilevanti che richiedono l'attenzione dell'utente. Il dashboard [Sicurezza e controllo](https://technet.microsoft.com/library/mt484091.aspx) è la schermata iniziale per tutti gli elementi correlati alla sicurezza in OMS. Offre una visione generale dello stato dei computer in termini di sicurezza. Consente anche di visualizzare tutti gli eventi delle ultime 24 ore, di 7 giorni o di qualsiasi altro intervallo di tempo personalizzato.

È anche possibile configurare OMS Security & Compliance per [eseguire automaticamente azioni specifiche](https://blogs.technet.microsoft.com/robdavies/2016/04/20/simple-look-at-oms-alert-remediation-with-runbooks-part-1/) quando viene rilevato un determinato evento.

### <a name="azure-resource-manager"></a>Gestione risorse di Azure
[Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) consente di usare le risorse incluse nella soluzione come un gruppo. È possibile distribuire, aggiornare o eliminare tutte le risorse della soluzione con un'unica operazione coordinata. Per la distribuzione viene usato un [modello di Azure Resource Manager](https://blogs.technet.microsoft.com/canitpro/2015/06/29/devops-basics-infrastructure-as-code-arm-templates/) che può essere usato per diversi ambienti, ad esempio di testing, di staging e di produzione. Gestione risorse offre funzionalità di sicurezza, controllo e categorizzazione che semplificano la gestione delle risorse dopo la distribuzione.

Le distribuzioni basate su un modello di Azure Resource Manager consentono di migliorare la sicurezza delle soluzioni distribuite in Azure perché le impostazioni sono controllate dalla sicurezza standard, che può essere integrata in distribuzioni basate su modelli standardizzate. In questo modo si riduce il rischio di errori di configurazione della sicurezza che potrebbero verificarsi in caso di distribuzioni manuali.

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/) è un servizio estendibile di gestione delle prestazioni delle applicazioni per sviluppatori Web, con cui è possibile monitorare le applicazioni Web live e rilevare automaticamente le anomalie nelle prestazioni. Application Insights include avanzati strumenti di analisi che consentono di diagnosticare i problemi e conoscere come vengono effettivamente usate le app dagli utenti. Esegue il monitoraggio dell'applicazione per tutto il tempo che è in esecuzione, sia durante il test che dopo la pubblicazione o la distribuzione.

Application Insights crea grafici e tabelle che illustrano, ad esempio, in quali ore del giorno si ottengono più utenti, i tempi di risposta dell'app e come funzionano i servizi esterni da cui dipende.

Se sono presenti arresti anomali del sistema, errori o problemi di prestazioni, è possibile cercare i dati di telemetria in dettaglio per diagnosticare la causa. Il servizio invia anche messaggi di posta elettronica in caso di variazioni nella disponibilità e nelle prestazioni dell'app. Application Insights diventa quindi uno strumento utile per la sicurezza perché contribuisce alla disponibilità nell'ambito della triade di sicurezza: riservatezza, integrità e disponibilità.

### <a name="azure-monitor"></a>Monitoraggio di Azure
[Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) offre funzionalità di visualizzazione, query, routing, avviso, ridimensionamento automatico e automazione dei dati sia dall'infrastruttura di Azure ([log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)) che da ogni singola risorsa di Azure ([log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)). È possibile usare Monitoraggio di Azure per ricevere avvisi sugli eventi relativi alla sicurezza generati nei log di Azure.

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/), parte di [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite), offre una soluzione di gestione IT per l'infrastruttura sia locale che basata sul cloud di terze parti (ad esempio AWS), oltre che per le risorse di Azure. I dati di Monitoraggio di Azure possono essere indirizzati direttamente a Log Analytics, in modo da visualizzare le metriche e i log dell'intero ambiente in un'unica posizione.

Log Analytics può essere utile per analisi della sicurezza per scopi legali e di altro tipo perché questo strumento consente di eseguire rapidamente ricerche in grandi quantità di voci relative alla sicurezza con un approccio di query flessibile. Inoltre, [i log di proxy e firewall locali possono essere esportati in Azure ed essere resi disponibili per l'analisi con Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-proxy-firewall).

### <a name="azure-advisor"></a>Azure Advisor
[Azure Advisor](https://docs.microsoft.com/azure/advisor/) è un servizio di consulenza personalizzato per il cloud che consente di ottimizzare le distribuzioni di Azure. Analizza la configurazione e la telemetria delle risorse e propone quindi soluzioni utili per migliorare le [prestazioni](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations), la [sicurezza](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) e la [disponibilità elevata](https://docs.microsoft.com/azure/advisor/advisor-high-availability-recommendations) delle risorse cercando al tempo stesso opportunità di [ridurre la spesa complessiva di Azure](https://docs.microsoft.com/azure/advisor/advisor-cost-recommendations). Azure Advisor offre raccomandazioni sulla sicurezza che possono migliorare notevolmente lo stato di sicurezza complessivo delle soluzioni distribuite in Azure. Tali raccomandazioni vengono ricavate dall'analisi della sicurezza eseguita dal [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro).

### <a name="azure-security-center"></a>Centro sicurezza di Azure
Il [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) consente di prevenire, rilevare e rispondere alle minacce con un livello di visibilità e controllo più elevato della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni di Azure, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

Il Centro sicurezza di Azure facilita anche le operazioni di sicurezza offrendo un unico dashboard che presenta avvisi e raccomandazioni su cui è possibile intervenire immediatamente. È spesso possibile risolvere i problemi con un solo clic nella console del Centro sicurezza di Azure.
## <a name="applications"></a>APPLICAZIONI
Questa sezione contiene informazioni aggiuntive sulle caratteristiche principali per la sicurezza delle applicazioni e informazioni di riepilogo su tali funzionalità.

### <a name="web-application-vulnerability-scanning"></a>Analisi delle vulnerabilità delle applicazioni Web
Uno dei modi più semplici per iniziare a testare le vulnerabilità di un'[app del servizio app](https://docs.microsoft.com/azure/app-service/app-service-web-overview) consiste nell'usare l'[integrazione con Tinfoil Security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) per eseguire sull'app un'analisi delle vulnerabilità con un solo clic. È possibile visualizzare i risultati del test in un report di facile comprensione e imparare a risolvere ogni vulnerabilità con istruzioni dettagliate.

### <a name="penetration-testing"></a>Test di penetrazione
Se si preferisce eseguire i propri test di penetrazione o usare un altro gruppo o fornitore di strumenti di scansione, è necessario seguire il [processo di approvazione dei test di penetrazione di Azure](https://security-forms.azure.com/penetration-testing/terms) e ottenere l'approvazione preventiva per eseguire i test di penetrazione desiderati.

### <a name="web-application-firewall"></a>Web application firewall
Il Web application firewall (WAF) del [gateway applicazione di Azure](https://azure.microsoft.com/services/application-gateway/) consente di proteggere le applicazioni Web da attacchi basati sul Web comuni come SQL injection, cross-site scripting e hijack della sessione. Include la protezione preconfigurata dalle minacce identificate da [Open Web Application Security Project (OWASP) come le 10 principali vulnerabilità comuni](https://msdn.microsoft.com/library/).

### <a name="authentication-and-authorization-in-azure-app-service"></a>Autenticazione e autorizzazione nel servizio app di Azure
La funzionalità di [autenticazione/autorizzazione del servizio app](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) consente all'applicazione di eseguire la procedura di accesso degli utenti in modo che non sia necessario modificare il codice nel back-end dell'app. Fornisce un modo semplice per proteggere l'applicazione e utilizzare dati per-utente.

### <a name="layered-security-architecture"></a>Architettura di sicurezza su più livelli
Dato che gli [ambienti del servizio app](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-intro) forniscono un ambiente di runtime isolato distribuito in una [rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview), gli sviluppatori possono creare un'architettura di sicurezza su più livelli offrendo livelli diversi di accesso alla rete per ogni livello applicazione. Un'esigenza comune è quella di nascondere i back-end delle API all'accesso a Internet generale e consentire alle API di essere chiamate solo dalle app Web upstream. I [gruppi di sicurezza di rete (NSG)](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/) possono essere usati nelle subnet delle reti virtuali di Azure contenenti ambienti del servizio app per limitare l'accesso pubblico alle applicazioni API.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Diagnostica del server Web e diagnostica applicazioni
App Web del servizio app offre funzionalità diagnostiche per la registrazione di informazioni sia dal server Web sia dall'applicazione Web, logicamente separate in [diagnostica server Web](https://docs.microsoft.com/azure/app-service/web-sites-enable-diagnostic-log) e [diagnostica applicazioni](https://technet.microsoft.com/library/hh530058(v=sc.12).aspx). Il server Web include due importanti progressi per la diagnosi e la risoluzione dei problemi di siti e applicazioni.

La prima nuova funzionalità è rappresentata dalle informazioni sullo stato in tempo reale per pool di applicazioni, processi di lavoro, siti, domini applicazione e richieste in esecuzione. Il secondo nuovo vantaggio risiede negli eventi di traccia dettagliati che tengono traccia di una richiesta nell'intero processo di richiesta e risposta.

Per abilitare la raccolta di tali eventi di traccia, è possibile configurare IIS 7 per l'acquisizione automatica di log di traccia completi in formato XML per specifiche richieste, in base al tempo trascorso o ai codici di risposta di errore.

#### <a name="web-server-diagnostics"></a>Diagnostica del server Web
È possibile abilitare o disabilitare i seguenti tipi di log:

-   Registrazione degli errori dettagliata: informazioni dettagliate sugli errori per i codici di stato HTTP che indicano un'operazione non riuscita (codice di stato 400 o superiore), incluse eventualmente le informazioni che aiutano a determinare il motivo per cui il server ha restituito il codice di errore.

-   Traccia delle richieste non riuscite: informazioni dettagliate sulle richieste non riuscite, con traccia dei componenti IIS usati per elaborare la richieste e il tempo impiegato in ogni componente. Ciò può essere utile se si sta tentando di aumentare le prestazioni del sito oppure isolare la causa della restituzione di uno specifico errore HTTP.

-   Registrazione del server Web: informazioni sulle transazioni HTTP che usano il formato di file di log esteso W3C. Questo è utile nel determinare le metriche generali del sito, ad esempio il numero delle richieste gestite oppure quante di esse provengono da uno specifico indirizzo IP

#### <a name="application-diagnostics"></a>Diagnostica applicazioni
[Diagnostica applicazioni](https://docs.microsoft.com/azure/app-service/web-sites-enable-diagnostic-log) consente di acquisire le informazioni generate da un'applicazione Web. Le applicazioni ASP.NET possono utilizzare la classe [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) per registrare le informazioni nel log di diagnostica applicazioni. In Diagnostica applicazioni esistono due tipi principali di eventi: quelli relativi alle prestazioni delle applicazioni e quelli relativi a operazioni non riuscite ed errori. Le operazioni non riuscite e gli errori possono essere ulteriormente suddivisi in problemi di connettività, di sicurezza e di errore. Questi ultimi sono in genere correlati a un problema con il codice dell'applicazione.

In Diagnostica applicazioni è possibile visualizzare gli eventi raggruppandoli nei modi seguenti:

-   Tutti (vengono visualizzati tutti gli eventi)
-   Errori dell'applicazione (vengono visualizzati gli eventi di eccezione)
-   Prestazioni (vengono visualizzati gli eventi prestazioni)

## <a name="storage"></a>Archiviazione
Questa sezione contiene informazioni aggiuntive sulle caratteristiche principali per la sicurezza delle risorse di archiviazione di Azure e informazioni di riepilogo su tali funzionalità.

### <a name="role-based-access-control-rbac"></a>Controllo degli accessi in base al ruolo
È possibile proteggere l'account di archiviazione con il controllo degli accessi in base al ruolo. Per le organizzazioni che intendono applicare criteri di sicurezza per l'accesso ai dati è fondamentale limitare l'accesso in base a principi di [necessità](https://en.wikipedia.org/wiki/Need_to_know) e [privilegi minimi](https://en.wikipedia.org/wiki/Principle_of_least_privilege). Questi diritti di accesso vengono concessi assegnando il ruolo di controllo degli accessi appropriato a gruppi e applicazioni in un ambito specifico. È possibile usare i [ruoli predefiniti del controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles), ad esempio Collaboratore Account di archiviazione, per assegnare privilegi agli utenti. L'accesso alle chiavi di archiviazione per un account di archiviazione con il modello [Azure Resource Manager](https://docs.microsoft.com/azure/storage/storage-security-guide) può essere controllato tramite il controllo degli accessi in base al ruolo.

### <a name="shared-access-signature"></a>Firma di accesso condiviso
Una [firma di accesso condiviso (SAS)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) fornisce accesso delegato alle risorse nell'account di archiviazione. La firma di accesso condiviso consente di concedere a un client autorizzazioni limitate per gli oggetti nell'account di archiviazione per un periodo di tempo e con un set di autorizzazioni specificati. È possibile concedere queste autorizzazioni limitate senza la necessità di condividere le chiavi di accesso all'account.

### <a name="encryption-in-transit"></a>Crittografia in transito
La crittografia in transito è un meccanismo di protezione dei dati durante la trasmissione tra le reti. Con Archiviazione di Azure è possibile proteggere i dati con:
-   [Crittografia a livello di trasporto](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit), ad esempio HTTPS quando si trasferiscono dati all'interno o all'esterno di Archiviazione di Azure.

-   [Crittografia di rete](https://docs.microsoft.com/azure/storage/storage-security-guide#using-encryption-during-transit-with-azure-file-shares), ad esempio la [crittografia SMB 3.0](https://docs.microsoft.com/azure/storage/storage-security-guide) per le [condivisioni file di Azure](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-files).

-   Crittografia lato client, per crittografare i dati prima che vengano trasferiti nella risorsa di archiviazione e decrittografarli dopo il trasferimento da tale risorsa.

### <a name="encryption-at-rest"></a>Crittografia di dati inattivi
Per molte organizzazioni, la crittografia dei dati inattivi è un passaggio obbligatorio per assicurare la privacy dei dati, la conformità e la sovranità dei dati. Esistono tre funzionalità di sicurezza delle risorse di archiviazione di Azure che consentono di crittografare dati inattivi:

-   [Crittografia del servizio di archiviazione](https://docs.microsoft.com/azure/storage/storage-service-encryption) consente di richiedere che il servizio di archiviazione crittografi automaticamente i dati durante la scrittura in Archiviazione di Azure.

-   [Client-side Encryption](https://docs.microsoft.com/azure/storage/storage-client-side-encryption) offre anche la funzionalità di crittografia dei dati inattivi.

-   [Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) consente di crittografare i dischi dati e del sistema operativo usati da una macchina virtuale IaaS.

### <a name="storage-analytics"></a>di Analisi archiviazione
L'[Analisi archiviazione di Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) esegue la registrazione e restituisce i dati delle metriche per un account di archiviazione. È possibile utilizzare questi dati per tenere traccia delle richieste, analizzare le tendenze d'uso e diagnosticare i problemi relativi al proprio account di archiviazione. Analisi archiviazione registra informazioni dettagliate sulle richieste riuscite e non a un servizio di archiviazione. Queste informazioni possono essere utilizzate per monitorare le singole richieste e per diagnosticare problemi relativi a un servizio di archiviazione. Le richieste vengono registrate in base al massimo sforzo. Vengono registrati i seguenti tipi di richieste autenticate:
-   Richieste riuscite

-   Richieste non riuscite, tra cui timeout, limitazione, rete, autorizzazione e altri errori

-   Richieste tramite una firma di accesso condiviso, incluse le richieste riuscite e non riuscite

-   Richieste ai dati di analisi

### <a name="enabling-browser-based-clients-using-cors"></a>Abilitazione dei client basati su browser con CORS
[Condivisione risorse tra le origini (CORS)](https://docs.microsoft.com/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) è un meccanismo che consente ai domini di concedersi reciprocamente l'autorizzazione ad accedere alle rispettive risorse. L'agente utente invia intestazioni aggiuntive affinché al codice JavaScript caricato da un determinato dominio venga consentito l'accesso alle risorse che si trovano in un altro dominio. Questo secondo dominio risponde quindi con intestazioni aggiuntive che consentono o negano al dominio originale l'accesso alle risorse.

Dato che i servizi di archiviazione di Azure supportano ora CORS, una volta impostate le regole CORS per il servizio, una richiesta correttamente autenticata inviata al servizio da un diverso dominio verrà valutata per determinare se è consentita in base alle regole che sono state specificate.
## <a name="networking"></a>Rete
Questa sezione contiene informazioni aggiuntive sulle caratteristiche principali per la sicurezza di rete di Azure e informazioni di riepilogo su tali funzionalità.

### <a name="network-layer-controls"></a>Controlli a livello rete
Il controllo di accesso alla rete comporta la limitazione della connettività da e verso subnet o dispositivi specifici e rappresenta la base della sicurezza di rete. L'obiettivo del controllo di accesso alla rete consiste nel garantire che le macchine virtuali e i servizi siano accessibili solo agli utenti e ai dispositivi a cui si vuole concedere l'accesso.

#### <a name="network-security-groups"></a>Gruppi di sicurezza di rete
Un [gruppo di sicurezza di rete (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) è un firewall di filtro dei pacchetti con stato di base e consente di controllare l'accesso in base a [5 tuple](https://www.techopedia.com/definition/28190/5-tuple). Gli NSG non forniscono ispezione a livello dell'applicazione o controlli di accesso autenticato. Possono essere usati per controllare il traffico tra subnet all'interno di una rete virtuale di Azure e il traffico tra una rete virtuale di Azure e Internet.

#### <a name="route-control-and-forced-tunneling"></a>Controllo di route e tunneling forzato
La possibilità di controllare il comportamento di routing nella rete virtuale di Azure è una funzionalità critica per il controllo di accesso e la sicurezza di rete. Se si vuole essere certi che tutto il traffico da e verso la rete virtuale di Azure passi attraverso una determinata appliance di sicurezza virtuale, ad esempio, è necessario poter controllare e personalizzare il comportamento di routing. A tale scopo è possibile configurare route definite dall'utente in Azure.

Le [route definite dall'utente](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) consentono di personalizzare i percorsi in ingresso e in uscita del traffico da e verso singole macchine virtuali o subnet per garantire la route più sicura possibile. [tunneling forzato](https://www.petri.com/azure-forced-tunneling) è un meccanismo che può essere usato per assicurarsi che ai servizi sia impedito di stabilire una connessione a dispositivi in Internet.

Questo approccio è diverso dalla possibilità di accettare connessioni in ingresso e quindi rispondere. I server Web front-end devono rispondere alle richieste dagli host in Internet e di conseguenza il traffico originato da Internet è consentito in ingresso verso questi server Web, che sono autorizzati a rispondere.

Il tunneling forzato viene in genere usato per imporre al traffico in uscita verso Internet di passare attraverso firewall e proxy di sicurezza locali.

#### <a name="virtual-network-security-appliances"></a>Appliance di sicurezza di rete virtuale
Nonostante i gruppi di sicurezza di rete, le route definite dall'utente e il tunneling forzato offrano un certo grado di sicurezza ai livelli di rete e trasporto del [modello OSI](https://en.wikipedia.org/wiki/OSI_model), a volte può essere consigliabile abilitare la sicurezza a livelli superiori dello stack. È possibile accedere a queste funzionalità di sicurezza di rete avanzate usando una soluzione di appliance di sicurezza di rete dei partner di Azure. Le soluzioni di sicurezza di rete più recenti offerte dai partner di Azure sono disponibili in [Azure Marketplace](https://azure.microsoft.com/marketplace/), cercando "sicurezza" e "sicurezza di rete".

### <a name="azure-virtual-network"></a>Rete virtuale di Azure

Una rete virtuale di Azure (VNet) è una rappresentazione della propria rete personalizzata nel cloud. È un isolamento logico dell'infrastruttura di rete di Azure dedicato alla sottoscrizione. È possibile controllare completamente i blocchi di indirizzi IP, le impostazioni DNS, i criteri di sicurezza e le tabelle di route in questa rete. La rete virtuale può essere segmentata in subnet e si possono inserire macchine virtuali (VM) IaaS di Azure e/o [servizi cloud (istanze del ruolo PaaS)](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me) nelle reti virtuali di Azure.

È anche possibile connettere la rete virtuale alla rete locale usando una delle [opzioni di connettività](https://docs.microsoft.com/azure/vpn-gateway/) disponibili in Azure. In pratica è possibile espandere la rete ad Azure, con il controllo completo sui blocchi di indirizzi IP con tutti i vantaggi di livello aziendale offerti da Azure.

La rete di Azure supporta vari scenari di accesso remoto sicuro, tra cui:

-   [Connettere singole workstation a una rete virtuale di Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

-   [Connettere la rete locale a una rete virtuale di Azure con una VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

-   [Connettere la rete locale a una rete virtuale di Azure con un collegamento WAN dedicato](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)

-   [Connettere tra loro reti virtuali di Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps)

### <a name="vpn-gateway"></a>Gateway VPN
Per inviare il traffico di rete tra la rete virtuale di Azure e il sito locale, è necessario creare un gateway VPN per la rete virtuale di Azure. Un [gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) è un tipo di gateway di rete virtuale che invia traffico crittografato tramite una connessione pubblica. È anche possibile usare i gateway VPN per inviare il traffico tra le reti virtuali di Azure tramite l'infrastruttura di rete di Azure.

### <a name="express-route"></a>Express Route
Microsoft Azure [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) è un collegamento WAN dedicato che consente di estendere le reti locali nel cloud Microsoft tramite una connessione privata dedicata supportata da un provider di connettività.

![Express Route](./media/azure-security/azure-security-fig1.png)

Con ExpressRoute è possibile stabilire connessioni ai servizi cloud Microsoft, come Microsoft Azure, Office 365 e CRM Online. La connettività può essere stabilita da una rete (IP VPN) any-to-any, da una rete Ethernet punto a punto o da una Cross Connection virtuale tramite un provider di connettività presso una struttura di condivisione del percorso.

Le connessioni ExpressRoute non usano la rete Internet pubblica e possono quindi essere considerate più sicure delle soluzioni basate su VPN. In questo modo possono offrire un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali.


### <a name="application-gateway"></a>gateway applicazione
Il [gateway applicazione di Microsoft Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) offre un servizio di [controller per la distribuzione di applicazioni](https://en.wikipedia.org/wiki/Application_delivery_controller) con numerose funzionalità di bilanciamento del carico di livello 7 per l'applicazione.

![gateway applicazione](./media/azure-security/azure-security-fig2.png)

Consente di ottimizzare la produttività delle Web farm eseguendo l'offload al gateway applicazione della terminazione SSL con utilizzo elevato di CPU, denominato anche "offload SSL" o "bridging SSL". Offre anche altre funzionalità di routing di livello 7, tra cui la distribuzione round robin del traffico in ingresso, l'affinità di sessione basata su cookie, il routing basato su percorso URL e la possibilità di ospitare più siti Web dietro un unico gateway applicazione. Il gateway applicazione di Azure è un dispositivo di bilanciamento del carico di livello 7.

Fornisce richieste HTTP con routing delle prestazioni e failover tra server diversi, sia nel cloud che in locale.

L'applicazione offre numerose funzionalità di controller per la distribuzione di applicazioni, tra cui bilanciamento del carico HTTP, affinità di sessione basata su cookie, offload [SSL (Secure Sockets Layer)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-powershell), probe di integrità personalizzati, supporto per più siti e molte altre.

### <a name="web-application-firewall"></a>Web application firewall
Web application firewall è una funzionalità del [gateway applicazione di Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) che consente di proteggere le applicazioni Web che usano il gateway applicazione per funzioni standard di controller per la distribuzione di applicazioni. Web application firewall protegge infatti le applicazioni dalla maggior parte delle 10 vulnerabilità Web OWASP più diffuse.

![Web application firewall](./media/azure-security/azure-security-fig1.png)

-   Protezione dagli attacchi SQL injection

-   Protezione dai comuni attacchi Web, ad esempio attacchi di iniezione di comandi, richieste HTTP non valide, attacchi HTTP Response Splitting e Remote File Inclusion

-   Protezione dalle violazioni del protocollo HTTP

-   Protezione contro eventuali anomalie del protocollo HTTP, ad esempio user agent host mancante e accept header

-   Prevenzione contro robot, crawler e scanner

-   Rilevamento di errori di configurazione dell'applicazione comuni (ad esempio, Apache, IIS e così via)


L'uso di un Web application firewall centralizzato per la protezione dagli attacchi Web semplifica notevolmente la gestione della sicurezza e offre all'applicazione migliori garanzie contro le minacce di intrusione. Una soluzione WAF è anche in grado di reagire più velocemente a una minaccia alla sicurezza tramite l'applicazione di patch su una vulnerabilità nota in una posizione centrale, anziché proteggere ogni singola applicazione Web. È possibile convertire facilmente i gateway applicazione esistenti in gateway applicazione con Web application firewall.
### <a name="traffic-manager"></a>servizio Gestione traffico
[Gestione traffico di Microsoft Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) consente di controllare la distribuzione del traffico utente per gli endpoint di servizio in diversi data center. Gli endpoint di servizio supportati da Gestione traffico includono servizi cloud, app Web e VM di Azure. È anche possibile usare Gestione traffico con endpoint esterni, non di Azure. Gestione traffico usa il sistema DNS (Domain Name System) per indirizzare le richieste del client all'endpoint più appropriato in base a un [metodo di routing del traffico](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) e all'integrità degli endpoint.

Gestione traffico offre diversi metodi di routing del traffico per soddisfare le diverse esigenze delle applicazioni. Offre inoltre [monitoraggio](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) dell'integrità degli endpoint e failover automatico. Gestione traffico è resiliente agli errori, incluso l'errore di un'intera area di Azure.
### <a name="azure-load-balancer"></a>Azure Load Balancer
[Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) offre elevati livelli di disponibilità e prestazioni di rete per le applicazioni. Si tratta di un servizio di bilanciamento del carico di livello 4 (TCP, UDP) che distribuisce il traffico in ingresso tra istanze integre di servizi definiti in un set con carico bilanciato. Azure Load Balancer può essere configurato per:

-   Bilanciare il carico del traffico Internet in ingresso nelle macchine virtuali. Questa configurazione è nota come [bilanciamento del carico Internet tra più macchine virtuali o servizi](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Bilanciare il carico del traffico tra macchine virtuali in una rete virtuale, tra macchine virtuali nei servizi cloud o tra computer locali e macchine virtuali in una rete virtuale cross-premise. Questa configurazione è nota come [bilanciamento del carico interno](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview). 

- Inoltrare il traffico esterno a una macchina virtuale specifica

### <a name="internal-dns"></a>DNS interno
È possibile gestire l'elenco dei server DNS usati in una rete virtuale nel portale di gestione o nel file di configurazione di rete. Il cliente può aggiungere fino a 12 server DNS per ogni rete virtuale. Quando si specificano i server DNS, è importante verificare che i server DNS del cliente siano elencati nell'ordine corretto per l'ambiente specifico. Gli elenchi dei server DNS non supportano il round robin. Vengono usati nell'ordine in cui sono specificati. Se il primo server DNS nell'elenco è raggiungibile, il client usa tale server DNS indipendentemente dal fatto che funzioni correttamente o meno. Per modificare l'ordine dei server DNS per la rete virtuale del cliente, rimuovere i server DNS dall'elenco e aggiungerli nuovamente nell'ordine desiderato. Il sistema DNS supporta l'aspetto della disponibilità della triade di sicurezza "riservatezza, integrità e disponibilità".

### <a name="azure-dns"></a>DNS di Azure
Il sistema [DNS (Domain Name System)](https://technet.microsoft.com/library/bb629410.aspx) è responsabile della conversione (o risoluzione) del nome di un servizio o sito Web nel relativo indirizzo IP. [DNS di Azure](https://docs.microsoft.com/azure/dns/dns-overview) è un servizio di hosting per domini DNS che fornisce la risoluzione dei nomi usando l'infrastruttura di Microsoft Azure. Ospitando i domini in Azure, è possibile gestire i record DNS usando le stesse credenziali, API, strumenti e fatturazione come per gli altri servizi Azure. Il sistema DNS supporta l'aspetto della disponibilità della triade di sicurezza "riservatezza, integrità e disponibilità".
### <a name="log-analytics-nsgs"></a>Gruppi di sicurezza di rete in Log Analytics
È possibile abilitare le seguenti categorie di log di diagnostica per i gruppi di sicurezza di rete:
-   Evento: contiene voci relative alle regole dei gruppi di sicurezza di rete applicate alle VM e ai ruoli delle istanze in base all'indirizzo MAC. Lo stato di queste regole viene raccolto ogni 60 secondi.

-   Contatore di regole: contiene voci che indicano quante volte ogni regola dei gruppi di sicurezza di rete viene applicata per rifiutare o consentire il traffico.

### <a name="azure-security-center"></a>Centro sicurezza di Azure
Il Centro sicurezza consente di prevenire, rilevare e rispondere alle minacce e offre un livello di visibilità e controllo più elevato della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri nelle sottoscrizioni di Azure, consente di rilevare minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza. Le raccomandazioni sulla rete sono incentrate su firewall, gruppi di sicurezza di rete, configurazione delle regole per il traffico in ingresso e altro ancora.

Sono disponibili le raccomandazioni sulla rete seguenti.

-   [Aggiungi un firewall di nuova generazione](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall): è consigliabile aggiungere un firewall di nuova generazione di un partner Microsoft per aumentare i meccanismi di protezione per la sicurezza.

-   [Indirizza il traffico solo tramite il firewall di nuova generazione](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall#route-traffic-through-ngfw-only): è consigliabile configurare regole del gruppo di sicurezza di rete che forzino il traffico in ingresso alla VM attraverso il firewall di nuova generazione.

-   [Abilita i gruppi di sicurezza di rete nelle subnet/sulle macchine virtuali](https://docs.microsoft.com/azure/security-center/security-center-enable-network-security-groups): è consigliabile abilitare gruppi di sicurezza di rete nelle subnet o sulle VM.

-   [Limita l'accesso tramite un endpoint con connessione Internet](https://docs.microsoft.com/azure/security-center/security-center-restrict-access-through-internet-facing-endpoints): è consigliabile configurare regole per il traffico in ingresso per i gruppi di sicurezza di rete.


## <a name="compute"></a>Calcolo

Questa sezione contiene informazioni aggiuntive sulle caratteristiche principali in quest'area e informazioni di riepilogo su tali funzionalità.

### <a name="antimalware--antivirus"></a>Antimalware e antivirus
Con Azure IaaS è possibile usare software antimalware di fornitori di soluzioni di sicurezza come Microsoft, Symantec, Trend Micro, McAfee e Kaspersky per proteggere le macchine virtuali da file dannosi, adware e altre minacce. [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) per Servizi cloud e Macchine virtuali di Azure è una funzionalità di protezione che consente di identificare e rimuovere virus, spyware e altro software dannoso. Microsoft Antimalware offre avvisi configurabili quando software dannoso o indesiderato tenta l'installazione o l'esecuzione nei sistemi di Azure. Microsoft Antimalware può anche essere distribuito usando il Centro sicurezza di Azure.

### <a name="hardware-security-module"></a>Modulo di protezione hardware
La crittografia e l'autenticazione non migliorano la sicurezza a meno che le chiavi stesse non siano protette. È possibile semplificare la gestione e la sicurezza di chiavi e segreti critici archiviandoli in [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis). Key Vault consente di archiviare le chiavi in moduli di protezione hardware certificati per gli standard FIPS 140-2 livello 2. Le chiavi di crittografia di SQL Server per backup o [Transparent Data Encryption](https://msdn.microsoft.com/library/bb934049.aspx) possono essere tutte archiviate nell'insieme di credenziali delle chiavi con qualsiasi chiave o segreto delle applicazioni. Le autorizzazioni e l'accesso per questi elementi protetti vengono gestiti tramite [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

### <a name="virtual-machine-backup"></a>Backup di una macchina virtuale
[Backup di Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) è una soluzione che protegge i dati delle applicazioni senza investimenti di capitale e con costi operativi minimi. Gli errori delle applicazioni possono danneggiare i dati e gli errori umani possono introdurre nelle applicazioni bug che potrebbero causare problemi di sicurezza. Con Backup di Azure, le macchine virtuali che eseguono Windows e Linux sono protette.

### <a name="azure-site-recovery"></a>Azure Site Recovery
Una parte importante della [strategia di continuità aziendale e ripristino di emergenza (BCDR)](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) dell'organizzazione consiste nel capire come mantenere operativi i carichi di lavoro e le app aziendali quando si verificano interruzioni pianificate e non pianificate. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) consente di orchestrare la replica, il failover e il ripristino di carichi di lavoro e app in modo che siano disponibili da una località secondaria in caso di inattività di quella primaria.

### <a name="sql-vm-tde"></a>TDE in VM SQL
[Transparent Data Encryption (TDE)](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault) e la crittografia a livello di colonna sono funzionalità di crittografia di SQL Server che richiedono ai clienti di gestire e archiviare le chiavi crittografiche usate per la crittografia.

Il servizio dell'insieme di credenziali delle chiavi di Azure (AKV) è progettato per migliorare la sicurezza e la gestione di queste chiavi in una posizione sicura e a elevata disponibilità. Il connettore SQL Server consente a SQL Server di usare queste chiavi da Azure Key Vault.

Se si esegue SQL Server con computer locali, è possibile seguire una procedura per accedere ad Azure Key Vault dal computer SQL Server locale. Se si esegue SQL Server in VM di Azure, invece, è possibile risparmiare tempo usando la funzionalità Integrazione di Azure Key Vault. Con alcuni cmdlet di Azure PowerShell che abilitano questa funzionalità, è possibile automatizzare la configurazione necessaria per l'accesso all'insieme di credenziali delle chiavi di una macchina virtuale di SQL.

### <a name="vm-disk-encryption"></a>Crittografia dei dischi delle VM
[Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) è una nuova funzionalità che consente di crittografare i dischi delle macchine virtuali IaaS Windows e Linux. Applica la funzionalità standard di settore BitLocker di Windows e la funzionalità DM-Crypt di Linux per offrire la crittografia del volume per i dischi dati e del sistema operativo. La soluzione è integrata con Azure Key Vault per consentire il controllo e la gestione dei segreti e delle chiavi di crittografia dei dischi nella sottoscrizione di Key Vault. Questa soluzione assicura anche che tutti i dati nei dischi delle macchine virtuali vengano crittografati quando inattivi in Archiviazione di Azure.

### <a name="virtual-networking"></a>Reti virtuali
La connettività di rete è indispensabile per le macchine virtuali. Per supportare questo requisito, Azure richiede che le macchine virtuali siano connesse a una rete virtuale di Azure. Una rete virtuale di Azure è un costrutto logico basato sull'infrastruttura di rete fisica di Azure. Ogni [rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) logica è isolata da tutte le altre reti virtuali di Azure. L'isolamento garantisce che il traffico di rete nelle distribuzioni di un utente non sia accessibile da altri clienti di Microsoft Azure.

### <a name="patch-updates"></a>Patch di aggiornamento
Le patch di aggiornamento offrono la base per trovare e correggere potenziali problemi e semplificare il processo di gestione degli aggiornamenti software, sia riducendo il numero di aggiornamenti software che è necessario distribuire nell'organizzazione, sia migliorando la capacità di monitorare la conformità.

### <a name="security-policy-management-and-reporting"></a>Gestione e reporting dei criteri di sicurezza
Il [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) consente di prevenire, rilevare e rispondere alle minacce e offre un livello di visibilità e controllo più elevato della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri nelle sottoscrizioni di Azure, consente di rilevare minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

### <a name="azure-security-center"></a>Centro sicurezza di Azure
Il Centro sicurezza impedisce, rileva e risponde alle minacce mediante visibilità e controllo avanzati della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni di Azure, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

## <a name="identify-and-access-management"></a>Gestione delle identità e dell'accesso

I controlli di accesso basati sull'identità sono il punto di partenza della protezione di sistemi, applicazioni e dati. Le funzionalità di gestione delle identità e dell'accesso incorporate nei prodotti e nei servizi aziendali Microsoft consentono di proteggere le informazioni personali e dell'organizzazione dall'accesso non autorizzato rendendole al tempo stesso disponibili per gli utenti legittimi ovunque e in qualsiasi momento ne abbiano bisogno.

### <a name="secure-identity"></a>Proteggere l'identità
Per gestire le identità e l'accesso, Microsoft usa più procedure e tecnologie di sicurezza nei propri prodotti e servizi.
-   [Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) richiede agli utenti di usare più metodi di accesso, in locale e nel cloud. Offre autenticazione avanzata con diverse facili opzioni di verifica, garantendo al tempo stesso agli utenti un processo di accesso semplice.

-   [Microsoft Authenticator](https://aka.ms/authenticator) semplifica l'uso di Multi-Factor Authentication, è applicabile ad account sia Microsoft che Microsoft Azure Active Directory e include il supporto per dispositivi indossabili e approvazioni basate sull'impronta digitale.

-   L'[applicazione di criteri password](https://azure.microsoft.com/documentation/articles/active-directory-passwords-policy/) aumenta la sicurezza delle password tradizionali imponendo requisiti di lunghezza e complessità, la rotazione periodica forzata e il blocco account dopo tentativi di autenticazione non riusciti.

-   L'[autenticazione basata su token](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/) consente l'autenticazione tramite Active Directory Federation Services (AD FS) o sistemi di token di sicurezza di terze parti.

-   Il [controllo degli accessi in base al ruolo](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/) consente di concedere l'accesso in base al ruolo assegnato all'utente, garantendo facilmente agli utenti solo il livello di accesso necessario per svolgere le proprie mansioni. È possibile personalizzare il controllo degli accessi in base al ruolo per il modello aziendale e la tolleranza al rischio dell'organizzazione.

-   La [soluzione ibrida (integrata) di gestione delle identità](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/) consente di mantenere il controllo sull'accesso degli utenti nei data center interni e nelle piattaforme cloud creando una singola identità utente per l'autenticazione e l'autorizzazione in tutte le risorse.

### <a name="secure-apps-and-data"></a>Proteggere app e dati
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) è una soluzione cloud completa per la gestione delle identità e dell'accesso che consente di proteggere l'accesso ai dati nelle applicazioni in locale e nel cloud e semplifica la gestione di gruppi e utenti. Combina servizi directory di base, governance avanzata delle identità, sicurezza e gestione dell'accesso alle applicazioni e consente agli sviluppatori di incorporare facilmente la gestione delle identità basata su criteri nelle proprie app. Per migliorare Azure Active Directory, è possibile aggiungere funzionalità a pagamento mediante Azure Active Directory Basic Edition, Premium P1 Edition e Premium P2 Edition.

| Funzionalità comuni/gratuite     | Funzionalità Basic    |Funzionalità Premium P1 |Funzionalità Premium P2 | Aggiunta ad Azure Active Directory - Solo funzionalità correlate a Windows 10|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Oggetti directory](https://docs.microsoft.com/azure/active-directory/active-directory-editions#directory-objects), [utente/gruppo di gestione (aggiunta/aggiornamento/eliminazione) / basato su utente provisioning, registrazione dispositivo](https://docs.microsoft.com/azure/active-directory/active-directory-editions#usergroup-management-addupdatedelete-user-based-provisioning-device-registration), [Single Sign-On (SSO)](https://docs.microsoft.com/azure/active-directory/active-directory-editions#single-sign-on-sso), [Self-Service Modifica della password per gli utenti del cloud](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-password-change-for-cloud-users), [Connect (motore di sincronizzazione che si estende su directory locali ad Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-editions#connect-sync-engine-that-extends-on-premises-directories-to-azure-active-directory), [sicurezza / report di utilizzo](https://docs.microsoft.com/azure/active-directory/active-directory-editions#securityusage-reports)       |   [Gestione degli accessi in base al gruppo / provisioning](https://docs.microsoft.com/azure/active-directory/active-directory-editions#group-based-access-managementprovisioning), [di reimpostazione Password Self-Service per gli utenti del cloud](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-password-reset-for-cloud-users), [(personalizzazione di pagine di accesso/pannello) di Branding aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-editions#company-branding-logon-pagesaccess-panel-customization), [ Proxy dell'applicazione](https://docs.microsoft.com/azure/active-directory/active-directory-editions#application-proxy), [contratto di servizio con disponibilità del 99,9%](https://docs.microsoft.com/azure/active-directory/active-directory-editions#sla-999) |  [Gruppi Self-Service e app Management/Self-Service applicazione aggiunte/dinamico gruppi](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-group), [Self-Service Password Reset/modifica/sblocco con locale writeback](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-password-resetchangeunlock-with-on-premises-write-back), [a più fattori Autenticazione ((Server di autenticazione a più fattori) locale e Cloud)](https://docs.microsoft.com/azure/active-directory/active-directory-editions#multi-factor-authentication-cloud-and-on-premises-mfa-server), [CAL MIM + Server MIM](https://docs.microsoft.com/azure/active-directory/active-directory-editions#mim-cal-mim-server), [Cloud App Discovery](https://docs.microsoft.com/azure/active-directory/active-directory-editions#cloud-app-discovery), [Connect Health](https://docs.microsoft.com/azure/active-directory/active-directory-editions#connect-health), [ Rollover automatico della password per gli account di gruppo](https://docs.microsoft.com/azure/active-directory/active-directory-editions#automatic-password-rollover-for-group-accounts)|   [La protezione dell'identità](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)|  [Aggiunta di un dispositivo ad Azure AD, SSO per desktop, Microsoft Passport per Azure AD, ripristino Bitlocker per amministratori](https://docs.microsoft.com/azure/active-directory/active-directory-editions#join-a-device-to-azure-ad-desktop-sso-microsoft-passport-for-azure-ad-administrator-bitlocker-recovery), [Iscrizione automatica MDM, ripristino Bitlocker self-service, amministratori locali aggiuntivi per dispositivi Windows 10 tramite aggiunta ad Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-editions#mdm-auto-enrollment)|


- [Cloud App Discovery](https://docs.microsoft.com/azure/active-directory/active-directory-cloudappdiscovery-whatis) è una funzionalità Premium di Azure Active Directory che consente di identificare le applicazioni cloud usate dai dipendenti dell'organizzazione.

- [Azure Active Directory Identity Protection](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/) è un servizio di sicurezza che usa le funzionalità di rilevamento anomalie di Azure Active Directory per offrire una visualizzazione consolidata degli eventi di rischio e delle potenziali vulnerabilità che potrebbero influire sulle identità dell'organizzazione.

- [Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) consente di aggiungere VM di Azure a un dominio senza che sia necessario distribuire controller di dominio. Gli utenti accedono a queste VM usando le credenziali aziendali di Active Directory e possono accedere alle risorse con facilità.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) è un servizio di gestione delle identità globale a disponibilità elevata per app rivolte agli utenti, scalabile fino a centinaia di milioni di identità e integrabile con piattaforme Web e per dispositivi mobili. I clienti possono accedere a tutte le applicazioni tramite esperienze personalizzabili con account dei social media esistenti oppure con creando nuove credenziali autonome.

- [Collaborazione B2B di Azure Active Directory](https://aka.ms/aad-b2b-collaboration) è una soluzione di integrazione dei partner che supporta le relazioni tra aziende consentendo ai partner di accedere in modo selettivo alle applicazioni e ai dati aziendali usando le proprie identità autogestite.

- L'[aggiunta ad Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-azureadjoin-overview/) consente di estendere le funzionalità del cloud a dispositivi Windows 10 per una gestione centralizzata. Gli utenti possono così connettersi al cloud dell'azienda o dell'organizzazione tramite Azure Active Directory usufruendo di un accesso semplificato alle app e alle risorse.

- Il [proxy di applicazione di Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-get-started/) fornisce l'accesso remoto sicuro e SSO per le applicazioni Web ospitate in locale.

## <a name="next-steps"></a>Fasi successive
- [Introduzione alla sicurezza in Microsoft Azure](https://docs.microsoft.com/azure/security/azure-security-getting-started)

Servizi e funzionalità di Azure che è possibile usare per proteggere i servizi e i dati in Azure

- [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/)

Prevenire, rilevare e rispondere alle minacce con un livello di visibilità e controllo più elevato della sicurezza delle risorse di Azure

- [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-monitoring)

Funzionalità di monitoraggio del Centro sicurezza di Azure per verificare la conformità ai criteri
