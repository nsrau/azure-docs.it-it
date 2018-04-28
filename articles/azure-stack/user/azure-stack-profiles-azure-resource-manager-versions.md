---
title: Versioni di API del provider di risorse supportate dai profili nello Stack di Azure | Documenti Microsoft
description: Conoscere la versione di gestione risorse di Azure supportata dai profili nello Stack di Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: db01df21c95ee41197344cec719f1c2ab2dfc2ed
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Versioni di API del provider di risorse supportate dai profili nello Stack di Azure

Un provider di risorse di Azure fornisce risorse è possibile distribuire e gestire tramite Gestione risorse di Azure. Ogni provider offre operazioni per l'utilizzo di risorse. Alcuni provider di risorse comuni includono Microsoft. COMPUTE, che fornisce le macchine virtuali, appartenga, che fornisce risorse dell'account di archiviazione, e Microsoft, che fornisce risorse correlate nelle App web. Per altre informazioni, vedere [Provider e tipi di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services).

Nella tabella seguente per ogni provider di risorse indica la versione supportata della versione dell'API per lo Stack di Azure quando si usano i profili.

### <a name="microsoftauthorization"></a>Microsoft.Authorization

Utilizzare il controllo di accesso basata su ruoli per gestire le azioni che gli utenti dell'organizzazione possono eseguire sulle risorse. Questo set di operazioni consente di definire i ruoli, assegnare ruoli a utenti o gruppi e ottenere informazioni sulle autorizzazioni. Per ulteriori informazioni, vedere [autorizzazione](https://docs.microsoft.com/rest/api/authorization/).

| Tipi di risorsa | Versioni dell'API |
|---------------------|--------------------|
| Blocchi | 2017-04-01 |
| Operazioni | 2015-07-01 |
| Autorizzazioni | 2015-07-01 |
| Assegnazioni dei criteri | 2016-12-01 (2017-06-01-anteprima) |
| Definizioni dei criteri | 2016-12-01 |
| Operazioni dei provider | 2015-07-01-anteprima |
| Assegnazioni di ruolo | 2015-07-01 |
| Definizioni di ruolo | 2015-07-01 |

### <a name="microsoftcommerce"></a>Microsoft.Commerce

| Tipo di risorsa | Versione dell'API |
|----------------------------------|----------------------|
| Sottoscrizioni di Provider delegata | 2015-06-01 - anteprima |
| Utilizzo di delegati aggregazioni | 2015-06-01 - anteprima |
| Risorse di stima della spesa | 2015-06-01-anteprima |
| Operazioni | 2015-06-01 - anteprima |
| Sottoscrittore utilizzo aggregazioni | 2015-06-01 - anteprima |
| Usage Aggregates | 2015-06-01 - anteprima |

### <a name="microsoftcompute"></a>Microsoft.Compute

