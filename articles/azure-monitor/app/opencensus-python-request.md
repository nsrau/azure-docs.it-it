---
title: Rilevamento delle richieste in ingresso in applicazione Azure Insights con OpenCensus Python | Microsoft Docs
description: Monitorare le chiamate di richiesta per le app Python tramite OpenCensus Python.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: f6b06e7bb2bb8637ca28b2fa4185754f8686798e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587927"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Tenere traccia delle richieste in ingresso con OpenCensus Python

I dati delle richieste in ingresso vengono raccolti usando OpenCensus Python e le varie integrazioni. Tieni traccia dei dati delle richieste in ingresso inviati alle tue applicazioni Web basate sui framework Web più diffusi `django`, `flask` e `pyramid`. I dati vengono quindi inviati a Application Insights sotto monitoraggio di Azure come `requests` la telemetria.

Per prima cosa, instrumentare l'applicazione Python con la versione più recente di [OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md).

## <a name="tracking-django-applications"></a>Rilevamento di applicazioni Django

1. Scaricare e installare `opencensus-ext-django` da [PyPI](https://pypi.org/project/opencensus-ext-django/) e instrumentare l'applicazione con il middleware di `django`. Le richieste in ingresso inviate all'applicazione `django` verranno registrate.

2. Includere `opencensus.ext.django.middleware.OpencensusMiddleware` nel file di `settings.py` in `MIDDLEWARE`.

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Verificare che AzureExporter sia configurato correttamente nel `settings.py` in `OPENCENSUS`.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                service_name='foobar',
            )''',
        }
    }
    ```

4. È anche possibile aggiungere URL a `settings.py` in `BLACKLIST_PATHS` per le richieste di cui non si vuole tenere traccia.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                service_name='foobar',
            )''',
            'BLACKLIST_PATHS': 'https://example.com',  <--- This site will not be traced if a request is sent from it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Rilevamento di applicazioni Flask

1. Scaricare e installare `opencensus-ext-flask` da [PyPI](https://pypi.org/project/opencensus-ext-flask/) e instrumentare l'applicazione con il middleware di `flask`. Le richieste in ingresso inviate all'applicazione `flask` verranno registrate.

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

2. È possibile configurare il middleware `flask` direttamente nel codice. Per le richieste provenienti da URL che non si desidera rilevare, aggiungerli a `BLACKLIST_PATHS`.

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                service_name='foobar',
            )''',
            'BLACKLIST_PATHS': 'https://example.com',  <--- This site will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>Rilevamento di applicazioni piramidali

1. Scaricare e installare `opencensus-ext-django` da [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) e instrumentare l'applicazione con l'interpolazione `pyramid`. Le richieste in ingresso inviate all'applicazione `pyramid` verranno registrate.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. È possibile configurare il `pyramid` Tween direttamente nel codice. Per le richieste provenienti da URL che non si desidera rilevare, aggiungerli a `BLACKLIST_PATHS`.

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    service_name='foobar',
                )''',
                'BLACKLIST_PATHS': 'https://example.com',  <--- This site will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Mappa delle applicazioni](../../azure-monitor/app/app-map.md)
* [Disponibilità](../../azure-monitor/app/monitor-web-app-availability.md)
* [Ricerca](../../azure-monitor/app/diagnostic-search.md)
* [Query log (analisi)](../../azure-monitor/log-query/log-query-overview.md)
* [Diagnostica delle transazioni](../../azure-monitor/app/transaction-diagnostics.md)
