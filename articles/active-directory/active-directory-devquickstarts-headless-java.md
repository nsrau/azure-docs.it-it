<properties
	pageTitle="Introduzione a Java per Azure AD | Microsoft Azure"
	description="Come compilare un'app della riga di comando Java che faccia accedere gli utenti a un'API."
	services="active-directory"
	documentationCenter="java"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
  ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="article"
	ms.date="02/19/2016"
	ms.author="brandwe"/>


# Uso dell'app della riga di comando Java per accedere a un'API con Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD facilita e semplifica l'outsourcing della gestione delle identità delle app Web, fornendo un unico account di accesso e disconnessione solo con poche righe di codice. Nelle app Web Java, è possibile eseguire questa operazione utilizzando l’implementazione Microsoft di ADAL4J basato sulla community.

  ADAL4J verrà usato per:
- Connettere l'utente all'app usando Azure AD come provider di identità.
- Visualizzare alcune informazioni relative all'utente.
- Disconnettere l'utente dall'app.

A questo scopo è necessario:

1. Registrare un'applicazione con Azure AD.
2. Configurare l'app per usare la libreria ADAL per Java (ADAL4J).
3. Usare la libreria ADAL4J per inviare le richieste di accesso e disconnessione ad Azure AD.
4. Visualizzare dati relativi all'utente.

Per iniziare, [scaricare la struttura dell'app](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/skeleton.zip) o [scaricare l'esempio completato](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect\/archive/complete.zip). Sarà necessario anche un tenant di Azure AD in cui registrare l'applicazione. Se non si ha già un tenant, vedere le [informazioni su come ottenerne uno](active-directory-howto-tenant.md).

## 1\. Registrare un'applicazione con Azure AD
Per consentire all'app di autenticare gli utenti, sarà innanzitutto necessario registrare una nuova applicazione nel tenant.

- Accedere al portale di gestione di Azure.
- Nel pannello di navigazione a sinistra fare clic su **Active Directory**.
- Selezionare il tenant in cui si desidera registrare l'applicazione.
- Fare clic sulla scheda **Applicazioni**, quindi fare clic su aggiungi nel pannello in basso.
- Seguire le istruzioni e creare una nuova **Applicazione Web e/o API Web**.
    - Il **nome** dell'applicazione deve essere una descrizione per gli utenti finali.
    - L'**URL accesso** è l'URL di base dell'app. Il valore predefinito della struttura è `http://localhost:8080/adal4jsample/`.
    - L'**URI ID app** è un identificatore univoco dell'applicazione. Per convenzione si usa `https://<tenant-domain>/<app-name>`, ad esempio `http://localhost:8080/adal4jsample/`.
- Dopo avere completato la registrazione, AAD assegnerà all'app un identificatore client univoco. Poiché questo valore sarà necessario nelle sezioni successive, copiarlo dalla scheda Configura.

Dopo aver eseguito l'accesso al portale per l'app, creare un **Segreto applicazione** per l'applicazione e prenderne nota. perché verrà richiesto a breve.


## 2\. Configurare l'app per usare la libreria ADAL4J e i prerequisiti tramite Maven
In questo caso, verrà configurata la libreria ADAL4J per l'uso del protocollo di autenticazione OpenID Connect. La libreria ADAL4J verrà usata, tra le altre cose, per inviare le richieste di accesso e disconnessione, gestire la sessione dell'utente e ottenere informazioni sull'utente.

-	Nella directory radice del progetto aprire o creare `pom.xml`, individuare `// TODO: provide dependencies for Maven` e sostituirlo con il codice seguente:

