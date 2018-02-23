---
title: Abilitare il backup di una macchina virtuale di Azure durante la creazione | Microsoft Docs
description: Vedere la procedura per abilitare il backup di una macchina virtuale di Azure durante il processo di creazione.
services: backup, virtual-machines
documentationcenter: 
author: markgalioto
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: 4041fc555fe4b61d10f84236dcae5156c6282fd3
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2018
---
# <a name="enable-backup-during-azure-virtual-machine-creation"></a>Abilitare il backup durante la creazione di una macchina virtuale di Azure 

Il servizio Backup di Azure fornisce un'interfaccia per la creazione e la configurazione di backup nel cloud. È consigliabile proteggere i dati eseguendo backup, definiti anche punti di recupero, a intervalli regolari. Backup di Azure crea punti di ripristino che possono essere archiviati in insiemi di credenziali di ripristino con ridondanza geografica. Questo articolo illustra come abilitare il backup durante la creazione di una macchina virtuale (VM) nel portale di Azure.  

## <a name="log-in-to-azure"></a>Accedere ad Azure 

Se non è già stato eseguito l'accesso al proprio account, accedere al [portale di Azure](http://portal.azure.com).
 
## <a name="create-virtual-machine-with-backup-configured"></a>Creare una macchina virtuale con backup configurato 

1. Nell'angolo superiore sinistro del portale di Azure fare clic su **Nuovo**. 

2. Selezionare **Calcolo**, quindi selezionare un'immagine della macchina virtuale.   

3. Immettere le informazioni relative alla macchina virtuale. Il nome utente e la password specificati verranno usati per accedere alla macchina virtuale. Al termine fare clic su **OK**. 

4. Selezionare una dimensione per la VM.  

5. In **Impostazioni > Backup** fare clic su **Abilitato** per visualizzare le impostazioni di configurazione del backup. È possibile accettare i valori predefiniti e fare clic su **OK** nella pagina Impostazioni per passare alla pagina di riepilogo per creare la macchina virtuale. Per modificare i valori, seguire questa procedura.  

6. Creare o selezionare un insieme di credenziali di Servizi di ripristino che contiene i backup della macchina virtuale. Se si sta creando un insieme di credenziali di Servizi di ripristino, è possibile scegliere un gruppo di risorse per l'insieme di credenziali.  

    ![Configurazione del backup nella pagina di creazione della macchina virtuale](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > Il gruppo di risorse per l'insieme di credenziali di Servizi di ripristino può essere diverso da quello per la macchina virtuale.  
    > 
    > 

7. Per impostazione predefinita, viene selezionato automaticamente un criterio di backup che consente di proteggere in modo rapido la macchina virtuale. Un criterio di backup specifica la frequenza con cui eseguire snapshot del backup e la durata di conservazione delle copie di backup. È possibile accettare i criteri predefiniti oppure è possibile creare o selezionare criteri di backup differenti. Per modificare i criteri di backup, selezionare **Criteri di backup** e modificare i valori dei criteri.  

8. Quando si è soddisfatti dei valori di configurazione del backup, nella pagina Impostazioni fare clic su **OK**.  

9. Nella pagina di riepilogo, dopo l'esito positivo della convalida, fare clic su **Crea** per creare una macchina virtuale che usi le impostazioni di backup configurate. 

## <a name="initiate-a-backup-after-creating-the-vm"></a>Avviare un backup dopo la creazione della macchina virtuale 

Anche se i criteri di backup sono stati creati, è consigliabile definire un backup iniziale. Per visualizzare i dettagli del backup per la macchina virtuale al termine del modello di creazione della macchina virtuale, dall'impostazione **Operazioni** nel menu a sinistra fare clic su **Backup**. È possibile usare questa impostazione per attivare un backup su richiesta, ripristinare una macchina virtuale completa o tutti i dischi, ripristinare file da un backup di macchina virtuale o modificare i criteri di backup associati alla macchina virtuale.  

## <a name="frequently-asked-questions"></a>Domande frequenti 

### <a name="which-vm-images-enable-backup-at-the-time-of-vm-creation"></a>Quali immagini di macchina virtuale abilitano il backup al momento della creazione della macchina virtuale? 

Per l'abilitazione del backup durante la creazione della macchina virtuale, sono supportate le immagini di base seguenti pubblicate da Microsoft. Per altre macchine virtuali, è possibile abilitare il backup dopo la creazione della macchina virtuale. Per altre informazioni, consultare l'articolo sull'[abilitazione del backup dopo la creazione della macchina virtuale](quick-backup-vm-portal.md). 

- **Windows**: Windows Server 2016 Datacenter, Windows Server 2016 Datacenter core, Windows Server 2012 Datacenter, Windows Server 2012 R2 Datacenter, Windows Server 2008 R2 SP1 
- **Ubuntu**: Ubuntu Server 1710, Ubuntu Server 1704, Ubuntu Server 1604 (LTS), Ubuntu Server 1404 (LTS) 
- **Redhat**: RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4 
- **SUSE**: SUSE Linux Enterprise Server 11 SP4, 12 SP2, 12 SP3 
- **Debian**: Debian 8, Debian 9 
- **CentOS**: CentOS 6.9, CentOS 7.3 
- **Oracle Linux**: Oracle Linux 6.7, 6.8, 6.9, 7.2, 7.3 
 
### <a name="is-backup-cost-included-in-the-vm-cost"></a>Il costo del backup è incluso nel costo della macchina virtuale? 

No, i costi di backup sono separati, o diversi, dai costi delle macchine virtuali. Per altre informazioni sui prezzi del backup, vedere la pagina [Prezzi di Backup](https://azure.microsoft.com/pricing/details/backup/).
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>Quali autorizzazioni sono necessarie per abilitare il backup in una macchina virtuale? 

Un utente con ruolo collaboratore della macchina virtuale può abilitare il backup della macchina virtuale. Un utente con ruolo personalizzato deve disporre delle autorizzazioni seguenti per abilitare il backup nella macchina virtuale. 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Se l'insieme di credenziali dei servizi di ripristino e la macchina virtuale dispongono di gruppi di risorse differenti, assicurarsi di disporre delle autorizzazioni di scrittura nel gruppo di risorse dell'insieme di credenziali dei servizi di ripristino.  

## <a name="next-steps"></a>Passaggi successivi 

Dopo aver protetto la macchina virtuale, vedere gli articoli seguenti per ottenere informazioni sulle attività di gestione delle macchine virtuali e su come ripristinare le macchine virtuali. 

- [Gestire e monitorare il backup delle macchine virtuali di Azure](backup-azure-manage-vms.md) 
- [Ripristino di macchine virtuali](backup-azure-arm-restore-vms.md) 
