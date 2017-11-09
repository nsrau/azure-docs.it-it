---
title: Configurare Apache Tomcat su una macchina virtuale Linux | Documentazione Microsoft
description: Informazioni su come configurare Apache Tomcat7 usando macchine virtuali di Azure che eseguono Linux.
services: virtual-machines-linux
documentationcenter: 
author: NingKuang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 45ecc89c-1cb0-4e80-8944-bd0d0bbedfdc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: ningk
ms.openlocfilehash: b76f6f7f53bc649fbc740a79e182f81f3fd983c7
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2017
---
# <a name="set-up-tomcat7-on-a-linux-virtual-machine-with-azure"></a>Configurare Tomcat7 in una macchina virtuale Linux con Azure
Apache Tomcat (o semplicemente Tomcat, precedentemente chiamato anche Jakarta Tomcat) è un server Web e contenitore di servlet open source sviluppato da Apache Software Foundation (ASF). Tomcat implementa le specifiche Java Servlet e Java Server Pages (JSP) di Sun Microsystems. Tomcat fornisce un ambiente server Web HTTP Java puro in cui eseguire codice Java. Nella configurazione più semplice, Tomcat viene eseguito in un singolo processo del sistema operativo. Questo processo esegue una macchina virtuale Java (JVM). Ogni richiesta HTTP da un browser a Tomcat viene elaborata nel processo di Tomcat come un thread separato.  

