---
title: Risolvere gli avvisi sull'integrità della protezione degli endpoint nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento illustra come implementare la raccomandazione **Risolvi gli avvisi sull'integrità di Endpoint Protection** del Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''

ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2016
ms.author: terrylan

---
# Risolvere gli avvisi sull'integrità della protezione degli endpoint nel Centro sicurezza di Azure
Il Centro sicurezza di Azure consiglia di risolvere gli avvisi sull'integrità della protezione degli endpoint rilevati. Il Centro sicurezza consente di vedere quali macchine virtuali (VM) presentano errori di protezione degli endpoint e il numero di errori.

> [!NOTE]
> Il documento introduce il servizio usando una distribuzione di esempio. Questa non è una guida dettagliata.
> 
> 

## Implementare la raccomandazione
1. Nel pannello **Raccomandazioni** selezionare **Risolvi gli avvisi sull'integrità di Endpoint Protection**. ![Risolvi gli avvisi sull'integrità di Endpoint Protection][1]
2. Verrà visualizzato il pannello **Errore di Endpoint Protection** che elenca le VM con errori e il numero di errori per ogni VM. Selezionare una VM dall'elenco. ![Errore di Endpoint Protection][2]
3. Verrà visualizzato il pannello **Elenco degli errori** per la VM selezionata, in cui sono elencati gli errori. Selezionare un errore nell'elenco per ulteriori informazioni. Verrà visualizzato un pannello con informazioni sull'errore selezionato. ![Elenco degli errori][3] ![Evento di errore][4]

## Vedere anche
Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md): informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md): informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md): informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): informazioni e notizie aggiornate sulla sicurezza di Azure.

<!--Image references-->
[1]: ./media/security-center-resolve-endpoint-protection/resolve-endpoint-protection.png
[2]: ./media/security-center-resolve-endpoint-protection/endpoint-protection-failure.png
[3]: ./media/security-center-resolve-endpoint-protection/failure-list.png
[4]: ./media/security-center-resolve-endpoint-protection/failure-event.png

<!---HONumber=AcomDC_0803_2016-->