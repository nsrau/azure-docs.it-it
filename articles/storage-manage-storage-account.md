<properties linkid="manage-services-how-to-manage-a-storage-account" urlDisplayName="How to manage" pageTitle="How to manage storage accounts | Microsoft Azure" metaKeywords="Azure manage storage accounts, storage account management portal, storage account geo-replication, Azure geo-replication, Azure access keys" description="Learn how to manage storage accounts in Azure by using the Management Portal." metaCanonical="" services="storage" documentationCenter="" title="How To Manage Storage Accounts" authors="tamram" solutions="" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# <span id="managestorageaccounts"></span></a>Come gestire gli account di archiviazione

## Sommario

-   [Procedura: Gestire la replica dell'account di archiviazione][]
-   [Procedura: Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione][]
-   [Procedura: Eliminare un account di archiviazione][]

## <span id="georeplication"></span></a>Procedura: Replicare i dati dell'account di archiviazione per garantire la durabilità e la disponibilità elevata

[WACOM.INCLUDE [storage-replication-options][]]

### Per specificare le impostazioni di replica per un account di archiviazione

1.  Nel [portale di gestione di Azure][] fare clic su **Storage** e quindi sul nome dell'account di archiviazione per visualizzare il dashboard.

2.  Fare clic su **Configure**.

3.  Nel campo **Replication** selezionare il tipo di replica desiderata per l'account di archiviazione.

4.  Fare clic su **Save** e confermare la scelta, se richiesto.

## <span id="regeneratestoragekeys"></span></a>Procedura: Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione

Quando si crea un account di archiviazione, Azure genera due chiavi di accesso alle risorse di archiviazione da 512 bit, che vengono utilizzate per l'autenticazione quando si accede all'account di archiviazione. Fornendo due chiavi di accesso alle risorse di archiviazione, Azure consente di rigenerare le chiavi senza interruzioni per il servizio di archiviazione o l'accesso a tale servizio.

Nel [portale di gestione][] utilizzare **Manage Keys** nel dashboard o la pagina **Storage** per visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione utilizzate per accedere ai servizi BLOB, tabelle e di accodamento.

### Copia di una chiave di accesso alle risorse di archiviazione

È possibile utilizzare **Manage Keys** per copiare una chiave di accesso alle risorse di archiviazione da utilizzare in una stringa di connessione. La stringa di connessione richiede il nome dell'account di archiviazione e una chiave da utilizzare nell'autenticazione. Per informazioni sulla configurazione delle stringhe di connessione per accedere ai servizi di archiviazione di Azure, vedere [Configurazione delle stringhe di connessione][].

1.  Nel [portale di gestione][] fare clic su **Storage** e quindi sul nome dell'account di archiviazione per aprire il dashboard.

2.  Fare clic su **Manage Keys**.

    **Verrà aperto** Manage Access Keys.

    ![ManageKeys][]

3.  Per copiare una chiave di accesso alle risorse di archiviazione, selezionare il testo della chiave. Fare quindi clic con il pulsante destro del mouse e scegliere **Copy**.

### Rigenerazione delle chiavi di accesso alle risorse di archiviazione

È consigliabile modificare le chiavi di accesso all'account di archiviazione periodicamente per mantenere più sicure le connessioni alle risorse di archiviazione. Vengono assegnate due chiavi di accesso per consentire di mantenere le connessioni all'account di archiviazione mediante una chiave di accesso mentre si rigenera l'altra chiave di accesso.

<div class="dev-callout"> 
    <b>Avviso</b> 
    <p>La rigenerazione delle chiavi di accesso influisce sulle macchine virtuali, sui servizi multimediali e sulle applicazioni che dipendono dall'account di archiviazione. &Egrave; necessario aggiornare tutti i client che usano la chiave di accesso per accedere all'account di archiviazione per l'uso della nuova chiave.
    </p> 
    </div>

**Macchine virtuali** - Se l'account di archiviazione contiene macchine virtuali in esecuzione, sarà necessario ridistribuire tutte le macchine virtuali dopo aver rigenerato le chiavi di accesso. Per evitare la ridistribuzione, arrestare le macchine virtuali prima di rigenerare le chiavi di accesso.

**Servizi multimediali** - Se dall'account di archiviazione dipendono servizi multimediali, è necessario risincronizzare le chiavi di accesso con il servizio multimediale dopo aver rigenerato le chiavi.

**Applicazioni** - Se si dispone di applicazioni Web o servizi cloud che utilizzano l'account di archiviazione, si perderanno le connessioni se si rigenerano le chiavi, a meno che non si registrino le chiavi. Di seguito è riportato il processo:

1.  Aggiornare le stringhe di connessione nel codice dell'applicazione in modo che facciano riferimento alla chiave di accesso secondaria dell'account di archiviazione.

2.  Rigenerare la chiave di accesso primaria per l'account di archiviazione. Nel [portale di gestione][], nel dashboard o nella pagina **Configure** fare clic su **Manage Keys**. Fare clic su **Regenerate** nella chiave di accesso primaria e quindi fare clic su **Yes** per confermare che si desidera generare una nuova chiave.

3.  Aggiornare le stringhe di connessione nel codice in modo che facciano riferimento alla nuova chiave di accesso primaria.

4.  Rigenerare la chiave di accesso secondaria.

## <span id="deletestorageaccount"></span></a>Procedura: Eliminare un account di archiviazione

Per rimuovere un account di archiviazione non più in uso, utilizzare **Delete** nel dashboard o nella pagina **Configure**. **Delete** elimina l'intero account di archiviazione, inclusi tutti i BLOB, le tabelle e le code nell'account.

<div class="dev-callout">
    <b>Avviso</b>
    <p>Non &egrave; possibile ripristinare il contenuto da un account di archiviazione eliminato. Assicurarsi 
    di eseguire il backup di tutti gli elementi da salvare prima di eliminare l'account.
    </p>
    <p>
    Se l'account di archiviazione contiene file o dischi VHD per una macchina virtuale di Azure, 
    &egrave; necessario eliminare le immagini e i dischi che usano tali file VHD 
    prima di poter eliminare l'account di archiviazione. Arrestare innanzitutto la macchina virtuale se &egrave; in esecuzione e quindi eliminarlo. Per eliminare i dischi, passare alla scheda Disks ed eliminare eventuali dischi contenuti nell'account di archiviazione. Per eliminare le immagini, passare alla scheda Images ed eliminare eventuali immagini archiviate nell'account.
    </p>
</div>

1.  Nel [portale di gestione][] fare clic su **Storage**.

2.  Fare clic ovunque nella voce dell'account di archiviazione tranne che sul nome e quindi fare clic su **Delete**.

    -Oppure-

    Fare clic sul nome dell'account di archiviazione per aprire il dashboard e quindi fare clic su **Delete**.

3.  Fare clic su **Yes** per confermare che si desidera eliminare l'account di archiviazione.

  [Procedura: Gestire la replica dell'account di archiviazione]: #georeplication
  [Procedura: Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione]: #regeneratestoragekeys
  [Procedura: Eliminare un account di archiviazione]: #deletestorageaccount
  [storage-replication-options]: ../includes/storage-replication-options.md
  [portale di gestione di Azure]: https://manage.windowsazure.com
  [portale di gestione]: http://manage.windowsazure.com
  [Configurazione delle stringhe di connessione]: http://msdn.microsoft.com/en-us/library/ee758697.aspx
  [ManageKeys]: ./media/storage-manage-storage-account/Storage_ManageKeys.png
