<properties linkid="manage-services-import-export" urlDisplayName="Azure Import/Export Service" pageTitle="Using import/export to transfer data to Blob Storage | Microsoft Azure" metaKeywords="" description="Learn how to create import and export jobs in the Azure Management Portal to transfer data to blob storage." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="Using the Azure Import/Export Service to Transfer Data to Blob Storage" authors="tamram" />

Utilizzo del servizio Importazione/Esportazione di Azure per il trasferimento di dati nell'archiviazione BLOB
=============================================================================================================

È possibile utilizzare il servizio Importazione/Esportazione di Azure per trasferire grandi quantità di dati di file nell'archivio BLOB di Azure in situazioni in cui il caricamento in rete è eccessivamente costoso o non è possibile. È inoltre possibile utilizzare il servizio Importazione/Esportazione per trasferire nelle installazioni locali, nel tempo previsto e in modo conveniente, grandi quantità di dati residenti nell'archiviazione BLOB.

Per trasferire un set di grandi dimensioni di dati di file nell'archiviazione BLOB, è possibile inviare uno o più dischi rigidi contenenti tali dati a un data center di Azure, dove i dati verranno caricati nell'account di archiviazione. Analogamente, per esportare i dati dall'archiviazione BLOB, è possibile inviare dischi rigidi vuoti a un data center di Azure, dove i dati BLOB verranno copiati dall'account di archiviazione ai dischi rigidi e quindi restituiti all'utente. Prima di inviare un'unità contenente i dati, sarà opportuno crittografarli. I dati, dopo essere stati esportati dal servizio Importazione/Esportazione per l'invio all'utente, verranno crittografati anche prima della spedizione.

È possibile creare e gestire i processi di importazione ed esportazione in due modi:

-   Utilizzando il portale di gestione di Azure.
-   Utilizzando un'interfaccia REST per il servizio.

