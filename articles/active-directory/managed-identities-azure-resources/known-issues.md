---
title: Domande frequenti e problemi noti relativi alle identità gestite - Azure AD
description: Problemi noti relativi alle identità gestite per le risorse di Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 6f18c9fe43b0b714e5709b014c051520b3722138
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85855134"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Domande frequenti e problemi noti nell'uso di identità gestite per le risorse di Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Domande frequenti (FAQ)

> [!NOTE]
> Identità gestite per le risorse di Azure è il nuovo nome per il servizio precedentemente noto come identità del servizio gestita.


### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>Come è possibile trovare le risorse con un'identità gestita?

È possibile trovare l'elenco delle risorse che hanno un'identità gestita assegnata dal sistema usando il comando dell'interfaccia della riga di comando di Azure seguente: 

`az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table`




### <a name="do-managed-identities-have-a-backing-app-object"></a>Le identità gestite hanno un oggetto app di supporto?

No. Le identità gestite e le registrazioni App Azure AD non sono la stessa cosa nella directory. 

Registrazioni app hanno due componenti: un oggetto applicazione e un oggetto entità servizio. Le identità gestite per le risorse di Azure hanno solo uno di questi componenti: un oggetto entità servizio. 

Le identità gestite non hanno un oggetto applicazione nella directory, ovvero ciò che viene comunemente usato per concedere le autorizzazioni dell'app per MS Graph. Al contrario, le autorizzazioni di Microsoft Graph per le identità gestite devono essere concesse direttamente all'entità servizio.  

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Le identità gestite per le risorse di Azure funzionano con i Servizi cloud di Azure?

No, non sono previste iniziative per supportare le identità gestite per le risorse di Azure nei Servizi cloud di Azure.

### <a name="does-managed-identities-for-azure-resources-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Le identità gestite per le risorse di Azure funzionano con Active Directory Authentication Library (ADAL) o con Microsoft Authentication Library (MSAL)?

No, le identità gestite per le risorse di Azure non sono ancora integrate con ADAL o MSAL. Per informazioni dettagliate su come acquisire un token per le identità gestite per le risorse di Azure usando l'endpoint REST, vedere [Come usare le identità gestite per le risorse di Azure in una macchina virtuale di Azure per acquisire un token di accesso](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Cosa si intende per limite di sicurezza delle identità gestite per le risorse di Azure?

Il limite di sicurezza dell'identità è la risorsa a cui è collegata. Ad esempio, il limite di sicurezza per una macchina virtuale con le identità gestite per le risorse di Azure abilitate è la macchina virtuale. Qualsiasi codice in esecuzione su quella macchina virtuale può chiamare l'endpoint delle identità gestite per le risorse di Azure e richiedere i token. L'esperienza è simile a quella con altre risorse che supportano le identità gestite per le risorse di Azure.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Quale identità sarà predefinita da IMDS se non si specifica l'identità nella richiesta?

- Se è abilitata l'identità gestita assegnata dal sistema e non viene specificata nessuna identità nella richiesta, IMDS userà come predefinita l'identità gestita assegnata dal sistema.
- Se non è abilitata l'identità gestita assegnata dal sistema ed esiste solo un'identità gestita assegnata dall'utente, IMDS userà come predefinita quella singola identità gestita assegnata dall'utente. 
- Se non è abilitata l'identità gestita assegnata dal sistema ed esistono più identità gestite assegnate dall'utente, è necessario allora specificare un'identità gestita nella richiesta.



### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Le identità gestite saranno ricreate automaticamente se si sposta una sottoscrizione in un'altra directory?

No. Se si sposta una sottoscrizione in un'altra directory, sarà necessario ricrearla manualmente e concedere nuovamente le assegnazioni di ruolo del Controllo degli accessi in base al ruolo di Azure.
- Per le identità gestite assegnate dal sistema: disabilitare e abilitare di nuovo. 
- Per le identità gestite assegnate dall'utente: eliminare, ricreare e collegare nuovamente alle risorse necessarie (ad esempio macchine virtuali)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>È possibile usare un'identità gestita per accedere a risorse in tenant/directory diversi?

No. Le identità gestite attualmente non supportano gli scenari tra directory. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Quali autorizzazioni di controllo degli accessi in base al ruolo di Azure sono necessarie per un'identità gestita in una risorsa? 

