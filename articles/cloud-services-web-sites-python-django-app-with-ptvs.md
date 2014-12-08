<properties urlDisplayName="Django with Python Tools for Visual Studio 2.0" pageTitle="Creazione di applicazioni Django con Python Tools per Visual Studio 2.0" metaKeywords="" description="Learn how to use the Python Tools for Visual Studio to create a Django application that stores data in a SQL Database or MySQL database instance and can be deployed to a website or cloud service." metaCanonical="" services="web-sites,cloud-services" documentationCenter="Python" title="Creating Django applications with Python Tools 2.0 for Visual Studio" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />





# Creazione di applicazioni Django con Python Tools 2.0 per Visual Studio

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>In questa esercitazione si utilizzerà Python Tools 2.0 per Visual Studio per creare una semplice applicazione Django. L'applicazione permetterà agli utenti di votare a sondaggi. Inizialmente verrà usato un database sqlite3 locale, quindi si passerà a un database SQL Server o MySQL in Azure. Verrà illustrato come abilitare l'interfaccia di amministrazione Django e come usarla per aggiungere sondaggi a un database. Verrà usata anche la shell di Django integrata in Visual Studio.  Infine l'applicazione verrà distribuita in un sito Web o in un servizio cloud di Azure.</p>
<p>Se si preferisce guardare un video, nel clip a destra vengono eseguiti gli stessi passaggi dell'esercitazione.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://www.youtube.com/watch?v=wkqjafvvU5w" target="_blank" class="label">guarda l'esercitazione</a> <a style="background-image: url('/media/devcenter/python/videos/django-tutorial-180x120.png') !important;" href="http://www.youtube.com/watch?v=wkqjafvvU5w" target="_blank" class="dev-onpage-video"><span class="icon">Riproduci video</span></a> <span class="time">33:08</span></div>
</div>

Questa esercitazione si concentra su Python Tools per Visual Studio e Azure. Per informazioni più dettagliate su Django e sull'applicazione per sondaggi creata in questa esercitazione, vedere [https://www.djangoproject.com/](https://www.djangoproject.com/).

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Requisiti
Per completare l'esercitazione è necessario disporre di quanto segue:

- [Python Tools 2.0 per Visual Studio](http://pytools.codeplex.com)
- [Python 2.7 (32 bit)](http://www.python.org/download/)
- Visual Studio e Azure SDK:
  - VS 2010 Pro o versione successiva con Azure SDK 2.1
  - VS 2012 Pro o versione successiva con Azure SDK 2.1, 2.2 o versione successiva
  - VS 2013 Pro o versione successiva con Azure SDK 2.2 o versione successiva
  - Shell integrata di VS 2013 gratuita. **Azure SDK non supporta la shell integrata**. È possibile sviluppare, sottoporre a debug ed eseguire applicazioni Django in locale usando la shell integrata gratuita, ma non è possibile pubblicare in siti Web o servizi cloud di Azure usando Visual Studio.

Installare Azure SDK mediante l'Installazione guidata piattaforma Web. Verranno installati l'SDK, l'emulatore e gli strumenti di Azure per Visual Studio. In Installazione guidata piattaforma Web cercare **Azure SDK per .NET** e selezionare una delle versioni supportate dell'SDK per la versione di Visual Studio in uso.

**Nota:** Per eseguire correttamente l'applicazione usando un sito Web o un servizio cloud di Azure, sarà necessario usare la distribuzione ufficiale di CPython 2.7da [python.org](http://www.python.org/download/). È possibile che altre distribuzioni funzionino, ma non sono ufficialmente supportate.

## Download di un progetto esistente

Per procedere ai passaggi successivi dell'esercitazione, è possibile [scaricare il codice sorgente del progetto](http://download-codeplex.sec.s-msft.com/Download?ProjectName=pytools&DownloadId=783376).

Il database sqlite3 è già stato creato, con le seguenti credenziali per l'utente principale:

```
Username: tutorial
Password: azure
```

Il download NON include un ambiente virtuale. È consigliabile crearne uno seguendo la procedura descritta nella sezione [Creazione di un ambiente virtuale](#creating-a-virtual-environment). Al termine dell'operazione, il progetto è pronto per la sezione [Debug](#debugging).

## Creare il progetto

Python Tools per Visual Studio supporta gli ambienti virtuali Python.  Verrà creato un progetto Django e verrà usato un ambiente virtuale per installare le dipendenze.  Questo è il metodo consigliato per configurare progetti che vengono pubblicati in Siti Web o Servizi cloud di Azure.

1. Aprire Visual Studio, File/Nuovo progetto, applicazione di Django, con il nome **tutorial**.

	![New Project](./media/cloud-services-python-create-deploy-django-app/django-tutorial-001-new-project.png)


**Nota:** In Esplora soluzioni, in Riferimenti, sarà presente un nodo per Django 1.4. Viene usato per la distribuzione del servizio cloud di Azure, per installare Python e Django nel computer di destinazione. Non eliminare il riferimento a Django 1.4 dalla nota dei riferimenti. Poiché si usa un ambiente virtuale in cui viene installato il pacchetto Django, verrà usato il pacchetto Django installato nell'ambiente virtuale.

##<a name="creating-a-virtual-environment"></a>Creazione di un ambiente virtuale

Verrà usato un ambiente virtuale per installare le dipendenze. È buona norma per qualsiasi applicazione Python ed è necessario per la pubblicazione in Azure.

1. Creazione di un nuovo ambiente virtuale.  In Esplora soluzioni fare clic con il pulsante destro del mouse su **Python Environments** e scegliere **Add Virtual Environment**.

	![Add Virtual Environment](./media/cloud-services-python-create-deploy-django-app/django-tutorial-002-add-virtual-env.png)

1. Selezionare Python 2.7 come interprete Python di base e accettare il nome predefinito **env**. PTVS installerà pip e/o virtualenv, se non sono ancora installati.

1. Fare clic con il pulsante destro del mouse su **env** e scegliere **Install Python Package**: **django**

	![Install Django](./media/cloud-services-python-create-deploy-django-app/django-tutorial-003-install-django.png)

1. Django include un numero considerevole di file, quindi l'installazione può richiedere qualche minuto. Nella finestra di output viene visualizzato lo stato dell'operazione.

	![Install Django Output](./media/cloud-services-python-create-deploy-django-app/django-tutorial-004-install-django-output.png)

	**Nota:** in rari casi è possibile che venga visualizzato un errore nella finestra di output. In un'eventualità di questo tipo, verificare se l'errore è correlato alla pulizia. Talvolta la pulizia può avere esito negativo ma l'installazione viene comunque completata correttamente (scorrere verso l'alto nella finestra di output per verificarlo). Questo è dovuto al fatto che PTVS ottiene un blocco sulle cartelle/file temporanei appena creati, che ne impedisce l'eliminazione nel passaggio di pulitura pip.

1. Fare clic con il pulsante destro del mouse su **env** e scegliere **Install Python Package**: **pytz** (facoltativo ma consigliato, usato da Django per il supporto del fuso orario)

## Verifica dell'ambiente virtuale

1. È importante verificare che tutto sia installato correttamente. Avviare il sito Web con **F5** o **CTRL+F5**. Verranno avviati il server di sviluppo Django e il Web browser. Dovrebbe essere visualizzata la pagina seguente:

	![Django Web Browser](./media/cloud-services-python-create-deploy-django-app/django-tutorial-004b-itworked.png)

## Creazione dell'applicazione per sondaggi

In questa sezione si aggiungerà un'applicazione che gestisce i voti nei sondaggi.

Un progetto Django può includere più applicazioni. Il nome del progetto usato in questa esercitazione è 'tutorial', corrispondente al progetto di Visual Studio. Il nome dell'applicazione aggiunta è **polls**, corrispondente a una cartella nel nodo del progetto.

1. Selezionare il **nodo del progetto**, **Add**->**Django app**, con il nome **polls**. Verrà creata una cartella per l'applicazione, con codice boilerplate per i file dell'applicazione di uso comune.

	![Add Django App](./media/cloud-services-python-create-deploy-django-app/django-tutorial-005-add-django-app.png)

1. In **tutorial/settings.py**, aggiungere quanto segue a **INSTALLED\_APPS**:

		'polls',

1. e rimuovere i simboli di commento da **INSTALLED\_APPS**:

		'django.contrib.admin',

1. Sostituire **tutorial/urls.py** con il codice seguente:

        from django.conf.urls import patterns, include, url

        from django.contrib import admin
        admin.autodiscover()

        urlpatterns = patterns('',
            url(r'^', include('polls.urls', namespace="polls")),
            url(r'^admin/', include(admin.site.urls)),
        )

1. Sostituire **polls/models.py** con il codice seguente:

        from django.db import models
		
        class Poll(models.Model):
            question = models.CharField(max_length=200)
            pub_date = models.DateTimeField('date published')
			
            def __unicode__(self):
                return self.question
		
        class Choice(models.Model):
            poll = models.ForeignKey(Poll)
            choice_text = models.CharField(max_length=200)
            votes = models.IntegerField(default=0)
			
            def __unicode__(self):
                return self.choice_text

1. Sostituire **polls/views.py** con il codice seguente:

		from django.shortcuts import get_object_or_404, render
		from django.http import HttpResponseRedirect
		from django.core.urlresolvers import reverse
		from polls.models import Choice, Poll
		
		def vote(request, poll_id):
		    p = get_object_or_404(Poll, pk=poll_id)
		    try:
		        selected_choice = p.choice_set.get(pk=request.POST['choice'])
		    except (KeyError, Choice.DoesNotExist):
		        # Redisplay the poll voting form.
		        return render(request, 'polls/detail.html', {
		            'poll': p,
		            'error_message': "You didn't select a choice.",
		        })
		    else:
		        selected_choice.votes += 1
		        selected_choice.save()
		        # Always return an HttpResponseRedirect after successfully dealing
		        # with POST data. This prevents data from being posted twice if a
		        # user hits the Back button.
		        return HttpResponseRedirect(reverse('polls:results', args=(p.id,)))
		
1. Aggiungere un nuovo file Python **polls/urls.py** con il codice seguente:

	    from django.conf.urls import patterns, url
	    from django.views.generic import DetailView, ListView
	    from polls.models import Poll
	
	    urlpatterns = patterns('',
	        url(r'^$',
	            ListView.as_view(
	                queryset=Poll.objects.order_by('-pub_date')[:5],
	                context_object_name='latest_poll_list',
	                template_name='polls/index.html'),
	            name='index'),
	        url(r'^(?P<pk>\d+)/$',
	            DetailView.as_view(
	                model=Poll,
	                template_name='polls/detail.html'),
	            name='detail'),
	        url(r'^(?P<pk>\d+)/results/$',
	            DetailView.as_view(
	                model=Poll,
	                template_name='polls/results.html'),
	            name='results'),
	        url(r'^(?P<poll_id>\d+)/vote/$', 'polls.views.vote', name='vote'),
	    )
	
1. Creare un nuovo elemento **polls/admin.py** con il codice seguente:

		from django.contrib import admin
		from polls.models import Choice, Poll
		
		class ChoiceInline(admin.TabularInline):
		    model = Choice
		    extra = 3
		
		class PollAdmin(admin.ModelAdmin):
		    fieldsets = [
		        (None,               {'fields': ['question']}),
		        ('Date information', {'fields': ['pub_date'], 'classes': ['collapse']}),
		    ]
		    inlines = [ChoiceInline]
		    list_display = ('question', 'pub_date')
		    list_filter = ['pub_date']
		    search_fields = ['question']
		    date_hierarchy = 'pub_date'
		
		admin.site.register(Poll, PollAdmin)

1. Nella cartella **polls/templates** creare una nuova cartelle denominata **polls**.

1. Spostare il file **polls/templates/index.html** nella cartella **polls/templates/polls** mediante trascinamento della selezione o copia/incolla.

1. Sostituire **polls/templates/polls/index.html** con il codice seguente:

        <html>
        <head></head>
        <body>
        {% if latest_poll_list %}
            <ul>
            {% for poll in latest_poll_list %}
                <li><a href="{% url 'polls:detail' poll.id %}">{{ poll.question }}</a></li>
            {% endfor %}
            </ul>
        {% else %}
            <p>No polls are available.</p>
        {% endif %}
        </body>
        </html>

1. Creare un nuovo modello HTML Django **polls/templates/polls/detail.html** con il codice seguente:

        <html>
        <head></head>
        <body>
        <h1>{{ poll.question }}</h1>
        {% if error_message %}<p><strong>{{ error_message }}</strong></p>{% endif %}
        <form action="{% url 'polls:vote' poll.id %}" method="post">
        {% csrf_token %}
        {% for choice in poll.choice_set.all %}
            <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}" />
            <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br />
        {% endfor %}
        <input type="submit" value="Vote" />
        </form>
        </body>
        </html>

1. Creare un nuovo modello HTML Django **polls/templates/polls/results.html** con il codice seguente:

        <html>
        <head></head>
        <body>
        <h1>{{ poll.question }}</h1>
        <ul>
        {% for choice in poll.choice_set.all %}
            <li>{{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|pluralize }}</li>
        {% endfor %}
        </ul>
        <a href="{% url 'polls:detail' poll.id %}">Vote again?</a>
        </body>
        </html>

1. A questo punto il progetto dovrebbe includere i file seguenti:

	![Solution Explorer](./media/cloud-services-python-create-deploy-django-app/django-tutorial-006-solution-explorer.png)

## Creazione di un database sqlite3 in locale

L'applicazione Web è quasi pronta per l'uso, ma prima è necessario configurare un database.  Per testare il sito Web in locale, si creerà un database sqlite3.  Si tratta di un database molto semplice che non richiede ulteriori installazioni.  Il file del database verrà creato nella cartella del progetto.

1. In **tutorial/settings.py**, aggiungere il seguente codice di importazione all'inizio del file:

        from os import path

1. Aggiungere la seguente definizione nella parte superiore del file, dopo il codice di importazione:

		PROJECT_ROOT = path.dirname(path.abspath(path.dirname(__file__)))

1. Modificare la sezione DATABASES con il codice seguente:

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.sqlite3',
                'NAME': path.join(PROJECT_ROOT, 'db.sqlite3'),
                'USER': '',
                'PASSWORD': '',
                'HOST': '',
                'PORT': '',
            }
        }

