<properties
	pageTitle="Introduzione ad Azure Active Directory Premium"
	description="Questo argomento descrive come effettuare l'iscrizione all'edizione Premium di Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo" 
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/18/2016"
	ms.author="markvi"/>

# Introduzione ad Azure Active Directory Premium

Azure Active Directory è disponibile in tre edizioni: gratuita, Basic e Premium. L'edizione gratuita è inclusa in una sottoscrizione di Azure o di Office 365. Le edizioni Basic e Premium sono disponibili tramite [Microsoft Enterprise Agreement](https://www.microsoft.com/it-IT/licensing/licensing-programs/enterprise.aspx) o il programma [Open Volume License](https://www.microsoft.com/it-IT/licensing/licensing-programs/open-license.aspx). I sottoscrittori di Azure e Office 365 possono acquistare Active Directory Premium anche online. Per acquistare Active Directory Premium [accedere qui](https://portal.office.com/Commerce/Catalog.aspx).

> [AZURE.NOTE]
Le edizioni Premium e Basic di Azure Active Directory sono disponibili per i clienti in Cina tramite l'istanza globale di Azure Active Directory. Azure Active Directory Premium e le edizioni Basic non sono attualmente supportati nel servizio di Microsoft Azure gestito da 21Vianet in Cina. Per altre informazioni, è possibile contattare Microsoft attraverso il [forum di Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

Azure Active Directory Premium è incluso anche in **Enterprise Mobility Suite**, Enterprise Mobility Suite è un modo conveniente che permette alle organizzazioni di usare in un unico piano di licenze tutti i servizi seguenti:

- Active Directory Premium 
- Azure Rights Management
- Microsoft Intune


