<properties urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Introduzione ad Azure Site Recovery: protezione da sito locale a sito locale" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery coordina la replica, il failover e il ripristino in Azure di macchine virtuali Hyper-V ubicate in cloud VMM locali a un altro sito locale." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Introduzione ad Azure Site Recovery: protezione da sito locale a sito locale" editor="jimbe" manager="johndaw" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew" />

# Introduzione ad Azure Site Recovery: protezione da sito locale a sito locale

<div class="dev-callout">

Usare Azure Site Recovery per orchestrare la protezione per le macchine virtuali in server host Hyper-V locali ubicati in cloud VMM. È possibile configurare:

-   **Protezione da sito locale a sito locale**: replica di macchine virtuali locali in un altro sito locale. Le impostazioni di protezione vengono configurate e abilitate negli insiemi di credenziali di Azure Site Recovery. I dati delle macchine virtuali vengono replicati tra server Hyper-V locali. Altre informazioni su questo scenario sono disponibili in [Introduzione ad Azure Site Recovery: protezione da sito locale a sito locale][Introduzione ad Azure Site Recovery: protezione da sito locale a sito locale].
-   **Protezione da sito locale ad Azure**: è possibile replicare le macchine virtuali locali in Azure. Le impostazioni di protezione vengono configurate e abilitate negli insiemi di credenziali di Azure Site Recovery. I dati delle macchine virtuali vengono replicati da un server Hyper-V locale nell'archiviazione di Azure. Altre informazioni su questo scenario sono disponibili in [Introduzione ad Azure Site Recovery: protezione da sito locale ad Azure][Introduzione ad Azure Site Recovery: protezione da sito locale ad Azure]

## <span id="about"></span></a>Informazioni sull'esercitazione

Usare questa esercitazione per configurare un modello di prova rapido per Azure Site Recovery in una distribuzione da sito locale ad Azure. Dove possibile, vengono usati il percorso più rapido e le impostazioni predefinite. Verranno effettuate le operazioni di creazione di un insieme di credenziali di Azure Site Recovery, installazione del provider di Azure Site Recovery nel server VMM di origine, configurazione delle impostazioni di protezione del cloud, abilitazione della protezione per le macchine virtuali e test della distribuzione.

Per informazioni su una distribuzione completa, vedere:

-   [Pianificare la distribuzione di Azure Site Recovery][Pianificare la distribuzione di Azure Site Recovery]: descrive i passaggi di pianificazione che è necessario eseguire prima di avviare una distribuzione completa.
-   [Distribuire Azure Site Recovery: protezione da sito locale a sito locale][Distribuire Azure Site Recovery: protezione da sito locale a sito locale]: fornisce istruzioni dettagliate per una distribuzione completa.

In caso di problemi durante questa esercitazione, vedere l'articolo wiki su [Azure Site Recovery: Scenari di errore comuni e soluzioni][Azure Site Recovery: Scenari di errore comuni e soluzioni] oppure inviare domande al [forum sui Servizi di ripristino di Azure][forum sui Servizi di ripristino di Azure].

</div>

## <span id="before"></span></a>Prima di iniziare

<div class="dev-callout">

Prima di iniziare l'esercitazione, verificare i prerequisiti seguenti.

### <span id="HVRMPrereq"></span></a>Prerequisiti

-   **Account Azure**: è necessario un account Azure. Se non si dispone di un account, vedere la pagina relativa alla [versione di prova gratuita di Azure][versione di prova gratuita di Azure]. Ottenere [informazioni dettagliate sui prezzi di Azure Site Recovery Manager][informazioni dettagliate sui prezzi di Azure Site Recovery Manager].
-   **Server VMM**: sarà necessario almeno un server VMM in esecuzione su System Center 2012 SP1 o System Center 2012 R2.
-   **Cloud VMM**: è necessario almeno un cloud sul server VMM di origine che si vuole proteggere e uno sul server VMM di destinazione. Se è in esecuzione un server VMM, saranno necessari due cloud. Il cloud primario da proteggere deve includere quanto segue:
    -   Uno o più gruppi host VMM
    -   Uno o più cluster o server host Hyper-V in ogni gruppo host.
    -   Una o più macchine virtuali situate nel server Hyper-V di origine nel cloud.

