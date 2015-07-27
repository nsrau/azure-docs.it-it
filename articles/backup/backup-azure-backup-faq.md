<properties
   pageTitle="Domande frequenti Backup Azure | Microsoft Azure"
   description="Domande frequenti sul servizio Backup di Azure"
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="shreeshd"
   editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="07/10/2015" ms.author="arunak"; "jimpark"; "aashishr"/>

# Backup di Azure - Domande frequenti
Di seguito è riportato un elenco di domande frequenti su Backup di Azure. Per altre domande su Backup di Azure, visitare il [forum di discussione](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup) e inviare una domanda. Un membro dalla community fornirà supporto agli utenti per individuare le risposte. Se una domanda viene posta più volte, verrà aggiunta a questo articolo per poter essere recuperata in modo rapido e semplice.

## Installazione e configurazione
**D1. Qual è l'elenco dei sistemi operativi supportati da cui è possibile eseguire il backup in Azure tramite Backup di Azure?** <br/> R1. Il seguente elenco di sistemi operativi è supportato da Azure Backup


| Sistema operativo | Piattaforma | SKU |
| :------------- |-------------| :-----|
| Windows 8 e versioni più recenti di SP | 64 bit | Enterprise, Pro |
| Windows 7 e versioni più recenti di SP | 64 bit | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| Windows 8.1 e versioni più recenti di SP | 64 bit | Enterprise, Pro |
|Windows Server 2012 R2 e più recenti SPs|	64 bit|	Standard, Datacenter, Foundation|
|Windows Server 2012 e versioni più recenti di SP|	64 bit|	Datacenter, Foundation, Standard|
|Windows Storage Server 2012 R2 e versioni più recenti di SP |64 bit|	Standard, Workgroup|
|Windows Storage Server 2012 e versioni più recenti di SP |64 bit |Standard, Workgroup
|Windows Server 2012 R2 e più recenti SPs |64 bit|	Essential|
|Windows Server 2008 R2 SP1, |64 bit|	Standard, Enterprise, Datacenter, Foundation|
|Windows Server 2008 SP2 |64 bit|	Standard, Enterprise, Datacenter, Foundation|

**D2. Dov'è possibile scaricare l'agente Backup di Azure più recente?** <br/> R2. È possibile scaricare la versione più recente dell'agente da [questa pagina](http://aka.ms/azurebackup_agent). Può essere installato su Windows Server, server SCDPM o client Windows

