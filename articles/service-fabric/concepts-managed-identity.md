---
title: Identità gestite per AzureManaged identities for Azure
description: Informazioni sull'uso delle identità gestite per Azure con Service Fabric.Learn about using Managed identities for Azure with Service Fabric.
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 06ebcfdf3d6a3815908752153acb09437d745d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76986751"
---
# <a name="using-managed-identities-for-azure-with-service-fabric-preview"></a>Uso di identità gestite per Azure con Service Fabric (anteprima)Using Managed identities for Azure with Service Fabric (Preview)

Una sfida comune quando si creano applicazioni cloud è come gestire in modo sicuro le credenziali nel codice per l'autenticazione a vari servizi senza salvarle in locale in una workstation di sviluppo o nel controllo del codice sorgente. *Le identità gestite per Azure* risolvono questo problema per tutte le risorse in Azure Active Directory (Azure AD) fornendo loro identità gestite automaticamente in Azure AD. È possibile usare l'identità di un servizio per eseguire l'autenticazione a qualsiasi servizio che supporta l'autenticazione di Azure AD, incluso l'insieme di credenziali delle chiavi, senza credenziali archiviate nel codice.

*Le identità gestite per le risorse* di Azure sono gratuite con le sottoscrizioni di Azure AD per Azure.Managed identities for Azure resources are free with Azure AD for Azure subscriptions. Non sono previsti costi aggiuntivi.

> [!NOTE]
> *Le identità gestite per Azure* sono il nuovo nome del servizio precedentemente noto come identità del servizio gestito (MSI).

## <a name="concepts"></a>Concetti

Le identità gestite per Azure si basano su diversi concetti chiave:Managed identities for Azure is based on several key concepts:

