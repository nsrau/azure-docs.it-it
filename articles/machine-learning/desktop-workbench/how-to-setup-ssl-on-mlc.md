---
title: Abilitare SSL in un cluster di calcolo di Azure Machine Learning | Microsoft Docs
description: Istruzioni per configurare SSL per chiamate del punteggio in un cluster di calcolo di Azure Machine Learning
services: machine-learning
author: SerinaKaye
ms.author: serinak
manager: hjerez
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/24/2018
ms.openlocfilehash: 14f8dd29b7d4185d01529631333de045ad23cdd0
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831458"
---
# <a name="enable-ssl-on-an-azure-machine-learning-compute-mlc-cluster"></a>Abilitare SSL in un cluster di calcolo di Azure Machine Learning 

Queste istruzioni consentono di configurare SSL per chiamate del punteggio in un cluster di calcolo di Azure Machine Learning. 

## <a name="prerequisites"></a>prerequisiti 

1. Scegliere un CNAME (nome DNS) da usare quando si effettuano chiamate del punteggio in tempo reale.

2. Creare un certificato *senza password* con il CNAME scelto.

3. Se il certificato non è ancora in formato PEM, convertirlo nel formato PEM avvalendosi di strumenti come *openssl*.

Dopo aver completato i prerequisiti, saranno disponibili due file:

* Un file per il certificato, ad esempio `cert.pem`. Assicurarsi che il file contenga la catena di certificati completa.
* Un file per la chiave, ad esempio `key.pem`



## <a name="set-up-an-ssl-certificate-on-a-new-acs-cluster"></a>Configurare un certificato SSL in un nuovo cluster del servizio contenitore di Azure

Usare l'interfaccia della riga di comando di Azure Machine Learning per eseguire il comando seguente con l'istruzione switch `-c` per creare un cluster del servizio contenitore di Azure con un certificato SSL associato:

```
az ml env create -c -g <resource group name> -n <cluster name> --cert-cname <CNAME> --cert-pem <path to cert.pem file> --key-pem <path to key.pem file>
```


## <a name="set-up-an-ssl-certificate-on-an-existing-acs-cluster"></a>Configurare un certificato SSL in un cluster del servizio contenitore di Azure esistente

Se la destinazione è un cluster creato senza SSL, è possibile aggiungere un certificato usando i cmdlet di Azure PowerShell.

È necessario fornire la chiave e il certificato in formato nel formato PEM non elaborato. Questi valori possono essere letti in variabili PowerShell:

```
$keyValueInPemFormat = [IO.File]::ReadAllText('<path to key.pem file>')
$certValueInPemFormat = [IO.File]::ReadAllText('<path to cert.pem file>')
```
Aggiungere il certificato al cluster: 

```
Set-AzureRmMlOpCluster -ResourceGroupName my-rg -Name my-cluster -SslStatus Enabled -SslCertificate $certValueInPemFormat -SslKey $keyValueInPemFormat -SslCName foo.mycompany.com
```

## <a name="map-the-cname-and-the-ip-address"></a>Eseguire il mapping del CNAME e dell'indirizzo IP

Creare un mapping tra il CNAME scelto nella sezione dei prerequisiti e l'indirizzo IP del front-end in tempo reale. Per trovare l'indirizzo IP del front-end, eseguire il comando seguente. Nell'output viene visualizzato un campo denominato "publicIpAddress" contenente l'indirizzo IP del front-end del cluster in tempo reale. Fare riferimento alle istruzioni del provider DNS per configurare un record dal nome di dominio completo usato in CNAME all'indirizzo IP pubblico.



## <a name="auto-detection-of-a-certificate"></a>Rilevamento automatico di un certificato 

Quando si crea un servizio Web in tempo reale tramite il comando "`az ml service create realtime`", Azure Machine Learning rileva automaticamente il certificato SSL configurato nel cluster e imposta automaticamente l'URL del punteggio con il certificato rilevato. 

Per visualizzare lo stato del certificato, eseguire il comando seguente. Osservare il prefisso "https" dell'URI del punteggio e il CNAME nel nome dell'host. 

``` 
az ml service show realtime -n <service name>
{
                "id" : "<your service id>",
                "name" : "<your service name >",
                "state" : "Succeeded",
                "createdAt" : "<datetime>”,
                "updatedAt" : "< datetime>",
                "scoringUri" : "https://<your CNAME>/api/v1/service/<service name>/score"
}
```
