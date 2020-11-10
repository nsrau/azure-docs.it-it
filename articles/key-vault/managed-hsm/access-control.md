---
title: Controllo di accesso HSM gestito di Azure
description: Gestire le autorizzazioni di accesso per le chiavi e HSM gestiti di Azure. Descrive il modello di autenticazione e autorizzazione per il modulo di protezione hardware gestito e come proteggere i HSM.
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 816941fe0ec3a81c41da56acedcedf2de7febe74
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445235"
---
# <a name="managed-hsm-access-control"></a>Controllo di accesso per il modulo di protezione hardware gestito

> [!NOTE]
> Il **provider di risorse** Key Vault supporta due tipi di risorse: insiemi di credenziali e **HSM gestiti**. Il controllo di accesso descritto in questo articolo si applica solo a **HSM gestiti**. Per altre informazioni sul controllo di accesso per il modulo di protezione hardware gestito, vedere [fornire l'accesso alle chiavi Key Vault, ai certificati e ai segreti con il controllo degli accessi in base al ruolo di Azure](../general/rbac-guide.md).

Il modulo di protezione hardware gestito di Azure Key Vault è un servizio cloud che consente di proteggere le chiavi di crittografia. Poiché questi dati sono riservati e importanti per l'azienda, è necessario proteggere i moduli di protezione hardware gestiti consentendo l'accesso solo ad applicazioni e utenti autorizzati. Questo articolo offre una panoramica del modello di controllo di accesso del modulo di protezione hardware gestito. Verranno illustrate l'autenticazione e l'autorizzazione e sarà descritto come proteggere l'accesso ai moduli di protezione hardware gestiti.

## <a name="access-control-model"></a>Modello di controllo di accesso

L'accesso a un modulo di protezione hardware gestito viene controllato tramite due interfacce, ovvero il **piano di gestione** e il **piano dati**. Il piano di gestione consente di gestire il modulo di protezione hardware. Le operazioni in questo piano includono la creazione e l'eliminazione di HSM gestiti e il recupero delle proprietà HSM gestite. Il piano dati è il punto in cui si lavora con i dati archiviati in un modulo di protezione hardware gestito, ovvero le chiavi di crittografia supportate da HSM. È possibile aggiungere, eliminare, modificare e usare chiavi per eseguire operazioni di crittografia, gestire le assegnazioni di ruolo per controllare l'accesso alle chiavi, creare un backup completo del modulo di protezione hardware, ripristinare il backup completo e gestire il dominio di sicurezza dall'interfaccia del piano dati.

Per accedere a un modulo di protezione hardware gestito in entrambi i piani, tutti i chiamanti devono disporre di autenticazione e autorizzazione appropriati. L'autenticazione stabilisce l'identità del chiamante. L'autorizzazione determina le operazioni che il chiamante può eseguire. Un chiamante può essere una delle entità di [sicurezza](../../role-based-access-control/overview.md#security-principal) definite in Azure Active Directory utente, gruppo, entità servizio o identità gestita.

Entrambi i piani utilizzano Azure Active Directory per l'autenticazione. Per l'autorizzazione, usano sistemi diversi come indicato di seguito
- Il piano di gestione usa il controllo degli accessi in base al ruolo di Azure--RBAC di Azure, un sistema di autorizzazione creato in Azure Azure Resource Manager 
- Il piano dati usa un controllo degli accessi in base al ruolo del modulo di protezione hardware gestito (RBAC locale del modulo HSM gestito), un sistema di autorizzazione implementato e applicato a livello di HSM gestito.

Quando viene creato un modulo HSM gestito, il richiedente fornisce anche un elenco di amministratori del piano dati (sono supportate tutte le [entità di sicurezza](../../role-based-access-control/overview.md#security-principal) ). Solo questi amministratori sono in grado di accedere al piano dati HSM gestito per eseguire operazioni chiave e gestire le assegnazioni di ruolo del piano dati (RBAC locale del modulo di protezione hardware gestito).

Il modello di autorizzazione per entrambi i piani usa la stessa sintassi, ma vengono applicati a livelli diversi e le assegnazioni di ruolo usano ambiti diversi. Il piano di gestione controllo degli accessi in base al ruolo di Azure viene applicato da Azure Resource Manager mentre il controllo del modulo di protezione hardware gestito dal piano dati viene applicato dal modulo di protezione hardware

> [!IMPORTANT]
> La concessione dell'accesso al piano di gestione di un'entità di sicurezza a un modulo di protezione hardware gestito non concede loro l'accesso al piano dati per le chiavi di accesso o le assegnazioni di ruolo del piano dati gestito dall'HSM locale. Questo isolamento è progettato per impedire l'espansione accidentale dei privilegi che influiscono sull'accesso alle chiavi archiviate nel modulo di protezione hardware gestito.

Ad esempio, un amministratore della sottoscrizione (poiché dispone dell'autorizzazione "collaboratore" per tutte le risorse nella sottoscrizione) può eliminare un HSM gestito nella propria sottoscrizione, ma se non dispone di accesso al piano dati concesso in modo specifico tramite il controllo degli accessi in base al ruolo locale di HSM, non può accedere alle chiavi o gestire l'assegnazione di ruolo nel modulo di protezione hardware gestito per concedere a se

## <a name="azure-active-directory-authentication"></a>Autenticazione di Azure Active Directory

Quando si crea un modulo di protezione hardware gestito in una sottoscrizione di Azure, questo viene associato automaticamente al tenant Azure Active Directory della sottoscrizione. Tutti i chiamanti in entrambi i piani devono essere registrati in questo tenant ed eseguire l'autenticazione per accedere al modulo di protezione hardware gestito.

L'applicazione esegue l'autenticazione con Azure Active Directory prima di chiamare uno dei due piani. L'applicazione può usare qualsiasi [metodo di autenticazione supportato](../../active-directory/develop/authentication-vs-authorization.md) in base al tipo di applicazione. L'applicazione acquisisce un token per una risorsa nel piano per ottenere l'accesso. La risorsa è un endpoint nel piano dati o di gestione, in base all'ambiente di Azure. L'applicazione usa il token e invia una richiesta API REST all'endpoint HSM gestito. Per altre informazioni, vedere l'[intero flusso di autenticazione](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

L'uso di un singolo meccanismo di autenticazione per entrambi i piani presenta diversi vantaggi:

- Le organizzazioni possono controllare l'accesso in modo centralizzato a tutti i HSM gestiti nella propria organizzazione.
- Se un utente lascia, perde immediatamente l'accesso a tutti i HSM gestiti nell'organizzazione.
- Le organizzazioni possono personalizzare l'autenticazione usando le opzioni disponibili in Azure Active Directory, ad esempio per abilitare l'autenticazione a più fattori per una maggiore sicurezza.

## <a name="resource-endpoints"></a>Endpoint delle risorse

Le entità di sicurezza accedono ai piani attraverso gli endpoint. I controlli di accesso per i due piani funzionano in maniera indipendente. Per concedere a un'applicazione l'accesso per usare le chiavi in un modulo di protezione hardware gestito, è possibile concedere l'accesso al piano dati usando il controllo RBAC locale gestito. Per concedere a un utente l'accesso a una risorsa HSM gestita per creare, leggere, eliminare, spostare il HSM gestito e modificare altre proprietà e tag si usa il controllo degli accessi in base al ruolo di Azure.

La tabella seguente illustra gli endpoint per il piano dati e di gestione.

| Piano di&nbsp;accesso | Endpoint di accesso | Operazioni | Meccanismo di controllo di accesso |
| --- | --- | --- | --- |
| Piano di gestione | **Globale:**<br> management.azure.com:443<br> | Creazione, lettura, aggiornamento, eliminazione e spostamento di HSM gestiti<br>Imposta tag HSM gestiti | Controllo degli accessi in base al ruolo di Azure |
| Piano dati | **Globale:**<br> &lt;HSM-Name &gt; . Vault.Azure.NET:443<br> | **Chiavi** : decrittografare, crittografare,<br> annullamento del wrapping, wrapping, verifica, firma, recupero, elenco, aggiornamento, creazione, importazione, eliminazione, backup, ripristino, eliminazione<br/><br/> **Gestione dei ruoli del piano dati (RBAC locale del modulo di protezione hardware gestito)**_: elencare le definizioni di ruolo, assegnare ruoli, <br/> <br/> eliminare le assegnazioni di ruolo, definire ruoli personalizzati_ * backup/ripristino **: <br/> <br/> backup, ripristino, controllo dello stato backup/ripristino operazioni** sicurezza dominio * *: scaricare e caricare il dominio di sicurezza | RBAC locale HSM gestito |
|||||
## <a name="management-plane-and-azure-rbac"></a>Piano di gestione e RBAC di Azure

Nel piano di gestione usare il controllo degli accessi in base al ruolo di Azure per autorizzare le operazioni che un chiamante può eseguire. Nel modello di controllo degli accessi in base al ruolo di Azure ogni sottoscrizione di Azure ha un'istanza di Azure Active Directory. È possibile concedere l'accesso a utenti, gruppi e applicazioni da questa directory. Viene concesso l'accesso per gestire le risorse della sottoscrizione di Azure che usano il modello di distribuzione Azure Resource Manager. Per concedere l'accesso, usare il [portale di Azure](https://portal.azure.com/), l'[interfaccia della riga di comando di Azure](/cli/azure/install-classic-cli), [Azure PowerShell](/powershell/azureps-cmdlets-docs) o le [API REST di Azure Resource Manager](/rest/api/authorization/roleassignments).

Si crea un insieme di credenziali delle chiavi in un gruppo di risorse e si gestisce l'accesso usando Azure Active Directory. È possibile consentire a utenti o gruppi di gestire gli insiemi di credenziali delle chiavi in un gruppo di risorse. Si concede l'accesso a un livello di ambito specifico assegnando i ruoli di Azure appropriati. Per concedere l'accesso a un utente in modo che possa gestire insiemi di credenziali delle chiavi, assegnare all'utente un ruolo `key vault Contributor` predefinito in un ambito specifico. I livelli di ambito seguenti possono essere assegnati a un ruolo di Azure:

- **Gruppo di gestione** : un ruolo di Azure assegnato a livello di sottoscrizione si applica a tutte le sottoscrizioni del gruppo di gestione.
- **Sottoscrizione** : un ruolo di Azure assegnato a livello di sottoscrizione si applica a tutti i gruppi di risorse e le risorse all'interno della sottoscrizione.
- **Gruppo di risorse** : un ruolo di Azure assegnato a livello di gruppo di risorse si applica a tutte le risorse nel gruppo di risorse.
- **Risorsa specifica** : un ruolo di Azure assegnato a una risorsa specifica si applica a tale risorsa. In questo caso, la risorsa è un insieme di credenziali delle chiavi specifico.

Ci sono diversi ruoli predefiniti. Se un ruolo predefinito non soddisfa le specifiche esigenze, è possibile definire un ruolo personalizzato. Per altre informazioni, vedere controllo degli accessi [in base al ruolo di Azure: ruoli predefiniti](../../role-based-access-control/built-in-roles.md).

## <a name="data-plane-and-managed-hsm-local-rbac"></a>RBAC locale del piano dati e del modulo di protezione hardware gestito

Si concede a un'entità di sicurezza l'accesso per eseguire operazioni chiave specifiche assegnando un ruolo. Per ogni assegnazione di ruolo è necessario specificare un ruolo e un ambito a cui si applica tale assegnazione. Per il modulo di protezione hardware gestito locale sono disponibili due ambiti.

- **"/" o "/Keys"** : ambito del livello HSM. Le entità di sicurezza a cui è stato assegnato un ruolo in questo ambito possono eseguire le operazioni definite nel ruolo per tutti gli oggetti (chiavi) nel modulo di protezione hardware gestito.
- **"/Keys/ &lt; key-name &gt; "** : ambito a livello di chiave. Le entità di sicurezza a cui è stato assegnato un ruolo in questo ambito possono eseguire le operazioni definite in questo ruolo solo per tutte le versioni della chiave specificata.

## <a name="next-steps"></a>Passaggi successivi

- Per un'esercitazione introduttiva per gli amministratori, vedere [Che cos'è il modulo di protezione hardware gestito?](overview.md).
- Per un'esercitazione sulla gestione dei ruoli, vedere [HSM gestito locale RBAC](role-management.md)
- Per altre informazioni sulla registrazione dell'utilizzo per il modulo di protezione hardware gestito, vedere [Registrazione del modulo di protezione hardware gestito](logging.md).