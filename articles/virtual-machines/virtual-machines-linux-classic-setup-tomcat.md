<properties
	pageTitle="Impostare Apache Tomcat su una macchina virtuale Linux | Microsoft Azure"
	description="Informazioni su come configurare Apache Tomcat7 usando una macchina virtuale Azure (VM) che esegue Linux."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="NingKuang"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/15/2015"
	ms.author="ningk"/>

#Come configurare Tomcat7 in una macchina virtuale Linux con Microsoft Azure

Apache Tomcat (o semplicemente Tomcat, precedentemente denominato anche Jakarta Tomcat) è un server Web e contenitore di servlet open source sviluppato da Apache Software Foundation (ASF). Tomcat implementa le specifiche Java Servlet e Java Server Pages (JSP) di Sun Microsystems e fornisce un ambiente server Web HTTP in puro Java in cui eseguire il codice Java. Nella configurazione più semplice, Tomcat viene eseguito in un singolo processo del sistema operativo. Questo processo esegue una macchina virtuale Java (JVM). Ogni richiesta HTTP da un browser a Tomcat viene elaborata nel processo di Tomcat come un thread separato.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


In questa guida si installerà tomcat7 su un'immagine Linux e la si distribuirà in Microsoft Azure.

Si acquisiranno le nozioni seguenti:

-	Come creare una macchina virtuale in Azure.
-	Come preparare la macchina virtuale per tomcat7.
-	Come installare tomcat7.

