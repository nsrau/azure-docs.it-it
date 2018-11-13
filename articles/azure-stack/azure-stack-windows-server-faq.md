---
title: Il Server di Windows Azure Stack-domande frequenti | Microsoft Docs
description: Elenco di domande frequenti sul Marketplace di Azure Stack per Windows Server
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2018
ms.author: sethm
ms.reviewer: avishwan
ms.openlocfilehash: 91404f01a1a675ac59898336ef8aa81e1d2638b6
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2018
ms.locfileid: "51579233"
---
# <a name="windows-server-in-azure-stack-marketplace-faq"></a>Windows Server nelle domande frequenti sul Marketplace di Azure Stack

Questo articolo risponde ad alcune domande frequenti sulle immagini di Windows Server nel [Marketplace Azure Stack](azure-stack-marketplace.md).

## <a name="marketplace-items"></a>Elementi del Marketplace

### <a name="how-do-i-update-to-a-newer-windows-image"></a>Come aggiornare a un'immagine di Windows più recente?

In primo luogo, determinare se i modelli di Azure Resource Manager fanno riferimento a versioni specifiche. In questo caso, aggiornare i modelli o mantenere le versioni precedenti di immagine. È consigliabile usare **versione: più recente**.

Successivamente, se qualsiasi set di scalabilità di macchine virtuali fanno riferimento a una versione specifica, è opportuno valutare se questi verranno ridimensionati in un secondo momento e decidere se mantenere le versioni precedenti. Se nessuna di queste condizioni è applicabile, eliminare le immagini precedenti nel Marketplace prima di scaricare quelle più recenti. Utilizzare Gestione Marketplace per eseguire questa operazione se questo è il modo in cui è stata scaricata originale. Scaricare la versione più recente.

### <a name="what-are-the-licensing-options-for-windows-server-marketplace-images-on-azure-stack"></a>Quali sono le opzioni di licenza per le immagini di Windows Server Marketplace in Azure Stack?

Microsoft offre due versioni di immagini di Windows Server tramite il Marketplace di Azure Stack:

- **Pagare quando si utilizza**: queste immagini di esecuzione dei contatori Windows prezzo pieno. 
   Chi dovrebbe utilizzare: i clienti con contratto Enterprise che usano il *modello di fatturazione del consumo*; CSP che non si desidera utilizzare Servizio licenze SPLA.
- **Bring Your Own License (BYOL)**: queste immagini eseguire i contatori di base.
   Chi dovrebbe utilizzare: i clienti con contratto Enterprise con una licenza di Windows Server; CSP che usano la licenza SPLA.

Azure Hybrid Usa vantaggio (AHUB) non è supportato in Azure Stack. I clienti che acquistano una licenza tramite il modello di "Capacità" è necessario utilizzare le immagini BYOL. Se si sta testando con Azure Stack Development Kit (ASDK), è possibile usare una di queste opzioni.

### <a name="what-if-i-downloaded-the-wrong-version-to-offer-my-tenantsusers"></a>Cosa accade se ho scaricato la versione errata di offrire i tenant/gli utenti?

Eliminare la versione non corretta prima di tutto tramite la gestione di Marketplace. Attendere il completamento completamente (esaminare le notifiche per il completamento, non il pannello di gestione di Marketplace). Quindi scaricare la versione corretta.

### <a name="what-if-my-user-incorrectly-checked-the-i-have-a-license-box-in-previous-windows-builds-and-they-dont-have-a-license"></a>Cosa accade se l'utente selezionato in modo non corretto la casella "Possedere una licenza" in Windows precedente le build e non abbiano una licenza?

