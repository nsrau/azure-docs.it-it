---
title: Controllo di sicurezza di Azure-configurazione sicura
description: Configurazione sicura del controllo di sicurezza
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 08458777d8a8c5c74ac20a63245135ffaf0a5c04
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564114"
---
# <a name="security-control-secure-configuration"></a>Controllo di sicurezza: configurazione sicura

Consente di stabilire, implementare e gestire attivamente (rilevare, segnalare, correggere) la configurazione di sicurezza delle risorse di Azure per impedire agli utenti malintenzionati di sfruttare le impostazioni e i servizi vulnerabili.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: definire configurazioni sicure per tutte le risorse di Azure

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 7.1 | 5.1 | Customer |

Usare i criteri di Azure o il Centro sicurezza di Azure per gestire le configurazioni di sicurezza per tutte le risorse di Azure.

Come configurare e gestire i criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="72-establish-secure-operating-system-configurations"></a>7,2: stabilire configurazioni del sistema operativo sicure

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 7.2 | 5.1 | Customer |

Usare la raccomandazione del Centro sicurezza di Azure &quot;correggere le vulnerabilità nelle configurazioni di sicurezza nelle macchine virtuali&quot; per mantenere le configurazioni di sicurezza in tutte le risorse di calcolo.

Come monitorare le raccomandazioni del Centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-recommendations

Come correggere le raccomandazioni del Centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: mantenere sicure le configurazioni delle risorse di Azure

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 7.3 | 5,2 | Customer |

Usare criteri di Azure [deny] e [Distribuisci se non esistono] per applicare impostazioni sicure tra le risorse di Azure.

Come configurare e gestire i criteri di Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Informazioni sugli effetti dei criteri di Azure:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

## <a name="74-maintain-secure-operating-system-configurations"></a>7,4: mantenere sicure le configurazioni del sistema operativo

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 7.4 | 5,2 | Condivisione |

Le immagini del sistema operativo di base vengono gestite e gestite da Microsoft.

Tuttavia, è possibile applicare le impostazioni di sicurezza richieste dall'organizzazione usando i modelli di Azure Resource Manager e/o la configurazione dello stato desiderato.

Come creare una macchina virtuale di Azure da un modello di Azure Resource Manager:

https://docs.microsoft.com/azure/virtual-machines/windows/ps-template

Comprendere la configurazione dello stato desiderato per le macchine virtuali di Azure:

https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-overview

## <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: archiviare in modo sicuro la configurazione delle risorse di Azure

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 7.5 | 5.3 | Customer |

Se si usano definizioni di criteri di Azure personalizzate, usare Azure DevOps o Azure Repos per archiviare e gestire il codice in modo sicuro.

Come archiviare il codice in Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentazione Azure Repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

## <a name="76-securely-store-custom-operating-system-images"></a>7,6: archiviare in modo sicuro immagini del sistema operativo personalizzate

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 7.6 | 5.3 | Customer |

Se si utilizzano immagini personalizzate, utilizzare il controllo degli accessi in base al ruolo per garantire che solo gli utenti autorizzati possano accedere alle Per le immagini contenitore, archiviarle in Azure Container Registry e sfruttare il controllo degli accessi in base al ruolo per assicurarsi che solo gli utenti autorizzati possano accedere alle immagini.

Comprendere il RBAC in Azure:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Comprendere RBAC per Container Registry:

https://docs.microsoft.com/azure/container-registry/container-registry-roles

Come configurare RBAC in Azure:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

## <a name="77-deploy-system-configuration-management-tools"></a>7,7: distribuire gli strumenti di gestione della configurazione di sistema

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 7.7 | 5.4 | Customer |

Usare i criteri di Azure per inviare avvisi, controllare e applicare configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni dei criteri.

Come configurare e gestire i criteri di Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 7.8 | 5.4 | Customer |

Usare le estensioni di calcolo di Azure, ad esempio PowerShell DSC (Desired state Configuration) per Windows Compute o Linux chef Extension per Linux.

Come installare le estensioni delle macchine virtuali in Azure:

https://docs.microsoft.com/azure/virtual-machines/extensions/overview#how-can-i-install-an-extension

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementare il monitoraggio automatizzato della configurazione per i servizi di Azure

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 7.9 | 5.5 | Customer |

Usare il Centro sicurezza di Azure per eseguire analisi di base per le risorse di Azure

Come correggere le raccomandazioni nel centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 7.1 | 5.5 | Customer |

Usare il Centro sicurezza di Azure per eseguire analisi di base per le impostazioni del sistema operativo e Docker per i contenitori.

Informazioni sulle raccomandazioni del contenitore del Centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-container-recommendations

## <a name="711-manage-azure-secrets-securely"></a>7,11: gestire i segreti di Azure in modo sicuro 

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 7.11 | 13.1 | Customer |

Usare identità del servizio gestita insieme a Azure Key Vault per semplificare e proteggere la gestione dei segreti per le applicazioni cloud.

Come eseguire l'integrazione con le identità gestite di Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Come creare un Key Vault: https://docs.microsoft.com/azure/key-vault/quick-create-portal

Come fornire Key Vault autenticazione con un'identità gestita: https://docs.microsoft.com/azure/key-vault/managed-identity

## <a name="712-manage-identities-securely-and-automatically"></a>7,12: gestire le identità in modo sicuro e automatico

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 7.12 | 4.1 | Customer |

Usare identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure AD. Le identità gestite consentono di eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure AD, incluso Key Vault, senza credenziali nel codice.

Come configurare le identità gestite: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

## <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminare l'esposizione delle credenziali non intenzionali

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 7,13 | 13.3 | Customer |

Implementare Credential scanner per identificare le credenziali all'interno del codice. Credential scanner incoraggerà inoltre lo stato di trasferimento delle credenziali individuate a posizioni più sicure, ad esempio Azure Key Vault. 

Come configurare Credential scanner: https://secdevtools.azurewebsites.net/helpcredscan.html

## <a name="next-steps"></a>Passaggi successivi

Vedere il controllo di sicurezza successivo: [malware Defense](security-control-malware-defense.md)
