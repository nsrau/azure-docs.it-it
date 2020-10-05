---
title: Che cos'è il controllo degli accessi in base al ruolo di Azure?
description: Verrà fornita una panoramica del controllo degli accessi in base al ruolo di Azure. Usare le assegnazioni di ruolo per controllare l'accesso alle risorse di Azure.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: overview
ms.workload: identity
ms.date: 09/30/2020
ms.author: rolyon
ms.custom: contperfq1, azuread-video-2020
ms.openlocfilehash: b61da9710b51ad4802b46cae7625d6ba9a66e86c
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91595759"
---
# <a name="what-is-azure-role-based-access-control-azure-rbac"></a>Che cos'è il controllo degli accessi in base al ruolo di Azure?

La gestione dell'accesso per le risorse cloud è una funzione essenziale per qualsiasi organizzazione che usa il cloud. Il controllo degli accessi in base al ruolo di Azure consente di gestire gli utenti autorizzati ad accedere alle risorse di Azure, le operazioni che possono eseguire su tali risorse e le aree a cui hanno accesso.

Il controllo degli accessi in base al ruolo di Azure è un sistema di autorizzazione basato su [Azure Resource Manager](../azure-resource-manager/management/overview.md) che garantisce una gestione con granularità fine degli accessi delle risorse di Azure.

Questo video offre una panoramica rapida del Controllo degli accessi in base al ruolo di Azure.

