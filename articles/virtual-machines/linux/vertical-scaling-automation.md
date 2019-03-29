---
title: Scalabilità verticale di macchine virtuali di Azure tramite Automazione di Azure | Microsoft Docs
description: Come eseguire la scalabilità verticale di una macchina virtuale Linux in risposta agli avvisi di monitoraggio tramite Automazione di Azure
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: dcee199e-fa25-44d5-9b25-df564cee9b45
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/29/2016
ms.author: singhkay
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8d58a9df519ea886372258dd0c7b012df8d8d3ae
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579394"
---
# <a name="vertically-scale-azure-linux-virtual-machine-with-azure-automation"></a>Scalabilità verticale di macchine virtuali di Linux in Azure tramite Automazione di Azure
La scalabilità verticale è il processo di aumento o riduzione delle risorse di una macchina in risposta al carico di lavoro. In Azure tale operazione può essere eseguita modificando le dimensioni della macchina virtuale. Può essere utile negli scenari seguenti:

* Se la macchina virtuale non viene usata di frequente, è possibile diminuirne le dimensioni per ridurre i costi mensili
* Se nella macchina virtuale si osserva un picco di carico, è possibile aumentarne le dimensioni per una maggiore capacità

Per eseguire questa operazione, seguire questa procedura:

1. Configurare Automazione di Azure per l'accesso alle macchine virtuali
2. Importare i runbook di scalabilità verticale di Automazione di Azure nella sottoscrizione
3. Aggiungere un webhook al runbook
4. Aggiungere un avviso alla macchina virtuale

> [!NOTE]
> A causa delle dimensioni della prima macchina virtuale, le dimensioni a cui la macchina può essere ridimensionata possono essere limitate a seconda della disponibilità di altre dimensioni nel cluster in cui viene distribuita la macchina virtuale corrente. Nei runbook di automazione pubblicati usati in questo articolo viene considerato questo caso e la scalabilità viene applicata solo all'interno delle coppie di dimensioni delle macchine virtuali seguenti. Pertanto, una macchina virtuale Standard_D1v2 non verrà improvvisamente ridimensionata verso l'alto a una Standard_G5 o verso il basso a una Basic_A0. Anche vincolata macchina virtuale di dimensioni di aumentare/ridurre le dimensioni non è supportato. È possibile scegliere di applicare il ridimensionamento tra le seguenti coppie di dimensioni:
> 
> | coppie di ridimensionamento di dimensioni delle macchine virtuali |  |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_A1_v2 |Standard_A8_v2 |
> | Standard_A2m_v2 |Standard_A8m_v2  |
> | Standard_B1s |Standard_B2s |
> | Standard_B1ms |Standard_B8ms |
> | Standard_D1 |Standard_D4 |
> | Standard_D11 |Standard_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1_v2 |Standard_D5_v2 |
> | Standard_D11_v2 |Standard_D14_v2 |
> | Standard_DS1_v2 |Standard_DS5_v2 |
> | Standard_DS11_v2 |Standard_DS14_v2 |
> | Standard_D2_v3 |Standard_D64_v3 |
> | Standard_D2s_v3 |Standard_D64s_v3 |
> | Standard_DC2s |Standard_DC4s |
> | Standard_E2v3 |Standard_E64v3 |
> | Standard_E2sv3 |Standard_E64sv3 |
> | Standard_F1 |Standard_F16 |
> | Standard_F1s |Standard_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> | Standard_H8 |Standard_H16 |
> | Standard_H8m |Standard_H16m |
> | Standard_L4s |Standard_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard_M8ms  |Standard_M128ms |
> | Standard_M32ls  |Standard_M64ls |
> | Standard_M64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standard_NC6 |Standard_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standard_NV6 |Standard_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Configurare Automazione di Azure per l'accesso alle macchine virtuali
La prima operazione da eseguire è creare l'account di Automazione di Azure che ospiterà i runbook usati per ridimensionare le istanze del set di scalabilità VM. Il servizio Automazione ha introdotto di recente la funzionalità "Account RunAs", che semplifica molto l'impostazione dell'entità servizio per l'esecuzione automatica di runbook per conto dell'utente. Altre informazioni sono disponibili nell'articolo seguente.

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

