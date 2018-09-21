---
title: 'Esercitazione: Trasmettere i log di Azure Active Directory a un hub eventi di Azure (anteprima) | Microsoft Docs'
description: Informazioni su come configurare Diagnostica di Azure per eseguire il push dei log di Azure Active Directory a un hub eventi (anteprima)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: c8157820f7137eb06a0fe10c42645ec91f33edbc
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364073"
---
# <a name="tutorial-stream-azure-ad-logs-to-an-azure-event-hub-preview"></a>Esercitazione: Trasmettere i log di Azure AD a un hub eventi di Azure (anteprima)

In questa esercitazione viene descritto come configurare le impostazioni di diagnostica di Monitoraggio di Azure per trasmettere i log di Azure Active Directory (Azure AD) a un hub eventi di Azure. Questo meccanismo consente di integrare i log con strumenti SIEM (Security Information and Event Management, informazioni di sicurezza e gestione degli eventi) di terze parti, come Splunk e QRadar.

## <a name="prerequisites"></a>Prerequisiti 

Per usare questa funzionalità, sono necessari:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile [iscriversi per ottenere una versione di valutazione gratuita](https://azure.microsoft.com/free/).
* Un tenant di Azure AD.
* Un utente con il ruolo di *amministratore globale* o *amministratore della sicurezza* per il tenant di Azure AD.
* Uno spazio dei nomi di Hub eventi e un hub eventi nella sottoscrizione di Azure. Leggere le informazioni su come [creare un hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="archive-logs-to-an-event-hub"></a>Archiviare i log in un hub eventi

1. Accedere al [portale di Azure](https://portal.azure.com). 

2. Selezionare **Azure Active Directory** > **Attività** > **Log di controllo**. 

3. Selezionare **Esporta impostazioni**.  
    
4. Nel riquadro **Impostazioni di diagnostica** eseguire una delle operazioni seguenti:
    * Per modificare le impostazioni esistenti, selezionare **Modifica l'impostazione**.
    * Per aggiungere nuove impostazioni, selezionare **Aggiungi impostazioni di diagnostica**.  
      È possibile avere fino a tre impostazioni.

      ![Esportazione impostazioni](./media/quickstart-azure-monitor-stream-logs-to-event-hub/ExportSettings.png)

5. Selezionare la casella di controllo **Streaming in un hub eventi** e quindi selezionare **Hub eventi/Configura**.

6. Selezionare la sottoscrizione di Azure e lo spazio dei nomi di Hub eventi a cui instradare i log.  
    Sia la sottoscrizione che lo spazio dei nomi di Hub eventi devono essere associati al tenant di Azure AD da cui vengono trasmessi i log. È anche possibile specificare un hub eventi nello spazio dei nomi di Hub eventi a cui devono essere inviati i log. Se non viene specificato alcun hub eventi, viene creato un hub eventi nello spazio dei nomi con il nome predefinito **insights-logs-audit**.

7. Selezionare **OK** per chiudere la configurazione dell'hub eventi.

8. Eseguire una di queste operazioni o entrambe:
    * Per inviare i log di controllo all'account di archiviazione, selezionare la casella di controllo **AuditLogs**. 
    * Per inviare i log di accesso all'account di archiviazione, selezionare la casella di controllo **SignInLogs**.

9. Selezionare **Salva** per salvare l'impostazione.

    ![Impostazioni di diagnostica](./media/quickstart-azure-monitor-stream-logs-to-event-hub/DiagnosticSettings.png)

10. Dopo circa 15 minuti, verificare che gli eventi vengano visualizzati nell'hub eventi. A tale scopo, passare all'hub eventi dal portale e verificare che il numero di **Messaggi in arrivo** sia maggiore di zero. 

    ![Log di controllo](./media/quickstart-azure-monitor-stream-logs-to-event-hub/InsightsLogsAudit.png)

## <a name="access-data-from-your-event-hub"></a>Accedere ai dati dall'hub eventi

Dopo che i dati vengono visualizzati nell'hub eventi, è possibile accedervi e leggerli in due modi:

* **Configurare uno strumento SIEM (Security Information and Event Management, informazioni di sicurezza e gestione degli eventi) supportato**. Per leggere i dati dall'hub eventi, la maggior parte degli strumenti richiede la stringa di connessione dell'hub eventi e determinate autorizzazioni per la sottoscrizione di Azure. Ecco alcuni strumenti di terze parti con integrazione di Monitoraggio di Azure:
    * **Splunk**: per altre informazioni sull'integrazione dei log di Azure AD con Splunk, vedere [Integrare i log di Azure AD con Splunk usando Monitoraggio di Azure](tutorial-integrate-activity-logs-with-splunk.md).
    
    * **IBM QRadar**: il modulo DSM e Azure Event Hub Protocol sono disponibili per il download nel [sito Web del supporto IBM](http://www.ibm.com/support). Per altre informazioni sull'integrazione con Azure, visitare il sito [IBM QRadar Security Intelligence Platform 7.3.0](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
    
    * **Sumo Logic**: per configurare Sumo Logic per usare i dati da un hub eventi, vedere [Install the Azure AD app and view the dashboards](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) (Installare l'app Azure AD e visualizzare i dashboard). 

* **Configurare strumenti personalizzati**. Se lo strumento SIEM in uso non è ancora supportato nella diagnostica di Monitoraggio di Azure, è possibile configurare strumenti personalizzati usando le API di Hub eventi. Per altre informazioni, vedere la [Guida introduttiva alla ricezione di messaggi da un hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph).


## <a name="next-steps"></a>Passaggi successivi

* [Integrare i log di Azure AD con Splunk usando Monitoraggio di Azure](tutorial-integrate-activity-logs-with-splunk.md)
* [Integrate Azure AD logs with SumoLogic by using Azure Monitor](howto-integrate-activity-logs-with-sumologic.md) (Integrare i log di Azure AD con SumoLogic usando Monitoraggio di Azure)
* [Interpretare lo schema dei log di controllo in Monitoraggio di Azure](reference-azure-monitor-audit-log-schema.md)
* [Interpretare lo schema dei log di accesso in Monitoraggio di Azure](reference-azure-monitor-sign-ins-log-schema.md)