**D3. Quale versione del server SCDPM è supportata?** <br/> R3. È consigliabile installare la versione [più recente](http://aka.ms/azurebackup_agent) dell'agente Backup di Azure sull'aggiornamento cumulativo più recente di SCDPM (UR6 a febbraio 2015)

**D4. Quando si configura l'agente Backup di Azure, viene richiesto di immettere le "credenziali di insieme". Le credenziali di insieme hanno una data di scadenza?** <br/> R4. Sì, le credenziali di insieme scadono dopo 48 ore. Se il file scade, accedere al portale di Azure e scaricare i file delle credenziali di insieme dall'insieme di credenziali per il backup.

**D5. Esistono limiti al numero degli insiemi di credenziali per il backup che è possibile creare in ogni sottoscrizione di Azure?** <br/> R5. Sì. A partire da marzo 2015, è possibile creare 25 insiemi di credenziali per ogni sottoscrizione. Se sono necessari più insiemi di credenziali, creare una nuova sottoscrizione.

**D6. È necessario considerare l'insieme di credenziali come un'entità di fatturazione?** <br/> R6. Sebbene sia possibile ottenere una fattura dettagliata per ogni insieme di credenziali, è consigliabile considerare una sottoscrizione di Azure come un'entità di fatturazione. È infatti coerente in tutti i servizi ed è più facile da gestire.

**D7. Esistono limiti al numero di server/computer che possono essere registrati in ogni insieme di credenziali?** <br/> R7. Sì, è possibile registrare fino a 50 computer per ogni insieme di credenziali. Se è necessario registrare più computer, creare un nuovo insieme di credenziali.

**D8. Esistono limiti alla quantità di dati di cui è possibile eseguire il backup da un server/client Windows o da un server SCDPM?** <br/> R8. No.

**D9. Come si registra un server in un altro data center?**<br/> R9. In genere, i dati di backup sono inviati al data center del servizio di backup in cui sono registrati. Il modo più semplice per modificare il data center consiste nel disinstallare e reinstallare l'agente e registrarlo in un nuovo data center.

**D10. Cosa accade se si rinomina un server Windows che esegue il backup dei dati in Azure?** <br/> R10. Tutti i backup attualmente configurati verranno arrestati. Il server dovrà essere nuovamente registrato nell'insieme di credenziali per il backup e verrà considerato come un nuovo server da Servizi di ripristino, pertanto la prima operazione che si verificherà in seguito alla registrazione consisterà in un backup completo di tutti i dati inclusi nel backup e non solo delle modifiche apportate dall'ultimo backup. Se tuttavia è necessario eseguire un'operazione di ripristino, è possibile ripristinare i dati sottoposti a backup mediante l'apposita opzione per il ripristino da un altro server. Per altre informazioni, vedere Rinominare un server.

**D11. Da quali tipi di unità è possibile eseguire il backup di file e cartelle?** <br/> R11. È possibile eseguire il backup del set di unità/volumi seguente:

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

**D16: È possibile eseguire la migrazione dell'insieme di credenziali per il backup tra sottoscrizioni?** <br/> R16: No. L'insieme di credenziali viene creato a livello di sottoscrizione e non può essere riassegnato a un'altra sottoscrizione.

**Q17: Utilizzare l'agente Azure Backup in un server che utilizza la deduplicazione di Windows Server 2012?** <br/>A17: Sì. Il servizio agente converte i dati deduplicati in dati normali quando si prepara l'operazione di backup. Quindi l'ottimizzazione dei dati per il backup, i dati vengono crittografati e quindi invia i dati crittografati al servizio di backup in linea.

**Q18: I dati di backup vengono eliminati se si annulla un backup dopo l'avvio?** <br/>A18: No. Credenziali per il backup vengono archiviati i backup dei dati trasferiti fino al punto dell'annullamento. Backup di Azure utilizza un meccanismo di checkpoint in modo che i dati di backup Ottiene il controllo a cui fa riferimento in alcuni casi durante il backup e il successivo processo di backup può convalidare l'integrità dei file. Il successivo backup avviato risulterà incrementale rispetto ai dati erano stato eseguito il backup in precedenza. Questo fornisce un migliore utilizzo della larghezza di banda in modo che non è necessario trasferire ripetutamente gli stessi dati.

**Q19: Perché viene visualizzato l'avviso "Backup di Azure non sono stati configurati per il server" anche se pianificati backup regolari in precedenza?** <br/>A19: Ciò può verificarsi quando le impostazioni di pianificazione di backup archiviate nel server locale non sono identiche alle impostazioni archiviate nell'archivio di backup. Quando il server o le impostazioni sono state ripristinate a uno stato noto soddisfacente, le pianificazioni di backup possono perdere la sincronizzazione. In questo caso, è necessario riconfigurare i criteri di backup e quindi**eseguire subito il backup**per risincronizzare il server locale con Azure.

## Backup e conservazione
**D1. Esiste un limite alle dimensioni di ogni origine dati sottoposta a backup?** <br/> R1. A partire da marzo 2015, le dimensioni di ogni origine dati deve essere minore o uguale a 1,7 TB. Un'origine dati può essere

- Un volume file o cartella
- Un database SQL
- Una farm di SharePoint
- Un server di Exchange
- Una macchina virtuale Hyper-V

**D2. Esistono limiti al numero di backup giornalieri che è possibile pianificare?**<br/> R2. Sì, Backup di Azure consente di eseguire 3 copie di backup al giorno tramite un server o client Windows e 2 copie di backup al giorno tramite SCDPM.

**D3. Esistono differenze tra i criteri di pianificazione del backup di DPM e di Backup di Azure (ad esempio, in Windows Server senza DPM)?** <br/> R3. Sì. Tramite DPM è possibile specificare la pianificazione giornaliera, settimanale, mensile e annuale, mentre da Windows Server (senza DPM) è possibile specificare solo pianificazioni giornaliere e settimanali.

**D4. Esistono differenze tra i criteri di conservazione di DPM e di Backup di Azure (ad esempio, in Windows Server senza DPM)?**<br/> R4. No, le due soluzioni offrono le stesse funzionalità. È possibile specificare criteri di conservazione giornalieri, settimanali, mensili e annuali.

**D5. È possibile configurare i criteri di conservazione in modo selettivo, ad esempio con frequenza settimanale e giornaliera ma non annuale e mensile?**<br/> R5. Con i criteri verrà visualizzato il set completo di funzionalità per definire in modo ottimale i requisiti di conformità/conservazione.

**D6. È possibile "pianificare un backup" alle 18.00 e specificare i "criteri di conservazione" a un orario diverso?**<br/> R6. No. I criteri di conservazione possono essere applicati solo ai punti di backup. Nell'immagine seguente viene specificato il criterio di conservazione per i backup eseguiti a mezzanotte e alle 18.00. <br/>

![Pianifica backup e conservazione](./media/backup-azure-backup-faq/Schedule.png) <br/>

**D7. Viene trasferita una copia incrementale per i criteri di conservazione pianificati?** <br/> R7. No, la copia incrementale viene inviata in base all'ora indicata nella pagina di pianificazione del backup. I punti che possono essere conservati vengono determinati in base ai criteri di conservazione.

**D8. Se il backup viene conservato per un lungo periodo di tempo, è necessario molto tempo per recuperare i dati, ad esempio dal punto meno recente?** <br/> R8. No, il tempo necessario per recuperare i dati dal punto meno recente e dal punto più recente è lo stesso. Ogni punto di ripristino si comporta come un punto completo.

**D9. Se ogni punto di ripristino si comporta come un punto completo, questo influisce sul totale dell'archiviazione di backup fatturabile?**<br/> R9. I punti di conservazione tipici a lungo termine archiviano i dati di backup come punti completi. Queste soluzioni sono inefficienti dal punto di vista dell'archiviazione ma consentono un ripristino più facile e veloce. Le copie incrementali sono efficienti dal punto di vista dell'archiviazione ma richiedono il ripristino una catena di dati che influisce sui tempi di ripristino. L'architettura di archiviazione di Backup di Azure offre il meglio dei due mondi, garantendo un'archiviazione dei dati ottimale per ripristini veloci e costi di archiviazione ridotti. Questo approccio assicura che la larghezza di banda (in entrata e in uscita) venga usata in modo efficiente e che l'archiviazione e il tempo di ripristino siano ridotti al minimo.

**D10. Esiste un limite al numero di punti di ripristino che è possibile creare?**<br/> R10. A partire da aprile 2015, è possibile creare fino a 366 punti di ripristino. È possibile usare qualsiasi permutazione per arrivare a un numero inferiore a 366. Ad esempio, il totale dei punti di conservazione nell'immagine seguente è 354. <br/>

![Schermata di conservazione](./media/backup-azure-backup-faq/RetentionScreen1.png)

**D11. Quando Microsoft aumenterà il limite massimo di 366 punti di conservazione, sarà necessario aggiornare l'agente o eseguire il reseeding del backup iniziale?** <br/> R11. No. Dopo che sarà stata eseguita la modifica nel servizio, si riceverà una notifica mediante i canali di social media come i blog, gli annunci di Azure, il portale e così via. In base alle esigenze, sarà necessario solo modificare i criteri di conservazione.

**D12. Perché la quantità di dati trasferiti nel backup non equivale alla quantità di dati sottoposti a backup?**<br/> R12. Tutti i dati sottoposti a backup vengono compressi e crittografati prima di essere trasferiti. In base al tipo di dati sottoposti a backup, l'ottimizzazione di compressione sarà presumibilmente del 30-40%.

## Ripristino
**D1. Quanti ripristini è possibile eseguire sui dati sottoposti a backup in Azure?**<br/> R1. Non esistono limiti al numero di ripristini da Backup di Azure.

**D2. È necessario pagare per il traffico in uscita dal data center di Azure durante i ripristini?**<br/> R2. No. I ripristini sono gratuiti e non viene addebitato alcun costo per il traffico in uscita.

## Sicurezza
**D1. I dati inviati in Azure sono crittografati?** <br/> R1. Sì. I dati vengono crittografati nel computer server/client/SCDPM locale mediante AES256 e i dati vengono inviati tramite un collegamento HTTPS sicuro.

**D2. Anche i dati di backup in Azure sono crittografati?**<br/> R2. Sì. I dati inviati ad Azure rimangono crittografati (in locale). Microsoft non decrittografa mai i dati di backup.

**D3. Qual è la lunghezza minima della chiave di crittografia usata per crittografare i dati di backup?** <br/> R3. La chiave di crittografia deve contenere almeno 16 caratteri.

**D4. Cosa accade se si smarrisce la chiave di crittografia? È possibile ripristinare i dati? Microsoft può recuperarli?** <br/> R4. La chiave usata per crittografare i dati di backup è disponibile solo nelle risorse del cliente. Microsoft non ne conserva una copia in Azure e non dispone dell'accesso alla chiave. Se il cliente smarrisce la chiave, Microsoft non può recuperare i dati di backup.

<!---HONumber=July15_HO3-->