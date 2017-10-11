---
title: "Usare Automazione di Azure per applicare la scalabilità verticale alle macchine virtuali di Windows | Microsoft Docs"
description: "Applicare la scalabilità verticale a una macchina virtuale Windows in risposta agli avvisi di monitoraggio con Automazione di Azure"
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4f964713-fb67-4bcc-8246-3431452ddf7d
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/29/2016
ms.author: kasing
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ea5169c1a95f00e78ae3f5f177812466eb7a0deb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="vertically-scale-windows-vms-with-azure-automation"></a>Applicare la scalabilità verticale a macchine virtuali di Windows con Automazione di Azure

La scalabilità verticale è il processo di aumento o riduzione delle risorse di una macchina in risposta al carico di lavoro. In Azure tale operazione può essere eseguita modificando le dimensioni della macchina virtuale. Può essere utile negli scenari seguenti:

* Se la macchina virtuale non viene usata di frequente, è possibile diminuirne le dimensioni per ridurre i costi mensili
* Se nella macchina virtuale si osserva un picco di carico, è possibile aumentarne le dimensioni per una maggiore capacità

Per eseguire questa operazione, seguire questa procedura:

1. Configurare Automazione di Azure per l'accesso alle macchine virtuali
2. Importare i runbook di scalabilità verticale di Automazione di Azure nella sottoscrizione
3. Aggiungere un webhook al runbook
4. Aggiungere un avviso alla macchina virtuale

> [!NOTE]
> A causa delle dimensioni della prima macchina virtuale, le dimensioni a cui la macchina può essere ridimensionata possono essere limitate a seconda della disponibilità di altre dimensioni nel cluster in cui viene distribuita la macchina virtuale corrente. Nei runbook di automazione pubblicati usati in questo articolo viene considerato questo caso e la scalabilità viene applicata solo all'interno delle coppie di dimensioni delle macchine virtuali seguenti. Pertanto, una macchina virtuale Standard_D1v2 non verrà improvvisamente ridimensionata verso l'alto a una Standard_G5 o verso il basso a una Basic_A0.
> 
> | coppie di ridimensionamento di dimensioni delle macchine virtuali |  |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_D1 |Standard_D4 |
> | Standard_D11 |Standard_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1v2 |Standard_D5v2 |
> | Standard_D11v2 |Standard_D14v2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> 
> 

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Configurare Automazione di Azure per l'accesso alle macchine virtuali
La prima operazione da eseguire è creare l'account di Automazione di Azure che ospiterà i runbook usati per ridimensionare una macchina virtuale. Il servizio Automazione ha introdotto di recente la funzionalità "Account RunAs", che semplifica molto l'impostazione dell'entità servizio per l'esecuzione automatica di runbook per conto dell'utente. Altre informazioni sono disponibili nell'articolo seguente.

* [Autenticare runbook con account RunAs di Azure](../../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importare i runbook di scalabilità verticale di Automazione di Azure nella sottoscrizione
I runbook necessari per la scalabilità verticale della macchina virtuale sono già stati pubblicati nella raccolta dei runbook di Automazione di Azure. Sarà necessario importarli nella sottoscrizione. Per informazioni sull'importazione dei runbook, vedere l'articolo seguente:

* [Raccolte di runbook e moduli per l'automazione di Azure](../../automation/automation-runbook-gallery.md)

I runbook da importare sono visualizzati nell'immagine seguente:

![Importazione runbook](./media/vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Aggiungere un webhook al runbook
Dopo aver importato i runbook, è necessario aggiungere un webhook al runbook in modo che possa essere attivato da un avviso da una macchina virtuale. Informazioni dettagliate sulla creazione di un webhook per il runbook sono disponibili nell'articolo seguente:

* [Webhook di Automazione di Azure](../../automation/automation-webhooks.md)

Assicurarsi di copiare il webhook prima di chiudere la finestra di dialogo del webhook, in quanto sarà necessario nella sezione successiva.

## <a name="add-an-alert-to-your-virtual-machine"></a>Aggiungere un avviso alla macchina virtuale
1. Selezionare le impostazioni della macchina virtuale
2. Selezionare "Regole di avviso"
3. Selezionare "Aggiungi avviso"
4. Selezionare una metrica per attivare l'avviso
5. Selezionare una condizione che, se soddisfatta, farà generare l'avviso
6. Selezionare una soglia affinché la condizione nel passaggio 5 sia soddisfatta.
7. Selezionare un periodo in cui il servizio di monitoraggio verificherà la condizione e la soglia dei passaggi 5 e 6
8. Incollare il webhook copiato dalla sezione precedente

![Aggiunta di un avviso alla macchina virtuale 1](./media/vertical-scaling-automation/add-alert-webhook-1.png)

![Aggiunta di un avviso alla macchina virtuale 2](./media/vertical-scaling-automation/add-alert-webhook-2.png)

