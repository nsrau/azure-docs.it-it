<properties 
	pageTitle="Caricamento di un sito Web Java personalizzato in Azure" 
	description="In questa esercitazione viene illustrato come caricare un'applicazione web di Java personalizzata in Azure applicazione servizio Web Apps." 
	services="app-service\web" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="03/04/2016" 
	ms.author="robmcm"/>

# Caricamento di un sito Web Java personalizzato in Azure

In questo argomento viene illustrato come caricare un'applicazione web di Java personalizzata di [servizio App Azure] applicazioni Web. Sono incluse informazioni applicabili a qualsiasi sito Web Java e vengono forniti alcuni esempi per specifiche applicazioni.

Si noti che Azure fornisce un mezzo per la creazione di applicazioni web Java utilizzando l'interfaccia utente di configurazione del portale di Azure e Azure Marketplace, come descritto in [creare un'applicazione web Java nel servizio App di Azure](web-sites-java-get-started.md). Questa esercitazione è adatta per gli scenari in cui si preferisce non usare l'interfaccia di configurazione del Portale di Azure o il Marketplace di Azure.

## Linee guida per la configurazione

Di seguito sono descritte le impostazioni previste per i siti Web Java personalizzati in Azure.

- La porta HTTP usata dal processo Java viene assegnata in modo dinamico. Il processo deve usare la porta indicata dalla variabile di ambiente `HTTP_PLATFORM_PORT`.
- Tutte le porte di ascolto diverse dal listener HTTP devono essere disabilitate. In Tomcat questo include le porte di arresto, HTTPS e AJP.
- Il contenitore deve essere configurato solo per il traffico IPv4.
- Il comando **startup** per l'applicazione deve essere impostato nella configurazione.
- Le applicazioni che richiedono directory con autorizzazioni di scrittura devono essere situate nella directory del contenuto del sito Web di Azure, ovvero **D:\\home**. La variabile di ambiente `HOME` fa riferimento a D:\\home.  

Le variabili di ambiente possono essere impostate come richiesto nel file web.config.

## Configurazione httpPlatform in web.config

Le informazioni riportate di seguito descrivono il formato **httpPlatform** all'interno di web.config.
                                 
**arguments** (impostazione predefinita ""): argomenti per l'eseguibile o lo script specificato nell'impostazione **processPath**.

Esempi (con **processPath** incluso):

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\catalina.bat"
    arguments="start"
    
    processPath="%JAVA_HOME\bin\java.exe"
    arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP\_PLATFORM\_PORT% -Djetty.base=";%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115"; -jar ";%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar";"


**processPath**: percorso dell'eseguibile o dello script che avvierà un processo in ascolto delle richieste HTTP.

Esempi:


    processPath="%JAVA_HOME%\bin\java.exe"

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\startup.bat"

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\catalina.bat"
                                                                                       
**rapidFailsPerMinute** (impostazione predefinita 10): numero di blocchi al minuto consentiti per il processo specificato in **processPath**. Se questo limite viene superato, **HttpPlatformHandler** interromperò l'avvio del processo per il resto del minuto.
                                    
**requestTimeout** (impostazione predefinita "00:02:00"): periodo di tempo per cui **HttpPlatformHandler** attende una risposta dal processo in ascolto su `%HTTP_PLATFORM_PORT%`.

**startupRetryCount** (Default=10): numero di volte per cui **HttpPlatformHandler** tenterà di avviare il processo specificato in **processPath**. Per informazioni più dettagliate, vedere **startupTimeLimit**.

**startupTimeLimit** (impostazione predefinita 10 secondi): tempo per cui **HttpPlatformHandler** attende che l'eseguibile/script avvii un processo in ascolto sulla porta. Se questo limite di tempo viene superato, **HttpPlatformHandler** terminerà il processo e tenterà di riavviarlo il numero di volte indicato da **startupRetryCount**.
                                                                                      
**stdoutLogEnabled** (impostazione predefinita "true"): se è impostato su true, **stdout** e **stderr** per il processo specificato nell'impostazione **processPath** verranno reindirizzati al file specificato in **stdoutLogFile** (vedere la sezione relativa a **stdoutLogFile**).
                                    
**stdoutLogFile** (impostazione predefinita "d:\\home\\LogFiles\\httpPlatformStdout.log"): percorso file assoluto per cui verranno registrati **stdout** e **stderr** dal processo specificato in **processPath**.
                                    
> [AZURE.NOTE] `%HTTP_PLATFORM_PORT%` è un segnaposto speciale che deve essere specificato come parte di **arguments** o dell'elenco **httpPlatform** **environmentVariables**. Verranno sostituiti da una porta generata internamente da **HttpPlatformHandler** affinché il processo specificato in **processPath** possa restare in ascolto su questa porta.

