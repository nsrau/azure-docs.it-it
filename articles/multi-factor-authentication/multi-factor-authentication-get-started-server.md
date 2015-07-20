<properties 
	pageTitle="Introduzione al server Azure Multi-Factor Authentication" 
	description="Questa è la pagina di Azure Multi-Factor Authentication in cui viene descritto come iniziare a utilizzare Azure MFA." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>

# Introduzione al server Azure Multi-Factor Authentication




<center>![Cloud](./media/multi-factor-authentication-get-started-server/server2.png)</center>

Dopo aver stabilito se è necessario utilizzare la modalità Multi-Factor Authentication locale, sarà possibile procedere. Questa pagina include una nuova installazione del server che include l'impostazione di Active Directory locale. Se il server Phonefactor è già installato e si cerca la procedura per effettuare questa operazione, vedere l'argomento relativo all'aggiornamento al server Azure multi-Factor o se si cercano informazioni semplicemente sull'installazione del servizio Web, vedere l'argomento relativo alla distribuzione del servizio Web app per dispositivi mobili del server Azure Multi-Factor Authentication.



## Scaricare il server Azure Multi-Factor Authentication

Esistono due modi diversi per scaricare il server Azure Multi-Factor Authentication. Il primo consiste nell'effettuare l'accesso al portale di Azure e il secondo modo consiste direttamente da [https://pfweb.phonefactor.net](https://pfweb.phonefactor.net).


### Per scaricare il server Azure Multi-Factor Authentication dal portale Azure
--------------------------------------------------------------------------------

1. Accedre al portale di Azure come amministratore.
2. A sinistra selezionare Active Directory.
3. Nella parte superiore della pagina Active Directory, selezionare **Provider Multi-Factor Authentication**
4. Nella parte inferiore fare clic su **Gestisci**
5. Fare clic su **Download**
6. Sopra all'opzion relativa alla **generazione delle credenziali di attivazione**, fare clic su **Download**
7. Salvare il download.

### Per scaricare il server Azure Multi-Factor Authentication direttamente
--------------------------------------------------------------------------------

1. Accedere a [https://pfweb.phonefactor.net](https://pfweb.phonefactor.net).
2. Fare clic su **Download**
<center>![Cloud](./media/multi-factor-authentication-get-started-server/download2.png)</center>
3. Sopra all'opzione relativa alla **generazione delle credenziali di attivazione**, fare clic su **Download** e lasciare la pagina aperta.
4. Salvare il download.



## Installare e configurare il server Azure Multi-Factor Authentication
Dopo averlo scaricato, è possibile installare e configurare il server. Assicurarsi che il server su cui viene installato soddisfi i requisiti seguenti:



Requisiti del server Azure Multi-Factor Authentication|Descrizione|
:------------- | :------------- | 
Hardware|<li>200 MB di spazio su disco rigido</li><li>processore in grado di supportare istruzioni x32 o x64</li><li>1 GB o più di RAM</li>
Software|<li>Windows Server 2003 o versione successiva, se l'host è un sistema operativo server</li><li>Windows Vista o versione successiva, se l'host è un sistema operativo client</li><li>Microsoft .NET 2.0 Framework</li><li>IIS 6.0 o versione successiva per l'installazione del portale utenti o dell'SDK servizio Web</li>

### Requisiti del firewall del server Azure Multi-Factor Authentication
--------------------------------------------------------------------------------
Ogni server Multi-Factor Authentication Server deve essere in grado di comunicare sulla porta 443 in uscita in modo seguente:

- https://pfd.phonefactor.net
- https://pfd2.phonefactor.net
- https://css.phonefactor.net

Se i firewall in uscita sono limitati sulla porta 443, sarà necessario aprire i seguenti intervalli di indirizzi IP:

Subnet IP|Netmask|Intervallo IP
:------------- | :------------- | :------------- |
134.170.116.0/25|255.255.255.128|134.170.116.1 – 134.170.116.126
134.170.165.0/25|255.255.255.128|134.170.165.1 – 134.170.165.126
70.37.154.128/25|255.255.255.128|70.37.154.129 – 70.37.154.254

Se non si utilizza la funzionalità di conferma dell'evento Azure Multi-Factor Authentication e se gli utenti non eseguono l'autenticazione con le app per dispositivi mobili Multi-Factor Authentication da dispositivi nella rete aziendale, gli intervalli di indirizzi IP possono essere ridotti a quanto segue:


Subnet IP|Netmask|Intervallo IP
:------------- | :------------- | :------------- |
134.170.116.72/29|255.255.255.248|134.170.116.72 – 134.170.116.79
134.170.165.72/29|255.255.255.248|134.170.165.72 – 134.170.165.79
70.37.154.200/29|255.255.255.248|70.37.154.201 – 70.37.154.206


### Per installare e configurare il server Azure Multi-Factor Authentication
--------------------------------------------------------------------------------


1. Fare doppio clic sul file eseguibile. Viene avviata l'installazione.
2. Nella schermata di selezione della cartella di installazione, assicurarsi che la cartella sia corretta e fare clic su Avanti.
3. Al termine dell'installazione, fare clic su Fine. Verrà avviata la configurazione guidata.
4. Nella schermata iniziale della configurazione guidata, inserire un segno di spunta sull'opzione per **non utilizzare la configurazione guidata di autentiazione** e fare clic su **Avanti**. Verrà chiusa la procedura guidata e avviato il server.
<center>![Cloud](./media/multi-factor-authentication-get-started-server/skip2.png)</center>

5. Tornare nella pagina da cui è stato scaricato il server, scegliere il pulsante relativo alla **generazione delle credenziali di attivazione**. Copiare queste informazioni nel server Azure MFA nelle apposite caselle e fare clic su **Attiva**.


I passaggi sopra riportati mostrano un'installazione rapida con la configurazione guidata. È possibile eseguire nuovamente la procedura guidata di autenticazione selezionandolo dal menu Strumenti sul server.



##Importare gli utenti da Active Directory

Ora che il server è stato installato e configurato, è possibile importare rapidamente gli utenti nel server Azure MFA.

### Per importare gli utenti da Active Directory
--------------------------------------------------------------------------------


1. Nel server Azure MFA, a sinistra, selezionare **Utenti**.
2. Nella parte inferiore, selezionare **Importa da Active Directory**.
3. A questo punto è possibile eseguire la ricerca di singoli utenti o effettuare una ricerca delle unità organizzative con utenti all'interno di Active Directory. In questo caso, specifichiamo l'Unità organizzativa utenti.
4. Selezionare tutti gli utenti a destra e fare clic per avviare l'**importazione**. Verrà visualizzata una finestra popup che informa che tutte le operazioni sono state eseguite correttamente. Chiudere la finestra di importazione.

<center>![Cloud](./media/multi-factor-authentication-get-started-server/import2.png)</center>

<!---HONumber=July15_HO2-->