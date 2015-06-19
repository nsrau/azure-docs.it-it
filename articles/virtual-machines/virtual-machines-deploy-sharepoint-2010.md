<properties
	pageTitle="Distribuzione di SharePoint 2010 in Macchine virtuali di Azure"
	description="Informazioni sugli scenari supportati per l'uso di SharePoint 2010 in macchine virtuali di Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/28/2015"
	ms.author="josephd"/>


# Distribuzione di SharePoint 2010 in Macchine virtuali di Azure

Microsoft SharePoint Server 2010 offre ampia flessibilità nella distribuzione, consentendo alle organizzazioni di identificare gli scenari appropriati e di allinearli alle esigenze e agli obiettivi aziendali. Il servizio Macchine virtuali di Azure, ospitato e gestito nel cloud, offre un'infrastruttura completa, affidabile e disponibile per supportare vari carichi di lavoro su richiesta di applicazioni e database, ad esempio le distribuzione di Microsoft SQL Server e SharePoint.

Anche se Macchine virtuali di Azure supporta più carichi di lavoro, questo articolo è incentrato sulle distribuzioni di SharePoint. Grazie a Macchine virtuali di Azure, le organizzazioni possono creare e gestire rapidamente un'infrastruttura di SharePoint, con il provisioning e l'accesso a qualsiasi host a livello globale. Questo servizio offre il controllo completo e la gestione di processori, RAM, intervalli della CPU e altre risorse delle macchine virtuali (VM, Virtual Machine) di SharePoint.

Macchine virtuali di Azure riduce la necessità di acquisire hardware, consentendo alle organizzazioni di dedicarsi alla creazione e alla gestione di un'infrastruttura scalabile in base a specifiche esigenze, senza affrontare costi iniziali elevati e complessità. In questo modo possono innovare, sperimentare e ripetere le procedure in poche ore anziché in diversi giorni e settimane come nelle tradizionali distribuzioni.

