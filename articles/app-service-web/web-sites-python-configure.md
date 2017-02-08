---
title: Configurazione di Python con App Web del servizio app di Azure
description: Questa esercitazione descrive le opzioni per la creazione e la configurazione di un&quot;applicazione Python di base conforme all&quot;interfaccia WSGI (Web Server Gateway Interface) in App Web del servizio app di Azure.
services: app-service
documentationcenter: python
tags: python
author: huguesv
manager: wpickett
editor: 
ms.assetid: fd00dc91-9935-4331-b955-4bd71e66d518
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/26/2016
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: c4fe19e608061c4b69f3856313ece87e9870bbf9


---
# <a name="configuring-python-with-azure-app-service-web-apps"></a>Configurazione di Python con App Web del servizio app di Azure
Questa esercitazione descrive le opzioni per la creazione e la configurazione di un'applicazione Python di base conforme all'interfaccia WSGI (Web Server Gateway Interface) in [App Web del servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

Descrive le funzionalità aggiuntive della distribuzione Git, ad esempio l'ambiente virtuale e l'installazione del pacchetto con requirements.txt.

## <a name="bottle-django-or-flask"></a>Bottle, Django o Flask?
Azure Marketplace contiene modelli per i framework Bottle, Django e Flask. Se si sta sviluppando la prima app Web nel servizio app di Azure o non si ha familiarità con Git, è consigliabile seguire una delle esercitazioni seguenti, che includono istruzioni dettagliate per la creazione di un'applicazione funzionante dalla raccolta tramite la distribuzione Git da Windows o Mac:

* [Creazione di app Web con Bottle](web-sites-python-create-deploy-bottle-app.md)
* [Creazione di app Web con Django](web-sites-python-create-deploy-django-app.md)
* [Creazione di app Web con Flask](web-sites-python-create-deploy-flask-app.md)

## <a name="web-app-creation-on-azure-portal"></a>Creazione di un'app Web nel portale di Azure
Per seguire questa esercitazione è necessaria una sottoscrizione di Azure e l'accesso al portale di Azure.

Se non si dispone di un'app Web esistente, è possibile crearne una dal [portale di Azure](https://portal.azure.com).  Fare clic sul pulsante NUOVO nell'angolo superiore sinistro e quindi su **Web e dispositivi mobili** > **App Web**.

## <a name="git-publishing"></a>Pubblicazione Git
Configurare la pubblicazione Git per l'app Web appena creata seguendo le istruzioni disponibili in [Distribuzione del repository Git locale nel servizio app di Azure](app-service-deploy-local-git.md). Questa esercitazione usa Git per creare, gestire e pubblicare l'app Web Python nel servizio app di Azure.

Dopo aver configurato la pubblicazione Git, verrà creato un repository che verrà associato all'app Web. L'URL del repository verrà visualizzato e potrà pertanto essere usato per effettuare il push dei dati dall'ambiente di sviluppo locale al cloud. Per pubblicare applicazioni tramite Git, assicurarsi che sia stato installato anche il client Git e attenersi alle istruzioni fornite per eseguire il push dei contenuti dell'app Web nel servizio app di Azure.

## <a name="application-overview"></a>Informazioni generali sull'applicazione
Nelle sezioni successive vengono creati i seguenti file. Devono essere posizionati nella radice del repository Git.

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


## <a name="wsgi-handler"></a>Gestore WSGI
WSGI è uno standard Python descritto da [PEP 3333](http://www.python.org/dev/peps/pep-3333/) che definisce un'interfaccia tra il server Web e Python che consente di scrivere varie applicazioni e framework Web tramite Python. Oggi i più comuni framework Web Python usano l'interfaccia WSGI. App Web del servizio app di Azure offre assistenza per qualsiasi framework. Inoltre, gli utenti avanzati possono anche creare il proprio framework a patto che il gestore personalizzato si attenga alle linee guida delle specifiche WSGI.

Di seguito è riportato un esempio di un `app.py` che definisce un gestore personalizzato:

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

È possibile eseguire l'applicazione localmente con `python app.py`, quindi passare a `http://localhost:5555` nel Web browser.

## <a name="virtual-environment"></a>Ambiente virtuale
Sebbene l'app dell'esempio precedente non necessiti di pacchetti esterni, è probabile che l'applicazione creata ne richieda alcuni.

Per gestire le dipendenze di pacchetti esterni, la distribuzione Git di Azure supporta la creazione di ambienti virtuali.

Quando Azure rileva un file requirements.txt nella radice del repository, crea automaticamente un ambiente virtuale denominato `env`. Questo si verifica solo durante la prima distribuzione o durante una qualsiasi distribuzione dopo che è cambiato il runtime di Python selezionato.

È opportuno creare un ambiente virtuale locale per lo sviluppo, ma evitare di includerlo nel repository Git.

## <a name="package-management"></a>Gestione dei pacchetti
I pacchetti elencati in requirements.txt verranno installati automaticamente nell'ambiente virtuale tramite pip. Ciò avviene in ogni distribuzione, tuttavia pip non eseguirà l'installazione se un pacchetto è già installato.

Esempio `requirements.txt`:

    azure==0.8.4


## <a name="python-version"></a>Versione di Python
[!INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

Esempio `runtime.txt`:

    python-2.7


## <a name="webconfig"></a>Web.config
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
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
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
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
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
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
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
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


I file statici verranno gestiti direttamente dal server Web, senza passare per il codice Python, per migliorare le prestazioni.

Negli esempi precedenti il percorso dei file statici sul disco deve corrispondere al percorso nell'URL. Ciò significa che una richiesta per `http://pythonapp.azurewebsites.net/static/site.css` servirà il file sul disco nel percorso `\static\site.css`.

`WSGI_ALT_VIRTUALENV_HANDLER` viene specificato il gestore WSGI. Negli esempi precedenti corrisponde a `app.wsgi_app` perché il gestore è una funzione denominata `wsgi_app` in `app.py` nella cartella radice.

`PYTHONPATH` può essere personalizzato ma, se si installano tutte le dipendenze nell'ambiente virtuale specificandole in requirements.txt, non è necessario modificarlo.

## <a name="virtual-environment-proxy"></a>Proxy dell'ambiente virtuale
Il seguente script viene usato per recuperare il gestore WSGI, attivare l'ambiente virtuale e registrare gli errori. È progettato per essere generico e per essere usato senza doverlo modificare.

Contenuto di `ptvs_virtualenv_proxy.py`:

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
    import traceback

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
            log('error importing ptvsd.\n')

    def get_wsgi_handler(handler_name):
        if not handler_name:
            raise Exception('WSGI_ALT_VIRTUALENV_HANDLER env var must be set')

        if not isinstance(handler_name, str):
            handler_name = to_str(handler_name)

        module_name, _, callable_name = handler_name.rpartition('.')
        should_call = callable_name.endswith('()')
        callable_name = callable_name[:-2] if should_call else callable_name
        name_list = [(callable_name, should_call)]
        handler = None
        last_tb = ''

        while module_name:
            try:
                handler = __import__(module_name, fromlist=[name_list[0][0]])
                last_tb = ''
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
                last_tb = ': ' + traceback.format_exc()

        if handler is None:
            raise ValueError('"%s" could not be imported%s' % (handler_name, last_tb))

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


## <a name="customize-git-deployment"></a>Personalizzare la distribuzione Git
[!INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-deployment.md)]

## <a name="troubleshooting---package-installation"></a>Risoluzione dei problemi - Installazione dei pacchetti
[!INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]

## <a name="troubleshooting---virtual-environment"></a>Risoluzione dei problemi - Ambiente virtuale
[!INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni, vedere il [Centro per sviluppatori di Python](/develop/python/).

> [!NOTE]
> Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](https://azure.microsoft.com/try/app-service/), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
> 
> 

## <a name="whats-changed"></a>Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)




<!--HONumber=Jan17_HO3-->