```Java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.microsoft.azure</groupId>
	<artifactId>public-client-adal4j-sample</artifactId>
	<packaging>jar</packaging>
	<version>0.0.1-SNAPSHOT</version>
	<name>public-client-adal4j-sample</name>
	<url>http://maven.apache.org</url>
	<properties>
		<spring.version>3.0.5.RELEASE</spring.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>com.microsoft.azure</groupId>
			<artifactId>adal4j</artifactId>
			<version>1.1.2</version>
		</dependency>
		<dependency>
			<groupId>com.nimbusds</groupId>
			<artifactId>oauth2-oidc-sdk</artifactId>
			<version>4.5</version>
		</dependency>
		<dependency>
			<groupId>org.json</groupId>
			<artifactId>json</artifactId>
			<version>20090211</version>
		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>
			<version>3.0.1</version>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-log4j12</artifactId>
			<version>1.7.5</version>
		</dependency>
</dependencies>
	<build>
		<finalName>public-client-adal4j-sample</finalName>
		<plugins>
		        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>1.2.1</version>
            <configuration>
                <mainClass>PublicClient</mainClass>
            </configuration>
        </plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<configuration>
					<source>1.7</source>
					<target>1.7</target>
					<encoding>UTF-8</encoding>
				</configuration>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-dependency-plugin</artifactId>
				<executions>
					<execution>
						<id>install</id>
						<phase>install</phase>
						<goals>
							<goal>sources</goal>
						</goals>
					</execution>
				</executions>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-resources-plugin</artifactId>
				<version>2.5</version>
				<configuration>
					<encoding>UTF-8</encoding>
				</configuration>
			</plugin>
			<plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
      </plugin>
      <plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-assembly-plugin</artifactId>
  <configuration>
    <archive>
      <manifest>
        <mainClass>PublicClient</mainClass>
      </manifest>
    </archive>
  </configuration>
</plugin>
		</plugins>
	</build>

</project>


```



## 3\. Creare il file Java PublicClient

Come indicato sopra, verrà usata l'API Graph per ottenere i dati relativi all'utente connesso. Per semplificare la procedura, sarà necessario creare un file per rappresentare un **Oggetto directory** e un singolo file per rappresentare l'**Utente**, affinché sia possibile usare il modello orientato a oggetti di Java.

1. Creare un file denominato `DirectoryObject.java` che verrà usato per archiviare i dati relativi a qualsiasi oggetto directory (sarà possibile riutilizzarlo in seguito per eventuali altre query dell'API Graph). È possibile tagliare e incollare dal codice seguente:

```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

import javax.naming.ServiceUnavailableException;

import com.microsoft.aad.adal4j.AuthenticationContext;
import com.microsoft.aad.adal4j.AuthenticationResult;

public class PublicClient {

    private final static String AUTHORITY = "https://login.microsoftonline.com/common/";
    private final static String CLIENT_ID = "2a4da06c-ff07-410d-af8a-542a512f5092";

    public static void main(String args[]) throws Exception {

        try (BufferedReader br = new BufferedReader(new InputStreamReader(
                System.in))) {
            System.out.print("Enter username: ");
            String username = br.readLine();
            System.out.print("Enter password: ");
            String password = br.readLine();

            AuthenticationResult result = getAccessTokenFromUserCredentials(
                    username, password);
            System.out.println("Access Token - " + result.getAccessToken());
            System.out.println("Refresh Token - " + result.getRefreshToken());
            System.out.println("ID Token - " + result.getIdToken());
        }
    }

    private static AuthenticationResult getAccessTokenFromUserCredentials(
            String username, String password) throws Exception {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(AUTHORITY, false, service);
            Future<AuthenticationResult> future = context.acquireToken(
                    "https://graph.windows.net", CLIENT_ID, username, password,
                    null);
            result = future.get();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }
}


```


##Compilare ed eseguire l'esempio

Passare alla directory radice ed eseguire il comando seguente per compilare l'esempio appena creato con `maven`. Verrà usato il file `pom.xml` scritto per le dipendenze.

`$ mvn package`

Nella directory `/targets` dovrebbe essere presente un file `adal4jsample.war`. È possibile eseguire la distribuzione nel contenitore Tomcat e visitare l'URL

`http://localhost:8080/adal4jsample/`


> [AZURE.NOTE] 
La distribuzione di un file WAR con i server Tomcat più recenti è un'operazione semplice. È sufficiente passare a `http://localhost:8080/manager/` e seguire le istruzioni per caricare il file `adal4jsample.war`. Verrà eseguita la distribuzione automatica con l'endpoint corretto.

##Passaggi successivi

Congratulazioni. È stata compilata un'applicazione Java funzionante che può autenticare gli utenti, chiamare in modo sicuro le API Web usando OAuth 2.0 e ottenere informazioni di base sull'utente. Se non si è ancora popolato il tenant con alcuni utenti, ora è possibile farlo.

Come riferimento, l'esempio completato (senza i valori di configurazione) [ è disponibile in un file con estensione zip](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/complete.zip). In alternativa, è possibile clonarlo da GitHub:

```git clone --branch complete https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect.git```

<!---HONumber=AcomDC_0302_2016-->