## <span id="tutorial"></span></a>Passaggi dell'esercitazione

Dopo la verifica dei prerequisiti, eseguire le operazioni seguenti:

-   [Passaggio 1: Creare un insieme di credenziali][Passaggio 1: Creare un insieme di credenziali]: creare un insieme di credenziali di Azure Site Recovery.
-   [Passaggio 2: Installare l'applicazione del provider][Passaggio 2: Installare l'applicazione del provider]: generare una chiave di registrazione ed eseguire l'applicazione Microsoft Azure Site Recovery Provider sul server VMM. In questo modo viene installato il provider e viene registrato il server VMM nell'insieme di credenziali.
-   [Passaggio 3: Configurare la protezione cloud][Passaggio 3: Configurare la protezione cloud]: configurare le impostazioni di protezione per i cloud VMM.
-   <a href="#networkmapping">Passaggio 5: Configurare il mapping di rete: facoltativamente, è possibile configurare il mapping di rete in modo da mappare le reti VM di origine alle reti Azure di destinazione.
-   [Passaggio 6: Configurare il mapping di archiviazione][Passaggio 6: Configurare il mapping di archiviazione]: facoltativamente, è possibile configurare il mapping di archiviazione per mappare le classificazioni di archiviazione presenti sul server VMM di origine a quelle presenti sul server di destinazione.
-   [Passaggio 7: Abilitare la protezione per le macchine virtuali][Passaggio 7: Abilitare la protezione per le macchine virtuali]: abilitare la protezione per le macchine virtuali presenti in cloud VMM protetti.
-   [Passaggio 8: Configurare ed eseguire piani di ripristino][Passaggio 8: Configurare ed eseguire piani di ripristino]: creare un piano di ripristino ed eseguire un failover di test per tale piano.

<a name="vault"></a>

## Passaggio 1: Creare un insieme di credenziali

</p>
1.  Accedere al [portale di gestione][portale di gestione].

2.  Espandere **Servizi dati**, espandere **Servizi di ripristino** e fare clic su **Insieme di credenziali di Azure Site Recovery**.

3.  Fare clic su **Crea nuovo** e quindi su **Creazione rapida**.

4.  In **Name** immettere un nome descrittivo per identificare l'insieme di credenziali.

5.  In **Region** selezionare l'area geografica per l'insieme di credenziali. Per verificare le aree geografiche supportate vedere [Prezzi di Azure Site Recovery][Prezzi di Azure Site Recovery]

6.  Fare clic su **Crea insieme di credenziali**.

    ![New Vault][New Vault]

Controllare la barra di stato per verificare che l'insieme di credenziali sia stato creato correttamente. L'insieme di credenziali verrà elencato come **Attivo** nella pagina principale di Servizi di ripristino.

<a name="upload"></a>

## Passaggio 2: Configurare l'insieme di credenziali

</p>
1.  Nella pagina **Servizi di ripristino** fare clic sull'insieme di credenziali per aprire la pagina Avvio rapido. La pagina Avvio rapido può essere anche aperta in qualsiasi momento tramite l'icona.

    ![Quick Start Icon][Quick Start Icon]

2.  Nell'elenco a discesa, selezionare **Tra due siti Hyper-V locali**.
3.  In **Preparare i server VMM** fare clic sul file **Genera file del codice di registrazione**. Il codice è valido per 5 giorni dal momento in cui viene generato. Copiare il file nel server VMM. Il file sarà necessario quando si configura il provider.

    ![Registration key][Registration key]

<a name="download"></a>

## Passaggio 3: Installare il provider di Azure Site Recovery

