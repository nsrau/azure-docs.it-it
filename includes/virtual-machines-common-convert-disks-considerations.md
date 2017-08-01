* Se il disco non gestito si trova in un account di archiviazione crittografato in precedenza tramite Crittografia del servizio di archiviazione di Azure, non è possibile convertirlo in un disco gestito. Per la procedura per copiare e usare questi dischi rigidi virtuali nei dischi gestiti, vedere la sezione [Dischi gestiti e Crittografia del servizio di archiviazione di Azure](#managed-disks-and-azure-storage-service-encryption) più avanti in questo articolo.

* La conversione richiede un riavvio della macchina virtuale, quindi pianificare la migrazione delle macchine virtuali in una finestra di manutenzione preesistente. 

* La conversione non è reversibile. 

* Assicurarsi di testare la conversione. Eseguire la migrazione di una macchina virtuale di test prima di eseguire la migrazione nell'ambiente di produzione.

* Durante la conversione la macchina virtuale verrà deallocata. La macchina virtuale riceve un nuovo indirizzo IP quando viene avviata dopo la conversione. Se necessario, è possibile [assegnare un indirizzo IP statico](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md) alla VM.

* I dischi rigidi virtuali originali e l'account di archiviazione usato dalla macchina virtuale prima della conversione non verranno eliminati e i costi correlati continueranno a essere addebitati. Per evitare addebiti per questi elementi, eliminare i BLOB VHD originali dopo aver verificato che la conversione sia stata completata.