<properties 
	pageTitle="Attività comuni di gestione di servizi cloud | Microsoft Azure" 
	description="Informazioni su come gestire i servizi cloud nel portale di Azure. Questi esempi utilizzano il portale di Azure." 
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
	ms.date="01/20/2016"
	ms.author="adegeo"/>


# Come gestire i servizi cloud

> [AZURE.SELECTOR]
- [Azure classic portal](cloud-services-how-to-manage.md)
- [Azure portal](cloud-services-how-to-manage-portal.md)

Nell'area **Servizi cloud** del portale di Azure è possibile aggiornare un ruolo di servizio o una distribuzione, convertire una distribuzione di gestione temporanea in una distribuzione di produzione, collegare risorse al servizio cloud per visualizzare le dipendenze delle risorse e scalare le risorse insieme, nonché eliminare un servizio cloud o una distribuzione.


## Procedura: Aggiornare un ruolo o una distribuzione del servizio cloud

Se è necessario aggiornare il codice dell'applicazione per il servizio cloud, utilizzare **Aggiorna** nel blade del servizio cloud. È possibile aggiornare un singolo ruolo o tutti i ruoli. Sarà necessario caricare un nuovo pacchetto del servizio e un nuovo file di configurazione del servizio.

1. Nel [portale di Azure][], selezionare il servizio cloud che si desidera aggiornare. Viene aperto il blade dell’istanza del servizio cloud.

2. Nel blade, scegliere il pulsante **Aggiorna**.

    ![Pulsante Aggiorna](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Aggiornare la distribuzione con un nuovo file di pacchetto di servizio (.cspkg) e il file di configurazione del servizio (.cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. **Facoltativamente** aggiornare l'etichetta di distribuzione e l'account di archiviazione.

5. Se l'aggiornamento cambia il numero o le dimensioni dei ruoli, selezionare la casella di controllo **Consenti aggiornamento se cambiano le dimensioni o il numero dei ruoli** per consentire all'aggiornamento di proseguire.

	>[AZURE.WARNING] Tenere presente che se si modificano le dimensioni di un ruolo, ovvero le dimensioni di una macchina virtuale che ospita un'istanza del ruolo, o il numero dei ruoli, è necessario ricreare l'immagine di ogni istanza del ruolo (macchina virtuale) e i dati locali andranno persi.

6. Se uno o più ruoli del servizio contengono una sola istanza del ruolo, selezionare la casella di controllo **Eseguire l'aggiornamento anche se uno o più ruoli contengono una sola istanza** per consentire l'esecuzione dell'aggiornamento.

	Durante un aggiornamento del servizio cloud, Azure può garantire una percentuale di disponibilità del servizio pari solo al 99,95% se ogni ruolo contiene almeno due istanze del ruolo (macchine virtuali). In questo modo, una macchina virtuale può elaborare le richieste dei client mentre l'altra viene aggiornata.

8. Fare clic su **OK** per iniziare l'aggiornamento del servizio.



## Procedura: Scambiare le distribuzioni per convertire una distribuzione di gestione temporanea in una distribuzione di produzione

Utilizzare **Swap** per convertire una distribuzione di gestione temporanea di un servizio cloud in una distribuzione di produzione. Quando si decide di distribuire una nuova versione di un servizio cloud, è possibile inserirla e testarla nell'ambiente di gestione temporanea del servizio cloud mentre i clienti usano la versione corrente in produzione. Quando la nuova versione è pronta per il passaggio in produzione, è possibile usare **Swap** per invertire gli URL di indirizzamento delle due distribuzioni.

È possibile scambiare le distribuzioni dalla pagina **Cloud Services** o dal dashboard.

1. Nel [portale di Azure][], selezionare il servizio cloud che si desidera aggiornare. Viene aperto il blade dell’istanza del servizio cloud.

2. Nel blade, scegliere il pulsante **Scambia**.

    ![Scambio di servizi cloud](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Verrà visualizzata la seguente richiesta di conferma.

	![Scambio di servizi cloud](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Dopo avere controllato le informazioni di distribuzione, fare clic su **OK** per scambiare le distribuzioni.

	Lo scambio delle distribuzioni avviene rapidamente perché l'unico elemento che cambia è rappresentato dagli indirizzi IP virtuali (VIP) delle distribuzioni.

	Per ridurre i costi di calcolo, è possibile eliminare la distribuzione nell'ambiente di gestione temporanea dopo avere verificato che le prestazioni della nuova distribuzione di produzione corrispondano alle aspettative.

## Procedura: Collegare una risorsa a un servizio cloud

Il portale di Azure non collega tra loro le risorse come nel portale di Azure classico. Pertanto, è necessario distribuire risorse aggiuntive allo stesso gruppo di risorse utilizzato dal servizio cloud.

## Procedura: Eliminare le distribuzioni e un servizio cloud

Per eliminare un servizio cloud è necessario prima eliminare tutte le distribuzioni esistenti.

Per ridurre i costi di calcolo, è possibile eliminare la distribuzione di gestione temporanea dopo avere verificato che la distribuzione di produzione funzioni nel modo previsto. I costi di calcolo per le istanze del ruolo vengono addebitati anche se il servizio cloud non è in esecuzione.

Per eliminare una distribuzione o il servizio cloud, attenersi alla procedura seguente.

1. Nel [portale di Azure][], selezionare il servizio cloud che si desidera eliminare. Viene aperto il blade dell’istanza del servizio cloud.

2. Nel blade, scegliere il pulsante **Elimina**.

    ![Scambio di servizi cloud](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. È possibile eliminare l'intero servizio cloud selezionando **Elimina il servizio cloud e le relative distribuzioni** o scegliere la **Distribuzione di produzione** o la **Distribuzione gestione temporanea**.

    ![Scambio di servizi cloud](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Scegliere il pulsante **Elimina** nella parte inferiore.

5. Per eliminare il servizio cloud fare clic su **Delete cloud service**. Quindi, alla richiesta di conferma fare clic su **Yes**.

> [AZURE.NOTE]
Se per il servizio cloud è configurato il monitoraggio dettagliato, i dati di monitoraggio dall'account di archiviazione non vengono eliminati quando si elimina il servizio cloud. I dati dovranno essere eliminati manualmente. Per informazioni sull'ubicazione delle tabelle di metriche, vedere [questo articolo](cloud-services-how-to-monitor.md):

[portale di Azure]: https://portal.azure.com

## Passaggi successivi

* [Configurazione generale del servizio cloud](cloud-services-how-to-configure-portal.md).
* Procedura [distribuire un servizio cloud](cloud-services-how-to-create-deploy-portal.md).
* Configurare un [nome di dominio personalizzato](cloud-services-custom-domain-name-portal.md).
* Configurare i [certificati ssl](cloud-services-configure-ssl-certificate-portal.md).

<!---HONumber=AcomDC_0128_2016-->