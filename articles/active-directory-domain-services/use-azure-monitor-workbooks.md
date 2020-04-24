---
title: Usare le cartelle di lavoro di monitoraggio di Azure con Azure AD Domain Services | Microsoft Docs
description: Informazioni su come usare le cartelle di lavoro di monitoraggio di Azure per esaminare i controlli di sicurezza e comprendere i problemi in un Azure Active Directory Domain Services dominio gestito.
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
# <a name="review-security-audit-events-in-azure-ad-domain-services-using-azure-monitor-workbooks"></a>Esaminare gli eventi di controllo di sicurezza in Azure AD Domain Services usando le cartelle di lavoro di monitoraggio di Azure

Per comprendere lo stato del dominio gestito di Azure Active Directory Domain Services (Azure AD DS), è possibile abilitare gli eventi di controllo di sicurezza. Questi eventi di controllo di sicurezza possono essere quindi esaminati usando le cartelle di lavoro di monitoraggio di Azure che combinano testo, query di analisi e parametri in report interattivi avanzati. Azure AD DS include modelli di cartella di lavoro per la panoramica della sicurezza e l'attività dell'account che consentono di analizzare gli eventi di controllo e di gestire l'ambiente.

Questo articolo illustra come usare le cartelle di lavoro di monitoraggio di Azure per esaminare gli eventi di controllo di sicurezza in Azure AD DS.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, completare l'esercitazione per [creare e configurare un'istanza di Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Eventi del controllo di sicurezza abilitati per il dominio gestito Azure Active Directory Domain Services che trasmettono i dati in un'area di lavoro Log Analytics.
    * Se necessario, [abilitare i controlli di sicurezza per Azure Active Directory Domain Services][enable-security-audits].

## <a name="azure-monitor-workbooks-overview"></a>Panoramica di cartelle di lavoro di monitoraggio di Azure

Quando gli eventi di controllo di sicurezza sono attivati in Azure AD DS, può essere difficile analizzare e identificare i problemi nel dominio gestito. Monitoraggio di Azure consente di aggregare questi eventi di controllo di sicurezza ed eseguire query sui dati. Con le cartelle di lavoro di monitoraggio di Azure, è possibile visualizzare questi dati per rendere più veloce e facile identificare i problemi.

I modelli di cartella di lavoro sono report curati progettati per un riutilizzo flessibile da parte di più utenti e team. Quando si apre un modello di cartella di lavoro, vengono caricati i dati dell'ambiente di monitoraggio di Azure. È possibile utilizzare i modelli senza alcun effetto su altri utenti dell'organizzazione e salvare le proprie cartelle di lavoro in base al modello.

Azure AD DS include i due modelli di cartella di lavoro seguenti:

* Report Panoramica sicurezza
* Report attività account

Per altre informazioni su come modificare e gestire le cartelle di lavoro, vedere [Cenni preliminari sulle cartelle di lavoro di monitoraggio di Azure](../azure-monitor/platform/workbooks-overview.md).

## <a name="use-the-security-overview-report-workbook"></a>Utilizzare la cartella di lavoro report Panoramica sicurezza

Per comprendere meglio l'utilizzo e identificare potenziali minacce per la sicurezza, il report Panoramica della sicurezza riepiloga i dati di accesso e identifica gli account che si desidera controllare. È possibile visualizzare gli eventi in un determinato intervallo di date ed eseguire il drill-down in eventi di accesso specifici, ad esempio i tentativi di accesso con password errata o la posizione in cui l'account è stato disabilitato.

Per accedere al modello di cartella di lavoro per il report Panoramica sicurezza, attenersi alla procedura seguente:

