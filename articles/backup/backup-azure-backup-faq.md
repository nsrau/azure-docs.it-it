<properties
   pageTitle="Domande frequenti Backup Azure | Microsoft Azure"
   description="Domande frequenti sulla soluzione Azure Backup: agente di backup, backup e conservazione, ripristino, sicurezza e altre domande frequenti sul servizio Azure Backup."
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="shreeshd"
   editor=""
   keywords="soluzione di backup; servizio di backup"/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/17/2015" ms.author="trinadhk";"giridham"; "arunak"; "jimpark"; "aashishr"/>

# Servizio di backup di Azure - Domande frequenti
Di seguito è riportato un elenco di domande frequenti su Backup di Azure. Per altre domande sulla soluzione Azure Backup, visitare il [forum di discussione](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup) e inviare una domanda. Un membro dalla community fornirà supporto agli utenti per individuare le risposte. Se una domanda viene posta più volte, verrà aggiunta a questo articolo per poter essere recuperata in modo rapido e semplice.

## Installazione e configurazione
**D1. Qual è l'elenco dei sistemi operativi supportati da cui è possibile eseguire il backup in Azure tramite Backup di Azure?** <br/> R1. Il seguente elenco di sistemi operativi è supportato da Azure Backup


| Sistema operativo | Piattaforma | SKU |
| :------------- |-------------| :-----|
| Windows 8 e versioni più recenti di SP | 64 bit | Enterprise, Pro |
| Windows 7 e versioni più recenti di SP | 64 bit | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| Windows 8.1 e versioni più recenti di SP | 64 bit | Enterprise, Pro |
| Windows 10 | 64 bit | Enterprise, Pro, Home |
|Windows Server 2012 R2 e più recenti SPs|	64 bit|	Standard, Datacenter, Foundation|
|Windows Server 2012 e versioni più recenti di SP|	64 bit|	Datacenter, Foundation, Standard|
|Windows Storage Server 2012 R2 e versioni più recenti di SP |64 bit|	Standard, Workgroup|
|Windows Storage Server 2012 e versioni più recenti di SP |64 bit |Standard, Workgroup
|Windows Server 2012 R2 e più recenti SPs |64 bit|	Essential|
|Windows Server 2008 R2 SP1, |64 bit|	Standard, Enterprise, Datacenter, Foundation|
|Windows Server 2008 SP2 |64 bit|	Standard, Enterprise, Datacenter, Foundation|

**D2. Dov'è possibile scaricare l'agente Backup di Azure più recente?** <br/> R2. È possibile scaricare la versione più recente dell'agente da [questa pagina](http://aka.ms/azurebackup_agent). Può essere installato su Windows Server, server SCDPM o client Windows

