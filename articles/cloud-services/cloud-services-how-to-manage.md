<properties 
	pageTitle="Come gestire un servizio cloud - Azure" 
	description="Informazioni su come gestire i servizi cloud nel portale di Azure." 
	services="cloud-services" 
	documentationCenter="" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/01/2015"
	ms.author="adegeo"/>





# Come gestire i servizi cloud

> [AZURE.SELECTOR]
- [Azure Portal](cloud-services-how-to-manage.md)
- [Azure Preview Portal](cloud-services-how-to-manage-portal.md)

Nell'area **Servizi cloud** del portale di Azure è possibile aggiornare un ruolo di servizio o una distribuzione, convertire una distribuzione di gestione temporanea in una distribuzione di produzione, collegare risorse al servizio cloud per visualizzare le dipendenze delle risorse e scalare le risorse insieme, nonché eliminare un servizio cloud o una distribuzione.


## Procedura: Aggiornare un ruolo o una distribuzione del servizio cloud

Se è necessario aggiornare il codice dell'applicazione per il servizio cloud, usare **Update** nel dashboard, la pagina **Cloud services** o la pagina **Instances**. È possibile aggiornare un singolo ruolo o tutti i ruoli. Sarà necessario caricare un nuovo pacchetto del servizio e un nuovo file di configurazione del servizio.

1. Nel dashboard [portale di Azure](https://manage.windowsazure.com/) fare clic su **Aggiorna** nella pagina **Servizi cloud** o nella pagina **Istanze**.

	![UpdateDeployment](./media/cloud-services-how-to-manage/CloudServices_UpdateDeployment.png)

2. In **Etichetta distribuzione**, immettere un nome per identificare la distribuzione (ad esempio, mycloudservice4). L'etichetta di distribuzione è disponibile nella **Guida introduttiva** nel dashboard.

3. In **Pacchetto**, usare **Sfoglia** per caricare il file del pacchetto di servizio (.cspkg).

4. In **Configurazione**, usare **Sfoglia** per caricare il file di configurazione del servizio (.cscfg).

5. In **Role** selezionare **All** se si desidera aggiornare tutti i ruoli nel servizio cloud. Per eseguire l'aggiornamento di un singolo ruolo, selezionare il ruolo da aggiornare. Anche se si seleziona un ruolo specifico per l'aggiornamento, gli aggiornamenti nel file di configurazione del servizio vengono applicati a tutti i ruoli.

6. Se l'aggiornamento cambia il numero o le dimensioni dei ruoli, selezionare la casella di controllo **Consenti aggiornamento se cambiano le dimensioni o il numero dei ruoli** per consentire all'aggiornamento di proseguire.

	Tenere presente che se si modificano le dimensioni di un ruolo, ovvero le dimensioni di una macchina virtuale che ospita un'istanza del ruolo, o il numero dei ruoli, è necessario ricreare l'immagine di ogni istanza del ruolo (macchina virtuale) e i dati locali andranno persi.

7. Se uno o più ruoli del servizio contengono una sola istanza del ruolo, selezionare la casella di controllo **Update even if one or more role contain a single instance** per consentire l'esecuzione dell'aggiornamento.

	Durante un aggiornamento del servizio cloud, Azure può garantire una percentuale di disponibilità del servizio pari solo al 99,95% se ogni ruolo contiene almeno due istanze del ruolo (macchine virtuali). In questo modo, una macchina virtuale può elaborare le richieste dei client mentre l'altra viene aggiornata.

8. Fare clic su **OK** (segno di spunta) per avviare l'aggiornamento del servizio.



## Procedura: Scambiare le distribuzioni per convertire una distribuzione di gestione temporanea in una distribuzione di produzione

Utilizzare **Swap** per convertire una distribuzione di gestione temporanea di un servizio cloud in una distribuzione di produzione. Quando si decide di distribuire una nuova versione di un servizio cloud, è possibile inserirla e testarla nell'ambiente di gestione temporanea del servizio cloud mentre i clienti usano la versione corrente in produzione. Quando la nuova versione è pronta per il passaggio in produzione, è possibile usare **Swap** per invertire gli URL di indirizzamento delle due distribuzioni.

È possibile scambiare le distribuzioni dalla pagina **Cloud Services** o dal dashboard.

1. Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Cloud Services**.

2. Nell'elenco dei servizi cloud fare clic sul servizio cloud per selezionarlo.

3. Fare clic su **Swap**.

	Verrà visualizzata la seguente richiesta di conferma.

	![Scambio di servizi cloud](./media/cloud-services-how-to-manage/CloudServices_Swap.png)

4. Dopo avere controllato le informazioni sulla distribuzione, fare clic su **Yes** per scambiare le distribuzioni.

	Lo scambio delle distribuzioni avviene rapidamente perché l'unico elemento che cambia è rappresentato dagli indirizzi IP virtuali (VIP) delle distribuzioni.

	Per ridurre i costi di calcolo, è possibile eliminare la distribuzione nell'ambiente di gestione temporanea dopo avere verificato che le prestazioni della nuova distribuzione di produzione corrispondano alle aspettative.

## Procedura: Collegare una risorsa a un servizio cloud

Per visualizzare le dipendenze del servizio cloud da altre risorse, è possibile collegare un'istanza di database SQL di Azure o un account di archiviazione al servizio cloud. È possibile collegare o scollegare le risorse nella pagina **Risorse collegato** e monitorare quindi il relativo uso nel dashboard del servizio cloud. Se in un account di archiviazione collegato è attivato il monitoraggio, è possibile monitorare il totale delle richieste nel dashboard del servizio cloud.

Utilizzare **Link** per collegare un'istanza di database SQL nuova o esistente o un account di archiviazione al servizio cloud. È quindi possibile scalare il database insieme al ruolo del servizio cloud che lo usa nella pagina **Scale** (un account di archiviazione viene scalato automaticamente man mano che aumenta l'uso). Per altre informazioni, vedere [Come scalare un servizio cloud e le risorse collegate](cloud-services-how-to-scale.md).

