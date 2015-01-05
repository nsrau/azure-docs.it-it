<properties urlDisplayName="How to create" pageTitle="Come creare, gestire o eliminare un account di archiviazione | Azure" metaKeywords="" description="Learn how to create, manage, or delete a storage account in the Azure management portal." metaCanonical="" services="storage" documentationCenter="" title="How To Create a Storage Account" solutions="" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/17/2014" ms.author="tamram" />


# Informazioni sugli account di archiviazione di Azure

Un account di archiviazione di Azure è un account sicuro che consente di accedere ai servizi in Archiviazione di Azure. L'account di archiviazione offre uno spazio dei nomi univoco per i dati e, per impostazione predefinita, è disponibile solo per il proprietario dell'account. 

Sono disponibili due tipi di account di archiviazione:

- Un account di archiviazione standard include l'archiviazione BLOB, tabelle e di accodamento. L'archiviazione file è disponibile su richiesta tramite la [pagina Anteprima di Azure](/it-it/services/preview/).
- Un account di archiviazione premium attualmente supporta solo dischi di macchine virtuali di Azure. L'archiviazione premium di Azure è disponibile su richiesta tramite la [pagina Anteprima di Azure](/it-it/services/preview/). Vedere la pagina [Archiviazione premium: archiviazione dalle prestazioni elevate per carichi di lavoro di macchine virtuali di Azure](http://go.microsoft.com/fwlink/?LinkId=521898) per una panoramica approfondita dell'archiviazione premium.

L'importo fatturato per l'uso di Archiviazione di Azure dipende dall'account di archiviazione. I costi di archiviazione sono determinati da quattro fattori: capacità di archiviazione, schema di replica, transazioni di archiviazione e uscita dei dati. 

- La capacità di archiviazione fa riferimento alla quantità di unità di servizio dell'account di archiviazione in uso per l'archiviazione dei dati. Il costo dell'archiviazione dei dati è determinato dalla quantità di dati archiviata e dalla modalità di replica dei dati. 
- La replica determina quante copie dei dati vengono gestite in una sola volta e in quali posizioni. 
- Le transazioni fanno riferimento a tutte le operazioni di lettura e scrittura in Archiviazione di Azure. 
- L'uscita dati fa riferimento ai dati trasferiti all'esterno di un'area di Azure. Quando un'applicazione non in esecuzione nella stessa area geografica accede ai dati nell'account di archiviazione, indipendentemente dal fatto che l'applicazione sia un servizio cloud o un altro tipo di applicazione, verrà addebitato il trasferimento dei dati in uscita. Per i servizi di Azure è possibile raggruppare dati e servizi nello stesso data center per ridurre o evitare del tutto gli addebiti per il trasferimento dei dati in uscita.  

