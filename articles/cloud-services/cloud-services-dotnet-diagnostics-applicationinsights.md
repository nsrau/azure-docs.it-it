---
title: Risolvere i problemi di Servizi cloud con Application Insights | Documentazione Microsoft
description: Informazioni su come risolvere i problemi relativi al servizio cloud mediante Application Insights per elaborare i dati del servizio Diagnostica di Azure.
services: cloud-services
documentationcenter: .net
author: sbtron
manager: timlt
editor: tysonn
ms.assetid: e93f387b-ef29-4731-ae41-0676722accb6
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: saurabh
ms.openlocfilehash: 4001ca908ff00b1a40829d687589080e9b07b18a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-cloud-services-using-application-insights"></a>Risoluzione dei problemi di Servizi Cloud tramite Application Insights
Con [Azure SDK 2.8](https://azure.microsoft.com/downloads/) e l'estensione Diagnostica di Azure 1.5 è possibile inviare i dati del servizio Diagnostica di Azure per il servizio cloud direttamente ad Application Insights. I log raccolti da Diagnostica di Azure, tra cui log applicazioni, registri eventi di Windows, log ETW e contatori delle prestazioni possono essere inviati ad Application Insights. È quindi possibile visualizzare queste informazioni nell'interfaccia utente del portale Application Insights. Si può poi usare Application Insights SDK per ottenere informazioni dettagliate sulle metriche e i log provenienti dall'applicazione, nonché sui dati del livello di sistema e dell'infrastruttura provenienti da Diagnostica di Azure.

## <a name="configure-azure-diagnostics-to-send-data-to-application-insights"></a>Configurare Diagnostica di Azure per inviare dati ad Application Insights
Seguire questi passaggi per configurare il progetto del servizio cloud per l'invio di dati del servizio Diagnostica di Azure ad Application Insights.

1. In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse su un ruolo e scegliere **Proprietà** per aprire la finestra di progettazione dei ruoli.

    ![Proprietà del ruolo di Esplora soluzioni][1]

2. Nella sezione **Diagnostica** della finestra di progettazione dei ruoli selezionare l'opzione **Invia i dati di diagnostica ad Application Insights**.

    ![La finestra di progettazione dei ruoli invia dati di diagnostica ad Application Insights][2]

3. Nella finestra di dialogo visualizzata selezionare la risorsa di Application Insights a cui si vogliono inviare i dati del servizio Diagnostica di Azure. La finestra di dialogo consente di selezionare una risorsa di Application Insights esistente dalla sottoscrizione o di specificare manualmente una chiave di strumentazione per una risorsa di Application Insights. Per altre informazioni sulla creazione di una risorsa di Application Insights, vedere [Creare una nuova risorsa di Application Insights](../application-insights/app-insights-create-new-resource.md).

    ![selezionare una risorsa di Application Insights][3]

    Dopo aver aggiunto la risorsa di Application Insights, la chiave di strumentazione per la risorsa viene archiviata come impostazione di configurazione del servizio con il nome **APPINSIGHTS_INSTRUMENTATIONKEY**. È possibile modificare questa impostazione di configurazione per ogni configurazione del servizio o ambiente. A tale scopo, selezionare una configurazione diversa nell'elenco **Configurazione servizio** e specificare una nuova chiave di strumentazione per la configurazione.

    ![selezionare una configurazione del servizio][4]

    L'impostazione di configurazione **APPINSIGHTS_INSTRUMENTATIONKEY** viene usata da Visual Studio per configurare l'estensione di diagnostica con le informazioni appropriate sulle risorse di Application Insights durante la pubblicazione. L'impostazione di configurazione è un modo pratico per definire chiavi di strumentazione diverse per configurazioni del servizio diverse. Visual Studio converte tale impostazione e la inserisce nella configurazione pubblica dell'estensione di diagnostica durante il processo di pubblicazione. Per semplificare il processo di configurazione dell'estensione di diagnostica con PowerShell, l'output del pacchetto di Visual Studio contiene anche il codice XML di configurazione pubblica con la chiave di strumentazione corretta di Application Insights. I file di configurazione pubblica vengono creati nella cartella Extensions e seguono il modello *PaaSDiagnostics.&lt;RoleName&gt;.PubConfig.xml*. Eventuali distribuzioni basate su PowerShell possono usare questo modello per il mapping di ogni configurazione a un ruolo.

4) Per configurare il servizio Diagnostica di Azure per inviare tutti i contatori delle prestazioni e i log a livello di errore raccolti dall'agente di Diagnostica di Azure ad Application Insights, abilitare l'opzione **Invia i dati di diagnostica ad Application Insights**. 

    Se si vogliono configurare ulteriormente i dati inviati ad Application Insights, è necessario modificare manualmente il file *diagnostics.wadcfgx* per ogni ruolo. Per altre informazioni sull'aggiornamento manuale della configurazione, vedere l'articolo relativo alla [configurazione di Diagnostica di Azure per inviare dati ad Application Insights](#configure-azure-diagnostics-to-send-data-to-application-insights) .

Quando il servizio cloud è configurato per l'invio di dati del servizio Diagnostica di Azure ad Application Insights, è possibile distribuirlo in Azure normalmente, verificando che l'estensione della diagnostica di Azure sia abilitata. Per altre informazioni, vedere [Pubblicazione di un servizio cloud di Azure con Visual Studio](../vs-azure-tools-publishing-a-cloud-service.md).  

## <a name="viewing-azure-diagnostics-data-in-application-insights"></a>Visualizzazione dei dati del servizio Diagnostica di Azure in Application Insights
La telemetria diagnostica di Azure viene visualizzata nella risorsa di Application Insights configurata per il servizio cloud.

I tipi di log di Diagnostica di Azure vengono mappati ai concetti di Application Insights in questi modi:

* I contatori delle prestazioni vengono visualizzati come metriche personalizzate in Application Insights.
* I registri eventi di Windows vengono visualizzati come tracce ed eventi personalizzati in Application Insights.
* I log applicazioni, i log ETW e gli eventuali log dell'infrastruttura di diagnostica vengono visualizzati come tracce in Application Insights.

Per visualizzare i dati del servizio Diagnostica di Azure in Application Insights, eseguire una delle operazioni seguenti:

* Usare [Esplora metriche](../application-insights/app-insights-metrics-explorer.md) per visualizzare tutti i contatori delle prestazioni personalizzati o i conteggi di diversi tipi di eventi del registro eventi di Windows.

    ![Metriche personalizzate in Esplora metriche][5]

* Usare [Cerca](../application-insights/app-insights-diagnostic-search.md) per eseguire ricerche nei log di traccia inviati da Diagnostica di Azure. Se un'eccezione non gestita ha causato l'arresto anomalo e il riciclo del ruolo, ad esempio, le informazioni sull'eccezione vengono visualizzate nel canale *Applicazione* del *registro eventi di Windows*. È possibile usare la funzionalità di ricerca per esaminare l'errore del registro eventi di Windows e ottenere l'analisi dello stack completa per l'eccezione per trovare più facilmente la causa del problema.

    ![Cerca tracce][6]

## <a name="next-steps"></a>Passaggi successivi
* [Aggiungere Application Insights SDK al servizio cloud](../application-insights/app-insights-cloudservices.md) per inviare dati relativi a richieste, eccezioni, dipendenze ed eventuale telemetria personalizzata dall'applicazione. In combinazione con i dati del servizio Diagnostica di Azure, queste informazioni offriranno una visione completa dell'applicazione e del sistema nella stessa risorsa di Application Insight.  

<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png
