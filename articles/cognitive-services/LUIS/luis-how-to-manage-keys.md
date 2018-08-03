---
title: Gestire le chiavi endpoint in LUIS | Microsoft Docs
description: Utilizzare Language Understanding Intelligent Service (LUIS) per gestire API di programmazione, endpoint e chiavi esterne.
titleSuffix: Azure
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/21/2018
ms.author: diberry
ms.openlocfilehash: 127c09a022f5efb95ab6a5ec2db0de633b437a54
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223039"
---
# <a name="manage-your-luis-endpoint-keys"></a>Gestire le chiavi endpoint di LUIS
Una chiave consente di creare e pubblicare l'app LUIS o eseguire una query dell'endpoint. 

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
## <a name="key-concepts"></a>Concetti chiave
Vedere [Chiavi in LUIS](luis-concept-keys.md) per comprendere i concetti chiave di endpoint e creazione in LUIS.

<a name="create-and-use-an-endpoint-key"></a>
## <a name="assign-endpoint-key"></a>Assegnare chiave endpoint
Nella pagina **Publish app** (Pubblica app) è già presente una chiave nella tabella **Resources and Keys** (Risorse e chiavi). Si tratta della chiave di creazione (avvio). 

1. Creare una chiave LUIS sul [portale di Azure](https://portal.azure.com). Per ulteriori istruzioni, vedere [Creazione di una chiave endpoint usando Azure](luis-how-to-azure-subscription.md).
 
2. Per aggiungere la chiave LUIS creata nel passaggio precedente, fare clic su **Add Key** (Aggiungi chiave) per aprire la finestra di dialogo **Assign a key to your app** (Assegna una chiave all'app). 

    ![Assegnare una chiave all'app](./media/luis-manage-keys/assign-key.png)
3. Nella finestra di dialogo selezionare un tenant. 
 
    > [!Note]
    > In Azure un tenant rappresenta l'ID Azure Active Directory del client o dell'organizzazione associata a un servizio. Se in precedenza è stata creata una sottoscrizione di Azure usando il proprio account Microsoft personale, si ha già un tenant. Quando si accede al portale di Azure, si esegue automaticamente l'accesso al [tenant predefinito](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant). È possibile usare questo tenant, ma potrebbe essere necessario dover creare un account amministratore aziendale.

4. Scegliere la sottoscrizione di Azure associata alla chiave LUIS di Azure da aggiungere.

5. Selezionare l'account LUIS di Azure. La regione dell'account viene visualizzata tra parentesi. 

    ![Scegliere la chiave](./media/luis-manage-keys/assign-key-filled-out.png)

6. Dopo aver assegnato questa chiave di endpoint, usarla in tutte le query di endpoint. 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

## <a name="publishing-regions"></a>Regioni di pubblicazione
Ulteriori informazioni sulle [regioni](luis-reference-regions.md) di pubblicazione, inclusa la pubblicazione in [Europa](luis-reference-regions.md#publishing-to-europe) e in [Australia](luis-reference-regions.md#publishing-to-australia). Le regioni di pubblicazione sono diverse dalle regioni di creazione. Verificare di creare un'app nella regione di creazione corrispondente alla regione di pubblicazione desiderata.

## <a name="unassign-key"></a>Annullare l'assegnazione della chiave

* Nell'**elenco di risorse e chiavi**, fare clic sull'icona del cestino accanto all'entità di cui si desidera annullare l'assegnazione. Quindi, fare clic su **OK** nel messaggio di conferma per confermare l'eliminazione.
 
    ![Annullare l'assegnazione dell'entità](./media/luis-manage-keys/unassign-key.png)

> [!NOTE]
> L'annullamento dell'assegnazione della chiave LUIS non la elimina dalla sottoscrizione di Azure.

## <a name="next-steps"></a>Passaggi successivi

Usare la chiave per pubblicare l'app nella pagina **Publish app** (Pubblica app). Per istruzioni sulla pubblicazione, vedere [Pubblicare app](luis-how-to-publish-app.md).