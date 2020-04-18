---
title: Procedure consigliate per l'uso dell'insieme di credenziali delle chiavi - Insieme di credenziali delle chiavi di Azure Documenti Microsoft
description: Questo documento illustra alcune delle procedure consigliate per l'utilizzo dell'insieme di credenziali delle chiavi
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 923fb90f7f0e8eefec650515ed2a3b9b75d2ae77
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617924"
---
# <a name="best-practices-to-use-key-vault"></a>Procedure consigliate per l'utilizzo dell'insieme di credenziali delle chiaviBest practices to use Key Vault

## <a name="control-access-to-your-vault"></a>Controllare l'accesso al vault

Azure Key Vault è un servizio cloud che protegge le chiavi di crittografia e i segreti, come certificati, stringhe di connessione e password. Poiché questi dati sono riservati e importanti per l'azienda, è necessario proteggere gli insiemi di credenziali delle chiavi consentendo l'accesso solo ad applicazioni e utenti autorizzati. In [questo articolo](secure-your-key-vault.md)viene fornita una panoramica del modello di accesso dell'insieme di credenziali delle chiavi. Verranno illustrate l'autenticazione e l'autorizzazione e sarà descritto come proteggere l'accesso agli insiemi di credenziali delle chiavi.

I suggerimenti durante il controllo dell'accesso al vault sono i seguenti:
1. Bloccare l'accesso alla sottoscrizione, al gruppo di risorse e agli insiemi di credenziali delle chiavi
2. Creare criteri di accesso per ogni insieme di credenzialiCreate Access policies for every vault
3. Usare l'entità di accesso con privilegi minimi per concedere l'accessoUse least privilege access principal to grant access
4. Attivare gli endpoint del servizio Firewall e [VNET](overview-vnet-service-endpoints.md))

## <a name="use-separate-key-vault"></a>Usa insieme di credenziali delle chiavi separato

Si consiglia di utilizzare un vault per ogni applicazione per ogni ambiente (Sviluppo, Pre-Produzione e Produzione). Ciò consente di non condividere segreti tra ambienti e riduce anche la minaccia in caso di violazione.

## <a name="backup"></a>Backup

Assicurarsi di eseguire backup regolari del [vault](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) durante l'aggiornamento/eliminazione/creazione di oggetti all'interno di un Vault.

## <a name="turn-on-logging"></a>Attivare la registrazione

[Attivare la registrazione](logging.md)) per il Vault. Impostare anche avvisi.

## <a name="turn-on-recovery-options"></a>Attivare le opzioni di ripristino

1. Attivare [Eliminazione temporanea](overview-soft-delete.md)).
2. Attivare la protezione di eliminazione se si desidera evitare l'eliminazione forzata del segreto / vault anche dopo l'eliminazione temporanea è attivata.
