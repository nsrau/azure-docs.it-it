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
ms.date: 12/10/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f213acea71f22815d8b26b6c4c6cb54f64b8b34
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807795"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Abilitare l'accesso remoto a SharePoint con il proxy di applicazione di Azure AD

Questo articolo illustra come integrare un server SharePoint locale con il proxy di applicazione di Azure Active Directory (Azure AD).

Per abilitare l'accesso remoto a SharePoint con il proxy di applicazione di Azure AD, seguire le istruzioni dettagliate riportate nelle sezioni di questo articolo.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che SharePoint 2013 o versione successiva sia già installato nell'ambiente. Tenere in considerazione anche i seguenti prerequisiti:

* SharePoint include il supporto nativo di Kerberos. Di conseguenza, gli utenti che accedono in remoto ai siti interni tramite il proxy di applicazione di Azure AD possono presumere di essere in possesso dell'esperienza Single Sign-On.
* Questo scenario include le modifiche di configurazione per il server SharePoint. Si consiglia di usare un ambiente di gestione temporanea. In questo modo, è possibile apportare aggiornamenti al server di gestione temporanea e quindi facilitare un ciclo di test prima del passaggio in produzione.
* Nell'URL pubblicato è necessario SSL. SSL è necessario anche nell'URL interno per garantire l'invio e il mapping corretti dei collegamenti.

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Passaggio 1: Configurare la delega vincolata Kerberos

Per le applicazioni locali che usano l'autenticazione di Windows, è possibile ottenere l'accesso Single Sign-On (SSO) con il protocollo di autenticazione Kerberos e una funzionalità chiamata delega vincolata Kerberos. Se configurata, questa funzionalità consente al connettore proxy di applicazione di ottenere un token di Windows per un utente, anche se questi non ha eseguito l'accesso diretto a Windows. Per altre informazioni sulla delega vincolata Kerberos, vedere [Panoramica della delega vincolata Kerberos](https://technet.microsoft.com/library/jj553400.aspx).

Per configurare la delega vincolata Kerberos per un server SharePoint, usare le procedure descritte nelle sezioni seguenti:

### <a name="ensure-that-sharepoint-web-application-is-running-under-a-domain-account"></a>Verificare che l'applicazione Web SharePoint sia in esecuzione in un account di dominio

Prima di tutto, verificare che l'applicazione Web SharePoint sia in esecuzione in un account di dominio, non in un sistema locale, un servizio locale o un servizio di rete. Eseguire questa operazione in modo da poter associare i nomi di entità servizio (SPN) a un account valido. I nomi SPN sono usati dal protocollo Kerberos per identificare i diversi servizi e saranno necessari in seguito per configurare la delega vincolata Kerberos.

> [!NOTE]
> È necessario avere un account di Azure AD creato in precedenza per il servizio. Si consiglia di consentire la modifica automatica della password. Per altre informazioni sull'insieme completo di passaggi e sulla risoluzione dei problemi, vedere [Configurare la modifica automatica della password in SharePoint](https://technet.microsoft.com/library/ff724280.aspx).

Per garantire che i siti siano in esecuzione in un account di servizio definito, seguire questa procedura:

