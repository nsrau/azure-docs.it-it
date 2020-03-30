---
title: Usare il provider di risorse di Archiviazione di Azure per accedere alle risorse di gestioneUse the Azure Storage resource provider to access management resources
description: Il provider di risorse di Archiviazione di Azure è un servizio che fornisce l'accesso alle risorse di gestione per Archiviazione di Azure.The Azure Storage resource provider is a service that provides access to management resources for Azure Storage. È possibile usare il provider di risorse di Archiviazione di Azure per creare, aggiornare, gestire ed eliminare risorse quali account di archiviazione, endpoint privati e chiavi di accesso agli account.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f5d42a6a0567d3949bc4b0fb1947450a9c957f18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972359"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>Usare il provider di risorse di Archiviazione di Azure per accedere alle risorse di gestioneUse the Azure Storage resource provider to access management resources

Azure Resource Manager è il servizio di distribuzione e gestione di Azure. Il provider di risorse di Archiviazione di Azure è un servizio basato su Azure Resource Manager e che fornisce l'accesso alle risorse di gestione per Archiviazione di Azure.The Azure Storage resource provider is a service that is based on Azure Resource Manager and that provides access to management resources for Azure Storage. È possibile usare il provider di risorse di Archiviazione di Azure per creare, aggiornare, gestire ed eliminare risorse quali account di archiviazione, endpoint privati e chiavi di accesso agli account. Per altre informazioni su Azure Resource Manager, vedere Panoramica di Azure Resource Manager.For more information about Azure Resource Manager, see [Azure Resource Manager overview.](/azure/azure-resource-manager/resource-group-overview)

È possibile usare il provider di risorse di Archiviazione di Azure per eseguire azioni come la creazione o l'eliminazione di un account di archiviazione o il recupero di un elenco di account di archiviazione in una sottoscrizione. Per autorizzare le richieste al provider di risorse di Archiviazione di Azure, usare Azure Active Directory (Azure AD). Questo articolo descrive come assegnare autorizzazioni alle risorse di gestione e punta ad esempi che illustrano come effettuare richieste al provider di risorse di Archiviazione di Azure.This article describes how to assign permissions to management resources, and points to examples that show to make requests against the Azure Storage resource provider.

## <a name="management-resources-versus-data-resources"></a>Risorse di gestione e risorse di datiManagement resources versus data resources

Microsoft fornisce due API REST per l'utilizzo delle risorse di Archiviazione di Azure.Microsoft provides two REST APIs for working with Azure Storage resources. Queste API costituiscono la base di tutte le azioni che è possibile eseguire su Archiviazione di Azure.These APIs form the basis of all actions you can perform against Azure Storage. L'API REST di Archiviazione di Azure consente di usare i dati nell'account di archiviazione, inclusi i dati BLOB, coda, file e tabella. L'API REST del provider di risorse di Archiviazione di Azure consente di usare l'account di archiviazione e le risorse correlate.

Una richiesta che legge o scrive dati BLOB richiede autorizzazioni diverse rispetto a una richiesta che esegue un'operazione di gestione. Il controllo degli accessi in base al ruolo offre un controllo granulare sulle autorizzazioni per entrambi i tipi di risorse. Quando si assegna un ruolo Controllo degli accessi in base al ruolo a un'entità di sicurezza, assicurarsi di comprendere quali autorizzazioni verranno concesse a tale entità. Per un riferimento dettagliato che descrive le azioni associate a ogni ruolo RBAC predefinito, vedere Ruoli predefiniti per le risorse di Azure.For a detailed reference that describes which actions are associated with each built-in RBAC role, see [Built-in roles for Azure resources.](../../role-based-access-control/built-in-roles.md)

