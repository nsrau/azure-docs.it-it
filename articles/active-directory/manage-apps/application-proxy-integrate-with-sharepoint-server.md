---
title: Abilitare l'accesso remoto a SharePoint - Proxy di applicazione di Azure ADEnable remote access to SharePoint - Azure AD Application Proxy
description: Offre le informazioni di base necessarie per integrare un server SharePoint locale con il proxy di applicazione di Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 983470994c103cb25d0d2aff96ae8544080e6288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481297"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Abilitare l'accesso remoto a SharePoint con il proxy di applicazione di Azure AD

Questa guida dettagliata illustra come integrare una farm di SharePoint locale con il proxy di applicazione di Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Prerequisiti

Per eseguire la configurazione, sono necessarie le risorse seguenti:To perform the configuration, you need the following resources:
- Una farm di SharePoint 2013 o versione più recente.
- Un tenant di Azure AD con un piano che include il proxy di applicazione. Altre informazioni sui piani e sui [prezzi di Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).
- Un [dominio personalizzato e verificato](../fundamentals/add-custom-domain.md) nel tenant di Azure AD.
- Active Directory locale sincronizzato con Azure AD Connect, tramite il quale gli utenti possono [accedere ad Azure.](../hybrid/plan-connect-user-signin.md)
- Un connettore proxy di applicazione installato e in esecuzione in un computer all'interno del dominio aziendale.

