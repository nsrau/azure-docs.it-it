---
title: Area o serie SKU non disponibili-Azure
description: Alcune serie di SKU non sono disponibili per la sottoscrizione selezionata per questa area, che potrebbe richiedere la richiesta di supporto per la gestione delle sottoscrizioni.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: article
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: b0f0762ded6804c0b0d90a19223c082f0fb8fd49
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843635"
---
# <a name="region-or-sku-unavailable"></a>Area o SKU non disponibili

Questo articolo illustra come risolvere il problema di una sottoscrizione di Azure che non ha accesso a un'area o a uno SKU di VM.

## <a name="symptoms"></a>Sintomi

Quando si distribuisce una macchina virtuale, viene visualizzato uno dei messaggi di errore seguenti:

```
Code: SkuNotAvailable
Message: The requested size for resource '<resource>' is currently not available in location 
'<location>' zones '<zone>' for subscription '<subscriptionID>'. Please try another size or 
deploy to a different location or zones. See https://aka.ms/azureskunotavailable for details.
```

```
Message: Your subscription doesn’t support virtual machine creation in <location>. Choose a 
different location. Supported locations are <list of locations>
```

```
Code: NotAvailableForSubscription
Message: This size is currently unavailable in this location for this subscription
```

Quando si acquistano delle istanze di macchine virtuali riservate, viene visualizzato uno dei messaggi di errore seguenti:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

Quando si crea una richiesta di supporto per aumentare la quota di core di calcolo, un'area o una famiglia di SKU non è disponibile per la selezione.

## <a name="solution"></a>Soluzione

Si consiglia innanzitutto di prendere in considerazione un'area o uno SKU alternativi che soddisfino le esigenze aziendali.

Se non si riesce a trovare un'area o uno SKU appropriato, creare una [richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) per la **gestione delle sottoscrizioni** attenendosi alla procedura seguente:

1. Scegliere **Guida e supporto**dal menu [portale di Azure](https://portal.azure.com) . Selezionare quindi **Nuova richiesta di supporto**.

1. In **nozioni di base**, per **tipo di problema**, selezionare **Gestione sottoscrizioni**.

1. Selezionare una **sottoscrizione** e immettere una breve descrizione in **Riepilogo**.

   ![Scheda nozioni di base della nuova richiesta di supporto](./media/SKU-series-unavailable/support-request-basics.png)

1. Per **tipo di problema**, scegliere **Seleziona tipo di problema**.

1. Per **selezionare il tipo di problema**, scegliere un'opzione, ad esempio, non è **possibile accedere alla sottoscrizione o alla risorsa** > **il problema non è elencato sopra**. Selezionare **Salva**.

   ![Specificare un problema per la richiesta](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. Selezionare **Avanti: soluzioni** per esplorare le possibili soluzioni. Se necessario, fare clic su **Avanti: dettagli** per continuare.

1. Immettere le informazioni aggiuntive che è possibile fornire, insieme alle informazioni di contatto.

1. Selezionare **Rivedi e crea**. Dopo aver verificato le informazioni, selezionare **Crea** per creare la richiesta.

## <a name="send-us-your-suggestions"></a>Inviaci i tuoi suggerimenti

Ci siamo sempre aperti a commenti e suggerimenti. È possibile inviare [suggerimenti](https://feedback.azure.com/forums/266794-support-feedback). Inoltre, è possibile interagire con Microsoft su [Twitter](https://twitter.com/azuresupport) o sui [Forum MSDN](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Altre informazioni.

[Domande frequenti relative al supporto tecnico Azure](https://azure.microsoft.com/support/faq)
