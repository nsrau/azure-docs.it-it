<properties linkid="configure-hyper-v-recovery-vault" urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery coordinates the replication, failover and recovery of Hyper-V virtual machines located in on-premises VMM clouds to another on-premises site." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection" editor="jimbe" manager="cfreeman" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew"></tags>

# Introduzione ad Azure Site Recovery: protezione da sito locale a sito locale

<div class="dev-callout">

Usare Azure Site Recovery per gestire la protezione delle macchine virtuali su server host Hyper-V locali presenti in cloud VMM. È possibile configurare i seguenti elementi:

-   **Protezione da sito locale a sito locale**: le macchine virtuali locali vengono replicate in un altro sito locale. Configurare e abilitare le impostazioni di protezione negli insiemi di credenziali di Azure Site Recovery. I dati delle macchine virtuali vengono replicati da un server Hyper-V locale a un altro. Per altre informazioni su questo scenario, vedere [Introduzione ad Azure Site Recovery: protezione da sito locale a sito locale][]
-   **Protezione da sito locale ad Azure**: le macchine virtuali locali vengono replicate in Azure. Configurare e abilitare le impostazioni di protezione negli insiemi di credenziali di Azure Site Recovery. I dati delle macchine virtuali vengono replicati da un server Hyper-V locale a una risorsa di archiviazione di Azure. Per altre informazioni su questo scenario, vedere [Introduzione ad Azure Site Recovery: protezione da sito locale ad Azure][]

## <span id="about"></span></a>Informazioni sull'esercitazione

Usare questa esercitazione per impostare un modello di prova rapido per Azure Site Recovery in una distribuzione locale di Azure. Quando possibile, vengono usati il percorso più veloce e le impostazioni predefinite. Verranno eseguite le seguenti attività: creazione di un insieme di credenziali di Azure Site Recovery, installazione del provider di Azure Site Recovery nel server VMM di origine, configurazione delle impostazioni di protezione del cloud, abilitazione delle macchine virtuali e test della distribuzione.

Per altre informazioni su una distribuzione completa, vedere:

-   [Pianificare la distribuzione di Azure Site Recovery][]: descrive i passaggi di pianificazione che è necessario eseguire prima di avviare una distribuzione completa.
-   [Distribuire Azure Site Recovery: protezione da sito locale a sito locale][]: fornisce istruzioni dettagliate per una distribuzione completa.

In caso di problemi nel corso dell'esercitazione, vedere l'articolo di Wiki relativo ad [Azure Site Recovery: scenari di errore comuni e soluzioni][] oppure inviare le proprie domande al [forum sui servizi di ripristino di Azure][].

</div>

## <span id="before"></span></a>Prima di iniziare

<div class="dev-callout">

Prima di iniziare l'esercitazione, verificare i prerequisiti seguenti.

### <span id="HVRMPrereq"></span></a>Prerequisiti

-   **Account di Azure**: è necessario disporre di un account di Azure. Se non si dispone di un account, vedere la pagina [Versione di valutazione gratuita per un mese][]. Per informazioni sui prezzi, consultare la pagina relativa ai [dettagli sui prezzi per Azure Site Recovery][].
-   **Server VMM**: è necessario disporre di almeno un server VMM in esecuzione su System Center 2012 SP1 o System Center 2012 R2.
-   **Cloud VMM**: è necessario disporre di almeno un cloud sul server VMM di origine che si desidera proteggere e uno sul server VMM di destinazione. Se è in esecuzione un server VMM, saranno necessari due cloud. Il cloud primario da proteggere deve includere quanto segue:
    -   Uno o più gruppi host VMM
    -   Uno o più cluster o server host Hyper-V in ogni gruppo host.
    -   Una o più macchine virtuali presenti sul server Hyper-V di origine nel cloud.

## <span id="tutorial"></span></a>Passaggi dell'esercitazione

Dopo la verifica dei prerequisiti, eseguire le operazioni seguenti:

