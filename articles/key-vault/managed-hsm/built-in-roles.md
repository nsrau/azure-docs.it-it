---
title: Ruoli predefiniti del controllo degli accessi in base al ruolo locale per il modulo di protezione hardware gestito - Azure Key Vault | Microsoft Docs
description: Panoramica dei ruoli predefiniti per il modulo di protezione hardware gestito che è possibile assegnare a utenti, entità servizio, gruppi e identità gestite
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 6b39c1d6fe2e84055e7c94dde076514d04a24f36
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90992443"
---
# <a name="managed-hsm-local-rbac-built-in-roles"></a>Ruoli predefiniti del controllo degli accessi in base al ruolo locale per il modulo di protezione hardware gestito

Il controllo degli accessi in base al ruolo locale prevede diversi ruoli predefiniti che è possibile assegnare a utenti, entità servizio, gruppi e identità gestite. Per consentire a un'entità di sicurezza di eseguire un'operazione, è necessario assegnare a tale entità un ruolo che conceda l'autorizzazione per eseguire l'operazione. Tutti questi ruoli e queste operazioni consentono solo di gestire l'autorizzazione per le operazioni relative al piano dati. Per gestire le autorizzazioni del piano di controllo per la risorsa del modulo di protezione hardware gestito (ad esempio, creare un nuovo modulo di protezione hardware gestito oppure aggiornarne, spostarne, eliminarne uno esistente), è necessario usare il [controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md).

## <a name="built-in-roles"></a>Ruoli predefiniti

|Nome ruolo|Descrizione|ID|
|---|---|---|
|Managed HSM Administrator| Concede accesso completo a tutte le azioni dati.|a290e904-7015-4bba-90c8-60543313cdb4|
|Managed HSM Crypto Officer| Concede accesso completo a tutte le operazioni di crittografia e gestione delle chiavi|515eb02d-2335-4d2d-92f2-b1cbdf9c3778|
|Managed HSM Crypto User|Concede accesso completo per la creazione e l'uso di chiavi per le operazioni di crittografia. Non può eliminare definitivamente le chiavi.|21dbd100-6940-42c2-9190-5d6cb909625b|
|Managed HSM Policy Administrator| Concede l'autorizzazione per creare ed eliminare assegnazioni di ruolo|4bd23610-cdcf-4971-bdee-bdc562cc28e4|
|Managed HSM Crypto Auditor|Concede l'autorizzazione di lettura per leggere le chiavi ma non usarle|2c18b078-7c48-4d3a-af88-5a3a1b3f82b3|
|Managed HSM Crypto Service Encryption| Concede l'autorizzazione per usare una chiave per la crittografia del servizio. |33413926-3206-4cdd-b39a-83574fe37a17|
|Managed HSM Backup| Concede l'autorizzazione per eseguire un backup di una singola chiave o dell'intero modulo di protezione hardware. |7b127d3c-77bd-4e3e-bbe0-dbb8971fa7f8|

## <a name="permitted-operations"></a>Operazioni consentite
> [!NOTE]  
> - Una 'X' indica che il ruolo è autorizzato a eseguire l'azione dati. Una cella vuota indica che il ruolo non è autorizzato a eseguire l'azione dati.
> - Tutti i nomi delle azioni dati sono contraddistinti dal prefisso 'Microsoft.KeyVault/managedHsm', che, per brevità, viene omesso nelle tabelle seguenti.
> - Tutti i nomi di ruolo sono contraddistinti dal prefisso "Managed HSM", che, per brevità, viene omesso nella tabella seguente.

|Azione dati | Amministratore | Crypto Officer | Crypto User | Policy Administrator | Crypto Service Encryption | Backup | Crypto Auditor|
|---|---|---|---|---|---|---|---|
|**Gestione del dominio di sicurezza**|
/securitydomain/download/action|<center>X</center>||||||
/securitydomain/upload/action|<center>X</center>||||||
/securitydomain/upload/read|<center>X</center>||||||
/securitydomain/transferkey/read|<center>X</center>||||||
|**Gestione delle chiavi**|
|/keys/read/action|<center>X</center>|<center>X</center>|<center>X</center>||<center>X</center>||<center>X</center>|
|/keys/write/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/create|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/delete|<center>X</center>|<center>X</center>|||||
|/keys/deletedKeys/read/action|<center>X</center>|<center>X</center>|||||
|/keys/deletedKeys/recover/action|<center>X</center>|<center>X</center>|||||
|/keys/deletedKeys/delete|<center>X</center>|<center>X</center>|||||<center>X</center>|
|/keys/backup/action|<center>X</center>|<center>X</center>|<center>X</center>|||<center>X</center>|
|/keys/restore/action|<center>X</center>|<center>X</center>|||||
|/keys/export/action|<center>X</center>|<center>X</center>|||||
|/keys/import/action|<center>X</center>|<center>X</center>|||||
|**Operazioni di crittografia delle chiavi**|
|/keys/encrypt/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/decrypt/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/wrap/action|<center>X</center>|<center>X</center>|<center>X</center>||<center>X</center>||
|/keys/unwrap/action|<center>X</center>|<center>X</center>|<center>X</center>||<center>X</center>||
|/keys/sign/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/verify/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|**Gestione dei ruoli**|
|/roleAssignments/delete/action|<center>X</center>|||<center>X</center>|||
|/roleAssignments/read/action|<center>X</center>|||<center>X</center>|||
|/roleAssignments/write/action|<center>X</center>|||<center>X</center>|||
|/roleDefinitions/read/action|<center>X</center>|||<center>X</center>|||
|**Gestione di backup/ripristino**|
|/backup/start/action|<center>X</center>|||||<center>X</center>|
|/backup/status/action|<center>X</center>|||||<center>X</center>|
|/restore/start/action|<center>X</center>||||||
|/restore/status/action|<center>X</center>||||||
||||||||

## <a name="next-steps"></a>Passaggi successivi

- Panoramica del [controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md).
- Esercitazione sulla [gestione dei ruoli del modulo di protezione hardware gestito](role-management.md)
