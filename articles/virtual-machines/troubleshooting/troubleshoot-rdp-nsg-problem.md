---
title: Non è possibile connettersi a macchine virtuali di Azure perché la porta RDP non è abilitata in NSG | Microsoft Docs
description: Informazioni su come risolvere un errore RDP causato dalla configurazione di NSG nel portale di Azure | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: cfb0464fd7fbd271272a992cffead44e9ba3b553
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103427"
---
#  <a name="cannot-connect-remotely-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>Non è possibile connettersi in remoto a una macchina virtuale perché la porta RDP non è abilitata in NSG

Questo articolo spiega come risolvere il problema di non riuscire a connettersi a una macchina virtuale Windows di Azure perché la porta Remote Desktop Protocol (RDP) non è abilitata nel gruppo di sicurezza di rete (NSG).


> [!NOTE] 
> Azure offre due modelli di distribuzione per creare e utilizzare le risorse: [Resource Manager e distribuzione classica](../../azure-resource-manager/resource-manager-deployment-model.md). Per le nuove distribuzioni si consiglia di usare il modello di distribuzione di Resource Manager anziché quello classico. 

## <a name="symptom"></a>Sintomo

Non è possibile stabilire una connessione RDP a una macchina virtuale in Azure perché la porta RDP non è aperta nel gruppo di sicurezza di rete.

## <a name="solution"></a>Soluzione 

Quando si crea una nuova macchina virtuale, tutto il traffico da Internet è bloccato per impostazione predefinita. 

Per abilitare la porta RDP in un gruppo di sicurezza di rete, seguire questa procedura:
1. Accedere al [portale di Azure](https://portal.azure.com).
2. In **Macchine virtuali** selezionare la macchina virtuale che presenta il problema. 
3. In **Impostazioni** selezionare **Rete**. 
4. In **Regole porta in ingresso** controllare se la porta per il protocollo RDP è impostata correttamente. Di seguito è riportato un esempio della configurazione: 

    **Priorità**: 300 </br>
    **Porta**: 3389 </br>
    **Nome**: Port_3389 </br>
    **Porta**: 3389 </br>
    **Protocollo**: TCP </br>
    **Origine**: Any </br>
    **Destinazioni**: Any </br>
    **Azione**: Allow </br>

Se si specifica l'indirizzo IP di origine, questa impostazione consente solo al traffico proveniente da un indirizzo IP o da un intervallo di indirizzi IP specifico di connettersi alla macchina virtuale. Assicurarsi che il computer utilizzato per avviare la sessione RDP sia compreso nell'intervallo.

Per altre informazioni sui gruppi di sicurezza di rete, vedere [gruppo di sicurezza di rete](../../virtual-network/security-overview.md).

> [!NOTE]
> La porta RDP 3389 è esposta a Internet. Pertanto è consigliabile usarla solo a scopo di test. Per gli ambienti di produzione si consiglia di usare una connessione privata o VPN.

## <a name="next-steps"></a>Passaggi successivi

Se la porta RDP è già abilitata nel gruppo di sicurezza di rete, vedere [Risolvere un errore generale RDP in una VM di Azure](./troubleshoot-rdp-general-error.md).