- Identità gestita assegnata dal sistema: Sono necessarie autorizzazioni di scrittura per la risorsa. Per le macchine virtuali, ad esempio, è necessario Microsoft.Compute/virtualMachines/write. Questa azione è inclusa in ruoli predefiniti specifici della risorsa come [Collaboratore Macchina virtuale](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).
- Identità gestita assegnata dall'utente: Sono necessarie autorizzazioni di scrittura per la risorsa. Per le macchine virtuali, ad esempio, è necessario Microsoft.Compute/virtualMachines/write. Oltre all'assegnazione di ruolo [Operatore di identità gestite](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-operator) per l'identità gestita.



## <a name="known-issues"></a>Problemi noti

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Lo "Script di automazione" restituisce un errore se si tenta di esportare lo schema per l'estensione relativa alle identità gestite per le risorse di Azure

Se le identità gestite per le risorse di Azure sono abilitate su una macchina virtuale, viene visualizzato l'errore seguente quando si tenta di usare la funzionalità "Script di automazione" per la macchina virtuale o per il relativo gruppo di risorse:

![Errore di esportazione dello script di automazione relativo alle identità gestite per le risorse di Azure](./media/msi-known-issues/automation-script-export-error.png)

L'estensione della macchina virtuale relativa alle identità gestite per le risorse di Azure (la cui deprecazione è prevista a gennaio 2019) non supporta attualmente la possibilità di esportare lo schema in un modello di gruppo di risorse. Di conseguenza, il modello generato non mostra i parametri di configurazione per abilitare le identità gestite per le risorse di Azure sulla risorsa. È possibile aggiungere manualmente queste sezioni seguendo gli esempi in [Configurare identità gestite per le risorse di Azure in una macchina virtuale di Azure usando modelli](qs-configure-template-windows-vm.md).

Quando la funzione di esportazione dello schema sarà disponibile per l'estensione della macchina virtuale relativa alle identità gestite per le risorse di Azure (la cui deprecazione è prevista a gennaio 2019), sarà elencata in [Esportazione di gruppi di risorse contenenti estensioni della macchina virtuale](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>La macchina virtuale non viene avviata dopo essere stata spostata dal gruppo di risorse o dalla sottoscrizione

Se si sposta una macchina virtuale in esecuzione, questa continuerà a eseguire durante lo spostamento. Tuttavia, dopo lo spostamento, se la macchina virtuale viene arrestata e riavviata, non si avvierà. Questo problema si verifica perché la macchina virtuale non aggiorna il riferimento alle identità gestite per le risorse di Azure e continua a puntare a questa nel gruppo di risorse precedente.

**Soluzione alternativa** 
 
Attivare un aggiornamento nella macchina virtuale così da poter ottenere i valori corretti per le identità gestite per le risorse di Azure. È possibile apportare una modifica alla proprietà della macchina virtuale per aggiornare il riferimento alle identità gestite per le risorse di Azure. Ad esempio, è possibile impostare un nuovo valore di tag nella macchina virtuale con il comando seguente:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Questo comando imposta un nuovo tag "fixVM" con valore 1 nella macchina virtuale. 
 
Impostando questa proprietà, la macchina virtuale si aggiorna inserendo l'URI risorsa corretto delle identità gestite per le risorse di Azure. La macchina virtuale dovrebbe a questo punto avviarsi. 
 
Dopo aver avviato la macchina virtuale, il tag può essere rimosso tramite il seguente comando:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```



### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Trasferimento di una sottoscrizione tra directory di Azure AD

Le identità gestite non vengono aggiornate quando una sottoscrizione viene spostata/trasferita a un'altra directory. Di conseguenza, tutte le identità gestite assegnate dal sistema o assegnate dall'utente saranno interrotte. 

Soluzione alternativa per le identità gestite in una sottoscrizione che è stata spostata in un'altra directory:

 - Per le identità gestite assegnate dal sistema: disabilitare e abilitare di nuovo. 
 - Per le identità gestite assegnate dall'utente: eliminare, ricreare e collegare nuovamente alle risorse necessarie (ad esempio macchine virtuali)

Per altre informazioni, vedere [trasferire una sottoscrizione di Azure a un'altra directory di Azure ad (anteprima)](../../role-based-access-control/transfer-subscription.md).

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Spostamento di un'identità gestita assegnata dall'utente in un'altra sottoscrizione o in un gruppo di risorse diverso

Lo spostamento di un'identità gestita assegnata dall'utente in un gruppo di risorse diverso non è supportato.