- **ID client:** un identificatore univoco generato da Azure AD collegato a un'applicazione e a un'entità servizio durante il provisioning iniziale (vedere anche [ID applicazione).](/azure/active-directory/develop/developer-glossary#application-id-client-id)

- **ID entità:** l'ID oggetto dell'oggetto entità servizio per l'identità gestita usato per concedere l'accesso in base al ruolo a una risorsa di Azure.Principal ID - the object of the service principal object for your Managed Identity that is used to grant role-based access to an Azure resource.

- **Entità servizio:** un oggetto di Azure Active Directory, che rappresenta la proiezione di un'applicazione AAD in un determinato tenant (vedere anche [entità servizio).](../active-directory/develop/developer-glossary.md#service-principal-object)

Sono disponibili due tipi di identità gestite:

- **Un'identità gestita assegnata dal sistema** viene abilitata direttamente in un'istanza del servizio di Azure.A System-assigned managed identity is enabled directly on an Azure service instance.  Il ciclo di vita di un'identità assegnata dal sistema è univoco per l'istanza del servizio Azure in cui è abilitata.
- **Un'identità gestita assegnata dall'utente** viene creata come risorsa autonoma di Azure.A user-assigned managed identity is created as a standalone Azure resource. L'identità può essere assegnata a una o più istanze del servizio di Azure e viene gestita separatamente dai cicli di vita di tali istanze.

Per comprendere meglio la differenza tra i tipi di identità gestiti, vedere [Come funzionano le identità gestite per le risorse](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) di Azure?

## <a name="supported-scenarios-for-service-fabric-applications"></a>Scenari supportati per le applicazioni Service FabricSupported scenarios for Service Fabric applications

Le identità gestite per Service Fabric sono supportate solo nei cluster di Service Fabric distribuiti in Azure e solo per le applicazioni distribuite come risorse di Azure.Managed identities for Service Fabric are only supported in Azure-deployed Service Fabric clusters, and only for applications deployed as Azure resources; a un'applicazione che non viene distribuita come risorsa di Azure non può essere assegnata un'identità. Dal punto di vista concettuale, il supporto per le identità gestite in un cluster di Azure Service Fabric è costituito da due fasi:Conceptually speaking, support for managed identities in an Azure Service Fabric cluster consists of two phases:

1. Assegnare una o più identità gestite alla risorsa dell'applicazione. a un'applicazione può essere assegnata una singola identità assegnata dal sistema e/o fino a 32 identità assegnate dall'utente, rispettivamente.

2. All'interno della definizione dell'applicazione, eseguire il mapping di una delle identità assegnate all'applicazione a qualsiasi singolo servizio che comprende l'applicazione.

L'identità assegnata dal sistema di un'applicazione è univoca per tale applicazione. un'identità assegnata dall'utente è una risorsa autonoma che può essere assegnata a più applicazioni. All'interno di un'applicazione, una singola identità (assegnata dal sistema o dall'utente) può essere assegnata a più servizi dell'applicazione, ma a ogni singolo servizio può essere assegnata una sola identità. Infine, a un servizio deve essere assegnata un'identità in modo esplicito per avere accesso a questa funzionalità. In effetti, il mapping delle identità di un'applicazione ai relativi servizi costitutivi consente l'isolamento all'uso dell'applicazione: un servizio può utilizzare solo l'identità mappata a essa.  

Attualmente, per questa funzionalità di anteprima sono supportati i seguenti scenari:

- Distribuire una nuova applicazione con uno o più servizi e una o più identità assegnateDeploy a new application with one or more services and one or more assigned identities

- Assegnare una o più identità gestite a un'applicazione esistente (distribuita in Azure) per accedere alle risorse di AzureAssign one or more managed identities to an existing (Azure-deployed) application to access Azure resources

Gli scenari seguenti non sono supportati o non sono consigliati. Notare che queste azioni potrebbero non essere bloccate, ma possono causare interruzioni nelle applicazioni:

- Rimuovere o modificare le identità assegnate a un'applicazione; Se è necessario apportare modifiche, inviare distribuzioni separate per aggiungere prima una nuova assegnazione di identità e quindi per rimuoverne una assegnata in precedenza. La rimozione di un'identità da un'applicazione esistente può avere effetti indesiderati, tra cui lasciare l'applicazione in uno stato non aggiornabile. È sicuro eliminare completamente l'applicazione se è necessaria la rimozione di un'identità; Si noti che questo eliminerà l'identità assegnata dal sistema (se definita) associata all'applicazione e rimuoverà tutte le associazioni con le identità assegnate dall'utente assegnate all'applicazione.

- Il supporto di Service Fabric per le identità gestite non è attualmente integrato in [AzureServiceTokenProvider](../key-vault/service-to-service-authentication.md); l'integrazione sarà realizzata entro la fine del periodo di anteprima per la funzionalità di identità gestita.

>
> [!NOTE]
>
> Questa funzionalità è in anteprima. Può essere soggetto a modifiche frequenti e non adatto per le distribuzioni di produzione.

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire un nuovo cluster di Azure Service Fabric con supporto delle identità gestiteDeploy a new Azure Service Fabric cluster with managed identity support](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [Abilitare il supporto delle identità gestite in un cluster di Azure Service Fabric esistenteEnable managed identity support in an existing Azure Service Fabric cluster](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [Distribuire un'applicazione Azure Service Fabric con un'identità gestita assegnata dal sistemaDeploy an Azure Service Fabric application with a system-assigned managed identity](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [Distribuire un'applicazione Azure Service Fabric con un'identità gestita assegnata dall'utenteDeploy an Azure Service Fabric application with a user-assigned managed identity](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [Sfruttare l'identità gestita di un'applicazione Service Fabric dal codice del servizio](./how-to-managed-identity-service-fabric-app-code.md)
- [Concedere a un'applicazione di Azure Service Fabric l'accesso ad altre risorse di AzureGrant an Azure Service Fabric application access to other Azure resources](./how-to-grant-access-other-resources.md)
- [Dichiarazione e utilizzo di segreti dell'applicazione come KeyVaultReferencesDeclaring and using application secrets as KeyVaultReferences](./service-fabric-keyvault-references.md)