Le API di calcolo di Azure forniscono accesso programmatico alle macchine virtuali e le risorse di supporto. Per altre informazioni, vedere [calcolo di Azure](https://docs.microsoft.com/rest/api/compute/).

| Tipo di risorsa | Versione dell'API |
|---------------------------------------------------------------|-------------|
| SET DI DISPONIBILITÀ | 2016-03-30 |
| Località | 2016-03-30 |
| Posizioni/operazioni | 2016-03-30 |
| Posizioni/server di pubblicazione | 2016-03-30 |
| Utilizzi di percorsi / | 2016-03-30 |
| Posizioni/vmSizes | 2016-03-30 |
| Operazioni | 2016-03-30 |
| Macchine virtuali | 2016-03-30 |
| / Estensioni delle macchine virtuali | 2016-03-30 |
| Set di scalabilità di macchine virtuali | 2016-03-30 |
| Set di scalabilità della macchina virtuale/estensioni | 2016-03-30 |
| Interfacce di rete nel set scalabilità di macchine virtuali | 2016-03-30 |
| Macchine virtuali o set scalabilità macchina virtuale | 2016-03-30 |
| Scalabilità di macchine virtuali set/virtualMachines/interfacce di rete | 2016-03-30 |

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
| Regole di avviso | 2016-03-01 |
| Categorie di eventi | 2017-03-01-anteprima |
| Tipi di eventi | 2017-03-01-anteprima |
| Definizioni metrica | 2016-03-01 |
| Operazioni | 2015-04-01 |

### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

Gestione della chiave insiemi di credenziali, nonché le chiavi, i segreti e certificati nell'insieme di credenziali chiave. Per altre informazioni, vedere [riferimento all'API REST dell'insieme di credenziali chiave di Azure](https://docs.microsoft.com/rest/api/keyvault/).

| Tipi di risorsa | Versioni dell'API |
|-------------------------|--------------|
| Operazioni | 2016-10-01 |
| Insiemi di credenziali | 2016-10-01 |
| Insiemi di credenziali / criteri di accesso | 2016-10-01 |
| Gli insiemi di credenziali/segreti | 2016-10-01 |

### <a name="microsoftkeyvaultadmin"></a>Microsoft.Keyvault.Admin

Gestione della chiave insiemi di credenziali, nonché le chiavi, i segreti e certificati nell'insieme di credenziali chiave. Per altre informazioni, vedere [riferimento all'API REST dell'insieme di credenziali chiave di Azure](https://docs.microsoft.com/rest/api/keyvault/).

| Tipi di risorsa | Versioni dell'API |
|------------------|--------------------|
| Località | 2017-02-01-anteprima |
| Quote di percorsi / | 2017-02-01-anteprima |

### <a name="microsoftnetwork"></a>Microsoft.Network

Risultato della chiamata di operazioni è una rappresentazione dell'elenco di operazioni cloud rete disponibile. Per altre informazioni, vedere [operazione REST API](https://docs.microsoft.com/rest/api/operation/).

| Tipi di risorsa | Versioni dell'API |
|---------------------------|--------------|
| connessioni | 2015-06-15 |
| Le zone DNS | 2016-04-01 |
| Servizi di bilanciamento del carico | 2015-06-15 |
| Gateway di rete locale | 2015-06-15 |
| Località | 2016-04-01 |
| Percorso/operationResults | 2016-04-01 |
| Posizioni/operazioni | 2016-04-01 |
| Utilizzi di percorsi / | 2016-04-01 |
| Interfacce di rete | 2015-06-15 |
| Gruppi di sicurezza di rete | 2015-06-15 |
| Operazioni | 2015-06-15 |
| Indirizzo IP pubblico | 2015-06-15 |
| Tabelle di route | 2015-06-15 |
| Gateway di rete virtuale | 2015-06-15 |
| Reti virtuali | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

Azure Resource Manager consente di distribuire e gestire l'infrastruttura per le soluzioni di Azure. È possibile organizzare le risorse correlate in gruppi di risorse e distribuirle con modelli JSON. Per un'introduzione alla distribuzione e la gestione delle risorse con Gestione risorse, vedere [Panoramica di gestione risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

| Tipi di risorsa | Versioni dell'API |
|-----------------------------------------|-------------------|
| Registrazioni dell'applicazione | 2015-01-01 |
| Verificare il nome della risorsa | 2015-012016-09-01 |
| Provider di delegati | 2015-01-01 |
| Provider/offerte Delegate | 2015-01-01 |
| DelegatedProviders/offers/estimatePrice | 2015-01-01 |
| Deployments | 2016-0209-01 |
| Le distribuzioni/operazioni | 2016-0209-01 |
| Le estensioni metadati | 2015-01-01 |
| Collegamenti | 2015-012016-09-01 |
| Località | 2015-01-01 |
| Offerte | 2015-01-01 |
| Operazioni | 2015-01-01 |
| Providers | 2015-012017-08-01 |
| Gruppi di risorse | 2015-012016-09-01 |
| Risorse | 2015-012016-09-01 |
| Sottoscrizioni | 2015-012016-09-01 |
| Le sottoscrizioni/posizione | 2015-012016-09-01 |
| Risultati di sottoscrizioni/operazione | 2015-012016-09-01 |
| Le sottoscrizioni/provider | 2015-012017-08-01 |
| Gruppi di risorse/sottoscrizioni | 2015-012016-09-01 |
| Le sottoscrizioni/resourceGroups/risorse | 2015-012016-09-01 |
| Le sottoscrizioni/risorse | 2015-012016-09-01 |
| Le sottoscrizioni/tagNames | 2016-0609-01 |
| Le sottoscrizioni/tagNames/tagValues | 2016-0609-01 |
| Tenant | 2015-012017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage 

Il Provider di risorse di archiviazione (SRP) consente di gestire gli account di archiviazione e le chiavi a livello di codice. Per altre informazioni, vedere [riferimento all'API REST del Provider di Azure Storage risorsa](https://docs.microsoft.com/rest/api/storagerp/).

| Tipi di risorsa | Versioni dell'API |
|-------------------------|--------------|
| Controlla la disponibilità del nome | 2016-01-01 |
| Località | 2016-01-01 |
| Quote di percorsi / | 2016-01-01 |
| Operazioni | 2016-01-01 |
| storageAccounts | 2016-01-01 |
| Usi | 2016-01-01 |

## <a name="next-steps"></a>Passaggi successivi

* [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md)
* [Configurare l'ambiente di PowerShell dell'utente Azure Stack](azure-stack-powershell-configure-user.md)  
