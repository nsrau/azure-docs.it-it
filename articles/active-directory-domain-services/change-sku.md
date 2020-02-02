---
title: Modificare lo SKU per un Azure AD Domain Services | Microsoft Docs
description: Informazioni sul livello SKU per un dominio gestito Azure AD Domain Services se i requisiti aziendali cambiano
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: 46557d802222190c0ed82f6243dd2a9b997ecaa5
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960568"
---
# <a name="change-the-sku-for-an-existing-azure-ad-domain-services-managed-domain"></a>Modificare lo SKU per un dominio gestito Azure AD Domain Services esistente

In Azure Active Directory Domain Services (Azure AD DS), le prestazioni e le funzionalità disponibili sono basate sul tipo di SKU. Queste differenze tra le funzionalità includono la frequenza di backup o il numero massimo di trust tra foreste in uscita unidirezionali (attualmente in anteprima). Si seleziona uno SKU quando si crea il dominio gestito ed è possibile cambiare SKU in base alle esigenze aziendali modificate dopo la distribuzione del dominio gestito. Le modifiche ai requisiti aziendali potrebbero includere la necessità di backup più frequenti o la creazione di trust tra foreste aggiuntivi. Per ulteriori informazioni sui limiti e sui prezzi dei diversi SKU, vedere la pagina relativa ai [concetti relativi a sku Azure AD DS][concepts-sku] e alle pagine relative ai [prezzi di Azure AD DS][pricing] .

Questo articolo illustra come modificare lo SKU per un dominio gestito di Azure AD DS esistente usando il portale di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, completare l'esercitazione per [creare e configurare un'istanza di Azure Active Directory Domain Services][create-azure-ad-ds-instance].

## <a name="sku-change-limitations"></a>Limitazioni della modifica dello SKU

Esistono alcune limitazioni per l'operazione di modifica dello SKU se si usa una foresta di risorse (attualmente in anteprima) e sono stati creati trust tra foreste in uscita unidirezionali da Azure AD DS a un ambiente di servizi di dominio Active Directory locale. Gli SKU *Premium* e *Enterprise* definiscono un limite per il numero di trust che è possibile creare. Non è possibile passare a uno SKU con un limite massimo inferiore rispetto al momento della configurazione.

Se, ad esempio, sono stati creati due trust tra foreste nello SKU *Premium* , non è possibile passare allo SKU *standard* . Lo SKU *standard* non supporta i trust tra foreste. In alternativa, se sono stati creati sette Trust nello SKU *Premium* , non è possibile passare allo SKU *Enterprise* . Lo SKU *Enterprise* supporta un massimo di cinque trust.

Per ulteriori informazioni su questi limiti, vedere la pagina relativa ai [limiti e alle funzionalità di SKU di Azure AD DS][concepts-sku].

## <a name="select-a-new-sku"></a>Selezionare un nuovo SKU

Per modificare lo SKU per un dominio gestito Azure AD DS usando il portale di Azure, completare i passaggi seguenti:

1. Nella parte superiore del portale di Azure cercare e selezionare **Azure ad Domain Services**. Scegliere il dominio gestito dall'elenco, ad esempio *aadds.contoso.com*.
1. Nel menu sul lato sinistro della pagina Azure AD DS selezionare **impostazioni > SKU**.

    ![Selezionare l'opzione di menu SKU per il dominio gestito di Azure AD DS nell'portale di Azure](media/change-sku/overview-change-sku.png)

1. Dal menu a discesa selezionare lo SKU desiderato per il dominio gestito di Azure AD DS. Se si dispone di una foresta di risorse, non è possibile selezionare lo SKU *standard* perché i trust tra foreste sono disponibili solo per lo SKU *Enterprise* o versione successiva.

    Scegliere lo SKU desiderato dal menu a discesa, quindi selezionare **Salva**.

    ![Scegliere lo SKU richiesto dal menu a discesa nel portale di Azure](media/change-sku/change-sku-selection.png)

Per modificare il tipo di SKU può essere necessario un minuto o due.

## <a name="next-steps"></a>Passaggi successivi

Se si dispone di una foresta di risorse e si desidera creare trust aggiuntivi dopo la modifica dello SKU, vedere [creare un trust tra foreste in uscita per un dominio locale in Azure AD DS (anteprima)][create-trust].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
