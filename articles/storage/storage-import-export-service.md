<properties
	pageTitle="Uso del servizio di importazione/esportazione per trasferire dati nell'archiviazione BLOB | Microsoft Azure"
	description="Informazioni su come creare ed esportare processi nel portale classico di Azure per trasferire dati all'archiviazione BLOB."
	authors="renashahmsft"
	manager="aungoo"
	editor="tysonn"
	services="storage"
	documentationCenter=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="renash"/>


# Usare il servizio di importazione/esportazione di Microsoft Azure per trasferire i dati nell'archivio Blob

## Panoramica

È possibile usare il servizio di Importazione/Esportazione di Microsoft Azure per trasferire grandi quantità di dati di file nell'archiviazione BLOB di Azure in situazioni in cui il caricamento in rete è eccessivamente costoso o non è realizzabile. È inoltre possibile usare il servizio Importazione/Esportazione per trasferire nelle installazioni locali, nel tempo previsto e in modo conveniente, grandi quantità di dati residenti nell'archiviazione BLOB.

Per trasferire un set di grandi dimensioni di dati di file nell'archiviazione BLOB, è possibile inviare uno o più dischi rigidi contenenti tali dati a un data center di Azure, dove i dati verranno caricati nell'account di archiviazione. Analogamente, per esportare i dati dall'archiviazione BLOB, è possibile inviare dischi rigidi vuoti a un data center di Azure, dove i dati BLOB verranno copiati dall'account di archiviazione ai dischi rigidi e quindi restituiti all'utente. Prima di inviare un'unità contenente i dati, sarà opportuno crittografarli. I dati, dopo essere stati esportati dal servizio Importazione/Esportazione per l'invio all'utente, verranno crittografati anche prima della spedizione.

È possibile creare e gestire i processi di importazione ed esportazione in due modi:

- Usando il [portale di Azure classico](https://manage.windowsazure.com).
- Utilizzando un'interfaccia REST per il servizio.

In questo articolo viene fornita una panoramica del servizio Importazione/Esportazione e viene descritto come utilizzare il portale di Azure classico per lavorare con il servizio Importazione/Esportazione. Per informazioni sull'API REST, vedere il [materiale di riferimento dell'API REST del servizio di importazione/esportazione dell'archiviazione](http://go.microsoft.com/fwlink/?LinkID=329099).

## Introduzione al servizio Importazione/Esportazione ##

Per iniziare il processo di importazione o di esportazione dall'archiviazione BLOB, creare innanzitutto un *processo*, che potrà essere un *processo di importazione* o un *processo di esportazione*:

- Creare un processo di importazione quando si desidera trasferire i dati locali nei BLOB nell'account di archiviazione di Azure.
- Creare un processo di esportazione quando si desidera trasferire su dischi rigidi spediti all'utente i dati attualmente archiviati come BLOB nell'account di archiviazione.

Quando si crea un processo, si notifica al servizio Importazione/Esportazione che si spedirà uno o più dischi rigidi a un data center di Azure. Per un processo di importazione, si spediranno dischi rigidi contenenti dati di file. Per un processo di esportazione, si spediranno dischi rigidi vuoti.

Per preparare l'unità per la spedizione per un processo di importazione, eseguire lo **strumento di importazione/esportazione di Microsoft Azure** che semplifica la copia dei dati nell'unità, la crittografia dei dati nell'unità con BitLocker e la generazione dei file journal dell'unità, discussi più avanti.

> [AZURE.NOTE] I dati sull'unità devono essere crittografati mediante Crittografia unità BitLocker, che protegge i dati mentre sono in transito. Per un processo di esportazione, il servizio Importazione/Esportazione crittograferà i dati prima della spedizione all'utente.

Quando si crea un processo di importazione o un processo di esportazione, sarà inoltre necessario l'*ID unità*, che è il numero di serie assegnato dal produttore dell'unità a un disco rigido specifico. L'ID unità è visibile all'esterno dell'unità.

### Requisiti e ambito

1.	**Sottoscrizione e account di archiviazione:** per usare il servizio Importazione/Esportazione, è necessario disporre di una sottoscrizione di Azure esistente e di uno o più account di archiviazione classici. Ogni processo può essere usato per trasferire dati da o verso un solo account di archiviazione classico. In altre parole, un processo non può estendersi tra più account di archiviazione. Per informazioni sulla creazione di un nuovo account di archiviazione, vedere [Come creare un account di archiviazione](storage-create-storage-account.md#create-a-storage-account). 

  > [AZURE.NOTE] Gli account di archiviazione che usano il modello di Azure Resource Manager (ARM) non sono ancora supportati.

2.	**Dischi rigidi:** con il servizio di importazione/esportazione sono supportati solo i dischi rigidi interni SATA II/III da 3,5 pollici. Sono supportati dischi rigidi fino a 8 TB. Per i processi di importazione, verrà elaborato solo il primo volume di dati sull'unità. Il volume di dati deve essere formattato con NTFS. È possibile collegare un disco SATA II/III esternamente alla maggior parte dei computer mediante una scheda USB SATA esterna II/III.
3.	**Crittografia BitLocker: **tutti i dati archiviati sui dischi rigidi devono essere crittografati mediante BitLocker con le chiavi di crittografia protette con password numeriche.
4.	**Destinazioni di archiviazione BLOB:** i dati possono essere caricati o scaricati da BLOB in blocchi e da BLOB di pagine.
5.	**Numero di processi: **un cliente può disporre di un massimo di 20 processi attivi per ogni account di archiviazione.
6.	**Dimensione massima di un processo:** la dimensione di un processo è determinata dalla capacità dei dischi rigidi usati e dalla quantità massima di dati che possono essere archiviati in un account di archiviazione. Ogni processo non può contenere più di 10 dischi rigidi.
7.  **Sistemi operativi supportati:** i clienti possono usare uno dei seguenti sistemi operativi a 64 bit per preparare il disco rigido tramite lo strumento Importazione/Esportazione di Azure prima della spedizione ad Azure: Windows 7, Windows 8, Windows 8.1, Windows 10*, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2.  

  > [AZURE.IMPORTANT]
  >  
  >  - Se per preparare il disco rigido si usa un computer con Windows 10, scaricare la versione più recente dello strumento Importazione/Esportazione di Azure.
  >  
  >  - I dischi rigidi esterni forniti con un adattatore USB incorporato non sono supportati da questo servizio. Non preparare un disco rigido esterno. Il disco all'interno del casing esterno non può essere utilizzato anche per l'importazione di dati. Utilizzare un'unità disco rigido **interna** da 3,5" SATA II/III. Se il disco SATA non può essere connesso direttamente al computer, utilizzare un SATA esterno per adattatore USB. Vedere l'elenco degli adattatori consigliati nella sezione Domande frequenti.

## Creazione di un processo di importazione nel portale classico##

Creare un processo di importazione per notificare al servizio Importazione/Esportazione che si spedirà al data center una o più unità contenenti dati da importare nell'account di archiviazione.

### Preparare le unità

Prima di creare un processo di importazione, preparare le unità con lo strumento Importazione/Esportazione di Azure. Per altri dettagli sull'uso dello strumento Importazione/Esportazione di Azure, vedere il [materiale di riferimento dello strumento Importazione/Esportazione di Azure](http://go.microsoft.com/fwlink/?LinkId=329032). È possibile scaricare lo [strumento Importazione/Esportazione di Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) come pacchetto autonomo.

Per preparare le unità, seguire questi tre passaggi:

1.	Determinare i dati da importare e il numero di unità necessarie.
2.	Identificare i BLOB di destinazione per i dati nell'archiviazione BLOB.
3.	Usare lo strumento Importazione/Esportazione di Azure per copiare i dati in uno o più dischi rigidi.

Lo strumento Importazione/Esportazione di Azure genera un *file journal dell'unità* per ogni unità preparata. Il file journal dell'unità viene archiviato nel computer locale, non nell'unità stessa. Il file journal verrà caricato quando si creerà il processo di importazione. Un file journal di unità include l'ID unità e la chiave BitLocker, nonché altre informazioni sull'unità.

### Creare il processo di importazione

1.	Dopo aver preparato l'unità, passare all'account di archiviazione nel [portale classico](https://manage.windowsazure.com) e visualizzare il dashboard. In **Quick Glance** fare clic su **Create an Import Job**.

2.	Nel passaggio 1 della procedura guidata indicare di aver preparato l'unità e che il file journal dell'unità è disponibile.

3.	Nel passaggio 2 fornire le informazioni di contatto per la persona responsabile di questo processo di importazione. Per salvare i dati del log dettagliato per il processo di importazione, selezionare l'opzione **Salva log dettagliato nel contenitore BLOB 'waimportexport'**.

4.	Nel passaggio 3 caricare i file journal dell'unità ottenuti durante il passaggio di preparazione dell'unità. È necessario caricare un file per ogni unità preparata.

	![Creare il processo di importazione - Passaggio 3][import-job-03]

5.	Nel passaggio 4 immettere un nome descrittivo per il processo di importazione. Il nome immesso può contenere solo lettere minuscole, numeri, trattini e caratteri di sottolineatura, deve iniziare con una lettera e non può contenere spazi. Il nome scelto verrà usato per tenere traccia dei processi mentre sono in corso e dopo essere stati completati.

	Selezionare quindi l'area geografica del data center dall'elenco. L'area geografica del data center indica il data center e l'indirizzo per la spedizione del pacchetto. Per altre informazioni, vedere le domande frequenti più avanti.

6. 	Nel passaggio 5 selezionare il vettore di ritorno dall'elenco, quindi immettere il numero di account del vettore. Microsoft usa questo account per inviare le unità al cliente al completamento del processo di importazione.

	Se si dispone del numero di spedizione, selezionare il vettore di consegna dall'elenco, quindi immettere il numero di spedizione.

	Se non si dispone ancora di un numero di spedizione, scegliere **I will provide my shipping information for this import job once I have shipped my package**, quindi completare il processo di importazione.

7. Per immettere il numero di spedizione dopo aver spedito il pacchetto, tornare nella pagina **Importazione/Esportazione** dell'account di archiviazione nel portale classico, selezionare il processo dall'elenco e scegliere **Informazioni sulla spedizione**. Nella procedura guidata, immettere il numero di spedizione nel passaggio 2.

	Se il numero di tracciabilità non viene aggiornato entro due settimane dalla creazione del processo, il processo scadrà.

	Se il processo si trova in stato di creazione, spedizione o trasferimento, è anche possibile aggiornare il numero di account del vettore nel passaggio 2 della procedura guidata. Quando il processo è in stato Packaging, non è possibile aggiornare il numero di account del vettore del processo.

## Creazione di un processo di esportazione nel portale classico##

Creare un processo di esportazione per notificare al servizio Importazione/Esportazione che si spedirà una o più unità vuote al data center, in modo che i dati possano essere esportati dall'account di archiviazione alle unità e le unità possano quindi essere spedite all'utente.

1. 	Per creare un processo di esportazione, passare all'account di archiviazione nel [portale classico](https://manage.windowsazure.com) e visualizzare il dashboard. In **Quick Glance**, fare clic su **Crea un processo di esportazione** e continuare la procedura guidata.

2. 	Nel passaggio 2 fornire le informazioni di contatto per la persona responsabile di questo processo di esportazione. Per salvare i dati del log dettagliato per il processo di esportazione, selezionare l'opzione **Salva log dettagliato nel contenitore BLOB 'waimportexport'**.

3.	Nel passaggio 3 specificare i dati BLOB da esportare dall'account di archiviazione a una o più unità vuote. È possibile scegliere di esportare tutti i dati Blob nell'account di archiviazione o specificare singoli Blob o set di Blob.

	![Creare il processo di esportazione - Passaggio 3][export-job-03]

	- Per specificare un BLOB da esportare, utilizzare il selettore **Equal To** e specificare il percorso relativo del BLOB, iniziando con il nome del contenitore. Utilizzare *$root* per specificare il contenitore radice.
	- Per specificare tutti i BLOB che iniziano con un prefisso, utilizzare il selettore **Starts With** e specificare il prefisso, iniziando con una barra "/". Il prefisso può essere il prefisso del nome del contenitore, il nome del contenitore completo o il nome del contenitore completo seguito dal prefisso del nome BLOB.

	Nella tabella vengono indicati esempi di percorsi BLOB validi:

	Selettore|Percorso BLOB|Descrizione
	---|---|---
	Starts With|/|Esporta tutti i BLOB nell'account di archiviazione
	Starts With|/$root/|Esporta tutti i BLOB nel contenitore radice
	Starts With|/book|Esporta tutti i BLOB in qualsiasi contenitore che inizia con il prefisso**book**
	Starts With|/music/|Esporta tutti i BLOB nel contenitore **music**
	Starts With|/music/love|Esporta nel contenitore **music** tutti i BLOB che iniziano con il prefisso **love**
	Equal To|$root/logo.bmp|Esporta il BLOB **logo.bmp** nel contenitore radice
	Equal To|videos/story.mp4|Esporta il BLOB **story.mp4** nel contenitore **video**


4.	Nel passaggio 4 immettere un nome descrittivo per il processo di esportazione. Il nome immesso può contenere solo lettere minuscole, numeri, trattini e caratteri di sottolineatura, deve iniziare con una lettera e non può contenere spazi.

	L'area del data center indicherà il data center a cui è necessario spedire il pacchetto. Per altre informazioni, vedere le domande frequenti più avanti.

5. 	Nel passaggio 5 selezionare il vettore di ritorno dall'elenco, quindi immettere il numero di account del vettore. Microsoft usa questo account per inviare le unità al cliente al completamento del processo di esportazione.

	Se si dispone del numero di spedizione, selezionare il vettore di consegna dall'elenco, quindi immettere il numero di spedizione.

	Se non si dispone ancora di un numero di spedizione, scegliere **Fornirò le informazioni sulla spedizione per questo processo di esportazione dopo aver spedito il pacchetto**, quindi completare il processo di esportazione.

6. Per immettere il numero di spedizione dopo aver spedito il pacchetto, tornare nella pagina **Importazione/Esportazione** dell'account di archiviazione nel portale classico, selezionare il processo dall'elenco e scegliere **Informazioni sulla spedizione**. Nella procedura guidata, immettere il numero di spedizione nel passaggio 2.

	Se il numero di tracciabilità non viene aggiornato entro due settimane dalla creazione del processo, il processo scadrà.

	Se il processo si trova in stato di creazione, spedizione o trasferimento, è anche possibile aggiornare il numero di account del vettore nel passaggio 2 della procedura guidata. Quando il processo è in stato Packaging, non è possibile aggiornare il numero di account del vettore del processo.

> [AZURE.NOTE] Se il blob da esportare è in uso al momento della copia sul disco rigido, servizio di Importazione/Esportazione di Azure creare uno snapshot del blob e copiare lo snapshot.

## Tenere traccia dello stato del processo nel portale classico##

È possibile tenere traccia dello stato dei processo di importazione o esportazione dal portale classico. Passare all'account di archiviazione nel portale classico e fare clic sulla scheda **Importazione/Esportazione**. Nella pagina verrà visualizzato un elenco dei processi. È possibile filtrare l'elenco per stato processo, nome processo, tipo di processo o numero di spedizione.

Nella tabella viene descritto il significato di ogni designazione dello stato del processo:

Stato processo|Descrizione
---|---
Creating|Il processo è stato creato, ma non sono ancora state specificate le informazioni per la spedizione.
Shipping|Il processo è stato creato e sono state specificate le informazioni per la spedizione.
Transferring|È in corso il trasferimento dei dati dal disco rigido (per un processo di importazione) o al disco rigido (per un processo di esportazione).
Packaging|Il trasferimento dei vostri dati è stato completato ed è in corso la preparazione del disco rigido per la spedizione all'utente.
Complete|Il disco rigido è stato spedito all'utente.


## Visualizzazione delle chiavi BitLocker per un processo di esportazione ##

Per i processi di esportazione, è possibile visualizzare e copiare le chiavi BitLocker generate dal servizio per l'unità, in modo che sia possibile decrittografare i dati esportati dopo aver ricevuto l'unità dal data center di Azure. Passare all'account di archiviazione nel portale classico e fare clic sulla scheda **Importazione/Esportazione**. Selezionare il processo di esportazione nell'elenco e fare clic sul pulsante **View Keys**. Le chiavi BitLocker vengono visualizzate come illustrato:

![Visualizzare le chiavi BitLocker per il processo di esportazione][export-job-bitlocker-keys]

## Domande frequenti ##

### Generale

**Qual è il prezzo per il servizio Importazione/Esportazione?**

- Per informazioni sui prezzi, vedere [Prezzi di Importazione/Esportazione](http://go.microsoft.com/fwlink/?LinkId=329033).

**Quanto tempo richiederà l'importazione o l'esportazione dei dati?**

- Il tempo necessario per spedire i dischi più alcune ore per ogni TB di dati da copiare.

**Quali tipi di interfaccia sono supportati?**

- Il servizio di importazione/esportazione supporta dischi rigidi interni SATA II/III da 3,5 pollici (HDD). È possibile usare i convertitori seguenti per trasferire dati nei dispositivi USB in SATA prima della spedizione:
	- Anker 68UPSATAA-02BU
	- Anker 68UPSHHDS-BU
	- Startech SATADOCK22UE

> [AZURE.NOTE] Se si ha un convertitore non incluso nell'elenco precedente, prima di acquistare un convertitore supportato è possibile provare a eseguire lo strumento Importazione/Esportazione di Azure con il convertitore per preparare l'unità e vedere se funziona.

- L’unità disco rigido esterno con un adattatore USB incorporato non è supportata.

**Che cosa si deve fare, se si desidera importare o esportare più di 10 unità?**

- Un unico processo di importazione o esportazione può fare riferimento solo a 10 unità in un singolo processo del servizio di importazione/esportazione. Se si desidera spedire più di 10 unità, è possibile creare più processi.

**Cosa accade se si invia per errore un disco rigido non conforme ai requisiti supportati?**

- Il data center di Azure restituirà all'utente l'unità non conforme ai requisiti supportati. Se solo alcune delle unità nel pacchetto soddisfano i requisiti per il supporto, tali unità verranno elaborate e le unità che non soddisfano i requisiti verranno restituite all'utente.

### Gestione dei processi di importazione/esportazione

**Che cosa accade ai processi di importazione ed esportazione se si elimina l'account di archiviazione di Azure?**

- Quando si elimina l'account di archiviazione, insieme all'account vengono eliminati tutti i processi di importazione/esportazione di Azure.  

**È possibile annullare il processo?**

- È possibile annullare un processo quando lo stato è Creating o Shipping.

**Per quanto tempo è possibile visualizzare lo stato dei processi completati nel portale classico?**

- È possibile visualizzare lo stato per i processi completati fino a 90 giorni. Tutti i processi completi vengono eliminati dopo 90 giorni.

**La crittografia Bitlocker è un requisito obbligatorio?**

- Sì. Tutte le unità devono essere crittografate con una chiave BitLocker.

**Formatti le unità prima di restituirle?**

- No. Tutte le unità devono essere preparate per BitLocker.

**È necessario eseguire la preparazione del disco durante la creazione di un processo di esportazione?**
- No, ma alcuni controlli preliminari sono consigliati Verificare il numero di dischi richiesti tramite il comando PreviewExport dello strumento Importazione/Esportazione di Azure. Per altre informazioni, vedere [Visualizzazione in anteprima dell'utilizzo del disco per un processo di esportazione](https://msdn.microsoft.com/library/azure/dn722414.aspx). Consente di visualizzare l'anteprima dell'utilizzo di unità per il Blob è selezionato, in base alla dimensione delle unità che si desidera utilizzare. Controllare inoltre che possono leggere/ scrivere sul disco rigido che verranno inviati per il processo di esportazione.

### Spedizione

**Quali servizi di corriere sono supportati?**

- Per le aree geografiche negli Stati Uniti e in Europa è supportato solo [Federal Express](http://www.fedex.com/us/oadr/) (FedEx). Tutti i pacchetti vengono restituiti tramite FedEx Ground o FedEx International Economy.

- Per le aree geografiche in Asia è supportato solo [DHL](http://www.dhl.com/). Tutti i pacchetti vengono restituiti tramite DHL Express Worldwide.

	> [AZURE.IMPORTANT] È necessario fornire il numero di spedizione per il servizio Importazione/Esportazione di Azure o il processo non potrà essere elaborato.

**Esistono costi associati alla spedizione di ritorno?**

- Microsoft usa il numero di account del vettore fornito al momento della creazione del processo per spedire le unità all'indirizzo di restituzione dal data center. Assicurarsi di fornire un numero di account di un vettore supportato per l'area geografica del data center. È possibile creare un account del vettore [FedEx](http://www.fedex.com/us/oadr/) (per Stati Uniti ed Europa) o [DHL](http://www.dhl.com/) (Asia), se non lo si ha già.

- La tariffa della spedizione di ritorno viene addebitata sull'account del vettore e dipende dal vettore stesso.

**Da dove e verso dove è possibile spedire i dati?**

- Il servizio di importazione/esportazione supporta l'importazione e l'esportazione dei dati dagli account di archiviazione nelle seguenti aree geografiche:
	- Stati Uniti orientali
	- Stati Uniti occidentali
	- Stati Uniti centro-settentrionali
	- Stati Uniti centro-meridionali
	- Europa settentrionale
	- Europa occidentale
	- Asia orientale
	- Asia sudorientale

- Viene fornito un indirizzo di spedizione nell'area geografica dell'account di archiviazione. Ad esempio, se il cliente vive negli Stati Uniti e l'account di archiviazione si trova nel data center Europa occidentale, viene fornito un indirizzo di spedizione europeo per l'invio delle unità.

	> [AZURE.IMPORTANT] È possibile che i supporti fisici spediti debbano passare i confini internazionali. È responsabilità del cliente assicurarsi che i supporti fisici e i dati siano importati e/o esportati conformemente alle leggi vigenti. Prima di spedire i supporti fisici, rivolgersi ai consulenti per verificare che i supporti e i dati possano essere spediti ai data center specificati in modo conforme alle normative. Ciò assicura che la spedizione raggiunga Microsoft in modo tempestivo.

- Durante la spedizione dei pacchetti, seguire le condizioni di [Condizioni per l’Uso dei Servizi di Microsoft Azure](https://azure.microsoft.com/support/legal/services-terms/).

**È possibile acquistare da Microsoft unità per i processi di importazione/esportazione?**

- 	No. Sarà necessario spedire le unità per i processi sia di importazione che di esportazione.

**Cosa va incluso nel pacchetto?**

- Spedire solo i dischi rigidi. Non inserire oggetti come cavi di alimentazione o cavi USB.

**Perché lo stato del processo nel portale classico risulta essere *Spedizione* quando il sito Web del vettore indica che il pacchetto è stato consegnato?**

- Lo stato indicato dal portale passa da *Spedizione* a *Trasferimento* quando inizia l'elaborazione dell'unità. Se l'unità ha raggiunto la struttura, ma l'elaborazione non è iniziata, lo stato del processo verrà visualizzato come *Spedizione*.

## Vedere anche

[Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md)


[import-job-03]: ./media/storage-import-export-service-classic-portal/import-job-03.png
[export-job-03]: ./media/storage-import-export-service-classic-portal/export-job-03.png
[export-job-bitlocker-keys]: ./media/storage-import-export-service-classic-portal/export-job-bitlocker-keys.png

<!---HONumber=AcomDC_0302_2016-->