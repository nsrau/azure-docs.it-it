---
title: Applicare la crittografia del disco nel Centro sicurezza di Azure | Documentazione Microsoft
description: Questo documento illustra come implementare la raccomandazione **Applicare crittografia dischi** del Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 67cff664f3723b2194ecd1519729cca17069d07f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
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

[Crittografare una macchina virtuale di Azure](security-center-disk-encryption.md) illustra la procedura.  Questo argomento presuppone l'uso di Windows 10 come computer client da cui si esegue la configurazione di crittografia dischi.

È possibile usare diversi approcci per le macchine virtuali di Azure. Se si ha buona familiarità con Azure PowerShell o l'interfaccia della riga di comando di Azure, è possibile adottare soluzioni alternative. Per altre informazioni su altri approcci, consultare l'articolo sulla [crittografia dischi di Azure](../security/azure-security-disk-encryption.md).

## <a name="see-also"></a>Vedere anche
In questo documento è stato illustrato come implementare la raccomandazione "Applicare crittografia dischi" del Centro sicurezza. Per altre informazioni sulla crittografia dischi, vedere gli argomenti seguenti:

* [Crittografia e gestione delle chiavi con l'insieme di credenziali delle chiavi di Azure](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (video, 36 minuti 39 secondi): informazioni su come usare la gestione della crittografia dischi per le VM IaaS e l'insieme di credenziali delle chiavi di Azure per proteggere e salvaguardare i dati.
* [Crittografare una macchina virtuale di Azure](security-center-disk-encryption.md) (documento): informazioni su come crittografare le macchine virtuali di Azure.
* [Crittografia dischi di Azure](../security/azure-security-disk-encryption.md) (documento): informazioni su come abilitare la crittografia dischi per le VM Windows e Linux.

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) : informazioni su come configurare i criteri di sicurezza.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md) : informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : post di blog sulla sicurezza e sulla conformità di Azure.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
