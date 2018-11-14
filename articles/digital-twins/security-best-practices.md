---
title: Informazioni sulle procedure consigliate per la sicurezza di Gemelli digitali di Azure | Microsoft Docs
description: Procedure consigliate per la sicurezza di Gemelli digitali di Azure
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: adgera
ms.openlocfilehash: 6ca01523744dbce15f8fdb3bbe2d5a9b44510f3f
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959478"
---
# <a name="security-best-practices"></a>Procedure di sicurezza consigliate

Le funzionalità di sicurezza di Gemelli digitali di Azure consentono un accesso preciso a risorse e azioni specifiche nel grafico IoT. A tale scopo viene usato un meccanismo di gestione granulare di ruoli e autorizzazioni detto "controllo degli accessi in base al ruolo".

Gemelli digitali di Azure usa anche altre funzionalità di sicurezza che sono presenti in Azure IoT, tra cui Azure Active Directory (Azure AD). Per questo motivo, per la configurazione dell'app di Gemelli digitali di Azure sono valide molte delle [procedure consigliate per la sicurezza di Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/).

Questo articolo riepiloga le principali procedure consigliate da seguire.

> [!IMPORTANT]
> Per garantire la massima sicurezza per lo spazio IoT, esaminare altre risorse relative alla sicurezza. Assicurarsi di includere i fornitori di dispositivi.

## <a name="iot-security-best-practices"></a>Procedure consigliate per la sicurezza IoT

Ecco alcune tra le principali procedure consigliate per proteggere in modo sicuro i dispositivi IoT:

> [!div class="checklist"]
> * Proteggere ogni dispositivo connesso allo spazio IoT in modo da impedirne la manomissione.
> * Limitare il ruolo di ogni dispositivo, sensore e persona all'interno dello spazio IoT. In caso di compromissione, l'impatto sarà ridotto al minimo.
> * Prendere in considerazione l'eventualità di usare il filtro degli indirizzi IP dei dispositivi e la restrizione della porta.
> * Limitare larghezza di banda di I/O e dei dispositivi per migliorare le prestazioni. La limitazione della velocità può consentire di migliorare la sicurezza impedendo attacchi Denial of Service.
> * Mantenere aggiornato il firmware del dispositivo.

Ecco alcune tra le principali procedure consigliate per proteggere in modo sicuro uno spazio IoT:

> [!div class="checklist"]
> * Crittografare i dati salvati, archiviati o salvati in modo permanente.
> * Richiedere la modifica o l'aggiornamento periodico di password o chiavi.
> * Limitare attentamente le autorizzazioni e l'accesso in base al ruolo. Vedere la sezione "Procedure consigliate per il controllo degli accessi in base al ruolo".
> * Usare la crittografia avanzata. Richiedere password lunghe, usare protocolli sicuri e l'autenticazione a due fattori.

Monitorare risorse IoT per individuare outlier, minacce o parametri delle risorse che non rientrano nell'intervallo del normale funzionamento. Usare Azure Analytics per gestire il monitoraggio.

> [!NOTE]
> Per altre informazioni sull'elaborazione e monitoraggio degli eventi, vedere [Routing di eventi e messaggi](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Procedure consigliate per Azure Active Directory

Gemelli digitali di Azure usa Azure AD per l'autenticazione degli utenti e la protezione delle applicazioni. Azure AD supporta l'autenticazione per un'ampia gamma di architetture moderne. Tutte queste sono basate sui protocolli standard come OAuth 2.0 o OpenID Connect. Ecco alcune tra le principali procedure consigliate per proteggere lo spazio IoT per Azure AD:

> [!div class="checklist"]
> * Archiviare chiavi e segreti delle app di Azure AD in una posizione sicura, ad esempio [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Per l'autenticazione usare un certificato emesso da un'[autorità di certificazione](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-certificate-based-authentication-get-started) attendibile invece dei segreti delle app.
> * Limitare l'ambito di accesso di OAuth 2.0 per un token.
> * Verificare la durata della validità di un token e se un token rimane valido.
> * Impostare periodi di tempo appropriati per la validità dei token.
> * Aggiornare i token scaduti.

## <a name="role-based-access-control-best-practices"></a>Procedure consigliate per il controllo degli accessi in base al ruolo

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle procedure consigliate per Azure IoT, vedere [Procedure consigliate per la sicurezza di IoT](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/).

Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Controllo degli accessi in base al ruolo](./security-role-based-access-control.md).

Per informazioni sull'autenticazione, vedere [Autenticazione con le API](./security-authenticating-apis.md).
