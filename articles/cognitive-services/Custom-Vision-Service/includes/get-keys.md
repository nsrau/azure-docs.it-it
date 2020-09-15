---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 874d76bebdfd3bd0daba1f83cb1f06c093f192ec
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89411804"
---
## <a name="get-the-training-and-prediction-keys"></a>Ottenere le chiavi di training e di stima

Il progetto richiede un set valido di chiavi di sottoscrizione per interagire con il servizio. È possibile trovare gli elementi nel [sito Web di Visione personalizzata](https://customvision.ai). Accedere con l'account associato all'account Azure usato per creare le risorse di Visione personalizzata. Nella home page (la pagina con l'opzione per l'aggiunta di un nuovo progetto) selezionare l'__icona dell'ingranaggio__ in alto a destra. Trovare le risorse di training e di previsione nell'elenco ed espanderle. Qui è possibile trovare i valori della chiave di training, della chiave di previsione e dell'ID della risorsa di previsione. Salvare questi valori in una posizione temporanea.

> [!NOTE]
> Se si usa una chiave universale di Servizi cognitivi per accedere a Visione personalizzata, nella schermata di impostazioni verrà visualizzata una sola chiave. In questo caso, si userà la stessa chiave per le operazioni di training e previsione.

![Immagine dell'interfaccia utente delle chiavi](../media/csharp-tutorial/training-prediction-keys.png)

In alternativa, è possibile ottenere le chiavi e l'ID dal [portale di Azure](https://www.portal.azure.com) visualizzando le risorse Training e Previsioni di Visione personalizzata ed esaminando la scheda __Chiavi__. Qui è possibile trovare la chiave di training e la chiave di previsione. Passare alla scheda __Proprietà__ della risorsa di previsione per ottenere l'ID risorsa di previsione.