> [AZURE.NOTE]Per informazioni sulla distribuzione di SharePoint 2013 in Azure, vedere [Pianificazione per SharePoint 2013 nei servizi infrastruttura di Azure](https://msdn.microsoft.com/library/dn275958.aspx) e [SharePoint nei servizi infrastruttura di Azure](virtual-machines-sharepoint-infrastructure-services.md).

## SharePoint in Macchine virtuali di Azure

La flessibilità di SharePoint 2010 supporta la maggior parte dei carichi di lavoro in una distribuzione di Macchine virtuali di Azure. Macchine virtuali di Azure rappresenta la soluzione ottimale per scenari FIS (SharePoint Server for Internet Sites) e di sviluppo. Analogamente, sono supportati anche i principali carichi di lavoro di SharePoint. Se un'organizzazione ha l'esigenza di gestire e controllare la propria implementazione di SharePoint 2010 sfruttando allo stesso tempo le opzioni per la virtualizzazione nel cloud, Macchine virtuali di Azure è la scelta ideale per la distribuzione.

L'offerta Macchine virtuali di Azure è ospitata e gestita nel cloud. Assicura flessibilità nella distribuzione e costi inferiori, grazie alla riduzione delle spese di capitale associate all'approvvigionamento dell'hardware. Con una maggiore flessibilità dell'infrastruttura, le organizzazioni possono distribuire SharePoint Server in poche ore anziché in diversi giorni o settimane. Grazie a Macchine virtuali di Azure, le organizzazioni possono inoltre distribuire carichi di lavoro di SharePoint nel cloud in base a un modello di pagamento a consumo. Con l'aumento dei carichi di lavoro di SharePoint, le organizzazioni possono ampliare rapidamente l'infrastruttura e in seguito, se le esigenze di elaborazione diminuiscono, possono restituire le risorse non più necessarie, assumendosi di fatto solo i costi di quelle che usano.

### Cambio di approccio per l'IT

Molte organizzazioni affidano in appalto i componenti comuni dell'infrastruttura IT e della gestione, ad esempio hardware, sistemi operativi, sicurezza, archiviazione dei dati e backup, mantenendo allo stesso tempo il controllo delle applicazioni mission-critical come SharePoint Server. Delegando tutti i livelli dei servizi non mission-critical delle loro piattaforme IT a un provider virtuale, le organizzazioni possono indirizzare i loro obiettivi IT verso i servizi di base e mission-critical di SharePoint e acquisire valore di business con i progetti di SharePoint, anziché dedicare più tempo alla configurazione dell'infrastruttura.

### Distribuzione più rapida

Le attività di supporto e distribuzione di un'infrastruttura SharePoint su vasta scala possono richiedere tempi più lunghi del previsto rispetto ai requisiti aziendali. Possono infatti essere necessarie diverse settimane, o addirittura mesi, per completare le procedure di creazione, test e preparazione di server e farm di SharePoint e la successiva distribuzione in un ambiente di produzione, a seconda dei processi e dei vincoli dell'organizzazione. Con Macchine virtuali di Azure, le organizzazioni possono invece distribuire rapidamente i loro carichi di lavoro di SharePoint, senza spese di capitale associate all'hardware. In questo modo possono sfruttare la flessibilità dell'infrastruttura per completare la distribuzione in poche ore anziché in diversi giorni o settimane.

### Scalabilità

Senza la necessità di occuparsi delle procedure di distribuzione, test e preparazione di server e farm fisici di SharePoint, le organizzazioni possono ampliare e acquistare capacità di calcolo su richiesta, con pochissimo preavviso. Con l'aumento dei requisiti dei carichi di lavoro di SharePoint, le organizzazioni possono rapidamente espanderne l'infrastruttura nel cloud. Analogamente, quando le esigenze di elaborazione diminuiscono, possono ridurre le risorse e assumersi solo i costi di quelle che utilizzano. Macchine virtuali di Azure consente di ridurre le spese iniziali e gli impegni a lungo termine, assicurando alle organizzazioni la massima scalabilità nello sviluppo e nella gestione delle infrastrutture di SharePoint. Anche in questo caso, il risultato è la possibilità di innovare, sperimentare e ripetere le procedure in poche ore anziché in diversi giorni e settimane come nelle tradizionali distribuzioni.

### Misurazione dell'utilizzo

L'offerta Macchine virtuali di Azure assicura la potenza di elaborazione, la memoria e lo spazio di archiviazione per gli scenari di SharePoint, i cui prezzi sono in genere basati sull'utilizzo delle risorse. Le organizzazioni si assumono solo i costi delle risorse che utilizzano e il servizio fornisce tutta la capacità necessaria per gestire l'infrastruttura SharePoint. Per altre informazioni su prezzi e fatturazione, vedere [Panoramica dei prezzi di Azure](http://azure.microsoft.com/pricing/). Si noti che per il trasferimento di spazio di archiviazione e dati all'esterno del cloud di Azure da una rete locale sono previsti costi nominali. Non sono invece previsti addebiti per il caricamento di dati.

### Flessibilità

Il servizio Macchine virtuali di Azure offre agli sviluppatori la flessibilità per scegliere il linguaggio o l'ambiente di runtime che preferiscono, con il supporto ufficiale per .NET, Node.js, Java e PHP, oltre alla possibilità di scegliere i loro strumenti, grazie al supporto per Microsoft Visual Studio, WebMatrix, Eclipse e editor di testo. Inoltre, Microsoft assicura un percorso a basso costo e a basso rischio verso il cloud e offre procedure semplici e convenienti di provisioning e distribuzione per le esigenze reporting nel cloud, grazie all'accesso a funzionalità di business intelligence (BI) da diversi dispositivi e postazioni. Infine, con l'offerta Azure, oltre a trasferire i dischi rigidi virtuali nel cloud, è anche possibile effettuarne una copia ed eseguirli in locale tramite un altro provider di servizi cloud, purché si disponga della licenza appropriata.

## Processo di provisioning

La raccolta immagini in Azure fornisce l'elenco di VM preconfigurate disponibili. Gli utenti possono pubblicare contenuti di SharePoint Server, SQL Server, Windows Server e altri ISO/VHD nella raccolta immagini. Per semplificare la creazione di VM, nella raccolta vengono create e pubblicate immagini di base. Gli utenti autorizzati possono usare queste immagini per creare la macchina virtuale desiderata. Per ulteriori informazioni, vedere [Creazione di una macchina virtuale che esegue Windows nel portale di anteprima di Azure](virtual-machines-windows-tutorial.md). Nella figura 1 è illustrata la procedura di base per creare una macchina virtuale tramite il portale di gestione di Azure.

**Figura 1: Panoramica della procedura di creazione di una macchina virtuale**

![azure-sharepoint-wp-2](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-2.png)

Gli utenti possono anche caricare un'immagine sottoposta a sysprep nel portale di gestione di Azure. Per ulteriori informazioni, vedere [Creare e caricare un disco rigido virtuale Windows Server in Azure](virtual-machines-create-upload-vhd-windows-server.md). Nella figura 2 è illustrata la procedura di base per caricare un'immagine per creare una macchina virtuale.

**Figura 2: Panoramica della procedura di caricamento di un’immagine**

![azure-sharepoint-wp-3](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-3.png)

## Distribuzione di SharePoint 2010 in Azure 

Per distribuire SharePoint 2010 in Azure, eseguire la procedura seguente:

1. Accedere al [portale di gestione di Azure](http://manage.windowsazure.com/) con l'account della sottoscrizione di Azure. Se non si dispone di un account Azure, [iscriversi per una versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/).
2. Per creare una macchina virtuale con sistema operativo di base, nel portale di gestione di Azure, fare clic su **Nuovo > Calcolo > Macchina virtuale > Da raccolta**.
3. Verrà visualizzata la finestra di dialogo **Scegli un'immagine**. Fare clic sull'immagine della piattaforma **Windows Server 2008 R2 SP1** e quindi sulla freccia a destra.
4. Verrà visualizzata la finestra di dialogo **Virtual machine configuration**. Specificare le informazioni seguenti:
	- Immettere un **Nome macchina virtuale**.
	- Selezionare la dimensione appropriata. Per un ambiente di produzione (server applicazioni e database di SharePoint), è consigliabile scegliere l'opzione A3 (4 core, 7 GB di memoria) o superiore.
	- In **Nuovo nome utente**, digitare un nome di account amministratore locale.
	- In **Nuova password**, digitare una password complessa.
	- In **Conferma**, digitare di nuovo la password, quindi fare clic sulla freccia destra.
5. Verrà visualizzata la seconda finestra di dialogo **Configurazione macchina virtuale**. Specificare le informazioni seguenti:
	- In **Servizio cloud**, selezionare **Crea un nuovo servizio cloud** e specificare un nome DNS del servizio cloud oppure selezionare un servizio cloud esistente.
	- In **Regione/Gruppo di affinità/Rete virtuale**, selezionare la regione che ospiterà l’immagine virtuale.
	- In **Account di archiviazione**, scegliere **Usa un account di archiviazione generato automaticamente** oppure selezionare un nome di account di archiviazione esistente. Verrà creato automaticamente un solo account di archiviazione per ogni area. Tutte le altre macchine virtuali create con questa impostazione verranno inserite in questo account di archiviazione. È possibile creare un massimo di 20 account di archiviazione. Per altre informazioni, vedere [Creare un account di archiviazione in Azure](virtual-machines-create-upload-vhd-windows-server.md#step-2-create-a-storage-account-in-azure).
	- In **Set di disponibilità**, selezionare **(nessuno)** e fare clic sulla freccia destra.
6. Nella terza finestra di dialogo **Configurazione macchina virtuale**, fare clic sul segno di spunta per creare la macchina virtuale.

Per connettersi alla macchina virtuale, vedere [Come accedere a una macchina virtuale che esegue Windows Server](virtual-machines-log-on-windows-server.md).

Creare la macchina virtuale di SQL Server con una delle opzioni seguenti:

- Creare una macchina virtuale di SQL Server 2012 eseguendo i passaggi da 1 a 7 descritti in precedenza, tuttavia, nel passaggio 3, usare l'immagine di SQL Server 2012 anziché quella di Windows Server 2008 R2 SP1. Per altre informazioni, vedere [Provisioning di una macchina virtuale di SQL Server in Azure](virtual-machines-provision-sql-server.md).
	- Se si sceglie questa opzione, tramite il processo di provisioning viene mantenuta una copia dei file del programma di installazione di SQL Server 2012 nel percorso della directory C:\SQLServer_11.0_Full, in modo che sia possibile personalizzare l'installazione. È ad esempio possibile convertire l'installazione di valutazione di SQL Server 2012 in una versione con licenza usando il codice di licenza.

- Utilizzare l'Utilità preparazione sistema (SysPrep) di SQL Server per installare SQL Server nella macchina virtuale con il sistema operativo di base, come illustrato in precedenza nei passaggi da 1 a 7. Per ulteriori informazioni, vedere [Installare SQL Server 2012 tramite SysPrep](http://msdn.microsoft.com/library/ee210664.aspx).

- Utilizzare il prompt dei comandi per installare SQL Server. Per altre informazioni, vedere [Installare SQL Server 2012 dal prompt dei comandi](http://msdn.microsoft.com/library/ms144259.aspx#SysPrep).

- Usare supporti compatibili con SQL Server e il proprio codice di licenza per installare SQL Server nella macchina virtuale con il sistema operativo di base, come illustrato in precedenza nei passaggi da 1 a 7.

Creare la farm di SharePoint effettuando i passaggi seguenti:

Passaggio 1. Configurare la sottoscrizione di Azure usando i file di script.

Passaggio 2. Eseguire il provisioning dei server SharePoint creando un'altra macchina virtuale con il sistema operativo di base, come illustrato in precedenza nei passaggi da 1 a 6. Per creare un server SharePoint nella macchina virtuale corrente, scegliere una delle opzioni seguenti:

- Provisioning tramite l'interfaccia utente grafica di SharePoint:
	- Per creare una farm di SharePoint ed eseguirne il provisioning, vedere [Creare una farm di Microsoft SharePoint Server](http://technet.microsoft.com/library/ee805948.aspx#CreateConfigure).
	- Per aggiungere un server Web o un server applicazioni alla farm, vedere [Aggiungere un server Web o un server applicazioni alla farm](http://technet.microsoft.com/library/cc261752.aspx).
	- Per aggiungere un server di database a una farm esistente, vedere [Aggiungere un server di database a una farm esistente in SharePoint 2013](http://technet.microsoft.com/library/cc262781).
	- Per utilizzare SQL Server 2012 per la farm di SharePoint, è necessario scaricare e installare il Service Pack 1 per SharePoint Server 2010 dopo aver installato l'applicazione e aver scelto di non configurare il server. Per ulteriori informazioni, vedere [Service Pack 1 per Microsoft SharePoint Server 2010](http://www.microsoft.com/download/details.aspx?id=26623).
	- Per sfruttare le funzionalità di business intelligence di SQL Server, è consigliabile installare SharePoint Server come server farm anziché come server autonomo. Per altre informazioni, vedere [Installare le funzionalità di business intelligence di SQL Server 2012](http://technet.microsoft.com/library/hh231681.aspx).

- Eseguire il provisioning di Microsoft Windows PowerShell: è possibile usare lo strumento da riga di comando Psconfig come interfaccia alternativa per eseguire diverse operazioni che controllano la modalità di provisioning dei prodotti SharePoint 2010. Per ulteriori informazioni, vedere [Informazioni di riferimento sullo strumento da riga di comando Psconfig](http://technet.microsoft.com/library/cc263093.aspx).

Passaggio 3. Configurare SharePoint. Quando ogni macchina virtuale di SharePoint è pronta per essere utilizzata, configurare SharePoint Server in ogni server scegliendo una delle opzioni seguenti:

- Configurare SharePoint dalla GUI.
- Configurare SharePoint tramite Windows PowerShell. Per ulteriori informazioni, vedere [Installare SharePoint Server 2010 tramite Windows PowerShell](http://technet.microsoft.com/library/cc262839.aspx).
- È anche possibile utilizzare CodePlex Project AutoSPInstaller, che comprende script di Windows PowerShell, un file di input XML e un file batch di Microsoft Windows standard. AutoSPInstaller fornisce un framework per uno script di installazione di SharePoint 2010 basato su Windows PowerShell. Per altre informazioni, vedere [CodePlex: AutoSPInstaller](http://autospinstaller.codeplex.com/).

Passaggio 4. Una volta completato lo script, connettersi alla macchina virtuale utilizzando l’apposito dashboard.

Per verificare la configurazione di SharePoint, accedere al server SharePoint e utilizzare Amministrazione centrale.

> [AZURE.NOTE]assicurarsi di configurare la sicurezza nell'endpoint del portale di gestione e di impostare una porta in ingresso in Windows Firewall della macchina virtuale. Quindi, verificare che sia possibile avviare una sessione remota di Windows PowerShell in uno dei server applicazioni SharePoint aprendo una sessione di Windows PowerShell con credenziali di amministratore.

### Creazione e caricamento di un disco rigido virtuale

È anche possibile creare le proprie immagini e caricarle in Azure come file VHD. Per informazioni, vedere [Creare e caricare un disco rigido virtuale Windows Server in Azure](virtual-machines-create-upload-vhd-windows-server.md).

## Scenari di utilizzo

In questa sezione vengono illustrati alcuni importanti scenari di clienti per le distribuzioni di SharePoint tramite Macchine virtuali di Azure.

### Scenario 1: Semplice ambiente di sviluppo e test di SharePoint

Le organizzazioni cercano soluzioni più flessibili per creare applicazioni di SharePoint e configurare gli ambienti per i test e lo sviluppo all'interno e in delocalizzazione. Fondamentalmente, desiderano abbreviare i tempi richiesti per configurare i progetti di sviluppo di applicazioni di SharePoint e ridurre i costi incrementando l'utilizzo degli ambienti di test. Potrebbero ad esempio scegliere di eseguire test di carico su richiesta in SharePoint Server e svolgere test di accettazione con più utenti simultanei in diverse località geografiche. Analogamente, l'integrazione di team interni e delocalizzati rappresenta un'esigenza aziendale sempre più importante per molte organizzazioni odierne.

In questo scenario viene illustrato come utilizzare farm di SharePoint preconfigurate per i carichi di lavoro di sviluppo e test. Una topologia di distribuzione di SharePoint è esattamente uguale a una distribuzione virtualizzata in locale. Le attuali competenze IT sono perfettamente adeguate per la distribuzione di Macchine virtuali di Azure, con il principale vantaggio costituito da un passaggio quasi completo da spese di capitale a spese operative, in quanto non è necessario l'acquisto iniziale di server fisici. Le organizzazioni possono eliminare i costi di capitale per l'hardware dei server e acquisire flessibilità grazie a una riduzione sostanziale dei tempi di provisioning necessari per creare, configurare o estendere una farm di SharePoint per un ambiente di test e sviluppo. I responsabili IT possono aggiungere e rimuovere dinamicamente la capacità in base a mutevoli esigenze di test e sviluppo. Possono inoltre dedicare più risorse ad attività a valore aggiunto con i progetti di SharePoint e meno alla gestione dell'infrastruttura.

Per sfruttare appieno i computer dei test di carico, le organizzazioni possono configurare sistemi virtualizzati SharePoint di test e sviluppo in Azure con il supporto del sistema operativo Windows Sever 2008 R2. In questo modo i team di sviluppo possono creare e testare le applicazioni ed eseguire facilmente la migrazione ad ambienti di produzione locali o su cloud senza modifiche al codice. È possibile utilizzare gli stessi framework e set di strumenti in locale e nel cloud, consentendo l'accesso di team distribuiti allo stesso ambiente. Gli utenti possono anche accedere a dati e applicazioni locali stabilendo una connessione VPN diretta.

Nella figura 3 è illustrato un ambiente di test e sviluppo di SharePoint in una macchina virtuale di Azure. Per creare questa distribuzione, sfruttare inizialmente lo stesso ambiente di test e sviluppo di SharePoint usato per sviluppare applicazioni. Quindi, caricare e distribuire le applicazioni nella macchina virtuale di Azure per i test e lo sviluppo. In seguito, sarà possibile spostare di nuovo le applicazioni in locale senza la necessità di modificarle.

**Figura 3: Ambiente di sviluppo e test di SharePoint in macchine virtuali di Azure**

![azure-sharepoint-wp-11](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-11.png)

Per implementare un ambiente di test e sviluppo di SharePoint in Azure, eseguire la procedura seguente:

1. Provisioning: eseguire innanzitutto il provisioning di una connessione VPN tra l'ambiente locale e Azure tramite Rete virtuale di Azure. Poiché in questo caso non si usa Active Directory, è necessario un tunnel VPN. Per altre informazioni, vedere [Panoramica di Rete virtuale](http://msdn.microsoft.com/library/jj156007.aspx). Usare quindi il portale di gestione per eseguire il provisioning di una nuova macchina virtuale da un'immagine della raccolta.
	- È possibile caricare le macchine virtuali di test e sviluppo di SharePoint locali nell'account di archiviazione di Azure e farvi riferimento tramite la raccolta immagini per creare l'ambiente richiesto.
	- È possibile utilizzare l'immagine di SQL Server 2012 anziché quella di Windows Server 2008 R2 SP1. Per altre informazioni, vedere [Provisioning di una macchina virtuale di SQL Server in Azure](virtual-machines-provision-sql-server.md).

2. Installazione: installare SharePoint Server, Visual Studio e SQL Server nelle macchine virtuali usando una connessione Desktop remoto.
	- Usare gli strumenti SharePoint 2010 Easy Setup Script per creare un computer SharePoint per lo sviluppo. Per ulteriori informazioni, vedere [SharePoint 2010 Easy Setup Script](http://www.microsoft.com/download/details.aspx?id=23415). Usare Windows PowerShell. Per altre informazioni, vedere [Installare SharePoint Server 2010 tramite Windows PowerShell](http://technet.microsoft.com/library/cc262839.aspx). Usare CodePlex Project AutoSPInstaller. Per ulteriori informazioni, vedere [CodePlex: AutoSPInstaller](http://autospinstaller.codeplex.com/).
	- Installare Visual Studio. Per ulteriori informazioni, vedere [Installazione di Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).
	- Installare SQL Server. Per ulteriori informazioni, vedere [Installare SQL Server tramite SysPrep](http://msdn.microsoft.com/library/ee210664.aspx).
3. Sviluppo di pacchetti e script di distribuzione per applicazioni e database: se si prevede di usare una macchina virtuale disponibile nella raccolta immagini, è possibile distribuire le applicazioni e i database locali desiderati in Macchine virtuali di Azure:
	- Creare i pacchetti di distribuzione per le applicazioni e i database locali esistenti usando SQL Server Data Tools e Visual Studio.
	- Usare questi pacchetti per distribuire le applicazioni e i database in Macchine virtuali di Azure.
4. Distribuire applicazioni e database di SharePoint:
	- Configurare la sicurezza nell'endpoint del portale di gestione per impostare una porta di ingresso in Windows Firewall della macchina virtuale.
	- Distribuire le applicazioni e i database di SharePoint in Macchine virtuali di Azure utilizzando i pacchetti e gli script di distribuzione creati nel passaggio 3.
- Testare le applicazioni e i database distribuiti.
5. Gestire le macchine virtuali:
	- Monitorare le macchine virtuali tramite il portale di gestione.
	- Monitorare le applicazioni tramite Visual Studio e SQL Server Management Studio.
	- È inoltre possibile monitorare e gestire le macchine virtuali usando software di gestione locale, come Microsoft System Center - Operations Manager.

### Scenario 2: Farm di SharePoint pubblica con personalizzazione

Le organizzazioni desiderano creare una presenza su Internet ospitata nel cloud che sia facilmente scalabile in base alle esigenze e alla domanda. Desiderano inoltre creare siti Web Extranet per la collaborazione con i partner e implementare procedure semplificate e distribuite di creazione e approvazione del relativo contenuto. Infine, per gestire l'aumento dei carichi di lavoro, desiderano fornire capacità su richiesta ai loro siti Web.

In questo scenario si utilizza SharePoint Server come base per l'hosting di un sito Web pubblico. In questo modo le organizzazioni possono rapidamente distribuire, personalizzare e ospitare i loro siti Web aziendali in un'infrastruttura cloud scalabile e sicura. Con i siti Web pubblici di SharePoint in Azure, le organizzazioni si assicurano la scalabilità per far fronte all'aumento del traffico e si assumono solo i costi delle risorse che utilizzano. È possibile utilizzare strumenti comuni, simili a quelli impiegati in locale, per la creazione di contenuti, il flusso di lavoro e l'approvazione con SharePoint in Azure.

Inoltre, utilizzando Macchine virtuali di Azure, le organizzazioni possono facilmente configurare ambienti di gestione temporanea e di produzione in esecuzione su macchine virtuali. Le macchine virtuali pubbliche di SharePoint create in Azure possono essere sottoposte a backup in uno spazio di archiviazione virtuale. Inoltre, ai fini del ripristino di emergenza, la funzionalità di replica geografica continua consente alle organizzazione di eseguire automaticamente il backup delle macchine virtuali di un data center in un altro data center situato a chilometri di distanza.

Le macchine virtuali dell'infrastruttura Azure sono convalidate e supportate per l'uso con altri prodotti Microsoft, ad esempio SQL Server e SharePoint Server. Azure e SharePoint Server funzionano meglio insieme: fanno entrambi parte della famiglia di prodotti Microsoft e sono pienamente integrati, supportati e testati per offrire nell'insieme un'esperienza ottimale. Prevedono entrambi un singolo punto di supporto per l'applicazione SharePoint e l'infrastruttura Azure.

In questo scenario è necessario aggiungere altri server Web front-end per SharePoint in modo da supportare l'aumento del traffico. Questi server richiedono sicurezza avanzata e controller di dominio di Servizi di dominio di Active directory per supportare l'autenticazione e l'autorizzazione degli utenti. Nella figura 4 è illustrato il layout di questo scenario.

**Figura 4: Farm di SharePoint pubblico con personalizzazione**

![azure-sharepoint-wp-12](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-12.png)

Per implementare una farm di SharePoint pubblica in Azure, eseguire la procedura seguente:

1. Distribuzione di Active Directory: i requisiti di base per la distribuzione di Active Directory in Macchine virtuali di Azure sono simili, ma non identici, a quelli della distribuzione in macchine virtuali (e, in una certa misura, in computer fisici) in locale. Per altre informazioni sulle differenze, oltre a linee guida e altre considerazioni, vedere [Linee guida per la distribuzione di Active Directory in macchine virtuali di Azure](http://msdn.microsoft.com/library/jj156090). Per distribuire Active Directory in Azure:
	- Definire e creare una rete virtuale in cui è possibile assegnare le macchine virtuali a specifiche subnet.
	- Usare il portale di gestione per creare e distribuire il controller di dominio in una nuova macchina virtuale in Azure. È anche possibile fare riferimento allo script di Windows PowerShell per distribuire un dominio autonomo nel cloud usando Macchine virtuali e Rete virtuale di Azure. Per altre informazioni sulla creazione di una nuova foresta Active Directory in una macchina virtuale di Rete virtuale di Azure, vedere [Installazione di una nuova foresta Active Directory in Azure](active-directory-new-forest-virtual-machine.md).
2. Provisioning di una macchina virtuale: usare il portale di gestione per eseguire il provisioning di una nuova macchina virtuale da un'immagine della raccolta.
3. Distribuire una farm di SharePoint.
	- Usare il portale di gestione per configurare il bilanciamento del carico. Configurare gli endpoint della macchina virtuale, selezionare l'opzione per il bilanciamento del carico del traffico in un endpoint esistente, quindi specificare il nome della macchina virtuale con carico bilanciato.
	- Aggiungere un'altra macchina virtuale Web front-end alla farm di SharePoint esistente per il traffico aggiuntivo.
3. Gestire le macchine virtuali:
	- Monitorare le macchine virtuali tramite il portale di gestione.
	- Monitorare la farm di SharePoint tramite Amministrazione centrale.

### Scenario 3: Farm con scalabilità orizzontale per servizi aggiuntivi di business intelligence

Le funzionalità di business intelligence sono essenziali per acquisire informazioni significative e prendere soluzioni rapide e oculate. Con la transizione da un approccio locale, le organizzazioni desiderano distribuire le applicazioni di business intelligence nel cloud senza apportare modifiche all'ambiente esistente. Desiderano ospitare i report di SQL Server Analysis Services (SSAS) o SQL Server Reporting Services (SSRS) in un ambiente ampiamente affidabile e disponibili, mantenendo il massimo controllo dell'applicazione di business intelligence e senza dedicare troppo tempo o risorse di budget alla manutenzione.

In questo scenario viene illustrato come utilizzare Macchine virtuali di Azure per ospitare applicazioni di business intelligence mission-critical. Le organizzazioni possono distribuire farm di SharePoint in Macchine virtuali di Azure e scalare orizzontalmente i componenti di BI della macchina virtuale del server applicazioni, come SSRS o Excel Services. Scalando i componenti a uso intensivo di risorse nel cloud, è possibile supportare in modo più semplice ed efficace i carichi di lavoro speciali. Si noti che SQL Server offre prestazioni elevate in Macchine Virtuali di Azure. Inoltre, le istanze di SQL Server sono facilmente scalabili da installazioni di piccole dimensioni a quelle molto grandi. In questo modo le organizzazioni ottengono la flessibilità per eseguire dinamicamente il provisioning (espansione) o il deprovisioning (riduzione) delle istanze di business intelligence in base ai requisiti immediati del carico di lavoro.

La migrazione delle attuali applicazioni di business intelligence in Azure assicura un ridimensionamento migliore. Con le funzionalità avanzate di SSAS, SSRS e SharePoint Server, le organizzazioni possono creare potenti applicazioni e dashboard di BI e reporting ampiamente scalabili in entrambe le direzioni. Tali applicazioni e dashboard possono inoltre essere integrati in modo sicuro con le applicazioni e i dati locali. Azure assicura la conformità del data center con il supporto per lo standard ISO 27001. Per altre informazioni, vedere   [Centro protezione Azure](http://azure.microsoft.com/support/trust-center/compliance/).

Per scalare orizzontalmente la distribuzione dei componenti di business intelligence, è necessario installare un nuovo server applicazioni con servizi come PowerPivot, Power View, Excel Services o PerformancePoint Services. In alternativa, è necessario aggiungere istanze di BI di SQL Server come SSAS o SSRS alla farm esistente per supportare l'elaborazione aggiuntiva di query. Il server può essere aggiunto come nuova macchina virtuale di Azure con SharePoint 2010 Server o SQL Server installato. Sarà quindi possibile installare, distribuire e configurare i componenti di business intelligence in tale server (figura 5).

**Figura 5: Farm di SharePoint con scalabilità orizzontale per servizi BI aggiuntivi**

![azure-sharepoint-wp-13](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-13.png)

Per scalare orizzontalmente un ambiente di business intelligence in Azure, eseguire la procedura seguente:

1. Provisioning:
	- Eseguire il provisioning di una connessione VPN tra l'ambiente locale e Azure tramite Rete virtuale di Azure. Per altre informazioni, vedere [Panoramica di Rete virtuale](http://msdn.microsoft.com/library/jj156007.aspx).
	- Usare il portale di gestione per eseguire il provisioning di una nuova macchina virtuale da un'immagine della raccolta. È possibile caricare le immagini dei carichi di lavoro BI di SharePoint Server o SQL Server nella raccolta immagini. In seguito, qualsiasi utente autorizzato potrà selezionare queste macchine virtuali di componenti di BI per creare l'ambiente con scalabilità orizzontale.
2. Installare: 
	- se l'organizzazione non dispone di immagini predefinite di componenti di BI di SharePoint Server o SQL Server, installare SharePoint Server e SQL Server nelle macchine virtuali usando una connessione Desktop remoto.
	- Per altre informazioni sull'installazione di SharePoint, vedere [Installare SharePoint Server 2010 tramite Windows PowerShell](http://technet.microsoft.com/library/cc262839.aspx) o [CodePlex: AutoSPInstaller](http://autospinstaller.codeplex.com/).
	- Per altre informazioni sull'installazione di SQL Server, vedere [Installare SQL Server tramite SysPrep](http://msdn.microsoft.com/library/ee210664.aspx).
3. Aggiungere la macchina virtuale BI:
	- Configurare la sicurezza nell'endpoint del portale di gestione per impostare una porta di ingresso in Windows Firewall della macchina virtuale.
	- Aggiungere la macchina virtuale BI creata alla farm di SharePoint o SQL Server esistente.
4. Gestire le macchine virtuali:
	- Monitorare le macchine virtuali tramite il portale di gestione.
	- Monitorare la farm di SharePoint tramite Amministrazione centrale.
	- Monitorare e gestire le macchine virtuali usando software di gestione locale come Microsoft System Center - Operations Manager.

### Scenario 4: Sito Web basato su SharePoint completamente personalizzato

Le organizzazioni desiderano sempre più spesso creare siti Web di SharePoint completamente personalizzati nel cloud. Avvertono l'esigenza di un ambiente ampiamente affidabile e disponibile che offra il controllo completo per mantenere complesse applicazioni in esecuzione sul cloud, ma senza dedicare grandi quantità di tempo e budget.

In questo scenario le organizzazioni possono distribuire l'intera farm di SharePoint nel cloud e scalare dinamicamente tutti i componenti per ottenere capacità aggiuntiva. In alternativa, possono estendere la distribuzione locale nel cloud per incrementare la capacità e migliorare le prestazioni quando è necessario. Questo scenario è destinato alle organizzazioni che desiderano un'esperienza completa con SharePoint per lo sviluppo di applicazioni e la gestione di contenuti aziendali. I siti più complessi possono anche includere funzionalità avanzate di reporting, Power View, PerformancePoint, PowerPivot, grafici approfonditi e la maggior parte delle altre funzionalità dei siti di SharePoint per la piena funzionalità end-to-end.

Le organizzazioni possono utilizzare Macchine virtuali di Azure per ospitare applicazioni personalizzate e i componenti associati in un'infrastruttura cloud ampiamente sicura e a costi contenuti. Possono inoltre usare la versione locale di Microsoft System Center come strumento di gestione comune per le applicazioni locali e su cloud.

Per implementare un sito Web di SharePoint completamente personalizzato in Azure, è necessario distribuire un dominio di Active Directory nel cloud ed eseguirvi il provisioning di nuove macchine virtuali. È quindi necessario creare e configurare una macchina virtuale che esegue SQL Server 2012 come parte di una farm di SharePoint. Infine, è necessario creare la farm di SharePoint con carico bilanciato e connetterla ad Active Directory e SQL Server (figura 6).

**Figura 6: Sito Web basato su SharePoint completamente personalizzato**

![azure-sharepoint-wp-14](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-14.png)

Nella procedura seguente viene illustrato come creare un ambiente farm di SharePoint personalizzato dalle immagini predefinite disponibili nella raccolta immagini. Si noti, tuttavia, che è anche possibile caricare le macchine virtuali della farm di SharePoint nella raccolta immagini. In seguito, gli utenti autorizzati potranno scegliere queste macchine virtuali per creare la necessaria farm di SharePoint in Azure.

1. Distribuzione di Active Directory: i requisiti di base per la distribuzione di Active Directory in Macchine virtuali di Azure sono simili, ma non identici, a quelli della distribuzione in macchine virtuali (e, in una certa misura, in computer fisici) in locale. Per altre informazioni sulle differenze, oltre a linee guida e altre considerazioni, vedere [Linee guida per la distribuzione di Active Directory in macchine virtuali di Azure](http://msdn.microsoft.com/library/jj156090). Per distribuire Active Directory in Azure:
	- Definire e creare una rete virtuale in cui è possibile assegnare le macchine virtuali a specifiche subnet.
	- Usare il portale di gestione per creare e distribuire il controller di dominio in una nuova macchina virtuale in Azure.
	- Per altre informazioni sulla creazione di una nuova foresta Active Directory in una macchina virtuale di Rete virtuale di Azure, vedere [Installazione di una nuova foresta Active Directory in Azure](active-directory-new-forest-virtual-machine).
2. Distribuire SQL Server:
	- Usare il portale di gestione per eseguire il provisioning di una nuova macchina virtuale da un'immagine della raccolta.
	- Configurare SQL Server nella macchina virtuale. Per ulteriori informazioni, vedere [Installare SQL Server tramite SysPrep](http://msdn.microsoft.com/library/ee210664.aspx).
	- Aggiungere la macchina virtuale al dominio di Active Directory appena creato.
3. Distribuire una farm di SharePoint multiserver:
	- Creare una rete virtuale. Per altre informazioni, vedere [Panoramica di Rete virtuale](http://msdn.microsoft.com/library/jj156007.aspx).
	- Quando si distribuiscono macchine virtuali di SharePoint, è necessario che vengano fornite subnet per SharePoint Server in modo che durante il provisioning siano disponibili gli indirizzi DNS dell'implementazione di Active Directory locale.
	- Utilizzare il portale di gestione per creare una macchina virtuale.
	- Installare SharePoint Server in questa macchina virtuale e generare un'immagine riutilizzabile. Per altre informazioni sull'installazione di SharePoint Server, vedere [Installare SharePoint Server 2010 tramite Windows PowerShell ](http://technet.microsoft.com/library/cc262839.aspx) o [CodePlex: AutoSPInstaller](http://autospinstaller.codeplex.com/).
	- Configurare la macchina virtuale di SharePoint in modo da creare e connettersi alla farm di SharePoint usando il comando [Join-SharePointFarm](http://technet.microsoft.com/library/ff607979.aspx).
	- Utilizzare il portale di gestione per configurare il servizio di bilanciamento del carico: configurare gli endpoint della macchina virtuale, selezionare l'opzione per il bilanciamento del carico del traffico in un endpoint esistente, quindi specificare il nome della macchina virtuale con carico bilanciato.
4. Gestire la farm di SharePoint tramite System Center:
	- Usare l'agente Operations Manager e il nuovo Azure Integration Pack per connettere la versione locale di System Center a Macchine virtuali di Azure.
	- Usare le versioni locali di App Controller e Orchestrator per le funzioni di gestione.

## Conclusioni

Macchine virtuali di Azure offre la piena continuità delle distribuzioni di SharePoint. Si tratta di una soluzione completamente supportata e testata per assicurare un'esperienza ottimale con altre applicazioni Microsoft. Di conseguenza, le organizzazioni possono facilmente configurare e distribuire SQL Server all'interno di Azure, per eseguire il provisioning dell'infrastruttura per una nuova distribuzione di SharePoint oppure per espanderne una esistente. Con l'aumento dei carichi di lavoro aziendali, le organizzazioni possono rapidamente espandere l'infrastruttura SharePoint. Analogamente, se le esigenze dei carichi di lavoro diminuiscono, possono acquisire risorse su richiesta, assumendosi solo i costi di quelle che usano. Macchine virtuali di Azure offre un'eccellente infrastruttura per un'ampia varietà di requisiti aziendali, come dimostrato nei quattro scenari basati su SharePoint presentati in questo documento.

Per una corretta distribuzione di SharePoint Server in Macchine virtuali di Azure, è necessaria un'attenta pianificazione, soprattutto considerando la gamma di opzioni disponibili per l'architettura e la distribuzione di farm strategiche. Le informazioni e le procedure dettagliate descritte in questo articolo possono risultare utili per prendere decisioni oculate sull'implementazione di una distribuzione di SharePoint.

## Risorse aggiuntive

[SharePoint in Macchine virtuali di Azure](http://msdn.microsoft.com/library/dn275955.aspx)

[Farm di SharePoint ospitati nei servizi di infrastruttura di Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Azure PowerShell](http://msdn.microsoft.com/library/jj156055)

[Cmdlet di gestione di Azure](http://msdn.microsoft.com/library/jj152841)

<!---HONumber=58--> 