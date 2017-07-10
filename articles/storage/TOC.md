# Panoramica

## [Introduzione](storage-introduction.md)
## [Scegliere BLOB, file o dischi dati](storage-decide-blobs-files-disks.md)

# Attività iniziali

## [Creare un account di archiviazione](storage-create-storage-account.md)

## Archiviazione BLOB
### [.NET](storage-dotnet-how-to-use-blobs.md)
### [Java](storage-java-how-to-use-blob-storage.md)
### [Node.js](storage-nodejs-how-to-use-blob-storage.md)
### [C++](storage-c-plus-plus-how-to-use-blobs.md)
### [PHP](storage-php-how-to-use-blobs.md)
### [Python](storage-python-how-to-use-blob-storage.md)
### [Ruby](storage-ruby-how-to-use-blob-storage.md)
### [iOS](storage-ios-how-to-use-blob-storage.md)
### [Xamarin](storage-xamarin-blob-storage.md)

## Archiviazione di accodamento
### [.NET](storage-dotnet-how-to-use-queues.md)
### [Java](storage-java-how-to-use-queue-storage.md)
### [Node.js](storage-nodejs-how-to-use-queues.md)
### [C++](storage-c-plus-plus-how-to-use-queues.md)
### [PHP](storage-php-how-to-use-queues.md)
### [Python](storage-python-how-to-use-queue-storage.md)
### [Ruby](storage-ruby-how-to-use-queue-storage.md)

## Archiviazione tabelle
### [.NET](storage-dotnet-how-to-use-tables.md)
### [F#](/dotnet/articles/fsharp/using-fsharp-on-azure/table-storage)
### [Java](storage-java-how-to-use-table-storage.md)
### [Node.js](storage-nodejs-how-to-use-table-storage.md)
### [C++](storage-c-plus-plus-how-to-use-tables.md)
### [PHP](storage-php-how-to-use-table-storage.md)
### [Python](storage-python-how-to-use-table-storage.md)
### [Ruby](storage-ruby-how-to-use-table-storage.md)

## Archiviazione file
### [Windows, .NET, PowerShell](storage-dotnet-how-to-use-files.md)
### [Linux](storage-how-to-use-files-linux.md)
### [Java](storage-java-how-to-use-file-storage.md)
### [C++](storage-c-plus-plus-how-to-use-files.md)
### [Python](storage-python-how-to-use-file-storage.md)

## Archiviazione su disco 
### [Creare una VM con Resource Manager e PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md)
### [Creare una VM Linux usando l'interfaccia della riga di comando di Azure 2.0](../virtual-machines/linux/quick-create-cli.md)
### [Collegare un disco gestito a una VM Windows tramite PowerShell](../virtual-machines/windows/attach-disk-ps.md)
### [Aggiungere un disco gestito a una VM Linux](../virtual-machines/linux/add-disk.md)
### [Creare una copia del disco rigido virtuale archiviato come disco gestito usando gli snapshot in Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)
### [Creare una copia del disco rigido virtuale archiviato come disco gestito usando gli snapshot in Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)
### [Uso di dischi gestiti nei modelli di Resource Manager](storage-using-managed-disks-template-deployments.md)

