---
title: Risolvere i problemi usando il comando dsregcmd - Azure Active DirectoryTroubleshoot using the dsregcmd command - Azure Active Directory
description: Uso dell'output di dsregcmd per comprendere lo stato dei dispositivi in Azure ADUsing the output from dsregcmd to understand the state of devices in Azure AD
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
ms.openlocfilehash: 2cd782cdab625934fe60617142e5ac0baf756398
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128769"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>Risoluzione dei problemi relativi ai dispositivi utilizzando il comando dsregcmd

L'utilità dsregcmd /status deve essere eseguita come account utente di dominio.

## <a name="device-state"></a>Stato del dispositivo

Questa sezione elenca i parametri dello stato di join del dispositivo. Nella tabella seguente sono elencati i criteri per il dispositivo in vari stati di join.

| AzureAdJoined | ImpresaAJoin | DominioUnito | Stato del dispositivo |
| ---   | ---   | ---   | ---   |
| YES | NO | NO | Azure AD Joined |
| NO | NO | YES | Aggiunta a un dominio |
| YES | NO | YES | Aggiunta ad Active Directory ibridaHybrid AD Joined |
| NO | YES | YES | DrS locale aggiunto |

> [!NOTE]
> Lo stato di aggiunta all'area di lavoro (registrato in Azure AD) viene visualizzato nella sezione "Stato utente"Workplace Join (Azure AD registered) state is displayed in the "User State" section

- **AzureAdJoined:** - Impostare su "YES" se il dispositivo è aggiunto ad Azure AD. "NO" in caso contrario.
- **EnterpriseJoined:** - Impostare su "YES" se il dispositivo è aggiunto a un DRS locale. Un dispositivo non può essere sia EnterpriseJoined che AzureAdJoined.A device cannot be both EnterpriseJoined and AzureAdJoined.
- **DomainJoined:** - Impostare su "YES" se il dispositivo fa parte di un dominio (AD).
- **NomeDominio:** - Impostare il nome del dominio se il dispositivo fa parte di un dominio.

### <a name="sample-device-state-output"></a>Output dello stato del dispositivo di esempioSample device state output

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

Viene visualizzato solo quando il dispositivo è aggiunto ad Azure AD o ad Azure AD ibrido aggiunto (non registrato ad Azure AD). In questa sezione sono elencati i dettagli di identificazione dei dispositivi archiviati nel cloud.

- **DeviceId:** - ID univoco del dispositivo nel tenant di Azure ADDeviceId: - Unique ID of the device in the Azure AD tenant
- **Identificazione personale:** - Identificazione personale del certificato del dispositivo 
- **DeviceCertificateValidity:** - Validità del certificato del dispositivo
- **KeyContainerId:** - ContainerId della chiave privata del dispositivo associata al certificato del dispositivo
- **KeyProvider:** - KeyProvider (Hardware/Software) utilizzato per archiviare la chiave privata del dispositivo.
- **TpmProtected:** - "YES" se la chiave privata del dispositivo è archiviata in un TPM hardware.

### <a name="sample-device-details-output"></a>Output dei dettagli del dispositivo di esempioSample device details output

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

## <a name="tenant-details"></a>Dettagli del tenant

Viene visualizzato solo quando il dispositivo è aggiunto ad Azure AD o ad Azure AD ibrido aggiunto (non registrato ad Azure AD). Questa sezione elenca i dettagli comuni del tenant quando un dispositivo viene aggiunto ad Azure AD.

> [!NOTE]
> Se gli URL MDM in questa sezione sono vuoti, indica che l'MDM non è stato configurato o che l'utente corrente non è nell'ambito della registrazione MDM. Controllare le impostazioni di Mobility in Azure AD per esaminare la configurazione MDM.

> [!NOTE]
> Anche se vedi URL MDM questo non significa che il dispositivo è gestito da un MDM. Le informazioni vengono visualizzate se il tenant ha la configurazione MDM per la registrazione automatica anche se il dispositivo stesso non è gestito. 

### <a name="sample-tenant-details-output"></a>Output dei dettagli del tenant di esempioSample tenant details output

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

Questa sezione elenca lo stato di vari attributi per l'utente attualmente connesso al dispositivo.

> [!NOTE]
> Il comando deve essere eseguito in un contesto utente per recuperare lo stato valido.

- **NgcSet:** - Impostare su "YES" se è impostata una chiave di Windows Hello per l'utente attualmente connesso.
- **NgcKeyId:** - ID della chiave di Windows Hello se uno è impostato per l'utente connesso corrente.
- **CanReset:** - Indica se la chiave di Windows Hello può essere reimpostata dall'utente. 
- **Valori possibili:** - DestructiveOnly, NonDestructiveOnly, DestructiveAndNonDestructive o Unknown se error. 
- **WorkplaceJoined:** - Impostare su "YES" se gli account registrati di Azure AD sono stati aggiunti al dispositivo nel contesto NTUSER corrente.
- **WamDefaultSet:** - Impostare su "YES" se viene creato un WebAccount predefinito WAM per l'utente connesso. Questo campo potrebbe visualizzare un errore se dsreg /status viene eseguito da un prompt dei comandi con privilegi elevati. 
- **WamDefaultAuthority:** - Impostare su "organizzazioni" per Azure AD.
- **WamDefaultId:** -https://login.microsoft.comSempre " " per Azure AD.
- **WamDefaultGUID:** - GUID del provider WAM (account Azure AD/Microsoft) per l'account Web WAM predefinito. 

