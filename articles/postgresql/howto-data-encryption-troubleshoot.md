---
title: Risolvere i problemi relativi alla crittografia dei dati - Database di Azure per PostgreSQL - Server singoloTroubleshoot data encryption - Azure Database for PostgreSQL - Single Server
description: Informazioni su come risolvere i problemi relativi alla crittografia dei dati nel database di Azure per PostgreSQL - Server singolo
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 2902ff17ac14a48f1a11259339c2ab1bc4595980
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299261"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-postgresql---single-server"></a>Risolvere i problemi di crittografia dei dati nel database di Azure per PostgreSQL - Server singoloTroubleshoot data encryption in Azure Database for PostgreSQL - Single Server

Questo articolo consente di identificare e risolvere i problemi comuni che possono verificarsi nella distribuzione a server singolo del database di Azure per PostgreSQL quando viene configurato con la crittografia dei dati usando una chiave gestita dal cliente.

## <a name="introduction"></a>Introduzione

Quando si configura la crittografia dei dati per usare una chiave gestita dal cliente in Archiviazione delle chiavi di Azure, il server richiede l'accesso continuo alla chiave. Se il server perde l'accesso alla chiave gestita dal cliente nell'insieme di chiavi di Azure, negherà tutte le connessioni, restituirà il messaggio di errore appropriato e ne modificherà lo stato in ***Inaccessibile*** nel portale di Azure.If the server loses access to the customer-managed key in Azure Key Vault, it will deny all connections, return the appropriate error message, and change its state to Inaccessible in the Azure portal.

Se non è più necessario un database di Azure inaccessibile per il server PostgreSQL, è possibile eliminarlo per interrompere i costi. Non sono consentite altre azioni sul server fino a quando non è stato ripristinato l'accesso all'insieme di credenziali delle chiavi e il server è disponibile. Non è inoltre possibile modificare l'opzione `Yes`di crittografia dei `No` dati da (gestito dal cliente) a (gestito dal servizio) in un server inaccessibile quando viene crittografato con una chiave gestita dal cliente. Sarà necessario riconvalidare manualmente la chiave prima che il server sia nuovamente accessibile. Questa azione è necessaria per proteggere i dati da accessi non autorizzati mentre le autorizzazioni per la chiave gestita dal cliente vengono revocate.

## <a name="common-errors-causing-server-to-become-inaccessible"></a>Errori comuni che causano l'inaccessibilità del server

Gli errori di configurazione seguenti causano la maggior parte dei problemi di crittografia dei dati che usano le chiavi dell'insieme di credenziali delle chiavi di Azure:The following misconfigurations cause most issues with data encryption that use Azure Key Vault keys:

- L'insieme di credenziali delle chiavi non è disponibile o non esiste:
  - L'insieme di credenziali delle chiavi è stato eliminato per errore.
  - Un errore di rete intermittente rende non disponibile l'insieme di credenziali delle chiavi.

- Non si dispone delle autorizzazioni per accedere all'insieme di credenziali delle chiavi o la chiave non esiste:
  - La chiave è scaduta o è stata accidentalmente eliminata o disabilitata.
- L'identità gestita dell'istanza del database di Azure per PostgreSQL è stata eliminata accidentalmente.
  - L'identità gestita del database di Azure per l'istanza PostgreSQL non dispone di autorizzazioni sufficienti per le chiavi. Ad esempio, le autorizzazioni non includono Get, Wrap e Unwrap.
  - Le autorizzazioni dell'identità gestita per l'istanza del database di Azure per PostgreSQL sono state revocate o eliminate.

## <a name="identify-and-resolve-common-errors"></a>Identificare e risolvere gli errori comuni

### <a name="errors-on-the-key-vault"></a>Errori nell'insieme di credenziali delle chiavi

#### <a name="disabled-key-vault"></a>Insieme di credenziali delle chiavi disabilitati

- `AzureKeyVaultKeyDisabledMessage`
- **Spiegazione:** Impossibile completare l'operazione nel server perché la chiave dell'insieme di credenziali delle chiavi di Azure è disabilitata.

#### <a name="missing-key-vault-permissions"></a>Autorizzazioni dell'insieme di credenziali delle chiavi mancanti

- `AzureKeyVaultMissingPermissionsMessage`
- **Spiegazione:** il server non dispone delle autorizzazioni Get, Wrap e Unwrap necessarie per Azure Key Vault. Concedere tutte le autorizzazioni mancanti all'entità servizio con ID.

### <a name="mitigation"></a>Strategia di riduzione del rischio

- Verificare che la chiave gestita dal cliente sia presente nell'insieme di credenziali delle chiavi.
- Identificare l'insieme di credenziali delle chiavi, quindi passare all'insieme di credenziali delle chiavi nel portale di Azure.
- Assicurarsi che l'URI della chiave identifichi una chiave presente.

## <a name="next-steps"></a>Passaggi successivi

[Usare il portale di Azure per configurare la crittografia dei dati con una chiave gestita dal cliente nel database di Azure per PostgreSQLUse the Azure portal to set up data encryption with a customer-managed key on Azure Database for PostgreSQL](howto-data-encryption-portal.md)
