---
title: Abilitare l'accesso remoto a Power BI con il proxy di applicazione di Azure ADEnable remote access to Power BI with Azure AD Application Proxy
description: Vengono illustrate le nozioni di base su come integrare un Power BI locale con il proxy di applicazione di Azure AD.
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
ms.date: 07/25/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc96c94152b39cc70cfc4553690faaa5b9cb8d20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111582"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>Abilitare l'accesso remoto a Power BI per dispositivi mobili con Azure AD Application Proxy

Questo articolo illustra come usare il proxy di applicazione di Azure AD per abilitare l'app Power BI per dispositivi mobili per la connessione al server di report di Power BI (PBIRS) e SQL Server Reporting Services (SSRS) 2016 e versioni successive. Tramite questa integrazione, gli utenti che si trovano lontano dalla rete aziendale possono accedere ai report di Power BI dall'app Power BI per dispositivi mobili ed essere protetti dall'autenticazione di Azure AD. Questa protezione include vantaggi in termini di [sicurezza,](application-proxy-security.md#security-benefits) ad esempio l'accesso condizionale e l'autenticazione a più fattori.  

## <a name="prerequisites"></a>Prerequisiti

In questo articolo si presuppone che siano già stati distribuiti Servizi report e che il proxy di applicazione sia stato [abilitato.](application-proxy-add-on-premises-application.md)

- L'abilitazione del proxy di applicazione richiede l'installazione di un connettore in un server Windows e il completamento dei [prerequisiti](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) in modo che il connettore possa comunicare con i servizi di Azure AD.  
- Quando si pubblica Power BI, è consigliabile usare gli stessi domini interni ed esterni. Per ulteriori informazioni sui domini personalizzati, vedere [Utilizzo dei domini personalizzati in Proxy di applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain).
- Questa integrazione è disponibile per l'applicazione **Power BI Mobile iOS e Android.This** integration is available for the Power BI Mobile iOS and Android application.

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Passaggio 1: Configurare la delega vincolata Kerberos (KCD)Step 1: Configure Kerberos Constrained Delegation (KCD)

