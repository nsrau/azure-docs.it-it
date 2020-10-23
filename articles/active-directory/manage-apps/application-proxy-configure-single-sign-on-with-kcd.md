---
title: Single Sign-On basata su Kerberos (SSO) in Azure Active Directory con il proxy di applicazione
description: Viene illustrato come fornire Single Sign-On utilizzando Azure Active Directory Application Proxy.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/13/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperfq2
ms.openlocfilehash: 860d29d3fff2187e770a5ff00b7145fc188a497c
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426486"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-sso-to-your-apps-with-application-proxy"></a>Delega vincolata Kerberos per Single Sign-On (SSO) alle app con il proxy di applicazione

È possibile fornire l'accesso single sign-on per le applicazioni locali pubblicate mediante il proxy di applicazione che sono protette con l'autenticazione integrata di Windows. Queste applicazioni richiedono un ticket Kerberos per l'accesso. Il proxy di applicazione usa la delega vincolata Kerberos (KCD) per supportare queste applicazioni. 

È possibile abilitare l'accesso Single Sign-On alle applicazioni tramite l'autenticazione integrata di Windows (IWA) concedendo ai connettori proxy dell'applicazione l'autorizzazione per rappresentare gli utenti in Active Directory. I connettori usano questa autorizzazione per inviare e ricevere token per loro conto.

## <a name="how-single-sign-on-with-kcd-works"></a>Funzionamento di Single Sign-On con KCD
Questo diagramma illustra il flusso quando un utente tenta di accedere a un'applicazione locale che usa l'autenticazione integrata.

![Diagramma del flusso di autenticazione di Microsoft AAD](./media/application-proxy-configure-single-sign-on-with-kcd/authdiagram.png)

1. L'utente immette l'URL per accedere all'applicazione locale tramite Application Proxy.
2. Il proxy di applicazione reindirizza la richiesta ai servizi di autenticazione di Azure AD per la preautenticazione. A questo punto, Azure AD applica gli eventuali criteri di autenticazione e autorizzazione appropriati, ad esempio l'autenticazione a più fattori. Se l'utente viene convalidato, Azure AD crea un token e lo invia all'utente.
3. L'utente passa il token al proxy di applicazione.
4. Il proxy di applicazione convalida il token e recupera il nome dell'entità utente (UPN) da esso, quindi il connettore estrae l'UPN e il nome dell'entità servizio (SPN) tramite un canale sicuro con autenticazione doppia.
5. Il connettore esegue la negoziazione della delega vincolata Kerberos (delega vincolata Kerberos) con l'istanza locale di AD, rappresentando l'utente per ottenere un token Kerberos per l'applicazione.
6. Active Directory invia il token Kerberos per l'applicazione al connettore.
7. Il connettore invia la richiesta originale al server dell'applicazione, usando il token Kerberos ricevuto da Active Directory.
8. L'applicazione invia la risposta al connettore, che viene quindi restituita al servizio proxy di applicazione e infine all'utente.

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare a usare SSO per le applicazioni IWA, verificare che l'ambiente sia pronto con le impostazioni e configurazioni seguenti:

* Le app, ad esempio le app Web SharePoint, devono essere impostate per usare l'autenticazione integrata di Windows. Per altre informazioni, vedere [Attivare il supporto per l'autenticazione Kerberos](https://technet.microsoft.com/library/dd759186.aspx). Per SharePoint, vedere [Pianificare l'autenticazione Kerberos in SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
* Tutte le app hanno [nomi di entità servizio](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx).
* Il server che esegue il connettore e il server che esegue l'app devono essere aggiunti a un dominio e appartenere allo stesso dominio o a domini trusting. Per altre informazioni sull'aggiunta a un dominio, vedere [Aggiungere un computer a un dominio](https://technet.microsoft.com/library/dd807102.aspx).
* Il server che esegue il connettore deve avere accesso in lettura all'attributo TokenGroupsGlobalAndUniversal per gli utenti. Questa impostazione predefinita potrebbe essere interessata dalla protezione avanzata dell'ambiente.

### <a name="configure-active-directory"></a>Configurare Active Directory
La configurazione di Active Directory varia a seconda del fatto che il connettore proxy di applicazione e il server dell'applicazione si trovino nello stesso dominio o meno.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Connettore e server applicazione nello stesso dominio
1. In Active Directory passare a **strumenti**  >  **utenti e computer**.
2. Selezionare il server che esegue il connettore.
3. Fare clic con il pulsante **Properties**destro del mouse e scegliere  >  **delega**proprietà.
4. Selezionare **Computer attendibile per la delega solo ai servizi specificati**. 
5. Selezionare **Utilizza un qualsiasi protocollo di autenticazione**.
6. In **Servizi ai quali l'account può presentare credenziali delegate** aggiungere il valore per l'identità SPN del server applicazioni. In questo modo il connettore proxy di applicazione può rappresentare gli utenti in AD nei confronti delle applicazioni definite nell'elenco.

   ![Schermata della finestra delle proprietà per Connector-SVR](./media/application-proxy-configure-single-sign-on-with-kcd/properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Connettore e server applicazione in domini differenti
1. Per un elenco dei prerequisiti necessari per usare la delega vincolata Kerberos tra domini, vedere [Delega vincolata Kerberos tra domini](https://technet.microsoft.com/library/hh831477.aspx).
2. Utilizzare la `principalsallowedtodelegateto` proprietà dell'account del servizio (computer o account utente di dominio dedicato) dell'applicazione Web per abilitare la delega di autenticazione Kerberos dal proxy di applicazione (connettore). Il server applicazioni viene eseguito nel contesto di `webserviceaccount` e il server di delega è `connectorcomputeraccount` . Eseguire i comandi seguenti in un controller di dominio (che esegue Windows Server 2012 R2 o versione successiva) nel dominio di `webserviceaccount` . Usare nomi flat (non UPN) per entrambi gli account.

   Se `webserviceaccount` è un account computer, usare i comandi seguenti:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADComputer -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADComputer webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

   Se `webserviceaccount` è un account utente, usare i comandi seguenti:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADUser -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADUser webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On 
1. Pubblicare l'applicazione seguendo le istruzioni contenute in [Pubblicare le applicazioni con il proxy di applicazione](application-proxy-add-on-premises-application.md). Assicurarsi di selezionare **Azure Active Directory** come **Metodo di preautenticazione**.
2. Quando l'applicazione viene visualizzata nell'elenco delle applicazioni aziendali, selezionarla e fare clic su **Single Sign-On**.
3. Impostare la modalità Single Sign-On su **Autenticazione integrata di Windows**.  
4. Immettere il valore di **SPN applicazione interna** del server applicazioni. In questo esempio l'SPN per l'applicazione pubblicata è http/www.contoso.com. Questo nome SPN deve essere nell'elenco dei servizi a cui il connettore può presentare credenziali delegate. 
5. Scegliere l'**identità di accesso delegato** che il connettore userà per conto degli utenti. Per altre informazioni, vedere [uso di diverse identità locali e cloud](#working-with-different-on-premises-and-cloud-identities)

   ![Configurazione avanzata dell'applicazione](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  

## <a name="sso-for-non-windows-apps"></a>Accesso Single Sign-On per app non Windows

Il flusso di delega Kerberos nel proxy di applicazione di Azure AD inizia quando Azure AD autentica l'utente nel cloud. Quando la richiesta arriva in locale, il connettore del proxy dell'applicazione di Azure AD rilascia un ticket Kerberos per conto dell'utente tramite l'interazione con Active Directory locale. Questo processo è definito come delega vincolata Kerberos. 

Nella fase successiva, viene inviata una richiesta all'applicazione back-end con il ticket Kerberos. 

Esistono diversi meccanismi che definiscono come inviare il ticket Kerberos in tali richieste. La maggior parte dei server non Windows si aspettano di riceverli sotto forma di token SPNEGO. Questo meccanismo è supportato nel proxy di applicazione Azure AD, ma è disabilitato per impostazione predefinita. Un connettore può essere configurato per SPNEGO o per il token Kerberos standard, ma non per entrambi.

Se si configura una macchina di connettori per SPNEGO, assicurarsi che tutti gli altri connettori in tale gruppo siano configurati anche con SPNEGO. Le applicazioni che prevedono il token Kerberos standard devono essere instradate tramite altri connettori che non sono configurati per SPNEGO. Alcune applicazioni Web accettano entrambi i formati senza apportare modifiche alla configurazione. 
 

Per abilitare SPNEGO:

1. Aprire un prompt dei comandi come amministratore.
2. Dal prompt dei comandi, eseguire i comandi seguenti nei server del connettore che richiedono SPNEGO.

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

Le app non Windows usano generalmente nomi utente o nomi account SAM invece di indirizzi e-mail di dominio. Se questa situazione si applica alle applicazioni, è necessario configurare il campo dell'identità di accesso delegata in modo che connetta le identità del cloud alle identità delle applicazioni. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Utilizzo dell'accesso Single Sign-On quando le identità cloud e locali non sono identiche
Il proxy di applicazione presuppone che gli utenti dispongano della stessa identità nel cloud e in locale. In alcuni ambienti, tuttavia, a causa di criteri aziendali o dipendenze dell'applicazione, è possibile che le organizzazioni debbano usare ID alternativi per l'accesso. In questi casi, è comunque possibile usare delega vincolata Kerberos per Single Sign-On. Configurare un'**identità di accesso delegata** per ogni applicazione in modo da specificare le identità da usare durante l'esecuzione del Single Sign-On.  

Questa funzionalità consente a molte organizzazioni con diverse identità locali e cloud di disporre dell'accesso Single Sign-On dal cloud ad applicazioni locali senza richiedere agli utenti di immettere nomi utente e password diversi. Sono incluse organizzazioni che:

* Hanno più domini internamente (joe@us.contoso.com, joe@eu.contoso.com) e un singolo dominio nel cloud (joe@contoso.com).
* Hanno un nome di dominio non instradabile internamente (joe@contoso.usa) e un dominio valido nel cloud.
* Non usano nomi di dominio internamente (joe).
* Usare alias diversi in locale e nel cloud. Ad esempio, joe-johns@contoso.com e joej@contoso.com  

Con il proxy di applicazione è possibile selezionare l'identità da usare per ottenere il ticket Kerberos. Questa impostazione viene configurata per ogni applicazione. Alcune di queste opzioni sono appropriate per i sistemi che non accettano il formato di indirizzo di posta elettronica, altre sono concepite per l'accesso alternativo.

![Schermata del parametro dell'identità di accesso delegata](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Se si usa l'identità di accesso delegata, il valore potrebbe non essere univoco per tutti i domini o tutte le foreste dell'organizzazione. Per evitare questo problema, pubblicare queste applicazioni due volte usando due gruppi di connettori diversi. Poiché ogni applicazione dispone di un pubblico di utenti diversi, è possibile unire i connettori a un altro dominio.

Se per l'identità di accesso viene usato il **nome dell'account SAM locale** , il computer che ospita il connettore deve essere aggiunto al dominio in cui si trova l'account utente.

### <a name="configure-sso-for-different-identities"></a>Configurare Single Sign-On per diverse identità
1. Configurare le impostazioni di Azure AD Connect in modo che l'identità principale sia l'indirizzo di posta elettronica (mail). Ciò avviene come parte del processo di personalizzazione, modificando il campo **Nome dell'entità utente** nelle impostazioni di sincronizzazione. Queste impostazioni determinano inoltre il modo in cui gli utenti accedono a Office 365, dispositivi Windows 10 e altre applicazioni che usano Azure AD come archivio di identità.  
   ![Schermata di identificazione degli utenti - Elenco a discesa Nome dell'entità utente](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. Nelle impostazioni di configurazione dell'applicazione che si desidera modificare, selezionare l' **Identità di accesso delegata** da usare:

   * Nome dell'entità utente (ad esempio joe@contoso.com)
   * Nome alternativo dell'entità utente (ad esempio joed@contoso.local)
   * Parte del nome utente del nome dell'entità utente (ad esempio joe)
   * Parte del nome utente del nome alternativo dell'entità utente (ad esempio joe)
   * Nome account SAM locale: in base alla configurazione del controller di dominio locale

### <a name="troubleshooting-sso-for-different-identities"></a>Risoluzione dei problemi di accesso Single Sign-On per diverse identità
Se si verifica un errore nel processo SSO, questo viene visualizzato nel registro eventi del computer connettore, come illustrato in [risoluzione dei problemi](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).
In alcuni casi, tuttavia, la richiesta viene inviata correttamente all'applicazione back-end mentre l'applicazione risponde in numerose altre risposte HTTP. Per la risoluzione di problemi di questo tipo, è consigliabile iniziare esaminando il numero di eventi 24029 sul computer connettore nel registro eventi di sessione del proxy di applicazione. L'identità utente usata per la delega viene visualizzata nel campo "utente" dei dettagli dell'evento. Per attivare il log della sessione, scegliere **Visualizza registri analitici e di debug** dal menu di visualizzazione del Visualizzatore eventi.

## <a name="next-steps"></a>Passaggi successivi

* [Come configurare un'applicazione proxy dell'applicazione per l'uso della delega vincolata Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Risolvere i problemi che si verificano con il proxy di applicazione](application-proxy-troubleshoot.md)

