---
title: Scaricare strumenti di Azure Stack da GitHub | Documenti Microsoft
description: Informazioni su come scaricare gli strumenti necessari per l'utilizzo dello Stack di Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: a116b7a048ff95ca601a65633cdc63f98fefee9d
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2018
---
# <a name="download-azure-stack-tools-from-github"></a>Scaricare strumenti di Azure Stack da GitHub

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

**Strumenti di AzureStack** è un repository di GitHub che ospita i moduli di PowerShell per la gestione e distribuzione delle risorse allo Stack di Azure. Se si prevede di stabilire la connettività VPN, è possibile scaricare questi moduli di PowerShell per il Kit di sviluppo dello Stack di Azure o a un client esterno basato su Windows. Per ottenere questi strumenti, clonare il repository GitHub o scaricare il **AzureStack strumenti** cartella eseguendo lo script seguente:

```PowerShell
# Change directory to the root directory. 
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```

## <a name="functionality-provided-by-the-modules"></a>Funzionalità offerte dai moduli

Il **AzureStack strumenti** repository contiene i moduli di PowerShell che supportano le funzionalità seguenti per lo Stack di Azure:  

| Funzionalità | DESCRIZIONE | chi può usare questo modulo? |
| --- | --- | --- |
| [Capacità del cloud](user/azure-stack-validate-templates.md) | Usare questo modulo per ottenere le funzionalità cloud di un cloud. Tramite questo modulo, ad esempio, è possibile ottenere le funzionalità del cloud, ad esempio la versione API e le risorse di gestione risorse di Azure. È anche possibile ottenere le estensioni di macchina virtuale per Stack di Azure e cloud di Azure usando questo modulo. | Gli utenti e agli operatori cloud |
| [Criteri di gestione risorse per lo Stack di Azure](user/azure-stack-policy-module.md) | Utilizzare questo modulo per configurare una sottoscrizione di Azure o un gruppo di risorse di Azure con la disponibilità di controllo delle versioni e il servizio stesso stack di Azure. | Gli utenti e agli operatori cloud |
| [Registrare con Azure](azure-stack-register.md) | Utilizzare questo modulo per registrare l'istanza del kit di sviluppo con Azure. Dopo la registrazione, è possibile scaricare gli elementi di marketplace da Azure e utilizzarli nello Stack di Azure. | Operatori di cloud |
| [Distribuzione di Azure Stack](azure-stack-run-powershell-script.md) | Usare questo modulo per preparare il computer host di Stack di Azure per distribuire e ridistribuire con l'immagine di disco rigido virtuale (VHD) di Azure Stack. | Operatori di cloud|
| [Connessione a Azure Stack](azure-stack-connect-powershell.md) | Usare questo modulo per configurare la connettività VPN tra Azure Stack. | Gli utenti e agli operatori cloud |
| [Validator del modello](user/azure-stack-validate-templates.md) | Utilizzare questo modulo per verificare se può essere distribuito esistente o un nuovo modello allo Stack di Azure. | Gli utenti e agli operatori cloud|


## <a name="next-steps"></a>Passaggi successivi
* [Configurare l'ambiente di PowerShell dell'utente Azure Stack](user/azure-stack-powershell-configure-user.md)   
* [Connettersi al Kit di sviluppo dello Stack di Azure tramite una VPN](azure-stack-connect-azure-stack.md)  