> [!IMPORTANT]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Azure Resource Manager e classico](../../../resource-manager-deployment-model.md). Questo articolo descrive come usare il modello di distribuzione classica. Per le distribuzioni più recenti si consiglia di usare il modello di Resource Manager. Per informazioni su come usare un modello di Resource Manager per distribuire una VM Ubuntu con Openjdk e Tomcat, vedere [questo articolo](https://azure.microsoft.com/documentation/templates/openjdk-tomcat-ubuntu-vm/).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

In questo articolo si installerà Tomcat7 su un'immagine Linux e la si distribuirà in Azure.  

Si acquisiranno le nozioni seguenti:  

* Come creare una macchina virtuale in Azure.
* Come preparare la macchina virtuale per Tomcat7.
* Come installare Tomcat7.

La procedura presuppone che l'utente disponga già di una sottoscrizione di Azure.  In caso contrario è possibile registrarsi per una versione di valutazione gratuita sul [sito Web di Azure](https://azure.microsoft.com/). Se si ha un abbonamento MSDN, vedere [Offerte speciali di Microsoft Azure: vantaggi per i membri di MSDN, MPN e Bizspark](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Per altre informazioni su Azure, vedere [Cos'è Microsoft Azure?](https://azure.microsoft.com/overview/what-is-azure/).

Questo articolo presuppone che l'utente abbia una conoscenza pratica di base di Tomcat e Linux.  

## <a name="phase-1-create-an-image"></a>Fase 1: creare un'immagine.
In questa fase si creerà una macchina virtuale usando un'immagine Linux in Azure.  

### <a name="step-1-generate-an-ssh-authentication-key"></a>Passaggio 1: generare una chiave di autenticazione SSH
SSH è uno strumento importante per gli amministratori di sistema. Non è consigliabile tuttavia configurare la protezione di accesso in base a una password stabilita da una persona fisica. Con un nome utente e una password debole, il sistema può essere esposto all'attacco da parte di utenti malintenzionati.

L'aspetto positivo è che esiste un modo per lasciare aperto l'accesso remoto e non doversi preoccupare delle password. Il metodo consiste nell'autenticazione con crittografia asimmetrica. La chiave privata dell'utente è quella che concede l'autenticazione. È anche possibile bloccare l'account dell'utente per impedire l'autenticazione tramite password.

Un altro vantaggio di questo metodo è che non sono necessarie password diverse per accedere a server diversi. È possibile autenticarsi tramite la chiave privata personale in tutti i server, perciò non è necessario ricordare diverse password.



Attenersi a questa procedura per generare la chiave di autenticazione SSH.

1. Scaricare e installare PuTTYgen dal percorso seguente: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2. Eseguire Puttygen.exe.
3. Fare clic su **Generate** per generare le chiavi. Nel processo è possibile aumentare la casualità spostando il puntatore del mouse sull'area vuota della finestra.  
   ![Schermata di PuTTY Key Generator che mostra il pulsante per generare una nuova chiave][1]
4. Dopo il processo di generazione Puttygen.exe visualizzerà la nuova chiave pubblica.  
   ![Schermata di PuTTY Key Generator che mostra la nuova chiave pubblica e il pulsante per salvare la chiave privata][2]
5. Selezionare e copiare la chiave pubblica e salvarla in un file denominato publicKey.pem. Non fare clic su **Save public key**, poiché il formato di file della chiave pubblica salvata è diverso dalla chiave pubblica richiesta.
6. Fare clic su **Save private key** e salvarla in un file denominato privateKey.ppk.

### <a name="step-2-create-the-image-in-the-azure-portal"></a>Passaggio 2: creare l'immagine nel portale di Azure
1. Nel [portale](https://portal.azure.com/) fare clic su **Nuovo** nella barra delle applicazioni per creare un'immagine. Scegliere quindi l'immagine Linux in base alle esigenze. Il seguente esempio usa l'immagine Ubuntu 14.04.
![Schermata del portale che mostra il pulsante Nuovo][3]

2. In **Nome host** specificare il nome per l'URL che verrà usato dall'utente e dai client Internet per accedere a questa macchina virtuale. Definire l'ultima parte del nome DNS, ad esempio tomcatdemo. Azure genera quindi l'URL tomcatdemo.cloudapp.net.  

3. In **Chiave di autenticazione SSH** copiare il valore della chiave dal file publicKey.pem che contiene la chiave pubblica generata da PuTTYgen.  
![Casella Chiave di autenticazione SSH nel portale][4]

4. Configurare altre impostazioni in base alle esigenze, quindi fare clic su **Crea**.  

## <a name="phase-2-prepare-your-virtual-machine-for-tomcat7"></a>Fase 2: preparare la macchina virtuale per Tomcat7
In questa fase si configurerà un endpoint per il traffico in Tomcat e ci si connetterà alla nuova macchina virtuale.

### <a name="step-1-open-the-http-port-to-allow-web-access"></a>Passaggio 1: aprire la porta HTTP per consentire l'accesso web
Gli endpoint di Azure sono costituiti da un protocollo TCP o UDP, insieme a una porta pubblica e privata. La porta privata è la porta ascoltata dal servizio sulla macchina virtuale. La porta pubblica è la porta ascoltata esternamente dal servizio cloud di Azure per il traffico in ingresso basato su Internet.  

La porta TCP 8080 è la porta predefinita su cui Tomcat è in ascolto. Se si apre questa porta con un endpoint di Azure, l'utente e altri client Internet potranno accedere alle pagine Tomcat.  

1. Nel portale di Azure fare clic su **Sfoglia** > **Macchine virtuali** e quindi fare clic sulla macchina virtuale che è stata creata.  
   ![Schermata della directory Macchine virtuali][5]
2. Per aggiungere un endpoint alla macchina virtuale, scegliere la casella **Endpoint** .
   ![Schermata che mostra la casella Endpoint][6]
3. Fare clic su **Aggiungi**.  

   1. In **Endpoint** digitare un nome per l'endpoint, quindi digitare 80 nel campo **Porta pubblica**.  

      Se la porta viene impostata su 80, non occorre includere il numero di porta nell'URL che si usa per accedere a Tomcat. Ad esempio, http://tomcatdemo.cloudapp.net.    

      Se si imposta la porta su un altro valore, come 81, è necessario aggiungere il numero di porta all'URL per accedere a Tomcat. Ad esempio http://tomcatdemo.cloudapp.net:81/.
   2. Digitare 8080 in **Porta privata**. Per impostazione predefinita Tomcat è in ascolto sulla porta TCP 8080. Se è stata modificata la porta di ascolto predefinita di Tomcat, è necessario aggiornare la **porta privata** in modo che corrisponda alla porta di ascolto di Tomcat.  
      ![Schermata dell'interfaccia utente che mostra il comando Aggiungi, Porta pubblica e Porta privata][7]
4. Fare clic su **OK** per aggiungere l'endpoint alla macchina virtuale.

### <a name="step-2-connect-to-the-image-you-created"></a>Passaggio 2: effettuare la connessione all'immagine creata
È possibile scegliere qualsiasi strumento SSH per connettersi alla macchina virtuale. In questo esempio viene usato PuTTY.  

1. Ottenere il nome DNS della macchina virtuale dal portale.
    1. Fare clic su **Sfoglia** > **Macchine virtuali**.
    2. Selezionare il nome della macchina virtuale e quindi fare clic su **Proprietà**.
    3. Nel riquadro **Proprietà** osservare la casella **Nome di dominio** per ottenere il nome DNS.  

2. Ricavare il numero di porta per le connessioni SSH dalla casella **SSH** .  
![Schermata che mostra il numero di porta della connessione SSH][8]

3. Scaricare [PuTTY](http://www.putty.org/).  

4. Dopo il download fare clic sul file eseguibile Putty.exe. Nella configurazione di PuTTY impostare le opzioni di base con il nome host e il numero di porta ottenuto dalle proprietà della macchina virtuale.   
![Schermata che mostra le opzioni di configurazione di PuTTY relative a nome host e porta][9]

5. Nel riquadro a sinistra fare clic su **Connessione** > **SSH** > **Autenticazione** e quindi fare clic su **Sfoglia** per specificare il percorso del file privateKey.ppk. Il file PrivateKey contiene la chiave privata generata da PuTTYgen in precedenza nella sezione "Fase 1: creare un'immagine" di questo articolo.  
![Schermata che mostra la gerarchia di directory di connessione e il pulsante Sfoglia][10]

6. Fare clic su **Apri**. Si potrebbe ricevere un avviso da una finestra di messaggio. Se il nome DNS e il numero di porta sono stati configurati correttamente, fare clic su **Yes**.
![Schermata che mostra la notifica][11]

7. Viene chiesto di inserire il nome utente.  
![Schermata che mostra dove inserite il nome utente][12]

8. Inserire il nome utente utilizzato per creare la macchina virtuale nella sezione "Fase 1: creare un'immagine" di questo articolo. Verrà visualizzata una schermata simile alla seguente:   
![Schermata che mostra la conferma dell'autenticazione][13]

## <a name="phase-3-install-software"></a>Fase 3: installare il software
In questa fase saranno installati l'ambiente di runtime Java, Tomcat7 e altri componenti di Tomcat7.  

### <a name="java-runtime-environment"></a>Ambiente di runtime Java
Tomcat è scritto in Java. Esistono due tipi di Java Development Kit (JDK): OpenJDK e Oracle JDK. È possibile scegliere quello preferito.  

> [!NOTE]
> Il codice contenuto dai due JDK per le classi nell'API Java è praticamente identico, mentre il codice per la macchina virtuale è diverso. OpenJDK tende a usare librerie aperte mentre Oracle JDK tende a usare librerie chiuse. Oracle JDK dispone di più classi e include alcuni bug corretti, inoltre è più stabile di OpenJDK.

#### <a name="install-openjdk"></a>Installare OpenJDK  

Usare il comando seguente per scaricare OpenJDK.   

    sudo apt-get update  
    sudo apt-get install openjdk-7-jre  


* Per creare una directory in cui includere i file di JDK:  

        sudo mkdir /usr/lib/jvm  
* Per estrarre i file di JDK nella directory /usr/lib/jvm/:  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/

#### <a name="install-oracle-jdk"></a>Installare Oracle JDK


Usare il comando seguente per scaricare Oracle JDK dal sito Web di Oracle.  

     wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  
* Per creare una directory in cui includere i file di JDK:  

        sudo mkdir /usr/lib/jvm  
* Per estrarre i file di JDK nella directory /usr/lib/jvm/:  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  
* Per impostare Oracle JDK come macchina virtuale Java predefinita:  

        sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  

        sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

#### <a name="confirm-that-java-installation-is-successful"></a>Verificare che l'installazione di Java sia riuscita
Per verificare se l'ambiente di runtime Java è stato installato correttamente, è possibile usare un comando simile al seguente:  

    java -version  

Se è stato installato OpenJDK, si dovrebbe vedere un messaggio simile al seguente: ![installazione di OpenJDK riuscita][14]

Se è stato installato Oracle JDK, si dovrebbe vedere un messaggio simile al seguente: ![installazione di Oracle JDK riuscita][15]

### <a name="install-tomcat7"></a>Installare Tomcat7
Usare il seguente comando per installare Tomcat7.  

    sudo apt-get install tomcat7  

Se non si usa Tomcat7, modificare il comando in modo appropriato.  

#### <a name="confirm-that-tomcat7-installation-is-successful"></a>Verificare che l'installazione di Tomcat7 sia riuscita
Per verificare se Tomcat7 è stato installato correttamente, passare al nome DNS del server Tomcat. In questo articolo l'URL di esempio è http://tomcatexample.cloudapp.net/. Se viene visualizzato un messaggio simile al seguente, Tomcat7 è stato installato correttamente.
![Messaggio con l'avviso che l'installazione di Tomcat7 è riuscita][16]

### <a name="install-other-tomcat7-components"></a>Installare altri componenti di Tomcat7
Esistono altri componenti facoltativi di Tomcat che è possibile installare.  

Usare il comando **sudo apt-cache search tomcat7** per visualizzare tutti i componenti disponibili. Usare i seguenti comandi per installare alcuni utili componenti.  

    sudo apt-get install tomcat7-admin      #admin web applications

    sudo apt-get install tomcat7-user         #tools to create user instances  

## <a name="phase-4-configure-tomcat7"></a>Fase 4: configurare Tomcat7
In questa fase si amministra Tomcat.

### <a name="start-and-stop-tomcat7"></a>Avviare e arrestare Tomcat7
Il server Tomcat7 si avvia automaticamente durante l'installazione. È possibile anche avviarlo con il seguente comando:   

    sudo /etc/init.d/tomcat7 start

Per arrestare Tomcat7：

    sudo /etc/init.d/tomcat7 stop

Per visualizzare lo stato di Tomcat7：

    sudo /etc/init.d/tomcat7 status

Per riavviare i servizi Tomcat： 

    sudo /etc/init.d/tomcat7 restart

### <a name="tomcat7-administration"></a>Amministrazione di Tomcat7
È possibile modificare il file di configurazione utente di Tomcat per configurare le proprie credenziali di amministratore. Usare il comando seguente:  

    sudo vi  /etc/tomcat7/tomcat-users.xml   

Di seguito è fornito un esempio:  
![Schermata che mostra l'output del comando sudo vi][17]  

> [!NOTE]
> Creare una password complessa per il nome utente dell'amministratore.  

Dopo avere modificato questo file, è necessario riavviare i servizi Tomcat7 con il seguente comando per rendere effettive le modifiche:  

    sudo /etc/init.d/tomcat7 restart  

Aprire il browser e immettere l'URL **http://<your tomcat server DNS name>/manager/html**. Ad esempio, in questo articolo l'URL è http://tomcatexample.cloudapp.net/manager/html.  

Dopo la connessione, si dovrebbe visualizzare una schermata simile alla seguente:   
![Schermata di Tomcat Web Application Manager][18]

## <a name="common-issues"></a>Problemi comuni
### <a name="cant-access-the-virtual-machine-with-tomcat-and-moodle-from-the-internet"></a>Impossibile accedere alla macchina virtuale con Tomcat e Moodle da Internet
#### <a name="symptom"></a>Sintomo  
  Tomcat è in esecuzione ma non è possibile visualizzare la pagina predefinita di Tomcat con il browser.
#### <a name="possible-root-cause"></a>Possibile causa principale   

  * La porta di ascolto di Tomcat non corrisponde alla porta privata dell'endpoint della macchina virtuale per il traffico in Tomcat.  

     Controllare le impostazioni dell'endpoint della porta pubblica e privata e assicurarsi che la porta privata corrisponda alla porta di ascolto di Tomcat. Per istruzioni sulla configurazione degli endpoint per la macchina virtuale vedere la sezione di questo articolo "Fase 1: creare un'immagine".  

     Per determinare la porta di ascolto di Tomcat, aprire /etc/httpd/conf/httpd.conf (versione Red Hat) o /etc/tomcat7/server.xml (versione Debian). Per impostazione predefinita la porta di ascolto di Tomcat è 8080. Di seguito è fornito un esempio:  

        <Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"   URIEncoding="UTF-8"            redirectPort="8443" />  

     Se si usa una macchina virtuale come Debian o Ubuntu e si vuole modificare la porta di ascolto di Tomcat predefinita (ad esempio 8081), è necessario aprire la porta anche per il sistema operativo. Aprire innanzitutto il profilo:  

        sudo vi /etc/default/tomcat7  

     Rimuovere quindi i commenti dall’ultima riga e modificare “no” in “sì”.  

        AUTHBIND=yes
  2. Il firewall ha disabilitato la porta di ascolto di Tomcat.

     È possibile visualizzare la pagina predefinita di Tomcat solo dall'host locale. Molto probabilmente il problema è che la porta su cui è in ascolto Tomcat è bloccata dal firewall. È possibile usare lo strumento w3m per passare alla pagina Web. I seguenti comandi consentono di installare w3m e di passare alla pagina predefinita di Tomcat:  


        sudo yum install w3m w3m-img


        w3m http://localhost:8080  
#### <a name="solution"></a>Soluzione

  * Se la porta di ascolto di Tomcat non corrisponde alla porta privata dell'endpoint per il traffico nella macchina virtuale, è necessario modificare la porta privata dell'endpoint affinché corrisponda alla porta di ascolto di Tomcat.   
  2. Se il problema è causato dal firewall o dagli iptable, aggiungere le seguenti righe a /etc/sysconfig/iptables. La seconda riga è necessaria solo per il traffico https:  

      -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT

      -A INPUT -p tcp -m tcp --dport 443 -j ACCEPT  

     > [!IMPORTANT]
     > Assicurarsi che le righe precedenti siano posizionate sopra eventuali righe che limiterebbero globalmente l'accesso, come la seguente: -A INPUT -j REJECT --reject-with icmp-host-prohibited



Per ricaricare gli iptable, eseguire il seguente comando:

    service iptables restart

Questa installazione è stata effettuata su CentOS 6.3.

### <a name="permission-denied-when-you-upload-project-files-to-varlibtomcat7webapps"></a>Autorizzazione negata quando si caricano i file di progetto in /var/lib/tomcat7/webapps/
#### <a name="symptom"></a>Sintomo
  Quando si usa un client FTP sicuro (ad esempio FileZilla) per connettersi alla macchina virtuale e passare a /var/lib/tomcat7/webapps/ per pubblicare il sito, si ottiene un messaggio di errore simile al seguente:  

     status:    Listing directory /var/lib/tomcat7/webapps
     Command:    put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
     Error:    /var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
     Error:    File transfer failed
#### <a name="possible-root-cause"></a>Possibile causa principale
  Non si dispone delle autorizzazioni di accesso alla cartella /var/lib/tomcat7/webapps.  
#### <a name="solution"></a>Soluzione  
  È necessario ottenere l'autorizzazione dall'account radice. È possibile cambiare la proprietà di tale cartella dalla radice al nome utente usato per il provisioning della macchina. Di seguito è riportato un esempio con il nome dell'account azureuser:  

     sudo chown azureuser -R /var/lib/tomcat7/webapps

  Usare l'opzione -R per applicare le autorizzazioni per tutti i file anche all'interno di una directory.  

  Questo comando funziona anche per le directory. L'opzione -R modifica le autorizzazioni per tutti i file e le directory all'interno della directory. Di seguito è fornito un esempio:  

     sudo chown -R username:group directory  

  Questo comando cambia la proprietà (sia utente che gruppo) per tutti i file e le directory che si trovano all'interno della directory stessa.  

  Il comando seguente modifica l'autorizzazione solo per la directory della cartella. I file e cartelle all'interno della directory non vengono modificati.  

     sudo chown username:group directory

[1]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-18.png
