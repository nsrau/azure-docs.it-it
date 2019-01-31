---
title: Errore "Can't Access this Corporate Application" (Impossibile accedere all'applicazione aziendale) quando si usa un'applicazione Proxy di applicazione | Microsoft Docs
description: Come risolvere problemi di accesso comuni con applicazioni Proxy di applicazione di Azure AD.
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: f282bdb54a96b6bf91df3fc7d185e68c41cd7008
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55177203"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>Errore "Can't Access this Corporate Application" (Impossibile accedere all'applicazione aziendale) quando si usa un'applicazione Proxy di applicazione

Questo articolo consente di risolvere alcuni problemi comuni per l'errore "Impossibile accedere a questa app aziendale" in un'applicazione Azure Active Directory Application Proxy.

## <a name="overview"></a>Panoramica
Quando viene visualizzato questo errore, individuare il codice di stato nella pagina dell'errore. Questo codice è probabilmente uno dei seguenti codici di stato:

-   **Gateway Timeout** (timeout del gateway): il servizio Proxy di applicazione non riesce a raggiungere il connettore. Questo errore in genere indica un problema con l'assegnazione del connettore, con il connettore stesso o con le regole di rete relative al connettore.

-   **Bad Gateway** (gateway non valido): il connettore non riesce a raggiungere l'applicazione back-end. Questo errore potrebbe indicare un errore di configurazione dell'applicazione.

-   **Forbidden** (accesso non consentito): l'utente non è autorizzato ad accedere all'applicazione. Questo errore può verificarsi quando l'utente non è assegnato all'applicazione in Azure Active Directory o se l'utente non dispone dell'autorizzazione per accedere all'applicazione sul back-end.

Per trovare il codice, cercare il campo "Status Code" (Codice di stato) nel testo sulla parte inferiore sinistra del messaggio di errore. Cercare anche altri suggerimenti in fondo della pagina.

   ![Errore di timeout del gateway](./media/application-proxy-sign-in-bad-gateway-timeout-error/connection-problem.png)

Per dettagli su come risolvere la causa principale di questi errori e per ulteriori dettagli sulle risoluzioni consigliate, vedere la sezione corrispondente di seguito.

## <a name="gateway-timeout-errors"></a>Errori di timeout del gateway

Un timeout del gateway si verifica quando i servizio tenta di raggiungere il connettore e non ci riesce entro il periodo di timeout. Questo errore è in genere causato da un'applicazione assegnata a un gruppo di connettori senza connettori funzionanti o a porte richieste dal connettore non aperte.


## <a name="bad-gateway-errors"></a>Errori di gateway non valido

Un errore di gateway non valido indica che il connettore non è in grado di raggiungere l'applicazione back-end. Assicurarsi di aver pubblicato l'applicazione corretta. Inesattezze comuni che causano questo errore sono:

-   Un errore di digitazione o un'imprecisione nell'URL interno

-   Pubblicazione di un indirizzo diverso dalla radice dell'applicazione. Ad esempio, pubblicare <http://expenses/reimbursement> ma tentare di accedere a <http://expenses>

-   Problemi di configurazione della delega vincolata Kerberos (KCD)

-   Problemi dell'applicazione back-end

## <a name="forbidden-errors"></a>Errori di accesso non consentito

Se viene visualizzato un errore di accesso non consentito, l'utente non è stato assegnato all'applicazione. Questo errore può riferirsi ad Azure Active Directory o all'applicazione back-end.

Per informazioni su come assegnare utenti all'applicazione in Azure, vedere la [documentazione di configurazione](application-proxy-add-on-premises-application.md#test-the-application).

Se l'utente risulta assegnato all'applicazione in Azure, controllare la configurazione dell'utente nell'applicazione back-end. Se si usa la delega vincolata Kerberos o l'autenticazione integrata di Windows, vedere la pagina di risoluzione dei problemi KCD per le linee guida.

## <a name="check-the-applications-internal-url"></a>Controllare l'URL interno dell'applicazione

Innanzitutto, controllare attentamente e correggere l'URL interno aprendo l'applicazione tramite **Applicazioni aziendali** e selezionando il menu **Proxy dell'applicazione**. Verificare che l'URL interno sia quello usato dalla rete locale per accedere all'applicazione.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Controllare che l'applicazione sia assegnata a un gruppo di connettori funzionante

Per verificare che l'applicazione sia assegnata a un gruppo di connettori funzionante:

1.  Aprire l'applicazione nel portale selezionando **Azure Active Directory**, facendo cli csu **Applicazioni aziendali** e quindi su **Tutte le applicazioni**. Aprire l'applicazione, quindi selezionare **Proxy dell'applicazione** dal menu a sinistra.

2.  Osservare il campo Gruppo di connettori. Se nel gruppo non sono presenti connettori, viene visualizzato un avviso. Se non viene visualizzato alcun avviso, procedere con la sezione "Controllare che tutte le porte richieste siano inserite nell'elenco degli elementi consentiti".

