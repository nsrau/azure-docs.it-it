---
title: Pubblicare Desktop remoto con il proxy applicazione di Azure AD | Microsoft Docs
description: Tratta i fondamenti dei connettori del proxy applicazione di Azure AD.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2017
ms.author: kgremban
ms.custom: it-pro
ms.reviewer: harshja
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: a66081596f2e8234f6169faa58c571420e706c45
ms.contentlocale: it-it
ms.lasthandoff: 06/15/2017

---

# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Pubblicare Desktop remoto con il proxy applicazione di Azure AD

Questo articolo descrive come distribuire Servizi Desktop remoto con il proxy applicazione in modo che gli utenti remoti restino produttivi.

I destinatari di questo articolo sono:
- I clienti attuali del proxy applicazione di Azure AD che vogliono offrire un maggior numero di applicazioni ai propri utenti finali tramite la pubblicazione di applicazioni locali attraverso Servizi Desktop remoto.
- I clienti attuali di Servizi Desktop remoto che vogliono ridurre la superficie di attacco della propria distribuzione usando il proxy applicazione di AD Azure. Questo scenario fornisce un set limitato di controlli di verifica in due passaggi e di controlli di accesso condizionale per Servizi Desktop remoto.

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Ruolo del proxy applicazione nella distribuzione standard di Servizi Desktop remoto

Una distribuzione standard di Servizi Desktop remoto include vari servizi ruolo Desktop remoto in esecuzione su Windows Server. L'[architettura di Servizi Desktop remoto](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) offre più opzioni di distribuzione. La differenza più evidente tra la [distribuzione di Servizi Desktop remoto con proxy applicazione di AD Azure](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) (illustrata nella figura seguente) e le altre opzioni di distribuzione è che lo scenario con il proxy applicazione dispone di una connessione permanente in uscita dal server che segue il servizio connettore. Altre distribuzioni lasciano le connessioni in ingresso aperte tramite un servizio di bilanciamento del carico.

![Il proxy applicazione si trova tra la VM di Servizi Desktop remoto e la rete Internet pubblica](./media/application-proxy-publish-remote-desktop/rds-with-app-proxy.png)

In una distribuzione di Servizi Desktop remoto, il ruolo di Web Desktop remoto e il ruolo di Gateway Desktop remoto vengono eseguiti su computer con connessione Internet. Questi endpoint vengono esposti per i motivi seguenti:
- Web Desktop remoto fornisce all'utente un endpoint pubblico per accedere e visualizzare le applicazioni e i desktop locali vari a cui può accedere. Dopo che è stata selezionata una risorsa, viene creata una connessione RDP tramite l'app nativa nel sistema operativo.
- Gateway Desktop remoto entra in gioco una volta che l'utente avvia la connessione RDP. Gateway Desktop remoto gestisce il traffico RDP crittografato su Internet e lo trasferisce nel server locale a cui si connette l'utente. In questo scenario il traffico che Gateway Desktop remoto riceve proviene dal proxy applicazione di AD Azure.

>[!TIP]
>Se Servizi Desktop remoto non è stato distribuito prima e se si desiderano altre informazioni prima di iniziare, vedere come [distribuire facilmente Servizi Desktop remoto con Azure Resource Manager e Azure Marketplace](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure).

## <a name="requirements"></a>Requisiti

- Entrambi gli endpoint Web Desktop remoto e Gateway Desktop remoto devono trovarsi nello stesso computer e avere una radice comune. Web Desktop remoto e Gateway Desktop remoto verranno pubblicati come un'unica applicazione in modo da consentire un'esperienza di accesso Single Sign-On tra le due applicazioni.

- Si dovrebbe avere già [distribuito Servizi Desktop remoto](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure) e avere già [abilitato il proxy applicazione](active-directory-application-proxy-enable.md).

