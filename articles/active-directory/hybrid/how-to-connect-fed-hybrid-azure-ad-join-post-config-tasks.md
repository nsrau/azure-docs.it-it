---
title: "Azure AD Connect: Attività di configurazione successiva all'aggiunta ad Azure AD ibrido | Microsoft Docs"
description: Questo documento illustra nel dettaglio le attività di configurazione posteriori necessarie per completare l'aggiunta ad Azure AD ibrido
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: da5cefbacbd3851d2609a687c1948d9bcba5ffae
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612470"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Attività successive di configurazione per Aggiunta ad Azure AD ibrido

Dopo aver eseguito Azure AD Connect per configurare l'organizzazione per Aggiunta ad Azure AD ibrido esistono alcuni passaggi aggiuntivi che è necessario completare per finalizzare il programma di installazione.  Eseguire solo la procedura che si applica per i dispositivi.

## <a name="1-configure-controlled-rollout-optional"></a>1. configurare l'implementazione controllata (facoltativo)
Tutti i dispositivi aggiunti a un dominio che eseguono la versione di Windows 10 e Windows Server 2016 vengono registrati automaticamente in Azure AD dopo aver completato l'intera procedura di configurazione. Se si preferisce un'implementazione controllata anziché questa registrazione automatica, è possibile usare dei criteri di gruppo per abilitare o disabilitare la distribuzione automatica in modo selettivo.  Questo criterio di gruppo deve essere impostato prima di avviare gli altri passaggi di configurazione:
* Creare un oggetto Criteri di gruppo in Active Directory.
* Denominarlo (ad es. l'aggiunta ad Azure AD ibrido).
* Modificare e passare a: Configurazione computer > criteri > Modelli amministrativi > componenti di Windows > registrazione del dispositivo.

>[!NOTE]
>Per 2012R2 le impostazioni dei criteri sono in **Configurazione computer > Criteri > Modelli amministrativi > Componenti di Windows > Aggiunta all'area di lavoro > Computer client con aggiunta automatica all'area di lavoro**

* Abilitare questa impostazione: registrare i computer aggiunti a un dominio come dispositivi.
* Applicare e fare clic su OK.
* Collegamento dell'oggetto Criteri di gruppo alla posizione di propria scelta (unità organizzativa, gruppo di sicurezza o dominio per tutti i dispositivi).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. configurare la rete con gli endpoint di registrazione del dispositivo
Verificare che gli URL seguenti siano accessibili dall'interno della rete aziendale per la registrazione ad Azure AD:

* `https://enterpriseregistration.windows.net`
* `https://login.microsoftonline.com`
* `https://device.login.microsoftonline.com` 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. implementare WPAD per i dispositivi Windows 10
Se l'organizzazione accede a Internet tramite un proxy in uscita, implementare Web Proxy Auto-Discovery (WPAD) per consentire ai computer Windows 10 di registrarsi ad Azure AD.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. configurare SCP in qualsiasi foresta non configurata da Azure AD Connect 

Il punto di connessione del servizio (SCP) contiene le informazioni del tenant di Azure AD che saranno usate dai dispositivi dell'utente per la registrazione automatica.  Eseguire lo script di PowerShell, ConfigureSCP.ps1, che è stato scaricato da Azure AD Connect.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. configurare tutti i servizi federativi che non sono stati configurati dal Azure AD Connect

Se l'organizzazione usa un servizio di federazione per accedere ad Azure AD, le regole di attestazione nel trust della relying party di Azure AD devono consentire l'autenticazione del dispositivo. Se si usa la federazione con ADFS, passare a [Guida di ADFS](https://aka.ms/aadrptclaimrules) per generare le regole di attestazione. Se si usa una soluzione di federazione non Microsoft, contattare il provider per avere del materiale sussidiario.  

>[!NOTE]
>Se si dispongono di dispositivi di livello inferiore di Windows, il servizio deve supportare il rilascio di attestazioni authenticationmethod e wiaormultiauthn quando si ricevono richieste per il trust di Azure AD. In AD FS, è necessario avere una regola di trasformazione rilascio che trasmetta il metodo di autenticazione.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. abilitare Azure AD SSO seamless per dispositivi Windows di livello inferiore

Se l'organizzazione usa la sincronizzazione del codice Hash della Password o l'autenticazione pass-through per eseguire l'accesso ad Azure AD, abilitare SSO facile di Azure Active Directory con tale metodo di accesso per autenticare i dispositivi Windows di livello inferiore:  https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso. 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. impostare i criteri di Azure AD per i dispositivi Windows di livello inferiore

Per registrare i dispositivi Windows di livello inferiore, è necessario verificare che il criterio di Azure AD consente agli utenti di registrare i dispositivi. 

* Accedere al proprio account nel portale di Azure.
* Passare a: Azure Active Directory > Dispositivi > Impostazioni del dispositivo
* Impostare "Gli utenti possono registrare i propri dispositivi in Azure AD" su ALL.
* Fare clic su Salva.

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. aggiungere Azure AD endpoint ai dispositivi Windows di livello inferiore

Aggiungere l'endpoint di autenticazione dei dispositivi di Azure AD alle aree Intranet locali sui dispositivi Windows di livello inferiore per evitare che vengano visualizzate richieste di certificati durante l'autenticazione del dispositivo: `https://device.login.microsoftonline.com` 

Se si usa [SSO facile](how-to-connect-sso.md), abilitare anche "Consentire aggiornamenti della barra di stato tramite script" in tale area e aggiungere l'endpoint seguente: `https://autologon.microsoftazuread-sso.com` 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. installare Microsoft Workplace Join nei dispositivi Windows di livello inferiore

Questo programma di installazione crea nel sistema del dispositivo un'attività pianificata che viene eseguita nel contesto dell'utente. e attivata nel momento in cui l'utente accede a Windows. L'attività aggiunge automaticamente il dispositivo ad Azure AD con le credenziali utente dopo aver usato l'autenticazione integrata di Windows. L'area download è in https://www.microsoft.com/download/details.aspx?id=53554. 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. configurare criteri di gruppo per consentire la registrazione del dispositivo

Per informazioni su come consentire il join ibrido Azure AD per i singoli dispositivi, vedere [convalida controllata del join Azure ad ibrido](../devices/hybrid-azuread-join-control.md).

## <a name="next-steps"></a>Passaggi successivi
[Configura il writeback dispositivi](how-to-connect-device-writeback.md)
