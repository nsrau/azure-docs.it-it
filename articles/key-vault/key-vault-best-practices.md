---
title: Procedure consigliate per usare Key Vault - Azure Key Vault | Microsoft Docs
description: Questo documento illustra alcune delle procedure consigliate per usare Key Vault
services: key-vault
documentationcenter: ''
author: yvprashanth
manager: barbkess
tags: azure-key-vault
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: prashanthyv
ms.openlocfilehash: 85bd1858bc3f2d505e1e4d0a88e8c77a46ae4447
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2019
ms.locfileid: "57887976"
---
# <a name="best-practices-to-use-key-vault"></a>Le procedure consigliate per usare Key Vault

## <a name="control-access-to-your-vault"></a>Controllare l'accesso all'insieme di credenziali

Azure Key Vault è un servizio cloud che protegge le chiavi di crittografia e i segreti, come certificati, stringhe di connessione e password. Poiché questi dati sono riservati e importanti per l'azienda, è necessario proteggere gli insiemi di credenziali delle chiavi consentendo l'accesso solo ad applicazioni e utenti autorizzati. Ciò [articolo](key-vault-secure-your-key-vault.md) offre una panoramica del modello di accesso di Key Vault. Verranno illustrate l'autenticazione e l'autorizzazione e sarà descritto come proteggere l'accesso agli insiemi di credenziali delle chiavi.

Suggerimenti durante il controllo dell'accesso all'insieme di credenziali sono i seguenti:
1. Bloccare l'accesso alla sottoscrizione, gruppo di risorse e gli insiemi di credenziali chiave (RBAC)
2. Creare criteri di accesso per ogni insieme di credenziali
3. Usare entità di accesso con privilegi minimi per concedere l'accesso
4. Attivare il Firewall e [endpoint del servizio rete virtuale](key-vault-overview-vnet-service-endpoints.md)

## <a name="use-separate-key-vault"></a>Usare l'insieme di credenziali delle chiavi separato

Si consiglia di utilizzare un insieme di credenziali per ogni applicazione per ogni ambiente (sviluppo, pre-produzione e produzione). Ciò consente di non condividere i segreti per gli ambienti e consente inoltre di ridurre la minaccia in caso di violazione.

## <a name="backup-your-vault"></a>L'insieme di credenziali di backup

Assicurarsi che sia possibile normali nuovamente ups delle [insieme di credenziali](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) su update/delete/creazione degli oggetti all'interno di un insieme di credenziali.

## <a name="turn-on-logging"></a>Abilitare la registrazione

[Abilitare la registrazione](key-vault-logging.md) dell'insieme di credenziali. Consente inoltre di impostare avvisi.

## <a name="turn-on-recovery-options"></a>Attivare le opzioni di ripristino

1. Accendere [eliminazione temporanea](key-vault-ovw-soft-delete.md).
2. Attivare la protezione dall'eliminazione se si desidera proteggersi da forzare l'eliminazione del segreto / anche dopo l'eliminazione temporanea è attivata l'insieme di credenziali.
