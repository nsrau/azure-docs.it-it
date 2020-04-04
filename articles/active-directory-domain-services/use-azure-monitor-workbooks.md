---
title: Usare Le cartelle di lavoro di Monitoraggio di Azure con Servizi di dominio Azure AD Documenti Microsoft
description: Informazioni su come usare le cartelle di lavoro di Monitoraggio di Azure per esaminare i controlli di sicurezza e comprendere i problemi in un dominio gestito di Servizi di dominio Azure Active Directory.Learn how to use Azure Monitor Workbooks to review security audits and understand issues in an Azure Active Directory Domain Services managed domain.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/18/2020
ms.author: iainfou
ms.openlocfilehash: bdfc7d37d99dc5511f47e33d1848c3f142a9693e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654462"
---
# <a name="review-security-audit-events-in-azure-ad-domain-services-using-azure-monitor-workbooks"></a>Esaminare gli eventi di controllo di sicurezza in Servizi di dominio Azure AD usando le cartelle di lavoro di Monitoraggio di AzureReview security audit events in Azure AD Domain Services using Azure Monitor Workbooks

Per comprendere lo stato del dominio gestito di Servizi di dominio Azure Active Directory (Azure AD DS), è possibile abilitare gli eventi di controllo di sicurezza. Questi eventi di controllo di sicurezza possono quindi essere esaminati usando cartelle di lavoro di Monitoraggio di Azure che combinano testo, query di analisi e parametri in report interattivi avanzati. Servizi di dominio Active Directory di Azure include modelli di cartella di lavoro per la panoramica della sicurezza e l'attività dell'account che consentono di esaminare gli eventi di controllo e gestire l'ambiente.

Questo articolo illustra come usare le cartelle di lavoro di Monitoraggio di Azure per esaminare gli eventi di controllo di sicurezza in Servizi di dominio Active Directory di Azure.This article shows you how to use Azure Monitor Workbooks to review security audit events in Azure AD DS.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, sono necessarie le risorse e i privilegi seguenti:To complete this article, you need the following resources and privileges:

* Una sottoscrizione di Azure attiva.
    * Se non si dispone di una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, completare l'esercitazione per [creare e configurare un'istanza][create-azure-ad-ds-instance]di Servizi di dominio Azure Active Directory.
* Eventi di controllo di sicurezza abilitati per il dominio gestito di Servizi di dominio Azure Active Directory che trasmettono dati in un'area di lavoro di Log Analytics.Security audit events enabled for your Azure Active Directory Domain Services managed domain that stream data to a Log Analytics workspace.
    * Se necessario, abilitare i controlli di sicurezza per Servizi di [dominio Azure Active Directory.][enable-security-audits]

## <a name="azure-monitor-workbooks-overview"></a>Panoramica di Monitoraggio di Azure

Quando gli eventi di controllo di sicurezza vengono attivati in Servizi di dominio Active Directory di Azure, può essere difficile analizzare e identificare i problemi nel dominio gestito. Monitoraggio di Azure consente di aggregare questi eventi di controllo di sicurezza ed eseguire query sui dati. Con Le cartelle di lavoro di Monitoraggio di Azure è possibile visualizzare questi dati per semplificare e velocità l'identificazione dei problemi.

I modelli di cartella di lavoro sono report selezionati progettati per un riutilizzo flessibile da parte di più utenti e team. Quando si apre un modello di cartella di lavoro, vengono caricati i dati dall'ambiente di Monitoraggio di Azure.When you open a workbook template, the data from your Azure Monitor environment is loaded. È possibile utilizzare i modelli senza influire sugli altri utenti dell'organizzazione e salvare le proprie cartelle di lavoro in base al modello.

Servizi di dominio Active Directory di Azure include i due modelli di cartella di lavoro seguenti:Azure AD DS includes the following two workbook templates:

* Report Panoramica sicurezza
* Report attività account

Per altre informazioni su come modificare e gestire le cartelle di lavoro, vedere Panoramica delle cartelle di lavoro di Monitoraggio di Azure.For more information about how to edit and manage [workbooks,](../azure-monitor/platform/workbooks-overview.md)see Azure Monitor Workbooks overview .

## <a name="use-the-security-overview-report-workbook"></a>Usare la cartella di lavoro del report Panoramica sicurezza

Per comprendere meglio l'utilizzo e identificare potenziali minacce alla sicurezza, il report Panoramica sicurezza riepiloga i dati di accesso e identifica gli account che potresti voler controllare. È possibile visualizzare gli eventi in un determinato intervallo di date ed eseguire il drill-down in eventi di accesso specifici, ad esempio tentativi con password errata o in cui l'account è stato disabilitato.

La procedura seguente illustra come accedere al modello di cartella di lavoro per il report Panoramica sicurezza:

