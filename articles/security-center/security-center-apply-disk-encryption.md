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
ms.openlocfilehash: d0f96fe758966a435f8fb8e448e75cbb18b85122
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604519"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Applicare la crittografia del disco nel Centro sicurezza Azure
Il Centro sicurezza di Azure suggerisce di applicare la crittografia dischi se sono presenti dischi di VM Windows o Linux che non vengono crittografati con Crittografia dischi di Azure. Crittografia dischi consente di crittografare i dischi delle VM IaaS Windows e Linux.  La crittografia è consigliabile sia per il sistema operativo sia per i volumi di dati della macchina virtuale.

La crittografia dischi usa la funzionalità standard di settore [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) di Windows e la funzionalità [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) di Linux. Queste funzionalità garantiscono la crittografia dei dati e del sistema operativo per contribuire alla protezione e alla salvaguardia dei dati e al rispetto degli impegni dell'organizzazione a livello di sicurezza e conformità. Il servizio Crittografia dischi è integrato con [Insieme di credenziali delle chiavi di Azure](https://azure.microsoft.com/documentation/services/key-vault/) per facilitare il controllo e la gestione dei segreti e delle chiavi di crittografa del disco nella sottoscrizione dell'insieme di credenziali delle chiavi, assicurando allo stesso tempo che tutti i dati inattivi nei dischi delle VM siano crittografati in [Archiviazione di Azure](https://azure.microsoft.com/documentation/services/storage/).

> [!NOTE]
> Crittografia dischi di Azure è supportata nei sistemi operativi Windows Server seguenti: Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2. La crittografia dischi è supportata nei sistemi operativi dei server Linux seguenti: Ubuntu, CentOS, SUSE e SUSE Linux Enterprise Server (SLES).
>
>

## <a name="implement-the-recommendation"></a>Implementare la raccomandazione
1. Nel pannello **Raccomandazioni** selezionare **Applica la crittografia del disco**.
2. Nel pannello **Apply disk encryption** (Applica crittografia dischi) verrà visualizzato un elenco di VM per cui è consigliabile la crittografia dischi.
3. Seguire le istruzioni per applicare la crittografia a queste VM.

![][1]

Per crittografare le macchine virtuali di Azure che in base al Centro sicurezza PC devono essere crittografate, è consigliabile seguire questa procedura:

* Installare e configurare Azure PowerShell. In questo modo è possibile eseguire i comandi di PowerShell per configurare i prerequisiti necessari per crittografare le macchine virtuali di Azure.
* Ottenere ed eseguire lo script di Azure PowerShell dei prerequisiti di Crittografia dischi di Azure.
* Crittografare le macchine virtuali.

L'articolo [Crittografare una macchina virtuale IaaS Windows con Azure PowerShell](../virtual-machines/windows/disk-encryption-powershell-quickstart.md) illustra questi passaggi. Questo argomento presuppone l'uso di un computer client Windows da cui si esegue la configurazione della crittografia dei dischi.

È possibile usare diversi approcci per le macchine virtuali di Azure. Se si ha buona familiarità con Azure PowerShell o l'interfaccia della riga di comando di Azure, è possibile adottare soluzioni alternative. Per altre informazioni su altri approcci, consultare l'articolo sulla [crittografia dischi di Azure](../security/fundamentals/encryption-overview.md).

## <a name="see-also"></a>Vedere anche
In questo documento è stato illustrato come implementare la raccomandazione "Applicare crittografia dischi" del Centro sicurezza. Per altre informazioni sulla crittografia dischi, vedere gli argomenti seguenti:

* [Crittografia e gestione delle chiavi con l'insieme di credenziali delle chiavi di Azure](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (video, 36 minuti 39 secondi): informazioni su come usare la gestione della crittografia dischi per le VM IaaS e l'insieme di credenziali delle chiavi di Azure per proteggere e salvaguardare i dati.
* [Crittografia dischi di Azure](../security/fundamentals/encryption-overview.md) (documento): informazioni su come abilitare la crittografia dischi per le VM Windows e Linux.

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](tutorial-security-policy.md) : informazioni su come configurare i criteri di sicurezza.
* [Monitoraggio dell'integrità](security-center-monitoring.md) della sicurezza nel Centro sicurezza di Azure: informazioni su come monitorare l'integrità delle risorse di Azure.Security health monitoring in Azure Security Center-- Learn how to monitor the health of your Azure resources.
* [Gestione e risposta agli avvisi](security-center-managing-and-responding-alerts.md) di sicurezza nel Centro sicurezza di Azure: informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Gestione dei consigli](security-center-recommendations.md) sulla sicurezza nel Centro sicurezza di Azure: informazioni su come i consigli consentono di proteggere le risorse di Azure.Managing security recommendations in Azure Security Center -- Learn how recommendations help you protect your Azure resources.
* [Blog sulla sicurezza di Azure:](https://blogs.msdn.com/b/azuresecurity/) trova post di blog su sicurezza e conformità di Azure.Azure Security blog -- Find blog posts about Azure security and compliance.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