</p>
1.  Nella pagina **Avvio rapido**, in **Preparare i server VMM**, fare clic su **Scaricare il provider di Microsoft Azure Site Recovery e installarlo nei server VMM** per ottenere la versione più recente del file di installazione del provider.

2.  Eseguire il file nei server VMM di origine e di destinazione.

3.  In **Verifica prerequisiti** scegliere di arrestare il servizio VMM per avviare il programma di installazione del provider. Il servizio verrà arrestato e riavviato automaticamente al termine dell'installazione.

    ![Prerequisiti][Prerequisiti]

4.  In **Microsoft Update** è possibile acconsentire esplicitamente a ricevere gli aggiornamenti. Se questa impostazione è abilitata, gli aggiornamenti del provider verranno installati in base ai criteri indicati in Microsoft Update.

    ![Microsoft Updates][Microsoft Updates]

Dopo l'installazione del provider, continuare con il programma di installazione per registrare il server nell'insieme di credenziali.

1.  Nella pagina Connessione Internet specificare la modalità di connessione a Internet del provider in esecuzione nel server VMM. Selezionare **Usa impostazioni proxy del sistema predefinite** per usare le impostazioni di connessione a Internet predefinite configurate nel server.

    ![Internet Settings][Internet Settings]

2.  Nella pagina **Codice di registrazione** selezionare l'opzione relativa al download da Azure Site Recovery e alla copia nel server VMM.
3.  In **Nome insieme di credenziali** verificare il nome dell'insieme di credenziali in cui verrà registrato il server.
4.  In **Nome server** specificare un nome descrittivo per identificare il server VMM nell'insieme di credenziali.

    ![Server registration][Server registration]

5.  In **Sincronizzazione iniziale metadati** scegliere se si vogliono sincronizzare i metadati per tutti i cloud nel server VMM con l'insieme di credenziali. È necessario eseguire questa azione solo una volta in ogni server. Se non si vogliono sincronizzare tutti i cloud, è possibile lasciare deselezionata questa opzione e sincronizzare ogni cloud singolarmente nelle proprietà del cloud nella console VMM.

6.  In **Crittografia dati** viene generato un certificato che viene usato per crittografare i dati protetti in Azure.
    Questa opzione non è importante se si esegue la replica da un sito locale a un altro sito locale.

    ![Server registration][1]

7.  Fare clic su **Register** per completare il processo. Dopo la registrazione, i metadati del server VMM vengono recuperati da Azure Site Recovery. Il server viene visualizzato nella scheda **Risorse** della pagina **Server** nell'insieme di credenziali.

## <span id="clouds"></span></a>Passaggio 4: Configurare le impostazioni di protezione del cloud

Dopo la registrazione dei server VMM, sarà possibile configurare le impostazioni di protezione del cloud. L'opzione **Sincronizza dati cloud con insieme credenziali** è stata abilitata in fase di installazione del provider per visualizzare tutti i cloud sul server VMM nella scheda **Elementi protetti** nell'insieme di credenziali.

![Cloud pubblicato][Cloud pubblicato]

1.  Nella pagina Avvio rapido fare clic su **Configurare la protezione per i cloud VMM**.
2.  Nella scheda **Protected Items** selezionare il cloud da configurare, quindi passare alla scheda **Configuration**. Si noti che:
3.  In **Destinazione** selezionare **VMM**.
4.  In **Percorso di destinazione** selezionare il server VMM locale che gestisce il cloud da usare per il ripristino.
5.  In **Cloud di destinazione** selezionare il cloud di destinazione da usare per il failover di macchine virtuali nel cloud di origine. Si noti che:

    -   È consigliabile selezionare un cloud di destinazione che soddisfi i requisiti di ripristino per le macchine virtuali da proteggere.
    -   Un cloud può appartenere solo a una singola coppia di cloud, come cloud primario o di destinazione.

