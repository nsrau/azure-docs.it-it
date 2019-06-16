---
title: Ritiro del Centro sicurezza features (luglio 2019) | Microsoft Docs
description: Questo articolo descrive le funzionalità nel Centro sicurezza che verrà ritirato il 31 luglio 2019.
services: security-center
author: yoavfrancis
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.date: 4/16/2019
ms.author: yoafr
ms.openlocfilehash: 069345f9c2d0fff0b580365153d8be13bb4ba204
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65952148"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Ritiro della funzionalità del Centro sicurezza (luglio 2019)

Sono stati apportati numerosi [miglioramenti](https://azure.microsoft.com/updates/?product=security-center) al Centro sicurezza di Azure negli ultimi sei mesi.
Con queste funzionalità migliorate, stiamo rimosse alcune funzionalità ridondanti e le relative API dal Centro sicurezza 31 luglio 2019.  

La maggior parte di queste funzionalità di ritirare può essere sostituita con nuove funzionalità nel Centro sicurezza di Azure o Azure Log Analitica. Altre funzionalità può essere implementata usando [Sentinel di Azure (anteprima)](https://azure.microsoft.com/services/azure-sentinel/).

Funzionalità del Centro sicurezza al ritiro includono:

- [Dashboard eventi](#menu_events)
- [Voce di menu Cerca](#menu_search)
- [Visualizzazione classica identità e accesso collegamento identità e accesso (anteprima)](#menu_classicidentity)
- [Gli eventi di sicurezza di eseguire il mapping come pulsante sulla mappa degli avvisi di sicurezza (anteprima)](#menu_securityeventsmap)
- [Regole di avviso personalizzate (anteprima)](#menu_customalerts)
- [Provare a utilizzare pulsante negli avvisi di sicurezza di threat protection](#menu_investigate)
- [Un sottoinsieme di soluzioni di sicurezza](#menu_solutions)
- [Modificare le configurazioni di sicurezza per i criteri di sicurezza](#menu_securityconfigurations)
- [Dashboard sicurezza e controllo (originariamente usato nel portale di OMS) per le aree di lavoro di Log Analitica](#menu_securityomsdashboard)

Questo articolo fornisce informazioni dettagliate per ogni funzionalità ritirato e i passaggi da eseguire per implementare le funzionalità di sostituzione.

## Dashboard eventi<a name="menu_events"></a>

Centro sicurezza Usa Microsoft Monitoring Agent per raccogliere varie configurazioni correlate alla sicurezza e gli eventi dalle macchine. Archivia questi eventi nelle aree di lavoro. Il [dashboard eventi](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) consente di visualizzare i dati e ti offre un punto di ingresso per Log Analitica.

Si sta ritiro il dashboard degli eventi che viene visualizzata quando si seleziona un'area di lavoro:

![Dashboard eventi][2]

### <a name="events-dashboard---the-new-experience"></a>Dashboard degli eventi - la nuova esperienza

Si incoraggia è possibile utilizzare le funzionalità native di Azure Log Analitica per visualizzare gli eventi rilevanti in aree di lavoro.

Se sono stati creati gli eventi significativi personalizzati nel Centro sicurezza, sarà possibile accedere. Nel Log Analitica, passare a **selezionare l'area di lavoro** > **ricerche salvate**. I dati non verranno persa o modificati. Sono disponibili dalla schermata stessa nel Log Analitica anche eventi rilevanti nativi.

![Le ricerche salvata dell'area di lavoro][3]

## Voce di menu Cerca<a name="menu_search"></a>

Centro sicurezza di Azure Usa attualmente la ricerca nei log di monitoraggio di Azure per recuperare e analizzare i dati di sicurezza. Questa schermata viene usato come una finestra alla pagina di ricerca Log Analitica e consente agli utenti di eseguire query di ricerca nell'area di lavoro selezionato. Per altre informazioni, vedere [ricerca nel Centro sicurezza Azure](https://docs.microsoft.com/azure/security-center/security-center-search). Si sta ritiro di questa finestra di ricerca:

![Pagina di ricerca][4]

### <a name="search-menu-entry---the-new-experience"></a>Voce di menu ricerca - la nuova esperienza

Si consiglia di usare le funzionalità native di Azure Log Analitica per eseguire query di ricerca in aree di lavoro. Passare a Azure Log Analitica e selezionare **registri**.

![Pagina dei log di log Analitica][5]

## Classico identità e accesso (anteprima)<a name="menu_classicidentity"></a>

L'esperienza classica di identità e accesso nel Centro sicurezza attualmente Mostra un dashboard di identità e accesso alle informazioni nel Log Analitica. Per visualizzare questo dashboard:

1. Selezionare **visualizzazione classica di identità e accesso**.

   ![Pagina di identità][6]

1. Visualizza i **dashboard identità e accesso**.

    ![Pagina di identità - selezione dell'area di lavoro][7]

1. Selezionare un'area di lavoro per aprire la **identità e accesso** dashboard nel Log Analitica per visualizzare l'identità e accedere alle informazioni sull'area di lavoro.

   ![Pagina di identità - dashboard][8]

Si sta ritiro di tutti i tre schermate che appaiono nei passaggi precedenti. I dati rimarranno disponibili nella soluzione di protezione Log Analitica e non modificare o rimuovere.

### <a name="classic-identity--access-preview---the-new-experience"></a>Classico identità e accesso (anteprima): la nuova esperienza

Il dashboard di Log Analitica hanno dimostrato insights in una singola area di lavoro. Tuttavia, funzionalità native di Centro sicurezza forniscono visibilità in tutte le sottoscrizioni e tutte le aree di lavoro associati. È possibile accedere a un semplice-utilizzo di visualizzazione che ti permette di concentrarti sugli aspetti più importanti con raccomandazioni classificate in base al relativo punteggio sicura.

Tutte le funzionalità dei **identità e accesso** dashboard di Log Analitica può essere raggiunto selezionando **identità e accesso (anteprima)** in Centro sicurezza.

![Pagina di identità - classica esperienza di ritiro][9]

## Eseguire il mapping degli eventi di protezione<a name="menu_securityeventsmap"></a>

Il Centro sicurezza offre una [mappa gli avvisi di sicurezza](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) per consentire di identificare le minacce alla sicurezza. Il **passare alla mappa di eventi di sicurezza** pulsante in questa mappa consente di aprire un dashboard che consente di visualizzare gli eventi di sicurezza non elaborati nell'area di lavoro selezionato.

Si sta rimuovendo il **passare alla mappa di eventi di sicurezza** pulsante e il dashboard per ogni area di lavoro.

![Pulsante mappa - agli avvisi di sicurezza][10]

Quando si seleziona il **passare alla mappa di eventi di sicurezza** pulsante, si apre il dashboard intelligence delle minacce. Si sta ritiro dashboard intelligence per le minacce.  

![Dashboard Intelligence per le minacce][11]

Quando si sceglie un'area di lavoro per visualizzare il rispettivo dashboard intelligence delle minacce, si apre la schermata di mappa (anteprima) gli avvisi di sicurezza nel Log Analitica. Si sta ritiro questa schermata.

![Mappa di avvisi di sicurezza nel Log Analitica][12]

I dati esistenti rimarranno disponibili nella soluzione di protezione Log Analitica e non è possibile modificare o rimuovere.

### <a name="security-events-map---the-new-experience"></a>Mappa eventi di sicurezza - la nuova esperienza

Si consiglia di usare la funzionalità mappa avvisi incorporata in Centro sicurezza: **Mappa di avvisi di sicurezza (anteprima)** . Questa funzionalità offre un'esperienza ottimizzata e ha effetto su tutte le sottoscrizioni e aree di lavoro associati. Ti offre una visualizzazione generale in tutto l'ambiente e non è incentrata su una singola area di lavoro.

## Regole di avviso personalizzate (anteprima)<a name="menu_customalerts"></a>

Siamo [disattivazione personalizzata degli avvisi esperienza](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) 30 giugno 2019 perché ritira l'infrastruttura sottostante. Fino ad allora, è possibile modificare le regole di avviso personalizzate esistenti, ma non è possibile aggiungerne di nuovi. È consigliabile abilitare [Sentinel Azure](https://azure.microsoft.com/services/azure-sentinel/) automaticamente la migrazione gli avvisi esistenti e crearne uno nuovo. In alternativa, è possibile creare gli avvisi con avvisi di log di monitoraggio di Azure.

Per mantenere gli avvisi esistenti ed eseguirne la migrazione ad Azure Sentinel:

1. Aprire Azure Sentinel e selezionare l'area di lavoro in cui sono archiviati gli avvisi personalizzati.
1. Selezionare **Analitica** dal menu per eseguire la migrazione automatica degli avvisi.

![Avvisi personalizzati][13]

Se non si è interessati alla transizione a Sentinel di Azure, è consigliabile creare gli avvisi con avvisi di log di monitoraggio di Azure. Per istruzioni, vedere [crea, visualizzare e gestire gli avvisi del log tramite Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) e [gli avvisi del Log in Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

Per altre informazioni sul ritiro di avvisi personalizzati, vedere [regole di avviso personalizzate nel Centro sicurezza di Azure (anteprima)](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## Analisi degli avvisi di sicurezza<a name="menu_investigate"></a>

[La funzionalità indagine](https://docs.microsoft.com/azure/security-center/security-center-investigation) in Centro sicurezza valutare i potenziali problemi di protezione. La funzionalità consente di determinare l'ambito di un evento imprevisto e tenere traccia della causa radice. È corso la rimozione della funzionalità dal Centro sicurezza perché è stato sostituito con un'esperienza migliorata in [Sentinel Azure](https://azure.microsoft.com/services/azure-sentinel/).

![Evento imprevisto della sicurezza][14]

Quando si seleziona il **ricerca causa** pulsante da una **eventi imprevisti della sicurezza** dello schermo, si apre il Dashboard di analisi (anteprima) in Log Analitica. Si sta ritirato il Dashboard di analisi.  

I dati esistenti rimarranno disponibili nella soluzione di protezione Log Analitica e non è possibile modificare o rimuovere.

![Dashboard indagine nel Log Analitica][15]

### <a name="investigation---the-new-experience"></a>Analisi - la nuova esperienza

Si consiglia di passare al [Sentinel Azure](https://azure.microsoft.com/services/azure-sentinel/) per un'esperienza di analisi avanzate. Sentinel Azure sono disponibili potenti strumenti di ricerca e query cercare minacce per la sicurezza tra le origini dati dell'organizzazione.  

## Subset di soluzioni di sicurezza<a name="menu_solutions"></a>

Il Centro sicurezza è possibile abilitare [soluzioni di sicurezza in Azure integrate](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). Abbiamo stiamo ritiro le seguenti soluzioni partner dal Centro sicurezza. Queste soluzioni sono abilitate nelle [Sentinel Azure](https://azure.microsoft.com/services/azure-sentinel/) insieme a un numero di origini dati aggiuntive.

- [Avanti generazione soluzioni e firewall web application firewall](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [Integrazione di soluzioni di sicurezza che supportano Common Event Format (CEF)](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analitica](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

Dopo il ritiro, sarà possibile aggiungere o modificare uno qualsiasi dei tipi di soluzioni indicati nell'elenco precedente, dall'interfaccia utente o l'API.

Se si dispone di soluzioni connesse esistente, si consiglia di spostare in Azure Sentinel.

![Centri sicurezza soluzioni][16]

## Modificare le configurazioni di sicurezza per i criteri di sicurezza<a name="menu_securityconfigurations"></a>

Il Centro sicurezza di Azure monitora le configurazioni di sicurezza applicando un set di [oltre 150 regole consigliate](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). per la protezione avanzata del sistema operativo. Queste regole sono relative a firewall, controllo, i criteri password e altro ancora. Se in un computer viene trovata una configurazione vulnerabile, il Centro sicurezza genera una raccomandazione di sicurezza. Il [schermata di configurazione della protezione Modifica](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) consente ai clienti di personalizzare la configurazione di sicurezza predefinita del sistema operativo nel Centro sicurezza.

Si sta ritiro di questa funzionalità in anteprima. Se, dopo la data di ritiro, si desidera ripristinare le configurazioni di sicurezza sui valori predefiniti, è possibile farlo tramite Powershell o API usando il [attenendosi alle istruzioni](https://aka.ms/ascresetsecurityconfigurations)

![Modificare le configurazioni di sicurezza][17]

### <a name="edit-security-configurations---the-new-experience"></a>Modifica configurazioni di sicurezza, la nuova esperienza

Si prevede di abilitare il Centro sicurezza supportare le [l'agente Guest configurazione](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration). L'aggiornamento consentirà un set di funzionalità molto più ampio, incluso il supporto per più sistemi operativi e integrazione dei criteri nel sistema operativo guest Azure per le configurazioni di guest. Dopo che queste modifiche sono abilitate, sarà necessario anche la possibilità di controllare le configurazioni su larga scala e applicati automaticamente alle nuove risorse.

## Dashboard sicurezza e controllo per le aree di lavoro di Log Analitica<a name="menu_securityomsdashboard"></a>

Il dashboard sicurezza e controllo è stato originariamente usato nel portale di OMS. Nel Log Analitica, il dashboard offre una panoramica per ogni area di lavoro di eventi di sicurezza rilevanti e le minacce, una mappa di intelligence delle minacce e una valutazione di identità e accesso degli eventi di sicurezza salvato nell'area di lavoro. Si sta rimozione del dashboard. Come abbiamo già consigliato nel dashboard dell'interfaccia utente, si consiglia di transizione al Centro sicurezza di Azure.

![Dashboard di sicurezza di log Analitica][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Dashboard sicurezza e controllo - la nuova esperienza

Si consiglia di passare al Centro sicurezza di Azure. Fornisce la stessa Panoramica della sicurezza tra più sottoscrizioni e le aree di lavoro associati, oltre a imposta una funzionalità più avanzata.

È possibile ottenere le query di Log Analitica originali che consentono di popolare il dashboard sicurezza e controllo nel [repository GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) per il Centro sicurezza.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/).
- Altre informazioni sulle [Sentinel Azure](https://docs.microsoft.com/azure/sentinel).

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
