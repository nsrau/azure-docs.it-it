<properties
	pageTitle="Anteprima di Servizi di dominio Active Directory di Azure: Guida all'amministrazione | Microsoft Azure"
	description="Aggiungere una macchina virtuale Windows Server ai Servizi di dominio Azure AD"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/31/2016"
	ms.author="maheshu"/>  

# Aggiungere una macchina virtuale Windows Server a un dominio gestito

> [AZURE.SELECTOR]
- [Portale di Azure classico - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
- [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)

<br>  

Questo articolo illustra come aggiungere una macchina virtuale che esegue Windows Server 2012 R2 a un dominio gestito di Servizi di dominio Azure AD, usando il portale di Azure classico.


## Passaggio 1: Creare la macchina virtuale Windows Server
Seguire le istruzioni disponibili nell'esercitazione [Creare una macchina virtuale che esegue Windows nel portale di Azure classico](../virtual-machines/virtual-machines-windows-classic-tutorial.md). È importante assicurare che la macchina virtuale appena creata venga aggiunta alla stessa rete virtuale in cui sono stati abilitati i Servizi di dominio Azure AD. L'opzione 'Creazione rapida' non consente di aggiungere la macchina virtuale a una rete virtuale. Sarà quindi necessario usare l'opzione 'Da raccolta' per creare la macchina virtuale.

Seguire questa procedura per creare una macchina virtuale Windows aggiunta alla rete virtuale in cui sono stati abilitati i Servizi di dominio Azure AD.

1. Fare clic su **Nuovo** sulla barra dei comandi nella parte inferiore della finestra del portale di Azure classico.

2. In **Calcolo** fare clic su **Macchina virtuale**, quindi su **Da raccolta**.

3. L'opzione **Scegli un'immagine** della prima schermata consente di scegliere dall'elenco un'immagine per la macchina virtuale. Scegliere l'immagine appropriata.

    ![Selezionare l'immagine](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)

4. Nella seconda schermata è possibile scegliere un nome computer, una dimensione e il nome e la password dell'amministratore. Scegliere il livello e la dimensione necessari per eseguire l'app o il carico di lavoro. Il nome utente selezionato qui corrisponde a un utente amministratore locale del computer. Non immettere in questo campo le credenziali di un account utente di dominio.

    ![Configurare la macchina virtuale](./media/active-directory-domain-services-admin-guide/create-windows-vm-config.png)  

5. Nella terza schermata è possibile configurare le risorse per le connessioni di rete, l'archiviazione e la disponibilità. Assicurarsi di selezionare la rete virtuale in cui sono stati abilitati i Servizi di dominio Azure AD dall'elenco a discesa **Area/Gruppo di affinità/Rete virtuale**. Specificare un **Nome DNS del servizio cloud** appropriato per la macchina virtuale.

    ![Selezionare una rete virtuale per la macchina virtuale](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [AZURE.WARNING]
    Assicurarsi di aggiungere la macchina virtuale alla stessa rete virtuale in cui sono stati abilitati i Servizi di dominio Azure AD. La macchina virtuale potrà così "vedere" il dominio ed eseguire attività quali l'aggiunta al dominio. Se si sceglie di creare la macchina virtuale in una rete virtuale diversa, assicurarsi che la rete virtuale sia connessa alla rete virtuale in cui sono stati abilitati i Servizi di dominio Azure AD.

6. La quarta schermata consente di installare l'agente di macchine virtuali e alcune delle estensioni disponibili.

    ![Operazione completata](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)

7. Dopo la creazione della macchina virtuale, nel portale classico la nuova macchina virtuale viene elencata nel nodo **Macchine virtuali**. La macchina virtuale e il servizio cloud vengono avviati automaticamente e viene indicato lo stato **In esecuzione**.

    ![Macchina virtuale attiva e in esecuzione](./media/active-directory-domain-services-admin-guide/create-windows-vm-running.png)


## Passaggio 2: Connettersi alla macchina virtuale Windows Server usando l'account amministratore locale
È ora possibile connettersi alla macchina virtuale Windows Server appena creata per aggiungerla al dominio. Usare le credenziali di amministratore locale specificate durante la creazione della macchina virtuale, in modo da stabilire la connessione.

Seguire questa procedura per connettersi alla macchina virtuale.

1. Passare al nodo **Macchine virtuali** nel portale classico. Selezionare la macchina virtuale creata nel passaggio 1 e fare clic su **Connetti** sulla barra dei comandi nella parte inferiore della finestra.

    ![Connettersi alla macchina virtuale Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)  

2. Il portale classico richiederà di aprire o salvare un file con estensione rdp, usato per connettersi alla macchina virtuale. Dopo aver terminato il download, fare clic sul file per aprirlo.

3. Al prompt di accesso immettere le **credenziali di amministratore locale** specificate durante la creazione della macchina virtuale, come 'localhost\\mahesh' in questo esempio.

A questo punto si dovrebbe essere connessi alla macchina virtuale Windows appena creata mediante le credenziali di amministratore locale. Il passaggio successivo consiste nell'aggiungere la macchina virtuale al dominio.


## Passaggio 3: Aggiungere la macchina virtuale Windows Server al dominio gestito dai Servizi di dominio Azure AD
Seguire questa procedura per aggiungere la macchina virtuale Windows Server al dominio gestito da Servizi di dominio Azure AD.

1. Connettersi a Windows Server come illustrato nel passaggio 2 precedente. Dalla schermata Start aprire **Server Manager**.

2. Fare clic su **Server locale** nel riquadro sinistro della finestra di Server Manager.

    ![Avviare Server Manager nella macchina virtuale](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)  

3. Fare clic su **GRUPPO DI LAVORO** nella sezione **PROPRIETÀ**. Verrà aperta la pagina **Proprietà del sistema**. Per l'aggiunta al dominio, fare clic su **Cambia**.

    ![Pagina Proprietà di sistema](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)

4. Specificare il nome di dominio del dominio gestito dai Servizi di dominio Azure AD nella casella di testo **Dominio** e fare clic su **OK**.

    ![Specificare il dominio per l'aggiunta](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)  

5. Verrà richiesta l'immissione delle credenziali per l'aggiunta al dominio. Assicurarsi di **specificare le credenziali per un utente appartenente al gruppo di amministratori dei controller di dominio di Azure AD**. Solo i membri di questo gruppo hanno i privilegi necessari per aggiungete computer al dominio gestito.

    ![Specificare le credenziali per l'aggiunta a un dominio](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)  

6. È possibile specificare le credenziali in uno dei modi seguenti:

    - Formato UPN: suffisso UPN per l'account utente, come configurato in Azure AD. In questo esempio il suffisso UPN dell'utente 'bob' è 'bob@domainservicespreview.onmicrosoft.com'.

    - Formato SAMAccountName: è possibile specificare il nome account con il formato SAMAccountName. In questo esempio l'utente 'bob' deve immettere 'CONTOSO100\\bob'. Si noti che se più utenti hanno lo stesso prefisso UPN, ad esempio 'bob', nel tenant di Azure AD, si verificheranno problemi durante l'accesso al dominio con il formato SAMAccountName. In questi casi è possibile usare in modo affidabile il formato UPN per accedere al dominio.

7. Dopo l'aggiunta al dominio verrà visualizzato il messaggio di benvenuto seguente. Riavviare la macchina virtuale per completare l'operazione di aggiunta al dominio.

    ![Messaggio di benvenuto al dominio](./media/active-directory-domain-services-admin-guide/join-domain-done.png)  


## Risoluzione dei problemi di aggiunta al dominio
### Problemi di connettività
Se la macchina virtuale non riesce a trovare il dominio, vedere le procedure seguenti per la risoluzione dei problemi:

- Assicurarsi che la macchina virtuale sia connessa alla stessa rete virtuale in cui sono stati abilitati i Servizi di dominio. In caso contrario, la macchina virtuale non riuscirà a connettersi al dominio e quindi non potrà essere aggiunta al dominio.

- Se la macchina virtuale è connessa a un'altra rete virtuale, assicurarsi che tale rete virtuale sia connessa alla rete virtuale in cui sono stati abilitati i Servizi di dominio.

- Provare a eseguire il ping del dominio usando il nome del dominio gestito, ad esempio 'ping contoso100.com'. Se non è possibile eseguire questa operazione, provare a eseguire il ping degli indirizzi IP per il dominio visualizzati nella pagina in cui sono stati abilitati i Servizi di dominio Azure AD, ad esempio 'ping 10.0.0.4'. Se si riesce a effettuare il ping dell'indirizzo IP ma non del dominio, è possibile che la configurazione del server DNS non sia valida. È possibile che gli indirizzi IP del dominio non siano stati configurati come server DNS per la rete virtuale.

- Provare a scaricare la cache del resolver DNS sulla macchina virtuale ('ipconfig /flushdns').

Se viene visualizzata la finestra di dialogo che richiede le credenziali per l'aggiunta al dominio, non sono presenti problemi di connettività.


### Problemi correlati alle credenziali
Se si verificano problemi con le credenziali e non è possibile completare l'aggiunta al dominio, seguire questa procedura.

- Assicurarsi di usare le credenziali di un account utente appartenente al gruppo di amministratori del controller di dominio di Azure AD. Gli utenti che non appartengono a questo gruppo non possono aggiungere computer al dominio gestito.

- Assicurarsi di avere [abilitato la sincronizzazione delle password](active-directory-ds-getting-started-password-sync.md) secondo i passaggi descritti nella Guida introduttiva.

- Assicurarsi di usare per l'accesso il valore UPN dell'utente come configurato in Azure AD, ad esempio 'bob@domainservicespreview.onmicrosoft.com'.

- Assicurarsi di avere atteso per il tempo necessario per consentire il completamento della sincronizzazione delle password, come specificato nella Guida introduttiva.


## Contenuti correlati

- [Servizi di dominio Azure AD: introduzione](./active-directory-ds-getting-started.md)

- [Amministrare un dominio gestito di Servizi di dominio Azure AD](./active-directory-ds-admin-guide-administer-domain.md)

<!---HONumber=AcomDC_0907_2016-->