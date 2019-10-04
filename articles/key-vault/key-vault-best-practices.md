---
title: Procedure consigliate per l'uso di Key Vault-Azure Key Vault | Microsoft Docs
description: In questo documento vengono illustrate alcune delle procedure consigliate per l'utilizzo di Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 654a9bb772c8a7426a335c98dfeca69515b9ce67
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881625"
---
# <a name="best-practices-to-use-key-vault"></a>Procedure consigliate per l'utilizzo di Key Vault

## <a name="control-access-to-your-vault"></a>Controllare l'accesso all'insieme di credenziali

Azure Key Vault è un servizio cloud che protegge le chiavi di crittografia e i segreti, come certificati, stringhe di connessione e password. Poiché questi dati sono riservati e importanti per l'azienda, è necessario proteggere gli insiemi di credenziali delle chiavi consentendo l'accesso solo ad applicazioni e utenti autorizzati. Questo [articolo](key-vault-secure-your-key-vault.md) fornisce una panoramica del modello di accesso key Vault. Verranno illustrate l'autenticazione e l'autorizzazione e sarà descritto come proteggere l'accesso agli insiemi di credenziali delle chiavi.

I suggerimenti per il controllo dell'accesso all'insieme di credenziali sono i seguenti:
1. Bloccare l'accesso alla sottoscrizione, al gruppo di risorse e agli insiemi di credenziali delle chiavi (RBAC)
2. Creare criteri di accesso per ogni insieme di credenziali
3. Usare l'entità di accesso con privilegi minimi per concedere l'accesso
4. Attivare gli endpoint di [servizio](key-vault-overview-vnet-service-endpoints.md) firewall e VNET

## <a name="use-separate-key-vault"></a>USA Key Vault separate

Si consiglia di usare un insieme di credenziali per ogni applicazione per ambiente (sviluppo, pre-produzione e produzione). Ciò consente di non condividere i segreti tra gli ambienti e di ridurre anche la minaccia in caso di violazione.

## <a name="backup"></a>Backup

Assicurarsi di eseguire backup regolari dell' [insieme di credenziali per l'](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) aggiornamento, l'eliminazione o la creazione di oggetti all'interno di un insieme di credenziali.

## <a name="turn-on-logging"></a>Attivare la registrazione

[Attivare la registrazione](key-vault-logging.md) per l'insieme di credenziali. Configurare anche gli avvisi.

## <a name="turn-on-recovery-options"></a>Attiva opzioni di ripristino

1. Attivare l' [eliminazione](key-vault-ovw-soft-delete.md)temporanea.
2. Attivare l'eliminazione della protezione se si vuole evitare l'eliminazione forzata del segreto/insieme di credenziali anche dopo aver attivato l'eliminazione temporanea.
