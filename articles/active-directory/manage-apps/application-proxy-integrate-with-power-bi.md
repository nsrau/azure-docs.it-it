---
title: Abilitare l'accesso remoto a Power BI con Azure AD proxy di applicazione | Microsoft Docs
description: Vengono illustrate le nozioni di base sull'integrazione di un Power BI locale con Azure AD proxy di applicazione.
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
ms.openlocfilehash: 845ffda22cae9464870786cc5997b9f5521c03e1
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795635"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>Abilitare l'accesso remoto a Power BI mobile con Azure AD proxy di applicazione

Questo articolo illustra come usare Azure AD proxy di applicazione per abilitare l'app per dispositivi mobili Power BI a connettersi Server di report di Power BI (PBIRS) e SQL Server Reporting Services (SSRS) 2016 e versioni successive. Grazie a questa integrazione, gli utenti che si trovano fuori dalla rete aziendale possono accedere ai report Power BI dall'app Power BI per dispositivi mobili ed essere protetti da Azure AD l'autenticazione. Questa protezione include [vantaggi](application-proxy-security.md#security-benefits) per la sicurezza, ad esempio l'accesso condizionale e l'autenticazione a più fattori.  

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che siano già stati distribuiti i servizi di report e il [proxy di applicazione abilitato](application-proxy-add-on-premises-application.md).

- Per abilitare il proxy di applicazione è necessario installare un connettore in un server Windows e completare i [prerequisiti](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) in modo che il connettore possa comunicare con Azure ad servizi.  
- Quando si pubblica Power BI, si consiglia di usare gli stessi domini interni ed esterni. Per ulteriori informazioni sui domini personalizzati, vedere [utilizzo di domini personalizzati nel proxy di applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain).
- Questa integrazione è disponibile per l'applicazione **Power BI per dispositivi mobili iOS e Android** .

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Passaggio 1: configurare la delega vincolata Kerberos (delega vincolata Kerberos)