### <a name="sample-user-state-output"></a>Esempio di output dello stato utente

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

Questa sezione può essere ignorata per i dispositivi registrati di Azure AD.

> [!NOTE]
> Il comando deve essere eseguito in un contesto utente per recuperare lo stato valido per tale utente.

- **AzureAdPrt:** - Impostare su "YES" se un PRT è presente nel dispositivo per l'utente connesso.
- **AzureAdPrtUpdateTime:** - Impostare l'ora UTC in cui è stato eseguito l'ultimo aggiornamento della richiesta PRT.
- **AzureAdPrtExpiryTime:** - Impostare l'ora UTC in cui il PRT sta per scadere se non viene rinnovato.
- **AzureAdPrtAuthority:** - URL dell'autorità di Azure ADAzureAdPrtAuthority: - Azure AD authority URL
- **EnterprisePrt:** - Impostare su "YES" se il dispositivo dispone di PRT da ADFS locale. Per i dispositivi aggiunti ad Azure AD ibridi, il dispositivo potrebbe avere PRT sia da Azure AD che da Active Directory locale contemporaneamente. I dispositivi aggiunti in locale avranno solo un Enterprise PRT.
- **EnterprisePrtUpdateTime:** - Impostare l'ora UTC in cui è stato eseguito l'ultimo aggiornamento di Enterprise PRT.
- **EnterprisePrtExpiryTime:** - Impostare l'ora UTC in cui il PRT sta per scadere se non viene rinnovato.
- **EnterprisePrtAuthority:** - URL dell'autorità ADFS

### <a name="sample-sso-state-output"></a>Output dello stato SSO di esempioSample SSO state output

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

### <a name="pre-join-diagnostics"></a>Diagnostica pre-joinPre-join diagnostics

Questa sezione viene visualizzata solo se il dispositivo fa parte di un dominio e non è in grado di creare un'aggiunta ibrida ad Azure AD.

In questa sezione vengono eseguiti vari test per facilitare la diagnosi degli errori di join. Questa sezione include anche i dettagli del precedente (?). Queste informazioni includono la fase di errore, il codice di errore, l'ID richiesta del server, lo stato http della risposta del server, il messaggio di errore di risposta del server.

- **Contesto utente:** - Il contesto in cui viene eseguita la diagnostica. Valori possibili: SYSTEM, UN-ELEVATED User, ELEVATED User. 

   > [!NOTE]
   > Poiché il join effettivo viene eseguito nel contesto SYSTEM, l'esecuzione della diagnostica nel contesto SYSTEM è più vicina allo scenario di join effettivo. Per eseguire la diagnostica nel contesto SYSTEM, il comando dsregcmd /status deve essere eseguito da un prompt dei comandi con privilegi elevati.

- **Ora client:** - L'ora di sistema in UTC.
- **Test di connettività di Active Directory:** - Test esegue un test di connettività al controller di dominio. Errore in questo test sarà probabilmente causare errori di join in fase di pre-controllo.
- **Test di configurazione di Active Directory:** - Test legge e verifica se l'oggetto SCP è configurato correttamente nella foresta di Active Directory locale. Gli errori in questo test potrebbero generare errori di join nella fase di individuazione con il codice di errore 0x801c001d.
- Test di **individuazione DRS:** - Il test ottiene gli endpoint DRS dall'endpoint dei metadati di individuazione ed esegue una richiesta dell'area di autenticazione utente. Gli errori in questo test potrebbero generare errori di join nella fase di individuazione.
- **Test di connettività DRS:** - Test esegue il test di connettività di base all'endpoint DRS.
- Test di **acquisizione token:** - Test tenta di ottenere un token di autenticazione di Azure AD se il tenant dell'utente è federato. Gli errori in questo test potrebbero generare errori di join nella fase di autenticazione. Se l'autenticazione non riesce il join di sincronizzazione verrà tentato come fallback, a meno che il fallback non venga disabilitato in modo esplicito con le impostazioni della chiave del Registro di sistema riportate di seguito.
```
    Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
    Value: FallbackToSyncJoin
    Type:  REG_DWORD
    Value: 0x0 -> Disabled
    Value: 0x1 -> Enabled
    Default (No Key): Enabled
 ```
