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
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: c4f5bf1b49d7f59b2bb938a7ddc53b96c1d354ef
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240218"
---
# <a name="tutorial-stream-azure-active-directory-logs-to-an-azure-event-hub-preview"></a>Esercitazione: Trasmettere i log di Azure Active Directory a un hub eventi di Azure (anteprima)

In questa esercitazione verrà descritto come configurare le impostazioni di diagnostica di Monitoraggio di Azure per trasmettere i log di Azure Active Directory a un hub eventi di Azure. Questo meccanismo consente di integrare i log con strumenti SIEM di terze parti, quali Splunk e QRadar.

## <a name="prerequisites"></a>Prerequisiti 

È necessario:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile [iscriversi per ottenere una versione di valutazione gratuita](https://azure.microsoft.com/free/).
* Tenant di Azure Active Directory
* Un utente con il ruolo di amministratore globale o amministratore della sicurezza per il tenant
* Uno spazio dei nomi di Hub eventi e un hub eventi nella sottoscrizione di Azure. Informazioni su come [crearne uno](https://docs.microsoft.com/azure/event-hubs/event-hubs-create.md).

## <a name="archive-logs-to-event-hub"></a>Archiviare i log in un hub eventi

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Fare clic su **Azure Active Directory** -> **Attività** -> **Log di controllo**. 
3. Fare clic su **Impostazioni di esportazione** per aprire il pannello Impostazioni di diagnostica. Fare clic su **Modifica l'impostazione** se si vuole modificare le impostazioni esistenti oppure fare clic su **Add diagnostic setting** (Aggiungi impostazione di diagnostica) per aggiungerne una nuova. È possibile avere fino a tre impostazioni. 
    ![Impostazioni di esportazione](./media/reporting-azure-monitor-diagnostics-azure-event-hub/ExportSettings.png "Impostazioni di esportazione")

4. Selezionare la casella di controllo **Stream to an event hub** (Esegui streaming in un Hub eventi) e fare clic su **Hub eventi/Configura**.
5. Selezionare una sottoscrizione di Azure e lo spazio dei nomi di Hub eventi a cui inviare log. Sia la sottoscrizione che lo spazio dei nomi di Hub eventi devono essere associati al tenant di Active Directory da cui vengono trasmessi i log. È anche possibile specificare un hub eventi nello spazio dei nomi di Hub eventi a cui devono essere inviati i log. Se non viene specificato alcun hub eventi, verrà creato un hub eventi nello spazio dei nomi con il nome predefinito **insights-logs-audit**.
6. Fare clic su **OK** per chiudere la configurazione degli hub eventi.
7. Selezionare la casella di controllo**AuditLogs** (Log di controllo) per inviare i log di controllo all'account di archiviazione. 
8. Selezionare la casella di controllo**SignInLogs** (Log di accesso) per inviare i log di accesso all'account di archiviazione.
9. Fare clic su **Salva** per salvare l'impostazione.
    ![Impostazioni di diagnostica](./media/reporting-azure-monitor-diagnostics-azure-event-hub/DiagnosticSettings.png "Impostazioni di diagnostica")

10. Dopo circa 15 minuti, verificare che gli eventi vengano visualizzati nell'hub eventi. A tale scopo, passare all'hub eventi dal portale e verificare che il conteggio dei **messaggi in arrivo** sia maggiore di zero. 
    ![Log di controllo](./media/reporting-azure-monitor-diagnostics-azure-event-hub/InsightsLogsAudit.png "Log di controllo")


## <a name="access-data-from-event-hubs"></a>Accedere ai dati da Hub eventi

Una volta che i dati sono visualizzati nell'hub eventi, è possibile accedervi in due modi.

* **Configurare uno strumento SIEM supportato per la lettura dei dati**: per leggere i dati dall'hub eventi, quasi tutti gli strumenti richiedono la stringa di connessione dell'hub eventi e determinate autorizzazioni di accesso alla sottoscrizione di Azure. Di seguito è riportato un elenco non esaustivo di strumenti con Monitoraggio di Azure integrato:
    1. **Splunk**: per altre informazioni sulla procedura di integrazione dei log di Azure AD con Splunk, vedere [Come integrare i log di Azure Active Directory con Splunk usando Monitoraggio di Azure](reporting-azure-monitor-diagnostics-splunk-integration.md).
    
    2. **IBM QRadar**: Microsoft Azure DSM e Microsoft Azure Event Hub Protocol sono scaricabili dal [sito Web del supporto IBM](http://www.ibm.com/support). [Altre informazioni sull'integrazione con Azure sono disponibili qui](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
    
    3. **SumoLogic**: seguire [queste istruzioni](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub) per configurare SumoLogic per l'uso dei dati di un hub eventi. 

* **Configurare strumenti personalizzati per la lettura dei dati**: se lo strumento SIEM in uso non è ancora supportato nella diagnostica di Monitoraggio di Azure, è possibile configurare strumenti personalizzati usando le API di Hub eventi. Per altre informazioni, vedere le [API di Hub eventi](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph).


## <a name="next-steps"></a>Passaggi successivi

* [Integrare i log di Azure Active Directory con Splunk usando Monitoraggio di Azure](reporting-azure-monitor-diagnostics-splunk-integration.md)
* [Interpretare lo schema dei log di controllo nella diagnostica di Monitoraggio di Azure](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Interpretare lo schema dei log di accesso nella diagnostica di Monitoraggio di Azure](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)