**D3. Quale versione del server SCDPM è supportata?** <br/> R3. È consigliabile installare la versione [più recente](http://aka.ms/azurebackup_agent) dell'agente Backup di Azure sull'aggiornamento cumulativo più recente di SCDPM (UR6 a luglio 2015)

**D4. Quando si configura l'agente Backup di Azure, viene richiesto di immettere le "credenziali di insieme". Le credenziali di insieme hanno una data di scadenza?** <br/> R4. Sì, le credenziali di insieme scadono dopo 48 ore. Se il file scade, accedere al portale di Azure e scaricare i file delle credenziali di insieme dall'insieme di credenziali per il backup.

**D5. Esistono limiti al numero degli insiemi di credenziali per il backup che è possibile creare in ogni sottoscrizione di Azure?** <br/> R5. Sì. A partire da marzo 2015, è possibile creare 25 insiemi di credenziali per ogni sottoscrizione. Se sono necessari più insiemi di credenziali, creare una nuova sottoscrizione.

**D6. È necessario considerare l'insieme di credenziali come un'entità di fatturazione?** <br/> R6. Sebbene sia possibile ottenere una fattura dettagliata per ogni insieme di credenziali, è consigliabile considerare una sottoscrizione di Azure come un'entità di fatturazione. È infatti coerente in tutti i servizi ed è più facile da gestire.

**D7. Esistono limiti al numero di server/computer che possono essere registrati in ogni insieme di credenziali?** <br/> R7. Sì, è possibile registrare fino a 50 computer per ogni insieme di credenziali. Per le macchine virtuali IaaS di Azure, il limite è di 100 macchine virtuali per ogni insieme di credenziali. Se è necessario registrare più computer, creare un nuovo insieme di credenziali.

**D8. Esistono limiti alla quantità di dati di cui è possibile eseguire il backup da un server/client Windows o da un server SCDPM?** <br/> R8. No.

**D9. Come si registra un server in un altro data center?**<br/> R9. In genere, i dati di backup sono inviati al data center del servizio di backup in cui sono registrati. Il modo più semplice per modificare il data center consiste nel disinstallare e reinstallare l'agente e registrarlo in un nuovo data center.

**D10. Cosa accade se si rinomina un server Windows che esegue il backup dei dati in Azure?** <br/> R10. Tutti i backup attualmente configurati verranno arrestati. Il server dovrà essere nuovamente registrato nell'insieme di credenziali per il backup e verrà considerato come un nuovo server da Servizi di ripristino, pertanto la prima operazione che si verificherà in seguito alla registrazione consisterà in un backup completo di tutti i dati inclusi nel backup e non solo delle modifiche apportate dall'ultimo backup. Se tuttavia è necessario eseguire un'operazione di ripristino, è possibile ripristinare i dati sottoposti a backup mediante l'apposita opzione per il ripristino da un altro server.

**D11. Da quali tipi di unità è possibile eseguire il backup di file e cartelle?** <br/> R11. Non è possibile eseguire il backup del set di unità/volumi seguente:

- Supporti rimovibili: l'unità deve essere segnalata come fissa per poter essere usata come origine di backup.
- Volumi di sola lettura: il volume deve essere accessibile in scrittura per garantire il funzionamento del servizio Copia Shadow del volume (VSS).
- Volumi offline: il volume deve essere online per garantire il funzionamento del servizio VSS.
- Condivisione di rete: il volume deve essere in locale nel server per poter essere sottoposto a backup online.
- Volumi protetti da BitLocker: il volume deve essere sbloccato prima di poter eseguire il backup.
- Identificazione del sistema del file: NTFS è l'unico file system supportato per questa versione del servizio di backup online.

**D12. Di quali tipi di file e cartelle è possibile eseguire il backup dal server?**<br/> R12. Sono supportati i tipi seguenti:

- Crittografato
- Compresso
- Sparse
- Compresso + Sparse
- Collegamenti reali: non supportato, ignorato
- Punto di analisi: non supportato, ignorato
- Crittografato + compresso: non supportato, ignorato
- Crittografato + Sparse: non supportato, ignorato
- Flusso compresso: non supportato, ignorato
- Flusso di tipo sparse: non supportato, ignorato

**D13. Qual è il requisito di dimensioni minime per la cartella della cache?** <br/> R13. La dimensione della cartella della cache viene determinata dalla quantità di dati sottoposti a backup. In genere, è consigliabile prevedere di allocare alla cartella della cache il 10-15% dello spazio necessario per l'archiviazione dei dati.

**D14. Come è possibile isolare dati di server specifici affinché non vengano ripristinati da altri server nell'organizzazione?**<br/> R14. Tutti i server che vengono registrati usando lo stesso insieme di credenziali saranno in grado di ripristinare i dati dei quali è stato effettuato il backup da altri server che usano la stessa passphrase. Per i server per i quali si vuole garantire che il ripristino venga eseguito solo in server specifici nell'organizzazione, è consigliabile usare passphrase separate. Ad esempio, per i server del reparto risorse umane può essere usata una passphrase, per quelli dell'ufficio contabilità un'altra e per quelli di archiviazione un'altra ancora.

**D15. È possibile eseguire la migrazione dei dati di backup tra sottoscrizioni?** <br/> R15: No

**D16. È possibile eseguire la migrazione dell'insieme di credenziali per il backup tra sottoscrizioni?** <br/> R16: No. L'insieme di credenziali viene creato a livello di sottoscrizione e non può essere riassegnato a un'altra sottoscrizione.

**D17. L'agente di Azure Backup funziona in un server che usa la deduplicazione di Windows Server 2012?** <br/> R17: Sì. Il servizio agente converte i dati deduplicati in dati normali quando si prepara l'operazione di backup. Quindi l'ottimizzazione dei dati per il backup, i dati vengono crittografati e quindi invia i dati crittografati al servizio di backup in linea.

**D18. I dati di backup vengono eliminati se si annulla un backup successivamente all'avvio?** <br/> R18: No. Nell'insieme di credenziali per il backup vengono archiviati i dati sottoposti a backup trasferiti fino al momento dell'annullamento. Backup di Azure utilizza un meccanismo di checkpoint in modo che i dati di backup Ottiene il controllo a cui fa riferimento in alcuni casi durante il backup e il successivo processo di backup può convalidare l'integrità dei file. Il successivo backup avviato risulterà incrementale rispetto ai dati erano stato eseguito il backup in precedenza. Questo fornisce un migliore utilizzo della larghezza di banda in modo che non è necessario trasferire ripetutamente gli stessi dati.

**D19. Perché viene visualizzato l'avviso "I backup di Azure non sono stati configurati per questo server", anche se in precedenza siano stati pianificati backup regolari?** <br/> R19: Questa situazione può verificarsi quando le impostazioni per la pianificazione del backup archiviate nel server locale non coincidono con quelle archiviate nell'insieme di credenziali per il backup. Quando il server o le impostazioni sono state ripristinate a uno stato noto soddisfacente, le pianificazioni di backup possono perdere la sincronizzazione. In questo caso, è opportuno riconfigurare i criteri di backup e quindi **eseguire subito il backup** per risincronizzare il server locale con Azure.

**D20. Quali regole del firewall devono essere configurate per il backup di Azure Backup?** <br/> R20. Assicurarsi che le regole del firewall consentano la comunicazione con gli URL seguenti per facilitare il backup locale per la protezione in Azure e la protezione del carico di lavoro in Azure:

- www.msftncsi.com
- *.Microsoft.com
- windowsazure.com
- *.microsoftonline.com
- *.windows.net


## Backup e conservazione
**D1. Esiste un limite alle dimensioni di ogni origine dati sottoposta a backup?** <br/> R1. A partire da agosto 2015, le dimensioni massime delle origini dati per i diversi sistemi operativi sono indicate di seguito

|Numero S. |	Sistema operativo |	Dimensione massima origine dati |
| :-------------: |:-------------| :-----|
|1| Windows Server 2012 o versioni successive| 54400 GB|
|2| Windows 8 o versione successiva| 54400 GB|
|3| Windows Server 2008, Windows Server 2008 R2 | 1700 GB|
|4| Windows 7 | 1700 GB|

La modalità di misurazione delle dimensioni dell'origine dati è indicata di seguito

|	Origine dati |	Dettagli |
| :-------------: |:-------------|
|Volume |Quantità di dati sottoposti a backup dal volume singolo di una macchina. Questa opzione è disponibile per i volumi protetti sul computer server e sul computer client.|
|Macchina virtuale Hyper-V|Somma dei dati di tutti i dischi rigidi virtuali della macchina virtuale di cui viene eseguito il backup|
|Database di Microsoft SQL Server|Dimensioni di un singolo database SQL di cui viene eseguito il backup |
|Microsoft SharePoint|Somma dei database di contenuto e di configurazione all'interno di una farm di SharePoint di cui viene eseguito il backup|
|Microsoft Exchange|Somma di tutti i database di Exchange in un server di Exchange di cui viene eseguito il backup|
|Stato del sistema/ripristino bare metal|Ogni copia del ripristino bare metal o dello stato del sistema del computer di cui viene eseguito il backup|

**D2. Esistono limiti al numero di backup giornalieri che è possibile pianificare?**<br/> R2. Sì, Backup di Azure consente di eseguire 3 copie di backup al giorno tramite un server o client Windows, 2 copie di backup al giorno tramite SCDPM e un backup al giorno per le macchine virtuali IaaS.

**D3. Esistono differenze tra i criteri di pianificazione del backup di DPM e di Backup di Azure (ad esempio, in Windows Server senza DPM)?** <br/> R3. Sì. Tramite DPM è possibile specificare la pianificazione giornaliera, settimanale, mensile e annuale, mentre da Windows Server (senza DPM) è possibile specificare solo pianificazioni giornaliere e settimanali.

**D4. Esistono differenze tra i criteri di conservazione di DPM e di Backup di Azure (ad esempio, in Windows Server senza DPM)?**<br/> R4. No, le due soluzioni offrono le stesse funzionalità. È possibile specificare criteri di conservazione giornalieri, settimanali, mensili e annuali.

**D5. È possibile configurare i criteri di conservazione in modo selettivo, ad esempio con frequenza settimanale e giornaliera ma non annuale e mensile?**<br/> R5. Sì, la struttura di memorizzazione del Backup di Azure consente di disporre della massima flessibilità nella definizione dei criteri di conservazione in base alle esigenze.

**D6. È possibile "pianificare un backup" alle 18.00 e specificare i "criteri di conservazione" a un orario diverso?**<br/> R6. No. I criteri di conservazione possono essere applicati solo ai punti di backup. Nell'immagine seguente vengono specificati i criteri di conservazione per i backup eseguiti a mezzanotte e alle 18.00. <br/>

![Pianifica backup e conservazione](./media/backup-azure-backup-faq/Schedule.png) <br/>

**D7. Viene trasferita una copia incrementale per i criteri di conservazione pianificati?** <br/> R7. No, la copia incrementale viene inviata in base all'ora indicata nella pagina di pianificazione del backup. I punti che possono essere conservati vengono determinati in base ai criteri di conservazione.

**D8. Se il backup viene conservato per un lungo periodo di tempo, è necessario molto tempo per recuperare i dati, ad esempio dal punto meno recente?** <br/> R8. No, il tempo necessario per recuperare i dati dal punto meno recente e dal punto più recente è lo stesso. Ogni punto di ripristino si comporta come un punto completo.

**D9. Se ogni punto di ripristino si comporta come un punto completo, questo influisce sul totale dell'archiviazione di backup fatturabile?**<br/> R9. I punti di conservazione tipici a lungo termine archiviano i dati di backup come punti completi. Queste soluzioni sono inefficienti dal punto di vista dell'archiviazione ma consentono un ripristino più facile e veloce. Le copie incrementali sono efficienti dal punto di vista dell'archiviazione ma richiedono il ripristino una catena di dati che influisce sui tempi di ripristino. L'architettura di archiviazione di Backup di Azure offre il meglio dei due mondi, garantendo un'archiviazione dei dati ottimale per ripristini veloci e costi di archiviazione ridotti. Questo approccio assicura che la larghezza di banda (in entrata e in uscita) venga usata in modo efficiente e che l'archiviazione e il tempo di ripristino siano ridotti al minimo.

**D10. Esiste un limite al numero di punti di ripristino che è possibile creare?**<br/> R10. No. Sono stati eliminati i limiti nei punti di ripristino. È possibile creare tutti i punti di ripristino che si desidera.

**D11. Perché la quantità di dati trasferiti nel backup non equivale alla quantità di dati sottoposti a backup?**<br/> R11. Tutti i dati sottoposti a backup vengono compressi e crittografati prima di essere trasferiti. In base al tipo di dati sottoposti a backup, l'ottimizzazione di compressione sarà presumibilmente del 30-40%.

## Ripristino
**D1. Quanti ripristini è possibile eseguire sui dati sottoposti a backup in Azure?**<br/> R1. Non esistono limiti al numero di ripristini da Backup di Azure.

**D2. È necessario pagare per il traffico in uscita dal data center di Azure durante i ripristini?**<br/> R2. No. I ripristini sono gratuiti e non viene addebitato alcun costo per il traffico in uscita.

## Sicurezza
**D1. I dati inviati in Azure sono crittografati?** <br/> R1. Sì. I dati vengono crittografati nel computer server/client/SCDPM locale mediante AES256 e i dati vengono inviati tramite un collegamento HTTPS sicuro.

**D2. Anche i dati di backup in Azure sono crittografati?**<br/> R2. Sì. I dati inviati ad Azure rimangono crittografati (in locale). Microsoft non decrittografa mai i dati di backup.

**D3. Qual è la lunghezza minima della chiave di crittografia usata per crittografare i dati di backup?** <br/> R3. La chiave di crittografia deve contenere almeno 16 caratteri.

**D4. Cosa accade se si smarrisce la chiave di crittografia? È possibile ripristinare i dati? Microsoft può recuperarli?** <br/> R4. La chiave usata per crittografare i dati di backup è disponibile solo nelle risorse del cliente. Microsoft non ne conserva una copia in Azure e non dispone dell'accesso alla chiave. Se il cliente smarrisce la chiave, Microsoft non può recuperare i dati di backup.

## Backup della cache

**D1. Come è possibile modificare il percorso della cache specificato per l'agente di Backup Azure?**

+ Arrestare OBEngine eseguendo il seguente comando in un prompt dei comandi con privilegi elevati:

  ```PS C:\> Net stop obengine```

+ Copiare la cartella dello spazio della cache in un'altra unità con spazio sufficiente. Si consiglia di copiare i file dalla cartella dello spazio della cache anziché spostarli; dopo aver confermato che i backup funzionino con il nuovo spazio della cache, è possibile rimuovere lo spazio della cache originale.

+ Aggiornare seguendo le voci del Registro di sistema con il percorso della nuova cartella di spazio della cache:


	| Percorso del Registro | Chiave del Registro | Valore |
	| ------ | ------- | ------ |
	| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Azure Backup\Config` | ScratchLocation | <i>Nuova posizione della cartella della cache</i> |
	| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Azure Backup\Config\CloudBackupProvider` | ScratchLocation | <i>Nuova posizione della cartella della cache</i> |


+ Avviare OBEngine eseguendo il seguente comando in un prompt dei comandi con privilegi elevati:

  ```PS C:\> Net start obengine```

Una volta che i backup funzionano correttamente con il nuovo percorso della cache, è possibile rimuovere la cartella della cache originale.

<!---HONumber=Nov15_HO4-->