---
title: Risolvere i problemi usando il comando dsregcmd-Azure Active Directory
description: Uso dell'output di dsregcmd per comprendere lo stato dei dispositivi in Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ef3edace53cf7367716027811cf3061b617a9a6
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74379210"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>Risoluzione dei problemi relativi ai dispositivi tramite il comando dsregcmd

L'utilità dsregcmd/status deve essere eseguita come account utente di dominio.

## <a name="device-state"></a>Stato del dispositivo

Questa sezione elenca i parametri di stato di join del dispositivo. La tabella seguente elenca i criteri per il dispositivo in diversi Stati di join.

| AzureAdJoined | EnterpriseJoined | DomainJoined | Stato del dispositivo |
| ---   | ---   | ---   | ---   |
| SÌ | NO | NO | Azure AD aggiunto |
| NO | NO | SÌ | Aggiunto a un dominio |
| SÌ | NO | SÌ | Aggiunto AD ibrido |
| NO | SÌ | SÌ | Unitamente a DRS locale |

> [!NOTE]
> Lo stato Workplace Join (Azure AD registrato) viene visualizzato nella sezione "stato utente"

- **AzureAdJoined:** impostare su "Yes" Se il dispositivo è stato aggiunto al Azure ad. "NO" in caso contrario.
- **EnterpriseJoined:** impostare su "Yes" Se il dispositivo è stato aggiunto a un DRS locale. Un dispositivo non può essere sia EnterpriseJoined che AzureAdJoined.
- **DomainJoined:** impostare su "Yes" Se il dispositivo è aggiunto a un dominio (ad).
- **DomainName:** -impostare sul nome del dominio se il dispositivo è aggiunto a un dominio.

### <a name="sample-device-state-output"></a>Esempio di output dello stato del dispositivo

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+
             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : HYBRIDADFS
+----------------------------------------------------------------------+
```

## <a name="device-details"></a>Dettagli dispositivo

Visualizzato solo quando il dispositivo è Azure AD aggiunto o ibrido Azure AD aggiunto (non Azure AD registrato). Questa sezione elenca i dettagli di identificazione del dispositivo archiviati nel cloud.

- **DeviceID:** ID univoco del dispositivo nel tenant Azure ad
- **Identificazione personale:** -identificazione personale del certificato del dispositivo 
- **DeviceCertificateValidity:** validità del certificato del dispositivo
- **KeyContainerId:** -ContainerId della chiave privata del dispositivo associata al certificato del dispositivo
- Chiave-fornito **:** -chiave (hardware/software) usata per archiviare la chiave privata del dispositivo.
- **TpmProtected:** -"Yes" se la chiave privata del dispositivo è archiviata in un TPM hardware.

### <a name="sample-device-details-output"></a>Output dei dettagli del dispositivo di esempio

```
+----------------------------------------------------------------------+
| Device Details                                                       |
+----------------------------------------------------------------------+

                  DeviceId : e92325d0-xxxx-xxxx-xxxx-94ae875dxxxx
                Thumbprint : D293213EF327483560EED8410CAE36BB67208179
 DeviceCertificateValidity : [ 2019-01-11 21:02:50.000 UTC -- 2029-01-11 21:32:50.000 UTC ]
            KeyContainerId : 13e68a58-xxxx-xxxx-xxxx-a20a2411xxxx
               KeyProvider : Microsoft Software Key Storage Provider
              TpmProtected : NO
