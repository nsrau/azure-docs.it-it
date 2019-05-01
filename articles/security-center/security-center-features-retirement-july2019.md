---
title: Ritiro del Centro sicurezza features (luglio 2019) | Microsoft Docs
description: Questo articolo illustra le funzionalità nel Centro sicurezza che verrà ritirato il 31 luglio 2019.
services: security-center
author: yoavfrancis
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.date: 4/16/2019
ms.author: yoafr
ms.openlocfilehash: 392782310d8bc3b38a3dd1349cb1760ca287acd1
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64875595"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Ritiro della funzionalità del Centro sicurezza (luglio 2019)

Sono stati apportati numerosi [miglioramenti](https://azure.microsoft.com/updates/?product=security-center) al Centro sicurezza di Azure negli ultimi 6 mesi.  
Con le capacità ottimizzate un numero di funzionalità di ridondanza, nonché API correlate, verranno rimosse dal Centro sicurezza 31 luglio 2019.  

La maggior parte delle funzionalità ritirata possono essere sostituita con le nuove funzionalità nel Centro sicurezza di Azure o Log Analitica; e alcune funzionalità può essere implementata usando [Sentinel di Azure (anteprima)](https://azure.microsoft.com/services/azure-sentinel/), annunciato di recente.

L'elenco delle funzionalità in fase di ritiro dal Centro sicurezza include:

- [Dashboard eventi](#menu_events)
- [Voce di menu Cerca](#menu_search)
- [Visualizzazione classica identità e accesso collegamento identità e accesso (anteprima)](#menu_classicidentity)
- [Gli eventi di sicurezza di eseguire il mapping come pulsante sulla mappa degli avvisi di sicurezza (anteprima)](#menu_securityeventsmap)
- [Regole di avviso personalizzate (anteprima)](#menu_customalerts)
- [Provare a utilizzare pulsante negli avvisi di sicurezza di threat protection](#menu_investigate)
- [Un sottoinsieme di soluzioni di sicurezza](#menu_solutions)
- [Modificare le configurazioni di sicurezza per i criteri di sicurezza](#menu_securityconfigurations)
- [Sicurezza e controllo dashboard (originariamente usato nel portale di OMS) per le aree di lavoro di Log Analitica.](#menu_securityomsdashboard)

Di seguito vengono fornite informazioni dettagliate per ogni funzionalità ritirato e i passaggi che possono essere utilizzate le funzionalità di sostituzione.

## Dashboard eventi<a name="menu_events"></a>
Il Centro sicurezza usa Microsoft Monitoring Agent per raccogliere diverse configurazione ed eventi correlati alla sicurezza dai computer e archivia questi eventi nelle aree di lavoro dell'utente. Il [dashboard eventi](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) consente di visualizzare i dati e fornisce essenzialmente un altro punto di ingresso per Log Analitica.

Verrà ritirato il dashboard degli eventi in futuro:

![Schermata di selezione dell'area di lavoro eventi][1]

Verranno anche ritirate il dashboard degli eventi che viene visualizzato una volta che un utente fa clic su un'area di lavoro:

![Dashboard Eventi][2]

### <a name="events-dashboard---new-experience"></a>Dashboard degli eventi - nuova esperienza

I clienti sono invitati a usare funzionalità native dei Log Analitica per visualizzare gli eventi significativi nel loro aree di lavoro.
Se è già stato creato personalizzati eventi rilevanti dal Centro sicurezza, questi saranno accessibili tramite Log analitica -> selezionare dell'area di lavoro -> ricerche salvate. I dati non essere perso né modificati. Eventi rilevanti nativi sono anche disponibili dalla schermata stessa.

![Le ricerche salvata dell'area di lavoro][3]

## Voce di menu Cerca<a name="menu_search"></a>
Centro sicurezza di Azure Usa attualmente la ricerca nei log di monitoraggio di Azure per recuperare e analizzare i dati di sicurezza. Questa schermata funge essenzialmente da interfaccia ai Log Analitica "[ricerca](https://docs.microsoft.com/azure/security-center/security-center-search)" pagina – consentendo agli utenti di eseguire query di ricerca nell'area di lavoro selezionato. Questa finestra di ricerca in futuro verrà ritirata:

![Pagina di ricerca][4]

### <a name="search-menu-entry---new-experience"></a>Voce di menu - nuova esperienza di ricerca

I clienti sono invitati a usare **Log Analitica** funzionalità native per eseguire la query di ricerca nelle loro aree di lavoro. A tale scopo, è possibile passare a analitica di Log in Azure e selezionare: "Logs":

![Pagina dei log di log Analitica][5]

## Classico identità e accesso (anteprima)<a name="menu_classicidentity"></a>
L'esperienza di identità e accesso "Classico" nel Centro sicurezza ha fornito un modo per i clienti per visualizzare la propria identità e accedere alle informazioni correlate di analitica di log. Tale operazione viene eseguita seguendo i clic di seguito:

Fare clic su "Visualizzazione classica Identity & Access"

![Pagina di identità][6]

Assieme allo schermo sarebbe aprire questa pagina, "dashboard identità e accesso":

![Pagina di identità - selezione dell'area di lavoro][7]

Un clic nell'area di lavoro viene aperto il "Identity and access" log analitica dashboard in cui i clienti possono visualizzare identità e accedere alle informazioni nell'area di lavoro:

![Pagina di identità - dashboard][8]

In futuro tutte le tre schermate precedenti verrà ritirata. I dati rimarranno disponibili nella soluzione di protezione log analitica e non verrà modificati o rimosso.

### <a name="classic-identity--access-preview---new-experience"></a>Classico identità e accesso (anteprima) - nuova esperienza
Mentre il dashboard di Log analitica ha fornito informazioni dettagliate su una determinata area di lavoro, le funzionalità native di Centro sicurezza forniscono visibilità in tutte le sottoscrizioni e tutte le aree di lavoro associati, in un modo facile-utilizzare la visualizzazione che ti permette di concentrarti su ciò che del importante, in base al punteggio di raccomandazioni di identità e accesso sicuro.
Tutte le funzionalità delle identità e accesso del dashboard di Log analitica possono essere ottenute selezionando "Identità e accesso (anteprima)" all'interno di Centro sicurezza:

![Pagina di identità - classica esperienza di ritiro][9]

## Eseguire il mapping degli eventi di protezione<a name="menu_securityeventsmap"></a>
Il Centro sicurezza offre una [mappa](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) che consente di identificare le minacce alla sicurezza per l'ambiente. Il pulsante 'Vai a mappa eventi di sicurezza' in questa mappa conduce a un dashboard che consente di visualizzare gli eventi di sicurezza non elaborati nell'area di lavoro selezionato.
Il pulsante, con il dashboard per ogni area di lavoro, verrà rimossi dopo la deprecazione.

![Pulsante mappa - agli avvisi di sicurezza][10]

Quando si fa clic su "Vai alla mappa di eventi di sicurezza" oggi è aperto dashboard intelligence per le minacce. Verrà ritirato il dashboard intelligence per le minacce.  

![Dashboard Intelligence per le minacce][11]

Quando si sceglie un'area di lavoro per visualizzare il rispettivo dashboard intelligence delle minacce, la sicurezza degli avvisi schermata map(Preview) *in Log Analitica* viene aperto. Questa schermata verrà ritirata.

![Mappa di avvisi di sicurezza nel Log Analitica][12]

I dati esistenti rimarranno disponibili nella soluzione di protezione log analitica e verranno non è possibile modificare o rimuovere.

### <a name="security-events-map---new-experience"></a>Mappa eventi di sicurezza - nuova esperienza
E invita i clienti di utilizzare la funzionalità di mappa avvisi incorporata in Centro sicurezza: "avvisi di sicurezza eseguire il mapping (anteprima)". Ciò offre un'esperienza ottimizzata e funziona su tutte le sottoscrizioni e aree di lavoro associati, consentendo una macro Visualizza nel tuo ambiente e non incentrata su una singola area di lavoro.

## Regole di avviso personalizzate (anteprima)<a name="menu_customalerts"></a>
L'esperienza avvisi personalizzati saranno [ritirato](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) il 30 giugno 2019, a causa di ritiro dell'infrastruttura sottostante si basa su. Nell'intervallo di tempo fino a elementi deprecati, gli utenti saranno in grado di modificare le regole di avviso personalizzate esistenti ma non sarà in grado di aggiungere quelli nuovi. Gli utenti è consigliabile abilitare [Sentinel Azure](https://azure.microsoft.com/services/azure-sentinel/) con onboarding di un solo clic per eseguire la migrazione degli avvisi esistenti e crearne uno nuovo o in alternativa creare nuovamente gli avvisi di monitoraggio di Azure automaticamente gli avvisi del log.

Per mantenere gli avvisi esistenti e ne esegue la migrazione a Azure Sentinel, avviare Azure Sentinel. Come primo passaggio, selezionare l'area di lavoro in cui sono archiviati gli avvisi personalizzati e quindi selezionare la voce di menu 'Analitica' per eseguire la migrazione automatica degli avvisi.

![Avvisi personalizzati][13]

I clienti non interessati all'onboarding per Azure Sentinel sono invitati a creare nuovamente gli avvisi con avvisi di log di monitoraggio di Azure. Per istruzioni, vedere: [crea, visualizzare e gestire gli avvisi del log tramite Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log). Per istruzioni su come creare avvisi del log, vedere: [Gli avvisi del log in Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

Per altre informazioni sul ritiro di avvisi personalizzati, vedere la [documentazione di avvisi del centro di sicurezza personalizzato](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## Analisi degli avvisi di sicurezza<a name="menu_investigate"></a>
[La funzionalità indagine](https://docs.microsoft.com/azure/security-center/security-center-investigation) nel Centro sicurezza consente di valutare, comprendere l'ambito e individuare la causa radice di un evento imprevisto della sicurezza potenziale. Questa funzionalità verrà rimossa dal Centro sicurezza di come è stato sostituito con un'esperienza migliorata in [Sentinel Azure](https://azure.microsoft.com/services/azure-sentinel/).

![Evento imprevisto della sicurezza][14]

Quando si fa clic sul pulsante "Analisi", "analisi (anteprima)" nel Log Analitica viene aperto il Dashboard. Verrà ritirato il Dashboard corrispondente.  
I dati esistenti rimarranno disponibili nella soluzione di protezione Log Analitica e verranno non è possibile modificare o rimuovere.

![Dashboard indagine nel Log Analitica][15]

### <a name="investigation---new-experience"></a>Analisi - nuova esperienza

E invita i clienti per eseguire l'onboarding [Sentinel Azure](https://azure.microsoft.com/services/azure-sentinel/) per un'esperienza di analisi avanzata, con tecnologia gli avvisi di funzionalità di ricerca. Sentinel Azure fornisce strumenti di ricerca e query potenti caccia a minacce per la sicurezza tra le origini dati dell'organizzazione.  

## Subset di soluzioni di sicurezza<a name="menu_solutions"></a>

Il Centro sicurezza offre la possibilità di abilitare [soluzioni di sicurezza in Azure integrate](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). Le soluzioni partner seguenti potrebbero essere rimosso e supportate in [Sentinel Azure](https://azure.microsoft.com/services/azure-sentinel/), insieme a anche a più origini dati.

- Nuova generazione del firewall e soluzioni Firewall applicazione Web (Azure Sentinel [documentazione](https://docs.microsoft.com/azure/sentinel/connect-data-sources))
- Integrazione di soluzioni di sicurezza che supportano il formato di evento comuni (CEF) (Azure Sentinel [documentazione](https://docs.microsoft.com/azure/sentinel/connect-common-event-format))
- Microsoft Advanced Threat Analitica (Azure Sentinel [documentazione](https://docs.microsoft.com/azure/sentinel/connect-azure-atp))
- Azure AD Identity Protection (Azure Sentinel [documentazione](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection))

In seguito la deprecazione, gli utenti non saranno in grado di aggiungere o modificare esistente soluzioni connesse dei tipi indicati in precedenza, l'interfaccia utente sia l'API.
Le soluzioni connesse esistenti sono invitate a spostare in Azure Sentinel entro la fine di questo periodo.

![Centri sicurezza soluzioni][16]

## Modificare le configurazioni di sicurezza per i criteri di sicurezza<a name="menu_securityconfigurations"></a>
Il Centro sicurezza di Azure monitora le configurazioni di sicurezza applicando un set di [più di 150 regole consigliate](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) per la protezione avanzata del sistema operativo, incluse regole relative a firewall, controllo, criteri di gestione delle password e altro ancora. Se in un computer viene trovata una configurazione vulnerabile, il Centro sicurezza genera una raccomandazione di sicurezza. Il [schermata di configurazione della protezione Modifica](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) consente ai clienti di personalizzare la configurazione di sicurezza predefinita del sistema operativo nel Centro sicurezza.

Questa funzionalità è stata disponibile in anteprima e verrà ritirata.

![Modifica le configurazioni di sicurezza][17]

### <a name="edit-security-configurations---new-experience"></a>Modificare le configurazioni di sicurezza - nuova esperienza

Il Centro sicurezza supporterà il [agente in guest](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) nel prossimo futuro, consentendo un set di funzionalità molto più ampio, incluso il supporto per l'integrazione con la configurazione di guest con i criteri di Azure (nel guest e altri sistemi operativi criteri). Ciò fornirà anche la possibilità di controllare su larga scala e applicare automaticamente le nuove risorse.

## Sicurezza e controllo dashboard (originariamente usato nel portale di OMS) per le aree di lavoro di Log Analitica<a name="menu_securityomsdashboard"></a>

Il dashboard di sicurezza in analitica Log fornisce una panoramica per ogni area di lavoro di eventi di sicurezza rilevanti e minacce, una mappa di intelligence delle minacce e della valutazione di identità e accesso degli eventi di sicurezza salvato nell'area di lavoro. Il dashboard verrà rimossa in futuro. Come abbiamo già consigliato nel dashboard dell'interfaccia utente, gli utenti è consigliabile usare il Centro sicurezza di Azure in futuro.

![Dashboard di sicurezza di log analitica][18]

### <a name="security--audit-dashboard---new-experience"></a>Dashboard del Centro sicurezza e controllo: nuova esperienza
I clienti è consigliabile usare il Centro sicurezza di Azure, che fornisce la stessa Panoramica della sicurezza in più sottoscrizioni e aree di lavoro associati, insieme a un insieme più ricco di funzionalità.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sul [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/)
- Altre informazioni su [Sentinel di Azure](https://docs.microsoft.com/azure/sentinel)

<!--Image references - events-->
[1]: ./media/security-center-features-retirement-july2019/asc_events_dashboard.png
[2]: ./media/security-center-features-retirement-july2019/asc_events_dashboard_inner.png
[3]: ./media/security-center-features-retirement-july2019/workspace_saved_searches.png
<!--Image references - search-->
[4]: ./media/security-center-features-retirement-july2019/asc_search.png
[5]: ./media/security-center-features-retirement-july2019/workspace_logs.png
<!--Image references - classic identity and access-->
[6]: ./media/security-center-features-retirement-july2019/asc_identity.png
[7]: ./media/security-center-features-retirement-july2019/asc_identity_workspace_selection.png
[8]: ./media/security-center-features-retirement-july2019/loganalytics_dashboard_identity.png
[9]: ./media/security-center-features-retirement-july2019/asc_identity_nobuttonhighlight.png
<!--Image references - alerts map-->
[10]: ./media/security-center-features-retirement-july2019/asc_security_alerts_map.png
[11]: ./media/security-center-features-retirement-july2019/asc_threat_intellignece_dashboard.png
[12]: ./media/security-center-features-retirement-july2019/loganalytics_security_alerts_map.png
<!--Image references - custom alerts-->
[13]: ./media/security-center-features-retirement-july2019/asc_custom_alerts.png
<!--Image references - Investigation-->
[14]: ./media/security-center-features-retirement-july2019/asc-security-incident.png
[15]: ./media/security-center-features-retirement-july2019/loganalytics_investigation_dashboard.png
<!--Image references - Solutions-->
[16]: ./media/security-center-features-retirement-july2019/asc_security_solutions.png
<!--Image references - Edit security configurations-->
[17]: ./media/security-center-features-retirement-july2019/asc_edit_security_configurations.png
<!--Image references - Security dashboard in log analytics-->
[18]: ./media/security-center-features-retirement-july2019/loganalytics_security_dashboard.png
