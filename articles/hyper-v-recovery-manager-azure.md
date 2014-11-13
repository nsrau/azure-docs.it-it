<properties urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Introduzione ad Azure Site Recovery: protezione da sito locale ad Azure" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery coordina la replica, il failover e il ripristino in Azure di macchine virtuali Hyper-V ubicate in cloud VMM locali." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Introduzione ad Azure Site Recovery: protezione da sito locale ad Azure" editor="jimbe" manager="johndaw" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew" />

# Introduzione ad Azure Site Recovery: protezione da sito locale ad Azure

<div class="dev-callout">

Usare Azure Site Recovery per orchestrare la protezione per le macchine virtuali in server host Hyper-V locali ubicati in cloud VMM. È possibile configurare:

-   **Protezione da sito locale ad Azure**: è possibile replicare le macchine virtuali locali in Azure. Le impostazioni di protezione vengono configurate e abilitate negli insiemi di credenziali di Azure Site Recovery. I dati delle macchine virtuali vengono replicati da un server Hyper-V locale nell'archiviazione di Azure.
-   **Protezione da sito locale a sito locale**: replica di macchine virtuali locali in un altro sito locale. Le impostazioni di protezione vengono configurate e abilitate negli insiemi di credenziali di Azure Site Recovery. I dati delle macchine virtuali vengono replicati tra server Hyper-V locali. Altre informazioni su questo scenario sono disponibili in [Introduzione ad Azure Site Recovery: protezione da sito locale a sito locale][Introduzione ad Azure Site Recovery: protezione da sito locale a sito locale].

## <span id="about"></span></a>Informazioni sull'esercitazione

Usare questa esercitazione per configurare un modello di prova rapido per Azure Site Recovery in una distribuzione da sito locale ad Azure. Dove possibile, vengono usati il percorso più rapido e le impostazioni predefinite. Verranno effettuate le operazioni di creazione di un insieme di credenziali di Azure Site Recovery, installazione del provider di Azure Site Recovery nel server VMM di origine, installazione dell'agente di Servizi di ripristino di Azure in server host Hyper-V nei cloud VMM, configurazione delle impostazioni di protezione cloud, abilitazione della protezione per le macchine virtuali e test della distribuzione.

Per informazioni su una distribuzione completa, vedere:

-   [Pianificare la distribuzione di Azure Site Recovery][Pianificare la distribuzione di Azure Site Recovery], che descrive i passaggi di pianificazione necessari prima di avviare una distribuzione completa.
-   [Distribuire Azure Site Recovery: Protezione da sito locale ad Azure][Distribuire Azure Site Recovery: Protezione da sito locale ad Azure], in cui sono fornite istruzioni dettagliate per una distribuzione completa.

In caso di problemi durante questa esercitazione, vedere l'articolo wiki su [Azure Site Recovery: Scenari di errore comuni e soluzioni][Azure Site Recovery: Scenari di errore comuni e soluzioni] oppure inviare domande al [forum sui Servizi di ripristino di Azure][forum sui Servizi di ripristino di Azure].

</div>

## <span id="before"></span></a>Prima di iniziare

<div class="dev-callout">

Prima di iniziare l'esercitazione, verificare i prerequisiti seguenti.

### <span id="HVRMPrereq"></span></a>Prerequisiti

-   **Account Azure**: è necessario un account Azure. Se non si dispone di un account, vedere la pagina relativa alla [versione di prova gratuita di Azure][versione di prova gratuita di Azure]. Ottenere [informazioni dettagliate sui prezzi di Azure Site Recovery Manager][informazioni dettagliate sui prezzi di Azure Site Recovery Manager].
-   **Account di archiviazione di Azure**: per archiviare dati replicati in Azure è necessario un account di archiviazione di Azure. Nell'account deve essere abilitata la replica geografica. L'account deve trovarsi nella stessa area geografica in cui si trova il servizio Azure Site Recovery e deve essere associato alla stessa sottoscrizione. Per altre informazioni sulla configurazione dell'archiviazione di Azure, vedere [Introduzione ad Archiviazione di Microsoft Azure][Introduzione ad Archiviazione di Microsoft Azure].
-   **Server VMM**: un server VMM in esecuzione su System Center 2012 R2.
-   **Cloud VMM**: almeno un cloud sul server VMM server. Il cloud deve includere:
    -   Uno o più gruppi host VMM
    -   Uno o più cluster o server host Hyper-V in ogni gruppo host.
    -   Una o più macchine virtuali situate nel server Hyper-V di origine nel cloud. Le macchine virtuali devono essere di generazione 1.
