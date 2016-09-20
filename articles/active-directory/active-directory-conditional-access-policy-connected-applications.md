<properties
	pageTitle="Come impostare criteri di accesso condizionale basato su dispositivo di Azure Active Directory per controllare gli accessi delle applicazioni connesse ad Azure Active Directory"
	description="Spiega in che modo gli amministratori IT possono configurare i criteri di accesso condizionale basato su dispositivo per le applicazioni Azure AD collegate."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/09/2016"
	ms.author="markvi"/>


# Come impostare criteri di accesso condizionale basato su dispositivo di Azure Active Directory per controllare gli accessi delle applicazioni connesse ad Azure Active Directory 


L'accesso condizionale basato su dispositivo di Azure Active Directory consente di proteggere le risorse dell'organizzazione da:

- Accesso da dispositivi sconosciuti/non gestiti
- Dispositivi che non soddisfano i criteri di sicurezza definiti dall'organizzazione.

Per una panoramica dell'accesso condizionale, vedere l'articolo sull'[accesso condizionale basato su dispositivo di Azure Active Directory](active-directory-conditional-access.md).

L'impostazione dei criteri di accesso condizionale basato su dispositivo consente di proteggere:

- Office 365 SharePoint Online per la sicurezza di documenti e siti organizzativi.

- Office 365 Exchange Online per la sicurezza dei messaggi di posta elettronica aziendali.

- Applicazioni SaaS connesse ad Azure AD per a scopo di autenticazione.

- Applicazioni locali pubblicate tramite il proxy dell'applicazione di Azure AD.


Nel portale di gestione di Azure, è possibile impostare questo criterio accedendo all'applicazione desiderata nella relativa directory.

 
  ![Applicazioni](./media/active-directory-conditional-access-policy-connected-applications/01.png "Applicazioni")  


Dopo aver selezionato l'applicazione, fare clic sulla scheda **Configura** per impostare i criteri di accesso condizionale.


  ![Regole di accesso basato su dispositivo](./media/active-directory-conditional-access-policy-connected-applications/02.png "Regole di accesso basato su dispositivo")  


 

Per abilitare i criteri di accesso condizionale basato su dispositivo, nella sezione **Regole di accesso in base al dispositivo** alla voce **Abilita regole di accesso** selezionare **On**.

Questo criterio è costituito da tre elementi:

1. **Apply to** (Applica a): l'ambito degli utenti a cui si applica questo criterio al momento di accedere all'applicazione.

2. **Regole dispositivo**: le condizioni che devono soddisfare i dispositivi per poter accedere all'applicazione.

3. **Imposizione dell'applicazione**: le applicazioni client (native e browser) da valutare in base ai criteri.

  ![Regole di accesso basato su dispositivo](./media/active-directory-conditional-access-policy-connected-applications/03.png "Regole di accesso basato su dispositivo")
 

## Impostazione degli utenti a cui vengono applicati i criteri 

Nella sezione **Apply to** (Applica a) è possibile selezionare l'ambito degli utenti a cui si applica questo criterio.

Le opzioni disponibili sono due:

- **Tutti gli utenti**: per chiunque acceda all'applicazione

- **Gruppi**: per gli utenti membri di uno o più gruppi.

![Applica a](./media/active-directory-conditional-access-policy-connected-applications/11.png "Applica a")  


Selezionando **Escludi**, è possibile escludere utenti da questi criteri durante l'accesso all'applicazione. Ciò risulta utile quando è necessario abilitare determinati utenti per l'accesso temporaneo all'applicazione. Selezionare questa opzione, ad esempio, se alcuni utenti possiedono dispositivi non predisposti per l'accesso condizionale (non ancora registrati, non conformi e così via).
 

## Selezione delle condizioni necessarie per i dispositivi 

In **Regole dispositivo** vengono impostate le condizioni di accesso all'applicazione da parte dei dispositivi.

Per l'accesso condizionale basato su dispositivo, sono supportati i dispositivi seguenti:

- Aggiornamento dell'anniversario di Windows 10, Windows 7 e Windows 8.1.

- Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2

- Dispositivi iOS (iPad, iPhone)

- Dispositivi Android

Presto sarà disponibile il supporto per dispositivi Mac.

  ![Dispositivi](./media/active-directory-conditional-access-policy-connected-applications/04.png "Applicazioni")  



 >[AZURE.NOTE] Per informazioni sulle differenze fra l'aggiunta a un dominio e l'aggiunta ad Azure AD, vedere [Uso di dispositivi Windows 10 in azienda](active-directory-azureadjoin-windows10-devices.md).


