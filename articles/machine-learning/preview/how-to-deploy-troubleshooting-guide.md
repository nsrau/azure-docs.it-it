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
ms.date: 10/09/2017
ms.openlocfilehash: b9287c7151c96aaccbcda81c111cfe36ead5ab38
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2017
---
# <a name="troubleshooting-service-deployment-and-environment-setup"></a>Risoluzione dei problemi di distribuzione dei servizi e di configurazione dell'ambiente
Le informazioni seguenti consentono di determinare la causa degli errori quando si configura l'ambiente di gestione modelli.

## <a name="model-management-environment"></a>Ambiente di gestione modelli
### <a name="owner-permission-required"></a>Autorizzazione proprietario obbligatoria
È necessario avere l'autorizzazione proprietario nella sottoscrizione di Azure per registrare il calcolo di Machine Learning.

L'autorizzazione proprietario è necessaria anche per configurare un cluster per la distribuzione dei servizi Web.

### <a name="resource-availability"></a>Disponibilità delle risorse
È necessario che nella sottoscrizione siano disponibili risorse sufficienti per poter effettuare il provisioning delle risorse di ambiente.

### <a name="subscription-caps"></a>Limiti massimi della sottoscrizione
La sottoscrizione può avere un limite massimo di fatturazione che può impedire di effettuare il provisioning delle risorse di ambiente. Rimuovere tale limite per abilitare il provisioning.

### <a name="enable-debug-and-verbose-options"></a>Abilitare le opzioni debug e verbose
Usare i flag `--debug` e `--verbose` nel comando setup per visualizzare le informazioni di debug e traccia mentre viene effettuato il provisioning dell'ambiente.

```
az ml env setup -l <loation> -n <name> -c --debug --verbose 
```

## <a name="service-deployment"></a>Distribuzione di servizi
Le informazioni seguenti aiutano a determinare la causa degli errori che possono verificarsi durante la distribuzione o quando si chiama il servizio Web.

### <a name="service-logs"></a>Log del servizio
L'opzione `logs` dell'interfaccia della riga di comando del servizio consente di visualizzare i dati di log generati da Docker e Kubernetes.

```
az ml service logs realtime -i <web service id>
```

Per altre impostazioni di log, usare l'opzione `--help` (o `-h`).

```
az ml service logs realtime -h
```

### <a name="debug-and-verbose-options"></a>Opzioni Debug e Verbose
Usare il flag `--debug` per visualizzare i log di debug mentre viene distribuito il servizio.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --debug
```

Usare il flag `--verbose` per visualizzare dettagli aggiuntivi mentre viene distribuito il servizio.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --verbose
```

### <a name="enable-request-logging-in-app-insights"></a>Abilitare la registrazione delle richieste in App Insights
Impostare il flag `-l` su true quando si crea un servizio Web per abilitare la registrazione a livello di richiesta. I log delle richieste vengono scritti nell'istanza di App Insights per l'ambiente in uso in Azure. Per eseguire la ricerca di questa istanza, usare il nome ambiente specificato durante l'uso del comando `az ml env setup`.

- Impostare `-l` su true durante la creazione del servizio.
- Aprire App Insights nel portale di Azure. Usare il nome ambiente specificato in precedenza per trovare l'istanza di App Insights.
- In App Insights fare clic sul pulsante di ricerca nel menu superiore per visualizzare i risultati.
- In alternativa, passare a `Analytics` > `Exceptions` > `exceptions take | 10`.


### <a name="add-error-handling-in-scoring-script"></a>Aggiungere la gestione degli errori nello script di assegnazione dei punteggi
Usare la gestione delle eccezioni nella funzione **run** dello script `scoring.py` per restituire il messaggio di errore come parte dell'output del servizio Web.

Esempio relativo a Python:
```
    try:
        <code to load model and score>
   except Exception as e:
        return(str(e))
```

## <a name="other-common-problems"></a>Altri problemi comuni
- Se il comando `env setup` ha esito negativo, assicurarsi che nella sottoscrizione sia disponibile un numero sufficiente di core.
- Non usare il carattere di sottolineatura (_) nel nome del servizio Web (come in *my_webservice*).
- Ripetere l'operazione se viene visualizzato un errore **502 - Gateway non valido** quando si chiama il servizio Web. Questo errore si verifica, in genere, se il contenitore non è ancora stato distribuito nel cluster.
- Se durante la creazione di un servizio viene visualizzato l'errore **CrashLoopBackOff**, controllare i log. Questo errore si verifica, in genere, in caso di dipendenze mancanti nella funzione **init**.
