---
title: Applicazione TLS 1,2-servizio di registrazione Azure Active Directory
description: Rimuovere il supporto per TLS 1,0 e 1,1 per il servizio Registrazione dispositivo Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 07/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bb8c6c64e0a68f5176c4eb0c0177c5220394695
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268758"
---
# <a name="enforce-tls-12-for-the-azure-ad-registration-service"></a>Applicare TLS 1,2 per il servizio di registrazione Azure AD

Il servizio di registrazione dispositivo Azure Active Directory (Azure AD) viene usato per connettere i dispositivi al cloud con un'identità del dispositivo. Il servizio Registrazione dispositivo Azure AD supporta attualmente l'uso di Transport Layer Security (TLS) 1,2 per le comunicazioni con Azure. Per garantire la sicurezza e la crittografia ottimale, Microsoft consiglia di disabilitare TLS 1,0 e 1,1. In questo documento vengono fornite informazioni su come verificare che i computer utilizzati per completare la registrazione e comunicano con il servizio Registrazione dispositivo Azure AD utilizzano TLS 1,2.

Il protocollo TLS versione 1,2 è un protocollo di crittografia progettato per fornire comunicazioni sicure. Lo scopo principale del protocollo TLS è garantire la privacy e l'integrità dei dati. TLS ha attraversato molte iterazioni con la versione 1,2 definita in [RFC 5246 (External link)](https://tools.ietf.org/html/rfc5246).

L'analisi corrente delle connessioni Mostra un utilizzo ridotto di TLS 1,1 e 1,0, ma queste informazioni vengono fornite in modo da poter aggiornare eventuali client o server interessati prima che il supporto per TLS 1,1 e 1,0 termini. Se si usa un'infrastruttura locale per scenari ibridi o Active Directory Federation Services (AD FS), assicurarsi che l'infrastruttura sia in grado di supportare sia le connessioni in ingresso che quelle in uscita che usano TLS 1,2.

## <a name="update-windows-servers"></a>Aggiornare i server Windows

Per i server Windows che usano il servizio Registrazione dispositivo Azure AD o fungono da proxy, attenersi alla procedura seguente per verificare che TLS 1,2 sia abilitato:

> [!IMPORTANT]
> Dopo aver aggiornato il registro di sistema, è necessario riavviare Windows Server per rendere effettive le modifiche.

### <a name="enable-tls-12"></a>Abilitare TLS 1.2

Verificare che le stringhe del registro di sistema seguenti siano configurate come illustrato:

- HKEY_LOCAL_MACHINE \SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ client
  - "DisabledByDefault" = DWORD: 00000000
  - "Enabled" = DWORD: 00000001
- HKEY_LOCAL_MACHINE \SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ server
  - "DisabledByDefault" = DWORD: 00000000
  - "Enabled" = DWORD: 00000001
- HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft \. NETFramework\v4.0.30319
  - "SchUseStrongCrypto"=dword:00000001

## <a name="update-non-windows-proxies"></a>Aggiornare i proxy non Windows

Tutti i computer che fungono da proxy tra dispositivi e il servizio Registrazione dispositivo Azure AD devono assicurarsi che TLS 1,2 sia abilitato. Seguire le indicazioni del fornitore per garantire il supporto.

## <a name="update-ad-fs-servers"></a>Aggiornare i server AD FS

Tutti i server AD FS usati per comunicare con il servizio Registrazione dispositivo Azure AD devono assicurarsi che TLS 1,2 sia abilitato. Per informazioni su come abilitare o verificare questa configurazione, vedere [Managing SSL/TLS Protocols and Cipher Suites for ad FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) .

## <a name="client-updates"></a>Aggiornamenti client

Poiché tutte le combinazioni client-server e browser-server devono usare TLS 1,2 per connettersi al servizio Registrazione dispositivo Azure AD, potrebbe essere necessario aggiornare questi dispositivi.

I client seguenti sono noti per non essere in grado di supportare TLS 1,2. Aggiornare i client per garantire l'accesso ininterrotto.

- Android versione 4,3 e versioni precedenti
- Firefox versione 5,0 e versioni precedenti
- Internet Explorer versioni 8-10 in Windows 7 e versioni precedenti
- Internet Explorer 10 in Windows Phone 8,0
- Versione di Safari 6.0.4 in OS X 10.8.4 e versioni precedenti

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di TLS/SSL (SSP Schannel)](/windows-server/security/tls/tls-ssl-schannel-ssp-overview)