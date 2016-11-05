---
title: Attività comuni di gestione di servizi cloud | Microsoft Docs
description: Informazioni su come gestire i servizi cloud nel portale di Azure. Questi esempi utilizzano il portale di Azure.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''

ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2016
ms.author: adegeo

---
# Come gestire i servizi cloud
> [!div class="op_single_selector"]
> * [Portale di Azure](cloud-services-how-to-manage-portal.md)
> * [Portale di Azure classico](cloud-services-how-to-manage.md)
> 
> 

Il servizio cloud viene gestito nell'area **Servizi cloud (classico)** del portale di Azure. Questo articolo descrive alcune azioni comuni da eseguire durante la gestione dei servizi cloud. Sono incluse l'aggiornamento, l'eliminazione, il ridimensionamento e l'innalzamento di livello di una pre-distribuzione in produzione.

Altre informazioni sul ridimensionamento del servizio cloud sono disponibili [qui](cloud-services-how-to-scale-portal.md).

## Procedura: Aggiornare un ruolo o una distribuzione del servizio cloud
Se è necessario aggiornare il codice dell'applicazione per il servizio cloud, utilizzare **Aggiorna** nel blade del servizio cloud. È possibile aggiornare un singolo ruolo o tutti i ruoli. Per eseguire l'aggiornamento, è possibile caricare un nuovo pacchetto del servizio o un nuovo file di configurazione del servizio.

1. Nel [portale di Azure][portale di Azure], selezionare il servizio cloud che si desidera aggiornare. Questo passaggio consente di aprire il pannello dell'istanza del servizio cloud.
2. Nel blade, scegliere il pulsante **Aggiorna**.
   
    ![Pulsante Aggiorna](./media/cloud-services-how-to-manage-portal/update-button.png)  
3. Aggiornare la distribuzione con un nuovo file di pacchetto di servizio (.cspkg) e il file di configurazione del servizio (.cscfg).
   
    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)  
4. **Facoltativamente** aggiornare l'etichetta di distribuzione e l'account di archiviazione.
5. Se uno o più ruoli contengono una sola istanza del ruolo, selezionare la casella di controllo **Distribuisci anche se uno o più ruoli contengono una singola istanza** per abilitare l'esecuzione dell'aggiornamento.
   
    Durante un aggiornamento del servizio cloud, Azure può garantire una percentuale di disponibilità del servizio pari solo al 99,95% se ogni ruolo contiene almeno due istanze del ruolo (macchine virtuali). Con sue istanze del ruolo, una macchina virtuale elaborerà le richieste dei client mentre l'altra viene aggiornata.
6. Selezionare **Avvia distribuzione** per applicare l'aggiornamento al termine del caricamento del pacchetto.
7. Fare clic su **OK** per iniziare l'aggiornamento del servizio.

## Procedura: Scambiare le distribuzioni per convertire una distribuzione di gestione temporanea in una distribuzione di produzione
Quando si decide di distribuire una nuova versione di un servizio cloud, è possibile eseguirne un'installazione di appoggio e testarla nell'ambiente di staging del servizio cloud. Usare **Scambia** per scambiare gli URL di indirizzamento delle due distribuzioni e alzare di livello la nuova versione in produzione.

È possibile scambiare le distribuzioni dalla pagina **Cloud Services** o dal dashboard.

1. Nel [portale di Azure][portale di Azure], selezionare il servizio cloud che si desidera aggiornare. Questo passaggio consente di aprire il pannello dell'istanza del servizio cloud.
2. Nel blade, scegliere il pulsante **Scambia**.
   
    ![Scambio di servizi cloud](./media/cloud-services-how-to-manage-portal/swap-button.png)
3. Verrà visualizzata la seguente richiesta di conferma.
   
    ![Scambio di servizi cloud](./media/cloud-services-how-to-manage-portal/swap-prompt.png)  
4. Dopo avere controllato le informazioni di distribuzione, fare clic su **OK** per scambiare le distribuzioni.
   
    Lo scambio delle distribuzioni avviene rapidamente perché l'unico elemento che cambia è rappresentato dagli indirizzi IP virtuali (VIP) delle distribuzioni.
   
    Per ridurre i costi di calcolo, è possibile eliminare la distribuzione di gestione di staging dopo avere verificato che la distribuzione di produzione funziona nel modo previsto.

## Procedura: Collegare una risorsa a un servizio cloud
Il portale di Azure non collega tra loro le risorse come nel portale di Azure classico. Distribuire invece risorse aggiuntive allo stesso gruppo di risorse usato dal servizio cloud.

## Procedura: Eliminare le distribuzioni e un servizio cloud
Per eliminare un servizio cloud è necessario prima eliminare tutte le distribuzioni esistenti.

Per ridurre i costi di calcolo, è possibile eliminare la distribuzione di gestione di staging dopo avere verificato che la distribuzione di produzione funziona nel modo previsto. Vengono addebitati i costi di calcolo per le istanze del ruolo distribuite che sono state arrestate.

Per eliminare una distribuzione o il servizio cloud, attenersi alla procedura seguente.

1. Nel [portale di Azure][portale di Azure], selezionare il servizio cloud che si desidera eliminare. Questo passaggio consente di aprire il pannello dell'istanza del servizio cloud.
2. Nel blade, scegliere il pulsante **Elimina**.
   
    ![Scambio di servizi cloud](./media/cloud-services-how-to-manage-portal/delete-button.png)
3. È possibile eliminare l'intero servizio cloud selezionando **Elimina il servizio cloud e le relative distribuzioni** o scegliere la **Distribuzione di produzione** o la **Distribuzione gestione temporanea**.
   
    ![Scambio di servizi cloud](./media/cloud-services-how-to-manage-portal/delete-blade.png)  
4. Scegliere il pulsante **Elimina** nella parte inferiore.
5. Per eliminare il servizio cloud fare clic su **Delete cloud service**. Quindi, alla richiesta di conferma fare clic su **Yes**.

> [!NOTE]
> Quando un servizio cloud viene eliminato e viene configurato il monitoraggio dettagliato, è necessario eliminare manualmente i dati dall'account di archiviazione. Per informazioni sull'ubicazione delle tabelle di metriche, vedere [questo articolo](cloud-services-how-to-monitor.md):
> 
> 

[portale di Azure]: https://portal.azure.com

## Passaggi successivi
* [Configurazione generale del servizio cloud](cloud-services-how-to-configure-portal.md).
* Procedura [distribuire un servizio cloud](cloud-services-how-to-create-deploy-portal.md).
* Configurare un [nome di dominio personalizzato](cloud-services-custom-domain-name-portal.md).
* Configurare i [certificati ssl](cloud-services-configure-ssl-certificate-portal.md).

<!---HONumber=AcomDC_0810_2016-->