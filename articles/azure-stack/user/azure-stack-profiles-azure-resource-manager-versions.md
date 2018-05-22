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
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: ee4321b905396f78e7dad9248b9e377dad250a13
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Versioni di API del provider di risorse supportate dai profili nello Stack di Azure

È possibile trovare il provider di risorse e i numeri di versione per ogni profilo di API usata dallo Stack di Azure in questo articolo. Le tabelle in questo articolo elencano le versioni supportate per ogni provider di risorse e le versioni API dei profili. Ogni provider di risorse contiene un set di tipi di risorse e i numeri di versione specifico.

Il profilo di API utilizza tre convenzioni di denominazione:
 - più recenti
 - Aaaa-mm-gg-ibrida
 - aaaa-mm-gg-profilo

Per una spiegazione dei profili di API e rilasci di versione per lo Stack di Azure, vedere [profili versione API di gestione in Azure Stack](azure-stack-version-profiles.md).

> [!Note]  
> Il **più recente** API profilo contiene la versione più recente della versione API del provider di risorse e non è elencato in questo articolo.

## <a name="overview-of-2018--03-01-hybrid"></a>Panoramica di 2018-03-01-ibrida

| Provider di risorse | versione dell'API |
|-----------------------------------------------|-----------------------------------------------------|
| Microsoft.Compute | 2017-03-30 |
| Microsoft.Network | 2017-10-01<br>Gateway VPN sarà 2017-03-01 |
| Appartenga (piano di dati) | 2017-04-17 |
| Appartenga (piano di controllo) | 2016-01-01 |
| Microsoft. Web | 2016-08-01<br>Qual è la versione più recente (a partire da ora) in Azure |
| Microsoft.KeyVault | 2016-10-01 (non modificare) |
| Microsoft.Resources (Gestione risorse di Azure stesso) | 2016-02-01 |
| Microsoft (operazioni di criteri) | 2015-11-01 |
| Microsoft.Insights | 2015-11-01 |
| Microsoft.Keyvault | 2016-10-01 |
| Criterio | 2016-10-01 |
| Risorse | 2016-10-01 |
| Resources_Links | 2016-10-01 |
| Resources_Locks | 2016-10-01 |
| Sottoscrizioni | 2016-10-01 |

Per più un elenco delle versioni per ogni tipo di risorsa per i provider nel profilo di api, vedere [i dettagli per 2018-03-01-ibrida](#details-for-the-2018-03-01-hybrid) profilo.

## <a name="overview-of-2017-03-09-profile"></a>Panoramica del 2017-03-09-profilo

| Provider di risorse | versione dell'API |
|------------------------------------------------|------------------------------|
| Microsoft.Compute | 2016-03-30 |
| Microsoft.Network | 2015-06-15 |
| Appartenga (piano di dati) | 2015-04-05  |
| Appartenga (piano di controllo) | 2016-01-01   |
| Microsoft.Websites | 2016-01-01 |
| Microsoft.KeyVault | 2016-10-01<br>(Non modificare) |
| Microsoft.Resources<br>(Gestione risorse di azure stesso) | 2016-02-01 |
| Microsoft.Authorization<Br>(operazioni di criteri) | 2015-11-01 |
| Microsoft.Insights | 2015-11-01 |
| Microsoft.Keyvault | 2016-10-01 |
| Criterio | 2015-10-01-anteprima |
| Risorse | 2016-02-01 |
| Resources_Links | 2016-09-01 |
| Resources_Locks | 2016-09-01 |
| Sottoscrizioni | 2016-06-1 |

Per più un elenco delle versioni per ogni tipo di risorsa per i provider nel profilo di api, vedere [dettagli per il 2017-03-09-profilo](#details-for-the-2017-03-09-profile)

## <a name="details-for-the-2018-03-01-hybrid"></a>Dettagli per 2018-03-01-ibrida

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
| StorageAccounts | 2016-01-01 |
| Usi | 2016-01-01 |

## <a name="details-for-the-2017-03-09-profile"></a>Dettagli per 2017-03-09-profilo

### <a name="microsoft-authorization"></a>Autorizzazione Microsoft

| Tipi di risorsa | Versioni dell'API |
|---------------------|---------------------------------|
| Blocchi | 2017-04-01 |
| Operazioni | 2015-07-01 |
| Autorizzazioni | 2015-07-01 |
| Assegnazioni dei criteri | 2016-12-01 (2017-06-01-anteprima) |
| Definizioni dei criteri | 2016-12-01 |
| Operazioni dei provider | 2015-07-01-anteprima |
| Assegnazioni di ruolo | 2015-07-01 |
| Definizioni di ruolo | 2015-07-01 |

### <a name="microsoftcompute"></a>Microsoft.Compute

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

### <a name="microsoftnetwork"></a>Microsoft.Network

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

| Tipi di risorsa | Versioni dell'API |
|-----------------------------------------|--------------|
| Registrazioni dell'applicazione | 2015-01-01 |
| Verificare il nome della risorsa | 2016-09-01 |
| Provider di delegati | 2015-01-01 |
| Provider/offerte Delegate | 2015-01-01 |
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
| Le sottoscrizioni/posizione | 2016-09-01 |
| Risultati di sottoscrizioni/operazione | 2016-09-01 |
| Le sottoscrizioni/provider | 2017-08-01 |
| Gruppi di risorse/sottoscrizioni | 2016-09-01 |
| Le sottoscrizioni/resourceGroups/risorse | 2016-09-01 |
| Le sottoscrizioni/risorse | 2016-09-01 |
| Subscriptiosn/tagNames | 2016-09-01 |
| Le sottoscrizioni/tagNames/tagValues | 2016-09-01 |
| Tenant | 2017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage

| Tipi di risorsa | Versioni dell'API |
|-------------------------|--------------|
| Controlla la disponibilità del nome | 2016-01-01 |
| Località | 2016-01-01 |
| Quote di percorsi / | 2016-01-01 |
| Operazioni | 2016-01-01 |
| StorageAccounts | 2016-01-01 |
| Usi | 2016-01-01 |

## <a name="next-steps"></a>Passaggi successivi

* [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md)
* [Configurare l'ambiente di PowerShell dell'utente Azure Stack](azure-stack-powershell-configure-user.md)  