Nella pagina [Dettagli prezzi di archiviazione](http://www.windowsazure.com/it-it/pricing/details/#storage) vengono fornite informazioni dettagliate sui prezzi per la capacità, la replica e le transazioni relative all'archiviazione. Nella pagina [Dettagli prezzi dei trasferimenti di dati](http://www.windowsazure.com/it-it/pricing/details/data-transfers/) vengono fornite informazioni dettagliate sui prezzi per il trasferimento dei dati in uscita.

Per informazioni sugli obiettivi di capacità e prestazioni dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](http://msdn.microsoft.com/library/windowsazure/dn249410.aspx).

> [WACOM.NOTE] Quando si crea una macchina virtuale di Azure, un account di archiviazione viene creato automaticamente nel percorso di distribuzione se non si ha già un account di archiviazione in tale percorso. Non è quindi necessario attenersi alla procedura riportata sotto per creare un account di archiviazione per i dischi della macchina virtuale. Il nome dell'account di archiviazione sarà basato sul nome della macchina virtuale. Per altri dettagli, vedere la [documentazione relativa alle macchine virtuali di Azure](/it-it/documentation/services/virtual-machines/). <br />

## Sommario ##

In questo articolo viene illustrato come creare un account di archiviazione standard e quali decisioni tenere in considerazione mentre lo si crea. Viene anche illustrato come gestire le chiavi di accesso dell'account di archiviazione e come eliminare un account di archiviazione.


- [Procedura: Creare un account di archiviazione](#create)
- [Procedura: Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione](#regeneratestoragekeys)
- [Procedura: Eliminare un account di archiviazione](#deletestorageaccount)
* [Passaggi successivi](#next)

## <a id="create"></a>Procedura: Creare un account di archiviazione ##

1. Accedere al [portale di gestione](https://manage.windowsazure.com).

2. Fare clic su **Crea nuovo**, quindi su **Archiviazione** e infine su **Creazione rapida**.

	![NewStorageAccount](./media/storage-create-storage-account/storage_NewStorageAccount.png)

3. In **URL** immettere un nome per l'account di archiviazione. Vedere [Endpoint dell'account di archiviazione](#account-endpoints) più avanti per informazioni dettagliate sul modo in cui questo nome verrà usato per indirizzare oggetti memorizzati in Archiviazione di Azure.

4. In **Località/gruppo di affinità** selezionare una località per l'account di archiviazione vicina all'utente o ai clienti. Se i dati di un account di archiviazione saranno accessibili da un altro servizio di Azure, come una macchina virtuale o un servizio cloud di Azure, potrebbe essere necessario selezionare un gruppo di affinità nell'elenco per raggruppare l'account di archiviazione nello stesso data center con altri servizi di Azure usati per migliorare le prestazioni e ridurre i costi. 

	> [WACOM.NOTE] Tenere presente che è necessario selezionare un gruppo di affinità quando l'account di archiviazione viene creato. Non è possibile spostare un account esistente in un gruppo di affinità.

	Per informazioni dettagliate sui gruppi di affinità, vedere [Condivisione del percorso del servizio con un gruppo di affinità](#affinity-group) più avanti.

	
5. Se si dispone di più sottoscrizioni di Azure, viene visualizzato il campo **Sottoscrizione**. In **Sottoscrizione** immettere la sottoscrizione di Azure con cui usare l'account di archiviazione. È possibile creare fino a cinque account di archiviazione per una sottoscrizione.

6. In **Replica** selezionare il livello di replica desiderato per l'account di archiviazione. L'opzione di replica raccomandata è la replica con ridondanza geografica, che fornisce il livello di durabilità massimo per i dati. Per altri dettagli sulle opzioni di replica di Archiviazione di Azure, vedere [Opzioni di replica dell'account di archiviazione](#replication-options) più avanti.

6. Fare clic su **Crea account di archiviazione**.

	Potrebbero essere necessari alcuni minuti per creare l'account di archiviazione. Per verificare lo stato, è possibile monitorare le notifiche nella parte inferiore del portale. Il nuovo account di archiviazione creato presenta lo stato **Online**ed è pronto per essere usato. 

![StoragePage](./media/storage-create-storage-account/Storage_StoragePage.png)


### <a id="account-endpoints"></a>Endpoint dell'account di archiviazione 

Ogni oggetto archiviato in Archiviazione di Azure ha un indirizzo URL univoco. Il nome dell'account di archiviazione costituisce il sottodominio di tale indirizzo. Il sottodominio con il nome di dominio, specifico di ogni servizio, costituisce un *endpoint* per l'account di archiviazione. 

Ad esempio, se l'account di archiviazione si chiama *mystorageaccount*, gli endpoint predefiniti per l'account di archiviazione sono: 

- Servizio BLOB: http://*mystorageaccount*.blob.core.windows.net

- Servizio tabelle: http://*mystorageaccount*.table.core.windows.net

- Servizio di accodamento: http://*mystorageaccount*.queue.core.windows.net

- Servizio file: http://*mystorageaccount*.file.core.windows.net

È possibile vedere gli endpoint per l'account di archiviazione nel dashboard di archiviazione nel portale di gestione di Azure una volta creato l'account.

L'URL per accedere a un oggetto in un account di archiviazione viene formato aggiungendo la posizione dell'oggetto nell'account di archiviazione all'endpoint. Ad esempio, il formato di un indirizzo BLOB è simile al seguente: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

È anche possibile configurare un nome di dominio personalizzato da usare con l'account di archiviazione. Per i dettagli, vedere [Configurare un nome di dominio personalizzato per i dati BLOB in un account di archiviazione](../storage-custom-domain-name/).

### <a id="affinity-group"></a>Condivisione del percorso del servizio con un gruppo di affinità 

Un *gruppo di affinità* è un raggruppamento geografico dei servizi e delle VM di Azure con l'account di archiviazione di Azure. che consente di migliorare le prestazioni del servizio individuando i carichi di lavoro del computer nello stesso data center o in prossimità dei destinatari. Non sono inoltre addebitate spese per l'uscita in caso di accesso ai dati di un account di archiviazione da parte di un altro servizio appartenente allo stesso gruppo di affinità.

> [WACOM.NOTE]  Per creare un gruppo di affinità, aprire l'area <b>Impostazioni</b> del portale di gestione, fare clic su <b>Gruppi di affinità</b> e quindi fare clic sul pulsante <b>Aggiungi un gruppo di affinità</b> o <b>Aggiungi</b>. È inoltre possibile creare e gestire gruppi di affinità mediante l'API Gestione servizi di Azure. Per altre informazioni, vedere <a href="http://msdn.microsoft.com/it-it/library/windowsazure/ee460798.aspx">Operazioni sui gruppi di affinità</a>.


### <a id="replication-options"></a>Opzioni di replica dell'account di archiviazione

[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]


## <a id="regeneratestoragekeys"></a>Procedura: Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione

Quando si crea un account di archiviazione, Azure genera due chiavi di accesso alle risorse di archiviazione da 512 bit, che vengono usate per l'autenticazione quando si accede all'account di archiviazione. Fornendo due chiavi di accesso alle risorse di archiviazione, Azure consente di rigenerare le chiavi senza interruzioni per il servizio di archiviazione o l'accesso a tale servizio.

> [WACOM.NOTE] È consigliabile non condividere le chiavi di accesso dell'account di archiviazione con altri utenti. Per permettere l'accesso alle risorse di archiviazione senza fornire le chiavi di accesso, è possibile usare una *firma di accesso condiviso*. Una firma di accesso condiviso fornisce l'accesso a una risorsa nell'account per un intervallo di tempo definito e con le autorizzazioni specificate. Per altre informazioni, vedere l'[esercitazione sulla firma di accesso condiviso](../storage-dotnet-shared-access-signature-part-1/).

Nel [portale di gestione](http://manage.windowsazure.com) usare **Gestisci chiavi** nel dashboard o la pagina **Archiviazione** per visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione usate per accedere ai servizi BLOB, tabelle e di accodamento. 

### Copiare una chiave di accesso alle risorse di archiviazione ###

È possibile usare **Gestisci chiavi** per copiare una chiave di accesso alle risorse di archiviazione da usare in una stringa di connessione. La stringa di connessione richiede il nome dell'account di archiviazione e una chiave da usare nell'autenticazione. Per informazioni sulla configurazione delle stringhe di connessione per accedere ai servizi di archiviazione di Azure, vedere [Configurazione delle stringhe di connessione](http://msdn.microsoft.com/it-it/library/ee758697.aspx).

1. Nel [portale di gestione](http://manage.windowsazure.com) fare clic su **Archiviazione** e quindi sul nome dell'account di archiviazione per aprire il dashboard.

2. Fare clic su **Gestisci chiavi**.

 	Verrà aperto**Gestisci chiavi di accesso**.

	![Managekeys](./media/storage-create-storage-account/Storage_ManageKeys.png)

 
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

## <a id="deletestorageaccount"></a>Procedura: Eliminare un account di archiviazione

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

## <a id="next"></a>Passaggi successivi

- Per altre informazioni sull'archiviazione di Azure, vedere la relativa documentazione sul sito Web [azure.com](http://azure.microsoft.com/it-it/documentation/services/storage/) e su [MSDN](http://msdn.microsoft.com/it-it/library/gg433040.aspx). 

- [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/).


 

<!--HONumber=35.1-->
