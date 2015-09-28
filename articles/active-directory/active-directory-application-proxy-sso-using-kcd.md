<properties
	pageTitle="Accesso Single Sign-On alle app locali con Autenticazione integrata di Windows tramite il proxy di applicazione"
	description="Viene illustrato come iniziare a utilizzare il proxy dell'applicazione di AD Azure."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="msStevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/09/2015"
	ms.author="rkarlin"/>



# Accesso Single Sign-On alle app locali con Autenticazione integrata di Windows tramite il proxy di applicazione


È possibile abilitare l'accesso Single Sign-On (SSO) alle applicazioni che usano l'autenticazione integrata di Windows concedendo ai connettori proxy di applicazione l'autorizzazione per rappresentare gli utenti e inviare e ricevere token per loro conto in Active Directory.

> [AZURE.IMPORTANT]Il proxy di applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).


## Diagramma di rete

![Diagramma del flusso di autenticazione di Microsoft AAD](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

Questo diagramma illustra il flusso quando un utente tenta di accedere a un'applicazione locale che usa l'autenticazione integrata di Windows. Flusso generale:

1. L'utente immette l'URL per accedere all'applicazione locale tramite il proxy di applicazione.
2. Il proxy di applicazione reindirizza la richiesta ai servizi di autenticazione di Azure AD per la preautenticazione. A questo punto, Azure AD applica gli eventuali criteri di autenticazione e autorizzazione appropriati, ad esempio l'autenticazione a più fattori. Se l'utente viene convalidato, Azure AD crea un token e lo invia all'utente.
3. L'utente passa il token al proxy di applicazione.
4. Il proxy di applicazione convalida il token e recupera il nome dell'entità utente (UPN) da esso e invia la richiesta, il nome dell'entità utente e il nome dell'entità servizio (SPN) al connettore tramite un canale sicuro con doppia autenticazione.
5. Il connettore esegue la negoziazione della delega vincolata Kerberos con l'istanza di Active Directory locale, rappresentando l'utente per ottenere un token Kerberos per l'applicazione.
6. Active Directory invia il token Kerberos per l'applicazione al connettore.
7. Il connettore invia la richiesta originale al server dell'applicazione, usando il token Kerberos ricevuto da Active Directory.
8. L'applicazione invia la risposta al connettore, che viene quindi restituita al servizio proxy di applicazione e infine all'utente.

### Prerequisiti

1. Assicurarsi che le app, ad esempio le app Web SharePoint, siano impostate per usare l'autenticazione integrata di Windows. Per altre informazioni, vedere [Attivare il supporto per l'autenticazione Kerberos](https://technet.microsoft.com/library/dd759186.aspx) oppure, per SharePoint, vedere [Pianificare l'autenticazione Kerberos in SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
2. Creare i nomi delle entità servizio per le applicazioni.
3. Assicurarsi che il server che esegue il connettore e che il server che esegue l'app in fase di pubblicazione siano aggiunti a un dominio e che appartengano allo stesso dominio. Per altre informazioni sull'aggiunta a un dominio, vedere [Aggiungere un computer a un dominio](https://technet.microsoft.com/library/dd807102.aspx).


## Configurazione di Active Directory

La configurazione di Active Directory varia a seconda del fatto che il connettore proxy di applicazione e il server pubblicato si trovino nello stesso dominio o meno.

### Connettore e server pubblicato nello stesso dominio



1. In Active Directory passare a **Strumenti** > **Utenti e computer**. 
2. Selezionare il server che esegue il connettore. 
3. Fare clic con il pulsante destro del mouse e scegliere **Proprietà** > **Delega**. 
4. Selezionare **Computer attendibile per la delega solo ai servizi specificati** e in **Servizi ai quali l'account può presentare credenziali delegate** aggiungere il valore per l'identità del nome dell'entità servizio (SPN) del server dell'applicazione. 
5. In questo modo il connettore proxy di applicazione può rappresentare gli utenti in AD nei confronti delle applicazioni definite nell'elenco.

![Schermata della finestra delle proprietà per Connector-SVR](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

### Connettore e server pubblicato in domini diversi

1. Per un elenco dei prerequisiti necessari per usare la delega vincolata Kerberos tra domini, vedere [Delega vincolata Kerberos tra domini](https://technet.microsoft.com/library/hh831477.aspx).
2. In Windows Server 2012 R2 usare la proprietà `principalsallowedtodelegateto` nel server del connettore per consentire al proxy di applicazione la delega per il server del connettore, dove il server pubblicato è `sharepointserviceaccount` e il server che delega è `connectormachineaccount`.

		$connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

		Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

		Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount


>[AZURE.NOTE]`sharepointserviceaccount` può essere l'account computer SPS o un account del servizio tramite cui è in esecuzione il pool di app SPS.


## Configurazione del portale di Azure

1. Pubblicare l'applicazione seguendo le istruzioni contenute in [Pubblicare le applicazioni con il proxy di applicazione](active-directory-application-proxy-publish.md). Assicurarsi di selezionare **Azure Active Directory** come **Metodo di autenticazione preliminare**.
2. Una volta visualizzata l'applicazione nell'elenco delle applicazioni, selezionarla e fare clic su **Configura**.
3. In **Proprietà** impostare **Metodo di autenticazione interna** su **Autenticazione integrata di Windows**.

![Configurazione dell'applicazione avanzata](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)

4. Immettere il **Nome dell'entità servizio dell'applicazione interna** del server dell'applicazione. In questo esempio il nome dell'entità servizio per l'applicazione pubblicata è http/lob.contoso.com.

>[AZURE.IMPORTANT]Per il corretto funzionamento della preautenticazione, i nomi delle entità servizio in Azure Active Directory devono essere identici ai nomi delle entità servizio in Active Directory locale. Assicurarsi che l'istanza di Azure Active Directory sia sincronizzato con l'istanza di Active Directory locale.

| | |
| --- | --- |
| Metodo di autenticazione interna | Se si usa il proxy di applicazione per la preautenticazione, è possibile impostare un metodo di autenticazione interno per consentire agli utenti di usufruire dell'accesso Single Sign-On a questa applicazione. <br><br> Selezionare**Autenticazione integrata di Windows (IWA)** se l'applicazione utilizza l'autenticazione integrata di Windows (IWA) ed è possibile configurare la delega vincolata Kerberos (Kerberos Constrained Delegation, KCD) per abilitare SSO per questa applicazione. Le applicazioni con autenticazione integrata di Windows devono essere configurate usando la delega vincolata Kerberos, in caso contrario il proxy di applicazione non sarà in grado di pubblicare queste applicazioni. <br><br>Selezionare**Nessuna** se l'applicazione non utilizza l'autenticazione integrata di Windows. |
| SPN dell’applicazione interna | Si tratta del nome dell'entità servizio (SPN) dell'applicazione interna come configurato nell'istanza di Azure AD locale. Il nome dell'entità servizio viene usato dal connettore proxy di applicazione per recuperare i token Kerberos per l'applicazione mediante la delega vincolata Kerberos. |

<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg

<!---HONumber=Sept15_HO3-->