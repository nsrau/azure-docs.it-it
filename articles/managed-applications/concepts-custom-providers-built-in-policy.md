---
title: Distribuire le associazioni per il provider personalizzato di Azure usando i criteri
description: Informazioni sulla distribuzione di associazioni per un provider personalizzato con il servizio criteri di Azure.
author: msHich
ms.service: managed-applications
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: 94f7128a473266824242f52a9c50f09b2c24f1d4
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932645"
---
# <a name="deploy-associations-for-a-custom-provider-using-azure-policy"></a>Distribuire le associazioni per un provider personalizzato usando criteri di Azure

I criteri di Azure possono essere usati per distribuire le associazioni per associare le risorse a un provider personalizzato. In questo articolo viene descritto un criterio incorporato che distribuisce le associazioni e come è possibile usare tale criterio.

## <a name="built-in-policy-to-deploy-associations"></a>Criteri predefiniti per distribuire le associazioni

Distribuisci associazioni per un provider personalizzato è un criterio predefinito che può essere usato per distribuire un'associazione per associare una risorsa a un provider personalizzato. Il criterio accetta tre parametri:

- ID provider personalizzato: questo ID è l'ID risorsa del provider personalizzato a cui è necessario associare le risorse.
- Tipi di risorse da associare: questi tipi di risorse sono l'elenco dei tipi di risorse da associare al provider personalizzato. È possibile associare più tipi di risorse a un provider personalizzato utilizzando gli stessi criteri.
- Prefisso nome associazione: questa stringa è il prefisso da aggiungere al nome della risorsa di associazione da creare. Il valore predefinito è "DeployedByPolicy".

Il criterio usa la valutazione DeployIfNotExists. Viene eseguito dopo che un provider di risorse ha gestito una richiesta di creazione o aggiornamento di una risorsa e la valutazione ha restituito un codice di stato di esito positivo. Successivamente, la risorsa di associazione viene distribuita usando una distribuzione modello.
Per altre informazioni sulle associazioni, vedere Caricamento di [risorse dei provider personalizzati di Azure](./concepts-custom-providers-resourceonboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>Come usare i criteri predefiniti Distribuisci associazioni 

### <a name="prerequisites"></a>Prerequisiti
Se il provider personalizzato necessita di autorizzazioni per l'ambito dei criteri per eseguire un'azione, la distribuzione dei criteri della risorsa di associazione non funzionerà senza concedere le autorizzazioni.

### <a name="policy-assignment"></a>Assegnazione di criteri
Per usare i criteri predefiniti, creare un'assegnazione di criteri e assegnare le associazioni Distribuisci per un criterio del provider personalizzato. Il criterio identificherà quindi le risorse non conformi e distribuirà le associazioni per tali risorse.

![Assegnare i criteri predefiniti](media/builtin-policy/assign-builtin-policy-customprovider.png)

## <a name="getting-help"></a>Risorse della Guida

In caso di domande sullo sviluppo di provider di risorse personalizzati di Azure, provare a richiederli [stack overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). È possibile che siano già state fornite risposte a domande simili, quindi verificare prima di inserirle. Aggiungere il tag ```azure-custom-providers``` per ottenere una risposta rapida.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo illustra come usare i criteri predefiniti per distribuire le associazioni. Per ulteriori informazioni, vedere questi articoli:

- [Concetti: onboarding delle risorse del provider personalizzato di Azure](./concepts-custom-providers-resourceonboarding.md)
- [Esercitazione: onboarding delle risorse con provider personalizzati](./tutorial-custom-providers-resource-onboarding.md)
- [Esercitazione: creare azioni e risorse personalizzate in Azure](./tutorial-custom-providers-101.md)
- [Guida introduttiva: creare un provider di risorse personalizzato e distribuire risorse personalizzate](./create-custom-provider.md)
- [Procedura: aggiungere azioni personalizzate a un'API REST di Azure](./custom-providers-action-endpoint-how-to.md)
- [Procedura: aggiungere risorse personalizzate a un'API REST di Azure](./custom-providers-resources-endpoint-how-to.md)
