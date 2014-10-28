<properties linkid="develop-python-django-with-visual-studio" urlDisplayName="Django with Visual Studio" pageTitle="Django with Visual Studio (Python) - Azure tutorial" metaKeywords="Azure Django web app, Azure Django virtual machine" description="A tutorial that teaches you how to build a Django web application hosted in an Azure virtual machine." metaCanonical="" services="cloud-services" documentationCenter="Python" title="Creating Django applications with Python Tools for Visual Studio 1.5" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo"></tags>

# Creazione di applicazioni Django con Python Tools per Visual Studio 1.5

**Nota:** Questa esercitazione è anche incapsulata in un [video di Youtube][video di Youtube].

**Nota:** è disponibile un'[esercitazione più recente e dettagliata][esercitazione più recente e dettagliata] destinata a PTVS 2.0 Beta.

Sviluppare applicazioni per Azure è facile quando si usano gli strumenti disponibili.
Nell'esercitazione si presuppone che l'utente non abbia già usato in precedenza Azure.
Al termine, si otterrà una semplice applicazione Django in esecuzione nel cloud.

Si apprenderà come:

-   Creare una semplice applicazione Django
-   Eseguire e sottoporre a debug l'applicazione Django localmente, usando il server di test Django
-   Eseguire l'applicazione Django localmente nell'emulatore di calcolo
-   Pubblicare e ripubblicare l'applicazione in Azure.

Seguendo questa esercitazione, verrà creata una semplice applicazione Web Hello World.
 L'applicazione verrà ospitata in un'istanza di un ruolo Web che, quando eseguito in Azure, verrà a sua volta ospitato in una macchina virtuale dedicata.

Di seguito è riportata una schermata dell'applicazione completata:

![][]

[WACOM.INCLUDE [create-account-note][create-account-note]]

## <span id="setup"></span> </a>Configurazione dell'ambiente di sviluppo

Prima di iniziare a usare l'applicazione di Azure, è necessario ottenere gli strumenti idonei e configurare l'ambiente di sviluppo. Per dettagli su come ottenere e configurare Azure SDK per Python, vedere l'articolo relativo all'[installazione di Python][installazione di Python].

**Nota:** per completare questa esercitazione, è necessario disporre di Python 2.7 e Django 1.4. Queste versioni sono incluse nella versione corrente di Azure SDK per Python.

**Nota:** la distribuzione nell'emulatore di calcolo e/o in Azure richiede la versione completa di Visual Studio (la shell integrata non è supportata).

## Creazione di una nuova applicazione Django