Visualizzare [convertire macchine virtuali Windows Server con il vantaggio nuovamente in base al consumo](../virtual-machines/windows/hybrid-use-benefit-licensing.md#powershell-1).

### <a name="what-if-i-have-an-older-image-and-my-user-forgot-to-check-the-i-have-a-license-box-or-we-use-our-own-images-and-we-do-have-ea-entitlement"></a>Cosa accade se dispone di un'immagine precedente e l'utente ha dimenticato di controllare la casella "Possedere una licenza", o si usano le proprie immagini e abbiamo entitlement con contratto Enterprise?

Visualizzare [convertire una VM esistente con vantaggio Azure Hybrid per Windows Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md#convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server). Si noti che il vantaggio Azure Hybrid non si applica ad Azure Stack, ma l'effetto di questa impostazione è applicabile.

### <a name="what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server"></a>Per quanto riguarda le altre macchine virtuali che usano Windows Server, ad esempio SQL o Machine Learning Server?

Queste immagini si applicano i **licenseType** parametro, in modo che siano pagamento quando si utilizza. È possibile impostare questo parametro (vedere la domanda precedente risposta). Questo vale solo per il software di Windows Server, non a prodotti a più livelli, ad esempio SQL, che richiedono agli utenti di usare la propria licenza. Pagamento in base al durante l'utilizzo di gestione delle licenze non è applicabile ai prodotti software a più livelli.

### <a name="i-have-an-ea-and-i-create-my-own-images-how-do-i-make-sure-they-are-billed-correctly"></a>Ho un contratto Enterprise e creo delle immagini; come assicurarmi che vengono fatturati in modo corretto?

È possibile aggiungere **licenseType: Windows_Server** in un modello di Azure Resource Manager. Questa impostazione deve essere aggiunto a ogni blocco di risorsa di macchina virtuale.

## <a name="activation"></a>Activation

Per attivare una macchina virtuale di Windows Server in Azure Stack, le condizioni seguenti devono essere true:

- OEM è impostato il marcatore del BIOS appropriato su tutti i sistemi host Azure Stack.
- È necessario usare Windows Server 2012 R2 e Windows Server 2016 [Automatic Virtual Machine Activation](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)). Servizio di gestione delle chiavi (KMS) e altri servizi di attivazione non sono supportati in Azure Stack.

### <a name="how-can-i-verify-that-my-virtual-machine-is-activated"></a>Come è possibile verificare che la macchina virtuale viene attivata?

Eseguire il comando seguente al prompt dei comandi con privilegi elevati: 

```shell
slmgr /dlv
``` 

Se è correttamente attivato, verrà visualizzato quanto segue in modo chiaro e il nome host visualizzato nei `slmgr` output. Non dipendono le filigrane sullo schermo man mano che potrebbe non essere aggiornate o vengano visualizzati da una macchina virtuale diversa dietro quelle in uso.

### <a name="my-vm-is-not-set-up-to-use-avma-how-can-i-fix-it"></a>La macchina virtuale non è configurata per usare questa funzionalità, come è possibile correggerlo?

Eseguire il comando seguente al prompt dei comandi con privilegi elevati: 

```shell
slmgr /ipk <AVMA key> 
```

Vedere l'articolo [Automatic Virtual Machine Activation](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) per le chiavi da usare per l'immagine.

### <a name="i-create-my-own-windows-server-images-how-can-i-make-sure-they-use-avma"></a>Creare immagini personalizzate Windows Server, come assicurarsi che usano questa funzionalità?

È consigliabile eseguire la `slmgr /ipk` riga di comando con la chiave appropriata prima di eseguire il `sysprep` comando. In alternativa, è possibile includere il codice avma in qualsiasi file di programma di installazione Unattend.exe.

### <a name="i-am-trying-to-use-my-windows-server-2016-image-created-on-azure-and-it-is-not-activating-or-using-kms-activation"></a>Sto cercando di usare l'immagine di Windows Server 2016 creato in Azure e non l'attivazione o attivazione del KMS.

Eseguire il comando `slmgr /ipk`. Immagini di Azure potrebbero non correttamente il fallback a questa funzionalità, ma se possono raggiungere i sistemi KMS di Azure, che attiverà. È consigliabile assicurarsi che queste VM sono configurate per utilizzare questa funzionalità.

### <a name="i-have-performed-all-of-these-steps-but-my-virtual-machines-are-still-not-activating"></a>Ho eseguite tutti questi passaggi, ma le macchine virtuali sono comunque non eseguire l'attivazione.

Rivolgersi al fornitore dell'hardware per verificare che siano stati installati i marcatori di BIOS corretti.

### <a name="what-about-earlier-versions-of-windows-server"></a>Per quanto riguarda le versioni precedenti di Windows Server?

[Attivazione automatica della macchina virtuale](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) non è supportato nelle versioni precedenti di Windows Server. È necessario attivare manualmente le macchine virtuali.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti:

- [La panoramica del Marketplace Azure Stack](azure-stack-marketplace.md)
- [Scaricare elementi di marketplace di Azure ad Azure Stack](azure-stack-download-azure-marketplace-item.md)
