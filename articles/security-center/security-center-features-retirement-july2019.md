---
title: Ritiro delle funzionalità del Centro sicurezza (luglio 2019) | Microsoft Docs
description: Questo articolo descrive le funzionalità del Centro sicurezza ritirate il 31 luglio 2019.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: article
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: c4fe185f5d203b1b0eba306c80cded14ad82bc98
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72438624"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Ritiro delle funzionalità del Centro sicurezza (luglio 2019)

> [!NOTE]
> Questo documento illustra in dettaglio l'elenco delle funzionalità che sono state ritirate dal centro sicurezza di Azure il 31 luglio 2019.
>
>

Sono stati apportati diversi [miglioramenti](https://azure.microsoft.com/updates/?product=security-center) al centro sicurezza di Azure nel corso dei sei mesi fino al 2019 luglio.
Con queste funzionalità ottimizzate, abbiamo rimosso alcune funzionalità ridondanti e le API correlate dal centro sicurezza il 31 luglio 2019.

La maggior parte di queste funzionalità ritirate può essere sostituita con altre funzionalità del Centro sicurezza di Azure o di Azure Log Analytics. Altre funzionalità possono essere implementate con [Azure Sentinel (Preview)](https://azure.microsoft.com/services/azure-sentinel/).

Le funzionalità del Centro sicurezza ritirate includono:

- [Dashboard eventi](#menu_events)
- [Voce di menu Cerca](#menu_search)
- [Visualizza il collegamento di accesso & di identità classico a identità e accesso (anteprima)](#menu_classicidentity)
- [Pulsante mappa eventi di sicurezza sulla mappa avvisi di sicurezza (anteprima)](#menu_securityeventsmap)
- [Regole di avviso personalizzate (anteprima)](#menu_customalerts)
- [Pulsante Analizza negli avvisi di sicurezza di Threat Protection](#menu_investigate)
- [Un subset di soluzioni di sicurezza](#menu_solutions)
- [Modificare le configurazioni di sicurezza per i criteri di sicurezza](#menu_securityconfigurations)
- [Dashboard sicurezza e controllo (originariamente usato nel portale di OMS) per aree di lavoro Log Analytics](#menu_securityomsdashboard)

Questo articolo fornisce informazioni dettagliate per ogni funzionalità ritirata e i passaggi da eseguire per implementare le funzionalità di sostituzione.

## Dashboard eventi<a name="menu_events"></a>

Il Centro sicurezza USA Microsoft Monitoring Agent per raccogliere diverse configurazioni ed eventi correlati alla sicurezza dai computer. Questi eventi vengono archiviati nelle aree di lavoro. Il [dashboard degli eventi](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) consente di visualizzare i dati e fornisce un punto di ingresso per log Analytics.

Il dashboard degli eventi è stato ritirato quando è stata selezionata un'area di lavoro:

![Dashboard eventi][2]

### <a name="events-dashboard---the-new-experience"></a>Dashboard eventi: nuova esperienza

Si consiglia di usare le funzionalità native di Azure Log Analytics per visualizzare gli eventi rilevanti nelle aree di lavoro.

Se sono stati creati eventi rilevanti personalizzati nel centro sicurezza, saranno accessibili. In Log Analytics passare a **Seleziona area di lavoro** > **ricerche salvate**. I dati non verranno persi o modificati. Gli eventi rilevanti nativi sono disponibili anche nella stessa schermata in Log Analytics.

![Ricerche salvate nell'area di lavoro][3]

## Voce di menu Cerca<a name="menu_search"></a>

Il Centro sicurezza di Azure usa attualmente la ricerca nei log di monitoraggio di Azure per recuperare e analizzare i dati di sicurezza. Questa schermata funge da finestra per Log Analytics pagina di ricerca e consente agli utenti di eseguire query di ricerca nell'area di lavoro selezionata. Per altre informazioni, vedere [ricerca nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-search). Questa finestra di ricerca è stata ritirata:

![Pagina di ricerca][4]

### <a name="search-menu-entry---the-new-experience"></a>Voce di menu Cerca: nuova esperienza

Si consiglia di usare le funzionalità native di Azure Log Analytics per eseguire query di ricerca nelle aree di lavoro. Passare ad Azure Log Analytics e selezionare **log**.

![Pagina log Log Analytics][5]

## Accesso all'identità classica & (anteprima)<a name="menu_classicidentity"></a>

L'esperienza di accesso & di identità classica nel centro sicurezza attualmente Mostra un dashboard di identità e informazioni di accesso in Log Analytics. Per visualizzare il Dashboard:

1. Selezionare **Visualizza identità classica & accesso**.

   ![Pagina identità][6]

1. Visualizzare il **Dashboard identità & Access**.

    ![Pagina identità-selezione area di lavoro][7]

1. Selezionare un'area di lavoro per aprire il dashboard **identità & accedi** in log Analytics per visualizzare le informazioni sull'identità e l'accesso nell'area di lavoro.

   ![Pagina identità-dashboard][8]

Sono state ritirate tutte e tre le schermate illustrate nei passaggi precedenti. I dati rimangono disponibili nella soluzione Log Analytics sicurezza e non sono stati modificati o rimossi.

### <a name="classic-identity--access-preview---the-new-experience"></a>Accesso all'identità classica & (anteprima): nuova esperienza

Il dashboard Log Analytics ha mostrato informazioni dettagliate su un'unica area di lavoro. Tuttavia, le funzionalità del Centro sicurezza nativo forniscono visibilità in tutte le sottoscrizioni e in tutte le aree di lavoro associate. È possibile accedere a una visualizzazione di facile utilizzo che consente di concentrarsi su ciò che è importante con le raccomandazioni classificate in base al Punteggio sicuro.

Tutte le funzionalità del dashboard **identity & Access** in log Analytics possono essere raggiunte selezionando **Identity & Access (Preview)** nel centro sicurezza.

![Pagina identità-ritiro dall'esperienza classica][9]

## Mappa eventi di sicurezza<a name="menu_securityeventsmap"></a>

Il Centro sicurezza fornisce una [Mappa degli avvisi di sicurezza](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) per identificare le minacce alla sicurezza. Il pulsante **Vai al mapping degli eventi di sicurezza** in tale mappa apre un dashboard che consente di visualizzare gli eventi di sicurezza non elaborati nell'area di lavoro selezionata.

È stato rimosso il pulsante **Vai al mapping eventi di sicurezza** e il dashboard per area di lavoro.

![Mappa avvisi di sicurezza-pulsante][10]

Quando si seleziona il pulsante **Vai al mapping degli eventi di sicurezza** , viene aperto il dashboard di intelligence per le minacce (ora ritirato).

![Dashboard Intelligence per le minacce][11]

Quando si sceglie un'area di lavoro per visualizzare il dashboard di intelligence per le minacce, è stata aperta la schermata (ora ritirata) della mappa degli avvisi di sicurezza (anteprima) in Log Analytics.

![Mappa degli avvisi di sicurezza in Log Analytics][12]

I dati esistenti rimangono disponibili nella soluzione Log Analytics sicurezza e non sono stati modificati o rimossi.

### <a name="security-events-map---the-new-experience"></a>Mappa degli eventi di sicurezza: nuova esperienza

Si consiglia di usare la funzionalità mappa avvisi integrata nel centro sicurezza: **mappa avvisi di sicurezza (anteprima)** . Questa funzionalità offre un'esperienza ottimizzata e funziona in tutte le sottoscrizioni e le aree di lavoro associate. Offre una visualizzazione di alto livello nell'ambiente e non si concentra su un'unica area di lavoro.

## Regole di avviso personalizzate (anteprima)<a name="menu_customalerts"></a>

[L'esperienza di avvisi personalizzati](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) è stata ritirata il 30 giugno 2019 perché l'infrastruttura sottostante è stata ritirata. Dopo la data di ritiro, non vengono più generati avvisi di sicurezza personalizzati.
Si consiglia di abilitare [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) e di ricreare gli avvisi personalizzati in tale posizione. In alternativa, è possibile creare gli avvisi con gli avvisi del log di monitoraggio di Azure.

Per creare avvisi personalizzati con Azure Sentinel:

1. [Aprire Sentinel di Azure](https://portal.azure.com/#create/Microsoft.ASI/preview) e selezionare l'area di lavoro in cui sono archiviati gli avvisi personalizzati
1. Selezionare **Analytics** dal menu
1. Seguire le istruzioni riportate nell' [esercitazione](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) seguente su come creare avvisi personalizzati in Sentinel di Azure

Se non si è interessati all'uso di Sentinel di Azure, è possibile creare gli avvisi con gli avvisi del log di monitoraggio di Azure. Per istruzioni, vedere [creare, visualizzare e gestire gli avvisi del log usando monitoraggio di Azure e gli](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) [avvisi del log in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

![Avvisi personalizzati][13]

Per altre informazioni sul ritiro degli avvisi personalizzati, vedere [regole di avviso personalizzate nel centro sicurezza di Azure (anteprima)](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## Analisi degli avvisi di sicurezza<a name="menu_investigate"></a>

[La funzionalità di indagine](https://docs.microsoft.com/azure/security-center/security-center-investigation) nel centro sicurezza consente di valutare un potenziale evento imprevisto di sicurezza. La funzionalità consente di comprendere l'ambito di un evento imprevisto e di individuare la causa radice. Questa funzionalità è stata rimossa dal centro sicurezza perché è stata sostituita con un'esperienza migliorata in [Sentinel di Azure](https://azure.microsoft.com/services/azure-sentinel/).

![Evento imprevisto della sicurezza][14]

Quando si seleziona il pulsante **analizza** da una schermata **evento imprevisto di sicurezza** , si apre il dashboard di analisi (anteprima) in log Analytics. Il dashboard di analisi è stato ritirato.

I dati esistenti rimangono disponibili nella soluzione Log Analytics sicurezza e non sono stati modificati o rimossi.

![Dashboard di analisi in Log Analytics][15]

### <a name="investigation---the-new-experience"></a>Analisi: nuova esperienza

Si consiglia di passare ad [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) per un'esperienza di analisi avanzata. Azure Sentinel fornisce potenti strumenti di ricerca e query per cercare le minacce alla sicurezza nelle origini dati dell'organizzazione.

## Subset di soluzioni di sicurezza<a name="menu_solutions"></a>

Il Centro sicurezza può abilitare [soluzioni di sicurezza integrate in Azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). Sono state ritirate le soluzioni dei partner seguenti dal centro sicurezza. Queste soluzioni sono abilitate in [Sentinel di Azure](https://azure.microsoft.com/services/azure-sentinel/) insieme a diverse origini dati aggiuntive.

- [Soluzioni di web application firewall e firewall di nuova generazione](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [Integrazione di soluzioni di sicurezza che supportano il formato di evento comune (CEF)](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

Al termine del ritiro, non è possibile aggiungere o modificare i tipi di soluzione indicati nell'elenco precedente, dall'interfaccia utente o dall'API. Il Centro sicurezza di Azure non troverà più le nuove istanze di queste soluzioni partner.

Se si hanno soluzioni connesse esistenti, si consiglia di passare ad Azure Sentinel.

![Soluzioni per i centri di sicurezza][16]

## Modificare le configurazioni di sicurezza per i criteri di sicurezza<a name="menu_securityconfigurations"></a>

Il Centro sicurezza di Azure monitora le configurazioni di sicurezza applicando un set di [più di 150 regole consigliate](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) per la protezione avanzata del sistema operativo. Queste regole riguardano firewall, controllo, criteri password e altro ancora. Se in un computer viene trovata una configurazione vulnerabile, il Centro sicurezza genera una raccomandazione di sicurezza. La [schermata Modifica configurazione sicurezza](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) consente ai clienti di personalizzare la configurazione di sicurezza del sistema operativo predefinita nel centro sicurezza.

Questa funzionalità di anteprima è stata ritirata. Per ripristinare i valori predefiniti delle configurazioni di sicurezza dopo la data di ritiro, eseguire questa operazione tramite l'API o PowerShell usando le [istruzioni seguenti](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20samples/Reset%20security%20configurations%20customization)

![Modificare le configurazioni di sicurezza][17]

### <a name="edit-security-configurations---the-new-experience"></a>Modificare le configurazioni di sicurezza: nuova esperienza

Si intende abilitare il Centro sicurezza per supportare l' [agente di configurazione Guest](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration). Tale aggiornamento consentirà un set di funzionalità molto più completo, incluso il supporto per più sistemi operativi e l'integrazione dei criteri di Azure in-Guest per le configurazioni Guest. Una volta abilitate queste modifiche, sarà anche possibile controllare le configurazioni su larga scala e applicarle automaticamente alle nuove risorse.

## Dashboard sicurezza e controllo per aree di lavoro Log Analytics<a name="menu_securityomsdashboard"></a>

Il dashboard sicurezza e controllo è stato originariamente usato nel portale di OMS. In Log Analytics, il dashboard offre una panoramica per area di lavoro di eventi di sicurezza e minacce rilevanti, una mappa di intelligence per le minacce e una valutazione dell'identità e dell'accesso degli eventi di sicurezza salvati nell'area di lavoro. Il dashboard è stato rimosso. Come è già stato consigliato nell'interfaccia utente del dashboard, si consiglia di passare al centro sicurezza di Azure.

![Dashboard di sicurezza Log Analytics][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Dashboard sicurezza e controllo: nuova esperienza

Si consiglia di passare al centro sicurezza di Azure. Fornisce la stessa panoramica della sicurezza tra più sottoscrizioni e le aree di lavoro associate, oltre a un set di funzionalità più completo.

È possibile ottenere le query di Log Analytics originali che popolano il dashboard sicurezza e controllo nel [repository GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) per il Centro sicurezza.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/).
- Altre informazioni su [Sentinel di Azure](https://docs.microsoft.com/azure/sentinel).

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
