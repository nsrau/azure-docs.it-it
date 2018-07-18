---
title: Scaricare strumenti di Azure Stack da GitHub | Documenti Microsoft
description: Informazioni su come scaricare gli strumenti necessari per l'utilizzo con lo Stack di Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 28F360AD-789A-488D-965F-FC6E6CCF3329
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: f4c4d907fe76c5e6ee5893b5b4dd1b2d766877a7
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287258"
---
# <a name="download-azure-stack-tools-from-github"></a>Scaricare strumenti di Azure Stack da GitHub

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Strumenti di AzureStack è un repository di GitHub che ospita i moduli di PowerShell che è possibile utilizzare per gestire e distribuire le risorse allo Stack di Azure.

## <a name="download-targets"></a>Scaricare le destinazioni

È possibile scaricare e usare questi moduli di PowerShell per Azure Stack Development Kit, o a un client esterno basato su Windows che utilizza una connessione VPN.

## <a name="how-to-get-the-tools"></a>Come ottenere gli strumenti

Per ottenere questi strumenti, clonare il repository GitHub AzureStack-Tools o scaricare la cartella Strumenti di AzureStack eseguendo lo script seguente:

```PowerShell
# Change directory to the root directory
cd \

# Download the tools archive
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
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

| Funzionalità | DESCRIZIONE | chi può usare questo modulo? |
| --- | --- | --- |
| [Capacità del cloud](azure-stack-validate-templates.md) | Usare questo modulo per ottenere le funzionalità cloud di un cloud. Ad esempio, è possibile ottenere le funzionalità del cloud, ad esempio versione dell'API, le risorse di gestione risorse di Azure, le estensioni VM e così via per Stack di Azure e Azure cloud, usare il modulo. | Gli amministratori di cloud e gli utenti. |
| [Criteri di gestione risorse per lo Stack di Azure](azure-stack-policy-module.md) | Utilizzare questo modulo per configurare una sottoscrizione di Azure o un gruppo di risorse di Azure con la disponibilità di controllo delle versioni e il servizio stesso stack di Azure. | Utenti e amministratori di cloud |
| [Connessione a Azure Stack](azure-stack-connect-azure-stack.md) | Usare questo modulo per connettersi a un'istanza dello Stack di Azure tramite PowerShell e per configurare la connettività VPN tra Azure Stack. | Utenti e amministratori di cloud |
| [Validator del modello](azure-stack-validate-templates.md) | Utilizzare questo modulo per verificare se può essere distribuito esistente o un nuovo modello allo Stack di Azure. | Utenti e amministratori di cloud |

## <a name="next-steps"></a>Passaggi successivi

* [Configurare l'ambiente di PowerShell dell'utente Azure Stack](azure-stack-powershell-configure-user.md)
* [Connettersi al Kit di sviluppo dello Stack di Azure tramite una VPN](azure-stack-connect-azure-stack.md)