1. Cercare e selezionare Servizi di dominio Azure Active Directory nel portale di Azure.Search for and select **Azure Active Directory Domain Services** in the Azure portal.
1. Selezionare il dominio gestito, ad esempio *aaddscontoso.com*
1. Dal menu a sinistra, scegliere **Monitoraggio > cartelle di lavoro**

    ![Selezionare l'opzione di menu Cartelle di lavoro nel portale di AzureSelect the Workbooks menu option in the Azure portal](./media/use-azure-monitor-workbooks/select-workbooks-in-azure-portal.png)

1. Scegliere il **report Panoramica sicurezza**.
1. Dai menu a discesa nella parte superiore della cartella di lavoro selezionare la sottoscrizione di Azure e quindi l'area di lavoro di Monitoraggio di Azure.From the drop-down menus on the top of the workbook, select your Azure subscription and then Azure Monitor workspace. Scegliere un **intervallo**di tempo , ad esempio *Ultimi 7 giorni*.

    ![Selezionare l'opzione di menu Cartelle di lavoro nel portale di AzureSelect the Workbooks menu option in the Azure portal](./media/use-azure-monitor-workbooks/select-query-filters.png)

    Le **opzioni di visualizzazione Affianca** e **Grafico** possono anche essere modificate per analizzare e visualizzare i dati come desiderato

1. Per eseguire il drill-down in un tipo di evento specifico, selezionare una delle schede dei risultati di **accesso,** ad esempio *Account bloccato,* come illustrato nell'esempio seguente:

    ![Esempio di panoramica della sicurezza Dati del report visualizzati nelle cartelle di lavoro di Monitoraggio di AzureExample Security Overview Report data visualized in Azure Monitor Workbooks](./media/use-azure-monitor-workbooks/example-security-overview-report.png)

1. La parte inferiore del report di panoramica della sicurezza sotto il grafico suddivide quindi il tipo di attività selezionato. È possibile filtrare in base ai nomi utente coinvolti sul lato destro, come illustrato nel report di esempio seguente:You can filter by usernames involved on the right-hand side, as shown in the following example report:

    [![](./media/use-azure-monitor-workbooks/account-lockout-details-cropped.png "Details of account lockouts in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-lockout-details.png#lightbox)

## <a name="use-the-account-activity-report-workbook"></a>Utilizzare la cartella di lavoro del report Attività account

Per facilitare la risoluzione dei problemi relativi a un account utente specifico, il rapporto sull'attività dell'account suddivide le informazioni dettagliate del registro eventi di controllo. È possibile verificare quando è stato fornito un nome utente o una password non valida durante l'accesso e l'origine del tentativo di accesso.

Per accedere al modello di cartella di lavoro per il report attività account, completare i passaggi seguenti:

1. Cercare e selezionare Servizi di dominio Azure Active Directory nel portale di Azure.Search for and select **Azure Active Directory Domain Services** in the Azure portal.
1. Selezionare il dominio gestito, ad esempio *aaddscontoso.com*
1. Dal menu a sinistra, scegliere **Monitoraggio > cartelle di lavoro**
1. Scegliere il **Rapporto attività account**.
1. Dai menu a discesa nella parte superiore della cartella di lavoro selezionare la sottoscrizione di Azure e quindi l'area di lavoro di Monitoraggio di Azure.From the drop-down menus on the top of the workbook, select your Azure subscription and then Azure Monitor workspace. Scegliere un **intervallo**di tempo , ad esempio *Ultimi 30 giorni*, quindi come si desidera che la **visualizzazione Affiancata** rappresenti i dati. È possibile filtrare in base **al nome utente dell'account**, ad esempio *felix*, come illustrato nel seguente report di esempio:

    [![](./media/use-azure-monitor-workbooks/account-activity-report-cropped.png "Account activity report in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-activity-report.png#lightbox)

    L'area sotto il grafico mostra singoli eventi di accesso insieme a informazioni quali il risultato dell'attività e la workstation di origine. Queste informazioni consentono di determinare le origini ripetute di eventi di accesso che possono causare blocchi degli account o indicare un potenziale attacco.

Come per il report di panoramica della sicurezza, è possibile eseguire il drill-down nei diversi riquadri nella parte superiore del report per visualizzare e analizzare i dati in base alle esigenze.

## <a name="save-and-edit-workbooks"></a>Salvare e modificare cartelle di lavoro

Le due cartelle di lavoro modello fornite da Servizi di dominio Active Directory di Azure sono un buon punto di partenza con l'analisi dei dati. Se è necessario ottenere un maggiore granulare nelle query di dati e nelle indagini, è possibile salvare le proprie cartelle di lavoro e modificare le query.

1. Per salvare una copia di uno dei modelli di cartella di lavoro, selezionare **Modifica > Salva come > report condivisi**, quindi specificare un nome e salvarlo.
1. Dalla copia del modello, selezionare **Modifica** per attivare la modalità di modifica. È possibile scegliere il pulsante blu **Modifica** accanto a qualsiasi parte del report e modificarlo.

Tutti i grafici e le tabelle nelle cartelle di lavoro di Monitoraggio di Azure vengono generati tramite query Kusto.All of the charts and tables in Azure Monitor Workbooks are generated using Kusto queries. Per altre informazioni sulla creazione di query personalizzate, vedere Esercitazione sulle query di log di [Azure Monitor][azure-monitor-queries] e Sulle query [Kusto][kusto-queries].

## <a name="next-steps"></a>Passaggi successivi

Se è necessario modificare i criteri di password e blocco, vedere Criteri di blocco di [password e account per i domini gestiti][password-policy].

Per i problemi con gli utenti, scopri come risolvere i problemi di [accesso dell'account][troubleshoot-sign-in] o i problemi di [blocco dell'account.][troubleshoot-account-lockout]

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-security-audits]: security-audit-events.md
[password-policy]: password-policy.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-monitor-queries]: ../azure-monitor/log-query/query-language.md
[kusto-queries]: https://docs.microsoft.com/azure/kusto/query/tutorial?pivots=azuredataexplorer
