---
title: Abilitare la crittografia per l&quot;account di archiviazione nel Centro sicurezza di Azure | Microsoft Docs
description: In questo documento viene illustrato come implementare le raccomandazioni del Centro sicurezza di Azure **Abilitare la crittografia per l&quot;account di archiviazione nel Centro sicurezza di Azure**.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 7b5df25f46a2824acb361401ab90c960b8e5978f
ms.openlocfilehash: 5b580183002ae5c42cc08343cea7f659c635d590


---
# <a name="enable-encryption-for-azure-storage-account-in-azure-security-center"></a>Abilitare la crittografia per l'account di archiviazione nel Centro sicurezza di Azure
Il Centro sicurezza di Azure consiglia all'utente di abilitare la crittografia del servizio di archiviazione di Azure per i dati inattivi.

La Crittografia del servizio di archiviazione (SSE) applica la crittografia ai dati quando vengono scritti nell'archiviazione di Azure e li decrittografa prima del recupero.  SSE è attualmente disponibile solo per il servizio BLOB di Azure e può essere usata per BLOB in blocchi, BLOB di pagine e BLOB di aggiunta.  Per altre informazioni, vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi](../storage/storage-service-encryption.md).


> [!Note]
> Dopo avere attivato la crittografia, vengono crittografati solo i nuovi dati. Qualsiasi BLOB esistente nell'account di archiviazione non viene crittografato. Per crittografare un BLOB esistente, vedere [Domande frequenti su Crittografia del servizio di archiviazione per i dati inattivi](../storage/storage-service-encryption.md#frequently-asked-questions-about-storage-service-encryption-for-data-at-rest).
>
>

La crittografia del servizio di archiviazione è supportata solo negli account di archiviazione di Gestione risorse. Gli account di archiviazione classici non sono attualmente supportati. Per informazioni sui modelli di distribuzione classico e di Gestione risorse, vedere i [modelli di distribuzione di Azure](../azure-classic-rm.md).

> [!NOTE]
> Il documento introduce il servizio usando una distribuzione di esempio.  Questo argomento non costituisce una guida dettagliata.
>
>

## <a name="implement-the-recommendation"></a>Implementare la raccomandazione
1. Nel pannello **Raccomandazioni** selezionare **Enable encryption for Azure Storage Account** (Abilita la crittografia per l'account di archiviazione di Azure).
   ![Enable encryption for storage account][1] (Abilita la crittografia per l'account di archiviazione)
2. Viene visualizzato il pannello **Enable storage encryption** (Abilita la crittografia di archiviazione). In questo pannello sono elencati gli account di archiviazione di Azure in cui la crittografia di archiviazione è disabilitata. In questo esempio selezioniamo **storageacct1**.
   ![Abilita la crittografia di archiviazione][2]
3. Si apre il pannello **Crittografia** per **storageacct1**. Selezionare **Enabled**.
   ![Pannello di crittografia][3]
4. Selezionare **Salva**.

A questo punto è stata abilitata la crittografia di archiviazione per **storageacct1**.


## <a name="see-also"></a>Vedere anche
In questo documento viene illustrato come implementare la raccomandazione del Centro sicurezza di Azure "Abilitare la crittografia per l'account di archiviazione di Azure". Per ulteriori informazioni sulla crittografia del servizio di archiviazione di Azure, vedere le risorse seguenti:

* [Crittografia del servizio di archiviazione di Azure per dati inattivi](../storage/storage-service-encryption.md)

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostare i criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md) : informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure.

<!--Image references-->
[1]: ./media/security-center-enable-encryption-for-storage-account/enable-encryption-for-storage-account.png
[2]: ./media/security-center-enable-encryption-for-storage-account/enable-storage-encryption.png
[3]: ./media/security-center-enable-encryption-for-storage-account/encryption-blade.png



<!--HONumber=Jan17_HO1-->