+----------------------------------------------------------------------+
```

## <a name="tenant-details"></a>Dettagli tenant

Visualizzato solo quando il dispositivo è Azure AD aggiunto o ibrido Azure AD aggiunto (non Azure AD registrato). Questa sezione elenca i dettagli del tenant comune quando un dispositivo viene aggiunto al Azure AD.

> [!NOTE]
> Se gli URL MDM in questa sezione sono vuoti, significa che MDM non è stato configurato o che l'utente corrente non rientra nell'ambito della registrazione MDM. Controllare le impostazioni di mobilità in Azure AD per esaminare la configurazione di MDM.

> [!NOTE]
> Anche se vengono visualizzati URL MDM, questo non significa che il dispositivo è gestito da MDM. Le informazioni vengono visualizzate se il tenant ha una configurazione MDM per la registrazione automatica anche se il dispositivo stesso non è gestito. 

### <a name="sample-tenant-details-output"></a>Output dei dettagli del tenant di esempio

```
+----------------------------------------------------------------------+
| Tenant Details                                                       |
+----------------------------------------------------------------------+

                TenantName : HybridADFS
                  TenantId : 96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
                       Idp : login.windows.net
               AuthCodeUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/authorize
            AccessTokenUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token
                    MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
                 MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
          MdmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance
               SettingsUrl : eyJVxxxxIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyxxxx==
            JoinSrvVersion : 1.0
                JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/
                 JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net
             KeySrvVersion : 1.0
                 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/
                  KeySrvId : urn:ms-drs:enterpriseregistration.windows.net
        WebAuthNSrvVersion : 1.0
            WebAuthNSrvUrl : https://enterpriseregistration.windows.net/webauthn/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
             WebAuthNSrvId : urn:ms-drs:enterpriseregistration.windows.net
    DeviceManagementSrvVer : 1.0
    DeviceManagementSrvUrl : https://enterpriseregistration.windows.net/manage/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
     DeviceManagementSrvId : urn:ms-drs:enterpriseregistration.windows.net
+----------------------------------------------------------------------+
```

## <a name="user-state"></a>Stato dell'utente

Questa sezione elenca lo stato di diversi attributi per l'utente attualmente connesso al dispositivo.

> [!NOTE]
> Il comando deve essere eseguito in un contesto utente per recuperare lo stato valido.

- **NgcSet:** impostare su "Sì" se è impostato un tasto Hello Windows per l'utente che ha eseguito l'accesso corrente.
- **NgcKeyId:** -ID del tasto Hello Windows se ne è stato impostato uno per l'utente connesso corrente.
- **Ripristinare:** indica se il tasto Windows Hello può essere reimpostato dall'utente. 
- **Valori possibili:** -DestructiveOnly, NonDestructiveOnly, DestructiveAndNonDestructive o Unknown se error. 
- **WorkplaceJoined:** impostare su "Yes" se Azure ad account registrati sono stati aggiunti al dispositivo nel contesto Ntuser corrente.
- **WamDefaultSet:** impostare su "Yes" Se viene creato un account WebAccount predefinito WAM per l'utente connesso. Questo campo potrebbe visualizzare un errore se dsreg/status viene eseguito nel contesto di amministrazione. 
- **WamDefaultAuthority:** impostare su "Organizations" per Azure ad.
- **WamDefaultId:** -Always "https://login.microsoft.com" per Azure ad.
- **WamDefaultGUID:** il GUID del provider wam (Azure AD/account Microsoft) per l'account predefinito WAM. 

### <a name="sample-user-state-output"></a>Output dello stato utente di esempio

```
+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

                    NgcSet : YES
                  NgcKeyId : {FA0DB076-A5D7-4844-82D8-50A2FB42EC7B}
                  CanReset : DestructiveAndNonDestructive
           WorkplaceJoined : NO
             WamDefaultSet : YES
       WamDefaultAuthority : organizations
              WamDefaultId : https://login.microsoft.com
            WamDefaultGUID : { B16898C6-A148-4967-9171-64D755DA8520 } (AzureAd)