-   [Passaggio 1: Creare un insieme di credenziali][]: creare un insieme di credenziali di Azure Site Recovery.
-   [Passaggio 2: Installare l'applicazione del provider][]: generare una chiave di registrazione ed eseguire l'applicazione Microsoft Azure Site Recovery Provider sul server VMM. Questa operazione installa il provider e registra il server VMM nell'insieme di credenziali.
-   [Passaggio 3: Configurare la protezione cloud][]: configurare le impostazioni di protezione per i cloud VMM.
-   <a href="#networkmapping">Passaggio 5: Configurare il mapping di rete: facoltativamente, è possibile configurare il mapping di rete per mappare le reti VM di origine a quelle di destinazione.
-   [Passaggio 6: Configurare il mapping di archiviazione][]: facoltativamente, è possibile configurare il mapping di archiviazione per mappare le classificazioni di archiviazione presenti sul server VMM di origine a quelle presenti sul server di destinazione.
-   [Passaggio 7: Abilitare la protezione per le macchine virtuali][]: abilitare la protezione per le macchine virtuali presenti in cloud VMM protetti.
-   [Passaggio 8: Configurare ed eseguire piani di ripristino][]: creare un piano di ripristino ed eseguire un failover di test per tale piano.

<a name="vault"></a>

## Passaggio 1: Creare un insieme di credenziali

</p>
1.  Accedere al [portale di gestione][].

2.  Espandere **Data Services**, quindi **Servizi di ripristino** e fare clic su **Site Recovery Vault**.

3.  Fare clic su **Crea nuovo**, quindi su **Creazione rapida**.

4.  In **Nome** immettere un nome descrittivo per identificare l'insieme di credenziali.

5.  In **Area** selezionare l'area geografica per l'insieme di credenziali. Per informazioni sulla disponibilità a livello geografico e sulle aree supportate, vedere la pagina relativa ai [dettagli sui prezzi per Azure Site Recovery][1].

6.  Fare clic su **Crea insieme di credenziali**.

    ![Nuovo insieme di credenziali][]

Controllare la barra di stato per verificare che l'insieme di credenziali sia stato creato correttamente. L'insieme di credenziali verrà indicato come **Attivo** nella pagina principale di Servizi di ripristino.

<a name="upload"></a>

## Passaggio 2: Configurare l'insieme di credenziali

</p>
1.  Nella pagina **Servizi di ripristino** fare clic sull'insieme di credenziali per aprire la pagina Avvio rapido. È possibile aprire la pagina Avvio rapido in qualsiasi momento usando l'icona corrispondente.

    ![Icona Avvio rapido][]

2.  Dall'elenco a discesa selezionare **Between two on-premises Hyper-V sites**.
3.  In **Prepare VMM Servers** fare clic sul file **Generate registration key**. Una volta generata, la chiave rimane valida per 5 giorni. Copiare il file sul server VMM. Questo file sarà necessario al momento della generazione del provider.

    ![Chiave di registrazione][]

<a name="download"></a>

## Passaggio 3: Installare Azure Site Recovery Provider

</p>
1.  Nella pagina **Avvio rapido**, in **Prepare VMM servers**, fare clic su **Download Microsoft Azure Site Recovery Provider for installation on VMM servers** per ottenere la versione più recente del file di installazione del provider.

2.  Eseguire questo file sul server VMM di origine e su quello di destinazione.

3.  In **Pre-requirements Check** selezionare l'opzione per l'arresto del servizio VMM per iniziare la configurazione del provider. Il servizio si arresta e verrà riavviato automaticamente al termine della configurazione.

    ![Prerequisiti][]

4.  In **Microsoft Update** è possibile fornire il consenso esplicito agli aggiornamenti. Con questa impostazione abilitata, gli aggiornamenti del provider verranno installati in base al criterio Microsoft Update dell'utente.

    ![Aggiornamenti Microsoft][]

Una volta installato il provider, continuare la configurazione per registrare il server nell'insieme di credenziali.

1.  Nella pagina Internet Connection specificare la modalità di connessione a Internet del provider in esecuzione nel server VMM. Selezionare **Use default system proxy settings** per usare le impostazioni predefinite di connessione a Internet configurate sul server.

    ![Impostazioni Internet][]

2.  In **Registration Key** selezionare il codice di registrazione scaricato da Azure Site Recovery e copiato nel server VMM.
3.  In **Vault name** verificare il nome dell'insieme di credenziali in cui verrà registrato il server.
4.  In **Nome server** specificare un nome descrittivo per identificare il server VMM nell'insieme di credenziali.

    ![Registrazione del server][]

5.  In **Initial cloud metadata** selezionare l'opzione relativa alla sincronizzazione dei metadati per tutti i cloud presenti sul server VMM con l'insieme di credenziali. È necessario eseguire questa azione solo una volta in ogni server. Se non si desidera sincronizzare tutti i cloud, è possibile lasciare deselezionata questa opzione e sincronizzare singolarmente ciascun cloud nelle proprietà del cloud della console VMM.

6.  In **Data Encryption** è possibile generare un certificato da usare per crittografare i dati protetti in Azure.
    Se si sta eseguendo la replica da sito locale a sito locale, questa opzione non è rilevante.

    ![Registrazione del server][2]

7.  Fare clic su **Register** per completare il processo. Dopo la registrazione, i metadati del server VMM vengono recuperati da Azure Site Recovery. Il server viene visualizzato nella parte finale della scheda **Resources** della pagina **Servers** dell'insieme di credenziali.

## <span id="clouds"></span></a>Passaggio 4: Configurare le impostazioni di protezione del cloud

Dopo la registrazione dei server VMM, sarà possibile configurare le impostazioni di protezione del cloud. Quando si installa il provider, abilitare l'opzione **Synchronize cloud data with the vault**, in modo che tutti i cloud presenti sul server VMM vengano visualizzati nella scheda **Elementi protetti** dell'insieme di credenziali.

![Cloud pubblicato][]

1.  Nella pagina Avvio rapido fare clic su **Set up protection for VMM clouds**.
2.  Nella scheda **Elementi protetti** selezionare il cloud da configurare, quindi passare alla scheda **Configurazione**. Si noti che:
3.  In **Destinazione** selezionare **VMM**.
4.  In **Percorso di destinazione** selezionare il server VMM locale che gestisce il cloud da usare per il ripristino.
5.  In **Cloud di destinazione** selezionare il cloud di destinazione da usare per il failover di macchine virtuali nel cloud di origine. Si noti che:

    -   È consigliabile selezionare un cloud di destinazione che soddisfi i requisiti di ripristino per le macchine virtuali da proteggere.
    -   Un cloud può appartenere solo a una singola coppia di cloud, come cloud primario o di destinazione.

6.  In **Frequenza di copia** lasciare l'impostazione predefinita. Questo valore consente di specificare la frequenza della sincronizzazione dei dati tra il percorso di origine e di destinazione. Risulta rilevante solo se l'host Hyper-V esegue Windows Server 2012 R2. Per gli altri server viene usata un'impostazione predefinita pari a cinque minuti.
7.  In **Punti di ripristino aggiuntivi** lasciare l'impostazione predefinita. Questo valore consente di specificare se si desidera creare punti di ripristino aggiuntivi. Il valore predefinito zero indica che solo il punto di ripristino più recente per una macchina virtuale primaria viene archiviato in un server host di replica.
8.  In **Frequenza dell'applicazione - Snapshot coerenti** lasciare l'impostazione predefinita. Questo valore consente di specificare la frequenza della creazione di snapshot. Gli snapshot usano il Servizio Copia Shadow del volume (VSS, Volume Shadow Copy Service) per assicurare che lo stato delle applicazioni sia coerente quando viene creato lo snapshot. Se si desidera impostare questo valore per la procedura dettagliata dell'esercitazione, verificare che sia impostato su un valore inferiore al numero di punti di ripristino aggiuntivi configurati.
    ![Configurazione delle impostazioni di protezione][]
9.  In **Compressione trasferimento dati** specificare se si desidera comprimere i dati replicati trasferiti.
10. In **Autenticazione** specificare come viene autenticato il traffico tra i server host Hyper-V primario e di ripristino. Ai fini di questa procedura dettagliata, selezionare HTTPS, a meno che non sia stato configurato un ambiente Kerberos funzionante. Azure Site Recovery configurerà automaticamente i certificati per l'autenticazione HTTPS. Non è necessaria alcuna configurazione manuale. Questa impostazione è rilevante solo per i server host Hyper-V in esecuzione su Windows Server 2012 R2.
11. In **Porta** lasciare l'impostazione predefinita. Questo valore consente di impostare il numero di porta su cui i computer host Hyper-V di origine e di destinazione rimangono in attesa del traffico di replica.
12. In **Metodo di replica** specificare il modo in cui verrà gestita la replica iniziale di dati dal percorso di origine al percorso di destinazione, prima dell'inizio della replica normale.

    -   **Over network**: la copia di dati tramite rete può richiedere molto tempo e molte risorse. È consigliabile usare questa opzione se il cloud contiene macchine virtuali con dischi rigidi virtuali relativamente piccoli e se il server VMM primario è connesso al server VMM secondario su una connessione a banda larga. È possibile specificare che la copia deve iniziare immediatamente oppure selezionare un orario. Se si usa la replica tramite la rete, è consigliabile pianificarla durante le fasce orarie di minore attività.
    -   **Offline**: questo metodo specifica che la replica iniziale sarà eseguita usando supporti esterni. Questa opzione è utile se si desidera evitare un calo delle prestazioni di rete oppure per percorsi remoti a livello geografico. Per usare questo metodo, è necessario specificare la posizione di esportazione nel cloud di origine e quella di importazione nel cloud di destinazione. Quando si abilita la protezione per una macchina virtuale, il disco rigido virtuale viene copiato nel percorso di esportazione specificato. È necessario inviarlo al sito di destinazione e copiarlo nel percorso di importazione. Il sistema copia le informazioni importate nelle macchine virtuali di replica. Per un elenco completo dei prerequisiti per la replica offline, vedere [Passaggio 3: Configurare le impostazioni di protezione per i cloud VMM][] nella Guida alla distribuzione.

13. Selezionare **Delete Replica Virtual Machine** per specificare che la replica della macchina virtuale deve essere eliminata se si interrompe la protezione della macchina virtuale stessa selezionando l'opzione **Delete protection for the virtual machine** sulla scheda delle proprietà del cloud relativa alle macchine virtuali. Con questa impostazione abilitata, quando si disabilita la protezione la macchina virtuale viene rimossa da Azure Site Recovery, le impostazioni di Site Recovery relative alla macchina virtuale vengono rimosse dalla console VMM e la replica viene eliminata.
    ![Configurazione delle impostazioni di protezione][3]

Una volta salvate le impostazioni, verrà creato un processo che potrà essere monitorato nella scheda **Processi**. Tutti i server host Hyper-V presenti nel cloud di origine VMM verranno configurati per la replica. È possibile modificare le impostazioni del cloud nella scheda **Configura**. Se si desidera modificare il percorso o il cloud di destinazione sarà necessario rimuovere la configurazione del cloud e quindi riconfigurare il cloud.

## <span id="networkmapping"></span></a>Passaggio 5: Configurare il mapping di rete

Questa esercitazione descrive la procedura più semplice per distribuire Azure Site Recovery in un ambiente di test. Se si desidera configurare il mapping di rete come parte dell'esercitazione, vedere [Passaggio 5: Configurare il mapping di archiviazione - Da sito locale a sito locale][] nella Guida alla pianificazione. Per ulteriori informazioni sul mapping di rete, vedere [Passaggio 4: Configurare il mapping delle reti - Da sito locale a sito locale][] nella Guida alla pianificazione.

## <span id="storagemapping"></span></a>Passaggio 6: Configurare il mapping di archiviazione

Questa esercitazione descrive la procedura più semplice per distribuire Azure Site Recovery in un ambiente di test. Se si desidera configurare il mapping di configurazione come parte dell'esercitazione, vedere [Passaggio 5: Configurare il mapping di archiviazione - Da sito locale a sito locale][4] nella Guida alla pianificazione.

## <span id="enablevirtual"></span></a>Passaggio 7: Abilitare la protezione per le macchine virtuali

Dopo la configurazione corretta di server, cloud e reti, sarà possibile abilitare la protezione per le macchine virtuali nel cloud.

1.  Nella scheda **Virtual Machines** del cloud in cui è presente la macchina virtuale fare clic su **Enable protection**, quindi selezionare **Add virtual machines**.
2.  Dall'elenco delle macchine virtuali presenti nel cloud selezionare quella che si desidera proteggere.

![Abilitare la protezione per le macchine virtuali][]

Tenere traccia dell'avanzamento dell'azione di abilitazione della protezione, inclusa la replica iniziale, nella scheda relativa ai processi. Dopo il completamento del processo di finalizzazione della protezione, la macchina virtuale è pronta per il failover. Una volta abilitata la protezione e replicate le macchine virtuali, sarà possibile visualizzare queste ultime in Azure.

![Processo di protezione della macchina virtuale][]

## <span id="recoveryplans"></span></a>Passaggio 8: Test della distribuzione

Per verificare la distribuzione, è possibile eseguire un failover di test per una singola macchina virtuale. In alternativa, è possibile creare un piano di ripristino comprendente più macchine virtuali ed eseguire un failover di test per tale piano. Il failover di test consente di simulare il meccanismo di failover e di ripristino in una rete isolata.

-   Per istruzioni sulla creazione di un piano di ripristino, vedere [Passaggio 7: Creare e personalizzare piani di ripristino - Da sito locale ad Azure][].
-   Per istruzioni sull'esecuzione di un failover di test, vedere [Testare una distribuzione da sito locale a sito locale][].

### <span id="runtest"></span></a>Monitoraggio dell'attività

È possibile usare le schede **Processi** e **Dashboard** per visualizzare e monitorare i principali processi eseguiti dall'insieme di credenziali di Azure Site Recovery, inclusi la configurazione della protezione per un cloud, l'abilitazione e la disabilitazione della protezione per una macchina virtuale, l'esecuzione di un failover (pianificato, non pianificato o di test) e il commit di un failover non pianificato.

Nella scheda **Processi** è possibile visualizzare i processi, analizzare in profondità i dettagli e gli errori, eseguire query per recuperare processi corrispondenti a criteri specifici, esportare processi in Excel e riavviare quelli non riusciti.

Nella scheda **Dashboard** è possibile scaricare le versioni più recenti dei file di installazione del provider e dell'agente, ottenere informazioni di configurazione per l'insieme di credenziali, visualizzare il numero di macchine virtuali con protezione gestita dall'insieme di credenziali, visualizzare i processi recenti, gestire il certificato dell'insieme di credenziali e risincronizzare le macchine virtuali.

Per altre informazioni sull'interazione con le schede Processi e Dashboard, vedere [Amministrare e monitorare Azure Site Recovery][].

## <span id="next"></span></a>Passaggi successivi

-   Per pianificare e distribuire Azure Site Recovery in un ambiente di produzione completo, vedere la [guida alla pianificazione di Azure Site Recovery][Pianificare la distribuzione di Azure Site Recovery] e [Distribuire Site Recovery di Azure - protezione da sito locale a sito locale][Distribuire Azure Site Recovery: protezione da sito locale a sito locale].
-   In caso di domande, vedere il [forum sui Servizi di ripristino di Azure][forum sui servizi di ripristino di Azure].

</div>

  [Introduzione ad Azure Site Recovery: protezione da sito locale a sito locale]: http://go.microsoft.com/fwlink/?LinkId=398765
  [Introduzione ad Azure Site Recovery: protezione da sito locale ad Azure]: http://go.microsoft.com/fwlink/?LinkId=398764
  [Pianificare la distribuzione di Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=321294
  [Distribuire Azure Site Recovery: protezione da sito locale a sito locale]: http://go.microsoft.com/fwlink/?LinkId=321295
  [Azure Site Recovery: scenari di errore comuni e soluzioni]: http://go.microsoft.com/fwlink/?LinkId=389879
  [forum sui servizi di ripristino di Azure]: http://go.microsoft.com/fwlink/?LinkId=313628
  [Versione di valutazione gratuita per un mese]: http://aka.ms/try-azure
  [dettagli sui prezzi per Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=378268
  [Passaggio 1: Creare un insieme di credenziali]: #vault
  [Passaggio 2: Installare l'applicazione del provider]: #download
  [Passaggio 3: Configurare la protezione cloud]: #clouds
  [Passaggio 6: Configurare il mapping di archiviazione]: #storagemapping
  [Passaggio 7: Abilitare la protezione per le macchine virtuali]: #enablevirtual
  [Passaggio 8: Configurare ed eseguire piani di ripristino]: #recovery%20plans
  [portale di gestione]: https://manage.windowsazure.com
  [1]: http://go.microsoft.com/fwlink/?LinkId=389880
  [Nuovo insieme di credenziali]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [Icona Avvio rapido]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [Chiave di registrazione]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2ERegisterKey.png
  [Prerequisiti]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png
  [Aggiornamenti Microsoft]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png
  [Impostazioni Internet]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [Registrazione del server]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png
  [2]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png
  [Cloud pubblicato]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png
  [Configurazione delle impostazioni di protezione]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2E.png
  [Passaggio 3: Configurare le impostazioni di protezione per i cloud VMM]: http://go.microsoft.com/fwlink/?LinkId=323469
  [3]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2ERep.png
  [Passaggio 5: Configurare il mapping di archiviazione - Da sito locale a sito locale]: http://go.microsoft.com/fwlink/?LinkId=324817
  [Passaggio 4: Configurare il mapping delle reti - Da sito locale a sito locale]: http://go.microsoft.com/fwlink/?LinkId=402534
  [4]: http://go.microsoft.com/fwlink/?LinkId=402535
  [Abilitare la protezione per le macchine virtuali]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png
  [Processo di protezione della macchina virtuale]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
  [Passaggio 7: Creare e personalizzare piani di ripristino - Da sito locale ad Azure]: http://go.microsoft.com/fwlink/?LinkId=511492
  [Testare una distribuzione da sito locale a sito locale]: http://go.microsoft.com/fwlink/?LinkId=511493
  [Amministrare e monitorare Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=398534