Per altre informazioni, visitare il sito Web di [Enterprise Mobility Suite](https://www.microsoft.com/it-IT/server-cloud/enterprise-mobility/overview.aspx).

Per iniziare subito a usare le funzionalità di Azure Active Directory Premium, seguire la procedura descritta nelle sezioni seguenti. La stessa procedura è applicabile anche all'edizione Basic di Azure Active Directory.

## Passaggio 1: Iscriversi ad Active Directory Premium

Per iscriversi, visitare il sito Web relativo ai [contratti multilicenza](http://www.microsoft.com/it-IT/licensing/how-to-buy/how-to-buy.aspx).

## Passaggio 2: Attivare un piano di licenze

Al termine dell'acquisto del primo piano di licenze tramite il programma multilicenza Enterprise di Microsoft si riceve un messaggio di posta elettronica di conferma, che è necessario per attivare il primo piano di licenze. Per ogni acquisto successivo relativo a questa directory, le licenze vengono attivate automaticamente nella stessa directory.

Per avviare l'attivazione, fare clic su **Sign In** o **Sign Up**.


![][1]

Se è già disponibile un tenant, fare clic su **Sign In** per accedere con l'account amministratore esistente. È importante eseguire l'accesso con le credenziali di amministratore globale dalla directory in cui devono essere attivate le licenze.

Se si vuole creare un nuovo tenant di Azure Active Directory da usare con il piano di licenze, fare clic su **Sign Up** per aprire la finestra di dialogo **Create Account Profile**.

![][2]

Al termine, viene visualizzata la finestra di dialogo seguente a conferma dell'attivazione del piano di licenze per il tenant.

![][3]

## Passaggio 3: Attivare l'accesso ad Azure Active Directory

Quando viene effettuato il provisioning delle licenze nella directory, viene inviato all'utente un **messaggio di posta elettronica di benvenuto**. Il messaggio conferma che è possibile iniziare a gestire le licenze di Azure Active Directory Premium o Enterprise Mobility Suite e le relative funzionalità.

Se si è usato Microsoft Azure in precedenza, è possibile passare a [http://manage.windowsazure.com](http://manage.windowsazure.com) per assegnare le nuove licenze. Per altre informazioni, vedere il [passaggio 4](#step-4-assign-license-to-user-accounts).

Se non si ha familiarità con Microsoft Azure, è possibile fare clic su **Sign In** nel messaggio di posta elettronica o visitare la [pagina di attivazione dell'accesso ad Azure Active Directory](https://account.windowsazure.com/signup?offer=MS-AZR-0110P). Con entrambi i metodi è necessario eseguire una serie di passaggi che consentono di accedere alla directory tramite il portale di Azure classico.

![][4]

Dopo aver eseguito l'accesso, è necessario completare una seconda schermata di autenticazione specificando e confermando un numero di cellulare. Al termine della verifica del dispositivo mobile, è possibile attivare l'accesso ad Azure Active Directory facendo clic su **Iscriviti**.

![][5]

L'attivazione potrebbe richiedere alcuni minuti. Quando l'accesso viene attivato, la barra marrone scompare ed è possibile fare clic su **Portale**.

![][6]

In questo caso, l'accesso ad Azure è limitato ad Azure Active Directory.

![][7]

Se invece si riesce ad accedere ad Azure grazie a un'iscrizione precedente, è possibile eseguire l'aggiornamento dell'accesso di Azure Active Directory all'accesso di Azure completo attivando sottoscrizioni Azure aggiuntive. In tal caso il portale di Azure classico offre maggiori funzionalità.

![][8]

Se si prova ad attivare l'accesso ad Azure Active Directory prima di ricevere il messaggio di posta elettronica di benvenuto, potrebbe essere visualizzato il messaggio di errore seguente. Dopo la ricezione del messaggio di posta elettronica, attendere alcuni minuti prima di ripetere il tentativo.

![][9]

Anche i nuovi amministratori inclusi nella sottoscrizione possono attivare l'accesso al portale di Azure classico con questo collegamento.

## Passaggio 4: Assegnare le licenze agli account utente

Prima di iniziare a usare il piano acquistato, è necessario assegnare manualmente le licenze agli account utente dell'organizzazione per consentire loro di usufruire dell'ampia gamma di funzionalità fornite dal livello Premium. Attenersi alla procedura seguente per assegnare le licenze agli utenti e consentire loro di usare le funzionalità di Azure Active Directory Premium.

Per assegnare le licenze agli utenti

1. Accedere al portale di Azure classico come amministratore globale della directory che si vuole personalizzare.
2. Fare clic su **Active Directory**, quindi selezionare la directory in cui si desidera assegnare le licenze.
3. Fare clic sulla scheda **Licenze**, selezionare **Active Directory Premium** o **Enterprise Mobility Suite**, quindi fare clic su **Assegna**.

    ![][10]

4. Nella finestra di dialogo selezionare gli utenti a cui assegnare le licenze, quindi fare clic sull'icona con il segno di spunta per salvare le modifiche.

    ![][11]

## Limitazioni relative alle licenze

Alcuni piani di licenze fanno parte di altri piani di licenze o includono a loro volta altri piani. In genere, non è possibile assegnare a un utente un piano di licenze che gli è già stato assegnato. Se si prevede di assegnare un piano di licenze che ne include un altro, è necessario prima rimuovere il piano secondario.

## Requisiti relativi alle licenze

Quando si assegna una licenza a un utente, è possibile specificare una località di utilizzo primaria nelle proprietà dell'account. Se non viene specificata una località di utilizzo, all'utente verrà assegnata automaticamente la località del tenant.

![][12]

La disponibilità di servizi e funzionalità per un servizio cloud Microsoft varia in base al paese o all'area geografica. È possibile che un servizio, ad esempio VoIP (Voice over Internet Protocol), sia disponibile solo in alcuni paesi o aree geografiche. È inoltre possibile che alcune funzionalità di un servizio siano limitate per motivi legali in determinati paesi o aree geografiche. Per verificare se un servizio o una funzionalità è disponibile con o senza limitazioni, cercare il proprio paese o area geografica nel sito dedicato alle limitazioni relative alle licenze del servizio.

## Passaggi successivi

- [Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso e al pannello di accesso](active-directory-add-company-branding.md)
- [Visualizzare i report di accesso e utilizzo](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-get-started-premium/MOLSEmail.png
[2]: ./media/active-directory-get-started-premium/MOLSAccountProfile.png
[3]: ./media/active-directory-get-started-premium/MOLSThankYou.png
[4]: ./media/active-directory-get-started-premium/AADEmail.png
[5]: ./media/active-directory-get-started-premium/SignUppage.png
[6]: ./media/active-directory-get-started-premium/Subscriptionspage.png
[7]: ./media/active-directory-get-started-premium/Premiuminportal.png
[8]: ./media/active-directory-get-started-premium/Premiuminportal_large.png
[9]: ./media/active-directory-get-started-premium/Signuppage_oops.png
[10]: ./media/active-directory-get-started-premium/contosolicenseplan.png
[11]: ./media/active-directory-get-started-premium/Assignlicensespicker.png
[12]: ./media/active-directory-get-started-premium/Usagelocation.png

<!---HONumber=AcomDC_0420_2016-->