La configurazione di SharePoint con il proxy di applicazione richiede due URL:
- Un URL esterno, visibile agli utenti finali e determinato in Azure AD. Questo URL può utilizzare un dominio personalizzato. Altre informazioni [sull'uso dei domini personalizzati in Proxy di applicazione di Azure AD.](application-proxy-configure-custom-domain.md)
- Un URL interno, noto solo all'interno del dominio aziendale e mai utilizzato direttamente.

> [!IMPORTANT]
> Per assicurarsi che i collegamenti siano mappati correttamente, seguire questi consigli per l'URL interno:
> - Usa HTTPS.
> - Non utilizzare porte personalizzate.
> - Nel sistema DNS (Domain Name System) aziendale creare un host (A) che punti al WFE di SharePoint (o al servizio di bilanciamento del carico) e non a un alias (CName).

In questo articolo vengono utilizzati i seguenti valori:
- URL interno:`https://sharepoint`
- URL esterno:`https://spsites-demo1984.msappproxy.net/`
- Account del pool di applicazioni per l'applicazione Web di SharePoint:`Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>Passaggio 1: Configurare un'applicazione in Azure AD che usa il proxy di applicazioneStep 1: Configure an application in Azure AD that uses Application Proxy

In questo passaggio viene creata un'applicazione nel tenant di Azure Active Directory che usa il proxy di applicazione. Impostare l'URL esterno e specificare l'URL interno, entrambi i quali vengono utilizzati in un secondo momento in SharePoint.

1. Creare l'app come descritto con le impostazioni seguenti. Per istruzioni dettagliate, vedere [Pubblicare applicazioni mediante il proxy di applicazione AD Azure](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **URL interno**: URL interno di SharePoint che verrà `https://sharepoint`impostato in un secondo momento in SharePoint, ad esempio .
   * **Pre-autenticazione**: Azure Active Directory
   * **Traduci URL nelle intestazioni**: No
   * **Tradurre URL nel corpo dell'applicazione**: No

   ![Pubblicare SharePoint come applicazione](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. Dopo la pubblicazione dell'app, segui questi passaggi per configurare le impostazioni del servizio Single Sign-on:

   1. Nella pagina dell'applicazione nel portale selezionare **Single Sign-On**.
   1. Per **Modalità Single Sign-On**, selezionare **Autenticazione integrata di Windows**.
   1. Impostare **SPN applicazione interna** sul valore impostato in precedenza. Per questo esempio, `HTTP/sharepoint`il valore è .
   1. In **Identità di accesso delegato**selezionare l'opzione più appropriata per la configurazione della foresta di Active Directory. Ad esempio, se si dispone di un singolo dominio di Active Directory nella foresta, selezionare **Nome account SAM locale** (come illustrato nella schermata seguente). Tuttavia, se gli utenti non si riunino nello stesso dominio di SharePoint e dei server Connettore proxy applicazione, selezionare **Nome dell'entità utente locale** (non visualizzato nella schermata).

   ![Configurare l'autenticazione integrata di Windows per SSO](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Per completare la configurazione dell'applicazione, passare alla sezione **Utenti e gruppi** e specificare gli utenti autorizzati ad accedere all'applicazione. 

## <a name="step-2-configure-the-sharepoint-web-application"></a>Passaggio 2: Configurare l'applicazione Web di SharePoint

L'applicazione Web di SharePoint deve essere configurata con Kerberos e i mapping di accesso alternativo appropriati per funzionare correttamente con il proxy di applicazione di Azure AD. Sono disponibili due opzioni:

- Creare una nuova applicazione Web e utilizzare solo l'area predefinita. Questa è l'opzione preferita, in quanto offre la migliore esperienza con SharePoint (ad esempio, i collegamenti negli avvisi di posta elettronica generati da SharePoint puntano sempre all'area predefinita).
- Estendere un'applicazione Web esistente per configurare Kerberos in un'area non predefinita.

> [!IMPORTANT]
> Indipendentemente dall'area utilizzata, l'account del pool di applicazioni dell'applicazione Web di SharePoint deve essere un account di dominio per il corretto funzionamento di Kerberos.

### <a name="provision-the-sharepoint-web-application"></a>Effettuare il provisioning dell'applicazione Web di SharePoint

- Se si crea una nuova applicazione Web e si utilizza solo l'area predefinita (opzione preferita):

    1. Avviare **SharePoint Management Shell** ed eseguire lo script seguente:

       ```powershell
       # This script creates a web application and configures the Default zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment. Note that the managed account must exist and it must be a domain account
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       $applicationPoolManagedAccount = "Contoso\spapppool"
            
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = New-SPWebApplication -Name "SharePoint - AAD Proxy" -Port 443 -SecureSocketsLayer -URL $externalUrl -ApplicationPool "SharePoint - AAD Proxy" -ApplicationPoolAccount (Get-SPManagedAccount $applicationPoolManagedAccount) -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Default -Internal
       ```

    2. Aprire il sito **Amministrazione centrale SharePoint**.
    1. In **Impostazioni di sistema** selezionare **Configura mapping di accesso alternativo**. Verrà visualizzata la finestra **di dialogo Raccolta mapping di accesso alternativo.**
    1. Filtrare la visualizzazione con la nuova applicazione web e confermare che si vede qualcosa di simile a questo:

       ![Mapping di accesso alternativo dell'applicazione Web](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- Se si estende un'applicazione Web esistente in una nuova area (nel caso in cui non sia possibile utilizzare l'area predefinita):

    1. Avviare SharePoint Management Shell ed eseguire lo script seguente:

       ```powershell
       # This script extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    2. Aprire il sito **Amministrazione centrale SharePoint**.
    1. In **Impostazioni di sistema** selezionare **Configura mapping di accesso alternativo**. Verrà visualizzata la finestra **di dialogo Raccolta mapping di accesso alternativo.**
    1. Filtrare la visualizzazione con l'applicazione web che è stata estesa e confermare che viene visualizzato qualcosa di simile a questo:

        ![Mapping di accesso alternativo dell'applicazione estesaAlternate Access Mappings of extended application](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="make-sure-the-sharepoint-web-application-is-running-under-a-domain-account"></a>Verificare che l'applicazione Web di SharePoint sia in esecuzione con un account di dominio

Per identificare l'account che esegue il pool di applicazioni dell'applicazione Web di SharePoint e assicurarsi che si tratti di un account di dominio, attenersi alla seguente procedura:

1. Aprire il sito **Amministrazione centrale SharePoint**.
1. Passare a **Sicurezza** e selezionare **Configura account di servizio**.
1. Selezionare **Pool di applicazioni Web - NomeApplicazioneWeb**.

   ![Opzioni per la configurazione di un account di servizio](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Verificare che **Select an account per questo componente** restituisca un account di dominio e ricordarlo, poiché sarà necessario nel passaggio successivo.

### <a name="make-sure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Assicurarsi che un certificato HTTPS sia configurato per il sito IIS dell'area Extranet

Poiché l'URL interno`https://SharePoint/`utilizza il protocollo HTTPS ( ), è necessario impostare un certificato nel sito Internet Information Services (IIS).

1. Aprire la console di Windows PowerShell.
1. Eseguire lo script seguente per generare un certificato autofirmato e aggiungerlo all'archivio MY del computer:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > I certificati autofirmati sono adatti per scopi di test. Negli ambienti di produzione, è consigliabile utilizzare i certificati emessi da un'autorità di certificazione.

1. Aprire la console di Gestione Internet Information Services.
1. Espandere il server nella visualizzazione albero, espandere **Siti**, selezionare il sito **SharePoint - Proxy AAD** e selezionare **Associazioni**.
1. Selezionare **associazione https** e quindi **Modifica**.
1. Nel campo Certificato TLS/SSL scegliere Certificato **di SharePoint** e quindi scegliere **OK**.

È ora possibile accedere al sito di SharePoint esternamente tramite il proxy di applicazione di Azure AD.

## <a name="step-3-configure-kerberos-constrained-delegation"></a>Passaggio 3: Configurare la delega vincolata KerberosStep 3: Configure Kerberos Constrained Delegation

Gli utenti verranno inizialmente autenticati in Azure AD e quindi in SharePoint tramite Kerberos tramite il connettore proxy di Azure AD. Per consentire al connettore di ottenere un token Kerberos per conto dell'utente di Azure AD, è necessario configurare la delega vincolata Kerberos (KCD) con la transizione di protocollo. Per ulteriori informazioni su KCD, vedere [Panoramica della delega vincolata Kerberos](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)).

### <a name="set-the-spn-for-the-sharepoint-service-account"></a>Impostare il nome SPN per l'account del servizio SharePoint

In questo articolo, l'URL interno è `https://sharepoint`e pertanto `HTTP/sharepoint`il nome dell'entità servizio (SPN) è . È necessario sostituire tali valori con i valori che corrispondono all'ambiente.
Per registrare SPN `HTTP/sharepoint` per l'account `Contoso\spapppool`del pool di applicazioni di SharePoint, eseguire il comando seguente da un prompt dei comandi come amministratore del dominio:

`setspn -S HTTP/sharepoint Contoso\spapppool`

Il `Setspn` comando cerca il nome SPN prima di aggiungerlo. Se il nome SPN esiste già, viene visualizzato un errore **di valore SPN duplicato.** In tal caso, è consigliabile rimuovere l'SPN esistente se non è impostato con l'account del pool di applicazioni corretto. È possibile verificare che il nome `Setspn` SPN sia stato aggiunto correttamente eseguendo il comando con l'opzione -L. Per altre informazioni su questo comando, vedere [Setspn](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11)).

### <a name="make-sure-the-connector-is-trusted-for-delegation-to-the-spn-that-was-added-to-the-sharepoint-application-pool-account"></a>Assicurarsi che il connettore è attendibile per la delega al nome SPN aggiunto all'account del pool di applicazioni di SharePoint

Configurare la delega vincolata Kerberos in modo che il servizio Azure AD Application Proxy possa delegare le identità utente all'account del pool di applicazioni di SharePoint. A questo scopo, consentire al connettore proxy di applicazione di recuperare i ticket Kerberos per gli utenti autenticati in Azure AD. Quindi, tale server passa il contesto all'applicazione di destinazione (SharePoint in questo caso).

Per configurare il KCD, attenersi alla seguente procedura per ogni macchina connettore:

1. Accedere a un controller di dominio come amministratore di dominio e quindi aprire Utenti e computer di Active Directory.
1. Individuare il computer che esegue il connettore proxy di Azure AD. In questo esempio, è il server SharePoint stesso.
1. Fare doppio clic sul computer, quindi selezionare la scheda **Delega.**
1. Assicurarsi che le opzioni di delega siano impostate su **Considera attendibile il computer per la delega solo ai servizi specificati.** Selezionare quindi **Usa un qualsiasi protocollo di autenticazione**.
1. Selezionare il pulsante **Aggiungi,** selezionare **Utenti o Computer**e individuare l'account del pool di applicazioni di SharePoint. Ad esempio `Contoso\spapppool`.
1. Nell'elenco di nomi SPN selezionare quello creato in precedenza per l'account del servizio.
1. Selezionare **OK** e quindi di nuovo **OK** per salvare le modifiche.
  
   ![Impostazioni di delega](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

È ora possibile accedere a SharePoint usando l'URL esterno e per eseguire l'autenticazione con Azure.You're ready to sign in to SharePoint by using the external URL and to authenticate with Azure.

## <a name="troubleshoot-sign-in-errors"></a>Risolvere gli errori di accessoTroubleshoot sign-in errors

Se l'accesso al sito non funziona, è possibile ottenere ulteriori informazioni sul problema nei registri del connettore: dal computer che esegue il connettore, aprire il Visualizzatore eventi, passare a **Registri** > applicazioni e servizi**Microsoft** > **AadApplicationProxy** > **Connector**e controllare il registro **di** amministrazione.

## <a name="next-steps"></a>Passaggi successivi

* [Utilizzo di domini personalizzati nel Proxy di applicazione AD Azure](application-proxy-configure-custom-domain.md)
* [Informazioni sui connettori del proxy di applicazione di Azure ADUnderstand Azure AD Application Proxy connectors](application-proxy-connectors.md)