Le opzioni disponibili sono due:

- **Tutti i dispositivi devono essere conformi**: questa opzione richiede che tutte le piattaforme dei dispositivi che accedono all'applicazione siano conformi. Ai dispositivi basati su piattaforme che non supportano l'accesso condizionale basato su dispositivo viene negato l'accesso.

- **Solo i dispositivi selezionati devono essere compatibili**: questa opzione richiede che solo le piattaforme dei dispositivi selezionati siano conformi. Alle piattaforme non selezionate o ad altre piattaforme che accedono all'applicazione sarà consentito l'accesso.

  ![Applicazioni](./media/active-directory-conditional-access-policy-connected-applications/05.png "Applicazioni")  



Ogni dispositivo aggiunto al dominio Azure AD è conforme se contrassegnato come **Conforme** nella directory da Microsoft Intune o da un sistema di gestione di dispositivi mobili (MDM) di terze parti che si integra con Azure AD.

I dispositivi aggiunti a un dominio sono conformi nei due casi seguenti:

- Se registrati con Azure AD, il fatto che siano aggiunti a un dominio fa sì che molte organizzazioni li considerino attendibili.

- Se sono contrassegnati come conformi in Azure AD in System Center Configuration Manager 2016.

 ![Regole dispositivo](./media/active-directory-conditional-access-policy-connected-applications/06.png "Regole dispositivo")
 

Ogni dispositivo personale Windows è conforme se contrassegnato come **Conforme** nella directory da Microsoft Intune o da un sistema di gestione di dispositivi mobili (MDM) di terze parti che si integra con Azure AD.

I dispositivi non Windows sono compatibili se contrassegnati con la dicitura **Conforme** nella directory tramite Microsoft Intune.

 >[AZURE.NOTE] Per ulteriori informazioni su come configurare Azure AD per la conformità del dispositivo tramite il sistema di gestione, vedere [Accesso condizionale di Azure Active Directory](active-directory-conditional-access.md).


Quando si selezionano determinate piattaforme dei dispositivi, è possibile selezionare una o più opzioni, tra cui Android, iOS, Windows Mobile (cellulari e tablet Windows 8.1) e Windows (tutti gli altri dispositivi Windows, compresi tutti i dispositivi Windows 10). Con questa opzione, la valutazione dei criteri viene effettuata solo su determinate piattaforme. Se si tenta di accedere da un dispositivo che non fa parte della selezione, non viene valutato alcun criterio del dispositivo e il dispositivo è ammesso solo se lo è l'utente.

![Regole dispositivo](./media/active-directory-conditional-access-policy-connected-applications/07.png "Regole dispositivo")
  

## Scelta del tipo di applicazioni client in cui verranno valutati i criteri 

Nella sezione **Imposizione dell'applicazione** impostare il tipo di applicazioni per cui è necessario valutare i criteri.


Le opzioni disponibili sono due:

- Per il browser e le applicazioni native
- Solo per le applicazioni native.


![Applicazioni](./media/active-directory-conditional-access-policy-connected-applications/08.png "Applicazioni")  


Scegliendo **Per browser e applicazioni native**, i criteri di accesso vengono applicati alle applicazioni tramite:

- Browser (ad esempio, Edge in Windows 10, Safari in iOS e così via)
- Applicazioni che usano Active Directory Authentication Library (ADAL) in qualsiasi piattaforma oppure l'API di Gestione account Web in Windows 10

>[AZURE.NOTE] Per ulteriori informazioni sui browser supportati e altre considerazioni per l'utente finale che accede alle applicazioni con protezione CA in base al dispositivo, vedere [Accesso condizionale di Azure Active Directory](active-directory-conditional-access.md).

 

## Protezione dell'accesso alla posta elettronica da applicazioni basate su Exchange Active Sync 

Nelle applicazioni di Office 365 Exchange Online è presente una sezione aggiuntiva denominata **Exchange Activesync**. In questa sezione è possibile bloccare l'accesso alla posta elettronica alle applicazioni di posta elettronica basate su Exchange Active Sync.

![Applicazioni](./media/active-directory-conditional-access-policy-connected-applications/09.png "Applicazioni")  
 
![Applicazioni](./media/active-directory-conditional-access-policy-connected-applications/10.png "Applicazioni")  

 
## Argomenti aggiuntivi

- [Accesso condizionale di Azure Active Directory](active-directory-conditional-access.md)

<!---HONumber=AcomDC_0907_2016-->