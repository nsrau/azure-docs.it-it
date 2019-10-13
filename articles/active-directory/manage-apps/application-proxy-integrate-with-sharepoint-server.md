---
title: Abilitare l'accesso remoto a SharePoint con il proxy di applicazione di Azure AD | Microsoft Docs
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
ms.openlocfilehash: a8f9cba1072866a3efdeb7b99981d0bfda22ab00
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286049"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Abilitare l'accesso remoto a SharePoint con il proxy di applicazione di Azure AD

Questa guida dettagliata illustra come integrare una farm di SharePoint locale con Azure Active Directory (Azure AD) proxy di applicazione.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire la configurazione, sono necessarie le risorse seguenti:
- Una farm di SharePoint 2013 o successiva.
- Un tenant di Azure Active Directory con un piano che include il proxy di applicazione. Scopri di più sui [piani di Azure Active Directory e sui prezzi](https://azure.microsoft.com/pricing/details/active-directory/).
- Un [dominio verificato personalizzato](../fundamentals/add-custom-domain.md) nel tenant del Azure ad.
- Active Directory locali sincronizzati con Azure AD Connect con gli utenti in grado di [accedere ad Azure](../hybrid/plan-connect-user-signin.md).
- Un connettore del proxy di applicazione installato e in esecuzione in un computer all'interno del dominio aziendale.

La configurazione di SharePoint con il proxy di applicazione richiede due URL:
- Un URL esterno, visibile agli utenti finali e determinato in Azure Active Directory. Questo URL può usare un dominio personalizzato. Altre informazioni sull' [uso di domini personalizzati nel proxy di applicazione Azure ad](application-proxy-configure-custom-domain.md).
- Un URL interno, noto solo all'interno del dominio aziendale e mai usato direttamente.

> [!IMPORTANT]
> Per assicurarsi che i collegamenti siano mappati correttamente, seguire queste raccomandazioni per l'URL interno:
> - Usare HTTPS
> - Non usare porte personalizzate
> - Nel DNS aziendale creare un host (A) in modo che punti al WFE di SharePoint (o al servizio di bilanciamento del carico) e non a un alias (CName)

In questo articolo verranno usati i valori seguenti:
- URL interno: `https://sharepoint`
- URL esterno: `https://spsites-demo1984.msappproxy.net/`
- Account del pool di applicazioni per l'applicazione Web di SharePoint: `Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>Passaggio 1: Configurare un'applicazione in Azure AD che usa il proxy di applicazione

In questo passaggio viene creata un'applicazione nel tenant di Azure Active Directory che utilizzerà il proxy di applicazione. Si imposterà l'URL esterno e si specificherà l'URL interno usato in un secondo momento in SharePoint.

1. Creare l'app come descritto con le impostazioni seguenti. Per istruzioni dettagliate, vedere [Pubblicare applicazioni mediante il proxy di applicazione AD Azure](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **URL interno**: URL interno di SharePoint che verrà impostato successivamente in SharePoint, ad esempio `https://sharepoint`.
   * **Metodo di autenticazione preliminare**: Azure Active Directory
   * **Convertire l'URL nelle intestazioni**: No
   * **Tradurre l'URL nel corpo dell'applicazione**: No

   ![Pubblicare SharePoint come applicazione](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. Dopo la pubblicazione dell'app, configurare le impostazioni Single Sign-On eseguendo le operazioni seguenti:

   1. Nella pagina dell'applicazione nel portale selezionare **Single Sign-On**.
   1. Per **modalità Single Sign-on**, selezionare **autenticazione integrata di Windows**.
   1. Impostare **SPN applicazione interna** sul valore impostato in precedenza. Per questo esempio, il valore sarà `HTTP/sharepoint`.
   1. In **identità di accesso delegata**selezionare l'opzione più adatta per la configurazione della foresta Active Directory. Se, ad esempio, si dispone di un singolo dominio AD nella foresta, selezionare il **nome dell'account SAM locale** (come illustrato di seguito), ma se gli utenti non si trovano nello stesso dominio di SharePoint e nei server del connettore del proxy di applicazione, selezionare l' **entità utente locale nome** (non visualizzato).

   ![Configurare l'autenticazione integrata di Windows per SSO](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Per completare la configurazione dell'applicazione, passare alla sezione **Utenti e gruppi** e specificare gli utenti autorizzati ad accedere all'applicazione. 

## <a name="step-2-configure-the-sharepoint-web-application"></a>Passaggio 2: Configurare l'applicazione Web di SharePoint

L'applicazione Web di SharePoint deve essere configurata con Kerberos e i mapping di accesso alternativo appropriati per funzionare correttamente con Azure AD proxy di applicazione. Esistono due possibili opzioni:

1. Creare una nuova applicazione Web e usare solo la zona predefinita. Si tratta dell'opzione preferita per ottenere la migliore esperienza con SharePoint (ad esempio, i collegamenti negli avvisi di posta elettronica generati da SharePoint puntano sempre all'area predefinita).
1. Estendere un'applicazione Web esistente per configurare Kerberos in una zona non predefinita.

> [!IMPORTANT]
> Indipendentemente dalla zona che verrà utilizzata, l'account del pool di applicazioni dell'applicazione Web di SharePoint deve essere un account di dominio per il corretto funzionamento di Kerberos.

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

    1. Aprire il sito **Amministrazione centrale SharePoint**.
    1. In **Impostazioni di sistema** selezionare **Configura mapping di accesso alternativo**. Verrà visualizzata la casella Mapping di accesso alternativo.
    1. Filtrare la visualizzazione con la nuova applicazione Web e verificare che venga visualizzata una schermata simile alla seguente:

       ![Mapping di accesso alternativo dell'applicazione Web](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- Se si estende un'applicazione Web esistente in una nuova zona, nel caso in cui non sia possibile usare la zona predefinita:

    1. Avviare la **Shell di gestione di SharePoint** ed eseguire lo script seguente:

       ```powershell
       # This scripts extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    1. Aprire il sito **Amministrazione centrale SharePoint**.
    1. In **Impostazioni di sistema** selezionare **Configura mapping di accesso alternativo**. Verrà visualizzata la casella Mapping di accesso alternativo.
    1. Filtrare la visualizzazione con l'applicazione Web che è stata estesa e verificare che venga visualizzata una schermata simile alla seguente:

        ![Mapping di accesso alternativo dell'applicazione estesa](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="ensure-that-the-sharepoint-web-application-is-running-under-a-domain-account"></a>Verificare che l'applicazione Web di SharePoint sia in esecuzione con un account di dominio

Utilizzare la procedura seguente per identificare l'account che esegue il pool di applicazioni dell'applicazione Web di SharePoint e verificare che sia un account di dominio:

1. Aprire il sito **Amministrazione centrale SharePoint**.
1. Passare a **Sicurezza** e selezionare **Configura account di servizio**.
1. Selezionare **pool di applicazioni Web-YourWebApplicationName**.

   ![Opzioni per la configurazione di un account di servizio](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Confermare che **selezionare un account per il componente** restituisce un account di dominio e ricordarlo perché sarà necessario nel passaggio successivo.

### <a name="ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Assicurarsi che sia configurato un certificato HTTPS per il sito IIS della zona Extranet

Poiché l'URL interno utilizza il protocollo HTTPS (`https://SharePoint/`), è necessario impostare un certificato nel sito IIS.

1. Aprire la console Windows PowerShell.
1. Eseguire lo script seguente per generare un certificato autofirmato e aggiungerlo all'archivio personale del computer:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > I certificati autofirmati sono adatti per scopi di test. Per gli ambienti di produzione, è invece consigliabile usare certificati rilasciati da un'autorità di certificazione.

1. Aprire la console "Gestione Internet Information Services".
1. Espandere il server nella visualizzazione albero, espandere "Siti", selezionare il sito "SharePoint - AAD Proxy" e fare clic su **Associazioni**.
1. Selezionare l'associazione HTTPS e fare clic su **Modifica**.
1. Nel campo del certificato SSL scegliere il certificato **SharePoint** e fare clic su OK.

È ora possibile accedere al sito di SharePoint esternamente tramite il proxy di applicazione di Azure AD.

## <a name="step-3-configure-kerberos-constrained-delegation-kcd"></a>Passaggio 3: Configurare la delega vincolata Kerberos

Gli utenti eseguiranno l'autenticazione iniziale in Azure Active Directory, quindi a SharePoint usando Kerberos tramite il connettore proxy di Azure AD. Per consentire al connettore di ottenere un token Kerberos per conto dell'utente Azure Active Directory, è necessario configurare la delega vincolata Kerberos con la transizione del protocollo. Per altre informazioni sulla delega vincolata Kerberos, vedere [Panoramica della delega vincolata Kerberos](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)).

### <a name="set-the-service-principal-name-for-the-sharepoint-service-account"></a>Impostare il nome dell'entità servizio per l'account del servizio SharePoint

In questo articolo l'URL interno è `https://sharepoint`, quindi il nome SPN è `HTTP/sharepoint`. È necessario sostituire questi valori con quelli corrispondenti all'ambiente in uso.
Per registrare il nome SPN `HTTP/sharepoint` per l'account del pool di applicazioni di SharePoint `Contoso\spapppool`, eseguire il comando seguente da un prompt dei comandi, come amministratore del dominio:

`setspn -S HTTP/sharepoint Contoso\spapppool`

Il comando Setspn cerca il nome SPN prima di aggiungerlo. Se esiste già, verrà visualizzato un errore di **valore SPN duplicato** . In questo caso, provare a rimuovere il nome SPN esistente se non è impostato nell'account del pool di applicazioni corretto.  
È possibile verificare che il nome SPN sia stato aggiunto correttamente eseguendo il comando setspn con l'opzione-L. Per altre informazioni su questo comando, vedere [Setspn](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11)).

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>Assicurarsi che il connettore sia attendibile per la delega all'SPN aggiunto nell'account del pool di applicazioni di SharePoint

Configurare la delega vincolata Kerberos in modo che il servizio Azure AD Application Proxy possa delegare le identità utente all'account del pool di applicazioni di SharePoint. A questo scopo, consentire al connettore proxy di applicazione di recuperare i ticket Kerberos per gli utenti autenticati in Azure AD. Quindi il server passa il contesto all'applicazione di destinazione o, in questo caso, a SharePoint.

Per configurare la delega vincolata Kerberos, ripetere i passaggi seguenti per ogni computer di connessione:

1. Accedere a un controller di dominio come amministratore di dominio e quindi aprire **Active Directory utenti e computer**.
1. Trovare il computer che esegue il connettore proxy Azure AD. In questo esempio si tratta del server SharePoint.
1. Fare doppio clic sul computer e quindi sulla scheda **Delega**.
1. Assicurarsi che la delega sia impostata su **Computer attendibile per la delega solo ai servizi specificati**. Selezionare quindi **Usa un qualsiasi protocollo di autenticazione**.
1. Fare clic sul pulsante **Aggiungi** , fare clic su **utenti o computer**e individuare l'account del pool di applicazioni di SharePoint, ad esempio `Contoso\spapppool`.
1. Nell'elenco di nomi SPN selezionare quello creato in precedenza per l'account del servizio.
1. Fare clic su **OK**. Fare nuovamente clic su **OK** per salvare le modifiche.
  
   ![Impostazioni di delega](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

A questo punto è possibile accedere a SharePoint usando l'URL esterno ed eseguire l'autenticazione con Azure.

## <a name="troubleshoot-sign-in-errors"></a>Risolvere gli errori di accesso

Se l'accesso al sito non funziona, è possibile ottenere altre informazioni sul problema nei log del connettore: Dal computer che esegue il connettore aprire il Visualizzatore eventi, passare a **registri applicazioni e servizi** > **Microsoft** > **AadApplicationProxy** > **Connector**ed esaminare il registro **amministratore** .

## <a name="next-steps"></a>Passaggi successivi

* [Uso di domini personalizzati nel proxy di applicazione Azure AD](application-proxy-configure-custom-domain.md)
* [Comprendere i connettori del proxy applicazione di Azure AD](application-proxy-connectors.md)
