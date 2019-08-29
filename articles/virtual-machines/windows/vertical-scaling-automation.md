---
title: Usare Automazione di Azure per applicare la scalabilità verticale alle macchine virtuali di Windows | Microsoft Docs
description: Applicare la scalabilità verticale a una macchina virtuale Windows in risposta agli avvisi di monitoraggio con Automazione di Azure
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4f964713-fb67-4bcc-8246-3431452ddf7d
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/18/2019
ms.author: kasing
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 71af54f0a1dd7ecd4d4f0dc6f7a465439993db39
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100177"
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


## <a name="scale-limitations"></a>Limitazioni di ridimensionamento

A causa delle dimensioni della prima macchina virtuale, le dimensioni a cui la macchina può essere ridimensionata possono essere limitate a seconda della disponibilità di altre dimensioni nel cluster in cui viene distribuita la macchina virtuale corrente. Nei runbook di automazione pubblicati usati in questo articolo viene considerato questo caso e la scalabilità viene applicata solo all'interno delle coppie di dimensioni delle macchine virtuali seguenti. Pertanto, una macchina virtuale Standard_D1v2 non verrà improvvisamente ridimensionata verso l'alto a una Standard_G5 o verso il basso a una Basic_A0. Inoltre, le dimensioni della macchina virtuale vincolata non sono supportate. 

È possibile scegliere di applicare il ridimensionamento tra le seguenti coppie di dimensioni:

