---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: marsma
ms.openlocfilehash: 687853720e8f963d5a58093b824bb36e8063bcf7
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149074"
---
## <a name="guidelines-for-using-javascript"></a>Linee guida per l'uso di JavaScript

Seguire queste linee guida per personalizzare l'interfaccia dell'applicazione tramite JavaScript:

- Non associare un evento clic in elementi HTML `<a>`.
- Non definire una dipendenza dal codice o da commenti di Azure AD B2C.
- Non modificare l'ordine o la gerarchia di elementi HTML di Azure AD B2C. Usare un criterio di Azure AD B2C per controllare l'ordine degli elementi dell'interfaccia utente.
- È possibile chiamare qualsiasi servizio RESTful tenendo presenti queste considerazioni:
    - Potrebbe essere necessario impostare CORS del servizio RESTful in modo da consentire le chiamate HTTP lato client.
    - Assicurarsi che il servizio RESTful sia sicuro e usi solo il protocollo HTTPS.
    - Non usare JavaScript direttamente per chiamare endpoint di Azure AD B2C.
- È possibile incorporare JavaScript o è possibile eseguire il collegamento a file JavaScript esterni. Quando si usa un file JavaScript esterno, assicurarsi di usare l'URL assoluto e non un URL relativo.
- Framework JavaScript:
    - Azure AD B2C usa una versione specifica di jQuery. Non includere un'altra versione di jQuery. L'uso di più versioni nella stessa pagina può provocare problemi.
    - L'uso di RequireJS non è supportato.
    - La maggior parte dei framework JavaScript non è supportata da Azure AD B2C.
- Le impostazioni di Azure AD B2C possono essere lette chiamando oggetti `window.SETTINGS` e `window.CONTENT`, ad esempio la lingua dell'interfaccia utente corrente. Non modificare il valore di questi oggetti.
- Per personalizzare il messaggio di errore di Azure AD B2C, usare la localizzazione in un criterio.
- Se è possibile ottenere il risultato desiderato usando un criterio, questo è in genere l'approccio consigliato.
