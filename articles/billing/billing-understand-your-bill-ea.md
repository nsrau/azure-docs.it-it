---
title: Informazioni sulla fattura Enterprise di Azure
description: Informazioni su come leggere e comprendere l'uso e la fattura per i contratti Enterprise di Azure.
author: adpick
manager: dougeby
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: f89d7935af10ebb477125c598a01278a4ceef0bd
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326934"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>Informazioni sulla fattura del contratto Enterprise di Azure

I clienti di Azure con un Contratto Enterprise ricevono una fattura quando superano il credito dell'organizzazione o usano i servizi che non sono coperti dal credito.

Il credito della propria organizzazione include l'impegno monetario. L'impegno monetario è l'importo anticipato pagato dall'organizzazione per l'uso dei servizi di Azure. È possibile aggiungere fondi di impegno monetario al Contratto Enterprise contattando l'account manager o il rivenditore Microsoft.  

## <a name="invoices-for-most-customers"></a>Fatture per la maggior parte dei clienti

Questa sezione non si applica ai clienti di Azure in Australia, Giappone o Singapore. Se ci si trova in uno di questi paesi/aree geografiche, vedere [Fatture per altri clienti](#invoices-for-other-customers).

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

## <a name="review-charges-for-most-customers"></a>Controllare gli addebiti per la maggior parte dei clienti
Questa sezione non si applica ai clienti di Azure in Australia, Giappone o Singapore. Se ci si trova in uno di questi paesi, vedere [Controllare gli addebiti per altri clienti](#review-charges-for-other-customers).

Per rivedere e verificare gli addebiti sulla propria fattura, bisogna essere l'amministratore di Enterprise. Per altre informazioni, consultare [Informazioni sui ruoli amministrativi del Contratto Enterprise di Azure in Azure](billing-understand-ea-roles.md). Se non si sa chi è l'amministratore di Enterprise della propria organizzazione, [contattare l'assistenza](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

La fattura mostra tutto l'utilizzo di Azure, seguito da eventuali addebiti del Marketplace. Se si dispone di un saldo dei crediti, questo viene applicato all'utilizzo di Azure.

Confrontare l'importo totale combinato mostrato in Enterprise Portal in **Report** > **Riepilogo utilizzo** con la fattura di Azure. Gli importi in **Riepilogo utilizzo** non includono l'imposta.

1. Accedere al [Portale Enterprise](https://ea.azure.com).
1. Selezionare **Report**.
1. Nell'angolo superiore destro della scheda passare dalla vista **M** alla **C** e abbinare il periodo alla fattura.  
    ![Screenshot che mostra l'opzione M + C nel Riepilogo utilizzo](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png).
1. L'importo combinato di **Utilizzo totale** e **Azure Marketplace** deve corrispondere all'**Importo totale esteso** della fattura.
1. Per ottenere altre informazioni sugli addebiti, vedere **Scarica utilizzo**.  
    ![Screenshot che mostra la scheda Scarica utilizzo](./media/billing-understand-your-bill-ea/ea-portal-download-usage.png)

## <a name="invoices-for-other-customers"></a>Fatture per altri clienti

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

## <a name="review-charges-for-other-customers"></a>Controllare gli addebiti per altri clienti

Questa sezione si applica solo se si risiede in Australia, Giappone o Singapore.

Per rivedere e verificare gli addebiti sulla propria fattura, bisogna essere l'amministratore di Enterprise. Per altre informazioni, consultare [Informazioni sui ruoli amministrativi del Contratto Enterprise di Azure in Azure](billing-understand-ea-roles.md). Se non si sa chi è l'amministratore di Enterprise della propria organizzazione, [contattare l'assistenza](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="review-service-overage-invoice"></a>Fattura per l'eccedenza del servizio di revisione

Confrontare l'importo totale dell'utilizzo in Enterprise Portal in **Report** > **Riepilogo utilizzo** con la fattura di eccedenza del servizio. La fattura di eccedenza del servizio include l'uso che supera il credito della propria organizzazione e/o i servizi che non sono coperti dal credito. Gli importi nel **Riepilogo uso** non includono l'imposta.

1. Accedere a [Enterprise Portal](https://ea.azure.com).
1. Selezionare **Report**.
1. Nell'angolo superiore destro della scheda passare dalla vista **M** alla **C** e abbinare il periodo alla fattura.  
    ![Screenshot che mostra l'opzione M + C nel Riepilogo utilizzo](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png).
1. L'importo dell'**Uso totale** deve corrispondere all'**Importo totale** esteso nella fattura di eccedenza del servizio.  
1. Per ottenere altre informazioni sui propri addebiti, visitare **Scarica uso** > **Download del report avanzato**. Questo report non include le imposte o gli addebiti per le prenotazioni o gli addebiti del marketplace.  
      ![Screenshot che mostra il download report avanzato nella scheda Scarica utilizzo.](./media/billing-understand-your-bill-ea/ea-portal-download-usage-advanced.png)

La tabella seguente elenca i termini e le descrizioni mostrate nella fattura e sul **Riepilogo uso** nel portale Enterprise:

|Termine di fatturazione|Termine Riepilogo uso|DESCRIZIONE|
|---|---|---|
|Importo totale esteso|Uso totale|L'addebito di uso totale al lordo delle imposte per il periodo specifico prima che venga applicato il credito.|
|Uso dell'impegno|Uso dell'impegno|Il credito applicato durante tale periodo specifico.|
|Vendita totale|Eccedenza totale|L'addebito di uso totale che supera l'importo del credito. Questa quantità non include l'imposta.|
|Importo imposta|Non applicabile|L'imposta che si applica all'importo di vendita totale per il periodo specifico.|
|Importo totale|Non applicabile|L'importo dovuto per la fattura dopo che viene applicato il credito e viene aggiunta l'imposta.|

### <a name="marketplace-invoice"></a>Fattura del Marketplace

Questa sezione si applica solo se si risiede in Australia, Giappone o Singapore.

Confrontare il totale di Azure Marketplace in **Report** > **Riepilogo uso** nel portale Enterprise con la fattura di marketplace. La fattura di marketplace è solo per gli acquisti e l'uso di Azure Marketplace. Gli importi nel **Riepilogo uso** non includono l'imposta.

1. Accedere a [Enterprise Portal](https://ea.azure.com).
1. Selezionare **Report**.
1. Nell'angolo superiore destro della scheda passare dalla vista **M** alla **C** e abbinare il periodo alla fattura.  
     ![Screenshot che mostra l'opzione M + C nel Riepilogo utilizzo.](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)  
1. Il totale **Azure Marketplace** deve corrispondere alla **Vendita totale** nella fattura di marketplace.
1. Per ottenere altre informazioni sui propri addebiti basati sull'uso, visitare **Scarica uso**. Sotto **Addebiti Marketplace**, selezionare **Download**. Questo report non include le imposte né mostra gli acquisti una tantum.  
     ![Screenshot che mostra l'opzione di download per gli addebiti del Marketplace.](./media/billing-understand-your-bill-ea/ea-portal-download-usage-marketplace.png)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi
- [Visualizzare e scaricare il file relativo all'utilizzo e agli addebiti di Azure](billing-download-azure-daily-usage.md)
