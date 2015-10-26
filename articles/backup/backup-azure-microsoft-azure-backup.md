<properties
  pageTitle="Preparazione del backup di carichi di lavoro in Microsoft Azure | Microsoft Azure"
  description="Questo articolo fornisce una panoramica delle funzionalità dei carichi di lavoro dell'applicazione in Backup di Microsoft Azure"
  services="backup"
  documentationCenter=""
  authors="SamirMehta"
  manager="shreeshd"
  editor=""/>

<tags
  ms.service="backup"
  ms.workload="storage-backup-recovery"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="10/07/2015"
  ms.author="sammehta; jimpark"/>

# Preparazione del backup di carichi di lavoro in Microsoft Azure

> [AZURE.SELECTOR]
- [SCDPM](backup-azure-dpm-introduction.md)
- [Azure Backup](backup-azure-microsoft-azure-backup.md)

Questo articolo illustra la preparazione dell'ambiente per eseguire il backup dei carichi di lavoro, in modo da poter eseguire il backup dei dati in Azure. Con il server di Backup di Microsoft Azure è possibile proteggere i carichi di lavoro dell'applicazione, ad esempio macchine virtuali Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange e ai client di Windows:

- **Disco** (D2D), per fornire RTO elevati per i carichi di lavoro di livello 1
- **Azure** (D2D2C) per la conservazione a lungo termine

È anche possibile gestire la protezione delle diverse entità protetta (server e client) da un'unica interfaccia utente locale.

>[AZURE.NOTE]Il server di Backup di Microsoft Azure eredita le funzionalità di Data Protection Manager (DPM) per il backup del carico di lavoro. Per alcune di queste funzionalità saranno presenti puntatori alla documentazione di DPM. Il server di Backup di Microsoft Azure, tuttavia, non fornisce la protezione su nastro o l'integrazione con System Center.

È possibile distribuire il server di Backup di Microsoft Azure come:

