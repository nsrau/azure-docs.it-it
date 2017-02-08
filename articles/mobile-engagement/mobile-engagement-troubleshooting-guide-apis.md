---
title: Guida alla risoluzione dei problemi di Azure Mobile Engagement - API
description: Guide alla risoluzione dei problemi di Azure Mobile Engagement - API
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 3efc8a52-2b74-4917-b887-815ae8277474
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/04/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: a7ae0a83046f2d67b790f672dcd3ae261987357a


---
# <a name="troubleshooting-guide-for-api-issues"></a>Guida alla risoluzione dei problemi relativi all’API
Di seguito sono indicati possibili problemi relativi al modo in cui gli amministratori interagiscono con Azure Mobile Engagement usando le API.

## <a name="syntax-issues"></a>Problemi di sintassi
### <a name="issue"></a>Problema
* Errori di sintassi durante l'uso dell'API (o comportamento imprevisto).

### <a name="causes"></a>Cause
* Problemi di sintassi:
  * Assicurarsi di controllare la sintassi dell'API in uso per confermare che l'opzione sia disponibile.
  * Un problema comune relativo all'utilizzo dell'API si verifica quando vengono confuse le API Reach e Push; la maggior parte delle attività deve essere eseguita tramite l'API Reach anziché l'API Push. 
  * Un altro problema comune relativo all'integrazione dell'SDK e all'utilizzo dell'API si verifica quando vengono confuse le chiavi SDK e API.
  * Gli script che si connettono alle API devono inviare dati almeno ogni 10 minuti, altrimenti si verifica il timeout della connessione. Questo errore è comune soprattutto negli script dell'API Monitor che sono in ascolto dei dati. Per impedire i timeout, fare in modo che lo script invii un ping XMPP ogni 10 minuti per mantenere attiva la sessione con il server.

### <a name="see-also"></a>Vedere anche
* [Documentazione dell'API][Link 4]
* [Informazioni sul protocollo XMPP](http://xmpp.org/extensions/xep-0199.html)

## <a name="unable-to-use-the-api-to-perform-the-same-action-available-in-the-azure-mobile-engagement-ui"></a>Impossibile usare l'API per eseguire la stessa azione disponibile nell'interfaccia utente di Azure Mobile Engagement
### <a name="issue"></a>Problema
* Un'azione che può essere eseguita dall'interfaccia utente di Azure Mobile Engagement non può essere eseguita dall'API di Azure Mobile Engagement correlata.

### <a name="causes"></a>Cause
* Quando si conferma che è possibile eseguire la stessa azione dall'interfaccia utente di Azure Mobile Engagement, vuol dire che la funzionalità di Azure Mobile Engagement è stata integrata correttamente nell'SDK.

### <a name="see-also"></a>Vedere anche
* [Documentazione dell'interfaccia utente][Link 1]

## <a name="error-messages"></a>Messaggi di errore
### <a name="issue"></a>Problema
* Codici di errore che usano l'API visualizzata nel runtime o nei log.

### <a name="causes"></a>Cause
* Di seguito, è riportato un elenco completo di codici di stato API comuni da usare come riferimento e per risolvere i problemi preliminari:
  
        200        Success.
        200        Account updated: device registered, associated, updated, or removed from the current account.
        200        Returns a list of projects as a JSON object or an authentication token generated and returned in the response’s body.
        201        Account created.
        400        Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
        401        Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
        402        Billing lock. The application is either off its quotas or is currently in a bad billing state.
        403        The application is not enabled or the specific API is disabled for this application.
        403        Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
        403        Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently “in progress”, campaign must be “in progress” and the campaign’s property named, manual Push must be set to true.
        403        The email address is already associated to another account (a super user for instance). No authentication token will be generated.
        404        Application, device, campaign, or project identifier not found.
        404        Query parameter is invalid JSON or has a field with an unexpected value.
        404        The email address is not associated with an account. Please create or update the account first.
        405        Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
        409        Name already associated to a different device ID or campaign.
        413        Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn’t retrieve the analytics because the user request is for a period that is too large).
        503        Analytics not available yet (the requested information is not computed yet for an application).
        504        The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

### <a name="see-also"></a>Vedere anche
* [Documentazione dell'API - Errori dettagliati su ogni API specifica][Link 4]

## <a name="silent-failures"></a>Errori invisibili all'utente
### <a name="issue"></a>Problema
* Non è possibile eseguire l'azione API e non vengono visualizzati messaggi di errore nel runtime o nei registri.

### <a name="causes"></a>Cause
* Molti elementi verranno visualizzati nell'interfaccia utente di Azure Mobile Engagement se sono integrati nel modo corretto, ma verranno nascosti automaticamente dall'API. Pertanto, è opportuno provare la stessa funzionalità dall'interfaccia utente per verificarne il funzionamento.
* Prima di usare Azure Mobile Engagement e alcune delle funzionalità del servizio, è necessario integrare questi elementi in modo individuale nell'app con l'SDK, eseguendo una procedura separata.

### <a name="see-also"></a>Vedere anche
* [Guida alla risoluzione dei problemi - SDK][Link 25]

<!--Link references-->
[Link 1]: mobile-engagement-user-interface-home.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/en-us/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/en-us/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/en-us/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md




<!--HONumber=Dec16_HO2-->