1. Fare clic con il pulsante destro del mouse sul nodo del progetto e scegliere **Django**->**Django Sync DB**.  Verrà visualizzata una finestra interattiva di gestione di Django.  Poiché il database non esiste ancora, verrà chiesto di creare le credenziali di amministratore.  Immettere un nome utente e una password. L'indirizzo di posta elettronica è facoltativo.

	![Django Sync DB](./media/cloud-services-python-create-deploy-django-app/django-tutorial-007-sqlite3.png)

1. Avviare il sito Web con F5 o CTRL+F5. Verranno avviati il server di sviluppo Django e il Web browser. L'URL radice del sito Web consente di visualizzare l'indice dei sondaggi, ma non è ancora presente alcun sondaggio.

	![Web Browser](./media/cloud-services-python-create-deploy-django-app/django-tutorial-008-dev-server.png)

1. Passare a **http://localhost:{port}/admin**. Dalla finestra della console del server di sviluppo è possibile ottenere il numero di porta. Accedere usando le credenziali create nel passaggio precedente.

	![Add Poll](./media/cloud-services-python-create-deploy-django-app/django-tutorial-009-admin-login.png)

1. Usare l'interfaccia di amministrazione per aggiungere uno o due sondaggi.  Non dedicare troppo tempo all'aggiunta di sondaggi al database locale. In seguito si passerà a un database cloud e il database verrà ripopolato.

	![Poll Index](./media/cloud-services-python-create-deploy-django-app/django-tutorial-009-admin-add-poll.png)

