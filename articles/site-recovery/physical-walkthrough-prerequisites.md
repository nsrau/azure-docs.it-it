---
title: Prerequisiti per la replica di server fisici in Azure usando Azure Site Recovery | Microsoft Docs
description: Vengono riepilogati i prerequisiti per la replica in Azure di carichi di lavoro in esecuzione in server fisici Windows/Linux usando il servizio Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 318156ba-793b-48d0-98d4-cc5436bf28a3
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 5f5cb4b9b6fcee6b56ccdcb6223afddd9de90fec
ms.contentlocale: it-it
ms.lasthandoff: 06/29/2017

---

# <a name="step-2-review-the-prerequisites-for-physical-server-to-azure-replication"></a>Passaggio 2: Esaminare i prerequisiti per la replica di server fisici in Azure

Esaminare i prerequisiti riepilogati nella tabella.


**Prerequisito** | **Dettagli**
--- | ---
**Azzurro** | Vedere i [requisiti di Azure](site-recovery-prereq.md#azure-requirements).
**Server di configurazione locale** | È necessario un server fisico (o una VM VMware) che esegue Windows Server 2012 R2 o versione successiva. Questo server viene configurato durante la distribuzione di Site Recovery.<br/><br/> Per impostazione predefinita, in questo computer vengono installati anche il server di elaborazione e il server di destinazione master. Quando si aumentano le prestazioni, potrebbe essere necessario un server di elaborazione separato. In questo caso, tale server ha gli stessi requisiti del server di configurazione.<br/><br/> [Altre informazioni](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements).
**VM locali** | I computer da replicare devono eseguire un [sistema operativo supportato](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) ed essere conformi ai [prerequisiti di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
**URL** | Il server di configurazione deve avere accesso a questi URL:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> Se sono presenti regole del firewall basate sull'indirizzo IP, verificare che consentano la comunicazione con Azure.<br/></br> Consentire gli [intervalli IP del data center di Azure ](https://www.microsoft.com/download/confirmation.aspx?id=41653) e la porta HTTPS (443).<br/></br> Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali (usati per il controllo di accesso e la gestione delle identità).<br/><br/> Consentire questo URL per il download di MySQL: http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi.
**Servizio Mobility** | Deve essere installato in ogni server replicato.




## <a name="limitations"></a>Limitazioni

Notare le limitazioni riepilogate nella tabella prima di eseguire la distribuzione.

**Limitazione** | **Dettagli**
--- | ---
**Azure** | Gli account di archiviazione e di rete devono trovarsi nella stessa area dell'insieme di credenziali.<br/><br/> Se si usa un account di archiviazione Premium, è necessario anche un account di archiviazione Standard per archiviare i log di replica.<br/><br/> In India centrale e India meridionale non è possibile eseguire la replica in account Premium.
**Server di configurazione locale** | Se si usa una VM VMware come configurazione del computer server, il tipo di scheda della VM VMware deve essere VMXNET3. In caso contrario, [installare questo aggiornamento](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1).<br/><br/> Per una VM VMware, è necessario installare vSphere PowerCLI 6.0.<br/><br> Il computer non deve essere un controller di dominio.<br/><br/> Il computer deve avere un indirizzo IP statico.<br/><br/> Il nome host può contenere al massimo 15 caratteri e il sistema operativo deve essere in inglese.
**Replicare le macchine** | Verificare le [limitazioni delle VM di Azure](site-recovery-prereq.md#azure-requirements).<br/><br/> Non è possibile replicare VM con dischi crittografati o con avvio UEFI/EFI.<br/><br> I cluster di dischi condivisi non sono supportati. Se nella VM di origine è presente un gruppo NIC, questo viene convertito in una singola scheda di interfaccia di rete dopo il failover.<br/><br/> Se nelle VM è presente un disco iSCSI, Site Recovery lo converte in un file VHD dopo il failover. Se la destinazione iSCSI è raggiungibile dalla VM di Azure, questa vi si connette e saranno visibili sia tale destinazione che il disco rigido virtuale. In questo caso, disconnettere la destinazione iSCSI.<br/><br/> Se si vuole abilitare la coerenza tra più VM, che consente di ripristinare insieme le VM che eseguono lo stesso carico di lavoro in un punto dati coerente, aprire la porta 20004 sulla VM.<br/><br/> Windows deve essere installato nell'unità C. Il disco del sistema operativo deve essere di base e non dinamico. Il disco dati può essere dinamico.<br/><br/> I file /etc/hosts di Linux nelle VM devono contenere le voci che eseguono il mapping del nome host locale agli indirizzi IP associati a tutte le schede di rete. Il nome host, i punti di montaggio, i nomi dei dispositivi, i percorsi di sistema e i nomi file (/etc/, /usr) devono essere specificati solo in inglese.<br/><br/> Sono supportati tipi specifici di [archiviazione Linux](site-recovery-support-matrix-to-azure.md#support-for-storage).<br/><br/>Creare o impostare **disk.enableUUID=true** nelle impostazioni della VM. In questo modo viene fornito un valore UUID coerente al file VMDK, che viene così installato correttamente, e si garantisce che durante il failback vengano ritrasferite nell'ambiente locale solo le modifiche differenziali, senza replica completa.


## <a name="next-steps"></a>Passaggi successivi

- Se si sta eseguendo una distribuzione completa, andare a [Passaggio 3: Pianificare la capacità](physical-walkthrough-capacity.md)
- Se si sta eseguendo una distribuzione di test semplice, andare a [Passaggio 4: Pianificare la rete](physical-walkthrough-network.md).

