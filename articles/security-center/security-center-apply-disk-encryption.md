---
title: Applicare la crittografia del disco nel Centro sicurezza di Azure | Documentazione Microsoft
description: Questo documento illustra come implementare la raccomandazione del Centro sicurezza di Azure Applicare la **crittografia del disco**.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: e0677ecc63573d04e384a0104ee0e4e28b4d5e3b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473266"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Applicare la crittografia del disco nel Centro sicurezza Azure

Il Centro sicurezza di Azure consiglia di usare Crittografia disco di Azure in dischi non crittografati sia nella macchina virtuale Windows che in quella di Linux.Azure Security Center recommends that you use Azure Disk Encryption on unencrypted disks on windows and Linux VM. Crittografia dischi consente di crittografare i dischi delle VM IaaS Windows e Linux.  La crittografia è consigliabile sia per il sistema operativo sia per i volumi di dati della macchina virtuale.

La crittografia dischi usa la funzionalità standard di settore [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) di Windows e la funzionalità [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) di Linux. Queste funzionalità garantiscono la crittografia dei dati e del sistema operativo per contribuire alla protezione e alla salvaguardia dei dati e al rispetto degli impegni dell'organizzazione a livello di sicurezza e conformità. Il servizio Crittografia dischi è integrato con [Insieme di credenziali delle chiavi di Azure](https://azure.microsoft.com/documentation/services/key-vault/) per facilitare il controllo e la gestione dei segreti e delle chiavi di crittografa del disco nella sottoscrizione dell'insieme di credenziali delle chiavi, assicurando allo stesso tempo che tutti i dati inattivi nei dischi delle VM siano crittografati in [Archiviazione di Azure](https://azure.microsoft.com/documentation/services/storage/).

Per l'elenco delle versioni supportate di Windows e Linux, vedere Macchine virtuali e sistemi operativi supportati nella documentazione di Crittografia disco di Azure.For the list of supported versions of Windows and Linux, see [Supported VMs and operating systems](../virtual-machines/windows/disk-encryption-overview.md#supported-vms-and-operating-systems) in the Azure Disk Encryption documentation.

## <a name="implement-the-recommendation"></a>Implementare la raccomandazione
1. Nella pagina **Consigli** selezionare **La crittografia del disco deve essere applicata**alle macchine virtuali.
2. In **Risorse non integre**selezionare una macchina virtuale per la quale è consigliata la crittografia del disco.
3. Seguire le istruzioni per applicare la crittografia a queste VM.

![Applicazione della crittografia del disco](./media/security-center-apply-disk-encryption/apply-disk-encryption.png)

Per crittografare le macchine virtuali di Azure che in base al Centro sicurezza PC devono essere crittografate, è consigliabile seguire questa procedura:

* Installare e configurare Azure PowerShell. In questo modo è possibile eseguire i comandi di PowerShell per configurare i prerequisiti necessari per crittografare le macchine virtuali di Azure.
* Ottenere ed eseguire lo script di Azure PowerShell dei prerequisiti di Crittografia dischi di Azure.
* Crittografare le macchine virtuali.

[Crittografare una macchina virtuale Windows IaaS con Azure PowerShell:](../virtual-machines/windows/disk-encryption-powershell-quickstart.md) illustra questi passaggi e presuppone che si stia usando un computer client Windows da cui è possibile configurare la crittografia del disco.

È possibile usare diversi approcci per le macchine virtuali di Azure. Se si è già esperti in Azure PowerShell o l'interfaccia della riga di comando di Azure, è preferibile usare approcci alternativi. Per altre informazioni su altri approcci, consultare l'articolo sulla [crittografia dischi di Azure](../security/fundamentals/encryption-overview.md).

## <a name="see-also"></a>Vedere anche
In questo documento è stato illustrato come implementare la raccomandazione "Applicare crittografia dischi" del Centro sicurezza. Per altre informazioni sulla crittografia del disco, vedere:To learn more about disk encryption, see:

* [Crittografia e gestione delle chiavi con L'insieme](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) di credenziali delle chiavi di Azure (video, 36 min 39 sec): informazioni su come usare la gestione della crittografia del disco per le macchine virtuali IaaS e l'insieme di credenziali delle chiavi di Azure per proteggere e proteggere i dati.
* [Crittografia del disco di Azure](../security/fundamentals/encryption-overview.md) (documento): informazioni su come abilitare la crittografia del disco per le macchine virtuali Windows e Linux.Azure disk encryption (document)-- Learn how to enable disk encryption for Windows and Linux VMs.