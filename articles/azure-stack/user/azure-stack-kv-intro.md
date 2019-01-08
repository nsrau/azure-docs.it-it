---
title: Introduzione di Azure Stack Key Vault | Microsoft Docs
description: Informazioni su come Azure Stack di Key Vault gestisce le chiavi e segreti
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 70f1684a-3fbb-4cd1-bf29-9f9882e98fe9
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/05/2019
ms.author: sethm
ms.openlocfilehash: 40a748895038dbadea7d12d79268f89796a4b428
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2019
ms.locfileid: "54061502"
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>Introduzione a Key Vault in Azure Stack

## <a name="prerequisites"></a>Prerequisiti

* È necessario sottoscrivere un'offerta che include il servizio Azure Key Vault.  
* [PowerShell è configurato per l'uso con Azure Stack](azure-stack-powershell-configure-user.md).

## <a name="key-vault-basics"></a>Nozioni di base di Key Vault

Key Vault in Azure Stack aiuta a proteggere le chiavi crittografiche e segreti che le applicazioni e servizi cloud usano. Tramite Azure Key Vault, è possibile crittografare chiavi e segreti, come:

* Chiavi di autenticazione
* Chiavi dell'account di archiviazione
* Chiavi di crittografia dei dati
* file con estensione pfx
* Password

L'insieme di credenziali chiave semplifica il processo di gestione delle chiavi e consente di mantenere il controllo delle chiavi che accedono ai dati e li crittografano. Gli sviluppatori possono creare chiavi per lo sviluppo e il test in pochi minuti e quindi eseguirne facilmente la migrazione alle chiavi di produzione. Gli amministratori della sicurezza possono concedere e revocare le autorizzazioni per chiavi, in base alle esigenze.

Chiunque abbia una sottoscrizione di Azure Stack puoi creare e usare insiemi di credenziali delle chiavi. Anche se l'insieme di credenziali chiave vantaggio per sviluppatori e amministratori della sicurezza, l'operatore che gestisce altri servizi di Azure Stack per un'organizzazione può implementare e gestirla. L'operatore può accedere con una sottoscrizione di Azure Stack, Azure Stack, ad esempio, creare un insieme di credenziali per l'organizzazione in cui archiviare le chiavi e quindi essere responsabile di queste attività operative:

* Creare o importare una chiave o un segreto.
* Revocare o eliminare una chiave o segreto.
* Autorizzare utenti e applicazioni di accedere a key vault, in modo che gestione o l'uso delle chiavi e segreti.
* Configura utilizzo delle chiave (ad esempio, firmare o crittografare).

L'operatore può quindi fornire agli sviluppatori con Uniform Resource Identifier (URI) da chiamare dalle rispettive applicazioni. Gli operatori è possono assistere gli amministratori della sicurezza con le informazioni di registrazione dell'utilizzo di chiave.

Gli sviluppatori possono gestire le chiavi anche direttamente, usando le API. Per altre informazioni, vedere la Guida per gli sviluppatori di Key Vault.

## <a name="scenarios"></a>Scenari

Gli scenari seguenti spiegano come insieme di credenziali delle chiavi può aiutare a soddisfare le esigenze di sviluppatori e amministratori della sicurezza.

### <a name="developer-for-an-azure-stack-application"></a>Per gli sviluppatori per un'applicazione di Azure Stack

**Problema:** Voglio scrivere un'applicazione per Azure Stack che utilizza chiavi di firma e crittografia. Desidera che dovranno essere esterne all'applicazione, in modo che la soluzione sia adatta a un'applicazione geograficamente distribuita.

**Istruzione:** Le chiavi vengono archiviate in un insieme di credenziali e richiamate da un URI, quando necessario.

### <a name="developer-for-software-as-a-service-saas"></a>Sviluppatore di software come un servizio (SaaS)

**Problema:** Non voglio la responsabilità o potenziale responsabilità per le chiavi e segreti del mio cliente. Voglio clienti a possedere e gestire le proprie chiavi, in modo che potermi concentrare su ciò che so fare meglio, che è fornire le principali funzionalità del software.

**Istruzione:** I clienti possono importare le loro stesse chiavi in Azure Stack e quindi gestirli.

### <a name="chief-security-officer-cso"></a>Sicurezza direttore (CSO)

**Problema:** Voglio assicurarmi che la mia organizzazione abbia il controllo del ciclo di vita delle chiavi e possa monitorare l'utilizzo delle chiavi.

**Istruzione:** L'insieme di credenziali delle chiavi è progettato in modo che Microsoft non possa vedere o estrarre le tue chiavi. Quando un'applicazione deve eseguire le operazioni di crittografia usando le chiavi dei clienti, Key Vault Usa le chiavi per conto dell'applicazione. L'applicazione non vede le chiavi dei clienti. Anche se usiamo più servizi di Azure Stack e le risorse, è possibile gestire le chiavi da un'unica posizione in Azure Stack. L'insieme di credenziali offre un'unica interfaccia, indipendentemente dal fatto quanti insiemi di credenziali disponibili in Azure Stack, in quali aree sono supporto e le applicazioni li utilizzano.

## <a name="next-steps"></a>Passaggi successivi

* [Gestire Key Vault in Azure Stack tramite il portale](azure-stack-kv-manage-portal.md)  
* [Gestire Key Vault in Azure Stack usando PowerShell](azure-stack-kv-manage-powershell.md)

