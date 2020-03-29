---
title: Comprendere le procedure consigliate per la sicurezza - Azure Digital Twins Documenti Microsoft
description: Informazioni sulle procedure consigliate per la sicurezza per i gemelli digitali di Azure e l'Internet of Things.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5fc5ba447557aa89e8f0870c576d6d4c439f3353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122560"
---
# <a name="azure-digital-twins-security-best-practices"></a>Procedure consigliate per la sicurezza di Gemelli digitali di Azure

Le funzionalità di sicurezza di Gemelli digitali di Azure consentono un accesso preciso a risorse e azioni specifiche nel grafico IoT. Lo fa attraverso la gestione granulare dei ruoli e delle autorizzazioni chiamata controllo degli [accessi in base](./security-role-based-access-control.md)al ruolo .

Gemelli digitali di Azure usa anche altre funzionalità di sicurezza che sono presenti in Azure IoT, tra cui Azure Active Directory (Azure AD). Per questo motivo, per la configurazione e la sicurezza delle app create su Gemelli digitali di Azure sono valide molte delle [procedure consigliate per la sicurezza di Azure IoT](../iot-fundamentals/iot-security-best-practices.md).

Questo articolo riepiloga le principali procedure consigliate da seguire.

> [!IMPORTANT]
> Per garantire la massima sicurezza per lo spazio IoT, esaminare altre risorse relative alla sicurezza. Assicurarsi di includere i fornitori di dispositivi.

> [!TIP]
> Usare il Centro sicurezza di [Azure per l'IoT](https://docs.microsoft.com/azure/asc-for-iot/) per rilevare le minacce e le vulnerabilità della sicurezza IoT.Use Azure Security Center for IoT to help detect IoT security threats and vulnerabilities.

## <a name="iot-security-best-practices"></a>Procedure consigliate per la sicurezza IoT

Ecco alcune tra le principali procedure consigliate per proteggere in modo sicuro i dispositivi IoT:

> [!div class="checklist"]
> * Proteggere ogni dispositivo connesso allo spazio IoT in modo da impedirne la manomissione.
> * Limitare il ruolo di ogni dispositivo, sensore e persona all'interno dello spazio IoT. In caso di compromissione, l'impatto sarà ridotto al minimo.
> * Prendere in considerazione l'eventualità di usare il filtro degli indirizzi IP dei dispositivi e la restrizione della porta.
> * Limitare larghezza di banda di I/O e dei dispositivi per migliorare le prestazioni. La limitazione della velocità può consentire di migliorare la sicurezza impedendo attacchi Denial of Service.
> * Mantenere aggiornati il firmware del dispositivo, il sistema operativo e il software.
> * Controlla e rivedi periodicamente le best practice di sicurezza di dispositivi, software, reti e gateway mentre continuano a migliorare ed evolvere.
> * Utilizza sistemi di sicurezza, software e dispositivi affidabili, certificati e conformi. Ad esempio, esaminare le offerte di conformità per Il cloud di Azure.For example, review the [compliance offerings](https://azure.microsoft.com/overview/trusted-cloud/compliance/) for Azure Cloud.

Ecco alcune tra le principali procedure consigliate per proteggere in modo sicuro uno spazio IoT:

> [!div class="checklist"]
> * Crittografare i dati salvati, archiviati o salvati in modo permanente.
> * Richiedere la modifica o l'aggiornamento periodico di password o chiavi.
> * Limitare attentamente le autorizzazioni e l'accesso in base al ruolo. Leggere la sezione Procedure consigliate per il controllo degli [accessi in base](#role-based-access-control-best-practices) al ruolo riportata di seguito.
> * Prendere in considerazione una topologia di rete suddivisa in modo che i dispositivi in ogni rete siano isolati dagli altri.
> * Usare la crittografia avanzata. Richiedere password lunghe, utilizzare protocolli protetti e [autenticazione a più fattori.](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)

[Monitorare](./how-to-configure-monitoring.md) le risorse IoT per individuare outlier, minacce o parametri delle risorse che non rientrano nell'intervallo del normale funzionamento. Usare Azure Analytics per monitorare la gestione.

> [!IMPORTANT]
> Leggi le procedure consigliate per la sicurezza di Azure [IoT](../iot-fundamentals/iot-security-best-practices.md) per iniziare una strategia di sicurezza IoT completa.

> [!NOTE]
> Per altre informazioni sull'elaborazione e il monitoraggio degli eventi, leggere [Route events and messages with Azure Digital Twins](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Procedure consigliate per Azure Active Directory

Azure Digital Twins usa [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/) per autenticare gli utenti e proteggere le applicazioni. Azure Active Directory supporta l'autenticazione per un'ampia gamma di architetture moderne. Tutte queste sono basate sui protocolli standard come OAuth 2.0 o OpenID Connect. Ecco alcune tra le principali procedure consigliate per proteggere lo spazio IoT per Azure Active Directory:

> [!div class="checklist"]
> * Archiviare chiavi e chiavi private delle app di Azure Active Directory in una posizione sicura, ad esempio [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Per l'autenticazione usare un certificato emesso da un'[autorità di certificazione](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) attendibile invece dei segreti delle app.
> * Limitare l'ambito di accesso di OAuth 2.0 per un token.
> * Verificare la durata della validità di un token e se un token rimane valido.
> * Impostare periodi di tempo appropriati per la validità dei token. Aggiornare i token scaduti.
> * Rimuovere le autorizzazioni e gli URI di **reindirizzamento** inutilizzati per le procedure consigliate per il controllo degli [accessi in base](#role-based-access-control-best-practices)al ruolo.

## <a name="role-based-access-control-best-practices"></a>Procedure consigliate per il controllo degli accessi in base al ruolo

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulle procedure consigliate per Azure IoT, vedere [Procedure consigliate per la sicurezza di IoT](../iot-fundamentals/iot-security-best-practices.md).

* Per informazioni sul controllo degli accessi in base al ruolo, vedere [Controllo degli accessi in base](./security-role-based-access-control.md)al ruolo .

* Per informazioni sull'autenticazione, vedere [Autenticazione con le API](./security-authenticating-apis.md).
