<properties 
   pageTitle="Gestire l'account di archiviazione StorSimple | Microsoft Azure"
   description="Spiega come usare la pagina Configura di StorSimple Manager per aggiungere, modificare, eliminare o ruotare le chiavi di sicurezza per un account di archiviazione associato all'array virtuale StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="03/02/2016"
   ms.author="alkohli" />

# Utilizzare il servizio StorSimple Manager per gestire l’account di archiviazione

## Panoramica

La pagina **Configura** presenta i parametri di servizio globali che possono essere creati nel servizio StorSimple Manager. Questi parametri possono essere applicati a tutti i dispositivi connessi al servizio e includono:

- Account di archiviazione 
- Record di controllo di accesso 

Questa esercitazione illustra come usare la pagina **Configura** per aggiungere, modificare o eliminare gli account di archiviazione per l'array virtuale StorSimple. Le informazioni contenute in questa esercitazione si applicano solo all'array virtuale StorSimple che esegue il software della versione di disponibilità generale di marzo 2016.

 ![Pagina Configura](./media/storsimple-ova-manage-storage-accounts/configure_service_page.png)

Gli account di archiviazione contengono le credenziali utilizzate dal dispositivo per accedere all'account di archiviazione con il provider di servizi cloud. Per gli account di archiviazione di Microsoft Azure, queste sono le credenziali, ad esempio il nome dell'account e la chiave di accesso primaria.

Nella pagina **Configura** tutti gli account di archiviazione creati per la sottoscrizione di fatturazione vengono visualizzati in un formato tabulare che contiene le informazioni seguenti:

- **Nome**: il nome univoco assegnato all'account al momento della creazione.
- **SSL abilitato**: se SSL è abilitato e la comunicazione tra dispositivo e cloud avviene tramite il canale protetto.

Le attività più comuni relative agli account di archiviazione che possono essere eseguite nella pagina **Configura** sono:

- Aggiungere un account di archiviazione 
- Modificare un account di archiviazione 
- Eliminare un account di archiviazione 


## Tipi di account di archiviazione

Esistono tre tipi di account di archiviazione che è possibile utilizzare con il dispositivo StorSimple.

- **Account di archiviazione generati automaticamente**: come suggerito dal nome, questo tipo di account di archiviazione viene generato automaticamente quando viene inizialmente creato il servizio. Per altre informazioni sulla creazione di questo account di archiviazione, vedere [Creare un nuovo servizio](storsimple-ova-manage-service.md#create-a-service). 
- **Account di archiviazione nella sottoscrizione del servizio**: sono gli account di archiviazione di Azure associati alla stessa sottoscrizione del servizio. Per ulteriori informazioni su come vengono creati questi account di archiviazione, vedere [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md). 
- **Account di archiviazione esterni alla sottoscrizione al servizio**: sono gli account di archiviazione di Azure non associati al servizio e probabilmente esistenti prima che il servizio fosse creato.

## Aggiungere un account di archiviazione

È possibile aggiungere un account di archiviazione al servizio StorSimple Manager indicando un nome descrittivo univoco e le credenziali di accesso collegate all'account di archiviazione. È inoltre possibile scegliere di abilitare la modalità SSL (Secure Socket Layer) per creare un canale protetto per la comunicazione di rete tra il dispositivo e il cloud.

È possibile creare più account per un provider del servizio cloud specificato. Durante il salvataggio dell'account di archiviazione, il servizio tenta di comunicare con il provider di servizi cloud. Le credenziali e i dati di accesso forniti verranno autenticati in questa fase. L’account di archiviazione viene creato solo se l'autenticazione ha esito positivo. Se l'autenticazione non riesce, verrà visualizzato un messaggio di errore appropriato.

Di seguito è riportata la procedura per aggiungere un account di archiviazione.

[AZURE.INCLUDE [add-a-storage-account](../../includes/storsimple-ova-configure-new-storage-account.md)]

## Modificare un account di archiviazione

È possibile modificare un account di archiviazione usato dal dispositivo. Se si modifica un account di archiviazione attualmente in uso, i campi disponibili per la modifica sono la chiave di accesso e la modalità SSL per l'account di archiviazione. È possibile indicare la nuova chiave di accesso alle risorse di archiviazione o modificare la selezione **Abilita modalità SSL** e salvare le impostazioni aggiornate.

#### Per modificare un account di archiviazione

1. Nella pagina di destinazione del servizio, selezionare il servizio, fare doppio clic sul nome del servizio, quindi fare clic su **Configura**.

2. Fare clic su **Aggiungi/modifica account di archiviazione**.

3. Nella finestra di dialogo **Aggiungi/modifica account di archiviazione**:

  1. Nell'elenco a discesa di **Account di archiviazione**, scegliere un account esistente da modificare.
  2. Se necessario, è possibile modificare la selezione **Abilita modalità SSL**.
  3. È possibile rigenerare le chiavi di accesso dell'account di archiviazione. Per altre informazioni, vedere l'articolo relativo alla [rigenerazione delle chiavi dell'account di archiviazione](storage-create-storage-account.md#manage-your-storage-access-keys). Indicare la nuova chiave dell'account di archiviazione. Per un account di archiviazione di Azure si tratta della chiave di accesso primaria. 
  4. Fare clic sull'icona del segno di spunta ![icona del segno di spunta](./media/storsimple-ova-manage-storage-accounts/checkicon.png) per salvare le impostazioni. Le impostazioni verranno aggiornate nella pagina **Configura**. 
  5. Fare clic su **Salva** nella parte inferiore della pagina per salvare le impostazioni aggiornate. 

     ![Modificare un account di archiviazione](./media/storsimple-ova-manage-storage-accounts/modifyexistingstorageaccount.png)
  
## Eliminare un account di archiviazione

> [AZURE.IMPORTANT] È possibile eliminare un account di archiviazione solo se non è in uso. Se un account di archiviazione è in uso, si riceve una notifica.

#### Per eliminare un account di archiviazione

1. Nella pagina di destinazione del servizio StorSimple Manager, selezionare il servizio, fare doppio clic sul nome del servizio, quindi fare clic su **Configura**.

2. Nell'elenco tabulare di account di archiviazione, passare il mouse sull'account che si desidera eliminare.

3. Un'icona di eliminazione (**x**) verrà visualizzata nella colonna all'estrema destra di tale account di archiviazione. Fare clic sull’icona **x** per eliminare le credenziali.

4. Quando viene richiesta la conferma, fare clic su **Sì** per continuare con l'eliminazione. L’elenco tabulare verrà aggiornato per riflettere le modifiche.

5. Fare clic su **Salva** nella parte inferiore della pagina per salvare le impostazioni aggiornate.


## Passaggi successivi

- Informazioni sulla [gestione dell'array virtuale StorSimple](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0309_2016-->