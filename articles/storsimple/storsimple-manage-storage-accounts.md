<properties 
   pageTitle="Gestire l'account di archiviazione StorSimple | Microsoft Azure"
   description="Viene illustrato come utilizzare la pagina Configura di StorSimple Manager per aggiungere, modificare, eliminare o ruotare le chiavi di protezione per un account di archiviazione."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/01/2015"
   ms.author="v-sharos" />

# Utilizzare il servizio StorSimple Manager per gestire l’account di archiviazione

## Panoramica

La pagina **Configura** presenta tutti i parametri di servizio globali che possono essere creati nel servizio StorSimple Manager. Questi parametri possono essere applicati a tutti i dispositivi connessi al servizio e includono:

- Account di archiviazione 
- Modelli di larghezza di banda 
- Record di controllo di accesso 

In questa esercitazione viene illustrato come utilizzare la pagina **Configura** per aggiungere, modificare o eliminare gli account di archiviazione o ruotare le chiavi di protezione per un account di archiviazione.

 ![Pagina Configura](./media/storsimple-manage-storage-accounts/HCS_ConfigureService.png)

Gli account di archiviazione contengono le credenziali utilizzate dal dispositivo per accedere all'account di archiviazione con il provider di servizi cloud. Per gli account di archiviazione di Microsoft Azure, queste sono le credenziali, ad esempio il nome dell'account e la chiave di accesso primaria.

Nella pagina **Configura** tutti gli account di archiviazione creati per la sottoscrizione di fatturazione vengono visualizzati in un formato tabulare che contiene le informazioni seguenti:

- **Nome**: il nome univoco assegnato all'account al momento della creazione.
- **SSL abilitato**: se SSL è abilitato e la comunicazione tra dispositivo e cloud avviene tramite il canale protetto.
- **Utilizzato da**: il numero di volumi che utilizzano l’account di archiviazione.

Le attività più comuni relative agli account di archiviazione che possono essere eseguite nella pagina **Configura** sono:

- Aggiungere un account di archiviazione 
- Modificare un account di archiviazione 
- Eliminare un account di archiviazione 
- Rotazione delle chiavi degli account di archiviazione 

## Tipi di account di archiviazione

Esistono tre tipi di account di archiviazione che è possibile utilizzare con il dispositivo StorSimple.

