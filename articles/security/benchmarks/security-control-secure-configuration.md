---
title: Azure Security Control - Secure Configuration
description: Configurazione sicura del controllo di sicurezza di AzureAzure Security Control Secure Configuration
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: bfa16f59155e420209ab6370056a7c612b5327e4
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759052"
---
# <a name="security-control-secure-configuration"></a>Controllo di sicurezza: Configurazione sicura

Stabilire, implementare e gestire attivamente (tenere traccia, creare report e correggere) la configurazione di sicurezza delle risorse di Azure per impedire agli utenti malintenzionati di sfruttare i servizi e le impostazioni vulnerabili.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Stabilire configurazioni sicure per tutte le risorse di Azure7.1: Establish secure configurations for all Azure resources

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 7.1 | 5.1 | Customer |

Usare gli alias di Criteri di Azure per creare criteri personalizzati per controllare o applicare la configurazione delle risorse di Azure.Use Azure Policy aliases to create custom policies to audit or enforce the configuration of your Azure resources. È anche possibile usare definizioni di criteri di Azure predefinite.

Inoltre, Azure Resource Manager è in grado di esportare il modello in JSON (JavaScript Object Notation), che deve essere rivisto per garantire che le configurazioni soddisfino o superino i requisiti di sicurezza per l'organizzazione.

È anche possibile usare consigli dal Centro sicurezza di Azure come linea di base di configurazione sicura per le risorse di Azure.You may also use recommendations from Azure Security Center as a secure configuration baseline for your Azure resources.

