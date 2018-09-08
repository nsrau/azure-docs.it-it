---
title: Risolvere i problemi di convalida di Azure Stack come servizio | Microsoft Docs
description: Risolvere i problemi di convalida come servizio per Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: ed070ac4fdf9ccca1b1b4b99b8031bc3fd035779
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44160150"
---
# <a name="troubleshoot-validation-as-a-service"></a>Risolvere i problemi di convalida come servizio

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Di seguito sono comuni dei problemi non correlati a versioni di software e le relative soluzioni.

## <a name="the-portal-shows-local-agent-in-debug-mode"></a>Il portale Mostra agente locale in modalità di debug

È possibile che l'agente è in grado di inviare gli heartbeat al servizio a causa di una connessione di rete instabile. Un heartbeat viene inviato ogni cinque minuti. Se il servizio non riceve un heartbeat per 15 minuti, il servizio prende in considerazione l'agente inattivi e i test non verranno pianificati non è più su di esso. Controllare il messaggio di errore *Agenthost.log* file che si trova nella directory in cui l'agente è stato avviato.

> [!Note] 
> Qualsiasi test già in esecuzione nell'agente continuerà a essere eseguito, ma se l'heartbeat non ripristinato prima della scadenza del test, quindi l'agente avrà esito negativo aggiornare lo stato del test o caricare i log. Il test verrà sempre visualizzata come **in esecuzione** e dovranno essere annullata.

## <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>Il processo dell'agente sul computer è stato arrestato durante l'esecuzione di test. Cosa aspettarsi?

Se il processo dell'agente è arrestato in modo anomalo, ad esempio, macchina riavviato, abbattuto processo (CTRL + C nella finestra dell'agente è considerata l'arresto normale) sarà ancora il test che era in esecuzione su di esso mostrare come **in esecuzione**. Se l'agente viene riavviato, quindi l'agente verrà aggiornato lo stato del test da **annullata**. Se l'agente non viene riavviato, quindi il test viene visualizzato come **in esecuzione** ed è necessario annullare manualmente il test

> [!Note] 
> I test all'interno di un flusso di lavoro sono pianificati per l'esecuzione sequenziale. **In sospeso** test sarà non venga eseguito fino al test nel **esecuzione** stato nello stesso flusso di lavoro completo.

## <a name="handle-slow-network-connectivity"></a>Gestire la connettività di rete lenta

È possibile scaricare l'immagine del repository in una condivisione nel tuo Data Center locale. E quindi è possibile verificare l'immagine.

<!-- This is from the appendix to the Deploy local agent topic. -->

### <a name="download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>Scaricare l'immagine di PIR per la condivisione locale in caso di traffico di rete lenta

1. Download di AzCopy da: [vaasexternaldependencies(AzCopy)](https://vaasexternaldependencies.blob.core.windows.net/prereqcomponents/AzCopy.zip)

2. Estrarre AzCopy.zip e passare alla directory contenente AzCopy.exe

3. Aprire Windows PowerShell da un prompt dei comandi con privilegi elevati. Eseguire i comandi seguenti:

```PowerShell  
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterFullBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterCoreBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'WindowsServer2012R2DatacenterBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1404LTS.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1604-20170619.1.vhd' /NC:12 /V:azcopylog.log /Y
```

> [!Note]  
> LocalFileShare è il percorso della condivisione o un percorso locale.

### <a name="verifying-pir-image-file-hash-value"></a>Verifica per determinare se valore hash del file immagine di repository

È possibile usare **Get-HashFile** per ottenere il valore hash per il repository scaricato immagine pubblica i file di immagine per verificare l'integrità delle immagini.

| File Name | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL.vhd | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL.vhd | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL.vhd | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS.vhd | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604 20170619.1.vhd | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle [convalida Azure Stack come servizio](https://docs.microsoft.com/azure/azure-stack/partner).