-   **Macchine virtuali**: sono necessarie macchine virtuali conformi ai requisiti di Azure. Vedere [Prerequisiti e supporto][Prerequisiti e supporto] nella Guida alla pianificazione.
-   Per un elenco completo dei requisiti di supporto delle macchine virtuali per il failover in Azure, vedere

## <span id="tutorial"></span></a>Passaggi dell'esercitazione

Dopo la verifica dei prerequisiti, eseguire le operazioni seguenti:

-   [Passaggio 1: Creare un insieme di credenziali][Passaggio 1: Creare un insieme di credenziali]: creare un insieme di credenziali di Azure Site Recovery.
-   [Passaggio 2: Installare l'applicazione del provider][Passaggio 2: Installare l'applicazione del provider]: generare una chiave di registrazione ed eseguire l'applicazione Microsoft Azure Site Recovery Provider sul server VMM. In questo modo viene installato il provider e viene registrato il server VMM nell'insieme di credenziali.
-   [Passaggio 3: Aggiungere un account di archiviazione di Azure][Passaggio 3: Aggiungere un account di archiviazione di Azure]: se non si dispone di un account, crearne uno.
-   [Passaggio 4: Installare l'applicazione dell'agente][Passaggio 4: Installare l'applicazione dell'agente]: installare l'agente di Servizi di ripristino di Microsoft Azure in ciascun host Hyper-V.
-   [Passaggio 5: Configurare la protezione cloud][Passaggio 5: Configurare la protezione cloud]: configurare le impostazioni di protezione per i cloud VMM.
-   [Passaggio 6: Configurare il mapping di rete][Passaggio 6: Configurare il mapping di rete]: facoltativamente, è possibile configurare il mapping di rete per mappare le reti VM di origine a quelle di destinazione.
-   [Passaggio 7: Abilitare la protezione per le macchine virtuali][Passaggio 7: Abilitare la protezione per le macchine virtuali]: abilitare la protezione per le macchine virtuali presenti nei cloud VMM protetti.
-   [Passaggio 8: Testare la distribuzione][Passaggio 8: Testare la distribuzione]: è possibile eseguire un failover di test per una singola macchina virtuale oppure creare un piano di ripristino ed eseguire un failover di test per il piano.

<a name="vault"></a>

## Passaggio 1: Creare un insieme di credenziali

</p>
1.  Accedere al [portale di gestione][portale di gestione].

2.  Espandere **Servizi dati**, espandere **Servizi di ripristino** e fare clic su **Insieme di credenziali di Azure Site Recovery**.

3.  Fare clic su **Crea nuovo** e quindi su **Creazione rapida**.

4.  In **Name** immettere un nome descrittivo per identificare l'insieme di credenziali.

5.  In **Region** selezionare l'area geografica per l'insieme di credenziali. Le aree geografiche disponibili includono Asia orientale, Europa occidentale, Stati Uniti occidentali, Stati Uniti orientali, Europa settentrionale e Asia sudorientale.
6.  Fare clic su **Crea insieme di credenziali**.

    ![New Vault][New Vault]

Controllare la barra di stato per verificare che l'insieme di credenziali sia stato creato correttamente. L'insieme di credenziali verrà elencato come **Attivo** nella pagina principale di Servizi di ripristino.

<a name="download"></a>

## Passaggio 2: Generare un codice di registrazione e installare il provider di Azure Site Recovery

</p>
1.  Nella pagina **Servizi di ripristino** fare clic sull'insieme di credenziali per aprire la pagina Avvio rapido. La pagina Avvio rapido può essere anche aperta in qualsiasi momento tramite l'icona.

    ![Quick Start Icon][Quick Start Icon]

2.  Nell'elenco a discesa selezionare **Tra un sito Hyper-V locale e Microsoft Azure**.
3.  In **Preparare i server VMM** fare clic sul file **Genera file del codice di registrazione**. Il codice è valido per 5 giorni dal momento in cui viene generato. Copiare il file nel server VMM. Il file sarà necessario quando si configura il provider.

    ![Registration key][Registration key]

