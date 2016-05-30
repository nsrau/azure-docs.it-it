<properties
	pageTitle="Aggiungere proprietari e utenti a un lab | Microsoft Azure"
	description="Aggiungere in modo sicuro un utente che non è incluso nella sottoscrizione del lab di sviluppo/test di Azure"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/08/2016"
	ms.author="tarcher"/>

# Aggiungere proprietari e utenti a un lab

> [AZURE.NOTE] Fare clic sul collegamento seguente per visualizzare il video associato a questo articolo, relativo alla [procedura di impostazione della sicurezza nei lab di sviluppo/test](/documentation/videos/how-to-set-security-in-your-devtest-lab)

## Panoramica
L'accesso ai lab di sviluppo e test è controllato dal Controllo di accesso in base al ruolo (RBAC) di Azure. Cercare [Controllo degli accessi in base al ruolo (RBAC)](https://azure.microsoft.com/search/?q=role%20based%20access%20control) nel [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) per altre informazioni.

Per concedere l'accesso al lab, si possono usare due ruoli:

- **Proprietario**: gli utenti assegnati al ruolo **proprietario** a livello di lab hanno accesso completo al lab, incluse le funzioni di gestione e monitoraggio. Il ruolo **proprietario** assegnato a livello di lab non concede agli utenti le autorizzazioni per accedere alle risorse nella sottoscrizione fuori dall'ambito del lab. Gli utenti assegnati al ruolo **proprietario** a livello di sottoscrizione di Azure usufruiscono automaticamente dei diritti del ruolo **proprietario** per qualsiasi lab creato nella sottoscrizione.

-  **Utente di lab di sviluppo e test**: gli utenti assegnati al ruolo**utente di lab di sviluppo e test** possono creare, aggiornare ed eliminare le macchine virtuali nel lab specificato. Gli utenti possono essere *interni* (un membro di Azure Active Directory per la sottoscrizione), o *esterni* (un utente che non è un membro di Azure AD, ad esempio un membro di un'organizzazione partner).
	-  Un ruolo **utente di lab di sviluppo e test** deve essere assegnato tramite il riquadro **Aggiungi utenti** del lab.
	-  Gli utenti con il ruolo **utente di lab di sviluppo e test** possono eseguire queste operazioni solo all'interno del lab a cui sono assegnati. Ad esempio, un **utente di lab di sviluppo e test** non può creare una macchina virtuale usando il servizio Macchina virtuale della sottoscrizione. La creazione di una macchina virtuale è consentita solo da account di lab di sviluppo e test.
	- Gli utenti *esterni* devono disporre di un account in uno dei domini di account Microsoft (ad esempio @hotmail.com, @live.com, @msn.com, @passport.com, @outlook.com o una variante di un paese specifico).
 
## Aggiungere un proprietario al lab

I lab di sviluppo e test considerano i proprietari di una sottoscrizione di Azure che contiene lab come proprietari di tali laboratori. Sebbene sia possibile aggiungere altri proprietari a un lab tramite il pannello del lab nel [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), questa operazione non è attualmente supportata.

Per aggiungere un proprietario a una sottoscrizione di Azure in cui si dispone di lab già creati o in cui si creeranno nuovi lab, seguire questi passaggi:

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Nella finestra di navigazione a sinistra, toccare **Sottoscrizioni**.

	![Collegamento a sottoscrizioni](./media/devtest-lab-add-devtest-user/subscriptions.png)
	
1. Toccare la sottoscrizione che conterrà il lab.

1. Toccare l’icona **Accesso**.

	![Utenti di accesso](./media/devtest-lab-add-devtest-user/access-users.png)

1. Nel pannello **Utenti** toccare **Aggiungi**.

	![Add user](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. Nel pannello **Seleziona un ruolo** toccare **Proprietario**.

1. Nella casella di testo **Utente** inserire l’indirizzo di posta elettronica dell'utente da aggiungere come proprietario. Se l'utente non viene trovato, si riceverà un messaggio di errore che spiega il problema. Se l'utente viene individuato, tale utente verrà elencato sotto la casella di testo **Utente**.

1. Toccare il nome utente individuato.

1. Toccare **Seleziona**.

1. Toccare **OK** per chiudere il pannello **Aggiungi accesso**.

1. Quando si torna al pannello **Utenti**, si noterà che l'utente è stato aggiunto come proprietario. Questa persona sarà ora un proprietario di qualsiasi lab creato in questa sottoscrizione e pertanto sarà in grado di eseguire le attività del proprietario.

## Aggiungere un utente di lab di sviluppo e test al lab

Per aggiungere un utente di lab di sviluppo e test al lab, seguire questi passaggi:

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Fare clic su **Esplora**.

1. Toccare **Lab di sviluppo e test**.

1. Dall'elenco dei lab, toccare il lab desiderato.

1. Toccare l’icona **Accesso**.

	![Accesso utente](./media/devtest-lab-add-devtest-user/devtest-lab-home-blade.png)

1. Nel pannello **Utenti** toccare **Aggiungi**.

	![Add user](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. Nel pannello **Seleziona un ruolo** toccare **utente di lab di sviluppo e test**.

1. Nel pannello **Aggiungi utenti**:

	1. Il pannello **Aggiungi utenti** mostra un elenco di utenti incorporati. Se l'utente desiderato è già nell'elenco, è possibile toccare semplicemente la riga utente per selezionarlo. Verrà visualizzato un segno di spunta a sinistra dell'utente per indicare che l'utente è stato selezionato. Per selezionare più utenti, tenere premuta la chiave**&lt;Ctrl>** durante la selezione di ogni utente. Per deselezionare un utente, tenere premuto la chiave **&lt;Ctrl>** e fare clic sull'utente. Un contatore nella parte inferiore del pannello indica il numero di utenti selezionati.

	1. Se l'utente desiderato non è presente nell'elenco, immettere un account di posta elettronica Microsoft valido nella casella di testo **Utenti**. Se l'indirizzo di posta elettronica è valido, l'utente verrà visualizzato sotto la casella di testo **Utente**. È sufficiente toccarlo per selezionarlo.

	1. Dopo aver selezionato gli utenti che si desidera aggiungere al lab, toccare **Seleziona**.

	1. Toccare **OK** per chiudere il pannello **Aggiungi accesso**.

1. Il pannello **Utenti** mostra ruoli e utenti aggiunti.

<!---HONumber=AcomDC_0518_2016-->