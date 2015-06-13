<properties
	pageTitle="Backup di Azure - domande frequenti"
	description="Domande frequenti sul servizio di Backup di Azure"
	services="backup"
	documentationCenter=""
	authors="prvijay"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.date="03/26/2015"
	 ms.author="prvijay"/>

# Backup di Azure - domande frequenti
Di seguito è riportato un elenco di domande frequenti su Azure Backup. Se si hanno ulteriori domande su Azure Backup, andare all'il [forum di discussione](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=windowsazureonlinebackup) e inserire le domande. Un utente della community consentirà di ottenere le risposte. Se una domanda è particolarmente frequente, si verrà aggiunta a questo articolo in modo da trovare rapidamente e facilmente.

## Installazione e configurazione

**T1. Che cos'è l'elenco dei sistemi operativi supportati da cui è possibile eseguire il backup in Azure utilizzando Backup di Azure?** <br/> A1. Il seguente elenco di OS'es è supportato da Azure Backup


| Sistema operativo | Piattaforma | SKU |
| :------------- |-------------| :-----|
| Windows 8 e più recenti SPs | a 64 bit | Enterprise, Pro |
| Windows 7 e più recenti SPs | a 64 bit | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| Windows 8.1 e più recenti SPs | a 64 bit | Enterprise, Pro |
|Windows Server 2012 R2 e più recenti SPs|	a 64 bit|	Foundation standard, Datacenter,|
|Windows Server 2012 e più recenti SPs|	a 64 bit|	Datacenter, Foundation, Standard|
|Windows Storage Server 2012 R2 e più recenti SPs |a 64 bit|	Standard, Workgroup|
|Windows Storage Server 2012 e più recenti SPs |a 64 bit |Standard, Workgroup
|Windows Server 2012 R2 e più recenti SPs |a 64 bit|	Essenziali|
|Windows Server 2008 R2 SP1, |a 64 bit|	Standard, Enterprise, Datacenter, Foundation|
|Windows Server 2008 SP2 |a 64 bit|	Standard, Enterprise, Datacenter, Foundation|

