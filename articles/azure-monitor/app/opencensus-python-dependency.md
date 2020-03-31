---
title: Rilevamento delle dipendenze in Azure Application Insights con OpenCensus Python Documenti Microsoft
description: Monitora le chiamate di dipendenza per le tue app Python tramite OpenCensus Python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: e400669fd96518adead74a81fc332767c5f9b23b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669931"
---
# <a name="track-dependencies-with-opencensus-python"></a>Tenere traccia delle dipendenze con OpenCensus Python

Una dipendenza è un componente esterno chiamato dall'applicazione. I dati sulle dipendenze vengono raccolti utilizzando OpenCensus Python e le relative integrazioni. I dati vengono quindi inviati ad `dependencies` Application Insights in Monitoraggio di Azure come telemetria.

In primo luogo, instrumentare l'applicazione Python con l'ultimo [OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md).

## <a name="in-process-dependencies"></a>Dipendenze in-process

OpenCensus Python SDK per Monitoraggio di Azure consente di inviare dati di telemetria delle dipendenze "in-process" (informazioni e logica che si verifica all'interno dell'applicazione). Le dipendenze in-process `type` avranno il campo come `INPROC` nell'analisi.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>Dipendenze con integrazione "richieste"

Tieni traccia delle richieste `requests` in uscita con l'integrazione OpenCensus.

Scaricare e `opencensus-ext-requests` installare da [PyPI](https://pypi.org/project/opencensus-ext-requests/) e aggiungerlo alle integrazioni di traccia. Le richieste inviate utilizzando la libreria [di richieste](https://pypi.org/project/requests/) Python verranno registrate.

```python
import requests
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])  # <-- this line enables the requests integration

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='parent'):
    response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit') # <-- this request will be tracked
```

## <a name="dependencies-with-httplib-integration"></a>Dipendenze con integrazione "httplib"

Tieni traccia delle tue `httplib` richieste in uscita con l'integrazione OpenCensus.

Scaricare e `opencensus-ext-httplib` installare da [PyPI](https://pypi.org/project/opencensus-ext-httplib/) e aggiungerlo alle integrazioni di traccia. Le richieste inviate utilizzando [http.client](https://docs.python.org/3.7/library/http.client.html) per Python3 o [httplib](https://docs.python.org/2/library/httplib.html) per Python2 verranno registrate.

```python
import http.client as httplib
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['httplib'])
conn = httplib.HTTPConnection("www.python.org")

tracer = Tracer(
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(1.0)
)

conn.request("GET", "http://www.python.org", "", {})
response = conn.getresponse()
conn.close()
```

## <a name="dependencies-with-django-integration"></a>Dipendenze con integrazione "django"

Tieni traccia delle richieste Django `django` in uscita con l'integrazione OpenCensus.

Scaricare e `opencensus-ext-django` installare da [PyPI](https://pypi.org/project/opencensus-ext-django/) e `MIDDLEWARE` aggiungere la seguente `settings.py` riga alla sezione nel file Django.

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

È possibile fornire una configurazione aggiuntiva, leggere [le personalizzazioni](https://github.com/census-instrumentation/opencensus-python#customization) per un riferimento completo.

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

## <a name="dependencies-with-mysql-integration"></a>Dipendenze con l'integrazione "mysql"

Tieni traccia delle dipendenze MYSQL con l'integrazione OpenCensus. `mysql` Questa integrazione supporta la libreria [mysql-connector.](https://pypi.org/project/mysql-connector-python/)

Scaricare e `opencensus-ext-mysql` installare da [PyPI](https://pypi.org/project/opencensus-ext-mysql/) e aggiungere le seguenti righe al codice.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>Dipendenze con integrazione "pymysql"

Tieni traccia delle dipendenze di `pymysql` PyMySQL con l'integrazione OpenCensus.

Scaricare e `opencensus-ext-pymysql` installare da [PyPI](https://pypi.org/project/opencensus-ext-pymysql/) e aggiungere le seguenti righe al codice.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>Dipendenze con integrazione "postgresql"

Tieni traccia delle dipendenze PostgreSQL con l'integrazione OpenCensus. `postgresql` Questa integrazione supporta la libreria [psycopg2.](https://pypi.org/project/psycopg2/)

Scaricare e `opencensus-ext-postgresql` installare da [PyPI](https://pypi.org/project/opencensus-ext-postgresql/) e aggiungere le seguenti righe al codice.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>Dipendenze con integrazione "pymongo"

Tenere traccia delle dipendenze di `pymongo` MongoDB con l'integrazione OpenCensus. Questa integrazione supporta la libreria [di piomone.](https://pypi.org/project/pymongo/)

Scaricare e `opencensus-ext-pymongo` installare da [PyPI](https://pypi.org/project/opencensus-ext-pymongo/) e aggiungere le seguenti righe al codice.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>Dipendenze con integrazione "sqlalchemy"

Tenere traccia delle dipendenze utilizzando SQLAlchemy tramite l'integrazione OpenCensus.Track your dependencies using SQLAlchemy using OpenCensus `sqlalchemy` integration. Questa integrazione tiene traccia dell'utilizzo del pacchetto [sqlalchemy,](https://pypi.org/project/SQLAlchemy/) indipendentemente dal database sottostante.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>Passaggi successivi

* [Mappa dell'applicazione](../../azure-monitor/app/app-map.md)
* [Disponibilità](../../azure-monitor/app/monitor-web-app-availability.md)
* [Ricerca](../../azure-monitor/app/diagnostic-search.md)
* [Query log (Analitica)](../../azure-monitor/log-query/log-query-overview.md)
* [Diagnostica delle transazioni](../../azure-monitor/app/transaction-diagnostics.md)
