---
title: Errore imprevisto durante la richiesta di consenso per un'applicazione | Microsoft Docs
description: "Vengono illustrati gli errori che possono verificarsi quando si tenta di ottenere il consenso per un'applicazione, nonché suggerite le operazioni da eseguire per risolverli"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 21dabdcf0697b38ad3598ee8126a1bd62007efb5
ms.contentlocale: it-it
ms.lasthandoff: 04/17/2017

---

# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Errore imprevisto durante la richiesta di consenso per un'applicazione

Questo articolo descrive gli errori che possono verificarsi quando si tenta di ottenere il consenso per un'applicazione. Per risolvere i problemi relativi a richieste di consenso impreviste che non contengono messaggi di errore, vedere [Scenari di autenticazione per Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios).

Molte applicazioni integrate in Azure Active Directory richiedono autorizzazioni per accedere ad altre risorse e garantire il funzionamento. Quando anche queste risorse sono integrate in Azure Active Directory, le autorizzazioni per il relativo accesso vengono spesso richieste tramite il framework di consenso comune. 

Questo comporta la visualizzazione di una richiesta di consenso, che in genere viene inviata al primo utilizzo di un'applicazione ma a volte anche successivamente.

Perché un utente possa concedere le autorizzazioni richieste da un'applicazione, è necessario che siano soddisfatte determinate condizioni. In caso contrario, possono verificarsi diversi errori, inclusi i seguenti:

## <a name="requesting-not-authorized-permissions-error"></a>Errore di richiesta di autorizzazioni non consentite
* **AADSTS90093:** &lt;NomeVisualizzatoAppClient&gt; richiede una o più autorizzazioni che l'utente non è autorizzato a concedere. Contattare un amministratore che possa concedere il consenso per l'applicazione per conto dell'utente.

Questo errore si verifica quando un utente non amministratore della società tenta di utilizzare un'applicazione che richiede autorizzazioni che solo un amministratore può concedere. Il problema può essere risolto chiedendo a un amministratore di concedere l'accesso all'applicazione per conto dell'organizzazione.

## <a name="policy-prevents-granting-permissions-error"></a>Errore dovuto a criteri che impediscono di concedere le autorizzazioni
* **AADSTS90093:** un amministratore di &lt;NomeVisualizzatoTenant&gt; ha configurato criteri che impediscono di concedere a &lt;nome dell'app&gt; le autorizzazioni che richiede. Contattare un amministratore di &lt;NomeVisualizzatoTenant&gt; che possa concedere le autorizzazioni richieste dall'app per conto dell'utente.

Questo errore si verifica quando un amministratore della società impedisce agli utenti di concedere il consenso per le applicazioni e un utente non amministratore tenta di usare un'applicazione che richiede il consenso. Il problema può essere risolto chiedendo a un amministratore di concedere l'accesso all'applicazione per conto dell'organizzazione.

## <a name="intermittent-problem-error"></a>Errore dovuto a un problema intermittente
* **AADSTS90090:** sembra che si sia verificato un problema intermittente durante la registrazione delle autorizzazioni che si è tentato di concedere a &lt;NomeVisualizzazioneAppClient&gt;. Riprovare in un secondo momento.

Questo errore indica che si è verificato un problema intermittente relativo al servizio. È possibile risolverlo provando a concedere di nuovo il consenso per l'applicazione.

## <a name="resource-not-available-error"></a>Errore di risorsa non disponibile
* **AADSTS65005:** l'app &lt;NomeVisualizzatoAppClient&gt; ha richiesto autorizzazioni per accedere alla risorsa &lt;NomeVisualizzatoAppRisorsa&gt; che non è disponibile. 

Contattare lo sviluppatore dell'applicazione.

##  <a name="resource-not-available-in-tenant-error"></a>Errore di risorsa non disponibile nel tenant
* **AADSTS65005:** &lt;NomeVisualizzatoAppClient&gt; richiede l'accesso alla risorsa &lt;NomeVisualizzatoAppRisorsa&gt; che non è disponibile all'interno dell'organizzazione &lt;NomeVisualizzatoTenant&gt;. 

Assicurarsi che la risorsa sia disponibile o contattare un amministratore di &lt;NomeVisualizzatoTenant&gt;.

## <a name="permissions-mismatch-error"></a>Errore di mancata corrispondenza delle autorizzazioni
* **AADSTS65005:** l'app ha richiesto il consenso per accedere alla risorsa &lt;NomeVisualizzatoAppRisorsa&gt;. Questa richiesta ha avuto esito negativo perché non corrisponde al modo in cui l'app è stata preconfigurata durante la registrazione. Contattare il fornitore dell'app.**

Tutti questi errori si verificano quando l'applicazione per la quale un utente sta provando a concedere il consenso richiede autorizzazioni per accedere a un'applicazione della risorsa non trovata nella directory dell'organizzazione (tenant). Questa situazione può verificarsi per diversi motivi:

-   Lo sviluppatore dell'applicazione client ha configurato l'applicazione in modo errato per cui questa richiede l'accesso a una risorsa non valida. In questo caso, lo sviluppatore dell'applicazione deve aggiornare la configurazione dell'applicazione client per risolvere il problema.

-   Un'entità servizio che rappresenta l'applicazione della risorsa di destinazione non esiste nell'organizzazione o esisteva in precedenza ma è stata rimossa. Per risolvere questo problema, è necessario eseguire il provisioning di un'entità servizio per l'applicazione della risorsa all'interno dell'organizzazione, in modo che l'applicazione client possa richiedere le autorizzazioni per tale entità. A seconda del tipo di applicazione, è possibile eseguire questa operazione in diversi modi, tra cui:

    -   Acquisendo una sottoscrizione per l'applicazione della risorsa (applicazioni pubblicate da Microsoft)

    -   Concedendo il consenso per l'applicazione della risorsa

    -   Concedendo le autorizzazioni all'applicazione tramite il portale di Azure

    -   Aggiungendo l'applicazione dalla raccolta di applicazioni di Azure AD

## <a name="next-steps"></a>Passaggi successivi 

[App, autorizzazioni e consenso in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Ambiti, autorizzazioni e consenso in Azure Active Directory (endpoint v2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)