3.  Se compare il gruppo di connettori sbagliato, selezionare il gruppo corretto e verificare che non siano più visualizzati avvisi. Se compare il gruppo di connettori desiderato, fare clic sul messaggio di avviso per aprire la pagina con la gestione connettori.

4.  A questo punto, vi sono alcuni metodi per ulteriori approfondimenti:

  * Spostare un connettore attivo nel gruppo: se è disponibile un connettore attivo che deve appartenere a questo gruppo e ha una linea di visuale sull'applicazione back-end, è possibile spostare il connettore nel gruppo assegnato. A tale scopo, fare clic sul connettore. Nel campo "Gruppo di connettori", usare l'elenco a discesa per selezionare il gruppo di connettori e fare clic su Salva.

  * Scaricare un nuovo connettore per il gruppo: da questa pagina, è possibile accedere al collegamento per [scaricare un nuovo connettore](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download). Installare il connettore in un computer che comunica direttamente con l'applicazione back-end. Normalmente il connettore è installato nello stesso server dell'applicazione. Usare il collegamento di download del connettore per scaricare un connettore nel computer di destinazione. Quindi, fare clic sul connettore e usare l'elenco a discesa "Gruppo di connettori" per assicurarsi che appartenga al gruppo corretto.

  * Verificare se è presente un connettore non attivo: se un connettore risulta inattivo, non riesce a raggiungere il servizio. Questo errore è dovuto in genere al fatto che alcune porte richieste sono bloccate. Per risolvere il problema, procedere con la sezione "Controllare che tutte le porte richieste siano inserite nell'elenco degli elementi consentiti".

Dopo aver eseguito tutte queste operazioni per assicurarsi che l'applicazione sia assegnata a un gruppo con connettori funzionanti, provare di nuovo l'applicazione. Se ancora non funziona, passare alla sezione successiva.

## <a name="check-all-required-ports-are-whitelisted"></a>Controllare che tutte le porte richieste siano inserite nell'elenco degli elementi consentiti

Per verificare che tutte le porte richieste siano aperte, vedere la documentazione sull'apertura delle porte. Se tutte le porte richieste sono aperte, passare alla sezione successiva.

## <a name="check-for-other-connector-errors"></a>Verificare la presenza di altri errori del connettore

Se nessuna delle operazioni precedenti ha risolto il problema, il passaggio successivo è cercare errori relativi al connettore stesso. Alcuni errori comuni sono riportati nel [documento di risoluzione dei problemi](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors). 

È anche possibile consultare direttamente i registri del connettore per verificare se sono riportati errori. Molti dei messaggi di errore condividono consigli specifici per la risoluzione dei problemi. Per visualizzare i registri, vedere la [documentazione sui connettori](application-proxy-connectors.md#under-the-hood).

## <a name="additional-resolutions"></a>Risorse aggiuntive

Se quanto sopra riportato non ha consentito di risolvere il problema, vi sono alcune altre cause possibili. Per identificare il problema:

Se l'applicazione è configurata per usare l'autenticazione integrata di Windows (IWA), provare l'applicazione senza Single Sign-On. In caso contrario, passare al paragrafo successivo. Per provare l'applicazione senza Single Sign-On, aprirla tramite **Applicazioni aziendali** e selezionare il menu **Single Sign-On**. Nell'elenco a discesa passare da "Autenticazione integrata di Windows" a "Single Sign-On di Azure AD disabilitato". 

Ora aprire un browser e provare nuovamente ad accedere all'applicazione. Dovrebbe venire richiesta l'autenticazione per accedere all'applicazione. Se si riesce a autenticarsi, il problema riguarda la configurazione della delega vincolata Kerberos (KCD) che abilita Single Sign-On. Per altre informazioni, vedere la pagina di risoluzione dei problemi di KCD.

Se si continua a visualizzare l'errore, accedere al computer su cui è installato il connettore, aprire un browser e tentare di raggiungere l'URL interno usato per l'applicazione. Il connettore funziona come un altro client dallo stesso computer. Se non è possibile raggiungere l'applicazione, ricercarne il motivo o usare un connettore su un server in grado di accedere all'applicazione.

Se è possibile raggiungere l'applicazione da tale computer, cercare problemi o errori relativi al connettore stesso. Alcuni errori comuni sono riportati nel [documento di risoluzione dei problemi](application-proxy-troubleshoot.md#connector-errors). È anche possibile consultare direttamente i registri del connettore per verificare se sono riportati errori. Molti dei messaggi di errore riportano consigli più specifici per la risoluzione dei problemi. Per informazioni su come visualizzare i registri, vedere la [documentazione sui connettori](application-proxy-connectors.md#under-the-hood).

## <a name="next-steps"></a>Passaggi successivi
[Comprendere i connettori del proxy applicazione di Azure AD](application-proxy-connectors.md)
