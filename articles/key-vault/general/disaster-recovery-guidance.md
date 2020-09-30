---
title: Operazioni da eseguire in caso di interruzione di un servizio di Azure con impatto su Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Informazioni sulle operazioni da eseguire in caso di interruzione di un servizio di Azure con impatto su Azure Key Vault.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: sudbalas
ms.openlocfilehash: 27d8d4de308fe7cf6e6f36dd33f33bb73c495073
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90983223"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Disponibilità e ridondanza dell'insieme di credenziali delle chiavi di Azure

L'insieme di credenziali delle chiavi di Azure dispone di più livelli di ridondanza, per garantire che le chiavi e i segreti rimangano disponibili per l'applicazione anche quando si verificano errori di singoli componenti del servizio.

> [!NOTE]
> Le informazioni della guida si applicano agli insiemi di credenziali. I pool del modulo di protezione hardware gestito usano un diverso modello di disponibilità elevata e ripristino di emergenza. Per altre informazioni, vedere la [guida al ripristino di emergenza del modulo di protezione hardware gestito](../managed-hsm/disaster-recovery-guide.md) for more information.

I contenuti dell'insieme di credenziali delle chiavi vengono replicati all'interno dell'area e in un'area secondaria distante almeno 250 chilometri, ma all'interno della stessa area geografica per mantenere una durabilità elevata delle chiavi e dei segreti. Per informazioni dettagliate su coppie di aree specifiche, vedere il documento [Aree abbinate di Azure](../../best-practices-availability-paired-regions.md).


Se si verificano errori di singoli componenti del servizio dell'insieme di credenziali delle chiavi, per gestire la richiesta subentrano componenti alternativi all'interno dell'area in modo che non si verifichi alcuna riduzione delle prestazioni delle funzionalità. Non è necessario intraprendere alcuna azione per avviare questo processo, si verifica automaticamente ed è trasparente per l'utente.

Nella rara eventualità che l'intera area di Azure risulti non disponibile, le richieste eseguite all'insieme di credenziali delle chiavi di tale area vengono indirizzate automaticamente a un'area secondaria (*failover*). Quando l'area primaria diventa di nuovo disponibile, le richieste vengono reindirizzate a tale area (*failback*). Anche in questo caso non è richiesta alcuna azione, poiché questa operazione viene eseguita in modo automatico.

Grazie a questa progettazione a disponibilità elevata, Azure Key Vault non richiede alcun tempo di inattività per le attività di manutenzione.

Esistono alcune limitazioni che è necessario tenere presenti:

* Nel caso del failover di un'area, il failover del servizio può richiedere alcuni minuti. Le richieste effettuate durante questo periodo prima del failover potrebbero non riuscire.
* Dopo il failover, l'insieme di credenziali delle chiavi è in modalità di sola lettura. Le richieste supportate in questa modalità sono le seguenti:
  * List certificates
  * Get certificates
  * List secrets
  * Get secrets
  * List keys
  * Get (properties of) keys
  * Encrypt
  * Decrypt
  * Wrap
  * Unwrap
  * Verifica
  * Sign
  * Backup

* Durante il failover, non sarà possibile apportare modifiche alle proprietà dell'insieme di credenziali delle chiavi. Non sarà possibile modificare le impostazioni e le configurazioni del firewall o dei criteri di accesso.

* Dopo il failback di un failover, tutti i tipi di richiesta, ad esempio le richieste di lettura *e* scrittura, risultano disponibili.
