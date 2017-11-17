---
title: Creare un'app Web Java e MySQL in Azure
description: Informazioni su come ottenere un'app Java che si connette al servizio di database MySQL di Azure e funziona nel Servizio app di Azure.
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
ms.topic: tutorial
ms.date: 05/22/2017
ms.author: bbenz
ms.custom: mvc
ms.openlocfilehash: 58e0533db1bb907b0c9a09cdeb7aabbf120d506f
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="build-a-java-and-mysql-web-app-in-azure"></a>Creare un'app Web Java e MySQL in Azure

Questa esercitazione illustra come creare un'app Web Java in Azure e connetterla a un database MySQL. Al termine, verrà creata un'applicazione [Spring Boot](https://projects.spring.io/spring-boot/) che archivia i dati in [Database di Azure per MySQL](https://docs.microsoft.com/azure/mysql/overview) in esecuzione in [App Web del servizio app di Azure](app-service-web-overview.md).

![App Java in esecuzione nel Servizio app di Azure](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un database MySQL in Azure
> * Connettere un'app di esempio al database
> * Distribuire l'app in Azure
> * Aggiornare e ridistribuire l'app
> * Eseguire lo streaming dei log di diagnostica in Azure
> * Monitorare l'app nel portale di Azure


## <a name="prerequisites"></a>Prerequisiti

1. [Scaricare e installare Git](https://git-scm.com/)
1. [Scaricare e installare Java 7 JDK o versione successiva](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
1. [Scaricare, installare e avviare MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql"></a>Preparare MySQL in locale 

In questo passaggio si crea un database in un server locale di MySQL da usare per il test dell'app in locale nel computer.

### <a name="connect-to-mysql-server"></a>Connettersi al server MySQL

In una finestra terminale connettersi al server MySQL locale. È possibile usare questa finestra del terminale per eseguire tutti i comandi presenti in questa esercitazione.

```bash
mysql -u root -p
```

Se viene richiesto di immettere una password, immettere la password per l'account `root`. Se non si ricorda la password dell'account radice, vedere [MySQL: procedura per reimpostare la password radice](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

Se il comando viene eseguito correttamente, il server MySQL è già in esecuzione. In caso contrario, assicurarsi che il server MySQL locale sia stato avviato seguendo la [procedura successiva all'installazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

### <a name="create-a-database"></a>Creare un database 

Nel prompt `mysql` creare un database e una tabella per gli elementi attività.

```sql
CREATE DATABASE tododb;
```

Chiudere la connessione al server digitando `quit`.

```sql
quit
```

## <a name="create-and-run-the-sample-app"></a>Creare ed eseguire l'app di esempio 

In questo passaggio si clona l'app Spring Boot di esempio, la si configura per l'uso del database MySQL locale e la si esegue nel computer. 

### <a name="clone-the-sample"></a>Clonare l'esempio

Nella finestra del terminale passare alla directory di lavoro e clonare il repository di esempio. 

```bash
git clone https://github.com/azure-samples/mysql-spring-boot-todo
```

### <a name="configure-the-app-to-use-the-mysql-database"></a>Configurare l'app per l'uso del database MySQL

Aggiornare `spring.datasource.password` e il valore in *spring-boot-mysql-todo/src/main/resources/application.properties* con la stessa password radice usata per aprire il prompt MySQL:

```
spring.datasource.password=mysqlpass
```

### <a name="build-and-run-the-sample"></a>Compilare ed eseguire l'esempio

Compilare ed eseguire l'esempio usando il wrapper Maven incluso nel repository:

```bash
cd spring-boot-mysql-todo
mvnw package spring-boot:run
```

Aprire nel browser l'indirizzo `http://localhost:8080` per vedere l'esempio in azione. Quando si aggiungono attività all'elenco, usare i comandi SQL seguenti nel prompt MySQL per visualizzare i dati archiviati in MySQL.

```SQL
use testdb;
select * from todo_item;
```

Arrestare l'applicazione premendo `Ctrl`+`C` nel terminale. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-mysql-database"></a>Creare un database MySQL di Azure

In questo passaggio si crea un'istanza di [Database di Azure per MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-cli.md) usando l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Si configura l'applicazione di esempio per usare questo database più avanti nell'esercitazione.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md) con il comando [az group create](/cli/azure/group#create). Un gruppo di risorse di Azure è un contenitore logico in cui vengono distribuite e gestite le risorse correlate, ad esempio app Web, database e account di archiviazione. 

L'esempio seguente crea un gruppo di risorse nell'area Europa settentrionale:

```azurecli-interactive
az group create --name myResourceGroup --location "North Europe"
```    

Per visualizzare i possibili valori utilizzabili per `--location`, usare il comando [az appservice list-locations](/cli/azure/appservice#list-locations).

### <a name="create-a-mysql-server"></a>Creare un server MySQL

In Cloud Shell creare un server nel database di Azure per MySQL (anteprima) con il comando [az mysql server create](/cli/azure/mysql/server#create).    
Sostituire il segnaposto `<mysql_server_name>` con il nome univoco del proprio server MySQL. Questo nome fa parte del nome host del server MySQL, `<mysql_server_name>.mysql.database.azure.com`, pertanto deve essere univoco a livello globale. Sostituire anche `<admin_user>` e `<admin_password>` con i valori desiderati.

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user <admin_user> --admin-password <admin_password>
```

Al termine della creazione del server MySQL, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente:

```json
{
  "administratorLogin": "admin_user",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mysql_server_name.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysql_server_name",
  "location": "northeurope",
  "name": "mysql_server_name",
  "resourceGroup": "mysqlJavaResourceGroup",
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>Configurare il firewall del server

In Cloud Shell creare una regola del firewall per il server MySQL per consentire le connessioni client tramite il comando [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#create). 

```azurecli-interactive
az mysql server firewall-rule create --name allIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Database di Azure per MySQL (anteprima) per il momento non consente di abilitare automaticamente le connessioni dai servizi di Azure. Poiché gli indirizzi IP in Azure vengono assegnati dinamicamente, per il momento è preferibile abilitarli tutti. Il servizio è ancora in anteprima, ma presto verranno abilitati metodi migliori per la protezione del database.

## <a name="configure-the-azure-mysql-database"></a>Configurare il database MySQL di Azure

Nella finestra del terminale locale connettersi al server MySQL in Azure. Usare il valore specificato precedentemente per `<admin_user>` e `<mysql_server_name>`.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-database"></a>Creare un database 

Nel prompt `mysql` creare un database e una tabella per gli elementi attività.

```sql
CREATE DATABASE tododb;
```

### <a name="create-a-user-with-permissions"></a>Creare un utente con autorizzazioni

Creare un utente del database e concedergli tutti i privilegi nel database `tododb`. Sostituire i segnaposto `<Javaapp_user>` e `<Javaapp_password>` con il proprio nome univoco dell'app.

```sql
CREATE USER '<Javaapp_user>' IDENTIFIED BY '<Javaapp_password>'; 
GRANT ALL PRIVILEGES ON tododb.* TO '<Javaapp_user>';
```

Chiudere la connessione al server digitando `quit`.

```sql
quit
```

## <a name="deploy-the-sample-to-azure-app-service"></a>Distribuire l'esempio in Servizio App di Azure

Creare un piano di servizio app di Azure con il piano tariffario **GRATUITO** usando il comando dell'interfaccia della riga di comando [az appservice plan create](/cli/azure/appservice/plan#create). Il piano di servizio app definisce le risorse fisiche usate per ospitare le app. Tutte le applicazioni assegnate a un piano di servizio app condividono queste risorse, per poter consentire un risparmio sui costi quando si ospitano più app. 

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Quando il piano è pronto, l'output dell'interfaccia della riga di comando di Azure è simile all'esempio seguente:

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
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Creare un'app Web di Azure

 In Cloud Shell usare il comando dell'interfaccia della riga di comando [az webapp create](/cli/azure/appservice/web#create) per creare la definizione di un'app Web nel piano di servizio app `myAppServicePlan`. La definizione dell'app Web fornisce un URL con cui accedere all'applicazione e configura diverse opzioni per distribuire il codice in Azure. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

Sostituire il segnaposto `<app_name>` con il nome univoco dell'app. Questo nome univoco fa parte del nome di dominio predefinito per l'app Web, quindi è necessario che sia univoco rispetto a tutte le app presenti in Azure. È possibile eseguire il mapping di una voce di nome di dominio personalizzata all'app Web prima di esporla agli utenti.

Quando la definizione dell'app Web è pronta, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
   ...
  < Output has been truncated for readability >
}
```

### <a name="configure-java"></a>Configurare Java 

In Cloud Shell impostare la configurazione del runtime Java necessaria per l'app con il comando [az appservice web config update](/cli/azure/appservice/web/config#update).

Il comando seguente configura l'app Web per l'esecuzione in un'istanza recente di Java 8 JDK e in [Apache Tomcat](http://tomcat.apache.org/) 8.0.

```azurecli-interactive
az webapp config set --name <app_name> --resource-group myResourceGroup --java-version 1.8 --java-container Tomcat --java-container-version 8.0
```

### <a name="configure-the-app-to-use-the-azure-sql-database"></a>Configurare l'app per l'uso del database SQL di Azure

Prima di eseguire l'app di esempio, configurare le impostazioni dell'applicazione nell'app Web per l'uso del database MySQL di Azure creato in Azure. Queste proprietà vengono esposte all'applicazione Web come variabili di ambiente ed eseguono l'override dei valori impostati in application.properties nell'app Web inclusa nel pacchetto. 

In Cloud Shell configurare le impostazioni dell'applicazione usando [az webapp config appsettings](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings) nell'interfaccia della riga di comando:

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_URL="jdbc:mysql://<mysql_server_name>.mysql.database.azure.com:3306/tododb?verifyServerCertificate=true&useSSL=true&requireSSL=false" --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_USERNAME=Javaapp_user@mysql_server_name --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_URL=Javaapp_password --resource-group myResourceGroup --name <app_name>
```

### <a name="get-ftp-deployment-credentials"></a>Ottenere le credenziali di distribuzione FTP 
È possibile distribuire l'applicazione nel Servizio app di Azure attraverso varie soluzioni, tra cui FTP, Git locale, GitHub, Visual Studio Team Services e BitBucket. In questo esempio viene usato FTP per distribuire in Servizio app di Azure il file WAR compilato prima sul computer locale.

Per determinare quali credenziali passare in un comando FTP per l'app Web, in Cloud Shell usare il comando [az appservice web deployment list-publishing-profiles](https://docs.microsoft.com/cli/azure/appservice/web/deployment#az_appservice_web_deployment_list_publishing_profiles): 

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app_name> --resource-group myResourceGroup --query "[?publishMethod=='FTP'].{URL:publishUrl, Username:userName,Password:userPWD}" --output json
```

```JSON
[
  {
    "Password": "aBcDeFgHiJkLmNoPqRsTuVwXyZ",
    "URL": "ftp://waws-prod-blu-069.ftp.azurewebsites.windows.net/site/wwwroot",
    "Username": "app_name\\$app_name"
  }
]
```

### <a name="upload-the-app-using-ftp"></a>Caricare l'app usando FTP

Usare lo strumento FTP preferito per distribuire il file WAR nella cartella */site/wwwroot/webapps* all'indirizzo server ottenuto dal campo `URL` nel comando precedente. Rimuovere la directory dell'applicazione predefinita esistente (ROOT) e sostituire il file ROOT.war esistente con il file WAR compilato prima nell'esercitazione.

```bash
ftp waws-prod-blu-069.ftp.azurewebsites.windows.net
Connected to waws-prod-blu-069.drip.azurewebsites.windows.net.
220 Microsoft FTP Service
Name (waws-prod-blu-069.ftp.azurewebsites.windows.net:raisa): app_name\$app_name
331 Password required
Password:
cd /site/wwwroot/webapps
mdelete -i ROOT/*
rmdir ROOT/
put target/TodoDemo-0.0.1-SNAPSHOT.war ROOT.war
```

### <a name="test-the-web-app"></a>Testare l'app Web

Passare a `http://<app_name>.azurewebsites.net/` e aggiungere alcune attività all'elenco. 

![App Java in esecuzione nel Servizio app di Azure](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

**Congratulazioni.** L'app Java basata su dati è in esecuzione in Servizio app di Azure.

## <a name="update-the-app-and-redeploy"></a>Aggiornare e ridistribuire l'app

Aggiornare l'applicazione per includere una colonna aggiuntiva nell'elenco attività, indicante il giorno in cui l'elemento è stato creato. Spring Boot gestisce automaticamente lo schema del database quando il modello di dati cambia senza modificare i record di database esistenti.

1. Nel sistema locale aprire *src/main/java/com/example/fabrikam/TodoItem.java* e aggiungere le importazioni seguenti alla classe:   

    ```java
    import java.text.SimpleDateFormat;
    import java.util.Calendar;
    ```

2. Aggiungere una proprietà `timeCreated` di tipo `String` a *src/main/java/com/example/fabrikam/TodoItem.java*, inizializzandola con un timestamp al momento della creazione dell'oggetto. Aggiungere getter/setter per la nuova proprietà `timeCreated` mentre si modifica questo file.

    ```java
    private String name;
    private boolean complete;
    private String timeCreated;
    ...

    public TodoItem(String category, String name) {
       this.category = category;
       this.name = name;
       this.complete = false;
       this.timeCreated = new SimpleDateFormat("MMMM dd, YYYY").format(Calendar.getInstance().getTime());
    }
    ...
    public void setTimeCreated(String timeCreated) {
       this.timeCreated = timeCreated;
    }

    public String getTimeCreated() {
        return timeCreated;
    }
    ```

3. Aggiornare *src/main/java/com/example/fabrikam/TodoDemoController.java* con una riga nel metodo `updateTodo` per impostare il timestamp:

    ```java
    item.setComplete(requestItem.isComplete());
    item.setId(requestItem.getId());
    item.setTimeCreated(requestItem.getTimeCreated());
    repository.save(item);
    ```

4. Aggiungere il supporto per il nuovo campo nel modello Thymeleaf. Aggiornare *src/main/resources/templates/index.html* con una nuova intestazione della tabella per il timestamp e un nuovo campo per visualizzare il valore del timestamp in ogni riga di dati della tabella.

    ```html
    <th>Name</th>
    <th>Category</th>
    <th>Time Created</th>
    <th>Complete</th>
    ...
    <td th:text="${item.category}">item_category</td><input type="hidden" th:field="*{todoList[__${i.index}__].category}"/>
    <td th:text="${item.timeCreated}">item_time_created</td><input type="hidden" th:field="*{todoList[__${i.index}__].timeCreated}"/>
    <td><input type="checkbox" th:checked="${item.complete} == true" th:field="*{todoList[__${i.index}__].complete}"/></td>
    ```

5. Ricompilare l'applicazione:

    ```bash
    mvnw clean package 
    ```

6. Distribuire tramite FTP il file WAR aggiornato come prima, rimuovendo la directory *site/wwwroot/webapps/ROOT* e il file *ROOT.war* esistenti, quindi caricando il file WAR aggiornato come ROOT.war. 

Quando si aggiorna l'app, è ora visibile una colonna **Time Created** (Ora di creazione). Quando si aggiunge una nuova attività, l'app popolerà il timestamp automaticamente. Le attività esistenti rimangono invariate e usano l'app anche se il modello di dati sottostante è stato modificato. 

![App Java aggiornata con una nuova colonna](./media/app-service-web-tutorial-java-mysql/appservice-updates-java.png)
      
## <a name="stream-diagnostic-logs"></a>Eseguire lo streaming dei log di diagnostica 

Mentre l'applicazione Java è in esecuzione in Servizio app di Azure, è possibile fare in modo che i log della console siano inviati tramite pipe direttamente al terminale. Ciò consente di ottenere gli stessi messaggi di diagnostica per il debug degli errori dell'applicazione.

Per avviare lo streaming dei log, usare il comando [az webapp log tail](/cli/azure/webapp/log?view=azure-cli-latest#az_webapp_log_tail) in Cloud Shell.

```azurecli-interactive 
az webapp log tail --name <app_name> --resource-group myResourceGroup 
``` 

## <a name="manage-your-azure-web-app"></a>Gestire l'app Web di Azure

Accedere al portale di Azure per visualizzare l'app Web creata.

A tale scopo, accedere a [https://portal.azure.com](https://portal.azure.com).

Nel menu a sinistra fare clic su **Servizi app** e quindi sul nome dell'app Web di Azure.

![Passare all'app Web di Azure nel portale](./media/app-service-web-tutorial-java-mysql/access-portal.png)

Per impostazione predefinita, nel pannello dell'app Web viene aperta la pagina **Panoramica**, che offre una visualizzazione dello stato dell'app. In questa pagina è anche possibile eseguire attività di gestione come arrestare, avviare, riavviare ed eliminare. Le schede sul lato sinistro del pannello mostrano le diverse pagine di configurazione che è possibile aprire.

![Pannello del servizio app nel portale di Azure](./media/app-service-web-tutorial-java-mysql/web-app-blade.png)

Queste schede del pannello mostrano le numerose utili funzionalità che è possibile aggiungere all'app Web. Nell'elenco seguente sono riportate solo alcune delle possibilità:
* Eseguire il mapping di un nome DNS personalizzato
* Associare un certificato SSL personalizzato
* Configurare la distribuzione continua
* Aumentare le prestazioni e il numero di istanze
* Aggiungere l'autenticazione utente

## <a name="clean-up-resources"></a>Pulire le risorse

Se queste risorse non sono necessarie per un'altra esercitazione (vedere [Passaggi successivi](#next)), è possibile eliminarle eseguendo questo comando in Cloud Shell: 
  
```azurecli-interactive
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## <a name="next-steps"></a>Passaggi successivi

> [!div class="checklist"]
> * Creare un database MySQL in Azure
> * Connettere un'app Java di esempio a MySQL
> * Distribuire l'app in Azure
> * Aggiornare e ridistribuire l'app
> * Eseguire lo streaming dei log di diagnostica in Azure
> * Gestire l'app nel portale di Azure

Passare all'esercitazione successiva per apprendere come eseguire il mapping di un nome DNS personalizzato all'app.

> [!div class="nextstepaction"] 
> [Eseguire il mapping di un nome DNS personalizzato esistente ad app Web di Azure](app-service-web-tutorial-custom-domain.md)