Per creare una nuova applicazione Django, avviare innanzitutto Visual Studio, quindi creare un nuovo progetto usando **File \> Nuovo progetto**. Trovare la scheda Python (disponibile nel primo livello oppure nell'area Altri linguaggi) quindi selezionare il modello di applicazione Django:

![Modelli nuovo progetto Python][Modelli nuovo progetto Python]

**Fare clic su OK** e si sarà creata la prima applicazione Django.

![Visual Studio aperto con il nuovo progetto Django][Visual Studio aperto con il nuovo progetto Django]

Sarà a questo punto possibile iniziare a sviluppare la prima applicazione Django. Per impostare una nuova applicazione all'interno del progetto Django, è possibile fare clic con il pulsante destro del mouse sul nodo del modulo e selezionare **Add New Django app**:

![Voce di menu per l'aggiunta di una nuova app][Voce di menu per l'aggiunta di una nuova app]

Immettere il nuovo nome per l'applicazione:

![Prompt per l'aggiunta del nuovo nome per l'app][Prompt per l'aggiunta del nuovo nome per l'app]

Dopo aver immesso un nome per l'applicazione e aver fatto clic su **OK**,verrà aggiunta una nuova app al progetto:

![Esplora soluzioni con la nuova app aggiunta][Esplora soluzioni con la nuova app aggiunta]

A questo punto, aggiornare **settings.py** per registrare l'applicazione. In questo modo, Django individuerà automaticamente i file di modello aggiunti alla directory dei modelli delle applicazioni. Aggiungere il nome dell'applicazione alla sezione INSTALLED\_APPS:

    'DjangoApplication.MyFirstApp',

![Aggiunta dell'app a settings.py in INSTALLED\_APPS][Aggiunta dell'app a settings.py in INSTALLED\_APPS]

A questo punto, aggiungere del codice a **views.py** in modo da restituire un semplice file di modello:

    from django.http import HttpResponse
    from django.template.loader import render_to_string
    def home(request):
        return HttpResponse(render_to_string(
                                            'index.html',
                                            {'content': 'Hello World'}
                                            ))

![Aggiunta dell'app a settings.py in INSTALLED\_APPS][1]

Aggiungere quindi un file di modello semplice di cui verrà eseguito il rendering quando si visita questa visualizzazione. A questo scopo, fare clic con il pulsante destro del mouse sulla cartella Modelli e selezionare **"Aggiungi nuovo elemento"**:

![Aggiunta di un nuovo elemento alla cartella Modelli][Aggiunta di un nuovo elemento alla cartella Modelli]

È ora possibile selezionare "Django HTML Template" dall'elenco dei modelli e immettere **index.html** come nome file:

![Aggiunta di un nuovo elemento alla cartella Modelli][2]

A questo punto il modello viene aggiunto al progetto e aperto. Di seguito è riportato un inizio di evidenziazione di sintassi per i tag del modello:

![Modello aggiunto a Esplora soluzioni][Modello aggiunto a Esplora soluzioni]

È a questo punto possibile proseguire iniziando ad aggiornare il modello per modificare il rendering HTML e sfruttare la funzionalità Intellisense per l'operazione:

![Intellisense nei modelli per i filtri Django][Intellisense nei modelli per i filtri Django]

È possibile includere o meno la capitalizzazione, poiché qualunque la scelta, non avrà molto effetto sul risultato di questa esercitazione. Sarà infine necessario registrare la visualizzazione con i modelli URL in **urls.py**. Aggiungere il codice seguente a **urlpatterns**:

    url(r'^$', 'DjangoApplication.MyFirstApp.views.home', name='home'),

![Registrazione URL][Registrazione URL]

## Esecuzione dell'applicazione in locale nel server di test

A questo punto si è creata la prima applicazione Django. Per eseguirla localmente, sarà sufficiente **premere F5**.

![Django Hello World in un browser del server di test][Django Hello World in un browser del server di test]

Verrà avviato l'interprete Python con **manage.py** di Django per l'esecuzione del server di test. Dopo il corretto avvio del server di test, verrà inoltre avviato un Web browser per la visualizzazione del sito Web. Poiché l'esecuzione è stata avviata premendo F5, questo verrà avviato nel debugger e sarà quindi possibile impostare punti di interruzione in qualsiasi parte del codice Python, ad esempio nel codice di visualizzazione o direttamente nei file di modello stessi.

![Debugger arrestato in corrispondenza di un punto di interruzione nel modello][Debugger arrestato in corrispondenza di un punto di interruzione nel modello]

È ora possibile **fare clic sul pulsante di arresto** e passare all'esecuzione nell'emulatore di calcolo di Azure.

## Eseguire l'applicazione in locale nell'emulatore

Per l'esecuzione nell'emulatore di calcolo è sufficiente aggiungere alla soluzione un progetto di distribuzione di Azure per il progetto Django.

**Nota:** la distribuzione nell'emulatore di calcolo e/o in Azure richiede la versione completa di Visual Studio (la shell integrata non è supportata).

Per eseguire questa operazione, fare clic con il pulsante destro del mouse sul nodo del progetto Django in Esplora soluzioni e selezionare **"Add Azure Cloud Service Project"**:

![Aggiunta di un progetto di distribuzione][Aggiunta di un progetto di distribuzione]

Dopo l'esecuzione di questo comando, il nuovo progetto aggiunto sarà visibile in Esplora soluzioni:

![Dopo l'aggiunta del progetto di distribuzione][Dopo l'aggiunta del progetto di distribuzione]

Il nuovo progetto è ora anche contrassegnato come progetto di avvio nella soluzione. A questo punto sarà necessario **riavviare Visual Studio come amministratore** per rendere possibile l'esecuzione nell'emulatore di calcolo, ma dopo di ciò sarà sufficiente **premere F5** per eseguire l'applicazione e distribuirla nell'emulatore di calcolo:

![Dopo l'aggiunta del progetto di distribuzione][3]

Si noterà che la pagina Web è la stessa, ma con un URL leggermente diverso. Si noterà inoltre che python.exe non è in esecuzione nel server di test Django. Django viene infatti eseguito tramite IIS usando un gateway FastCGI incluso e impostato automaticamente quando si lavora da Visual Studio.

Durante l'esecuzione nell'emulatore di calcolo è possibile scorrere rapidamente l'applicazione: è sufficiente tornare a Visual Studio, aggiornare i file e quindi aggiornare il browser. I risultati saranno immediatamente visibili.

## Distribuire l'applicazione in Azure

È ora possibile distribuire il progetto in Azure. A questo scopo sarà sufficiente fare clic con il pulsante destro del mouse sul progetto di distribuzione di Azure in Esplora soluzioni, quindi selezionare **Pubblica**:

![Menu creazione pacchetto app][Menu creazione pacchetto app]

Dopo avere selezionato Pubblica verrà visualizzata la richiesta di accedere ad Azure. È possibile importare le credenziali esistenti o impostarne di nuove:

![Sottoscrizione pacchetto][Sottoscrizione pacchetto]

Dopo avere selezionato le credenziali verrà visualizzata la schermata Impostazioni di pubblicazione Azure. È possibile selezionare le varie opzioni che determineranno le modalità di distribuzione, oppure scegliere semplicemente **Pubblica**:

![Impostazioni pacchetto][Impostazioni pacchetto]

Sarà ora necessario attendere che l'applicazione venga impostata e distribuita.

![Sviluppo pacchetto][Sviluppo pacchetto]

Al termine dell'impostazione, è possibile fare clic sul collegamento sotto Nome DNS per visualizzare il sito Web in esecuzione nel cloud:

![L'applicazione Django nel cloud](./media/python-django-app-tools-visual-studio/ptvs-dj-FirstAppInCloud.png)
  [video di Youtube]: http://www.youtube.com/watch?v=UsLti4KlgAY
  [esercitazione più recente e dettagliata]: ../web-sites-python-create-deploy-django-app/
  []: ./media/python-django-app-tools-visual-studio/ptvs-dj-FirstAppInCloud.png
  [create-account-note]: ../includes/create-account-note.md
  [installazione di Python]: ../python-how-to-install/
  [Modelli nuovo progetto Python]: ./media/python-django-app-tools-visual-studio/ptvs-dj-NewProject.png
  [Visual Studio aperto con il nuovo progetto Django]: ./media/python-django-app-tools-visual-studio/ptvs-dj-FirstProject.png
  [Voce di menu per l'aggiunta di una nuova app]: ./media/python-django-app-tools-visual-studio/ptvs-dj-AddNewApp.png
  [Prompt per l'aggiunta del nuovo nome per l'app]: ./media/python-django-app-tools-visual-studio/ptvs-dj-AddNewAppPrompt.png
  [Esplora soluzioni con la nuova app aggiunta]: ./media/python-django-app-tools-visual-studio/ptvs-dj-MyFirstApp.png
  [Aggiunta dell'app a settings.py in INSTALLED\_APPS]: ./media/python-django-app-tools-visual-studio/ptvs-dj-InstallApp.png
  [1]: ./media/python-django-app-tools-visual-studio/ptvs-dj-FirstView.png
  [Aggiunta di un nuovo elemento alla cartella Modelli]: ./media/python-django-app-tools-visual-studio/ptvs-dj-AddFirstTemplate.png
  [2]: ./media/python-django-app-tools-visual-studio/ptvs-dj-NewDjangoTemplate.png
  [Modello aggiunto a Esplora soluzioni]: ./media/python-django-app-tools-visual-studio/ptvs-dj-TemplateAdded.png
  [Intellisense nei modelli per i filtri Django]: ./media/python-django-app-tools-visual-studio/ptvs-dj-TemplateIntellisense.png
  [Registrazione URL]: ./media/python-django-app-tools-visual-studio/ptvs-dj-RegisterUrl.png
  [Django Hello World in un browser del server di test]: ./media/python-django-app-tools-visual-studio/ptvs-dj-DjangoHelloWorldTestServer.png
  [Debugger arrestato in corrispondenza di un punto di interruzione nel modello]: ./media/python-django-app-tools-visual-studio/ptvs-dj-TemplateBreakpoint.png
  [Aggiunta di un progetto di distribuzione]: ./media/python-django-app-tools-visual-studio/ptvs-dj-AddDeploymentProject.png
  [Dopo l'aggiunta del progetto di distribuzione]: ./media/python-django-app-tools-visual-studio/ptvs-dj-AfterDeployProjAdded.png
  [3]: ./media/python-django-app-tools-visual-studio/ptvs-dj-ComputeEmulator.png
  [Menu creazione pacchetto app]: ./media/python-django-app-tools-visual-studio/ptvs-dj-publish1.png
  [Sottoscrizione pacchetto]: ./media/python-django-app-tools-visual-studio/ptvs-dj-publish2.png
  [Impostazioni pacchetto]: ./media/python-django-app-tools-visual-studio/ptvs-dj-publish3.png
  [Sviluppo pacchetto]: ./media/python-django-app-tools-visual-studio/ptvs-dj-publish4.png
