---
title: Creare un&quot;app Web Java e MySQL in Azure | Microsoft Docs
description: Informazioni su come ottenere un&quot;app Java che si connette al servizio di database MySQL di Azure e funziona nel Servizio app di Azure.
services: app-service\web
documentationcenter: Java
author: bbenz
manager: jeffsand
editor: jasonwhowell
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 05/06/2017
ms.author: bbenz
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fab7759154b38b8043a17c933f896d7af9514c85
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---
# <a name="build-a-java-and-mysql-web-app-in-azure"></a>Creare un'app Web Java e MySQL in Azure
Questa esercitazione illustra come creare un'app Web Java in Azure e connetterla a un database MySQL. Il primo passaggio consiste nel clonare un'applicazione nel computer locale e renderla funzionante con un'istanza locale di MySQL.
Il passaggio successivo consiste nel configurare i servizi di Azure per l'app Java e MySQL, quindi distribuire l'applicazione in un Servizio app di Azure.
Al termine, si disporrà di un'applicazione To-Do List in esecuzione su Azure che si connette al servizio di database MySQL di Azure.

![App Java in esecuzione nel Servizio app di Azure](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

## <a name="before-you-begin"></a>Prima di iniziare

Prima di eseguire l'esempio, installare in locale questi prerequisiti:

1. [Scaricare e installare Git](https://git-scm.com/)
1. [Scaricare e installare Java 7 o una versione successiva](http://Java.net/downloads.Java)
1. [Scaricare e installare Maven](https://maven.apache.org/download.cgi)
1. [Scaricare, installare e avviare MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 
1. [Scaricare e installare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql-database"></a>Preparare il database MySQL in locale

In questo passaggio, si crea un database in un server locale di MySQL da usare in questa esercitazione.

### <a name="connect-to-mysql-server"></a>Connettersi al server MySQL
Connettersi al server MySQL locale dalla riga di comando:

```bash
mysql -u root -p
```

Se il comando viene eseguito correttamente, il server MySQL è già in esecuzione. In caso contrario, assicurarsi che il server MySQL locale sia stato avviato seguendo la [procedura successiva all'installazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

Se viene richiesto di immettere una password, immettere la password per l'account `root`. Se non si ricorda la password dell'account radice, vedere [MySQL: procedura per reimpostare la password radice](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).


### <a name="create-a-database-and-table"></a>Creare un database e una tabella

Nel prompt `mysql` creare un database e una tabella per gli elementi attività.

```sql
CREATE DATABASE todoItemDb;
USE todoItemDb;
CREATE TABLE ITEMS ( id varchar(255), name varchar(255), category varchar(255), complete bool);
```

Chiudere la connessione al server digitando `quit`.

```sql
quit
```

## <a name="create-local-java-application"></a>Creare un'applicazione Java locale
In questo passaggio, clonare un repository GitHub, configurare la connessione al database MySQL ed eseguire l'app in locale. 

### <a name="clone-the-sample"></a>Clonare l'esempio

Dal prompt dei comandi, passare alla directory di lavoro.  

Eseguire i comandi seguenti per clonare il repository di esempio. 

```bash
git clone https://github.com/bbenz/azure-mysql-java-todo-app
```

Successivamente, configurare lombok.jar seguendo la procedura nel file Leggimi del repository.


### <a name="configure-mysql-connection"></a>Configurare la connessione di MySQL

Questa applicazione usa il plug-in Maven Jetty per eseguire l'applicazione in locale e connettersi al database MySQL.
Per abilitare l'accesso all'istanza locale di MySQL, impostare l'ID utente e la password di MySQL locali in WebContent/WEB-INF/jetty-env.xml.

Aggiornare i valori Utente e Password con l'ID utente e la password dell'istanza di MySQL locale:

```
<Configure id='wac' class="org.eclipse.jetty.webapp.WebAppContext">
  <New id="itemdb" class="org.eclipse.jetty.plus.jndi.Resource">
     <Arg></Arg>
     <Arg>jdbc/todoItemDb</Arg>
     <Arg>
        <New class="com.mysql.jdbc.jdbc2.optional.MysqlConnectionPoolDataSource">
           <Set name="Url">jdbc:mysql://localhost:3306/itemdb</Set>
           <Set name="User">root</Set>
           <Set name="Password"></Set>
        </New>
     </Arg>
    </New>
</Configure>

```

&gt; [!NOTE]
&gt; Per informazioni su come Jetty usa il file `jetty-env.xml`, vedere [Jetty XML Reference](http://www.eclipse.org/jetty/documentation/9.4.x/jetty-env-xml.html) (Riferimenti di Jetty XML).
&gt; &gt;

### <a name="run-the-sample"></a>Eseguire l'esempio

Usare un comando Maven per eseguire l'esempio: 

```bash
mvn package jetty:run
```

Successivamente, passare a `http://localhost:8080` in un browser. Nella pagina aggiungere alcune attività.

Per arrestare l'applicazione in qualsiasi momento, digitare `Ctrl`+`C` nel prompt dei comandi. 

## <a name="create-a-mysql-database-in-azure"></a>Creare un database MySQL in Azure

In questo passaggio viene creato un database MySQL nel [database di Azure per MySQL (anteprima)](/azure/mysql). Successivamente verrà configurata l'applicazione Java per la connessione al database.

### <a name="log-in-to-azure"></a>Accedere ad Azure

Usare l'interfaccia della riga di comando di Azure 2.0 in una finestra terminale per creare le risorse necessarie per ospitare l'applicazione Java nel Servizio app di Azure. Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/#login) e seguire le istruzioni visualizzate. 

```azurecli 
az login 
``` 

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md) con il comando [az group create](/cli/azure/group#create). Un gruppo di risorse di Azure è un contenitore logico in cui vengono distribuite e gestite risorse di Azure come app Web, database e account di archiviazione. 

L'esempio seguente crea un gruppo di risorse nell'area Europa settentrionale:

```azurecli
az group create --name myResourceGroup  --location "North Europe"
```

Per visualizzare i valori per `--location`, usare il comando [az appservice list-locations](/cli/azure/appservice#list-locations).

### <a name="create-a-mysql-server"></a>Creare un server MySQL

Creare un server nel database di Azure per MySQL (anteprima) con il comando [az mysql server create](/cli/azure/mysql/server#create).

Sostituire il segnaposto `&lt;mysql_server_name&gt;` con il nome univoco del proprio server MySQL. Questo nome fa parte del nome host del server MySQL, `&lt;mysql_server_name&gt;.database.windows.net`, pertanto deve essere univoco a livello globale. Sostituire anche `&lt;admin_user&gt;` e `&lt;admin_password&gt;` con i valori desiderati.

```azurecli
az mysql server create --name &lt;mysql_server_name&gt; --resource-group myResourceGroup --location "North Europe" --user &lt;admin_user&gt; --password &lt;admin_password&gt;
```

Al termine della creazione del server MySQL, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente:

```json
{
  "administratorLogin": "&lt;admin_user&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;mysql_server_name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/&lt;mysql_server_name&gt;",
  "location": "northeurope",
  "name": "&lt;mysql_server_name&gt;",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-a-server-firewall"></a>Configurare un firewall del server

Creare una regola del firewall per il server MySQL per consentire le connessioni client tramite il comando [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#create). 

```azurecli
az mysql server firewall-rule create --name allIPs --server &lt;mysql_server_name&gt; --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

&gt; [!NOTE]
&gt; Il database di Azure per MySQL (anteprima) non consente ancora di abilitare le connessioni solo dai servizi di Azure. Poiché gli indirizzi IP in Azure vengono assegnati dinamicamente, per il momento è preferibile abilitarli tutti. Poiché il servizio è in anteprima, presto verranno abilitati metodi migliori per la protezione del database.
&gt; &gt;

### <a name="connect-to-the-mysql-server"></a>Connettersi al server MySQL

Nella finestra terminale connettersi al server MySQL in Azure. Usare il valore specificato precedentemente per `&lt;admin_user&gt;` e `&lt;mysql_server_name&gt;`.

```bash
mysql -u &lt;admin_user&gt;@&lt;mysql_server_name&gt; -h &lt;mysql_server_name&gt;.database.windows.net -P 3306 -p
```

### <a name="create-a-database-and-table-in-the-azure-mysql-service"></a>Creare un database e una tabella nel servizio MySQL di Azure

Nel prompt `mysql` creare un database e una tabella per gli elementi attività.

```sql
CREATE DATABASE todoItemDb;
USE todoItemDb;
CREATE TABLE ITEMS ( id varchar(255), name varchar(255), category varchar(255), complete bool);
```

### <a name="create-a-user-with-permissions"></a>Creare un utente con autorizzazioni

Creare un utente del database e concedergli tutti i privilegi nel database `todoItemDb`. Sostituire i segnaposto `&lt;Javaapp_user&gt;` e `&lt;Javaapp_password&gt;` con il proprio nome univoco dell'app.

```sql
CREATE USER '&lt;Javaapp_user&gt;' IDENTIFIED BY '&lt;Javaapp_password&gt;'; 
GRANT ALL PRIVILEGES ON todoItemDb.* TO '&lt;Javaapp_user&gt;';
```

Chiudere la connessione al server digitando `quit`.

```sql
quit
```

### <a name="configure-the-local-mysql-connection-with-the-new-azure-mysql-service"></a>Configurare la connessione a MySQL locale con il nuovo servizio MySQL di Azure

In questo passaggio l'applicazione Java viene connessa al database MySQL creato nel database di Azure per MySQL (anteprima). 

Per abilitare l'accesso dall'applicazione locale al servizio MySQL di Azure, impostare il nuovo endpoint, l'ID utente e la di password MySQL in WebContent/WEB-INF/jetty-env.xml:

```
<Configure id='wac' class="org.eclipse.jetty.webapp.WebAppContext">
  <New id="itemdb" class="org.eclipse.jetty.plus.jndi.Resource">
     <Arg></Arg>
     <Arg>jdbc/todoItemDb</Arg>
     <Arg>
        <New class="com.mysql.jdbc.jdbc2.optional.MysqlConnectionPoolDataSource">
           <Set name="Url">jdbc:mysql:<paste the endpoint for the Azure MySQL Service>/itemdb</Set>
           <Set name="User">Azure MySQL userID</Set>
           <Set name="Password">Azure MySQL Password</Set>
        </New>
     </Arg>
    </New>
</Configure>

```

Salvare le modifiche.

## <a name="test-the-application"></a>Test dell'applicazione

Usare lo stesso comando Maven come indicato in precedenza per eseguire nuovamente l'esempio in locale, questa volta eseguendo la connessione al servizio MySQL di Azure: 

```bash
mvn package jetty:run
```

Andare a `http://localhost:8080` in un browser. Se la pagina viene caricata senza errori significa che l'applicazione Java si connette al database MySQL in Azure. 

In questa pagina non dovrebbe essere presente Add a few tasks (Aggiungi alcune attività).

Per arrestare l'applicazione in qualsiasi momento, digitare `Ctrl`+`C` nel terminale. 

### <a name="secure-sensitive-data"></a>Proteggere i dati sensibili

Assicurarsi che per i dati sensibili in `WebContent/WEB-INF/jetty-env.xml` non venga eseguito il commit in Git.

A tale scopo, aprire `.gitignore` dalla radice del repository e aggiungere `WebContent/WEB-INF/jetty-env.xml` in una nuova riga. Salvare le modifiche.

Eseguire il commit delle modifiche per `.gitignore`.

```bash
git add .gitignore
git commit -m "keep sensitive data in WebContent/WEB-INF/jetty-env.xml out of git"
```

## <a name="deploy-the-java-application-to-azure"></a>Distribuire l'applicazione Java in Azure
Successivamente l'applicazione Java viene distribuita in un Servizio app di Azure.

### <a name="create-an-appservice-plan"></a>Creare un piano di servizio app

Creare un piano di servizio app con il comando [az appservice plan create](/cli/azure/appservice/plan#create). 

&gt; [!NOTE] 
&gt; Un piano di servizio app rappresenta la raccolta delle risorse fisiche usate per ospitare le app. Tutte le applicazioni assegnate a un piano di servizio app condividono le risorse definite dal piano, in modo da consentire un risparmio sui costi quando si ospitano più app. 
&gt;&gt; i piani di servizio app definiscono: &gt; &gt; * l'area (Europa settentrionale, Stati Uniti orientali, Asia sud-orientale) &gt; * le dimensioni dell'istanza (piccola, media, grande) &gt; * il numero di scala (uno, due o tre istanze e così via.) &gt; * la SKU (Gratuito, Condiviso, Basic, Standard, Premium)&gt; 

L'esempio seguente crea un piano di servizio app denominato `myAppServicePlan` usando il piano tariffario **GRATUITO**:

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Al termine della creazione del piano di servizio app, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente:

```json 
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
``` 

### <a name="create-an-azure-web-app"></a>Creare un'app Web di Azure

È stato creato un piano di servizio app `myAppServicePlan` e ora occorre creare un'app Web di Azure al suo interno. L'app Web fornisce uno spazio host per distribuire il codice e un URL in cui visualizzare l'applicazione distribuita. Usare il comando [az appservice web create](/cli/azure/appservice/web#create) per creare l'app Web. 

Nel comando seguente sostituire il segnaposto `&lt;app_name&gt;` con il nome univoco dell'app. Questo nome verrà usato come parte del nome di dominio predefinito per l'app Web, pertanto è necessario che sia univoco rispetto a tutte le app presenti in Azure. In un secondo momento è possibile eseguire il mapping di qualsiasi voce DNS personalizzata all'app Web prima di esporla agli utenti. 

```azurecli
az appservice web create --name &lt;app_name&gt; --resource-group myResourceGroup --plan myAppServicePlan
```

Al termine della creazione dell'app Web, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app_name&gt;.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "&lt;app_name&gt;.azurewebsites.net",
    "&lt;app_name&gt;.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "&lt;app_name&gt;.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    &lt; JSON data removed for brevity. &gt;
}
```

### <a name="set-the-java-version-the-java-application-server-type-and-the-application-server-version"></a>Impostare la versione di Java, il tipo di server applicazioni Java e la versione del server applicazioni

Impostare la versione Java, il server app Java (contenitore) e la versione del contenitore usando il [az appservice web config update](/cli/azure/appservice/web/config#update).

Il comando seguente imposta la versione Java a 8, il server app Java su Jetty e la versione di Jetty sulla versione più recente di Jetty 9.3.

```azurecli
az appservice web config update --name &lt;app_name&gt; --resource-group myResourceGroup --java-version 1.8 --java-container Jetty --java-container-version 9.3
```


### <a name="get-credentials-for-deployment-to-the-web-app-using-ftp"></a>Ottenere le credenziali per la distribuzione nell'app Web tramite FTP 

È possibile distribuire l'applicazione nel Servizio app di Azure attraverso varie soluzioni, tra cui FTP, Git locale, GitHub, Visual Studio Team Services e BitBucket. Per questo esempio, viene usato Maven per compilare un file con estensione WAR e l'FTP per distribuire il file con estensione WAR nell'applicazione Web

Per determinare quali credenziali passare in un comando FTP per l'app Web, usare il comando [az appservice web deployment list-publishing-profiles](https://docs.microsoft.com/cli/azure/appservice/web/deployment#list-publishing-profiles), come indicato di seguito: 

```azurecli

az appservice web deployment list-publishing-profiles --name &lt;app_name&gt; --resource-group myResourceGroup --query "[?publishMethod=='FTP'].{URL:publishUrl, Username:userName,Password:userPWD}" --o table

```
### <a name="compile-the-local-application-to-deply-to-the-web-app"></a>Compilare l'applicazione locale per distribuire l'app Web 

Per preparare l'applicazione Java locale all'esecuzione nell'app Web di Azure, ricompilare tutte le risorse nell'applicazione Java in un unico file con estensione WAR pronto per la distribuzione. Passare alla directory in cui si trova il file pom.xml delle applicazioni e digitare:

```bash 
mvn clean package
``` 
Verso la fine del processo del pacchetto Maven, verrà visualizzato il percorso del file con estensione WAR.  L'output dovrebbe essere simile a quanto segue:

```bash

[INFO] Processing war project
[INFO] Copying webapp resources [local-location\GitHub\mysql-java-todo-app\WebContent]
[INFO] Webapp assembled in [1519 msecs]
[INFO] Building war: C:\Users\your\localGitHub\mysql-java-todo-app\target\azure-appservice-mysql-java-sample-0.0.1-SNAPSHOT.war
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------

```

Prendere nota del percorso del file con estensione WAR e usare il metodo FTP preferito per distribuire il file WAR nella cartella WebApps di Jetty.  Si noti che la cartella WebApps di Jetty si trova in /site/wwwroot/webapps in un'app Web di Azure. 

### <a name="browse-to-the-azure-web-app"></a>Passare all'app Web di Azure

Passare a `http://&lt;app_name&gt;.azurewebsites.net/&lt;app_name&gt;` e aggiungere alcune attività all'elenco. 

![App Java in esecuzione nel Servizio app di Azure](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)


**Congratulazioni.** L'app Java basata su dati è in esecuzione nel Servizio app di Azure.
Per aggiornare l'app, ripetere il comando del pacchetto di pulizia Maven e ridistribuire l'app tramite FTP.

## <a name="manage-your-azure-web-app"></a>Gestire l'app Web di Azure

Passare al portale di Azure per visualizzare l'app Web creata tramite l'accesso a [https://portal.azure.com](https://portal.azure.com).

Nel menu a sinistra fare clic su **appservice** e quindi sul nome dell'app Web di Azure.

Si accede così al _pannello_, ovvero una pagina del portale visualizzata in orizzontale, dell'app Web.

Per impostazione predefinita, nel pannello dell'app Web viene aperta la pagina **Panoramica**, che offre una visualizzazione dello stato dell'app. In questa pagina è anche possibile eseguire attività di gestione di base come esplorare, arrestare, avviare, riavviare ed eliminare. Le schede sul lato sinistro del pannello mostrano le diverse pagine di configurazione che è possibile aprire.

Nella pagina **Impostazioni applicazione** 

![Impostazioni applicazione dell'app Web del Servizio app di Azure](./media/app-service-web-tutorial-java-mysql/appservice-web-app-application-settings.png)



Queste schede del pannello mostrano le numerose utili funzionalità che è possibile aggiungere all'app Web. Nell'elenco seguente sono riportate solo alcune delle possibilità:

* Eseguire il mapping di un nome DNS personalizzato
* Associare un certificato SSL personalizzato
* Configurare la distribuzione continua
* Aumentare le prestazioni e il numero di istanze
* Aggiungere l'autenticazione utente

## <a name="more-resources"></a>Altre risorse

- [Esecuzione del mapping di un nome DNS personalizzato esistente con un app Web di Azure](app-service-web-tutorial-custom-domain.md)
- [Associare un certificato SSL personalizzato esistente ad app Web di Azure](app-service-web-tutorial-custom-ssl.md)
- [Script dell'interfaccia della riga di comando delle app Web](app-service-cli-samples.md)</PRE>

