---
title: Configurare un criterio di replica per la replica Hyper-V in un sito secondario con Azure Site Recovery | Microsoft Docs
description: Descrive come configurare un criterio per la replica di VM Hyper-V in un sito VMM secondario con Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5d9b79cf-89f2-4af9-ac8e-3a32ad8c6c4d
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: a3ecc555e049914d7bffdddce7b0522f09362d24
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="step-8-set-up-a-replication-policy"></a>Passaggio 8: Configurare un criterio di replica

Dopo la configurazione del [mapping di rete](vmm-to-vmm-walkthrough-network-mapping.md), usare le istruzioni disponibili in questo articolo per configurare un criterio di replica per la replica di macchine virtuali Hyper-V in un sito secondario tramite [Azure Site Recovery](site-recovery-overview.md).

È possibile inviare commenti nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Prima di iniziare

- Quando si crea un criterio di replica, tutti gli host che usano il criterio devono avere lo stesso sistema operativo. Il cloud VMM può contenere host Hyper-V che eseguono versioni diverse di Windows Server, ma in questo caso sono necessari più criteri di replica.
- È possibile eseguire offline la replica iniziale.

## <a name="configure-replication-settings"></a>Configurare le impostazioni di replica

1. Per creare nuovi criteri di replica, fare clic su **Preparare l'infrastruttura** > **Impostazioni della replica** > **+Crea e associa**.

    ![Rete](./media/vmm-to-vmm-walkthrough-replication/gs-replication.png)
2. In **Criteri di creazione e associazione**specificare il nome dei criteri. Il tipo di origine e di destinazione deve essere **Hyper-V**.
3. In **Versione host Hyper-V** selezionare il sistema operativo in esecuzione nell'host.
4. In **Tipo di autenticazione** e **Porta di autenticazione** specificare come viene autenticato il traffico tra il server host Hyper-V primario e quello di ripristino. Selezionare **Certificato** a meno che non sia configurato un ambiente Kerberos funzionante. Azure Site Recovery configura automaticamente i certificati per l'autenticazione HTTPS. Non è necessario intervenire manualmente. Per impostazione predefinita, le porte 8083 e 8084 (per i certificati) sono aperte in Windows Firewall per i server host Hyper-V. Se si seleziona **Kerberos**, verrà usato un ticket Kerberos per l'autenticazione reciproca dei server host. Questa impostazione è rilevante solo per i server host Hyper-V in esecuzione su Windows Server 2012 R2.
5. In **Frequenza di copia**specificare la frequenza con cui replicare i dati differenziali dopo la replica iniziale, ogni 30 secondi oppure ogni 5 o 15 minuti.
6. In **Conservazione del punto di ripristino**specificare la durata in ore dell'intervallo di conservazione per ogni punto di ripristino. I computer protetti possono essere ripristinati in qualsiasi punto all'interno di un intervallo.
7. In **Frequenza snapshot coerenti con l'app** specificare la frequenza, da 1 a 12 ore, per la creazione di punti di ripristino contenenti snapshot coerenti con l'applicazione. Hyper-V utilizza due tipi di snapshot, uno snapshot standard che fornisce uno snapshot incrementale dell'intera macchina virtuale e uno snapshot coerente con l'applicazione che accetta uno snapshot temporizzato dei dati dell'applicazione all'interno della macchina virtuale. Negli snapshot coerenti dell'applicazione viene usato il servizio Copia Shadow del volume (VSS) per garantire che le applicazioni siano coerenti durante la creazione dello snapshot. L'abilitazione di snapshot coerenti con l'applicazione influirà sulle prestazioni delle applicazioni in esecuzione nelle macchine virtuali di origine. Assicurarsi che il valore impostato sia inferiore al numero di punti di ripristino aggiuntivi configurati.
8. In **Compressione trasferimento dati**specificare se i dati replicati che vengono trasferiti devono essere compressi.
9. Selezionare **Eliminare la macchina virtuale di replica** per specificare che la macchina virtuale di replica deve essere eliminata nel caso in cui si disabiliti la protezione per la VM di origine. Con questa impostazione abilitata, quando si disabilita la protezione per la macchina virtuale di origine, questa viene rimossa dalla console di Site Recovery, le impostazioni di Site Recovery relative a VMM vengono rimosse dalla console VMM e la replica viene eliminata.
10. Se si esegue la replica in rete, in **Metodo di replica iniziale** specificare se si preferisce avviare la replica iniziale o pianificarla. Per risparmiare larghezza di banda di rete, è opportuno pianificarla al di fuori dell'orario di lavoro. Fare quindi clic su **OK**.

     ![Criteri di replica](./media/vmm-to-vmm-walkthrough-replication/gs-replication2.png)
11. Quando si creano nuovi criteri, questi vengono associati automaticamente al cloud VMM. In **Criteri di replica** fare clic su **OK**. È possibile associare altri cloud VMM (e le VM in essi contenute) a questi criteri di replica in **Replica** > nome del criterio > **Associate VMM Cloud** (Associa cloud VMM).

     ![Criteri di replica](./media/vmm-to-vmm-walkthrough-replication/policy-associate.png)



## <a name="prepare-for-offline-initial-replication"></a>Preparare la replica iniziale offline

Per la copia iniziale dei dati è possibile eseguire la replica non in linea. Per prepararla, procedere come segue:

* Nel server di origine specificare il percorso dal quale verrà eseguita l'esportazione dei dati. Assegnare il controllo completo per NTFS e le autorizzazioni di condivisione al servizio VMM nel percorso di esportazione. Nel server di destinazione specificare il percorso dal quale verrà eseguita l'importazione dei dati. Assegnare le stesse autorizzazioni indicate in precedenza per questo percorso esportazione.
* Se il percorso importazione o esportazione è condiviso, assegnare l'appartenenza al gruppo Administrator, Power User, Print Operators o Server Operators per l'account del servizio VMM nel computer remoto in cui si trova il percorso condiviso.
* Se si usano account RunAs per aggiungere host, nei percorsi di importazione e di esportazione assegnare autorizzazioni di lettura e scrittura per gli account RunAs in VMM.
* Le condivisioni di importazione ed esportazione non devono essere posizionate nei computer usati come server host Hyper-V, in quanto la configurazione del loopback non è supportata da Hyper-V.
* In Active Directory, in ogni server host Hyper-V contenente le macchine virtuali che si desidera proteggere, abilitare e configurare la delega vincolata affinché i computer remoti nei quali sono situati i percorsi di esportazione e importazione siano considerati attendibili, come illustrato di seguito:
  1. Nel controller di dominio, aprire **Utenti e computer di Active Directory**.
  2. Nell'albero della console fare clic su **NomeDominio** > **Computer**.
  3. Fare clic con il pulsante destro del mouse sul nome del server host Hyper-V e scegliere **Proprietà**.
  4. Nella scheda **Delega** fare clic su **Computer attendibile per la delega solo ai servizi specificati**.
  5. Fare clic su **Usa un qualsiasi protocollo di autenticazione**.
  6. Fare clic su **Aggiungi** > **Utenti e computer**.
  7. Digitare il nome del computer che ospita il percorso esportazione e fare clic su **OK**. Nell'elenco dei servizi disponibili tenere premuto il tasto CTRL e fare clic su **cifs** > **OK**. Ripetere l'operazione per il nome del computer che ospita il percorso esportazione. Ripetere l'operazione per eventuali altri server host Hyper-V.



## <a name="next-steps"></a>Passaggi successivi

Andare al [Passaggio 9: Abilitare la replica](vmm-to-vmm-walkthrough-enable-replication.md).
