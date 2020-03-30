---
title: Consigli sulla sicurezza per l'insieme di credenziali delle chiavi di AzureSecurity recommendations for Azure Key Vault
description: Consigli sulla sicurezza per Archiviazione delle chiavi di Azure.Security recommendations for Azure Key Vault. L'implementazione di queste linee guida ti aiuterà a rispettare i tuoi obblighi di sicurezza come descritto nel nostro modello di responsabilità condivisa
services: key-vault
author: barclayn
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: article
ms.date: 09/30/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: 9fd41ae9b61a9c74fd5d99dd71199cfde06f5cb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192371"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Consigli sulla sicurezza per l'insieme di credenziali delle chiavi di AzureSecurity recommendations for Azure Key Vault

Questo articolo contiene consigli sulla sicurezza per L'insieme di credenziali delle chiavi di Azure.This article contains security recommendations for Azure Key Vault. L'implementazione di queste raccomandazioni ti aiuterà a rispettare i tuoi obblighi di sicurezza come descritto nel nostro modello di responsabilità condivisa. Per ulteriori informazioni sulle operazioni eseguite da Microsoft per adempiere alle responsabilità dei provider di servizi, leggere [Responsabilità condivise per](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)il cloud computing .

Alcuni dei consigli inclusi in questo articolo possono essere monitorati automaticamente dal Centro sicurezza di Azure.Some of the recommendations included in this article can be automatically monitored by Azure Security Center. Azure Security Center is the first line of defense in protecting your resources in Azure. Analizza periodicamente lo stato di sicurezza delle risorse di Azure per identificare potenziali vulnerabilità della sicurezza. Fornisce quindi consigli su come affrontarli.

- Per altre informazioni sui consigli del Centro sicurezza di Azure, vedere Consigli sulla sicurezza in Centro sicurezza di Azure.For more information on Azure Security Center [recommendations,](../security-center/security-center-recommendations.md)see Security recommendations in Azure Security Center .
- Per informazioni sul Centro sicurezza di Azure, vedere Il Centro sicurezza di [Azure?](../security-center/security-center-intro.md)

## <a name="data-protection"></a>Protezione dei dati

| Recommendation | Commenti | Centro sicurezza |
|-|----|--|
|Abilita eliminazione temporanea | [L'eliminazione temporanea](key-vault-ovw-soft-delete.md) consente di recuperare i vault e gli oggetti del vault eliminati |  - |
| Limitare l'accesso ai dati del vault  | Seguire il principio dei privilegi minimi e limitare i membri dell'organizzazione che hanno accesso ai dati del vault |  - |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Recommendation | Commenti | Centro sicurezza |
|-|----|--|
| Limitare il numero di utenti con accesso come collaboratore | Se un utente dispone delle autorizzazioni di collaboratore per un piano di gestione dell'insieme di credenziali delle chiavi, può concedere a se stesso l'accesso al piano dati impostando un criterio di accesso dell'insieme di credenziali delle chiavi. È consigliabile controllare rigorosamente chi dispone dell'accesso del ruolo Collaboratore agli insiemi di credenziali delle chiavi. Assicurarsi che solo coloro che hanno bisogno di accedere alle persone autorizzate possano accedere e gestire i vault. È possibile leggere [Accesso sicuro a un insieme](key-vault-secure-your-key-vault.md) di credenziali delle chiavi | - |

## <a name="monitoring"></a>Monitoraggio

| Recommendation | Commenti | Centro sicurezza |
|-|----|--|
 I log di diagnostica in Key Vault devono essere abilitati | Abilitare i log e conservarli per un periodo massimo di un anno. Ciò consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. | [Sì](../security-center/security-center-identity-access.md) |
| Limitare gli utenti che possono accedere ai log dell'insieme di credenziali delle chiavi di AzureRestrict who can access your Azure Key vault logs | [I registri dell'insieme](key-vault-logging.md) di credenziali delle chiavi salvano informazioni sulle attività eseguite nel vault, ad esempio la creazione o l'eliminazione di insiemi di credenziali, chiavi, segreti e possono essere utilizzati durante un'indagine |  - |

## <a name="networking"></a>Rete

| Recommendation | Commenti | Centro sicurezza |
|-|----|--|
|Limitare l'esposizione alla rete | L'accesso alla rete deve essere limitato alle reti virtuali utilizzate dalle soluzioni che richiedono l'accesso al vault.Network access should be limited to the virtual networks used by solutions requiring vault access. Esaminare le informazioni sugli endpoint del servizio di rete virtuale per l'insieme di credenziali delle chiavi di AzureReview information on [Virtual network service endpoints for Azure Key Vault](key-vault-overview-vnet-service-endpoints.md) | - |

## <a name="next-steps"></a>Passaggi successivi

Rivolgersi al provider dell'applicazione per verificare se sono presenti ulteriori requisiti di sicurezza. Per ulteriori informazioni sullo sviluppo di applicazioni protette, vedere [Documentazione sullo sviluppo protetto](../security/fundamentals/abstract-develop-secure-apps.md).