È inoltre possibile monitorare, gestire e scalare il database nel nodo **Databases** del portale di gestione.

In questo senso, il "collegamento" di una risorsa non comporta la connessione dell'app alla risorsa. Se si crea un nuovo database mediante **Link**, sarà necessario aggiungere le stringhe di connessione al codice dell'applicazione, quindi aggiornare il servizio cloud. Sarà anche necessario aggiungere le stringhe di connessione se l'app usa risorse in un account di archiviazione collegato.

Nella procedura seguente viene descritto come collegare una nuova istanza di database SQL, distribuita su un nuovo server di database SQL, a un servizio cloud.

### Per collegare un'istanza di database SQL a un servizio cloud

1. Nel [portale di gestione](http://manage.windowsazure.com/) fare clic su **Cloud Services**. Quindi fare clic sul nome del servizio cloud per aprire il dashboard.

2. Fare clic su **Linked Resources**.

	Verrà visualizzata la pagina **Linked Resources**.

	![LinkedResourcesPage](./media/cloud-services-how-to-manage/CloudServices_LinkedResourcesPage.png)

3. Fare clic su **Link a Resource** o **Link**.

	Verrà avviata la procedura guidata **Link Resource**.

	![Link Page1](./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkPage1.png)

4. Fare clic su **Create a new resource** o **Link an existing resource**.

5. Scegliere il tipo di risorsa da collegare. Nel [portale di gestione](http://manage.windowsazure.com/) fare clic su **SQL Database** (il portale di gestione in anteprima non supporta il collegamento di un account di archiviazione a un servizio cloud).

6. Per completare la configurazione del database, seguire le istruzioni nella guida per l'area **SQL Databases** del portale di gestione.

	È possibile seguire l'avanzamento dell'operazione di collegamento nell'area dei messaggi.

	![Avanzamento dell'operazione di collegamento](./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkProgress.png)

	Terminato il collegamento, è possibile monitorare lo stato della risorsa collegata nel dashboard del servizio cloud. Per informazioni sulla scalabilità di un database SQL collegato, vedere [Come scalare un servizio cloud e le risorse collegate](cloud-services-how-to-scale.md).

### Per scollegare una risorsa collegata

1. Nel [portale di gestione](http://manage.windowsazure.com/) fare clic su **Cloud Services**. Quindi fare clic sul nome del servizio cloud per aprire il dashboard.

2. Fare clic su **Linked Resources** e selezionare la risorsa.

3. Fare clic su **Unlink**. Fare quindi clic su**Yes** alla richiesta di conferma.

	Lo scollegamento di un database SQL non ha alcun effetto sul database o sulle connessioni dell'applicazione al database. È comunque possibile gestire il database nell'area **SQL Databases** del portale di gestione.



## Procedura: Eliminare le distribuzioni e un servizio cloud

Per eliminare un servizio cloud è necessario prima eliminare tutte le distribuzioni esistenti.

Per ridurre i costi di calcolo, è possibile eliminare la distribuzione di gestione temporanea dopo avere verificato che la distribuzione di produzione funzioni nel modo previsto. I costi di calcolo per le istanze del ruolo vengono addebitati anche se il servizio cloud non è in esecuzione.

Per eliminare una distribuzione o il servizio cloud, attenersi alla procedura seguente.

1. Nel [portale di gestione](http://manage.windowsazure.com/) fare clic su **Cloud Services**.

2. Selezionare il servizio cloud e fare clic su **Delete** (per selezionare un servizio cloud senza aprire il dashboard, fare clic in un punto qualsiasi tranne che sul nome nella voce del servizio cloud).

	Se è presente una distribuzione di gestione temporanea o di produzione, nella parte inferiore della finestra sarà visualizzato un menu analogo al seguente. Prima di eliminare il servizio cloud, è necessario eliminare le eventuali distribuzioni esistenti.

	![Menu Delete](./media/cloud-services-how-to-manage/CloudServices_DeleteMenu.png)


3. Per eliminare una distribuzione, fare clic su **Delete production deployment** o **Delete staging deployment**. Quindi, alla richiesta di conferma fare clic su **Yes**.

4. Se si intende eliminare il servizio cloud, ripetere il passaggio 3, se necessario, per eliminare le altre distribuzioni.

5. Per eliminare il servizio cloud fare clic su **Delete cloud service**. Quindi, alla richiesta di conferma fare clic su **Yes**.

> [AZURE.NOTE]Se per il servizio cloud è configurato il monitoraggio dettagliato, i dati di monitoraggio dall'account di archiviazione non vengono eliminati quando si elimina il servizio cloud. I dati dovranno essere eliminati manualmente. Per informazioni sull'ubicazione delle tabelle delle metriche, vedere "Procedura: Accedere ai dati di monitoraggio dettagliati all'esterno del portale di gestione" in <a href="http://azure.microsoft.com/documentation/articles/cloud-services-how-to-monitor/">Come monitorare i servizi cloud</a>.

 

<!---HONumber=July15_HO3-->