---
title: Pubblicare Desktop remoto con il proxy applicazione Azure Active Directory
description: Illustra come configurare il proxy applicazione con Servizi Desktop remoto (RDS)
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 03e89b0da25a915a00c70a9a87bd0f675b8e12d6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997529"
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Pubblicare Desktop remoto con il proxy applicazione di Azure AD

Servizi Desktop remoto e il proxy di applicazione di Azure AD si integrano per migliorare la produttività dei dipendenti che si trovano all'esterno della rete aziendale. 

I destinatari di questo articolo sono:
- Attuali clienti del proxy di applicazione di Azure AD che vogliono offrire un maggior numero di applicazioni ai propri utenti finali tramite la pubblicazione di applicazioni locali attraverso Servizi Desktop remoto.
- I clienti attuali di Servizi Desktop remoto che vogliono ridurre la superficie di attacco della propria distribuzione usando il proxy applicazione di AD Azure. Questo scenario fornisce un set di controlli di verifica in due passaggi e di accesso condizionale a Servizi Desktop remoto.

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Ruolo del proxy applicazione nella distribuzione standard di Servizi Desktop remoto

Una distribuzione standard di Servizi Desktop remoto include vari servizi ruolo Desktop remoto in esecuzione su Windows Server. L'[architettura di Servizi Desktop remoto](/windows-server/remote/remote-desktop-services/Desktop-hosting-logical-architecture) offre più opzioni di distribuzione. Diversamente da altre opzioni di distribuzione di Servizi Desktop remoto, la [distribuzione di Servizi Desktop remoto con il proxy di applicazione di AD Azure](/windows-server/remote/remote-desktop-services/Desktop-hosting-logical-architecture) (mostrata nel diagramma seguente) ha una connessione in uscita permanente dal server che esegue il servizio connettore. Altre distribuzioni lasciano le connessioni in ingresso aperte tramite un servizio di bilanciamento del carico.

