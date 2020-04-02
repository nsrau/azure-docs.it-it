---
title: Funzionalità di Azure Cloud Shell | Microsoft Docs
description: Panoramica delle funzionalità in Azure Cloud ShellOverview of features in Azure Cloud Shell
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: damaerte
ms.openlocfilehash: 78275211b77cb34cf9aa92e44778e5d4d81fffed
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521020"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Funzionalità e strumenti per Azure Cloud Shell

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Azure Cloud Shell viene eseguito su `Ubuntu 16.04 LTS`.

## <a name="features"></a>Funzionalità

### <a name="secure-automatic-authentication"></a>Autenticazione automatica sicura

Cloud Shell autentica in modo sicuro e automatico l'accesso agli account per l'interfaccia della riga di comando di Azure e Azure PowerShell.

### <a name="home-persistence-across-sessions"></a>Persistenza $Home tra le sessioni

Per rendere persistenti i file fra le sessioni, Cloud Shell illustra come associare una condivisione file di Azure al primo avvio.
Al termine, Cloud Shell assocerà automaticamente la risorsa di archiviazione (montata come `$HOME\clouddrive`) per tutte le sessioni future.
Inoltre, la directory `$HOME` viene resa persistente come un .img nella condivisione file di Azure.
I file all'esterno di `$HOME` e lo stato della macchina non sono persistenti tra le sessioni. Usare le procedure consigliate per l'archiviazione dei segreti, ad esempio le chiavi SSH. Servizi, ad esempio [Azure Key Vault dispongono di esercitazioni per l'installazione](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites).

[Altre informazioni sulla persistenza dei file all’interno di Cloud Shell.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Unità Azure (Azure:)

PowerShell in Cloud Shell fornisce`Azure:`l'unità di Azure ( ). È possibile passare all'unità di Azure `cd Azure:` con `cd  ~`e tornare alla home directory con .
L'unità Azure consente una facile individuazione e la navigazione tra le risorse di Azure, ad es. calcolo, rete, archiviazione ecc. analogamente alla navigazione di file system.
È possibile continuare a utilizzare i consueti [cmdlet di Azure PowerShell](https://docs.microsoft.com/powershell/azure) per gestire queste risorse indipendentemente dall'unità in cui ci si trova.
Tutte le modifiche apportate alle risorse di Azure, sia se eseguite direttamente nel portale di Azure che tramite i cmdlet di Azure PowerShell, vengono applicate anche all'unità Azure.  È possibile eseguire `dir -Force` per aggiornare le risorse.

![](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>Gestire Exchange Online

PowerShell in Cloud Shell contiene una build privata del modulo Exchange Online.  Eseguire `Connect-EXOPSSession` per ottenere i cmdlet di Exchange.

![](media/features-powershell/exchangeonline.png)

 Eseguire `Get-Command -Module tmp_*`
> [!NOTE]
> Il nome del `tmp_`modulo deve iniziare con , se sono stati installati moduli con lo stesso prefisso, verranno visualizzati anche i relativi cmdlet. 

![](media/features-powershell/exchangeonlinecmdlets.png)

### <a name="deep-integration-with-open-source-tooling"></a>Stretta integrazione con strumenti open source

Cloud Shell include l'autenticazione preconfigurata per strumenti open source, ad esempio Terraform, Ansible e Chef InSpec. È possibile provarlo nelle procedure dettagliate di esempio.

## <a name="tools"></a>Strumenti

|Category   |Nome   |
|---|---|
|Strumenti di Linux            |bash<br> zsh<br> sh<br> tmux<br> dig<br>               |
|Strumenti di Azure            |[Interfaccia della riga di comando di Azure](https://github.com/Azure/azure-cli) e [interfaccia della riga di comando classica di Azure](https://github.com/Azure/azure-xplat-cli)<br> [Copia Azzurra](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#writing-your-first-azcopy-command)<br> [Interfaccia della riga di comando di Funzioni di Azure](https://github.com/Azure/azure-functions-core-tools)<br> [Interfaccia della riga di comando di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|Editor di testo           |codice (editor di Cloud Shell)<br> vim<br> nano<br> emacs    |
|Controllo del codice sorgente         |git                    |
|Strumenti di compilazione            |make<br> maven<br> npm<br> pip         |
|Contenitori             |[Computer Docker](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Timone](https://github.com/kubernetes/helm)<br> [Interfaccia della riga di comando DC/OS](https://github.com/dcos/dcos-cli)         |
|Database              |Client MySQL<br> Client PostgreSql<br> [Utilità sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Altri                  |Client iPython<br> [Interfaccia della riga di comando Cloud Foundry](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Chef InSpec](https://www.chef.io/inspec/)<br> [Bullone Marionetta](https://puppet.com/docs/bolt/latest/bolt.html)<br> [HashiCorp Packer](https://www.packer.io/)|

## <a name="language-support"></a>Lingue supportate

|Linguaggio   |Versione   |
|---|---|
|.NET Core  |2.2.402       |
|Go         |1.9        |
|Java       |1.8        |
|Node.js    |8.16.0      |
|PowerShell |[7.0.0](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 e 3.5 (impostazione predefinita)|

## <a name="next-steps"></a>Passaggi successivi
[Guida introduttiva di Bash in Cloud Shell](quickstart.md) <br>
[Guida introduttiva di PowerShell in Cloud Shell](quickstart-powershell.md) <br>
[Informazioni sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/) <br>
[Informazioni su Azure PowerShell](https://docs.microsoft.com/powershell/azure/) <br>
