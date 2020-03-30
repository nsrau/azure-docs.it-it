---
title: Configurare criteri di accesso condizionale in modalità solo report - Azure Active DirectoryConfigure a Conditional Access policy in report-only mode - Azure Active Directory
description: Utilizzo della modalità solo report nell'accesso condizionale per facilitare l'adozione
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d9bec829d7fb3e76eb243bda73423303670585e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295122"
---
# <a name="configure-a-conditional-access-policy-in-report-only-mode-preview"></a>Configurare un criterio di accesso condizionale in modalità solo report (anteprima)Configure a Conditional Access policy in report-only (Preview)

Per configurare un criterio di accesso condizionale in modalità solo report:To configure a Conditional Access policy in report-only mode:

> [!IMPORTANT]
> Se l'organizzazione non è già presente, [configurare l'integrazione](#set-up-azure-monitor-integration-with-azure-ad)di Monitoraggio di Azure con Azure AD. Questo processo deve aver luogo prima che i dati siano disponibili per la revisione.

1. Accedere al portale di **Azure** come amministratore di accesso condizionale, amministratore della sicurezza o amministratore globale.
1. Passare ad Accesso**condizionale**alla**sicurezza** > di **Azure Active Directory** > .
1. Selezionare **Nuovi criteri**.
1. Configurare le condizioni dei criteri e concedere i controlli necessari in base alle esigenze.
1. In **Abilita criteri** impostare l'interruttore sulla modalità Solo **report.**
1. Selezionare **Salva**

> [!TIP]
> È possibile modificare lo stato **Abilita stato dei criteri** di un criterio esistente da **Attivato** a **Solo report,** ma in questo modo verrà disabilitata l'applicazione dei criteri. 

Visualizzare il risultato solo report nei log di accesso di Azure AD.

Per visualizzare il risultato di un criterio solo report per un determinato accesso:

1. Accedere al portale di **Azure** come lettore di report, lettore di sicurezza, amministratore della sicurezza o amministratore globale.
1. Passare agli**accessi**di **Azure Active Directory** > .
1. Selezionare un accesso o aggiungere filtri per limitare i risultati.
1. Nel cassetto **Dettagli** selezionare la scheda **Solo report (anteprima)** per visualizzare i criteri valutati durante l'accesso.

> [!NOTE]
> Quando si scaricano i log di accesso, scegliere il formato JSON per includere i dati dei risultati di solo report di accesso condizionale.

## <a name="set-up-azure-monitor-integration-with-azure-ad"></a>Configurare l'integrazione di Monitoraggio di Azure con Azure ADSet up Azure Monitor integration with Azure AD

Per visualizzare l'impatto aggregato dei criteri di accesso condizionale usando la nuova cartella di lavoro Informazioni sull'accesso condizionale, è necessario integrare Monitoraggio di Azure con Azure AD ed esportare i log di accesso. Esistono due passaggi per configurare questa integrazione:There are two steps to set up this integration: 

1. Iscriversi a una sottoscrizione di [Azure Monitor e creare un'area di lavoro.](/azure/azure-monitor/learn/quick-create-workspace)
1. [Esportare i log](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)di accesso da Azure AD a Monitoraggio di Azure.

Ulteriori informazioni sui prezzi di Monitoraggio di Azure sono disponibili nella pagina Prezzi di [Monitoraggio di Azure.](https://azure.microsoft.com/pricing/details/monitor/) Le risorse per stimare i costi, impostare un limite giornaliero o personalizzare il periodo di conservazione dei dati sono disponibili nell'articolo [Gestire l'utilizzo e i costi con](../../azure-monitor/platform/manage-cost-storage.md#estimating-the-costs-to-manage-your-environment)i log di Monitoraggio di Azure.

## <a name="view-conditional-access-insights-workbook"></a>Visualizzare la cartella di lavoro informazioni dettagliate sull'accesso condizionaleView Conditional Access Insights workbook

Dopo aver integrato i log di Azure AD con Monitoraggio di Azure, è possibile monitorare l'impatto dei criteri di accesso condizionale usando le nuove cartelle di lavoro di informazioni dettagliate sull'accesso condizionale.

1. Accedere al portale di **Azure** come amministratore della sicurezza o amministratore globale.
1. Passare alle**cartelle di lavoro**di Azure Active **Directory** > .
1. Selezionare **Informazioni dettagliate sull'accesso condizionale (anteprima)**.
1. Selezionare uno o più criteri nell'elenco a discesa **Criteri di accesso condizionale.** Tutti i criteri abilitati sono selezionati per impostazione predefinita.
1. Selezionare un intervallo di tempo (se l'intervallo di tempo supera il set di dati disponibile, il report mostrerà tutti i dati disponibili). Dopo aver impostato i parametri **Criteri di accesso condizionale** e **Intervallo** di tempo, verrà caricato il report.
   1. Facoltativamente, cercare singoli **utenti** o **app** per restringere l'ambito del report.
1. Selezionare tra la visualizzazione dei dati nell'intervallo di tempo in base al numero di utenti o al numero di indirizzi inviti.
1. A seconda della **visualizzazione Dati**, il **Riepilogo impatto** visualizza il numero di utenti o di accesso nell'ambito dei parametri scelti, raggruppati per Numero totale, **Esito positivo**, **Errore**, Azione **utente richiesta**e Non **applicato**. Selezionare un riquadro per esaminare gli invii di un determinato tipo di risultato. 
   1. Se sono stati modificati i parametri della cartella di lavoro, è possibile scegliere di salvarne una copia per un utilizzo futuro. Selezionare l'icona di salvataggio nella parte superiore del report e specificare un nome e una posizione in cui salvare.
1. Scorrere verso il basso per visualizzare la suddivisione degli invii per ogni condizione.
1. Visualizzare i **dettagli** di accesso nella parte inferiore del report per analizzare i singoli eventi di accesso filtrati in base alle selezioni precedenti.

> [!TIP] 
> È necessario eseguire il drill-down in una query specifica o esportare i dettagli di accesso? Selezionare il pulsante a destra di qualsiasi query per aprire la query in Log Analytics. Selezionare Esporta per esportare in CSV o Power BI.

## <a name="common-problems-and-solutions"></a>Problemi e soluzioni comuni

### <a name="why-are-the-queries-in-the-workbook-failing"></a>Perché le query nella cartella di lavoro hanno esito negativo?

I clienti hanno notato che le query a volte non riescono se all'area di lavoro sono associate aree di lavoro errate o multiple. Per risolvere questo problema, fare clic su **Modifica** nella parte superiore della cartella di lavoro e quindi l'ingranaggio Impostazioni. Selezionare e quindi rimuovere le aree di lavoro non associate alla cartella di lavoro. Deve essere presente una sola area di lavoro associata a ogni cartella di lavoro.

### <a name="why-doesnt-the-conditional-access-policies-dropdown-parameter-contain-my-policies"></a>Perché il parametro a discesa Criteri di accesso condizionale non contiene criteri personali?

L'elenco a discesa Criteri di accesso condizionale viene popolato eseguendo una query sugli accessi più recenti per un periodo di 4 ore. Se un tenant non dispone di alcun accesso nelle ultime 4 ore, è possibile che l'elenco a discesa sia vuoto. Se questo ritardo è un problema persistente, ad esempio nei tenant di piccole dimensioni con accessi non frequenti, gli amministratori possono modificare la query per l'elenco a discesa Criteri di accesso condizionale ed estendere il tempo per la query a un tempo superiore a 4 ore.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di Accesso condizionaleConditional Access common policies](concept-conditional-access-policy-common.md)

Per altre informazioni sulle cartelle di lavoro di Azure AD, vedere l'articolo Come usare le cartelle di lavoro di Monitoraggio di Azure per i report di Azure Active Directory.For more information about Azure AD workbooks, see the article, [How to use Azure Monitor workbooks for Azure Active Directory reports.](../reports-monitoring/howto-use-azure-monitor-workbooks.md)