# Procedure
## [Creare un account di archiviazione](storage-create-storage-account.md)
## Usare i BLOB
### [Panoramica del servizio](https://msdn.microsoft.com/library/dd179376.aspx)
### [Ricerca nell'archivio BLOB tramite Ricerca di Azure](../search/search-blob-storage-integration.md)
### [livelli ad accesso frequente e sporadico](storage-blob-storage-tiers.md)
### [Domini personalizzati](storage-custom-domain-name.md)
### [Usare la rete CDN di Azure per accedere ai BLOB con domini personalizzati tramite HTTPS](storage-https-custom-domain-cdn.md) 
### [Accesso anonimo ai BLOB](storage-manage-access-to-resources.md)
### [Esempi](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob)
## Usare le code
### [Concetti](https://msdn.microsoft.com/library/dd179353.aspx)
### [Esempi](https://azure.microsoft.com/documentation/samples/?service=storage&term=queue)
## Usare le tabelle
### [Panoramica](https://msdn.microsoft.com/library/dd179463.aspx)
### [Guida alla progettazione di tabelle](storage-table-design-guide.md)
### [Esempi](https://azure.microsoft.com/documentation/samples/?service=storage&term=table)
## Usare i file
### [Panoramica](/rest/api/storageservices/File-Service-Concepts)
### [Risolvere i problemi relativi a File di Azure: Windows](storage-troubleshoot-windows-file-connection-problems.md)
### [Risolvere i problemi relativi a File di Azure: Linux](storage-troubleshoot-linux-file-connection-problems.md)
### [Esempi](https://azure.microsoft.com/documentation/samples/?service=storage&term=file)
## Usare i dischi
### [Dischi e VHD per VM Windows](storage-about-disks-and-vhds-windows.md)
### [Dischi e VHD per VM Linux](storage-about-disks-and-vhds-linux.md)
### [Panoramica di Azure Managed Disks](storage-managed-disks-overview.md)
### [Eseguire la migrazione di macchine virtuali di Azure ad Azure Managed Disks](../virtual-machines/windows/migrate-to-managed-disks.md)
### [Eseguire la migrazione da AWS e altre piattaforme a Managed Disks](../virtual-machines/windows/on-prem-to-azure.md)
### [Domande frequenti sui dischi delle macchine virtuali IaaS di Azure](storage-faq-for-disks.md)
### Archiviazione Premium
#### [Archiviazione Premium con prestazioni elevate per dischi delle macchine virtuali](storage-premium-storage.md)
#### [Migrazione ad Archiviazione Premium con Azure Site Recovery](storage-migrate-to-premium-storage-using-azure-site-recovery.md)
#### [Progettare per prestazioni elevate](storage-premium-storage-performance.md)
### Archiviazione standard
#### [Archiviazione Standard economicamente conveniente e dischi gestiti e non gestiti di macchine virtuali](storage-standard-storage.md)
### Uso di dischi non gestiti
#### [Eseguire la migrazione ad Archiviazione Premium](storage-migration-to-premium-storage.md)
#### [Eseguire il backup dei dischi non gestiti delle macchine virtuali con snapshot incrementali](storage-incremental-snapshots.md)
## Pianificare e progettare
### [Replica](storage-redundancy.md)
### [Obiettivi di scalabilità e prestazioni](storage-scalability-targets.md)
### [Elenco di controllo di prestazioni e scalabilità](storage-performance-checklist.md)
### [Concorrenza](storage-concurrency.md)
## Sviluppare
### Esempi
#### [.NET](storage-samples-dotnet.md)
#### [Java](storage-samples-java.md)
### [Progettazione di app HA con RA-GRS](storage-designing-ha-apps-with-ragrs.md)
### [Configurare le stringhe di connessione](storage-configure-connection-string.md)
### [Usare l'emulatore di archiviazione](storage-use-emulator.md)
### [Impostare e recuperare proprietà e metadati](storage-properties-metadata.md)
## Gestisci
### [PowerShell](storage-powershell-guide-full.md)
### [Interfaccia della riga di comando di Azure 2.0](storage-azure-cli.md)
### [Interfaccia della riga di comando di Azure 1.0](storage-azure-cli-nodejs.md)
### [Automazione di Azure](automation-manage-storage.md)
## Proteggere
### [Guida alla sicurezza](storage-security-guide.md)
### [Crittografia per i dati inattivi](storage-service-encryption.md)
### [Crittografia dei dati inattivi con le chiavi clienti](storage-service-encryption-customer-managed-keys.md)
### [Autenticazione della chiave condivisa](/rest/api/storageservices/Authentication-for-the-Azure-Storage-Services)
### [Firme di accesso condiviso](storage-dotnet-shared-access-signature-part-1.md)
### [Esercitazione: Crittografare e decrittografare BLOB con l'insieme di credenziali delle chiavi di Azure](storage-encrypt-decrypt-blobs-key-vault.md)
### [Richiedere il trasferimento sicuro](storage-require-secure-transfer.md)
### Crittografia lato client
#### [.NET](storage-client-side-encryption.md)
#### [Java](storage-client-side-encryption-java.md)
#### [Python](storage-client-side-encryption-python.md)
## Monitorare e risolvere i problemi
### [Risolvere i problemi di Storage Explorer](storage-explorer-troubleshooting.md)
### Metriche e registrazione
#### [Analisi dell'archiviazione](storage-analytics.md)
#### [Abilitare e visualizzare le metriche](storage-enable-and-view-metrics.md)
#### [Monitoraggio, diagnosi e risoluzione dei problemi](storage-monitoring-diagnosing-troubleshooting.md)
#### [Esercitazione sulla risoluzione dei problemi](storage-e2e-troubleshooting.md)
### Risolvere gli errori relativi all'eliminazione del disco
#### [Distribuzione di Gestione risorse](storage-resource-manager-cannot-delete-storage-account-container-vhd.md)
#### [Distribuzione classica](storage-cannot-delete-storage-account-container-vhd.md)
### [Risolvere i problemi relativi all'archiviazione di file](storage-troubleshoot-file-connection-problems.md)
### [Informazioni aggiuntive sul ripristino di emergenza](storage-disaster-recovery-guidance.md)
## Trasferire i dati
### [Spostare dati da e verso Archiviazione](storage-moving-data.md)
### [AzCopy in Windows](storage-use-azcopy.md)
### [AzCopy in Linux](storage-use-azcopy-linux.md)
### [Uso del servizio Importazione/Esportazione](storage-import-export-service.md)
### [Uso dello strumento Importazione/Esportazione](storage-import-export-tool-how-to.md)
#### [Configurazione dello strumento Importazione/Esportazione](storage-import-export-tool-setup.md)
#### [Preparazione dei dischi rigidi per un processo di importazione](storage-import-export-tool-preparing-hard-drives-import.md)
##### [Impostazione di proprietà e metadati durante il processo di importazione](storage-import-export-tool-setting-properties-metadata-import.md)
##### [Flusso di lavoro campione per preparare i dischi rigidi per un processo di importazione](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
##### [Riferimento rapido per i comandi usati più di frequente per i processi di importazione](storage-import-export-tool-quick-reference.md)
#### [Anteprima dell'uso del disco per un processo di esportazione](storage-import-export-tool-previewing-drive-usage-export-v1.md)
#### [Revisione dello stato dei processi con i file di log di copia](storage-import-export-tool-reviewing-job-status-v1.md)
#### [Riparazione di un processo di importazione](storage-import-export-tool-repairing-an-import-job-v1.md)
#### [Riparazione di un processo di esportazione](storage-import-export-tool-repairing-an-export-job-v1.md)
#### [Risoluzione dei problemi relativi allo strumento Importazione/Esportazione](storage-import-export-tool-troubleshooting-v1.md)
#### [Formato di file manifesto del servizio Importazione/Esportazione](storage-import-export-file-format-manifest.md)
#### [Formato di file di proprietà e metadati del servizio Importazione/Esportazione](storage-import-export-file-format-metadata-and-properties.md)
#### [Formato file log del servizio Importazione/Esportazione](storage-import-export-file-format-log.md)
### [Uso dello strumento Importazione/Esportazione (v1)](storage-import-export-tool-how-to-v1.md)
#### [Configurazione dello strumento Importazione/Esportazione](storage-import-export-tool-setup-v1.md)
#### [Preparazione dei dischi rigidi per un processo di importazione](storage-import-export-tool-preparing-hard-drives-import-v1.md)
##### [Impostazione di proprietà e metadati durante il processo di importazione](storage-import-export-tool-setting-properties-metadata-import-v1.md)
##### [Flusso di lavoro campione per preparare i dischi rigidi per un processo di importazione](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
##### [Riferimento rapido per i comandi usati più di frequente per i processi di importazione](storage-import-export-tool-quick-reference-v1.md)
#### [Anteprima dell'uso del disco per un processo di esportazione](storage-import-export-tool-previewing-drive-usage-export-v1.md)
#### [Revisione dello stato dei processi con i file di log di copia](storage-import-export-tool-reviewing-job-status-v1.md)
#### [Riparazione di un processo di importazione](storage-import-export-tool-repairing-an-import-job-v1.md)
#### [Riparazione di un processo di esportazione](storage-import-export-tool-repairing-an-export-job-v1.md)
#### [Risoluzione dei problemi relativi allo strumento Importazione/Esportazione](storage-import-export-tool-troubleshooting-v1.md)
#### [Formato di file manifesto del servizio Importazione/Esportazione](storage-import-export-file-format-manifest.md)
#### [Formato di file di proprietà e metadati del servizio Importazione/Esportazione](storage-import-export-file-format-metadata-and-properties.md)
#### [Formato file log del servizio Importazione/Esportazione](storage-import-export-file-format-log.md)
### [Uso dell'API REST del servizio Importazione/Esportazione di Azure](storage-import-export-using-the-rest-api.md)
#### [Creazione di un processo di importazione](storage-import-export-creating-an-import-job.md)
#### [Creazione di un processo di esportazione](storage-import-export-creating-an-export-job.md)
#### [Recupero delle informazioni sullo stato per un processo](storage-import-export-retrieving-state-info-for-a-job.md)
#### [Enumerazione dei processi](storage-import-export-enumerating-jobs.md)
#### [Annullamento ed eliminazione dei processi](storage-import-export-cancelling-and-deleting-jobs.md)
#### [Backup dei manifesti delle unità](storage-import-export-backing-up-drive-manifests.md)
#### [Diagnostica e ripristino dagli errori per i processi di Importazione/Esportazione](storage-import-export-diagnostics-and-error-recovery.md)
# Riferimento
## [PowerShell](/powershell/module/azure.storage)
## [Interfaccia della riga di comando di Azure](/cli/azure/storage)
## .NET
### [Gestione risorse](/dotnet/api/microsoft.azure.management.storage)
### [Spostamento dei dati](/dotnet/api/microsoft.windowsazure.storage.datamovement)
### [BLOB, code, tabelle e file](https://msdn.microsoft.com/library/azure/mt347887.aspx)
## [Java](http://azure.github.io/azure-storage-java/)
## [Node.js](http://azure.github.io/azure-storage-node)
## [Ruby](http://azure.github.io/azure-storage-ruby)
## [PHP](http://azure.github.io/azure-storage-php/)
## [Python](https://azure-storage.readthedocs.io/en/latest/index.html)
## [C++](http://azure.github.io/azure-storage-cpp)
## [iOS](http://azure.github.io/azure-storage-ios/)
## [Android](http://azure.github.io/azure-storage-android)
## REST
### [BLOB, code, tabelle e file](/rest/api/storageservices)
### [Provider di risorse](/rest/api/storagerp)
### [Importazione/Esportazione](/rest/api/storageimportexport)

