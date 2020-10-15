---
title: Automatizzare i report personalizzati con i dati di Azure Application Insights
description: Automatizzare i report personalizzati giornalieri/settimanali/mensili con i dati di Azure Application Insights
ms.topic: conceptual
ms.date: 05/20/2019
ms.reviewer: sdash
ms.openlocfilehash: f35b7cbde7b719a91b23b19481ab704c445485cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91827735"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatizzare i report personalizzati con i dati di Azure Application Insights

I report periodici aiutano a mantenere aggiornati i team relativamente al funzionamento dei servizi aziendali critici. La produttività di sviluppatori, team DevOps/SRE e dei relativi manager può essere aumentata con report automatici che offrono informazioni cognitive dettagliate affidabili senza richiedere a tutti gli utenti l'accesso al portale. Questi report possono facilitare l'identificazione di aumenti graduali delle latenze, dei carichi e della frequenza degli errori che possono non attivare le regole di avviso.

Ogni azienda ha esigenze di report univoche, ad esempio:

* Specifiche aggregazioni di percentili delle metriche o metriche personalizzate in un report.
* Disporre di report diversi per riepiloghi giornalieri, settimanali e mensili dei dati per diverse tipologie di pubblico.
* Segmentazione in base ad attributi personalizzati, ad esempio area o ambiente.
* Raggruppare alcune risorse Al in un singolo report, anche se si trovano in diverse sottoscrizioni o gruppi di risorse ecc.
* Distinguere report contenenti metriche riservate inviati a un pubblico selezionato.
* Report destinati a stakeholder che non hanno accesso alle risorse del portale.

> [!NOTE] 
> L'invio di un digest settimanale con Application Insight non consentiva alcuna personalizzazione; verrà sospeso a favore delle opzioni personalizzate elencate di seguito. L'ultimo digest settimanale verrà inviato l'11 giugno 2018. Configurare una delle opzioni seguenti per ottenere report personalizzati simili (usare la query consigliata).

## <a name="to-automate-custom-report-emails"></a>Per automatizzare l'invio di report personalizzati

