<properties
	pageTitle="Aggiungere proprietari e utenti a un lab | Microsoft Azure"
	description="Aggiungere in modo sicuro un utente non incluso nella sottoscrizione in Azure DevTest Labs"
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
	ms.date="08/23/2016"
	ms.author="tarcher"/>

# Aggiungere proprietari e utenti a un lab

> [AZURE.VIDEO how-to-set-security-in-your-devtest-lab]

## Panoramica
L'accesso ai lab di sviluppo e test è controllato dal Controllo di accesso in base al ruolo (RBAC) di Azure. Cercare [Controllo degli accessi in base al ruolo (RBAC)](https://azure.microsoft.com/search/?q=role%20based%20access%20control) nel [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) per altre informazioni.


Per concedere l'accesso al lab, si possono usare due ruoli:

- **Proprietario**: gli utenti assegnati al ruolo **proprietario** a livello di lab hanno accesso completo al lab, incluse le funzioni di gestione e monitoraggio. Il ruolo **Proprietario** assegnato a livello di lab non concede agli utenti le autorizzazioni per accedere alle risorse nella sottoscrizione fuori dall'ambito del lab. Gli utenti assegnati al ruolo **Proprietario** a livello di sottoscrizione di Azure hanno automaticamente diritti di **Proprietario** per tutte le risorse create nella sottoscrizione, inclusi lab e macchine virtuali.

-  **Utente DevTest Labs**: gli utenti assegnati al ruolo **Utente DevTest Labs** possono creare macchine virtuali nel lab specificato e visualizzare tutte le risorse lab, come macchine virtuali, criteri o reti virtuali. Gli utenti possono essere *interni*, ovvero un membro di Azure Active Directory per la sottoscrizione, o *esterni*, ovvero un utente che non è membro di Azure AD, ad esempio un membro di un'organizzazione partner.
	-  Un ruolo **Utente DevTest Labs** deve essere assegnato tramite il riquadro **Aggiungi utenti** del lab.
	-  Gli utenti con il ruolo **Utente DevTest Labs** possono eseguire queste operazioni solo all'interno del lab a cui sono assegnati. Ad esempio, un **Utente DevTest Labs** non può creare una macchina virtuale usando il servizio Macchina virtuale della sottoscrizione. La creazione di una macchina virtuale è consentita solo da account di lab di sviluppo e test.
	- Gli utenti *esterni* sono utenti con un Account Microsoft (MSA).
 
Dopo aver creato una macchina virtuale, all'utente che l'ha creata viene assegnato automaticamente il ruolo **Proprietario** nella macchina virtuale e l'utente può quindi eseguire tutte le azioni disponibili nel lab.

## Aggiungere un proprietario al lab

Dato il modo in cui le autorizzazioni vengono propagate dall'ambito padre all'ambito figlio in Azure, i proprietari di una sottoscrizione di Azure che contiene lab saranno automaticamente proprietari di tali lab nonché di tutte le macchine virtuali e le altre risorse che vengono create dagli utenti del lab e dal servizio DevTest Labs. È possibile aggiungere altri proprietari a un lab tramite il relativo pannello nel [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), ma il loro ambito di amministrazione sarà più limitato rispetto a quello dei proprietari di sottoscrizioni, perché non avranno accesso completo ad alcune delle risorse create nella sottoscrizione dal servizio DevTest Labs.

Per aggiungere un proprietario a una sottoscrizione di Azure in cui sono già stati creati o verranno creati lab, seguire questa procedura:

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Nella riquadro di spostamento a sinistra selezionare **Sottoscrizioni**.

	![Collegamento a Sottoscrizioni](./media/devtest-lab-add-devtest-user/subscriptions.png)
	
1. Selezionare la sottoscrizione che conterrà i lab.

1. Selezionare l’icona **Accesso**.

	![Utenti di accesso](./media/devtest-lab-add-devtest-user/access-users.png)

1. Nel pannello **Utenti** selezionare **Aggiungi**.

	![Add user](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. Nel pannello **Seleziona un ruolo** selezionare **Proprietario**.

1. Nella casella di testo **Utente** inserire l’indirizzo di posta elettronica dell'utente da aggiungere come proprietario. Se l'utente non viene trovato, si riceverà un messaggio di errore che spiega il problema. Se l'utente viene individuato, tale utente verrà elencato sotto la casella di testo **Utente**.

1. Selezionare il nome utente individuato.

1. Scegliere **Seleziona**.

1. Selezionare **OK** per chiudere il pannello **Aggiungi accesso**.

1. Quando si torna al pannello **Utenti**, si noterà che l'utente è stato aggiunto come proprietario. Questa persona sarà ora un proprietario di qualsiasi lab creato in questa sottoscrizione e quindi sarà in grado di eseguire le attività del proprietario.

## Aggiungere un utente di lab di sviluppo e test al lab

Per aggiungere un utente di lab di sviluppo e test al lab, seguire questi passaggi:

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **Esplora**.

1. Selezionare **Lab di sviluppo e test**.

1. Nell'elenco dei lab selezionare il lab desiderato.

1. Selezionare l’icona **Accesso**.

	![Accesso utente](./media/devtest-lab-add-devtest-user/devtest-lab-home-blade.png)

1. Nel pannello **Utenti** selezionare **Aggiungi**.

	![Add user](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. Nel pannello **Seleziona un ruolo** selezionare **utente di lab di sviluppo e test**.

1. Nel pannello **Aggiungi utenti**:

	1. Il pannello **Aggiungi utenti** mostra un elenco di utenti incorporati. Se l'utente desiderato è già nell'elenco, è possibile selezionare semplicemente la riga utente per selezionarlo. Verrà visualizzato un segno di spunta a sinistra dell'utente per indicare che l'utente è stato selezionato. Per selezionare più utenti, tenere premuta la chiave**&lt;Ctrl>** durante la selezione di ogni utente. Per deselezionare un utente, tenere premuto la chiave **&lt;Ctrl>** e selezionare l'utente. Un contatore nella parte inferiore del pannello indica il numero di utenti selezionati.

	1. Se l'utente desiderato non è presente nell'elenco, immettere un account di posta elettronica Microsoft valido nella casella di testo **Utenti**. Se l'indirizzo di posta elettronica è valido, l'utente verrà visualizzato sotto la casella di testo **Utente**.

	1. Dopo aver selezionato gli utenti che si desidera aggiungere al lab, scegliere **Seleziona**.

	1. Selezionare **OK** per chiudere il pannello **Aggiungi accesso**.

1. Il pannello **Utenti** mostra ruoli e utenti aggiunti.

<!---HONumber=AcomDC_0824_2016-->