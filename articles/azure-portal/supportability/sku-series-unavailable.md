---
title: Area o serie di SKU non disponibili - Azure
description: Alcune serie di SKU non sono disponibili per la sottoscrizione selezionata per questa area, per cui potrebbe essere necessario inviare una richiesta di supporto per la gestione della sottoscrizione.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: troubleshooting
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: 87533b2e0c1397d0b8b2e9a89450a80a75c5e348
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84763823"
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

Durante la creazione di una richiesta di supporto per aumentare la quota di core di calcolo, un'area o una famiglia di SKU non sono disponibili per la selezione.

## <a name="solution"></a>Soluzione

Si consiglia innanzitutto di prendere in considerazione un'area o uno SKU alternativi che soddisfino le esigenze aziendali.

Se non si trovano aree o SKU adatti, creare una **richiesta di supporto** per la [gestione della sottoscrizione](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) seguendo questa procedura:

1. Nel menu del [portale di Azure](https://portal.azure.com) selezionare **Guida e supporto**. Selezionare quindi **Nuova richiesta di supporto**.

1. In **Informazioni di base** per **Tipo di problema** selezionare **Gestione delle sottoscrizioni**.

1. Selezionare una **Sottoscrizione** e immettere una breve descrizione in **Riepilogo**.

   ![Scheda Informazioni di base della nuova richiesta di supporto](./media/SKU-series-unavailable/support-request-basics.png)

1. Per **Tipo di problema** scegliere **Selezionare il tipo di problema**.

1. Per **Selezionare il tipo di problema** scegliere un'opzione, ad esempio **Non è possibile accedere alla sottoscrizione o alla risorsa** > **Il problema non è elencato sopra**. Selezionare **Salva**.

   ![Specificare un problema per la richiesta](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. Selezionare **Avanti: Soluzioni** per esplorare le possibili soluzioni. Se necessario, selezionare **Avanti: Dettagli** per continuare.

1. Immettere le informazioni aggiuntive che è possibile fornire, insieme alle informazioni di contatto.

1. Selezionare **Rivedi e crea**. Dopo aver verificato le informazioni, selezionare **Crea** per creare la richiesta.

## <a name="send-us-your-suggestions"></a>Inviare suggerimenti

Commenti e suggerimenti sono sempre graditi. È possibile inviare [suggerimenti](https://feedback.azure.com/forums/266794-support-feedback). Inoltre, è possibile con Microsoft su [Twitter](https://twitter.com/azuresupport) o sulla [pagina delle domande di Domande e risposte Microsoft](https://docs.microsoft.com/answers/products/azure).

## <a name="learn-more"></a>Altre informazioni

[Domande frequenti relative al supporto tecnico Azure](https://azure.microsoft.com/support/faq)
