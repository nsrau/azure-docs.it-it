---
title: "Attività comuni di gestione di servizi cloud | Documentazione Microsoft"
description: Informazioni su come gestire Servizi cloud nel portale di Azure. Questi esempi utilizzano il portale di Azure.
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
ms.openlocfilehash: e60bf5c82e68d49abaa44d80ac9fafba2d8265da
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="manage-cloud-services-in-the-azure-portal"></a>Gestire Servizi cloud nel portale di Azure
Nell'area **Servizi cloud** del portale di Azure è possibile:

* Aggiornare un ruolo o una distribuzione del servizio.
* Alzare di livello una pre-distribuzione convertendola in una distribuzione di produzione.
* Collegare risorse al servizio cloud in modo da visualizzare le dipendenze delle risorse e ridimensionare le risorse tra loro.
* Eliminare un servizio cloud o una distribuzione.

Per altre informazioni sul ridimensionamento del servizio cloud, vedere [Come configurare la scalabilità automatica per un servizio cloud nel portale](cloud-services-how-to-scale-portal.md).

## <a name="update-a-cloud-service-role-or-deployment"></a>Aggiornare un ruolo o una distribuzione del servizio cloud
Se è necessario aggiornare il codice dell'applicazione per il servizio cloud, utilizzare **Aggiorna** nel blade del servizio cloud. È possibile aggiornare un singolo ruolo o tutti i ruoli. Per eseguire l'aggiornamento, è possibile caricare un nuovo pacchetto del servizio o un nuovo file di configurazione del servizio.

1. Nel [portale di Azure][Azure portal] selezionare il servizio cloud che si desidera aggiornare. Questo passaggio consente di aprire il pannello dell'istanza del servizio cloud.

