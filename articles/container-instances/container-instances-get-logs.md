---
title: Recuperare log ed eventi dei contenitori con Istanze di contenitore di Azure
description: Informazioni su come eseguire il debug con i log e gli eventi dei contenitori con Istanze di contenitore di Azure
services: container-instances
author: jluk
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 05/30/18
ms.author: juluk
ms.custom: mvc
ms.openlocfilehash: 4c8197e570c429893084fc1c2f8e4b36fd43a721
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39425589"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Recuperare log ed eventi dei contenitori in Istanze di contenitore di Azure

Quando un contenitore non funziona correttamente, iniziare a visualizzarne i log con [az container logs][az-container-logs] e a trasmetterne l'output standard e gli errori standard con [az container attach][az-container-attach].

## <a name="view-logs"></a>Visualizzare i log

Per visualizzare i log generati dal codice dell'applicazione all'interno di un contenitore, è possibile usare il comando [az container logs][az-container-logs].

Il seguente è l'output del log del contenitore basato su attività di esempio in [Eseguire un'attività in contenitori in Istanze di contenitore di Azure](container-instances-restart-policy.md), dopo avergli fornito un URL non valido da elaborare:

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
Traceback (most recent call last):
  File "wordcount.py", line 11, in <module>
    urllib.request.urlretrieve (sys.argv[1], "foo.txt")
  File "/usr/local/lib/python3.6/urllib/request.py", line 248, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/local/lib/python3.6/urllib/request.py", line 223, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/local/lib/python3.6/urllib/request.py", line 532, in open
    response = meth(req, response)
  File "/usr/local/lib/python3.6/urllib/request.py", line 642, in http_response
    'http', request, response, code, msg, hdrs)
  File "/usr/local/lib/python3.6/urllib/request.py", line 570, in error
    return self._call_chain(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 504, in _call_chain
    result = func(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 650, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found
```

## <a name="attach-output-streams"></a>Associare i flussi di output

Il comando [az container attach][az-container-attach] fornisce informazioni diagnostiche durante l'avvio del contenitore. Dopo l'avvio, il contenitore trasmette STDOUT e STDERR alla console locale.

Ecco ad esempio l'output del contenitore basato su attività in [Eseguire un'attività in contenitori in Istanze di contenitore di Azure](container-instances-restart-policy.md), dopo avere specificato un URL valido di un file di testo di grandi dimensioni da elaborare:

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2018-03-09 23:21:33+00:00) pulling image "microsoft/aci-wordcount:latest"
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Successfully pulled image "microsoft/aci-wordcount:latest"
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Created container with id e495ad3e411f0570e1fd37c1e73b0e0962f185aa8a7c982ebd410ad63d238618
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Started container with id e495ad3e411f0570e1fd37c1e73b0e0962f185aa8a7c982ebd410ad63d238618

Start streaming logs:
[('the', 22979),
 ('I', 20003),
 ('and', 18373),
 ('to', 15651),
 ('of', 15558),
 ('a', 12500),
 ('you', 11818),
 ('my', 10651),
 ('in', 9707),
 ('is', 8195)]
```

## <a name="get-diagnostic-events"></a>Recuperare gli eventi di diagnostica

Se il contenitore non viene distribuito correttamente, è necessario esaminare le informazioni di diagnostica fornite dal provider di risorse Istanze di contenitore di Azure. Per visualizzare gli eventi per il contenitore, eseguire il comando [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

L'output include le proprietà principali del contenitore e gli eventi di distribuzione (visualizzati qui troncati):

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "microsoft/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:49+00:00",
            "lastTimestamp": "2017-12-21T22:50:49+00:00",
            "message": "pulling image \"microsoft/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Successfully pulled image \"microsoft/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Created container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Started container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```
## <a name="next-steps"></a>Passaggi successivi
Scoprire come [risolvere i problemi comuni dei contenitori e delle distribuzioni](container-instances-troubleshooting.md) per Istanze di contenitore di Azure.

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-logs]: /cli/azure/container#az-container-logs