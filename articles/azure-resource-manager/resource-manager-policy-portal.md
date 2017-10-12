---
title: Portale di Azure per i criteri delle risorse | Microsoft Docs
description: "Descrive come usare il portale di Azure per creare e gestire i criteri di Resource Manager. È possibile applicare i criteri alla sottoscrizione o a gruppi di risorse."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: tomfitz
ms.openlocfilehash: 868b2cc1559053057d17b34c03e2e31347f399bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-portal-to-assign-and-manage-resource-policies"></a>Usare il portale di Azure per assegnare e gestire i criteri delle risorse
Il portale di Azure consente di assegnare i criteri delle risorse a gruppi di risorse e sottoscrizioni. L'interfaccia utente rende più semplice la selezione dei criteri da assegnare e la specifica dei valori dei parametri dei criteri per personalizzare le impostazioni dei criteri. 

Per assegnare i criteri tramite il portale è necessario che la definizione dei criteri sia già presente nella sottoscrizione. La sottoscrizione include diverse definizioni di criteri predefiniti che è possibile assegnare a gruppi di risorse o sottoscrizioni. I criteri predefiniti e i criteri personalizzati definiti vengono visualizzati quando si usa il portale per assegnare i criteri. Per un'introduzione ai criteri e alla definizione di criteri personalizzati, vedere [Cenni preliminari sui criteri delle risorse](resource-manager-policy.md).

I criteri vengono ereditati da tutte le risorse figlio. Se quindi un criterio viene applicato a un gruppo di risorse, è applicabile a tutte le risorse in tale gruppo. In questo articolo il termine **ambito** si riferisce al gruppo di risorse o alla sottoscrizione cui vengono assegnati i criteri. 

I criteri vengono valutati durante la creazione e l'aggiornamento delle risorse (operazioni PUT e PATCH).

## <a name="assign-a-policy"></a>Assegnare i criteri

1. Per assegnare i criteri a un gruppo di risorse o a una sottoscrizione, selezionare il gruppo di risorse o la sottoscrizione. Nelle impostazioni selezionare **Criteri**.

   ![selezionare i criteri](./media/resource-manager-policy-portal/select-policies.png)

2. Per creare un'assegnazione di criteri per questo ambito, selezionare **Aggiungi assegnazione**.

   ![aggiungere un'assegnazione](./media/resource-manager-policy-portal/add-assignment.png)

3. Selezionare i criteri che si vuole assegnare. I criteri predefiniti disponibili per l'assegnazione sono visibili anche se non sono state assegnate definizioni dei criteri alla sottoscrizione. I criteri predefiniti sono applicabili a numerosi scenari comuni.

   ![selezionare la definizione](./media/resource-manager-policy-portal/select-definition.png)

4. Dopo la selezione dei criteri, vengono visualizzati una descrizione dei criteri e i parametri per i criteri. Ad esempio, la figura seguente illustra il parametro **Allowed locations** (Località consentite) obbligatorio per i criteri che limitano le località disponibili.

   ![visualizzazione dei parametri](./media/resource-manager-policy-portal/show-parameters.png)

5. Tramite l'interfaccia utente, selezionare i valori da specificare per i parametri dei criteri, ad esempio le località che possono essere usate per la distribuzione.

   ![selezionare i valori dei parametri](./media/resource-manager-policy-portal/select-parameters.png)

6. Specificare i valori per gli altri parametri. L'ambito viene assegnato automaticamente in base al pannello selezionato all'inizio dell'assegnazione dei criteri. Selezionare **OK** al termine dell'operazione.

   ![definire i parametri](./media/resource-manager-policy-portal/define-parameters.png)

  I criteri sono stati assegnati all'ambito specificato.

## <a name="view-policy-assignments"></a>Visualizzare le assegnazioni di criteri

Dopo l'assegnazione, i criteri vengono visualizzati nell'elenco dei criteri per l'ambito. La scheda **Dettagli** visualizza un riepilogo dell'assegnazione dei criteri.

![visualizzare i dettagli](./media/resource-manager-policy-portal/show-details.png)

La scheda **Regola di assegnazione** visualizza il file JSON della definizione dei criteri.

![visualizzare la regola di assegnazione](./media/resource-manager-policy-portal/show-assignment-rule.png)

## <a name="change-an-existing-policy-assignment"></a>Modificare un'assegnazione di criteri esistente

Per modificare i criteri, selezionare **Modifica assegnazione** o **Elimina**

![modificare o eliminare l'assegnazione](./media/resource-manager-policy-portal/edit-delete-policy.png)

## <a name="assign-custom-policies"></a>Assegnare criteri personalizzati

Se sono stati definiti criteri personalizzati nella sottoscrizione, i criteri sono disponibili per l'assegnazione tramite il portale. I criteri personalizzati sono preceduti da **[Personalizzato]**

![criteri personalizzati](./media/resource-manager-policy-portal/show-custom-policy.png)

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulla sintassi JSON per la definizione dei criteri, vedere [Cenni preliminari sui criteri delle risorse](resource-manager-policy.md).
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).
* Lo schema del criterio è pubblicato in [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 

