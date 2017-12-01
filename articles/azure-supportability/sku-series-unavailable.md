---
title: Serie di SKU non disponibile | Microsoft Docs
description: Alcune serie di SKU non sono disponibili per la sottoscrizione selezionata nell'area.
services: Azure Supportability
documentationcenter: 
author: stevendotwang
manager: rajatk
editor: 
ms.service: azure-supportability
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: xingwan
ms.openlocfilehash: 62964d0c5d75168226a35b25e5c256a1b57f3f81
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="region-or-sku-unavailable"></a>Area o SKU non disponibili
Questo articolo illustra come risolvere il problema di una sottoscrizione di Azure che non ha accesso a un'area o a uno SKU di VM.

## <a name="symptoms"></a>Sintomi

### <a name="when-deploying-a-virtual-machine-you-receive-one-of-the-following-error-messages"></a>Quando si distribuisce una macchina virtuale, viene visualizzato uno dei messaggi di errore seguenti:
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

### <a name="when-purchasing-reserved-virtual-machine-instances-you-receive-one-of-the-following-error-messages"></a>Quando si acquistano delle istanze di macchine virtuali riservate, viene visualizzato uno dei messaggi di errore seguenti:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-creating-a-support-request-to-increase-compute-core-quota-a-region-or-a-sku-family-is-not-available-for-selection"></a>Durante la creazione di una richiesta di supporto per aumentare la quota di core di calcolo, un'area o una famiglia di SKU non sono disponibili per la selezione.

## <a name="solution"></a>Soluzione
Si consiglia innanzitutto di prendere in considerazione un'area o uno SKU alternativi che soddisfino le esigenze aziendali. Se non si trovano aree o SKU adatti, creare una [richiesta di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) "Gestione della sottoscrizione" seguendo questa procedura:


- Nella pagina Informazioni di base selezionare "Gestione della sottoscrizione" come tipo di problema, selezionare la sottoscrizione e fare clic su "Avanti".

![Pannello Nozioni di base](./media/SKU-series-unavailable/BasicsSubMgmt.png)


-   Nella pagina Problema selezionare "Other General questions" (Altre domande generiche).
- Nella sezione dei dettagli:
  - Indicare se si stanno distribuendo le macchine virtuali o si stanno acquistando le istanze di macchina virtuale riservate
  - Specificare l'area, lo SKU e il numero di istanze di macchina virtuale che si prevede di distribuire o acquistare


![Problema](./media/SKU-series-unavailable/ProblemSubMgmt.png)

-   Immettere i dati richiesti e fare clic su "Crea".

![Informazioni di contatto](./media/SKU-series-unavailable/ContactInformation.png)

## <a name="feedback"></a>Commenti e suggerimenti
Commenti e suggerimenti sono sempre graditi. È possibile inviare [suggerimenti](https://feedback.azure.com/forums/266794-support-feedback). È possibile inoltre comunicare tramite [Twitter](https://twitter.com/azuresupport) o i [forum MSDN](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Altre informazioni
[Domande frequenti relative al supporto tecnico Azure](https://azure.microsoft.com/support/faq)

