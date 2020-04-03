---
title: Ritiro delle funzionalità del Centro sicurezza (luglio 2019) Documenti Microsoft
description: In questo articolo vengono descritte le funzionalità del Centro sicurezza che sono state ritirate il 31 luglio 2019.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: article
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 4a760fe1e5df7cf614a68f8fa8a05926326d3edf
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583226"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Ritiro delle funzionalità del Centro sicurezza (luglio 2019)

> [!NOTE]
> Questo documento descrive in dettaglio l'elenco delle funzionalità ritirate dal Centro sicurezza di Azure il 31 luglio 2019.This document details the list of features that were retired from Azure Security Center on July 31st, 2019.
>
>

Sono stati apportati diversi miglioramenti al Centro sicurezza di Azure nei sei mesi precedenti a luglio 2019.We made several [improvements](https://azure.microsoft.com/updates/?product=security-center) to Azure Security Center over the six months before up to July 2019.
Con queste funzionalità migliorate, alcune funzionalità ridondanti e le API correlate sono state rimosse dal Centro sicurezza il 31 luglio 2019.With these improved capabilities, we removed some redundant features and related APIs from Security Center on July 31, 2019.

La maggior parte di queste funzionalità ritirate può essere sostituita con altre funzionalità nel Centro sicurezza di Azure o in Azure Log Analytics.Most of these retired features can be replaced with other functionality in Azure Security Center or Azure Log Analytics. Altre funzionalità possono essere implementate usando [Azure Sentinel (anteprima).](https://azure.microsoft.com/services/azure-sentinel/)

Le funzionalità del Centro sicurezza ritirate includono:

- [Dashboard eventi](#menu_events)
- [Voce di menu di ricerca](#menu_search)
- [Visualizzare il collegamento di accesso & identità classico su identità e accesso (anteprima)View classic Identity & Access link on Identity and access (preview)](#menu_classicidentity)
- [Pulsante Mappa eventi di sicurezza nella mappa Avvisi di sicurezza (anteprima)Security events map button on Security alerts map (preview)](#menu_securityeventsmap)
- [Regole di avviso personalizzate (anteprima)Custom alert rules (preview)](#menu_customalerts)
- [Pulsante Analizza negli avvisi di sicurezza per la protezione dalle minacce](#menu_investigate)
- [Un sottoinsieme di soluzioni di sicurezza](#menu_solutions)
- [Modificare le configurazioni di sicurezza per i criteri di sicurezza](#menu_securityconfigurations)
- [Dashboard di sicurezza e controllo (originariamente usato nel portale Di OMS) per le aree di lavoro di Log Analytics](#menu_securityomsdashboard)

In questo articolo vengono fornite informazioni dettagliate per ogni funzionalità ritirata e i passaggi che è possibile eseguire per implementare le funzionalità di sostituzione.

## <a name="events-dashboard"></a>Dashboard eventi<a name="menu_events"></a>

Il Centro sicurezza usa l'agente log Analytics per raccogliere varie configurazioni ed eventi correlati alla sicurezza dai computer. Archivia questi eventi nelle aree di lavoro. Il [dashboard degli eventi](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) consente di visualizzare questi dati e fornisce un punto di ingresso a Log Analytics.

È stato ritirato il dashboard degli eventi visualizzato quando è stata selezionata un'area di lavoro:We retired the events dashboard that appeared when you selected a workspace:

![Dashboard eventi][2]

### <a name="events-dashboard---the-new-experience"></a>Dashboard Eventi - la nuova esperienza

Ti consigliamo di usare le funzionalità native di Analisi dei log di Azure per visualizzare eventi importanti nelle aree di lavoro.

Se hai creato eventi importanti personalizzati nel Centro sicurezza, saranno accessibili. In Log Analytics passare a **Seleziona ricerche salvate area di lavoro** > **Saved Searches**. I tuoi dati non andranno persi o modificati. Gli eventi noti nativi sono disponibili anche nella stessa schermata in Log Analytics.Native notable events are also available from the same screen in Log Analytics.

![Ricerche nell'area di lavoro salvata][3]

## <a name="search-menu-entry"></a>Voce di menu di ricerca<a name="menu_search"></a>

Il Centro sicurezza di Azure usa attualmente la ricerca dei log di Monitoraggio di Azure per recuperare e analizzare i dati di sicurezza. Questa schermata funge da finestra della pagina di ricerca di Log Analytics e consente agli utenti di eseguire query di ricerca nell'area di lavoro selezionata. Per altre informazioni, vedere Ricerca nel [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-search). Abbiamo ritirato questa finestra di ricerca:

![Pagina di ricerca][4]

### <a name="search-menu-entry---the-new-experience"></a>Casella di menu Di ricerca - la nuova esperienza

Ti invitiamo a usare le funzionalità native di Azure Log Analytics per eseguire query di ricerca nelle aree di lavoro. Passare ad Analisi dei log di Azure e selezionare **Registri**.

![Pagina dei log di Log Analytics][5]

## <a name="classic-identity--access-preview"></a>Identità classica & Access (anteprima)Classic Identity & Access (Preview)<a name="menu_classicidentity"></a>

L'esperienza di accesso & identità classica nel Centro sicurezza mostra attualmente un dashboard di informazioni di identità e accesso in Log Analytics.The Classic Identity & Access experience in Security Center currently shows a dashboard of identity and access information in Log Analytics. Per visualizzare questo dashboard:

1. Selezionare **Visualizza accesso classico & identità**.

   ![Pagina Identità][6]

1. Visualizzare il **dashboard di Identità & Access**.

    ![Pagina Identità - selezione dell'area di lavoro][7]

1. Selezionare un'area di lavoro per aprire il dashboard **identità & accesso** in Log Analytics per visualizzare le informazioni sull'identità e accedere all'area di lavoro.

   ![Pagina Identità - dashboard][8]

Abbiamo ritirato tutti e tre gli schermi mostrati nei passaggi precedenti. I dati rimangono disponibili nella soluzione di sicurezza di Log Analytics e non sono stati modificati o rimossi.

### <a name="classic-identity--access-preview---the-new-experience"></a>Classic Identity & Access (anteprima) - la nuova esperienza

Il dashboard di Log Analytics ha mostrato informazioni dettagliate in una singola area di lavoro. Tuttavia, le funzionalità native del Centro sicurezza offrono visibilità su tutte le sottoscrizioni e su tutte le aree di lavoro ad esse associate. È possibile accedere a una visualizzazione facile da usare che consente di concentrarsi su ciò che è importante con i consigli classificati in base al loro punteggio sicuro.

Tutte le funzionalità del dashboard **Identity & Access** in Log Analytics possono essere raggiunte selezionando Accesso & **identità (anteprima)** all'interno del Centro sicurezza.

![Pagina Identità - pensione classica dell'esperienza][9]

## <a name="security-events-map"></a>Mappa degli eventi di sicurezza<a name="menu_securityeventsmap"></a>

Il Centro sicurezza fornisce una mappa degli avvisi di sicurezza per identificare le minacce alla sicurezza. Il pulsante **Vai alla mappa degli eventi** di sicurezza in tale mappa apre un dashboard che consente di visualizzare gli eventi di sicurezza non elaborati nell'area di lavoro selezionata.

È stato rimosso il pulsante **Vai alla mappa degli eventi** di sicurezza e il dashboard per area di lavoro.

![Mappa avvisi di sicurezza - pulsante][10]

Quando si seleziona il pulsante Vai alla mappa degli eventi di **sicurezza,** è stato aperto il dashboard di intelligence sulle minacce (ora ritirato).

![Dashboard Intelligence per le minacce][11]

Quando si sceglie un'area di lavoro per visualizzare il dashboard di Business Intelligence sulle minacce, è stata aperta la mappa (anteprima) degli avvisi di sicurezza (ora ritirata) in Log Analytics.

![Mappa degli avvisi di sicurezza in Log Analytics][12]

I dati esistenti rimangono disponibili nella soluzione di sicurezza di Log Analytics e non sono stati modificati o rimossi.

### <a name="security-events-map---the-new-experience"></a>Mappa degli eventi di sicurezza - la nuova esperienza

Si consiglia di utilizzare la funzionalità della mappa degli avvisi incorporata nel Centro sicurezza: mappa degli avvisi di **sicurezza (anteprima)**. Questa funzionalità offre un'esperienza ottimizzata e funziona in tutte le sottoscrizioni e le aree di lavoro associate. Offre una visualizzazione di alto livello in tutto l'ambiente e non è focalizzata su una singola area di lavoro.

## <a name="custom-alert-rules-preview"></a>Regole di avviso personalizzate (anteprima)Custom alert rules (Preview)<a name="menu_customalerts"></a>

Abbiamo [ritirato l'esperienza](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) di avvisi personalizzati il 30 giugno 2019 perché l'infrastruttura sottostante è stata ritirata. Dopo la data di pensionamento, gli avvisi di sicurezza personalizzati non vengono più generati.
È consigliabile abilitare [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) e ricreare gli avvisi personalizzati. In alternativa, è possibile creare gli avvisi con gli avvisi del log di Monitoraggio di Azure.Alternatively, you can create your alerts with Azure Monitor log alerts.

Per creare avvisi personalizzati con Azure Sentinel:To create custom alerts with Azure Sentinel:

1. Aprire Azure Sentinel e selezionare l'area di lavoro in cui sono archiviati gli avvisi [personalizzatiOpen Azure Sentinel](https://portal.azure.com/#create/Microsoft.ASI/preview) and select the workspace where your custom alerts are stored
1. Seleziona **Analytics** dal menu
1. Seguire le istruzioni [nell'esercitazione](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) seguente su come creare avvisi personalizzati in Azure SentinelFollow instructions in the following tutorial on how to create custom alerts in Azure Sentinel

Se non si è interessati all'uso di Azure Sentinel, è possibile creare gli avvisi con gli avvisi del log di Monitoraggio di Azure.If you're not interested in using Azure Sentinel, you can create your alerts with Azure Monitor log alerts. Per istruzioni, vedere Creare, visualizzare e gestire gli avvisi di log tramite Monitoraggio di Azure e [Registrare avvisi in Monitoraggio di Azure.For instructions,](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)see [Create, view, and manage log alerts by using Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) and Log alerts in Azure Monitor.

![Avvisi personalizzati][13]

Per altre informazioni sul ritiro degli avvisi personalizzati, vedere Regole di avviso personalizzate nel Centro sicurezza di [Azure (anteprima).](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)

## <a name="security-alerts-investigation"></a>Analisi degli avvisi di sicurezza<a name="menu_investigate"></a>

La funzionalità Di corso in un'indagine nel Centro sicurezza consente di valutare un potenziale incidente di sicurezza. La funzionalità consente di comprendere l'ambito di un evento imprevisto e rintracciarne la causa principale. Questa funzionalità è stata rimossa dal Centro sicurezza perché è stata sostituita con un'esperienza migliorata in [Azure Sentinel.](https://azure.microsoft.com/services/azure-sentinel/)

![Evento imprevisto della sicurezza][14]

Quando si seleziona il pulsante **Analizza** da una schermata **Evento imprevisto** di sicurezza, si apre il Dashboard di indagine (anteprima) in Log Analytics. Abbiamo ritirato il quadro d'indagine.

I dati esistenti rimangono disponibili nella soluzione di sicurezza di Log Analytics e non sono stati modificati o rimossi.

![Dashboard di indagine in Log Analytics][15]

### <a name="investigation---the-new-experience"></a>Indagine - la nuova esperienza

Ti invitiamo a passare ad [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) per un'esperienza di indagine avanzata. Azure Sentinel offre potenti strumenti di ricerca e query per cercare minacce alla sicurezza nelle origini dati dell'organizzazione.

## <a name="subset-of-security-solutions"></a>Sottoinsieme di soluzioni di sicurezza<a name="menu_solutions"></a>

Il Centro sicurezza può abilitare [soluzioni di sicurezza integrate in Azure.Security](https://docs.microsoft.com/azure/security-center/security-center-partner-integration)Center can enable integrated security solutions in Azure . Abbiamo ritirato le seguenti soluzioni partner dal Centro sicurezza. Queste soluzioni sono abilitate in [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) insieme a numerose origini dati aggiuntive.

- [Soluzioni firewall e web application firewall di nuova generazione](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [Integrazione di soluzioni di sicurezza che supportano il formato COMMON Event Format (CEF)](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

Dopo il ritiro, non è possibile aggiungere o modificare i tipi di soluzione indicati nell'elenco precedente, dall'interfaccia utente o dall'API. Il Centro sicurezza di Azure non rileverà più nuove istanze di queste soluzioni partner.

Se si dispone di soluzioni connesse esistenti, è consigliabile passare ad Azure Sentinel.If you have existing connected solutions, we encourage you to move to Azure Sentinel.

![Soluzioni per centri di sicurezza][16]

## <a name="edit-security-configurations-for-security-policies"></a>Modificare le configurazioni di sicurezza per i criteri di sicurezza<a name="menu_securityconfigurations"></a>

Il Centro sicurezza di Azure monitora le configurazioni di sicurezza applicando un set di [più di 150 regole consigliate](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) per la protezione avanzata del sistema operativo. Queste regole riguardano firewall, controllo, criteri password e altro ancora. Se in un computer viene trovata una configurazione vulnerabile, il Centro sicurezza genera una raccomandazione di sicurezza. La [schermata Modifica configurazione sicurezza](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) consente ai clienti di personalizzare la configurazione di sicurezza del sistema operativo predefinito nel Centro sicurezza PC.

Abbiamo ritirato questa funzione di anteprima. Per ripristinare i valori predefiniti delle configurazioni di sicurezza dopo la data di pensionamento, eseguire questa operazione tramite l'API o Powershell utilizzando le [istruzioni seguenti.](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/Reset%20security%20configurations%20customization)

![Modificare le configurazioni di sicurezza][17]

### <a name="edit-security-configurations---the-new-experience"></a>Modificare le configurazioni di sicurezza - la nuova esperienza

Si intende abilitare il Centro sicurezza per il supporto [dell'agente di configurazione Guest.](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) Un aggiornamento di questo tipo consentirà un set di funzionalità molto più completo, incluso il supporto per più sistemi operativi e l'integrazione dei criteri di Azure in-guest per le configurazioni guest. Dopo aver abilitato queste modifiche, avrai anche la possibilità di controllare le configurazioni su larga scala e applicarle automaticamente alle nuove risorse.

## <a name="security-and-audit-dashboard-for-log-analytics-workspaces"></a>Dashboard di sicurezza e controllo per le aree di lavoro di Log Analytics<a name="menu_securityomsdashboard"></a>

Il dashboard di sicurezza e controllo è stato originariamente utilizzato nel portale OMS. In Log Analytics, il dashboard offre una panoramica per ogni area di lavoro di importanti eventi e minacce di sicurezza, una mappa di Threat Intelligence e una valutazione di identità e accesso degli eventi di sicurezza salvati nell'area di lavoro. Il dashboard è stato rimosso. Come già consigliato nell'interfaccia utente del dashboard, è consigliabile passare al Centro sicurezza di Azure.As we already recommended in the dashboard UI, we advise you to transition to Azure Security Center.

![Dashboard di sicurezza di Log AnalyticsLog Analytics security dashboard][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Dashboard di sicurezza e audit: la nuova esperienza

È consigliabile passare al Centro sicurezza di Azure.We advise you to switch to Azure Security Center. Fornisce la stessa panoramica sulla sicurezza tra più sottoscrizioni e le aree di lavoro ad esse associate, oltre a un set di funzionalità più completo.

È possibile ottenere le query originali di Log Analytics che popolano il dashboard di sicurezza e controllo nel [repository GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) per il Centro sicurezza.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [Centro sicurezza](https://docs.microsoft.com/azure/security-center/)di Azure .Learn Center .
- Altre informazioni su [Azure Sentinel](https://docs.microsoft.com/azure/sentinel).

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
