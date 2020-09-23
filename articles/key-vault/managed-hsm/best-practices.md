---
title: Procedure consigliate con Azure Key Vault HSM gestito
description: In questo documento vengono illustrate alcune delle procedure consigliate per l'utilizzo di Key Vault
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: ambapat
ms.openlocfilehash: 7a30a7ab6689b602bc9ad4f696a6fe54c80f2151
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90996854"
---
# <a name="best-practices-when-using-managed-hsm"></a>Procedure consigliate per l'uso di HSM gestito

## <a name="control-access-to-your-managed-hsm"></a>Controllare l'accesso al modulo di protezione hardware gestito

Il modulo HSM gestito è un servizio cloud che protegge le chiavi di crittografia. Poiché queste chiavi sono sensibili e cruciali per l'azienda, assicurarsi di proteggere l'accesso alla HSM gestita consentendo solo le applicazioni e gli utenti autorizzati. Questo [articolo](access-control.md) fornisce una panoramica del modello di accesso. Vengono illustrati l'autenticazione e l'autorizzazione e il controllo degli accessi in base al ruolo.
- Creare un [gruppo di sicurezza Azure Active Directory](../../active-directory/fundamentals/active-directory-manage-groups.md) per gli amministratori del modulo di protezione hardware, anziché assegnare il ruolo di amministratore a singoli utenti. In questo modo verrà impedito l'"blocco dell'amministrazione" in caso di eliminazione di un singolo account.
- Bloccare l'accesso a gruppi di gestione, sottoscrizioni, gruppi di risorse e HSM gestiti: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso ai gruppi di gestione, alle sottoscrizioni e ai gruppi
- Creare assegnazioni di ruolo per chiave usando il [RBAC locale del](access-control.md#data-plane-and-managed-hsm-local-rbac) modulo di protezione hardware gestito
- Usare l'entità di accesso con privilegi minimi per assegnare i ruoli

## <a name="choose-regions-that-support-availability-zones"></a>Scegliere le aree che supportano le zone di disponibilità

- Per garantire la massima disponibilità e resilienza della zona, scegliere le aree di Azure in cui sono supportate [zone di disponibilità](../../availability-zones/az-overview.md) . Queste aree vengono visualizzate come "aree consigliate" nel portale di Azure.

## <a name="backup"></a>Backup

- Assicurarsi di eseguire backup regolari del modulo di protezione hardware. I backup possono essere eseguiti a livello di modulo di protezione hardware e per chiavi specifiche. 

## <a name="turn-on-logging"></a>Abilitare la registrazione

- [Attivare la registrazione](logging.md) per il modulo di protezione hardware. Configurare anche gli avvisi.

## <a name="turn-on-recovery-options"></a>Attiva opzioni di ripristino

- L' [eliminazione](../general/soft-delete-overview.md) temporanea è abilitata per impostazione predefinita.
- Attivare l'eliminazione della protezione se si vuole evitare l'eliminazione forzata del modulo HSM anche dopo l'attivazione dell'eliminazione temporanea.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sul backup/ripristino completo di HSM, vedere [backup/ripristino completo](backup-restore.md) .
- Vedere [registrazione HSM gestita](logging.md) per informazioni su come usare monitoraggio di Azure per configurare la registrazione
- Vedere [gestire le chiavi HSM gestite](key-management.md) per la gestione delle chiavi.
- Per gestire le assegnazioni di ruolo, vedere [gestione dei ruoli HSM gestiti](role-management.md) .
