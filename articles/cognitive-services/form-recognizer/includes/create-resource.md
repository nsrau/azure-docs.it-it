---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: cedd302453e75c037f17cc47d8e00a06d3f3a8cd
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996797"
---
Passare al portale di Azure e <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="creare una nuova risorsa di riconoscimento modulo" target="_blank">creare una nuova risorsa di riconoscimento modulo <span class="docon docon-navigate-external x-hidden-focus"></span></a>. Nel riquadro **Crea** specificare le informazioni seguenti:

|    |    |
|--|--|
| **Nome** | Nome per la risorsa. È consigliabile usare un nome descrittivo, ad esempio *MyNameFormRecognizer*. |
| **Sottoscrizione** | Selezionare la sottoscrizione di Azure a cui è stato concesso l'accesso. |
| **Posizione** | Posizione dell'istanza di Servizi cognitivi. Posizioni diverse possono introdurre latenza, ma non hanno alcun impatto sulla disponibilità di runtime della risorsa. |
| **Piano tariffario** | Il costo della risorsa varia a seconda del piano tariffario selezionato e dell'utilizzo. Per altre informazioni, vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/) delle API.
| **Gruppo di risorse** | [Gruppo di risorse di Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) che conterrà la risorsa. È possibile creare un nuovo gruppo o aggiungerla a un gruppo già esistente. |

> [!NOTE]
> In genere, quando si crea una risorsa di Servizi cognitivi nel portale di Azure, si ha la possibilità di creare una chiave di sottoscrizione multiservizio (usata per più servizi cognitivi) o una chiave di sottoscrizione per singolo servizio (usata solo con un determinato servizio cognitivo). L'API di riconoscimento modulo è tuttavia disponibile in versione di anteprima. Non è quindi inclusa nella sottoscrizione multiservizio e non è possibile creare la sottoscrizione per singolo servizio se non tramite il collegamento riportato nel messaggio di posta elettronica di benvenuto.

Quando la distribuzione della risorsa di riconoscimento modulo è completata, individuarla e selezionarla dall'elenco **Tutte le risorse** nel portale. La chiave e l'endpoint saranno disponibili nella pagina relativa della risorsa in Gestione risorse. Salvarli entrambi in un percorso temporaneo prima di procedere.