È possibile [eseguire una query a livello di codice sui dati di Application Insights](https://dev.applicationinsights.io/) per generare report personalizzati in base alla pianificazione scelta. Le opzioni seguenti consentono di essere subito operativi:

* [Automatizzare i report con Microsoft Flow](../platform/logicapp-flow-connector.md)
* [Automatizzare i report con App per la logica](automate-with-logic-apps.md)
* Usare il modello di [funzione di Azure](../../azure-functions/functions-create-first-azure-function.md) del digest pianificato di Application Insights nello scenario di monitoraggio. Questa funzione usa SendGrid per recapitare il messaggio di posta elettronica. 

    ![Modello di funzione di Azure](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Query di esempio per un digest di posta elettronica settimanale
La query seguente illustra l'unione di più set di dati in un report inviato come digest di posta elettronica settimanale. Può essere personalizzata in base alle esigenze e usata con le opzioni elencate in precedenza per automatizzare un report settimanale.

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

## <a name="application-insights-scheduled-digest-report"></a>Report digest pianificato di Application Insights

1. Creare un _app per le funzioni di Azure. (Application Insights è_ necessario solo se si vuole monitorare la nuova app per le funzioni con Application Insights)

   Vedere la documentazione di funzioni di Azure per informazioni su come [creare un'app](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app) per le funzioni

2. Dopo avere completato la distribuzione dell'app per le funzioni, selezionare **Vai alla risorsa **.

3. Selezionare **Nuova funzione**.

   ![Screenshot della creazione di una nuova funzione](./media/automate-custom-reports/new-function.png)

4. Selezionare il **_modello digest pianificato di Application Insights_**.

     > [!NOTE]
     > Per impostazione predefinita, le app per le funzioni vengono create con Runtime versione 3. x. È necessario fare [riferimento al runtime di funzioni di Azure versione](../../azure-functions/set-runtime-version.md) **1. x** per usare il modello di digest pianificato Application Insights. Passare a configurazione > impostazioni di runtime funzione per modificare la versione di Runtime. ![schermata di runtime](./media/automate-custom-reports/change-runtime-v.png)

   ![Screenshot del modello di Application Insights per la nuova app per le funzioni](./media/automate-custom-reports/function-app-04.png)

5. Immettere un indirizzo di posta elettronica del destinatario appropriato per il report e selezionare **Crea**.

   ![Screenshot delle impostazioni dell'app per le funzioni](./media/automate-custom-reports/scheduled-digest.png)

6. Selezionare la **Function App**  >  configurazione delle**funzionalità della piattaforma**app per le funzioni  >  **Configuration**.

    ![Screenshot delle impostazioni dell'app per le funzioni di Azure](./media/automate-custom-reports/config.png)

7. Creare tre nuove impostazioni dell'applicazione con i valori corrispondenti ``AI_APP_ID``, ``AI_APP_KEY`` e ``SendGridAPI`` appropriati. Selezionare **Salva**.

     ![Screenshot dell'interfaccia di integrazione della funzione](./media/automate-custom-reports/app-settings.png)
    
    I valori AI_ sono disponibili in Accesso all'API per la risorsa di Application Insights su cui si vuole creare il report. Se non si dispone di una chiave API di Application Insights, è disponibile un'opzione per **creare la chiave API**.
    
   * AI_APP_ID = Application ID
   * AI_APP_KEY = API Key
   * SendGridAPI =SendGrid API Key

     > [!NOTE]
     > Se non si dispone già di un account SendGrid, crearne uno. La documentazione di SendGrid per Funzioni di Azure è disponibile [qui ](../../azure-functions/functions-bindings-sendgrid.md). Per una spiegazione di base su come impostare SendGrid e generare una chiave API, leggere le informazioni in fondo a questo articolo. 

8. Selezionare **Integra** e in Output fare clic su **SendGrid ($return)**.

     ![Screenshot dell'output](./media/automate-custom-reports/integrate.png)

9. In **Impostazione app per la chiave API SendGrid** selezionare l'impostazione dell'app appena creata per **API SendGrid**.

     ![Screenshot dell'esecuzione dell'app per le funzioni](./media/automate-custom-reports/sendgrid-output.png)

10. Eseguire e testare l'app per le funzioni.

     ![Screenshot del test](./media/automate-custom-reports/function-app-11.png)

11. Controllare la posta elettronica per verificare che il messaggio sia stato inviato/ricevuto correttamente.

     ![Screenshot della riga dell'oggetto del messaggio di posta elettronica](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>SendGrid con Azure

Questi passaggi sono validi solo se non è già stato configurato un account SendGrid.

1. Dal portale di Azure selezionare **Crea una risorsa** > Cerca per il **recapito tramite posta elettronica SendGrid** > fare clic su **Crea** > compilare le istruzioni di creazione specifiche di SendGrid.

     ![Screenshot della creazione della risorsa SendGrid](./media/automate-custom-reports/sendgrid.png)

2. Dopo la creazione in Account SendGrid selezionare **Gestisci**.

     ![Screenshot delle chiavi API delle impostazioni](./media/automate-custom-reports/sendgrid-manage.png)

3. Verrà avviato il sito di SendGrid. Selezionare **Impostazioni**  >  **chiavi API**.

     ![Screenshot della creazione e visualizzazione dell'app delle chiavi API](./media/automate-custom-reports/function-app-15.png)

4. Creare una chiave API > scegliere **crea & visualizzazione**. Vedere la documentazione di SendGrid sull'accesso limitato per determinare il livello di autorizzazioni appropriato per la chiave API. In questo caso è selezionato l'accesso completo solo come esempio.

   ![Screenshot dell'accesso completo](./media/automate-custom-reports/function-app-16.png)

5. Copiare la chiave completa, che è il valore che serve nelle impostazioni dell'app per le funzioni come valore per SendGridAPI

   ![Screenshot della copia della chiave API](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla creazione di [query di Analisi](../log-query/get-started-queries.md).
* Altre informazioni sull'[esecuzione di query a livello di codice sui dati di Application Insights](https://dev.applicationinsights.io/)
* Altre informazioni su [App per la logica](../../logic-apps/logic-apps-overview.md).
* Scopri di più su [Microsoft Power Automatic](https://ms.flow.microsoft.com).