+----------------------------------------------------------------------+
```

## <a name="sso-state"></a>Stato SSO

Questa sezione può essere ignorata per Azure AD dispositivi registrati.

> [!NOTE]
> Il comando deve essere eseguito in un contesto utente per recuperare lo stato valido per tale utente.

- **AzureAdPrt:** impostare su "Yes" Se nel dispositivo è presente un PRT per l'utente connesso.
- **AzureAdPrtUpdateTime:** -impostare sull'ora UTC in cui è stato eseguito l'ultimo aggiornamento del PRT.
- **AzureAdPrtExpiryTime:** -impostare sull'ora UTC in cui il PRT scadrà se non viene rinnovato.
- **AzureAdPrtAuthority:** -URL autorità Azure ad
- **EnterprisePrt:** impostare su "Yes" Se il dispositivo ha PRT da ADFS locale. Per i dispositivi ibridi Azure AD aggiunti, il dispositivo potrebbe avere PRT sia da Azure AD sia da AD locale simultaneamente. I dispositivi aggiunti in locale avranno solo un PRT Enterprise.
- **EnterprisePrtUpdateTime:** -impostare sull'ora UTC in cui è stato eseguito l'ultimo aggiornamento dell'azienda PRT.
- **EnterprisePrtExpiryTime:** -impostare sull'ora UTC in cui il PRT scadrà se non viene rinnovato.
- **EnterprisePrtAuthority:** -URL autorità ADFS

### <a name="sample-sso-state-output"></a>Output dello stato SSO di esempio

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : YES
      AzureAdPrtUpdateTime : 2019-01-24 19:15:26.000 UTC
      AzureAdPrtExpiryTime : 2019-02-07 19:15:26.000 UTC
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2019-01-24 19:15:33.000 UTC
   EnterprisePrtExpiryTime : 2019-02-07 19:15:33.000 UTC
    EnterprisePrtAuthority : https://fs.hybridadfs.nttest.microsoft.com:443/adfs

+----------------------------------------------------------------------+
```

## <a name="diagnostic-data"></a>Dati di diagnostica

### <a name="pre-join-diagnostics"></a>Diagnostica di pre-join

Questa sezione viene visualizzata solo se il dispositivo è aggiunto a un dominio e non è in grado di ibrido Azure AD join.

Questa sezione esegue vari test per facilitare la diagnosi degli errori di join. In questa sezione sono inoltre inclusi i dettagli della precedente (?). Queste informazioni includono la fase di errore, il codice di errore, l'ID richiesta server, lo stato HTTP risposta server, il messaggio di errore risposta server.

- **Contesto utente:** il contesto in cui vengono eseguite le operazioni di diagnostica. Valori possibili: sistema, utente non con privilegi elevati, utente con privilegi elevati. 

   > [!NOTE]
   > Poiché il join effettivo viene eseguito nel contesto di sistema, l'esecuzione della diagnostica nel contesto di sistema è più vicina allo scenario di join effettivo. Per eseguire la diagnostica in un contesto di sistema, è necessario eseguire il comando dsregcmd/status da un prompt dei comandi con privilegi elevati.

- **Client Time:** ora di sistema in formato UTC.
- **Ad Connectivity test:** -test esegue un test di connettività al controller di dominio. Un errore in questo test comporterà probabilmente errori di join nella fase di controllo preliminare.
- **Test di configurazione di Active Directory:** -il test legge e verifica se l'oggetto SCP è configurato correttamente nella foresta ad locale. Gli errori in questo test potrebbero causare errori di join nella fase di individuazione con il codice di errore 0x801c001d.
- **Test DRS Discovery:** -test ottiene gli endpoint DRS dall'endpoint dei metadati di individuazione ed esegue una richiesta di autenticazione utente. Gli errori in questo test potrebbero causare errori di join nella fase di individuazione.
- **Test di connettività DRS:** -test esegue un test di connettività di base per l'endpoint DRS.
- **Test di acquisizione dei token:** -test tenta di ottenere un token di autenticazione Azure ad se il tenant utente è federato. Gli errori in questo test potrebbero causare errori di join nella fase di autenticazione. Se l'autenticazione non riesce, il join di sincronizzazione verrà tentato come fallback, a meno che il fallback non venga disabilitato esplicitamente con una chiave del registro di sistema
- **Fallback per Sync-join:** -impostare su "Enabled" se la chiave del registro di sistema, per impedire il fallback per sincronizzare il join con errori di autenticazione, non è presente. Questa opzione è disponibile in Windows 10 1803 e versioni successive.
- **Registrazione precedente:** ora in cui si è verificato il tentativo di join precedente. Vengono registrati solo i tentativi di join non riusciti.
- **Fase di errore:** -la fase del join in cui è stata interrotta. I valori possibili sono pre-check, Discovery, auth, join.
- **ErrorCode client:** -codice di errore client restituito (HRESULT).
- **Server ErrorCode:** -codice di errore del server se una richiesta è stata inviata al server e il server ha restituito un codice di errore. 
- **Messaggio Server:** -messaggio Server restituito insieme al codice di errore.
- **Stato https:** -stato HTTP restituito dal server.
- **ID richiesta:** -il client RequestId inviato al server. Utile per la correlazione con i log sul lato server.

