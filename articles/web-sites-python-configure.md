<properties linkid="develop-python-tutorials-web-sites-configuration" urlDisplayName="Configuring Python with Azure Web Sites" pageTitle="Configuring Python with Azure Web Sites" metaKeywords="" description="This tutorial describes options for authoring and configuring a basic Web server Gateway Interface (WSGI) compliant Python application on Azure Web Sites." metaCanonical="" services="web-sites" documentationCenter="Python" title="Configuring Python with Azure Web Sites" authors="" solutions="" manager="" editor="" />

Configurazione di Python con Siti Web di Azure
==============================================

In questa esercitazione verranno descritte le opzioni per la creazione e la configurazione di un'applicazione Python di base conforme con l'interfaccia WSGI (Web Server Gateway Interface) sui siti Web di Azure. È facile iniziare a utilizzare i siti Web di Azure e sarà possibile scalare ed estendere l'applicazione Python ad altri servizi di Azure. La piattaforma Siti Web di Azure include Python 2.7 il gestore wfastcgi.py FastCGI generico per Python. Basta configurare il proprio sito Web in modo da utilizzare il gestore Python.

Per un esempio più complesso di configurazione del framework Django sulla piattaforma Siti Web di Azure, vedere l'esercitazione seguente: <http://www.windowsazure.com/en-us/develop/python/tutorials/web-sites-with-django>.

Supporto WSGI
-------------