>[!VIDEO https://www.youtube.com/embed/Dzhm-garKBM]

## <a name="what-can-i-do-with-azure-rbac"></a>Che cosa è possibile fare con il controllo degli accessi in base al ruolo di Azure?

Di seguito sono riportati alcuni esempi di ciò che è possibile fare con il controllo degli accessi in base al ruolo di Azure:

- Consentire a un utente di gestire le macchine virtuali in una sottoscrizione e un altro utente a gestire le reti virtuali
- Consentire a un gruppo di amministratori di database di gestire database SQL all'interno di una sottoscrizione
- Consentire a un utente di gestire tutte le risorse in un gruppo di risorse, ad esempio le macchine virtuali, i siti Web e le subnet
- Consentire a un'applicazione di accedere a tutte le risorse in un gruppo di risorse

## <a name="how-azure-rbac-works"></a>Funzionamento del controllo degli accessi in base al ruolo di Azure

Per controllare l'accesso alle risorse tramite il controllo degli accessi in base al ruolo di Azure è necessario creare assegnazioni di ruolo. Questo è un concetto chiave da comprendere: si tratta di come vengono applicate le autorizzazioni. Un'assegnazione di ruolo è costituita da tre elementi: entità di sicurezza, definizione del ruolo e ambito.

### <a name="security-principal"></a>Entità di sicurezza

Un'*entità di sicurezza* è un oggetto che rappresenta un utente, un gruppo, un'entità servizio o un'identità gestita che richiede l'accesso alle risorse di Azure. È possibile assegnare un ruolo a una di queste entità di sicurezza.

![Entità di sicurezza per un'assegnazione di ruolo](./media/shared/rbac-security-principal.png)

### <a name="role-definition"></a>Definizione di ruolo

Una *definizione di ruolo* è una raccolta di autorizzazioni, generalmente chiamata *ruolo*. Una definizione di ruolo elenca le operazioni che è possibile eseguire, ad esempio lettura, scrittura ed eliminazione, I ruoli possono essere di livello superiore, ad esempio quello di proprietario, o specifici, ad esempio quello di lettore di macchina virtuale.

![Definizione di ruolo per un'assegnazione di ruolo](./media/shared/rbac-role-definition.png)

Azure include diversi [ruoli predefiniti](built-in-roles.md) che è possibile usare. Ad esempio, il ruolo [Collaboratore Macchina virtuale](built-in-roles.md#virtual-machine-contributor) consente a un utente di creare e gestire macchine virtuali. Se i ruoli predefiniti non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare [ruoli personalizzati di Azure](custom-roles.md).

Questo video offre una panoramica rapida dei ruoli predefiniti e dei ruoli personalizzati.

>[!VIDEO https://www.youtube.com/embed/I1mefHptRgo]

Azure dispone di operazioni sui dati che consentono di concedere l'accesso ai dati all'interno di un oggetto. Ad esempio, se un utente dispone dell'accesso in lettura ai dati per un account di archiviazione, può leggere i BLOB o i messaggi all'interno di tale account.

Per altre informazioni, vedere [Informazioni sulle definizioni dei ruoli di Azure](role-definitions.md).

### <a name="scope"></a>Scope

*Ambito* è il set di risorse a cui si applica l'accesso. Quando si assegna un ruolo, è possibile limitare ulteriormente le azioni consentite definendo un ambito. Ciò è utile se si intende creare un [collaboratore di siti Web](built-in-roles.md#website-contributor), ma solo per un gruppo di risorse.

In Azure, è possibile specificare un ambito su quattro livelli: [gruppo di gestione](../governance/management-groups/overview.md), sottoscrizione, [gruppo di risorse](../azure-resource-manager/management/overview.md#resource-groups) o risorsa. Gli ambiti sono strutturati in una relazione padre-figlio. È possibile assegnare i ruoli a qualsiasi livello dell'ambito.

![Ambito per un'assegnazione di ruolo](./media/shared/rbac-scope.png)

Per altre informazioni sull'ambito, vedere [Informazioni sull'ambito](scope-overview.md).

### <a name="role-assignments"></a>Assegnazioni di ruoli

Un'*assegnazione di ruolo* è il processo di associazione di una definizione di ruolo a un utente, un gruppo, un'entità servizio o un'identità gestita in un determinato ambito allo scopo di concedere l'accesso. L'accesso viene concesso mediante la creazione di un'assegnazione di ruolo e viene revocato rimuovendo un'assegnazione di ruolo.

Lo schema seguente mostra un esempio di assegnazione di ruolo. In questo esempio, al gruppo marketing è stato assegnato il ruolo di [collaboratore](built-in-roles.md#contributor) per il gruppo di risorse pharma-sales. Ciò significa che gli utenti del gruppo marketing possono creare o gestire le risorse di Azure nel gruppo di risorse pharma-sales. Gli utenti marketing non hanno accesso alle risorse all'esterno del gruppo di risorse pharma-sales, a meno che non facciano parte di un'altra assegnazione di ruolo.

![Assegnazione di ruolo per controllare l'accesso](./media/overview/rbac-overview.png)

È possibile creare assegnazioni di ruolo nel portale di Azure, nell'interfaccia della riga di comando di Azure, in Azure PowerShell, tramite gli SDK Azure o le API REST.

Per altre informazioni, vedere [Procedura per aggiungere un'assegnazione di ruolo](role-assignments-steps.md).

## <a name="multiple-role-assignments"></a>Più assegnazioni di ruolo

Cosa succede se si hanno più assegnazioni di ruolo sovrapposte? Il controllo degli accessi in base al ruolo di Azure è un modello additivo, per cui le autorizzazioni effettive corrispondono alla somma delle assegnazioni di ruolo. Nell'esempio seguente a un utente viene concesso il ruolo Collaboratore nell'ambito della sottoscrizione e il ruolo Lettore in un gruppo di risorse. La somma delle autorizzazioni di Collaboratore e di quelle di Lettore rende effettivo il ruolo di Collaboratore per il gruppo di risorse. Quindi, in questo caso, l'assegnazione del ruolo Lettore non ha alcun impatto.

![Più assegnazioni di ruolo](./media/overview/rbac-multiple-roles.png)

## <a name="deny-assignments"></a>Assegnazioni di rifiuto

In precedenza, il controllo degli accessi in base al ruolo di Azure era un modello per il solo consenso, ma ora supporta in modo limitato le assegnazioni di rifiuto. Analogamente a un'assegnazione di ruolo, un'*assegnazione di rifiuto* associa un set di azioni di rifiuto a un utente, un gruppo, un'entità servizio o un'identità gestita in un determinato ambito allo scopo di rifiutare l'accesso. Un'assegnazione di ruolo definisce un set di azioni *consentite*, mentre un'assegnazione di rifiuto definisce un set di azioni *non consentite*. In altre parole, le assegnazioni di rifiuto impediscono agli utenti di eseguire azioni specificate, anche se un'assegnazione di ruolo concede loro l'accesso. Le assegnazioni di rifiuto hanno la precedenza sulle assegnazioni di ruolo.

Per altre informazioni, vedere [Informazioni sulle assegnazioni di rifiuto di Azure](deny-assignments.md).

## <a name="how-azure-rbac-determines-if-a-user-has-access-to-a-resource"></a>Come il controllo degli accessi in base al ruolo di Azure determina se un utente può accedere a una risorsa

Di seguito è indicata la procedura generale usata dal controllo degli accessi in base al ruolo per determinare se l'utente ha accesso a una risorsa nel piano di gestione. Può essere utile se si sta tentando di risolvere un problema di accesso.

1. Un utente (o entità servizio) acquisisce un token per Azure Resource Manager.

    Il token include le appartenenze a gruppi dell'utente (incluse le appartenenze a gruppi transitive).

1. L'utente effettua una chiamata API REST ad Azure Resource Manager con il token associato.

1. Azure Resource Manager recupera tutte le assegnazioni di ruolo e le assegnazioni di rifiuto che si applicano alla risorsa su cui viene eseguita l'azione.

1. Azure Resource Manager restringe le assegnazioni di ruolo a quelle che si applicano all'utente o al gruppo corrispondente e determina i ruoli di cui l'utente dispone per questa risorsa.

1. Azure Resource Manager determina se l'azione nella chiamata API è inclusa nei ruoli di cui l'utente dispone per questa risorsa.

1. Se l'utente non ha un ruolo con l'azione nell'ambito richiesto, l'accesso non è consentito. In caso contrario, Azure Resource Manager controlla se si applica un'assegnazione di rifiuto.

1. Se si applica un'assegnazione di rifiuto, l'accesso viene bloccato. In caso contrario, l'accesso viene concesso.

## <a name="license-requirements"></a>Requisiti relativi alle licenze

[!INCLUDE [Azure AD free license](../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere o rimuovere assegnazioni di ruolo di Azure usando il portale di Azure](role-assignments-portal.md)
- [Informazioni sui diversi ruoli](rbac-and-directory-admin-roles.md)
- [Cloud Adoption Framework: gestione dell'accesso alle risorse in Azure](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management)
