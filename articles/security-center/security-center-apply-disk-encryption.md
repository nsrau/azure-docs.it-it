---
title: Applicare la crittografia del disco nel Centro sicurezza Azure | Microsoft Docs
description: Questo documento illustra come implementare la raccomandazione **Applicare crittografia dischi** del Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''

ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2016
ms.author: terrylan

---
# Applicare la crittografia del disco nel Centro sicurezza Azure
Centro protezione di Azure suggerisce di applicare la crittografia dischi se sono presenti dischi di VM Windows o Linux che non vengono crittografati con Crittografia dischi di Azure. Crittografia dischi consente di crittografare i dischi delle VM IaaS Windows e Linux. La crittografia è consigliabile sia per il sistema operativo sia per i volumi di dati della macchina virtuale.

Crittografia dischi sfrutta la funzionalità [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) di Windows standard di settore e la funzionalità [DM Crypt](https://en.wikipedia.org/wiki/Dm-crypt) di Linux per fornire la crittografia del sistema operativo e dei dati per proteggere e salvaguardare i dati e rispettare gli impegni in termini di sicurezza e conformità dell'organizzazione. Crittografia dischi è integrata con l'[insieme di credenziali delle chiavi di Azure](https://azure.microsoft.com/documentation/services/key-vault/) per facilitare il controllo e la gestione dei segreti e delle chiavi di crittografa del disco nella sottoscrizione dell'insieme di credenziali delle chiavi, assicurando allo stesso tempo che tutti i dati inattivi sui dischi delle VM siano crittografati nell'[Archiviazione di Azure](https://azure.microsoft.com/documentation/services/storage/).

> [!NOTE]
> Crittografia dischi di Azure è supportata nei sistemi operativi Windows Server seguenti: Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2. Crittografia dischi di Azure è supportata nei sistemi operativi dei server Linux: Ubuntu, CentOS, SUSE e SUSE Linux Enterprise Server (SLES).
> 
> 

## Implementare la raccomandazione
1. Nel pannello **Raccomandazioni** selezionare **Applicare crittografia dischi**.
2. Nel pannello **Applicare crittografia dischi** verrà visualizzato un elenco di VM per cui è consigliabile crittografare il disco.
3. Seguire le istruzioni per applicare la crittografia a queste VM.

![][1]

Per crittografare le macchine virtuali di Azure che in base al Centro sicurezza PC devono essere crittografate, è consigliabile seguire questa procedura:

* Installare e configurare Azure PowerShell. In questo modo sarà possibile eseguire i comandi di PowerShell per configurare i prerequisiti necessari per crittografare le macchine virtuali di Azure.
* Ottenere ed eseguire lo script di Azure PowerShell dei prerequisiti di Crittografia dischi di Azure.
* Crittografare le macchine virtuali.

L'articolo [Crittografare una macchina virtuale di Azure](security-center-disk-encryption.md) illustrerà la procedura. Questo articolo presuppone l'uso di Windows 10 nel computer client da cui si esegue la configurazione di crittografia dischi.

L'installazione dei prerequisiti e la configurazione della crittografia per le macchine virtuali di Azure possono essere eseguite in diversi modi. Se si ha buona familiarità con Azure PowerShell o l'interfaccia della riga di comando di Azure, è possibile adottare soluzioni alternative. Per altre informazioni su altri approcci, vedere [Crittografia dischi di Azure](../security/azure-security-disk-encryption.md).

## Vedere anche
In questo documento è stato illustrato come implementare la raccomandazione "Applicare crittografia dischi" del Centro sicurezza. Per altre informazioni sulla crittografia dischi, vedere gli argomenti seguenti:

* [Crittografia e gestione delle chiavi con l'insieme di credenziali delle chiavi di Azure](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (video, 36 minuti 39 secondi): informazioni su come usare la gestione della crittografia dischi per le VM IaaS e l'insieme di credenziali delle chiavi di Azure per proteggere e salvaguardare i dati.
* [Crittografare una macchina virtuale di Azure](security-center-disk-encryption.md) (documento): informazioni su come crittografare le macchine virtuali di Azure.
* [Crittografia dischi di Azure](../security/azure-security-disk-encryption.md) (documento): informazioni su come abilitare la crittografia dischi per le VM Windows e Linux.

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md): informazioni su come configurare i criteri di sicurezza.
* [Monitoraggio dello stato della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
* [Come gestire e rispondere agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md): informazioni sul modo in cui le raccomandazioni facilitano la protezione delle risorse di Azure.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): post del blog sulla sicurezza e sulla conformità di Azure.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png

<!---HONumber=AcomDC_0803_2016-->