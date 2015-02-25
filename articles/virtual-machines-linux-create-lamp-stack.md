<properties pageTitle="Come creare uno stack LAMP con Microsoft Azure" description="Informazioni su come creare uno stack LAMP con Microsoft Azure usando macchine virtuali (VM) di Azure che eseguono Linux." services="virtual-machines" documentationCenter="" authors="NingKuang" manager="timlt" editor="tysonn"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="10/27/2014" ms.author="ningk"/>

#Come creare uno stack LAMP con Microsoft Azure 

Uno stack "LAMP" è un gruppo di software open source che viene in genere installato insieme per consentire a un server di ospitare siti Web dinamici e applicazioni Web. Il termine è in realtà un acronimo che rappresenta il sistema operativo Linux con il server web Apache. I dati del sito vengono archiviati in un database MySQL e il contenuto dinamico viene elaborato da PHP.  

In questa guida uno stack LAMP verrà installato in un'immagine Linux e distribuita in Microsoft Azure.  

Si apprenderà:  

-	Come creare una macchina virtuale di Azure.
-	Come preparare la macchina virtuale per lo stack LAMP.
-	Come installare il software necessario al server LAMP nella macchina virtuale.

Si presuppone che l'utente disponga già di una sottoscrizione di Azure.  In caso contrario, è possibile registrarsi per una versione di valutazione gratuita all'indirizzo [http://azure.microsoft.com](http://azure.microsoft.com). Se si ha un abbonamento MSDN, vedere [Offerte speciali di Microsoft Azure: vantaggi per i membri di MSDN, MPN e Bizspark](http://azure.microsoft.com/it-it/pricing/member-offers/msdn-benefits/?c=14-39). Per altre informazioni su Azure, vedere [Cos'è Azure?](http://azure.microsoft.com/it-it/overview/what-is-azure/).

Oltre a questo argomento, il video seguente visualizza presenta informazioni dettagliate sull'uso dello stack LAMP in Microsoft Azure:  

> [AZURE.VIDEO lamp-stack-on-azure-vms-with-guy-bowerman]

##Fase 1: Creare un'immagine
In questa fase si creerà la macchina virtuale usando un'immagine Linux in Azure.  

###Passaggio 1: Generare una chiave di autenticazione SSH
SSH è uno strumento importante per gli amministratori di sistema. Tuttavia, fare affidamento su una password di sicurezza stabilita da una persona fisica non sempre è una scelta consigliabile. Una chiave SSH avanzata consente di lasciare aperto l'accesso remoto senza doversi preoccupare delle password. Il metodo consiste nell'autenticazione con crittografia asimmetrica. La chiave privata dell'utente è quella che concede l'autenticazione. È anche possibile bloccare l'account dell'utente per impedire completamente l'autenticazione tramite password.    

Attenersi a questa procedura per generare la chiave di autenticazione SSH.

-	Scaricare e installare puttygen dal seguente percorso: [http://www.chiark.greenend.org.uk/~sgtatham/](http://www.chiark.greenend.org.uk/~sgtatham/)putty/download.html 
-	Eseguire puttygen.exe.
-	Fare clic su **Generate** per generare le chiavi. Nel processo è possibile aumentare la casualità spostando il puntatore del mouse sull'area vuota della finestra.  
![][1]
-	Dopo il processo di generazione, Puttygen.exe visualizzerà la chiave generata. Ad esempio:  
![][2]
-	Selezionare e copiare la chiave pubblica in **Key** e salvarla in un file denominato **publicKey.pem**. Non fare clic su **Save public key** poiché il formato di file della chiave pubblica salvata è diverso dalla chiave pubblica richiesta.
-	Fare clic su **Save private key** e salvarla in un file denominato **privateKey.ppk**. 

###Passaggio 2: Creare l'immagine nel portale di anteprima di Azure.
Nel [portale di anteprima di Azure](https://portal.azure.com/) fare clic su **Nuovo** nella barra delle applicazioni a creare un'immagine usando le istruzioni seguenti e scegliendo l'immagine Linux in base alle esigenze. In questo esempio viene usata l'immagine Ubuntu 14.04. 

![][3] 

In **Nome host** specificare il nome per l'URL che verrà usato dall'utente e dai client Internet per accedere a questa macchina virtuale. Definire l'ultima parte del nome DNS, ad esempio LAMPDemo; Azure genererà l'URL come Lampdemo.cloudapp.net.   

In **Nome utente** scegliere un nome che verrà usato successivamente per l'accesso alla macchina virtuale.   

In **Chiave di autenticazione SSH** copiare il valore della chiave dal file **publicKey.pem** che contiene la chiave pubblica generata da puttygen.  

![][4]
  
Configurare altre impostazioni in base alle esigenze, quindi fare clic su **Crea**.

##Fase 2: Preparare la macchina virtuale per lo stack LAMP
In questa fase verrà configurare un endpoint per il traffico web e sarà quindi connettersi alla nuova macchina virtuale.

###Passaggio 1: Aprire la porta HTTP per consentire l'accesso web
Gli endpoint di Azure sono costituiti da un protocollo (TCP o UDP), insieme a una porta pubblica e privata. La porta privata è la porta ascoltata dal servizio sulla macchina virtuale. La porta pubblica è la porta ascoltata dal servizio cloud di Azure esternamente per il traffico basato su Internet. In alcuni casi si tratta dello stesso numero di porta.  

La porta TCP 80 è il numero di porta predefinito su cui Apache è in ascolto. Se si apre questa porta con un endpoint di Azure l'utente e altri client Internet potranno accedere al server Web Apache.  

Nel portale di anteprima di Azure, fare clic su **Sfoglia -> Macchina virtuale**, quindi scegliere la macchina virtuale creata.

![][5] 

Per aggiungere un endpoint a una macchina virtuale, scegliere la casella **Endpoint**.

![][6]
 
Fare clic su **Aggiungi**. Quando si effettua il provisioning di una nuova macchina virtuale è possibile abilitare o disabilitare gli endpoint in base alle esigenze.   

Configurare l'endpoint:  

1.	Digitare un nome per l'endpoint in **Endpoint**.
2.	Digitare 80 in **Porta pubblica**. Se è stata modificata la porta di ascolto predefinita di Apache, è necessario aggiornare la porta privata in modo che sia uguale alla porta di ascolto di Apache. 
3.	Digitare 80 in **Porta pubblica**. Per impostazione predefinita, il traffico HTTP usa la porta 80.
Se la porta viene impostata su 80, non occorre includere il numero di porta nell'URL che consente di accedere al servizio Web di Apache. Ad esempio, http://lampdemo.cloudapp.net.
Se si imposta la porta di ascolto Apache su un altro valore, come la 81, è necessario aggiungere il numero di porta all'URL per accedere al servizio Web di Apache. Ad esempio, http://lampdemo.cloudapp.net:81/.

![][7] 

Fare clic su **OK** per aggiungere l'endpoint alla macchina virtuale.




###Passaggio 2: Connettersi all'immagine creata
È possibile scegliere qualsiasi strumento SSH per connettersi alla nuova macchina virtuale. In questo esempio, viene usato Putty.  

Innanzitutto, ottenere il nome DNS della macchina virtuale dal portale di anteprima di Azure. Fare clic su **Sfoglia -> macchine virtuali ->** nome della macchina virtuale **-> Proprietà** e osservare il campo **Nome di dominio** del riquadro **Proprietà**. 

Ottenere il numero di porta per le connessioni SSH dal campo **SSH**.   Di seguito è fornito un esempio.  

![][8]
  
Scaricare Putty da [qui](http://www.putty.org/).  

Dopo il download, fare clic sul file eseguibile PUTTY.EXE. Configurare le opzioni di base con il nome host e il numero di porta ottenuto dalle proprietà della macchina virtuale. Di seguito è fornito un esempio:
 
![][9]

Nel riquadro sinistro fare clic su **Connection -> SSH -> Auth** e quindi fare clic su **Browse** per specificare il percorso del file **privateKey.ppk** che contiene la chiave privata generata da puttygen nella fase 1: Creare un'immagine. Di seguito è fornito un esempio:  

![][10]
 
Fare clic su **Apri**. Si potrebbe ricevere un avviso da una finestra di messaggio. Se il nome DNS e il numero di porta sono stati configurati correttamente, fare clic su **Yes**.
  
![][11]


Dovrebbe essere visualizzata la schermata seguente. 
 
![][12]

Immettere il nome utente specificato durante la creazione della macchina virtuale nella fase 1: Creare un'immagine. Verrà visualizzata una schermata simile alla seguente:  
 
![][13]

##Fase 3: Installare lo Stack LAMP

A seconda di quali distribuzione Linux usata per creare la macchina virtuale, esistono diversi modi per installare lo stack LAMP. Le sezioni seguenti contengono i passaggi tipici da eseguire in alcuni sistemi operativi Linux di uso comune.

###Base Red Hat, CentOS

####Installare Apache
Per installare Apache, aprire il terminale ed eseguire il comando seguente:  

	sudo yum install httpd

Dopo l'installazione, avviare Apache con il comando seguente:  

	sudo service httpd start
####Testare Apache
Per verificare se Apache è stato installato correttamente, selezionare il nome DNS del server Apache (per l'URL di esempio di questo articolo, http://lampdemo.cloudapp.net/). La pagina deve visualizzare le parole "It works!"
![][14]

####Risoluzione dei problemi 
Se Apache è in esecuzione ma è possibile visualizzare la pagina predefinita di Apache riportata sopra, è necessario controllare che:  

-	Il servizio Web di Apache sia in ascolto dell'indirizzo/della porta
	-	Controllare l'impostazione dell'endpoint per la macchina virtuale di Azure. Assicurarsi che la configurazione dell'endpoint sia appropriata. Vedere le istruzioni della fase 1: Creare un'immagine in questo articolo.
	-	Aprire /etc/httpd/conf/httpd.conf e quindi cercare la stringa "Listen". Assicurarsi che la porta di ascolto di Apache corrisponda alla porta privata configurata per l'endpoint. La porta predefinita per Apache è 80. Di seguito è fornito un esempio.  

			......
			......
				# prevent Apache from glomming onto all bound IP addresses (0.0.0.0)
				#
				#Listen 12.34.56.78:80
				Listen 80
				#
				# Dynamic Shared Object (DSO) Support
			......
			......  

-	Configurazione di firewall, iptable  
Se è possibile visualizzare la pagina predefinita di Apache dall'host locale, il problema potrebbe risiedere nel fatto che la porta su cui Apache è in ascolto è bloccata dal firewall. È possibile usare lo strumento w3m per passare alla pagina web di Apache. I comandi seguenti consentono di installare w3m e di passare alla pagina predefinita di Apache:  

		sudo yum  install w3m w3m-img  
		w3m http://localhost

	Se il problema è causato dal firewall o da iptable, aggiungere le righe seguenti per /etc/sysconfig/iptables:  

		-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
		-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT

	Si noti che la seconda riga è necessaria solo per il traffico https.  

	Assicurarsi che queste righe si trovino sopra eventuali righe che limiterebbero l'accesso a livello globale, come le seguenti:  

		-A INPUT -j REJECT --reject-with icmp-host-prohibited  

	Per rendere effettive le nuove impostazioni, usare il comando seguente:  

		service iptables restart

####Installare MySQL
Per installare MySQL, aprire il terminale ed eseguire i comandi seguenti:  

	sudo yum install mysql-server
	sudo service mysqld start

Durante l'installazione MySQL richiederà l'autorizzazione due volte. Dopo aver risposto affermativamente a entrambe le query, MySQL verrà installato.

####Configurazione di MySQL
Al termine dell'installazione è possibile impostare una password MySQL radice con il codice seguente:  

	sudo /usr/bin/mysql_secure_installation  

Il prompt dei comandi richiederà la password radice corrente.    

Poiché MySQL è stato appena installato, è molto probabile che non si disponga della password, quindi lasciare vuoto il campo premendo INVIO.  

	Enter current password for root (enter for none): 
	OK, successfully used password, moving on...  

Verrà richiesto di impostare una password radice. Proseguire e scegliere Y, quindi seguire le istruzioni.  

CentOS automatizza il processo di configurazione di MySQL, che richiede una serie di domande alle quali occorre rispondere affermativamente o negativamente. Le domande sono visualizzate sotto. Scegliere Y o N per la configurazione. Al termine, MySQL si ricaricherà e implementerà le nuove modifiche.  

	By default, a MySQL installation has an anonymous user, allowing anyone
	to log into MySQL without having to have a user account created for
	them.  This is intended only for testing, and to make the installation
	go a bit smoother.  You should remove them before moving into a
	production environment.
	
	Remove anonymous users? [Y/n] y                                            
	 ... Success!
	
	Normally, root should only be allowed to connect from 'localhost'.  This
	ensures that someone cannot guess at the root password from the network.
	
	Disallow root login remotely? [Y/n] y
	... Success!
	
	By default, MySQL comes with a database named 'test' that anyone can
	access.  This is also intended only for testing, and should be removed
	before moving into a production environment.
	
	Remove test database and access to it? [Y/n] y
	 - Dropping test database...
	 ... Success!
	 - Removing privileges on test database...
	 ... Success!
	
	Reloading the privilege tables will ensure that all changes made so far
	will take effect immediately.
	
	Reload privilege tables now? [Y/n] y
	 ... Success!
	
	Cleaning up...
	
	All done!  If you've completed all of the above steps, your MySQL
	installation should now be secure.
	
	Thanks for using MySQL!  

####Installare PHP
PHP è un linguaggio di scripting Web open source che è ampiamente usato per creare pagine Web dinamiche.  

Per installare PHP nella macchina virtuale, aprire il terminale ed eseguire il comando seguente:  

	sudo yum install php php-mysql  

Rispondere "y" per scaricare i pacchetti software. Quindi, rispondere "y" per importare la chiave GPG 0xE8562897 "CentOS-5 Key (CentOS 5 Official Signing Key)". PHP verrà installato.   

	warning: rpmts_HdrFromFdno: Header V3 DSA signature: NOKEY, key ID e8562897
	updates/gpgkey                                                                                                                                                                       | 1.5 kB     00:00
	Importing GPG key 0xE8562897 "CentOS-5 Key (CentOS 5 Official Signing Key) <centos-5-key@centos.org>" from /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5
	Is this ok [y/N]: y  

###Base Debian, Ubuntu
Questa installazione è stata effettuata su Ubuntu 14.04.  

Ubuntu è basato su Debian. È possibile installare lo stack LAMP nello stesso modo della serie Red Hat. Per semplificare le operazioni, usare lo strumento Tasksel.

Tasksel è uno strumento Debian/Ubuntu che consente di installare più pacchetti correlati come attività coordinata nel sistema. Per altre informazioni, vedere la sezione [Tasksel wiki di assistenza della community](https://help.ubuntu.com/community/Tasksel).

Usare tasksel per installare il software necessario per lo stack LAMP.

- Per scaricare gli elenchi di pacchetto dai repository e aggiornarli per ottenere informazioni sulle versioni più recenti dei pacchetti e sulle relative dipendenze:  

		sudo apt-get update
-	Per installare lo stack LAMP Ubuntu usando Tasksel:  

		sudo apt-get install tasksel
		sudo tasksel install lamp-server

Quindi, eseguire la procedura guidata e scegliere la **password MySQL radice**.

![][15] 


##Test di LAMP sul server
È possibile testare il sistema LAMP creando una pagina informativa rapida PHP.   

Innanzitutto, creare un nuovo file:  

	sudo nano /var/www/html/info.php  

Aggiungere la riga seguente:  

	<?php
	phpinfo();
	?>  

Quindi, salvare e uscire.  

Riavviare Apache per rendere effettive tutte le modifiche sul computer. Se il sistema operativo della macchina virtuale è CentOS, usare il comando seguente per riavviare Apache:  

	sudo service httpd restart

Se il sistema operativo della macchina virtuale è Ubuntu, usare il comando seguente per riavviare Apache:  

	sudo service apache2 restart  

Terminare passando alla pagina informativa PHP (per il server Web di esempio usato in questo argomento, l'URL sarebbe http://lampdemo.cloudapp.net/info.php).  

Il browser dovrebbe avere un aspetto simile al seguente:
 
![][16]

##Passaggi aggiuntivi

Come regola generale, verranno modificate alcune impostazioni predefinite per prepararsi alla distribuzione delle applicazioni Web.  

###Consentire l'accesso remoto a MySQL
Se sono presenti più macchine virtuali installate con MySQL e per esse è necessario effettuare lo scambio di dati, abilitare l'accesso remoto di MySQL e concedere le autorizzazioni appropriate.  
 
**Formato di riferimento del comando:**

	grant [authority] on [databaseName].[tableName] to [username]@[login host] identified by "[passwd]"  

**Esempio:**

	grant select,insert,update,delete on studentManage.student to user1@"%" Identified by "abc";

È inoltre necessario modificare il profilo /etc/mysql/my.cnf. Se sono presenti righe simili alla seguente:  

	skip-networking
	bind-address = 127.0.0.1  

È necessario commentarle (aggiungere il simbolo # all'inizio delle righe) e riavviare MySQL.  

Per aggiungere un endpoint allo scopo di consentire l'accesso remoto, fare riferimento alle istruzioni della fase 1: Creare un'immagine per creare un nuovo endpoint. Il numero di porta TCP predefinito per l'accesso remoto di MySQL è 3306. Di seguito è fornito un esempio:
 
![][17]

###Distribuire le applicazioni Web al server Apache
Dopo aver installato lo stack LAMP correttamente, è possibile distribuire l'applicazione Web esistente al server Web Apache (la macchina virtuale in uso). Si tratta della stessa procedura usata per la distribuzione di un'applicazione Web esistente in un server Web fisico.

-	La radice del server Web si trova in **/var/www/html**. È necessario concedere privilegi agli utenti che hanno la necessità di caricare file in questa cartella. L'esempio seguente mostra come aggiungere un'autorizzazione di scrittura a un gruppo denominato lampappgroup e inserire il nome utente azureuser in questo gruppo:  
 
		sudo groupadd lampappgroup                      # Create a group
		sudo gpasswd -a azureuser lampappgroup    # Add azureuser to lampappgroup
		sudo chgrp lampappgroup /var/www/html/  # Change the ownership to group lampappgroup
		sudo chmod g+w /var/www/html/                 # grant write permission to group lampappgroup    

	>[AZURE.NOTE] Potrebbe essere necessario eseguire nuovamente l'accesso per modificare un file in/var/www/html /. 
-	Usare qualsiasi client FTP sicuro (ad esempio, FileZilla) per connettersi al nome DNS della macchina virtuale (ad esempio, lampdemo.cloudapp.net) e passare a /**var/www/html** per pubblicare il sito.  
![][18]

 

##Problemi comuni e risoluzione dei problemi

###Impossibile accedere alla macchina virtuale con Apache e Moodle da Internet

-	**Sintomo**
Apache è in esecuzione ma non è possibile visualizzare la pagina predefinita di Apache con il browser.
-	**Possibile caso radice**
	1.	La porta di ascolto di Apache non corrisponde alla porta privata dell'endpoint della macchina virtuale per il traffico Web.</br>   
	Controllare le impostazioni dell'endpoint della porta pubblica e privata e assicurarsi che la porta privata corrisponda alla porta di ascolto di Apache. Vedere la fase 1: Creare un'immagine per istruzioni sulla configurazione degli endpoint per la macchina virtuale.</br>   
	Per determinare la porta di ascolto di Apache, aprire /etc/httpd/conf/httpd.conf (versione Red Hat) o /etc/apache2/ports.conf (versione Debian), cercare la stringa "Listen". La porta predefinita è 80.

	2.	Il firewall ha disabilitato la porta di ascolto di Apache.</br>  
	Se è possibile visualizzare la pagina predefinita di Apache dall'host locale, molto probabilmente il problema risiede nel fatto che la porta su cui Apache è in ascolto è bloccata dal firewall. È possibile usare lo strumento w3m per passare alla pagina web. I comandi seguenti consentono di installare w3m e di passare alla pagina predefinita di Apache:  
  
			sudo yum  install w3m w3m-img
			w3m http://localhost 

-	**Soluzione**

	1.	Se la porta di ascolto di Apache non corrisponde alla porta privata dell'endpoint per il traffico Web nella macchina virtuale, è necessario modificare la porta privata dell'endpoint affinché corrisponda alla porta di ascolto Apache.
	2.	Se il problema è causato dal firewall o dagli iptable, aggiungere le seguenti righe a /etc/sysconfig/iptables:  
	
			-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
			-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT    

		Si noti che la seconda riga è necessaria solo per il traffico https.   
 
		Assicurarsi che questa riga si trovi sopra eventuali righe che limiterebbero l'accesso a livello globale, come la seguente:  

			-A INPUT -j REJECT --reject-with icmp-host-prohibited  

		Per ricaricare gli iptable, eseguire il seguente comando:  

			service iptables restart  

		Questa installazione è stata effettuata su CentOS 6.3.

###Autorizzazione negata quando si caricano i file di progetto in /var/www/html/  

-	**Sintomo**
Quando si usa qualsiasi client FTP sicuro (ad esempio, FileZilla) per connettersi alla macchina virtuale e passare a /var/www/html per pubblicare il sito, viene visualizzato un messaggio di errore simile al seguente:  

		status:	Listing directory /var/www/html
		Command:	put "C:\Users\liang\Desktop\info.php" "info.php"
		Error:	/var/www/html/info.php: open for write: permission denied
		Error:	File transfer failed

-	**Possibile caso radice**
Non si dispone di autorizzazioni di accesso alla cartella /var/www/html.  
-	**Soluzione**
È necessario ottenere l'autorizzazione dall'account radice. È possibile modificare la proprietà di tale cartella dalla radice al nome utente usato per il provisioning della macchina. Di seguito è riportato un esempio con il nome dell'account azureuser:  

		sudo chown azureuser -R /var/www/html  

	Usare l'opzione -R per applicare le autorizzazioni per tutti i file anche all'interno di una directory.  

	Si noti che questo comando funziona anche per le directory. L'opzione -R modifica le autorizzazioni per tutti i file e le directory all'interno della directory. Di seguito è fornito un esempio:  

		sudo chown -R username:group directory  

	Questo comando modifica la proprietà (dell'utente e del gruppo) per tutti i file e le directory all'interno della directory e per la directory stessa.  

	Il seguente comando modifica solo l'autorizzazione della directory della cartella ma lascia all'interno della directory solo i file e le cartelle.  

		sudo chown username:group directory  

###Impossibile determinare in modo affidabile il nome di dominio completo del server 

-	**Sintomo**
Quando si riavvia il server Apache usando uno dei seguenti comandi:  

		sudo /etc/init.d/apache2 restart  # Debian release  

	oppure   

		sudo /etc/init.d/httpd restart   # Red Hat release  

	Viene visualizzato l'errore seguente:  

		Restarting web server apache2
		apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1 for ServerName
		... waiting apache2:
		Could not reliably determine the server's fully qualified domain name, using 127.0.1.1 for ServerName  

-	**Possibile caso radice**
	Non è stato impostato il nome del server di Apache.

-	**Soluzione**
	Inserire una riga "ServerName localhost" (versione Red Hat) o apache2.conf (versione Debian) in /etc/apache2 e riavviare Apache. L'avviso scomparirà. 



[1]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-01.png
[2]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-02.png
[3]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-03.png
[4]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-04.png
[5]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-05.png
[6]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-06.png
[7]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-07.png
[8]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-08.png
[9]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-09.png
[10]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-10.png
[11]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-11.png
[12]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-12.png
[13]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-13.png
[14]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-14.png
[15]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-15.png
[16]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-16.png
[17]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-17.png
[18]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-18.jpg




<!--HONumber=42-->
