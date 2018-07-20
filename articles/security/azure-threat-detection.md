---
title: Rilevamento delle minacce avanzato in Azure | Microsoft Docs
description: Informazioni su Azure Active Directory Identity Protection e le relative funzionalità.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 9b688ca6faaa7e0d84dff0ae28e2a9b8b8279490
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856879"
---
# <a name="azure-advanced-threat-detection"></a>Rilevamento delle minacce avanzato in Azure

Azure offre funzionalità predefinite di rilevamento avanzato delle minacce attraverso servizi quali Azure Active Directory (Azure AD), Azure Operations Management Suite (OMS) e Centro sicurezza di Azure. Questa raccolta di servizi e funzionalità di sicurezza offre un modo semplice e veloce per comprendere ciò che accade all'interno delle distribuzioni di Azure.

Azure offre una vasta gamma di opzioni per configurare e personalizzare la sicurezza e soddisfare i requisiti delle distribuzioni di app. Questo articolo illustra come soddisfare questi requisiti.

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) è una funzionalità di [Azure Active Directory Premium P2](https://docs.microsoft.com/azure/active-directory/active-directory-editions) che offre una panoramica degli eventi di rischio e delle potenziali vulnerabilità che possono interessare le identità dell'organizzazione. Identity Protection si avvale delle funzionalità di rilevamento anomalie di Azure AD, che sono disponibili tramite i [report di Anomalie dell'attività di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-view-access-usage-reports#anomalous-activity-reports), e introduce nuovi tipi di eventi di rischio che permettono di rilevare le anomalie in tempo reale.

![Schema di Azure AD Identity Protection](./media/azure-threat-detection/azure-threat-detection-fig1.png)

Identity Protection usa l'euristica e algoritmi di apprendimento automatico adattivi per rilevare anomalie ed eventi di rischio che possono indicare la compromissione di un'identità. Sulla base di tali dati, Identity Protection genera report e avvisi che consentono di analizzare gli eventi di rischio e adottare le azioni di correzione o mitigazione appropriate.

Azure Active Directory Identity Protection è ben più di un semplice strumento di monitoraggio e reporting. In base agli eventi di rischio, Identity Protection calcola un livello di rischio utente per ogni utente e permette di configurare criteri basati sul rischio per proteggere automaticamente le identità dell'organizzazione.

I criteri basati sul rischio, insieme ad altri [controlli di accesso condizionale](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) che sono disponibili in Azure Active Directory ed [EMS](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access), possono eseguire il blocco automatico o proporre azioni di correzione adattive, incluse la reimpostazione della password e l'applicazione dell'autenticazione a più fattori.

### <a name="identity-protection-capabilities"></a>Funzionalità di Identity Protection

Azure Active Directory Identity Protection è ben più di un semplice strumento di monitoraggio e reporting. Per proteggere le identità dell'organizzazione, è possibile configurare criteri basati sul rischio che rispondano automaticamente ai problemi rilevati quando viene raggiunto un livello di rischio specificato. Questi criteri, con altri controlli di accesso condizionale forniti da Azure Active Directory e da EMS, possono eseguire il blocco automatico o avviare azioni di correzione adattive, incluse la reimpostazione della password e l'applicazione dell'autenticazione a più fattori.

Di seguito sono riportati esempi di alcuni dei modi in cui Azure Identity Protection consente di proteggere gli account e le identità:

[Rilevamento di eventi di rischio e account rischiosi](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#detection)
-   Rilevamento di sei tipi di eventi di rischio tramite regole euristiche e apprendimento automatico.
-   Calcolo dei livelli di rischio utente.
-   Raccomandazioni personalizzate per migliorare il comportamento di sicurezza in generale evidenziando le vulnerabilità.

[Analisi degli eventi di rischio](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#investigation)
-   Invio di notifiche per gli eventi di rischio.
-   Analisi degli eventi di rischio con informazioni rilevanti e contestuali.
-   Flussi di lavoro di base per tenere traccia delle analisi.
-   Accesso semplificato ad azioni di correzione come la reimpostazione della password.

[Criteri di accesso condizionale basati sul rischio](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#risky-sign-ins)
-   Mitigazione degli accessi rischiosi con il blocco degli accessi o le richieste di autenticazione a più fattori.
-   Blocco o protezione degli account utente rischiosi.
-   Richiedere la registrazione degli utenti per l'autenticazione a più fattori.

### <a name="azure-ad-privileged-identity-management"></a>Gestione identità con privilegi di Azure AD

Con [Azure Active Directory Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) è possibile gestire, il controllo e monitorare l'accesso all'interno dell'organizzazione. Questa funzionalità include l'accesso alle risorse in Azure AD e altri Microsoft Online Services, ad esempio Office 365 o Microsoft Intune.

![Schema di gestione identità con privilegi di Azure AD](./media/azure-threat-detection/azure-threat-detection-fig2.png)

PIM consente di:

-   Ricevere avvisi e report sugli amministratori di Azure AD e accesso just-in-time (JIT) come amministratore ai Microsoft Online Services, ad esempio Office 365 e Intune.

-   Ottenere report sulla cronologia degli accessi degli amministratori e sulle modifiche alle assegnazioni degli amministratori.

-   Ricevere avvisi relativi all'accesso a un ruolo con privilegi.

## <a name="operations-management-suite"></a>Operations Management Suite

[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) è una soluzione Microsoft per la gestione IT basata sul cloud che consente di gestire e proteggere l'infrastruttura locale e cloud. Poiché OMS viene implementato come servizio basato sul cloud, è possibile renderlo operativo rapidamente con un investimento minimo nei servizi di infrastruttura. Le nuove funzionalità di sicurezza sono disponibili automaticamente, evitando così i costi di manutenzione e aggiornamento continui.

Oltre a offrire importanti servizi in sé, OMS può essere integrato con i componenti di System Center, come [System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/), per estendere al cloud gli investimenti per la gestione della sicurezza già esistenti. System Center e OMS possono essere usati insieme per offrire un'esperienza di gestione ibrida completa.

### <a name="holistic-security-and-compliance-posture"></a>Approccio olistico a sicurezza e conformità

Il [dashboard Sicurezza e controllo di OMS](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) consente di ottenere una panoramica completa dell'infrastruttura di sicurezza IT dell'organizzazione, grazie alla disponibilità di query di ricerca predefinite per i problemi rilevanti che richiedono l'attenzione dell'utente. Il dashboard Sicurezza e controllo è la schermata iniziale per tutti gli elementi correlati alla sicurezza in OMS. Fornire una visione generale lo stato di sicurezza dei computer. Consente anche di visualizzare tutti gli eventi delle ultime 24 ore, di 7 giorni o di qualsiasi altro intervallo di tempo personalizzato.

I dashboard OMS permettono di comprendere rapidamente e facilmente l'approccio complessivo alla sicurezza di qualsiasi ambiente, tutto nel contesto delle operazioni IT, tra cui la valutazione dell'aggiornamento software, la valutazione antimalware e le linee di base di configurazione. I dati del log di sicurezza sono facilmente accessibili per semplificare i processi di controllo di sicurezza e conformità.

![Il dashboard Sicurezza e controllo di OMS](./media/azure-threat-detection/azure-threat-detection-fig3.jpg)

Il dashboard Sicurezza e controllo di OMS è organizzato in quattro categorie principali:

-   **Domini di sicurezza**: qui si possono esplorare ulteriormente i record di sicurezza nel tempo, accedere alla valutazione della presenza di malware, aggiornare la valutazione, verificare la sicurezza della rete, controllare identità e accesso alle informazioni, visualizzare i computer con eventi di sicurezza e accedere rapidamente al dashboard del Centro sicurezza di Azure.

-   **Errori rilevanti**: consente di identificare rapidamente il numero di problemi attivi e la relativa gravità.

-   **Rilevamenti (anteprima)**: consente di identificare i modelli di attacco mostrando gli avvisi di sicurezza relativi alle risorse non appena vengono generati.

-   **Intelligence per le minacce**: consente di identificare i modelli di attacco mostrando il numero totale di server con traffico IP dannoso in uscita, il tipo di minaccia e una mappa delle posizioni degli IP.

-   **Query comuni sulla sicurezza**: elenca le query più comuni sulla sicurezza che è possibile usare per monitorare l'ambiente. Quando si seleziona una qualsiasi query, il riquadro di ricerca apre e mostra i risultati della query.

### <a name="insight-and-analytics"></a>Informazioni dettagliate e analisi
Al centro di [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) è presente il repository OMS, ospitato da Azure.

![Schema di informazioni dettagliate e analisi](./media/azure-threat-detection/azure-threat-detection-fig4.png)

I dati vengono raccolti nel repository da origini connesse configurando le origini dati e aggiungendo soluzioni alla sottoscrizione.

![Il dashboard OMS ](./media/azure-threat-detection/azure-threat-detection-fig5.png)

Origini dati e soluzioni creano tipi di record distinti con il proprio set di proprietà, ma è comunque possibile analizzarli insieme nelle query al repository. Questa funzionalità consente di usare gli stessi strumenti e metodi per lavorare con diversi tipi di dati raccolti da diverse origini.


La maggior parte delle interazioni con Log Analytics avviene attraverso il portale OMS che viene eseguito in qualsiasi browser e fornisce all'utente l'accesso alle impostazioni e a più strumenti di configurazione per analizzare e agire in base ai dati raccolti. Nel portale è possibile usare:
* [Ricerche nei log](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-searches) in cui si creano query per analizzare i dati raccolti.
* [Dashboard](https://docs.microsoft.com/azure/log-analytics/log-analytics-dashboards), che è possibile personalizzare con rappresentazioni grafiche delle ricerche più importanti.
* [Soluzioni](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions), che forniscono ulteriori funzionalità e strumenti di analisi.

![Strumenti di analisi](./media/azure-threat-detection/azure-threat-detection-fig6.png)

Le soluzioni aggiungono funzionalità a Log Analytics. Vengono eseguite principalmente nel cloud e forniscono un'analisi dei dati raccolti nel repository OMS. Le soluzioni possono anche definire nuovi tipi di record da raccogliere, che possono essere analizzati con ricerche di log o con l'interfaccia utente aggiuntiva fornita dalla soluzione nel dashboard di OMS.

Il dashboard Sicurezza e controllo è un esempio di questo tipo di soluzioni.

### <a name="automation-and-control-alert-on-security-configuration-drifts"></a>Automazione e controllo: avvisi di deviazione dalla configurazione di sicurezza

Automazione di Azure consente di automatizzare i processi amministrativi con runbook basati su PowerShell ed eseguiti nel cloud. I runbook possono inoltre essere eseguiti in un server nel data center locale per gestire le risorse locali. Automazione di Azure consente la gestione della configurazione con Configurazione dello stato desiderato (DSC) di PowerShell.

![Schema di Automazione di Azure](./media/azure-threat-detection/azure-threat-detection-fig7.png)

È possibile creare e gestire risorse DSC ospitate in Azure e applicarle a sistemi cloud e locali. In questo modo, è possibile definire e implementare automaticamente la loro configurazione o ricevere report sulle deviazioni per garantire che le configurazioni di sicurezza restino all'interno dei criteri.

## <a name="azure-security-center"></a>Centro sicurezza di Azure

Il Centro sicurezza di Azure consente di proteggere le risorse di Azure. Integra il monitoraggio della sicurezza e la gestione dei criteri in tutte le sottoscrizioni di Azure. All'interno del servizio, è possibile definire criteri per entrambe le sottoscrizioni di Azure e [gruppi di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) per una maggiore granularità.

![Diagramma del Centro sicurezza di Azure](./media/azure-threat-detection/azure-threat-detection-fig8.png)

I ricercatori Microsoft nell'ambito della sicurezza sono costantemente impegnati nella ricerca delle minacce. Hanno accesso a un ampio set di dati di telemetria acquisiti grazie alla presenza globale di Microsoft nel cloud e in locale. Questa raccolta di set di dati di vasta portata e diversificata consente a Microsoft di individuare nuovi modelli di attacco e tendenze nei propri prodotti consumer e aziendali locali, nonché nei servizi online.

Di conseguenza, il Centro sicurezza può aggiornare rapidamente gli algoritmi di rilevamento a fronte del rilascio di exploit nuovi e sofisticati da parte di utenti malintenzionati. Questo approccio consente di tenere il passo con un ambiente caratterizzato da minacce in rapida evoluzione.

![Rilevamento delle minacce del centro sicurezza](./media/azure-threat-detection/azure-threat-detection-fig9.jpg)

Il sistema di rilevamento delle minacce del Centro sicurezza funziona mediante la raccolta automatica di informazioni sulla sicurezza dalle risorse di Azure, dalla rete e dalle soluzioni dei partner connessi. Per identificare le minacce, analizza queste informazioni, correlando quelle raccolte da più origini.

Gli avvisi di sicurezza sono classificati in ordine di priorità nel Centro sicurezza insieme a indicazioni su come su correggere la minaccia.

Il Centro sicurezza si avvale di analisi della sicurezza avanzate, che vanno ben oltre gli approcci basati sulle firme. Innovazioni in dati di grandi dimensioni e tecnologie di [apprendimento automatico](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) sono utilizzate per valutare eventi nell'intera infrastruttura cloud. Gli strumenti di analisi avanzati sono in grado di rilevare minacce altrimenti impossibili da identificare mediante approcci manuali e previsione dell'evoluzione di attacchi. Questi tipi di analisi di sicurezza sono illustrate nelle sezioni successive.

### <a name="threat-intelligence"></a>Intelligence per le minacce

Microsoft ha accesso a un'enorme quantità di dati di intelligence per le minacce globali.

Il flusso di dati di telemetria proviene da più origini, ad esempio Azure, Office 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Crimes Unit (DCU) e Microsoft Security Response Center (MSRC).

![Risultati di intelligence sulle minacce](./media/azure-threat-detection/azure-threat-detection-fig10.jpg)

I ricercatori ricevono anche informazioni di intelligence per le minacce condivise tra i principali provider di servizi cloud e sottoscrivono i feed di terze parti di intelligence per le minacce. Il Centro sicurezza di Azure usa queste informazioni per avvisare gli utenti nel caso di minacce provenienti da attori dannosi noti. Di seguito sono riportati alcuni esempi:

-   **Uso della potenza di Machine Learning**: il Centro sicurezza di Azure ha accesso a una grande quantità di dati sulle attività di rete nel cloud, che può usare per rilevare le minacce alle distribuzioni di Azure.

-   **Rilevamento di attacchi di forza bruta**: Machine Learning viene usato per creare un modello cronologico dei tentativi di accesso remoto, che consente di rilevare gli attacchi di forza bruta contro porte SQL, Remote Desktop Protocol (RDP) e Secure Shell (SSH).

-   **Rilevamento di attacchi DDoS in uscita e Botnet**: un obiettivo comune agli attacchi alle risorse cloud è sfruttare la potenza di calcolo di queste risorse per eseguire altri attacchi.

-   **Nuovi server e macchine virtuali per l'analisi del comportamento**: se una macchina virtuale o un server viene compromesso, gli utenti malintenzionati usano un'ampia gamma di tecniche per eseguire codice dannoso in tale sistema evitando il rilevamento, garantendo la persistenza e contrastando i controlli di sicurezza.

-   **Rilevamento delle minacce per il database SQL Azure**: il rilevamento delle minacce per il database di SQL Azure identifica le attività di database anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere ai database o sfruttarli.

### <a name="behavioral-analytics"></a>Analisi del comportamento

L'analisi del comportamento è una tecnica che analizza e confronta i dati con una raccolta di modelli noti. Tuttavia, questi modelli non sono semplici firme. Sono determinati usando algoritmi di Machine Learning complessi applicati a set di dati di grandi dimensioni.

![Risultati dell’analisi del comportamento](./media/azure-threat-detection/azure-threat-detection-fig11.jpg)

I criteri sono anche definiti tramite l'attento esame di comportamenti dannosi da parte di analisti esperti. Il Centro sicurezza di Azure può usare le analisi del comportamento per identificare le risorse compromesse in base all'analisi dei log delle macchine virtuali, dei dispositivi di rete virtuale, dell'infrastruttura, nonché dei dump di arresto anomalo del sistema e di altre origini.

Esiste inoltre una correlazione dei criteri con altri segnali per verificare la presenza di elementi a riprova di una campagna su larga scala. La correlazione consente di identificare gli eventi che risultano coerenti con gli indicatori di violazione stabiliti.

Di seguito sono riportati alcuni esempi:
-   **Esecuzione di processi sospetti**: gli utenti malintenzionati usano diverse tecniche per eseguire software dannoso senza che venga rilevato. Ad esempio, un utente malintenzionato potrebbe assegnare al malware gli stessi nomi di file di sistema legittimi, inserendo però questi file in percorsi alternativi, usare un nome simile a quello di un file innocuo o mascherare la vera estensione del file. I modelli del Centro sicurezza elaborano i comportamenti e monitorano l'esecuzione dei processi per rilevare outlier come questi.

-   **Malware nascosto e tentativi di exploit**: il malware sofisticato può eludere i prodotti antimalware tradizionali, non scrivendo mai su disco o crittografando i componenti software archiviati su disco. Tuttavia, il malware può essere rilevato tramite l'analisi della memoria, perché per funzionare il malware deve lasciare tracce in memoria. Quando il software si arresta in modo anomalo, un dump di arresto anomalo acquisisce una porzione della memoria al momento dell'arresto. Analizzando la memoria nel dump di arresto anomalo, il Centro sicurezza di Azure può rilevare le tecniche usate per sfruttare le vulnerabilità del software, accedere ai dati riservati e rimanere permanentemente all'interno di un computer infetto in modo furtivo senza influire sulle relative prestazioni.

-   **Spostamento laterale e ricognizione interna**: per rimanere permanentemente all'interno di una rete compromessa e individuare e raccogliere dati importanti, gli utenti malintenzionati provano spesso a muoversi lateralmente dal computer compromesso spostandosi in altri computer all'interno della stessa rete. Il Centro sicurezza consente di monitorare le attività di elaborazione e accesso per individuare i tentativi di espansione del punto di appoggio di un utente malintenzionato all'interno della rete, ad esempio il probing della rete per individuare l'esecuzione di comandi remoti e l'enumerazione di account.

-   **Script PowerShell dannosi**: PowerShell può essere usato da utenti malintenzionati per eseguire codice dannoso in macchine virtuali di destinazione per molteplici scopi. Il Centro sicurezza ispeziona l'attività di PowerShell alla ricerca di prove di attività sospette.

-   **Attacchi in uscita**: gli utenti malintenzionati attaccano spesso le risorse cloud con l'obiettivo di usarle per organizzare altri attacchi. Le macchine virtuali compromesse, ad esempio, possono essere usate per sferrare attacchi di forza bruta contro altre macchine virtuali, inviare posta indesiderata o analizzare le porte aperte e altri dispositivi su Internet. Applicando le tecniche di apprendimento automatico al traffico di rete, il Centro sicurezza può rilevare quando le comunicazioni di rete in uscita superano la norma. Nel caso in cui venga rilevata posta indesiderata, il Centro sicurezza correla anche il traffico di posta elettronica insolito con le informazioni di Office 365 per determinare se la posta elettronica è probabilmente dannosa o il risultato di una campagna di posta elettronica legittima.

### <a name="anomaly-detection"></a>Rilevamento anomalie

Il Centro sicurezza di Azure usa inoltre il rilevamento anomalie per identificare le minacce. A differenza dell'analisi del comportamento, che dipende da modelli noti derivati da set di dati di grandi dimensioni, il rilevamento anomalie è più "personalizzato" e incentrato sulle baseline specifiche delle distribuzioni. Le tecniche di apprendimento automatico vengono applicate per determinare la normale attività per le distribuzioni dei clienti e quindi vengono generate regole per definire le condizioni degli outlier che possono rappresentare un evento di sicurezza. Ecco un esempio:

-   **Attacchi di forza bruta RDP/SSH in ingresso**: nelle distribuzioni dei clienti possono essere presenti macchine virtuali occupate da molti accessi ogni giorno e altre con pochi accessi, se esistenti. Il Centro sicurezza di Azure può determinare l'attività di accesso di base per queste macchine virtuali e usare le tecniche di apprendimento automatico per definire gli eventi attorno alle normali attività di accesso. In caso di discrepanza con la baseline definita per le caratteristiche relative all'accesso, potrebbe essere generato un avviso. Anche in questo caso, le tecniche di apprendimento automatico determinano gli eventi significativi.

### <a name="continuous-threat-intelligence-monitoring"></a>Monitoraggio continuo dell'intelligence per le minacce

Il Centro sicurezza di Azure opera insieme a team dedicati alle ricerche sulla sicurezza e all'analisi scientifica dei dati a livello mondiale che monitorano costantemente le modifiche che avvengono nel panorama delle minacce. Sono incluse le iniziative seguenti:

-   **Monitoraggio dell'intelligence per le minacce**: questo tipo di intelligence include meccanismi, indicatori, implicazioni e consigli utili sulle minacce esistenti o emergenti. Queste informazioni sono condivise nella community sulla sicurezza e Microsoft monitora costantemente i feed di intelligence per le minacce da origini interne ed esterne.

-   **Condivisione dei segnali**: le informazioni dettagliate dai team della sicurezza nell'ampio portfolio di servizi, server e dispositivi endpoint client locali e cloud di Microsoft vengono condivise e analizzate.

-   **Specialisti della sicurezza Microsoft**: in contatto costante con i team Microsoft che operano in ambiti di sicurezza specializzati, ad esempio analisi scientifiche e rilevamento di attacchi Web.

-   **Ottimizzazione del rilevamento**: gli algoritmi vengono eseguiti su set di dati reali del cliente e ricercatori dedicati alla sicurezza collaborano con i clienti per convalidare i risultati. Per perfezionare gli algoritmi di Machine Learning vengono usati veri e falsi positivi.

Questi sforzi combinati convergono in rilevamenti nuovi e migliorati, da cui è possibile trarre vantaggio immediatamente, senza che sia richiesta alcuna azione.

## <a name="advanced-threat-detection-features-other-azure-services"></a>Funzionalità di rilevamento delle minacce avanzato: altri servizi di Azure

### <a name="virtual-machines-microsoft-antimalware"></a>Macchine virtuali: Microsoft antimalware

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) per Azure è una soluzione con un agente singolo per applicazioni e ambienti tenant, progettata per l'esecuzione in background senza intervento da parte dell'utente. È possibile distribuire la protezione in base alle esigenze dei carichi di lavoro dell'applicazione, con una configurazione sicura per impostazione predefinita o avanzata personalizzata, incluso il monitoraggio antimalware. Antimalware Azure è un'opzione di sicurezza per macchine virtuali di Azure e viene installato automaticamente in tutte le macchine virtuali PaaS di Azure.

#### <a name="microsoft-antimalware-core-features"></a>Funzionalità principali di Microsoft Antimalware

Di seguito sono riportate alcune funzionalità di Azure per distribuire e abilitare Microsoft Antimalware per le applicazioni:

-   **Protezione in tempo reale**: monitora l'attività in Servizi cloud e Macchine virtuali per rilevare e bloccare l'esecuzione di malware.

-   **Analisi pianificata**: esegue periodicamente un'analisi mirata per rilevare il malware, inclusi i programmi in esecuzione attiva.

-   **Correzione del malware**: agisce automaticamente sul malware rilevato, ad esempio eliminando o mettendo in quarantena i file dannosi e pulendo le voci dannose del Registro di sistema.

-   **Aggiornamenti delle firme**: installa automaticamente le firme di protezione più recenti (definizioni di virus) per garantire che la protezione sia aggiornata in base a una frequenza predeterminata.

-   **Aggiornamenti del motore antimalware**: aggiorna automaticamente il motore antimalware di Microsoft.

-   **Aggiornamenti della piattaforma antimalware**: aggiorna automaticamente la piattaforma Microsoft Antimalware.

-   **Protezione attiva**: segnala a Microsoft Azure i metadati di telemetria sulle minacce rilevate e sulle risorse sospette per garantire una risposta rapida al mutevole panorama delle minacce, oltre ad abilitare la distribuzione di firme sincrone in tempo reale tramite Microsoft Active Protection System.

-   **Creazione di report di esempi**: raccoglie e segnala al servizio Microsoft Antimalware esempi che consentono di perfezionare il servizio e di abilitare la risoluzione dei problemi.

-   **Esclusioni**: consente agli amministratori di applicazioni e del servizio di configurare alcuni file, processi e unità per escluderli dalla protezione e dall'analisi per motivi di prestazioni e/o di altro tipo.

-   **Raccolta di eventi antimalware**: registra l'integrità del servizio antimalware, le attività sospette e le azioni di correzione eseguite nel registro eventi del sistema operativo e le raccoglie nell'account di archiviazione di Azure del cliente.

### <a name="azure-sql-database-threat-detection"></a>Rilevamento delle minacce per il database SQL di Azure

Il [rilevamento delle minacce per il database SQL di Azure](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) è una nuova funzionalità di intelligence sulla sicurezza incorporata nel database SQL di Azure. Il rilevamento delle minacce per il database SQL di Azure è sempre in funzione per apprendere, profilare e rilevare attività di database anomale e identificare potenziali minacce per il database.

I responsabili della sicurezza o altri amministratori designati possono ricevere una notifica immediata sulle attività di database sospette non appena si verificano. Ogni notifica contiene dettagli sulle attività sospette e consigli su come eseguire ulteriori indagini e mitigare la minaccia.

Attualmente, il rilevamento delle minacce per il database SQL di Azure rileva vulnerabilità potenziali, attacchi SQL injection e modelli anomali di accesso ai database.

Dopo aver ricevuto una notifica di posta elettronica di rilevamento delle minacce, gli utenti sono in grado di trovare e visualizzare i record di controllo tramite un collegamento diretto nel messaggio di posta elettronica. Il link apre un visualizzatore di controllo o un modello di Excel di controllo preconfigurato che mostra i record di controllo rilevanti al momento dell'evento sospetto, secondo quanto segue:

-   Archiviazione di controllo per il database/server con le attività di database anomale.

-   Tabella di archiviazione di controllo pertinente usata al momento dell'evento per la scrittura del log di controllo.

-   Record di controllo dell’ora subito dopo il verificarsi di eventi.

-   Record di controllo con ID evento simile al momento dell'evento (facoltativo per alcuni rilevatori).

I rilevatori di minacce per il database SQL fanno uso di una delle metodologie di rilevamento riportate di seguito:

-   **Rilevamento deterministico:** rileva modelli sospetti (in base a regole) nelle query del client SQL corrispondenti ad attacchi noti. Questa metodologia offre prestazioni elevate di rilevamento e falsi positivi ridotti, ma ha una copertura limitata perché rientra nella categoria dei "rilevamenti atomici".

-   **Rilevamento del comportamento:** rileva le attività anomale, vale a dire comportamenti anomali per il database non rilevati negli ultimi 30 giorni. Esempio di attività anomala per il client SQL può essere un picco di accessi non riusciti o query, l'estrazione di un volume elevato di dati, query canoniche insolite e l'uso di indirizzi IP sconosciuti per accedere al database.

### <a name="application-gateway-web-application-firewall"></a>Web application firewall del gateway applicazione

[Web application firewall (WAF)](../app-service/environment/app-service-app-service-environment-web-application-firewall.md) è una funzionalità del [gateway applicazione di Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-webapplicationfirewall-overview) che consente di proteggere le applicazioni Web che usano il gateway applicazione per funzioni standard di [controller per la distribuzione di applicazioni](https://kemptechnologies.com/in/application-delivery-controllers). Web application firewall protegge infatti le applicazioni dalla maggior parte delle [10 vulnerabilità Open Web Application Security Project (OWASP) più diffuse](https://www.owasp.org/index.php/Top_10_2010-Main).

![Schema deò Web application firewall del gateway applicazione di Azure](./media/azure-threat-detection/azure-threat-detection-fig13.png)

Le protezioni includono:

-   Protezione dagli attacchi SQL injection.

-   Protezione dagli attacchi di scripting intersito.

-   Protezione dai comuni attacchi Web, ad esempio attacchi di iniezione di comandi, richieste HTTP non valide, attacchi HTTP Response Splitting e Remote File Inclusion.

-   Protezione dalle violazioni del protocollo HTTP.

-   Protezione contro eventuali anomalie del protocollo HTTP, ad esempio user agent host mancante e accept header.

-   Prevenzione contro robot, crawler e scanner.

-   Rilevamento di errori di configurazione dell'applicazione comuni (ad esempio, Apache, IIS e così via).

La configurazione del WAF nel gateway applicazione offre i vantaggi seguenti:

-   Protezione dell'applicazione Web dalle vulnerabilità e dagli attacchi del Web, senza alcuna modifica al codice di back-end.

-   Protezione contemporanea di più applicazioni Web con un gateway applicazione. Un gateway applicativo supporta l’hosting di max. 20 siti Web.

-   Monitoraggio dell'applicazione Web contro gli attacchi tramite report in tempo reale generati dai log del WAF del gateway applicazione.

-   Aiuta a soddisfare i requisiti di conformità. Alcuni controlli di conformità richiedono che tutti gli endpoint comunicanti con Internet siano protetti da una soluzione WAF.

### <a name="anomaly-detection-api-built-with-azure-machine-learning"></a>API di rilevamento delle anomalie: integrata in Azure Machine Learning

L'API di rilevamento delle anomalie è un'API che è utile per rilevare un'ampia gamma di criteri anomali nei dati delle serie temporali. L'API assegna un punteggio di anomalia a ogni punto dati della serie temporale, che può essere usato per la creazione di avvisi, il monitoraggio tramite dashboard o la connessione con i sistemi di generazione dei ticket.

L'[API di rilevamento delle anomalie](https://docs.microsoft.com/azure/machine-learning/machine-learning-apps-anomaly-detection-api) può rilevare i seguenti tipi di anomalie nei dati delle serie temporali:

-   **Picchi e flessioni**: quando si monitora il numero di errori di accesso a un servizio o il numero di transazioni completate in un sito di e-commerce, i picchi o le flessioni possono indicare attacchi alla sicurezza o interruzioni del servizio.

-   **Tendenze positive e negative**: quando si esegue il monitoraggio dell'utilizzo di memoria nei sistemi informatici, la riduzione della dimensione della memoria libera indica una potenziale perdita di memoria. Per il monitoraggio della lunghezza della coda di servizio, una tendenza persistente all'aumento potrebbe indicare un problema software sottostante.

-   **Cambi di livello e le modifiche all'intervallo dinamico dei valori:** i cambi di livello nelle latenze di un servizio dopo un aggiornamento o livelli ridotti di eccezioni dopo un aggiornamento possono essere interessanti da monitorare.

L'API basata sull'apprendimento automatico offre:

-   **Rilevamento flessibile e affidabile:** i modelli di rilevamento delle anomalie consentono agli utenti di configurare le impostazioni di sensibilità e rilevare anomalie tra set di dati stagionali e non stagionali. Gli utenti possono modificare il modello di rilevamento delle anomalie per rendere l'API più o meno sensibile in base alle esigenze. Ciò significa poter rilevare le anomalie più o meno visibili nei dati con e senza modelli stagionali.

-   **Rilevamento ridimensionabile e tempestivo:** il monitoraggio tradizionale con soglie impostate in base alle competenze degli esperti è costosi e non offre scalabilità per milioni di set di dati che cambiano continuamente. I modelli di rilevamento delle anomalie in questa API vengono acquisiti e i modelli vengono ottimizzati automaticamente in base a dati in tempo reale e cronologici.

-   **Rilevamento proattivo ed eseguibile:** è possibile applicare il rilevamento di tendenze lente e cambi di livello per rilevare in anticipo le anomalie. Il rilevamento dei primi segni di anomalia permette di indirizzare gli utenti verso l'analisi e la risoluzione delle aree problematiche. Inoltre, è possibile sviluppare modelli di analisi della causa radice e gli strumenti di avviso sull'API di rilevamento delle anomalie.

L'API rappresenta una soluzione efficace ed efficiente per un'ampia gamma di scenari, come il monitoraggio dell'integrità del servizio e dei KPI, l'IoT, il monitoraggio delle prestazioni e del traffico di rete. Di seguito sono riportati alcuni scenari comuni in cui questa API può risultare utile:

- Reparti IT che hanno bisogno di strumenti per tenere traccia di eventi, codici di errore, log di utilizzo e prestazioni (CPU, memoria e così via) in modo tempestivo.

-   Siti di e-commerce che vogliono tenere traccia delle attività dei clienti, delle visualizzazioni di pagine dei clic e così via.

-   Aziende di pubblici servizi che vogliono tenere traccia dei consumi di acqua, gas, elettricità e altre risorse.

-   Servizi di gestione di strutture ed edifici che vogliono monitorare temperatura, umidità, traffico e così via.

-   Produttori/IoT che vogliono usare i dati dei sensori nelle serie temporali per monitorare il flusso di lavoro, la qualità e così via.

-   Provider di servizi, ad esempio call center, che vogliono monitorare le tendenze della domanda di servizi, il volume di eventi imprevisti, la lunghezza delle code di attesa e così via.

-   Gruppi di analisi business che vogliono monitorare in tempo reale i movimenti anomali dei KPI aziendali (ad esempio il volume delle vendite, il sentiment dei clienti o i prezzi).

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) è un componente fondamentale dello stack di sicurezza di Microsoft Cloud. Si tratta di una soluzione completa che consente all'organizzazione di sfruttare appieno il potenziale delle applicazioni cloud, mantenendo il controllo grazie a una maggiore visibilità nelle attività. Consente inoltre di migliorare la protezione dei dati critici nelle applicazioni cloud.

Grazie a strumenti che permettono di scoprire shadow IT, valutare i rischi, applicare i criteri, analizzare le attività e arrestare le minacce, l'organizzazione può passare al cloud in piena sicurezza mantenendo il controllo dei dati critici.

| | |
|---|---|
| Scoprire | Scoprire shadow IT con Cloud App Security. Ottenere visibilità tramite l'identificazione di app, attività, utenti, dati e file nell'ambiente cloud. Trovare app di terze parti connesse al cloud.|
|Analisi dei problemi | Analizzare i problemi delle app cloud tramite strumenti forensi di approfondimento in app rischiose, utenti specifici e file nella rete. Trovare i modelli nei dati raccolti dal cloud. Generare report per monitorare il cloud. |
| Controllo | Mitigare il rischio tramite l'impostazione di criteri e avvisi per ottenere il massimo controllo sul traffico di rete nel cloud. Usare Cloud App Security per eseguire la migrazione degli utenti verso app cloud alternative sicure e approvate. |
| Proteggere | Usare Cloud App Security per approvare o vietare applicazioni, applicare misure di prevenzione contro la perdita di dati, controllare autorizzazioni e condivisioni e generare avvisi e report personalizzati. |
| Controllo | Mitigare il rischio tramite l'impostazione di criteri e avvisi per ottenere il massimo controllo sul traffico di rete nel cloud. Usare Cloud App Security per eseguire la migrazione degli utenti verso app cloud alternative sicure e approvate. |
| | |


![Schema di Cloud App Security](./media/azure-threat-detection/azure-threat-detection-fig14.png)

Cloud App Security integra la visibilità con il cloud tramite:

-   Uso di Cloud Discovery per eseguire il mapping e identificare l'ambiente cloud e le app cloud usate dall'organizzazione.

-   Approvazione e divieto di app nel cloud.

-   Uso di connettori app facili da distribuire che si avvalgono di API del provider per ottenere visibilità e governance delle app a cui ci si connette.

-   Controllo continuo grazie all'impostazione e all'ottimizzazione costante di criteri.

Durante la raccolta di dati da queste origini, Cloud App Security esegue analisi complesse su di essi. Segnala immediatamente le attività anomale e offre una visibilità dettagliata nell'ambiente cloud. È possibile configurare criteri in Cloud App Security e usarli per proteggere tutti i dati nell'ambiente cloud.

## <a name="third-party-advanced-threat-detection-capabilities-through-the-azure-marketplace"></a>Funzionalità di rilevamento avanzato delle minacce di terze parti tramite Azure Marketplace

### <a name="web-application-firewall"></a>Web application firewall

Web application firewall controlla il traffico Web in ingresso e blocca SQL injection, attacchi tramite script da altri siti, caricamenti di malware, attacchi DDoS alle applicazioni e altri attacchi destinati alle applicazioni Web. Esamina anche le risposte provenienti dai server Web back-end per la prevenzione della perdita dei dati. Il motore di controllo di accesso integrato consente agli amministratori di creare criteri di controllo di accesso granulari per l'autenticazione, l'autorizzazione e la contabilità e garantisce alle organizzazioni un'autenticazione e un controllo utente affidabili.

Web Application Firewall offre i vantaggi seguenti:

-   Rileva e blocca SQL injection, attacchi tramite script da altri siti, caricamenti di malware, DDoS di applicazioni e altri attacchi destinati alle applicazioni.

-   Offre autenticazione e controllo di accesso.

-   Analizza il traffico in uscita per rilevare i dati sensibili e può mascherare o bloccare la divulgazione di informazioni.

-   Accelera la distribuzione dei contenuti delle applicazioni Web con funzionalità quali la memorizzazione nella cache, la compressione e altre funzioni di ottimizzazione del traffico.

Per esempi di firewall di applicazioni Web disponibili su Azure Marketplace, vedere [Barracuda WAF, Brocade Virtual Web Application Firewall (vWAF), Imperva SecureSphere e il firewall IP ThreatSTOP](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf).

## <a name="next-steps"></a>Passaggi successivi

- [Funzionalità di rilevamento del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities): consentono di identificare le minacce attive rivolte alle risorse di Azure e forniscono le informazioni dettagliate necessarie per rispondere rapidamente a tali minacce.

- [Rilevamento delle minacce per il database SQL](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/): di Azure consente di risolvere i problemi relativi a potenziali minacce per i database.
