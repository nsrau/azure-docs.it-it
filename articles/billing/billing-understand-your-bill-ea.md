---
title: Informazioni sulla fattura per Azure Enterprise | Microsoft Docs
description: Informazioni su come leggere e comprendere l'uso e la fattura per i clienti di Azure con un Contratto Enterprise
services: ''
documentationcenter: ''
author: adpick
manager: dougeby
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2018
ms.author: banders
ms.openlocfilehash: 16feb41707e5afa07054d20b620f5822eedad773
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57858913"
---
# <a name="understand-your-bill-for-azure-customers-with-an-enterprise-agreement"></a>Informazioni sulla fattura per i clienti di Azure con Contratto Enterprise

I clienti di Azure con un Contratto Enterprise ricevono una fattura quando superano il credito dell'organizzazione o usano i servizi che non sono coperti dal credito. 

Il credito della propria organizzazione include l'impegno monetario. L'impegno monetario è l'importo anticipato pagato dall'organizzazione per l'uso dei servizi di Azure. È possibile aggiungere fondi di impegno monetario al Contratto Enterprise contattando l'account manager o il rivenditore Microsoft.  

## <a name="when-credit-exceeded-or-doesnt-apply"></a>Quando il credito viene superato o non è applicabile

Si ricevono una o più fatture quando si verifica quanto segue:

- **Eccedenza del servizio**: gli addebiti per l'utilizzo della propria organizzazione superano il saldo del credito.
- **Addebiti fatturati separatamente**: i servizi usati dall'organizzazione non sono coperti dal credito. Si riceve una fattura per i servizi seguenti indipendentemente dal saldo del credito:
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
- **Addebiti per Azure Marketplace**: gli acquisti e l'utilizzo di Azure Marketplace non sono coperti dal credito della propria organizzazione e vengono fatturati separatamente. L'Amministratore dell'organizzazione ha la facoltà di abilitare e disabilitare gli acquisti su Marketplace per la propria organizzazione nell'Enterprise Portal. 

Se durante il periodo di fatturazione vengono fatturati separatamente sia l'eccedenza di servizio sia gli addebiti, si riceve un'unica fattura che include entrambi i tipi di addebiti. Gli addebiti di Marketplace vengono sempre fatturati separatamente.

## <a name="review-charges"></a>Addebiti di revisione

Per rivedere e verificare gli addebiti sulla propria fattura, bisogna essere l'amministratore di Enterprise. Per altre informazioni, consultare [Informazioni sui ruoli amministrativi del Contratto Enterprise di Azure in Azure](billing-understand-ea-roles.md). Se non si sa chi è l'amministratore di Enterprise della propria organizzazione, [contattare l'assistenza](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="review-service-overage-invoice"></a>Fattura per l'eccedenza del servizio di revisione

Confrontare l'importo totale dell'uso dal portale Enterprise in **Report** > **Riepilogo uso** con la fattura di eccedenza del servizio. La fattura di eccedenza del servizio include l'uso che supera il credito della propria organizzazione e/o i servizi che non sono coperti dal credito. Gli importi nel **Riepilogo uso** non includono l'imposta. 

1. Accedere al [Portale Enterprise](https://ea.azure.com).
1. Selezionare **Report**.
1. Nell'angolo superiore destro della scheda, passare dalla vista **M** alla **C** e abbinare il periodo alla fattura.
 
   ![Screenshot che mostra l'opzione M + C nel Riepilogo uso.](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)

1. L'importo dell'**Uso totale** deve corrispondere all'**Importo totale** esteso nella fattura di eccedenza del servizio. La tabella seguente elenca i termini e le descrizioni mostrate nella fattura e sul **Riepilogo uso** nel portale Enterprise:

   |Termine di fatturazione|Termine Riepilogo uso|DESCRIZIONE|
   |---|---|---|
   |Importo totale esteso|Uso totale|L'addebito di uso totale al lordo delle imposte per il periodo specifico prima che venga applicato il credito.|
   |Uso dell'impegno|Uso dell'impegno|Il credito applicato durante tale periodo specifico.|
   |Vendita totale|Eccedenza totale|L'addebito di uso totale che supera l'importo del credito. Questa quantità non include l'imposta.|
   |Importo imposta|Non applicabile|L'imposta che si applica all'importo di vendita totale per il periodo specifico.|
   |Importo totale|Non applicabile|L'importo dovuto per la fattura dopo che viene applicato il credito e viene aggiunta l'imposta.|
1. Per ottenere altre informazioni sui propri addebiti, visitare **Scarica uso** > **Download del report avanzato**. Questo report non include le imposte o gli addebiti per le prenotazioni o gli addebiti di marketplace.

      ![Screenshot che mostra il Download del report avanzato nella scheda Scarica uso.](./media/billing-understand-your-bill-ea/ea-portal-download-usage-advanced.png)

### <a name="review-marketplace-invoice"></a>Revisione fattura di marketplace

Confrontare il totale di Azure Marketplace in **Report** > **Riepilogo uso** nel portale Enterprise con la fattura di marketplace. La fattura di marketplace è solo per gli acquisti e l'uso di Azure Marketplace. Gli importi nel **Riepilogo uso** non includono l'imposta. 

1. Accedere al [Portale Enterprise](https://ea.azure.com).
1. Selezionare **Report**.
1. Nell'angolo superiore destro della scheda, passare dalla vista **M** alla **C** e abbinare il periodo alla fattura.

     ![Screenshot che mostra l'opzione M + C nel Riepilogo uso.](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)

1. Il totale **Azure Marketplace** deve corrispondere alla **Vendita totale** nella fattura di marketplace.
1. Per ottenere altre informazioni sui propri addebiti basati sull'uso, visitare **Scarica uso**. Sotto **Addebiti Marketplace**, selezionare **Download**. Questo report non include le imposte né mostra gli acquisti una tantum.

     ![Screenshot che mostra l'opzione di download sotto gli addebiti di Marketplace.](./media/billing-understand-your-bill-ea/ea-portal-download-usage-marketplace.png)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Se si hanno domande o assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).