6.  In **Frequenza di copia** lasciare l'impostazione predefinita. Questo valore consente di specificare la frequenza della sincronizzazione dei dati tra il percorso di origine e di destinazione. Risulta rilevante solo se l'host Hyper-V esegue Windows Server 2012 R2. Per gli altri server viene usata un'impostazione predefinita pari a cinque minuti.
7.  In **Punti di ripristino aggiuntivi** lasciare l'impostazione predefinita. Questo valore consente di specificare se si vogliono creare punti di ripristino aggiuntivi. Il valore predefinito zero specifica che solo il punto di ripristino più recente per una macchina virtuale primaria viene archiviato in un server host di replica.
8.  In **Frequenza degli snapshop coerenti con l'applicazione** lasciare l'impostazione predefinita. Questo valore consente di specificare la frequenza della creazione di snapshot. Gli snapshot usano il Servizio Copia Shadow del volume (VSS, Volume Shadow Copy Service) per assicurare che lo stato delle applicazioni sia coerente quando viene creato lo snapshot. Se si vuole impostare questo valore per la procedura dettagliata dell'esercitazione, verificare che sia impostato su un valore inferiore al numero di punti di ripristino aggiuntivi configurati.
    ![Configure protection settings][Configure protection settings]
9.  In **Compressione trasferimento dati** specificare se si vogliono comprimere i dati replicati trasferiti.
10. In **Authentication** specificare come viene autenticato il traffico tra i server host Hyper-V primario e di ripristino. Per questa procedura dettagliata, selezionare HTTPS a meno che non sia stato configurato un ambiente Kerberos funzionante. Azure Site Recovery configura automaticamente i certificati per l'autenticazione HTTPS. Non è necessaria alcuna configurazione manuale. Questa impostazione è rilevante solo per i server host Hyper-V in esecuzione su Windows Server 2012 R2.
11. In **Port** lasciare l'impostazione predefinita. Questo valore consente di impostare il numero di porta su cui i computer host Hyper-V di origine e di destinazione rimangono in attesa di traffico di replica.
12. In **Metodo di replica** specificare il modo in cui verrà gestita la replica iniziale dei dati dalla posizione di origine a quella di destinazione, prima che la replica normale venga avviata.

    -   **Sulla rete**: la copia di dati tramite la rete può richiedere molto tempo e numerose risorse. È consigliabile usare questa opzione se il cloud contiene macchine virtuali con dischi rigidi virtuali relativamente piccoli e se il server VMM primario è connesso al server VMM secondario su una connessione a banda larga. È possibile specificare che la copia deve iniziare immediatamente oppure selezionare un orario. Se si usa la replica tramite la rete, è consigliabile pianificarla durante le fasce orarie di minore attività.
    -   **Offline**: tramite questo metodo viene specificato che la replica iniziale sarà eseguita usando supporti esterni. Questa opzione è utile se si desidera evitare un calo delle prestazioni di rete oppure per percorsi remoti a livello geografico. Per usare questo metodo, è necessario specificare la posizione di esportazione nel cloud di origine e quella di importazione nel cloud di destinazione. Quando si abilita la protezione per una macchina virtuale, il disco rigido virtuale viene copiato nel percorso di esportazione specificato. È necessario inviarlo al sito di destinazione e copiarlo nel percorso di importazione. Il sistema copia le informazioni importate nelle macchine virtuali di replica. Per un elenco completo dei prerequisiti per la replica offline, vedere [Passaggio 3: Configurare le impostazioni di protezione per i cloud VMM][Passaggio 3: Configurare le impostazioni di protezione per i cloud VMM] nella Guida alla distribuzione.

13. Selezionare **Elimina macchina virtuale di replica** per specificare che la macchina virtuale di replica deve essere eliminata se si interrompe la protezione della macchina virtuale selezionando l'opzione **Disabilita protezione per la macchina virtuale** nella scheda Macchine virtuali del cloud. Se questa impostazione è abilitata, quando si disabilita la protezione, la macchina virtuale viene rimossa da Azure Site Recovery, vengono rimosse anche le impostazioni di questo servizio relative alle macchine virtuali nella console VMM e la replica viene eliminata.
    ![Configure protection settings][2]

