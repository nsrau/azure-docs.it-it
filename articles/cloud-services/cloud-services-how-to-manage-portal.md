---
title: "Attività comuni di gestione di servizi cloud | Microsoft Docs"
description: Informazioni su come gestire i servizi cloud nel portale di Azure. Questi esempi utilizzano il portale di Azure.
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: cb218ad9-77d4-4149-83db-71159c00767e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: a06becda42541b22d3e090087dd96cd08c980f44
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2017
---
# <a name="how-to-manage-cloud-services"></a>Come gestire i servizi cloud
Nell'area **Servizi cloud** del portale di Azure è possibile aggiornare un ruolo di servizio o una distribuzione, convertire una distribuzione di gestione temporanea in una distribuzione di produzione, collegare risorse al servizio cloud per visualizzare le dipendenze delle risorse e scalare le risorse insieme, nonché eliminare un servizio cloud o una distribuzione.

Altre informazioni sul ridimensionamento del servizio cloud sono disponibili [qui](cloud-services-how-to-scale-portal.md).

## <a name="how-to-update-a-cloud-service-role-or-deployment"></a>Procedura: Aggiornare un ruolo o una distribuzione del servizio cloud
Se è necessario aggiornare il codice dell'applicazione per il servizio cloud, utilizzare **Aggiorna** nel blade del servizio cloud. È possibile aggiornare un singolo ruolo o tutti i ruoli. Per eseguire l'aggiornamento, è possibile caricare un nuovo pacchetto del servizio o un nuovo file di configurazione del servizio.

