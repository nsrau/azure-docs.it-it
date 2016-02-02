<properties
	pageTitle="Come creare, gestire o eliminare un account di archiviazione nel portale di Azure | Microsoft Azure"
	description="Creare un nuovo account di archiviazione, gestire le chiavi di accesso dell'account o eliminare un account di archiviazione nel portale di Azure. Informazioni sugli account di archiviazione Standard e Premium."
	services="storage"
	documentationCenter=""
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/26/2016"
	ms.author="robinsh"/>


# Informazioni sugli account di archiviazione di Azure

[AZURE.INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]

## Panoramica

Un account di archiviazione di Azure consente di accedere ai servizi BLOB, di accodamento, tabelle e file nel servizio di archiviazione di Azure. L'account di archiviazione offre uno spazio dei nomi univoco per gli oggetti dati dell'archiviazione di Azure. Per impostazione predefinita, i dati nel proprio account sono accessibili solo all'utente, ovvero al proprietario dell'account.

Sono disponibili due tipi di account di archiviazione:

- Un account di archiviazione standard include l'archiviazione BLOB, tabelle, di accodamento e file.
- Un account di archiviazione Premium attualmente supporta solo dischi di macchine virtuali di Azure. Vedere [Archiviazione Premium: archiviazione dalle prestazioni elevate per carichi di lavoro di macchine virtuali di Azure](storage-premium-storage-preview-portal.md) per una panoramica approfondita del servizio di archiviazione Premium.

## Fatturazione dell'account di archiviazione

L'importo fatturato per l'uso di Archiviazione di Azure dipende dall'account di archiviazione. I costi di archiviazione sono determinati da quattro fattori: capacità di archiviazione, schema di replica, transazioni di archiviazione e uscita dei dati.

- La capacità di archiviazione fa riferimento alla quantità di unità di servizio dell'account di archiviazione in uso per l'archiviazione dei dati. Il costo dell'archiviazione dei dati è determinato dalla quantità di dati archiviata e dalla modalità di replica dei dati.
- La replica determina quante copie dei dati vengono gestite in una sola volta e in quali posizioni.
- Le transazioni fanno riferimento a tutte le operazioni di lettura e scrittura in Archiviazione di Azure.
- L'uscita dati fa riferimento ai dati trasferiti all'esterno di un'area di Azure. Quando un'applicazione non in esecuzione nella stessa area geografica accede ai dati nell'account di archiviazione, indipendentemente dal fatto che l'applicazione sia un servizio cloud o un altro tipo di applicazione, verrà addebitato il trasferimento dei dati in uscita. Per i servizi di Azure è possibile raggruppare dati e servizi nello stesso data center per ridurre o evitare del tutto gli addebiti per il trasferimento dei dati in uscita.  