4.  Nella pagina **Avvio rapido**, in **Preparare i server VMM**, fare clic su **Scaricare il provider di Microsoft Azure Site Recovery e installarlo nei server VMM** per ottenere la versione più recente del file di installazione del provider.

5.  Eseguire il file nel server VMM di origine.

6.  In **Verifica prerequisiti** scegliere di arrestare il servizio VMM per avviare il programma di installazione del provider. Il servizio verrà arrestato e riavviato automaticamente al termine dell'installazione.

    ![Prerequisiti][Prerequisiti]

7.  In **Microsoft Update** è possibile acconsentire esplicitamente a ricevere gli aggiornamenti. Se questa impostazione è abilitata, gli aggiornamenti del provider verranno installati in base ai criteri indicati in Microsoft Update.

    ![Microsoft Updates][Microsoft Updates]

Dopo l'installazione del provider, continuare con il programma di installazione per registrare il server nell'insieme di credenziali.

1.  Nella pagina **Connessione Internet** specificare la modalità di connessione Internet del provider in esecuzione sul server VMM. Selezionare **Usa impostazioni proxy del sistema predefinite** per usare le impostazioni di connessione a Internet predefinite configurate nel server.

    ![Internet Settings][Internet Settings]

2.  Nella pagina **Codice di registrazione** selezionare l'opzione relativa al download da Azure Site Recovery e alla copia nel server VMM.
3.  In **Nome insieme di credenziali** verificare il nome dell'insieme di credenziali in cui verrà registrato il server.
4.  In **Nome server** specificare un nome descrittivo per identificare il server VMM nell'insieme di credenziali.

    ![Server registration][Server registration]

5.  In **Sincronizzazione iniziale metadati** scegliere se si vogliono sincronizzare i metadati per tutti i cloud nel server VMM con l'insieme di credenziali. È necessario eseguire questa azione solo una volta in ogni server. Se non si vogliono sincronizzare tutti i cloud, è possibile lasciare deselezionata questa opzione e sincronizzare ogni cloud singolarmente nelle proprietà del cloud nella console VMM.

6.  In **Crittografia dati** specificare un percorso in cui salvare un certificato SSL generato automaticamente per la crittografia dei dati. Questo certificato viene usato se si abilita la crittografia dei dati per un cloud protetto da Azure nel portale di Azure Site Recovery. Conservare il certificato in una posizione sicura, perché verrà selezionato per effettuare la decrittografia dei dati crittografati quando si effettua un failover in Azure.
    Questa opzione non è importante se si esegue la replica da un sito locale a un altro sito locale.

    ![Server registration][1]

7.  Fare clic su **Register** per completare il processo. Dopo la registrazione, i metadati del server VMM vengono recuperati da Azure Site Recovery. Il server viene visualizzato nella scheda **Risorse** della pagina **Server** nell'insieme di credenziali.

## <span id="storage"></span></a>Passaggio 3: Creare un account di archiviazione di Azure

Se non si dispone di un account di archiviazione di Azure, fare clic su **Aggiungi un account di archiviazione di Azure**. Nell'account deve essere abilitata la replica geografica. L'account deve trovarsi nella stessa area geografica del servizio Azure Site Recovery e deve essere associato alla stessa sottoscrizione.

Usare questa esercitazione per configurare un modello di prova rapido per Azure Site Recovery in una distribuzione da sito locale ad Azure. Dove possibile, vengono usati il percorso più rapido e le impostazioni predefinite. Verranno effettuate le operazioni di creazione di un insieme di credenziali di Azure Site Recovery, installazione del provider di Azure Site Recovery nel server VMM di origine, installazione dell'agente di Servizi di ripristino di Azure in server host Hyper-V nei cloud VMM, configurazione delle impostazioni di protezione cloud, abilitazione della protezione per le macchine virtuali e test della distribuzione.

![Account di archiviazione][Account di archiviazione]

## <span id="agent"></span></a>Passaggio 4: Installare l'agente di Servizi di ripristino di Azure in host Hyper-V

Installare l'agente di Servizi di ripristino di Azure su ogni server host Hyper-V presente nei cloud VMM da proteggere.

