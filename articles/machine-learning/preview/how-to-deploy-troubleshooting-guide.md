---
title: Guida per la risoluzione dei problemi di distribuzione di Azure Machine Learning | Microsoft Docs
description: Guida per la risoluzione dei problemi relativi alla distribuzione e alla creazione di servizi
services: machine-learning
author: raymondl
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/05/2017
ms.openlocfilehash: 066a6a223692055c7855abc63667e345ee8dc2d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="model-management-troubleshooting"></a>Risoluzione dei problemi di gestione dei modelli
Le informazioni seguenti aiutano a determinare la causa degli errori che possono verificarsi durante la distribuzione o quando si chiama il servizio Web.
 
## <a name="1-service-logs"></a>1. Log del servizio
L'opzione `logs` dell'interfaccia della riga di comando del servizio consente di visualizzare i dati di log generati da Docker e Kubernetes.

```
az ml service logs realtime -i <web service id>
```

Per altre impostazioni di log, usare l'opzione `--help` (o `-h`).

```
az ml service logs realtime -h
```

## <a name="2-debug-and-verbose-options"></a>2. Opzioni Debug e Verbose
Usare il flag `--debug` per visualizzare i log di debug mentre viene distribuito il servizio.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --debug
```

Usare il flag `--verbose` per visualizzare dettagli aggiuntivi mentre viene distribuito il servizio.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --verbose
```

## <a name="3-app-insights"></a>3. Informazioni sull'app
Impostare il flag `-l` su true quando si crea un servizio Web per abilitare la registrazione a livello di richiesta. I log delle richieste vengono scritti nell'istanza di App Insights per l'ambiente in uso in Azure. Per eseguire la ricerca di questa istanza, usare il nome ambiente specificato durante l'uso del comando `az ml env setup`.

- Impostare `-l` su true durante la creazione del servizio.
- Aprire App Insights nel portale di Azure. Usare il nome ambiente specificato in precedenza per trovare l'istanza di App Insights.
- In App Insights fare clic sul pulsante di ricerca nel menu superiore per visualizzare i risultati.
- In alternativa, passare a `Analytics` > `Exceptions` > `exceptions take | 10`.


## <a name="4-error-handling-in-script"></a>4. Gestione degli errori nello script
Usare la gestione delle eccezioni nella funzione **run** dello script `scoring.py` per restituire il messaggio di errore come parte dell'output del servizio Web.

Esempio relativo a Python:
```
    try:
        <code to load model and score>
   except Exception as e:
        return(str(e))
```

## <a name="5-other-common-problems"></a>5. Altri problemi comuni
- Se il comando `env setup` ha esito negativo, assicurarsi che nella sottoscrizione sia disponibile un numero sufficiente di core.
- Non usare il carattere di sottolineatura (_) nel nome del servizio Web (come in *my_webservice*).
- Ripetere l'operazione se viene visualizzato un errore **502 - Gateway non valido** quando si chiama il servizio Web. Questo errore si verifica, in genere, se il contenitore non è ancora stato distribuito nel cluster.
- Se durante la creazione di un servizio viene visualizzato l'errore **CrashLoopBackOff**, controllare i log. Questo errore si verifica, in genere, in caso di dipendenze mancanti nella funzione **init**.