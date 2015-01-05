<properties urlDisplayName="How to manage" pageTitle="Come gestire gli account di archiviazione | Microsoft Azure" metaKeywords="Azure manage storage accounts, storage account management portal, storage account geo-replication, Azure geo-replication, Azure access keys" description="Learn how to manage storage accounts in Azure by using the Management Portal." metaCanonical="" services="storage" documentationCenter="" title="How To Manage Storage Accounts" authors="tamram" solutions="" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/17/2014" ms.author="tamram" />



<h1><a id="managestorageaccounts"></a>Come gestire gli account di archiviazione</h1>

##Sommario##

* [Procedura: Gestire la replica dell'account di archiviazione](#georeplication)
* [Procedura: Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione](#regeneratestoragekeys)
* [Procedura: Eliminare un account di archiviazione](#deletestorageaccount)

<h2><a id="georeplication"></a>Procedura: Replicare i dati dell'account di archiviazione per garantire la durabilità e la disponibilità elevata</h2>

[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

### Per specificare le impostazioni di replica per un account di archiviazione ###

1. Nel [portale di gestione di Azure](https://manage.windowsazure.com) fare clic su **Archiviazione** e quindi sul nome dell'account di archiviazione per visualizzare il dashboard.

2. Fare clic su **Configura**.

3. Nel campo **Replica** selezionare il tipo di replica desiderata per l'account di archiviazione.

4. Fare clic su **Salva** e confermare la scelta, se richiesto.


<h2><a id="regeneratestoragekeys"></a>Procedura: Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione</h2>
Quando si crea un account di archiviazione, Azure genera due chiavi di accesso alle risorse di archiviazione da 512 bit, che vengono usate per l'autenticazione quando si accede all'account di archiviazione. Fornendo due chiavi di accesso alle risorse di archiviazione, Azure consente di rigenerare le chiavi senza interruzioni per il servizio di archiviazione o l'accesso a tale servizio.

Nel [portale di gestione](http://manage.windowsazure.com) usare **Gestisci chiavi** nel dashboard o la pagina **Archiviazione** per visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione usate per accedere ai servizi BLOB, tabelle e di accodamento. 

### Copiare una chiave di accesso alle risorse di archiviazione ###

È possibile usare **Gestisci chiavi** per copiare una chiave di accesso alle risorse di archiviazione da usare in una stringa di connessione. La stringa di connessione richiede il nome dell'account di archiviazione e una chiave da usare nell'autenticazione. Per informazioni sulla configurazione delle stringhe di connessione per accedere ai servizi di archiviazione di Azure, vedere [Configurazione delle stringhe di connessione](http://msdn.microsoft.com/it-it/library/ee758697.aspx).

1. Nel [portale di gestione](http://manage.windowsazure.com) fare clic su **Archiviazione** e quindi sul nome dell'account di archiviazione per aprire il dashboard.

2. Fare clic su **Gestisci chiavi**.

 	**Manage Access Keys** opens.

	![Managekeys](./media/storage-manage-storage-account/Storage_ManageKeys.png)

 
3. Per copiare una chiave di accesso alle risorse di archiviazione, selezionare il testo della chiave. Quindi, fare clic con il pulsante destro del mouse e scegliere **Copia**.

### Rigenerare le chiavi di accesso alle risorse di archiviazione ###
È consigliabile modificare le chiavi di accesso all'account di archiviazione periodicamente per mantenere più sicure le connessioni alle risorse di archiviazione. Vengono assegnate due chiavi di accesso per consentire di mantenere le connessioni all'account di archiviazione mediante una chiave di accesso mentre si rigenera l'altra chiave di accesso. 

<div class="dev-callout"> 
    <b>Avviso</b> 
    <p>La rigenerazione delle chiavi di accesso influisce sulle macchine virtuali, sui servizi multimediali e sulle applicazioni che dipendono dall'account di archiviazione. È necessario aggiornare tutti i client che usano la chiave di accesso per accedere all'account di archiviazione per l'uso della nuova chiave.
    </p> 
    </div>

**Macchine virtuali**: se l'account di archiviazione contiene macchine virtuali in esecuzione, sarà necessario ridistribuire tutte le macchine virtuali dopo aver rigenerato le chiavi di accesso. Per evitare la ridistribuzione, arrestare le macchine virtuali prima di rigenerare le chiavi di accesso.
 
**Servizi multimediali**: se dall'account di archiviazione dipendono servizi multimediali, è necessario risincronizzare le chiavi di accesso con il servizio multimediale dopo aver rigenerato le chiavi.
 
**Applicazioni**: se si dispone di applicazioni Web o servizi cloud che usano l'account di archiviazione, le connessioni andranno perdute se si rigenerano le chiavi, a meno di non registrare le chiavi. Di seguito è riportato il processo:

1. Aggiornare le stringhe di connessione nel codice dell'applicazione in modo che facciano riferimento alla chiave di accesso secondaria dell'account di archiviazione. 

2. Rigenerare la chiave di accesso primaria per l'account di archiviazione. Nel dashboard o nella pagina **Configura** nel [portale di gestione](http://manage.windowsazure.com) fare clic su **Gestisci chiavi**. Fare clic su **Rigenera** in corrispondenza della chiave di accesso primaria e quindi fare clic su **Sì** per confermare che si vuole generare una nuova chiave.

3. Aggiornare le stringhe di connessione nel codice in modo che facciano riferimento alla nuova chiave di accesso primaria.

4. Rigenerare la chiave di accesso secondaria.


<h2><a id="deletestorageaccount"></a>Procedura: Eliminare un account di archiviazione</h2>

Per rimuovere un account di archiviazione non più in uso, usare **Elimina** nel dashboard o nella pagina **Configura**. **Elimina** elimina l'intero account di archiviazione, inclusi tutti i BLOB, le tabelle e le code nell'account. 

<div class="dev-callout">
	<b>Avviso</b>
	<p>Non è possibile ripristinare il contenuto da un account di archiviazione eliminato. Assicurarsi 
	di eseguire il backup di tutti gli elementi da salvare prima di eliminare l'account.
	</p>
	<p>
	Se l'account di archiviazione contiene file o dischi VHD per una macchina virtuale di Azure, 
	è necessario eliminare le immagini e i dischi che usano tali file VHD 
	prima di poter eliminare l'account di archiviazione. Arrestare innanzitutto la macchina virtuale se è in esecuzione e quindi eliminarlo. Per eliminare i dischi, passare alla scheda Disks ed eliminare eventuali dischi contenuti nell'account di archiviazione. Per eliminare le immagini, passare alla scheda Images ed eliminare eventuali immagini archiviate nell'account.
	</p>
</div>


1. Nel [portale di gestione](http://manage.windowsazure.com) fare clic su **Archiviazione**.

2. Fare clic in un punto qualsiasi della voce dell'account di archiviazione tranne che sul nome e quindi fare clic su **Elimina**.

	 -Oppure-

	Fare clic sul nome dell'account di archiviazione per aprire il dashboard e quindi fare clic su **Elimina**.

3. Fare clic su **Sì** per confermare che si vuole eliminare l'account di archiviazione.

<!--HONumber=35.1-->