In questo articolo viene fornita una panoramica del servizio Importazione/Esportazione e viene descritto come utilizzare il portale di gestione per lavorare con il servizio Importazione/Esportazione. Per informazioni sull'API REST, vedere il [Riferimento dell'API REST del servizio di importazione/esportazione di Azure](http://go.microsoft.com/fwlink/?LinkID=329099).

Panoramica del servizio Importazione/Esportazione
-------------------------------------------------

Per iniziare il processo di importazione o di esportazione dall'archiviazione BLOB, creare innanzitutto un *processo*, che potrà essere un *processo di importazione* o un *processo di esportazione*:

-   Creare un processo di importazione quando si desidera trasferire i dati locali nei BLOB nell'account di archiviazione di Azure.
-   Creare un processo di esportazione quando si desidera trasferire su dischi rigidi spediti all'utente i dati attualmente archiviati come BLOB nell'account di archiviazione.

Quando si crea un processo, si notifica al servizio Importazione/Esportazione che si spedirà uno o più dischi rigidi a un data center di Azure. Per un processo di importazione, si spediranno dischi rigidi contenenti dati di file. Per un processo di esportazione, si spediranno dischi rigidi vuoti.

Per preparare l'unità per la spedizione per un processo di importazione, si eseguirà lo strumento **WAImportExport**, che facilita la copia dei dati sull'unità, crittografando i dati sull'unità con BitLocker e generando i file journal dell'unità, illustrati di seguito.
**Nota**

I dati sull'unità devono essere crittografati mediante Crittografia unità BitLocker, che protegge i dati mentre sono in transito. Per un processo di esportazione, il servizio Importazione/Esportazione crittograferà i dati prima della spedizione all'utente.

Quando si crea un processo di importazione o un processo di esportazione, sarà inoltre necessario l'*ID unità*, che è il numero di serie assegnato dal produttore dell'unità a un disco rigido specifico. L'ID unità è visibile all'esterno dell'unità.

### Requisiti e ambito

1.  **Sottoscrizione ed account di archiviazione:** per utilizzare il servizio Importazione/Esportazione, è necessario disporre di una sottoscrizione di Azure esistente e di uno o più account di archiviazione. Ogni processo può essere utilizzato per trasferire dati da o verso un solo account di archiviazione. In altre parole, un processo non può estendersi tra più account di archiviazione. Per informazioni sulla creazione di un nuovo account di archiviazione, vedere [Come creare un account di archiviazione](http://www.windowsazure.com/it-it/manage/services/storage/how-to-create-a-storage-account/).
2.  **Dischi rigidi:** per l'utilizzo con il servizio Importazione/Esportazione, sono supportati solo dischi rigidi SATA II da 3,5 pollici. I dischi rigidi da più di 4 TB non sono supportati con la versione preliminare. Per i processi di importazione, verrà elaborato solo il primo volume di dati sull'unità. Il volume di dati deve essere formattato con NTFS. È possibile collegare un disco SATA II esternamente alla maggior parte dei computer mediante un adattatore USB SATA II.
3.  **Crittografia BitLocker:** tutti i dati archiviati sui dischi rigidi devono essere crittografati mediante BitLocker con le chiavi di crittografia protette con password numeriche.
4.  **Destinazioni di archiviazione BLOB:** i dati possono essere caricati o scaricati da BLOB in blocchi e da BLOB di pagine.
5.  **Numero di processi:** un cliente può disporre di un massimo di 20 processi attivi per ogni sottoscrizione.
6.  **Dimensione massima di un processo:** la dimensione di un processo è determinata dalla capacità dei dischi rigidi utilizzati e dalla quantità massima di dati che possono essere archiviati in un account di archiviazione. Ogni processo non può contenere più di 10 dischi rigidi.

Creazione di un processo di importazione nel portale di gestione
----------------------------------------------------------------

Creare un processo di importazione per notificare al servizio Importazione/Esportazione che si spedirà al data center una o più unità contenenti dati da importare nell'account di archiviazione.

### Preparare le unità

Prima di creare un processo di importazione, preparare le unità con lo [strumento WAImportExport](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409). Per ulteriori dettagli sull'utilizzo dello strumento WAImportExport, vedere [Riferimento dello strumento WAImportExport](http://go.microsoft.com/fwlink/?LinkId=329032).

Per preparare le unità, seguire questi tre passaggi:

1.  Determinare i dati da importare e il numero di unità necessarie.
2.  Identificare i BLOB di destinazione per i dati nel servizio BLOB di Azure.
3.  Utilizzare lo strumento WAImportExport per copiare i dati in uno o più dischi rigidi.

Lo strumento WAImportExport genera un file *journal di unità* per ogni unità preparata. Il file journal dell'unità viene archiviato nel computer locale, non nell'unità stessa. Il file journal verrà caricato quando si creerà il processo di importazione. Un file journal di unità include l'ID unità e la chiave BitLocker, nonché altre informazioni sull'unità.

### Creare il processo di importazione

1.  Dopo aver preparato l'unità, passare all'account di archiviazione nel portale di gestione e visualizzare il dashboard. In **Quick Glance** fare clic su **Create an Import Job**.

2.  Nel passaggio 1 della procedura guidata indicare di aver preparato l'unità e che il file journal dell'unità è disponibile.

3.  Nel passaggio 2 fornire le informazioni di contatto per la persona responsabile di questo processo di importazione. Se si desidera salvare i dati del log dettagliato per il processo di importazione, selezionare l'opzione **Save the verbose log in my 'waimportexport' blob container**.

4.  Nel passaggio 3 caricare i file journal dell'unità ottenuti durante il passaggio di preparazione dell'unità. Sarà necessario caricare un file per ogni unità preparata.

    ![Creare il processo di importazione - Passaggio 3](./media/storage-import-export-service/import-job-03.png)

5.  Nel passaggio 4 immettere un nome descrittivo per il processo di importazione. Si noti che il nome che immesso può contenere solo lettere minuscole, numeri, trattini e caratteri di sottolineatura, deve iniziare con una lettera e non può contenere spazi. Il nome scelto verrà utilizzato per tenere traccia dei processi mentre sono in corso e dopo essere stati completati.

    L'area del data center indicherà il data center a cui è necessario spedire il pacchetto. Per ulteriori informazioni, vedere le domande frequenti più avanti.

    Se si dispone già di un numero di spedizione FedEx, selezionare **I have my tracking number and want to enter it now** e passare al passaggio successivo. Se non si dispone ancora di un numero di spedizione, scegliere **I will provide my shipping information for this import job once I have shipped my package**, quindi completare il processo di importazione.

6.  Se si dispone già del numero di spedizione, nel passaggio 5 immetterlo e confermarlo.

Creazione di un processo di esportazione nel portale di gestione
----------------------------------------------------------------

Creare un processo di esportazione per notificare al servizio Importazione/Esportazione che si spedirà una o più unità vuote al data center, in modo che i dati possano essere esportati dall'account di archiviazione alle unità e le unità possano quindi essere spedite all'utente.

1.  Per creare un processo di esportazione, passare all'account di archiviazione nel portale di gestione e visualizzare il dashboard. In **Quick Glance** fare clic su **Create an Export Job** ed eseguire la procedura guidata.

2.  Nel passaggio 2 fornire le informazioni di contatto per la persona responsabile di questo processo di esportazione. Se si desidera salvare i dati del log dettagliato per il processo di esportazione, selezionare l'opzione **Save the verbose log in my 'waimportexport' blob container**.

3.  Nel passaggio 3 specificare quali dati BLOB si desidera esportare dall'account di archiviazione a una o più unità vuote. È possibile scegliere di esportare tutti i dati BLOB nell'account di archiviazione oppure è possibile specificare quali BLOB oppure set di BLOB di esportare.

    ![Creare il processo di esportazione - Passaggio 3](./media/storage-import-export-service/export-job-03.png)

    -   Per specificare un BLOB da esportare, utilizzare il selettore **Equal To** e specificare il percorso relativo del BLOB, iniziando con il nome del contenitore. Utilizzare *$root* per specificare il contenitore radice.
    -   Per specificare tutti i BLOB che iniziano con un prefisso, utilizzare il selettore **Starts With** e specificare il prefisso, iniziando con una barra "/". Il prefisso può essere il prefisso del nome del contenitore, il nome del contenitore completo o il nome del contenitore completo seguito dal prefisso del nome BLOB.

    Nella tabella vengono indicati esempi di percorsi BLOB validi:

	<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
		<tbody>
			<tr>
				<td><strong>Selettore</strong></td>
				<td><strong>Percorso BLOB</strong></td>
				<td><strong>Descrizione</strong></td>
			</tr>
			<tr>
				<td>Starts With</td>
				<td>/</td>
				<td>Esporta tutti i BLOB nell'account di archiviazione</td>
			</tr>
			<tr>
				<td>Starts With</td>
				<td>/$root/</td>
				<td>Esporta tutti i BLOB nel contenitore radice</td>
			</tr>
			<tr>
				<td>Starts With</td>
				<td>/book</td>
				<td>Esporta tutti i BLOB in qualsiasi contenitore che inizia con il prefissobook <strong>book</strong></td>
			</tr>
			<tr>
				<td>Starts With</td>
				<td>/music/</td>
				<td>Esporta tutti i BLOB nel contenitore <strong>music</strong></td>
			</tr>
			<tr>
				<td>Starts With</td>
				<td>/music/love</td>
				<td>Esporta nel contenitore <strong>music</strong> tutti i BLOB che iniziano con il prefisso <strong>love</strong></td>
			</tr>
			<tr>
				<td>Equal To</td>
				<td>$root/logo.bmp</td>
				<td>Esporta il BLOB <strong>logo.bmp</strong> nel contenitore radice</td>
			</tr>
			<tr>
				<td>Equal To</td>
				<td>videos/story.mp4</td>
				<td>Esporta il BLOB <strong>story.mp4</strong> nel contenitore <strong>videos</strong></td>
			</tr>
		</tbody>
	</table>


4.  Nel passaggio 4 immettere un nome descrittivo per il processo di esportazione. Il nome che immesso può contenere solo lettere minuscole, numeri, trattini e caratteri di sottolineatura, deve iniziare con una lettera e non può contenere spazi.

    L'area del data center indicherà il data center a cui è necessario spedire il pacchetto. Per ulteriori informazioni, vedere le domande frequenti più avanti.

    Se si dispone già di un numero di spedizione FedEx, selezionare **I have my tracking number and want to enter it now** e passare al passaggio successivo. Se non si dispone ancora di un numero di spedizione, scegliere **I will provide my shipping information for this export job once I have shipped my package**, quindi completare il processo di importazione.

5.  Nel passaggio 5 immettere il numero di spedizione, quindi confermarlo.

Traccia dello stato del processo nel portale di gestione
--------------------------------------------------------

È possibile tenere traccia dello stato dei processo di importazione o esportazione dal portale di gestione. Passare all'account di archiviazione nel portale di gestione e fare clic sulla scheda **Import/Export**. Nella pagina verrà visualizzato un elenco dei processi. È possibile filtrare l'elenco per stato processo, nome processo, tipo di processo o numero di spedizione.

Nella tabella viene descritto il significato di ogni designazione dello stato del processo:


<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
	<tbody>
		<tr>
			<td><strong>Stato processo</strong></td>
			<td><strong>Descrizione</strong></td>
		</tr>
		<tr>
			<td>Creating</td>
			<td>Il processo è stato creato, ma non sono ancora state specificate le informazioni per la spedizione.</td>
		</tr>
		<tr>
			<td>Shipping</td>
			<td>Il processo è stato creato e sono state specificate le informazioni per la spedizione.</td>
		</tr>
		<tr>
			<td>Transferring</td>
			<td>È in corso il trasferimento dei dati dal disco rigido (per un processo di importazione) o al disco rigido (per un processo di esportazione).</td>
		</tr>
		<tr>
			<td>Packaging</td>
			<td>Il trasferimento dei vostri dati è stato completato ed è in corso la preparazione del disco rigido per la spedizione all'utente.</td>
		</tr>
		<tr>
			<td>Complete</td>
			<td>Il disco rigido è stato spedito all'utente.</td>
		</tr>
	</tbody>
</table>

Visualizzazione delle chiavi BitLocker per un processo di esportazione
----------------------------------------------------------------------

Per i processi di esportazione, è possibile visualizzare e copiare le chiavi BitLocker generate dal servizio per l'unità, in modo che sia possibile decrittografare i dati esportati dopo aver ricevuto l'unità dal data center di Azure. Passare all'account di archiviazione nel portale di gestione e fare clic sulla scheda **Import/Export**. Selezionare il processo di esportazione nell'elenco e fare clic sul pulsante **View Keys**. Le chiavi BitLocker vengono visualizzate come illustrato:

![Visualizzare le chiavi BitLocker per il processo di esportazione](./media/storage-import-export-service/export-job-bitlocker-keys.png)

Domande frequenti
-----------------

### Generale

**Qual è il prezzo per il servizio Importazione/Esportazione?**

-   Per informazioni sui prezzi, vedere la [pagina dei prezzi](http://go.microsoft.com/fwlink/?LinkId=329033).

**Quanto tempo richiederà l'importazione o l'esportazione dei dati?**

-   Il tempo necessario per spedire i dischi più alcune ore per ogni TB di dati da copiare.

**Quali tipi di interfaccia sono supportati?**

-   Il servizio Importazione/Esportazione supporta dischi rigidi SATA II da 3,5 pollici. È possibile utilizzare i convertitori seguenti per trasferire dati nei dispositivi USB in SATA prima della spedizione:
    -   Anker 68UPSATAA-02BU
    -   Anker 68UPSHHDS-BU
    -   Startech SATADOCK22UE

**Che cosa si deve fare, se si desidera importare o esportare più di 10 unità?**

-   Un unico processo di importazione o esportazione può fare riferimento solo a 10 unità nella versione preliminare del servizio Importazione/Esportazione. Se si desidera spedire più di 10 unità, è possibile creare più processi.

**Cosa accade se si invia per errore un disco rigido non conforme ai requisiti supportati?**

-   Il data center di Azure restituirà all'utente l'unità non conforme ai requisiti supportati. Se solo alcune delle unità nel pacchetto soddisfano i requisiti per il supporto, tali unità verranno elaborate e le unità che non soddisfano i requisiti verranno restituite all'utente.

### Gestione dei processi di importazione/esportazione

**Che cosa accade ai processi di importazione ed esportazione se si elimina l'account di archiviazione di Azure?**

-   Quando si elimina l'account di archiviazione, insieme all'account vengono eliminati tutti i processi di importazione/esportazione di Azure.

**È possibile annullare il processo?**

-   È possibile annullare un processo quando lo stato è Creating o Shipping.

**Per quanto tempo è possibile visualizzare lo stato dei processi completati nel portale di gestione?**

-   È possibile visualizzare lo stato per i processi completati fino a 90 giorni. Tutti i processi completati verranno archiviati dopo 90 giorni. Se è necessario recuperare lo stato dei processi completati dopo 90 giorni, è possibile contattare il supporto clienti.

### Spedizione

**Quali servizi di corriere sono supportati?**

-   Solo Federal Express (FedEx) è supportato nella versione preliminare.
-   I pacchetti per un processo di importazione possono essere spediti con FedEx Express o FedEx Ground.
-   Tutti i pacchetti saranno restituiti tramite FedEx Ground.

    **Importante**

    È necessario fornire il numero di spedizione per il servizio Importazione/Esportazione di Azure o il processo non potrà essere elaborato.

**Esistono costi associati alla spedizione di ritorno?**

-   La spedizione di ritorno è gratuita nella versione preliminare.

**Da dove e verso dove è possibile spedire i dati?**

- Il servizio di Importazione/Esportazione può accettare solo spedizioni **in partenza** da località degli Stati Uniti e può restituire i pacchetti solo a indirizzi negli Stati Uniti. Il servizio supporta l'importazione e l'esportazione dei dati verso e da account di archiviazione nelle aree seguenti:
	- Stati Uniti orientali
    -   Stati Uniti occidentali
    -   Stati Uniti centro-settentrionali
    -   Stati Uniti centro-meridionali
    -   Europa settentrionale
    -   Europa occidentale
    -   Asia orientale
    -   Asia sudorientale

-   Se l'account di archiviazione si trova in un data center degli Stati Uniti, potrebbe essere necessario spedire le unità a un data center in un'area diversa, in quanto non tutti i data center attualmente supportano il servizio Importazione/Esportazione. È possibile incorrere in spese di uscita se il processo viene elaborato in un'area diversa da quella in cui si trova l'account di archiviazione.

-   Se l'account di archiviazione si trova in un data center europeo o asiatico, è necessario spedire l'unità in una delle aree supportate negli Stati Uniti e la spedizione deve partire dagli Stati Uniti. Il servizio Importazione/Esportazione copierà quindi i dati verso o dall'account di archiviazione in Europa o in Asia.
    -   Per un processo di importazione non sono previste spese di entrata per l'operazione di copia.
    -   Per un processo di esportazione, sono previsti corrispettivi per i trasferimenti dei dati per la copia dei dati tra data center di Azure. Se, ad esempio, l'account di archiviazione si trova in Europa occidentale e si spedisce l'unità al data center degli Stati Uniti orientali, si incorrerà in spese di uscita per lo spostamento dei dati dall'Europa occidentale agli Stati Uniti orientali per esportarli.

	<div class="dev-callout">
	<strong>Importante</strong>
	<p>I data center di Azure non possono ricevere unità spedite da località al di fuori degli Stati Uniti e rifiuteranno la distribuzione di tali pacchetti.</p>
	</div>


**È possibile acquistare da Microsoft unità per i processi di importazione/esportazione?**

-   No. Sarà necessario spedire le unità per i processi sia di importazione che di esportazione.

### Sicurezza

**La crittografia Bitlocker è un requisito obbligatorio?**

-   Sì. Tutte le unità devono essere crittografate con una chiave BitLocker.

**Microsoft formatta le unità prima di restituirle?**

-   No. Tutte le unità devono essere preparate per BitLocker.

