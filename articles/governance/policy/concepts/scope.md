---
title: Comprendere l'ambito nei criteri di Azure
description: Viene descritto il concetto di ambito in Azure Resource Manager e il modo in cui si applica ai criteri di Azure per controllare quali risorse valutano i criteri di Azure.
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 83eda330a35b007abfa37046a202b14728726849
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90984426"
---
# <a name="understand-scope-in-azure-policy"></a>Comprendere l'ambito nei criteri di Azure

Sono disponibili diverse impostazioni che determinano quali risorse sono in grado di valutare e quali risorse vengono valutate dai criteri di Azure. Il concetto principale di questi controlli è l' _ambito_.
Per una panoramica di alto livello, vedere [ambito in Azure Resource Manager](../../../azure-resource-manager/management/overview.md#understand-scope).
Questo articolo illustra l'effetto dell' _ambito_ nei criteri di Azure e le proprietà e gli oggetti correlati.

## <a name="definition-location"></a>Posizione della definizione

Il primo ambito di istanza usato dai criteri di Azure è quando viene creata una definizione di criteri. La definizione può essere salvata in un gruppo di gestione o in una sottoscrizione. Il percorso determina l'ambito a cui è possibile assegnare l'iniziativa o i criteri. Le risorse devono trovarsi all'interno della gerarchia delle risorse della posizione di definizione da usare come destinazione per l'assegnazione.

Se la posizione della definizione è:

- È possibile assegnare la definizione dei criteri alle risorse solo della **sottoscrizione** all'interno di tale sottoscrizione.
- **Gruppo di gestione** : è possibile assegnare la definizione dei criteri solo alle risorse solo nei gruppi di gestione figlio e nelle sottoscrizioni figlio. Se si prevede di applicare la definizione dei criteri a diverse sottoscrizioni, il percorso deve essere un gruppo di gestione che contiene ogni sottoscrizione.

Il percorso deve essere il contenitore di risorse condiviso da tutte le risorse in cui si vuole usare la definizione dei criteri. Questo contenitore di risorse è in genere un gruppo di gestione vicino al gruppo di gestione radice.

## <a name="assignment-scopes"></a>Ambiti di assegnazione

Un'assegnazione dispone di diverse proprietà che impostano un ambito. L'uso di queste proprietà determina la risorsa per la valutazione di criteri di Azure e le risorse che vengono conteggiate per la conformità. Queste proprietà sono mappate ai concetti seguenti:

- Inclusione: una gerarchia di risorse o una singola risorsa deve essere valutata per la conformità con la definizione. La `properties.scope` proprietà di un oggetto di assegnazione determina cosa includere e valutare la conformità. Per altre informazioni, vedere [definizione di assegnazione](./assignment-structure.md).

- Esclusione: una gerarchia di risorse o una singola risorsa non deve essere valutata per la conformità con la definizione. La `properties.notScopes` proprietà _Array_ in un oggetto di assegnazione determina gli elementi da escludere. Le risorse all'interno di questi ambiti non vengono valutate o incluse nel conteggio di conformità. Per altre informazioni, vedere [ambiti esclusi](./assignment-structure.md#excluded-scopes)per la definizione di assegnazione.

Oltre alle proprietà nell'assegnazione dei criteri, è l'oggetto di [esenzione dei criteri](./exemption-structure.md) . Le esenzioni migliorano la storia dell'ambito fornendo un metodo per identificare una parte di un'assegnazione che non viene valutata.

- Esenzione (**disponibile in anteprima** ): una gerarchia di risorse o una singola risorsa deve essere valutata per la conformità dalla definizione, ma non verrà valutata per un motivo come la presenza di una deroga o la mitigazione tramite un altro metodo. Le risorse in questo stato mostrano come **esentate** nei report di conformità in modo che possano essere rilevate. L'oggetto esenzione viene creato nella gerarchia delle risorse o in una singola risorsa come oggetto figlio, che determina l'ambito dell'esenzione. Una gerarchia di risorse o una singola risorsa può essere esentata da più assegnazioni. L'esenzione può essere configurata in modo da scadere in base a una pianificazione utilizzando la `expiresOn` Proprietà. Per altre informazioni, vedere [definizione di esenzione](./exemption-structure.md).

  > [!NOTE]
  > A causa dell'effetto della concessione di un'esenzione per una gerarchia di risorse o una singola risorsa, le esenzioni dispongono di misure di sicurezza aggiuntive. Oltre a richiedere l'operazione alla `Microsoft.Authorization/policyExemptions/write` gerarchia delle risorse o a una singola risorsa, l'autore di un'esenzione deve avere il `exempt/Action` verbo nell'assegnazione di destinazione.

## <a name="scope-comparison"></a>Confronto ambito

Nella tabella seguente è riportato un confronto tra le opzioni di ambito:

| | Inclusione | Esclusione (notScopes) | Esenzione |
|---|:---:|:---:|:---:|
|**Le risorse vengono valutate** | &#10004; | - | - |
|**Oggetto Gestione risorse** | - | - | &#10004; |
|**Richiede la modifica dell'oggetto di assegnazione dei criteri** | &#10004; | &#10004; | - |

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla [struttura delle definizioni dei criteri](./definition-structure.md).
- Informazioni su come [creare criteri a livello di codice](../how-to/programmatically-create.md).
- Leggere le informazioni su come [ottenere dati sulla conformità](../how-to/get-compliance-data.md).
- Informazioni su come [correggere le risorse non conformi](../how-to/remediate-resources.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).