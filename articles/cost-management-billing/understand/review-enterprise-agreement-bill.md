---
title: Esaminare la fattura del Contratto Enterprise di Azure
description: Informazioni su come leggere e comprendere l'uso e la fattura per i contratti Enterprise di Azure.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: banders
ms.openlocfilehash: 5de6baf81f67ef6bee43b54e7aa0d62088c15be9
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898647"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>Informazioni sulla fattura del contratto Enterprise di Azure

I clienti di Azure con un Contratto Enterprise ricevono una fattura quando superano il credito dell'organizzazione o usano i servizi che non sono coperti dal credito.

Il credito della propria organizzazione include l'impegno monetario. L'impegno monetario è l'importo anticipato pagato dall'organizzazione per l'uso dei servizi di Azure. È possibile aggiungere fondi di impegno monetario al Contratto Enterprise contattando l'account manager o il rivenditore Microsoft.

Questa esercitazione si applica solo ai clienti di Azure con un Contratto Enterprise di Azure.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Controllare gli addebiti fatturati
> * Controllare gli addebiti per l'eccedenza del servizio
> * Controllare la fattura del Marketplace

## <a name="prerequisites"></a>Prerequisiti

Per rivedere e verificare gli addebiti sulla propria fattura, bisogna essere l'amministratore di Enterprise. Per altre informazioni, vedere [Informazioni sui ruoli amministrativi per il Contratto Enterprise di Azure](../manage/understand-ea-roles.md). Se non si sa chi è l'amministratore di Enterprise della propria organizzazione, [contattare l'assistenza](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="review-invoiced-charges-for-most-customers"></a>Controllare gli addebiti fatturati per la maggior parte dei clienti

Questa sezione non si applica ai clienti di Azure in Australia, Giappone o Singapore.

Si riceve una fattura di Azure quando si verifica uno degli eventi seguenti durante il periodo di fatturazione:

- **Eccedenza del servizio**: gli addebiti per l'utilizzo della propria organizzazione superano il saldo del credito.
- **Addebiti fatturati separatamente**: i servizi usati dall'organizzazione non sono coperti dal credito. Si riceve una fattura per i servizi seguenti nonostante il saldo del credito:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Utente registrato
    - Openlogic
    - Utente registrato XenApp Essentials con Diritti di accesso remoto
    - Ubuntu Advantage
    - Visual Studio Enterprise (mensile)
    - Visual Studio Enterprise (annuale)
    - Visual Studio Professional (mensile)
    - Visual Studio Professional (annuale)
- **Addebiti per Azure Marketplace**: gli acquisti e l'utilizzo di Azure Marketplace non sono coperti dal credito della propria organizzazione. Quindi, si riceve una fattura per gli addebiti del Marketplace nonostante il saldo del credito. In Enterprise Portal un amministratore dell'organizzazione può abilitare e disabilitare gli acquisti nel Marketplace.

La fattura visualizzerà prima gli addebiti per l'utilizzo di Azure con i costi associati, seguiti da eventuali addebiti per il Marketplace. Nel caso di un saldo a credito, questo viene applicato all'utilizzo di Azure e la fattura visualizzerà l'utilizzo di Azure e l'utilizzo del Marketplace senza costi rimanenti.

Confrontare l'importo totale combinato mostrato in Enterprise Portal in **Report** > **Riepilogo utilizzo** con la fattura di Azure. Gli importi in **Riepilogo utilizzo** non includono l'imposta.

Accedere ad [Azure EA Portal](https://ea.azure.com). Selezionare quindi **Report**. Nell'angolo superiore destro della scheda passare dalla vista **M** alla **C** e abbinare il periodo alla fattura.  

![Screenshot che mostra l'opzione M + C nel Riepilogo utilizzo.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

L'importo combinato di **Utilizzo totale** e **Azure Marketplace** deve corrispondere all'**Importo totale esteso** della fattura. Per ottenere altre informazioni sugli addebiti, vedere **Scarica utilizzo**.  

![Screenshot che mostra la scheda Scarica utilizzo](./media/review-enterprise-agreement-bill/ea-portal-download-usage.png)

## <a name="review-invoiced-charges-for-other-customers"></a>Controllare gli addebiti fatturati per altri clienti

Questa sezione si applica solo ai clienti di Azure in Australia, Giappone o Singapore.

Si ricevono una o più fatture di Azure quando si verifica uno degli eventi seguenti:

- **Eccedenza del servizio**: gli addebiti per l'utilizzo della propria organizzazione superano il saldo del credito.
- **Addebiti fatturati separatamente**: i servizi usati dall'organizzazione non sono coperti dal credito. Si riceve una fattura per i servizi seguenti:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Utente registrato
    - Openlogic
    - Utente registrato XenApp Essentials con Diritti di accesso remoto
    - Ubuntu Advantage
    - Visual Studio Enterprise (mensile)
    - Visual Studio Enterprise (annuale)
    - Visual Studio Professional (mensile)
    - Visual Studio Professional (annuale)
- **Addebiti per Azure Marketplace**: gli acquisti e l'utilizzo di Azure Marketplace non sono coperti dal credito della propria organizzazione e vengono fatturati separatamente. In Enterprise Portal un amministratore dell'organizzazione può abilitare e disabilitare gli acquisti nel Marketplace.

Se durante il periodo di fatturazione vengono fatturati separatamente l'eccedenza di servizio e gli addebiti, si riceve un'unica fattura che include entrambi i tipi di addebiti. Gli addebiti di Marketplace vengono sempre fatturati separatamente.

## <a name="review-service-overage-charges-for-other-customers"></a>Controllare gli addebiti per l'eccedenza del servizio per altri clienti

Questa sezione si applica solo se si risiede in Australia, Giappone o Singapore.

Confrontare l'importo totale dell'utilizzo in Enterprise Portal in **Report** > **Riepilogo utilizzo** con la fattura di eccedenza del servizio. La fattura di eccedenza del servizio include l'uso che supera il credito della propria organizzazione e/o i servizi che non sono coperti dal credito. Gli importi nel **Riepilogo uso** non includono l'imposta.

Accedere ad [Azure EA Portal](https://ea.azure.com) e quindi selezionare **Report**. Nell'angolo superiore destro della scheda passare dalla vista **M** alla **C** e abbinare il periodo alla fattura.  

![Screenshot che mostra l'opzione M + C nel Riepilogo utilizzo.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

L'importo dell'**Uso totale** deve corrispondere all'**Importo totale** esteso nella fattura di eccedenza del servizio. Per ottenere altre informazioni sui propri addebiti, visitare **Scarica uso** > **Download del report avanzato**. Questo report non include le imposte o gli addebiti per le prenotazioni o gli addebiti del marketplace.  

![Screenshot che mostra il Download del report avanzato nella scheda Scarica uso.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-advanced.png)

La tabella seguente elenca i termini e le descrizioni mostrate nella fattura e sul **Riepilogo uso** nel portale Enterprise:

|Termine di fatturazione|Termine Riepilogo uso|Descrizione|
|---|---|---|
|Importo totale esteso|Uso totale|L'addebito di uso totale al lordo delle imposte per il periodo specifico prima che venga applicato il credito.|
|Uso dell'impegno|Uso dell'impegno|Il credito applicato durante tale periodo specifico.|
|Vendita totale|Eccedenza totale|L'addebito di uso totale che supera l'importo del credito. Questa quantità non include l'imposta.|
|Importo imposta|Non applicabile|L'imposta che si applica all'importo di vendita totale per il periodo specifico.|
|Importo totale|Non applicabile|L'importo dovuto per la fattura dopo che viene applicato il credito e viene aggiunta l'imposta.|

### <a name="review-marketplace-invoice"></a>Controllare la fattura del Marketplace

Questa sezione si applica solo se si risiede in Australia, Giappone o Singapore.

Confrontare il totale di Azure Marketplace in **Report** > **Riepilogo uso** nel portale Enterprise con la fattura di marketplace. La fattura di marketplace è solo per gli acquisti e l'uso di Azure Marketplace. Gli importi indicati in **Riepilogo utilizzo** includono già un'imposta, determinata dall'editore.

Accedere a [Enterprise Portal](https://ea.azure.com) e quindi selezionare **Report**. Nell'angolo superiore destro della scheda passare dalla vista **M** alla **C** e abbinare il periodo alla fattura.  

![Screenshot che mostra l'opzione M + C nel Riepilogo uso.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)  

Il totale **Azure Marketplace** deve corrispondere alla **Vendita totale** nella fattura di marketplace. Per ottenere altre informazioni sui propri addebiti basati sull'uso, visitare **Scarica uso**. Sotto **Addebiti Marketplace**, selezionare **Download**. Il prezzo del Marketplace include un'imposta determinata dall'editore. I clienti non riceveranno una fattura separata dall'editore per l'imposta sulla transazione.

![Screenshot che mostra l'opzione di download sotto gli addebiti di Marketplace.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-marketplace.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Controllare gli addebiti fatturati
> * Controllare gli addebiti per l'eccedenza del servizio
> * Controllare la fattura del Marketplace

Passare all'articolo successivo per altre informazioni sull'uso di Azure EA Portal.

> [!div class="nextstepaction"]
> [Introduzione ad Azure EA Portal](../manage/ea-portal-get-started.md)
