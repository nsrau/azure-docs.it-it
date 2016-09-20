<properties
	pageTitle="Anteprima di Servizi di dominio Azure Active Directory: amministrare DNS nei domini gestiti | Microsoft Azure"
	description="Amministrare DNS nei domini gestiti usando Servizi di dominio Azure Active Directory"
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

# Amministrare DNS in un dominio gestito dai Servizi di dominio Azure AD
I Servizi di dominio Azure Active Directory includono un server DNS (Domain Name System, sistema dei nomi di dominio) che fornisce la risoluzione DNS per il dominio gestito. In alcuni casi potrebbe essere necessario configurare DNS nel dominio gestito. Potrebbe anche essere necessario creare record DNS per i computer non aggiunti al dominio, configurare indirizzi IP virtuali per servizi di bilanciamento del carico oppure impostare server d'inoltro DNS esterni. Per questo motivo, agli utenti che appartengono al gruppo "AAD DC Administrators" vengono concessi privilegi di amministrazione DNS nel dominio gestito.


## Prima di iniziare
Per eseguire le attività elencate in questo articolo sono necessari gli elementi seguenti:

1. Una **sottoscrizione di Azure** valida.

2. Una **directory di Azure AD** sincronizzata con una directory locale o con una directory solo cloud.

3. I **Servizi di dominio Azure AD** devono essere abilitati per la directory di Azure AD. Se non è stato fatto, eseguire tutte le attività descritte nella [guida introduttiva](./active-directory-ds-getting-started.md).

4. Una **macchina virtuale aggiunta al dominio** da cui si amministrerà il dominio gestito di Servizi di dominio Azure AD. Se non si dispone di questo tipo di macchina virtuale, seguire tutte le attività descritte nell'articolo [Aggiungere una macchina virtuale Windows Server a un dominio gestito](./active-directory-ds-admin-guide-join-windows-vm.md).

5. È necessario disporre delle credenziali di un **account utente appartenente al gruppo 'AAD DC Administrators'** nella directory per poter amministrare DNS per il dominio gestito.

<br>  

## Attività 1: Eseguire il provisioning di una macchina virtuale aggiunta a un dominio per amministrare in remoto DNS per il dominio gestito
I domini gestiti di Servizi di dominio Azure AD possono essere gestiti in remoto con i familiari strumenti di amministrazione di Active Directory, ad esempio il Centro di amministrazione di Active Directory o AD PowerShell. Analogamente, il DNS per il dominio gestito può essere amministrato in remoto usando gli strumenti di amministrazione del server DNS.

Gli amministratori nella directory di Azure AD non hanno i privilegi necessari per connettersi ai controller di dominio nel dominio gestito con Desktop remoto. I membri del gruppo 'AAD DC Administrators' possono amministrare DNS per i domini gestiti in remoto usando Strumenti per server DNS da un computer client/Windows Server aggiunto al dominio gestito. La funzionalità Strumenti per server DNS può essere installate come parte della funzionalità facoltativa Strumenti di amministrazione remota del server in Windows Server e nei computer client aggiunti al dominio gestito.

La prima attività consiste nel provisioning di una macchina virtuale Windows Server aggiunta al dominio gestito. Per istruzioni, vedere l'articolo [Aggiungere una macchina virtuale Windows Server a un dominio gestito](active-directory-ds-admin-guide-join-windows-vm.md).


