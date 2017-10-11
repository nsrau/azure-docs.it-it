---
title: Procedure consigliate per la sicurezza operativa di Azure | Microsoft Docs
description: Questo articolo propone un set di procedure consigliate per la sicurezza operativa di Azure.
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: ed3c4bf2dcdda3e04a18682da568ae73f41bb37d
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="azure-operational-security-best-practices"></a>Procedure consigliate per la sicurezza operativa di Azure
La sicurezza operativa di Azure include i servizi, i controlli e le funzionalità offerti ai clienti per proteggere i dati, le applicazioni e gli altri asset di Microsoft Azure. Questa soluzione si basa su un framework che incorpora le conoscenze acquisite tramite varie funzionalità univoche di Microsoft, tra cui Microsoft Security Development Lifecycle (SDL), il programma Microsoft Security Response Center e una profonda consapevolezza del panorama delle minacce per la sicurezza informatica.

In questo articolo viene illustrata una raccolta di procedure consigliate per la sicurezza del database di Azure, derivate dalla nostra esperienza con la sicurezza dei database di Azure e dalle esperienze di altri clienti.

Per ogni procedura consigliata verrà illustrato:
-   Qual è la procedura consigliata
-   Il motivo per cui si vuole abilitare tale procedura consigliata
-   Quale potrebbe essere il risultato se non fosse possibile abilitare la procedura consigliata
- Come imparare ad abilitare la procedura consigliata

Il presente articolo sulle procedure consigliate per la sicurezza operativa di Azure si basa su opinioni condivise, nonché sulle capacità e sui set di funzionalità della piattaforma di Azure esistenti al momento della scrittura. Le opinioni e le tecnologie cambiano nel tempo e questo articolo verrà aggiornato regolarmente per riflettere tali modifiche.

Le procedure consigliate per la sicurezza operativa di Azure discusse in questo articolo includono:

-   Monitorare, gestire e proteggere l'infrastruttura cloud
-   Gestire le identità e implementare Single Sign-On (SSO)
-   Tenere traccia delle richieste, analizzare le tendenze di utilizzo e diagnosticare i problemi
-   Monitoraggio dei servizi con una soluzione di monitoraggio centralizzata
-   Prevenire le minacce, rilevarle e rispondere
-   Monitoraggio della rete basato su scenari end-to-end
-   Distribuzione sicura tramite strumenti DevOps collaudati

## <a name="monitor-manage-and-protect-cloud-infrastructure"></a>Monitorare, gestire e proteggere l'infrastruttura cloud
Il reparto delle operazioni IT è responsabile della gestione dell'infrastruttura del centro di dati, delle applicazioni e dei dati, compresa la stabilità e la sicurezza di questi sistemi. Tuttavia, ottenere informazioni di sicurezza in ambienti IT sempre più complessi spesso richiede alle organizzazioni di assemblare i dati da più sistemi di gestione e sicurezza.

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) è la soluzione Microsoft per la gestione IT basata sul cloud che consente di gestire e proteggere l'infrastruttura locale e cloud.

La [soluzione Sicurezza e controllo di OMS](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) consente al personale IT di monitorare attivamente tutte le risorse, per ridurre l’impatto dei problemi di sicurezza. OMS Security and Audit prevede domini di sicurezza che possono essere usati per il monitoraggio delle risorse.

Per altre informazioni su OMS, leggere l'articolo [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

Per consentire di evitare, rilevare e rispondere alle minacce, la [soluzione Sicurezza e controllo di Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) raccoglie ed elabora i dati sulle risorse, tra cui:

-   Registri eventi sicurezza
-   Tracciamento degli eventi di Windows (ETW)
-   Eventi di controllo AppLocker
-   Log di Windows Firewall
-   Eventi di Advanced Threat Analytics
-   Risultati della valutazione baseline
-   Risultati di Antimalware Assessment
-   Risultati della valutazione di aggiornamenti/patch
-   Flussi di Syslog abilitati esplicitamente nell'agente


## <a name="manage-identity-and-implement-single-sign-on"></a>Gestire le identità e implementare Single Sign-On
[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) è il servizio Microsoft di gestione di identità e directory basato sul cloud e multi-tenant.