1.  Nella pagina Avvio rapido fare clic su **Scarica agente Servizi di ripristino di Azure e installa negli host** per ottenere la versione più recente dei file di installazione dell'agente.

    ![Installare l'agente di Servizi di ripristino][Installare l'agente di Servizi di ripristino]

2.  Eseguire il file di installazione su ogni server host Hyper-V presente nei cloud VMM da proteggere.
3.  Nella pagina **Controllo dei prerequisiti** fare clic su **Avanti**. Gli eventuali prerequisiti mancanti verranno installati automaticamente.

    ![Prerequisiti per l'agente di Servizi di ripristino di Azure][Prerequisiti per l'agente di Servizi di ripristino di Azure]

4.  Nella pagina **Impostazioni di installazione** specificare dove si vuole installare l'agente e selezionare il percorso della cache in cui verranno installati i metadati di backup. Fare clic su **Installa**.

## <span id="clouds"></span></a>Passaggio 5: Configurare le impostazioni di protezione del cloud

Dopo la registrazione dei server VMM, sarà possibile configurare le impostazioni di protezione del cloud. L'opzione **Sincronizza dati cloud con insieme credenziali** è stata abilitata in fase di installazione del provider per visualizzare tutti i cloud sul server VMM nella scheda **Elementi protetti** nell'insieme di credenziali.

![Cloud pubblicato][Cloud pubblicato]

1.  Nella pagina Avvio rapido fare clic su **Configurare la protezione per i cloud VMM**.

2.  Nella scheda **Elementi protetti** fare clic sul cloud da configurare e passare alla scheda **Configurazione**.
3.  In **Destinazione** selezionare **Microsoft Azure**.
4.  In **Account di archiviazione** selezionare l'archiviazione di Azure che si vuole usare per archiviare le macchine virtuali di Azure.
5.  Impostare **Crittografa dati archiviati** su **Disattivato**. Questa impostazione specifica che i dati devono essere replicati crittografati tra il sito locale e Azure.
6.  In **Frequenza di copia** lasciare l'impostazione predefinita. Questo valore consente di specificare la frequenza della sincronizzazione dei dati tra il percorso di origine e di destinazione.
7.  In **Mantieni punti di ripristino per** lasciare l'impostazione predefinita. Il valore predefinito zero indica che solo il punto di ripristino più recente per una macchina virtuale primaria viene archiviato in un server host di replica.
8.  In **Frequenza degli snapshop coerenti con l'applicazione** lasciare l'impostazione predefinita. Questo valore consente di specificare la frequenza della creazione di snapshot. Gli snapshot usano il Servizio Copia Shadow del volume (VSS, Volume Shadow Copy Service) per assicurare che lo stato delle applicazioni sia coerente quando viene creato lo snapshot. Se si imposta un valore, questo deve essere inferiore al numero di punti di ripristino aggiuntivi configurati.
9.  In **Ora inizio replica** specificare quando deve essere avviata la replica iniziale dei dati in Azure. Verrà usato il fuso orario del server host Hyper-V. È consigliabile pianificare la replica iniziale durante le fasce orarie di minore attività.

    ![Impostazioni della replica cloud][Impostazioni della replica cloud]

Dopo avere salvato le impostazioni, verrà creato un processo che potrà essere monitorato nella scheda **Processi**. Tutti i server host Hyper-V nel cloud di origine VMM verranno configurati per la replica. Dopo il salvataggio, le impostazioni cloud possono essere modificate nella scheda **Configura**. Per modificare il percorso o la risorsa di archiviazione di destinazione, è necessario rimuovere la configurazione del cloud e quindi riconfigurare il cloud. Si noti che se si cambia l'account di archiviazione, la modifica viene applicata alle macchine virtuali abilitate per la protezione solo dopo la modifica dell'account di archiviazione. Non viene eseguita la migrazione delle macchine virtuali esistenti al nuovo account di archiviazione.

## <span id="networkmapping"></span></a>Passaggio 6: Configurare il mapping di rete

Questa esercitazione descrive il percorso più semplice per l'implementazione di Azure Site Recovery in un ambiente di test. Se si vuole configurare il mapping di rete nell'ambito di questa esercitazione, vedere [Preparare il mapping delle reti][Preparare il mapping delle reti] nella Guida alla pianificazione. Per configurare il mapping, attenersi alla procedura riportata in [Configurare il mapping di rete][Configurare il mapping di rete] nella guida alla distribuzione.

