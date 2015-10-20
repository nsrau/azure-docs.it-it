<properties
	pageTitle="Aggiungere il proprio nome di dominio a Azure AD| Microsoft Azure"
	description="Argomento che illustra come aggiungere un nome di dominio personalizzato ad Azure AD e fornisce informazioni correlate."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/14/2015"
	ms.author="curtand"/>

# Aggiungere un nome di dominio personalizzato ad Azure AD

Quando si esegue l'iscrizione per un servizio cloud Microsoft, viene emesso un nome di dominio nel formato seguente: contoso.onmicrosoft.com. È possibile continuare a usare tale nome di dominio iniziale oppure è possibile aggiungere il proprio nome di dominio personalizzato al servizio cloud. Questo argomento illustra come aggiungere un nome di dominio personalizzato e fornisce informazioni correlate.

Per gli utenti di Office 365 possono risultare utili questi argomenti correlati:

- [Informazioni di base sul DNS](https://support.office.com/article/854b6b2b-0255-4089-8019-b765cff70377)
- [Trovare il registrar o il provider di hosting DNS](https://support.office.com/article/Find-your-domain-registrar-or-DNS-hosting-provider-b5b633ba-1e56-4a98-8ff5-2acaac63a5c8/)
- [Lavorare con nomi di dominio in Office 365](https://support.office.com/article/Work-with-domain-names-in-Office-365-4f1bd681-337a-4bd3-a7b7-cf77b18d0870/)

## Informazioni sul dominio onmicrosoft.com

È possibile usare il dominio onmicrosoft.com con altri servizi. Ad esempio, è possibile usare il dominio con Exchange Online e Lync Online per creare liste di distribuzione e account di accesso, in modo che gli utenti possano accedere a SharePoint Online e alle raccolte siti.

Se si aggiungono nomi di dominio personalizzati alla directory, è possibile continuare a usare il dominio onmicrosoft.com.

Dopo aver scelto il nome da usare con il servizio cloud al momento dell'iscrizione, ad esempio contoso.onmicrosoft.com, non è possibile modificarlo.

## Come è possibile aggiungere un dominio personalizzato?

Se l'organizzazione dispone già di un nome di dominio personalizzato, l'amministratore può aggiungerlo alla directory di Azure AD in modo che venga usato con tutti i Microsoft Online Services sottoscritti. Dopo aver aggiunto il nome di dominio ad Azure AD, è possibile iniziare ad associarlo ai diversi servizi cloud.

È possibile aggiungere fino a 900 nomi di dominio al tenant di Azure AD usando uno degli strumenti seguenti:

- Portale di gestione di Azure, portale di Office 365 o portale di Microsoft Intune.
- Modulo di Azure Active Directory per Windows PowerShell. Per altre informazioni sui cmdlet che è possibile usare a questo scopo, vedere [Gestire i domini in Azure AD](https://msdn.microsoft.com/library/azure/dn919677.aspx).

È necessario avere già registrato un nome di dominio e disporre delle credenziali di accesso necessarie per il registrar (ad esempio, GoDaddy o Register.com).

È possibile aggiungere più domini alla directory. Tuttavia, non è possibile aggiungere lo stesso dominio a directory diverse. Pertanto, se, ad esempio, si aggiunge il dominio personalizzato alla directory, non è possibile creare un'altra directory di Azure AD e aggiungervi lo stesso nome di dominio.

Se si prevede di usare la funzionalità Single Sign-On con il servizio cloud, è consigliabile preparare l'ambiente Active Directory tramite l'esecuzione di Microsoft Deployment Readiness Tool. Questo strumento analizza l'ambiente Active Directory e fornisce un report che indica se l'ambiente è pronto per la configurazione di Single Sign-On. In caso negativo, il report indica le modifiche necessarie per la preparazione per Single Sign-On. Lo strumento analizza, ad esempio, se gli utenti dispongono di nomi UPN e se il formato di tali nomi UPN è corretto. Per scaricare lo strumento, vedere [Microsoft Deployment Readiness Tool](http://go.microsoft.com/fwlink/?linkid=235650).

> [AZURE.NOTE]Se si usa Office 365, dopo avere configurato il dominio, è possibile iniziare a creare indirizzi di posta elettronica, account di Lync Online e gruppi di distribuzione che usano il nome di dominio personalizzato. È anche possibile usare il nome di dominio per un sito Web pubblico ospitato in SharePoint Online.

- [Aggiungere e verificare un dominio tramite il portale di gestione di Azure](#add-and-verify-a-domain-using-the-azure-management-portal)
- [Modificare i record DNS per i servizi cloud](#edit-dns-records-for-your-cloud-services)
- [Verificare un dominio presso qualsiasi registrar](#verify-a-domain-at-any-domain-name-registrar)

### Aggiungere e verificare un dominio tramite il portale di gestione di Azure

1. Nel portale fare clic su **Active Directory** e quindi sul nome della directory dell'organizzazione. È possibile eseguire una delle procedure seguenti:
    1. Nella pagina della directory predefinita fare clic su **Aggiungi dominio** nella sezione **Migliorare l'esperienza di accesso degli utenti**.
2. Fare clic su **Domini** e quindi sul pulsante **Aggiungi dominio personalizzato** o **Aggiungi**.
2. Nella pagina **Aggiungi dominio** digitare il nome di dominio che si vuole aggiungere ed eseguire una delle operazioni seguenti:
    1. Se non si prevede di integrare Active Directory locale con Azure AD, eseguire le operazioni seguenti:
        1. Lasciare deselezionata la casella di controllo **Desidero configurare questo dominio per l'accesso Single Sign-On con il mio servizio Active Directory locale** e fare clic sul pulsante **Aggiungi**.
        2. Dopo che viene visualizzato il messaggio che indica che il dominio è stato aggiunto correttamente ad Azure AD, fare clic sulla freccia per passare alla pagina successiva e verificare il dominio.
        3. Seguire le istruzioni nella pagina successiva per verificare di essere proprietari del nome di dominio aggiunto nei passaggi precedenti. Per istruzioni dettagliate, vedere Verificare un dominio presso qualsiasi registrar.
    2. Se si vuole integrare Active Directory locale con Azure AD, eseguire le operazioni seguenti:
        1. Selezionare la casella di controllo **Desidero configurare questo dominio per l'accesso Single Sign-On con il mio servizio Active Directory locale** e quindi fare clic sul pulsante **Aggiungi**.
        2. Dopo che viene visualizzato il messaggio che indica che il dominio è stato aggiunto correttamente ad Azure AD, fare clic sulla freccia per passare alla pagina successiva e quindi seguire le istruzioni nella pagina per configurare il dominio aggiunto per la funzionalità Single Sign-On.

> [AZURE.NOTE]Dopo aver aggiunto il nome di dominio ad Azure AD, è possibile modificare il nome di dominio predefinito per i nuovi indirizzi di posta elettronica. Per altre informazioni, vedere [Come è possibile modificare il nome di dominio principale per gli utenti?](#how-can-i-change-the-primary-domain-name-for-users?) È anche possibile modificare il profilo per un account utente esistente per aggiornare l'indirizzo di posta elettronica (che è anche l'ID utente) in modo da usare il nome di dominio personalizzato anziché il dominio onmicrosoft.com.

### Modificare i record DNS per i servizi cloud

> [AZURE.NOTE]Se si usa Microsoft Intune, non è necessario modificare i record DNS per il servizio cloud Microsoft Intune.

Dopo aver aggiunto e verificato il nome di dominio personalizzato, il passaggio successivo consiste nel modificare, presso il registrar o il provider di hosting DNS, i record DNS che indirizzano il traffico al servizio cloud. Azure AD fornisce le informazioni DNS necessarie.

Se è stata appena completata la procedura guidata **Aggiungi un dominio**, fare clic su **Configura record DNS**. In caso contrario, attenersi alla procedura seguente.

1. Nel riquadro sinistro del portale fare clic su **Domini**.
2. A seconda del portale in uso, fare clic sul nome di dominio che si vuole configurare e quindi su **Impostazioni DNS** o su **Visualizza impostazioni DNS**. La pagina **Impostazioni DNS** elenca i record DNS per il servizio cloud.

    Se si vuole configurare un servizio non visualizzato nella scheda Impostazioni DNS, verificare i servizi di dominio selezionati per assicurarsi di aver scelto tale servizio per il nome di dominio. Per modificare le impostazioni, ad esempio per aggiungere Lync Online, vedere Specificare i servizi da usare con il dominio.

3. Nel sito Web del registrar aggiungere i record necessari nel file DNS.

Affinché le modifiche diventino effettive, sono necessari in genere 15 minuti. Per la propagazione del record DNS creato nel sistema DNS possono invece essere necessarie fino a 72 ore. Se è necessario visualizzare di nuovo queste impostazioni dei record, nella pagina **Domini** fare clic sul dominio e quindi, nella pagina **Proprietà dominio**, fare clic sulla scheda **Impostazioni DNS**.

Per verificare lo stato del dominio, nella pagina **Domini**, fare clic sul dominio e poi scegliere la pagina **Proprietà del dominio**, fare clic su **Risolvi dei problemi di dominio**.

### Verificare un dominio presso qualsiasi registrar

Se si dispone già di un dominio registrato con un registrar e lo si vuole configurare per il funzionamento con Azure AD, è necessaria la verifica del dominio per confermare di essere il proprietario del dominio. Per verificare il dominio, è necessario creare un record DNS presso il registrar, o nella posizione in cui è ospitato il sistema DNS, e quindi Azure AD usa tale record per confermare la proprietà del dominio.

Per poter verificare il dominio, è prima necessario aggiungere un dominio personalizzato in Azure AD. Quando un dominio personalizzato è stato aggiunto, ma non è stato ancora verificato, lo stato sarà impostato su **Fare clic per verificare il dominio** o su **Non verificato**.

#### Raccogliere informazioni sul dominio

In base al portale in uso per amministrare la directory di Azure AD, sarà necessario raccogliere alcune informazioni sul dominio in modo che in un secondo momento sia possibile creare un record DNS che verrà usato durante il processo di verifica.

Se si usa Microsoft Intune o il portale degli account di Azure:

1. Nell'elenco di nomi di dominio nella pagina **Domini** trovare il dominio che si sta verificando. Nella colonna **Stato** fare clic su **Fare clic per verificare il dominio**.
2. Nell'elenco a discesa **Vedere le istruzioni per eseguire questo passaggio con** della pagina **Verifica dominio** scegliere il provider di hosting DNS. Se il provider non viene visualizzato nell'elenco, scegliere **Istruzioni generali**.
3. Nell'elenco a discesa **Selezionare un metodo di verifica** scegliere **Aggiungi un record TXT (metodo consigliato)** o **Aggiungi un record MX (metodo alternativo)**.

    Se il provider di hosting DNS consente di creare record TXT, è consigliabile usare un record TXT per la verifica. Perché? I record TXT sono semplici da creare e non rischiano di interferire con il recapito tramite posta elettronica se viene immesso accidentalmente un valore non corretto.

4. Dalla tabella, copiare o registrare le informazioni in **Indirizzo di destinazione o puntamento**.

Se si usa il portale di gestione:

1. Nel portale fare clic su **Active Directory**, quindi sul nome della directory e infine su **Domini**.
2. Nell'elenco di nomi di dominio nella pagina **Domini** fare clic sul dominio che si vuole verificare e quindi su **Verifica**.
2. Nell'elenco a discesa **Tipo di record** della pagina **Verifica** scegliere **Record TXT** o **Record MX**.
3. Copiare o registrare le informazioni.

#### Aggiungere un record DNS presso il registrar

Azure AD usa un record DNS creato presso il registrar per confermare la proprietà del dominio. Fare riferimento alle istruzioni seguenti per creare un tipo di record TXT o MX per un dominio registrato presso il registrar.

Se il registrar non accetta "@" come nome host, contattare il registrar per capire come rappresentare la "zona padre della zona corrente".

Per Office 365 sono disponibili [istruzioni specifiche per i registrar comuni](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

Per istruzioni generali, attenersi alla procedura seguente per l'aggiunta di un record TXT o MX:

1. Accedere al sito Web del registrar e quindi selezionare il dominio da verificare.
2. Nell'area di gestione DNS per l'account selezionare l'opzione per aggiungere un record TXT o MX per il dominio.
3. Nella casella **TXT** o **MX** per il dominio digitare quanto segue: @
4. Nella casella **Nome di dominio completo (FQDN)** o **Punta a** digitare o incollare le informazioni relative a **Indirizzo di destinazione o puntamento** registrate nel passaggio precedente.
5. Per un record TXT, vengono chieste le informazioni relative a **TTL**. Digitare **1** per impostare la durata (TTL) su un'ora.

    Per un record MX, viene richiesta una priorità (o preferenza). Digitare un numero maggiore rispetto a quello specificato per i record MX esistenti. In questo modo, il nuovo record MX non interferisce con l'inoltro della posta per il dominio. Anziché una priorità, potrebbero venire visualizzate le opzioni seguenti: **Bassa**, **Media**, **Alta**. In questo caso, scegliere **Bassa**.

6. Salvare le modifiche e quindi disconnettersi dal sito Web del registrar.

Dopo avere creato il record TXT o il record MX ed essersi disconnessi dal sito Web, tornare al servizio cloud per verificare il dominio. Affinché le modifiche diventino effettive, sono necessari in genere 15 minuti. Per la propagazione del record creato nel sistema DNS possono invece essere necessarie fino a 72 ore.

#### Verificare il dominio

Dopo che il record creato per il dominio è stato propagato correttamente nel sistema DNS, eseguire le operazioni seguenti per completare la verifica del dominio con Azure AD.

1. Nel portale fare clic su **Domini**.
2. Nell'elenco **Domini** trovare innanzitutto il dominio che si sta verificando e quindi, in base al portale in uso, fare clic su **Fare clic per verificare il dominio** o su **Verifica**.
3. Seguire le istruzioni fornite per completare il processo di verifica.
    - Se la verifica del dominio ha esito positivo, si riceverà una notifica che indica che il dominio è stato aggiunto all'account.
    - Se la verifica del dominio ha esito negativo, le modifiche apportate al registrar potrebbero richiedere più tempo per la propagazione. Annullare il processo di verifica e provare a eseguire di nuovo la verifica in un secondo momento.

Se sono passate più di 72 ore da quando sono state apportate le modifiche al dominio, accedere al sito Web del registrar e verificare che le informazioni sull'alias siano state immesse correttamente. Se le informazioni immesse non sono corrette, è necessario rimuovere il record DNS errato e crearne uno nuovo con le informazioni corrette usando le procedure descritte in questo argomento.

Dopo aver verificato il dominio, è possibile configurarlo per l'uso con i propri account.

## Come è possibile modificare il nome di dominio principale per gli utenti?

Dopo avere aggiunto il nome di dominio ad Azure AD, è possibile modificare il nome di dominio che deve essere visualizzato come predefinito quando si crea un nuovo account utente. A questo scopo, attenersi alla procedura seguente.

1. Nella pagina del portale, nell'angolo superiore sinistro, fare clic sul nome dell'organizzazione.
2. Fare clic su **Modifica**.
3. Scegliere un nuovo nome di dominio predefinito, ad esempio il nome di dominio personalizzato aggiunto.

## Come è possibile rimuovere un dominio?

Prima di rimuovere un nome di dominio, è consigliabile leggere le informazioni seguenti:

- Il nome di dominio contoso.onmicrosoft.com originale fornito per la directory al momento dell'iscrizione non può essere rimosso.
- Qualsiasi dominio di primo livello a cui sono associati sottodomini non può essere rimosso fino a quando non vengono rimossi i sottodomini. Ad esempio, non è possibile rimuovere adatum.com se è presente corp.adatum.com o un altro sottodominio che usa il nome di dominio di primo livello. Per altre informazioni, vedere questo [articolo del Supporto tecnico](https://support.microsoft.com/kb/2787792/).
- Se è stata attivata la sincronizzazione della directory, all'account è stato aggiunto automaticamente un dominio simile al seguente: contoso.mail.onmicrosoft.com. Questo nome di dominio non può essere rimosso.
- Per poter rimuovere un nome di dominio, è innanzitutto necessario rimuovere il nome di dominio da tutti gli account utente o di posta elettronica associati al dominio. È possibile rimuovere tutti gli account oppure modificare in blocco gli account utente per cambiare le informazioni sul nome di dominio e gli indirizzi di posta elettronica. Per altre informazioni, vedere [Creare o modificare utenti in Azure AD](active-directory-create-users.md).
- Se si ospita un sito di SharePoint Online con un nome di dominio in uso per una raccolta siti di SharePoint Online, è prima necessario eliminare la raccolta siti per poter rimuovere il nome di dominio.

Per rimuovere un nome di dominio:

1. Nel riquadro sinistro della pagina del portale fare clic su **Domini**.
2. Nella pagina **Domini** selezionare il nome di dominio che si vuole rimuovere e quindi fare clic su **Rimuovi dominio**.
3. Nella pagina **Rimuovi dominio** fare clic su **Sì**.

Se non è possibile rimuovere il nome di dominio, lo stato per il nome di dominio verrà visualizzato come Rimozione in sospeso nella pagina Domini. Se si continua a visualizzare questo stato, riprovare a rimuovere il nome di dominio.

## Risoluzione dei problemi dopo la modifica del nome di dominio

### Dopo aver apportato modifiche a un dominio, queste non vengono ancora visualizzate.

A causa della modalità di spostamento degli aggiornamenti nel sistema DNS (Domain Name System), possono essere necessarie fino a 72 ore prima che le modifiche apportate presso un registrar o un provider di hosting si propaghino completamente in Internet e sia possibile iniziare a usare il nome di dominio con i servizi.

Inoltre, le modifiche apportate presso il registrar devono essere corrette. Se si torna indietro per correggere un errore, potrebbero essere necessari diversi giorni affinché l'impostazione aggiornata venga visualizzata nel sito portale di servizi cloud.

Il tempo necessario dipende in parte dall'impostazione della durata (TTL) specificata per il record DNS che si sta sostituendo o aggiornando. Fino alla scadenza della durata (TTL), i server Internet che hanno memorizzato nella cache i dati precedenti non eseguono query sul server dei nomi autorevole per richiedere il nuovo valore.

### È stato aggiunto e verificato un dominio e i record DNS nel sito del registrar sono stati configurati. Perché i nuovi account di posta elettronica non ricevono ancora la posta?

Dopo aver aggiunto o aggiornato i record DNS per il dominio, possono essere necessarie fino a 72 ore affinché le modifiche diventino effettive.

Inoltre, le informazioni relative alle impostazioni devono essere immesse correttamente nel sito del registrar. Verificare le impostazioni e assicurarsi di aver aspettato per un tempo sufficiente per la propagazione dei record DNS modificati nel sistema.

### Non è possibile verificare il nome di dominio. Come è possibile capire qual è il problema?

Un metodo per individuare i problemi consiste nell'usare la procedura guidata di risoluzione dei problemi dei domini. Per avviare la procedura guidata, eseguire le operazioni seguenti: nella pagina di amministrazione del portale di servizi cloud fare clic su **Domini** e quindi fare doppio clic sul nome di dominio che si vuole verificare. In **Risoluzione dei problemi** fare quindi clic su **Risolvi problemi di dominio**.

La procedura guidata di risoluzione dei problemi richiede informazioni sul punto del processo di verifica in cui ci si trova e quindi fornisce indicazioni utili per completare la verifica.

### È stato aggiunto e verificato un dominio, ma il nuovo nome di dominio non funziona per gli indirizzi di posta elettronica degli utenti esistenti.

Se si aggiunge il nome di dominio personalizzato al servizio cloud dopo l'aggiunta degli account utente, potrebbero essere necessari aggiornamenti per poter usare il nuovo nome di dominio. Ad esempio, sarà necessario modificare gli account utente impostando i relativi indirizzi di posta elettronica in modo che usino il dominio personalizzato.

## Passaggi successivi

- [Forum di Azure AD](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD)
- [Stackoverflow](http://stackoverflow.com/questions/tagged/azure)
- [Iscriversi ad Azure come organizzazione](sign-up-organization.md)
- [Gestire i domini in Azure AD](https://msdn.microsoft.com/library/azure/dn919677.aspx)

<!---HONumber=Oct15_HO3-->