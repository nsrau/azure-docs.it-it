---
title: Autenticazione RADIUS con Azure Active Directory
description: Indicazioni sull'architettura per ottenere l'autenticazione RADIUS con Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ec8062eb864c877b0f3659ca2dd8f103e935071
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836785"
---
# <a name="radius-authentication-with-azure-active-directory"></a>Autenticazione RADIUS con Azure Active Directory

Remote Authentication Dial-In User Service (RADIUS) è un protocollo di rete che protegge una rete consentendo l'autenticazione e l'autorizzazione centralizzate degli utenti con connessione remota. Molte applicazioni si basano ancora sul protocollo RADIUS per l'autenticazione degli utenti.

Microsoft Windows Server dispone di un ruolo denominato server dei criteri di rete, che può fungere da server RADIUS e supportare l'autenticazione RADIUS.

Azure Active Directory (Azure AD) Abilita la funzionalità di autenticazione a più fattori con sistemi basati su RADIUS. Se un cliente vuole applicare Azure AD Multi-Factor Authentication a uno dei carichi di lavoro RADIUS indicati in precedenza, è possibile installare il Azure AD Multi-Factor Authentication estensione NPS nel server dei criteri di servizio Windows. 

Il server dei criteri di criteri di Windows autentica le credenziali dell'utente rispetto a Active Directory e quindi Invia la richiesta di Multi-Factor Authentication ad Azure. L'utente riceve quindi una richiesta di verifica per l'autenticatore mobile. Una volta completata l'operazione, l'applicazione client potrà connettersi al servizio. 

## <a name="use-when"></a>Utilizzare quando: 

È necessario aggiungere Multi-Factor Authentication ad applicazioni come
* una rete privata virtuale (VPN)
* Accesso Wi-Fi
* Gateway Desktop remoto (RDG)
* Virtual Desktop Infrastructure (VDI)
* Tutti gli altri che dipendono dal protocollo RADIUS per autenticare gli utenti nel servizio. 

> [!NOTE]
> Invece di basarsi su RADIUS e sull'Azure AD Multi-Factor Authentication estensione NPS per applicare Azure AD Multi-Factor Authentication ai carichi di lavoro VPN, è consigliabile aggiornare le VPN a SAML e federare direttamente la VPN con Azure AD. In questo modo, la tua VPN offre una gamma completa di protezione Azure AD, tra cui l'accesso condizionale, Multi-Factor Authentication, la conformità dei dispositivi e la protezione delle identità.

![diagramma dell'architettura](./media/authentication-patterns/radius-auth.png)


## <a name="components-of-the-system"></a>Componenti del sistema 

* **Applicazione client (client VPN)**: Invia la richiesta di autenticazione al client RADIUS.

* **Client RADIUS**: converte le richieste dall'applicazione client e le invia al server RADIUS in cui è installata l'estensione NPS.

* **Server RADIUS**: si connette con Active Directory per eseguire l'autenticazione primaria per la richiesta RADIUS. Al termine dell'operazione, la richiesta viene passata a Azure AD Multi-Factor Authentication estensione NPS.

* **Estensione NPS**: attiva una richiesta di Azure ad multi-factor authentication per un'autenticazione secondaria. In caso di esito positivo, l'estensione NPS completa la richiesta di autenticazione fornendo al server RADIUS i token di sicurezza che includono Multi-Factor Authentication attestazione, rilasciato dal servizio token di sicurezza di Azure.

* **Azure AD multi-factor authentication**: comunica con Azure ad per recuperare i dettagli dell'utente ed esegue un'autenticazione secondaria usando un metodo di verifica configurato dall'utente.

## <a name="implement-radius-with-azure-ad"></a>Implementare RADIUS con Azure AD 

* [Fornire Azure AD funzionalità di Multi-Factor Authentication utilizzando NPS](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension) 

* [Configurare l'estensione server dei criteri di Azure AD Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-advanced) 

* [VPN con Azure AD Multi-Factor Authentication mediante l'estensione NPS](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-vpn) 

  
‎ 

 
