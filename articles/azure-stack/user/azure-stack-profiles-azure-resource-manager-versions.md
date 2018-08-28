---
title: Le versioni di API del provider di risorse supportate da profili in Azure Stack | Microsoft Docs
description: Scopri la versione di Azure Resource Manager è supportata per i profili in Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 9d33ccf9262d4432ac7255121e97f318d00b5145
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2018
ms.locfileid: "43050650"
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Versioni di API del provider di risorse supportate da profili in Azure Stack

È possibile trovare il provider di risorse e i numeri di versione per ogni profilo di API usata da Azure Stack in questo articolo. Le tabelle in questo articolo elencano le versioni supportate per ogni provider di risorse e le versioni dell'API dei profili. Ogni provider di risorse contiene un set di tipi di risorse e i numeri di versione specifico.

Il profilo di API Usa le convenzioni di denominazione tre:
 - più recenti
 - Aaaa-mm-gg-hybrid
 - aaaa-mm-gg-profilo

Per una spiegazione dei profili di API e ritmo di rilascio versione per Azure Stack, vedere [profili della versione di gestione API in Azure Stack](azure-stack-version-profiles.md).

> [!Note]  
> Il **più recente** API profilo contiene la versione più recente della versione API del provider di risorse e non è elencato in questo articolo.

## <a name="overview-of-2018--03-01-hybrid"></a>Panoramica del 2018-03-01-hybrid

| Provider di risorse | Versione API-version |
|-----------------------------------------------|-----------------------------------------------------|
| Microsoft.Compute | 2017-03-30 |
| Microsoft.Network | 2017-10-01<br>Gateway VPN sarà 2017-03-01 |
| Microsoft. Storage (piano dati) | 2017-04-17 |
| Microsoft. Storage (piano di controllo) | 2016-01-01 |
| Microsoft. Web | 2016-08-01<br>Qual è il più recente (a partire da ora) di Azure |
| Microsoft.KeyVault | 2016-10-01 (invariato) |
| Resources (Azure Resource Manager se stesso) | 2016-02-01 |
| Authorization (operazioni di criteri) | 2015-11-01 |
| Microsoft.Insights | 2015-11-01 |
| Microsoft. keyvault | 2016-10-01 |
| Criterio | 2016-10-01 |
| Risorse | 2016-10-01 |
| Resources_Links | 2016-10-01 |
| Resources_Locks | 2016-10-01 |
| Sottoscrizioni | 2016-10-01 |