Archiviazione di Azure supporta l'uso di Azure AD per autorizzare le richieste nell'archiviazione BLOB e coda. Per informazioni sui ruoli RBAC per le operazioni sui dati BLOB e di coda, vedere [Autorizzare l'accesso a BLOB e code tramite Active Directory.](storage-auth-aad.md)

## <a name="assign-management-permissions-with-role-based-access-control-rbac"></a>Assegnare autorizzazioni di gestione con il controllo degli accessi in base al ruolo

A ogni sottoscrizione di Azure è associato Azure Active Directory che gestisce utenti, gruppi e applicazioni. Un utente, un gruppo o un'applicazione viene anche definito entità di sicurezza nel contesto della piattaforma di [identità Microsoft](/azure/active-directory/develop/). È possibile concedere l'accesso alle risorse di una sottoscrizione a un'entità di sicurezza definita in Active Directory utilizzando il controllo degli accessi in base al ruolo.

Quando si assegna un ruolo Controllo degli accessi in base al ruolo a un'entità di sicurezza, si indica anche l'ambito in cui sono attive le autorizzazioni concesse dal ruolo. Per le operazioni di gestione, è possibile assegnare un ruolo a livello di sottoscrizione, del gruppo di risorse o dell'account di archiviazione. È possibile assegnare un ruolo Controllo degli accessi in base al ruolo a un'entità di sicurezza usando il portale di [Azure,](https://portal.azure.com/)gli [strumenti dell'interfaccia della](../../cli-install-nodejs.md)riga di comando di Azure , [PowerShell](/powershell/azureps-cmdlets-docs)o l'API REST del provider di risorse di [Archiviazione di Azure.](/rest/api/storagerp)

Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Che cos'è](../../role-based-access-control/overview.md) il controllo degli accessi in base al ruolo per le risorse di Azure? e Ruoli di [amministratore della sottoscrizione classica, ruoli di controllo degli accessi in](../../role-based-access-control/rbac-and-directory-admin-roles.md)base al ruolo di Azure e ruoli di amministratore di Azure AD.

### <a name="built-in-roles-for-management-operations"></a>Ruoli predefiniti per le operazioni di gestione

Azure fornisce ruoli predefiniti che concedono le autorizzazioni per le operazioni di gestione delle chiamate. Archiviazione di Azure offre anche ruoli predefiniti specifici per l'uso con il provider di risorse di Archiviazione di Azure.Azure Storage also provides built-in roles specifically for use with the Azure Storage resource provider.

I ruoli predefiniti che concedono le autorizzazioni per le operazioni di gestione dell'archiviazione delle chiamate includono i ruoli descritti nella tabella seguente:Built-in roles that grant permissions to call storage management operations include the roles described in the following table:

|    Ruolo di controllo degli accessi in base al ruolo    |    Descrizione    |    Include l'accesso alle chiavi dell'account?    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **Proprietario** | Può gestire tutte le risorse di archiviazione e l'accesso alle risorse.  | Sì, fornisce le autorizzazioni per visualizzare e rigenerare le chiavi dell'account di archiviazione. |
| **Collaboratore**  | Può gestire tutte le risorse di archiviazione, ma non può gestire l'assegnazione alle risorse. | Sì, fornisce le autorizzazioni per visualizzare e rigenerare le chiavi dell'account di archiviazione. |
| **Lettore** | Può visualizzare informazioni sull'account di archiviazione, ma non sulle chiavi dell'account. | No. |
| **Collaboratore account di archiviazione** | Può gestire l'account di archiviazione, ottenere informazioni sui gruppi di risorse e le risorse della sottoscrizione e creare e gestire distribuzioni di gruppi di risorse di sottoscrizione. | Sì, fornisce le autorizzazioni per visualizzare e rigenerare le chiavi dell'account di archiviazione. |
| **Amministratore Accesso utenti** | Può gestire l'accesso all'account di archiviazione.   | Sì, consente a un'entità di sicurezza di assegnare qualsiasi autorizzazione a se stessa e ad altre. |
| **Collaboratore macchine virtuali** | Può gestire le macchine virtuali, ma non l'account di archiviazione a cui sono connesse.   | Sì, fornisce le autorizzazioni per visualizzare e rigenerare le chiavi dell'account di archiviazione. |

La terza colonna della tabella indica se il ruolo incorporato supporta **Microsoft.Storage/storageAccounts/listkeys/action**. Questa azione concede le autorizzazioni per leggere e rigenerare le chiavi dell'account di archiviazione. Le autorizzazioni per accedere alle risorse di gestione di Archiviazione di Azure non includono anche le autorizzazioni per accedere ai dati. Tuttavia, se un utente ha accesso alle chiavi dell'account, può usare le chiavi dell'account per accedere ai dati di Archiviazione di Azure tramite l'autorizzazione della chiave condivisa.

### <a name="custom-roles-for-management-operations"></a>Ruoli personalizzati per le operazioni di gestione

Azure supporta anche la definizione di ruoli RBAC personalizzati per l'accesso alle risorse di gestione. Per altre informazioni sui ruoli personalizzati, vedere Ruoli personalizzati per le risorse di Azure.For more information about custom roles, see [Custom roles for Azure resources.](../../role-based-access-control/custom-roles.md)

## <a name="code-samples"></a>Esempi di codice

Per esempi di codice che illustrano come autorizzare e chiamare le operazioni di gestione dalle librerie di gestione di Archiviazione di Azure, vedere gli esempi seguenti:For code examples that show how to authorize and call management operations from the Azure Storage management libraries, see the following samples:

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [Java](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Node.js](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Python](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>Azure Resource Manager e distribuzioni classicheAzure Resource Manager versus classic deployments

I modelli di distribuzione classica e Resource Manager rappresentano due diversi modi di distribuire e gestire le soluzioni Azure. Microsoft consiglia di usare il modello di distribuzione di Azure Resource Manager quando si crea un nuovo account di archiviazione. Se possibile, Microsoft consiglia inoltre di ricreare gli account di archiviazione classici esistenti con il modello di Resource Manager.If possible, Microsoft also recommend s recommend that you recreate existing classic storage accounts with the Resource Manager model. Sebbene sia possibile creare un account di archiviazione usando il modello di distribuzione classica, il modello classico è meno flessibile e alla fine sarà deprecato.

Per altre informazioni sui modelli di distribuzione di Azure, vedere [Resource Manager e la distribuzione classica.](../../azure-resource-manager/management/deployment-models.md)

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica di Azure Resource ManagerAzure Resource Manager overview](/azure/azure-resource-manager/resource-group-overview)
- [Che cos'è il controllo degli accessi in base al ruolo per le risorse di Azure?](../../role-based-access-control/overview.md)
- [Obiettivi di scalabilità per il provider di risorse di Archiviazione di AzureScalability targets for the Azure Storage resource provider](scalability-targets-resource-provider.md)