1. Passare a **http://localhost:{port}/**.  Verrà visualizzato un indice dei sondaggi che sono stati aggiunti.

	![](./media/cloud-services-python-create-deploy-django-app/django-tutorial-010-index.png)

1. Fare clic su uno dei sondaggi per passare alla pagina di voto.

	![Poll Detail](./media/cloud-services-python-create-deploy-django-app/django-tutorial-011-detail.png)

1. Dopo l'invio del voto si verrà reindirizzati alla pagina dei risultati, in cui il conteggio dei voti dovrebbe risultare aumentato.

	![Poll Results](./media/cloud-services-python-create-deploy-django-app/django-tutorial-012-results.png)

## Utilizzo di fogli di stile e altri file statici

In questa sezione verrà modificato l'aspetto del sito usando un foglio di stile. I file statici come i fogli di stile vengono trattati in modo diverso, quindi è importante memorizzarli nella posizione corretta.

1. In **tutorial/settings.py**, individuare l'assegnazione di **STATIC_ROOT** e modificarla in:

        STATIC_ROOT = path.join(PROJECT_ROOT, 'static').replace('\\','/')

1. Nella cartella **polls** creare una nuova cartella denominata **static**.

1. Nella cartella **polls/static** creare una nuova cartella denominata **polls**.

