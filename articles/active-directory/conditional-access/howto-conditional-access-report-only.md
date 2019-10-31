---
title: Configurare un criterio di accesso condizionale in modalità solo report-Azure Active Directory
description: Uso della modalità solo report nell'accesso condizionale per facilitare l'adozione
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3cd57c09c08ab4c86feeca27915639123b439d0
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180303"
---
# <a name="configure-a-conditional-access-policy-in-report-only-mode-preview"></a>Configurare un criterio di accesso condizionale in modalità solo report (anteprima)

Per configurare un criterio di accesso condizionale in modalità solo report:

1. Accedere al **portale di Azure** come amministratore di accesso condizionale, amministratore della sicurezza o amministratore globale.
1. Passare a **Azure Active Directory** **sicurezza** >  > **l'accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Configurare le condizioni dei criteri e i controlli di concessione richiesti in base alle esigenze.
1. In **Attiva criterio** impostare l'interruttore sulla modalità di **sola segnalazione** .
1. Selezionare **Salva**

> [!TIP]
> È possibile modificare lo stato di **Abilitazione** dei criteri di un criterio esistente da **on** a **solo report,** ma in questo modo si disabilita l'applicazione dei criteri. 

Visualizza i risultati di sola segnalazione nei log di accesso Azure AD.

Per visualizzare il risultato di un criterio di solo report per un particolare accesso:

1. Accedere al **portale di Azure** come un lettore di report, un lettore di sicurezza, un amministratore della sicurezza o un amministratore globale.
1. Passare a **Azure Active Directory** > **accessi**.
1. Selezionare un accesso o aggiungere i filtri per restringere i risultati.
1. Nel cassetto dei **Dettagli** selezionare la scheda **solo report (anteprima)** per visualizzare i criteri valutati durante l'accesso.

## <a name="set-up-azure-monitor-integration-with-azure-ad"></a>Configurare l'integrazione di monitoraggio di Azure con Azure AD

Per visualizzare l'effetto aggregato dei criteri di accesso condizionale usando la nuova cartella di lavoro di Insights per l'accesso condizionale, è necessario integrare monitoraggio di Azure con Azure AD ed esportare i log di accesso. Per impostare questa integrazione, è necessario eseguire due passaggi: 

1. [Iscriversi per ottenere una sottoscrizione di monitoraggio di Azure e creare un'area di lavoro](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).
1. [Esportare i log di accesso da Azure ad a monitoraggio di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

Altre informazioni sui prezzi di monitoraggio di Azure sono disponibili nella [pagina dei prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/). Le risorse per stimare i costi, impostare un limite giornaliero o personalizzare il periodo di conservazione dei dati sono disponibili nell'articolo [gestire l'utilizzo e i costi con i log di monitoraggio di Azure](../../azure-monitor/platform/manage-cost-storage.md#estimating-the-costs-to-manage-your-environment).

## <a name="view-conditional-access-insights-workbook"></a>Visualizza la cartella di lavoro delle informazioni dettagliate sull'accesso condizionale

Dopo aver integrato i log di Azure AD con monitoraggio di Azure, è possibile monitorare l'effetto dei criteri di accesso condizionale usando le nuove cartelle di lavoro di Insights per l'accesso condizionale.

1. Accedere al **portale di Azure** come amministratore della sicurezza o amministratore globale.
1. Individuare **Azure Active Directory** > **cartelle di lavoro**di.
1. Selezionare **informazioni dettagliate sull'accesso condizionale (anteprima)** .
1. Selezionare uno o più criteri dall'elenco a discesa **criteri di accesso condizionale** . Tutti i criteri abilitati sono selezionati per impostazione predefinita.
1. Selezionare un intervallo di tempo (se l'intervallo di tempo supera il set di dati disponibile, il report visualizzerà tutti i dati disponibili). Dopo aver impostato i parametri di **criteri di accesso condizionale** e **intervallo di tempo** , il report viene caricato.
   1. Facoltativamente, cercare singoli **utenti** o **app** per restringere l'ambito del report.
1. Consente di selezionare la visualizzazione dei dati nell'intervallo di tempo in base al numero di utenti o al numero di accessi.
1. A seconda della **visualizzazione dati**, il **Riepilogo di Impact** Visualizza il numero di utenti o accessi nell'ambito dei parametri scelti, raggruppati per numero totale, **esito positivo**, **errore**, **azione dell'utente richiesta**e **non applicato** . Selezionare un riquadro per esaminare gli accessi di un tipo di risultato specifico. 
   1. Se sono stati modificati i parametri della cartella di lavoro, è possibile scegliere di salvare una copia per un uso futuro. Selezionare l'icona Salva nella parte superiore del report e specificare un nome e un percorso in cui salvare.
1. Scorrere verso il basso per visualizzare la suddivisione degli accessi per ogni condizione.
1. Visualizzare i **Dettagli di accesso** nella parte inferiore del report per esaminare gli eventi di accesso singoli filtrati in base alle selezioni sopra riportate.

> [!TIP] 
> Devi eseguire il drill-down su una query specifica o esportare i dettagli di accesso? Selezionare il pulsante a destra di una query per aprire la query in Log Analytics. Selezionare Esporta per esportare in CSV o Power BI.

## <a name="common-problems-and-solutions"></a>Problemi comuni e soluzioni

### <a name="why-are-the-queries-in-the-workbook-failing"></a>Perché le query nella cartella di lavoro hanno esito negativo?

I clienti hanno notato che talvolta le query hanno esito negativo se la cartella di lavoro è associata a una o più aree di lavoro errate. Per risolvere il problema, fare clic su **modifica** nella parte superiore della cartella di lavoro e quindi sull'ingranaggio impostazioni. Selezionare e rimuovere le aree di lavoro non associate alla cartella di lavoro. Deve essere presente una sola area di lavoro associata a ogni cartella di lavoro.

### <a name="why-doesnt-the-conditional-access-policies-dropdown-parameter-contain-my-policies"></a>Perché il parametro elenco a discesa dei criteri di accesso condizionale non contiene i criteri?

L'elenco a discesa Criteri di accesso condizionale viene popolato eseguendo una query sugli accessi più recenti in un periodo di 4 ore. Se un tenant non ha accessi nelle ultime 4 ore, è possibile che l'elenco a discesa sia vuoto. Se questo ritardo è un problema persistente, ad esempio in tenant di piccole dimensioni con accessi non frequenti, gli amministratori possono modificare la query per l'elenco a discesa Criteri di accesso condizionale ed estendere l'intervallo di tempo per la query a un tempo più lungo di 4 ore.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di accesso condizionale](concept-conditional-access-policy-common.md)

Per altre informazioni sulle cartelle di lavoro di Azure AD, vedere l'articolo [come usare le cartelle di lavoro di monitoraggio di Azure per i report Azure Active Directory](../reports-monitoring/howto-use-azure-monitor-workbooks.md).
