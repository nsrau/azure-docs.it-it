---
title: Django e MySQL in Azure con Python Tools 2.2 per Visual Studio
description: Informazioni su come usare Python Tools per Visual Studio per creare un&quot;app Web Django che archivia i dati in un&quot;istanza di database MySQL e per distribuirla in App Web del servizio app di Azure.
services: app-service\web
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
ms.assetid: c60a50b5-8b5e-4818-a442-16362273dabb
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: get-started-article
ms.date: 07/07/2016
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 759441c5d64ee59f13d50eb415fbaa884dd4821a


---
# <a name="django-and-mysql-on-azure-with-python-tools-22-for-visual-studio"></a>Django e MySQL in Azure con Python Tools 2.2 per Visual Studio
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

In questa esercitazione si userà [Python Tools per Visual Studio](https://www.visualstudio.com/vs/python) al fine di creare una semplice app Web per sondaggi con uno dei modelli di esempio PTVS. Si apprenderà come usare un servizio MySQL ospitato in Azure, come configurare l'app Web per l'uso di MySQL e come pubblicare l'app Web in [App Web del servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

> [!NOTE]
> Le informazioni contenute in questa esercitazione sono disponibili anche nel video seguente:
> 
> Video [PTVS 2.1: Django app with MySQL][ (PTVS 2.1: app Django con MySQL)]
> 
> 

Vedere il [Centro per sviluppatori Python] per altri articoli che trattano lo sviluppo di app Web del servizio app di Azure con PTVS usando i framework Web di Bottle, Flask e Django con i servizi di archiviazione tabelle di Azure, MySQL e Database SQL. Sebbene questo articolo sia incentrato sul servizio app, i passaggi sono simili a quelli previsti per lo sviluppo dei [servizi cloud di Azure].

## <a name="prerequisites"></a>Prerequisiti
* Visual Studio 2015
* [Python 2.7 a 32 bit] o [Python 3.4 a 32 bit]
* [Python Tools 2.2 per Visual Studio]
* [VSIX degli esempi di Python Tools 2.2 per Visual Studio]
* [Strumenti di Azure SDK per Visual Studio 2015]
* Django 1.9 o versione successiva

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<!-- This note should not render as part of the the previous include. -->

> [!NOTE]
> Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
> 
> 

## <a name="create-the-project"></a>Creare il progetto
In questa sezione verrà creato un progetto di Visual Studio usando un modello di esempio. Verrà creato un ambiente virtuale e verranno installati i pacchetti necessari. Si creerà un database locale usando sqlite, quindi verrà eseguita l'applicazione in locale.

1. In Visual Studio selezionare **File**, **Nuovo progetto**.
2. I modelli di progetto di [VSIX degli esempi di Python Tools 2.2 per Visual Studio] sono disponibili in **Python**, **Esempi**. Selezionare **Polls Django Web Project** e fare clic su OK per creare il progetto.
   
    ![Finestra di dialogo Nuovo progetto](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)
3. Verrà richiesto di installare pacchetti esterni. Selezionare **Installa in un ambiente virtuale**.
   
    ![Finestra di dialogo dei pacchetti esterni](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)
4. Selezionare **Python 2.7** o **Python 3.4** come interprete di base.
   
    ![Finestra di dialogo Aggiungi ambiente virtuale](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)
5. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nodo del progetto, scegliere **Python** e quindi selezionare**Django Migrate** (Migrazione Django).  Selezionare quindi **Django Create Superuser**(Creazione SuperUser Django).
6. Verrà aperta una console di gestione Django e verrà creato un database sqlite database nella cartella del progetto. Seguire le istruzioni visualizzate per creare un utente.
7. Verificare che l'applicazione funzioni premendo `F5`.
8. Fare clic su **Log in** sulla barra di spostamento in alto.
   
    ![Barra di spostamento di Django](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)
9. Immettere le credenziali per l'utente creato al momento della sincronizzazione del database.
   
    ![Form di accesso](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)
10. Fare clic su **Create Sample Polls**.
    
     ![Create Sample Polls](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)
11. Fare clic su un sondaggio e votare.
    
     ![Votazione nei poll di esempio](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

## <a name="create-a-mysql-database"></a>Creare un database MySQL
Per il database verrà creato un database ospitato MySQL di ClearDB in Azure.

In alternativa, è possibile creare una propria macchina virtuale in esecuzione in Azure, quindi installare e amministrare MySQL manualmente.

Per creare un database con un piano gratuito, attenersi alla procedura seguente.

1. Accedere al [Portale di Azure].
2. Nella parte superiore del riquadro di spostamento fare clic su **NUOVO**, quindi su **Dati e archiviazione** e infine su **Database MySQL**.
3. Configurare il nuovo database MySQL creando un nuovo gruppo di risorse e selezionare il percorso appropriato.
4. Dopo aver creato il database MySQL, fare clic su **Proprietà** nel pannello del database.
5. Usare il pulsante Copia per inserire il valore della **STRINGA DI CONNESSIONE** negli Appunti.

## <a name="configure-the-project"></a>Configurare il progetto
In questa sezione verrà configurata l'app Web per usare il database MySQL appena creato. Verranno anche installati i pacchetti Python aggiuntivi necessari per usare i database MySQL con Django, quindi l'app Web verrà eseguita in locale.

1. In Visual Studio aprire **settings.py**dalla cartella *NomeProgetto* . Incollare temporaneamente la stringa di connessione nell'editor. La stringa di connessione è nel formato seguente:
   
        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>
   
    Modificare il database predefinito **ENGINE** in modo che usi MySQL e impostare i valori relativi a **NAME**, **USER**, **PASSWORD** e **HOST** da **CONNECTIONSTRING**.
   
        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '<Database>',
                'USER': '<User Id>',
                'PASSWORD': '<Password>',
                'HOST': '<Data Source>',
                'PORT': '',
            }
        }
