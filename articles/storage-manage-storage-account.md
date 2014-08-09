<properties linkid="manage-services-how-to-manage-a-storage-account" urlDisplayName="How to manage" pageTitle="How to manage storage accounts | Microsoft Azure" metaKeywords="Azure manage storage accounts, storage account management portal, storage account geo-replication, Azure geo-replication, Azure access keys" description="Learn how to manage storage accounts in Azure by using the Management Portal." metaCanonical="" services="storage" documentationCenter="" title="How To Manage Storage Accounts" authors="tamram" solutions="" manager="mbaldwin" editor="cgronlun" />

Come gestire gli account di archiviazione
=========================================

Sommario
--------

-   [Procedura: Gestire la replica dell'account di archiviazione](#georeplication)
-   [Procedura: Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione](#regeneratestoragekeys)
-   [Procedura: Eliminare un account di archiviazione](#deletestorageaccount)

Procedura: Gestire la replica dell'account di archiviazione
-----------------------------------------------------------

Sono disponibili tre opzioni per replicare l'account di archiviazione:

-   **Replica con ridondanza geografica.** La replica con ridondanza geografica per l'account di archiviazione è abilitata per impostazione predefinita. Con la replica con ridondanza geografica, i dati vengono replicati in una posizione geografica secondaria per consentire il failover in tale posizione in caso di errore grave nella posizione primaria. La posizione secondaria si trova nella stessa area, ma a centinaia di chilometri dalla posizione primaria.

-   **Replica con ridondanza geografica con accesso in lettura.** La replica con ridondanza geografica con accesso in lettura replica i dati in una posizione geografica secondaria e fornisce inoltre l'accesso in lettura ai dati nella posizione secondaria. La replica con ridondanza geografica con accesso in lettura consente di accedere ai dati dalla posizione primaria o secondaria, qualora una posizione non sia disponibile.

-   **Replica con ridondanza locale**. Con la replica con ridondanza locale, i dati dell'account di archiviazione vengono replicati tre volte all'interno dello stesso data center. La replica con ridondanza locale è offerta a prezzi scontati.

    Tenere presente che, se si specifica la replica con ridondanza locale per l'account di archiviazione e in seguito si decide di abilitare la replica con ridondanza geografica, è previsto un costo una tantum per replicare i dati esistenti nella posizione secondaria.

-   **Replica con ridondanza geografica con accesso in lettura (solo anteprima).** La replica con ridondanza geografica con accesso in lettura replica i dati in una posizione geografica secondaria e fornisce inoltre l'accesso in lettura ai dati nella posizione secondaria. La replica con ridondanza geografica con accesso in lettura consente di accedere ai dati dalla posizione primaria o secondaria, qualora una posizione non sia disponibile.

Per informazioni sui prezzi per la replica dell'account di archiviazione, vedere [Dettagli prezzi di archiviazione](http://www.windowsazure.com/it-it/pricing/details/storage/).

### Per specificare le impostazioni di replica per un account di archiviazione

1.  Nel [portale di gestione di Azure](https://manage.windowsazure.com) fare clic su **Storage** e quindi sul nome dell'account di archiviazione per visualizzare il dashboard.

2.  Fare clic su **Configure**.

3.  Nel campo **Replication** selezionare il tipo di replica desiderata per l'account di archiviazione.

4.  Fare clic su **Save** e confermare la scelta, se richiesto.

Procedura: Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione
------------------------------------------------------------------------------------------------

Quando si crea un account di archiviazione, Azure genera due chiavi di accesso alle risorse di archiviazione da 512 bit, che vengono utilizzate per l'autenticazione quando si accede all'account di archiviazione. Fornendo due chiavi di accesso alle risorse di archiviazione, Azure consente di rigenerare le chiavi senza interruzioni per il servizio di archiviazione o l'accesso a tale servizio.

Nel [portale di gestione](http://manage.windowsazure.com) utilizzare **Manage Keys** nel dashboard o la pagina **Storage** per visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione utilizzate per accedere ai servizi BLOB, tabelle e di accodamento.

### Copia di una chiave di accesso alle risorse di archiviazione

È possibile utilizzare **Manage Keys** per copiare una chiave di accesso alle risorse di archiviazione da utilizzare in una stringa di connessione. La stringa di connessione richiede il nome dell'account di archiviazione e una chiave da utilizzare nell'autenticazione. Per informazioni sulla configurazione delle stringhe di connessione per accedere ai servizi di archiviazione di Azure, vedere [Configurazione delle stringhe di connessione](http://msdn.microsoft.com/it-it/library/ee758697.aspx).

1.  Nel [portale di gestione](http://manage.windowsazure.com) fare clic su **Storage** e quindi sul nome dell'account di archiviazione per aprire il dashboard.

2.  Fare clic su **Manage Keys**.

	**Verrà aperto** Manage Access Keys.

	![Managekeys](./media/storage-manage-storage-account/Storage_ManageKeys.png)


1.  Per copiare una chiave di accesso alle risorse di archiviazione, selezionare il testo della chiave. Fare quindi clic con il pulsante destro del mouse e scegliere **Copy**.

### Rigenerazione delle chiavi di accesso alle risorse di archiviazione

È consigliabile modificare le chiavi di accesso all'account di archiviazione periodicamente per mantenere più sicure le connessioni alle risorse di archiviazione. Vengono assegnate due chiavi di accesso per consentire di mantenere le connessioni all'account di archiviazione mediante una chiave di accesso mentre si rigenera l'altra chiave di accesso.
**Avviso**

La rigenerazione delle chiavi di accesso influisce sulle macchine virtuali, sui servizi multimediali e sulle applicazioni che dipendono dall'account di archiviazione.

**Macchine virtuali** - Se l'account di archiviazione contiene macchine virtuali in esecuzione, sarà necessario ridistribuire tutte le macchine virtuali dopo aver rigenerato le chiavi di accesso. Per evitare la ridistribuzione, arrestare le macchine virtuali prima di rigenerare le chiavi di accesso.

**Servizi multimediali** - Se dall'account di archiviazione dipendono servizi multimediali, è necessario risincronizzare le chiavi di accesso con il servizio multimediale dopo aver rigenerato le chiavi.

**Applicazioni** - Se si dispone di applicazioni Web o servizi cloud che utilizzano l'account di archiviazione, si perderanno le connessioni se si rigenerano le chiavi, a meno che non si registrino le chiavi. Di seguito è riportato il processo:

1.  Aggiornare le stringhe di connessione nel codice dell'applicazione in modo che facciano riferimento alla chiave di accesso secondaria dell'account di archiviazione.

2.  Rigenerare la chiave di accesso primaria per l'account di archiviazione. Nel [portale di gestione](http://manage.windowsazure.com), nel dashboard o nella pagina **Configure** fare clic su **Manage Keys**. Fare clic su **Regenerate** nella chiave di accesso primaria e quindi fare clic su **Yes** per confermare che si desidera generare una nuova chiave.

3.  Aggiornare le stringhe di connessione nel codice in modo che facciano riferimento alla nuova chiave di accesso primaria.

4.  Rigenerare la chiave di accesso secondaria.

Procedura: Eliminare un account di archiviazione
------------------------------------------------

Per rimuovere un account di archiviazione non più in uso, utilizzare **Delete** nel dashboard o nella pagina **Configure**. **Delete** elimina l'intero account di archiviazione, inclusi tutti i BLOB, le tabelle e le code nell'account.
**Avviso**

Non è possibile ripristinare il contenuto da un account di archiviazione eliminato. Assicurarsi di eseguire il backup di tutto ciò che si desidera salvare prima di eliminare l'account.

Se l'account di archiviazione contiene file VHD o dischi per una macchina virtuale di Azure, è necessario eliminare eventuali immagini e dischi che utilizzano tali file VHD prima di poter eliminare l'account di archiviazione. Arrestare innanzitutto la macchina virtuale se è in esecuzione e quindi eliminarlo. Per eliminare i dischi, passare alla scheda Disks ed eliminare eventuali dischi contenuti nell'account di archiviazione. Per eliminare le immagini, passare alla scheda Images ed eliminare eventuali immagini archiviate nell'account.

1.  Nel [portale di gestione](http://manage.windowsazure.com) fare clic su **Storage**.

2.  Fare clic ovunque nella voce dell'account di archiviazione tranne che sul nome e quindi fare clic su **Delete**.

    -Oppure-

    Fare clic sul nome dell'account di archiviazione per aprire il dashboard e quindi fare clic su **Delete**.

3.  Fare clic su **Yes** per confermare che si desidera eliminare l'account di archiviazione.


