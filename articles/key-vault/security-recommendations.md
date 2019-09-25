---
title: Raccomandazioni sulla sicurezza per Azure Key Vault
description: Raccomandazioni sulla sicurezza per Azure Key Vault. L'implementazione di queste linee guida consente di soddisfare gli obblighi di sicurezza come descritto nel modello di responsabilità condivisa
services: key-vault
author: barclayn
manager: rkarlin
ms.service: key-vault
ms.topic: article
ms.date: 09/23/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: 90965618b1d2cb126d56453d46a5bebc85319c0f
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219638"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Raccomandazioni sulla sicurezza per Azure Key Vault

Questo articolo contiene raccomandazioni sulla sicurezza per Azure Key Vault. L'implementazione di questi consigli consentirà di soddisfare gli obblighi di sicurezza come descritto nel modello di responsabilità condivisa. Per ulteriori informazioni sulle attività svolte da Microsoft per soddisfare le responsabilità del provider di servizi, vedere [responsabilità condivise per cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf).

Alcuni consigli inclusi in questo articolo possono essere monitorati automaticamente dal centro sicurezza di Azure. Il Centro sicurezza di Azure è la prima linea di difesa per la protezione delle risorse in Azure. Analizza periodicamente lo stato di sicurezza delle risorse di Azure per identificare le potenziali vulnerabilità di sicurezza. Fornisce quindi suggerimenti su come risolverli.

- Per altre informazioni sulle raccomandazioni del Centro sicurezza di Azure, vedere [raccomandazioni per la sicurezza nel centro sicurezza di Azure](../security-center/security-center-recommendations.md).
- Per informazioni sul centro sicurezza di Azure, vedere [che cos'è il Centro sicurezza di Azure?](../security-center/security-center-intro.md)

## <a name="recommendations"></a>Consigli

| Category | Indicazione | Commenti | Centro sicurezza |
|-|-|----|--|
| Protezione dei dati |Abilita eliminazione temporanea | L' [eliminazione](key-vault-ovw-soft-delete.md) temporanea consente di ripristinare gli insiemi di credenziali eliminati e gli oggetti dell'insieme di credenziali |  - |
| Protezione dei dati | Limitare l'accesso ai dati dell'insieme di credenziali  | Seguire il principio dei privilegi minimi e limitare i membri dell'organizzazione che hanno accesso ai dati dell'insieme di credenziali |  - |
| Gestione delle identità e dell'accesso | Limitare il numero di utenti con accesso collaboratore | Se un utente ha le autorizzazioni di collaboratore per un piano di gestione dell'insieme di credenziali delle chiavi, l'utente può concedere l'accesso al piano dati impostando un criterio di accesso Key Vault. È necessario controllare strettamente chi ha accesso al ruolo Collaboratore agli insiemi di credenziali delle chiavi. Assicurarsi che solo gli utenti che hanno la necessità di accedere alle persone autorizzate possano accedere e gestire gli insiemi di credenziali. È possibile leggere [l'accesso sicuro a un insieme di](key-vault-secure-your-key-vault.md) credenziali delle chiavi | - |
| Monitoraggio | I log di diagnostica in Key Vault devono essere abilitati | Abilitare i log e conservarli per un periodo massimo di un anno. Ciò consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. | [Sì](../security-center/security-center-identity-access.md) |
| Monitoraggio | Limitare gli utenti che possono accedere ai log di Azure Key Vault | I [log di Key Vault](key-vault-logging.md) salvano le informazioni sulle attività eseguite nell'insieme di credenziali, ad esempio la creazione o l'eliminazione di insiemi di credenziali, chiavi, segreti e possono essere usate durante un'indagine |  - |
| Rete |Limita l'esposizione alla rete | L'accesso alla rete deve essere limitato alle reti virtuali usate dalle soluzioni che richiedono l'accesso all'insieme di credenziali. Esaminare le informazioni sugli [endpoint del servizio rete virtuale per Azure Key Vault](key-vault-overview-vnet-service-endpoints.md) | - |

## <a name="next-steps"></a>Passaggi successivi

Rivolgersi al provider di applicazioni per verificare se sono presenti requisiti di sicurezza aggiuntivi. Per ulteriori informazioni sullo sviluppo di applicazioni sicure, vedere la [documentazione sullo sviluppo protetto](../security/fundamentals/abstract-develop-secure-apps.md).