1. Nella cartella **polls/static/polls** creare una nuova cartella denominata **images**.

1. Aggiungere un nuovo file di foglio di stile **polls/static/polls/style.css** con quanto segue:

        body {
            color: darkblue;
            background: white url("images/background.jpg");
        }
		
1. Aggiungere un'immagine esistente alla cartella **polls/static/polls/images** e denominarla **background.jpg**.

1. A questo punto il progetto dovrebbe includere i file seguenti:

	![Solution Explorer](./media/cloud-services-python-create-deploy-django-app/django-tutorial-013-solution-explorer.png)

1. Modificare l'intestazione di tutti i modelli in modo che facciano riferimento al foglio di stile con il codice seguente:

        <head>
        {% load staticfiles %}
        <link rel="stylesheet" type="text/css" href="{% static 'polls/style.css' %}" />
        </head>

1. Eseguire nuovamente il sito Web.  Le pagine di indice, sondaggio e risultati utilizzeranno il foglio di stile creato, con testo blu scuro e un'immagine di sfondo.

	![Poll Detail](./media/cloud-services-python-create-deploy-django-app/django-tutorial-014-detail-styled.png)

##<a name="debugging"></a>Debug

Python Tools per Visual Studio include un supporto speciale per il debug dei modelli di Django.

1. Aprire **polls/templates/polls/index.html** e inserire un punto di interruzione con **F9** nella riga seguente:

        {% if latest_poll_list %}

1. Avviare il debug premendo **F5**. Visual Studio si interromperà nel file del modello.

1. Selezionare **Debug->Finestre->Variabili locali** per visualizzare la finestra **Variabili locali** in cui sarà presente la variabile **latest_poll_list** con il relativo valore.

1. Come nel normale codice Python, è possibile premere **F10** per procedere.  All'interno del ciclo for è possibile esaminare il valore di **poll**:

	![Debugging](./media/cloud-services-python-create-deploy-django-app/django-tutorial-015-debugging.png)

## Creazione di un database in Azure

Dopo avere verificato il funzionamento dell'applicazione per sondaggi in locale, si utilizzerà un database ospitato in Azure.

Nelle 2 sezioni seguenti verrà illustrato come usare un database SQL e un database MySQL.  Entrambi sono servizi ospitati.

