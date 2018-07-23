---
title: Scaricare gli strumenti di Azure Stack da GitHub | Microsoft Docs
description: Informazioni su come scaricare gli strumenti necessari per lavorare con Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2018
ms.author: mabrigg
ms.openlocfilehash: 9c1e4abe50b06db58a4ca05a99e1ae4a531b2294
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187452"
---
# <a name="download-azure-stack-tools-from-github"></a>Scaricare gli strumenti di Azure Stack da GitHub

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

AzureStack-Tools è un repository di GitHub che ospita i moduli di PowerShell che è possibile usare per gestire e distribuire le risorse di Azure Stack.

## <a name="download-targets"></a>Scaricare le destinazioni

È possibile scaricare e usare questi moduli di PowerShell per Azure Stack Development Kit, o a un client esterno basato su Windows che usa una connessione VPN.

## <a name="how-to-get-the-tools"></a>Come ottenere gli strumenti

Per ottenere questi strumenti, clonare il repository GitHub degli strumenti di AzureStack o scaricare la cartella Strumenti di AzureStack eseguendo lo script seguente:

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

Il repository AzureStack-Tools contiene i moduli di PowerShell che supportano le funzionalità seguenti per Azure Stack:

| Funzionalità | DESCRIZIONE | Chi può usare questo modulo? |
| --- | --- | --- |
| [Funzionalità del cloud](https://github.com/Azure/AzureStack-Tools/tree/master/CloudCapabilities) | Usare questo modulo per ottenere le funzionalità cloud di un cloud. Ad esempio, è possibile ottenere le funzionalità di cloud, ad esempio versione dell'API, risorse di Azure Resource Manager, le estensioni di VM e così via per Azure Stack e cloud di Azure usando il modulo. | Gli amministratori del cloud e utenti. |
| [Criteri di Resource Manager per Azure Stack](azure-stack-policy-module.md) | Usare questo modulo per configurare una sottoscrizione di Azure o un gruppo di risorse di Azure con la stessa disponibilità di servizio e controllo delle versioni di Azure Stack. | Gli utenti e amministratori cloud |
| [La connessione ad Azure Stack](azure-stack-connect-azure-stack.md) | Usare questo modulo per la connessione a un'istanza di Azure Stack tramite PowerShell e per configurare la connettività VPN ad Azure Stack. | Gli utenti e amministratori cloud |
| [Validator del modello](azure-stack-validate-templates.md) | Usare questo modulo per verificare se un modello nuovo o esistente può essere distribuito in Azure Stack. | Gli utenti e amministratori cloud |

## <a name="next-steps"></a>Passaggi successivi

- [Configurare l'ambiente di PowerShell dell'utente Azure Stack](azure-stack-powershell-configure-user.md)
- [Connettersi a Azure Stack Development Kit su una rete VPN](azure-stack-connect-azure-stack.md)