1. Cercare e selezionare **Azure Active Directory Domain Services** nell'portale di Azure.
1. Selezionare il dominio gestito, ad esempio *aaddscontoso.com*
1. Dal menu sul lato sinistro scegliere **monitoraggio > cartelle di lavoro**

    ![Selezionare l'opzione di menu cartelle di lavoro nella portale di Azure](./media/use-azure-monitor-workbooks/select-workbooks-in-azure-portal.png)

1. Scegliere il **report Panoramica sicurezza**.
1. Dai menu a discesa nella parte superiore della cartella di lavoro selezionare la sottoscrizione di Azure e quindi l'area di lavoro Monitoraggio di Azure. Scegliere un **intervallo di tempo**, ad esempio gli *ultimi 7 giorni*.

    ![Selezionare l'opzione di menu cartelle di lavoro nella portale di Azure](./media/use-azure-monitor-workbooks/select-query-filters.png)

    È anche possibile modificare le opzioni **visualizzazione affiancata** e **visualizzazione grafico** per analizzare e visualizzare i dati nel modo desiderato.

1. Per eseguire il drill-down in un tipo di evento specifico, selezionare una delle schede dei **risultati di accesso** , ad esempio *account bloccato*, come illustrato nell'esempio seguente:

    ![Panoramica della sicurezza di esempio visualizzazione dei dati del report in cartelle di lavoro di monitoraggio di Azure](./media/use-azure-monitor-workbooks/example-security-overview-report.png)

1. Nella parte inferiore del report sulla panoramica della sicurezza sotto il grafico si interrompe quindi il tipo di attività selezionato. È possibile filtrare in base ai nomi utente interessati sul lato destro, come illustrato nel report di esempio seguente:

    [![](./media/use-azure-monitor-workbooks/account-lockout-details-cropped.png "Details of account lockouts in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-lockout-details.png#lightbox)

## <a name="use-the-account-activity-report-workbook"></a>Utilizzare la cartella di lavoro report attività account

Per semplificare la risoluzione dei problemi relativi a un account utente specifico, il report attività account suddivide le informazioni dettagliate sul registro eventi di controllo. È possibile verificare quando è stato fornito un nome utente o una password non valida durante l'accesso e l'origine del tentativo di accesso.

Per accedere al modello di cartella di lavoro per il report attività account, completare i passaggi seguenti:

1. Cercare e selezionare **Azure Active Directory Domain Services** nell'portale di Azure.
1. Selezionare il dominio gestito, ad esempio *aaddscontoso.com*
1. Dal menu sul lato sinistro scegliere **monitoraggio > cartelle di lavoro**
1. Scegliere il **report attività account**.
1. Dai menu a discesa nella parte superiore della cartella di lavoro selezionare la sottoscrizione di Azure e quindi l'area di lavoro Monitoraggio di Azure. Scegliere un **intervallo di tempo**, ad esempio gli *ultimi 30 giorni*, quindi come si desidera che la **visualizzazione affiancata** rappresenti i dati. È possibile filtrare in base al **nome utente dell'account**, ad esempio *Felix*, come illustrato nel report di esempio seguente:

    [![](./media/use-azure-monitor-workbooks/account-activity-report-cropped.png "Account activity report in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-activity-report.png#lightbox)

    L'area sotto il grafico Mostra gli eventi di accesso singoli insieme a informazioni come il risultato dell'attività e la workstation di origine. Queste informazioni consentono di determinare le origini ripetute degli eventi di accesso che possono causare blocchi degli account o indicare un potenziale attacco.

Come nel rapporto Panoramica sicurezza, è possibile eseguire il drill-down nei diversi riquadri nella parte superiore del report per visualizzare e analizzare i dati in base alle esigenze.

## <a name="save-and-edit-workbooks"></a>Salvare e modificare le cartelle di lavoro

Le due cartelle di lavoro dei modelli fornite da Azure AD DS sono una posizione ideale per iniziare con l'analisi dei dati. Se è necessario ottenere una maggiore granularità nelle query e nelle indagini sui dati, è possibile salvare le cartelle di lavoro e modificare le query.

1. Per salvare una copia di uno dei modelli di cartella di lavoro, selezionare **modifica > Salva come > report condivisi**, quindi specificare un nome e salvarlo.
1. Dalla propria copia del modello, selezionare **modifica** per accedere alla modalità di modifica. È possibile scegliere il pulsante **modifica** blu accanto a qualsiasi parte del report e modificarlo.

Tutti i grafici e le tabelle nelle cartelle di lavoro di monitoraggio di Azure vengono generati usando le query kusto. Per altre informazioni sulla creazione di query personalizzate, vedere l'esercitazione sulle query [log di monitoraggio di Azure][azure-monitor-queries] e sulle [query kusto][kusto-queries].

## <a name="next-steps"></a>Passaggi successivi

Se è necessario modificare i criteri per le password e il blocco, vedere [criteri di blocco di password e account nei domini gestiti][password-policy].

Per i problemi relativi agli utenti, informazioni su come risolvere i problemi di [accesso dell'account][troubleshoot-sign-in] o i [problemi di blocco degli account][troubleshoot-account-lockout].

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