Un'altra opzione consiste nel creare una macchina virtuale e installare un server di database.  Vedere le istruzioni riportate [qui](http://www.windowsazure.com/it-it/manage/linux/common-tasks/mysql-on-a-linux-vm/) per configurare MySQL su una macchina virtuale Linux Azure.

**Nota:** È possibile usare un database sqlite3 in Azure (solo per finalità di sviluppo, non consigliato in produzione). Sarà necessario aggiungere il file **db.sqlite3** al progetto per distribuire il database con l'applicazione Django.

### Database SQL

In questa sezione verrà creato un database SQL in Azure, verranno aggiunti i pacchetti necessari all'ambiente virtuale e verranno modificate le impostazioni in modo da usare il nuovo database.

1. Nel portale di gestione di Azure selezionare **SQL DATABASES**.

1. Creare innanzitutto un server in cui ospitare il database.  Selezionare **SERVERS** e **ADD**.

1. Nella scheda **CONFIGURE** del server appena creato sarà visualizzato l'indirizzo IP del client corrente. Accanto a esso fare clic su **ADD TO THE ALLOWED IP ADDRESSES**.

	**Nota:** In alcuni casi Azure non rileva correttamente l'indirizzo IP del client. Se viene visualizzato un errore durante la sincronizzazione del database, copiare/incollare l'indirizzo IP indicato nel messaggio di errore e aggiungerlo agli indirizzi IP consentiti.

1. A questo punto si creerà il database. Nella scheda **DATABASES** fare clic su **ADD** sulla barra inferiore.

1. In Visual Studio verranno installati i pacchetti necessari per accedere ai database di SQL Server da Django nell'ambiente virtuale.

1. Fare clic con il pulsante destro del mouse su **env** e scegliere **Install Python Package**: **pyodbc** usando **easy\_install**.

1. Fare clic con il pulsante destro del mouse su **env** e scegliere **Install Python Package**: **django-pyodbc-azure** usando **pip**.

1. Modificare **tutorial/settings.py** e modificare la definizione **DATABASES** nel modo seguente, sostituendo **NAME**, **USER**, **PASSWORD** e **HOST** con i valori elencati nel pannello di controllo di ClearDB:

        DATABASES = {
            'default': {
                'ENGINE': 'sql_server.pyodbc',
                'NAME': '<database name>',
                'USER': '<user name>@<server name>',
                'PASSWORD': '<user password>',
                'HOST': '<server name>.database.windows.net',
                'PORT': '',
                'OPTIONS': {
                    'driver': 'SQL Server Native Client 11.0',
                    'MARS_Connection': True,
                },
            }
        }

	Sarà necessario assicurarsi di usare un driver installato nel computer in uso.  Aprire **Strumenti di amministrazione** dal menu o dalla schermata Start, quindi **Origini dati ODBC (32 bit)**. I driver sono elencati nella scheda **Driver** tab.
	
	Per l'esecuzione su un sito Web di Azure funzioneranno sia **SQL Server Native Client 10.0** che **SQL Server Native Client 11.0**.

	Per l'esecuzione su un servizio cloud di Azure funzionerà solo **SQL Server Native Client 11.0**.
	
1. Sincronizzare il database e creare le credenziali di amministratore come per il database sqlite3 locale.

### Database MySQL

In questa sezione verrà creato un database MySQL in Azure, verranno aggiunti i pacchetti necessari all'ambiente virtuale e verranno modificate le impostazioni in modo da usare il nuovo database.

In Azure Store è possibile aggiungere vari servizi all'account, incluso un database MySQL.  È possibile creare un account di valutazione gratuita e i database di piccola entità sono gratuiti fino a una certa dimensione.

1. Nel portale di gestione di Azure selezionare **NEW**->**STORE**->**APP SERVICES**->**ClearDB MySQL Database**.  Creare un database con il piano gratuito.

1. In seguito verranno installati i pacchetti necessari per accedere ai database di MySQL da Django nell'ambiente virtuale.  Fare clic con il pulsante destro del mouse su **env** e scegliere **Install Python Package**: **mysql-python** usando **easy\_install**.