Si presuppone che l'utente disponga già di una sottoscrizione di Azure. Diversamente, è possibile iscriversi per una versione di valutazione gratuita sul sito [http://azure.microsoft.com](https://azure.microsoft.com/). Se si ha un abbonamento MSDN, vedere [Offerte speciali di Microsoft Azure: vantaggi per i membri di MSDN, MPN e Bizspark](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Per altre informazioni su Azure, vedere [Cos'è Microsoft Azure?](https://azure.microsoft.com/overview/what-is-azure/).

In questo argomento si presuppone che l'utente abbia una conoscenza pratica di base di tomcat e Linux.

##Fase 1: creare un'immagine.
In questa fase si creerà una macchina virtuale usando un'immagine Linux in Azure.

###Passaggio 1: generare una chiave di autenticazione SSH
SSH è uno strumento importante per gli amministratori di sistema. Tuttavia, non è consigliabile configurare la protezione di accesso in base a una password stabilita da una persona fisica. Con un nome utente e una password debole, il sistema può essere esposto all'attacco da parte di utenti malintenzionati.

L'aspetto positivo è che esiste un modo per lasciare aperto l'accesso remoto e non doversi preoccupare delle password. Il metodo consiste nell'autenticazione con crittografia asimmetrica. La chiave privata dell'utente è quella che concede l'autenticazione. È anche possibile bloccare l'account dell'utente per impedire completamente l'autenticazione tramite password.

Un altro vantaggio di questo metodo è che non sono necessarie password diverse per accedere a server diversi. È possibile autenticarsi tramite la chiave privata personale in tutti i server. In questo modo non sarà necessario ricordare diverse password.

Con questo metodo è inoltre possibile eseguire l'accesso richiedendo una password.

Attenersi a questa procedura per generare la chiave di autenticazione SSH.

1.	Scaricare e installare puttygen dal percorso seguente: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.	Eseguire PUTTYGEN.EXE.
3.	Fare clic su **Generate** per generare le chiavi. Nel processo è possibile aumentare la casualità spostando il puntatore del mouse sull'area vuota della finestra. ![][1]
4.	Dopo il processo di generazione, Puttygen.exe visualizzerà la chiave generata. Ad esempio: ![][2]
5.	Selezionare e copiare la chiave pubblica in **Key** e salvarla in un file denominato publicKey.pem. Non fare clic su **Save public key**, poiché il formato di file della chiave pubblica salvata è diverso dalla chiave pubblica richiesta.
6.	Fare clic su **Save private key** e salvarla in un file denominato privateKey.ppk.

###Passaggio 2: creare l'immagine nel portale di Azure.
Nel [portale di Azure](https://portal.azure.com/) fare clic su **Nuovo** nella barra delle applicazioni per creare un'immagine, scegliendo l'immagine Linux in base alle esigenze. Il seguente esempio usa l'immagine Ubuntu 14.04. ![][3]

In **Nome host** specificare il nome per l'URL che verrà usato dall'utente e dai client Internet per accedere a questa macchina virtuale. Definire l'ultima parte del nome DNS, ad esempio tomcatdemo, e Azure genererà l'URL come tomcatdemo.cloudapp.net.

In **Chiave di autenticazione SSH** copiare il valore della chiave dal file **publicKey.pem** che contiene la chiave pubblica generata da puttygen. ![][4]

Configurare altre impostazioni in base alle esigenze, quindi fare clic su Crea.

##Fase 2: preparare la macchina virtuale per Tomcat7
In questa fase si configurerà un endpoint per il traffico in tomcat e si effettuerà la connessione alla nuova macchina virtuale.
###Passaggio 1: aprire la porta HTTP per consentire l'accesso web
Gli endpoint di Azure sono costituiti da un protocollo (TCP o UDP), insieme a una porta pubblica e privata. La porta privata è la porta ascoltata dal servizio sulla macchina virtuale. La porta pubblica è la porta ascoltata esternamente dal servizio cloud di Azure per il traffico in ingresso basato su Internet.

La porta TCP 8080 è la porta predefinita su cui tomcat è in ascolto. Se si apre questa porta con un endpoint di Azure, l'utente e altri client Internet potranno accedere alle pagine tomcat.

1.	Nel portale di Azure, fare clic su **Sfoglia** -> **Macchina virtuale**, quindi fare clic sulla macchina virtuale creata. ![][5]
2.	Per aggiungere un endpoint alla macchina virtuale, scegliere la casella **Endpoint**. ![][6]
3.	Fare clic su **Aggiungi**.
	1.	Nell'**endpoint** digitare un nome per l'endpoint, quindi digitare 80 nella **porta pubblica**.

		Se la porta viene impostata su 80, non occorre includere il numero di porta nell'URL che consente di accedere a tomcat. ad esempio http://tomcatdemo.cloudapp.net.

		Se si imposta la porta su un altro valore, come la 81, è necessario aggiungere il numero di porta all'URL per accedere a tomcat. ad esempio http://tomcatdemo.cloudapp.net:81/.
	2.	Digitare 8080 nella porta privata. Per impostazione predefinita, tomcat è in ascolto sulla porta TCP 8080. Se è stata modificata la porta di ascolto predefinita di tomcat, è necessario aggiornare la porta privata in modo che corrisponda alla porta di ascolto di tomcat. ![][7]

4.	Fare clic su **OK** per aggiungere l'endpoint alla macchina virtuale.



###Passaggio 2: connettersi all'immagine creata.
È possibile scegliere qualsiasi strumento SSH per connettersi alla macchina virtuale. In questo esempio, viene usato Putty.

Innanzitutto, ottenere il nome DNS della macchina virtuale dal portale di Azure. **Fare clic su Sfoglia** -> **Macchine virtuali** -> nome della macchina virtuale -> **Proprietà** e osservare il campo **Nome di dominio** del riquadro **Proprietà**.

Ottenere il numero di porta per le connessioni SSH dal campo **SSH**. Di seguito è fornito un esempio. ![][8]

Scaricare Putty da [questa pagina](http://www.putty.org/).

Dopo il download, fare clic sul file eseguibile PUTTY.EXE. Configurare le opzioni di base con il nome host e il numero di porta ottenuto dalle proprietà della macchina virtuale. Di seguito è fornito un esempio: ![][9]

Nel riquadro sinistro fare clic su **Connection** -> **SSH** -> **Auth** e quindi fare clic su **Browse** per specificare il percorso del file **privateKey.ppk** che contiene la chiave privata generata da puttygen nella fase 1: creare un’immagine. Di seguito è fornito un esempio: ![][10]

Fare clic su **Apri**. Si potrebbe ricevere un avviso da una finestra di messaggio. Se il nome DNS e il numero di porta sono stati configurati correttamente, fare clic su **Yes**. ![][11]


Dovrebbe essere visualizzata la seguente schermata: ![][12]

Immettere il nome utente specificato durante la creazione della macchina virtuale nella fase 1: creare un’immagine. Verrà visualizzata una schermata simile alla seguente: ![][13]





##Fase 3: installare il software
In questa fase si installerà l'ambiente di runtime Java, tomcat e altri componenti di tomcat.

###Ambiente di runtime Java
Tomcat è scritto in Java. Esistono due tipi di Java Development Kit (JDK) (OpenJDK e Oracle JDK) tra cui è possibile scegliere.

>AZURE.NOTE: il codice contenuto dai due JDK per le classi nell’API Java è praticamente identico, mentre il codice per la macchina virtuale è effettivamente diverso. Inoltre, OpenJDK tende a usare librerie aperte mentre Oracle tende a usare librerie chiuse. Oracle JDK dispone tuttavia di più classi, include alcuni bug corretti ed è più stabile di OpenJDK.

Tramite i seguenti comandi è possibile scaricare i due diversi JDK.

open-jdk

	sudo apt-get update  
	sudo apt-get install openjdk-7-jre  

oracle-jdk

-	Per scaricare JDK dal sito Web di Oracle:

		wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  

-	Per creare una directory in cui includere i file di JDK:

		sudo mkdir /usr/lib/jvm  

-	Per estrarre i file di JDK nella directory /usr/lib/jvm/:

		sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  

-	Per impostare Oracle JDK come il JVM predefinito:

		sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  
		sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

####Test:
Per verificare se l'ambiente di runtime Java è stato installato correttamente, è possibile usare un comando simile al seguente:

	java -version  

Se è stato installato open-jdk, si dovrebbe visualizzare un messaggio simile al seguente: ![][14]

Se è stato installato oracle-jdk, si dovrebbe visualizzare un messaggio simile al seguente: ![][15]

###Tomcat7
Usare il seguente comando per installare tomcat7:

	sudo apt-get install tomcat7  

Se non si usa tomcat7, modificare il comando in modo appropriato.

####Test:

Per verificare se tomcat7 è stato installato correttamente, selezionare il nome DNS del server tomcat (http://tomcatexample.cloudapp.net/ è l’URL di esempio di questo articolo). Se si visualizza una pagina simile alla seguente, tomcat7 è stato installato correttamente. ![][16]


###Installare altri componenti di Tomcat
Esistono altri componenti facoltativi di tomcat che è possibile installare.

Usare il comando **sudo apt-cache search tomcat7** per visualizzare tutti i componenti disponibili. I seguenti comandi sono esempi che illustrano come installare alcune parti utili.

	sudo apt-get install tomcat7-admin      #admin web applications
	sudo apt-get install tomcat7-user         #tools to create user instances  

##Fase 4: configurare Tomcat
In questa fase si apprenderà come amministrare tomcat.
###Avviare e arrestare tomcat7
Il server tomcat7 si avvierà automaticamente durante l'installazione. È possibile anche avviarlo manualmente con il seguente comando:

	sudo /etc/init.d/tomcat7 start

Per arrestare tomcat7：

	sudo /etc/init.d/tomcat7 stop

Per visualizzare lo stato di tomcat7：

	sudo /etc/init.d/tomcat7 status

Per riavviare i servizi tomcat：

	sudo /etc/init.d/tomcat7 restart

###Amministrazione di Tomcat
È possibile modificare il file di configurazione utente di Tomcat per impostare le credenziali di amministratore con il seguente comando:

	sudo vi  /etc/tomcat7/tomcat-users.xml   

Di seguito è fornito un esempio: ![][17]

>AZURE.NOTE: creare una password complessa per il nome utente dell'amministratore.

Dopo avere modificato questo file, è necessario riavviare i servizi tomcat7 con il seguente comando per rendere effettive le modifiche:

	sudo /etc/init.d/tomcat7 restart  

Aprire il browser e immettere l'URL **http://<nome DNS server tomcat>/manager/html**. Ad esempio, in questo articolo l'URL è http://tomcatexample.cloudapp.net/manager/html.

Dopo la connessione, si dovrebbe visualizzare una schermata simile alla seguente: ![][18]

##Problemi comuni

###Impossibile accedere alla macchina virtuale con Tomcat e Moodle da Internet

-	**Sintomo** Tomcat è in esecuzione ma non è possibile visualizzare la pagina predefinita di Tomcat con il browser.
-	**Possibile causa principale**
	1.	La porta di ascolto di tomcat non corrisponde alla porta privata dell'endpoint della macchina virtuale per il traffico in tomcat.

		Controllare le impostazioni dell'endpoint della porta pubblica e privata e assicurarsi che la porta privata corrisponda alla porta di ascolto di tomcat. Vedere Fase 1: creare un'immagine per istruzioni sulla configurazione degli endpoint per la macchina virtuale.

		Per determinare la porta di ascolto tomcat, aprire /etc/httpd/conf/httpd.conf (versione Red Hat) o /etc/tomcat7/server.xml (versione Debian). Per impostazione predefinita, la porta di ascolto di tomcat è 8080. Di seguito è fornito un esempio:

			<Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"  URIEncoding="UTF-8"            redirectPort="8443" />  

		Se si usa una macchina virtuale come Debian o Ubuntu e si desidera modificare la porta predefinita di ascolto di Tomcat (ad esempio, 8081), è necessario aprire la porta anche per il sistema operativo. Aprire innanzitutto il profilo:

			sudo vi /etc/default/tomcat7  

		Rimuovere quindi i commenti dall’ultima riga e modificare “no” in “sì”.

			AUTHBIND=yes

	2.	Il firewall ha disabilitato la porta di ascolto di tomcat.

		Se è possibile visualizzare la pagina predefinita di Tomcat dall'host locale, molto probabilmente il problema risiede nel fatto che la porta su cui tomcat è in ascolto è bloccata dal firewall. È possibile usare lo strumento w3m per passare alla pagina web. I seguenti comandi consentono di installare w3m e di passare alla pagina predefinita di Tomcat:

			sudo yum  install w3m w3m-img
			w3m http://localhost:8080  

-	**Soluzione**
	1. Se la porta di ascolto di tomcat non corrisponde alla porta privata dell'endpoint per il traffico nella macchina virtuale, è necessario modificare la porta privata dell'endpoint affinché corrisponda alla porta di ascolto di tomcat.

	2.	Se il problema è causato dal firewall o dagli iptable, aggiungere le seguenti righe a /etc/sysconfig/iptables:

			-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
			-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT  

		Si noti che la seconda riga è necessaria solo per il traffico https.

		Assicurarsi che questa riga si trovi sopra eventuali righe che limiterebbero l'accesso a livello globale, come la seguente:

			-A INPUT -j REJECT --reject-with icmp-host-prohibited  

		Per ricaricare gli iptable, eseguire il seguente comando:

			service iptables restart  

		Questa installazione è stata effettuata su CentOS 6.3.

###Autorizzazione negata quando si caricano i file di progetto in /var/lib/tomcat7/webapps/  

-	**Sintomo** Quando si usa qualsiasi client FTP sicuro (ad esempio, FileZilla) per connettersi alla macchina virtuale e passare a /var/lib/tomcat7/webapps/ per pubblicare il sito, si visualizza un messaggio di errore simile al seguente:

		status:	Listing directory /var/lib/tomcat7/webapps
		Command:	put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
		Error:	/var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
		Error:	File transfer failed

-	**Possibile causa principale** Non si dispone delle autorizzazioni di accesso alla cartella /var/lib/tomcat7/webapps.
-	**Soluzione** È necessario ottenere l'autorizzazione dall'account radice. È possibile modificare la proprietà di tale cartella dalla radice al nome utente usato per il provisioning della macchina. Di seguito è riportato un esempio con il nome dell'account azureuser:

		sudo chown azureuser -R /var/lib/tomcat7/webapps

	Usare l'opzione -R per applicare le autorizzazioni per tutti i file anche all'interno di una directory.

	Si noti che questo comando funziona anche per le directory. L'opzione -R modifica le autorizzazioni per tutti i file e le directory all'interno della directory. Di seguito è fornito un esempio:

		sudo chown -R username:group directory  

	Questo comando modifica la proprietà (dell'utente e del gruppo) per tutti i file e le directory all'interno della directory e per la directory stessa.

	Il seguente comando modifica solo l'autorizzazione della directory della cartella ma lascia all'interno della directory solo i file e le cartelle.

		sudo chown username:group directory



[1]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-18.png

<!---HONumber=AcomDC_0629_2016-->