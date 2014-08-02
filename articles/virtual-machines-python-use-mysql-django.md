<properties linkid="develop-python-web-app-with-django-and-mysql" urlDisplayName="Web with Django + MySQL" pageTitle="Python web app with Django and MySQL - Azure tutorial" metaKeywords="Azure django web app, Azure Django MySQL, Azure django Python" description="A tutorial that teaches you how to use MySQL in with Django on an Azure virtual machine. Code samples written in Python." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World - MySQL Windows Edition" authors="" solutions="" manager="" editor="" />

Django Hello World - Edizione MySQL Windows
===========================================

In questa esercitazione viene descritto come utilizzare MySQL in combinazione con Django in una singola macchina virtuale di Azure. Nella guida si presuppone una certa esperienza nell'utilizzo di Azure e di Django. Per un'introduzione di Azure e Django, vedere [Django Hello World](http://windowsazure.com/en-us/documentation/articles/virtual-machines-python-django-web-app-windows-server). Nella guida si presuppone anche una conoscenza di base di MySQL. Per una panoramica di MySQL, vedere il [sito Web MySQL](http://dev.mysql.com/doc/).

In questa esercitazione si apprenderà come:

-   Configurare una macchina virtuale di Azure per l'hosting di MySQL e Django. Sebbene nell'esercitazione la procedura venga illustrata in Windows Server 2008 R2, è possibile eseguirla anche con una macchina virtuale Linux ospitata in Azure.
-   Installare un [driver MySQL](http://pypi.python.org/pypi/MySQL-python/1.2.3) per Python.
-   Configurare un'applicazione Django esistente per l'utilizzo di un database MySQL.
-   Utilizzare MySQL direttamente da Python.
-   Ospitare ed eseguire l'applicazione Django MySQL.

Verrà ampliato l'esempio [Django Hello World](http://windowsazure.com/en-us/documentation/articles/virtual-machines-python-django-web-app-windows-server) utilizzando un database MySQL ospitato in una macchina virtuale di Azure per trovare un contenuto sostitutivo interessante per *World*. Il contenuto sostitutivo verrà a sua volta determinato tramite un'app *counter* Django supportata da MySQL. Come per l'esempio di Hello World, anche questa applicazione Django verrà ospitata in una macchina virtuale di Azure.

I file di progetto per l'esercitazione verranno archiviati nel percorso **C:\\django\\helloworld** e l'applicazione completata avrà un aspetto simile al seguente:

![](./media/virtual-machines-python-use-mysql-django/mysql_tutorial01.png)

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

Configurazione di una macchina virtuale per l'hosting di MySQL e Django
-----------------------------------------------------------------------

1.  Attenersi alle istruzioni riportate [qui](/en-us/manage/windows/tutorials/virtual-machine-from-gallery/) per creare una macchina virtuale di Azure della distribuzione *Windows Server 2008 R2*.

2.  Aprire una porta TCP per le transazioni MySQL nella macchina virtuale:
    -   Passare alla macchina virtuale appena creata nel portale di Azure e fare clic sulla scheda *ENDPOINTS*.
    -   Fare clic sul pulsante *ADD ENDPOINT* nella parte inferiore della schermata.
    -   Aggiungere un endpoint con i valori NAME = **mysql**, PROTOCOL = **TCP**, PUBLIC PORT = **3306**, PRIVATE PORT = **3306**..

3.  Aggiungere un altro endpoint con i valori NAME = **web**, PROTOCOL = **TCP**, PUBLIC PORT = **80**, PRIVATE PORT = **80**. Le richieste Internet esterne verranno in questo modo reindirizzate alla porta in cui è in esecuzione Django, in particolare *80*.

4.  Utilizzare *Desktop remoto* di Windows per accedere in remoto alla macchina virtuale di Azure appena creata.

5.  Aprire la porta TCP **80** sulla macchina virtuale:
    -   Nel menu **Start** scegliere **Strumenti di amministrazione** e quindi **Windows Firewall con protezione avanzata**.
    -   Nel riquadro sinistro selezionare **Regole connessioni in entrata**. Nel riquadro **Azioni** a destra selezionare **Nuova regola**.
    -   In **Creazione guidata nuova regola connessioni in entrata** selezionare **Porta** e quindi fare clic su **Avanti**.
    -   Selezionare **TCP** e quindi **Porte locali specifiche**. Specificare la porta "80", ovvero quella in cui rimane in ascolto Django, e fare clic su **Avanti**.
    -   Selezionare **Consenti la connessione** e fare clic su **Avanti**.
    -   Fare di nuovo clic su **Avanti**.
    -   Specificare un nome per la regola, ad esempio "DjangoPort", e fare clic su Fine.

6.  Installare la versione più recente di [MySQL Community Server](http://dev.mysql.com/downloads/mysql/) per Windows nella macchina virtuale:

    **Nota**: è consigliabile installare il database in una partizione dati diversa rispetto al sistema operativo.

    -   Eseguire *Windows (x86, 64 bit), MSI Installer* seguendo il collegamento [qui](http://dev.mysql.com/downloads/mysql/) e scaricare il programma di installazione MSI appropriato dal mirror di download più vicino. Di seguito sono riportati alcuni suggerimenti utili:
        -   Selezionare *Completa* come tipo di installazione.
        -   Selezionare una *configurazione dettagliata* nella Configurazione guidata.
        -   **Assicurarsi di abilitare il protocollo di rete TCP/IP sulla porta numero 3306 e aggiungere un'eccezione del firewall per la porta.**
        -   Impostare una password radice e abilitare l'accesso radice dai computer remoti.
    -   Installare un [database "world"](http://dev.mysql.com/doc/index-other.html) di esempio (versione MyISAM):
        -   Scaricare [questo](http://downloads.mysql.com/docs/world.sql.zip) file ZIP nella macchina virtuale di Azure.
        -   **Estrarre il file nel percorso *C:\\Users\\Administrator\\Desktop\\world.sql*.**

1.  Dopo l'installazione di MySQL, fare clic sul pulsante *Start* di Windows ed eseguire il *client della riga di comando MySQL 5.5* appena installato. Eseguire i comandi seguenti:

		CREATE DATABASE world;
		USE world;
		SOURCE C:\Users\Administrator\Desktop\world.sql
		CREATE USER 'testazureuser'@'%' IDENTIFIED BY 'testazure';
		CREATE DATABASE djangoazure;
		GRANT ALL ON djangoazure.* TO 'testazureuser'@'%';
		GRANT ALL ON world.* TO 'testazureuser'@'%';
		SELECT name from country LIMIT 1;


    La risposta visualizzata sarà simile alla seguente:

    ![](./media/virtual-machines-python-use-mysql-django/mysql_tutorial01-2.png)

1.  Prima di iniziare lo sviluppo dell'applicazione Django, è ovviamente necessario installare Python+Django nella macchina virtuale. A tale scopo, è possibile utilizzare l'[Installazione guidata piattaforma Web](http://www.microsoft.com/web/downloads/platform.aspx). Dopo aver eseguito l'Installazione guidata piattaforma Web, utilizzarla per cercare "Django" e installare il prodotto Django (Python).

    **Nota:** per il funzionamento dell'esercitazione è necessario installare solo il prodotto *Django* dall'Installazione guidata piattaforma. Non **è invece necessario installare** Python Tools for Visual Studio *o Azure Python SDK.*

1.  Installare il pacchetto client MySQL Python. È possibile installarlo direttamente da [questo collegamento](http://code.google.com/p/soemin/downloads/detail?name=MySQL-python-1.2.3.win32-py2.7.exe&can=2&q=). Al termine, eseguire il comando seguente per verificare l'installazione:

    ![](./media/virtual-machines-python-use-mysql-django/mysql_tutorial01-1.png)

Estensione dell'applicazione Hello World Django
-----------------------------------------------

1.  Attenersi alle istruzioni riportate nell'esercitazione [Django Hello World](http://windowsazure.com/en-us/documentation/articles/virtual-machines-python-django-web-app-windows-server) per creare un'applicazione Web "Hello World" fittizia in Django.

2.  Aprire **C:\\django\\helloworld\\helloworld\\settings.py** in un editor di testo. Modificare il dizionario globale **DATABASES** nel modo seguente:

         DATABASES = {
             'default': {
                 'ENGINE': 'django.db.backends.mysql',
                 'NAME': 'djangoazure',               
                 'USER': 'testazureuser',  
                 'PASSWORD': 'testazure',
                 'HOST': '127.0.0.1', 
                 'PORT': '3306',
                 },
             'world': {
                 'ENGINE': 'django.db.backends.mysql',
                 'NAME': 'world',               
                 'USER': 'testazureuser',  
                 'PASSWORD': 'testazure',
                 'HOST': '127.0.0.1', 
                 'PORT': '3306',
                 }
             }

    In questo modo, a Django è stato indicato dove trovare il database MySQL.

    **Nota:** è **necessario** modificare la chiave *HOST* in modo che corrisponda all'indirizzo IP **permanente** della macchina virtuale di Azure (MySQL). A questo punto, la chiave *HOST* dovrebbe essere impostata dove indicato dal comando *ipconfig* di Windows.

    Dopo aver modificato *HOST* in modo che corrisponda all'indirizzo IP della macchina virtuale MySQL, salvare il file e chiuderlo.

1.  Dopo aver fatto riferimento al database *djangoazure*, è necessario utilizzarlo in modo utile. A questo scopo, verrà creato un modello per un'app *counter* fittizia. Per indicare a Django di creare questa app, eseguire i comandi seguenti:

         cd C:\django\helloworld
         C:\Python27\python.exe manage.py startapp counter

    Se Django non restituisce alcun output dal comando finale precedente, significa che l'operazione è riuscita.

1.  Aggiungere il testo seguente alla fine di **C:\\django\\helloworld\\counter\\models.py**:

         class Counter(models.Model):
             count = models.IntegerField()
             def __unicode__(self):
                 return u'%s' % (self.count)

    In questo modo è stata definita una sottoclasse della classe *Model* di Django, denominata *Counter*, con un singolo campo di numeri interi, *count*. Questo modello di app counter fittizia registrerà alla fine il numero di riscontri nell'applicazione Django.

1.  A questo punto è necessario indicare a Django l'esistenza di *Counter*:

    -   Modificare di nuovo il file **C:\\django\\helloworld\\helloworld\\settings.py**. Aggiungere *'counter'* alla tupla *INSTALLED\_APPS*.
    -   Al prompt dei comandi eseguire:

              cd C:\django\helloworld
              C:\Python27\python manage.py sql counter
              C:\Python27\python manage.py syncdb

        Questi comandi archiviano il modello *Counter* nel database Django attivo e generano un output simile al seguente:

            C:\django\helloworld> C:\Python27\python manage.py sql counter
            BEGIN;
            CREATE TABLE `counter_counter` (

            `id` integer AUTO\_INCREMENT NOT NULL PRIMARY KEY,

            `count` integer NOT NULL
            )
            ;
            COMMIT;
            
            C:\django\helloworld> C:\Python27\python manage.py syncdb
            Creating tables ...
            Creating table auth_permission
            Creating table auth_group_permissions
            Creating table auth_group
            Creating table auth_user_user_permissions
            Creating table auth_user_groups
            Creating table auth_user
            Creating table django_content_type
            Creating table django_session
            Creating table django_site
            Creating table counter_counter
            
            You just installed Django's auth system, which means you don't have any superusers defined.
            Would you like to create one now
            (yes/no): no
            Installing custom SQL ...
            Installing indexes ...
            Installed 0 object(s) from 0 fixture(s)

6 .  Sostituire il contenuto di **C:\\django\\helloworld\\helloworld\\views.py**. La nuova implementazione della funzione *hello* seguente utilizza il modello *Counter* in combinazione con un database di esempio distinto installato in precedenza, *world*, per generare un contenuto sostitutivo appropriato per la stringa "*World*":

         from django.http import HttpResponse
         import django.db
         from counter.models import Counter
            
         def getCountry(intId):
             #Connect to the MySQL sample database 'world'
             cur = django.db.connections['world'].cursor()
             #Execute a trivial SQL query which returns the name of 
             #all countries contained in 'world'
             cur.execute("SELECT name from country")
             tmp = cur.fetchall()
             #Clean-up after ourselves
             cur.close()
             if intId >= len(tmp):
                 return "countries exhausted"
             return tmp[intId][0]
            
         def hello(request):
             if len(Counter.objects.all())==0:
                 #when the database corresponding to 'helloworld.counter' is 
                 #initially empty...
                 c = Counter(count=0)
             else:
                 c = Counter.objects.all()[0]
                 c.count += 1
             c.save()      
             world = getCountry(int(c.count))
             return HttpResponse("<html><body>Hello <em>" + world + "</em></body></html>")

Distribuzione ed esecuzione del sito Web Django
-----------------------------------------------

Nota: di seguito viene illustrato come eseguire Django in un ambiente di test. Per eseguirlo in produzione, attenersi alla procedura descritta nella sezione relativa alla configurazione di IIS con FastCGI nell'esercitazione "Django Hello World". L'utilizzo del client Windows Firewall per aprire la porta 80 al traffico Internet nella macchina virtuale di Windows Server 2008 R2 non è necessario con FastCGI.

1.  Passare di nuovo in una finestra di Windows PowerShell e immettere i comandi seguenti per distribuire il sito Web pubblicamente:

        PS C:\django\helloworld> $ipPort = 
        PS C:\django\helloworld> $ipPort = [string]$ipPort.AddressList[1]
        PS C:\django\helloworld> $ipPort += ":80"
        PS C:\django\helloworld> C:\Python27\python.exe .\manage.py runserver $ipPort

    Il parametro **runserver** indica a Django di eseguire il sito Web *helloworld* sulla porta TCP *80*. Il risultato del comando dovrebbe essere simile al seguente:

        PS C:\django\helloworld> C:\Python27\python.exe .\manage.py runserver $ipPort
        Validating models...
            
        0 errors found
        Django version 1.4, using settings 'helloworld.settings'
        Development server is running at http://123.34.56.78:80
        Quit the server with CTRL-BREAK.

2.  Nel Web browser locale aprire **http://*nomeVM*.cloudapp.net**, dove *nomeVM* è il nome utilizzato nel passaggio di creazione della macchina virtuale. Dovrebbe essere visualizzato "Hello ... !" come illustrato nella schermata seguente. Ciò indica che Django è in esecuzione nella macchina virtuale e funziona correttamente.

    ![](./media/virtual-machines-python-use-mysql-django/mysql_tutorial01.png)

   Aggiornare diverse volte il Web browser e in seguito il messaggio cambierà da *"Hello **&lt;paese abc\>**"* a *"Hello **&lt;altro paese\>**"*.

1.  Per interrompere l'hosting del sito Web in Django, passare in una finestra di PowerShell e premere **CTRL+C**.

Arresto della macchina virtuale di Azure
----------------------------------------

Al termine dell'esercitazione, arrestare e/o rimuovere la macchina virtuale di Azure creata per liberare le risorse per altre esercitazioni e per evitare di incorrere in addebiti per l'utilizzo di Azure.

