---
title: Abilitare l'accesso remoto a SharePoint-Azure AD proxy di applicazione
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
ms.openlocfilehash: 8d8f1bbd79e6dcbbd75e1ea1b98bd211d77ed1a9
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275454"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Abilitare l'accesso remoto a SharePoint con il proxy di applicazione di Azure AD

Questa guida dettagliata illustra come integrare una farm di SharePoint locale con il proxy di applicazione Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>prerequisiti

Per eseguire la configurazione, sono necessarie le risorse seguenti:
- Una farm di SharePoint 2013 o successiva.
- Un tenant di Azure AD con un piano che include il proxy di applicazione. Scopri di più sui [piani di Azure ad e sui prezzi](https://azure.microsoft.com/pricing/details/active-directory/).
- Un [dominio personalizzato e verificato](../fundamentals/add-custom-domain.md) nel tenant del Azure ad.
- Active Directory locali sincronizzati con Azure AD Connect, tramite cui gli utenti possono [accedere ad Azure](../hybrid/plan-connect-user-signin.md).
- Un connettore del proxy di applicazione installato e in esecuzione in un computer all'interno del dominio aziendale.

La configurazione di SharePoint con il proxy di applicazione richiede due URL:
- Un URL esterno, visibile agli utenti finali e determinato in Azure AD. Questo URL può usare un dominio personalizzato. Altre informazioni sull' [uso di domini personalizzati nel proxy di applicazione Azure ad](application-proxy-configure-custom-domain.md).
- Un URL interno, noto solo all'interno del dominio aziendale e mai usato direttamente.

> [!IMPORTANT]
> Per assicurarsi che i collegamenti siano mappati correttamente, seguire queste raccomandazioni per l'URL interno:
> - Usare HTTPS.
> - Non usare porte personalizzate.
> - Nel Domain Name System aziendale (DNS) creare un host (A) in modo che punti al WFE di SharePoint (o al servizio di bilanciamento del carico) e non a un alias (CName).

Questo articolo usa i valori seguenti:
- URL interno: `https://sharepoint`
- URL esterno: `https://spsites-demo1984.msappproxy.net/`
- Account del pool di applicazioni per l'applicazione Web di SharePoint: `Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>Passaggio 1: configurare un'applicazione in Azure AD che usa il proxy di applicazione

In questo passaggio viene creata un'applicazione nel tenant di Azure Active Directory che usa il proxy di applicazione. Si imposta l'URL esterno e si specifica l'URL interno, entrambi usati in un secondo momento in SharePoint.

1. Creare l'app come descritto con le impostazioni seguenti. Per istruzioni dettagliate, vedere [Pubblicare applicazioni mediante il proxy di applicazione AD Azure](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **URL interno**: URL interno di SharePoint che verrà impostato successivamente in SharePoint, ad esempio `https://sharepoint`.
   * **Pre-autenticazione**: Azure Active Directory
   * **Converti URL nelle intestazioni**: No
   * **Convertire gli URL nel corpo dell'applicazione**: No

   ![Pubblicare SharePoint come applicazione](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. Dopo la pubblicazione dell'app, seguire questa procedura per configurare le impostazioni di Single Sign-On:

   1. Nella pagina dell'applicazione nel portale selezionare **Single Sign-On**.
   1. Per **modalità Single Sign-on**, selezionare **autenticazione integrata di Windows**.
   1. Impostare **SPN applicazione interna** sul valore impostato in precedenza. Per questo esempio, il valore è `HTTP/sharepoint`.
   1. In **identità account di accesso delegato**selezionare l'opzione più adatta per la configurazione della foresta Active Directory. Se, ad esempio, si dispone di un singolo dominio Active Directory nella foresta, selezionare il **nome dell'account SAM locale** (come illustrato nello screenshot seguente). Tuttavia, se gli utenti non si trovano nello stesso dominio di SharePoint e nei server del connettore del proxy di applicazione, selezionare il **nome dell'entità utente locale** (non mostrato nello screenshot).

   ![Configurare l'autenticazione integrata di Windows per SSO](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Per completare la configurazione dell'applicazione, passare alla sezione **Utenti e gruppi** e specificare gli utenti autorizzati ad accedere all'applicazione. 

## <a name="step-2-configure-the-sharepoint-web-application"></a>Passaggio 2: configurare l'applicazione Web di SharePoint

L'applicazione Web di SharePoint deve essere configurata con Kerberos e i mapping di accesso alternativo appropriati per funzionare correttamente con Azure AD proxy di applicazione. Esistono due possibili opzioni:

- Creare una nuova applicazione Web e usare solo la zona predefinita. Si tratta dell'opzione preferita, perché offre la migliore esperienza con SharePoint (ad esempio, i collegamenti negli avvisi di posta elettronica generati da SharePoint puntano sempre all'area predefinita).
- Estendere un'applicazione Web esistente per configurare Kerberos in una zona non predefinita.

> [!IMPORTANT]
> Indipendentemente dalla zona utilizzata, l'account del pool di applicazioni dell'applicazione Web di SharePoint deve essere un account di dominio per il corretto funzionamento di Kerberos.

### <a name="provision-the-sharepoint-web-application"></a>Eseguire il provisioning dell'applicazione Web di SharePoint

- Se si crea una nuova applicazione Web e si usa solo la zona predefinita (opzione preferita):

    1. Avviare la **Shell di gestione di SharePoint** ed eseguire lo script seguente:

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
    1. In **Impostazioni di sistema** selezionare **Configura mapping di accesso alternativo**. Verrà visualizzata la casella **raccolta mapping di accesso alternativo** .
    1. Filtrare la visualizzazione con la nuova applicazione Web e verificare che venga visualizzata una schermata simile alla seguente:

       ![Mapping di accesso alternativo dell'applicazione Web](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- Se si estende un'applicazione Web esistente in una nuova zona, nel caso in cui non sia possibile usare la zona predefinita:

    1. Avviare la shell di gestione di SharePoint ed eseguire lo script seguente:

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
    1. In **Impostazioni di sistema** selezionare **Configura mapping di accesso alternativo**. Verrà visualizzata la casella **raccolta mapping di accesso alternativo** .
    1. Filtrare la visualizzazione con l'applicazione Web estesa e verificare che venga visualizzata una schermata simile alla seguente:

        ![Mapping di accesso alternativo dell'applicazione estesa](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="make-sure-the-sharepoint-web-application-is-running-under-a-domain-account"></a>Verificare che l'applicazione Web di SharePoint sia in esecuzione con un account di dominio

Per identificare l'account che esegue il pool di applicazioni dell'applicazione Web di SharePoint e verificare che sia un account di dominio, attenersi alla procedura seguente:

1. Aprire il sito **Amministrazione centrale SharePoint**.
1. Passare a **Sicurezza** e selezionare **Configura account di servizio**.
1. Selezionare **pool di applicazioni Web-YourWebApplicationName**.

   ![Opzioni per la configurazione di un account di servizio](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Confermare che **selezionare un account per il componente** restituisce un account di dominio e ricordarlo, perché sarà necessario nel passaggio successivo.

### <a name="make-sure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Verificare che sia configurato un certificato HTTPS per il sito IIS dell'area Extranet

Poiché l'URL interno utilizza il protocollo HTTPS (`https://SharePoint/`), è necessario impostare un certificato sul sito Internet Information Services (IIS).

1. Aprire la console di Windows PowerShell.
1. Eseguire lo script seguente per generare un certificato autofirmato e aggiungerlo all'archivio personale del computer:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > I certificati autofirmati sono adatti per scopi di test. Negli ambienti di produzione è consigliabile usare invece i certificati emessi da un'autorità di certificazione.

1. Aprire la console di Internet Information Services Manager.
1. Espandere il server nella visualizzazione albero, espandere **siti**, selezionare il sito **proxy SharePoint-AAD** e selezionare **Binding**.
1. Selezionare **binding HTTPS** , quindi fare clic su **modifica**.
1. Nel campo certificato SSL scegliere certificato **SharePoint** , quindi fare clic su **OK**.

È ora possibile accedere al sito di SharePoint esternamente tramite Azure AD proxy di applicazione.

## <a name="step-3-configure-kerberos-constrained-delegation"></a>Passaggio 3: configurare la delega vincolata Kerberos

Gli utenti eseguiranno l'autenticazione iniziale in Azure AD e quindi a SharePoint usando Kerberos tramite il connettore proxy di Azure AD. Per consentire al connettore di ottenere un token Kerberos per conto dell'utente Azure AD, è necessario configurare la delega vincolata Kerberos (delega vincolata Kerberos) con la transizione del protocollo. Per altre informazioni su delega vincolata Kerberos, vedere [Cenni preliminari sulla delega vincolata Kerberos](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)).

### <a name="set-the-spn-for-the-sharepoint-service-account"></a>Impostare il nome SPN per l'account del servizio SharePoint

In questo articolo, l'URL interno è `https://sharepoint`, quindi il nome dell'entità servizio (SPN) è `HTTP/sharepoint`. È necessario sostituire i valori con quelli corrispondenti all'ambiente in uso.
Per registrare `HTTP/sharepoint` SPN per l'account del pool di applicazioni di SharePoint `Contoso\spapppool`, eseguire il comando seguente da un prompt dei comandi, come amministratore del dominio:

`setspn -S HTTP/sharepoint Contoso\spapppool`

Il `Setspn` comando Cerca il nome SPN prima di aggiungerlo. Se il nome SPN esiste già, viene visualizzato un errore di **valore SPN duplicato** . In tal caso, provare a rimuovere il nome SPN esistente se non è impostato nell'account del pool di applicazioni corretto. È possibile verificare che il nome SPN sia stato aggiunto correttamente eseguendo il comando `Setspn` con l'opzione-L. Per altre informazioni su questo comando, vedere [Setspn](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11)).

### <a name="make-sure-the-connector-is-trusted-for-delegation-to-the-spn-that-was-added-to-the-sharepoint-application-pool-account"></a>Verificare che il connettore sia attendibile per la delega al nome SPN aggiunto all'account del pool di applicazioni di SharePoint

Configurare la delega vincolata Kerberos in modo che il servizio Azure AD Application Proxy possa delegare le identità utente all'account del pool di applicazioni di SharePoint. A questo scopo, consentire al connettore proxy di applicazione di recuperare i ticket Kerberos per gli utenti autenticati in Azure AD. Quindi, il server passa il contesto all'applicazione di destinazione (in questo caso, SharePoint).

Per configurare il delega vincolata Kerberos, seguire questa procedura per ogni computer connettore:

1. Accedere a un controller di dominio come amministratore di dominio e quindi aprire Active Directory utenti e computer.
1. Trovare il computer che esegue il connettore proxy Azure AD. In questo esempio si tratta del server SharePoint.
1. Fare doppio clic sul computer e quindi selezionare la scheda **delega** .
1. Verificare che le opzioni di delega siano impostate su **Considera attendibile il computer per la delega solo ai servizi specificati**. Selezionare quindi **Usa un qualsiasi protocollo di autenticazione**.
1. Selezionare il pulsante **Aggiungi** , selezionare **utenti o computer**e individuare l'account del pool di applicazioni di SharePoint. Ad esempio: `Contoso\spapppool`.
1. Nell'elenco di nomi SPN selezionare quello creato in precedenza per l'account del servizio.
1. Selezionare **OK** , quindi fare di nuovo clic su **OK** per salvare le modifiche.
  
   ![Impostazioni di delega](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

A questo punto si è pronti per accedere a SharePoint usando l'URL esterno e per eseguire l'autenticazione con Azure.

## <a name="troubleshoot-sign-in-errors"></a>Risolvere gli errori di accesso

Se l'accesso al sito non funziona, è possibile ottenere altre informazioni sul problema nei log del connettore: dal computer che esegue il connettore, aprire il Visualizzatore eventi, passare a **registri applicazioni e servizi** > **Microsoft** > **AadApplicationProxy** > **Connector**ed esaminare il registro **amministratore** .

## <a name="next-steps"></a>Passaggi successivi

* [Uso di domini personalizzati nel proxy di applicazione Azure AD](application-proxy-configure-custom-domain.md)
* [Comprendere i connettori del proxy applicazione Azure AD](application-proxy-connectors.md)