* [Serie A](#a-series)
* [Serie B](#b-series)
* [Serie D](#d-series)
* [Serie E](#e-series)
* [Serie F](#f-series)
* [Serie G](#g-series)
* [Serie H](#h-series)
* [Serie L](#l-series)
* [Serie M](#m-series)
* [Serie N](#n-series)

### <a name="a-series"></a>Serie A

| Dimensioni iniziali | Ridimensionare le dimensioni | 
| --- | --- |
| Basic_A0 | Basic_A1 |
| Basic_A1 | Basic_A2 |
| Basic_A2 | Basic_A3 |
| Basic_A3 | Basic_A4 |
| Standard_A0 | Standard_A1 |
| Standard_A1 | Standard_A2 |
| Standard_A2 | Standard_A3 |
| Standard_A3 | Standard_A4 |
| Standard_A5 | Standard_A6 |
| Standard_A6 | Standard_A7 |
| Standard_A8 | Standard_A9 |
| Standard_A10 | Standard_A11 |
| Standard_A1_v2 | Standard_A2_v2 |
| Standard_A2_v2 | Standard_A4_v2 |
| Standard_A4_v2 | Standard_A8_v2 |
| Standard_A2m_v2 | Standard_A4m_v2 |
| Standard_A4m_v2 | Standard_A8m_v2 |

### <a name="b-series"></a>Serie B

| Dimensioni iniziali | Ridimensionare le dimensioni | 
| --- | --- |
| Standard_B1s | Standard_B2s |
| Standard_B1ms | Standard_B2ms |
| Standard_B2ms | Standard_B4ms |
| Standard_B4ms | Standard_B8ms |

### <a name="d-series"></a>Serie D

| Dimensioni iniziali | Ridimensionare le dimensioni | 
| --- | --- |
| Standard_D1 | Standard_D2 |
| Standard_D2 | Standard_D3 |
| Standard_D3 | Standard_D4 |
| Standard_D11 | Standard_D12 |
| Standard_D12 | Standard_D13 |
| Standard_D13 | Standard_D14 |
| Standard_DS1 | Standard_DS2 |
| Standard_DS2 | Standard_DS3 |
| Standard_DS3 | Standard_DS4 |
| Standard_DS11 | Standard_DS12 |
| Standard_DS12 | Standard_DS13 |
| Standard_DS13 | Standard_DS14 |
| Standard_D1_v2 | Standard_D2_v2 |
| Standard_D2_v2 | Standard_D3_v2 |
| Standard_D3_v2 | Standard_D4_v2 |
| Standard_D4_v2 | Standard_D5_v2 |
| Standard_D11_v2 | Standard_D12_v2 |
| Standard_D12_v2 | Standard_D13_v2 |
| Standard_D13_v2 | Standard_D14_v2 |
| Standard_DS1_v2 | Standard_DS2_v2 |
| Standard_DS2_v2 | Standard_DS3_v2 |
| Standard_DS3_v2 | Standard_DS4_v2 |
| Standard_DS4_v2 | Standard_DS5_v2 |
| Standard_DS11_v2 | Standard_DS12_v2 |
| Standard_DS12_v2 | Standard_DS13_v2 |
| Standard_DS13_v2 | Standard_DS14_v2 |
| Standard_D2_v3 | Standard_D4_v3 |
| Standard_D4_v3 | Standard_D8_v3 |
| Standard_D8_v3 | Standard_D16_v3 |
| Standard_D16_v3 | Standard_D32_v3 |
| Standard_D32_v3 | Standard_D64_v3 |
| Standard_D2s_v3 | Standard_D4s_v3 |
| Standard_D4s_v3 | Standard_D8s_v3 |
| Standard_D8s_v3 | Standard_D16s_v3 |
| Standard_D16s_v3 | Standard_D32s_v3 |
| Standard_D32s_v3 | Standard_D64s_v3 |
| Standard_DC2s | Standard_DC4s |

### <a name="e-series"></a>Serie E

| Dimensioni iniziali | Ridimensionare le dimensioni | 
| --- | --- |
| Standard_E2_v3 | Standard_E4_v3 |
| Standard_E4_v3 | Standard_E8_v3 |
| Standard_E8_v3 | Standard_E16_v3 |
| Standard_E16_v3 | Standard_E20_v3 |
| Standard_E20_v3 | Standard_E32_v3 |
| Standard_E32_v3 | Standard_E64_v3 |
| Standard_E2s_v3 | Standard_E4s_v3 |
| Standard_E4s_v3 | Standard_E8s_v3 |
| Standard_E8s_v3 | Standard_E16s_v3 |
| Standard_E16s_v3 | Standard_E20s_v3 |
| Standard_E20s_v3 | Standard_E32s_v3 |
| Standard_E32s_v3 | Standard_E64s_v3 |

### <a name="f-series"></a>Serie F

| Dimensioni iniziali | Ridimensionare le dimensioni | 
| --- | --- |
| Standard_F1 | Standard_F2 |
| Standard_F2 | Standard_F4 |
| Standard_F4 | Standard_F8 |
| Standard_F8 | Standard_F16 |
| Standard_F1s | Standard_F2s |
| Standard_F2s | Standard_F4s |
| Standard_F4s | Standard_F8s |
| Standard_F8s | Standard_F16s |
| Standard_F2s_v2 | Standard_F4s_v2 |
| Standard_F4s_v2 | Standard_F8s_v2 |
| Standard_F8s_v2 | Standard_F16s_v2 |
| Standard_F16s_v2 | Standard_F32s_v2 |
| Standard_F32s_v2 | Standard_F64s_v2 |
| Standard_F64s_v2 | Standard_F7s_v2 |

### <a name="g-series"></a>Serie G

| Dimensioni iniziali | Ridimensionare le dimensioni | 
| --- | --- |
| Standard_G1 | Standard_G2 |
| Standard_G2 | Standard_G3 |
| Standard_G3 | Standard_G4 |
| Standard_G4 | Standard_G5 |
| Standard_GS1 | Standard_GS2 |
| Standard_GS2 | Standard_GS3 |
| Standard_GS3 | Standard_GS4 |
| Standard_GS4 | Standard_GS5 |

### <a name="h-series"></a>Serie H

| Dimensioni iniziali | Ridimensionare le dimensioni | 
| --- | --- |
| Standard_H8 | Standard_H16 |
| Standard_H8m | Standard_H16m |

### <a name="l-series"></a>Serie L

| Dimensioni iniziali | Ridimensionare le dimensioni | 
| --- | --- |
| Standard_L4s | Standard_L8s |
| Standard_L8s | Standard_L16s |
| Standard_L16s | Standard_L32s |
| Standard_L8s_v2 | Standard_L16s_v2 |
| Standard_L16s_v2 | Standard_L32s_v2 |
| Standard_L32s_v2 | Standard_L64s_v2 |
| Standard_L64s_v2 | Standard_L80s_v2 |

### <a name="m-series"></a>Serie M

| Dimensioni iniziali | Ridimensionare le dimensioni | 
| --- | --- |
| Standard_M8ms | Standard_M16ms |
| Standard_M16ms | Standard_M32ms |
| Standard_M32ms | Standard_M64ms |
| Standard_M64ms | Standard_M128ms |
| Standard_M32ls | Standard_M64ls |
| Standard_M64s | Standard_M128s |
| Standard_M64 | Standard_M128 |
| Standard_M64m | Standard_M128m |

### <a name="n-series"></a>Serie N

| Dimensioni iniziali | Ridimensionare le dimensioni | 
| --- | --- |
| Standard_NC6 | Standard_NC12 |
| Standard_NC12 | Standard_NC24 |
| Standard_NC6s_v2 | Standard_NC12s_v2 |
| Standard_NC12s_v2 | Standard_NC24s_v2 |
| Standard_NC6s_v3 | Standard_NC12s_v3 |
| Standard_NC12s_v3 | Standard_NC24s_v3 |
| Standard_ND6 | Standard_ND12 |
| Standard_ND12 | Standard_ND24 |
| Standard_NV6 | Standard_NV12 |
| Standard_NV12 | Standard_NV24 |
| Standard_NV6s_v2 | Standard_NV12s_v2 |
| Standard_NV12s_v2 | Standard_NV24s_v2 |
| Standard_NV12s_v3 |Standard_NV48s_v3 |

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

