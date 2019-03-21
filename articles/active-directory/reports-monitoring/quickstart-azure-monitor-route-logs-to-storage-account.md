---
title: 'Esercitazione: Archiviare i log di Azure Active Directory in un account di archiviazione (anteprima) | Microsoft Docs'
description: Informazioni su come configurare Diagnostica di Azure per eseguire il push dei log di Azure Active Directory a un account di archiviazione (anteprima)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8706e839005d43ab9fa2662d13d7d3c3994c136
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58077752"
---
# <a name="tutorial-archive-azure-ad-logs-to-an-azure-storage-account-preview"></a>Esercitazione: Archiviare i log di Azure AD in un account di archiviazione di Azure (anteprima)

In questa esercitazione viene descritto come configurare le impostazioni di diagnostica di Monitoraggio di Azure per instradare i log di Azure Active Directory (Azure AD) a un account di archiviazione di Azure.

## <a name="prerequisites"></a>Prerequisiti 

Per usare questa funzionalità, sono necessari:

* Una sottoscrizione di Azure con un account di archiviazione di Azure. Se non si ha una sottoscrizione di Azure, è possibile [iscriversi per ottenere una versione di valutazione gratuita](https://azure.microsoft.com/free/).
* Un tenant di Azure AD.
* Un utente con il ruolo di *amministratore globale* o *amministratore della sicurezza* per il tenant di Azure AD.

## <a name="archive-logs-to-an-azure-storage-account"></a>Archiviare i log in un account di archiviazione di Azure

1. Accedere al [portale di Azure](https://portal.azure.com). 

2. Selezionare **Azure Active Directory** > **Attività** > **Log di controllo**. 

3. Selezionare **Esporta impostazioni**. 

4. Nel riquadro **Impostazioni di diagnostica** eseguire una delle operazioni seguenti:
   * Per modificare le impostazioni esistenti, selezionare **Modifica l'impostazione**.
   * Per aggiungere nuove impostazioni, selezionare **Aggiungi impostazioni di diagnostica**.  
     È possibile avere fino a tre impostazioni. 

     ![Esportazione impostazioni](./media/quickstart-azure-monitor-route-logs-to-storage-account/ExportSettings.png)

5. Immettere un nome descrittivo per l'impostazione per ricordarsi del suo scopo (ad esempio *Invia ad account di archiviazione di Azure*). 

6. Selezionare la casella di controllo **Archivia in un account di archiviazione** e quindi selezionare **Account di archiviazione**. 

7. Selezionare la sottoscrizione di Azure e l'account di archiviazione a cui instradare i log.
 
8. Selezionare **OK** per chiudere la configurazione.

9. Eseguire una di queste operazioni o entrambe:
    * Per inviare i log di controllo all'account di archiviazione, selezionare la casella di controllo **AuditLogs**. 
    * Per inviare i log di accesso all'account di archiviazione, selezionare la casella di controllo **SignInLogs**.

10. Usare il dispositivo di scorrimento per impostare il periodo di conservazione per i dati dei log. Per impostazione predefinita, questo valore è *0*, che indica che i log vengono conservati nell'account di archiviazione per un periodo illimitato. Se si imposta un valore diverso, gli eventi meno recenti rispetto al numero di giorni selezionato vengono eliminati automaticamente.

11. Selezionare **Salva** per salvare l'impostazione.

    ![Impostazioni di diagnostica](./media/quickstart-azure-monitor-route-logs-to-storage-account/DiagnosticSettings.png)

12. Dopo circa 15 minuti, verificare che i log vengano inviati all'account di archiviazione. Passare al [portale di Azure](https://portal.azure.com), selezionare **Account di archiviazione**, selezionare l'account di archiviazione usato in precedenza e quindi selezionare **BLOB**. Per **Log di controllo**, selezionare **insights-log-audit**. Per **Log di accesso**, selezionare **insights-logs-signin**.

    ![Account di archiviazione](./media/quickstart-azure-monitor-route-logs-to-storage-account/StorageAccount.png)

## <a name="next-steps"></a>Passaggi successivi

* [Interpretare lo schema dei log di controllo in Monitoraggio di Azure](reference-azure-monitor-audit-log-schema.md)
* [Interpretare lo schema dei log di accesso in Monitoraggio di Azure](reference-azure-monitor-sign-ins-log-schema.md)
* [Domande frequenti e problemi noti](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