1. Modificare **tutorial/settings.py** e modificare la definizione **DATABASES** nel modo seguente, sostituendo **NAME**, **USER**, **PASSWORD** e **HOST** con i valori elencati nel pannello di controllo di ClearDB:

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '<database name>',
                'USER': '<user name>',
                'PASSWORD': '<user password>',
                'HOST': '<host url>',
                'PORT': '',
            }
        }

1. Sincronizzare il database e creare le credenziali di amministratore come per il database sqlite3 locale.

## Utilizzo della shell di Django

1. Fare clic con il pulsante destro del mouse sul nodo del progetto e scegliere **Django** -> **Open Django Shell**.

1. In questa finestra interattiva è possibile accedere al database usando i modelli disponibili.  Immettere il codice seguente per aggiungere un sondaggio al database:

		from polls.models import Poll, Choice
		from django.utils import timezone

		p = Poll(question="Favorite way to host Django on Azure?", pub_date=timezone.now())
		p.save()

		p.choice_set.create(choice_text='Web Site', votes=0)
		p.choice_set.create(choice_text='Cloud Service', votes=0)
		p.choice_set.create(choice_text='Virtual Machine', votes=0)

	![Django Shell Add Poll](./media/cloud-services-python-create-deploy-django-app/django-tutorial-018-shell-add-poll.png)

1. L'analisi statica dei modelli fornisce una vista limitata dell'API completa.  La finestra interattiva consente di usare IntelliSense per gli oggetti attivi e rappresenta quindi un ottimo modo per esplorare l'API.  Di seguito sono indicate alcune opzioni da provare nella finestra interattiva:

		# all poll objects
		Poll.objects.all()

		# primary key for poll
		p.id

		# all choice objects for the poll
		p.choice_set.all()

		# get object by primary key
		Poll.objects.get(pk=1)

	![Django Shell Query Poll](./media/cloud-services-python-create-deploy-django-app/django-tutorial-019-shell-query.png)

1. Avviare il sito Web.  Dovrebbe essere visualizzato il sondaggio aggiunto usando la shell di Django.

## Pubblicazione in Azure

Dopo avere inserito il database in Azure, il passaggio successivo consiste nell'ospitare il sito Web stesso in Azure.

Azure presenta alcune opzioni per l'hosting di applicazione Django:

- [Sito Web](http://www.windowsazure.com/it-it/services/web-sites/)
- [Servizio cloud](http://www.windowsazure.com/it-it/services/cloud-services/)
- [Macchina virtuale](http://www.windowsazure.com/it-it/services/virtual-machines/)

Python Tools per Visual Studio include funzionalità di pubblicazione per Siti Web e Servizi cloud di Azure.  Queste funzionalità sono descritte nelle prossime 2 sezioni ed è possibile sceglierne una o entrambe.

In entrambi i casi, la configurazione di IIS viene eseguita automaticamente da PTVS e verrà generato un file web.config, se non ancora presente nel progetto.  I file statici verranno raccolti automaticamente (manage.py collectstatic) purché sia stato impostato STATIC_ROOT in settings.py.

L'hosting di Django in una macchina virtuale esula dall'ambito di questa esercitazione.  Implica la creazione di una macchina virtuale con il sistema operativo desiderato (Windows or Linux), l'installazione di Python e la distribuzione manuale dell'applicazione Django. 

### Sito Web di Azure

1. In primo luogo è necessario creare un sito Web.  Nel portale di gestione di Azure fare clic su **NUOVO**->**CALCOLO**->**SITO WEB**->**CREAZIONE RAPIDA**.  Scegliere uno qualsiasi dei nomi disponibili.

1. Dopo la creazione, scaricare il profilo di pubblicazione per il sito Web.

	![Web Site Download Profile](./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website-download-profile.png)

1. In Visual Studio fare clic con il pulsante destro del mouse sul nodo del progetto e scegliere **Pubblica**.

	![Web Site Publish](./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website-publish.png)

1. Importare il profilo di pubblicazione del sito Web scaricato in precedenza.

1. Accettare le impostazioni predefinite e fare clic su **Pubblica** per avviare la pubblicazione.

1. Al termine della pubblicazione, verrà aperto un Web browser e verrà visualizzato il sito Web pubblicato.

	![Web Site Browser](./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website.png)

### Servizio cloud di Azure

1. Fare clic con il pulsante destro del mouse sul nodo del progetto e scegliere **Aggiungi progetto Servizi cloud di Azure** oppure **Converti -> Converti nel progetto di servizio cloud di Azure** (verrà visualizzata l'una o l'altra opzione, a seconda della versione di Visual Studio in uso).  Verrà aggiunto un nuovo progetto alla soluzione, con il suffisso .Azure. Questo nuovo progetto è contrassegnato come progetto di avvio nella soluzione.

	**Nota:** Se il comando per creare il progetto servizio cloud di Azure non è disponibile nel menu di scelta rapida del progetto, è necessario installare Azure Tools per Visual Studio. Questi strumenti vengono installati insieme a Azure SDK per .NET. Vedere la sezione relativa ai requisiti all'inizio di questa esercitazione.

	![Solution Explorer](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-solution-explorer.png)

#### Esecuzione nell'emulatore di Azure

1. Per l'esecuzione nell'emulatore di calcolo è necessario **riavviare Visual Studio come amministratore**.

1. Avviare il debug con **F5**. L'app verrà eseguita e distribuita nell'emulatore di calcolo. Verificare che l'interfaccia di amministrazione funzioni e che sia possibile votare nei sondaggi.

	![Compute Emulator](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-emulator.png)

1. A questo punto è possibile riavviare Visual Studio, se non si desidera continuare l'esecuzione come amministratore.

#### Pubblicazione nel servizio cloud di Azure

1. Il servizio cloud verrà quindi pubblicato in Azure. Fare clic con il pulsante destro del mouse sul progetto di servizio cloud **tutorial.Azure** e scegliere **Pubblica**.

	**Nota:** Assicurarsi di selezionare **Publish** nel progetto servizio cloud. Verrà visualizzata la finestra di dialogo **Publish Azure Application** che consente di pubblicare in un servizio cloud di Azure  Se si seleziona **Pubblica** nel progetto Django verrà visualizzata la finestra di dialogo **Pubblica sito Web** che permette di pubblicare in un sito Web di Azure.  

	![Cloud Service Publish](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-publish.png)

1. Sarà necessario importare il file della sottoscrizione di Azure.  Fare clic sul collegamento [Accedi per scaricare le credenziali](https://manage.windowsazure.com/publishsettings/index?client=vs&schemaversion=2.0) per scaricarlo dal portale di Azure.

1. Nella pagina di impostazioni selezionare **Crea nuovo** nella casella combinata Servizio cloud per creare un nuovo servizio cloud. È possibile usare uno qualsiasi dei nomi disponibili.

	![Cloud Service Settings](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-settings.png)

1. Accettare le impostazioni predefinite e fare clic su **Pubblica**. Questa operazione è più lunga rispetto alla pubblicazione in un sito Web poiché richiede il provisioning di una macchina virtuale per il servizio cloud. Nella finestra Azure Activity Log viene visualizzato lo stato dell'operazione:

	![Cloud Service Publish](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-publish-progress.png)

1. Al termine dell'operazione, fare clic sull'URL del sito Web nella finestra Azure Activity Log per aprire un Web browser.

	![Cloud Service Browser](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice.png)

## Conclusioni

In questa esercitazione è stata sviluppata un'applicazione Django usando [Python Tools per Visual Studio](http://pytools.codeplex.com).  Sono stati usati 3 diversi database: database di sqlite3, SQL Server e MySQL.  Infine, l'applicazione è stata pubblicata in Siti Web di Azure e Servizi cloud di Azure.