Per più un elenco delle versioni per ogni tipo di risorsa per i provider nel profilo di api, vedere [dettagli per il 2018-03-01-hybrid](#details-for-the-2018-03-01-hybrid) profilo.

## <a name="overview-of-2017-03-09-profile"></a>Panoramica del 2017-03-09-profilo

| Provider di risorse | Versione API-version |
|------------------------------------------------|------------------------------|
| Microsoft.Compute | 2016-03-30 |
| Microsoft.Network | 2015-06-15 |
| Microsoft. Storage (piano dati) | 2015-04-05  |
| Microsoft. Storage (piano di controllo) | 2016-01-01   |
| Microsoft.Websites | 2016-01-01 |
| Microsoft.KeyVault | 2016-10-01<br>(Non modificare) |
| Microsoft.Resources<br>(Azure Resource Manager se stesso) | 2016-02-01 |
| Microsoft.Authorization<Br>(operazioni di criteri) | 2015-11-01 |
| Microsoft.Insights | 2015-11-01 |
| Microsoft. keyvault | 2016-10-01 |
| Criterio | 2015-10-01-preview |
| Risorse | 2016-02-01 |
| Resources_Links | 2016-09-01 |
| Resources_Locks | 2016-09-01 |
| Sottoscrizioni | 2016-06-1 |

Per più un elenco delle versioni per ogni tipo di risorsa per i provider nel profilo di api, vedere [dettagli per il 2017-03-09-profilo](#details-for-the-2017-03-09-profile)

## <a name="details-for-the-2018-03-01-hybrid"></a>Dettagli per il 2018-03-01-hybrid

### <a name="microsoftauthorization"></a>Microsoft.Authorization

Usare controllo degli accessi in base al ruolo per gestire le azioni che gli utenti dell'organizzazione possono eseguire sulle risorse. Questo set di operazioni consente di definire i ruoli, assegnare ruoli a utenti o gruppi e ottenere informazioni sulle autorizzazioni. Per ulteriori informazioni, vedere [autorizzazione](https://docs.microsoft.com/rest/api/authorization/).

| Tipi di risorsa | Versioni dell'API |
|---------------------|--------------------|
| Blocchi | 2017-04-01 |
| Operazioni | 2015-07-01 |
| Autorizzazioni | 2015-07-01 |
| Assegnazioni di criteri | 2016-12-01 (2017-06-01-preview) |
| Definizioni dei criteri | 2016-12-01 |
| Operazioni di provider | 2015-07-01-preview |
| Assegnazioni di ruoli | 2015-07-01 |
| Definizioni di ruolo | 2015-07-01 |

### <a name="microsoftcommerce"></a>Microsoft.Commerce

| Tipo di risorsa | Versione dell'API |
|----------------------------------|----------------------|
| Sottoscrizioni del Provider delegata | 2015-06-01 - anteprima |
| Aggregazioni di utilizzo di delegati | 2015-06-01 - anteprima |
| Dedicare risorse stima | 2015-06-01-anteprima |
| Operazioni | 2015-06-01 - anteprima |
| Sottoscrittore dell'utilizzo aggregazioni | 2015-06-01 - anteprima |
| Usage Aggregates | 2015-06-01 - anteprima |

### <a name="microsoftcompute"></a>Microsoft.Compute

L'API di calcolo di Azure forniscono accesso programmatico alle macchine virtuali e le risorse di supporto. Per altre informazioni, vedere [calcolo di Azure](https://docs.microsoft.com/rest/api/compute/).

| Tipo di risorsa | Versione dell'API |
|---------------------------------------------------------------|-------------|
| SET DI DISPONIBILITÀ | 2016-03-30 |
| Località | 2016-03-30 |
| Località/operazioni | 2016-03-30 |
| Server di pubblicazione/località | 2016-03-30 |
| Utilizzi di percorsi / | 2016-03-30 |
| Località/vmSizes | 2016-03-30 |
| Operazioni | 2016-03-30 |
| Macchine virtuali | 2016-03-30 |
| Le macchine virtuali/extensions | 2016-03-30 |
| Set di scalabilità di macchine virtuali | 2016-03-30 |
| Set di scalabilità di macchine virtuali/le estensioni | 2016-03-30 |
| Interfacce di rete/set di scalabilità di macchine virtuali | 2016-03-30 |
| Macchine virtuale/set di scalabilità di macchine virtuali | 2016-03-30 |
| Set di scalabilità di macchine virtuali/virtualMachines/networkInterfaces | 2016-03-30 |

### <a name="microsoftgallery"></a>Microsoft.Gallery

| Tipo di risorsa | Versione dell'API |
|------------------|-------------|
| Cura | 2015-04-01 |
| Contenuto curato | 2015-04-01 |
| Estratto curato | 2015-04-01 |
| Elementi della raccolta | 2015-04-01 |
| Operazioni | 2015-04-01 |
| Portale | 2015-04-01 |
| Ricerca | 2015-04-01 |
| Suggerisci | 2015-04-01 |

### <a name="microsoftinsights"></a>Microsoft.Insights

| Tipi di risorsa | Versioni dell'API |
|--------------------|--------------------|
| Operazioni | 2015-04-01 |
| Tipi di eventi | 2015-04-01 |
| Categorie di eventi | 2015-04-01 |
| Definizioni metrica | 2018-01-01 |
| Metriche | 2018-01-01 |
| Impostazioni di diagnostica | 2017-05-01-preview |
| Categorie di impostazioni di diagnostica | 2017-05-01-preview |


### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

Gestione della chiave di insiemi di credenziali, nonché le chiavi, segreti e certificati all'interno di insiemi di credenziali delle chiavi. Per altre informazioni, vedere [riferimento all'API REST di Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/).

| Tipi di risorsa | Versioni dell'API |
|-------------------------|--------------|
| Operazioni | 2016-10-01 |
| Insiemi di credenziali | 2016-10-01 |
| Gli insiemi di credenziali / criteri di accesso | 2016-10-01 |
| Insiemi di credenziali dei segreti | 2016-10-01 |

### <a name="microsoftnetwork"></a>Microsoft.Network

Risultato della chiamata di operazioni è una rappresentazione in forma di elenco di operazioni disponibili rete cloud. Per altre informazioni, vedere [operazione REST API](https://docs.microsoft.com/rest/api/operation/).

| Tipi di risorsa | Versioni dell'API |
|---------------------------|--------------|
| connessioni | 2015-06-15 |
| Zone DNS | 2016-04-01 |
| Servizi di bilanciamento del carico | 2015-06-15 |
| Gateway di rete locale | 2015-06-15 |
| Località | 2016-04-01 |
| Percorso/operationResults | 2016-04-01 |
| Località/operazioni | 2016-04-01 |
| Utilizzi di percorsi / | 2016-04-01 |
| Interfacce di rete | 2015-06-15 |
| Gruppi di sicurezza di rete | 2015-06-15 |
| Operazioni | 2015-06-15 |
| Indirizzo IP pubblico | 2015-06-15 |
| Tabelle di route | 2015-06-15 |
| Gateway di rete virtuale | 2015-06-15 |
| Reti virtuali | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

Azure Resource Manager consente di distribuire e gestire l'infrastruttura per le soluzioni di Azure. È possibile organizzare le risorse correlate in gruppi di risorse e distribuirle con modelli JSON. Per un'introduzione alla distribuzione e la gestione delle risorse con Resource Manager, vedere [Panoramica di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

| Tipi di risorsa | Versioni dell'API |
|-----------------------------------------|-------------------|
| Registrazioni di applicazioni | 2015-01-01 |
| Verificare il nome della risorsa | 2016-09-01 |
| Provider delegati | 2015-01-01 |
| I provider/offerte Delegate | 2015-01-01 |
| DelegatedProviders/offers/estimatePrice | 2015-01-01 |
| Deployments | 2016-09-01 |
| Le distribuzioni/operazioni | 2016-09-01 |
| Le estensioni metadati | 2015-01-01 |
| Collegamenti | 2016-09-01 |
| Località | 2015-01-01 |
| Offerte | 2015-01-01 |
| Operazioni | 2015-01-01 |
| Providers | 2017-08-01 |
| Gruppi di risorse | 2016-09-01 |
| Risorse | 2016-09-01 |
| Sottoscrizioni | 2016-09-01 |
| Le sottoscrizioni/località | 2016-09-01 |
| Risultati dell'operazione/sottoscrizioni | 2016-09-01 |
| Le sottoscrizioni/provider | 2017-08-01 |
| Gruppi di risorse/sottoscrizioni | 2016-09-01 |
| Le sottoscrizioni/resourceGroups/risorse | 2016-09-01 |
| Le sottoscrizioni e risorse | 2016-09-01 |
| Le sottoscrizioni/tagNames | 2016-09-01 |
| Le sottoscrizioni/tagNames/tagValues | 2016-09-01 |
| Tenant | 2017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage 

Il Provider di risorse archiviazione (SRP) consente di gestire l'account di archiviazione e le chiavi a livello di codice. Per altre informazioni, vedere [riferimento all'API di Azure Storage Resource Provider REST](https://docs.microsoft.com/rest/api/storagerp/).

| Tipi di risorsa | Versioni dell'API |
|-------------------------|--------------|
| Controlla la disponibilità del nome | 2016-01-01 |
| Località | 2016-01-01 |
| Località/quote | 2016-01-01 |
| Operazioni | 2016-01-01 |
| StorageAccounts | 2016-01-01 |
| Usi | 2016-01-01 |

## <a name="details-for-the-2017-03-09-profile"></a>Dettagli per il 2017-03-09-profilo

### <a name="microsoft-authorization"></a>Autorizzazione Microsoft

| Tipi di risorsa | Versioni dell'API |
|---------------------|---------------------------------|
| Blocchi | 2017-04-01 |
| Operazioni | 2015-07-01 |
| Autorizzazioni | 2015-07-01 |
| Assegnazioni di criteri | 2016-12-01 (2017-06-01-preview) |
| Definizioni dei criteri | 2016-12-01 |
| Operazioni di provider | 2015-07-01-preview |
| Assegnazioni di ruoli | 2015-07-01 |
| Definizioni di ruolo | 2015-07-01 |

### <a name="microsoftcompute"></a>Microsoft.Compute

| Tipo di risorsa | Versione dell'API |
|---------------------------------------------------------------|-------------|
| SET DI DISPONIBILITÀ | 2016-03-30 |
| Località | 2016-03-30 |
| Località/operazioni | 2016-03-30 |
| Server di pubblicazione/località | 2016-03-30 |
| Utilizzi di percorsi / | 2016-03-30 |
| Località/vmSizes | 2016-03-30 |
| Operazioni | 2016-03-30 |
| Macchine virtuali | 2016-03-30 |
| Le macchine virtuali/extensions | 2016-03-30 |
| Set di scalabilità di macchine virtuali | 2016-03-30 |
| Set di scalabilità di macchine virtuali/le estensioni | 2016-03-30 |
| Interfacce di rete/set di scalabilità di macchine virtuali | 2016-03-30 |
| Macchine virtuale/set di scalabilità di macchine virtuali | 2016-03-30 |
| Set di scalabilità di macchine virtuali/virtualMachines/networkInterfaces | 2016-03-30 |

### <a name="microsoftnetwork"></a>Microsoft.Network

| Tipi di risorsa | Versioni dell'API |
|---------------------------|--------------|
| connessioni | 2015-06-15 |
| Zone DNS | 2016-04-01 |
| Servizi di bilanciamento del carico | 2015-06-15 |
| Gateway di rete locale | 2015-06-15 |
| Località | 2016-04-01 |
| Percorso/operationResults | 2016-04-01 |
| Località/operazioni | 2016-04-01 |
| Utilizzi di percorsi / | 2016-04-01 |
| Interfacce di rete | 2015-06-15 |
| Gruppi di sicurezza di rete | 2015-06-15 |
| Operazioni | 2015-06-15 |
| Indirizzo IP pubblico | 2015-06-15 |
| Tabelle di route | 2015-06-15 |
| Gateway di rete virtuale | 2015-06-15 |
| Reti virtuali | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

| Tipi di risorsa | Versioni dell'API |
|-----------------------------------------|--------------|
| Registrazioni di applicazioni | 2015-01-01 |
| Verificare il nome della risorsa | 2016-09-01 |
| Provider delegati | 2015-01-01 |
| I provider/offerte Delegate | 2015-01-01 |
| DelegatedProviders/offers/estimatePrice | 2015-01-01 |
| Deployments | 2016-09-01 |
| Le distribuzioni/operazioni | 2016-09-01 |
| Le estensioni metadati | 2015-01-01 |
| Collegamenti | 2016-09-01 |
| Località | 2015-01-01 |
| Offerte | 2015-01-01 |
| Operazioni | 2015-01-01 |
| Providers | 2017-08-01 |
| Gruppi di risorse | 2016-09-01 |
| Risorse | 2016-09-01 |
| Sottoscrizioni | 2016-09-01 |
| Le sottoscrizioni/località | 2016-09-01 |
| Risultati dell'operazione/sottoscrizioni | 2016-09-01 |
| Le sottoscrizioni/provider | 2017-08-01 |
| Gruppi di risorse/sottoscrizioni | 2016-09-01 |
| Le sottoscrizioni/resourceGroups/risorse | 2016-09-01 |
| Le sottoscrizioni e risorse | 2016-09-01 |
| Subscriptiosn/tagNames | 2016-09-01 |
| Le sottoscrizioni/tagNames/tagValues | 2016-09-01 |
| Tenant | 2017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage

| Tipi di risorsa | Versioni dell'API |
|-------------------------|--------------|
| Controlla la disponibilità del nome | 2016-01-01 |
| Località | 2016-01-01 |
| Località/quote | 2016-01-01 |
| Operazioni | 2016-01-01 |
| StorageAccounts | 2016-01-01 |
| Usi | 2016-01-01 |

## <a name="next-steps"></a>Passaggi successivi

* [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md)
* [Configurare l'ambiente di PowerShell dell'utente Azure Stack](azure-stack-powershell-configure-user.md)  
