<properties 
	pageTitle="Configurazione di Python con Siti Web di Azure" 
	description="Questa esercitazione descrive le opzioni per la creazione e la configurazione di un'applicazione Python di base conforme all'interfaccia WSGI (Web Server Gateway Interface) in Siti Web di Azure." 
	services="app-service\web" 
	documentationCenter="python" 
	tags="python"
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/09/2015" 
	ms.author="huguesv"/>




# Configurazione di Python con Siti Web di Azure

Questa esercitazione descrive le opzioni per la creazione e la configurazione di un'applicazione Python di base conforme all'interfaccia WSGI (Web Server Gateway Interface) in Siti Web di Azure.

Descrive le funzionalità aggiuntive della distribuzione Git, ad esempio l'ambiente virtuale e l'installazione del pacchetto con requirements.txt.


## Bottle, Django o Flask?

La raccolta di Azure contiene modelli per i framework Bottle, Django e Flask.  Se si sta sviluppando il primo sito Web di Azure o non si ha familiarità con Git, è consigliabile seguire una delle seguenti esercitazioni, che includono istruzioni dettagliate per la creazione di un'applicazione funzionante dalla raccolta tramite la distribuzione Git da Windows o Mac:

- [Creazione di siti Web con Bottle][]
- [Creazione di siti Web con Django][]
- [Creazione di siti Web con Flask][]


## Creazione di un sito Web sul portale

In questa esercitazione si presume che sia stata eseguita una sottoscrizione di Azure e sia stato effettuato l'accesso al portale di gestione Azure.

Se non si dispone di un sito Web esistente, è possibile crearne uno dal portale di gestione di Azure.  Fare clic sul pulsante NUOVO nell'angolo inferiore sinistro. Verrà visualizzata una finestra. Fare clic su CALCOLO, SITO WEB, quindi su CREAZIONE RAPIDA.

![](./media/web-sites-python-configure/configure-python-create-website.png)


## Pubblicazione Git

Usare la scheda AVVIO RAPIDO o DASHBOARD per il sito Web appena creato per configurare la pubblicazione Git.  In questa esercitazione viene USATO Git per creare, gestire e pubblicare il sito Web Python su Siti Web di Azure.

![](./media/web-sites-python-configure/configure-python-git.png)

Dopo aver configurato la pubblicazione Git, verrà un repository Git verrà creato e associato al sito Web.  L'URL del repository verrà visualizzato e potrà pertanto essere usato per effettuare il push dei dati dall'ambiente di sviluppo locale al cloud. Per pubblicare applicazioni tramite Git, assicurarsi che sia stato installato anche il client Git e attenersi alle istruzioni fornite per effettuare il push del contenuto del sito Web su Siti Web di Azure.


## Informazioni generali sull'applicazione

Nelle sezioni successive vengono creati i seguenti file.  Devono essere posizionati nella radice del repository Git.

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


## Gestore WSGI

WSGI è uno standard Python descritto da [PEP 3333](http://www.python.org/dev/peps/pep-3333/) che definisce un'interfaccia tra il server Web e Python e che consente di scrivere varie applicazioni e framework Web tramite Python.  Oggi i più comuni framework Web Python usano l'interfaccia WSGI.  Siti Web di Azure offre supporto per qualsiasi framework. Inoltre, gli utenti esperti possono anche creare il proprio framework a condizione che il gestore personalizzato rispetti le linee guida delle specifiche WSGI.

Di seguito è riportato un esempio di un  `app.py` che definisce un gestore personalizzato:

    def wsgi_app(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type', 'text/plain')]
        start_response(status, response_headers)
        response_body = 'Hello World'
        yield response_body.encode()

    if __name__ == '__main__':
        from wsgiref.simple_server import make_server

        httpd = make_server('localhost', 5555, wsgi_app)
        httpd.serve_forever()

È possibile eseguire l'applicazione localmente con  `python app.py`, quindi passare a `http://localhost:5555` nel Web browser.


## Ambiente virtuale

Sebbene l'app dell'esempio precedente non necessiti di pacchetti esterni, è probabile che l'applicazione creata ne richieda alcuni.

Per gestire le dipendenze di pacchetti esterni, la distribuzione Git di Azure supporta la creazione di ambienti virtuali.

Quando Azure rileva un file requirements.txt nella radice del repository, crea automaticamente un ambiente virtuale denominato  `env`.  Questo si verifica solo durante la prima distribuzione o durante una qualsiasi distribuzione dopo che è cambiato il runtime di Python selezionato.

È opportuno creare un ambiente virtuale locale per lo sviluppo, ma evitare di includerlo nel repository Git.


## Gestione dei pacchetti

I pacchetti elencati in requirements.txt verranno installati automaticamente nell'ambiente virtuale tramite pip.  Ciò avviene in ogni distribuzione, tuttavia pip non eseguirà l'installazione se un pacchetto è già installato.

Esempio `requirements.txt`:

    azure==0.8.4


## Versione di Python

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../includes/web-sites-python-customizing-runtime.md)]