## <span id="virtualmachines"></span></a>Passaggio 7: Abilitare la protezione per le macchine virtuali

Dopo la configurazione corretta di server, cloud e reti, sarà possibile abilitare la protezione per le macchine virtuali nel cloud. Tenere presente quanto segue:

-   Le macchine virtuali devono essere conformi ai requisiti di Azure. Vedere tali requisiti in [Prerequisiti e supporto][Prerequisiti e supporto] nella Guida alla pianificazione.
-   Per abilitare la protezione, è necessario che le proprietà del sistema operativo e del disco del sistema operativo siano impostate per la macchina virtuale. Quando si crea una macchina virtuale basata su un modello di macchina virtuale VMM è possibile impostare la proprietà. È inoltre possibile impostare queste proprietà per le macchine virtuali esistenti nelle schede \*\*Generale\*\* e \*\*Configurazione hardware\*\* delle proprietà delle macchine virtuali. Se queste proprietà non vengono impostate in VMM, sarà possibile configurarle nel portale di Azure Site Recovery.

![Create virtual machine][Create virtual machine]

![Modificare le proprietà della macchina virtuale][Modificare le proprietà della macchina virtuale]

1.  Per abilitare la protezione, nella scheda **Macchine virtuali** del cloud in cui si trova la macchina virtuale fare clic su **Abilita protezione** e selezionare **Aggiungi macchine virtuali**.
2.  Nell'elenco di macchine virtuali nel cloud selezionare quella da proteggere.

    ![Abilitare la protezione delle macchine virtuali][Abilitare la protezione delle macchine virtuali]

3.  Verificare le proprietà della macchina virtuale e modificarle in base alle esigenze.

    ![Verify virtual machines][Verify virtual machines]

Tenere traccia dello stato di avanzamento dell'azione Abilita protezione nella scheda \*\*Processi\*\*, inclusa la replica iniziale. Dopo l'esecuzione del processo di finalizzazione della protezione la macchina virtuale è pronta per il failover. Al termine dell'operazione di abilitazione della protezione e di replica delle macchine virtuali, sarà possibile visualizzarle in Azure.

![Processo di protezione delle macchine virtuali][Processo di protezione delle macchine virtuali]

## <span id="test"></span></a>Passaggio 8: testare la distribuzione

Per testare la distribuzione è possibile eseguire un failover di test per una singola macchina virtuale oppure creare un piano di ripristino costituito da più macchine virtuali ed eseguire un failover di test per il piano. Il failover di test consente di simulare il meccanismo di failover e di ripristino in una rete isolata. Tenere presente quanto segue:

-   Per eseguire la connessione alla macchina virtuale in Azure tramite Desktop remoto dopo il failover, abilitare Connessione Desktop remoto sulla macchina virtuale prima di eseguire il failover di test.
-   Dopo il failover si userà un indirizzo IP pubblico per connettersi alla macchina virtuale in Azure tramite Desktop remoto. Se si vuole procedere in questo senso, assicurarsi che non siano presenti criteri di dominio che impediscono la connessione a una macchina virtuale mediante un indirizzo pubblico.

-   Per istruzioni sulla creazione di un piano di ripristino, vedere [Creare e personalizzare piani di ripristino - Da sito locale ad Azure][Creare e personalizzare piani di ripristino - Da sito locale ad Azure].
-   Per istruzioni sull'esecuzione di un failover di test vedere [Testare una distribuzione da sito locale a sito locale][Testare una distribuzione da sito locale a sito locale].

</p>
### <span id="runtest"></span></a>Monitorare l'attività

La scheda **Processi** e il **Dashboard** consentono di visualizzare e monitorare i processi principali eseguiti dall'insieme di credenziali di Azure Site Recovery, tra cui la configurazione della protezione per un cloud, l'abilitazione e la disabilitazione della protezione per una macchina virtuale, l'esecuzione di un failover (pianificato, non pianificato o di test) e il commit di un failover non pianificato.

Nella scheda **Processi** è possibile visualizzare i processi, eseguire il drill-down di dettagli ed errori dei processi, eseguire query sui processi per recuperare i processi che soddisfano specifici criteri, esportare processi in Excel e riavviare i processi non riusciti.

