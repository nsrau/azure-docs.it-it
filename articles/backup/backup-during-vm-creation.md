---
title: Abilitare il backup di una macchina virtuale di Azure durante la creazione
description: Come abilitare il backup di macchine virtuali di Azure durante il processo di creazione.
services: backup, virtual-machines
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: fd2beaa39f03d4f2342c94bf1cd8b8aea7440e62
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780444"
---
# <a name="enable-backup-when-you-create-an-azure-virtual-machine"></a>Abilitare il backup quando si crea una macchina virtuale di Azure

Usare il servizio Backup di Azure per eseguire il backup di macchine virtuali di Azure (VM). Backup delle macchine virtuali in base a una pianificazione specificata in un criterio di backup e i punti di ripristino vengono creati dalle copie di backup. I punti di ripristino vengono archiviati negli insiemi di credenziali di servizi di ripristino.

Questo articolo illustra come abilitare il backup mentre si crea una macchina virtuale (VM) nel portale di Azure.  

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Se si non sono già connessi al proprio account, accedere al [portale di Azure](https://portal.azure.com).
 
## <a name="create-a-vm-with-backup-configured"></a>Creare una macchina virtuale con Backup configurato 

1. Nell'angolo superiore sinistro del portale di Azure, selezionare **New**.

1. Selezionare **calcolo**e quindi selezionare un'immagine della macchina virtuale.

1. Immettere le informazioni per la macchina virtuale. Il nome utente e la password specificata dall'utente da utilizzare per accedere alla macchina virtuale. Al termine, selezionare **OK**. 

1. Selezionare una dimensione per la VM.  

1. Sotto **le impostazioni** > **Backup**, selezionare **abilitato** per aprire le impostazioni di configurazione del backup.

   - Per accettare i valori predefiniti, selezionare **OK** nel **impostazioni** pagina. Si passerà quindi al **riepilogo** pagina per creare la macchina virtuale. Ignorare i passaggi da 6 a 8.
   - Per modificare i valori di configurazione del backup, seguire i passaggi successivi.  

1. Creare o selezionare un insieme di credenziali di servizi di ripristino per contenere i backup della macchina virtuale. Se si crea un insieme di credenziali di servizi di ripristino, è possibile scegliere un gruppo di risorse per l'insieme di credenziali.  

    ![Impostazioni di configurazione del backup nella pagina di creazione della macchina virtuale](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > Il gruppo di risorse per l'insieme di credenziali di servizi di ripristino può essere diverso da quello del gruppo di risorse per la macchina virtuale.  

1. Per impostazione predefinita, viene selezionato un criterio di backup per proteggere la macchina virtuale. Criteri di backup specificano la frequenza con cui eseguire snapshot del backup e tempo di conservazione delle copie di backup. 

   - È possibile accettare i criteri predefiniti oppure è possibile creare o selezionare criteri di backup differenti. 
   - Per modificare i criteri di backup, selezionare **criteri di Backup** e modificare i valori.  

1. Quando hai finito l'impostazione dei valori di configurazione del backup, selezionare **OK** nel **impostazioni** pagina.  

1. Nel **Summary** pagina, dopo l'esito positivo della convalida, selezionare **crea** per creare una VM che usa le impostazioni di backup configurate. 

## <a name="start-a-backup-after-creating-the-vm"></a>Avviare un backup dopo aver creato la macchina virtuale 

Anche se è stato configurato un criterio di Backup per la macchina virtuale, è buona norma creare un backup iniziale. 

Al termine il modello di creazione della macchina virtuale, passare a **Operations** nel menu a sinistra e selezionare **Backup** per visualizzare i dettagli sul backup della macchina virtuale. È possibile utilizzare questa pagina per:

- Attivare un backup su richiesta.
- Ripristinare una macchina virtuale completa o tutti i relativi dischi.
- Ripristinare file da un backup della macchina virtuale.
- Modificare il criterio di backup associato alla macchina virtuale.  

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Usare un modello di Resource Manager per distribuire una macchina virtuale protetta

I passaggi precedenti illustrano come usare il portale di Azure per creare una macchina virtuale e proteggerla in un insieme di credenziali di servizi di ripristino. Per distribuire uno o più macchine virtuali e proteggerle in un insieme di credenziali di servizi di ripristino, vedere il modello [distribuire una VM Windows e abilitare il backup](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).

## <a name="frequently-asked-questions"></a>Domande frequenti 

### <a name="which-vm-images-support-backup-configuration-during-vm-creation"></a>Quali immagini di macchina virtuale supportano la configurazione del backup durante la creazione della macchina virtuale?

Le immagini di base seguenti pubblicate da Microsoft sono supportate per l'abilitazione del backup durante la creazione della macchina virtuale. Per altre macchine virtuali, è possibile abilitare il backup dopo aver creata la macchina virtuale. Per altre informazioni, vedere [Abilita backup dopo la creazione di VM](quick-backup-vm-portal.md).

- **Windows** -Windows Server 2016 Datacenter, Windows Server 2016 Datacenter Core, Windows Server 2012 Datacenter, Windows Server 2012 R2 Datacenter, Windows Server 2008 R2 SP1 
- **Ubuntu** - Ubuntu Server 17.10, Ubuntu Server 17.04, Ubuntu Server 16.04 (LTS), Ubuntu Server 14.04 (LTS) 
- **Red Hat**: RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4 
- **SUSE**: SUSE Linux Enterprise Server 11 SP4, 12 SP2, 12 SP3 
- **Debian**: Debian 8, Debian 9 
- **CentOS**: CentOS 6.9, CentOS 7.3 
- **Oracle Linux**: Oracle Linux 6.7, 6.8, 6.9, 7.2, 7.3 
 
### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>È il costo del backup è incluso nel costo della macchina virtuale? 

 No. Costi di backup sono separati dai costi della macchina virtuale. Per altre informazioni sui prezzi di backup, vedere [prezzi di Backup di Azure](https://azure.microsoft.com/pricing/details/backup/).
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>Quali autorizzazioni sono necessarie per abilitare il backup in una macchina virtuale? 

Se si ha un collaboratore macchina virtuale, è possibile abilitare il backup della macchina virtuale. Se si usa un ruolo personalizzato, sono necessarie le autorizzazioni seguenti per abilitare il backup della macchina virtuale: 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Se l'insieme di credenziali di servizi di ripristino e della macchina virtuale dispone di diversi gruppi di risorse, verificare di che disporre delle autorizzazioni di scrittura nel gruppo di risorse per l'insieme di credenziali di servizi di ripristino.  

## <a name="next-steps"></a>Passaggi successivi 

Ora che è stato protetto la macchina virtuale, vedere gli articoli seguenti per informazioni su come gestire e ripristinare le macchine virtuali:

- [Gestire e monitorare il backup delle macchine virtuali di Azure](backup-azure-manage-vms.md) 
- [Ripristino di macchine virtuali](backup-azure-arm-restore-vms.md) 