Dopo avere salvato le impostazioni, verrà creato un processo che potrà essere monitorato nella scheda **Processi**. Tutti i server host Hyper-V nel cloud di origine VMM verranno configurati per la replica. È possibile modificare le impostazioni del cloud nella scheda **Configure**. Se si vuole modificare la posizione o il cloud di destinazione, è necessario rimuovere la configurazione del cloud e, successivamente, riconfigurare il cloud.

## <span id="networkmapping"></span></a>Passaggio 5: Configurare il mapping di rete

Questa esercitazione descrive il percorso più semplice per l'implementazione di Azure Site Recovery in un ambiente di test. Se si vuole configurare il mapping di rete nell'ambito di questa esercitazione, vedere [Preparare il mapping delle reti][Preparare il mapping delle reti] nella Guida alla pianificazione. Per configurare il mapping, attenersi alla procedura riportata in [Configurare il mapping di rete][Configurare il mapping di rete] nella guida alla distribuzione.

## <span id="storagemapping"></span></a>Passaggio 6: Configurare il mapping di archiviazione

Questa esercitazione descrive il percorso più semplice per l'implementazione di Azure Site Recovery in un ambiente di test. Se si vuole configurare il mapping di archiviazione nell'ambito di questa esercitazione, attenersi alla procedura riportata in [Configurare il mapping di archiviazione][Configurare il mapping di archiviazione] nella Guida alla pianificazione.

## <span id="enablevirtual"></span></a>Passaggio 7: Abilitare la protezione delle macchine virtuali

Dopo la configurazione corretta di server, cloud e reti, sarà possibile abilitare la protezione per le macchine virtuali nel cloud.

1.  Nella scheda **Macchine virtuali** nel cloud in cui si trova la macchina virtuale fare clic su **Abilita protezione** e selezionare **Aggiungi macchine virtuali**.
2.  Nell'elenco di macchine virtuali nel cloud selezionare quella da proteggere.

![Abilitare la protezione delle macchine virtuali][Abilitare la protezione delle macchine virtuali]

Tenere traccia dello stato di avanzamento dell'azione Abilita protezione nella scheda \*\*Processi\*\*, inclusa la replica iniziale. Dopo l'esecuzione del processo di finalizzazione della protezione la macchina virtuale è pronta per il failover. Al termine dell'operazione di abilitazione della protezione e di replica delle macchine virtuali, sarà possibile visualizzarle in Azure.

![Processo di protezione delle macchine virtuali][Processo di protezione delle macchine virtuali]

## <span id="recoveryplans"></span></a>Passaggio 8: testare la distribuzione

Per testare la distribuzione è possibile eseguire un failover di test per una singola macchina virtuale oppure creare un piano di ripristino costituito da più macchine virtuali ed eseguire un failover di test per il piano. Il failover di test consente di simulare il meccanismo di failover e di ripristino in una rete isolata.

-   Per istruzioni sulla creazione di un piano di ripristino, vedere [Creare e personalizzare piani di ripristino - Da sito locale ad Azure][Creare e personalizzare piani di ripristino - Da sito locale ad Azure].
-   Per istruzioni sull'esecuzione di un failover di test vedere [Testare una distribuzione da sito locale a sito locale][Testare una distribuzione da sito locale a sito locale].

### <span id="runtest"></span></a>Monitorare l'attività

La scheda **Processi** e il **Dashboard** consentono di visualizzare e monitorare i processi principali eseguiti dall'insieme di credenziali di Azure Site Recovery, tra cui la configurazione della protezione per un cloud, l'abilitazione e la disabilitazione della protezione per una macchina virtuale, l'esecuzione di un failover (pianificato, non pianificato o di test) e il commit di un failover non pianificato.

Nella scheda **Processi** è possibile visualizzare i processi, eseguire il drill-down di dettagli ed errori dei processi, eseguire query sui processi per recuperare i processi che soddisfano specifici criteri, esportare processi in Excel e riavviare i processi non riusciti.

