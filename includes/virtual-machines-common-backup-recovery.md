
## <a name="azure-backup"></a>Backup di Azure

Per il backup di VM di Azure che eseguono carichi di lavoro di produzione, usare Backup di Azure. Backup di Azure supporta i backup coerenti con l'applicazione per le macchine virtuali di Windows e di Linux. Backup di Azure crea punti di recupero che vengono archiviati negli insiemi di credenziali di ripristino con ridondanza geografica. Quando si ripristina da un punto di recupero, è possibile ripristinare la macchina virtuale intera o parziale. 

Per una semplice introduzione pratica a Backup di Azure per macchine virtuali di Azure, vedere "Eseguire il backup di macchine virtuali di Azure" per [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) o [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).

Per altre informazioni sul funzionamento di Backup di Azure, vedere [Pianificare l'infrastruttura di backup delle VM in Azure](../articles/backup/backup-azure-vms-introduction.md)


## <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery protegge le VMs da uno scenario reale di ripristino di emergenza, quando in un'intera area si verifica un'interruzione a causa di un grave disastro naturale o di un'interruzione diffusa del servizio. È possibile configurare Azure Site Recovery per le macchine virtuali in modo che sia possibile recuperare l'applicazione con un solo clic in pochi minuti. È possibile eseguire la replica in un'area di Azure di propria scelta, non limitata alle aree associate. 

È possibile eseguire esercitazioni per il ripristino di emergenza con failover di test su richiesta, senza influire sui carichi di lavoro di produzione o sulla replica continua. Creare piani di ripristino per orchestrare il failover e il failback dell'intera applicazione in esecuzione in più VM. La funzionalità del piano di ripristino è integrata con i runbook di Automazione di Azure.

Iniziare eseguendo [la replica delle macchine virtuali](https://aka.ms/a2a-getting-started). 

## <a name="managed-snapshots"></a>Snapshot gestiti 

Negli ambienti di sviluppo e test, gli snapshot offrono un'opzione rapida e semplice per il backup di VM che usano Managed Disks. Uno snapshot gestito è una copia completa di sola lettura di un disco gestito. Gli snapshot esistono indipendentemente dal disco di origine e possono essere usati per creare nuove istanze di Managed Disks per la ricompilazione di una VM. Vengano addebitati in funzione della parte di disco usata. Ad esempio, se si crea uno snapshot di un disco gestito con una capacità di provisioning di 64 GB e una dimensione di dati effettivamente usata di 10 GB, verranno addebitati solo gli snapshot relativi alla dimensione di dati usata di 10 GB.  

Per altre informazioni sulla creazione di snapshot, vedere:

* [Creare una copia del disco rigido virtuale archiviato come disco gestito usando gli snapshot in Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Creare una copia del disco rigido virtuale archiviato come disco gestito usando gli snapshot in Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>Passaggi successivi
È possibile provare Backup di Azure seguendo la "Esercitazione sul backup di macchine virtuali Windows " per [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) o [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).