Per le applicazioni locali che usano l'autenticazione di Windows, è possibile ottenere l'accesso Single Sign-On (SSO) con il protocollo di autenticazione Kerberos e una funzionalità chiamata delega vincolata Kerberos. Quando è configurato, delega vincolata Kerberos consente al connettore del proxy di applicazione di ottenere un token Windows per un utente, anche se l'utente non ha effettuato l'accesso direttamente a Windows. Per altre informazioni su delega vincolata Kerberos, vedere [Cenni preliminari sulla delega vincolata Kerberos](https://technet.microsoft.com/library/jj553400.aspx) e [la delega vincolata Kerberos per Single Sign-on alle app con il proxy di applicazione](application-proxy-configure-single-sign-on-with-kcd.md).

Non è molto necessario configurare sul lato Reporting Services. Assicurarsi di avere un nome dell'entità servizio (SPN) valido per consentire l'autenticazione Kerberos corretta. Verificare inoltre che il Server Reporting Services sia abilitato per l'autenticazione Negotiate.

Per configurare delega vincolata Kerberos per Reporting Services, continuare con i passaggi seguenti.

### <a name="configure-the-service-principal-name-spn"></a>Configurare il nome dell'entità servizio (SPN)

Il nome SPN è un identificatore univoco per un servizio che utilizza l'autenticazione Kerberos. È necessario assicurarsi che sia presente un nome SPN HTTP appropriato per il server di report. Per informazioni su come configurare il nome dell'entità servizio (SPN) appropriato per il server di report, vedere [registrare un nome dell'entità servizio (SPN) per un server di report](https://msdn.microsoft.com/library/cc281382.aspx).
È possibile verificare che l'SPN sia stato aggiunto tramite il comando Setspn con l'opzione -L. Per altre informazioni su questo comando, vedere [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx).

### <a name="enable-negotiate-authentication"></a>Abilita autenticazione negoziata

Per consentire a un server di report di utilizzare l'autenticazione Kerberos, configurare il tipo di autenticazione del server di report in modo che sia RSWindowsNegotiate. Configurare questa impostazione usando il file RSReportServer. config.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

Per ulteriori informazioni, vedere [modificare un file di configurazione Reporting Services](https://msdn.microsoft.com/library/bb630448.aspx) e [configurare l'autenticazione di Windows in un server di report](https://msdn.microsoft.com/library/cc281253.aspx).

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>Verificare che il connettore sia attendibile per la delega al nome SPN aggiunto all'account del pool di applicazioni Reporting Services
Configurare delega vincolata Kerberos in modo che il servizio proxy applicazione Azure AD possa delegare le identità utente all'account del pool di applicazioni Reporting Services. A questo scopo, consentire al connettore proxy di applicazione di recuperare i ticket Kerberos per gli utenti autenticati in Azure AD. Il server passa quindi il contesto all'applicazione di destinazione o Reporting Services in questo caso.

Per configurare delega vincolata Kerberos, ripetere i passaggi seguenti per ogni computer connettore:

1. Accedere a un controller di dominio come amministratore di dominio e quindi aprire **Active Directory utenti e computer**.
2. Trovare il computer in cui è in esecuzione il connettore.  
3. Fare doppio clic sul computer e quindi selezionare la scheda **delega** .
4. Impostare le impostazioni di delega per **considerare attendibile il computer per la delega solo ai servizi specificati**. Selezionare quindi **Usa un qualsiasi protocollo di autenticazione**.
5. Selezionare **Aggiungi**, quindi selezionare **utenti o computer**.
6. Immettere l'account del servizio che si sta usando per Reporting Services. Si tratta dell'account a cui è stato aggiunto il nome SPN all'interno della configurazione del Reporting Services.
7. Fare clic su **OK**. Per salvare le modifiche, fare di nuovo clic su **OK** .

Per altre informazioni, vedere [delega vincolata Kerberos per Single Sign-on alle app con il proxy di applicazione](application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>Passaggio 2: pubblicare i servizi di report tramite Azure AD proxy di applicazione

A questo punto si è pronti per configurare Azure AD proxy di applicazione.

1. Pubblicare i servizi di report tramite il proxy di applicazione con le impostazioni seguenti. Per istruzioni dettagliate su come pubblicare un'applicazione tramite il proxy di applicazione, vedere [pubblicazione di applicazioni con Azure ad proxy di applicazione](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   - **URL interno**: immettere l'URL del server di report che il connettore può raggiungere nella rete aziendale. Verificare che l'URL sia raggiungibile dal server in cui è installato il connettore. Una procedura consigliata consiste nell'usare un dominio di primo livello, ad esempio `https://servername/` per evitare problemi con i sottopercorsi pubblicati tramite il proxy di applicazione. Ad esempio, usare `https://servername/` e non `https://servername/reports/` o `https://servername/reportserver/`.
     > [!NOTE]
     > Si consiglia di utilizzare una connessione HTTPS sicura al server di report. Per informazioni su come, vedere [configurare connessioni SSL in un server di report in modalità nativa](https://docs.microsoft.com/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017) .
   - **URL esterno**: immettere l'URL pubblico a cui si connetterà Power bi app per dispositivi mobili. Ad esempio, potrebbe essere simile `https://reports.contoso.com` se viene utilizzato un dominio personalizzato. Per usare un dominio personalizzato, caricare un certificato per il dominio e puntare un record DNS al dominio msappproxy.net predefinito per l'applicazione. Per i passaggi dettagliati, vedere [utilizzo di domini personalizzati nel proxy di applicazione Azure ad](application-proxy-configure-custom-domain.md).

   - **Metodo di pre-autenticazione**: Azure Active Directory

2. Dopo la pubblicazione dell'app, configurare le impostazioni Single Sign-On eseguendo le operazioni seguenti:

   a. Nella pagina dell'applicazione nel portale selezionare **Single Sign-On**.

   b. Per **modalità Single Sign-on**, selezionare **autenticazione integrata di Windows**.

   c. Impostare **Nome dell'entità servizio dell'applicazione interna** sul valore impostato in precedenza,  

   d. Scegliere l'**identità di accesso delegato** che il connettore userà per conto degli utenti. Per altre informazioni, vedere [uso di diverse identità locali e cloud](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities).

   e. Fare clic su **Salva** per salvare le modifiche.

Per completare la configurazione dell'applicazione, passare alla sezione **utenti e gruppi** e assegnare agli utenti l'accesso a questa applicazione.

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>Passaggio 3: modificare l'URI di risposta per l'applicazione

Prima che l'app per dispositivi mobili Power BI possa connettersi e accedere a servizi di report, è necessario configurare la registrazione dell'applicazione creata automaticamente nel passaggio 2. 

1. Nella pagina **panoramica** Azure Active Directory selezionare **registrazioni app**.
2. Nella scheda **tutte le applicazioni** cercare l'applicazione creata nel passaggio 2.
3. Selezionare l'applicazione, quindi selezionare **autenticazione**.
4. Aggiungere gli URI di reindirizzamento seguenti in base alla piattaforma in uso.

   Quando si configura l'app per Power BI **iOS**per dispositivi mobili, aggiungere gli URI di reindirizzamento seguenti di tipo public client (Mobile & desktop):
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`
   
   Quando si configura l'app per Power BI mobile **Android**, aggiungere gli URI di reindirizzamento seguenti di tipo public client (Mobile & desktop):
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D` 
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > Per il corretto funzionamento dell'applicazione, è necessario aggiungere gli URI di reindirizzamento. Se si configura l'app per dispositivi mobili Power BI iOS e Android, aggiungere l'URI di reindirizzamento seguente di tipo public client (Mobile & desktop) all'elenco degli URI di reindirizzamento configurati per iOS: `urn:ietf:wg:oauth:2.0:oob`.

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>Passaggio 4: connettersi dall'app per dispositivi mobili Power BI

1. Nell'app per dispositivi mobili Power BI connettersi all'istanza di Reporting Services. A tale scopo, immettere l' **URL esterno** per l'applicazione pubblicata tramite il proxy di applicazione.

   ![Power BI app per dispositivi mobili con URL esterno](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. Selezionare **Connessione**. Si verrà indirizzati alla pagina di accesso Azure Active Directory.

3. Immettere le credenziali valide per l'utente e selezionare **Accedi**. Gli elementi verranno visualizzati dal server Reporting Services.

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>Passaggio 5: configurare i criteri di Intune per i dispositivi gestiti (facoltativo)

> [!NOTE]
> Questa funzionalità è attualmente disponibile solo in iOS.

È possibile usare Microsoft Intune per gestire le app client utilizzate dalla forza lavoro della società. Intune consente di usare funzionalità quali la crittografia dei dati e i requisiti di accesso aggiuntivi. Per altre informazioni sulla gestione delle app tramite Intune, vedere Gestione delle app di Intune. Per consentire all'applicazione Power BI per dispositivi mobili di usare i criteri di Intune, attenersi alla procedura seguente.

1. Passare a **Azure Active Directory** e quindi a registrazioni per l' **app**.
2. Selezionare l'applicazione configurata nel passaggio 3 durante la registrazione dell'applicazione client nativa.
3. Nella pagina dell'applicazione selezionare **autorizzazioni API**.
4. Fare clic su **Aggiungi un'autorizzazione**. 
5. In **API utilizzate dall'organizzazione**, cercare "Microsoft Mobile Application Management" e selezionarlo.
6. Aggiungere l'autorizzazione **DeviceManagementManagedApps. ReadWrite** all'applicazione
7. Fare clic su **Concedi consenso amministratore** per concedere l'autorizzazione di accesso all'applicazione.
8. Configurare i criteri di Intune desiderati facendo riferimento a [come creare e assegnare criteri di protezione delle app](https://docs.microsoft.com/intune/app-protection-policies).

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se l'applicazione restituisce una pagina di errore dopo aver tentato di caricare un report per più di pochi minuti, potrebbe essere necessario modificare l'impostazione del timeout. Per impostazione predefinita, il proxy di applicazione supporta le applicazioni che richiedono fino a 85 secondi per rispondere a una richiesta. Per estendere questa impostazione a 180 secondi, selezionare il timeout di back-end a **Long** nella pagina delle impostazioni del proxy dell'applicazione per l'applicazione. Per suggerimenti su come creare report veloci e affidabili, vedere [Power bi segnala le procedure consigliate](https://docs.microsoft.com/power-bi/power-bi-reports-performance).

## <a name="next-steps"></a>Passaggi successivi

- [Consentire alle applicazioni client native di interagire con le applicazioni proxy](application-proxy-configure-native-client-application.md)
- [Visualizzare report e indicatori KPI locali del server di report nelle app per dispositivi mobili Power BI](https://docs.microsoft.com/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
