---
title: "Funzionalità di Azure Cloud Shell (anteprima) | Documentazione Microsoft"
description: "Panoramica delle funzionalità di Azure Cloud Shell"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 3e8eb5b2fe7d3d072132044e2445eab6fcd82ce1
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="features-and-tools-for-azure-cloud-shell"></a>Funzionalità e strumenti per Azure Cloud Shell
Azure Cloud Shell è un'esperienza shell basata su browser per gestire e sviluppare risorse di Azure.

Cloud Shell offre un'esperienza shell preconfigurata accessibile tramite browser per la gestione delle risorse di Azure senza l'onere di dover installare, controllare le versioni e mantenere un computer manualmente.

Cloud Shell esegue il provisioning delle macchine sulla base delle richieste e di conseguenza lo stato della macchina non viene mantenuto tra le sessioni. Poiché Cloud Shell è pensato per le sessioni interattive, le shell vengono terminate automaticamente dopo 20 minuti di inattività della shell stessa.

## <a name="bash-in-cloud-shell"></a>Bash in Cloud Shell
### <a name="tools"></a>Strumenti
|Categoria   |Nome   |
|---|---|
|Interprete shell di Linux|Bash<br> sh               |
|Strumenti di Azure            |[Interfaccia della riga di comando di Azure 2.0](https://github.com/Azure/azure-cli) e [1.0](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard)     |
|Editor di testo           |vim<br> nano<br> emacs       |
|Controllo del codice sorgente         |git                    |
|Strumenti di compilazione            |make<br> maven<br> npm<br> pip         |
|Contenitori             |[Interfaccia della riga di comando Docker](https://github.com/docker/cli)/[Computer Docker](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Bozza](https://github.com/Azure/draft)<br> [Interfaccia della riga di comando DC/OS](https://github.com/dcos/dcos-cli)         |
|Database              |Client MySQL<br> Client PostgreSql<br> [Utilità sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility)      |
|Altri                  |Client iPython<br> [Interfaccia della riga di comando Cloud Foundry](https://github.com/cloudfoundry/cli)<br> |

### <a name="language-support"></a>Supporto per le lingue
|Lingua   |Versione   |
|---|---|
|.NET       |1.01       |
|Go         |1.7        |
|Java       |1.8        |
|Node.js    |6.9.4      |
|Python     |2.7 e 3.5 (impostazione predefinita)|

## <a name="secure-automatic-authentication"></a>Autenticazione automatica sicura
Cloud Shell autentica in modo sicuro e automatico l'accesso agli account per l'interfaccia della riga di comando di Azure 2.0.

## <a name="azure-files-persistence"></a>Persistenza di File di Azure
Poiché Cloud Shell viene allocato su base richiesta usando un computer temporaneo, i file esterni a $Home e lo stato del computer non sono mantenuti tra le sessioni.
Per rendere persistenti i file fra le sessioni, Cloud Shell illustra come associare una condivisione file di Azure al primo avvio.
Al termine, Cloud Shell assocerà automaticamente lo spazio di archiviazione per tutte le sessioni future.

[Altre informazioni sul collegamento delle condivisioni di file di Azure a Cloud Shell](persisting-shell-storage.md).

## <a name="next-steps"></a>Passaggi successivi
[Avvio rapido di Cloud Shell](quickstart.md) <br>
[Informazioni sull'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/) <br>
