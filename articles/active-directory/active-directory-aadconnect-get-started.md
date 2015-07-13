<properties 
	pageTitle="Introduzione ad Azure AD Connect" 
	description="Informazioni su come scaricare, installare e configurare la procedura guidata per Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Introduzione ad Azure AD Connect

<div class="dev-center-tutorial-selector sublanding">
<a href="../active-directory-aadconnect/" title="Che cos&apos;è">Che cos'è</a> <a href="../active-directory-aadconnect-how-it-works/" title="Come funziona">Come funziona</a> <a href="../active-directory-aadconnect-get-started/" title="Introduzione" class="current">Introduzione</a> <a href="../active-directory-aadconnect-whats-next/" title="Passaggi successivi">Passaggi successivi</a> <a href="../active-directory-aadconnect-learn-more/" title="Altre informazioni">Altre informazioni</a>
</div>


La documentazione seguente contiene le informazioni necessarie per iniziare a usare Azure Active Directory Connect. In questa documentazione viene illustrato l'uso dell'installazione rapida di Azure AD Connect. Per informazioni su un'installazione personalizzata vedere [Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md). Per informazioni sull'aggiornamento da DirSync ad Azure AD Connect, vedere [Aggiornamento da DirSync ad Azure Active Directory Connect.](active-directory-aadconnect-dirsync-upgrade-get-started.md)

## Scaricare Azure AD Connect



Per iniziare a usare Azure AD Connect, è possibile scaricare la versione più recente dalla pagina di [download di Azure AD Connect ](http://go.microsoft.com/fwlink/?LinkID=615771).

## Prima di installare Azure AD Connect
Prima di installare Azure AD Connect con Impostazioni rapide, sono necessari alcuni elementi.


 
- Sottoscrizione di Azure o [sottoscrizione di una versione di valutazione di Azure](http://azure.microsoft.com/pricing/free-trial/). È necessaria solo per l'accesso al portale di Azure, non per l'uso di Azure AD Connect. Se si usa PowerShell o Office 365 non è necessaria una sottoscrizione di Azure per usare Azure AD Connect.
- Un account amministratore globale di Azure AD per il tenant di Azure AD con cui si vuole eseguire l'integrazione
- Un controller di dominio o server membro di Active Directory con Windows Server 2008 o versioni successive
- Un account amministratore dell'organizzazione per Active Directory locale
- Facoltativo: un account utente di prova per verificare la sincronizzazione. 


Per le opzioni personalizzate, ad esempio più foreste o l'accesso federato, sono disponibili informazioni su requisiti aggiuntivi [qui.](active-directory-aadconnect-get-started-custom.md)


## Installazione rapida di Azure AD Connect
La selezione di Impostazioni rapide rappresenta l'opzione predefinita ed è uno degli scenari più comuni. In questo caso, Azure AD Connect distribuisce la sincronizzazione con l'opzione di sincronizzazione degli hash delle password. Questo scenario è possibile solo per una singola foresta e permette agli utenti di usare la propria password locale per accedere al cloud. Se si usa Impostazioni rapide, viene automaticamente avviata una sincronizzazione al termine dell'installazione (anche se è possibile scegliere di non avviarla). Questa opzione richiede solo pochi clic per estendere la directory locale nel cloud.

<center>![Welcome to Azure AD Connect](./media/active-directory-aadconnect-get-started/welcome.png)</center>

### Per installare Azure AD Connect utilizzando le impostazioni di rapide
--------------------------------------------------------------------------------------------

1. Accedere al server in cui si vuole installare Azure AD Connect come amministratore dell'organizzazione. Deve essere il server da impostare come server di sincronizzazione.
2. Selezionare e fare doppio clic su AzureADConnect.msi.
3. Nella schermata iniziale, selezionare la casella che consente di accettare le condizioni di licenza e fare clic su **Continua**.
4. Nella schermata Impostazioni rapide fare clic su **Usa impostazioni rapide**.
<center>![Welcome to Azure AD Connect](./media/active-directory-aadconnect-get-started/express.png)</center>
6. Nella schermata Connessione ad Azure AD immettere il nome utente e password di un amministratore globale di Azure per Azure AD. Fare clic su **Next**.
8. Nella schermata Connessione a Servizi di dominio di Active Directory immettere il nome utente e la password di un account amministratore dell'organizzazione. Fare clic su **Next**.
<center>![Welcome to Azure AD Connect](./media/active-directory-aadconnect-get-started/install4.png)</center>
9. Nella schermata Pronto per la configurazione fare clic su **Installa**.
	- Nella pagina Pronto per la configurazione lasciare deselezionata l'opzione "**Avviare il processo di sincronizzazione non appena viene completata la configurazione iniziale**". In tal caso, la procedura guidata configurerà la sincronizzazione ma lascerà disabilitata l'attività in modo che non venga eseguita fino a quando non la si abilita manualmente nell'Utilità di pianificazione. Dopo avere abilitato l'attività, la sincronizzazione verrà eseguita ogni tre ore.
	- È anche possibile scegliere di configurare i servizi di sincronizzazione per **Distribuzione ibrida di Exchange** selezionando la casella di controllo corrispondente. Se non si prevede di avere cassette postali di Exchange nel cloud e in locale, non è necessario selezionare questa opzione.

<center>![Welcome to Azure AD Connect](./media/active-directory-aadconnect-get-started/readyinstall.png)</center>
8. Al termine dell'installazione, fare clic su **Esci**.


<br> <br>

Per un video sull'uso dell'Installazione rapida, vedere:

<center>[AZURE.VIDEO azure-active-directory-connect-express-settings]</center>



## Verifica dell'installazione

Dopo avere installato Azure AD Connect, è possibile verificare se la sincronizzazione viene eseguita. A questo scopo accedere al portale di Azure e verificare l'ora dell'ultima sincronizzazione.

1.  Accedere al portale di Azure.
2.  A sinistra, selezionare Active Directory.
3.  Fare doppio clic sulla directory appena usata per configurare Azure AD Connect.
4.  Nella parte superiore, selezionare Integrazione directory. Prendere nota dell'ora dell'ultima sincronizzazione.

<center>![Express Installation](./media/active-directory-aadconnect-get-started/verify.png)</center>

## Operazioni successive
Dopo avere installato Azure AD Connect, è possibile fare clic [qui](active-directory-aadconnect-whats-next.md) per procedere con le attività successive all'installazione, ad esempio assegnare agli utenti le licenze di Azure AD Premium o Enterprise Mobility oppure configurare le opzioni aggiuntive.

 

<!---HONumber=62-->