### <a name="sample-pre-join-diagnostics-output"></a>Esempio di output di diagnostica pre-join

Nell'esempio seguente viene illustrato un errore di individuazione del test di diagnostica.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:25:31.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x801c000c]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:23:30.000 UTC
               Error Phase : discover
          Client ErrorCode : 0x801c0021

+----------------------------------------------------------------------+
```

Nell'esempio seguente vengono illustrati i test di diagnostica che passano, ma il tentativo di registrazione non è riuscito con un errore di directory, previsto per Sync join. Al termine del processo di sincronizzazione Azure AD Connect, il dispositivo sarà in grado di partecipare.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:16:50.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : PASS
     DRS Connectivity Test : PASS
    Token acquisition Test : PASS
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e

+----------------------------------------------------------------------+
```

### <a name="post-join-diagnostics"></a>Diagnostica post-join

In questa sezione viene visualizzato l'output dei controlli di integrità di integrità eseguiti in un dispositivo aggiunto al cloud.

- **AadRecoveryEnabled:** se l'opzione è impostata su "Sì", le chiavi archiviate nel dispositivo non sono utilizzabili e il dispositivo è contrassegnato per il ripristino. Il successivo accesso attiverà il flusso di ripristino e registrerà nuovamente il dispositivo.
- **KeySignTest:** se "superato", le chiavi del dispositivo sono in stato valido. Se KeySignTest ha esito negativo, il dispositivo viene in genere contrassegnato per il ripristino. Il successivo accesso attiverà il flusso di ripristino e registrerà nuovamente il dispositivo. Per i dispositivi ibridi Azure AD aggiunti il ripristino è invisibile all'utente. Se Azure AD aggiunto o Azure AD registrato, i dispositivi richiederanno l'autenticazione utente per il ripristino e la registrazione del dispositivo, se necessario. **Il KeySignTest richiede privilegi elevati.**

#### <a name="sample-post-join-diagnostics-output"></a>Esempio di output di diagnostica post-join

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisite-check"></a>Controllo dei prerequisiti NGC

Questa sezione esegue i controlli prerequisito per il provisioning di una chiave NGC. 

> [!NOTE]
> Non è possibile visualizzare i dettagli del controllo dei prerequisiti NGC in dsregcmd/status se l'utente ha già configurato le credenziali NGC.

### <a name="sample-ngc-prerequisite-check-output"></a>Output controllo prerequisiti NGC di esempio

```
+----------------------------------------------------------------------+
| Ngc Prerequisite Check                                               |
+----------------------------------------------------------------------+

            IsDeviceJoined : YES
             IsUserAzureAD : YES
             PolicyEnabled : YES
          PostLogonEnabled : YES
            DeviceEligible : YES
        SessionIsNotRemote : YES
            CertEnrollment : enrollment authority
          AdfsRefreshToken : YES
             AdfsRaIsReady : YES
    LogonCertTemplateReady : YES ( StateReady )
              PreReqResult : WillProvision
+----------------------------------------------------------------------+
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le [domande frequenti sulla gestione dei dispositivi](faq.md)
