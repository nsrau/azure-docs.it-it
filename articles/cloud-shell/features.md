---
title: "Funzionalità di Bash in Azure Cloud Shell (anteprima) | Microsoft Docs"
description: "Panoramica delle funzionalità di Bash in Azure Cloud Shell"
services: Azure
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
ms.date: 10/16/2017
ms.author: juluk
ms.openlocfilehash: 23faadc38fde75d4c83361465fdacc76d42d7523
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2017
---
# <a name="features--tools-for-bash-in-azure-cloud-shell"></a>Funzionalità e strumenti per Bash in Azure Cloud Shell

[!include [features-introblock](../../includes/cloud-shell-features-introblock.md)]

> [!TIP]
> Strumenti e funzionalità è disponibile anche in [PowerShell](features-powershell.md).

Bash in Azure Cloud Shell viene eseguito su `Ubuntu 16.04 LTS`.

## <a name="features"></a>Funzionalità

### <a name="secure-automatic-authentication"></a>Autenticazione automatica sicura

Bash in Cloud Shell autentica in modo sicuro e automatico l'accesso agli account per l'interfaccia della riga di comando di Azure 2.0.

### <a name="ssh-into-azure-linux-virtual-machines"></a>SSH nelle macchine virtuali Linux Azure

Creazione di una VM Linux dall’interfaccia della riga di comando di Azure 2.0 consente di creare una chiave SSH predefinita e di posizionarla nella directory `$Home`. Immissione di chiavi SSH in `$Home` consente di indirizzare le connessioni SSH alle macchine virtuali Linux Azure direttamente da Cloud Shell. Le chiavi vengono tenute in acc_<user>.img nel sistema di condivisione di file, utilizzare le procedure consigliate quando si usa o si condivide l’accesso alla condivisione file o alle chiavi.

### <a name="home-persistence-across-sessions"></a>Persistenza $Home tra le sessioni

Per rendere persistenti i file fra le sessioni, Cloud Shell illustra come associare una condivisione file di Azure al primo avvio.
Al termine, Cloud Shell assocerà automaticamente la risorsa di archiviazione (montata come `$Home\clouddrive`) per tutte le sessioni future.
Inoltre, in Bash all’interno di Cloud Shell la directory `$Home` viene resa persistente come un .img nella condivisione file di Azure.
I file all'esterno di `$Home` e lo stato della macchina non sono persistenti tra le sessioni.

[Altre informazioni sulla persistenza dei file in Bash all’interno di Cloud Shell.](persisting-shell-storage.md)

## <a name="tools"></a>Strumenti

|Categoria   |Nome   |
|---|---|
|Strumenti di Linux            |bash<br> sh<br> tmux<br> dig<br>               |
|Strumenti di Azure            |[Interfaccia della riga di comando di Azure 2.0](https://github.com/Azure/azure-cli) e [1.0](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard) <br> [Interfaccia della riga di comando di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) <br>    |
|Editor di testo           |vim<br> nano<br> emacs       |
|Controllo del codice sorgente         |git                    |
|Strumenti di compilazione            |make<br> maven<br> npm<br> pip         |
|Contenitori             |[Interfaccia della riga di comando Docker](https://github.com/docker/cli)/[Computer Docker](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [Interfaccia della riga di comando DC/OS](https://github.com/dcos/dcos-cli)         |
|Database              |Client MySQL<br> Client PostgreSql<br> [Utilità sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Altre                  |Client iPython<br> [Interfaccia della riga di comando Cloud Foundry](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> |

## <a name="language-support"></a>Supporto per le lingue

|Lingua   |Versione   |
|---|---|
|.NET       |2.0.0       |
|Go         |1.7        |
|Java       |1.8        |
|Node.js    |6.9.4      |
|PowerShell |[6.0 (beta)](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 e 3.5 (impostazione predefinita)|

## <a name="next-steps"></a>Passaggi successivi
[Avvio rapido di Bash in Cloud Shell](quickstart.md) <br>
[Informazioni sull'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/)