---
title: Azure Security Control - Secure Configuration
description: Configurazione sicura del controllo di sicurezza
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 03564effeee36ddb3316d48329ccab8ccfce75b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934302"
---
# <a name="security-control-secure-configuration"></a>Controllo di sicurezza: Configurazione sicura

Stabilire, implementare e gestire attivamente (tenere traccia, creare report e correggere) la configurazione di sicurezza delle risorse di Azure per impedire agli utenti malintenzionati di sfruttare i servizi e le impostazioni vulnerabili.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Stabilire configurazioni sicure per tutte le risorse di Azure7.1: Establish secure configurations for all Azure resources

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 7.1 | 5.1 | Customer |

Usare Criteri di Azure o Centro sicurezza di Azure per gestire le configurazioni di sicurezza per tutte le risorse di Azure.Use Azure Policy or Azure Security Center to maintain security configurations for all Azure Resources.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: Stabilire configurazioni sicure del sistema operativo

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 7.2 | 5.1 | Customer |

Usare il Consiglio &quot;di sicurezza di Azure per correggere&quot; le vulnerabilità nelle configurazioni di sicurezza nelle macchine virtuali per gestire le configurazioni di sicurezza in tutte le risorse di calcolo.

Come monitorare i consigli del Centro sicurezza di Azure:How to monitor Azure Security Center recommendations:

https://docs.microsoft.com/azure/security-center/security-center-recommendations

Come risolvere i suggerimenti del Centro sicurezza di Azure:How to remediate Azure Security Center recommendations:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Gestire le configurazioni delle risorse di Azure protette

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 7.3 | 5,2 | Customer |

Usare i criteri di Azure [nega] e [distribuzione se non esistono] per applicare impostazioni sicure tra le risorse di Azure.Use Azure policy [deny] and [deploy if not exist] to enforce secure settings across your Azure resources.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Informazioni sugli effetti dei criteri di Azure:Understand Azure Policy Effects:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Mantenere le configurazioni sicure del sistema operativo

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 7.4 | 5,2 | Condiviso |

Le immagini del sistema operativo di base vengono gestite e gestite da Microsoft.

Tuttavia, è possibile applicare le impostazioni di sicurezza richieste dall'organizzazione usando i modelli di Azure Resource Manager e/o la configurazione dello stato desiderato.

Come creare una macchina virtuale di Azure da un modello di Azure Resource Manager:How to create an Azure Virtual Machine from an Azure Resource Manager template:

https://docs.microsoft.com/azure/virtual-machines/windows/ps-template

Informazioni sulla configurazione dello stato desiderato per le macchine virtuali di Azure:Understand Desired State Configuration for Azure Virtual Machines:

https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-overview

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Archiviare in modo sicuro la configurazione delle risorse di Azure7.5: Securely store configuration of Azure resources

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 7.5 | 5.3 | Customer |

Se si usano definizioni di criteri di Azure personalizzate, usare DevOps di Azure o Azure Repos per archiviare e gestire il codice in modo sicuro.

Come archiviare il codice in DevOps di Azure:How to store code in Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos Documentation:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: Memorizzare in modo sicuro immagini personalizzate del sistema operativo

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 7.6 | 5.3 | Customer |

Se si usano immagini personalizzate, usare il controllo degli accessi in base al ruolo per garantire che solo gli utenti autorizzati possano accedere alle immagini. Per le immagini del contenitore, archiviarle nel Registro di sistema del contenitore di Azure e sfruttare il controllo degli accessi in base al ruolo per garantire che solo gli utenti autorizzati possano accedere alle immagini.

Informazioni sul controllo degli accessi in base al ruolo in Azure:Understand RBAC in Azure

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Comprendere il controllo degli accessi in base al ruolo per il Registro di sistema del contenitore:

https://docs.microsoft.com/azure/container-registry/container-registry-roles

Come configurare il controllo degli accessi in base al ruolo in Azure:How to configure RBAC in Azure:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

## <a name="77-deploy-system-configuration-management-tools"></a>7.7: Distribuire gli strumenti di gestione della configurazione di sistema

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 7.7 | 5.4 | Customer |

Usare Criteri di Azure per avvisare, controllare e applicare le configurazioni di sistema. Inoltre, sviluppare un processo e una pipeline per la gestione delle eccezioni dei criteri.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 7.8 | 5.4 | Customer |

Usare le estensioni di calcolo di Azure, ad esempio PowerShell Desired State Configuration for Windows compute o Linux Chef Extension per Linux.Use Azure compute extensions such as PowerShell Desired State Configuration for Windows compute or Linux Chef Extension for Linux.

Come installare le estensioni delle macchine virtuali in Azure:How to install Virtual Machine Extensions in Azure:

https://docs.microsoft.com/azure/virtual-machines/extensions/overview#how-can-i-install-an-extension

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementare il monitoraggio automatico della configurazione per i servizi di Azure7.9: Implement automated configuration monitoring for Azure services

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 7.9 | 5.5 | Customer |

Usare il Centro sicurezza di Azure per eseguire analisi di base per le risorse di AzureUse Azure Security Center to perform baseline scans for your Azure Resources

Come correggere i consigli nel Centro sicurezza di Azure:How to remediate recommendations in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementare il monitoraggio automatico della configurazione per i sistemi operativi

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 7.1 | 5.5 | Customer |

Usare il Centro sicurezza di Azure per eseguire le analisi di base per le impostazioni del sistema operativo e Docker per i contenitori.

Informazioni sui consigli per i contenitori del Centro sicurezza di Azure:Understand Azure Security Center container recommendations:

https://docs.microsoft.com/azure/security-center/security-center-container-recommendations

## <a name="711-manage-azure-secrets-securely"></a>7.11: Gestire i segreti di Azure in modo sicuro 

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 7.11 | 13.1 | Customer |

Usare l'identità del servizio gestito insieme a Archiviazione delle chiavi di Azure per semplificare e proteggere la gestione dei segreti per le applicazioni cloud.

Come integrare con le identità gestite di Azure:How to integrate with Azure Managed Identities:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Come creare un insieme di credenziali delle chiavi:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Come fornire l'autenticazione key Vault con un'identità gestita:How to provide Key Vault authentication with a managed identity:

https://docs.microsoft.com/azure/key-vault/managed-identity

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gestire le identità in modo sicuro e automatico

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 7.12 | 4.1 | Customer |

Usare le identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure AD. Managed Identities consente di eseguire l'autenticazione a qualsiasi servizio che supporta l'autenticazione di Azure AD, incluso Key Vault, senza credenziali nel codice.

Come configurare le identità gestite:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminare l'esposizione di credenziali indesiderate

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 7.13 | 13.3 | Customer |

Implementare Credential Scanner per identificare le credenziali all'interno del codice. Credential Scanner will also encourage moving discovered credentials to more secure locations such as Azure Key Vault. 

Come configurare Credential Scanner:

https://secdevtools.azurewebsites.net/helpcredscan.html

## <a name="next-steps"></a>Passaggi successivi

Vedere il controllo di sicurezza successivo: [Malware Defense](security-control-malware-defense.md)
