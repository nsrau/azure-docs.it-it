---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: d322aa27c7c9dcf4ad365ec22f8074aeacc85d3b
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502826"
---
Quando viene concesso l'accesso per l'uso del riconoscimento modulo, si riceve un messaggio di posta elettronica di benvenuto con più collegamenti e risorse. Usare il collegamento al portale di Azure nel messaggio per aprire il portale di Azure e creare una risorsa di riconoscimento modulo. Nel riquadro **Crea** specificare le informazioni seguenti:

|    |    |
|--|--|
| **Nome** | Nome per la risorsa. È consigliabile usare un nome descrittivo, ad esempio *MyNameFormRecognizer*. |
| **Sottoscrizione** | Selezionare la sottoscrizione di Azure a cui è stato concesso l'accesso. |
| **Posizione** | Posizione dell'istanza di Servizi cognitivi. Posizioni diverse possono introdurre latenza, ma non hanno alcun impatto sulla disponibilità di runtime della risorsa. |
| **Piano tariffario** | Il costo della risorsa varia a seconda del piano tariffario selezionato e dell'utilizzo. Per altre informazioni, vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/) delle API.
| **Gruppo di risorse** | [Gruppo di risorse di Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) che conterrà la risorsa. È possibile creare un nuovo gruppo o aggiungerla a un gruppo già esistente. |

> [!IMPORTANT]
> In genere, quando si crea una risorsa di Servizi cognitivi nel portale di Azure, si ha la possibilità di creare una chiave di sottoscrizione multiservizio (usata per più servizi cognitivi) o una chiave di sottoscrizione per singolo servizio (usata solo con un determinato servizio cognitivo). L'API di riconoscimento modulo è tuttavia disponibile in versione di anteprima. Non è quindi inclusa nella sottoscrizione multiservizio e non è possibile creare la sottoscrizione per singolo servizio se non tramite il collegamento riportato nel messaggio di posta elettronica di benvenuto.

Quando la distribuzione della risorsa di riconoscimento modulo è completata, individuarla e selezionarla dall'elenco **Tutte le risorse** nel portale. Selezionare quindi la scheda **Chiavi** per visualizzare le chiavi della sottoscrizione. L'app potrà accedere alla risorsa con una delle due chiavi. Copiare il valore di **CHIAVE 1**, che sarà necessario nella sezione successiva.