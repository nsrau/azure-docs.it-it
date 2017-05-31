---
title: Rilevamento delle minacce avanzato in Azure | Microsoft Docs
description: "Informazioni sulla protezione delle identità e le relative funzionalità."
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
ms.date: 04/27/2017
ms.author: TomSh
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 854ad17006b70dfbdaf680744320a87ffb654e13
ms.contentlocale: it-it
ms.lasthandoff: 05/12/2017


---

# <a name="azure-advanced-threat-detection"></a>Rilevamento delle minacce avanzato in Azure
## <a name="introduction"></a>Introduzione

### <a name="overview"></a>Panoramica

Microsoft ha sviluppato una serie di white paper, panoramiche sulla sicurezza, procedure consigliate ed elenchi di controllo per assistere i clienti di Azure con le diverse funzionalità correlate alla sicurezza disponibili nel contesto della piattaforma Azure. Gli argomenti variano per ampiezza e livello di approfondimento e vengono aggiornati periodicamente. Questo documento fa parte di tale serie come descritto nella sezione Sintesi di seguito.

### <a name="azure-platform"></a>Piattaforma Azure

Azure è una piattaforma aperta e flessibile di servizi cloud che supporta la più ampia gamma di sistemi operativi, linguaggi di programmazione, framework, strumenti, database e dispositivi.
Supporta i linguaggi di programmazione seguenti per:
-    Eseguire i contenitori Linux con l'integrazione Docker.
-    Compilare le app con JavaScript, Python, .NET, PHP, Java e Node.js.
-    Compilare back-end per dispositivi iOS, Android e Windows.

I servizi cloud pubblici di Azure supportano le stesse tecnologie già considerate affidabili e usate da milioni di sviluppatori e professionisti IT.

Quando si esegue la migrazione a un cloud pubblico con un'organizzazione, quest'ultima è responsabile della protezione dei dati, della sicurezza e della governance di tutto il sistema.

L'infrastruttura di Azure è stata progettata, dalla struttura fino alle applicazioni, per ospitare milioni di clienti contemporaneamente e fornisce alle aziende una solida base per poter soddisfare le esigenze di sicurezza. Azure offre una vasta gamma di opzioni per configurare e personalizzare la sicurezza e soddisfare i requisiti delle distribuzioni di app. Questo documento consente di soddisfare questi requisiti.

### <a name="abstract"></a>Sunto

Microsoft Azure offre funzionalità predefinite di rilevamento delle minacce avanzato attraverso servizi quali Azure Active Directory, Azure Operations Management Suite (OMS) e Centro sicurezza di Azure. Questa raccolta di servizi e funzionalità di sicurezza offre un modo semplice e veloce per comprendere ciò che accade all'interno delle distribuzioni di Azure.

Questo white paper illustra gli approcci di Microsoft Azure alla diagnostica della vulnerabilità alle minacce e all'analisi dei rischi associati alle attività dannose nei confronti di server e altre risorse di Azure. Questo permette di determinare i metodi di identificazione e gestione delle vulnerabilità con soluzioni ottimizzate tramite la piattaforma Azure e tecnologie e servizi di sicurezza per i clienti.

Questo white paper è incentrato sulla tecnologia della piattaforma Azure e sui controlli per i clienti e non tratta contratti di servizio, modelli tariffari e attività DevOps.

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

![Azure Active Directory Identity Protection](./media/azure-threat-detection/azure-threat-detection-fig1.png)


[Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) è una funzionalità di [Azure AD Premium P2](https://docs.microsoft.com/azure/active-directory/active-directory-editions) che offre una panoramica degli eventi di rischio e delle potenziali vulnerabilità che interessano le identità dell'organizzazione. Microsoft protegge le identità basate sul cloud da oltre dieci anni. Con Azure AD Identity Protection, questi stessi sistemi di protezione sono ora a disposizione dei clienti aziendali. Identity Protection si avvale delle funzionalità di rilevamento anomalie di Azure AD, disponibili tramite i [report di Anomalie dell'attività di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-view-access-usage-reports#anomalous-activity-reports), e introduce nuovi tipi di eventi di rischio che permettono di rilevare le anomalie in tempo reale.

Identity Protection usa l'euristica e algoritmi di apprendimento automatico adattivi per rilevare anomalie ed eventi di rischio che possono indicare la compromissione di un'identità. Sulla base di tali dati, Identity Protection genera report e avvisi che consentono di analizzare gli eventi di rischio e adottare le azioni di correzione o mitigazione appropriate.

Azure Active Directory Identity Protection è, del resto, ben più di un semplice strumento di monitoraggio e reporting. In base agli eventi di rischio, Identity Protection calcola un livello di rischio utente per ogni utente e permette di configurare criteri basati sul rischio per proteggere automaticamente le identità dell'organizzazione.

I criteri basati sul rischio, insieme ad altri [controlli di accesso condizionale](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) disponibili in Azure Active Directory ed [EMS](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access), possono eseguire il blocco automatico o proporre azioni di correzione adattive, incluse la reimpostazione della password e l'applicazione dell'autenticazione a più fattori.

### <a name="identity-protections-capabilities"></a>Funzionalità di Identity Protection

Azure Active Directory Identity Protection è ben più di un semplice strumento di monitoraggio e reporting. Per proteggere le identità dell'organizzazione, è possibile configurare criteri basati sul rischio che rispondano automaticamente ai problemi rilevati quando viene raggiunto un livello di rischio specificato. Questi criteri, con altri controlli di accesso condizionale forniti da Azure Active Directory e da EMS, possono eseguire il blocco automatico o avviare azioni di correzione adattive, incluse la reimpostazione della password e l'applicazione dell'autenticazione a più fattori.

Di seguito sono riportati esempi di alcuni dei modi in cui Azure Identity Protection consente di proteggere gli account e le identità:

[Rilevamento di eventi di rischio e account rischiosi:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#detection)
-    Rilevamento di sei tipi di eventi di rischio tramite regole euristiche e apprendimento automatico.
-    Calcolo dei livelli di rischio utente.
-    Raccomandazioni personalizzate per migliorare il comportamento di sicurezza in generale evidenziando le vulnerabilità.

[Analisi degli eventi di rischio:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#investigation)
-    Invio di notifiche per gli eventi di rischio.
-    Analisi degli eventi di rischio con informazioni rilevanti e contestuali.
-    Flussi di lavoro di base per tenere traccia delle analisi.
-    Accesso semplificato ad azioni di correzione come la reimpostazione della password.

[Criteri di accesso condizionale basati sul rischio:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#risky-sign-ins)
-    Criteri per mitigare gli accessi rischiosi con il blocco degli accessi o le richieste di autenticazione a più fattori.
-    Criteri per bloccare o proteggere gli account utente rischiosi.
-    Criteri per richiedere la registrazione degli utenti per l'autenticazione a più fattori

### <a name="azure-ad-privileged-identity-management-pim"></a>Azure AD Privileged Identity Management (PIM)

Con [Azure Active Directory (AD) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure),

![Gestione identità con privilegi di Azure AD](./media/azure-threat-detection/azure-threat-detection-fig2.png)

è possibile gestire, controllare e monitorare l'accesso all'interno dell'organizzazione. È incluso l'accesso alle risorse in Azure AD e altri Microsoft Online Services, ad esempio Office 365 o Microsoft Intune.

Azure AD Privileged Identity Management consente di effettuare le operazioni seguenti:

-    Ricevere avvisi e report sugli amministratori di Azure AD e accesso JIT come amministratore ai Microsoft Online Services, ad esempio Office 365 e Intune

-    Ottenere report sulla cronologia degli accessi degli amministratori e sulle modifiche alle assegnazioni degli amministratori

-    Ricevere avvisi relativi all'accesso a un ruolo con privilegi

## <a name="microsoft-operations-management-suite-oms"></a>Microsoft Operations Management Suite (OMS)

[Microsoft Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) è la soluzione Microsoft per la gestione IT basata sul cloud che consente di gestire e proteggere l'infrastruttura locale e cloud. Poiché OMS viene implementato come servizio basato sul cloud, è possibile renderlo operativo rapidamente con un investimento minimo nei servizi di infrastruttura. Le nuove funzionalità di sicurezza sono disponibili automaticamente, evitando così i costi di manutenzione e aggiornamento continui.

Oltre a offrire importanti servizi in sé, OMS può essere integrato con i componenti di System Center, come [System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/), per estendere al cloud gli investimenti per la gestione della sicurezza già esistenti. System Center e OMS possono essere usati insieme per offrire un'esperienza di gestione ibrida completa.

### <a name="holistic-security-and-compliance-posture"></a>Approccio olistico a sicurezza e conformità

Il [dashboard Sicurezza e controllo di OMS](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) consente di ottenere una panoramica completa dell'infrastruttura di sicurezza IT dell'organizzazione, grazie alla disponibilità di query di ricerca predefinite per i problemi rilevanti che richiedono l'attenzione dell'utente. Il dashboard Sicurezza e controllo è la schermata iniziale per tutti gli elementi correlati alla sicurezza in OMS. Fornire una visione generale lo stato di sicurezza dei computer. Consente anche di visualizzare tutti gli eventi delle ultime 24 ore, di 7 giorni o di qualsiasi altro intervallo di tempo personalizzato.

I dashboard OMS permettono di comprendere rapidamente e facilmente l'approccio complessivo alla sicurezza di qualsiasi ambiente, tutto nel contesto delle operazioni IT, tra cui: valutazione dell'aggiornamento software, valutazione antimalware e linee di base di configurazione. I dati del log di sicurezza sono facilmente accessibili per semplificare i processi di controllo di sicurezza e conformità.

Il dashboard Sicurezza e controllo di OMS è organizzato in quattro categorie principali:

![Dashboard di Sicurezza e controllo di OMS](./media/azure-threat-detection/azure-threat-detection-fig3.jpg)

-    **Domini di sicurezza**: in questa area si possono esplorare ulteriormente i record di sicurezza nel tempo, accedere alla valutazione della presenza di malware, aggiornare la valutazione, verificare la sicurezza della rete, controllare identità e accesso alle informazioni, visualizzare i computer con eventi di sicurezza e accedere rapidamente al dashboard del Centro sicurezza di Azure.

-    **Errori rilevanti**: questa opzione consente di identificare rapidamente il numero di problemi attivi e la relativa gravità.

-    **Rilevamenti (anteprima)**: consente di identificare i modelli di attacco visualizzando gli avvisi di sicurezza non appena vengono generati per le risorse.

-    **Intelligence per le minacce**: consente di identificare i modelli di attacco visualizzando il numero totale di server con traffico IP dannoso in uscita, il tipo di minaccia e una mappa che mostra la provenienza di questi indirizzi IP.

-    **Query comuni sulla sicurezza**: questa opzione fornisce un elenco delle query di sicurezza più comuni che è possibile usare per monitorare l'ambiente. Quando si fa clic in una di queste query, viene aperto il pannello Ricerca con i risultati della query.

### <a name="insight-and-analytics"></a>Insight & Analytics
Al centro di [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) c'è l'archivio OMS, ospitato nel cloud di Azure.

![Insight & Analytics](./media/azure-threat-detection/azure-threat-detection-fig4.png)

I dati vengono raccolti nel repository da origini connesse configurando le origini dati e aggiungendo soluzioni alla sottoscrizione.

![sottoscrizione](./media/azure-threat-detection/azure-threat-detection-fig5.png)

Le origini dati e le soluzioni creeranno diversi tipi di record con uno specifico set di proprietà, ma che possono comunque essere analizzati insieme nelle query al repository. In questo modo è possibile usare gli stessi strumenti e metodi per lavorare con diversi tipi di dati raccolti da diverse origini.


La maggior parte delle interazioni con Log Analytics avviene attraverso il portale OMS che viene eseguito in qualsiasi browser e fornisce all'utente l'accesso alle impostazioni e a più strumenti di configurazione per analizzare e agire in base ai dati raccolti. Dal portale è possibile usare le [ricerche nei log](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-searches) in cui si creano query per analizzare i dati raccolti, i [dashboard](https://docs.microsoft.com/azure/log-analytics/log-analytics-dashboards) che è possibile personalizzare con rappresentazioni grafiche delle ricerche più importanti e le [soluzioni](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) che forniscono funzionalità e strumenti di analisi aggiuntivi.

![strumenti di analisi](./media/azure-threat-detection/azure-threat-detection-fig6.png)

Le soluzioni aggiungono funzionalità a Log Analytics. Vengono eseguite principalmente nel cloud e forniscono un'analisi dei dati raccolti nel repository OMS. Possono anche definire nuovi tipi di record da raccogliere, che possono essere analizzati con ricerche di log o con l'interfaccia utente aggiuntiva fornita dalla soluzione nel dashboard di OMS.
Sicurezza e controllo è un esempio di questo tipo di soluzioni.



### <a name="automation--control-alert-on-security-configuration-drifts"></a>Automazione e controllo: avvisi di deviazione dalla configurazione di sicurezza

Automazione di Azure consente di automatizzare i processi amministrativi con runbook basati su PowerShell ed eseguiti nel cloud di Azure. I runbook possono inoltre essere eseguiti in un server nel data center locale per gestire le risorse locali. Automazione di Azure consente la gestione della configurazione con PowerShell DSC (Desired State Configuration).

![Automazione di Azure](./media/azure-threat-detection/azure-threat-detection-fig7.png)

È possibile creare e gestire risorse DSC ospitate in Azure e applicarle a sistemi cloud e locali per definire e implementare automaticamente la loro configurazione o ricevere report sulle deviazioni per garantire il rispetto dei criteri nelle configurazioni di sicurezza.

## <a name="azure-security-center"></a>Centro sicurezza di Azure

Il Centro sicurezza di Azure consente di proteggere le risorse di Azure. Integra il monitoraggio della sicurezza e la gestione dei criteri in tutte le sottoscrizioni di Azure. Nell'ambito del servizio, è possibile definire criteri non solo per le sottoscrizioni di Azure, ma anche per i [gruppi di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), in modo da ottenere una maggiore granularità.

![Centro sicurezza di Azure](./media/azure-threat-detection/azure-threat-detection-fig8.png)

I ricercatori Microsoft nell'ambito della sicurezza sono costantemente impegnati nella ricerca delle minacce. Hanno accesso a un ampio set di dati di telemetria acquisiti grazie alla presenza globale di Microsoft nel cloud e in locale. Questa raccolta di set di dati di vasta portata e diversificata consente a Microsoft di individuare nuovi modelli di attacco e tendenze nei propri prodotti consumer e aziendali locali, nonché nei servizi online.

Di conseguenza, il Centro sicurezza può aggiornare rapidamente gli algoritmi di rilevamento a fronte del rilascio di exploit nuovi e sofisticati da parte di utenti malintenzionati. Questo approccio consente di tenere il passo con un ambiente caratterizzato da minacce in rapida evoluzione.

![Centro sicurezza](./media/azure-threat-detection/azure-threat-detection-fig9.jpg)

Il sistema di rilevamento delle minacce del Centro sicurezza funziona mediante la raccolta automatica di informazioni sulla sicurezza dalle risorse di Azure, dalla rete e dalle soluzioni dei partner connessi.  Per identificare le minacce, analizza queste informazioni, correlando quelle raccolte da più origini.
Gli avvisi di sicurezza sono classificati in ordine di priorità nel Centro sicurezza insieme a indicazioni su come su correggere la minaccia.

Il Centro sicurezza si avvale di analisi della sicurezza avanzate, che vanno ben oltre gli approcci basati sulle firme. Le ultime innovazioni sul piano delle tecnologie per i Big Data e l'[apprendimento automatico](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) vengono usate per valutare gli eventi in tutta l'infrastruttura cloud, rilevando minacce impossibili da identificare con approcci manuali e prevedendo l'evoluzione degli attacchi. Le analisi della sicurezza includono quanto illustrato di seguito.

### <a name="threat-intelligence"></a>Intelligence per le minacce

Microsoft vanta un'enorme quantità di dati di intelligence per le minacce globali.
Il flusso di dati di telemetria proviene da più origini, ad esempio Azure, Office 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Crimes Unit (DCU) e Microsoft Security Response Center (MSRC).

![Intelligence per le minacce](./media/azure-threat-detection/azure-threat-detection-fig10.jpg)

I ricercatori ricevono anche informazioni di intelligence per le minacce condivise tra i principali provider di servizi cloud e sottoscrivono i feed di terze parti di intelligence per le minacce. Il Centro sicurezza di Azure usa queste informazioni per avvisare gli utenti nel caso di minacce provenienti da attori dannosi noti. Di seguito sono riportati alcuni esempi:

-    **Uso della potenza di Machine Learning**: il Centro sicurezza di Azure ha accesso a una grande quantità di dati sulle attività di rete nel cloud, che può usare per rilevare le minacce alle distribuzioni di Azure. Ad esempio:

-    **Rilevamento di attacchi di forza bruta**: Machine Learning viene usato per creare un modello cronologico dei tentativi di accesso remoto, che consente di rilevare gli attacchi di forza bruta contro porte SQL, RDP e SSH.

-    **Rilevamento di attacchi DDoS in uscita e Botnet**: un obiettivo comune agli attacchi alle risorse cloud è sfruttare la potenza di calcolo di queste risorse per eseguire altri attacchi.

-    **Nuovi server e macchine virtuali per l'analisi del comportamento**: se una macchina virtuale o un server è compromesso, gli utenti malintenzionati usano un'ampia gamma di tecniche per eseguire codice dannoso in tale sistema evitando il rilevamento, garantendo la persistenza e contrastando i controlli di sicurezza.

-    **Rilevamento delle minacce per il database SQL Azure**: il rilevamento delle minacce per il database di SQL Azure identifica le attività di database anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere ai database o sfruttarli.

### <a name="behavioral-analytics"></a>Analisi del comportamento

L'analisi del comportamento è una tecnica che analizza e confronta i dati con una raccolta di modelli noti. Tuttavia, questi modelli non sono semplici firme. Sono determinati usando algoritmi di Machine Learning complessi applicati a set di dati di grandi dimensioni.

![Analisi del comportamento](./media/azure-threat-detection/azure-threat-detection-fig11.jpg)


Sono anche definiti tramite l'attento esame di comportamenti dannosi da parte di analisti esperti. Il Centro sicurezza di Azure può usare le analisi del comportamento per identificare le risorse compromesse in base all'analisi dei log delle macchine virtuali, dei dispositivi di rete virtuale, dell'infrastruttura, nonché dei dump di arresto anomalo del sistema e di altre origini.

Esiste inoltre una correlazione con altri segnali per verificare la presenza di elementi a riprova di una campagna su larga scala. La correlazione consente di identificare gli eventi che risultano coerenti con gli indicatori di violazione stabiliti.

Di seguito sono riportati alcuni esempi:
-    **Esecuzione di processi sospetti**: gli utenti malintenzionati usano diverse tecniche per eseguire software dannoso senza che venga rilevato. Ad esempio, un utente malintenzionato potrebbe assegnare al malware gli stessi nomi di file di sistema legittimi, inserendo però questi file in percorsi alternativi, usare un nome molto simile a un file innocuo o mascherare la vera estensione del file. I modelli del Centro sicurezza elaborano i comportamenti e monitorano l'esecuzione dei processi per rilevare outlier come questi.

-    **Malware nascosto e tentativi di exploit**: il malware sofisticato può eludere i prodotti antimalware tradizionali, non scrivendo mai su disco o crittografando i componenti software archiviati su disco. Tuttavia, il malware può essere rilevato tramite l'analisi della memoria, perché per funzionare il malware deve lasciare tracce in memoria. Quando il software si arresta in modo anomalo, un dump di arresto anomalo acquisisce una porzione della memoria al momento dell'arresto. Analizzando la memoria nel dump di arresto anomalo, il Centro sicurezza di Azure può rilevare le tecniche usate per sfruttare le vulnerabilità del software, accedere ai dati riservati e rimanere permanentemente all'interno di un computer infetto in modo furtivo senza influire sulle relative prestazioni.

-    **Spostamento laterale e ricognizione interna**: per rimanere permanentemente all'interno di una rete compromessa e individuare/raccogliere dati importanti, gli utenti malintenzionati provano spesso a muoversi lateralmente dal computer compromesso spostandosi in altri computer all'interno della stessa rete. Il Centro sicurezza consente di monitorare le attività di elaborazione e accesso per individuare i tentativi di espansione del punto di appoggio di un utente malintenzionato all'interno della rete, ad esempio il probing della rete per individuare l'esecuzione di comandi remoti e l'enumerazione di account.

-    **Script PowerShell dannosi**: PowerShell può essere usato da utenti malintenzionati per eseguire codice dannoso in macchine virtuali di destinazione per molteplici scopi. Il Centro sicurezza ispeziona l'attività di PowerShell alla ricerca di prove di attività sospette.

-    **Attacchi in uscita**: gli utenti malintenzionati attaccano spesso le risorse cloud con l'obiettivo di usarle per organizzare altri attacchi. Le macchine virtuali compromesse, ad esempio, possono essere usate per sferrare attacchi di forza bruta contro altre macchine virtuali, inviare posta indesiderata o analizzare le porte aperte e altri dispositivi su Internet. Applicando le tecniche di apprendimento automatico al traffico di rete, il Centro sicurezza può rilevare quando le comunicazioni di rete in uscita superano la norma. Nel caso di posta indesiderata, il Centro sicurezza correla anche il traffico di posta elettronica insolito con le informazioni di Office 365 per determinare se la posta elettronica è probabilmente dannosa o il risultato di una campagna di posta elettronica legittima.

### <a name="anomaly-detection"></a>Anomaly Detection

Il Centro sicurezza di Azure usa inoltre il rilevamento anomalie per identificare le minacce. A differenza dell'analisi del comportamento, che dipende da modelli noti derivati da set di dati di grandi dimensioni, il rilevamento anomalie è più "personalizzato" e incentrato sulle baseline specifiche delle distribuzioni. Le tecniche di apprendimento automatico vengono applicate per determinare la normale attività per le distribuzioni dei clienti e quindi vengono generate regole per definire le condizioni degli outlier che possono rappresentare un evento di sicurezza. Ecco un esempio:

-    **Attacchi di forza bruta RDP/SSH in ingresso**: nelle distribuzioni dei clienti possono essere presenti macchine virtuali occupate da molti accessi ogni giorno e altre con pochi o nessun accesso. Il Centro sicurezza di Azure può determinare l'attività di accesso di base per queste macchine virtuali e usare le tecniche di apprendimento automatico per definire gli eventi attorno alle normali attività di accesso. In caso di discrepanza con la baseline definita per le caratteristiche relative all'accesso, potrebbe essere generato un avviso. Anche in questo caso, le tecniche di apprendimento automatico determinano gli eventi significativi.

### <a name="continuous-threat-intelligence-monitoring"></a>Monitoraggio continuo dell'intelligence per le minacce

Il Centro sicurezza di Azure opera insieme a team dedicati alle ricerche sulla sicurezza e all'analisi scientifica dei dati a livello mondiale che monitorano costantemente le modifiche che avvengono nel panorama delle minacce. Sono incluse le iniziative seguenti:

-    **Monitoraggio dell'intelligence per le minacce**: questo tipo di intelligence include meccanismi, indicatori, implicazioni e consigli utili sulle minacce esistenti o emergenti. Queste informazioni sono condivise nella community sulla sicurezza e Microsoft monitora costantemente i feed di intelligence per le minacce da origini interne ed esterne.

-    **Condivisione dei segnali**: le informazioni dettagliate dai team della sicurezza nell'ampio portfolio di servizi, server e dispositivi endpoint client locali e cloud di Microsoft vengono condivise e analizzate.

-    **Specialisti della sicurezza Microsoft**: in contatto costante con i team Microsoft che operano in ambiti di sicurezza specializzati, ad esempio analisi scientifiche e rilevamento di attacchi Web.

-    **Ottimizzazione del rilevamento**: gli algoritmi vengono eseguiti su set di dati reali del cliente e ricercatori dedicati alla sicurezza collaborano con i clienti per convalidare i risultati. Per perfezionare gli algoritmi di Machine Learning vengono usati veri e falsi positivi.

Questi sforzi combinati convergono in rilevamenti nuovi e migliorati, da cui è possibile trarre vantaggio immediatamente, senza che sia richiesta alcuna azione.

## <a name="advanced-threat-detection-features---other-azure-services"></a>Funzionalità di rilevamento delle minacce avanzato: altri servizi di Azure

### <a name="virtual-machine-microsoft-antimalware"></a>Macchina virtuale: Microsoft Antimalware

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) per Azure è una soluzione con un agente singolo per applicazioni e ambienti tenant, progettata per l'esecuzione in background senza intervento da parte dell'utente. È possibile distribuire la protezione in base alle esigenze dei carichi di lavoro dell'applicazione, con una configurazione sicura per impostazione predefinita o avanzata personalizzata, incluso il monitoraggio antimalware. Antimalware Azure è un'opzione di sicurezza per macchine virtuali di Azure e viene installato automaticamente in tutte le macchine virtuali PaaS di Azure.

**Funzionalità di Azure per distribuire e abilitare Microsoft Antimalware per le applicazioni**

#### <a name="microsoft-antimalware-core-features"></a>Funzionalità principali di Microsoft Antimalware

-    **Protezione in tempo reale**: monitora l'attività in Servizi cloud e Macchine virtuali per rilevare e bloccare l'esecuzione di malware.

-    **Analisi pianificata**: esegue periodicamente un'analisi mirata per rilevare il malware, inclusi i programmi in esecuzione attiva.

-    **Correzione del malware**: interviene automaticamente sul malware rilevato, ad esempio eliminando o mettendo in quarantena i file dannosi e pulendo le voci dannose del Registro di sistema.

-    **Aggiornamenti delle firme**: installa automaticamente le firme di protezione più recenti (definizioni di virus) per garantire che la protezione sia aggiornata in base a una frequenza predeterminata.

-    **Aggiornamenti del motore antimalware**: aggiorna automaticamente il motore di Microsoft Antimalware.

-    **Aggiornamenti della piattaforma antimalware**: aggiorna automaticamente la piattaforma di Microsoft Antimalware.

-    **Protezione attiva**: segnala a Microsoft Azure i metadati di telemetria sulle minacce rilevate e sulle risorse sospette per garantire una risposta rapida al mutevole panorama delle minacce, oltre ad abilitare la distribuzione di firme sincrone in tempo reale tramite Microsoft Active Protection System (MAPS).

-    **Creazione di report di esempio**: raccoglie e segnala al servizio Microsoft Antimalware esempi che consentono di perfezionare il servizio e risolvere i problemi.

-    **Esclusioni**: consente agli amministratori dell'applicazione e del servizio di configurare alcuni file, processi e unità per escluderli dalla protezione e dall'analisi per motivi di prestazioni e/o di altro tipo.

-    **Raccolta di eventi antimalware**: registra l'integrità del servizio antimalware, le attività sospette e le azioni di correzione eseguite nel registro eventi del sistema operativo e le raccoglie nell'account di archiviazione di Azure del cliente.

### <a name="azure-sql-database-threat-detection"></a>Rilevamento delle minacce per il database SQL di Azure

Il [rilevamento delle minacce per il database SQL di Azure](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) è una nuova funzionalità di intelligence sulla sicurezza incorporata nel database SQL di Azure. Il rilevamento delle minacce per il database SQL di Azure è sempre in funzione per apprendere, profilare e rilevare attività di database anomale e identificare potenziali minacce per il database.

I responsabili della sicurezza o altri amministratori designati possono ricevere una notifica immediata sulle attività di database sospette non appena si verificano. Ogni notifica contiene dettagli sulle attività sospette e consigli su come eseguire ulteriori indagini e mitigare la minaccia.

Attualmente, il rilevamento delle minacce per il database SQL di Azure rileva vulnerabilità potenziali, attacchi SQL injection e modelli anomali di accesso ai database.

Quando ricevono la notifica tramite posta elettronica della minaccia rilevata, gli utenti possono trovare e visualizzare i record di controllo pertinenti tramite il collegamento diretto nel messaggio di posta elettronica, che consente di aprire un visualizzatore di controllo e/o un modello di controllo di Excel preconfigurato che mostra i record di controllo relativi all'orario dell'evento sospetto in base a quanto segue:
-    Archiviazione di controllo per il database/server con le attività di database anomale.

-    Tabella di archiviazione di controllo pertinente usata al momento dell'evento per la scrittura del log di controllo.

-    Record di controllo dell'ora successiva all'evento.

-    Record di controllo con ID evento simile al momento dell'evento (facoltativo per alcuni rilevatori).

I rilevatori di minacce per il database SQL fanno uso di una delle metodologie di rilevamento riportate di seguito:

-    **Rilevamento deterministico:** rileva modelli sospetti (in base a regole) nelle query del client SQL corrispondenti ad attacchi noti. Questa metodologia offre prestazioni elevate di rilevamento e falsi positivi ridotti, tuttavia ha una copertura limitata perché rientra nella categoria dei "rilevamenti atomici".

-    **Rilevamento del comportamento:** rileva le attività anomale, vale a dire comportamenti anomali per il database non rilevati negli ultimi 30 giorni.  Un esempio di attività anomala per il client SQL può essere un picco di accessi non riusciti/query, l'estrazione di un volume elevato di dati, query canoniche insolite e l'uso di indirizzi IP sconosciuti per accedere al database.

### <a name="application-gateway-web-application-firewall"></a>Web application firewall del gateway applicazione

[Web application firewall](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall) è una funzionalità del [gateway applicazione di Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-webapplicationfirewall-overview) che consente di proteggere le applicazioni Web che usano il gateway applicazione per funzioni standard di [controller per la distribuzione di applicazioni](https://kemptechnologies.com/in/application-delivery-controllers). Web application firewall protegge infatti le applicazioni dalla maggior parte delle [10 vulnerabilità Web OWASP più diffuse](https://www.owasp.org/index.php/Top_10_2010-Main).

![Web application firewall del gateway applicazione](./media/azure-threat-detection/azure-threat-detection-fig13.png)

-    Protezione dagli attacchi SQL injection

-    Protezione dagli attacchi di scripting intersito

-    Protezione dai comuni attacchi Web, ad esempio attacchi di iniezione di comandi, richieste HTTP non valide, attacchi HTTP Response Splitting e Remote File Inclusion

-    Protezione dalle violazioni del protocollo HTTP

-    Protezione contro eventuali anomalie del protocollo HTTP, ad esempio user agent host mancante e accept header

-    Prevenzione contro robot, crawler e scanner

-    Rilevamento di errori di configurazione dell'applicazione comuni (ad esempio, Apache, IIS e così via)

La configurazione del WAF nel gateway applicazione offre i vantaggi seguenti:

-    Protezione dell'applicazione Web dalle vulnerabilità e dagli attacchi del Web, senza alcuna modifica al codice di back-end.

-    Protezione contemporanea di più applicazioni Web con un gateway applicazione. Il gateway applicazione può ospitare e proteggere dagli attacchi Web fino a 20 siti Web su un singolo gateway.

-    Monitoraggio dell'applicazione Web contro gli attacchi tramite report in tempo reale generati dai log del WAF del gateway applicazione.

-    Alcuni controlli di conformità richiedono che tutti gli endpoint comunicanti con Internet siano protetti da una soluzione WAF. Grazie al gateway applicazione con WAF abilitato è possibile soddisfare questi requisiti di conformità.

### <a name="anomaly-detection--an-api-built-with-azure-machine-learning"></a>API di rilevamento delle anomalie integrata in Azure Machine Learning

L'API di rilevamento delle anomalie è integrata in Azure Machine Learning e permette di rilevare i diversi tipi di modelli anomali nei dati delle serie temporali. L'API assegna un punteggio di anomalia a ogni punto dati della serie temporale, che può essere usato per la creazione di avvisi, il monitoraggio tramite dashboard o la connessione con i sistemi di generazione dei ticket.

L'[API di rilevamento delle anomalie](https://docs.microsoft.com/azure/machine-learning/machine-learning-apps-anomaly-detection-api) può rilevare i seguenti tipi di anomalie nei dati delle serie temporali:

-    **Picchi e flessioni**: ad esempio, quando si monitora il numero di errori di accesso a un servizio o il numero di transazioni completate in un sito di e-commerce, i picchi o le flessioni possono indicare attacchi alla sicurezza o interruzioni del servizio.

-    **Tendenze positive e negative:** durante il monitoraggio dell'utilizzo della memoria per l'elaborazione, ad esempio, una riduzione delle dimensioni della memoria disponibile può indicare una possibile perdita di memoria. Durante il monitoraggio della lunghezza della coda del servizio, una tendenza persistente verso l'alto può indicare un problema software sottostante.

-    **Cambi di livello e le modifiche all'intervallo dinamico dei valori:** i cambi di livello nelle latenze di un servizio dopo un aggiornamento o livelli ridotti di eccezioni dopo un aggiornamento possono essere interessanti da monitorare.

L'API basata sull'apprendimento automatico offre:

-    **Rilevamento flessibile e affidabile:** i modelli di rilevamento delle anomalie consentono agli utenti di configurare le impostazioni di sensibilità e rilevare anomalie tra set di dati stagionali e non stagionali. Gli utenti possono modificare il modello di rilevamento delle anomalie per rendere l'API più o meno sensibile in base alle esigenze. Ciò significa poter rilevare le anomalie più o meno visibili nei dati con e senza modelli stagionali.

-    **Rilevamento ridimensionabile e tempestivo:** il monitoraggio tradizionale con soglie impostate in base alle competenze degli esperti è costosi e non offre scalabilità per milioni di set di dati che cambiano continuamente. I modelli di rilevamento delle anomalie in questa API vengono acquisiti e i modelli vengono ottimizzati automaticamente in base a dati in tempo reale e cronologici.

-    **Rilevamento proattivo ed eseguibile:** è possibile applicare il rilevamento di tendenze lente e cambi di livello per rilevare in anticipo le anomalie. Il rilevamento dei primi segni di anomalia permette di indirizzare gli utenti verso l'analisi e la risoluzione delle aree problematiche.  Inoltre, è possibile sviluppare modelli di analisi della causa radice e gli strumenti di avviso sull'API di rilevamento delle anomalie.

L'API rappresenta una soluzione efficace ed efficiente per un'ampia gamma di scenari, come il monitoraggio dell'integrità del servizio e dei KPI, l'IoT, il monitoraggio delle prestazioni e del traffico di rete. Di seguito sono riportati alcuni scenari comuni in cui questa API può risultare utile:
- Reparti IT che hanno bisogno di strumenti per tenere traccia di eventi, codici di errore, log di utilizzo e prestazioni (CPU, memoria e così via) in modo tempestivo.

-    Siti di e-commerce che vogliono tenere traccia delle attività dei clienti, delle visualizzazioni di pagine dei clic e così via.

-    Aziende di pubblici servizi che vogliono tenere traccia dei consumi di acqua, gas, elettricità e altre risorse.

-    Servizi di gestione di strutture/edifici che vogliono monitorare temperatura, umidità, traffico e così via.

-    Produttori/IoT che vogliono usare i dati dei sensori nelle serie temporali per monitorare il flusso di lavoro, la qualità e così via.

-    Provider di servizi, ad esempio call center, che vogliono monitorare le tendenze della domanda di servizi, il volume di eventi imprevisti, la lunghezza delle code di attesa e così via.

-    Gruppi di analisi business che vogliono monitorare in tempo reale i movimenti anomali dei KPI aziendali, ad esempio il volume delle vendite, il sentiment dei clienti o i prezzi.

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) è un componente fondamentale dello stack di sicurezza di Microsoft Cloud. Si tratta di una soluzione completa che consente all'organizzazione di sfruttare appieno il potenziale delle applicazioni cloud, mantenendo il controllo grazie a una maggiore visibilità nelle attività. Consente inoltre di migliorare la protezione dei dati critici nelle applicazioni cloud.

Grazie a strumenti che permettono di scoprire shadow IT, valutare i rischi, applicare i criteri, analizzare le attività e arrestare le minacce, l'organizzazione può passare al cloud in piena sicurezza mantenendo il controllo dei dati critici.

<table style="width:100%">
 <tr>
   <td>Scoprire</td>
   <td>Scoprire shadow IT con Cloud App Security. Ottenere visibilità tramite l'identificazione di app, attività, utenti, dati e file nell'ambiente cloud. Trovare app di terze parti connesse al cloud.</td>
 </tr>
 <tr>
   <td>Analisi dei problemi</td>
   <td>Analizzare i problemi delle app cloud tramite strumenti forensi di approfondimento in app rischiose, utenti specifici e file nella rete. Trovare i modelli nei dati raccolti dal cloud. Generare report per monitorare il cloud.</td>

 </tr>
 <tr>
   <td>Controllo</td>
   <td>Mitigare il rischio tramite l'impostazione di criteri e avvisi per ottenere il massimo controllo sul traffico di rete nel cloud. Usare Cloud App Security per eseguire la migrazione degli utenti verso app cloud alternative sicure e approvate.</td>

 </tr>
 <tr>
   <td>Proteggere</td>
   <td>Usare Cloud App Security per approvare o vietare applicazioni, applicare misure di prevenzione contro la perdita di dati, controllare autorizzazioni e condivisioni e generare avvisi e report personalizzati.</td>

 </tr>
 <tr>
   <td>Controllo</td>
   <td>Mitigare il rischio tramite l'impostazione di criteri e avvisi per ottenere il massimo controllo sul traffico di rete nel cloud. Usare Cloud App Security per eseguire la migrazione degli utenti verso app cloud alternative sicure e approvate.</td>

 </tr>
</table>


![Cloud App Security](./media/azure-threat-detection/azure-threat-detection-fig14.png)

Cloud App Security integra la visibilità con il cloud tramite:

-    Uso di Cloud Discovery per eseguire il mapping e identificare l'ambiente cloud e le app cloud usate dall'organizzazione.


-    Approvazione e divieto di app nel cloud.



-    Uso di connettori app facili da distribuire che si avvalgono di API del provider per ottenere visibilità e governance delle app a cui ci si connette.

-    Controllo continuo grazie all'impostazione e all'ottimizzazione costante di criteri.

Durante la raccolta di dati da queste origini, Cloud App Security esegue analisi complesse sui dati. Segnala immediatamente le attività anomale e offre una visibilità dettagliata nell'ambiente cloud. È possibile configurare criteri in Cloud App Security e usarli per proteggere tutti i dati nell'ambiente cloud.

## <a name="third-party-atd-capabilities-through-azure-marketplace"></a>Funzionalità ATD di terze parti tramite Azure Marketplace

### <a name="web-application-firewall"></a>Web application firewall

Web application firewall controlla il traffico Web in ingresso e blocca SQL injection, attacchi tramite script da altri siti, caricamenti di malware, DDoS di applicazioni e altri attacchi destinati alle applicazioni Web. Esamina anche le risposte provenienti dai server Web back-end per la prevenzione della perdita dei dati. Il motore di controllo di accesso integrato consente agli amministratori di creare criteri di controllo di accesso granulari per l'autenticazione, l'autorizzazione e la contabilità e garantisce alle organizzazioni un'autenticazione e un controllo utente affidabili.

**In evidenza:**
-    Rileva e blocca SQL injection, attacchi tramite script da altri siti, caricamenti di malware, DDoS di applicazioni e altri attacchi destinati alle applicazioni.

-    Offre autenticazione e controllo di accesso.

-    Analizza il traffico in uscita per rilevare i dati sensibili e può mascherare o bloccare la divulgazione di informazioni.

-    Accelera la distribuzione dei contenuti delle applicazioni Web con funzionalità quali la memorizzazione nella cache, la compressione e altre funzioni di ottimizzazione del traffico.

Di seguito sono riportati alcuni esempi di Web application firewall disponibili in Azure Marketplace:

[Barracuda Web Application Firewall, Brocade Virtual Web Application Firewall (Brocade vWAF), Imperva SecureSphere e The ThreatSTOP IP Firewall.](https://azure.microsoft.com/marketplace/partners/brocade_communications/brocade-virtual-web-application-firewall-templatevtmcluster/)

## <a name="next-steps"></a>Passaggi successivi

- [Funzionalità di rilevamento del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities)

Le funzionalità di rilevamento avanzate del Centro sicurezza di Azure consentono di identificare le minacce attive rivolte alle risorse di Microsoft Azure e forniscono le informazioni dettagliate necessarie per rispondere rapidamente a tali minacce.

- [Rilevamento delle minacce per il database SQL di Azure](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/)

Il rilevamento delle minacce per il database SQL di Azure consente di risolvere i problemi relativi a potenziali minacce per i database.