- Server fisico autonomo.
- Macchina virtuale Hyper-V: è possibile eseguire il server di Backup di Microsoft Azure come una macchina virtuale ospitata in un server host Hyper-V locale per eseguire il backup dei dati locali. Per un elenco di considerazioni relative a questo ambiente, vedere [Installare DPM come macchina virtuale in un server Hyper-V locale](https://technet.microsoft.com/library/dn440941.aspx).
- Macchina virtuale Windows in VMWare: è possibile distribuire il server di Backup di Microsoft Azure per fornire la protezione per i carichi di lavoro Microsoft in esecuzione su macchine virtuali Windows in VMWare. In questo scenario il server di Backup di Microsoft Azure può essere distribuito come server fisico autonomo, come macchina virtuale Hyper-V o come macchina virtuale Windows in VMWare.
- Macchina virtuale di Azure: è possibile eseguire il server di Backup di Microsoft Azure come macchina virtuale in Azure per eseguire il backup dei carichi di lavoro nel cloud in esecuzione come macchine virtuali di Azure. Per informazioni su questa distribuzione vedere [Installare DPM come macchina virtuale di Azure](https://technet.microsoft.com/library/hh758176.aspx).

Inoltre,

- Backup di Microsoft Azure può essere installato in Windows Server 2008 R2 SP1, 2012 e 2012 R2.
- Non è possibile installare il server di Backup di Microsoft Azure in un computer nel quale è installato l'agente SCDPM o SCDPM RA.
- Non è possibile installare il server di Backup di Microsoft Azure in un computer nel quale l'agente di Backup di Microsoft Azure è installato e registrato con un insieme di credenziali di Backup di Azure.



Dopo aver selezionato il server in cui installare Backup di Microsoft Azure, è necessario:

- Verificare che siano soddisfatti i prerequisiti del server di Backup di Microsoft Azure.
- Creare un insieme di credenziali per il backup.
- Scaricare credenziali dell'insieme di credenziali.
- Scaricare i file di installazione del server di Backup di Microsoft Azure.

Ognuno di questi passaggi è illustrato in dettaglio di seguito.

## Prima di iniziare

Attualmente, il server di Backup di Microsoft Azure è disponibile solo con impostazioni locali in inglese. Servizi di ripristino di Microsoft Azure è attualmente disponibile in tutte le aree geografiche in cui è disponibile Azure, ad eccezione dei data center di Microsoft Azure per enti pubblici e di Microsoft Azure in Cina.

Per un'installazione senza problemi verificare che i prerequisiti seguenti siano soddisfatti prima di installare server di Backup di Microsoft Azure.

- Il server deve avere la connettività Internet. Microsoft Azure deve essere accessibile al server.
- I requisiti del server per l'installazione di Backup di Microsoft Azure sono le stesse di DPM. Per altre informazioni, vedere questi [requisiti hardware](https://technet.microsoft.com/library/dn726764.aspx).
- Il server di Backup di Microsoft Azure deve essere aggiunto al dominio.
- Nel server di Backup di Microsoft Azure devono essere installate le funzionalità di .NET 3.5, .NET 4.0 e .NET 3.5 SP1. Per altre informazioni, vedere le [informazioni aggiuntive sull'abilitazione di .Net Framework](https://technet.microsoft.com/library/dn482071.aspx).
- Nel server di Backup di Microsoft Azure deve essere installato Windows Management Framework 4.0. È possibile scaricarlo [qui](http://www.microsoft.com/download/details.aspx?id=40855).
- Per il disco usato come spazio dedicato per l'archiviazione dei dati del server di Backup di Microsoft Azure, le dimensioni del pool di archiviazione consigliate sono di 1,5 volte le dimensioni dei dati protetti. Per altre informazioni, vedere la sezione relativa ai dischi e all'archiviazione di [questo argomento] (Server https://technet.microsoft.com/library/hh758176.aspx#DPM).

Preparare il server di Backup di Microsoft Azure per eseguire il backup dei dati mediante le operazioni seguenti:

1. **Creazione di un insieme di credenziali per il backup**: creare un insieme di credenziali nella console di Backup di Azure .
2. **Download delle credenziali dell'insieme di credenziali**: caricare il certificato di gestione creato nell'insieme di credenziali in Backup di Azure.
3. **Download del server di Backup di Microsoft Azure**: è possibile scaricare il server di Backup di Microsoft Azure per i carichi di lavoro dell'applicazione dalla pagina Avvio rapido di un insieme di credenziali per il backup.

[AZURE.INCLUDE [backup-create-vault](../../includes/backup-create-vault.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

## Scaricare il server di Backup di Microsoft Azure
In modo analogo alle credenziali dell'insieme di credenziali, è possibile scaricare il server di Backup di Microsoft Azure per i carichi di lavoro dell'applicazione dalla ** pagina Avvio rapido** dell'insieme di credenziali per il backup.

1. Fare clic sull'opzione **per carichi di lavoro dell'applicazione (da disco a disco a cloud)**.

    ![Schermata iniziale di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/dpm-venus1.png)

2. Fare clic su **Download**.

    ![Area download 1](./media/backup-azure-microsoft-azure-backup/downloadcenter1.png)

3. Selezionare tutti i file e fare clic su **Avanti**. ![Area download 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    >[AZURE.NOTE]Poiché le dimensioni di download di tutti questi file è pari a > 3 GB, per il download completo potrebbero essere necessari fino a 60 minuti su un collegamento di download a 10 Mbps.

4. Inserire tutti i file nella stessa cartella.

5. Scaricare tutti i file provenienti dalla pagina di download di Backup di Microsoft Azure.

## Installare il server di Backup di Microsoft Azure
Prima di avviare il programma di installazione, assicurarsi che siano soddisfatti i prerequisiti descritti nella sezione precedente.

1. Dopo aver scaricato tutti i file, fare clic su **MicrosoftAzureBackupInstaller.exe**. Verrà visualizzata l'**installazione guidata di Backup di Microsoft Azure**.

    ![Programma di installazione di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/installer-click.png)

2. Specificare il percorso nel server locale in cui verranno estratti i file e fare clic su **Avanti**.

    ![Installazione guidata di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/extract-to.png)

3. Fare clic su **Estrai** per avviare l'estrazione dei file di installazione.

    ![Installazione guidata di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/ready-to-extract.png)

4. Selezionare la casella per avviare setup.exe per iniziare l'installazione del server di Backup di Microsoft Azure e fare clic su **Fine**.

    ![Installazione guidata di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/launch-setup.png)

5. Fare clic su **Backup di Microsoft Azure** per avviare l'installazione guidata.

    ![Installazione guidata di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)

6. Nella schermata iniziale fare clic su **Avanti**.

    ![Schermata iniziale di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/welcome-screen.png)

7. Fare clic su **Controlla** per determinare se i prerequisiti hardware e software per il server di Backup di Microsoft Azure sono stati soddisfatti.

    ![PreReq1 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/prereq-screen1.png)

8. Se tutti i prerequisiti sono stati soddisfatti, verrà visualizzato un messaggio che indica che il computer soddisfa i requisiti. Fare clic su **Avanti**.

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/prereq-screen2.png)

9. Il server di Backup di Microsoft Azure richiede SQL Server Standard. Selezionare un'istanza locale di SQL Server Standard 2014 esistente o consentire alla procedura guidata di installare SQL Server Standard. Fare clic su **Controlla e installa** per assicurarsi che nel server siano installati i prerequisiti necessari per SQL.

    ![SQL1 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/sql-setup1.png)

    Se si verifica un errore con l'indicazione di riavviare il computer, eseguire questa operazione e fare clic su **Controlla di nuovo**.

    ![Errore del filtro SiS](./media/backup-azure-microsoft-azure-backup/sis-filter.png)

10. Una volta che i prerequisiti vengono installati correttamente, fare clic su **Avanti**.

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/sql-setup3.png)

11. Specificare un percorso di installazione dei file del server di Backup di Microsoft Azure e fare clic su **Avanti**.

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    >[AZURE.NOTE]La disponibilità di uno spazio di lavoro è un requisito per il backup in Azure. Verificare che lo spazio di lavoro sia almeno il 5% dei dati pianificati per il backup nel cloud. Per la protezione disco, è necessario configurare dischi separati una volta completata l'installazione. Per altre informazioni sui pool di archiviazione, vedere [Configurare i pool di archiviazione e l'archiviazione su disco](https://technet.microsoft.com/library/hh758075.aspx).

12. Fornire una password complessa per gli account utente locali con restrizioni e fare clic su **Avanti**.

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/security-screen.png)

    >[AZURE.NOTE]L'impostazione di password complesse è essenziale per la sicurezza del sistema. Una password complessa è una password lunga almeno sei caratteri, non contiene tutto o parte del nome account utente e include almeno tre delle quattro categorie di caratteri seguenti: caratteri maiuscoli, caratteri minuscoli, cifre decimali e simboli (ad esempio !, @, #).

13. Selezionare se si vuole usare *Microsoft Update* per cercare gli aggiornamenti e fare clic su **Avanti**.

    >[AZURE.NOTE]È consigliabile impostare Windows Update per il reindirizzamento a Microsoft Update, che offre sicurezza e importanti aggiornamenti per Windows e altri prodotti come il server di Backup di Microsoft Azure.

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

14. Esaminare *Riepilogo impostazioni* e fare clic su **Installa**.

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/summary-screen.png)

    L'installazione avviene a fasi. Nella prima fase viene installato un agente nel server che deve essere registrato con le credenziali dell'insieme di credenziali di Backup di Azure valide scaricate con il prodotto. La procedura guidata controlla quindi per la connettività Internet diretta. Se la connettività Internet è disponibile, si può procedere con l'installazione, in caso contrario è necessario fornire i dettagli relativi al proxy per connettersi a Internet.

15. Fare clic su **Avanti** per avviare la fase di installazione dell'agente di Servizi di ripristino di Microsoft Azure.

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/proxy.png)

    La procedura guidata verifica i prerequisiti e li installa se rileva eventuali prerequisiti mancanti.

16. Fare clic su **Installa**.

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/mars-prereq.png)

17. Al termine dell'installazione dell'agente, fare clic su **Avanti** per registrare il server con l'insieme di credenziali di Backup di Azure.

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/mars-successful.png)

18. Fornire le credenziali dell'insieme di credenziali di Backup di Azure per registrare il server e fare clic su **Avanti**.

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/vault-reg-screen.png)

