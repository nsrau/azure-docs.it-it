---
title: Controllo di sicurezza di Azure-configurazione sicura
description: Configurazione sicura del controllo di sicurezza di Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e3d43239dabe75bc2b25319945c2c6b08d726d2a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82193106"
---
# <a name="security-control-secure-configuration"></a>Controllo di sicurezza: configurazione sicura

Consente di stabilire, implementare e gestire attivamente (rilevare, segnalare, correggere) la configurazione di sicurezza delle risorse di Azure per impedire agli utenti malintenzionati di sfruttare le impostazioni e i servizi vulnerabili.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 7.1 | 5.1 | Customer |

Usare gli alias di criteri di Azure per creare criteri personalizzati per controllare o applicare la configurazione delle risorse di Azure. È anche possibile usare le definizioni di criteri di Azure predefinite.

Inoltre, Azure Resource Manager è in grado di esportare il modello in JavaScript Object Notation (JSON), che deve essere esaminato per garantire che le configurazioni soddisfino o superino i requisiti di sicurezza per l'organizzazione.

È anche possibile usare le raccomandazioni del Centro sicurezza di Azure come linea di base di configurazione sicura per le risorse di Azure.

- [Come visualizzare gli alias dei criteri di Azure disponibili](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Esercitazione: Creare e gestire i criteri per applicare la conformità](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Esportazione di una singola e più risorse in un modello in portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [Raccomandazioni sulla sicurezza: una guida di riferimento](https://docs.microsoft.com/azure/security-center/recommendations-reference)

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 7.2 | 5.1 | Customer |

Usare le raccomandazioni del Centro sicurezza di Azure per gestire le configurazioni di sicurezza in tutte le risorse di calcolo.  Inoltre, è possibile usare immagini del sistema operativo personalizzate o la configurazione dello stato di automazione di Azure per stabilire la configurazione di sicurezza del sistema operativo richiesto dall'organizzazione.

- [Come monitorare le raccomandazioni del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

- [Raccomandazioni sulla sicurezza: una guida di riferimento](https://docs.microsoft.com/azure/security-center/recommendations-reference)

- [Panoramica della configurazione dello stato di automazione di Azure](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Caricare un disco rigido virtuale e usarlo per creare nuove macchine virtuali Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

- [Creare una macchina virtuale Linux da un disco personalizzato tramite l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 7.3 | 5,2 | Customer |

Usare criteri di Azure [deny] e [Distribuisci se non esistono] per applicare impostazioni sicure tra le risorse di Azure.  Inoltre, è possibile usare i modelli di Azure Resource Manager per gestire la configurazione di sicurezza delle risorse di Azure richieste dall'organizzazione. 

- [Informazioni sugli effetti di Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [Creare e gestire i criteri per applicare la conformità](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Panoramica sui modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 7.4 | 5,2 | Condiviso |

Seguire le raccomandazioni del Centro sicurezza di Azure per l'esecuzione di valutazioni delle vulnerabilità nelle risorse di calcolo di Azure.  Inoltre, è possibile usare modelli di Azure Resource Manager, immagini del sistema operativo personalizzate o la configurazione dello stato di automazione di Azure per mantenere la configurazione di sicurezza del sistema operativo richiesto dall'organizzazione.   I modelli di macchina virtuale Microsoft combinati con la configurazione dello stato desiderato di automazione di Azure possono contribuire alla riunione e alla gestione dei requisiti di sicurezza. 

Si noti inoltre che le immagini di macchine virtuali di Azure Marketplace pubblicate da Microsoft sono gestite e gestite da Microsoft. 

- [Come implementare le raccomandazioni per la valutazione della vulnerabilità del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

- [Come creare una macchina virtuale di Azure da un modello di Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

- [Panoramica della configurazione dello stato di automazione di Azure](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Creare una macchina virtuale di Windows nel portale di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

- [Informazioni su come scaricare il modello di macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

- [Script di esempio per caricare un disco rigido virtuale in Azure e creare una nuova macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 7.5 | 5.3 | Customer |

USA Azure DevOps per archiviare e gestire in modo sicuro il codice, ad esempio criteri personalizzati di Azure, Azure Resource Manager modelli e gli script di configurazione dello stato desiderato. Per accedere alle risorse gestite in Azure DevOps, è possibile concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza incorporati o gruppi definiti in Azure Active Directory (Azure AD) se integrati con Azure DevOps oppure Active Directory se integrati con TFS.

- [Come archiviare il codice in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Informazioni sulle autorizzazioni e sui gruppi in Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: archiviare in modo sicuro immagini personalizzate del sistema operativo

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 7.6 | 5.3 | Customer |

Se si usano immagini personalizzate, usare il controllo degli accessi in base al ruolo (RBAC) per garantire che solo gli utenti autorizzati possano accedere alle immagini. Usando una raccolta di immagini condivise è possibile condividere le immagini con utenti diversi, entità servizio o gruppi di Active Directory all'interno dell'organizzazione.  Per le immagini contenitore, archiviarle in Azure Container Registry e sfruttare il controllo degli accessi in base al ruolo per assicurarsi che solo gli utenti autorizzati possano accedere alle immagini.  

- [Informazioni sul RBAC in Azure](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

- [Comprendere il RBAC per Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

- [Come configurare RBAC in Azure](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

- [Panoramica sulla raccolta di immagini condivise](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 7.7 | 5.4 | Customer |

Definire e implementare configurazioni di sicurezza standard per le risorse di Azure usando i criteri di Azure. Usare gli alias di criteri di Azure per creare criteri personalizzati per controllare o applicare la configurazione di rete delle risorse di Azure. È anche possibile usare le definizioni di criteri predefinite correlate alle risorse specifiche.  Inoltre, è possibile usare automazione di Azure per distribuire le modifiche di configurazione.

- [Come configurare e gestire Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Come usare gli alias](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: distribuire gli strumenti di gestione della configurazione per i sistemi operativi

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 7.8 | 5.4 | Customer |

La configurazione dello stato di automazione di Azure è un servizio di gestione della configurazione per i nodi DSC (Desired state Configuration) in qualsiasi Data Center nel cloud o in locale. È possibile caricare facilmente i computer, assegnare agli stessi configurazioni dichiarative e visualizzare report che mostrano la conformità di ogni computer con lo stato desiderato specificato. 

- [Onboarding di computer per la gestione tramite Configurazione stato di Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 7.9 | 5.5 | Customer |

Usare il Centro sicurezza di Azure per eseguire analisi di base per le risorse di Azure.  Usare inoltre i criteri di Azure per inviare avvisi e controllare le configurazioni delle risorse di Azure.

- [Come correggere le raccomandazioni nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 7.10 | 5.5 | Customer |

Usare il Centro sicurezza di Azure per eseguire analisi di base per le impostazioni del sistema operativo e Docker per i contenitori.

- [Informazioni sulle raccomandazioni per i contenitori nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-container-recommendations)

## <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 7.11 | 13.1 | Customer |

Usare identità del servizio gestita insieme a Azure Key Vault per semplificare e proteggere la gestione dei segreti per le applicazioni cloud.

- [Come eseguire l'integrazione con le identità gestite di Azure](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

- [Come creare una Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [Come fornire l'autenticazione Key Vault con un'identità gestita](https://docs.microsoft.com/azure/key-vault/managed-identity)

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 7.12 | 4.1 | Customer |

Usare identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure AD. Le identità gestite consentono di eseguire l'autenticazione per qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire credenziali nel codice.

- [Come configurare le identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 7,13 | 18,1, 18,7 | Customer |

Implementare Credential scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault. 

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>Passaggi successivi

- Vedere il controllo di sicurezza successivo: [malware Defense](security-control-malware-defense.md)