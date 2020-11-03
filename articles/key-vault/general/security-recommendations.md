---
title: Raccomandazioni sulla sicurezza per Azure Key Vault
description: Raccomandazioni sulla sicurezza per Azure Key Vault. Implementando queste linee guida sarà possibile adeguarsi gli obblighi di sicurezza descritti nel modello di responsabilità condivisa
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: article
ms.date: 09/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: f96df14fb754578b58fb67ac116ac79cff3ffdcb
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289841"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Raccomandazioni sulla sicurezza per Azure Key Vault

Questo articolo contiene raccomandazioni sulla sicurezza per Azure Key Vault. Implementando queste raccomandazioni sarà possibile adeguarsi gli obblighi di sicurezza descritti nel modello di responsabilità condivisa. Per altre informazioni sulle iniziative intraprese da Microsoft per assolvere alle responsabilità dei provider di servizi, vedere [Responsabilità condivise per il cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Alcune raccomandazioni presenti in questo articolo possono essere monitorate automaticamente dal Centro sicurezza di Azure. Il Centro sicurezza di Azure è la prima linea di difesa per la protezione delle risorse di Azure: analizza periodicamente lo stato di sicurezza delle risorse di Azure per identificare potenziali vulnerabilità di sicurezza e fornisce raccomandazioni per affrontarle in modo efficace.

- Per altre informazioni sulle raccomandazioni del Centro di sicurezza di Azure, vedere [Raccomandazioni di sicurezza nel Centro sicurezza di Azure](../../security-center/security-center-recommendations.md).
- Per informazioni sul Centro sicurezza di Azure, vedere [Che cos'è il Centro sicurezza di Azure?](../../security-center/security-center-introduction.md)

## <a name="data-protection"></a>Protezione dei dati

| Recommendation | Commenti | Centro sicurezza |
|-|----|--|
|Abilitare l'eliminazione temporanea | L'[eliminazione temporanea](soft-delete-overview.md) consente il recupero di insiemi di credenziali e di oggetti di insiemi di credenziali eliminati |  - |
| Limitare l'accesso ai dati dell'insieme di credenziali  | Seguire il principio del privilegio minimo e limitare i membri dell'organizzazione che hanno accesso ai dati dell'insieme di credenziali |  - |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Recommendation | Commenti | Centro sicurezza |
|-|----|--|
| Limitare il numero di utenti con accesso di tipo Collaboratore | Se un utente dispone di autorizzazioni di tipo Collaboratore per un piano di gestione dell'insieme di credenziali delle chiavi, può concedere a se stesso l'accesso al piano dati impostando i criteri di accesso per Key Vault. È consigliabile controllare attentamente chi ha accesso come collaboratore agli insiemi di credenziali delle chiavi e assicurarsi che solo le persone autorizzate possano accedere e gestire gli insiemi di credenziali. Vedere [Proteggere l'accesso a un insieme di credenziali delle chiavi](secure-your-key-vault.md). | - |

## <a name="monitoring"></a>Monitoraggio

| Recommendation | Commenti | Centro sicurezza |
|-|----|--|
 I log di diagnostica in Key Vault devono essere abilitati | Abilitare i log e conservarli per un periodo massimo di un anno. Ciò consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. | [Sì](../../security-center/security-center-identity-access.md) |
| Limitare gli utenti che possono accedere ai log degli insiemi di credenziali di Azure Key Vault | I [log di Key Vault](logging.md) salvano le informazioni sulle attività eseguite nell'insieme di credenziali, come la creazione o l'eliminazione di insiemi di credenziali, chiavi e segreti, e possono essere usati durante un'indagine |  - |

## <a name="networking"></a>Rete

| Recommendation | Commenti | Centro sicurezza |
|-|----|--|
|Limitare l'esposizione della rete | L'accesso alla rete deve essere limitato alle reti virtuali usate dalle soluzioni che richiedono l'accesso all'insieme di credenziali. Rivedere le informazioni contenute nell'articolo [Endpoint servizio di rete virtuale per Azure Key Vault](overview-vnet-service-endpoints.md) | - |

## <a name="next-steps"></a>Passaggi successivi

Verificare con il provider di applicazioni l'eventuale necessità di soddisfare requisiti di sicurezza aggiuntivi. Per altre informazioni sullo sviluppo di applicazioni sicure, vedere [Documentazione sullo sviluppo sicuro](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/).