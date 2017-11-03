---
title: Scaricare strumenti di Azure Stack da GitHub | Documenti Microsoft
description: Informazioni su come scaricare gli strumenti necessari per l'utilizzo con lo Stack di Azure.
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: sngun
ms.openlocfilehash: 69ca030d1b7601df424fa9446e1d194a3f6bd50a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="download-azure-stack-tools-from-github"></a>Scaricare strumenti di Azure Stack da GitHub

Strumenti di AzureStack è un repository di GitHub che ospita i moduli di PowerShell che è possibile utilizzare per gestire e distribuire le risorse allo Stack di Azure. È possibile scaricare e usare questi moduli di PowerShell per il Kit di sviluppo dello Stack di Azure o a un client esterno basato su windows, se si prevede di stabilire la connettività VPN. Per ottenere questi strumenti, clonare il repository GitHub o scaricare la cartella Strumenti di AzureStack. 

Per clonare il repository, scaricare [Git](https://git-scm.com/download/win) per Windows, aprire una finestra del prompt dei comandi ed eseguire lo script seguente:

```PowerShell
# Change directory to the root directory 
cd \

# clone the repository
git clone https://github.com/Azure/AzureStack-Tools.git --recursive

# Change to the tools directory
cd AzureStack-Tools
```

Per scaricare la cartella Strumenti, eseguire lo script seguente:

```PowerShell
# Change directory to the root directory 
cd \

# Download the tools archive
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory
cd AzureStack-Tools-master

```

## <a name="functionalities-provided-by-the-modules"></a>Funzionalità fornite dai moduli

Il repository AzureStack strumenti contiene i moduli di PowerShell che supportano le funzionalità seguenti per lo Stack di Azure:  

| Funzionalità | Descrizione | chi può usare questo modulo? |
| --- | --- | --- |
| [Capacità del cloud](azure-stack-validate-templates.md) | Usare questo modulo per ottenere le funzionalità cloud di un cloud. Ad esempio, è possibile ottenere le funzionalità del cloud, ad esempio versione dell'API, le risorse di gestione risorse di Azure, le estensioni VM e così via per Stack di Azure e Azure cloud, usare il modulo. | Gli amministratori di cloud e gli utenti. |
| [Criteri di gestione risorse per lo Stack di Azure](azure-stack-policy-module.md) | Utilizzare questo modulo per configurare una sottoscrizione di Azure o un gruppo di risorse di Azure con la disponibilità di controllo delle versioni e il servizio stesso stack di Azure. | Utenti e amministratori di cloud |
| [Connessione a Azure Stack](azure-stack-connect-azure-stack.md) | Usare questo modulo per connettersi a un'istanza dello Stack di Azure tramite PowerShell e per configurare la connettività VPN tra Azure Stack. | Utenti e amministratori di cloud |
| [Validator del modello](azure-stack-validate-templates.md) | Utilizzare questo modulo per verificare se può essere distribuito esistente o un nuovo modello allo Stack di Azure. | Utenti e amministratori di cloud |


## <a name="next-steps"></a>Passaggi successivi
* [Configurare l'ambiente di PowerShell dell'utente Azure Stack](azure-stack-powershell-configure-user.md)   
* [Connettersi al Kit di sviluppo dello Stack di Azure tramite una VPN](azure-stack-connect-azure-stack.md)  
