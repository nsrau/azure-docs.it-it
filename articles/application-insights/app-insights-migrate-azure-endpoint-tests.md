---
title: Migrare dai test degli endpoint di Azure ai test di disponibilità di Application Insights
description: Migrazione dei test di monitoraggio degli endpoint di Azure nei test di disponibilità di Application Insights entro il 31 ottobre 2016.
services: application-insights
documentationcenter: ''
author: soubhagyadash
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: awills

---
# <a name="moving-from-azure-endpoint-monitoring-to-application-insights-availability-tests"></a>Spostamento dal monitoraggio degli endpoint di Azure ai test di disponibilità di Application Insights
Si usa il [monitoraggio degli endpoint](https://blogs.msdn.microsoft.com/mast/2013/03/03/windows-azure-portal-update-configure-web-endpoint-status-monitoring-preview/) per le app di Azure? Entro il *31 ottobre 2016* il monitoraggio degli endpoint sarà sostituito dai nuovi e più efficienti [test di disponibilità](app-insights-monitor-web-app-availability.md). Anche se rimangono disabilitati fino al 31 ottobre 2016, alcuni dei nuovi test sono già stati creati. 

Se si desidera, è possibile modificare i nuovi test ed eseguire il passaggio manualmente. I test sono disponibili nel [portale di Azure](https://portal.azure.com) nel gruppo di risorse Default-ApplicationInsights-CentralUS.

## <a name="what-are-availability-tests?"></a>Che cosa sono i test di disponibilità?
I test di disponibilità sono una funzionalità di Azure che verifica in modo continuo che tutti i servizi o siti Web siano attivi e in esecuzione inviando richieste HTTP al servizio o al sito (test a ping singolo o test Web di Visual Studio) da un massimo di 16 posizioni in tutto il mondo. 

Nel [portale classico di Azure](https://manage.windowsazure.com)questi test erano chiamati monitoraggio degli endpoint. L'ambito era più limitato. I nuovi test di disponibilità rappresentano un miglioramento significativo:

* Fino a 10 test web di Visual Studio o test di ping per ogni risorsa di Application Insights. 
* Fino a 16 posizioni in tutto il mondo per inviare richieste di test all'app Web. Maggiore controllo dei criteri di successo dei test. 
* Test di qualsiasi sito o servizio Web, non solo delle app Web di Azure.
* Più tentativi di test riducono gli avvisi di falsi positivi causati da problemi di rete temporanei. 
* I webhook possono ricevere notifiche POST HTTP per gli avvisi.

![](./media/app-insights-migrate-azure-endpoint-tests/16-1test.png)

Per altre informazioni sui test di disponibilità, vedere [qui](app-insights-monitor-web-app-availability.md).

I test di disponibilità sono inclusi in [Visual Studio Application Insights](app-insights-overview.md), un servizio di analisi estendibile per qualsiasi applicazione Web.

## <a name="so-what's-happening-to-my-endpoint-tests?"></a>Che cosa succede ai miei test degli endpoint?
* I test di monitoraggio degli endpoint sono stati copiati nei test di disponibilità di Application Insights.
* I nuovi test di disponibilità sono attualmente disabilitati, mentre i test degli endpoint precedenti sono ancora in esecuzione.
* Le regole di avviso *non* sono state trasferite. I nuovi test sono stati impostati inizialmente con una regola predefinita:
  * L'attivazione avviene quando più di una posizione segnala errori in un periodo di tempo di 5 minuti.
  * Viene inviato un messaggio di posta elettronica agli amministratori della sottoscrizione.

I test trasferiti sono disponibili nel [portale di Azure](https://portal.azure.com)nel gruppo di risorse "Default-ApplicationInsights-CentralUS". I nomi dei test hanno il prefisso "Migrated-". 

## <a name="what-do-i-need-to-do?"></a>Cosa occorre fare?
* Se non è stata eseguita la migrazione dei test, i nuovi test di disponibilità sono [semplici da configurare](app-insights-monitor-web-app-availability.md).

### <a name="option-a:-do-nothing.-leave-it-to-us."></a>Opzione A: non eseguire alcuna operazione. La migrazione viene eseguita automaticamente.
**Il 31 ottobre 2016** verranno eseguite le seguenti operazioni:

* Disabilitazione dei test degli endpoint precedenti.
* Abilitazione dei test di disponibilità migrati.

### <a name="option-b:-you-manage-and/or-enable-the-new-tests."></a>Opzione B: gestire e/o abilitare i nuovi test.
* Rivedere e modificare i nuovi test di disponibilità nel nuovo [portale di Azure](https://portal.azure.com). 
  * Esaminare i criteri di trigger
  * Esaminare i destinatari di posta elettronica
* Abilitare i nuovi test
* I test di monitoraggio degli endpoint precedenti verranno disabilitati il 31 ottobre 2016 

### <a name="option-c:-opt-out"></a>Opzione C: non usare i test di disponibilità
Se non si desidera usare i test di disponibilità, è possibile eliminarli nel [portale di Azure](https://portal.azure.com). È inoltre disponibile un collegamento per l'annullamento della sottoscrizione nella parte inferiore dei messaggi di posta elettronica delle notifiche.

I test degli endpoint precedenti verranno in ogni caso eliminati il 31 ottobre 2016. 

## <a name="how-do-i-edit-the-new-tests?"></a>Come modificare i nuovi test?
Accedere al [portale di Azure](https://portal.azure.com) e individuare i test Web con prefisso 'Migrated-': 

![Scegliere Gruppi di risorse, Default-ApplicationInsights-CentralUS e aprire i test con indicazione 'migrated'.](./media/app-insights-migrate-azure-endpoint-tests/20.png)

Modificare e/o abilitare il test:

![](./media/app-insights-migrate-azure-endpoint-tests/21.png)

## <a name="why-is-this-happening?"></a>Perché accade?
Per offrire un servizio migliore. Il servizio degli endpoint precedente era molto più restrittivo. Era possibile specificare soltanto due URL per eseguire semplici test di ping da 3 posizioni geografiche in una VM o un'app di Azure. I nuovi test consentono di eseguire test Web in più passaggi da un massimo di 16 posizioni ed è possibile specificare fino a 10 test per una singola applicazione. È possibile testare qualsiasi URL che non deve necessariamente essere un sito di Azure.

I nuovi test vengono configurati separatamente dall'app Web o dalla macchina virtuale che si sta testando. 

La migrazione dei test viene eseguita per garantire il controllo dei test dal nuovo portale. 

## <a name="what-is-application-insights?"></a>Informazioni su Azure Application Insights
I nuovi test di disponibilità sono inclusi in [Visual Studio Application Insights](app-insights-overview.md). Ecco un [video di 2 minuti](http://go.microsoft.com/fwlink/?LinkID=733921).

## <a name="am-i-paying-for-the-new-tests?"></a>I nuovi test sono a pagamento?
I test migrati vengono impostati in una risorsa Application Insights nel piano gratuito predefinito. Ciò consente una raccolta di un massimo di 5 milioni di punti dati. Un limite che copre sicuramente il volume di dati attualmente usato dai test. 

Naturalmente, se si usa Application Insights e si creano altri test di disponibilità o si usano più funzioni di monitoraggio delle prestazioni e di diagnostica, verranno generati ulteriori punti dati.  L'unica conseguenza sarebbe comunque soltanto il superamento della quota del piano gratuito. Non verrà richiesto alcun pagamento, a meno che non si aderisca ai piani Standard e Premium. 

Per altre informazioni, vedere [Gestire i prezzi e la quota per Application Insights](app-insights-pricing.md). 

## <a name="what-is-and-isn't-migrated?"></a>Quali sono gli elementi dei quali viene e non viene eseguita la migrazione?
Mantenuti dai test degli endpoint precedenti:

* URL dell'endpoint da sottoporre a test.
* Posizioni geografiche da cui vengono inviate le richieste.
* La frequenza dei test rimane di 5 minuti.
* Il timeout dei test rimane di 30 secondi. 

Non migrati:

* Regola del trigger di avviso. La regola impostata viene attivata quando una posizione  segnala gli errori in un periodo di tempo di 5 minuti.
* Destinatari degli avvisi. Le notifiche tramite posta elettronica verranno inviate ai proprietari delle sottoscrizioni e ai co-proprietari. 

## <a name="how-do-i-find-the-new-tests?"></a>Dove è possibile trovare i nuovi test?
Se si desidera, è possibile modificare i nuovi test. Accedere al [portale di Azure](https://portal.azure.com), aprire **Gruppi di risorse** e selezionare **Default-ApplicationInsights-CentralUS**. Il gruppo include i nuovi test Web. Per informazioni sui nuovi test di disponibilità, vedere [Monitorare la disponibilità e la velocità di risposta dei siti Web](app-insights-monitor-web-app-availability.md).

Si noti che i nuovi avvisi tramite posta elettronica verranno inviati da questo indirizzo: Avvisi di App Insights (ai-noreply@microsoft.com)

## <a name="what-happens-if-i-do-nothing?"></a>Cosa accade se non si esegue alcuna operazione?
Verrà applicata l'opzione A. Verranno abilitati i test migrati e verranno impostate le regole di avviso predefinite. Sarà necessario aggiungere eventuali regole di avviso personalizzate e i destinatari come descritto in precedenza. I test di monitoraggio degli endpoint precedenti verranno disabilitati. 

## <a name="where-can-i-provide-feedback-on-this?"></a>Dove posso inviare commenti e suggerimenti?
I commenti e suggerimenti degli utenti sono molto apprezzati. Inviarli con [un messaggio di posta elettronica](mailto:vsai@microsoft.com). 

<!--HONumber=Oct16_HO2-->


