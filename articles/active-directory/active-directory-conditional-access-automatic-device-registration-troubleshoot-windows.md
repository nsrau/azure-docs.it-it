---
title: Risoluzione dei problemi di registrazione automatica di computer aggiunti al dominio Azure AD per client di livello inferiore di Windows 10 e Windows Server 2016 | Microsoft Docs
description: Risoluzione dei problemi di registrazione automatica di computer aggiunti al dominio Azure AD per client di livello inferiore di Windows 10 e Windows Server 2016.
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
ms.date: 02/15/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: d3688b6a71dcd9964839da3cda35413d00f362d1
ms.openlocfilehash: 836d43b17515beb565748f28603069c7c6a52fbf
ms.lasthandoff: 02/15/2017


---
# <a name="troubleshooting-auto-registration-of-domain-joined-computers-to-azure-ad--windows-10-and-windows-server-2016"></a>Risoluzione dei problemi di registrazione automatica di computer aggiunti al dominio di Azure AD - Windows 10 e Windows Server 2016

Questo argomento è applicabile ai seguenti client:

-    Windows 10
-    Windows Server 2016

Per altri client Windows, vedere [Risoluzione dei problemi di registrazione automatica di computer aggiunti al dominio Azure AD per client di livello inferiore di Windows](active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy.md).

Questo argomento presuppone che la registrazione automatica dei dispositivi aggiunti a un dominio sia stata configurata come descritto in [Come configurare la registrazione automatica dei dispositivi aggiunti al dominio di Windows con Azure Active Directory](active-directory-conditional-access-automatic-device-registration-get-started.md) per il supporto agli scenari seguenti:

1.    [Accesso condizionale basato su dispositivo](active-directory-conditional-access-automatic-device-registration-setup.md)

2.    [Roaming aziendale delle impostazioni](active-directory-windows-enterprise-state-roaming-overview.md)

3.    [Windows Hello for Business](active-directory-azureadjoin-passport-deployment.md) (Configurare Windows Hello for Business)


Questo documento fornisce indicazioni sulla risoluzione di potenziali problemi. 

La registrazione è supportata in nell'aggiornamento di Windows 10 di novembre 2015 e versioni successive.  
È consigliabile usare l'aggiornamento dell'anniversario per abilitare gli scenari descritti in precedenza.

## <a name="step-1-retrieve-the-registration-status"></a>Passaggio 1: Recuperare lo stato della registrazione 

**Per recuperare lo stato della registrazione:**

1. Aprire il prompt dei comandi come amministratore.

2. Digitare **dsregcmd /status**



    +----------------------------------------------------------------------+
    | Device State                                                         |  +----------------------------------------------------------------------+
    
        AzureAdJoined : YES
     EnterpriseJoined : NO DeviceId : 5820fbe9-60c8-43b0-bb11-44aee233e4e7 Thumbprint : B753A6679CE720451921302CA873794D94C6204A KeyContainerId : bae6a60b-1d2f-4d2a-a298-33385f6d05e9 KeyProvider : Microsoft Platform Crypto Provider TpmProtected : YES KeySignTest: : MUST Run elevated to test.
                  Idp : login.windows.net TenantId : 72b988bf-86f1-41af-91ab-2d7cd011db47 TenantName : Contoso AuthCodeUrl : https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize AccessTokenUrl : https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx dmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance SettingsUrl : eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ== JoinSrvVersion : 1.0 JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/ JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net KeySrvVersion : 1.0 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/ KeySrvId : urn:ms-drs:enterpriseregistration.windows.net DomainJoined : YES DomainName : CONTOSO
    
    +----------------------------------------------------------------------+
    | User State                                                           |  +----------------------------------------------------------------------+
    
                 NgcSet : YES
               NgcKeyId : {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
        WorkplaceJoined : NO
          WamDefaultSet : YES
    WamDefaultAuthority : organizations         WamDefaultId : https://login.microsoft.com       WamDefaultGUID : {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)           AzureAdPrt : YES



## <a name="step-2-evaluate-the-registration-status"></a>Passaggio 2: Valutare lo stato della registrazione 

Esaminare i campi seguenti e assicurarsi che siano presenti i valori previsti:

### <a name="azureadjoined--yes"></a>AzureAdJoined: YES  

Questo campo indica se il dispositivo è registrato con Azure AD. Se il valore visualizzato è "NO", la registrazione non è stata completata. 

**Possibili cause:**

1.    Autenticazione del computer non riuscita per la registrazione.

2.    È presente un proxy HTTP nell'organizzazione che non può essere individuato dal computer

3.    Il computer non riesce a raggiungere Azure AD per l'autenticazione o il servizio Registrazione dispositivo Azure per la registrazione

4.    Il computer non è presente nella rete interna dell'organizzazione o nella VPN con connessione diretta a un controller di dominio locale AD.

5.    Se il computer dispone di TPM, potrebbe essere in uno stato non valido.

6.    Verificare nuovamente le configurazioni precedentemente indicate per assicurarsi che non ci siano errori. Esempi comuni:

    1.    Il server federativo non dispone di endpoint WS-Trust abilitati

    2.    Il server federativo potrebbe non consentire l'autenticazione in ingresso dai computer nella rete con l'autenticazione integrata di Windows.

    3.    Non è presente alcun oggetto Punto di connessione del servizio che punti al nome di dominio verificato in Azure AD nella foresta AD a cui appartiene il computer

---

### <a name="domainjoined--yes"></a>DomainJoined: YES  

Questo campo indica se il dispositivo è aggiunto a un dominio Active Directory locale. Se il valore visualizzato è "NO", il dispositivo non può eseguire la registrazione automatica con Azure AD. Verificare innanzitutto che il dispositivo sia aggiunto al dominio Active Directory locale prima della registrazione in Azure AD. Se si sta cercando di aggiungere il computer direttamente in Azure AD, passare a Ulteriori informazioni sulle funzionalità di Aggiunta ad Azure Active Directory.

---

### <a name="workplacejoined--no"></a>WorkplaceJoined: NO  

Questo campo indica se il dispositivo è registrato con Azure AD ma come dispositivo personale, contrassegnato come aggiunto correttamente all'area di lavoro. Il valore visualizzato deve essere "NO" per un computer aggiunto a un dominio e registrato con Azure AD. Se è "YES", significa che è stato aggiunto un account aziendale o dell'istituto di istruzione prima del completamento della registrazione. In questo caso l'account verrà ignorato se si usa l'aggiornamento dell'anniversario di Windows 10 (1607 quando si usa il comando WinVer nella finestra "Esegui" o in un prompt dei comandi).

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet: YES e AzureADPrt: YES
  
Questi campi mostrano che l'utente è autenticato correttamente in Azure AD durante l'accesso al dispositivo. Se il valore visualizzato è "NO", di seguito vengono elencate alcune possibili cause:

1.    Chiave di archiviazione STK non valida in TPM e associata al dispositivo al momento della registrazione. Verificare KeySignTest durante l'esecuzione con privilegi elevati.

2.    ID di accesso alternativo

3.    Proxy HTTP non trovato

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere [Domande frequenti sulla registrazione automatica dei dispositivi](active-directory-conditional-access-automatic-device-registration-faq.md) 
