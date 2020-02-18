---
title: Crittografia dei dati per la risoluzione dei problemi del database di Azure per MySQL
description: Informazioni su come risolvere i problemi relativi alla crittografia dei dati per il database di Azure per MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 4b517a463ec949d804798787ad4b35b53145a4a8
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371553"
---
# <a name="troubleshooting-data-encryption-with-customer-managed-keys-in-azure-database-for-mysql"></a>Risoluzione dei problemi di crittografia dei dati con chiavi gestite dal cliente nel database di Azure per MySQL
Questo articolo descrive come identificare e risolvere i problemi comuni e gli errori che si verificano in un database di Azure per MySQL configurato con la crittografia dei dati tramite la chiave gestita dal cliente.

## <a name="introduction"></a>Introduzione
Quando la crittografia dei dati è configurata in modo da usare una chiave gestita dal cliente in Azure Key Vault, è necessario l'accesso continuo a questa chiave affinché il server resti disponibile. Se il server perde l'accesso alla chiave gestita dal cliente in Azure Key Vault, il server inizierà a negare tutte le connessioni con il messaggio di errore appropriato e a impostare lo stato su ***inaccessibile*** nel portale di Azure.

Se un database di Azure non accessibile per il server MySQL non è più necessario, è possibile eliminarlo immediatamente per arrestare i costi. Tutte le altre azioni nel server non sono consentite fino a quando non viene ripristinato l'accesso all'insieme di credenziali delle chiavi di Azure e il server è nuovamente disponibile. Non è possibile modificare l'opzione di crittografia dei dati da' Yes ' (gestita dal cliente) a' No ' (gestita dal servizio) in un server non accessibile quando un server è crittografato con Customer-Managed. È necessario rivalidare manualmente la chiave per rendere disponibile il server. Questa operazione è necessaria per proteggere i dati da accessi non autorizzati mentre le autorizzazioni per la chiave gestita dal cliente sono state revocate.

## <a name="common-errors-causing-server-to-become-inaccessible"></a>Errori comuni che causano l'inaccessibilità del server

La maggior parte dei problemi che si verificano quando si usa la crittografia dei dati con Azure Key Vault sono causati da uno dei seguenti errori di configurazione:

L'insieme di credenziali delle chiavi non è disponibile o non esiste

* L'insieme di credenziali delle chiavi è stato eliminato per errore.
* Un errore di rete intermittente rende non disponibile l'insieme di credenziali delle chiavi.

Non sono disponibili le autorizzazioni per accedere all'insieme di credenziali delle chiavi o la chiave non esiste

* La chiave è stata accidentalmente eliminata o disabilitata oppure è scaduta.
* L'identità gestita dall'istanza del database di Azure per MySQL è stata eliminata accidentalmente.
* Le autorizzazioni concesse all'identità gestita del database di Azure per il server MySQL per le chiavi non sono sufficienti (non includono Get, wrap e unwrap).
* Sono state revocate le autorizzazioni per l'identità gestita del database di Azure per l'istanza del server MySQL.

## <a name="identify-and-resolve-common-errors"></a>Identificare e risolvere gli errori comuni
### <a name="errors-on-the-key-vault"></a>Errori nell'insieme di credenziali delle chiavi

#### <a name="disabled-key-vault"></a>Key Vault disabilitato
* AzureKeyVaultKeyDisabledMessage
* **Spiegazione** : non è stato possibile completare l'operazione nel server perché la chiave Azure Key Vault è disabilitata.

#### <a name="missing-key-vault-permissions"></a>Autorizzazioni Key Vault mancanti
* AzureKeyVaultMissingPermissionsMessage
* Il server non dispone delle autorizzazioni Get, wrap e unwrap necessarie per le autorizzazioni Azure Key Vault. Concedere le autorizzazioni mancanti all'entità servizio con ID.

### <a name="mitigation"></a>Strategia di riduzione del rischio
* Verificare che la chiave gestita dal cliente sia presente nel Key Vault:
* Identificare l'insieme di credenziali delle chiavi, quindi passare all'insieme di credenziali delle chiavi nel portale di Azure.
* Assicurarsi che la chiave identificata dall'URI della chiave sia presente.


## <a name="next-steps"></a>Passaggi successivi
[Configurare la crittografia dei dati con una chiave gestita dal cliente per il database di Azure per MySQL usando il portale di Azure](howto-data-encryption-portal.md).