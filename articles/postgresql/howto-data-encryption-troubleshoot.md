---
title: Risolvere i problemi di crittografia dei dati-database di Azure per PostgreSQL-server singolo
description: Informazioni su come risolvere i problemi relativi alla crittografia dei dati nel database di Azure per PostgreSQL-server singolo
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 02/13/2020
ms.openlocfilehash: ee0a1ebe483dd4719fd1a84fec37906329116eba
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86117900"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-postgresql---single-server"></a>Risolvere i problemi di crittografia dei dati in database di Azure per PostgreSQL-server singolo

Questo articolo illustra come identificare e risolvere i problemi comuni che possono verificarsi nella distribuzione a server singolo di database di Azure per PostgreSQL se configurata con la crittografia dei dati tramite una chiave gestita dal cliente.

## <a name="introduction"></a>Introduzione

Quando si configura la crittografia dei dati per l'uso di una chiave gestita dal cliente in Azure Key Vault, il server richiede l'accesso continuo alla chiave. Se il server perde l'accesso alla chiave gestita dal cliente in Azure Key Vault, rifiuterà tutte le connessioni, restituirà il messaggio di errore appropriato e lo stato sarà ***inaccessibile*** nella portale di Azure.

Se non è più necessario un database di Azure per il server PostgreSQL inaccessibile, è possibile eliminarlo per arrestare i costi. Non sono consentite altre azioni nel server finché non viene ripristinato l'accesso all'insieme di credenziali delle chiavi e il server non è disponibile. Non è inoltre possibile modificare l'opzione di crittografia dei dati da `Yes` (gestito dal cliente) a `No` (gestito dal servizio) in un server non accessibile quando viene crittografato con una chiave gestita dal cliente. È necessario riconvalidare manualmente la chiave prima che il server sia nuovamente accessibile. Questa azione è necessaria per proteggere i dati da accessi non autorizzati, mentre le autorizzazioni per la chiave gestita dal cliente vengono revocate.

## <a name="common-errors-causing-server-to-become-inaccessible"></a>Errori comuni che causano l'inaccessibilità del server

Gli errori di configurazione seguenti provocano la maggior parte dei problemi con la crittografia dei dati che usano chiavi di Azure Key Vault:

- L'insieme di credenziali delle chiavi non è disponibile o non esiste:
  - L'insieme di credenziali delle chiavi è stato eliminato per errore.
  - Un errore di rete intermittente rende non disponibile l'insieme di credenziali delle chiavi.

- Non si è autorizzati ad accedere all'insieme di credenziali delle chiavi o la chiave non esiste:
  - La chiave è scaduta o è stata accidentalmente eliminata o disabilitata.
- L'identità gestita dell'istanza del database di Azure per PostgreSQL è stata eliminata accidentalmente.
  - L'identità gestita dell'istanza del database di Azure per PostgreSQL non ha autorizzazioni di chiave sufficienti. Ad esempio, le autorizzazioni non includono Get, wrap e unwrap.
  - Le autorizzazioni di identità gestite per l'istanza del database di Azure per PostgreSQL sono state revocate o eliminate.

## <a name="identify-and-resolve-common-errors"></a>Identificare e risolvere gli errori comuni

### <a name="errors-on-the-key-vault"></a>Errori nell'insieme di credenziali delle chiavi

#### <a name="disabled-key-vault"></a>Key Vault disabilitato

- `AzureKeyVaultKeyDisabledMessage`
- **Spiegazione**: non è stato possibile completare l'operazione nel server perché la chiave Azure Key Vault è disabilitata.

#### <a name="missing-key-vault-permissions"></a>Autorizzazioni Key Vault mancanti

- `AzureKeyVaultMissingPermissionsMessage`
- **Spiegazione**: il server non dispone delle autorizzazioni Get, wrap e unwrap necessarie per Azure Key Vault. Concedere le autorizzazioni mancanti all'entità servizio con ID.

### <a name="mitigation"></a>Strategia di riduzione del rischio

- Verificare che la chiave gestita dal cliente sia presente nell'insieme di credenziali delle chiavi.
- Identificare l'insieme di credenziali delle chiavi, quindi passare all'insieme di credenziali delle chiavi nel portale di Azure.
- Verificare che l'URI della chiave identifichi una chiave presente.

## <a name="next-steps"></a>Passaggi successivi

[Usare il portale di Azure per configurare la crittografia dei dati con una chiave gestita dal cliente nel database di Azure per PostgreSQL](howto-data-encryption-portal.md)
