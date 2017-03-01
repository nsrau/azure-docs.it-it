---
title: Connessione al portale Azure per enti pubblici con PowerShell | Documentazione Microsoft
description: Informazioni sulla connessione della sottoscrizione in Azure per enti pubblici con PowerShell
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: 47e5e535-baa0-457e-8c41-f9fd65478b38
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: 3a0594fa639a2ac34c53b2263dbf7dd2410cf16a
ms.lasthandoff: 02/21/2017


---



# <a name="connect-to-azure-government-with-powershell"></a>Connettersi ad Azure per enti pubblici con PowerShell
Per usare l'interfaccia della riga di comando di Azure, è necessario connettersi ad Azure per enti pubblici invece della versione pubblica di Azure. L'interfaccia della riga di comando di Azure consente di gestire una sottoscrizione di grandi dimensioni tramite script o di accedere alle funzionalità attualmente non disponibili nel portale di Azure. Se è stato usato PowerShell nella versione pubblica di Azure, è essenzialmente la stessa cosa.  Le differenze in Azure per enti pubblici sono le seguenti:

* Connessione dell'account
* Nomi delle aree

> [!NOTE]
> Se non è stato ancora usato PowerShell, vedere l'[introduzione ad Azure PowerShell](/powershell/azureps-cmdlets-docs).
> 
> 

Quando si avvia PowerShell, è necessario indicare ad Azure PowerShell di connettersi ad Azure per enti pubblici specificando un parametro di ambiente.  Il parametro assicura che PowerShell si connetta agli endpoint corretti.  La raccolta di endpoint viene determinata durante l'accesso al proprio account.  API diverse richiedono versioni diverse dell'opzione Environment:

| Tipo di connessione | Comando |
| --- | --- |
| Comandi di [Azure Active Directory (modello di distribuzione classica)](https://msdn.microsoft.com/library/dn708504.aspx) |`Add-AzureAccount -Environment AzureUSGovernment` |
| Comandi di [gestione delle risorse](https://msdn.microsoft.com/library/mt125356.aspx) |`Login-AzureRmAccount -EnvironmentName AzureUSGovernment` |
| Comandi di [Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx) |`Connect-MsolService -AzureEnvironment UsGovernment` |
| Comandi di [Azure Active Directory (modello di distribuzione di Resource Manager)](https://msdn.microsoft.com/library/azure/mt757189.aspx) |`Connect-AzureAD -AzureEnvironmentName AzureUSGovernment` |
| [Riga di comando dell'interfaccia della riga di comando di Azure](../xplat-cli-install.md) |`azure login –environment "AzureUSGovernment"` |

È anche possibile usare l'opzione Environment quando ci si connette a un account di archiviazione usando New-AzureStorageContext e specificando AzureUSGovernment.

### <a name="determining-region"></a>Determinazione dell'area
Una volta stabilita la connessione, un'altra differenza è rappresentata dalle aree usate come destinazione per un servizio.  Ogni cloud Azure ha aree diverse.  Le aree sono elencate nella pagina relativa alla disponibilità del servizio.  In genere si usano le aree nel parametro Location per un comando.

C'è un aspetto da considerare.  Le aree di Azure per enti pubblici devono essere formattate in modo diverso rispetto ai relativi nomi comuni:

| Nome comune | Comando |
| --- | --- |
| US Gov Virginia |USGov Virginia |
| US Gov Iowa |USGov Iowa |

> [!NOTE]
> Non c'è alcuno spazio tra US e Gov quando si usa il parametro Location.
> 
> 

Per convalidare le aree disponibili in Azure per enti pubblici, è possibile eseguire i comandi seguenti e stampare l'elenco corrente:

    Get-AzureLocation

Per informazioni sugli ambienti disponibili in Azure, è possibile eseguire:

    Get-AzureEnvironment