[Azure AD](https://azure.microsoft.com/services/active-directory/) include anche un insieme completo di funzionalità per la [gestione delle identità](https://docs.microsoft.com/azure/security/security-identity-management-overview), tra cui l'[autenticazione a più fattori](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication), la registrazione dei dispositivi, la gestione self-service delle password e dei gruppi, la gestione degli account con privilegi, il controllo degli accessi in base al ruolo, il monitoraggio dell'utilizzo dell'applicazione, il controllo avanzato e il monitoraggio e avvisi relativi alla sicurezza.

Le funzionalità seguenti possono contribuire alla protezione di applicazioni basate sul cloud, alla semplificazione dei processi IT, alla riduzione dei costi e alla soddisfazione degli obiettivi aziendali relativi alla conformità:

-   Gestione di identità e accessi per il cloud
-   Semplificazione dell'accesso utente a qualsiasi app cloud
-   Protezione di dati e applicazioni sensibili
-   Abilitazione della modalità self-service per i dipendenti
-   Integrazione con Azure Active Directory

### <a name="identity-and-access-management-for-the-cloud"></a>Gestione di identità e accessi per il cloud
Azure Active Directory (Azure AD) è una [soluzione cloud completa per la gestione delle identità e dell'accesso](https://www.microsoft.com/cloud-platform/identity-management) che offre un solido set di funzionalità per gestire utenti e gruppi. Il servizio aiuta a proteggere l'accesso ad applicazioni locali e cloud, tra cui i servizi Web Microsoft come Office 365 e molte applicazioni SaaS (Software as a Service) non Microsoft.
Per altre informazioni su come abilitare la protezione delle identità in Azure AD, vedere [Abilitazione di Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable).

### <a name="simplify-user-access-to-any-cloud-app"></a>Semplificazione dell'accesso utente a qualsiasi app cloud
[Abilitare Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps) per semplificare l'accesso degli utenti a migliaia di applicazioni cloud da dispositivi Windows, Mac, Android e iOS. Gli utenti possono avviare applicazioni cloud da un pannello di accesso Web personalizzato usando le proprie credenziali aziendali. Usare il modulo proxy di applicazione di Azure AD per andare oltre le applicazioni SaaS e pubblicare applicazioni Web locali per offrire accesso remoto a sicurezza elevata e l'accesso Single Sign-On.

### <a name="protect-sensitive-data-and-applications"></a>Protezione di dati e applicazioni sensibili
Abilitare [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) per impedire l'accesso non autorizzato alle applicazioni locali e cloud attraverso un livello aggiuntivo di autenticazione. Proteggere l'azienda e prevenire le possibili minacce mediante monitoraggio della sicurezza, avvisi e report basati su Machine Learning in grado di identificare i modelli di accesso non coerenti.

### <a name="enable-self-service-for-your-employees"></a>Abilitazione della modalità self-service per i dipendenti
Delegare attività importanti ai dipendenti, come la reimpostazione delle password e la creazione e la gestione di gruppi. [Abilitare la modifica self-service della password](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password), la reimpostazione e la gestione di gruppi in modalità self-service con Azure AD.

### <a name="integrate-with-azure-active-directory"></a>Integrazione con Azure Active Directory
Estendere [Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate) e qualsiasi altra directory locale ad Azure AD per consentire l'accesso Single Sign-On per tutte le applicazioni basate sul cloud. Gli attributi utente possono essere sincronizzati automaticamente nella directory cloud da qualsiasi tipo di directory locale.

Per altre informazioni sull'integrazione e l'abilitazione di Azure Active Directory, vedere [Integrare le directory locali con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="trace-requests-analyze-usage-trends-and-diagnose-issues"></a>Tenere traccia delle richieste, analizzare le tendenze di utilizzo e diagnosticare i problemi
L'[Analisi archiviazione di Azure](https://docs.microsoft.com/azure/storage/storage-analytics) esegue la registrazione e restituisce i dati delle metriche per un account di archiviazione. È possibile utilizzare questi dati per tenere traccia delle richieste, analizzare le tendenze d'uso e diagnosticare i problemi relativi al proprio account di archiviazione.

Le metriche di Analisi archiviazione sono abilitate per impostazione predefinita per i nuovi account di archiviazione. È possibile abilitare la registrazione e configurare sia le metriche che la registrazione nel portale di Azure. Per informazioni dettagliate, vedere [Monitorare un account di archiviazione nel portale di Azure](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). È inoltre possibile abilitare Analisi archiviazione a livello di codice tramite l'API REST o la libreria client. Per abilitare l'Analisi archiviazione per ogni servizio, usare le operazioni che consentono di impostare le proprietà dei servizi.

Per una guida dettagliata sull'utilizzo di Analisi archiviazione e di altri strumenti per identificare, diagnosticare e risolvere i problemi relativi ad Archiviazione di Azure, vedere [Monitoraggio, diagnosi e risoluzione dei problemi del servizio di archiviazione di Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Per altre informazioni sull'integrazione e l'abilitazione di Azure Active Directory, leggere l'articolo [Enabling and Configuring Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics?redirectedfrom=MSDN) (Abilitazione e configurazione di Analisi archiviazione).

## <a name="monitoring-services"></a>Monitoraggio dei servizi
Le applicazioni cloud sono complesse e hanno molte parti mobili. Il monitoraggio offre la possibilità di garantire il funzionamento e l'integrità dell'applicazione. Consente anche di prevenire i problemi potenziali o di risolvere quelli precedenti. Inoltre, è possibile usare i dati di monitoraggio per ottenere informazioni approfondite sull'applicazione, utili per migliorarne le prestazioni o la manutenibilità oppure per automatizzare azioni che altrimenti richiederebbero un intervento manuale.

### <a name="monitor-azure-resources"></a>Monitorare le risorse di Azure
[Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) è il servizio di piattaforma che mette a disposizione un'unica origine per il monitoraggio delle risorse di Azure. Con Monitoraggio di Azure è possibile visualizzare, fare query, indirizzare, archiviare ed effettuare operazioni sulle metriche e sui log provenienti dalle risorse di Azure. È possibile usare questi dati tramite il pannello del portale di Monitoraggio di Azure, i [cmdlet di PowerShell di Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples), l'[interfaccia della riga di comando multi-piattaforma](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples) o le [API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/dn931943.aspx).

### <a name="enable-autoscale-with-azure-monitor"></a>Abilitare la scalabilità automatica con Monitoraggio di Azure
La possibilità di abilitare la [scalabilità automatica di Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started) si applica solo a set di scalabilità di macchine virtuali (VMSS), servizi cloud, piani di servizio app e ambienti di servizio app.

### <a name="manage-roles-permissions-and-security"></a>Gestire le autorizzazioni e la sicurezza dei ruoli
Molti team hanno bisogno di [regolare rigorosamente l'accesso ai dati e alle impostazioni di monitoraggio](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security). Ad esempio, se si dispone di membri del team che lavorano esclusivamente sul monitoraggio (tecnici del supporto, tecnici DevOps) o si usa un provider di servizi gestiti, si consiglia di concedere loro l'accesso ai dati di monitoraggio solo limitandone la possibilità di creare, modificare o eliminare le risorse.

Questo articolo illustra come applicare rapidamente un ruolo di monitoraggio predefinito del controllo degli accessi in base al ruolo a un utente in Azure o creare il proprio ruolo personalizzato per un utente che ha bisogno di autorizzazioni di monitoraggio limitate. Vengono poi esposte alcune considerazioni sulla sicurezza per le risorse legate al monitoraggio di Azure e viene illustrato come è possibile limitare l'accesso ai dati che contengono.

## <a name="prevent-detect-and-respond-to-threats"></a>Prevenire le minacce, rilevarle e rispondere
Il sistema di rilevamento delle minacce del Centro sicurezza funziona mediante la raccolta automatica di informazioni sulla sicurezza dalle risorse di Azure, dalla rete e dalle soluzioni dei partner connesse. Per identificare le minacce, analizza queste informazioni, correlando spesso quelle raccolte da più origini. Gli avvisi di sicurezza sono classificati in ordine di priorità nel Centro sicurezza insieme a indicazioni su come su correggere la minaccia.

-   [Configurare i criteri di sicurezza](https://docs.microsoft.com/azure/security-center/security-center-policies) per la sottoscrizione di Azure.
-   Usare le [raccomandazioni nel Centro sicurezza](https://docs.microsoft.com/azure/security-center/security-center-recommendations) per la protezione delle risorse di Azure.
-   Esaminare e gestire gli [avvisi di sicurezza](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) correnti.

Il [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) consente di prevenire, rilevare e rispondere alle minacce con un livello di visibilità e controllo più elevato della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni di Azure, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

Il Centro sicurezza fornisce funzionalità efficaci e facili da usare, integrate in Azure, per la prevenzione e il rilevamento delle minacce e la relativa risposta. Funzionalità principali:

-   Ottenere informazioni sullo stato della sicurezza nel cloud
-   Acquisire il controllo della sicurezza nel cloud
-   Distribuire in tutta semplicità soluzioni di sicurezza nel cloud integrate
-   Rilevare le minacce e rispondere rapidamente

### <a name="understand-cloud-security-state"></a>Ottenere informazioni sullo stato della sicurezza nel cloud
Usare il Centro sicurezza di Azure per ottenere un punto di vista centralizzato sullo stato della sicurezza di tutte le risorse di Azure. È possibile verificare subito che i controlli di sicurezza appropriati siano implementati e configurati correttamente e identificare rapidamente le eventuali risorse che richiedono attenzione.

### <a name="take-control-of-cloud-security"></a>Acquisire il controllo della sicurezza nel cloud
Definire i [criteri di sicurezza](https://docs.microsoft.com/azure/security-center/security-center-policies) per le sottoscrizioni di Azure in base alle esigenze di sicurezza nel cloud della società, personalizzandoli secondo il tipo di applicazione o il livello di riservatezza dei dati in ogni sottoscrizione. Usare raccomandazioni basate sui criteri per guidare i proprietari delle risorse nel processo di implementazione dei controlli richiesti, per non lasciare al caso la sicurezza nel cloud.

### <a name="easily-deploy-integrated-cloud-security-solutions"></a>Distribuire in tutta semplicità soluzioni di sicurezza nel cloud integrate
[Abilitare soluzioni di sicurezza](https://docs.microsoft.com/azure/security-center/security-center-partner-integration) di Microsoft e dei suoi partner, tra cui firewall e antimalware leader del settore. Usare un provisioning semplificato per distribuire le soluzioni di sicurezza: persino le modifiche alla rete vengono configurate automaticamente. Gli eventi di sicurezza delle soluzioni dei partner vengono raccolti automaticamente per l'analisi e la creazione di avvisi.

### <a name="detect-threats-and-respond-fast"></a>Rilevare le minacce e rispondere rapidamente
Per anticipare le minacce al cloud attuali ed emergenti, è necessario un approccio integrato basato sull'analisi. Combinando l'esperienza e l'[intelligence per le minacce](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities) globali di Microsoft con informazioni dettagliate su eventi correlati alla sicurezza in tutte le distribuzioni di Azure, Centro sicurezza consente di rilevare in anticipo le minacce effettive con minori falsi positivi. Gli avvisi di sicurezza nel cloud offrono informazioni dettagliate sulla campagna di attacco, tra cui eventi correlati e risorse interessate, e suggeriscono metodi per la risoluzione dei problemi e un rapido ripristino.

## <a name="end-to-end-scenario-based-network-monitoring"></a>Monitoraggio della rete basato su scenari end-to-end
Per creare una rete end-to-end in Azure è possibile orchestrare e comporre varie risorse di rete individuali, quali rete virtuale, ExpressRoute, gateway applicazione, servizi di bilanciamento del carico e altro ancora. Il monitoraggio è disponibile in ognuna delle risorse di rete.

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) è un servizio a livello di area che permette di monitorare e diagnosticare le condizioni al livello di scenario di rete da, verso e all'interno di Azure. Gli strumenti di visualizzazione e diagnostica di rete disponibili in Network Watcher permettono di comprendere, diagnosticare e ottenere informazioni dettagliate sulla rete in Azure.

### <a name="automate-remote-network-monitoring-with-packet-capture"></a>Automatizzare il monitoraggio di rete remoto con l'acquisizione pacchetti
Monitorare e diagnosticare i problemi di rete senza accedere alle macchine virtuali con Network Watcher. Attivare l'[acquisizione dei pacchetti](https://docs.microsoft.com/azure/network-watcher/network-watcher-alert-triggered-packet-capture) impostando gli avvisi e ottenere l'accesso alle informazioni sulle prestazioni in tempo reale a livello del pacchetto. Quando viene rilevato un problema, è possibile esaminarlo in dettaglio per una diagnosi migliore.

### <a name="gain-insight-into-your-network-traffic-using-flow-logs"></a>Acquisire informazioni dettagliate sul traffico di rete con i log dei flussi
Ottenere informazioni approfondite sul modello del traffico di rete con i [log dei flussi del gruppo di sicurezza di rete](https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-nsg-flow-logging-overview). Le informazioni fornite dai log dei flussi permettono di raccogliere i dati per la conformità, il controllo e il monitoraggio del profilo di sicurezza della rete.

### <a name="diagnose-vpn-connectivity-issues"></a>Diagnosticare i problemi di connettività della VPN
Network Watcher permette di [diagnosticare i problemi più comuni delle connessioni e dei gateway VPN](https://docs.microsoft.com/azure/network-watcher/network-watcher-diagnose-on-premises-connectivity). Non solo aiuta a identificare il problema, ma offre anche log dettagliati per eseguire ulteriori indagini.

Per altre informazioni sulla configurazione e abilitazione di Network Watcher, vedere [Configurare Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="secure-deployment-using-proven-devops-tools"></a>Distribuzione sicura tramite strumenti DevOps collaudati
Queste sono alcune delle procedure consigliate di DevOps per Azure in questo spazio Microsoft Cloud, che promuovono la produttività e l'efficienza di aziende e team.

-   **Infrastruttura come codice (IaC):** si tratta di un set di tecniche e procedure consigliate, che aiutano i professionisti IT a evitare il carico di lavoro associato alla creazione e gestione quotidiana dell'infrastruttura modulare. Lo scopo è consentire ai professionisti IT di creare e gestire un ambiente server moderno in modo simile a quello usato dagli sviluppatori per la compilazione e manutenzione del codice delle applicazioni. Nel caso di Azure è disponibile [Azure Resource Manager]( https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) che consente di effettuare il provisioning delle applicazioni usando un modello dichiarativo. In un unico modello, è possibile distribuire più servizi con le relative dipendenze. Si usa lo stesso modello per distribuire più volte l'applicazione durante ogni fase del ciclo di vita dell'applicazione.
-   **Integrazione e distribuzione continue:** è possibile configurare i progetti del proprio team di Visual Studio Online in modo da [compilare ed eseguire automaticamente la distribuzione](https://www.visualstudio.com/docs/build/overview) nelle app Web o nei servizi cloud di Azure. Visual Studio Online distribuisce automaticamente i file binari dopo una compilazione in Azure e dopo ogni archiviazione del codice. Il processo di compilazione del pacchetto qui descritto equivale al comando Pacchetto di Visual Studio, mentre i passaggi per la pubblicazione equivalgono al comando Pubblica di Visual Studio.
-   **Gestione del rilascio:** [Release Management](https://msdn.microsoft.com/library/vs/alm/release/overview) per Visual Studio è un'ottima soluzione per automatizzare la distribuzione in più fasi e la gestione del processo di rilascio. È possibile creare pipeline gestite di distribuzione continua per rilasciare versioni in modo rapido, semplice e frequente. Con Release Management è possibile automatizzare in gran parte il processo di rilascio e definire flussi di lavoro predefiniti per l'approvazione. Sono supportate la distribuzione locale e nel cloud, l'estensione e la personalizzazione in base alle specifiche esigenze.
-   **Monitoraggio delle prestazioni delle app:** rilevare problemi, risolverli e migliorare continuamente le applicazioni. Diagnosticare rapidamente eventuali problemi nell'applicazione live. Comprendere in che modo gli utenti lo usano. La configurazione include semplicemente l'aggiunta di codice JS e una voce webconfig. I risultati vengono visualizzati entro pochi minuti nel portale con tutti i dettagli. [Application Insights](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/) aiuta le aziende a velocizzare il rilevamento e la risoluzione dei problemi.
-   **Test di carico e scalabilità automatica:** è possibile trovare i problemi di prestazione nell'app per migliorare la qualità della distribuzione e assicurarsi che l'app sia sempre aggiornata o disponibile per soddisfare le esigenze aziendali. Verificare che l'app possa gestire il traffico per la prossima campagna di lancio o di marketing. Con Visual Studio Online è possibile iniziare molto velocemente a eseguire [test di carico](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) basati sul cloud.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulla [sicurezza operativa di Azure](https://docs.microsoft.com/azure/security/azure-operational-security).
- Per altre informazioni, vedere [Operations Management Suite | Sicurezza e conformità](https://www.microsoft.com/cloud-platform/security-and-compliance).
- [Introduzione alla soluzione Sicurezza e controllo di Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started).