1. Nel [portale di Azure][Azure portal] selezionare il servizio cloud che si desidera aggiornare. Questo passaggio consente di aprire il pannello dell'istanza del servizio cloud.
2. Nel blade, scegliere il pulsante **Aggiorna** .

    ![Pulsante Aggiorna](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Aggiornare la distribuzione con un nuovo file di pacchetto di servizio (.cspkg) e il file di configurazione del servizio (.cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. **Facoltativamente** aggiornare l'etichetta di distribuzione e l'account di archiviazione.
5. Se uno o più ruoli contengono una sola istanza del ruolo, selezionare la casella di controllo **Distribuisci anche se uno o più ruoli contengono una singola istanza** per abilitare l'esecuzione dell'aggiornamento.

    Durante un aggiornamento del servizio cloud, Azure può garantire una percentuale di disponibilità del servizio pari solo al 99,95% se ogni ruolo contiene almeno due istanze del ruolo (macchine virtuali). Con due istanze del ruolo, una macchina virtuale elabora le richieste dei client mentre l'altra viene aggiornata.

6. Selezionare **Avvia distribuzione** per applicare l'aggiornamento al termine del caricamento del pacchetto.
7. Fare clic su **OK** per iniziare l'aggiornamento del servizio.

## <a name="how-to-swap-deployments-to-promote-a-staged-deployment-to-production"></a>Procedura: Scambiare le distribuzioni per convertire una distribuzione di gestione temporanea in una distribuzione di produzione
Quando si decide di distribuire una nuova versione di un servizio cloud, è possibile eseguirne un'installazione di appoggio e testarla nell'ambiente di staging del servizio cloud. Usare **Scambia** per scambiare gli URL di indirizzamento delle due distribuzioni e alzare di livello la nuova versione in produzione.

È possibile scambiare le distribuzioni dalla pagina **Cloud Services** o dal dashboard.

1. Nel [portale di Azure][Azure portal] selezionare il servizio cloud che si desidera aggiornare. Questo passaggio consente di aprire il pannello dell'istanza del servizio cloud.
2. Nel blade, scegliere il pulsante **Scambia** .

    ![Scambio di servizi cloud](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Verrà visualizzata la seguente richiesta di conferma.

    ![Scambio di servizi cloud](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Dopo avere controllato le informazioni di distribuzione, fare clic su **OK** per scambiare le distribuzioni.

    Lo scambio delle distribuzioni avviene rapidamente perché l'unico elemento che cambia è rappresentato dagli indirizzi IP virtuali (VIP) delle distribuzioni.

    Per ridurre i costi di calcolo, è possibile eliminare la distribuzione di gestione di staging dopo avere verificato che la distribuzione di produzione funziona nel modo previsto.

### <a name="common-questions-about-swapping-deployments"></a>Domande comuni sullo scambio di distribuzioni

**Quali sono i prerequisiti per lo scambio delle distribuzioni?**

Esistono due prerequisiti chiave per lo scambio corretto di distribuzioni:

- Se si desidera usare un indirizzo IP statico per lo slot di produzione, è necessario riservarne uno anche per lo slot di gestione temporanea. In caso contrario, lo scambio ha esito negativo.

- Tutte le istanze dei ruoli devono essere in esecuzione prima di poter eseguire lo scambio. È possibile controllare lo stato delle istanze nel pannello Panoramica del portale di Azure. In alternativa, è possibile usare il comando [Get-AzureRole](/powershell/module/azure/get-azurerole?view=azuresmps-3.7.0) in Windows PowerShell.

Si noti che anche gli aggiornamenti del sistema operativo guest e le operazioni di correzione del servizio possono ostacolare il corretto scambio delle distribuzioni. Per altre informazioni, vedere [Risolvere eventuali problemi di distribuzione dei servizi cloud](cloud-services-troubleshoot-deployment-problems.md).

**Uno scambio comporta un tempo di inattività per l'applicazione? Come gestire questa situazione?**

Come descritto nella sezione precedente, lo scambio di distribuzioni è in genere veloce perché è una semplice modifica della configurazione in Azure Load Balancer. In alcuni casi, tuttavia, può richiedere più di dieci secondi e causare errori di connessione temporanei. Per limitare l'impatto sui clienti, si consiglia di implementare la [logica di ripetizione dei tentativi nel client](../best-practices-retry-general.md).

## <a name="how-to-delete-deployments-and-a-cloud-service"></a>Procedura: Eliminare le distribuzioni e un servizio cloud
Per eliminare un servizio cloud è necessario prima eliminare tutte le distribuzioni esistenti.

Per ridurre i costi di calcolo, è possibile eliminare la distribuzione di gestione di staging dopo avere verificato che la distribuzione di produzione funziona nel modo previsto. Vengono addebitati i costi di calcolo per le istanze del ruolo distribuite che sono state arrestate.

Per eliminare una distribuzione o il servizio cloud, attenersi alla procedura seguente.

1. Nel [portale di Azure][Azure portal] selezionare il servizio cloud che si desidera eliminare. Questo passaggio consente di aprire il pannello dell'istanza del servizio cloud.
2. Nel blade, scegliere il pulsante **Elimina** .

    ![Scambio di servizi cloud](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. È possibile eliminare l'intero servizio cloud selezionando **Servizio cloud e relative distribuzioni** oppure scegliere la **Distribuzione di produzione** o la **Distribuzione di staging**.

    ![Scambio di servizi cloud](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Scegliere il pulsante **Elimina** nella parte inferiore.
5. Per eliminare il servizio cloud fare clic su **Delete cloud service**. Quindi, alla richiesta di conferma fare clic su **Yes**.

> [!NOTE]
> Quando un servizio cloud viene eliminato e viene configurato il monitoraggio dettagliato, è necessario eliminare manualmente i dati dall'account di archiviazione. Per informazioni sull'ubicazione delle tabelle di metriche, vedere [questo articolo](cloud-services-how-to-monitor.md) :


## <a name="how-to-find-more-information-about-failed-deployments"></a>Procedura: Trovare altre informazioni sulle distribuzioni non riuscite
Il pannello **Panoramica** comprende una barra di stato in alto. Quando si fa clic sulla barra, si apre un nuovo pannello che visualizza le informazioni sugli errori. Se la distribuzione non contiene errori, il pannello delle informazioni è vuoto.

![Scambio di servizi cloud](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Passaggi successivi
* [Configurazione generale del servizio cloud](cloud-services-how-to-configure-portal.md).
* Procedura [distribuire un servizio cloud](cloud-services-how-to-create-deploy-portal.md).
* Configurare un [nome di dominio personalizzato](cloud-services-custom-domain-name-portal.md).
* Configurare i [certificati ssl](cloud-services-configure-ssl-certificate-portal.md).