WSGI è uno standard Python descritto da [PEP 3333](http://www.python.org/dev/peps/pep-3333/) che definisce un'interfaccia tra il server Web e Python che consente di scrivere varie applicazioni e framework Web tramite Python. Oggi i più comuni framework Web Python utilizzano l'interfaccia WSGI. Siti Web di Azure offre assistenza per qualsiasi framework; inoltre, gli utenti avanzati possono persino creare il proprio framework a patto che il gestore personalizzato si attenga alle linee guida delle specifiche WSGI.

Creazione di un sito Web
------------------------

In questa esercitazione si presume che sia stata eseguita una sottoscrizione di Azure e sia stato effettuato l'accesso al portale di gestione Azure. Linee guida dettagliate sulla creazione di un sito Web sono disponibili all'indirizzo <http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-create-websites>.

In breve, se non si dispone di un sito Web esistente è possibile crearne uno dal portale di gestione di Azure. Selezionare la funzionalità WEB SITES e quindi l'opzione QUICK CREATE, specificando un URL per il proprio sito Web.

![](./media/web-sites-python-configure/configure-python-create-website.png)

Pubblicazione Git
-----------------

Utilizzare le schede QUICK START o DASHBOARD per il sito Web appena creato per configurare la pubblicazione Git. In questa esercitazione viene utilizzato Git per creare, gestire e pubblicare il nostro sito Web Python sulla piattaforma Siti Web di Azure.

![](./media/web-sites-python-configure/configure-python-git.png)

Dopo aver configurato la pubblicazione Git, verrà creato un apposito archivio che sarà quindi associato al sito Web. L'URL dell'archivio verrà visualizzato e potrà pertanto essere utilizzato per effettuare il push dei dati dall'ambiente di sviluppo locale al cloud. Per pubblicare le applicazioni tramite Git, assicurarsi che sia stato installato anche il client Git e attenersi alle istruzioni fornite per effettuare il push dei contenuti del sito Web sulla piattaforma Siti Web di Azure.

Contenuto del sito Web
----------------------

Come esempio si utilizzerà un'applicazione Python di base con un gestore WSGI di base che illustra la quantità minima di attività necessaria per sfruttare l'assistenza Python sulla piattaforma Siti Web di Azure. L'applicazione Python scheletro potrà quindi essere utilizzata per avviare la creazione di una serie di soluzioni la cui complessità varia dall'esempio sotto riportato fino a un framework Web completo.

Di seguito è riportato il codice per il gestore WSGI di base, simile a quello suggerito dalla specifica [PEP 3333](http://www.python.org/dev/peps/pep-3333/) come punto di partenza per un'applicazione conforme con WSGI. Il contenuto è stato salvato in un file denominato ConfigurePython.py e creato in una cartella ConfigurePython nella directory principale del sito Web:

    def application(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type', 'text/plain')]
        start_response(status, response_headers)
        yield 'Hello from Azure Websites\n'

Per *application* si intende un'applicazione Python chiamabile che fungerà da punto di ingresso chiamato da un server conforme con WSGI. Questo oggetto chiamabile accetta due argomenti posizionali:

-   *environ*: un dizionario con molte variabili di ambiente
-   *start\_response*: un oggetto chiamabile fornito dal server Web per il trasferimento dell'intestazione dello stato e di risposta HTTP

Questo gestore restituirà il testo normale "Hello from Azure Websites" per ogni richiesta effettuata.

Opzioni di configurazione
-------------------------

Vi sono due opzioni differenti per la configurazione dell'applicazione Python con Siti Web di Azure.

### Opzione 1: Portale

1.1. Registrare il gestore FastCGI tramite la scheda CONFIGURE nel portale. Per questo esempio si utilizzerà il gestore FastCGI per Python incluso con Siti Web di Azure. A tale scopo, utilizzare i percorsi seguenti per l'elaboratore di script e l'argomento gestore FastCGI:

-   Percorso dell'elaboratore di script Python: D:\\python27\\python.exe
-   Percorso gestore FastCGI Python: D:\\python27\\scripts\\wfastcgi.py

![](./media/web-sites-python-configure/configure-python-handler-mapping.png)

1.2. Configurare le impostazioni applicazione nella stessa scheda CONFIGURE del portale. Le impostazioni applicazione verranno convertite in variabili di ambiente. Questo meccanismo può essere utilizzato per i valori di configurazione richiesti dall'applicazione Python. Per quest'applicazione di esempio di base sono stati configurati i seguenti dati:

-   PYTHONPATH informa Python della directory in cui eseguire la ricerca di moduli. Siti Web di Azure fornisce il percorso D:\\home\\site\\wwwroot come zucchero sintattico che punta alla directory principale del sito Web.
-   WSGI\_HANDLER registra un nome di pacchetto o modulo e l'attributo da utilizzare.

![](./media/web-sites-python-configure/configure-python-app-settings.png)

### Opzione 2: web.config

La configurazione alternativa consiste nell'utilizzare un file web.config file nella directory principale del sito Web per le azioni descritte di seguito. L'utilizzo dell'opzione web.config offre un miglior potenziale di portabilità per un'applicazione Web. Sono disponibili due approcci alle richieste di route all'applicazione Web: impostare un gestore che gestisca il percorso \*, in modo da istruire IIS a indirizzare ogni richiesta ricevuta attraverso Python, oppure impostare un percorso specifico che Python gestirà per poi successivamente adoperare la Riscrittura URL al fine di reindirizzare vari URL al percorso selezionato. In effetti, per ottenere migliori prestazioni è consigliabile adottare il secondo approccio, ossia utilizzare un file gestore vuoto nella directory principale del sito Web affinché funga da destinazione della richiesta (handler.fcgi nell'esempio fornito). Nello scenario precedente, tutte le richieste, incluse quelle di contenuti statici (ad esempio, file immagine e fogli di stile) dovranno passare attraverso Python, minando le ottimizzazioni fornite dal server Web per accedere ai file statici. L'adozione del secondo approccio consente di gestire contenuti statici in maniera efficiente e di richiamare Python solo quando è necessario.

2.1. Specificare la variabile PYTHONPATH. \> In tal modo, Python verrà informato sul percorso in cui cercare il codice dell'applicazione. In questa esercitazione viene utilizzato il percorso D:\\home\\site\\wwwroot anche come percorso assoluto al sito Web.

2.2. Impostare la variabile WSGI\_HANDLER. \> Siti Web di Azure utilizza questo valore per indirizzare Python a chiamare il gestore WSGI. Il valore di questa variabile è un'espressione Python che dovrebbe, alla sua esecuzione, restituire un oggetto chiamabile che rappresenti un gestore WSGI.

2.3. Aggiungere un gestore per Python. \> In tal modo, la piattaforma Siti Web di Azure verrà informata dell'opportunità che Python gestisca le richieste inviate al percorso handler.fcgi. È importante che la sintassi del gestore sia esattamente identica al contenuto del tag &lt;handlers\> nell'esempio seguente, a meno che non si fornisca il proprio gestore FastCGI o stack di sviluppo Python.

2.4. Riscrivere l'URL come handler.fcgi. \> Richiedere sempre handler.fcgi potrebbe non essere una buona idea. Per selezionare il percorso dei file da assegnare al gestore Python è stata utilizzata la Riscrittura URL, pertanto tutti gli URL vengono presi in carico dal gestore Python.

    <configuration>

          <add key="pythonpath" value="D:\home\site\wwwroot\ConfigurePython" />
            <add key="WSGI_HANDLER" value="ConfigurePython.application" />

	</appSettings\> <system.webServer\>

         <handlers>
                <add name="PythonHandler" 
                verb="*" path="handler.fcgi" 
                modules="FastCgiModule" 
                scriptProcessor="D:\Python27\Python.exe|D:\Python27\Scripts\wfastcgi.py" 
                resourceType="Either" />

		</handlers\>

         <rewrite>
                <rules>
                    <rule name="Configure Python" stopProcessing="true">
                        <match url="(.*)" ignoreCase="false" />
                        <conditions>
                            <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
                        </conditions>
                        <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
                    </rule>
                </rules>
            </rewrite>

	</system.webServer\>

    </configuration> 

La struttura di cartelle per l'esempio nella directory principale del sito Web è la seguente (è importante operare la distinzione tra maiuscole e minuscole nei nomi della cartella e dei file Python, come si può osservare in web.config):

-   ConfigurePython\\ConfigurePython.py
-   web.config
-   handler.fcgi

Poiché si stanno riscrivendo tutti gli URL come handler.fcgi e si gestisce il percorso attraverso FastCGI fino a Python, è necessario creare un file segnaposto con lo stesso nome, in modo che IIS non restituisca un errore HTTP 404. Ciò è dovuto al comportamento interno del modulo IIS FastCGI, che richiede l'esistenza del file richiesto prima che sia passato all'applicazione dell'elaboratore di script specificata.

Esplorare il sito Web per testare la corretta configurazione. Per questo esempio, il messaggio "Hello from Azure Websites" è visibile all'accesso.

![](./media/web-sites-python-configure/configure-python-result.png)