Esempio `runtime.txt`:

    python-2.7


## Web.config

È necessario creare un file web.config per specificare il modo in cui il server deve gestire le richieste.

Si noti che se nel repository si dispone di un file web.x.y.config, dove x.y corrisponde al runtime di Python selezionato, Azure copierà automaticamente il file appropriato come web.config.

I seguenti esempi di file web.config si basano su uno script proxy dell'ambiente virtuale, descritto nella sezione successiva.  Funzionano con il gestore WSGI usato nell' `app.py` di esempio precedente.

 `web.config` di esempio per Python 2.7:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\activate_this.py" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_virtualenv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python273_via_FastCGI" />
          <remove name="Python340_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python27\python.exe|D:\Python27\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
              </conditions>
              <action type="Rewrite"
                      url="handler.fcgi/{R:1}"
                      appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


 `web.config` di esempio per Python 3.4:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\python.exe" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_venv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python273_via_FastCGI" />
          <remove name="Python340_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python34\python.exe|D:\Python34\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
              </conditions>
              <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


I file statici verranno gestiti direttamente dal server Web, senza passare per il codice Python, per migliorare le prestazioni.

Negli esempi precedenti il percorso dei file statici sul disco deve corrispondere al percorso nell'URL.  Ciò significa che una richiesta per  `http://pythonapp.azurewebsites.net/static/site.css` servirà il file sul disco nel percorso  `\static\site.css`.

È possibile configurare la regola  `Static Files` per servire i file da un percorso su disco diverso dal percorso nell'URL.  Nella seguente definizione della regola una richiesta per  `http://pythonapp.azurewebsites.net/static/site.css` servirà il file sul disco nel percorso  `\FlaskWebProject\static\site.css`, anziché  `\static\site.css`.

    <rule name="Static Files" stopProcessing="true">
      <match url="^/static/.*" ignoreCase="true" />
      <action type="Rewrite" url="^/FlaskWebProject/static/.*" appendQueryString="true" />
    </rule>

`WSGI_ALT_VIRTUALENV_HANDLER` consente di specificare il gestore WSGI.  Negli esempi precedenti corrisponde a  `app.wsgi_app` perché il gestore è una funzione denominata  `wsgi_app` in  `app.py` nella cartella radice.

`PYTHONPATH` può essere personalizzato ma, se si installano tutte le dipendenze nell'ambiente virtuale specificandole in requirements.txt, non è necessario modificarlo.


## Proxy dell'ambiente virtuale

Il seguente script viene usato per recuperare il gestore WSGI, attivare l'ambiente virtuale e registrare gli errori. È progettato per essere generico e per essere usato senza doverlo modificare.