Per le applicazioni locali che usano l'autenticazione di Windows, è possibile ottenere l'accesso Single Sign-On (SSO) con il protocollo di autenticazione Kerberos e una funzionalità chiamata delega vincolata Kerberos. Quando configurato, KCD consente al connettore proxy di applicazione di ottenere un token Windows per un utente, anche se l'utente non ha eseguito l'accesso direttamente a Windows. Per ulteriori informazioni su KCD, consultate [Panoramica della delega vincolata Kerberos](https://technet.microsoft.com/library/jj553400.aspx) e [Delega vincolata Kerberos per l'accesso Single Sign-On alle app con il proxy di applicazione.](application-proxy-configure-single-sign-on-with-kcd.md)

Per quanto riguarda Reporting Services, non c'è molto da configurare. È sufficiente assicurarsi di disporre di un nome dell'entità servizio (SPN) valido per abilitare l'autenticazione Kerberos corretta. Assicurarsi inoltre che il server Reporting Services sia abilitato per l'autenticazione Negotiate.

Per configurare KCD per Reporting Services, continuare con i passaggi seguenti.

### <a name="configure-the-service-principal-name-spn"></a>Configurare il nome dell'entità servizio (SPN)

Il nome dell'entità servizio (SPN) è un identificatore univoco per un servizio che usa l'autenticazione Kerberos. È necessario assicurarsi di disporre di un SPN HTTP corretto presente per il server di report. Per informazioni su come configurare il corretto nome dell'entità servizio (SPN) per il server di report, vedere [Registrare un nome dell'entità servizio (SPN) per un server di report](https://msdn.microsoft.com/library/cc281382.aspx).
È possibile verificare che l'SPN sia stato aggiunto tramite il comando Setspn con l'opzione -L. Per altre informazioni su questo comando, vedere [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx).

### <a name="enable-negotiate-authentication"></a>Abilitare l'autenticazione NegotiateEnable Negotiate authentication

Per consentire a un server di report di utilizzare l'autenticazione Kerberos, configurare il tipo di autenticazione del server di report in RSWindowsNegotiate. Configurare questa impostazione utilizzando il file rsreportserver.config.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

Per altre informazioni, vedere [Modificare un file di configurazione di Reporting Services](https://msdn.microsoft.com/library/bb630448.aspx) e [Configurare l'autenticazione di Windows nel server di report](https://msdn.microsoft.com/library/cc281253.aspx).

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>Verificare che il connettore sia attendibile per la delega al nome SPN aggiunto all'account del pool di applicazioni di Reporting Services
Configurare KCD in modo che il servizio Proxy di applicazione di Azure AD possa delegare le identità utente all'account del pool di applicazioni di Reporting ServicesReporting Services . A questo scopo, consentire al connettore proxy di applicazione di recuperare i ticket Kerberos per gli utenti autenticati in Azure AD. Il server passa quindi il contesto all'applicazione di destinazione o a Reporting ServicesReporting Services in questo caso.

Per configurare KCD, ripetere i passaggi seguenti per ogni computer connettore:

1. Accedere a un controller di dominio come amministratore di dominio e quindi aprire **Utenti e computer**di Active Directory .
2. Trovare il computer in cui è in esecuzione il connettore.  
3. Fare doppio clic sul computer, quindi selezionare la scheda **Delega.**
4. Impostare le impostazioni di delega su **Considera attendibile il computer per la delega solo ai servizi specificati**. Selezionare quindi **Usa un qualsiasi protocollo di autenticazione**.
5. Selezionare **Aggiungi**, quindi **Utenti o Computer**.
6. Immettere l'account del servizio in uso per Reporting Services. Si tratta dell'account a cui è stato aggiunto il nome SPN all'interno della configurazione di Reporting Services.
7. Fare clic su **OK**. Per salvare le modifiche, fare di nuovo clic **su OK.**

Per ulteriori informazioni, consultate [Delega vincolata Kerberos per l'accesso Single Sign-On alle app con il proxy di applicazione.](application-proxy-configure-single-sign-on-with-kcd.md)

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>Passaggio 2: Pubblicare I servizi report tramite il proxy di applicazione di Azure ADStep 2: Publish Report Services through Azure AD Application Proxy

A questo punto è possibile configurare il proxy di applicazione di Azure AD.

1. Pubblicare Service Services report tramite il proxy di applicazione con le impostazioni seguenti. Per istruzioni dettagliate su come pubblicare un'applicazione tramite il proxy di applicazione, vedere [Pubblicazione di applicazioni tramite](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)il proxy di applicazione di Azure AD.
   - **URL interno:** immettere l'URL del server di report che il connettore può raggiungere nella rete aziendale. Assicurarsi che l'URL sia raggiungibile dal server in cui è installato il connettore. Una procedura consigliata consiste nell'utilizzare `https://servername/` un dominio di primo livello, ad esempio per evitare problemi con i sottopercorsi pubblicati tramite il proxy di applicazione. Ad esempio, `https://servername/` utilizzare `https://servername/reports/` `https://servername/reportserver/`e non o .
     > [!NOTE]
     > È consigliabile usare una connessione HTTPS protetta al server di report. Per informazioni su come, vedere [Configurare le connessioni SSL in un server di report in modalità nativa.](https://docs.microsoft.com/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017)
   - **URL esterno:** immettere l'URL pubblico a cui si connetterà l'app Power BI per dispositivi mobili. Ad esempio, potrebbe `https://reports.contoso.com` apparire come se viene utilizzato un dominio personalizzato. Per usare un dominio personalizzato, caricare un certificato per il dominio e fare in modo che un record DNS punti al dominio msappproxy.net predefinito per l'applicazione. Per la procedura dettagliata, vedere Utilizzo di domini personalizzati nel proxy di applicazione di [Azure AD.](application-proxy-configure-custom-domain.md)

   - **Metodo di pre-autenticazione**: Azure Active DirectoryPre-authentication Method : Azure Active Directory

2. Dopo la pubblicazione dell'app, configurare le impostazioni Single Sign-On eseguendo le operazioni seguenti:

   a. Nella pagina dell'applicazione nel portale selezionare **Single Sign-On**.

   b. Per **Modalità Single Sign-On**, selezionare **Autenticazione integrata di Windows**.

   c. Impostare **SPN applicazione interna** sul valore impostato in precedenza.  

   d. Scegliere l'**identità di accesso delegato** che il connettore userà per conto degli utenti. Per ulteriori informazioni, vedere [Utilizzo di identità locali e cloud diverse](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities).

   e. Fare clic su **Salva** per salvare le modifiche.

Per completare la configurazione dell'applicazione, passare alla sezione **Utenti e gruppi** e assegnare agli utenti l'accesso all'applicazione.

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>Passaggio 3: Modificare gli URI di risposta per l'applicazioneStep 3: Modify the Reply URIs for the application

Prima che l'app Power BI per dispositivi mobili possa connettersi e accedere a Report Services, è necessario configurare la registrazione dell'applicazione creata automaticamente nel passaggio 2. 

1. Nella pagina **Panoramica** di Azure Active Directory selezionare **Registrazioni app**.
2. Nella scheda **Tutte le applicazioni** cercare l'applicazione creata nel passaggio 2.
3. Selezionare l'applicazione , quindi **selezionare Autenticazione**.
4. Aggiungere i seguenti URI di reindirizzamento in base alla piattaforma in uso.

   Quando si configura l'app per Power BI Mobile **iOS**, aggiungere i seguenti URI di reindirizzamento di tipo Client pubblico (Mobile & Desktop):
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`
   
   Quando si configura l'app per Power BI Mobile **Android**, aggiungere i seguenti URI di reindirizzamento di tipo Client pubblico (Mobile & Desktop):
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D` 
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > Affinché l'applicazione funzioni correttamente, è necessario aggiungere URI di reindirizzamento. Se si configura l'app sia per Power BI Mobile iOS che per Android, aggiungere il seguente URI di reindirizzamento di `urn:ietf:wg:oauth:2.0:oob`tipo Public Client (Mobile & Desktop) all'elenco degli URI di reindirizzamento configurati per iOS: .

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>Passaggio 4: Connettersi dall'app Power BI per dispositivi mobili

1. Nell'app Power BI per dispositivi mobili connettersi all'istanza di Reporting ServicesReporting Services . A tale scopo, immettere **l'URL esterno** per l'applicazione pubblicata tramite il proxy di applicazione.

   ![App Power BI per dispositivi mobili con URL esterno](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. Selezionare **Connetti**. Verrà indirizzata alla pagina di accesso di Azure Active Directory.You'll be directed to the Azure Active Directory sign in page.

3. Immettere le credenziali valide per l'utente e selezionare **Accedi**. Verranno visualizzati gli elementi dal server Reporting ServicesReporting Services .

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>Passaggio 5: Configurare i criteri di Intune per i dispositivi gestiti (facoltativo)Step 5: Configure Intune policy for managed devices (optional)

È possibile usare Microsoft Intune per gestire le app client usate dalla forza lavoro dell'azienda. Intune consente di usare funzionalità quali la crittografia dei dati e requisiti di accesso aggiuntivi. Per altre informazioni sulla gestione delle app tramite Intune, vedere Gestione app di Intune.To learn more about app management through Intune, see Intune App Management. Per abilitare l'applicazione per dispositivi mobili di Power BI per l'uso con i criteri di Intune, eseguire la procedura seguente.

1. Passare ad **Azure Active Directory** e quindi a **Registrazioni app**.
2. Selezionare l'applicazione configurata nel passaggio 3 durante la registrazione dell'applicazione client nativa.
3. Nella pagina dell'applicazione selezionare **Autorizzazioni API**.
4. Fare clic su **Aggiungi un'autorizzazione**. 
5. In **API l'organizzazione utilizza**, cercare "Gestione applicazioni mobili Microsoft" e selezionarla.
6. Aggiungere l'autorizzazione **DeviceManagementManagedApps.ReadWrite** all'applicazione
7. Fare clic su **Concedi il consenso dell'amministratore** per concedere l'autorizzazione all'accesso all'applicazione.
8. Configurare i criteri di Intune desiderati facendo riferimento a [Come creare e assegnare criteri](https://docs.microsoft.com/intune/app-protection-policies)di protezione delle app.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se l'applicazione restituisce una pagina di errore dopo aver tentato di caricare un report per più di alcuni minuti, potrebbe essere necessario modificare l'impostazione di timeout. Per impostazione predefinita, il proxy di applicazione supporta le applicazioni che richiedono fino a 85 secondi per rispondere a una richiesta. Per allungare questa impostazione a 180 secondi, selezionare il timeout back-end su **Lungo** nella pagina Impostazioni proxy app per l'applicazione. Per suggerimenti su come creare report veloci e affidabili, vedere Procedure consigliate per i report di [Power BI](https://docs.microsoft.com/power-bi/power-bi-reports-performance).

## <a name="next-steps"></a>Passaggi successivi

- [Consentire alle applicazioni client native di interagire con le applicazioni proxyEnable native client applications to interact with proxy applications](application-proxy-configure-native-client-application.md)
- [Visualizzare report e indicatori KPI locali dei server di report nelle app Power BI per dispositivi mobili](https://docs.microsoft.com/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
