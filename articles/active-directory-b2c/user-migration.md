---
title: Approcci alla migrazione degli utenti
titleSuffix: Azure AD B2C
description: Eseguire la migrazione degli account utente da un altro provider di identità a Azure AD B2C usando l'importazione bulk o i metodi di migrazione senza problemi.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 759379d99bbd422c43998997be1d52ecbf34966d
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484290"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>Migrare gli utenti a Azure AD B2C

La migrazione da un altro provider di identità a Azure Active Directory B2C (Azure AD B2C) potrebbe richiedere anche la migrazione degli account utente esistenti. Di seguito sono descritti due metodi di migrazione, ovvero l' *importazione bulk* e la *migrazione senza*problemi. Con entrambi gli approcci, è necessario scrivere un'applicazione o uno script che usi l' [API Microsoft Graph](manage-user-accounts-graph-api.md) per creare gli account utente nel Azure ad B2C.

## <a name="bulk-import"></a>Importazione bulk

Nel flusso di importazione bulk, l'applicazione di migrazione esegue questi passaggi per ogni account utente:

1. Leggere l'account utente dal vecchio provider di identità, incluse le credenziali correnti (nome utente e password).
1. Creare un account corrispondente nella directory Azure AD B2C con le credenziali correnti.

Usare il flusso di importazione bulk in una delle due situazioni seguenti:

- È possibile accedere alle credenziali in testo non crittografato dell'utente (nome utente e password).
- Le credenziali sono crittografate, ma è possibile decrittografarle.

Per informazioni sulla creazione di account utente a livello di codice, vedere [manage Azure ad B2C user accounts with Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="seamless-migration"></a>Migrazione senza problemi

Usare il flusso di migrazione senza problemi se le password non crittografate nel vecchio provider di identità non sono accessibili. Ad esempio, quando:

- La password viene archiviata in un formato crittografato unidirezionale, ad esempio con una funzione hash.
- La password viene archiviata dal provider di identità legacy in modo che non sia possibile accedere a. Ad esempio, quando il provider di identità convalida le credenziali chiamando un servizio Web.

Il flusso di migrazione senza problemi richiede comunque la migrazione in blocco degli account utente, ma usa un [criterio personalizzato](restful-technical-profile.md) per eseguire una query su un' [API REST](rest-api-claims-exchange-dotnet.md) (creata) per impostare la password di ogni utente al primo accesso.

Il flusso di migrazione senza problemi comporta quindi due fasi: l' *importazione bulk* e l' *impostazione delle credenziali*.

### <a name="phase-1-bulk-import"></a>Fase 1: importazione bulk

1. L'applicazione di migrazione legge gli account utente dal vecchio provider di identità.
1. L'applicazione di migrazione crea gli account utente corrispondenti nella directory Azure AD B2C, ma *non imposta le password*.

### <a name="phase-2-set-credentials"></a>Fase 2: impostare le credenziali

Al termine della migrazione in blocco degli account, i criteri personalizzati e l'API REST eseguono le operazioni seguenti quando un utente accede:

1. Leggere l'account utente Azure AD B2C corrispondente all'indirizzo di posta elettronica immesso.
1. Controllare se l'account è contrassegnato per la migrazione valutando un attributo di estensione booleano.
    - Se l'attributo Extension restituisce `True`, chiamare l'API REST per convalidare la password rispetto al provider di identità legacy.
      - Se l'API REST determina che la password non è corretta, restituire un errore descrittivo all'utente.
      - Se l'API REST determina che la password è corretta, scrivere la password nell'account Azure AD B2C e modificare l'attributo di estensione booleano in `False`.
    - Se l'attributo di estensione booleano restituisce `False`, continuare il processo di accesso come di consueto.

Per visualizzare un criterio personalizzato di esempio e un'API REST, vedere l' [esempio di migrazione di utenti](https://aka.ms/b2c-account-seamless-migration) semplificati su GitHub.

diagramma ![diagramma di flusso dell'approccio di migrazione trasparente alla migrazione degli utenti](./media/user-migration/diagram-01-seamless-migration.png)<br />*Diagramma: flusso di migrazione senza problemi*

## <a name="best-practices"></a>Procedure consigliate

### <a name="security"></a>Security

L'approccio di migrazione trasparente usa l'API REST personalizzata per convalidare le credenziali di un utente rispetto al provider di identità legacy.

**È necessario proteggere l'API REST da attacchi di forza bruta.** Un utente malintenzionato può inviare diverse password con la speranza di indovinare le credenziali di un utente. Per evitare questi attacchi, interrompere la conservazione delle richieste all'API REST quando il numero di tentativi di accesso supera una determinata soglia. Inoltre, è possibile proteggere le comunicazioni tra Azure AD B2C e l'API REST usando un [certificato client](secure-rest-api-dotnet-certificate-auth.md).

### <a name="user-attributes"></a>Attributi utente

Non è necessario eseguire la migrazione di tutte le informazioni del provider di identità legacy alla directory Azure AD B2C. Identificare il set appropriato di attributi utente da archiviare in Azure AD B2C prima della migrazione.

- **Archivia in** Azure ad B2C
  - Nome utente, password, indirizzi di posta elettronica, numeri di telefono, numeri di appartenenza/identificatori.
  - Indicatori di consenso per l'informativa sulla privacy e i contratti di licenza con l'utente finale
- **Non** archiviare in Azure ad B2C
  - Dati sensibili, come numeri di carta di credito, codici fiscali (SSN, Social Security Number), record medici o altri dati regolamentati da enti governativi o di conformità del settore.
  - Preferenze di marketing o comunicazione, comportamenti degli utenti e informazioni dettagliate.

### <a name="directory-clean-up"></a>Pulizia directory

Prima di iniziare il processo di migrazione, è possibile eliminare la directory.

- Identificare il set di attributi utente da archiviare in Azure AD B2C e migrare solo gli elementi necessari. Se necessario, è possibile creare [attributi personalizzati](custom-policy-custom-attributes.md) per archiviare più dati relativi a un utente.
- Se si esegue la migrazione da un ambiente con più origini di autenticazione (ad esempio, ogni applicazione dispone di una propria directory utente), eseguire la migrazione a un account unificato in Azure AD B2C.
- Se più applicazioni hanno nomi utente diversi, è possibile archiviarli tutti in un account utente Azure AD B2C usando l'insieme di identità. Per quanto riguarda la password, consentire all'utente di sceglierne una e impostarla nella directory. Ad esempio, con la migrazione senza problemi, solo la password scelta deve essere archiviata nell'account Azure AD B2C.
- Rimuovere gli account utente inutilizzati prima della migrazione oppure non eseguire la migrazione degli account obsoleti.

### <a name="password-policy"></a>Criteri password

Se gli account di cui si sta eseguendo la migrazione hanno un livello di complessità della password più debole rispetto al livello di [attendibilità della password forte](../active-directory/authentication/concept-sspr-policy.md) applicato da Azure ad B2C, è possibile disabilitare il requisito per la password complessa. Per ulteriori informazioni, vedere [proprietà dei criteri password](manage-user-accounts-graph-api.md#password-policy-property).

## <a name="next-steps"></a>Passaggi successivi

Il repository [Azure-ad-B2C/migrazione utenti](https://github.com/azure-ad-b2c/user-migration) in github contiene un esempio di codice personalizzato per la migrazione e un esempio di codice dell'API REST:

[Esempio di codice dell'API REST per la migrazione utente semplice &](https://aka.ms/b2c-account-seamless-migration)