1. Aprire il sito **Amministrazione centrale SharePoint**.
1. Passare a **Sicurezza** e selezionare **Configura account di servizio**.
1. Selezionare **Pool di applicazioni Web - SharePoint - 80**. È possibile che le opzioni siano leggermente diverse, a seconda del nome del pool Web o se il pool Web usa SSL per impostazione predefinita.

   ![Opzioni per la configurazione di un account di servizio](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Se il campo **Selezionare un account per il componente** è impostato su **Servizio locale** o **Servizio di rete**, è necessario creare un account. In caso contrario, l'operazione è terminata ed è possibile procedere alla sezione successiva.
1. Selezionare **Registra nuovo account gestito**. Dopo aver creato l'account, prima di poterlo usare è necessario impostare l'opzione **Pool di applicazioni Web**.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Impostare un nome dell'entità servizio per l'account del servizio SharePoint

Prima di configurare la delega vincolata Kerberos, è necessario:

* Identificare l'account di dominio che esegue l'applicazione Web SharePoint che verrà esposta dal proxy di Azure AD.
* Scegliere un URL interno che verrà configurato nel proxy di Azure AD e in SharePoint. Questo URL interno non deve essere già in uso nell'applicazione Web e non verrà mai visualizzato nel Web browser.

Supponendo che l'URL interno scelto sia <https://sharepoint>, l'SPN sarà:

```
HTTP/SharePoint
```

> [!NOTE]
> Seguire questi consigli per l'URL interno:
> * Usare HTTPS
> * Non usare porte personalizzate
> * Nel DNS creare un host (A) che punti al front-end Web di SharePoint (o a un servizio di bilanciamento del carico) e non un alias (CName)

Per registrare l'SPN, eseguire il comando seguente dal prompt dei comandi come amministratore del dominio:

```
setspn -S HTTP/SharePoint demo\spAppPoolAccount
```

Questo comando imposta l'SPN _HTTP/SharePoint_ per l'account del pool di applicazioni SharePoint _demo\spAppPoolAccount_.

Sostituire _HTTP/SharePoint_ con l'SPN per l'URL interno e _demo\spAppPoolAccount_ con l'account del pool di applicazioni nell'ambiente. Il comando Setspn cerca il nome SPN prima di aggiungerlo. Se esiste già, verrà visualizzato l'errore **Duplicate SPN Value** (Valore SPN duplicato). In questo caso, provare a rimuovere l'SPN esistente se non è impostato nell'account del pool di applicazioni corretto.

È possibile verificare che l'SPN sia stato aggiunto tramite il comando Setspn con l'opzione -L. Per altre informazioni su questo comando, vedere [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>Assicurarsi che il connettore sia attendibile per la delega all'SPN aggiunto nell'account del pool di applicazioni di SharePoint

Configurare la delega vincolata Kerberos in modo che il servizio Azure AD Application Proxy possa delegare le identità utente all'account del pool di applicazioni di SharePoint. A questo scopo, consentire al connettore proxy di applicazione di recuperare i ticket Kerberos per gli utenti autenticati in Azure AD. Quindi il server passa il contesto all'applicazione di destinazione o, in questo caso, a SharePoint.

Per configurare la delega vincolata Kerberos, ripetere i passaggi seguenti per ogni computer di connessione:

1. Eseguire l'accesso come amministratore a un controller di dominio e quindi aprire un dominio **Utenti e computer di Active Directory**.
1. Trovare il computer in cui è in esecuzione il connettore. In questo esempio è lo stesso server SharePoint.
1. Fare doppio clic sul computer e quindi sulla scheda **Delega**.
1. Assicurarsi che la delega sia impostata su **Computer attendibile per la delega solo ai servizi specificati**. Selezionare quindi **Usa un qualsiasi protocollo di autenticazione**.
1. Fare clic sul pulsante **Aggiungi**, fare clic su **Utenti o computer** e quindi individuare l'account del pool di applicazioni di SharePoint, ad esempio _demo\spAppPoolAccount_.
1. Nell'elenco di nomi SPN selezionare quello creato in precedenza per l'account del servizio.
1. Fare clic su **OK**. Fare nuovamente clic su **OK** per salvare le modifiche.
  
   ![Impostazioni di delega](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

## <a name="step-2-configure-azure-ad-proxy"></a>Passaggio 2: Configurare il proxy di Azure AD

Dopo aver configurato la delega vincolata Kerberos, è possibile configurare Azure AD Application Proxy.

1. Pubblicare il sito di SharePoint con le impostazioni seguenti. Per istruzioni dettagliate, vedere [Pubblicare applicazioni mediante il proxy di applicazione AD Azure](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **URL interno**: URL interno di SharePoint scelto in precedenza, ad esempio **<https://SharePoint/>** .
   * **Metodo di autenticazione preliminare**: Azure Active Directory
   * **Convertire l'URL nelle intestazioni**: NO

   > [!TIP]
   > SharePoint usa il valore _Intestazione host_ per cercare il sito e, in base a questo valore, genera i collegamenti. Di conseguenza, qualsiasi collegamento generato da SharePoint è un URL pubblicato e impostato correttamente per l'uso dell'URL esterno. Impostando il valore su **SÌ** si consente al connettore di inoltrare la richiesta all'applicazione back-end. Se invece si imposta il valore su **NO**, il connettore non invierà il nome host interno, ma invia l'intestazione host come URL pubblicato per l'applicazione back-end.

   ![Pubblicare SharePoint come applicazione](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. Dopo la pubblicazione dell'app, configurare le impostazioni Single Sign-On eseguendo le operazioni seguenti:

   1. Nella pagina dell'applicazione nel portale selezionare **Single Sign-On**.
   1. Per la modalità Single Sign-On, selezionare **Autenticazione integrata di Windows**.
   1. Impostare l'SPN dell'applicazione interna sul valore configurato in precedenza. Per questo esempio, il valore è **HTTP/SharePoint**.
   1. In "Identità di accesso delegata", selezionare l'opzione più adatta per la configurazione di foresta di Active Directory. Ad esempio se si dispone di un singolo dominio di Active Directory nella foresta, seleziona **nome dell'account SAM di On-premises** (come indicato di seguito), ma se gli utenti non sono nello stesso dominio di SharePoint e quindi selezionare i server App Proxy Connector  **Nome dell'entità utente locale** (non illustrato).

   ![Configurare l'autenticazione integrata di Windows per SSO](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Per completare la configurazione dell'applicazione, passare alla sezione **Utenti e gruppi** e specificare gli utenti autorizzati ad accedere all'applicazione. 

## <a name="step-3-configure-sharepoint-to-use-kerberos-and-azure-ad-proxy-urls"></a>Passaggio 3: Configurare SharePoint per l'uso di Kerberos e URL del proxy di Azure AD

Il passaggio successivo consiste nell'estendere l'applicazione Web SharePoint a una nuova zona, configurata con Kerberos e il mapping di accesso alternativo appropriato in modo da consentire a SharePoint di gestire le richieste in ingresso inviate all'URL interno e rispondere con collegamenti creati per l'URL esterno.

1. Avviare **Shell di gestione SharePoint**.
1. Eseguire lo script seguente per estendere l'applicazione Web alla zona Extranet e abilitare l'autenticazione Kerberos:

   ```powershell
   # Replace "http://spsites/" with the URL of your web application
   # Replace "https://sharepoint-f128.msappproxy.net/" with the External URL in your Azure AD proxy application
   $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
   Get-SPWebApplication "http://spsites/" | New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -SecureSocketsLayer -Zone "Extranet" -Url "https://sharepoint-f128.msappproxy.net/" -AuthenticationProvider $winAp
   ```

1. Aprire il sito **Amministrazione centrale SharePoint**.
1. In **Impostazioni di sistema** selezionare **Configura mapping di accesso alternativo**. Verrà visualizzata la casella Mapping di accesso alternativo.
1. Selezionare il sito, ad esempio **SharePoint - 80**. Per il momento, l'URL interno non è ancora impostato correttamente per la zona Extranet:

   ![Viene visualizzata la finestra di mapping di accesso alternativo](./media/application-proxy-integrate-with-sharepoint-server/alternate-access1.png)

1. Fare clic su **Aggiungi URL interni**.
1. Nella casella di testo **URL protocol, host and port** (Protocollo, host e porta URL) immettere in **URL interno** il valore configurato nel proxy di Azure AD, ad esempio <https://SharePoint/>.
1. Selezionare la zona **Extranet** nell'elenco a discesa.
1. Fare clic su **Save**.
1. I mapping di accesso alternativo saranno simili ai seguenti:

    ![Mapping di accesso alternativo corretti](./media/application-proxy-integrate-with-sharepoint-server/alternate-access3.png)

## <a name="step-4-ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Passaggio 4: Assicurarsi che sia configurato un certificato HTTPS per il sito IIS della zona Extranet

La configurazione di SharePoint è completa, ma poiché l'URL interno della zona Extranet è <https://SharePoint/>, è necessario impostare un certificato per questo sito.

1. Aprire la console Windows PowerShell.
1. Eseguire lo script seguente per generare un certificato autofirmato e aggiungerlo all'archivio personale del computer:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!NOTE]
   > I certificati autofirmati sono adatti per scopi di test. Per gli ambienti di produzione, è invece consigliabile usare certificati rilasciati da un'autorità di certificazione.

1. Aprire la console "Gestione Internet Information Services".
1. Espandere il server nella visualizzazione albero, espandere "Siti", selezionare il sito "SharePoint - AAD Proxy" e fare clic su **Associazioni**.
1. Selezionare l'associazione HTTPS e fare clic su **Modifica**.
1. Nel campo del certificato SSL scegliere il certificato **SharePoint** e fare clic su OK.

È ora possibile accedere al sito di SharePoint esternamente tramite il proxy di applicazione di Azure AD.

## <a name="next-steps"></a>Passaggi successivi

* [Uso di domini personalizzati nel proxy di applicazione Azure AD](application-proxy-configure-custom-domain.md)
* [Comprendere i connettori del proxy applicazione di Azure AD](application-proxy-connectors.md)