![Il proxy applicazione si trova tra la VM di Servizi Desktop remoto e la rete Internet pubblica](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

In una distribuzione di Servizi Desktop remoto, il ruolo di Web Desktop remoto e il ruolo di Gateway Desktop remoto vengono eseguiti su computer con connessione Internet. Questi endpoint vengono esposti per i motivi seguenti:
- Web Desktop remoto fornisce all'utente un endpoint pubblico per accedere e visualizzare le applicazioni e i desktop locali vari a cui può accedere. Dopo che è stata selezionata una risorsa, viene creata una connessione RDP tramite l'app nativa nel sistema operativo.
- Gateway Desktop remoto entra in gioco una volta che l'utente avvia la connessione RDP. Gateway Desktop remoto gestisce il traffico RDP crittografato su Internet e lo trasferisce nel server locale a cui si connette l'utente. In questo scenario il traffico che Gateway Desktop remoto riceve proviene dal proxy applicazione di AD Azure.

>[!TIP]
>Se Servizi Desktop remoto non è stato distribuito prima e se si desiderano altre informazioni prima di iniziare, vedere come [distribuire facilmente Servizi Desktop remoto con Azure Resource Manager e Azure Marketplace](/windows-server/remote/remote-desktop-services/rds-in-azure).

## <a name="requirements"></a>Requisiti

- Entrambi gli endpoint Web Desktop remoto e Gateway Desktop remoto devono trovarsi nello stesso computer e avere una radice comune. Web Desktop remoto e Gateway Desktop remoto vengono pubblicati come un'unica applicazione con il proxy di applicazione in modo da offrire un'esperienza di accesso Single Sign-On tra le due applicazioni.

- Si dovrebbe avere già [distribuito Servizi Desktop remoto](/windows-server/remote/remote-desktop-services/rds-in-azure) e avere già [abilitato il proxy applicazione](application-proxy-add-on-premises-application.md).

- Gli utenti finali devono usare un browser compatibile per connettersi al client Web Desktop remoto o desktop remoto. Per altri dettagli, vedere [supporto per le configurazioni client](#support-for-other-client-configurations).

- Quando si pubblica Web Desktop remoto, è consigliabile usare lo stesso FQDN interno ed esterno. Se gli FQDN interni ed esterni sono diversi è consigliabile disabilitare la richiesta di traduzione dell'intestazione per evitare che il client riceva collegamenti non validi.

- Se si utilizza il Web Desktop remoto in Internet Explorer, sarà necessario abilitare il componente aggiuntivo ActiveX di Servizi Desktop remoto.

- Se si usa il client Web Desktop remoto, sarà necessario usare il connettore del proxy di applicazione [1.5.1975 o versione successiva](./application-proxy-release-version-history.md).

- Per il flusso di pre-autenticazione Azure AD, gli utenti possono connettersi solo alle risorse pubblicate nel riquadro **RemoteApp e desktop** . Gli utenti non possono connettersi a un desktop usando il riquadro **Connetti a un PC remoto** .

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>Distribuire lo scenario di Servizi desktop remoto e proxy applicazione congiunti

Dopo avere configurato Servizi Desktop remoto e il proxy applicazione di Azure AD per l'ambiente in uso, seguire questa procedura per combinare le due soluzioni. Questa procedura descrive come pubblicare i due endpoint di Servizi Desktop remoto per il Web (Web Desktop remoto e Gateway Desktop remoto) come applicazioni e quindi come indirizzare il traffico su Servizi Desktop remoto attraverso il proxy applicazione.

### <a name="publish-the-rd-host-endpoint"></a>Pubblicare l'endpoint host di Desktop remoto

1. [Pubblicare un nuovo proxy applicazione](application-proxy-add-on-premises-application.md) con i valori seguenti:
   - URL interno: `https://\<rdhost\>.com/`, dove `\<rdhost\>` è la radice comune condivisa da Web Desktop Remoto e Gateway Desktop remoto.
   - URL esterno: questo campo viene popolato automaticamente in base al nome dell'applicazione, ma è possibile modificarlo. Gli utenti passeranno a questo URL quando accedono a Servizi Desktop remoto.
   - Metodo di autenticazione preliminare: Azure Active Directory
   - Tradurre le intestazioni degli URL: No
2. Assegnare utenti all'applicazione di Desktop remoto pubblicata. Assicurarsi che tutti gli utenti abbiano accesso anche a Servizi Desktop remoto.
3. Lasciare il metodo Single Sign-On per l'applicazione come **Single Sign-On di Azure AD disabilitato**.

   >[!Note]
   >Agli utenti viene richiesto di eseguire l'autenticazione una sola volta per Azure AD e una volta al Web Desktop remoto, ma sono Single Sign-On a Gateway Desktop remoto.

4. Selezionare **Azure Active Directory**, quindi registrazioni per l' **app**. Scegli l'app dall'elenco.
5. In **Gestisci** selezionare **personalizzazione**.
6. Aggiornare il campo **URL della Home page** in modo che punti all'endpoint Web Desktop remoto (ad esempio `https://\<rdhost\>.com/RDWeb` ).

### <a name="direct-rds-traffic-to-application-proxy"></a>Dirigere il traffico di Servizi Desktop remoto verso il proxy applicazione

Connettersi alla distribuzione di Servizi Desktop remoto come amministratore e modificare il nome del server Gateway Desktop remoto per la distribuzione. Questa configurazione garantisce che le connessioni vengano indirizzate tramite il servizio proxy di applicazione di Azure AD.

1. Connettersi al server di Servizi Desktop remoto che esegue il ruolo Gestore connessione Desktop remoto.
2. Avviare **Server Manager**.
3. Selezionare **Servizi Desktop remoto** dal riquadro a sinistra.
4. Selezionare **Panoramica**.
5. Nella sezione Panoramica della distribuzione selezionare il menu a discesa e scegliere **Modificare proprietà di distribuzione**.
6. Nella scheda Gateway Desktop remoto modificare il campo **Nome server** con l'URL esterno che è stato impostato per l'endpoint host di Desktop remoto nel proxy applicazione.
7. Impostare il campo **Metodo di accesso** su **Autenticazione della password**.

   ![Schermata Proprietà di distribuzione in Servizi Desktop remoto](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. Per ogni raccolta, eseguire questo comando. Sostituire *\<yourcollectionname\>* e *\<proxyfrontendurl\>* con le proprie informazioni. Questo comando abilita l'accesso Single Sign-On tra Web Desktop remoto e Gateway Desktop remoto e ottimizza le prestazioni:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Ad esempio:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```
   >[!NOTE]
   >Il comando precedente usa un apice inverso in "`nrequire".

9. Per verificare la modifica delle proprietà RDP personalizzate, nonché visualizzare il contenuto del file RDP che sarà scaricato da Web Desktop remoto per questa raccolta, eseguire il comando seguente:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Dopo avere configurato Desktop remoto, il proxy applicazione di Azure AD diventa il componente con connessione Internet di Servizi Desktop remoto. È possibile rimuovere gli altri endpoint con connessione Internet pubblici nei computer Web Desktop remoto e Gateway Desktop remoto.

### <a name="enable-the-rd-web-client"></a>Abilitare il client Web Desktop remoto
Se si desidera che gli utenti siano in grado di utilizzare il client Web Desktop remoto, attenersi alla procedura descritta in [configurare la desktop remoto client Web per gli utenti](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin) per abilitare questa operazione.

Il client Web di Desktop remoto consente agli utenti di accedere all'infrastruttura Desktop remoto dell'organizzazione tramite un Web browser compatibile con HTML5, ad esempio Microsoft Edge, Internet Explorer 11, Google Chrome, Safari o Mozilla Firefox (v 55.0 e versioni successive).

## <a name="test-the-scenario"></a>Testare lo scenario

Testare lo scenario con Internet Explorer su un computer Windows 7 o 10.

1. Passare all'URL esterno impostato o individuare l'applicazione nel [pannello MyApps](https://myapps.microsoft.com).
2. Viene chiesto di eseguire l'autenticazione ad Azure Active Directory. Usare un account che è stato precedentemente assegnato all'applicazione.
3. Viene chiesto di eseguire l'autenticazione a Web Desktop remoto.
4. Al termine dell'autenticazione di Servizi Desktop remoto, è possibile selezionare il desktop o l'applicazione che si desidera e iniziare a lavorare.

## <a name="support-for-other-client-configurations"></a>Supportare altre configurazione client

La configurazione descritta in questo articolo è relativa all'accesso a Servizi Desktop remoto tramite Web Desktop remoto o il client Web Desktop remoto. Se necessario è possibile comunque supportare altri sistemi operativi o browser. La differenza sta nell'uso del metodo di autenticazione.

| Metodo di autenticazione | Configurazione client supportata |
| --------------------- | ------------------------------ |
| Pre-autenticazione    | Web Desktop remoto-Windows 7/10 con Internet Explorer o la modalità di utilizzo di Microsoft [Edge Chromium](/deployedge/edge-ie-mode) + RDS ActiveX |
| Pre-autenticazione    | Client Web Desktop remoto: Web browser compatibile con HTML5, ad esempio Microsoft Edge, Internet Explorer 11, Google Chrome, Safari o Mozilla Firefox (v 55.0 e versioni successive) |
| Pass-through | Qualsiasi altro sistema operativo che supporta l'applicazione Desktop remoto Microsoft |

Il flusso di pre-autenticazione offre più vantaggi in termini di protezione rispetto al flusso di pass-through. Con la pre-autenticazione è possibile usare Azure AD funzionalità di autenticazione come Single Sign-On, l'accesso condizionale e la verifica in due passaggi per le risorse locali. È anche possibile garantire che solo il traffico autenticato raggiunga la rete.

Per usare l'autenticazione pass-through, sono necessarie solo due modifiche ai passaggi descritti in questo articolo:
1. Nel passaggio 1 [Pubblicare l'endpoint host di Desktop remoto](#publish-the-rd-host-endpoint) impostare il metodo di preautenticazione su **Pass-through**.
2. In [Dirigere il traffico di Servizi Desktop remoto verso il proxy applicazione](#direct-rds-traffic-to-application-proxy), ignorare interamente il passaggio 8.

## <a name="next-steps"></a>Passaggi successivi
- [Abilitare l'accesso remoto a SharePoint con il proxy di applicazione di Azure AD](application-proxy-integrate-with-sharepoint-server.md)
- [Considerazioni relative alla sicurezza quando si accede alle app in remoto usando il proxy applicazione di Azure AD](application-proxy-security.md)
- [Procedure consigliate per il bilanciamento del carico di più server app](application-proxy-high-availability-load-balancing.md#best-practices-for-load-balancing-among-multiple-app-servers)