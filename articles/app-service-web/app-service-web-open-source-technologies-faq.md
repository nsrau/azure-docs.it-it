---
title: Domande frequenti sulle tecnologie open source per App Web di Azure | Microsoft Docs
description: "È possibile ottenere risposte alle domande frequenti sulle tecnologie open source nella funzionalità App Web del servizio app di Azure."
services: app-service\web
documentationcenter: 
author: simonxjx
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 5/16/2017
ms.author: v-six
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 3f7dcaf21e39ebfd6a36f1bfe38f8e78adad081f
ms.contentlocale: it-it
ms.lasthandoff: 06/15/2017


---


# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Domande frequenti sulle tecnologie open source per App Web in Azure

Questo articolo fornisce risposte alle domande frequenti sui problemi relativi alle tecnologie per la [funzionalità App Web del servizio app di Azure](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-cleardb-database-is-down-how-do-i-resolve-this"></a>Il database ClearDB è inattivo. Come posso risolvere il problema?

Per i problemi relativi ai database, contattare il [supporto tecnico di ClearDB](https://www.cleardb.com/developers/help/support). 

Per risposte alle domande frequenti su ClearDB, vedere [Domande frequenti su ClearDB](https://docs.microsoft.com/azure/store-cleardb-faq/).

## <a name="why-isnt-my-cleardb-database-listed-in-the-portal"></a>Perché il database ClearDB non è elencato nel portale?

Se si crea un database ClearDB nel [portale di Azure](http://portal.azure.com/), il database non viene visualizzato nel [portale di Azure classico](http://manage.windowsazure.com/). Per risolvere il problema, è possibile collegare manualmente il database all'app Web.

Analogamente, se si crea un database ClearDB nel [portale di Azure classico](http://manage.windowsazure.com/), il database non verrà visualizzato nel [portale di Azure](http://portal.azure.com/). In questo caso non sono disponibili soluzioni alternative. 

Per altre informazioni, vedere [Domande frequenti per i database MySQL ClearDB con il Servizio app di Azure](https://docs.microsoft.com/azure/store-cleardb-faq/).

## <a name="why-wasnt-my-cleardb-database-migrated-during-my-subscription-migration"></a>Per quale ragione non è stata eseguita la migrazione del database ClearDB durante la migrazione della sottoscrizione?

Quando si esegue la migrazione delle risorse tra le sottoscrizioni, esistono alcune limitazioni. Il database MySQL ClearDB è un servizio di terze parti e non viene sottoposto a migrazione durante la migrazione di una sottoscrizione di Azure.

Se non si gestisce la migrazione del database MySQL prima della migrazione delle risorse di Azure, è possibile che il database MySQL ClearDB non sia disponibile. Per evitare questo problema, eseguire manualmente la migrazione del database ClearDB e quindi eseguire la migrazione della sottoscrizione di Azure per l'app Web.

Per altre informazioni, vedere [Domande frequenti per i database MySQL ClearDB con il Servizio app di Azure](https://docs.microsoft.com/azure/store-cleardb-faq/).

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Come si attiva la registrazione PHP per risolvere i problemi relativi a PHP?

Per attivare la registrazione PHP:

1. Accedere al [sito Web Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. Nel menu in alto selezionare **Debug Console** (Console di debug)  > **CMD**.
3. Selezionare la cartella **Site** (Sito).
4. Selezionare la cartella **wwwroot**.
5. Selezionare l'icona **+** e quindi selezionare **New File** (Nuovo file).
6. Impostare **.user.ini** come nome del file.
7. Selezionare l'icona della matita accanto a **.user.ini**.
8. Nel file aggiungere questo codice: `log_errors=on`
9. Selezionare **Salva**.
10. Selezionare l'icona della matita accanto a **wp-config.php**.
11. Modificare il testo specificando il codice seguente:
   ```
   //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
   //Supress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Supress PHP errors to screenini_set('display_errors', 0);
   ```
12. Nel portale di Azure riavviare l'app Web dal menu corrispondente.

Per altre informazioni, vedere [Enable WordPress error logs](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/) (Abilitare i log degli errori di WordPress).

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>Come si registrano gli errori delle applicazioni Python in app ospitate nel servizio app?

Per acquisire gli errori delle applicazioni Python:

1. Nel portale di Azure selezionare **Impostazioni** nell'app Web.
2. Nella scheda **Impostazioni** selezionare **Impostazioni dell'applicazione**.
3. In **Impostazioni dell'applicazione** immettere la coppia chiave/valore seguente:
    * Chiave : WSGI_LOG
    * Valore : D:\home\site\wwwroot\logs.txt (immettere il nome file scelto)

Gli errori dovrebbero essere ora visualizzati nel file logs.txt nella cartella wwwroot.

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>Come si cambia la versione dell'applicazione Node.js ospitata nel servizio app?

Per cambiare la versione dell'applicazione Node.js, è possibile usare una delle opzioni seguenti:

*   Nel portale di Azure usare **Impostazioni dell'applicazione**.
    1. Nel portale di Azure passare all'app Web.
    2. Nel pannello **Impostazioni** selezionare **Impostazioni dell'applicazione**.
    3. In **Impostazioni dell'applicazione** è possibile includere WEBSITE_NODE_DEFAULT_VERSION come chiave e la versione di Node.js da usare come valore.
    4. Passare alla [console Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
    5. Per verificare la versione di Node.js, immettere il comando seguente:  
   ```
   node -v
   ```
*   Modificare il file iisnode.yml. La modifica della versione di Node.js nel file iisnode.yml consente solo di configurare l'ambiente di runtime usato da iisnode. Kudu CMD e altre applicazioni usano ancora la versione Node.js specificata in **Impostazioni dell'applicazione** nel portale di Azure.

    Per configurare manualmente iisnode.yml, creare un file iisnode.yml nella cartella radice dell'app. Nel file includere la riga seguente:
   ```
   nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
   ```
   
*   Configurare il file iisnode.yml usando package.json durante la distribuzione del controllo del codice sorgente.
    Il processo di distribuzione del controllo del codice sorgente di Azure prevede la procedura seguente:
    1. Spostamento del contenuto nell'app Web di Azure.
    2. Creazione di uno script di distribuzione predefinito, se non è già presente (file deploy.cmd, file con estensione deployment), nella cartella radice dell'app Web.
    3. Esecuzione di uno script di distribuzione in cui viene creato un file iisnode.yml file se si indica la versione di Node.js nel file package.json > motore `"engines": {"node": "5.9.1","npm": "3.7.3"}`
    4. Il file iisnode.yml include la riga di codice seguente:
        ```
        nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
        ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>Viene visualizzato il messaggio "Errore di connessione al database" nell'app WordPress ospitata nel servizio app. Come si risolve il problema?

Se viene visualizzato questo errore nell'app Azure WordPress, per abilitare php_errors.log e debug.log completare la procedura illustrata in [Enable WordPress error logs](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/) (Abilitare i log degli errori di WordPress).

Quando i log sono abilitati, riprodurre l'errore e quindi controllare i log per verificare se le connessioni sono quasi esaurite:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Se viene visualizzato questo errore nei file debug.log o php_errors.log, l'app ha superato il numero di connessioni disponibili. Se l'hosting viene eseguito su ClearDB, verificare il numero di connessioni disponibili nel [piano di servizio](https://www.cleardb.com/pricing.view).

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>Come si esegue il debug di un'app Node.js ospitata nel servizio app?

1.  Passare alla [console Kudu](https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole).
2.  Passare alla cartella dei log dell'applicazione (D:\home\LogFiles\Application).
3.  Nel file logging_errors.txt controllare il contenuto.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Come si installano i moduli Python nativi in un'app Web o in un'app per le API del servizio app?

È possibile che alcuni pacchetti non vengano installati tramite PIP in Azure. È possibile che il pacchetto non sia disponibile in PIP (Python Package Index) o che sia necessario un compilatore. I compilatori non sono disponibili nei computer che eseguono app Web nel servizio app. Per informazioni sull'installazione dei moduli nativi nelle app Web e nelle app per le API del servizio app, vedere [Install Python modules in App Service](https://blogs.msdn.microsoft.com/azureossds/2015/06/29/install-native-python-modules-on-azure-web-apps-api-apps/) (Installare moduli Python nel servizio app).

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Come si distribuisce un'app Django nel servizio app usando Git e la nuova versione di Python?

Per informazioni sull'installazione di Django, vedere [Deploying a Django app to App Service](https://blogs.msdn.microsoft.com/azureossds/2016/08/25/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python/) (Distribuzione di un'app Django nel servizio app).

## <a name="where-are-the-tomcat-log-files-located"></a>Dove si trovano i file di log di Tomcat?

Per Azure Marketplace e per le distribuzioni personalizzate:

* Percorso della cartella: D:\home\site\wwwroot\bin\apache-tomcat-8.0.33\logs
* File rilevanti:
    * catalina.*aaaa-mm-gg*.log
    * host-manager.*aaaa-mm-gg*.log
    * localhost.*aaaa-mm-gg*.log
    * manager.*aaaa-mm-gg*.log
    * site_access_log.*aaaa-mm-gg*.log


Per distribuzioni di tipo **Impostazioni dell'app** nel portale:

* Percorso della cartella: D:\home\LogFiles
* File rilevanti:
    * catalina.*aaaa-mm-gg*.log
    * host-manager.*aaaa-mm-gg*.log
    * localhost.*aaaa-mm-gg*.log
    * manager.*aaaa-mm-gg*.log
    * site_access_log.*aaaa-mm-gg*.log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Come si risolvono gli errori di connessione del driver JDBC?

È possibile che venga visualizzato il messaggio seguente nei log di Tomcat:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

Per risolvere l'errore:

1. Rimuovere il file sqljdbc*.jar dalla cartella app/lib.
2. Se si usa il server Web Tomcat o Azure Marketplace Tomcat personalizzato, copiare questo file con estensione jar nella cartella lib di Tomcat.
3. Se si abilita Java dal portale di Azure, selezionando **Java 1.8** > **Tomcat server** (Server Tomcat), copiare il file sqljdbc.* jar nella cartella parallela all'app specifica. Aggiungere quindi l'impostazione classpath seguente al file web.config:

    ```
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Perché vengono visualizzati errori quando si prova a copiare i file di log attivi?

Se si prova a copiare i file di log attivi per un'app Java (ad esempio, Tomcat), è possibile che venga visualizzato questo errore FTP:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

Il messaggio di errore può variare in base al client FTP.

Tutte le app Java presentano questo errore di blocco. Solo Kudu supporta il download di questo file durante l'esecuzione dell'app.

L'arresto dell'app consente a FTP di accedere a questi file.

Un'altra soluzione consiste nello scrivere un processo Web eseguito in base a una pianificazione che copia questi file in una directory diversa. Per un progetto di esempio, vedere il progetto [CopyLogsJob](https://github.com/kamilsykora/CopyLogsJob).

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Dove si trovano i file di log per Jetty?

Per il Marketplace e le distribuzioni personalizzate il file di log si trova nella cartella D:\home\site\wwwroot\bin\jetty-distribution-9.1.2.v20140210\logs. Si noti che il percorso della cartella dipende dalla versione di Jetty in uso. Ad esempio, il percorso specificato qui è per Jetty 9.1.2. Cercare jetty_*AAAA_MM_GG*.stderrout.log.

Per le distribuzioni di tipo Impostazioni dell'app nel portale il file si trova in D:\home\LogFiles. Cercare jetty_*AAAA_MM_GG*.stderrout.log.

## <a name="can-i-send-email-from-my-azure-web-app"></a>È possibile inviare messaggi di posta elettronica dall'app Web di Azure?

Il servizio app non include alcuna funzionalità di posta elettronica predefinita. Per alternative valide per l'invio di messaggi di posta elettronica dall'app, vedere questa [discussione su Stack Overflow](http://stackoverflow.com/questions/17666161/sending-email-from-azure).

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>Perché il sito WordPress reindirizza a un altro URL?

Se è stata eseguita di recente la migrazione ad Azure, è possibile che WordPress reindirizzi all'URL di dominio precedente. Questo problema è causato da un'impostazione nel database MySQL.

WordPress Buddy+ è un'estensione del sito di Azure che consente di aggiornare l'URL di reindirizzamento direttamente nel database. Per altre informazioni sull'uso di WordPress Buddy+, vedere [WordPress tools and MySQL migration with WordPress Buddy+](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/) (Strumenti di WordPress e migrazione di MySQL con WordPress Buddy+).

In alternativa, se si preferisce aggiornare manualmente l'URL di reindirizzamento usando query SQL o PHPMyAdmin, vedere [WordPress: Redirecting to wrong URL](https://blogs.msdn.microsoft.com/azureossds/2016/07/12/wordpress-redirecting-to-wrong-url/) (WordPress: Reindirizzamento all'URL errato).

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>Come si modifica la password di accesso di WordPress?

Se si dimentica la password di accesso di WordPress, è possibile usare WordPress Buddy+ per aggiornarla. Per reimpostare la password, installare l'estensione WordPress Buddy+ del sito di Azure e quindi completare la procedura descritta in [WordPress tools and MySQL migration with WordPress Buddy+](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/) (Strumenti di WordPress e migrazione di MySQL con WordPress Buddy+).

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>Non è possibile accedere a WordPress. Come posso risolvere il problema?

Se non è possibile accedere a WordPress dopo l'installazione recente di un plug-in, è possibile che il plug-in sia danneggiato. WordPress Buddy+ è un'estensione del sito di Azure che consente di disabilitare i plug-in in WordPress. Per altre informazioni, vedere [WordPress tools and MySQL migration with WordPress Buddy+](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/) (Strumenti di WordPress e migrazione di MySQL con WordPress Buddy+).

## <a name="how-do-i-migrate-my-wordpress-database"></a>Come si esegue la migrazione del database di WordPress?

Sono disponibili diverse opzioni per la migrazione del database MySQL connesso al sito Web WordPress:

* Sviluppatori: usare il [prompt dei comandi o PHPMyAdmin](https://blogs.msdn.microsoft.com/azureossds/2016/03/02/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service/)
* Non sviluppatori: usare [WordPress Buddy+](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/)

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Come si migliora la protezione di WordPress?

Per informazioni sulle procedure consigliate per la sicurezza di WordPress, vedere [Best practices for WordPress security in Azure](https://blogs.msdn.microsoft.com/azureossds/2016/12/26/best-practices-for-wordpress-security-on-azure/) (Procedure consigliate per la sicurezza di WordPress in Azure).

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>Si sta provando a usare PHPMyAdmin e viene visualizzato il messaggio "Accesso negato". Come posso risolvere il problema?

È possibile che si verifichi questo problema se la funzionalità MySQL in-app non è ancora in esecuzione in questa istanza del servizio app. Per risolvere il problema, provare ad accedere al sito Web. Verranno avviati i processi necessari, incluso il processo MySQL in-app. Per verificare che il processo MySQL in-app sia in esecuzione, in Esplora processi assicurarsi che mysqld.exe sia elencato nei processi.

Dopo avere verificato che MySQL in-app è in esecuzione, provare a usare PHPMyAdmin.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>Viene visualizzato un errore HTTP 403 quando si prova a importare o esportare il database di MySQL in-app usando PHPMyadmin. Come posso risolvere il problema?

Se si usa una versione precedente di Chrome, è possibile che si riscontri un bug noto. Per risolvere il problema, eseguire l'aggiornamento a una versione più recente di Chrome. Provare anche a usare un browser diverso, ad esempio Internet Explorer o Microsoft Edge, in cui non si verifica il problema.

