---
title: Regione o serie SKU non disponibile - AzureRegion or SKU series unavailable - Azure
description: Alcune serie di SKU non sono disponibili per la sottoscrizione selezionata per questa area, che potrebbe richiedere una richiesta di supporto per la gestione delle sottoscrizioni.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: article
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: b0f0762ded6804c0b0d90a19223c082f0fb8fd49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
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

Quando si crea una richiesta di supporto per aumentare la quota core di calcolo, un'area o una famiglia di SKU non è disponibile per la selezione.

## <a name="solution"></a>Soluzione

Si consiglia innanzitutto di prendere in considerazione un'area o uno SKU alternativi che soddisfino le esigenze aziendali.

Se non si riesce a trovare un'area o uno SKU adatto, creare una richiesta di supporto per la gestione delle sottoscrizioni seguendo questi passaggi:If you're unable to find a suitable region or SKU, create a **Subscription management** support [request](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) following these steps:

1. Dal menu del portale di [Azure,](https://portal.azure.com) selezionare **Guida e supporto**. Selezionare quindi **Nuova richiesta di supporto**.

1. In **Nozioni di base**, per Tipo di **problema**, selezionare **Gestione sottoscrizioni**.

1. Selezionare un **abbonamento** e immettere una breve descrizione in **Riepilogo**.

   ![Scheda Nozioni di base della nuova richiesta di supporto](./media/SKU-series-unavailable/support-request-basics.png)

1. Per **Tipo di problema**scegliere Seleziona tipo di **problema**.

1. Per **Seleziona tipo di problema**scegliere un'opzione, ad esempio **Impossibile accedere all'abbonamento o alla risorsa** > **Il problema non è elencato in precedenza.** Selezionare **Salva**.

   ![Specificare un problema per la richiesta](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. Selezionare **Avanti: Soluzioni** per esplorare le possibili soluzioni. Se necessario, selezionare **Avanti: Dettagli** per continuare.

1. Inserisci eventuali informazioni aggiuntive che puoi fornire, insieme alle tue informazioni di contatto.

1. Selezionare **Revisione e creazione**. Dopo aver verificato le informazioni, selezionare **Crea** per creare la richiesta.

## <a name="send-us-your-suggestions"></a>Inviaci i tuoi suggerimenti

Siamo sempre aperti a feedback e suggerimenti! È possibile inviare [suggerimenti](https://feedback.azure.com/forums/266794-support-feedback). Inoltre, è possibile interagire con noi su [Twitter](https://twitter.com/azuresupport) o sui [forum MSDN](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Altre informazioni

[Domande frequenti relative al supporto tecnico Azure](https://azure.microsoft.com/support/faq)