- [Come visualizzare gli alias di Criteri di Azure disponibiliHow to view available Azure Policy aliases](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Esercitazione: Creare e gestire i criteri per applicare la conformitàTutorial: Create and manage policies to enforce compliance](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Esportazione di risorse singole e multi-risorsa in un modello nel portale di AzureSingle and multi-resource export to a template in Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [Raccomandazioni sulla sicurezza - una guida di riferimento](https://docs.microsoft.com/azure/security-center/recommendations-reference)

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: Stabilire configurazioni sicure del sistema operativo

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 7.2 | 5.1 | Customer |

Usare i consigli del Centro sicurezza di Azure per gestire le configurazioni di sicurezza in tutte le risorse di calcolo.  Inoltre, è possibile usare immagini del sistema operativo personalizzate o la configurazione dello stato di automazione di Azure per stabilire la configurazione di sicurezza del sistema operativo richiesto dall'organizzazione.

- [Come monitorare i consigli del Centro sicurezza di AzureHow to monitor Azure Security Center recommendations](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

- [Raccomandazioni sulla sicurezza - una guida di riferimento](https://docs.microsoft.com/azure/security-center/recommendations-reference)

- [Panoramica della configurazione dello stato di automazione di AzureAzure Automation State Configuration Overview](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Caricare un disco rigido virtuale e usarlo per creare nuove macchine virtuali Windows in AzureUpload a VHD and use it to create new Windows VMs in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

- [Creare una macchina virtuale Linux da un disco personalizzato tramite l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Gestire le configurazioni delle risorse di Azure protette

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 7.3 | 5,2 | Customer |

Usare i criteri di Azure [nega] e [distribuzione se non esistono] per applicare impostazioni sicure tra le risorse di Azure.Use Azure policy [deny] and [deploy if not exist] to enforce secure settings across your Azure resources.  Inoltre, è possibile usare i modelli di Azure Resource Manager per gestire la configurazione di sicurezza delle risorse di Azure richieste dall'organizzazione. 

- [Informazioni sugli effetti di Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [Creare e gestire i criteri per applicare la conformità](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Panoramica dei modelli di Azure Resource ManagerAzure Resource Manager templates overview](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Mantenere le configurazioni sicure del sistema operativo

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 7.4 | 5,2 | Condiviso |

Seguire i consigli del Centro sicurezza di Azure per l'esecuzione di valutazioni delle vulnerabilità nelle risorse di calcolo di Azure.Follow recommendations from Azure Security Center on performing vulnerability assessments on your Azure compute resources.  Inoltre, è possibile usare i modelli di Azure Resource Manager, le immagini personalizzate del sistema operativo o la configurazione dello stato di automazione di Azure per mantenere la configurazione di sicurezza del sistema operativo richiesto dall'organizzazione.   I modelli di macchina virtuale Microsoft combinati con la configurazione dello stato desiderato di Automazione di Azure possono essere utili per soddisfare e gestire i requisiti di sicurezza. 

Si noti inoltre che le immagini delle macchine virtuali di Azure Marketplace pubblicate da Microsoft vengono gestite e gestite da Microsoft. 

- [Come implementare i consigli per la valutazione della vulnerabilità del Centro sicurezza pc di AzureHow to implement Azure Security Center vulnerability assessment recommendations](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

- [Come creare una macchina virtuale di Azure da un modello di Azure Resource ManagerHow to create an Azure Virtual Machine from an Azure Resource Manager template](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

- [Panoramica della configurazione dello stato di automazione di AzureAzure Automation State Configuration Overview](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Creare una macchina virtuale di Windows nel portale di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

- [Informazioni su come scaricare il modello di macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

- [Script di esempio per caricare un disco rigido virtuale in Azure e creare una nuova macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Archiviare in modo sicuro la configurazione delle risorse di Azure7.5: Securely store configuration of Azure resources

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 7.5 | 5.3 | Customer |

Usare DevOps di Azure per archiviare e gestire in modo sicuro il codice, ad esempio criteri di Azure personalizzati, modelli di Azure Resource Manager e script di configurazione dello stato desiderato. Per accedere alle risorse gestite in Azure DevOps, è possibile concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza incorporati o gruppi definiti in Azure Active Directory (Azure AD) se integrati con Azure DevOps o Active Directory se integrati con TFS.

- [Come archiviare il codice in DevOps di AzureHow to store code in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Informazioni su autorizzazioni e gruppi in DevOps di AzureAbout permissions and groups in Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: Memorizzare in modo sicuro immagini personalizzate del sistema operativo

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 7.6 | 5.3 | Customer |

Se si usano immagini personalizzate, usare il controllo degli accessi in base al ruolo per garantire che solo gli utenti autorizzati possano accedere alle immagini. Usando una raccolta di immagini condivise è possibile condividere le immagini con utenti diversi, entità servizio o gruppi di Active Directory all'interno dell'organizzazione.  Per le immagini del contenitore, archiviarle nel Registro di sistema del contenitore di Azure e sfruttare il controllo degli accessi in base al ruolo per garantire che solo gli utenti autorizzati possano accedere alle immagini.  

- [Informazioni sul controllo degli accessi in base al ruolo in AzureUnderstand RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

- [Informazioni sul controllo degli accessi in base al ruolo per il Registro di sistema dei](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

- [Come configurare il controllo degli accessi in base al ruolo in AzureHow to configure RBAC in Azure](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

- [Panoramica sulla raccolta di immagini condivise](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuire gli strumenti di gestione della configurazione per le risorse di Azure7.7: Deploy configuration management tools for Azure resources

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 7.7 | 5.4 | Customer |

Definire e implementare configurazioni di sicurezza standard per le risorse di Azure usando Criteri di Azure.Define and implement standard security configurations for Azure resources using Azure Policy. Usare gli alias di Criteri di Azure per creare criteri personalizzati per controllare o applicare la configurazione di rete delle risorse di Azure.Use Azure Policy aliases to create custom policies to audit or enforce the network configuration of your Azure resources. È inoltre possibile utilizzare le definizioni dei criteri predefinite relative alle risorse specifiche.  Inoltre, è possibile usare Automazione di Azure per distribuire le modifiche alla configurazione.

- [Come configurare e gestire Criteri di AzureHow to configure and manage Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Come utilizzare gli alias](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Distribuire gli strumenti di gestione della configurazione per i sistemi operativi

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 7.8 | 5.4 | Customer |

Configurazione dello stato di automazione di Azure è un servizio di gestione della configurazione per i nodi DSC (Desired State Configuration) in qualsiasi data center cloud o locale. È possibile caricare facilmente i computer, assegnare agli stessi configurazioni dichiarative e visualizzare report che mostrano la conformità di ogni computer con lo stato desiderato specificato. 

- [Onboarding di computer per la gestione tramite Configurazione stato di Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementare il monitoraggio automatico della configurazione per le risorse di Azure7.9: Implement automated configuration monitoring for Azure resources

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 7.9 | 5.5 | Customer |

Usare il Centro sicurezza di Azure per eseguire analisi di base per le risorse di Azure.Use Azure Security Center to perform baseline scans for your Azure Resources.  Usare inoltre Criteri di Azure per avvisare e controllare le configurazioni delle risorse di Azure.Additionally, use Azure Policy to alert and audit Azure resource configurations.

- [Come correggere i consigli nel Centro sicurezza di AzureHow to remediate recommendations in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementare il monitoraggio automatico della configurazione per i sistemi operativi

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 7.10 | 5.5 | Customer |

Usare il Centro sicurezza di Azure per eseguire le analisi di base per le impostazioni del sistema operativo e Docker per i contenitori.

- [Informazioni sulle raccomandazioni per i contenitori nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-container-recommendations)

## <a name="711-manage-azure-secrets-securely"></a>7.11: Gestire i segreti di Azure in modo sicuro

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 7.11 | 13.1 | Customer |

Usare l'identità del servizio gestito insieme a Archiviazione delle chiavi di Azure per semplificare e proteggere la gestione dei segreti per le applicazioni cloud.

- [Come integrare con le identità gestite di AzureHow to integrate with Azure Managed Identities](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

- [Come creare un insieme di credenziali delle chiaviHow to create a Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [Come fornire l'autenticazione dell'insieme di credenziali delle chiavi con un'identità gestitaHow to provide Key Vault authentication with a managed identity](https://docs.microsoft.com/azure/key-vault/managed-identity)

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gestire le identità in modo sicuro e automatico

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 7.12 | 4.1 | Customer |

Usare le identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure AD. Managed Identities consente di eseguire l'autenticazione a qualsiasi servizio che supporta l'autenticazione di Azure AD, incluso Key Vault, senza credenziali nel codice.

- [Come configurare le identità gestiteHow to configure Managed Identities](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminare l'esposizione di credenziali indesiderate

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 7.13 | 18.1, 18.7 | Customer |

Implementare Credential Scanner per identificare le credenziali all'interno del codice. Credential Scanner will also encourage moving discovered credentials to more secure locations such as Azure Key Vault. 

- [Come configurare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>Passaggi successivi

- Vedere il prossimo controllo di sicurezza: [protezione da malware](security-control-malware-defense.md)