19. Fornire una passphrase usata per crittografare/decrittografare i dati inviati ad Azure e specificare un percorso per archiviarla. È possibile generare automaticamente una passphrase o fornire una passphrase personalizzata con un minimo di 16 caratteri. Fare clic su **Avanti**.

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/encrypt1.png)

    >[AZURE.NOTE]Per garantire la riservatezza, il server di Backup di Microsoft Azure non invia la passphrase ad Azure insieme ai dati. È necessario archiviare la passphrase in un luogo sicuro. Sarà necessaria quando si ripristinano i dati da Azure.

    Una volta completata correttamente la registrazione del server di Backup di Microsoft Azure, l'installazione prosegue con l'installazione e la configurazione di SQL Server Standard 2014 (se selezionato).

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/sql-install-start-screen.png)

    Al termine dell'installazione di SQL vengono installati i componenti del server di Backup di Microsoft Azure.

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/venus-installation-screen.png)

20. Quando il prodotto è installato e sono state create le icone del desktop, fare doppio clic sull'icona per avviare il prodotto.

    ![Icone](./media/backup-azure-microsoft-azure-backup/icons.png)

## Aggiungere un disco al pool di archiviazione

Per eseguire il backup dei carichi di lavoro su disco e in Azure, è necessario prima di tutto aggiungere un disco al pool di archiviazione. Per altre informazioni sull'aggiunta di dischi, vedere [Configurare i pool di archiviazione e l'archiviazione su disco](https://technet.microsoft.com/library/hh758075.aspx).

Dopo aver configurato i pool di archiviazione, è possibile eseguire il backup dei carichi di lavoro dell'applicazione su disco e in Azure.

## Risoluzione dei problemi

In caso di errori del server di Backup di Microsoft Azure durante la fase di installazione, o di backup o ripristino, vedere questo [documento sui codici di errore](https://support.microsoft.com/kb/3041338) per altre informazioni. È anche possibile vedere [Backup di Azure - Domande frequenti](backup-azure-backup-faq.md)

## Domande frequenti

### Installazione e distribuzione

D: La compressione NTFS è supportata in dischi o volumi del server di Backup di Microsoft Azure usati per i backup su disco? <br>R: NTFS **non** è supportato su dischi o volumi collegati al server di Backup di Microsoft Azure.

Q: È possibile spostare il server di Backup di Microsoft Azure in una nuova post-distribuzione del dominio? <br>R: No, lo spostamento del server di Backup di Microsoft Azure in una nuova post-distribuzione del dominio **non** è supportata.

D: Quali risorse sono protette dal server di Backup di Microsoft Azure installato in un controller di dominio? <br>R: Il server di Backup di Microsoft Azure può proteggere solo origini dati locali installati nello stesso server come controller di dominio. Perché il server di Backup di Microsoft Azure possa proteggere i carichi di lavoro su altri server, non installarlo nello stesso computer come controller di dominio.

D: Il server di Backup di Microsoft Azure può usare un'istanza remota di SQL Server? <br>R: No, il server di Backup di Microsoft Azure può usare solo un'istanza di SQL Server locale.

### Pool di archiviazione del server di Backup di Microsoft Azure

Q: È possibile includere un disco virtuale (VHD/VHDx) nel pool di archiviazione del server di Backup di Microsoft Azure? <br>R: Sì.

D: È possibile ottenere la deduplicazione in un pool di archiviazione del server di Backup di Microsoft Azure? <br>R: Sì, la deduplicazione è disponibile in un pool di archiviazione del server di Backup di Microsoft Azure. L'esperienza utente per il server di Backup di Microsoft Azure è esattamente come descritta in dettaglio in questo [post di blog su DPM](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx).

Q: Le unità USB o rimovibili possono essere usate per un pool di archiviazione del server di Backup di Microsoft Azure? <br>R: No.

### Connettività del server di Backup di Microsoft Azure al servizio di Azure

D: In che modo la connettività del server di Backup di Microsoft Azure al servizio di Azure e allo stato di sottoscrizione di Azure influiscono su backup e ripristino? <br>R: Il server di Backup di Microsoft Azure richiede la connettività al servizio di Azure per il funzionamento corretto del prodotto. Allo stesso tempo, è necessario che la sottoscrizione di Azure sia in uno stato integro.

La tabella seguente illustra la funzionalità del server di Backup di Microsoft Azure rispetto ai diversi stati di connettività e di sottoscrizione di Azure.

| Stato di connettività | Sottoscrizione di Azure | Backup in Azure| Backup su disco | Ripristino da Azure | Ripristino da disco |
| -------- | ------- | --------------------- | ------------------- | --------------------------- | ----------------------- |
| Connesso | Attivo | Consentito | Consentito | Consentito | Consentito |
| Connesso | Scaduto | Arrestato | Arrestato | Consentito | Consentito |
| Normale | Deprovisioning eseguito | Arrestato | Arrestato | Arrestato e punti di ripristino di Azure eliminati | Arrestato |
| Connettività persa > 15 giorni | Attivo | Arrestato | Arrestato | Consentito | Consentito |
| Connettività persa > 15 giorni | Scaduto | Arrestato | Arrestato | Consentito | Consentito |
| Connettività persa > 15 giorni | Deprovisioning eseguito | Arrestato | Arrestato | Arrestato e punti di ripristino di Azure eliminati | Arrestato |

D: Supponendo che lo stato della sottoscrizione continui a essere *Attivo*, cosa accade se viene ripristinata la connettività del server di Backup di Microsoft Azure? <br>R: Una volta che la connettività del server di Backup di Microsoft Azure al servizio di Azure ritorna normale e lo stato della sottoscrizione è *Attivo*, tutte le operazioni del server di Backup di Microsoft Azure sono consentite. I backup su entrambi i dischi, oltre che in Azure, verranno eseguiti normalmente.

D: Cosa accade se viene ripristinato lo stato della sottoscrizione da uno stato *Scaduto* in uno stato *Attivo*? <br>R: Supponendo che la connettività del server di Backup di Microsoft Azure al servizio di Azure sia normale, una volta che viene ripristinato lo stato *Attivo* della sottoscrizione del server di Backup di Microsoft Azure, tutte le operazioni del server di Backup di Microsoft Azure sono consentite. I backup su entrambi i dischi, oltre che in Azure, verranno ora eseguiti normalmente.

D: Cosa accade se viene ripristinato lo stato della sottoscrizione da uno stato *Deprovisioning eseguito* in uno stato *Attivo*? <br>R: Supponendo che la connettività del server di Backup di Microsoft Azure al servizio di Azure sia normale, una volta che viene ripristinato lo stato *Attivo* della sottoscrizione del server di Backup di Microsoft Azure, tutte le operazioni del server di Backup di Microsoft Azure sono consentite. Tuttavia, i punti di ripristino di Azure vengono eliminati e non possono essere ripristinati. I punti di ripristino su disco possono essere ripristinati se i backup su disco rientrano in un periodo di mantenimento dati valido.

D: Quali eccezioni è necessario creare per consentire la connettività del server di Backup di Microsoft Azure a un servizio pubblico di Azure? <br>R: È necessario consentire gli indirizzi di dominio seguenti nel profilo del firewall: www.msftncsi.com, *.Microsoft.com, *.WindowsAzure.com, *.microsoftonline.com, *.windows.net.

D: Come è possibile verificare se per il server di Backup di Microsoft Azure è disponibile la connettività al servizio di Azure? <br>R: Eseguire il cmdlet di PowerShell seguente nella console del server di Backup di Microsoft Azure:

```
Get-DPMCloudConnection
Output:
TRUE - Connected to Azure service
FALSE - Not connected to Azure service
```

D: Perché lo stato della sottoscrizione non è *Attivo*? Come è possibile modificarlo in *Attivo*? <br>R: Accedere al [portale di sottoscrizione](https://account.windowsazure.com/Subscriptions) e gestire la sottoscrizione.

### Fatturazione

D: Qual è il modello di fatturazione per il server di Backup di Microsoft Azure? <br>R: La fatturazione agli utenti verrà eseguita tramite un modello di istanza protetto. Per altre informazioni, vedere le domande frequenti nella pagina relativa ai [prezzi](http://azure.microsoft.com/pricing/details/backup/).

D: Qual è il modello di fatturazione se sono protetti solo i dati su disco? <br>R: Il modello di fatturazione è uguale al modello di istanza protetto. Poiché questi dati sono protetti nella risorsa di archiviazione locale, non saranno addebitati costi di archiviazione di Azure solo per i backup basati su disco. In questo caso, al cliente verrà addebitata solo tariffa per l'istanza protetta. Per altre informazioni su ciò che definisce un'istanza e quanto viene addebitato per ogni istanza, vedere le domande frequenti nella pagina relativa ai [prezzi ](http://azure.microsoft.com/pricing/details/backup/).

D: Qual è il costo addebitato per ogni istanza protetta? <br>R: Vedere la pagina relativa ai [prezzi ](http://azure.microsoft.com/pricing/details/backup/).

D: Dove è possibile trovare esempi che evidenziano i diversi carichi di lavoro dell'applicazione e la tariffa per le relative istanze protette? <br>R: Vedere la "sezione degli esempi" nella pagina relativa ai [prezzi ](http://azure.microsoft.com/pricing/details/backup/).

D: In che modo verrebbe addebitata un'origine dati protetta su disco e nel cloud tramite il server di Backup di Microsoft Azure? <br>R: Indipendentemente dal backup dei dati su disco o nel cloud, l'addebito del server di Backup di Microsoft Azure avverrà in base alle istanze protette. Le dimensioni dell'istanza protetto vengono calcolate in base alle dimensioni front-end dell'origine dati. Per i dati di backup nell'archiviazione di Azure, si applicano anche i costi di archiviazione di Azure.

## Passaggi successivi

È possibile usare questi articoli per acquisire una comprensione più profonda della protezione dei carichi di lavoro tramite il server di Backup di Microsoft Azure.

- [Backup di SQL Server](backup-azure-backup-sql.md)
- [Backup di SharePoint Server](backup-azure-backup-sharepoint.md)
- [Backup del server alternativo](backup-azure-alternate-dpm-server.md)

<!---HONumber=Oct15_HO3-->