## Attività 2: Installare gli strumenti per server DNS nella macchina virtuale
Eseguire questa procedura per installare gli strumenti di amministrazione DNS nella macchina virtuale aggiunta a un dominio. Per altri dettagli su come [installare e usare gli strumenti di amministrazione remota del server](https://technet.microsoft.com/library/hh831501.aspx), fare riferimento a TechNet.

1. Accedere al nodo **Macchine virtuali** nel portale di Azure classico. Selezionare la macchina virtuale creata nell'attività 1 e fare clic su **Connetti** sulla barra dei comandi nella parte inferiore della finestra.

    ![Connettersi alla macchina virtuale Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)  

2. Il portale classico richiederà di aprire o salvare un file con estensione rdp, usato per connettersi alla macchina virtuale. Dopo aver terminato il download, fare clic sul file con estensione rdp.

3. Al prompt di accesso usare le credenziali di un utente appartenente al gruppo "AAD DC Administrators". In questo caso, ad esempio, usare 'bob@domainservicespreview.onmicrosoft.com'.

4. Dalla schermata Start aprire **Server Manager**. Fare clic su **Aggiungi ruoli e funzionalità** nel riquadro centrale della finestra di Server Manager.

    ![Avviare Server Manager nella macchina virtuale](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)  

5. Nella pagina **Prima di iniziare** di **Aggiunta guidata ruoli e funzionalità**, fare clic su **Avanti**.

    ![Pagina Prima di iniziare](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)

6. Nella pagina **Tipo di installazione** lasciare selezionata l'opzione **Installazione basata su ruoli o basata su funzionalità** e fare clic su **Avanti**.

	![Pagina Tipo di installazione](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)

7. Nella pagina **Selezione dei server** selezionare la macchina virtuale corrente dal pool di server e fare clic su **Avanti**.

	![Pagina Selezione dei server](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)

8. Nella pagina **Ruoli del server**, fare clic su **Avanti**. Questa pagina verrà ignorata perché non si stanno installando ruoli nel server.

9. Nella pagina **Funzionalità** fare clic per espandere il nodo **Strumenti di amministrazione remota del server**, quindi fare clic per espandere il nodo **Strumenti di amministrazione ruoli**. Selezionare la funzionalità **Strumenti per server DNS** dall'elenco di strumenti di amministrazione ruoli, come illustrato di seguito.

	![Pagina Funzionalità](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)  

10. Nella pagina **Conferma** fare clic su **Installa** per installare la funzionalità Strumenti per server DNS nella macchina virtuale. Quando l'installazione della funzionalità viene completata correttamente, fare clic su **Chiudi** per uscire dall'**Aggiunta guidata ruoli e funzionalità**.

	![Pagina di conferma](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)


## Attività 3: Avviare la console di gestione DNS per l'amministrazione di DNS
Dopo aver installato la funzionalità Strumenti per server DNS nella macchina virtuale aggiunta a un dominio sarà possibile usare gli strumenti DNS per amministrare il sistema DNS nel dominio gestito.

> [AZURE.NOTE] È necessario essere un membro del gruppo 'AAD DC Administrators' per poter amministrare DNS nel dominio gestito.

1. Dalla schermata Start fare clic su **Strumenti di amministrazione**. Verrà visualizzata la console **DNS** installata nella macchina virtuale.

	![Strumenti di amministrazione - Console DNS](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)  

2. Fare clic su **DNS** per avviare la console di gestione DNS.

3. Nella finestra di dialogo **Connessione al server DNS** fare clic sull'opzione **Il computer seguente** e immettere il nome di dominio DNS del dominio gestito, ad esempio 'contoso100.com'.

    ![Console DNS - connettersi al dominio](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)  

4. La console DNS si connette al dominio gestito. Verrà visualizzata una vista simile alla seguente.

    ![Console DNS - amministrare il dominio](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

5. È ora possibile usare la console DNS per aggiungere voci DNS per i computer nella rete virtuale in cui è stato abilitato Servizi di dominio AAD.

> [AZURE.WARNING] Prestare estrema attenzione durante l'amministrazione di DNS per il dominio gestito con gli strumenti di amministrazione DNS. Assicurarsi di **non eliminare o modificare i record DNS integrati che vengono usati da Servizi di dominio nel dominio**. I record DNS predefiniti includono record DNS di dominio, record del server dei nomi e altri record usati per l'individuazione dei controller di dominio. Se si modificano questi record, i servizi di dominio verranno interrotti nella rete virtuale.


Per altre informazioni sulla gestione DNS, vedere l'[articolo sugli strumenti DNS in Technet](https://technet.microsoft.com/library/cc753579.aspx).


## Contenuti correlati

- [Servizi di dominio Azure AD: introduzione](./active-directory-ds-getting-started.md)

- [Aggiungere una macchina virtuale Windows Server a un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)

- [Amministrare un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-administer-domain.md)

- [Strumenti di amministrazione DNS](https://technet.microsoft.com/library/cc753579.aspx)

<!---HONumber=AcomDC_0907_2016-->