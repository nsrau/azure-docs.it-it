---
title: Criteri per l'assegnazione di tag alle risorse
description: Descrive i criteri di Azure che è possibile assegnare per garantire la conformità dei tag.
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: c6867bc01306ac3c08a9797ece0567a45e060af2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89293737"
---
# <a name="assign-policies-for-tag-compliance"></a>Assegnare criteri per la conformità dei tag

Usare i [criteri di Azure](../../governance/policy/overview.md) per applicare le regole e le convenzioni per l'assegnazione di tag. La creazione di un criterio consente di evitare lo scenario di distribuzione delle risorse nella sottoscrizione che non dispongono dei tag previsti per l'organizzazione. Anziché applicare manualmente i tag o cercare le risorse non conformi, è necessario creare un criterio che applichi automaticamente i tag necessari durante la distribuzione. I tag possono essere ora applicati anche alle risorse esistenti con il nuovo effetto [modifica](../../governance/policy/concepts/effects.md#modify) e un' [attività di correzione](../../governance/policy/how-to/remediate-resources.md). La sezione seguente illustra i criteri di esempio per i tag.

## <a name="policies"></a>Criteri

[!INCLUDE [Tag policies](../../../includes/policy/reference/bycat/policies-tags.md)]

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'applicazione di tag alle risorse, vedere [Uso dei tag per organizzare le risorse di Azure](tag-resources.md).
* Non tutti i tipi di risorse supportano i tag. Per determinare se è possibile applicare un tag a un tipo di risorsa, vedere [Supporto dei tag per le risorse di Azure](tag-support.md).