## Distribuzione

I siti Web basati su Java possono essere distribuiti facilmente con quasi tutti i metodi usati per le applicazioni Web basate su Internet Information Services (IIS). FTP, Git e Kudu sono supportati come meccanismi di distribuzione, così come la funzionalità SCM integrata per i siti Web. WebDeploy è supportato come protocollo ma non è adatto negli scenari di utilizzo che prevedono la distribuzione di un sito Web Java poiché Java non è sviluppato in Visual Studio.

## Esempi di configurazione delle applicazioni

Per le applicazioni seguenti vengono forniti un file web.config e la configurazione dell'applicazione come esempi per illustrare come abilitare l'applicazione Java nei siti Web di Azure.

### Tomcat
Con Siti Web di Azure vengono fornite due varianti Tomcat, ma è comunque possibile caricare istanze specifiche del cliente. Di seguito è riportato un esempio di installazione di Tomcat con una Java Virtual Machine (JVM) diversa.

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	      <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%HOME%\site\wwwroot\bin\tomcat\bin\startup.bat" 
	        arguments="">
	      <environmentVariables>
	        <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
	        <environmentVariable name="CATALINA_HOME" value="%HOME%\site\wwwroot\bin\tomcat" />
	        <environmentVariable name="JRE_HOME" value="%HOME%\site\wwwroot\bin\java" /> <!-- optional, if not specified, this will default to %programfiles%\Java -->
	        <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
	      </environmentVariables>
	    </httpPlatform>
	  </system.webServer>
	</configuration>

Sul lato Tomcat è necessario apportare alcune modifiche alla configurazione. È necessario modificare il file server.xml per impostare quanto segue:

-	Porta di arresto = -1
-	Porta connettore HTTP = ${port.http}
-	Indirizzo connettore HTTP = "127.0.0.1"
-	Impostare come commento i connettori HTTPS e AJP
-	L'impostazione IPv4 può essere configurata anche nel file catalina.properties in cui è possibile aggiungere `java.net.preferIPv4Stack=true`
    
Chiamate Direct3d non sono supportate in applicazione servizio Web App. Per disabilitarle, aggiungere l'opzione Java seguente nel caso in cui l'applicazione esegua chiamate di tale tipo: `-Dsun.java2d.d3d=false`

### Jetty

Come nel caso di Tomcat, i clienti possono caricare istanze personalizzate per Jetty. Se viene eseguita l'installazione completa di Jetty, la configurazione sarà analoga alla seguente:

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	      <add name="httppPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" 
	         arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP_PLATFORM_PORT% -Djetty.base=";%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115"; -jar ";%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar";"
	        startupTimeLimit="20"
		  startupRetryCount="10"
		  stdoutLogEnabled="true">
	    </httpPlatform>
	  </system.webServer>
	</configuration>

La configurazione Jetty deve essere modificata in start.ini per impostare `java.net.preferIPv4Stack=true`.

### Springboot
Per ottenere un’applicazione Springboot in esecuzione, è necessario caricare il file JAR o WAR e aggiungere il seguente file web.config. Il file web.config viene inserito nella cartella wwwroot. Modificare gli argomenti in modo da puntare al file JAR, nell'esempio seguente il file con estensione JAR si trova anche nella cartella wwwroot.

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	      <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%JAVA_HOME%\bin\java.exe"
	        arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar ";%HOME%\site\wwwroot\my-web-project.jar";">
	    </httpPlatform>
	  </system.webServer>
	</configuration>


### Hudson

Nel test sono stati utilizzati il file WAR di Hudson 3.1.2 e l'istanza predefinita di Tomcat 7.0.50, senza utilizzare l'interfaccia utente per la configurazione. Poiché Hudson è uno strumento per la compilazione di software, è consigliabile installarlo su istanze dedicate nelle quali sia possibile impostare il flag **AlwaysOn** sul sito.

1. Nella radice del sito Web di Azure, ad esempio **d:\\home\\site\\wwwroot**, creare una directory **webapps**, se non è già presente, quindi inserire Hudson.war in **d:\\home\\site\\wwwroot\\webapps**.
2. Scaricare apache maven 3.0.5 (compatibile con Hudson) e posizionarlo in **d:\\home\\site\\wwwroot**.
3. Creare il file web.config in **d:\\home\\site\\wwwroot** e incollarvi il contenuto seguente:
	
		<?xml version="1.0" encoding="UTF-8"?>
		<configuration>
		  <system.webServer>
		    <handlers>
		      <add name="httppPlatformHandler" path="*" verb="*" 
		modules="httpPlatformHandler" resourceType="Unspecified" />
		    </handlers>
		    <httpPlatform processPath="%AZURE_TOMCAT7_HOME%\bin\startup.bat"
		startupTimeLimit="20"
		startupRetryCount="10">
		<environmentVariables>
		  <environmentVariable name="HUDSON_HOME" 
		value="%HOME%\site\wwwroot\hudson_home" />
		  <environmentVariable name="JAVA_OPTS" 
		value="-Djava.net.preferIPv4Stack=true -Duser.home=%HOME%/site/wwwroot/user_home -Dhudson.DNSMultiCast.disabled=true" />
		</environmentVariables>            
		    </httpPlatform>
		  </system.webServer>
		</configuration>

    A questo punto è possibile riavviare il sito Web per applicare le modifiche. Connettersi a http://yourwebapp/hudson per avviare Hudson.

