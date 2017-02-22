---
title: Django e database SQL in Azure con Python Tools 2.2 per Visual Studio
description: Informazioni su come usare Python Tools per Visual Studio per creare un&quot;app Web Django che archivia i dati in un&quot;istanza di database SQL e per distribuirla in App Web del servizio app di Azure.
services: app-service\web
tags: python
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
ms.assetid: 3a677e64-b5a9-4d43-b9c0-66246368b483
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 07/07/2016
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: c5e01ef322f72eb5a704fd2273194315bf1a9f6f


---
# <a name="django-and-sql-database-on-azure-with-python-tools-22-for-visual-studio"></a>Django e database SQL in Azure con Python Tools 2.2 per Visual Studio
In questa esercitazione si userà [Python Tools per Visual Studio] al fine di creare una semplice app Web per sondaggi con uno dei modelli di esempio PTVS. Questa esercitazione è anche disponibile in formato [video](https://www.youtube.com/watch?v=ZwcoGcIeHF4).

Si apprenderà come usare un database SQL ospitato in Azure, come configurare l'app Web per usare un database SQL e come pubblicare l'app Web in [App Web del servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

Vedere il [Centro per sviluppatori Python] per altri articoli che trattano lo sviluppo di app Web del servizio app di Azure con PTVS usando i framework Web di Bottle, Flask e Django con i servizi di archiviazione tabelle di Azure, MySQL e database SQL. Sebbene questo articolo sia incentrato sul servizio app, i passaggi sono simili a quelli previsti per lo sviluppo dei [servizi cloud di Azure].

## <a name="prerequisites"></a>Prerequisiti
* Visual Studio 2015
* [Python 2.7 a 32 bit]
* [Python Tools 2.2 per Visual Studio]
* [VSIX degli esempi di Python Tools 2.2 per Visual Studio]
* [Strumenti di Azure SDK per Visual Studio 2015]
* Django 1.9 o versione successiva

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

> [!NOTE]
> Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](https://azure.microsoft.com/try/app-service/), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
>
>

## <a name="create-the-project"></a>Creare il progetto
In questa sezione verrà creato un progetto di Visual Studio usando un modello di esempio. Verrà creato un ambiente virtuale e verranno installati i pacchetti necessari. Si creerà un database locale usando sqlite, Quindi, l'app Web verrà eseguita in locale.

1. In Visual Studio selezionare **File**, **Nuovo progetto**.
2. I modelli di progetto di [VSIX degli esempi di Python Tools 2.2 per Visual Studio] sono disponibili in **Python**, **Esempi**. Selezionare **Polls Django Web Project** e fare clic su OK per creare il progetto.

     ![Finestra di dialogo Nuovo progetto](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)
3. Verrà richiesto di installare pacchetti esterni. Selezionare **Installa in un ambiente virtuale**.

     ![Finestra di dialogo dei pacchetti esterni](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)
4. Selezionare **Python 2.7** come interprete di base.

     ![Finestra di dialogo Aggiungi ambiente virtuale](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)
5. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nodo del progetto, scegliere **Python** e quindi selezionare**Django Migrate** (Migrazione Django).  Selezionare quindi **Django Create Superuser**(Creazione SuperUser Django).
6. Verrà aperta una console di gestione Django e verrà creato un database sqlite database nella cartella del progetto. Seguire le istruzioni visualizzate per creare un utente.
7. Verificare che l'applicazione funzioni premendo <kbd>F5</kbd>.
8. Fare clic su **Log in** sulla barra di spostamento in alto.

     ![Web browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)
9. Immettere le credenziali per l'utente creato al momento della sincronizzazione del database.

     ![Web browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)
10. Fare clic su **Create Sample Polls**.

      ![Web browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)
11. Fare clic su un sondaggio e votare.

      ![Web browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

## <a name="create-a-sql-database"></a>Creare un database SQL
Per il database verrà creato un database SQL di Azure.

Per creare un database, seguire questa procedura.

1. Accedere al [portale di Azure].
2. Nella parte inferiore del pannello di navigazione fare clic su **NUOVO**. Fare clic su **Dati e archiviazione** > **Database SQL**.
3. Configurare il nuovo database SQL creando un nuovo gruppo di risorse e selezionare il percorso appropriato.
4. Dopo aver creato il database SQL, fare clic su **Apri in Visual Studio** nel pannello del database.
5. Fare clic su **Configura firewall**.
6. Nel pannello **Impostazioni firewall** aggiungere una regola firewall con **IP INIZIALE** e **IP FINALE** impostati sull'indirizzo IP pubblico del computer di sviluppo. Fare clic su **Save**.

   Questa impostazione permetterà le connessioni al server di database dal computer di sviluppo.
7. Nel pannello del database fare clic su **Proprietà**, quindi su **Mostra stringhe di connessione database**.
8. Usare il pulsante Copia per inserire il valore di **ADO.NET** negli Appunti.

## <a name="configure-the-project"></a>Configurare il progetto
In questa sezione l'app Web verrà configurata per usare il database SQL appena creato. e verranno installati i pacchetti Python aggiuntivi necessari per usare i database SQL con Django. Quindi, l'app Web verrà eseguita in locale.

1. In Visual Studio aprire **settings.py**dalla cartella *NomeProgetto* . Incollare temporaneamente la stringa di connessione nell'editor. La stringa di connessione è nel formato seguente:

       Server=<ServerName>,<ServerPort>;Database=<DatabaseName>;User ID=<UserName>;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;

Modificare la definizione di `DATABASES` per usare i valori precedenti.

        DATABASES = {
            'default': {
                'ENGINE': 'sql_server.pyodbc',
                'NAME': '<DatabaseName>',
                'USER': '<UserName>',
                'PASSWORD': '{your_password_here}',
                'HOST': '<ServerName>',
                'PORT': '<ServerPort>',
                'OPTIONS': {
                    'driver': 'SQL Server Native Client 11.0',
                    'MARS_Connection': 'True',
                }
            }
        }

1. In Esplora soluzioni, under **Python Environments**, fare clic con il pulsante destro del mouse sull'ambiente virtuale e selezionare **Install Python Package**.
2. Installare il pacchetto `pyodbc` usando **pip**.

     ![Finestra di dialogo per l'installazione del pacchetto](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)
3. Installare il pacchetto `django-pyodbc-azure` usando **pip**.

     ![Finestra di dialogo per l'installazione del pacchetto](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)
4. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nodo del progetto, scegliere **Python** e quindi selezionare**Django Migrate** (Migrazione Django).  Selezionare quindi **Django Create Superuser**(Creazione SuperUser Django).

   Verranno create le tabelle per il database SQL creato nella sezione precedente. Seguire le istruzioni per creare un utente, che non deve necessariamente corrispondere all'utente nel database sqlite creato nella prima sezione.
5. Eseguire l'applicazione con `F5`. I sondaggi creati con **Create Sample Polls** e i dati inviati mediante voto verranno serializzati nel database SQL.

## <a name="publish-the-web-app-to-azure-app-service"></a>Pubblicare l'app Web nel servizio app di Azure
L'SDK .NET di Azure offre un modo semplice di distribuire l'app Web in App Web del servizio app di Azure.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nodo di progetto e scegliere **Pubblica**.

     ![Finestra di dialogo Pubblica sito Web](./media/web-sites-python-ptvs-django-sql/PollsCommonPublishWebSiteDialog.png)
2. Fare clic su **App Web di Microsoft Azure**.
3. Fare clic su **Nuovo** per creare una nuova app Web.
4. Compilare i campi seguenti, quindi fare clic su **Crea**.

   * **Nome dell'app Web**
   * **Piano di servizio app**
   * **Gruppo di risorse**
   * **Area**
   * Lasciare **Server database** impostato su **Nessun database**.
5. Accettare tutte le altre impostazioni predefinite e fare clic su **Pubblica**.
6. L'app Web pubblicata verrà aperto automaticamente nel Web browser. L'app Web dovrebbe funzionare come previsto, usando il database **SQL** ospitato in Azure.

   Congratulazioni.

     ![Web browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoAzureBrowser.png)

## <a name="next-steps"></a>Passaggi successivi
Usare i collegamenti seguenti per altre informazioni su Python Tools per Visual Studio, Django e il database SQL.

* [Documentazione di Python Tools per Visual Studio]
  * [Progetti Web]
  * [Progetti servizio cloud]
  * [Debug remoto in Microsoft Azure]
* [Documentazione di Django]
* [Database SQL]

## <a name="whats-changed"></a>Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)

<!--Link references-->
[Centro per sviluppatori Python]: /develop/python/
[servizi cloud di Azure]: ../cloud-services/cloud-services-python-ptvs.md

<!--External Link references-->
[portale di Azure]: https://portal.azure.com
[Python Tools per Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 per Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[VSIX degli esempi di Python Tools 2.2 per Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Strumenti di Azure SDK per Visual Studio 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 a 32 bit]: http://go.microsoft.com/fwlink/?LinkId=517190
[Documentazione di Python Tools per Visual Studio]: http://aka.ms/ptvsdocs
[Debug remoto in Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=624026
[Progetti Web]: http://go.microsoft.com/fwlink/?LinkId=624027
[Progetti servizio cloud]: http://go.microsoft.com/fwlink/?LinkId=624028
[Documentazione di Django]: https://www.djangoproject.com/
[Database SQL]: /documentation/services/sql-database/



<!--HONumber=Feb17_HO3-->


