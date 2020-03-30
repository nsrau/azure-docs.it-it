---
title: Criteri per l'assegnazione di tag alle risorse
description: Descrive i criteri di Azure che è possibile assegnare per garantire la conformità dei tag.
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: e7febe4c833cefd0ee9a5c49b4b70f4901fea8d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80147018"
---
# <a name="assign-policies-for-tag-compliance"></a>Assegnare criteri per la conformità dei tagAssign policies for tag compliance

Usare [Criteri](../../governance/policy/overview.md) di Azure per applicare regole e convenzioni di tagging. Creando un criterio, si evita lo scenario di distribuzione delle risorse nella sottoscrizione che non dispongono dei tag previsti per l'organizzazione. Anziché applicare manualmente i tag o cercare risorse non conformi, si crea un criterio che applica automaticamente i tag necessari durante la distribuzione. I tag possono ora essere applicati anche alle risorse esistenti con il nuovo effetto [Modifica](../../governance/policy/concepts/effects.md#modify) e [un'attività](../../governance/policy/how-to/remediate-resources.md)di correzione. La sezione seguente illustra i criteri di esempio per i tag.

## <a name="policies"></a>Criteri

[!INCLUDE [Tag policies](../../../includes/azure-policy-samples-policies-tags.md)]

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'applicazione di tag alle risorse, vedere [Uso dei tag per organizzare le risorse di Azure](tag-resources.md).
* Non tutti i tipi di risorse supportano i tag. Per determinare se è possibile applicare un tag a un tipo di risorsa, vedere [Supporto dei tag per le risorse di Azure](tag-support.md).
