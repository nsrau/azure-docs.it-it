---
title: Rilevamento delle richieste in ingresso in Azure Application Insights con OpenCensus Python Documenti Microsoft
description: Monitorare le chiamate di richiesta per le applicazioni Python tramite OpenCensus Python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: 0396bd8d150c6145a39f36e7be9e6e2dcacef2c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669948"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Tieni traccia delle richieste in arrivo con OpenCensus Python

I dati delle richieste in ingresso vengono raccolti utilizzando OpenCensus Python e le sue varie integrazioni. Tenere traccia dei dati delle richieste in arrivo inviati `django`alle `flask` `pyramid`applicazioni Web basate sui framework Web più diffusi e . I dati vengono quindi inviati ad `requests` Application Insights in Monitoraggio di Azure come telemetria.

In primo luogo, instrumentare l'applicazione Python con l'ultimo [OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md).

## <a name="tracking-django-applications"></a>Monitoraggio delle applicazioni Django

1. Scaricare e `opencensus-ext-django` installare da [PyPI](https://pypi.org/project/opencensus-ext-django/) e `django` instrumentare l'applicazione con il middleware. Le richieste in `django` arrivo inviate all'applicazione verranno registrate.

2. Includere `opencensus.ext.django.middleware.OpencensusMiddleware` nel `settings.py` file `MIDDLEWARE`in .

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Verificare che AzureExporter sia `settings.py` `OPENCENSUS`configurato correttamente in .

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
        }
    }
    ```

4. È inoltre possibile aggiungere `settings.py` `BLACKLIST_PATHS` URL a under per le richieste che non si desidera tenere traccia.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent from it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Applicazioni di monitoraggio Flask

1. Scaricare e `opencensus-ext-flask` installare da [PyPI](https://pypi.org/project/opencensus-ext-flask/) e `flask` instrumentare l'applicazione con il middleware. Le richieste in `flask` arrivo inviate all'applicazione verranno registrate.

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

2. È possibile `flask` configurare il middleware direttamente nel codice. Per le richieste provenienti da URL di cui `BLACKLIST_PATHS`non si desidera tenere traccia, aggiungerle a .

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

## <a name="tracking-pyramid-applications"></a>Applicazioni Tracking Pyramid

1. Scaricare e `opencensus-ext-django` installare da [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) e `pyramid` instrumentare l'applicazione con l'interpolazione. Le richieste in `pyramid` arrivo inviate all'applicazione verranno registrate.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. È possibile `pyramid` configurare l'interpolazione direttamente nel codice. Per le richieste provenienti da URL di cui `BLACKLIST_PATHS`non si desidera tenere traccia, aggiungerle a .

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

* [Mappa dell'applicazione](../../azure-monitor/app/app-map.md)
* [Disponibilità](../../azure-monitor/app/monitor-web-app-availability.md)
* [Ricerca](../../azure-monitor/app/diagnostic-search.md)
* [Query log (Analitica)](../../azure-monitor/log-query/log-query-overview.md)
* [Diagnostica delle transazioni](../../azure-monitor/app/transaction-diagnostics.md)