**Q2. Dove è possibile scaricare l'agente Azure Backup più recente?** <br/> A2. È possibile scaricare l'agente più recente da [qui](http://aka.ms/azurebackup_agent). Questo può essere installato su Windows Server, SCDPM server o client Windows

**Q3. La versione del server SCDPM è supportata?** <br/> A3. Si consiglia di installare il [più recente](http://aka.ms/azurebackup_agent) agente Azure Backup sull'aggiornamento cumulativo più recente di SCDPM (UR5 a febbraio 2015)

**T4. Quando si configura l'agente Azure Backup, viene richiesto di immettere le credenziali dell'insieme di credenziali"". Vi qualsiasi data di scadenza associato alle credenziali dell'insieme di credenziali?** <br/> A4. Sì, le credenziali dell'insieme di credenziali scadono dopo 48hours. Se il file scade, accedere al portale di Azure e scaricare i file di credenziali insieme di credenziali dall'archivio di Backup.

**Q5. Esiste un limite al numero degli archivi di backup che può essere creato in ogni sottoscrizione di Azure?** <br/> A5. Sì. A partire da marzo 2015, è possibile creare insiemi di credenziali di 25 per ogni sottoscrizione. Se è necessario più insiemi di credenziali, creare una nuova sottoscrizione.

**(DOMANDA 6). È necessario esaminare insieme di credenziali come entità di fatturazione?** <br/> A6. Sebbene sia possibile ottenere una distinta base dettagliata per ogni insieme di credenziali, è consigliabile prendere in considerazione una sottoscrizione di Azure come un'entità di fatturazione. Questa sia coerenza in tutti i servizi ed è più facile da gestire.

**(DOMANDA 7). Esistono limiti al numero di server/macchine possono essere registrati con ogni insieme di credenziali?** <br/> A7. Sì, è possibile registrare fino a 50 macchine per ogni insieme di credenziali. Se è necessario registrare più macchine, creare un nuovo insieme di credenziali.

**(DOMANDA 8). Esiste eventuali limiti sulla quantità di dati che possono essere eseguiti da un client Windows in server o server SCDPM?** <br/> A8. No.

**(DOMANDA 9). Come per la registrazione di un server in un altro Data Center**<br/> A9. In generale, i dati di backup viene inviati al Data Center di servizio di Backup in cui è registrato. Il modo più semplice per modificare il Data Center consiste nel disinstallare l'agente e reinstallare l'agente e registrarlo in un nuovo datacenter.

**(DOMANDA 10). Cosa accade se si rinomina un server Windows che è backup dei dati in Azure?** <br/> A10. Qualsiasi backup attualmente configurato verrà arrestato. Sarà necessario registrare il server con credenziali per il backup e verrà considerato un nuovo server dai servizi di ripristino, in modo che la prima operazione di backup che si verifica dopo la registrazione sia un backup completo di tutti i dati inclusi nel backup, non solo delle modifiche, poiché si è verificato l'ultimo backup. Tuttavia, se è necessario eseguire un'operazione di ripristino è possibile ripristinare i dati sottoposti a backup tramite il ripristino da un'altra opzione di ripristino del server. Per ulteriori informazioni, vedere rinominare un server.

**Q11. Quali tipi di unità è possibile backup file e cartelle?** <br/> A11. Il seguente set di unità di volumi possono essere backup:

+ Supporti rimovibili: L'unità deve essere segnalata come fissa per poter essere utilizzata l'origine di backup.

+ Volumi di sola lettura: il volume deve essere accessibile in scrittura per il servizio Copia shadow del volume (VSS) alla funzione.

+ Volumi offline: Il volume deve essere online per VSS alla funzione.

+ Condivisione di rete: il volume deve essere locale al server per eseguire il backup utilizzando backup in linea.

**DOMANDA 12. I tipi di file e cartelle è possibile eseguire il backup dal server?**<br/> A12. Sono supportati i seguenti tipi:

+ Crittografato

+ Compresso

+ Tipo sparse

+ Compresso + Sparse

+ Collegamenti reali: Non supportato, ignorato

+ Punto di analisi: Non supportato, ignorato

+ Crittografato + compresso: Non supportato, ignorato

+ Crittografato + Sparse: Non supportato, ignorato

+ Flusso compresso: Non supportato, ignorato

+ Flusso di tipo sparse: Non supportato, ignorato

**D13. Che cos'è il requisito delle dimensioni minime per la cartella cache?** <br/> A13. La dimensione della cartella della cache è determinata dalla quantità di dati che esegue il backup. In generale, è possibile aspettarsi che 10-15% dello spazio necessario per l'archiviazione dei dati deve essere allocata per la cartella della cache.

**Q14. Come è possibile isolare i dati server specifico vengano ripristinati da altri server nell'organizzazione? **<br/> A14. Qualsiasi server che vengono registrati utilizzando lo stesso archivio saranno in grado di ripristinare i dati di backup da altri server che utilizzano la stessa passphrase. Se si dispone di server che si desidera garantire il ripristino venga eseguito solo a server specifici all'interno dell'organizzazione, è consigliabile utilizzare una passphrase distinta destinata per tali server. Server delle risorse umane, ad esempio, possibile utilizzare una passphrase di crittografia, accounting server un altro e server di archiviazione un terzo.

**D15. È possibile "migrare" backup dati tra le sottoscrizioni?** <br/> A15: No

**Q16: È possibile "migrare" l'insieme di credenziali di backup tra le sottoscrizioni?** <br/> 16: No. L'insieme di credenziali viene creato a livello di sottoscrizione e non può essere riassegnato a un'altra sottoscrizione dopo averla creata.

## Backup e memorizzazione
**T1. Esiste un limite alle dimensioni di ogni origine dati che vengono sottoposti a backup?** <br/> A1. A partire da marzo 2015, ogni origine dati deve essere minore o uguale a 1,7 TB. È un'origine dati

+ Volume di file o cartella

+ DATABASE SQL

+ Farm di SharePoint

+ Server di Exchange

+ Macchina virtuale Hyper-V

**Q2. È esistono limite sul numero di volte in cui è possibile pianificare backup al giorno?**<br/> A2. Sì, Backup di Azure consente di 3 copie di backup al giorno tramite Windows Server/Client e 2 copie di backup al giorno tramite SCDPM.

**Q3. Esiste una differenza tra DPM e criteri di pianificazione di backup di Backup di Azure (ad es. in Windows Server senza Data Protection Manager)?** <br/> A3. Sì. Utilizzo di DPM, è possibile specificare la pianificazione giornaliera, settimanale, mensile, annuale di mentre da un Server di Windows (senza DPM), è possibile specificare solo ogni giorno, pianificazioni settimanali.

**T4. Esiste una differenza tra DPM e Azure del Backup (ad es. in Windows Server senza Data Protection Manager) criteri di conservazione?**<br/> A4. No, è necessario disporre delle stesse funzionalità. È possibile specificare giornaliero, settimanale, mensile e annuale criteri di conservazione.

**Q5. È possibile configurare la mia memorizzazione criteri configurare in modo selettivo, vale a dire settimanale e giornaliero ma non annuale e mensile?**<br/> A5. È il set completo di misure per ottenere i criteri che consentono di definire meglio i requisiti di conformità/memorizzazione.

**(DOMANDA 6). È possibile "pianifica un backup" alle 18.00 e specificare "criteri di conservazione" in un momento diverso?**<br/> A6. No. Criteri di conservazione possono essere applicati solo nei punti di backup. Nel sotto l'immagine, viene specificato il criterio di conservazione su backup eseguiti in 12 am e pm 6. <br/>

![Pianifica Backup e memorizzazione][1] <br/>

**(DOMANDA 7). È una copia incrementale trasferita per criteri di conservazione pianificati?** <br/> A7. No, copia incrementale viene inviata in base all'ora indicato nella pagina di pianificazione di backup. I punti che possono essere conservati vengono determinati in base al criterio di conservazione.

**(DOMANDA 8). Se il backup viene mantenuto per un lungo periodo di tempo, è necessario molto tempo per recuperare i dati (ad esempio il punto meno recente)?** <br/> A8. No-i tempi di ripristino meno recente o l'ultimo punto è la stessa. Ogni punto di ripristino si comporta come un punto completo.

**(DOMANDA 9). Se ogni punto di ripristino è simile a un punto completo, impatto ha l'archiviazione di backup fatturabile totale?**<br/> A9. Prodotti di punto di memorizzazione a lungo termine tipico archiviare dati di backup come punti completi. Tuttavia, questi sono archiviazione inefficiente ma più facile e veloce da ripristinare. Copie incrementali sono efficienti archiviazione ma è necessario ripristinare una catena di dati che influisce sui tempi di ripristino. Architettura di archiviazione univoco del Backup di Azure offre il meglio dei due mondi dall'archiviazione dei dati per i ripristini veloci in modo ottimale e incorrere in costi di archiviazione insufficiente. Questo approccio assicura che la larghezza di banda (in entrata e in uscita) viene utilizzato in modo efficiente, è ridotto al minimo, archiviazione e il tempo di ripristino è ridotto al minimo.

**(DOMANDA 10). Esiste un limite al numero di punti di ripristino che può essere creato?**<br/> A10. A partire da aprile 2015, è possibile avere fino a 366 punti di ripristino. È possibile utilizzare qualsiasi permutazione per arrivare a un numero minore di 366. Ad esempio, il periodo di conservazione punti nel sotto immagine aggiungervi 354. <br/>

![Schermata di conservazione][2]

**Q11. Una volta Microsoft migliora limite da 366, sarà necessario aggiornare l'agente o reinizializzare il backup iniziale?** <br/> A11. No. Dopo aver effettuato la modifica nel servizio, verrà notificata mediante il social networking (blog, annunci di Azure, portale e così via). In base alle proprie esigenze, è quindi necessario per modificare solo i criteri di conservazione.

**DOMANDA 12. Perché la quantità di dati trasferita nel backup non equivale alla quantità di dati che sottoposti a backup?**<br/> A12. Tutti i dati sottoposti a backup vengono compressi e crittografati prima di essere trasferiti. È possibile prevedere le prestazioni di compressione 30-40% in base al tipo di dati sottoposti a backup.

## Ripristini
**T1. Il numero di ripristini è eseguire sui dati in cui viene eseguito il backup in Azure?**<br/> A1. Non esiste alcun limite al numero di ripristini di Backup di Azure.

**Q2. È necessario pagare per il traffico in uscita dal data center di Azure durante il ripristino?**<br/> A2. No. I ripristini sono disponibili gratuitamente e non viene applicata per il traffico in uscita.

## Sicurezza
**T1. I dati inviati in Azure crittografato?** <br/> A1. Sì. I dati vengono crittografati nel computer client/server/SCDPM locale tramite AES256 e i dati vengono inviati utilizzando una connessione HTTPS protetta.

**Q2. I dati di backup si trova in Azure in forma crittografata?**<br/> A2. Sì. I dati che viene inviati a Azure rimangono crittografati (nel resto). Microsoft non decrittografa i dati di backup in qualsiasi momento.

**Q3. Che cos'è la lunghezza minima della chiave di crittografia utilizzata per crittografare i dati di backup?** <br/> A3. La chiave di crittografia deve essere almeno 16 caratteri.

**T4. Cosa accade se registrano la chiave di crittografia? È possibile recuperare i dati (o) Microsoft possono ripristinare i dati?** <br/> A4. La chiave utilizzata per crittografare i dati di backup è presente solo nella sede del cliente. Microsoft non mantiene una copia in Azure e non dispone di accesso alla chiave. Se il cliente misplaces la chiave, Microsoft non può recuperare i dati di backup.


<!--Image references-->
[1]: ./media/backup-azure-backup-faq/Schedule.png
[2]: ./media/backup-azure-backup-faq/RetentionScreen1.png

<!---HONumber=GIT-SubDir--> 