Nel **Dashboard** è possibile scaricare le versioni più recenti dei file di installazione del provider e dell'agente, ottenere informazioni di configurazione per l'insieme di credenziali, visualizzare il numero di macchine virtuali la cui protezione è gestita dall'insieme di credenziali, visualizzare i processi recenti, gestire il certificato dell'insieme di credenziali e risincronizzare le macchine virtuali.

Per altre informazioni sull'interazione con i processi e il dashboard, vedere la [guida alle operazioni e al monitoraggio][guida alle operazioni e al monitoraggio].

## <span id="next"></span></a>Passaggi successivi

-   Per pianificare e distribuire Azure Site Recovery in un ambiente di produzione completo, vedere la [guida alla pianificazione per Azure Site Recovery][Pianificare la distribuzione di Azure Site Recovery] e la [guida alla distribuzione per Azure Site Recovery][guida alla distribuzione per Azure Site Recovery].
-   In caso di domande, vedere il [forum relativo ai Servizi di ripristino di Azure][forum sui Servizi di ripristino di Azure].

</div>

  [Introduzione ad Azure Site Recovery: protezione da sito locale a sito locale]: http://go.microsoft.com/fwlink/?LinkId=398765
  [Pianificare la distribuzione di Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=321294
  [Distribuire Azure Site Recovery: Protezione da sito locale ad Azure]: http://go.microsoft.com/fwlink/?LinkId=402679
  [Azure Site Recovery: Scenari di errore comuni e soluzioni]: http://go.microsoft.com/fwlink/?LinkId=389879
  [forum sui Servizi di ripristino di Azure]: http://go.microsoft.com/fwlink/?LinkId=313628
  [versione di prova gratuita di Azure]: http://aka.ms/try-azure
  [informazioni dettagliate sui prezzi di Azure Site Recovery Manager]: http://go.microsoft.com/fwlink/?LinkId=378268
  [Introduzione ad Archiviazione di Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=398704
  [Prerequisiti e supporto]: http://go.microsoft.com/fwlink/?LinkId=402602
  [Passaggio 1: Creare un insieme di credenziali]: #vault
  [Passaggio 2: Installare l'applicazione del provider]: #download
  [Passaggio 3: Aggiungere un account di archiviazione di Azure]: #storage
  [Passaggio 4: Installare l'applicazione dell'agente]: #agent
  [Passaggio 5: Configurare la protezione cloud]: #clouds
  [Passaggio 6: Configurare il mapping di rete]: #NetworkMapping
  [Passaggio 7: Abilitare la protezione per le macchine virtuali]: #virtualmachines
  [Passaggio 8: Testare la distribuzione]: #test
  [portale di gestione]: https://manage.windowsazure.com
  [New Vault]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [Quick Start Icon]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [Registration key]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2ARegisterKey.png
  [Prerequisiti]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png
  [Microsoft Updates]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png
  [Internet Settings]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [Server registration]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png
  [1]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png
  [Account di archiviazione]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2AStorageAgent.png
  [Installare l'agente di Servizi di ripristino]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2AInstallHyperVAgent.png
  [Prerequisiti per l'agente di Servizi di ripristino di Azure]: ./media/hyper-v-recovery-manager-configure-vault/SR_AgentPrereqs.png
  [Cloud pubblicato]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png
  [Impostazioni della replica cloud]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2A.png
  [Preparare il mapping delle reti]: http://go.microsoft.com/fwlink/?LinkId=324817
  [Configurare il mapping di rete]: http://go.microsoft.com/fwlink/?LinkId=402533
  [Create virtual machine]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ANew.png
  [Modificare le proprietà della macchina virtuale]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2AExisting.png
  [Abilitare la protezione delle macchine virtuali]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ASelectVM.png
  [Verify virtual machines]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVME2AProps.png
  [Processo di protezione delle macchine virtuali]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
  [Creare e personalizzare piani di ripristino - Da sito locale ad Azure]: http://go.microsoft.com/fwlink/?LinkId=511492
  [Testare una distribuzione da sito locale a sito locale]: http://go.microsoft.com/fwlink/?LinkId=511494
  [guida alle operazioni e al monitoraggio]: http://go.microsoft.com/fwlink/?LinkId=398534
  [guida alla distribuzione per Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=321295