- Questo scenario presuppone che gli utenti finali usino Internet Explorer su PC desktop Windows 7 o Windows 10 che si connettono tramite la pagina di Web Desktop remoto. Se è necessario supportare altri sistemi operativi, vedere [Supportare altre configurazione client](#support-for-other-client-configurations).

  >[!NOTE]
  >Windows 10 Creators Update non è attualmente supportato.

- In Internet Explorer abilitare il componente aggiuntivo ActiveX di Servizi Desktop remoto.

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>Distribuire lo scenario di Servizi desktop remoto e proxy applicazione congiunti

Dopo avere configurato Servizi Desktop remoto e il proxy applicazione di Azure AD per l'ambiente in uso, seguire questa procedura per combinare le due soluzioni. Questa procedura descrive come pubblicare i due endpoint di Servizi Desktop remoto per il Web (Web Desktop remoto e Gateway Desktop remoto) come applicazioni e quindi come indirizzare il traffico su Servizi Desktop remoto attraverso il proxy applicazione.

### <a name="publish-the-rd-host-endpoint"></a>Pubblicare l'endpoint host di Desktop remoto

1. [Pubblicare un nuovo proxy applicazione](application-proxy-publish-azure-portal.md) con i valori seguenti:
   - URL interno: https://\<rdhost\>.com, dove \<rdhost\> è la radice comune condivisa da Web Desktop remoto e Gateway Desktop remoto.
   - URL esterno: questo campo viene popolato automaticamente in base al nome dell'applicazione, ma è possibile modificarlo. Gli utenti passeranno a questo URL quando accedono a Servizi Desktop remoto.
   - Metodo di autenticazione preliminare: Azure Active Directory
   - Tradurre le intestazioni degli URL: No
2. Assegnare utenti all'applicazione di Desktop remoto pubblicata. Assicurarsi che tutti gli utenti abbiano accesso anche a Servizi Desktop remoto.
3. Lasciare il metodo Single Sign-On per l'applicazione come **Single Sign-On di Azure AD disabilitato**. Agli utenti viene richiesto di eseguire l'autenticazione una volta in Azure AD e una volta in Web Desktop remoto, ma dispongono dell'accesso Single Sign-On a Gateway Desktop remoto.
4. Andare a **Azure Active Directory** > **Registrazioni per l'app** > *Applicazione* > **Impostazioni**.
5. Selezionare **Proprietà** e aggiornare il campo **URL della pagina iniziale** in modo che punti all'endpoint di Web Desktop remoto, ad esempio https://\<rdhost\>.com/RDWeb.

### <a name="direct-rds-traffic-to-application-proxy"></a>Dirigere il traffico di Servizi Desktop remoto verso il proxy applicazione

Connettersi alla distribuzione di Servizi Desktop remoto come amministratore e modificare il nome del server Gateway Desktop remoto per la distribuzione. Ciò garantisce che le connessioni vengano indirizzate tramite il proxy applicazione di Azure AD.

1. Connettersi al server di Servizi Desktop remoto che esegue il ruolo Gestore connessione Desktop remoto.
2. Avviare **Server Manager**.
3. Selezionare **Servizi Desktop remoto** dal riquadro a sinistra.
4. Selezionare **Panoramica**.
5. Nella sezione Panoramica della distribuzione selezionare il menu a discesa e scegliere **Modificare proprietà di distribuzione**.
6. Nella scheda Gateway Desktop remoto modificare il campo **Nome server** con l'URL esterno che è stato impostato per l'endpoint host di Desktop remoto nel proxy applicazione.
7. Impostare il campo **Metodo di accesso** su **Autenticazione della password**.

  ![Schermata Proprietà di distribuzione in Servizi Desktop remoto](./media/application-proxy-publish-remote-desktop/rds-deployment-properties.png)

8. Per ogni raccolta, eseguire il comando seguente. Sostituire *\<yourcollectionname\>* e *\<proxyfrontendurl\>* con le proprie informazioni. Questo comando abilita l'accesso Single Sign-On tra Web Desktop remoto e Gateway Desktop remoto e ottimizza le prestazioni:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Ad esempio:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://gateway.contoso.msappproxy.net/`nrequire pre-authentication:i:1"
   ```

9. Per verificare la modifica delle proprietà RDP personalizzate, nonché visualizzare il contenuto del file RDP che sarà scaricato da Web Desktop remoto per questa raccolta, eseguire il comando seguente:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Dopo avere configurato Desktop remoto, il proxy applicazione di Azure AD diventa il componente con connessione Internet di Servizi Desktop remoto. È possibile rimuovere gli altri endpoint con connessione Internet pubblici nei computer Web Desktop remoto e Gateway Desktop remoto.

## <a name="test-the-scenario"></a>Testare lo scenario

Testare lo scenario con Internet Explorer su un computer Windows 7 o 10.

1. Passare all'URL esterno impostato o individuare l'applicazione nel [pannello MyApps](https://myapps.microsoft.com).
2. Viene chiesto di eseguire l'autenticazione ad Azure Active Directory. Usare un account che è stato precedentemente assegnato all'applicazione.
3. Viene chiesto di eseguire l'autenticazione a Web Desktop remoto.
4. Al termine dell'autenticazione di Servizi Desktop remoto, è possibile selezionare il desktop o l'applicazione che si desidera e iniziare a lavorare.

## <a name="support-for-other-client-configurations"></a>Supportare altre configurazione client

La configurazione descritta in questo articolo è rivolta agli utenti di Windows 7 o 10 che usano Internet Explorer e il componente aggiuntivo ActiveX di Servizi Desktop remoto. Se necessario è possibile comunque supportare altri sistemi operativi o browser. La differenza sta nell'uso del metodo di autenticazione.

| Metodo di autenticazione | Configurazione client supportata |
| --------------------- | ------------------------------ |
| Pre-autenticazione    | Windows 7/10 con Internet Explorer + componente aggiuntivo ActiveX di Servizi Desktop remoto |
| Pass-through | Qualsiasi altro sistema operativo che supporta l'applicazione Desktop remoto Microsoft |

Il flusso di pre-autenticazione offre più vantaggi in termini di protezione rispetto al flusso di pass-through. Con la pre-autenticazione è possibile sfruttare le funzionalità di autenticazione di Azure AD, ad esempio Single Sign-On, accesso condizionale e verifica in due passaggi per le risorse locali. È anche possibile garantire che solo il traffico autenticato raggiunga la rete.

Per usare l'autenticazione pass-through, sono necessarie solo due modifiche ai passaggi descritti in questo articolo:
1. Nel passaggio 1 [Pubblicare l'endpoint host di Desktop remoto](#publish-the-rd-host-endpoint) impostare il metodo di preautenticazione su **Pass-through**.
2. In [Dirigere il traffico di Servizi Desktop remoto verso il proxy applicazione](#direct-rds-traffic-to-application-proxy), ignorare interamente il passaggio 8.

## <a name="next-steps"></a>Passaggi successivi

[Abilitare l'accesso remoto a SharePoint con il proxy applicazione di Azure AD](application-proxy-enable-remote-access-sharepoint.md)  
[Considerazioni relative alla sicurezza quando si accede alle app in remoto usando il proxy applicazione di Azure AD](application-proxy-security-considerations.md)

