---
title: Identità gestite per Azure
description: Informazioni sull'uso delle identità gestite per Azure con Service Fabric.
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: f477acab332cf39de2504c675b04abb5b14a305f
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934973"
---
# <a name="using-managed-identities-for-azure-with-service-fabric-preview"></a>Uso delle identità gestite per Azure con Service Fabric (anteprima)

Un problema comune durante la creazione di applicazioni cloud è la modalità di gestione sicura delle credenziali nel codice per l'autenticazione a diversi servizi senza salvarle localmente in una workstation per sviluppatori o nel controllo del codice sorgente. Le *identità gestite per Azure* risolvono questo problema per tutte le risorse in Azure Active Directory (Azure ad) fornendo loro identità gestite automaticamente all'interno Azure ad. È possibile usare l'identità di un servizio per l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure AD, incluso Key Vault, senza credenziali archiviate nel codice.

*Le identità gestite per le risorse di Azure* sono gratuite con Azure ad per le sottoscrizioni di Azure. Non sono previsti costi aggiuntivi.

> [!NOTE]
> *Identità gestite per Azure* è il nuovo nome del servizio precedentemente noto come identità del servizio gestita (MSI).

## <a name="concepts"></a>Concetti

Le identità gestite per Azure sono basate su diversi concetti chiave:

- **ID client** : un identificatore univoco generato da Azure ad associato a un'applicazione e un'entità servizio durante il provisioning iniziale (vedere anche [ID applicazione](/azure/active-directory/develop/developer-glossary#application-id-client-id)).

- **ID entità** : ID oggetto dell'oggetto entità servizio per l'identità gestita usata per concedere l'accesso in base al ruolo a una risorsa di Azure.

- **Entità servizio** : oggetto Azure Active Directory, che rappresenta la proiezione di un'applicazione AAD in un determinato tenant. vedere anche [entità servizio](../active-directory/develop/developer-glossary.md#service-principal-object).

Sono due tipi di identità gestite:

- Un' **identità gestita assegnata dal sistema** è abilitata direttamente in un'istanza del servizio di Azure.  Il ciclo di vita di un'identità assegnata dal sistema è univoco per l'istanza del servizio di Azure in cui è abilitata.
- Un'**identità gestita assegnata dall'utente** viene creata come risorsa di Azure autonoma. L'identità può essere assegnata a una o più istanze del servizio di Azure ed è gestita separatamente dai cicli di vita di tali istanze.

Per comprendere meglio la differenza tra i tipi di identità gestiti, vedere [come funzionano le identità gestite per le risorse di Azure?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)

## <a name="supported-scenarios-for-service-fabric-applications"></a>Scenari supportati per applicazioni Service Fabric

Le identità gestite per Service Fabric sono supportate solo nei cluster Service Fabric distribuiti da Azure e solo per le applicazioni distribuite come risorse di Azure. non è possibile assegnare un'identità a un'applicazione non distribuita come risorsa di Azure. In teoria, il supporto per le identità gestite in Azure Service Fabric cluster è costituito da due fasi:

1. Assegnare una o più identità gestite alla risorsa dell'applicazione; a un'applicazione può essere assegnata una sola identità assegnata dal sistema e/o fino a 32 identità assegnate dall'utente, rispettivamente.

2. All'interno della definizione dell'applicazione, eseguire il mapping di una delle identità assegnate all'applicazione a un singolo servizio che comprende l'applicazione.

L'identità assegnata dal sistema di un'applicazione è univoca per l'applicazione. un'identità assegnata dall'utente è una risorsa autonoma, che può essere assegnata a più applicazioni. All'interno di un'applicazione, una singola identità (indipendente dal sistema o assegnata dall'utente) può essere assegnata a più servizi dell'applicazione, ma a ogni singolo servizio può essere assegnata una sola identità. Infine, a un servizio deve essere assegnata un'identità in modo esplicito per poter accedere a questa funzionalità. In effetti, il mapping delle identità di un'applicazione ai servizi costitutivi consente l'isolamento in-Application: un servizio può usare solo l'identità mappata.  

Attualmente, per questa funzionalità di anteprima sono supportati gli scenari seguenti:

- Distribuire una nuova applicazione con uno o più servizi e una o più identità assegnate

- Assegnare una o più identità gestite a un'applicazione esistente (distribuita in Azure) per accedere alle risorse di Azure

Gli scenari seguenti non sono supportati o non sono consigliati. Si noti che queste azioni potrebbero non essere bloccate, ma possono causare interruzioni nelle applicazioni:

- Rimuovere o modificare le identità assegnate a un'applicazione; Se è necessario apportare modifiche, inviare distribuzioni separate prima di aggiungere una nuova assegnazione di identità e quindi rimuovere una assegnata in precedenza. La rimozione di un'identità da un'applicazione esistente può avere effetti indesiderati, inclusa l'uscita dall'applicazione in uno stato non aggiornabile. Se la rimozione di un'identità è necessaria, è possibile eliminare l'applicazione in modo sicuro. Si noti che questa operazione eliminerà l'identità assegnata dal sistema (se così definita) associata all'applicazione e rimuoverà tutte le associazioni con le identità assegnate dall'utente assegnate all'applicazione.

- Il supporto Service Fabric per le identità gestite non è integrato in questo momento nel [AzureServiceTokenProvider](../key-vault/service-to-service-authentication.md); l'integrazione verrà eseguita entro la fine del periodo di anteprima per la funzionalità Gestione identità gestita.

>
> [!NOTE]
>
> Questa funzionalità è in anteprima. Può essere soggetto a modifiche frequenti e non è adatto per le distribuzioni di produzione.

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire un nuovo cluster di Azure Service Fabric con supporto di identità gestito](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [Abilitare il supporto di identità gestite in un cluster di Azure Service Fabric esistente](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [Distribuire un'applicazione Service Fabric di Azure con un'identità gestita assegnata dal sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [Distribuire un'applicazione Service Fabric di Azure con un'identità gestita assegnata dall'utente](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [Sfruttare l'identità gestita di un'applicazione Service Fabric dal codice del servizio](./how-to-managed-identity-service-fabric-app-code.md)
- [Concedere a un'applicazione Service Fabric di Azure l'accesso ad altre risorse di Azure](./how-to-grant-access-other-resources.md)
- [Dichiarazione e utilizzo dei segreti dell'applicazione come KeyVaultReferences](./service-fabric-keyvault-references.md)