2. Nel pannello selezionare **Aggiorna**.

    ![Pulsante Aggiorna](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Aggiornare la distribuzione con un nuovo file di pacchetto di servizio (.cspkg) e il file di configurazione del servizio (.cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. Facoltativamente, aggiornare l'account di archiviazione e l'etichetta di distribuzione.

5. Se uno o più ruoli contengono una sola istanza del ruolo, selezionare la casella di controllo **Distribuisci anche se uno o più ruoli contengono una singola istanza** per abilitare l'esecuzione dell'aggiornamento.

    Durante un aggiornamento del servizio cloud, Azure può garantire solo una percentuale di disponibilità del servizio pari al 99,95% se ogni ruolo contiene almeno due istanze del ruolo (macchine virtuali). Con due istanze del ruolo, una macchina virtuale elabora le richieste dei client mentre l'altra viene aggiornata.

6. Selezionare la casella di controllo **Avvia distribuzione** per applicare l'aggiornamento al termine del caricamento del pacchetto.

7. Scegliere **OK** per iniziare l'aggiornamento del servizio.

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>Scambiare le distribuzioni per alzare di livello una pre-distribuzione convertendola in una distribuzione di produzione
Quando si decide di distribuire una nuova versione di un servizio cloud, è possibile eseguirne un'installazione di appoggio e testarla nell'ambiente di staging del servizio cloud. Usare **Scambia** per scambiare gli URL di indirizzamento delle due distribuzioni e alzare di livello la nuova versione in produzione.

È possibile scambiare le distribuzioni dalla pagina **Cloud Services** o dal dashboard.

1. Nel [portale di Azure][Azure portal] selezionare il servizio cloud che si desidera aggiornare. Questo passaggio consente di aprire il pannello dell'istanza del servizio cloud.

2. Nel pannello selezionare **Scambia**.

    ![Pulsante Scambia - Servizi cloud](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Verrà visualizzata la seguente richiesta di conferma:

    ![Scambio di servizi cloud](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Dopo avere controllato le informazioni di distribuzione, scegliere **OK** per scambiare le distribuzioni.

    Lo scambio delle distribuzioni avviene rapidamente perché l'unico elemento che cambia è rappresentato dagli indirizzi IP virtuali (VIP) delle distribuzioni.

    Per ridurre i costi di calcolo, è possibile eliminare la distribuzione di gestione di staging dopo avere verificato che la distribuzione di produzione funziona nel modo previsto.

### <a name="common-questions-about-swapping-deployments"></a>Domande comuni sullo scambio di distribuzioni

**Quali sono i prerequisiti per lo scambio delle distribuzioni?**

Esistono due prerequisiti chiave per lo scambio corretto di distribuzioni:

- Se si vuole usare un indirizzo IP statico per lo slot di produzione, è necessario riservarne uno anche per lo slot di staging. In caso contrario, lo scambio ha esito negativo.

- Tutte le istanze dei ruoli devono essere in esecuzione prima di poter eseguire lo scambio. È possibile controllare lo stato delle istanze nel pannello **Panoramica** del portale di Azure. In alternativa, è possibile usare il comando [Get-AzureRole](/powershell/module/azure/get-azurerole?view=azuresmps-3.7.0) in Windows PowerShell.

Si noti che anche gli aggiornamenti del sistema operativo guest e le operazioni di correzione del servizio possono ostacolare il corretto scambio delle distribuzioni. Per altre informazioni, vedere [Risolvere eventuali problemi di distribuzione dei servizi cloud](cloud-services-troubleshoot-deployment-problems.md).

**Uno scambio comporta un tempo di inattività per l'applicazione? Come gestire questa situazione?**

Come descritto nella sezione precedente, lo scambio di distribuzioni è in genere veloce perché è una semplice modifica della configurazione in Azure Load Balancer. In alcuni casi, può richiedere più di 10 secondi e causare errori di connessione temporanei. Per limitare l'impatto sui clienti, si consiglia di implementare la [logica di ripetizione dei tentativi nel client](../best-practices-retry-general.md).

## <a name="delete-deployments-and-a-cloud-service"></a>Eliminare le distribuzioni e un servizio cloud
Per eliminare un servizio cloud è necessario prima eliminare tutte le distribuzioni esistenti.

Per ridurre i costi di calcolo, è possibile eliminare la distribuzione di gestione di staging dopo avere verificato che la distribuzione di produzione funziona nel modo previsto. Vengono addebitati i costi di calcolo per le istanze del ruolo distribuite che sono state arrestate.

Per eliminare una distribuzione o il servizio cloud, attenersi alla procedura seguente.

1. Nel [portale di Azure][Azure portal] selezionare il servizio cloud che si desidera eliminare. Questo passaggio consente di aprire il pannello dell'istanza del servizio cloud.

2. Nel pannello selezionare **Elimina**.

    ![Pulsante Elimina - Servizi cloud](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Per eliminare l'intero servizio cloud, selezionare la casella di controllo **Servizio cloud e relative distribuzioni**. In alternativa, è possibile selezionare la casella di controllo **Distribuzione di produzione** o **Distribuzione di staging**.

    ![Elimina - Servizi cloud](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Selezionare **Elimina** in basso.

5. Per eliminare il servizio cloud, selezionare **Elimina servizio cloud**. Quindi, alla richiesta di conferma fare clic su **Sì**.

> [!NOTE]
> Quando un servizio cloud viene eliminato e viene configurato il monitoraggio dettagliato, è necessario eliminare manualmente i dati dall'account di archiviazione. Per informazioni su dove trovare le tabelle di metriche, vedere [Presentazione del monitoraggio del servizio cloud](cloud-services-how-to-monitor.md).


## <a name="find-more-information-about-failed-deployments"></a>Trovare altre informazioni sulle distribuzioni non riuscite
Il pannello **Panoramica** comprende una barra di stato in alto. Quando si seleziona la barra, si apre un nuovo pannello che visualizza le informazioni sugli errori. Se la distribuzione non contiene errori, il pannello delle informazioni è vuoto.

![Panoramica - Servizi cloud](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Passaggi successivi
* [Configurazione generale del servizio cloud](cloud-services-how-to-configure-portal.md).
* Procedura [distribuire un servizio cloud](cloud-services-how-to-create-deploy-portal.md).
* Configurare un [nome di dominio personalizzato](cloud-services-custom-domain-name-portal.md).
* Configurare i [certificati SSL](cloud-services-configure-ssl-certificate-portal.md).