- **Account di archiviazione generati automaticamente**: come suggerito dal nome, questo tipo di account di archiviazione viene generato automaticamente quando viene inizialmente creato il servizio. Per ulteriori informazioni sulla creazione di questo account di archiviazione, vedere il [Passaggio 1: Creare un nuovo servizio](storsimple-deployment-walkthrough-u1.md#step-1-create-a-new-service) in [Distribuire il dispositivo StorSimple locale](storsimple-deployment-walkthrough.md). 
- **Account di archiviazione nella sottoscrizione del servizio**: sono gli account di archiviazione di Azure associati alla stessa sottoscrizione del servizio. Per ulteriori informazioni su come vengono creati questi account di archiviazione, vedere [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md). 
- **Account di archiviazione esterni alla sottoscrizione al servizio**: sono gli account di archiviazione di Azure non associati al servizio e probabilmente esistenti prima che il servizio fosse creato.

## Aggiungere un account di archiviazione

È possibile aggiungere un account di archiviazione fornendo un nome descrittivo univoco e le credenziali di accesso collegate all'account di archiviazione (con il provider del servizio cloud specificato). È inoltre possibile scegliere di abilitare la modalità SSL (Secure Socket Layer) per creare un canale protetto per la comunicazione di rete tra il dispositivo e il cloud.

È possibile creare più account per un provider del servizio cloud specificato. Tenere presente, tuttavia, che dopo aver creato un account di archiviazione non è possibile modificare il provider di servizi cloud.

Durante il salvataggio dell'account di archiviazione, il servizio tenta di comunicare con il provider di servizi cloud. Le credenziali e i dati di accesso forniti verranno autenticati in questa fase. L’account di archiviazione viene creato solo se l'autenticazione ha esito positivo. Se l'autenticazione non riesce, verrà visualizzato un messaggio di errore appropriato.

> [AZURE.NOTE]La procedura per l'aggiunta di un account di archiviazione varia in base alla versione del software StorSimple in uso. Assicurarsi di seguire la procedura corretta per la versione di StorSimple a disposizione.

[AZURE.INCLUDE [add-a-storage-account-update1](../../includes/storsimple-configure-new-storage-account-u1.md)]

[AZURE.INCLUDE [add-a-storage-account](../../includes/storsimple-configure-new-storage-account.md)]

## Modificare un account di archiviazione

È possibile modificare un account di archiviazione utilizzato da un contenitore di volumi. Se si modifica un account di archiviazione in uso, l'unico campo disponibile per la modifica è la chiave di accesso dell'account di archiviazione. È possibile fornire la nuova chiave di accesso di archiviazione e salvare le impostazioni aggiornate.

#### Per modificare un account di archiviazione

1. Nella pagina di destinazione del servizio, selezionare il servizio, fare doppio clic sul nome del servizio, quindi fare clic su **Configura**.

2. Fare clic su **Aggiungi/modifica account di archiviazione**.

3. Nella finestra di dialogo **Aggiungi/modifica account di archiviazione**:

  1. Nell'elenco a discesa di **Account di archiviazione**, scegliere un account esistente da modificare. È possibile scegliere anche gli account di archiviazione generati automaticamente in fase di creazione del servizio.
  2. Se necessario, è possibile modificare la selezione **Abilita modalità SSL**.
  3. È possibile ruotare le chiavi di accesso dell’account di archiviazione. Vedere [Rotazione delle chiavi degli account di archiviazione](#key-rotation-of-storage-accounts) per ulteriori informazioni su come eseguire la rotazione delle chiavi.
  4. Fare clic sull'icona del segno di spunta ![icona del segno di spunta](./media/storsimple-manage-storage-accounts/HCS_CheckIcon.png) per salvare le impostazioni. Le impostazioni verranno aggiornate nella pagina **Configura**. Fare clic su **Salva** per salvare le impostazioni appena aggiornate.

     ![Modificare un account di archiviazione](./media/storsimple-manage-storage-accounts/HCs_AddEditStorageAccount.png)
  
## Eliminare un account di archiviazione

> [AZURE.IMPORTANT]È possibile eliminare un account di archiviazione solo se non viene utilizzato da un contenitore di volumi. Se un account di archiviazione viene utilizzato da un contenitore di volumi, eliminare prima il contenitore di volumi e poi l'account di archiviazione associato.

#### Per eliminare un account di archiviazione

1. Nella pagina di destinazione del servizio StorSimple Manager, selezionare il servizio, fare doppio clic sul nome del servizio, quindi fare clic su **Configura**.

2. Nell'elenco tabulare di account di archiviazione, passare il mouse sull'account che si desidera eliminare.

3. Un'icona di eliminazione (**x**) verrà visualizzata nella colonna all'estrema destra di tale account di archiviazione. Fare clic sull’icona **x** per eliminare le credenziali.

4. Quando viene richiesta la conferma, fare clic su **Sì** per continuare con l'eliminazione. L’elenco tabulare verrà aggiornato per riflettere le modifiche.

## Rotazione delle chiavi degli account di archiviazione

Per motivi di sicurezza, la rotazione delle chiavi è spesso un requisito nei centri dati.

> [AZURE.NOTE]Le seguenti informazioni per la rotazione delle chiavi e la procedura di rotazione si applicano solo agli account di archiviazione di Microsoft Azure. Se si usa un altro provider di servizi cloud, è possibile gestire le chiavi degli account di archiviazione tramite il dashboard di tale provider.
 
Ogni sottoscrizione di Microsoft Azure può essere associata a uno o più account di archiviazione. L'accesso a tali account di archiviazione è controllato dalle chiavi di sottoscrizione e di accesso associate a ogni account di archiviazione.

Quando si crea un account di archiviazione, Microsoft Azure genera due chiavi di accesso alle risorse di archiviazione da 512 bit, che vengono utilizzate per l'autenticazione quando si accede all'account di archiviazione. Fornendo due chiavi di accesso alle risorse di archiviazione, Azure consente di rigenerare le chiavi senza interruzioni per il servizio di archiviazione o l'accesso a tale servizio. La chiave in uso è la chiave *primaria* e la chiave di backup è indicata come chiave *secondaria*. Una di queste due chiavi deve essere specificata quando il dispositivo StorSimple di Microsoft Azure accede al provider di servizi di archiviazione cloud.

## Che cos'è la rotazione delle chiavi?

In genere, le applicazioni utilizzano solo una delle chiavi per accedere ai dati. Dopo un determinato periodo di tempo, è possibile impostare alcune applicazioni perché utilizzino la seconda chiave. Dopo avere impostato le applicazioni per la chiave secondaria, è possibile ritirare la prima chiave e quindi generare una nuova chiave. Utilizzando due chiavi in questo modo le applicazioni possono accedere ai dati senza tempi di inattività.

Le chiavi degli account di archiviazione vengono sempre archiviate nel servizio in formato crittografato. Tuttavia, possono essere reimpostate tramite il servizio StorSimple Manager. Il servizio consente di ottenere la chiave primaria e la chiave secondaria per tutti gli account di archiviazione nella stessa sottoscrizione, inclusi gli account creati nel servizio di archiviazione e gli account di archiviazione predefiniti generati in fase di creazione del servizio StorSimple Manager. Il servizio StorSimple Manager otterrà sempre queste chiavi dal portale di Azure classico e le archivierà in modo crittografato.

## Flusso di lavoro di rotazione

Un amministratore di Microsoft Azure può rigenerare oppure modificare la chiave primaria o secondaria accedendo direttamente all'account di archiviazione (tramite il servizio di archiviazione di Microsoft Azure). Il servizio StorSimple Manager non consente di visualizzare la modifica automaticamente.

Per informare il servizio StorSimple Manager della modifica, sarà necessario accedere al servizio StorSimple Manager, accedere all'account di archiviazione e quindi sincronizzare la chiave primaria o secondaria (a seconda di quale è stata modificata). Quindi, il servizio ottiene la chiave più recente, la crittografa e la invia al dispositivo.

#### Per sincronizzare le chiavi per gli account di archiviazione nella stessa sottoscrizione del servizio (solo Azure)

1. Nella pagina **Servizi** fare clic sulla scheda **Configura**.

2. Fare clic su **Aggiungi/modifica account di archiviazione**.

3. Nella finestra di dialogo procedere come segue:

  1. Selezionare l'account di archiviazione con la chiave che si desidera sincronizzare. Le chiavi degli account di archiviazione vengono visualizzate in forma crittografata.
  2. Nel servizio StorSimple Manager, è necessario aggiornare la chiave precedentemente modificata nel servizio di archiviazione di Microsoft Azure. Se la chiave di accesso primaria è stata modificata (rigenerata), fare clic su **Sincronizza chiave primaria**. Se è stata modificata la chiave secondaria, fare clic su **Sincronizza chiave secondaria**.

    ![sincronizzare le chiavi](./media/storsimple-manage-storage-accounts/HCS_KeyRotationStorageAccountSameSubscriptionAsService.png)

#### Per sincronizzare le chiavi per gli account di archiviazione esterni alla sottoscrizione del servizio

1. Nella pagina **Servizi** fare clic sulla scheda **Configura**.

2. Fare clic su **Aggiungi/modifica account di archiviazione**.

3. Nella finestra di dialogo procedere come segue:

  1. Selezionare l'account di archiviazione con la chiave di accesso che si desidera aggiornare.
  2. Sarà necessario aggiornare la chiave di accesso di archiviazione nel servizio StorSimple Manager. In questo caso, è possibile visualizzare la chiave di accesso di archiviazione. Immettere la nuova chiave nella casella **Chiave di accesso dell'account di archiviazione**. 
  3. Salvare le modifiche. La chiave di accesso dell’account di archiviazione appare aggiornata.

## Passaggi successivi

- Ulteriori informazioni sulla [sicurezza di StorSimple](storsimple-security.md).
- Ulteriori informazioni sull’[utilizzo del servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_1203_2015-->