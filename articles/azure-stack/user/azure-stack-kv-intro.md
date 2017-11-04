---
title: Introduzione di Azure insieme di credenziali chiave Stack | Documenti Microsoft
description: Informazioni su come insieme di credenziali chiave di Azure Stack gestisce le chiavi e segreti
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 70f1684a-3fbb-4cd1-bf29-9f9882e98fe9
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/04/2017
ms.author: sngun
ms.openlocfilehash: 621a0cb865d0c050d7271d10bd14076f9f0c6f67
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2017
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>Introduzione alla chiave dell'insieme di credenziali di Azure Stack

## <a name="prerequisites"></a>Prerequisiti 

* È necessario sottoscrivere un'offerta che include il servizio insieme credenziali chiavi Azure.  
* [PowerShell è configurato per l'utilizzo con Azure Stack](azure-stack-powershell-configure-user.md).
 
## <a name="key-vault-basics"></a>Nozioni fondamentali di insieme di credenziali chiave
Insieme di credenziali chiave nello Stack di Azure consente di proteggere le chiavi di crittografia e utilizzano informazioni riservate che le applicazioni e servizi cloud. Tramite l'insieme di credenziali chiave, è possibile crittografare, ad esempio chiavi e segreti:
   * Chiavi di autenticazione 
   * Chiavi dell'account di archiviazione
   * Chiavi di crittografia dati
   * file con estensione pfx
   * Password

L'insieme di credenziali chiave semplifica il processo di gestione delle chiavi e consente di mantenere il controllo delle chiavi che accedono ai dati e li crittografano. Gli sviluppatori possono creare chiavi per lo sviluppo e il test in pochi minuti e quindi eseguirne facilmente la migrazione alle chiavi di produzione. Gli amministratori della sicurezza possono concedere (e revocare) le autorizzazioni per chiavi, in base alle esigenze.

Chiunque con una sottoscrizione di Azure Stack possa creare e utilizzare gli insiemi di credenziali chiave. Anche se l'insieme di credenziali chiave vantaggi agli sviluppatori e amministratori della sicurezza, l'operatore che consente di gestire altri servizi Azure Stack per un'organizzazione può implementare e gestirlo. Lo Stack di Azure operatore è possibile accedere con una sottoscrizione di Azure Stack, ad esempio, creare un insieme di credenziali per l'organizzazione in cui archiviare le chiavi e quindi essere responsabile di queste attività operative:

* Creare o importare una chiave o un segreto.
* Revocare o eliminare una chiave o un segreto.
* Autorizzare gli utenti o applicazioni per l'insieme di credenziali chiave di accesso in modo da poter quindi gestire o usare le chiavi e segreti.
* Configurare l'utilizzo della chiave (ad esempio, firmare o crittografare).

L'operatore può quindi fornire agli sviluppatori con Uniform Resource Identifier (URI) di chiamare dalle rispettive applicazioni. Operatori possono anche fornire gli amministratori della sicurezza con le informazioni di registrazione utilizzo chiave.

Gli sviluppatori possono gestire le chiavi anche direttamente, usando le API. Per ulteriori informazioni, vedere Guida per gli sviluppatori insieme di credenziali chiave.

## <a name="scenarios"></a>Scenari
Gli scenari seguenti indicano come insieme di credenziali chiave consente di soddisfare le esigenze di sviluppatori e amministratori della sicurezza.

### <a name="developer-for-an-azure-stack-application"></a>Sviluppo di un'applicazione Azure Stack
**Problema:** scrivere un'applicazione per lo Stack di Azure che usa chiavi per la firma e crittografia. Voglio queste chiavi è esterno dall'applicazione, in modo che la soluzione è adatta per un'applicazione geograficamente distribuiti.

**Istruzione:** chiavi sono memorizzate in un insieme di credenziali e richiamate da un URI, quando necessario.

### <a name="developer-for-software-as-a-service-saas"></a>Sviluppatori di software come servizio (SaaS)
**Problema:** si desidera che la responsabilità di responsabilità o potenziale per le chiavi e segreti il cliente. Voglio ai clienti di gestione delle chiavi, in modo che è possibile concentrarsi sull'esecuzione di operazioni ottimale, che fornisce le principali funzionalità del software.

**Istruzione:** i clienti possono importare le rispettive chiavi nello Stack di Azure e quindi gestirli. 

### <a name="chief-security-officer-cso"></a>Sicurezza direttore (compagnia)
**Problema:** si desidera assicurarsi che l'organizzazione nel controllo del ciclo di vita chiave che è possibile monitorare l'utilizzo della chiave.

**Istruzione:** insieme di credenziali chiave è progettato in modo da Microsoft non o estrarre le chiavi. Quando un'applicazione deve eseguire operazioni di crittografia tramite chiavi dei clienti, insieme di credenziali chiave vengono utilizzate le chiavi per conto dell'applicazione. L'applicazione non vede le chiavi dei clienti. Anche se si usa più servizi dello Stack di Azure e risorse, è possibile gestire le chiavi da un'unica posizione nello Stack di Azure. L'insieme di credenziali fornisce un'interfaccia singola, indipendentemente dal quanti gli insiemi di credenziali è nello Stack di Azure, le aree sono, supporto e le applicazioni li utilizzano.

## <a name="next-steps"></a>Passaggi successivi

* [Gestire l'insieme di credenziali chiave nello Stack di Azure tramite il portale](azure-stack-kv-manage-portal.md)  
* [Gestire l'insieme di credenziali chiave nello Stack di Azure usando PowerShell](azure-stack-kv-manage-powershell.md)

