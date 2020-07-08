---
title: Informazioni sul processo di ripristino della macchina virtuale di Azure
description: Informazioni su come il servizio backup di Azure Ripristina le macchine virtuali di Azure
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: b9a2636a2144ea40457bdc3d88786785cb012e0d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84325676"
---
# <a name="about-azure-vm-restore"></a>Informazioni sul ripristino di VM di Azure

Questo articolo descrive il modo in cui il [servizio backup di Azure](https://docs.microsoft.com/azure/backup/backup-overview) Ripristina le macchine virtuali (VM) di Azure. Sono disponibili diverse opzioni di ripristino. Verranno illustrati i vari scenari supportati.

## <a name="concepts"></a>Concetti

- **Punto di ripristino** (noto anche come **punto di ripristino**): un punto di ripristino è una copia dei dati originali di cui viene eseguito il backup.

- **Tier (snapshot e Vault)**: il backup delle macchine virtuali di Azure avviene in due fasi:

  - Nella fase 1, lo snapshot assunto viene archiviato insieme al disco. Questa operazione viene definita **livello snapshot**. I ripristini del livello snapshot sono più veloci (rispetto al ripristino dall'insieme di credenziali) perché eliminano il tempo di attesa per la copia degli snapshot nell'insieme di credenziali prima di attivare il ripristino. Il ripristino dal livello snapshot viene anche definito [ripristino istantaneo](https://docs.microsoft.com/azure/backup/backup-instant-restore-capability).
  - Nella fase 2, lo snapshot viene trasferito e archiviato nell'insieme di credenziali gestito dal servizio backup di Azure. Questa operazione viene definita **livello**di insieme di credenziali.

- **Ripristino del percorso originale (OLR)**: un ripristino eseguito dal punto di ripristino alla macchina virtuale di Azure di origine da cui sono stati eseguiti i backup, sostituendolo con lo stato archiviato nel punto di ripristino. Questo sostituisce il disco del sistema operativo e i dischi dati della VM di origine.

- **Ripristino in un percorso alternativo (ALR)**: ripristino eseguito dal punto di ripristino a un server diverso dal server originale in cui sono stati eseguiti i backup.

- **Ripristino a livello di elemento (ILR):** Ripristino di singoli file o cartelle all'interno della macchina virtuale dal punto di ripristino

- **Disponibilità (tipi di replica)**: backup di Azure offre due tipi di replica per assicurare la disponibilità elevata di archiviazione/dati:
  - [Archiviazione con ridondanza locale (con ridondanza locale)](../storage/common/storage-redundancy-lrs.md) replica i dati tre volte (crea tre copie dei dati) in un'unità di scala di archiviazione in un Data Center. Tutte le copie dei dati si trovano nella stessa area geografica. L'archiviazione con ridondanza locale è un'opzione a costo contenuto per la protezione dei dati da errori hardware locali.
  - L' [archiviazione con ridondanza geografica (GRS)](../storage/common/storage-redundancy-grs.md) è l'opzione di replica predefinita e consigliata. L'archiviazione con ridondanza geografica replica i dati in un'area secondaria a centinaia di chilometri di distanza dalla posizione primaria dei dati di origine. L'archiviazione con ridondanza geografica è più costosa dell'archiviazione con ridondanza locale, ma offre un livello più elevato di durabilità per i dati, anche in presenza di un'interruzione di servizio a livello di area.

- **Ripristino tra più aree (CRR)**: come una delle [Opzioni di ripristino](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-options), il ripristino tra aree (CRR) consente di ripristinare macchine virtuali di Azure in un'area secondaria, ovvero un' [area abbinata ad Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions).

## <a name="restore-scenarios"></a>Scenari di ripristino

![Scenari di ripristino ](./media/about-azure-vm-restore/recovery-scenarios.png)

| **Scenario**                                                 | **Operazioni eseguite**                                             | **Utilizzo**                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Ripristinare per creare una nuova macchina virtuale](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms) | Ripristina l'intera VM in OLR (se la macchina virtuale di origine esiste ancora) o ALR | <li> Se la macchina virtuale di origine viene persa o danneggiata, è possibile ripristinare l'intera macchina virtuale  <li> È possibile creare una copia della macchina virtuale  <li> È possibile eseguire un'esercitazione di ripristino per il controllo o la conformità  <li> Questa opzione non funzionerà per le macchine virtuali di Azure create dalle immagini del Marketplace, ovvero se non sono disponibili perché la licenza è scaduta. |
| [Ripristinare i dischi della macchina virtuale](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) | Ripristinare i dischi collegati alla macchina virtuale                             |  Tutti i dischi: questa opzione consente di creare il modello e di ripristinare il disco. È possibile modificare questo modello con configurazioni speciali (ad esempio, set di disponibilità) per soddisfare i requisiti e quindi usare il modello e ripristinare il disco per ricreare la macchina virtuale. |
| [Ripristinare file specifici all'interno della VM](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm) | Scegliere punto di ripristino, Sfoglia, selezionare i file e ripristinarli nello stesso sistema operativo (o compatibile) della macchina virtuale di cui è stato eseguito il backup. |  Se si conoscono i file specifici da ripristinare, usare questa opzione anziché ripristinare l'intera macchina virtuale. |
| [Ripristinare una macchina virtuale crittografata](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) | Dal portale ripristinare i dischi e quindi usare PowerShell per creare la macchina virtuale | <li> [VM crittografata con Azure Active Directory (AAD)](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-windows-aad)  <li> [VM crittografata senza AAD](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-windows) <li> [VM crittografata *con AAD* migrata a *senza AAD*](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-faq#can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app) |
| [Ripristino tra aree](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#cross-region-restore) | Creare una nuova macchina virtuale o ripristinare i dischi in un'area secondaria (area abbinata di Azure) | <li> **Interruzione completa**: con la funzionalità di ripristino tra aree, non è previsto alcun tempo di attesa per il ripristino dei dati nell'area secondaria. È possibile avviare i ripristini nell'area secondaria anche prima che Azure dichiari un'interruzione del servizio. <li> **Interruzione parziale**: i tempi di inattività possono verificarsi in cluster di archiviazione specifici in cui backup di Azure archivia i dati sottoposti a backup o persino in rete, connettendo i cluster di backup e archiviazione di Azure associati ai dati di cui è stato eseguito il backup. Con il ripristino tra più aree, è possibile eseguire un ripristino nell'area secondaria usando una replica di dati di cui è stato eseguito il backup nell'area secondaria. <li> **Nessuna interruzione**: è possibile eseguire esercitazioni di continuità aziendale e ripristino di emergenza (BCdR) a scopo di controllo o conformità con i dati dell'area secondaria. In questo modo è possibile eseguire un ripristino dei dati di cui è stato eseguito il backup nell'area secondaria anche se non è presente un'interruzione completa o parziale nell'area primaria per le esercitazioni di continuità aziendale e ripristino di emergenza.  |

------





## <a name="next-steps"></a>Passaggi successivi

- [Domande frequenti sul ripristino delle macchine virtuali](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq#restore)
- [Metodi di ripristino supportati](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#supported-restore-methods)
- [Risolvere i problemi di ripristino](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#restore)
