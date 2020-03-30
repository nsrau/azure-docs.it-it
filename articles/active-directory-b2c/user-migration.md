---
title: Approcci di migrazione degli utenti
titleSuffix: Azure AD B2C
description: Eseguire la migrazione degli account utente da un altro provider di identità ad Azure AD B2C usando i metodi di importazione in blocco o di migrazione senza problemi.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b3ee069985fd39288a562d3caafc50b12290c060
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332342"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>Eseguire la migrazione degli utenti ad Azure AD B2CMigrate users to Azure AD B2C

La migrazione da un altro provider di identità ad Azure Active Directory B2C (Azure AD B2C) potrebbe anche richiedere la migrazione di account utente esistenti. Qui vengono discussi due metodi di migrazione, *l'importazione in blocco* e la migrazione senza soluzione di *continuità.* Con entrambi gli approcci, è necessario scrivere un'applicazione o uno script che usa [l'API Microsoft Graph](manage-user-accounts-graph-api.md) per creare account utente in Azure AD B2C.

## <a name="bulk-import"></a>Importazione in blocco

Nel flusso di importazione in blocco, l'applicazione di migrazione esegue questi passaggi per ogni account utente:In the bulk import flow, your migration application performs these steps for each user account:

1. Leggere l'account utente dal provider di identità precedente, incluse le credenziali correnti (nome utente e password).
1. Creare un account corrispondente nella directory B2C di Azure AD con le credenziali correnti.

Usare il flusso di importazione bulk in una di queste due situazioni:Use the bulk import flow in either of these two situations:

- Si ha accesso alle credenziali di testo non crittografato dell'utente (nome utente e password).
- Le credenziali sono crittografate, ma è possibile decrittografarle.

Per informazioni sulla creazione di account utente a livello di codice, vedere [Gestire gli account utente di Azure AD B2C con Microsoft Graph.](manage-user-accounts-graph-api.md)

## <a name="seamless-migration"></a>Migrazione senza soluzione di continuità

Utilizzare il flusso di migrazione senza interruzioni se le password in testo non crittografato nel provider di identità precedente non sono accessibili. Ad esempio, quando:

- La password viene archiviata in un formato crittografato unidirezionale, ad esempio con una funzione hash.
- La password viene archiviata dal provider di identità legacy in modo che non sia possibile accedere. Ad esempio, quando il provider di identità convalida le credenziali chiamando un servizio Web.For example, when the identity provider validates credentials by calling a web service.

Il flusso di migrazione senza soluzione di continuità richiede ancora la migrazione in blocco degli account utente, ma quindi usa un [criterio personalizzato](custom-policy-get-started.md) per eseguire una query su [un'API REST](custom-policy-rest-api-intro.md) (creata) per impostare la password di ogni utente al primo accesso.

Il flusso di migrazione senza soluzione di continuità prevede quindi due fasi: *importazione in blocco* e *impostazione delle credenziali*.

### <a name="phase-1-bulk-import"></a>Fase 1: importazione in bloccoPhase 1: Bulk import

1. L'applicazione di migrazione legge gli account utente dal provider di identità precedente.
1. L'applicazione di migrazione crea gli account utente corrispondenti nella directory B2C di Azure AD, ma *non imposta le password.*

### <a name="phase-2-set-credentials"></a>Fase 2: Impostare le credenzialiPhase 2: Set credentials

Al termine della migrazione in blocco degli account, i criteri personalizzati e l'API REST eseguono le operazioni seguenti quando un utente esegue l'accesso:

1. Leggere l'account utente B2C di Azure AD corrispondente all'indirizzo di posta elettronica immesso.
1. Verificare se l'account è contrassegnato per la migrazione valutando un attributo di estensione booleano.
    - Se l'attributo di estensione restituisce `True`, chiamare l'API REST per convalidare la password rispetto al provider di identità legacy.
      - Se l'API REST determina che la password non è corretta, restituire un errore descrittivo all'utente.
      - Se l'API REST determina che la password è corretta, scrivere la password nell'account B2C di Azure AD e modificare l'attributo di estensione booleano in `False`.
    - Se l'attributo `False`di estensione booleano restituisce , continuare il processo di accesso normalmente.

Per visualizzare un'API REST e criteri personalizzati di esempio, vedere l'esempio di [migrazione degli utenti senza problemi](https://aka.ms/b2c-account-seamless-migration) in GitHub.To see an example custom policy and REST API, see the seamless user migration sample on GitHub.

![Diagramma di flusso dell'approccio di migrazione senza soluzione di continuità alla migrazione degli utenti](./media/user-migration/diagram-01-seamless-migration.png)<br />*Diagramma: flusso di migrazione senza interruzioni*

## <a name="best-practices"></a>Procedure consigliate

### <a name="security"></a>Security

L'approccio di migrazione senza soluzione di continuità usa la propria API REST personalizzata per convalidare le credenziali di un utente rispetto al provider di identità legacy.

**È necessario proteggere l'API REST dagli attacchi a forza bruta.** Un utente malintenzionato può inviare diverse password nella speranza di indovinare le credenziali di un utente. Per sconfiggere tali attacchi, interrompere la pubblicazione delle richieste all'API REST quando il numero di tentativi di accesso supera una determinata soglia. Inoltre, proteggere la comunicazione tra Azure AD B2C e l'API REST. Per informazioni su come proteggere le API RESTful per la produzione, vedere [Secure RESTful API](secure-rest-api.md).

### <a name="user-attributes"></a>Attributi utente

Non tutte le informazioni nel provider di identità legacy devono essere migrate nella directory B2C di Azure AD. Identificare il set appropriato di attributi utente da archiviare in Azure AD B2C prima della migrazione.

- **Archivio DO** in Azure AD B2C
  - Nome utente, password, indirizzi e-mail, numeri di telefono, numeri di iscrizione/identificatori.
  - Marcatori di consenso per l'informativa sulla privacy e i contratti di licenza con l'utente finale.
- **NON** archiviare in Azure AD B2C
  - Dati sensibili come numeri di carta di credito, numeri di previdenza sociale (SSN), cartelle cliniche o altri dati regolamentati da enti governativi o di conformità del settore.
  - Preferenze di marketing o di comunicazione, comportamenti degli utenti e approfondimenti.

### <a name="directory-clean-up"></a>Pulizia directory

Prima di avviare il processo di migrazione, cogliere l'opportunità di pulire la directory.

- Identificare il set di attributi utente da archiviare in Azure AD B2C ed eseguire la migrazione solo di ciò che è necessario. Se necessario, è possibile creare [attributi personalizzati](custom-policy-custom-attributes.md) per archiviare più dati su un utente.
- Se si esegue la migrazione da un ambiente con più origini di autenticazione (ad esempio, ogni applicazione ha la propria directory utente), eseguire la migrazione a un account unificato in Azure AD B2C.
- Se più applicazioni hanno nomi utente diversi, è possibile archiviarle tutte in un account utente B2C di Azure AD usando la raccolta di identità. Per quanto riguarda la password, lasciare che l'utente sceglierne uno e impostarlo nella directory. Ad esempio, con la migrazione senza problemi, solo la password scelta deve essere archiviata nell'account B2C di Azure AD.
- Rimuovere gli account utente inutilizzati prima della migrazione o non eseguire la migrazione degli account non aggiornati.

### <a name="password-policy"></a>Criteri password

Se gli account di cui si esegue la migrazione hanno [una](../active-directory/authentication/concept-sspr-policy.md) complessità della password inferiore a quella applicata da Azure AD B2C, è possibile disabilitare il requisito della password complessa. Per ulteriori informazioni, vedere [Proprietà dei criteri password](manage-user-accounts-graph-api.md#password-policy-property).

## <a name="next-steps"></a>Passaggi successivi

Il repository [azure-ad-b2c/user-migration](https://github.com/azure-ad-b2c/user-migration) in GitHub contiene un esempio di criteri personalizzati di migrazione senza soluzione di continuità e un esempio di codice dell'API REST:The azure-ad-b2c/user-migration repository on GitHub contains a seamless migration custom policy example and REST API code sample:

[Criteri personalizzati di migrazione degli utenti senza soluzione di continuità &'esempio di codice dell'API REST](https://aka.ms/b2c-account-seamless-migration)
