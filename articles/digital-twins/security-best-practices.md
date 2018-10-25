---
title: Informazioni sulle procedure consigliate per la sicurezza di Gemelli digitali di Azure | Microsoft Docs
description: Procedure consigliate per la sicurezza di Gemelli digitali di Azure
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: adgera
ms.openlocfilehash: 28eb8b5dc0f75b5e031070803d35c8a1ceb1f000
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364343"
---
# <a name="security-best-practices"></a>Procedure consigliate per la sicurezza

Le funzionalità di sicurezza di Gemelli digitali di Azure consentono un accesso preciso a risorse e azioni specifiche nel grafico IoT. A tale scopo viene usato un meccanismo di gestione granulare di ruoli e autorizzazioni detto controllo degli accessi in base al ruolo.

Gemelli digitali di Azure usa anche altre funzionalità di sicurezza presenti in Azure IoT, tra cui Azure Active Directory. Per questo motivo, per la configurazione dell'app di Gemelli digitali di Azure sono valide molte delle [procedure consigliate per la sicurezza di Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/).

Questo articolo riepiloga le principali procedure consigliate da seguire.

> [!IMPORTANT]
> Esaminare altre risorse relative alla sicurezza (incluse quelle dei fornitori dei dispositivi) per garantire la massima sicurezza per lo spazio IoT.

## <a name="iot-security-best-practices"></a>Procedure consigliate per la sicurezza IoT

Ecco alcune tra le principali procedure consigliate per proteggere in modo sicuro i dispositivi IoT:

> [!div class="checklist"]
> * Proteggere ogni dispositivo connesso allo spazio IoT in modo da impedirne la manomissione.
> * Limitare il ruolo di ogni dispositivo, sensore e persona all'interno dello spazio IoT. In caso di compromissione, l'impatto verrà ridotto al minimo.
> * Usare, se possibile, il filtro degli indirizzi IP dei dispositivi.
> * Limitare larghezza di banda di I/O e dei dispositivi per migliorare le prestazioni. La limitazione della velocità può consentire di migliorare la sicurezza impedendo attacchi Denial of Service.

Ecco alcune tra le principali procedure consigliate per proteggere in modo sicuro uno spazio IoT:

> [!div class="checklist"]
> * Crittografare i dati salvati, archiviati o salvati in modo permanente.
> * Richiedere la modifica o l'aggiornamento periodico di password o chiavi.
> * Limitare attentamente le autorizzazioni e l'accesso in base al ruolo (vedere le procedure consigliate per il controllo degli accessi in base al ruolo più avanti).
> * Usare la crittografia avanzata. Ciò significa richiedere password lunghe, usare protocolli sicuri e applicare l'autenticazione a due fattori.

Il monitoraggio delle risorse di IoT per individuare outlier, minacce o parametri delle risorse che non rientrano nell'intervallo del normale funzionamento può essere eseguito tramite le funzionalità di analisi di Azure.

> [!NOTE]
> Per altre informazioni sull'elaborazione e sul monitoraggio degli eventi, vedere l'articolo relativo al [routing di eventi](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Procedure consigliate per Azure Active Directory

Gemelli digitali di Azure usa Azure Active Directory per l'autenticazione degli utenti e la protezione delle applicazioni. Azure Active Directory supporta l'autenticazione per un'ampia gamma di architetture moderne, tutte basate su protocolli standard del settore, come OAuth 2.0 o OpenID Connect. Ecco alcune tra le principali procedure consigliate per proteggere lo spazio IoT per Azure Active Directory:

> [!div class="checklist"]
> * Archiviare chiavi e segreti delle app di Azure Active Directory in una posizione sicura, ad esempio [Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Usare un certificato emesso da un'[autorità di certificazione](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-certificate-based-authentication-get-started) attendibile invece dei segreti delle app per l'autenticazione.
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
