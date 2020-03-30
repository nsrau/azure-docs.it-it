---
title: Distribuire associazioni tramite criteriDeploy associations using policies
description: Informazioni sulla distribuzione di associazioni per un provider personalizzato usando il servizio Criteri di Azure.Learn about deploying associations for a custom provider using Azure Policy service.
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: 536b95eb07619d0ce2d02ec01e1f51ed52c1b5e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650474"
---
# <a name="deploy-associations-for-a-custom-provider-using-azure-policy"></a>Distribuire associazioni per un provider personalizzato usando i criteri di AzureDeploy associations for a custom provider using Azure policy

I criteri di Azure possono essere usati per distribuire associazioni per associare risorse a un provider personalizzato. In questo articolo viene descritto un criterio predefinito che distribuisce le associazioni e come è possibile utilizzare tale criterio.

## <a name="built-in-policy-to-deploy-associations"></a>Criteri predefiniti per la distribuzione delle associazioni

Distribuire associazioni per un provider personalizzato è un criterio predefinito che può essere usato per distribuire un'associazione per associare una risorsa a un provider personalizzato. Il criterio accetta tre parametri:

- ID provider personalizzato: questo ID è l'ID risorsa del provider personalizzato a cui devono essere associate le risorse.
- Tipi di risorse da associare: questi tipi di risorse sono l'elenco dei tipi di risorse da associare al provider personalizzato. È possibile associare più tipi di risorse a un provider personalizzato usando gli stessi criteri.
- Prefisso nome associazione: stringa è il prefisso da aggiungere al nome della risorsa di associazione da creare. Il valore predefinito è "DeployedByPolicy".

Il criterio utilizza la valutazione DeployIfNotExists.The policy uses DeployIfNotExists evaluation. Viene eseguito dopo che un provider di risorse ha gestito una richiesta di risorsa di creazione o aggiornamento e la valutazione ha restituito un codice di stato di esito positivo. Successivamente, la risorsa di associazione viene distribuita usando una distribuzione di modelli.
Per altre informazioni sulle associazioni, vedere [Onboarding delle risorse di Provider personalizzati di AzureFor](./concepts-resource-onboarding.md) more information on associations, see Azure Custom Providers resource onboarding

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>Come utilizzare i criteri predefiniti delle associazioni di distribuzioneHow to use the deploy associations built-in policy 

### <a name="prerequisites"></a>Prerequisiti
Se il provider personalizzato necessita di autorizzazioni per l'ambito del criterio per eseguire un'azione, la distribuzione dei criteri della risorsa di associazione non funzionerebbe senza concedere le autorizzazioni.

### <a name="policy-assignment"></a>Assegnazione di criteri
Per utilizzare il criterio predefinito, creare un'assegnazione di criteri e assegnare le associazioni Deploy per un criterio provider personalizzato. Il criterio identificherà quindi le risorse non conformi e distribuirà l'associazione per tali risorse.

![Assegnare criteri predefinitiAssign built-in policy](media/concepts-built-in-policy/assign-builtin-policy-customprovider.png)

## <a name="getting-help"></a>Risorse della Guida

In caso di domande sullo sviluppo di provider di risorse personalizzati di Azure, provare a chiedere loro [sull'overflow dello stack](https://stackoverflow.com/questions/tagged/azure-custom-providers). È possibile che siano già state fornite risposte a domande simili, quindi verificare prima di inserirle. Aggiungere il tag ```azure-custom-providers``` per ottenere una risposta rapida.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono apprese informazioni sull'uso dei criteri predefiniti per distribuire le associazioni. Per ulteriori informazioni, consulta questi articoli:

- [Concetti: Onboarding delle risorse dei provider personalizzati di Azure](./concepts-resource-onboarding.md)
- [Esercitazione: Onboarding delle risorse con provider personalizzatiTutorial: Resource onboarding with custom providers](./tutorial-resource-onboarding.md)
- [Esercitazione: Creare azioni e risorse personalizzate in AzureTutorial: Create custom actions and resources in Azure](./tutorial-get-started-with-custom-providers.md)
- [Guida introduttiva: Creare un provider di risorse personalizzato e distribuire risorse personalizzate](./create-custom-provider.md)
- [Procedura: Aggiungere azioni personalizzate a un'API REST di AzureHow to: Adding custom actions to an Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Procedura: Aggiungere risorse personalizzate a un'API REST di AzureHow to: Adding custom resources to an Azure REST API](./custom-providers-resources-endpoint-how-to.md)
