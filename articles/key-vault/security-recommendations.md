---
title: Raccomandazioni sulla sicurezza per Azure Key Vault
description: Raccomandazioni sulla sicurezza per Azure Key Vault. L'implementazione di queste linee guida consente di soddisfare gli obblighi di sicurezza come descritto nel modello di responsabilità condivisa
services: key-vault
author: barclayn
manager: rkarlin
ms.service: key-vault
ms.topic: article
ms.date: 09/30/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: 09ccfd6e344f2776cfedfc56976f2a5c34f79d5c
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428174"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Raccomandazioni sulla sicurezza per Azure Key Vault

Questo articolo contiene raccomandazioni sulla sicurezza per Azure Key Vault. L'implementazione di questi consigli consentirà di soddisfare gli obblighi di sicurezza come descritto nel modello di responsabilità condivisa. Per ulteriori informazioni sulle attività svolte da Microsoft per soddisfare le responsabilità del provider di servizi, vedere [responsabilità condivise per cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225237/1/Shared%20Responsibilities%20for%20Cloud%20Computing%20(2017-04-03).pdf).

Alcuni consigli inclusi in questo articolo possono essere monitorati automaticamente dal centro sicurezza di Azure. Il Centro sicurezza di Azure è la prima linea di difesa per la protezione delle risorse in Azure. Analizza periodicamente lo stato di sicurezza delle risorse di Azure per identificare le potenziali vulnerabilità di sicurezza. Fornisce quindi suggerimenti su come risolverli.

- Per altre informazioni sulle raccomandazioni del Centro sicurezza di Azure, vedere [raccomandazioni per la sicurezza nel centro sicurezza di Azure](../security-center/security-center-recommendations.md).
- Per informazioni sul centro sicurezza di Azure, vedere [che cos'è il Centro sicurezza di Azure?](../security-center/security-center-intro.md)

## <a name="data-protection"></a>Protezione dei dati

| Recommendation | Commenti | Centro sicurezza |
|-|----|--|
|Abilita eliminazione temporanea | L' [eliminazione](key-vault-ovw-soft-delete.md) temporanea consente di ripristinare gli insiemi di credenziali eliminati e gli oggetti dell'insieme di credenziali |  - |
| Limitare l'accesso ai dati dell'insieme di credenziali  | Seguire il principio dei privilegi minimi e limitare i membri dell'organizzazione che hanno accesso ai dati dell'insieme di credenziali |  - |

## <a name="identity-and-access-management"></a>Gestione degli accessi e delle identità

| Recommendation | Commenti | Centro sicurezza |
|-|----|--|
| Limitare il numero di utenti con accesso collaboratore | Se un utente ha le autorizzazioni di collaboratore per un piano di gestione dell'insieme di credenziali delle chiavi, l'utente può concedere l'accesso al piano dati impostando un criterio di accesso Key Vault. È necessario controllare strettamente chi ha accesso al ruolo Collaboratore agli insiemi di credenziali delle chiavi. Assicurarsi che solo gli utenti che hanno la necessità di accedere alle persone autorizzate possano accedere e gestire gli insiemi di credenziali. È possibile leggere [l'accesso sicuro a un insieme di](key-vault-secure-your-key-vault.md) credenziali delle chiavi | - |

## <a name="monitoring"></a>Monitorare

| Recommendation | Commenti | Centro sicurezza |
|-|----|--|
 I log di diagnostica in Key Vault devono essere abilitati | Abilitare i log e conservarli per un periodo massimo di un anno. Questo consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. | [Sì](../security-center/security-center-identity-access.md) |
| Limitare gli utenti che possono accedere ai log di Azure Key Vault | I [log di Key Vault](key-vault-logging.md) salvano le informazioni sulle attività eseguite nell'insieme di credenziali, ad esempio la creazione o l'eliminazione di insiemi di credenziali, chiavi, segreti e possono essere usate durante un'indagine |  - |

## <a name="networking"></a>Rete

| Recommendation | Commenti | Centro sicurezza |
|-|----|--|
|Limita l'esposizione alla rete | L'accesso alla rete deve essere limitato alle reti virtuali usate dalle soluzioni che richiedono l'accesso all'insieme di credenziali. Esaminare le informazioni sugli [endpoint del servizio rete virtuale per Azure Key Vault](key-vault-overview-vnet-service-endpoints.md) | - |

## <a name="next-steps"></a>Passaggi successivi

Rivolgersi al provider di applicazioni per verificare se sono presenti requisiti di sicurezza aggiuntivi. Per ulteriori informazioni sullo sviluppo di applicazioni sicure, vedere la [documentazione sullo sviluppo protetto](../security/fundamentals/abstract-develop-secure-apps.md).