Contenuto di  `ptvs_virtualenv_proxy.py`:

     # ############################################################################
     #
     # Copyright (c) Microsoft Corporation. 
     #
     # This source code is subject to terms and conditions of the Apache License, Version 2.0. A 
     # copy of the license can be found in the License.html file at the root of this distribution. If 
     # you cannot locate the Apache License, Version 2.0, please send an email to 
     # vspython@microsoft.com. By using this source code in any fashion, you are agreeing to be bound 
     # by the terms of the Apache License, Version 2.0.
     #
     # You must not remove this notice, or any other, from this software.
     #
     # ###########################################################################

    import datetime
    import os
    import sys

    if sys.version_info[0] == 3:
        def to_str(value):
            return value.decode(sys.getfilesystemencoding())

        def execfile(path, global_dict):
            """Execute a file"""
            with open(path, 'r') as f:
                code = f.read()
            code = code.replace('\r\n', '\n') + '\n'
            exec(code, global_dict)
    else:
        def to_str(value):
            return value.encode(sys.getfilesystemencoding())

    def log(txt):
        """Logs fatal errors to a log file if WSGI_LOG env var is defined"""
        log_file = os.environ.get('WSGI_LOG')
        if log_file:
            f = open(log_file, 'a+')
            try:
                f.write('%s: %s' % (datetime.datetime.now(), txt))
            finally:
                f.close()

    ptvsd_secret = os.getenv('WSGI_PTVSD_SECRET')
    if ptvsd_secret:
        log('Enabling ptvsd ...\n')
        try:
            import ptvsd
            try:
                ptvsd.enable_attach(ptvsd_secret)
                log('ptvsd enabled.\n')
            except: 
                log('ptvsd.enable_attach failed\n')
        except ImportError:
            log('error importing ptvsd.\n');

    def get_wsgi_handler(handler_name):
        if not handler_name:
            raise Exception('WSGI_HANDLER env var must be set')
        
        if not isinstance(handler_name, str):
            handler_name = to_str(handler_name)

        module_name, _, callable_name = handler_name.rpartition('.')
        should_call = callable_name.endswith('()')
        callable_name = callable_name[:-2] if should_call else callable_name
        name_list = [(callable_name, should_call)]
        handler = None

        while module_name:
            try:
                handler = __import__(module_name, fromlist=[name_list[0][0]])
                for name, should_call in name_list:
                    handler = getattr(handler, name)
                    if should_call:
                        handler = handler()
                break
            except ImportError:
                module_name, _, callable_name = module_name.rpartition('.')
                should_call = callable_name.endswith('()')
                callable_name = callable_name[:-2] if should_call else callable_name
                name_list.insert(0, (callable_name, should_call))
                handler = None

        if handler is None:
            raise ValueError('"%s" could not be imported' % handler_name)

        return handler

    activate_this = os.getenv('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS')
    if not activate_this:
        raise Exception('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS is not set')

    def get_virtualenv_handler():
        log('Activating virtualenv with %s\n' % activate_this)
        execfile(activate_this, dict(__file__=activate_this))

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler

    def get_venv_handler():
        log('Activating venv with executable at %s\n' % activate_this)
        import site
        sys.executable = activate_this
        old_sys_path, sys.path = sys.path, []
        
        site.main()
        
        sys.path.insert(0, '')
        for item in old_sys_path:
            if item not in sys.path:
                sys.path.append(item)

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler


## Personalizzare la distribuzione Git

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../includes/web-sites-python-customizing-deployment.md)]


## Risoluzione dei problemi - Distribuzione

[AZURE.INCLUDE [web-sites-python-troubleshooting-deployment](../includes/web-sites-python-troubleshooting-deployment.md)]


## Risoluzione dei problemi - Installazione dei pacchetti

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../includes/web-sites-python-troubleshooting-package-installation.md)]


## Risoluzione dei problemi - Ambiente virtuale

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../includes/web-sites-python-troubleshooting-virtual-environment.md)]



[Creazione di siti Web con Bottle]: web-sites-python-create-deploy-bottle-app.md
[Creazione di siti Web con Django]: web-sites-python-create-deploy-django-app.md
[Creazione di siti Web con Flask]: web-sites-python-create-deploy-flask-app.md


<!--HONumber=52-->