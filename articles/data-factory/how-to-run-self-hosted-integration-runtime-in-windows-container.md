---
title: Come eseguire Integration Runtime self-hosted nel contenitore Windows
description: Informazioni su come eseguire Integration Runtime self-hosted nel contenitore di Windows.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: d6f292ff89a70de90e6b86f19f73de26963d997f
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927564"
---
# <a name="how-to-run-self-hosted-integration-runtime-in-windows-container"></a>Come eseguire Integration Runtime self-hosted nel contenitore Windows

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo illustra come eseguire Integration Runtime self-hosted nel contenitore di Windows.
Azure Data Factory offrono il supporto ufficiale per i contenitori di Windows di Integration Runtime indipendenti. È possibile scaricare il codice sorgente di compilazione Docker e combinare il processo di compilazione ed esecuzione nella pipeline di recapito continuo. 

## <a name="prerequisites"></a>Prerequisiti 
- [Requisiti dei contenitori di Windows](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/system-requirements)
- Docker versione 2,3 e successive 
- Self-Hosted Integration Runtime versione 4.11.7512.1 e versioni successive 
## <a name="get-started"></a>Introduzione 
1.  Installare Docker e abilitare il contenitore Windows 
2.  Scaricare il codice sorgente da https://github.com/Azure/Azure-Data-Factory-Integration-Runtime-in-Windows-Container
3.  Scaricare la versione più recente di "cazzi" nella cartella 
4.  Aprire la cartella nella shell: 
```console
cd "yourFolderPath"
```

5.  Compilare l'immagine Docker di Windows: 
```console
docker build . -t "yourDockerImageName" 
```
6.  Eseguire il contenitore docker: 
```console
docker run -d -e NODE_NAME="irNodeName" -e AUTH_KEY="IR_AUTHENTICATION_KEY" -e ENABLE_HA=true HA_PORT=8060 "yourDockerImageName"    
```
> [!NOTE]
> AUTH_KEY è obbligatorio per questo comando. NODE_NAME, ENABLE_HA e HA_PORT sono facoltativi. Se il valore non viene impostato, il comando utilizzerà i valori predefiniti. Il valore predefinito di ENABLE_HA è false e HA_PORT è 8060.

## <a name="container-health-check"></a>Controllo integrità contenitore 
Dopo il periodo di avvio di 120 secondi, il controllo dell'integrità viene eseguito periodicamente ogni 30 secondi. Fornirà lo stato di integrità di IR al motore del contenitore. 

## <a name="limitations"></a>Limitazioni
Attualmente non sono supportate le funzionalità seguenti quando si esegue Integration Runtime self-hosted nel contenitore di Windows:
- Proxy HTTP 
- Comunicazione nodo-nodo crittografata con certificato TLS/SSL 
- Generare e importare il backup 
- Servizio daemon 
- Aggiornamento automatico 

### <a name="next-steps"></a>Passaggi successivi
- Vedere [Concetti relativi al runtime di integrazione in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime).
- Informazioni su come [creare un runtime di integrazione self-hosted nel portale di Azure](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).