Nella pagina relativa ai [prezzi di Azure](https://azure.microsoft.com/pricing/details/#storage) vengono fornite informazioni dettagliate sui prezzi per la capacità, la replica e le transazioni relative all'archiviazione. Nella pagina [Dettagli prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/) vengono fornite informazioni dettagliate sui prezzi per il trasferimento dei dati in uscita.

Per informazioni sugli obiettivi di capacità e prestazioni dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](http://msdn.microsoft.com/library/azure/dn249410.aspx).

> [AZURE.NOTE] Quando si crea una macchina virtuale di Azure, un account di archiviazione viene creato automaticamente nel percorso di distribuzione se non si ha già un account di archiviazione in tale percorso. Non è quindi necessario attenersi alla procedura riportata sotto per creare un account di archiviazione per i dischi della macchina virtuale. Il nome dell'account di archiviazione sarà basato sul nome della macchina virtuale. Per altri dettagli, vedere la [documentazione relativa alle macchine virtuali di Azure](https://azure.microsoft.com/documentation/services/virtual-machines/).

## Endpoint dell'account di archiviazione

Ogni oggetto archiviato in Archiviazione di Azure ha un indirizzo URL univoco. Il nome dell'account di archiviazione costituisce il sottodominio dell'indirizzo. La combinazione di sottodominio e nome di dominio, specifico di ogni servizio, costituisce un *endpoint* per l'account di archiviazione.

Ad esempio, se l'account di archiviazione si chiama *mystorageaccount*, gli endpoint predefiniti per l'account di archiviazione sono:

- Servizio BLOB: http://*mystorageaccount*.blob.core.windows.net

- Servizio tabelle: http://*mystorageaccount*.table.core.windows.net

- Servizio di accodamento: http://*mystorageaccount*.queue.core.windows.net

- Servizio file: http://*mystorageaccount*.file.core.windows.net

L'URL per accedere a un oggetto in un account di archiviazione viene formato aggiungendo la posizione dell'oggetto nell'account di archiviazione all'endpoint. Ad esempio, il formato di un indirizzo BLOB è simile al seguente: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

È anche possibile configurare un nome di dominio personalizzato da usare con l'account di archiviazione. Per altre informazioni, vedere [Configurare un nome di dominio personalizzato per i dati BLOB in un account di archiviazione di Azure](storage-custom-domain-name.md).

## Creare un account di archiviazione

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel menu Hub selezionare **Nuovo** -> **Dati e archiviazione** -> **Account di archiviazione**.

3. Selezionare un modello di distribuzione: **Gestione risorse** o **Classico**. **Gestione risorse** è il modello di distribuzione consigliato. Per altre informazioni, vedere [Comprendere la distribuzione di Gestione delle risorse e distribuzione classica](../resource-manager-deployment-model.md).

4. Immettere un nome per l'account di archiviazione.

	> [AZURE.NOTE] I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole.
	>  
	> Nome dell'account di archiviazione deve essere univoco all'interno di Azure. Il portale di Azure indica se il nome di account di archiviazione selezionato è già in uso.

	Per informazioni dettagliate sul modo in cui questo nome verrà usato per indirizzare oggetti in Archiviazione di Azure, vedere [Endpoint dell'account di archiviazione](#storage-account-endpoints) più avanti.

5. Specificare il tipo di account di archiviazione da creare. Il tipo di account di archiviazione determina la modalità di replica dell'account di archiviazione e se si tratta di un account di archiviazione Standard o Premium.

	Il tipo di account di archiviazione predefinito è **Standard RAGRS**, ovvero un account di archiviazione standard con replica con ridondanza geografica e accesso in lettura. Questo tipo di account di archiviazione viene replicato in un'area secondaria a centinaia di chilometri di distanza dall'area primaria e fornisce accesso in lettura alla posizione secondaria.

	Per altre informazioni sulle opzioni di replica di Archiviazione di Azure, vedere [Replica di Archiviazione di Azure](storage-redundancy.md). Per altre informazioni sugli account di archiviazione Standard e Premium, vedere [Introduzione ad Archiviazione di Microsoft Azure](storage-introduction.md) e [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](storage-premium-storage-preview-portal.md).

6. Indicare se si desidera abilitare le funzionalità di diagnostica per l'account di archiviazione. Le funzionalità di diagnostica includono le metriche e la registrazione di Analisi archiviazione.

7. Se si dispone di più sottoscrizioni Azure, viene visualizzato il campo **Subscription**. Selezionare la sottoscrizione in cui si desidera creare il nuovo account di archiviazione.

8. Specificare un nuovo gruppo di risorse o selezionarne uno esistente. Per altre informazioni su gruppi di risorse, vedere [Uso del portale di Azure per gestire le risorse di Azure](../azure-portal/resource-group-portal.md).

9. Selezionare la posizione geografica dell'account di archiviazione.

10. Fare clic su **Crea** per creare l'account di archiviazione.

## Gestire le chiavi di accesso alle risorse di archiviazione

Quando si crea un account di archiviazione, Azure genera due chiavi di accesso alle risorse di archiviazione da 512 bit, che vengono utilizzate per l'autenticazione quando si accede all'account di archiviazione. Fornendo due chiavi di accesso alle risorse di archiviazione, Azure consente di rigenerare le chiavi senza interruzioni per il servizio di archiviazione o l'accesso a tale servizio.

> [AZURE.NOTE] È consigliabile non condividere le chiavi di accesso alle risorse di archiviazione con altri utenti. Per permettere l'accesso alle risorse di archiviazione senza fornire le chiavi di accesso, è possibile usare una *firma di accesso condiviso*. Una firma di accesso condiviso fornisce l'accesso a una risorsa nell'account per un intervallo di tempo definito e con le autorizzazioni specificate. Per altre informazioni, vedere l'[esercitazione sulla firma di accesso condiviso](storage-dotnet-shared-access-signature-part-1.md).

### Visualizzare e copiare le chiavi di accesso alle risorse di archiviazione

Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione e fare clic sull'icona **Chiavi** per visualizzare, copiare e rigenerare le chiavi di accesso all'account. Il pannello **Chiavi di accesso** include anche le stringhe di connessione preconfigurate che usano le chiavi primarie e secondarie, che è possibile copiare per usarle nelle applicazioni.

### Rigenerazione delle chiavi di accesso alle risorse di archiviazione

Si consiglia di modificare periodicamente le chiavi di accesso all'account di archiviazione per mantenere le connessioni dell'archiviazione sicure. Vengono assegnate due chiavi di accesso in modo da mantenere le connessioni all'account di archiviazione mediante una chiave di accesso mentre si rigenera l'altra.

> [AZURE.WARNING] La rigenerazione delle chiavi di accesso influisce sulle macchine virtuali, sui servizi multimediali e sulle applicazioni che dipendono dall'account di archiviazione. È necessario aggiornare tutti i client che usano la chiave di accesso per accedere all'account di archiviazione per l'uso della nuova chiave.

**Macchine virtuali** - Se l'account di archiviazione contiene macchine virtuali in esecuzione, sarà necessario ridistribuire tutte le macchine virtuali dopo aver rigenerato le chiavi di accesso. Per evitare la ridistribuzione, arrestare le macchine virtuali prima di rigenerare le chiavi di accesso.

**Servizi multimediali**: se si dispone di servizi multimediali dipendenti dall'account di archiviazione, è necessario risincronizzare le chiavi di accesso con il proprio servizio multimediale dopo la rigenerazione delle chiavi.

**Applicazioni**: se sono presenti applicazioni Web o servizi cloud che usano l'account di archiviazione, le connessioni andranno perse se si rigenerano le chiavi, a meno che non venga eseguito il rollover delle chiavi.

Di seguito è riportato il processo per la rotazione delle chiavi di accesso alle risorse di archiviazione:

1. Aggiornare le stringhe di connessione nel codice dell'applicazione in modo che facciano riferimento alla chiave di accesso secondaria dell'account di archiviazione.

2. Rigenerare la chiave di accesso primaria per l'account di archiviazione. Nel pannello **Chiavi di accesso** fare clic su **Rigenerare Key1** e quindi su **Sì** per confermare che si desidera generare una nuova chiave.

3. Aggiornare le stringhe di connessione nel codice in modo che facciano riferimento alla nuova chiave di accesso primaria.

4. Rigenerare la chiave di accesso secondaria nello stesso modo.

## Eliminare un account di archiviazione

Per rimuovere un account di archiviazione che non si usa più, passare all'account di archiviazione nel [portale di Azure](https://portal.azure.com) e fare clic su **Elimina**. Se si elimina un account di archiviazione, viene eliminato l'intero account, inclusi tutti i dati in esso contenuti.

> [AZURE.WARNING] Non è possibile ripristinare un account di archiviazione eliminato, né recuperare gli elementi che conteneva prima dell'eliminazione. Assicurarsi di eseguire il backup di tutti gli elementi da salvare prima di eliminare l'account. Lo stesso vale per tutte le risorse nell'account: dopo aver eliminato un BLOB, una tabella, una coda o un file, non è più possibile recuperarlo.

Per eliminare un account di archiviazione associato a una macchina virtuale di Azure, è necessario assicurarsi prima di tutto che i dischi delle macchine virtuali siano stati eliminati. Se prima non si eliminano i dischi delle macchine virtuali, quando si prova a eliminare l'account di archiviazione, verrà visualizzato un messaggio di errore simile al seguente:

    Failed to delete storage account <vm-storage-account-name>. Unable to delete storage account <vm-storage-account-name>: 'Storage account <vm-storage-account-name> has some active image(s) and/or disk(s). Ensure these image(s) and/or disk(s) are removed before deleting this storage account.'.

Per rimuovere il disco della macchina virtuale, seguire questa procedura nel portale di Azure classico:

1. Passare al [portale di Azure classico](https://manage.windowsazure.com).
2. Passare alla scheda Macchine virtuali.
3. Fare clic sulla scheda Dischi.
4. Selezionare il disco dati, quindi fare clic su Elimina disco.
5. Per eliminare le immagini del disco, passare alla scheda Immagini ed eliminare le eventuali immagini archiviate nell'account.

Per altre informazioni, vedere la [documentazione relativa alle macchine virtuali di Azure](http://azure.microsoft.com/documentation/services/virtual-machines/).

## Passaggi successivi

- [Replica di Archiviazione di Azure](storage-redundancy.md)
- [Configurare una stringa di connessione ad archiviazione di Azure](storage-configure-connection-string.md)
- [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md)
- [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/).

<!---HONumber=AcomDC_0128_2016-->