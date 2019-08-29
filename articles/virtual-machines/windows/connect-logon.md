---
title: Connettersi a una VM Windows Server | Microsoft Docs
description: Informazioni su come connettersi e accedere a una macchina virtuale Windows tramite il portale di Azure e il modello di distribuzione di Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: aa56cfafdcca163d3bdb3f339f84ceeaa37f3dc5
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70079916"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows
Per avviare una sessione di Desktop remoto (RDP) da un desktop di Windows, sarà necessario usare il pulsante **Connetti** nel portale di Azure. Connettersi prima di tutto alla macchina virtuale e quindi eseguire l'accesso.

Per connettersi a una macchina virtuale Windows da un Mac, sarà necessario installare un client RDP per Mac come [Desktop remoto Microsoft](https://aka.ms/rdmac).

## <a name="connect-to-the-virtual-machine"></a>Connettersi alla macchina virtuale
1. Accedere al [portale di Azure](https://portal.azure.com/), se questa operazione non è già stata eseguita.
2. Nel menu a sinistra selezionare **Macchine virtuali**.
3. Selezionare la macchina virtuale dall'elenco.
4. Nella parte superiore della pagina della macchina virtuale selezionare **Connetti**.
2. Nella pagina **Connetti a macchina virtuale** selezionare l'indirizzo IP e la porta appropriati. Nella maggior parte dei casi, è consigliabile usare l'indirizzo IP e la porta predefiniti. Selezionare **Scarica file RDP**. Se per la macchina virtuale è impostato un criterio JIT, selezionare prima il pulsante **Richiedi accesso** per richiedere l'accesso e poter quindi scaricare il file RDP. Per altre informazioni sui criteri JIT, vedere [Gestire l'accesso alle macchine virtuali con la funzionalità JIT (Just-in-Time)](../../security-center/security-center-just-in-time.md).
2. Aprire il file RDP scaricato e selezionare **Connetti** quando richiesto. 
2. Verrà visualizzato un avviso per segnalare che l'autore del file `.rdp` è sconosciuto. Si tratta di un comportamento previsto. Nella finestra **Connessione Desktop remoto** selezionare **Connetti** per continuare.
   
    ![Screenshot di un avviso relativo a un autore sconosciuto.](./media/connect-logon/rdp-warn.png)
3. Nella finestra **Sicurezza di Windows** selezionare **Altre opzioni** e quindi **Usa un altro account**. Immettere le credenziali per un account nella macchina virtuale e quindi selezionare **OK**.
   
     **Account locale**: corrisponde in genere al nome utente e alla password dell'account locale specificati al momento della creazione della macchina virtuale. In questo caso, il dominio è il nome della macchina virtuale e viene immesso come *nomevm*&#92;*nomeutente*.  
   
    **Macchina virtuale aggiunta a un dominio**: se la macchina virtuale appartiene a un dominio, immettere il nome utente nel formato *Dominio*&#92;*Nomeutente*. L'account deve anche appartenere al gruppo degli amministratori o deve possedere privilegi di accesso remoto alla VM.
   
    **Controller di dominio**: se la macchina virtuale è un controller di dominio, immettere nome utente e password di un account amministratore di dominio per tale dominio.
4. Selezionare **Sì** per verificare l'identità della macchina virtuale e terminare la procedura di accesso.
   
   ![Screenshot che visualizza un messaggio sulla verifica dell'identità della VM.](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Se il pulsante **Connetti** nel portale è disattivato e non si è connessi ad Azure tramite una connessione [Express Route](../../expressroute/expressroute-introduction.md) o [VPN da sito a sito](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), per poter usare il protocollo RDP sarà necessario creare un indirizzo IP pubblico e assegnarlo alla macchina virtuale. Per altre informazioni, vedere [Indirizzi IP pubblici in Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>Connettersi alla macchina virtuale tramite PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se si usa PowerShell ed è installato il modulo aure di PowerShell, è anche possibile connettersi usando `Get-AzRemoteDesktopFile` il cmdlet, come illustrato di seguito.

In questo esempio verrà avviata immediatamente la connessione RDP, con prompt simili ai precedenti.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

È anche possibile salvare il file RDP per un uso futuro.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>Passaggi successivi
Se si ha difficoltà con la connessione, vedere [Risolvere i problemi relativi alle connessioni Desktop remoto](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

