---
title: Panoramica dell'identità gestita di Service Fabric | Microsoft Docs
description: Questo articolo è una panoramica dell'identità gestita.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: 3e95412675100043eb21f50c8f93aa0ec0b6b7e6
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963996"
---
# <a name="managed-identity-for-service-fabric-application-preview"></a>Identità gestita per Service Fabric applicazione (anteprima)

Una difficoltà comune durante la creazione di applicazioni cloud è rappresentata dalla gestione delle credenziali presenti nel codice per l'autenticazione ai servizi cloud. Mantenere sicure le credenziali è un'attività importante, perché non vengono mai visualizzate nelle workstation degli sviluppatori e non vengono archiviate nel controllo del codice sorgente. La funzionalità di identità gestita per le risorse di Azure in Azure Active Directory (Azure AD) risolve questo problema. La funzionalità fornisce servizi di Azure con un'identità gestita automaticamente in Azure AD. È possibile usare l'identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire le credenziali nel codice.

La funzionalità di identità gestita per le risorse di Azure è gratuita con Azure AD per le sottoscrizioni di Azure. Non sono previsti costi aggiuntivi.

> [!NOTE]
> Identità gestita per le risorse di Azure è il nuovo nome del servizio precedentemente noto come identità del servizio gestita (MSI).

## <a name="terminology"></a>Terminologia

I termini seguenti vengono usati in tutta l'identità gestita per le risorse di Azure insieme alla documentazione:

- **ID client** : un identificatore univoco generato da Azure ad associato a un'applicazione e un'entità servizio durante il provisioning iniziale (vedere anche [ID applicazione](/azure/active-directory/develop/developer-glossary#application-id-client-id)).

- **ID entità** : ID oggetto dell'oggetto entità servizio per l'identità gestita usata per concedere l'accesso in base al ruolo a una risorsa di Azure.

- **Entità servizio** : oggetto Azure Active Directory, che rappresenta la proiezione di un'applicazione AAD in un determinato tenant. vedere anche [entità servizio](../active-directory/develop/developer-glossary.md#service-principal-object).


## <a name="about-managed-identities-in-azure"></a>Informazioni sulle identità gestite in Azure

- [Tipi di identità gestita (MI) in Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work)

- [Funzionamento dell'identità gestita assegnata dal sistema in Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-system-assigned-managed-identity-works-with-an-azure-vm)

- [Come funziona l'identità gestita definita dall'utente (MI) in Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-user-assigned-managed-identity-works-with-an-azure-vm)


## <a name="supported-scenarios-for-service-fabric-applications"></a>Scenari supportati per applicazioni Service Fabric

Le identità gestite per Service Fabric sono supportate solo nei cluster Service Fabric distribuiti di Azure e solo per le applicazioni distribuite come risorse di Azure. alle applicazioni non distribuite come risorsa di Azure non può essere assegnata un'identità. In teoria, il supporto per le identità gestite in Azure Service Fabric cluster è costituito da due fasi:

1. Assegnare una o più identità gestite alla risorsa dell'applicazione; a un'applicazione può essere assegnata una sola identità assegnata dal sistema e/o fino a 32 identità assegnate dall'utente, rispettivamente.

2. All'interno della definizione dell'applicazione, eseguire il mapping di una delle identità assegnate all'applicazione a un singolo servizio che comprende l'applicazione.

L'identità assegnata dal sistema di un'applicazione è univoca per l'applicazione. un'identità assegnata dall'utente è una risorsa autonoma, che può essere assegnata a più applicazioni. All'interno di un'applicazione, una singola identità (indipendente dal sistema o assegnata dall'utente) può essere assegnata a più servizi dell'applicazione, ma a ogni singolo servizio può essere assegnata una sola identità. Infine, a un servizio deve essere assegnata un'identità in modo esplicito per poter accedere a questa funzionalità. In effetti, il mapping delle identità di un'applicazione ai relativi servizi costitutivi consente un isolamento in-Application: un servizio può usare solo l'identità mappata a esso (e nessuno se non ne è stato assegnato in modo esplicito uno).  

L'elenco degli scenari supportati per la versione di anteprima è il seguente:

   - Distribuire una nuova applicazione con uno o più servizi e una o più identità assegnate

   - Assegnare una o più identità gestite a un'applicazione esistente per accedere alle risorse di Azure; l'applicazione deve essere stata distribuita come risorsa di Azure


Gli scenari seguenti non sono supportati o non sono consigliati. Si noti che queste azioni potrebbero non essere bloccate, ma possono causare interruzioni nelle applicazioni:

   - Rimuovere o modificare le identità assegnate a un'applicazione; se è necessario apportare modifiche, inviare distribuzioni separate prima di aggiungere una nuova assegnazione di identità e quindi rimuovere una assegnata in precedenza. La rimozione di un'identità da un'applicazione esistente può avere effetti indesiderati, inclusa l'uscita dell'applicazione in uno stato non aggiornabile. Se la rimozione di un'identità è necessaria, è possibile eliminare l'applicazione in modo sicuro. Si noti che questa operazione eliminerà l'identità assegnata dal sistema (se così definita) associata all'applicazione e rimuoverà tutte le associazioni con le identità assegnate dall'utente assegnate all'applicazione.

   - Non è consigliabile combinare identità assegnate dal sistema e assegnate dall'utente nella stessa applicazione.
>
> [!NOTE]
>
> Questa funzionalità è in anteprima; di conseguenza, può essere soggetto a modifiche frequenti e potrebbe non essere adatto per le distribuzioni di produzione.

## <a name="next-steps"></a>Passaggi successivi
* [Distribuire un nuovo cluster di Azure Service Fabric con supporto di identità gestito](./configure-new-azure-service-fabric-enable-managed-identity.md) 
* [Abilitare il supporto di identità gestite in un cluster di Azure Service Fabric esistente](./configure-existing-cluster-enable-managed-identity-token-service.md)
* [Distribuire un'applicazione Service Fabric di Azure con un'identità gestita assegnata dal sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Distribuire un'applicazione Service Fabric di Azure con un'identità gestita assegnata dall'utente](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Sfruttare l'identità gestita di un'applicazione Service Fabric dal codice del servizio](./how-to-managed-identity-service-fabric-app-code.md)
* [Concedere a un'applicazione Service Fabric di Azure l'accesso ad altre risorse di Azure](./how-to-grant-access-other-resources.md)
