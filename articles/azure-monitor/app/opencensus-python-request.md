---
title: Rilevamento delle richieste in ingresso in applicazione Azure Insights con OpenCensus Python | Microsoft Docs
description: Monitorare le chiamate di richiesta per le app Python tramite OpenCensus Python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: c94bc949f13ee19a9d2150c9d3c1b6a2bdb959b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87850067"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Tenere traccia delle richieste in ingresso con OpenCensus Python

I dati delle richieste in ingresso vengono raccolti usando OpenCensus Python e le varie integrazioni. Tieni traccia dei dati delle richieste in ingresso inviati alle tue applicazioni Web basate sui framework Web più diffusi `django` , `flask` e `pyramid` . I dati vengono quindi inviati a Application Insights sotto monitoraggio di Azure come dati di `requests` telemetria.

Per prima cosa, instrumentare l'applicazione Python con la versione più recente di [OpenCensus Python SDK](./opencensus-python.md).

## <a name="tracking-django-applications"></a>Rilevamento di applicazioni Django

1. Scaricare e installare `opencensus-ext-django` da [PyPI](https://pypi.org/project/opencensus-ext-django/) e instrumentare l'applicazione con il `django` middleware. Le richieste in ingresso inviate all' `django` applicazione verranno registrate.

2. Includere `opencensus.ext.django.middleware.OpencensusMiddleware` nel `settings.py` file in `MIDDLEWARE` .

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Verificare che AzureExporter sia configurato correttamente nel `settings.py` sotto `OPENCENSUS` . Per le richieste provenienti da URL che non si desidera rilevare, aggiungerli a `BLACKLIST_PATHS` .

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Rilevamento di applicazioni Flask

1. Scaricare e installare `opencensus-ext-flask` da [PyPI](https://pypi.org/project/opencensus-ext-flask/) e instrumentare l'applicazione con il `flask` middleware. Le richieste in ingresso inviate all' `flask` applicazione verranno registrate.

    ```python
    
    from flask import Flask
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.ext.flask.flask_middleware import FlaskMiddleware
    from opencensus.trace.samplers import ProbabilitySampler
    
    app = Flask(__name__)
    middleware = FlaskMiddleware(
        app,
        exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"),
        sampler=ProbabilitySampler(rate=1.0),
    )
    
    @app.route('/')
    def hello():
        return 'Hello World!'
    
    if __name__ == '__main__':
        app.run(host='localhost', port=8080, threaded=True)
    
    ```

2. È anche possibile configurare l' `flask` applicazione tramite `app.config` . Per le richieste provenienti da URL che non si desidera rilevare, aggiungerli a `BLACKLIST_PATHS` .

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>Rilevamento di applicazioni piramidali

1. Scaricare e installare `opencensus-ext-django` da [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) e instrumentare l'applicazione con l' `pyramid` interpolazione. Le richieste in ingresso inviate all' `pyramid` applicazione verranno registrate.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. È possibile configurare l' `pyramid` interpolazione direttamente nel codice. Per le richieste provenienti da URL che non si desidera rilevare, aggiungerli a `BLACKLIST_PATHS` .

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Mappa delle applicazioni](./app-map.md)
* [Disponibilità](./monitor-web-app-availability.md)
* [Ricerca](./diagnostic-search.md)
* [Query log (analisi)](../log-query/log-query-overview.md)
* [Diagnostica delle transazioni](./transaction-diagnostics.md)

