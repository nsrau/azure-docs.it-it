---
title: Automatizzare i report personalizzati con i dati di Azure Application Insights
description: Automatizzare i report personalizzati giornalieri/settimanali/mensili con i dati di Azure Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/20/2019
ms.reviewer: sdash
ms.openlocfilehash: 2b23374972a071421b59bedf0eb5b9358b37d7a9
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677618"
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

* [Automatizzare i report con Microsoft Flow](automate-with-flow.md)
* [Automatizzare i report con App per la logica](automate-with-logic-apps.md)
* Usare il modello di [funzione di Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) del digest pianificato di Application Insights nello scenario di monitoraggio. Questa funzione usa SendGrid per recapitare il messaggio di posta elettronica. 

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

1. Nel portale di Azure selezionare **Crea una risorsa** > **Calcolo** > **App per le funzioni**.

   ![Screenshot della creazione di un'app per le funzioni delle risorse di Azure](./media/automate-custom-reports/function-app-01.png)

2. Immettere le informazioni appropriate per l'app e scegliere _Crea_. È necessario che Application Insights sia _attivo_ solo se si vuole monitorare la nuova app per le funzioni con Application Insights.

   ![Screenshot della creazione delle impostazioni di un'app per le funzioni delle risorse di Azure](./media/automate-custom-reports/function-app-02.png)

3. Dopo avere completato la distribuzione dell'app per le funzioni, selezionare **Vai alla risorsa** .

4. Selezionare **Nuova funzione**.

   ![Screenshot della creazione di una nuova funzione](./media/automate-custom-reports/function-app-03.png)

5. Selezionare il **_modello digest pianificato di Application Insights_** .

     > [!NOTE]
     > Per impostazione predefinita, le app per le funzioni vengono create con la versione 2. x del runtime. È necessario fare [riferimento al runtime di funzioni di Azure versione](https://docs.microsoft.com/azure/azure-functions/set-runtime-version) **1. x** per usare il modello di digest pianificato Application Insights.  ![runtime screenshot ](./../../../includes/media/functions-view-update-version-portal/function-app-view-version.png)



   ![Screenshot del modello di Application Insights per la nuova app per le funzioni](./media/automate-custom-reports/function-app-04.png)

6. Immettere un indirizzo di posta elettronica del destinatario appropriato per il report e selezionare **Crea**.

   ![Screenshot delle impostazioni dell'app per le funzioni](./media/automate-custom-reports/function-app-05.png)

7. Selezionare **App per le funzioni** > **Funzionalità della piattaforma** > **Impostazioni applicazione**.

    ![Screenshot delle impostazioni dell'app per le funzioni di Azure](./media/automate-custom-reports/function-app-07.png)

8. Creare tre nuove impostazioni dell'applicazione con i valori corrispondenti ``AI_APP_ID``, ``AI_APP_KEY`` e ``SendGridAPI`` appropriati. Selezionare **Salva**.

     ![Screenshot dell'interfaccia di integrazione della funzione](./media/automate-custom-reports/function-app-08.png)
    
    I valori AI_ sono disponibili in Accesso all'API per la risorsa di Application Insights su cui si vuole creare il report. Se non si dispone di una chiave API di Application Insights, è disponibile un'opzione per **creare la chiave API**.
    
   * AI_APP_ID = Application ID
   * AI_APP_KEY = API Key
   * SendGridAPI =SendGrid API Key

     > [!NOTE]
     > Se non si dispone già di un account SendGrid, crearne uno. La documentazione di SendGrid per Funzioni di Azure è disponibile [qui ](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid). Per una spiegazione di base su come impostare SendGrid e generare una chiave API, leggere le informazioni in fondo a questo articolo. 

9. Selezionare **Integra** e in Output fare clic su **SendGrid ($return)** .

     ![Screenshot dell'output](./media/automate-custom-reports/function-app-09.png)

10. In **Impostazione app per la chiave API SendGrid** selezionare l'impostazione dell'app appena creata per **API SendGrid**.

     ![Screenshot dell'esecuzione dell'app per le funzioni](./media/automate-custom-reports/function-app-010.png)

11. Eseguire e testare l'app per le funzioni.

     ![Screenshot del test](./media/automate-custom-reports/function-app-11.png)

12. Controllare la posta elettronica per verificare che il messaggio sia stato inviato/ricevuto correttamente.

     ![Screenshot della riga dell'oggetto del messaggio di posta elettronica](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>SendGrid con Azure

Questi passaggi sono validi solo se non è già stato configurato un account SendGrid.

1. Dal portale di Azure selezionare **Crea risorsa**, cercare **Recapito della posta elettronica SendGrid** > fare clic su **Crea** > e compilare le istruzioni per la creazione specifica di SendGrid. 

     ![Screenshot della creazione della risorsa SendGrid](./media/automate-custom-reports/function-app-13.png)

2. Dopo la creazione in Account SendGrid selezionare **Gestisci**.

     ![Screenshot delle chiavi API delle impostazioni](./media/automate-custom-reports/function-app-14.png)

3. Verrà avviato il sito di SendGrid. Selezionare **Impostazioni** > **Chiavi API**.

     ![Screenshot della creazione e visualizzazione dell'app delle chiavi API](./media/automate-custom-reports/function-app-15.png)

4. Creare una chiave API > scegliere **Create & View** (Crea e visualizza). Per determinare il livello di autorizzazioni appropriato per la chiave API in uso, consultare la documentazione di SendGrid sull'accesso con restrizioni. In questo caso è selezionato l'accesso completo solo come esempio.

   ![Screenshot dell'accesso completo](./media/automate-custom-reports/function-app-16.png)

5. Copiare la chiave completa, che è il valore che serve nelle impostazioni dell'app per le funzioni come valore per SendGridAPI

   ![Screenshot della copia della chiave API](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla creazione di [query di Analisi](../../azure-monitor/log-query/get-started-queries.md).
* Altre informazioni sull'[esecuzione di query a livello di codice sui dati di Application Insights](https://dev.applicationinsights.io/)
* Altre informazioni su [App per la logica](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
* Altre informazioni su [Microsoft Flow](https://ms.flow.microsoft.com).