4. Al termine della configurazione automatica di Hudson, dovrebbe essere visualizzata la schermata seguente:

    ![Hudson](./media/web-sites-java-custom-upload/hudson1.png)
    
5. Accedere alla pagina di configurazione di Hudson: fare clic su **Manage Hudson**, quindi fare clic su **configurazione sistema**.
6. Configurare JDK come mostrato di seguito:

	![Configurazione di Hudson](./media/web-sites-java-custom-upload/hudson2.png)

7. Configurare Maven come mostrato di seguito:

	![Configurazione di Maven](./media/web-sites-java-custom-upload/maven.png)

8. Salvare le impostazioni. A questo punto Hudson dovrebbe essere configurato e pronto per l'uso.

Per ulteriori informazioni su Hudson, vedere [http://hudson-ci.org](http://hudson-ci.org).

### Liferay

Liferay è supportato in applicazione servizio Web App. Poiché Liferay può richiedere una quantità di memoria considerevole, è necessario eseguire il sito su un processo di lavoro dedicato di medie o grandi dimensioni, che sia in grado di fornire memoria sufficiente. Liferay richiede inoltre alcuni minuti per l'avvio. Per questa ragione è consigliabile impostare il sito su **Always On**.

Mediante Liferay 6.1.2 Community Edition GA3 in bundle con Tomcat sono stati modificati i file seguenti dopo il download di Liferay:

**Server.xml**

- Impostare la porta di arresto su -1.
- Impostare il connettore HTTP su `<Connector port="${port.http}" protocol="HTTP/1.1" connectionTimeout="600000" address="127.0.0.1" URIEncoding="UTF-8" />`
- Impostare come commento il connettore AJP.

Nella cartella **liferay\\tomcat-7.0.40\\webapps\\ROOT\\WEB-INF\\classes** creare un file denominato **portal-ext.properties**. Il file deve contenere una riga, come illustrato di seguito:

    liferay.home=%HOME%/site/wwwroot/liferay

Allo stesso livello di directory della cartella tomcat-7.0.40 creare un file denominato **web.config** con il contenuto seguente:

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	<add name="httpPlatformHandler" path="*" verb="*"
	     modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%HOME%\site\wwwroot\tomcat-7.0.40\bin\catalina.bat" 
	                  arguments="run" 
	                  startupTimeLimit="10" 
	                  requestTimeout="00:10:00" 
	                  stdoutLogEnabled="true">
	      <environmentVariables>
	  <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
	  <environmentVariable name="CATALINA_HOME" value="%HOME%\site\wwwroot\tomcat-7.0.40" />
	        <environmentVariable name="JRE_HOME" value="D:\Program Files\Java\jdk1.7.0_51" /> 
	        <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
	      </environmentVariables>
	    </httpPlatform>
	  </system.webServer>
	</configuration>

Nel blocco **httpPlatform**, la variabile **requestTimeout** è impostata su "00:10:00". Questo valore può essere ridotto ma in questo caso è probabile che si verifichino errori di timeout durante il bootstrap di Liferay. Se il valore viene modificato, è necessario modificare anche **connectionTimeout** nel file server.xml di Tomcat.

Si noti che nel file web.config riportato sopra la variabile di ambiente JRE\_HOME è impostata in modo da fare riferimento alla versione di JDK a 64 bit. La versione predefinita è a 32 bit, ma poiché Liferay può richiedere livelli elevati di memoria è consigliabile usare il JDK a 64 bit.

Dopo avere apportato le modifiche indicate, riavviare il sito Web che esegue Liferay, quindi aprire http://yourwebapp. Il portale Liferay è disponibile dalla radice del sito Web.

## Passaggi successivi

Per ulteriori informazioni su Liferay, vedere [http://www.liferay.com](http://www.liferay.com).

Per ulteriori informazioni su Java, vedere il [Centro per sviluppatori di Java](/develop/java/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
 
<!-- External Links -->
[servizio App Azure]: http://go.microsoft.com/fwlink/?LinkId=529714

<!---HONumber=AcomDC_0309_2016-->