- **Fallback a Sync-Join:** - Impostare su "Abilitato" se la chiave del Registro di sistema precedente, per impedire il fallback di sincronizzazione join con errori di autenticazione, NON è presente. Questa opzione è disponibile da Windows 10 1803 e versioni successive.
- **Registrazione precedente:** - Ora in cui si è verificato il tentativo di join precedente. Vengono registrati solo i tentativi di join non riusciti.
- **Fase di errore:** - Fase del join in cui è stato interrotto. I valori possibili sono pre-check, discover, auth, join.
- **Codice errore client:** - Codice di errore client restituito (HRESULT).
- **Codice errore server:** - Codice di errore del server se è stata inviata una richiesta al server e il server ha risposto con un codice di errore. 
- **Messaggio server:** - Messaggio del server restituito insieme al codice di errore.
- **Https Status (Stato https):** - Stato http restituito dal server.
- **ID richiesta:** - Il requestId del client inviato al server. Utile per correlare con i log sul lato server.

### <a name="sample-pre-join-diagnostics-output"></a>Esempio di output diagnostico pre-join

Nell'esempio seguente viene illustrato il test di diagnostica non riuscito con un errore di individuazione.

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

Nell'esempio seguente viene illustrato che i test di diagnostica sono stati superati, ma il tentativo di registrazione non è riuscito con un errore di directory, che è previsto per il join di sincronizzazione. Al termine del processo di sincronizzazione di Azure AD Connect, il dispositivo sarà in grado di partecipare.

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

In questa sezione viene visualizzato l'output dei controlli di integrità eseguiti su un dispositivo aggiunto al cloud.

- **AadRecoveryEnabled:** - Se "Yes", le chiavi archiviate nel dispositivo non sono utilizzabili e il dispositivo è contrassegnato per il ripristino. L'accesso successivo attiverà il flusso di ripristino e registrerà nuovamente il dispositivo.
- **KeySignTest:** - Se "PASSATO" le chiavi del dispositivo sono in buona salute. Se KeySignTest ha esito negativo, il dispositivo verrà in genere contrassegnato per il ripristino. L'accesso successivo attiverà il flusso di ripristino e registrerà nuovamente il dispositivo. Per i dispositivi aggiunti ad Azure AD ibridi, il ripristino è invisibile all'utente. Durante l'aggiunta ad Azure AD o la registrazione di Azure AD, i dispositivi richiederanno l'autenticazione utente per ripristinare e registrare nuovamente il dispositivo, se necessario. **KeySignTest richiede privilegi elevati.**

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

In questa sezione vengono eseguiti i controlli del prerequisito per il provisioning di Windows Hello for Business (WHFB). 

> [!NOTE]
> È possibile che i dettagli del controllo dei prerequisiti NGC non vengano visualizzati in dsregcmd /status se l'utente ha già configurato correttamente WHFB.

- **IsDeviceJoined:** - Impostare su "YES" se il dispositivo è aggiunto ad Azure AD.
- **IsUserAzureAD:** - Impostare su "YES" se l'utente connesso è presente in Azure AD.
- **PolicyEnabled:** - Impostare su "YES" se il criterio WHFB è abilitato nel dispositivo.
- **PostLogonEnabled:** - Impostare su "YES" se la registrazione WHFB viene attivata in modo nativo dalla piattaforma. Se è impostato su "NO", indica che la registrazione di Windows Hello for Business viene attivata da un meccanismo personalizzato
- **DeviceEligible:** - Impostare su "YES" se il dispositivo soddisfa i requisiti hardware per la registrazione con WHFB.
- **SessionIsNotRemote:** - Impostare su "YES" se l'utente corrente è connesso direttamente al dispositivo e non in remoto.
- **CertEnrollment:** - Specifico della distribuzione dell'attendibilità del certificato WHFB, che indica l'autorità di registrazione dei certificati per WHFB. Impostare su "autorità di registrazione" se l'origine del criterio WHFB è Criteri di gruppo, "gestione dei dispositivi mobili" se l'origine è MDM. "nessuno" altrimenti
- **AdfsRefreshToken:** - Specifica della distribuzione di attendibilità dei certificati WHFB. Presente solo se CertEnrollment è "autorità di registrazione". Indica se il dispositivo dispone di un PRT aziendale per l'utente.
- **AdfsRaIsReady:** - Specifica della distribuzione di attendibilità dei certificati WHFB.  Presente solo se CertEnrollment è "autorità di registrazione". Impostare su "YES" se ADFS indicato nei metadati di individuazione che supporta WHFB *e* se è disponibile il modello di certificato di accesso.
- **LogonCertTemplateReady:** - Specifico per la distribuzione di attendibilità del certificato WHFB. Presente solo se CertEnrollment è "autorità di registrazione". Impostare su "YES" se lo stato del modello di certificato di accesso è valido e consente di risolvere i problemi relativi all'UNITÀ di registrazione applicazioni ADFS.
- **PreReqResult:** - Fornisce il risultato di tutta la valutazione dei prerequisiti WHFB. Impostare su "Provisioning" se la registrazione WHFB viene avviata come attività di post-accesso quando l'utente accede la prossima volta.

### <a name="sample-ngc-prerequisite-check-output"></a>Esempio di output di controllo dei prerequisiti NGC

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
