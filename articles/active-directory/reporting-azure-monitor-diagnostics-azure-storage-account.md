---
title: 'Esercitazione: Archiviare i log di Azure Active Directory in un account di archiviazione di Azure (anteprima) | Microsoft Docs'
description: Informazioni su come configurare Diagnostica di Azure per eseguire il push dei log di Azure Active Directory a un account di archiviazione (anteprima)
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
ms.openlocfilehash: ea26da91e2a0b88e8afa54a4210b7a2365e4949b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240244"
---
# <a name="tutorial-archive-azure-active-directory-logs-to-an-azure-storage-account-preview"></a>Esercitazione: Archiviare i log di Azure Active Directory in un account di archiviazione di Azure (anteprima)

In questa esercitazione verrà descritto come configurare le impostazioni di diagnostica di Monitoraggio di Azure per trasmettere i log di Azure Active Directory a un account di archiviazione di Azure.

## <a name="prerequisites"></a>Prerequisiti 

È necessario:

* Una sottoscrizione di Azure con un account di archiviazione di Azure. Se non si ha una sottoscrizione di Azure, è possibile [iscriversi per ottenere una versione di valutazione gratuita](https://azure.microsoft.com/free/).
* Un tenant di Azure Active Directory.
* Un utente con il ruolo di amministratore globale o amministratore della sicurezza per il tenant.

## <a name="archive-logs-to-an-azure-storage-account"></a>Archiviare i log in un account di archiviazione di Azure

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Fare clic su **Azure Active Directory** -> **Attività** -> **Log di controllo**. 
3. Fare clic su **Impostazioni di esportazione** per aprire il pannello Impostazioni di diagnostica. Fare clic su **Modifica l'impostazione** se si vuole modificare le impostazioni esistenti oppure fare clic su **Add diagnostic setting** (Aggiungi impostazione di diagnostica) per aggiungerne una nuova. È possibile avere un massimo di tre impostazioni. 
    ![Impostazioni di esportazione](./media/reporting-azure-monitor-diagnostics-azure-storage-account/ExportSettings.png "Impostazioni di esportazione")

4. Aggiungere un nome descrittivo per l'impostazione, che ne descriva la funzione. Ad esempio, "Invia all'account di archiviazione di Azure". 
5. Selezionare la casella di controllo **Archivia in un account di archiviazione** e fare clic su **Account di archiviazione** per scegliere l'account di archiviazione di Azure. 
6. Selezionare una sottoscrizione di Azure e un account di archiviazione a cui inviare i log, quindi fare clic su **OK** per completare la configurazione.
7. Selezionare la casella di controllo**AuditLogs** (Log di controllo) per inviare i log di controllo all'account di archiviazione. 
8. Selezionare la casella di controllo**SignInLogs** (Log di accesso) per inviare i log di accesso all'account di archiviazione.
9. Usare il dispositivo di scorrimento per impostare la conservazione per i dati dei log. Per impostazione predefinita, questo valore è "0" e i log verranno mantenuti nell'account di archiviazione per un periodo illimitato. In alternativa, è possibile impostare un valore e verrà automaticamente eseguita la pulizia degli eventi meno recenti rispetto al numero di giorni selezionato.
10. Fare clic su **Salva** per salvare l'impostazione.
    ![Impostazioni di diagnostica](./media/reporting-azure-monitor-diagnostics-azure-storage-account/DiagnosticSettings.png "Impostazioni di diagnostica")

11. Dopo circa 15 minuti, verificare che i log vengano inviati all'account di archiviazione. Passare al portale di Azure, fare clic su **Account di archiviazione**, scegliere l'account di archiviazione usato in precedenza e fare clic su **BLOB**. 
12. Per **Log di controllo**, fare clic su **insights-log-audit**. Per **Log di accesso**, fare clic su **insights-logs-signin**.
    ![Account di archiviazione](./media/reporting-azure-monitor-diagnostics-azure-storage-account/StorageAccount.png "Account di archiviazione")

## <a name="next-steps"></a>Passaggi successivi

* [Interpretare lo schema del log di controllo in Monitoraggio di Azure](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Interpretare lo schema dei log di accesso in Monitoraggio di Azure](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [Domande frequenti e problemi noti](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)