Nel **Dashboard** è possibile scaricare le versioni più recenti dei file di installazione del provider e dell'agente, ottenere informazioni di configurazione per l'insieme di credenziali, visualizzare il numero di macchine virtuali la cui protezione è gestita dall'insieme di credenziali, visualizzare i processi recenti, gestire il certificato dell'insieme di credenziali e risincronizzare le macchine virtuali.

Per altre informazioni sull'interazione con i processi e il dashboard, vedere la [guida alle operazioni e al monitoraggio][guida alle operazioni e al monitoraggio].

## <span id="next"></span></a>Passaggi successivi

-   Per pianificare e distribuire Azure Site Recovery in un ambiente di produzione completo, vedere la [guida alla pianificazione per Azure Site Recovery][Pianificare la distribuzione di Azure Site Recovery] e la [guida alla distribuzione per Azure Site Recovery][Distribuire Azure Site Recovery: protezione da sito locale a sito locale].
-   In caso di domande, vedere il [forum relativo ai Servizi di ripristino di Azure][forum sui Servizi di ripristino di Azure].

</div>

  [Introduzione ad Azure Site Recovery: protezione da sito locale a sito locale]: http://go.microsoft.com/fwlink/?LinkId=398765
  [Introduzione ad Azure Site Recovery: protezione da sito locale ad Azure]: http://go.microsoft.com/fwlink/?LinkId=398764
  [Pianificare la distribuzione di Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=321294
  [Distribuire Azure Site Recovery: protezione da sito locale a sito locale]: http://go.microsoft.com/fwlink/?LinkId=321295
  [Azure Site Recovery: Scenari di errore comuni e soluzioni]: http://go.microsoft.com/fwlink/?LinkId=389879
  [forum sui Servizi di ripristino di Azure]: http://go.microsoft.com/fwlink/?LinkId=313628
  [versione di prova gratuita di Azure]: http://aka.ms/try-azure
  [informazioni dettagliate sui prezzi di Azure Site Recovery Manager]: http://go.microsoft.com/fwlink/?LinkId=378268
  [Passaggio 1: Creare un insieme di credenziali]: #vault
  [Passaggio 2: Installare l'applicazione del provider]: #download
  [Passaggio 3: Configurare la protezione cloud]: #clouds
  [Passaggio 6: Configurare il mapping di archiviazione]: #storagemapping
  [Passaggio 7: Abilitare la protezione per le macchine virtuali]: #enablevirtual
  [Passaggio 8: Configurare ed eseguire piani di ripristino]: #recovery%20plans
  [portale di gestione]: https://manage.windowsazure.com
  [Prezzi di Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=389880
  [New Vault]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [Quick Start Icon]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [Registration key]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2ERegisterKey.png
  [Prerequisiti]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png
  [Microsoft Updates]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png
  [Internet Settings]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [Server registration]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png
  [1]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png
  [Cloud pubblicato]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png
  [Configure protection settings]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2E.png
  [Passaggio 3: Configurare le impostazioni di protezione per i cloud VMM]: http://go.microsoft.com/fwlink/?LinkId=323469
  [2]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2ERep.png
  [Preparare il mapping delle reti]: http://go.microsoft.com/fwlink/?LinkId=324817
  [Configurare il mapping di rete]: http://go.microsoft.com/fwlink/?LinkId=402534
  [Configurare il mapping di archiviazione]: http://go.microsoft.com/fwlink/?LinkId=402535
  [Abilitare la protezione delle macchine virtuali]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png
  [Processo di protezione delle macchine virtuali]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
  [Creare e personalizzare piani di ripristino - Da sito locale ad Azure]: http://go.microsoft.com/fwlink/?LinkId=511492
  [Testare una distribuzione da sito locale a sito locale]: http://go.microsoft.com/fwlink/?LinkId=511493
  [guida alle operazioni e al monitoraggio]: http://go.microsoft.com/fwlink/?LinkId=398534
