---
title: Risoluzione dei problemi relativi ai dispositivi aggiunti a Azure Active Directory ibrido-Azure Active Directory
description: Risoluzione dei problemi relativi a dispositivi Windows 10 e Windows Server 2016 aggiunti all'identità ibrida di Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49658e3e57748ffb7542508530940aa5331f5db1
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162416"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>Risoluzione dei problemi relativi ai dispositivi ibridi Azure Active Directory aggiunti 

Il contenuto di questo articolo è applicabile ai dispositivi che eseguono Windows 10 o Windows Server 2016.

Per altri client Windows, vedere l'articolo [risoluzione dei problemi relativi ai dispositivi ibridi Azure Active Directory aggiunti a livello inferiore](troubleshoot-hybrid-join-windows-legacy.md).

Questo articolo presuppone che siano stati [configurati dispositivi aggiunti all'identità ibrida di Azure Active Directory](hybrid-azuread-join-plan.md) per supportare gli scenari seguenti:

- Accesso condizionale basato su dispositivo
- [Roaming aziendale delle impostazioni](../active-directory-windows-enterprise-state-roaming-overview.md)
- [Windows Hello for Business](../active-directory-azureadjoin-passport-deployment.md) (Configurare Windows Hello for Business)

Questo documento fornisce indicazioni sulla risoluzione dei problemi per risolvere potenziali problemi. 

Per Windows 10 e Windows Server 2016, l'aggiunta all'identità ibrida di Azure Active Directory supporta l'aggiornamento di Windows del 10 novembre 2015 e versioni successive.

## <a name="troubleshoot-join-failures"></a>Risolvere gli errori di join

### <a name="step-1-retrieve-the-join-status"></a>Passaggio 1: Recuperare lo stato delle aggiunte 

**Per recuperare lo stato delle aggiunte:**

1. Aprire un prompt dei comandi come amministratore
2. Digitare `dsregcmd /status`.

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-xxxx-xxxx-xxxx-2d7cd011xxxx
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```

### <a name="step-2-evaluate-the-join-status"></a>Passaggio 2: Valutare lo stato delle aggiunte 

Esaminare i campi seguenti e assicurarsi che siano presenti i valori previsti:

#### <a name="domainjoined--yes"></a>DomainJoined: Sì  

Questo campo mostra se il dispositivo è aggiunto a un dominio Active Directory locale. Se il valore è **NO**, il dispositivo non riesce a eseguire un'aggiunta all'identità ibrida di Azure AD.  

#### <a name="workplacejoined--no"></a>WorkplaceJoined: NO  

Questo campo mostra se il dispositivo è registrato con Azure AD come dispositivo personale, contrassegnato come *aggiunto correttamente* all'area di lavoro. Questo valore deve essere **NO** per un computer aggiunto al dominio che è anche aggiunto all'identità ibrida di Azure AD. Se il valore è **YES**, è stato aggiunto un account aziendale o dell'istituto di istruzione prima del completamento dell'aggiunta all'identità ibrida di Azure AD. In questo caso l'account viene ignorato quando si usa la versione anniversario dell'aggiornamento di Windows 10 (1607).

#### <a name="azureadjoined--yes"></a>AzureAdJoined: Sì  

Questo campo mostra se il dispositivo è aggiunto ad Azure AD. Se il valore è **NO**, l'aggiunta ad Azure AD non è ancora completata. 

Per ulteriori operazioni di risoluzione dei problemi, procedere con i passaggi successivi.

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>Passaggio 3: Individuare la fase in cui il join ha avuto esito negativo e il codice ErrorCode

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 e versioni successive

Cercare la sottosezione "registrazione precedente" nella sezione "dati di diagnostica" dell'output dello stato del join. Questa sezione viene visualizzata solo se il dispositivo è aggiunto a un dominio e non è in grado di ibrido Azure AD join.
Il campo ' Error Phase ' indica la fase dell'errore di join mentre ' client ErrorCode ' indica il codice di errore dell'operazione di join.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>Versioni precedenti di Windows 10

Utilizzare i registri Visualizzatore eventi per individuare la fase e il codice di errore per gli errori di join.

1. Aprire i registri eventi di **registrazione dispositivo utente** nel Visualizzatore eventi. Disponibile in >  **registri applicazioni e servizi** **registrazione dispositivo utente** **Microsoft** > **Windows** > 
2. Cercare gli eventi con i seguenti EventID compresi 304, 305, 307.

![Evento del log degli errori](./media/troubleshoot-hybrid-join-windows-current/1.png)

![Evento del log degli errori](./media/troubleshoot-hybrid-join-windows-current/2.png)

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>Passaggio 4: Verificare le possibili cause e le soluzioni degli elenchi seguenti

#### <a name="pre-check-phase"></a>Fase di controllo preliminare

Possibili cause dell'errore:

- Il dispositivo non ha alcuna linea di visibilità per il controller di dominio.
   - Il dispositivo deve trovarsi nella rete interna dell'organizzazione o in una VPN con la linea di rete a un controller di dominio di Active Directory locale (AD).

#### <a name="discover-phase"></a>Fase di individuazione

Possibili cause dell'errore:

- L'oggetto del punto di connessione del servizio (SCP) non è stato configurato correttamente. Impossibile leggere l'oggetto SCP dal controller di dominio.
   - È necessario un oggetto SCP valido nella foresta AD a cui appartiene il dispositivo, che punta a un nome di dominio verificato in Azure AD.
   - I dettagli sono disponibili nella sezione [configurare un punto di connessione del servizio](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- Errore durante la connessione e il recupero dei metadati di individuazione dall'endpoint di individuazione.
   - Il dispositivo deve essere in grado di `https://enterpriseregistration.windows.net`accedere al contesto di sistema per individuare gli endpoint di registrazione e autorizzazione. 
   - Se l'ambiente locale richiede un proxy in uscita, l'amministratore IT deve assicurarsi che l'account computer del dispositivo sia in grado di individuare e autenticare automaticamente il proxy in uscita.
- Errore di connessione all'endpoint dell'area di autenticazione utente ed esecuzione dell'individuazione dell'area di autenticazione. (Solo Windows 10 versione 1809 e successive)
   - Il dispositivo deve essere in grado di `https://login.microsoftonline.com`accedere, nel contesto di sistema, per eseguire l'individuazione dell'area di autenticazione per il dominio verificato e determinare il tipo di dominio (gestito/federato).
   - Se l'ambiente locale richiede un proxy in uscita, l'amministratore IT deve verificare che il contesto di sistema nel dispositivo sia in grado di individuare e autenticare automaticamente il proxy in uscita.

**Codici di errore comuni:**

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611)
   - Motivo: Non è possibile leggere l'oggetto SCP e ottenere le informazioni sul tenant Azure AD.
   - Risoluzione: Vedere la sezione [configurare un punto di connessione del servizio](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- **DSREG_AUTOJOIN_DISC_FAILED** (0x801c0021/-2145648607)
   - Motivo: Errore di individuazione generica. Non è stato possibile ottenere i metadati di individuazione da DRS.
   - Risoluzione: Trovare il sottoerrore seguente per approfondire l'analisi.
- **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT**  (0x801c001f/-2145648609)
   - Motivo: Timeout dell'operazione durante l'esecuzione dell'individuazione.
   - Risoluzione: Verificare che `https://enterpriseregistration.windows.net` sia accessibile nel contesto di sistema. Per ulteriori informazioni, vedere la sezione [requisiti di connettività di rete](hybrid-azuread-join-managed-domains.md#prerequisites).
- **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** (0x801c0021/-2145648611)
   - Motivo: Errore di individuazione dell'area di autenticazione generica. Impossibile determinare il tipo di dominio (gestito/federato) dal servizio token di servizio. 
   - Risoluzione: Trovare il sottoerrore seguente per approfondire l'analisi.

**Codici di errore comuni:**

Per trovare il codice di errore per il codice di errore di individuazione, utilizzare uno dei metodi seguenti.

##### <a name="windows-10-1803-and-above"></a>Windows 10 1803 e versioni successive

Cercare "DRS Discovery test" nella sezione "dati di diagnostica" dell'output dello stato del join. Questa sezione viene visualizzata solo se il dispositivo è aggiunto a un dominio e non è in grado di ibrido Azure AD join.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : UN-ELEVATED User
               Client Time : 2019-06-05 08:25:29.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x80072ee2]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

+----------------------------------------------------------------------+
```

##### <a name="older-windows-10-versions"></a>Versioni precedenti di Windows 10

Usare i log di Visualizzatore eventi per individuare la fase e il codice ErrorCode per gli errori di join.

1. Aprire i registri eventi di **registrazione dispositivo utente** nel Visualizzatore eventi. Disponibile in >  **registri applicazioni e servizi** **registrazione dispositivo utente** **Microsoft** > **Windows** > 
2. Cercare gli eventi con il seguente EventID compresi 201

![Evento del log degli errori](./media/troubleshoot-hybrid-join-windows-current/5.png)

###### <a name="network-errors"></a>errori di rete

- **WININET_E_CANNOT_CONNECT** (0x80072EFD/-2147012867)
   - Motivo: Non è stato possibile stabilire una connessione con il server
   - Risoluzione: Verificare la connettività di rete alle risorse Microsoft richieste. Per ulteriori informazioni, vedere [requisiti di connettività di rete](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_TIMEOUT** (0x80072EE2/-2147012894)
   - Motivo: Timeout di rete generale.
   - Risoluzione: Verificare la connettività di rete alle risorse Microsoft richieste. Per ulteriori informazioni, vedere [requisiti di connettività di rete](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_DECODING_FAILED** (0x80072F8F/-2147012721)
   - Motivo: Lo stack di rete non è riuscito a decodificare la risposta dal server.
   - Risoluzione: Verificare che il proxy di rete non interferisca e modifichi la risposta del server.

###### <a name="http-errors"></a>Errori HTTP

- **DSREG_DISCOVERY_TENANT_NOT_FOUND** (0x801c003a/-2145648582)
   - Motivo: Oggetto SCP configurato con ID tenant errato. O non sono state trovate sottoscrizioni attive nel tenant.
   - Risoluzione: Verificare che l'oggetto SCP sia configurato con l'ID tenant Azure AD corretto e con le sottoscrizioni attive o presenti nel tenant.
- **DSREG_SERVER_BUSY** (0x801c0025/-2145648603)
   - Motivo: HTTP 503 dal server DRS.
   - Risoluzione: Il server non è attualmente disponibile. probabilmente, i tentativi di join futuri potranno avere esito positivo quando il server sarà nuovamente online.

###### <a name="other-errors"></a>Altri errori

- **E_INVALIDDATA** (0x8007000d/-2147024883)
   - Motivo: Non è stato possibile analizzare la risposta del server JSON. Probabilmente a causa del proxy che restituisce HTTP 200 con una pagina di autenticazione HTML.
   - Risoluzione: Se l'ambiente locale richiede un proxy in uscita, l'amministratore IT deve verificare che il contesto di sistema nel dispositivo sia in grado di individuare e autenticare automaticamente il proxy in uscita.

#### <a name="authentication-phase"></a>Fase di autenticazione

Applicabile solo per gli account di dominio federato.

Motivi dell'errore:

- Non è possibile ottenere un token di accesso in modalità invisibile all'utente per la risorsa DRS.
   - I dispositivi Windows 10 acquisiscono il token di autenticazione dal servizio federativo usando l'autenticazione integrata di Windows a un endpoint WS-Trust attivo. Dettagli: [Configurazione di Servizio federativo](hybrid-azuread-join-manual.md##set-up-issuance-of-claims)

**Codici di errore comuni:**

Utilizzare i registri Visualizzatore eventi per individuare il codice di errore, il codice di errore, il codice di errore del server e il messaggio di errore del server.

1. Aprire i registri eventi di **registrazione dispositivo utente** nel Visualizzatore eventi. Disponibile in >  **registri applicazioni e servizi** **registrazione dispositivo utente** **Microsoft** > **Windows** > 
2. Cerca gli eventi con gli EventID 305 seguenti

![Evento del log degli errori](./media/troubleshoot-hybrid-join-windows-current/3.png)

##### <a name="configuration-errors"></a>Errori di configurazione

- **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** (0xcaa90017/-894894057)
   - Motivo: Il protocollo di autenticazione non è WS-Trust.
   - Risoluzione: Il provider di identità locale deve supportare WS-Trust 
- **ERROR_ADAL_FAILED_TO_PARSE_XML** (0xcaa9002c/-894894036)
   - Motivo: Il servizio federativo locale non ha restituito una risposta XML.
   - Risoluzione: Verificare che l'endpoint MEX restituisca un XML valido. Verificare che il proxy non interferisca e restituisca risposte non XML.
- **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** (0xcaa90023/-894894045)
   - Motivo: Non è stato possibile individuare l'endpoint per l'autenticazione nome utente/password.
   - Risoluzione: Controllare le impostazioni del provider di identità locale. Verificare che gli endpoint WS-Trust siano abilitati e assicurarsi che la risposta MEX contenga questi endpoint corretti.

##### <a name="network-errors"></a>errori di rete

- **ERROR_ADAL_INTERNET_TIMEOUT** (0xcaa82ee2/-894947614)
   - Motivo: Timeout di rete generale.
   - Risoluzione: Verificare che `https://login.microsoftonline.com` sia accessibile nel contesto di sistema. Verificare che il provider di identità locale sia accessibile nel contesto di sistema. Per ulteriori informazioni, vedere [requisiti di connettività di rete](hybrid-azuread-join-managed-domains.md#prerequisites).
- **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** (0xcaa82efe/-894947586)
   - Motivo: La connessione con l'endpoint di autenticazione è stata interrotta.
   - Risoluzione: Riprovare tra qualche minuto o provare a partecipare da un percorso di rete stabile alternativo.
- **ERROR_ADAL_INTERNET_SECURE_FAILURE** (0xcaa82f8f/-894947441)
   - Motivo: Non è stato possibile convalidare il certificato di Secure Sockets Layer (SSL) inviato dal server.
   - Risoluzione: Verificare lo sfasamento dell'ora del client. Riprovare tra qualche minuto o provare a partecipare da un percorso di rete stabile alternativo. 
- **ERROR_ADAL_INTERNET_CANNOT_CONNECT** (0xcaa82efd/-894947587)
   - Motivo: Tentativo di connessione a `https://login.microsoftonline.com` non riuscito.
   - Risoluzione: Controllare la connessione di `https://login.microsoftonline.com`rete a.

##### <a name="other-errors"></a>Altri errori

- **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** (0xcaa20003/-895352829)
   - Motivo: Il token SAML del provider di identità locale non è stato accettato dal Azure AD.
   - Risoluzione: Controllare le impostazioni del server federativo. Cercare il codice di errore del server nei log di autenticazione.
- **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** (0xcaa90014/-894894060)
   - Motivo: Eccezione di errore segnalata dal server WS-Trust. Impossibile ottenere l'asserzione
   - Risoluzione: Controllare le impostazioni del server federativo. Cercare il codice di errore del server nei log di autenticazione.
- **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** (0xcaa90006/-894894074)
   - Motivo: È stato ricevuto un errore durante il tentativo di ottenere il token di accesso dall'endpoint del token.
   - Risoluzione: Cercare l'errore sottostante nel log ADAL. 
- **ERROR_ADAL_OPERATION_PENDING** (0xcaa1002d/-895418323)
   - Motivo: Errore generale ADAL
   - Risoluzione: Cercare il codice di errore o il codice di errore del server nei log di autenticazione.
    
#### <a name="join-phase"></a>Fase di join

Motivi dell'errore:

Individuare il tipo di registrazione e cercare il codice di errore nell'elenco seguente.

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 e versioni successive

Cercare la sottosezione "registrazione precedente" nella sezione "dati di diagnostica" dell'output dello stato del join. Questa sezione viene visualizzata solo se il dispositivo è aggiunto a un dominio e non è in grado di ibrido Azure AD join.
Il campo ' Registration Type ' indica il tipo di join eseguito.

```
+----------------------------------------------------------------------+
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

#### <a name="older-windows-10-versions"></a>Versioni precedenti di Windows 10

Usare i log di Visualizzatore eventi per individuare la fase e il codice ErrorCode per gli errori di join.

1. Aprire i registri eventi di **registrazione dispositivo utente** nel Visualizzatore eventi. Disponibile in >  **registri applicazioni e servizi** **registrazione dispositivo utente** **Microsoft** > **Windows** > 
2. Cercare gli eventi con il seguente EventID compresi 204

![Evento del log degli errori](./media/troubleshoot-hybrid-join-windows-current/4.png)

##### <a name="http-errors-returned-from-drs-server"></a>Errori HTTP restituiti dal server DRS

- **DSREG_E_DIRECTORY_FAILURE** (0x801c03f2/-2145647630)
   - Motivo: È stata ricevuta una risposta di errore da DRS con ErrorCode: DirectoryError
   - Risoluzione: Per possibili motivi e soluzioni, vedere il codice di errore del server.
- **DSREG_E_DEVICE_AUTHENTICATION_ERROR** (0x801c0002/-2145648638)
   - Motivo: È stata ricevuta una risposta di errore da DRS con ErrorCode: "AuthenticationError" e ErrorSubCode non sono "DeviceNotFound". 
   - Risoluzione: Per possibili motivi e soluzioni, vedere il codice di errore del server.
- **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** (0x801c0006/-2145648634)
   - Motivo: È stata ricevuta una risposta di errore da DRS con ErrorCode: DirectoryError
   - Risoluzione: Per possibili motivi e soluzioni, vedere il codice di errore del server.

##### <a name="tpm-errors"></a>Errori TPM

- **NTE_BAD_KEYSET** (0x80090016/-2146893802)
   - Motivo: Operazione TPM non riuscita o non valida
   - Risoluzione: Probabilmente a causa di un'immagine Sysprep non valida. Verificare che il computer da cui è stata creata l'immagine Sysprep non sia Azure AD aggiunto, ibrido Azure AD aggiunto o Azure AD registrato.
- **TPM_E_PCP_INTERNAL_ERROR** (0x80290407/-2144795641)
   - Motivo: Errore di TPM generico. 
   - Risoluzione: Disabilitare TPM nei dispositivi con questo errore. Windows 10 versione 1809 e successive rileva automaticamente gli errori del TPM e completa il join Azure AD ibrido senza usare il TPM.
- **TPM_E_NOTFIPS** (0x80280036/-2144862154)
   - Motivo: TPM in modalità FIPS attualmente non supportato.
   - Risoluzione: Disabilitare TPM nei dispositivi con questo errore. Windows 1809 rileva automaticamente gli errori del TPM e completa il join Azure AD ibrido senza utilizzare il TPM.
- **NTE_AUTHENTICATION_IGNORED** (0x80090031/-2146893775)
   - Motivo: TPM bloccato.
   - Risoluzione: Errore temporaneo. Attendere il periodo di ricarica. Il tentativo di join dopo un certo tempo dovrebbe avere esito positivo. Altre informazioni sono reperibili nell'articolo [nozioni fondamentali sui TPM](https://docs.microsoft.com/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering)

##### <a name="network-errors"></a>Errori di rete

- **WININET_E_TIMEOUT** (0x80072EE2/-2147012894)
   - Motivo: Timeout generale della rete durante il tentativo di registrare il dispositivo in DRS
   - Risoluzione: Controllare la connettività di `https://enterpriseregistration.windows.net`rete a.
- **WININET_E_NAME_NOT_RESOLVED** (0x80072EE7/-2147012889)
   - Motivo: Non è stato possibile risolvere il nome o l'indirizzo del server.
   - Risoluzione: Controllare la connettività di `https://enterpriseregistration.windows.net`rete a. Assicurarsi che la risoluzione DNS per il nome host sia accurata in n/w e nel dispositivo.
- **WININET_E_CONNECTION_ABORTED** (0x80072efe/-2147012866)
   - Motivo: La connessione al server è stata interrotta in modo anomalo.
   - Risoluzione: Riprovare tra qualche minuto o provare a partecipare da un percorso di rete stabile alternativo.

##### <a name="federated-join-server-errors"></a>Errori del server join federato

| Codice di errore del server | Messaggio di errore del server | Possibili cause | Risoluzione |
| --- | --- | --- | --- |
| DirectoryError | La richiesta è limitata temporaneamente. Provare dopo 300 secondi. | Errore previsto. Probabilmente a causa dell'esecuzione di più richieste di registrazione in rapida successione. | Riprova join dopo il periodo di ricarica |

##### <a name="sync-join-server-errors"></a>Errori del server di sincronizzazione join

| Codice di errore del server | Messaggio di errore del server | Possibili cause | Risoluzione |
| --- | --- | --- | --- |
| DirectoryError | AADSTS90002: Il <UUID> tenant non è stato trovato. Questo errore può verificarsi se non sono presenti sottoscrizioni attive per il tenant. Rivolgersi all'amministratore della sottoscrizione. | L'ID tenant nell'oggetto SCP non è corretto | Verificare che l'oggetto SCP sia configurato con l'ID tenant Azure AD corretto e con le sottoscrizioni attive e presenti nel tenant. |
| DirectoryError | L'oggetto dispositivo in base all'ID specificato non è stato trovato. | Errore previsto per Sync join. L'oggetto dispositivo non è stato sincronizzato da AD Azure AD | Attendere il completamento della sincronizzazione del Azure AD Connect e il successivo tentativo di join dopo il completamento della sincronizzazione risolverà il problema |
| AuthenticationError | Verifica del SID del computer di destinazione | Il certificato nel dispositivo Azure AD non corrisponde al certificato usato per firmare il BLOB durante il join di sincronizzazione. Questo errore indica in genere che la sincronizzazione non è stata ancora completata. |  Attendere il completamento della sincronizzazione del Azure AD Connect e il successivo tentativo di join dopo il completamento della sincronizzazione risolverà il problema |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>Passaggio 5: Raccogli log e Contatta supporto tecnico Microsoft

Ottenere gli script pubblici qui: [ https://1drv.ms/u/s! AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ]( https://1drv.ms/u/s!AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ)

1. Aprire un prompt dei comandi di amministrazione `start_ngc_tracing_public.cmd`ed eseguire.
2. Eseguire i passaggi per riprodurre il problema.
3. Arrestare l'esecuzione dello script di `stop_ngc_tracing_public.cmd`registrazione eseguendo.
4. Zip e inviare i log in `%SYSTEMDRIVE%\TraceDJPP\*` per l'analisi.

## <a name="troubleshoot-post-join-issues"></a>Risolvere i problemi relativi a post-join

### <a name="retrieve-the-join-status"></a>Recuperare lo stato delle aggiunte 

#### <a name="wamdefaultset-yes-and-azureadprt-yes"></a>WamDefaultSet Sì e AzureADPrt: Sì
  
Questi campi indicano se l'utente è autenticato correttamente in Azure AD durante l'accesso al dispositivo. Se i valori sono **NO**, il motivo potrebbe essere:

- Chiave di archiviazione non valida nel TPM associato al dispositivo durante la registrazione. controllare KeySignTest durante l'esecuzione di con privilegi elevati.
- ID di accesso alternativo
- Proxy HTTP non trovato

## <a name="known-issues"></a>Problemi noti
- In Impostazioni-account >-> Accedi all'azienda o all'Istituto di istruzione, Azure AD ibrido dispositivi aggiunti possono visualizzare due account diversi, uno per Azure AD e uno per AD locale, quando si è connessi a hotspot mobili o a reti Wi-Fi esterne. Si tratta solo di un problema dell'interfaccia utente e non ha alcun effetto sulla funzionalità. 
 
## <a name="next-steps"></a>Passaggi successivi

Continuare [la risoluzione dei problemi dei dispositivi tramite il comando dsregcmd](troubleshoot-device-dsregcmd.md)

Per altre informazioni, vedere le [domande frequenti sulla gestione dei dispositivi](faq.md)