# Risorse correlate
## Portale classico
### [Creare un account di archiviazione](storage-create-storage-account-classic-portal.md)
### [Abilitare e visualizzare le metriche](storage-enable-and-view-metrics-classic-portal.md)
### [Monitoraggio, diagnosi e risoluzione dei problemi](storage-monitoring-diagnosing-troubleshooting-classic-portal.md)
### [Esercitazione sulla risoluzione dei problemi](storage-e2e-troubleshooting-classic-portal.md)

# Risorse
## [Roadmap per Azure](https://azure.microsoft.com/roadmap/)
## [Strumenti client di Archiviazione di Azure](storage-explorers.md)
## [Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
## [Prezzi](https://azure.microsoft.com/pricing/details/storage/blobs/)
## [Aggiornamenti del servizio](https://azure.microsoft.com/updates/?product=storage)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/windows-azure-storage)
## [Video](https://azure.microsoft.com/documentation/videos/index/?services=storage)

## Azure Storage Explorer
### [Storage Explorer (anteprima)](../vs-azure-tools-storage-manage-with-storage-explorer.md)
### [Gestire BLOB con Storage Explorer (anteprima)](../vs-azure-tools-storage-explorer-blobs.md)
### [Uso di Storage Explorer (anteprima) con Archiviazione file di Azure](../vs-azure-tools-storage-explorer-files.md)

## Pacchetti NuGet
### [Libreria client di archiviazione di Azure per .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
### [Libreria di spostamento dei dati di Archiviazione di Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)
### [Gestione configurazione di Azure](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)

## Codice sorgente
### .NET
#### [BLOB, code, tabelle e file](https://github.com/Azure/azure-storage-net)
#### [Spostamento dei dati](https://github.com/Azure/azure-storage-net-data-movement)
#### [Provider di risorse](https://github.com/Azure/azure-sdk-for-net)
### [Node.js](http://azure.github.io/azure-storage-node/)
### [Java](https://github.com/Azure/azure-storage-java)
### [C++](https://github.com/Azure/azure-storage-cpp)
### [PHP](https://github.com/Azure/azure-storage-php)
### [Python](https://github.com/Azure/azure-storage-python)
### [Ruby](https://github.com/Azure/azure-storage-ruby)
### [iOS](https://github.com/Azure/azure-storage-ios)
