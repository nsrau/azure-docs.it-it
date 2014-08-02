<properties linkid="develop-java-tutorials-web-site-add-app" urlDisplayName="Add an application to your Java web site" pageTitle="Add an application to your Java web site" metaKeywords="" description="This tutorial shows you how to add a page or application to your Java web site on Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Add an application to your Java web site" videoId="" scriptId="" authors="waltpo" solutions="" manager="keboyd" editor="mollybos" />

Aggiunta di un'applicazione a un sito Web Java in Azure
=======================================================

In questa esercitazione viene illustrato come aggiungere una pagina Web o un'applicazione a un sito Web Java precedentemente creato utilizzando la raccolta di Azure o l'interfaccia di configurazione di Azure.

Si noti che è possibile utilizzare il controllo del codice sorgente per caricare l'applicazione o le pagine Web, anche negli scenari di integrazione continuata. Per istruzioni relative all'utilizzo del controllo del codice sorgente nel proprio sito Web, vedere [Pubblicazione da controllo del codice sorgente in Siti Web di Azure](../web-sites-publish-source-control). In questo articolo viene spiegato come caricare l'applicazione tramite FTP, in alternativa al controllo del codice sorgente.

In questa esercitazione si presuppone che sia già stata completata la procedura indicata in [Introduzione a Siti Web di Microsoft Azure e Java](../web-sites-java-get-started).

Personalizzazione di un sito Web tramite FTP
============================================

Sarà necessario individuare le proprie credenziali FTP e utilizzarle per accedere al contenuto del sito Web. In seguito è possibile modificare il contenuto per eseguire l'applicazione. Nell'esempio qui riportato verrà utilizzato Esplora file per le funzionalità FTP, ma è anche possibile utilizzare altre tecniche FTP.

Utilizzo delle credenziali FTP per accedere al contenuto del sito
-----------------------------------------------------------------

1.  Nel portale di gestione di Microsoft Azure passare alla visualizzazione **Web Sites**.
2.  Nella visualizzazione **Web Sites** fare clic sul nome del proprio sito Web.
3.  Fare clic su **Dashboard**.
4.  Nella visualizzazione **Dashboard** fare clic su **Download the publish profile** in **Quick Glance**. Salvare il file in locale. È importante proteggere questo file poiché contiene il nome utente e la password che consentono di pubblicare nel sito, nonché di copiare contenuti dal sito.
5.  Aprire in un editor di testo il file Publish Settings scaricato. All'interno del file osservare i valori **userName** e **userPwd**. Rappresentano rispettivamente il nome utente e la password da utilizzare per accedere ai file nel sito.
6.  Accedere ai file del sito Web, specificando il nome utente e la password quando viene richiesto. In questo esempio l'FTP verrà utilizzato da Internet Explorer, ma è anche possibile utilizzare altre tecniche. Per utilizzare l'FTP, nella visualizzazione **Dashboard** fare clic sull'URL indicato in **FTP Host Name**. Il nome host FTP può essere individuato anche nel file Publish Settings osservando il valore assegnato a **publishUrl**.
7.  Quando viene chiesto di inserire il nome utente e la password, utilizzare i valori presenti nel file Publish Settings in corrispondenza di **userName** e **userPwd**.
8.  Sempre in Internet Explorer, per passare alla visualizzazione Esplora file fare clic su **Visualizza**, quindi su **Apri sito FTP in Esplora file**.

Accesso alla cartella webapps per il sito Web
---------------------------------------------

A questo punto è possibile personalizzare il sito Web nella visualizzazione Esplora file del sito Web in Azure. Sarà necessario copiare l'applicazione nella cartella **webapps** per il sito Web. Il percorso di tale cartella varia a seconda della configurazione del sito Web.

-   Se il sito Web è stato configurato tramite la raccolta di applicazioni di Azure, in Esplora file fare doppio clic su **site**, fare doppio clic su **wwwroot**, fare doppio clic su **bin**, fare doppio clic sulla versione del server applicazioni utilizzato dal sito Web, quindi fare doppio clic su **webapps**.
-   Se il sito Web è stato configurato utilizzando l'interfaccia di configurazione di Azure, in Esplora file fare doppio clic su **site**, fare doppio clic su **wwwroot**, quindi fare doppio clic su **webapps**.
-   Se il sito Web è stato configurato utilizzando un caricamento personalizzato, passare alla cartella **webapps** nel modo appropriato.

Per aggiungere un file WAR al sito Web tramite FTP
--------------------------------------------------

1.  Passare alla cartella **webapps** utilizzando la tecnica appropriata per il sito Web, come descritto in precedenza.
2.  Copiare il file WAR nella cartella **webapps**.

Il server applicazioni rileverà l'aggiunta del file WAR e lo caricherà automaticamente. Sarà quindi possibile eseguire l'app nel browser tramite l'URL del sito Web a cui è stato aggiunto il nome del file WAR.

Ad esempio, passare a http://*nomesito*.azurewebsites.net/*fileWAR*, dove *nomesito* è il nome specificato per l'URL e *fileWAR* è il nome del file WAR (con distinzione maiuscole/minuscole) copiato in precedenza, senza **.war** alla fine.

Per aggiungere una pagina Web al sito Web tramite FTP
-----------------------------------------------------

1.  Passare alla cartella **webapps**.
2.  Creare una nuova cartella all'interno della cartella **webapps**.
3.  Aprire la nuova cartella.
4.  Aggiungere la pagina Web alla nuova cartella.

Il server applicazioni rileverà l'aggiunta della nuova cartella e del nuovo file Web e li caricherà automaticamente. Eseguire quindi il file JSP utilizzando l'URL nel formato http://*nomesito*.azurewebsites.net/*cartellapersonale*/*filepersonale.jsp*, dove *nomesito* è il nome specificato per l'URL, *cartellapersonale* è la cartella creata in **webapps** e *filepersonale.jsp* è il nome del file JSP creato.

Si noti che se vengono copiati file (eccetto i file WAR) nella directory ROOT, è necessario riavviare il server applicazioni per utilizzarli. La funzionalità di caricamento automatico per i siti Web Java in esecuzione su Azure si basa su un nuovo file WAR aggiunto o su nuovi file o nuove directory create nella cartella **webapps**.

