---
title: Modificare lo SKU per Servizi di dominio Azure AD Documenti Microsoft
description: Informazioni su come eseguire il livello SKU per un dominio gestito di Servizi di dominio Azure AD se i requisiti aziendali cambiano
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: 32f8f157abaf5076911c3908a83be4a644e09656
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655591"
---
# <a name="change-the-sku-for-an-existing-azure-ad-domain-services-managed-domain"></a>Modificare lo SKU per un dominio gestito di Servizi di dominio Azure AD esistenteChange the SKU for an existing Azure AD Domain Services managed domain

In Servizi di dominio Azure Active Directory le prestazioni e le funzionalità disponibili sono basate sul tipo di SKU. Queste differenze di funzionalità includono la frequenza di backup o il numero massimo di trust tra foreste in uscita unidirezionali (attualmente in anteprima). Si seleziona uno SKU quando si crea il dominio gestito ed è possibile passare SKU verso l'alto o verso il basso in quanto le esigenze aziendali cambiano dopo la distribuzione del dominio gestito. Le modifiche ai requisiti aziendali potrebbero includere la necessità di backup più frequenti o di creare ulteriori trust tra foreste. Per altre informazioni sui limiti e sui prezzi dei diversi SKU, vedere Concetti dello SKU di Azure AD DS e Pagine dei prezzi di Azure AD DS.For more information on the limits and pricing of the different SKUs, see [Azure AD DS SKU concepts][concepts-sku] and Azure AD [DS pricing][pricing] pages.

Questo articolo illustra come modificare lo SKU per un dominio gestito di Servizi di dominio Active Directory di Azure esistente usando il portale di Azure.This article shows you how to change the SKU for an existing Azure AD DS managed domain using the Azure portal.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, sono necessarie le risorse e i privilegi seguenti:To complete this article, you need the following resources and privileges:

* Una sottoscrizione di Azure attiva.
    * Se non si dispone di una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, completare l'esercitazione per [creare e configurare un'istanza][create-azure-ad-ds-instance]di Servizi di dominio Azure Active Directory.

## <a name="sku-change-limitations"></a>Limitazioni delle modifiche SKU

È possibile modificare gli SKU verso l'alto o verso il basso dopo la distribuzione del dominio gestito di Servizi di dominio Active Directory di Azure.You can change SKUs up or down after the Azure AD DS managed domain has been deployed. Tuttavia, se si usa una foresta di risorse (attualmente in anteprima) e sono stati creati trust di foresta in uscita unidirezionali da Servizi di dominio Active Directory a un ambiente di Dominio Active Directory locale, esistono alcune limitazioni per l'operazione di modifica dello SKU. Gli SKU *Premium* ed *Enterprise* definiscono un limite al numero di trust che è possibile creare. Non è possibile passare a uno SKU con un limite massimo inferiore a quello attualmente configurato.

Ad esempio:

* Se sono stati creati due trust tra foreste nello SKU *Premium,* non è possibile passare allo SKU *Standard.If* you have created two forest trusts on the Premium SKU, you can't change down to the Standard SKU. Lo SKU *Standard* non supporta i trust tra foreste.
* In alternativa, se sono state create sette relazioni di trust per lo SKU *Premium,* non è possibile passare allo SKU *Enterprise.* Lo SKU *Enterprise* supporta un massimo di cinque trust.

Per altre informazioni su questi limiti, vedere Limiti e caratteristiche dello SKU di [Azure AD DS.][concepts-sku]

## <a name="select-a-new-sku"></a>Selezionare un nuovo SKU

Per modificare lo SKU per un dominio gestito di Servizi di dominio Active Directory di Azure usando il portale di Azure, completare i passaggi seguenti:To change the SKU for an Azure AD DS managed domain using the Azure portal, complete the following steps:

1. Nella parte superiore del portale di Azure cercare e selezionare Servizi di **dominio Azure AD.** Scegliere il dominio gestito dall'elenco, ad esempio *aaddscontoso.com*.
1. Nel menu sul lato sinistro della pagina Servizi di dominio Azure ad Azure selezionare **Impostazioni > SKU**.

    ![Selezionare l'opzione di menu SKU per il dominio gestito di Azure AD DS nel portale di AzureSelect the SKU menu option for your Azure AD DS managed domain in the Azure portal](media/change-sku/overview-change-sku.png)

1. Nel menu a discesa selezionare lo SKU desiderato per il dominio gestito di Azure AD DS. Se si dispone di una foresta di risorse, non è possibile selezionare SKU *Standard* poiché i trust della foresta sono disponibili solo nello SKU *Enterprise* o versione successiva.

    Scegliere lo SKU desiderato dal menu a discesa, quindi selezionare **Salva**.

    ![Scegliere lo SKU richiesto dal menu a discesa nel portale di AzureChoose the required SKU from the drop-down menu in the Azure portal](media/change-sku/change-sku-selection.png)

La modifica del tipo di SKU può richiedere uno o due minuti.

## <a name="next-steps"></a>Passaggi successivi

Se si dispone di una foresta di risorse e si desidera creare ulteriori trust dopo la modifica dello SKU, vedere Creare un trust di foresta in uscita a un dominio locale in Servizi di dominio Active Directory di [Azure (anteprima).][create-trust]

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