2. In Esplora soluzioni, under **Python Environments**, fare clic con il pulsante destro del mouse sull'ambiente virtuale e selezionare **Install Python Package**.
3. Installare il pacchetto `mysqlclient` usando **pip**.
   
    ![Finestra di dialogo per l'installazione del pacchetto](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)
4. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nodo del progetto, scegliere **Python** e quindi selezionare**Django Migrate** (Migrazione Django).  Selezionare quindi **Django Create Superuser**(Creazione SuperUser Django).
   
    Verranno in tal modo create le tabelle per il database MySQL creato nella sezione precedente. Seguire le istruzioni per creare un utente, che non deve necessariamente corrispondere all'utente nel database sqlite creato nella prima sezione di questo articolo.
5. Eseguire l'applicazione con `F5`. I sondaggi creati con **Create Sample Polls** e i dati inviati mediante voto verranno serializzati nel database MySQL.

## <a name="publish-the-web-app-to-azure-app-service"></a>Pubblicare l'app Web nel servizio app di Azure
L'SDK .NET di Azure offre un modo semplice di distribuire l'app Web nel servizio app di Azure.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nodo di progetto e scegliere **Pubblica**.
   
    ![Finestra di dialogo Pubblica sito Web](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)
2. Fare clic su **Servizio app di Microsoft Azure**.
3. Fare clic su **Nuovo** per creare una nuova app Web.
4. Compilare i campi seguenti, quindi fare clic su **Crea**:
   
   * **Nome dell'app Web**
   * **Piano di servizio app**
   * **Gruppo di risorse**
   * **Area**
   * Lasciare **Server database** impostato su **Nessun database**.
5. Accettare tutte le altre impostazioni predefinite e fare clic su **Pubblica**.
6. L'app Web pubblicata verrà aperto automaticamente nel Web browser. L'app Web dovrebbe funzionare come previsto, usando il database **MySQL** ospitato in Azure.
   
    ![Web browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)
   
    Congratulazioni. La pubblicazione in Azure dell'app Web basata su MySQL è stata completata.

## <a name="next-steps"></a>Passaggi successivi
Usare i collegamenti seguenti per altre informazioni su Python Tools per Visual Studio, Django e MySQL.

* [Documentazione di Python Tools per Visual Studio]
  * [Progetti Web]
  * [Progetti servizio cloud]
  * [Debug remoto in Microsoft Azure]
* [Documentazione di Django]
* [MySQL]

Per ulteriori informazioni, vedere il [Centro per sviluppatori di Python](/develop/python/).

<!--Link references-->

[Centro per sviluppatori Python]: /develop/python/
[servizi cloud di Azure]: ../cloud-services/cloud-services-python-ptvs.md

<!--External Link references-->

[Portale di Azure]: https://portal.azure.com
[Python Tools per Visual Studio]: https://www.visualstudio.com/vs/python/
[Python Tools 2.2 per Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[VSIX degli esempi di Python Tools 2.2 per Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Strumenti di Azure SDK per Visual Studio 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 a 32 bit]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 a 32 bit]: http://go.microsoft.com/fwlink/?LinkId=517191
[Documentazione di Python Tools per Visual Studio]: http://aka.ms/ptvsdocs
[Debug remoto in Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=624026
[Progetti Web]: http://go.microsoft.com/fwlink/?LinkId=624027
[Progetti servizio cloud]: http://go.microsoft.com/fwlink/?LinkId=624028
[Documentazione di Django]: https://www.djangoproject.com/
[MySQL]: http://www.mysql.com/
[video]: http://youtu.be/oKCApIrS0Lo



<!--HONumber=Nov16_HO2-->


