---
title: "Risoluzione dei problemi relativi a dispositivi Windows 10 e Windows Server 2016 aggiunti all'identità ibrida di Azure Active Directory | Microsoft Docs"
description: "Risoluzione dei problemi relativi a dispositivi Windows 10 e Windows Server 2016 aggiunti all'identità ibrida di Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 51962c14a3c32bbfa9a613fa203cc48cfea50c0b
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-windows-10-and-windows-server-2016-devices"></a>Risoluzione dei problemi relativi a dispositivi Windows 10 e Windows Server 2016 aggiunti all'identità ibrida di Azure Active Directory 

Questo argomento è applicabile ai seguenti client:

-   Windows 10
-   Windows Server 2016

Per altri client Windows, vedere [Risoluzione dei problemi relativi a dispositivi di livello inferiore aggiunti all'identità ibrida di Azure Active Directory](device-management-troubleshoot-hybrid-join-windows-legacy.md).

Questo argomento presuppone che siano stati [configurati dispositivi aggiunti all'identità ibrida di Azure Active Directory](device-management-hybrid-azuread-joined-devices-setup.md) per supportare gli scenari seguenti:

- Accesso condizionale basato su dispositivo

- [Roaming aziendale delle impostazioni](active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello for Business](active-directory-azureadjoin-passport-deployment.md) (Configurare Windows Hello for Business)


Questo documento fornisce indicazioni sulla risoluzione di potenziali problemi. 


Per Windows 10 e Windows Server 2016, l'aggiunta all'identità ibrida di Azure Active Directory supporta l'aggiornamento di Windows del 10 novembre 2015 e versioni successive. È consigliabile usare l'aggiornamento dell'anniversario.

## <a name="step-1-retrieve-the-join-status"></a>Passaggio 1: Recuperare lo stato delle aggiunte 

**Per recuperare lo stato delle aggiunte:**

1. Aprire il prompt dei comandi come amministratore

2. Digitare **dsregcmd /status**



    +----------------------------------------------------------------------+
    | Device State                                                         |  +----------------------------------------------------------------------+
    
        AzureAdJoined: YES
     EnterpriseJoined: NO DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7 Thumbprint: B753A6679CE720451921302CA873794D94C6204A KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9 KeyProvider: Microsoft Platform Crypto Provider TpmProtected: YES KeySignTest: : MUST Run elevated to test.
                  Idp: login.windows.net TenantId: 72b988bf-86f1-41af-91ab-2d7cd011db47 TenantName: Contoso AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ== JoinSrvVersion: 1.0 JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/ JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net KeySrvVersion: 1.0 KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/ KeySrvId: urn:ms-drs:enterpriseregistration.windows.net DomainJoined: YES DomainName: CONTOSO
    
    +----------------------------------------------------------------------+
    | User State                                                           |  +----------------------------------------------------------------------+
    
                 NgcSet: YES
               NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
        WorkplaceJoined: NO
          WamDefaultSet: YES
    WamDefaultAuthority: organizations         WamDefaultId: https://login.microsoft.com       WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)           AzureAdPrt: YES



## <a name="step-2-evaluate-the-join-status"></a>Passaggio 2: Valutare lo stato delle aggiunte 

Esaminare i campi seguenti e assicurarsi che siano presenti i valori previsti:

### <a name="azureadjoined--yes"></a>AzureAdJoined: YES  

Questo campo mostra se il dispositivo è aggiunto ad Azure AD. Se il valore è **NO**, l'aggiunta ad Azure AD non è ancora completata. 

**Possibili cause:**

- L'autenticazione del computer per un'aggiunta non è riuscita.

- È presente un proxy HTTP nell'organizzazione che non può essere individuato dal computer

- Il computer non riesce a raggiungere Azure AD per l'autenticazione o il servizio Registrazione dispositivo Azure per la registrazione

- Il computer non è presente nella rete interna dell'organizzazione o nella VPN con connessione diretta a un controller di dominio locale AD.

- Se il computer dispone di TPM, potrebbe essere in uno stato non valido.

- Verificare nuovamente le configurazioni precedentemente indicate per assicurarsi che non ci siano errori. Esempi comuni:

    - Il server federativo non dispone di endpoint WS-Trust abilitati

    - Il server federativo non consente l'autenticazione in ingresso dai computer nella rete con l'autenticazione integrata di Windows.

    - Non è presente alcun oggetto Punto di connessione del servizio che punti al nome di dominio verificato in Azure AD nella foresta AD a cui appartiene il computer

---

### <a name="domainjoined--yes"></a>DomainJoined: YES  

Questo campo mostra se il dispositivo è aggiunto a un dominio Active Directory locale. Se il valore è **NO**, il dispositivo non riesce a eseguire un'aggiunta all'identità ibrida di Azure AD.  

---

### <a name="workplacejoined--no"></a>WorkplaceJoined: NO  

Questo campo mostra se il dispositivo è registrato con Azure AD come dispositivo personale, contrassegnato come *aggiunto correttamente* all'area di lavoro. Questo valore deve essere **NO** per un computer aggiunto al dominio che è anche aggiunto all'identità ibrida di Azure AD. Se il valore è **YES**, è stato aggiunto un account aziendale o dell'istituto di istruzione prima del completamento dell'aggiunta all'identità ibrida di Azure AD. In questo caso l'account viene ignorato quando si usa la versione anniversario dell'aggiornamento di Windows 10 (1607).

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet: YES e AzureADPrt: YES
  
Questi campi indicano se l'utente è autenticato correttamente in Azure AD durante l'accesso al dispositivo. Se i valori sono **NO**, il motivo potrebbe essere:

- Chiave di archiviazione STK non valida in TPM e associata al dispositivo al momento della registrazione. Verificare KeySignTest durante l'esecuzione con privilegi elevati.

- ID di accesso alternativo

- Proxy HTTP non trovato

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le [domande frequenti sulla gestione dei dispositivi](device-management-faq.md) 