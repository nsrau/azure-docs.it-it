---
title: 'Informazioni sulle procedure consigliate per la sicurezza: dispositivi gemelli digitali di Azure | Microsoft Docs'
description: Informazioni sulle procedure di sicurezza consigliate per i dispositivi gemelli digitali di Azure e il Internet delle cose.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: f5c0f6d9f6f7f35722f3df5f35dc1da945f21b9a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229045"
---
# <a name="azure-digital-twins-security-best-practices"></a>Procedure consigliate per la sicurezza di Gemelli digitali di Azure

Le funzionalità di sicurezza di Gemelli digitali di Azure consentono un accesso preciso a risorse e azioni specifiche nel grafico IoT. A tale scopo viene usato un meccanismo di gestione granulare di ruoli e autorizzazioni detto ["controllo degli accessi in base al ruolo"](./security-role-based-access-control.md).

Gemelli digitali di Azure usa anche altre funzionalità di sicurezza che sono presenti in Azure IoT, tra cui Azure Active Directory (Azure AD). Per questo motivo, per la configurazione e la sicurezza delle app create su Gemelli digitali di Azure sono valide molte delle [procedure consigliate per la sicurezza di Azure IoT](../iot-fundamentals/iot-security-best-practices.md).

Questo articolo riepiloga le principali procedure consigliate da seguire.

> [!IMPORTANT]
> Per garantire la massima sicurezza per lo spazio IoT, esaminare altre risorse relative alla sicurezza. Assicurarsi di includere i fornitori di dispositivi.

> [!TIP]
> Usa il [Centro sicurezza di Azure per](https://docs.microsoft.com/azure/asc-for-iot/) gli Internet per scoprire le minacce e le vulnerabilità della sicurezza.

## <a name="iot-security-best-practices"></a>Procedure consigliate per la sicurezza IoT

Ecco alcune tra le principali procedure consigliate per proteggere in modo sicuro i dispositivi IoT:

> [!div class="checklist"]
> * Proteggere ogni dispositivo connesso allo spazio IoT in modo da impedirne la manomissione.
> * Limitare il ruolo di ogni dispositivo, sensore e persona all'interno dello spazio IoT. In caso di compromissione, l'impatto sarà ridotto al minimo.
> * Prendere in considerazione l'eventualità di usare il filtro degli indirizzi IP dei dispositivi e la restrizione della porta.
> * Limitare larghezza di banda di I/O e dei dispositivi per migliorare le prestazioni. La limitazione della velocità può consentire di migliorare la sicurezza impedendo attacchi Denial of Service.
> * Mantieni aggiornato il firmware, il sistema operativo e il software del dispositivo.
> * Controllare periodicamente le procedure consigliate per la sicurezza dei dispositivi, dei software, delle reti e dei gateway Man mano che continuano a migliorare e a evolversi.

Ecco alcune tra le principali procedure consigliate per proteggere in modo sicuro uno spazio IoT:

> [!div class="checklist"]
> * Crittografare i dati salvati, archiviati o salvati in modo permanente.
> * Richiedere la modifica o l'aggiornamento periodico di password o chiavi.
> * Limitare attentamente le autorizzazioni e l'accesso in base al ruolo. Vedere la sezione [Procedure consigliate per il controllo degli accessi in base al ruolo](#role-based-access-control-best-practices) seguente.
> * Si consideri una topologia di rete divisa in modo che i dispositivi in ogni rete siano isolati dagli altri.
> * Usare la crittografia avanzata. Richiedi password lunghe, USA protocolli sicuri e autenticazione a più [fattori](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

[Monitorare](./how-to-configure-monitoring.md) le risorse IoT per individuare outlier, minacce o parametri delle risorse che non rientrano nell'intervallo del normale funzionamento. Usare Azure Analytics per monitorare la gestione.

> [!IMPORTANT]
> Leggi le [procedure consigliate](../iot-fundamentals/iot-security-best-practices.md) per la sicurezza di Azure. per iniziare una strategia di sicurezza completa.

> [!NOTE]
> Per altre informazioni sull'elaborazione e monitoraggio degli eventi, vedere [Routing di eventi e messaggi](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Procedure consigliate per Azure Active Directory

I dispositivi gemelli digitali di Azure usano [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/) per autenticare gli utenti e proteggere le applicazioni. Azure Active Directory supporta l'autenticazione per un'ampia gamma di architetture moderne. Tutte queste sono basate sui protocolli standard come OAuth 2.0 o OpenID Connect. Ecco alcune tra le principali procedure consigliate per proteggere lo spazio IoT per Azure Active Directory:

> [!div class="checklist"]
> * Archiviare chiavi e chiavi private delle app di Azure Active Directory in una posizione sicura, ad esempio [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Per l'autenticazione usare un certificato emesso da un'[autorità di certificazione](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) attendibile invece dei segreti delle app.
> * Limitare l'ambito di accesso di OAuth 2.0 per un token.
> * Verificare la durata della validità di un token e se un token rimane valido.
> * Impostare periodi di tempo appropriati per la validità dei token.
> * Aggiornare i token scaduti.

## <a name="role-based-access-control-best-practices"></a>Procedure consigliate per il controllo degli accessi in base al ruolo

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulle procedure consigliate per Azure IoT, vedere [Procedure consigliate per la sicurezza di IoT](../iot-fundamentals/iot-security-best-practices.md).

* Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Controllo degli accessi in base al ruolo](./security-role-based-access-control.md).

* Per informazioni sull'autenticazione, vedere [Autenticazione con le API](./security-authenticating-apis.md).