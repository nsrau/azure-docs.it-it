---
title: "Esercitazione: Aggiungere un'istanza gestita di database SQL a un gruppo di failover"
description: Informazioni su come configurare un gruppo di failover per l'istanza gestita di database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 06/27/2019
ms.openlocfilehash: e4b7de3931c0d3508e5af6aa6bf85dfa18641aee
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624976"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>Esercitazione: Aggiungere un'istanza gestita di database SQL a un gruppo di failover

Aggiungere un'istanza gestita di database SQL a un gruppo di failover. In questo articolo verrà spiegato come:

> [!div class="checklist"]
> - Creare un'istanza gestita primaria
> - Creare un'istanza gestita secondaria come parte di un [gruppo di failover](sql-database-auto-failover-group.md). 
> - Failover di test

  > [!NOTE]
  > La creazione di un'istanza gestita può richiedere una quantità di tempo significativa. Di conseguenza, per il completamento di questa esercitazione potrebbero essere necessarie diverse ore. Per altre informazioni sui tempi di provisioning, vedere [operazioni di gestione di istanze gestite](sql-database-managed-instance.md#managed-instance-management-operations). L'uso di gruppi di failover con istanze gestite è attualmente in anteprima. 

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, accertarsi di avere: 

- Una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) se non ne è già disponibile uno. 


## <a name="1----create-resource-group-and-primary-managed-instance"></a>1-creare il gruppo di risorse e l'istanza gestita primaria
In questo passaggio si creeranno il gruppo di risorse e l'istanza gestita primaria per il gruppo di failover usando il portale di Azure. 

1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Scegliere di **creare una risorsa** nell'angolo superiore sinistro della portale di Azure. 
1. Digitare `managed instance` nella casella di ricerca e selezionare l'opzione per Azure SQL istanza gestita. 
1. Selezionare **Crea** per avviare la pagina di creazione dell' **istanza gestita di SQL** . 
1. Nella pagina **crea istanza gestita di database SQL di Azure** , nella scheda nozioni di **base**
    1. In **Dettagli progetto**selezionare la **sottoscrizione** dall'elenco a discesa, quindi scegliere di creare un **nuovo** gruppo di risorse. Digitare un nome per il gruppo di risorse, ad esempio `myResourceGroup`. 
    1. In **istanza gestita dettagli**specificare il nome dell'istanza gestita e l'area in cui si desidera distribuire l'istanza gestita. Assicurarsi di selezionare un'area con un' [area abbinata](/azure/best-practices-availability-paired-regions). Lasciare i valori predefiniti per il calcolo e l' **archiviazione** . 
    1. In **account amministratore**specificare un account di accesso amministratore, ad `azureuser`esempio, e una password amministratore complessa. 

    ![Crea la MI primaria](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. Lasciare i valori predefiniti per le altre impostazioni e selezionare **Verifica + crea** per esaminare le impostazioni dell'istanza gestita. 
1. Selezionare **Crea** per creare l'istanza gestita primaria. 


## <a name="2---create-a-virtual-network"></a>2-creare una rete virtuale
In questo passaggio verrà creata una rete virtuale per l'istanza gestita secondaria. Questo passaggio è necessario perché è necessario che la subnet delle istanze gestite primarie e secondarie disponga di intervalli di indirizzi non sovrapposti. 

Per verificare l'intervallo di subnet della rete virtuale primaria, attenersi alla procedura seguente:
1. Nella [portale di Azure](https://portal.azure.com)passare al gruppo di risorse e selezionare la rete virtuale per l'istanza primaria. 
1. Selezionare **subnet** in **Impostazioni** e prendere nota dell' **intervallo di indirizzi**. L'intervallo di indirizzi della subnet della rete virtuale per l'istanza gestita secondaria non può sovrapporsi a questo. 


   ![Subnet primaria](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

Per creare una rete virtuale, seguire questa procedura:

1. Nella [portale di Azure](https://portal.azure.com)selezionare **Crea una risorsa** e cercare *rete virtuale*. 
1. Selezionare l'opzione **rete virtuale** pubblicata da Microsoft e quindi fare clic su **Crea** nella pagina successiva. 
1. Compilare i campi obbligatori per configurare la rete virtuale per l'istanza gestita secondaria, quindi selezionare **Crea**. 

   La tabella seguente mostra i valori necessari per la rete virtuale secondaria:

    | **Campo** | Value |
    | --- | --- |
    | **Nome** |  Nome della rete virtuale che deve essere utilizzato dall'istanza gestita secondaria, ad esempio `vnet-sql-mi-secondary`. |
    | **Spazio degli indirizzi** | Spazio degli indirizzi per la rete virtuale, ad esempio `10.128.0.0/16`. | 
    | **Sottoscrizione** | Sottoscrizione in cui risiedono l'istanza gestita primaria e il gruppo di risorse. |
    | **Region** | Il percorso in cui si distribuirà l'istanza gestita secondaria; deve trovarsi in un' [area abbinata](/azure/best-practices-availability-paired-regions) all'istanza gestita primaria.  |
    | **Subnet** | Nome della subnet. `default`viene fornito per impostazione predefinita. |
    | **Intervallo di indirizzi**| Intervallo di indirizzi per la subnet. Questo deve essere diverso dall'intervallo di indirizzi della subnet usato dalla rete virtuale dell'istanza gestita primaria, ad esempio `10.128.0.0/24`.  |
    | &nbsp; | &nbsp; |

    ![Valori della rete virtuale secondaria](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)


## <a name="3---create-a-secondary-managed-instance"></a>3-creare un'istanza gestita secondaria
In questo passaggio verrà creata un'istanza gestita secondaria nel portale di Azure, che configurerà anche la rete tra le due istanze gestite. 

La seconda istanza gestita deve:
- Essere vuoto. 
- Trovarsi all'interno di un' [area abbinata](/azure/best-practices-availability-paired-regions) con la corrispondente istanza gestita primaria. 
- Avere una subnet e un intervallo IP diversi rispetto all'istanza gestita primaria. 

Per creare un'istanza gestita secondaria, attenersi alla procedura seguente: 

1. Nella [portale di Azure](http://portal.azure.com)selezionare **Crea una risorsa** e cercare *istanza gestita SQL di Azure*. 
1. Selezionare l'opzione **istanza gestita di Azure SQL** pubblicata da Microsoft e quindi selezionare **Crea** nella pagina successiva.
1. Nella scheda **nozioni di base** della pagina **Crea istanza gestita di database SQL di Azure** compilare i campi obbligatori per configurare l'istanza gestita secondaria. 

   Nella tabella seguente sono illustrati i valori necessari per l'istanza gestita secondaria:
 
    | **Campo** | Valore |
    | --- | --- |
    | **Sottoscrizione** |  Sottoscrizione in cui si trova l'istanza gestita primaria. |
    | **Gruppo di risorse**| Il gruppo di risorse in cui si trova l'istanza gestita primaria. |
    | **Nome istanza gestita** | Nome della nuova istanza gestita secondaria, ad esempio`sql-mi-secondary`  | 
    | **Region**| Percorso dell' [area abbinata](/azure/best-practices-availability-paired-regions) per l'istanza gestita secondaria.  |
    | **Account di accesso amministratore istanza gestita** | Account di accesso che si desidera utilizzare per la nuova istanza gestita secondaria, ad `azureuser`esempio. |
    | **Password** | Password complessa che verrà utilizzata dall'account di accesso amministratore per la nuova istanza gestita secondaria.  |
    | &nbsp; | &nbsp; |

1. Nella scheda rete, per la **rete virtuale**, selezionare la rete virtuale creata per l'istanza gestita secondaria dall'elenco a discesa.

   ![Rete MI secondaria](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. Nella scheda **Impostazioni aggiuntive** , per la **replica geografica**, scegliere **Sì** da _usare come database secondario di failover_. Selezionare l'istanza gestita primaria dall'elenco a discesa. 
    1. Assicurarsi che le regole di confronto e il fuso orario corrispondano a quelle dell'istanza gestita primaria. Per l'istanza gestita primaria creata in questa esercitazione sono state usate `SQL_Latin1_General_CP1_CI_AS` le regole di confronto `(UTC) Coordinated Universal Time` predefinite e il fuso orario. 

   ![Rete MI secondaria](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. Selezionare **Verifica + crea** per esaminare le impostazioni per l'istanza gestita secondaria. 
1. Selezionare **Crea** per creare un'istanza gestita secondaria. 


## <a name="4---create-primary-virtual-network-gateway"></a>4-creare un gateway di rete virtuale primario 

Affinché due istanze gestite partecipino a un gruppo di failover, è necessario che sia configurato un gateway tra le reti virtuali delle due istanze gestite per consentire la comunicazione di rete. È possibile creare il gateway per l'istanza gestita primaria usando il portale di Azure:

1. Nel [portale di Azure](https://portal.azure.com)passare al gruppo di risorse e selezionare la risorsa di **rete virtuale** per l'istanza gestita primaria. 
1. Selezionare **subnet** in **Impostazioni** e quindi selezionare per aggiungere una nuova **subnet del gateway**. Lasciare i valori predefiniti. 

   ![Aggiungi gateway per istanza gestita primaria](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. Dopo aver creato il gateway della subnet, selezionare **Crea una risorsa** nel riquadro di spostamento a sinistra e `Virtual network gateway` quindi digitare nella casella di ricerca. Selezionare la risorsa **gateway di rete virtuale** pubblicata da **Microsoft**. 

   ![Creare un nuovo gateway di rete virtuale](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Compilare i campi obbligatori per configurare il gateway per l'istanza gestita primaria. 

   La tabella seguente mostra i valori necessari per il gateway per l'istanza gestita primaria:
 
    | **Campo** | Value |
    | --- | --- |
    | **Sottoscrizione** |  Sottoscrizione in cui si trova l'istanza gestita primaria. |
    | **Nome** | Nome del gateway di rete virtuale, ad esempio `primary-mi-gateway`. | 
    | **Region** | Area in cui si trova l'istanza gestita secondaria. |
    | **Tipo di gateway** | Selezionare **VPN**. |
    | **Tipo di VPN** | Seleziona **basato su Route** |
    | **SKU**| Lasciare il valore `VpnGw1`predefinito di. |
    | **Location**| Il percorso in cui si trova l'istanza gestita primaria e la rete virtuale primaria.   |
    | **Rete virtuale**| Selezionare la rete virtuale creata nella sezione 2, ad esempio `vnet-sql-mi-primary`. |
    | **Indirizzo IP pubblico**| Selezionare **Crea nuovo**. |
    | **Nome dell'indirizzo IP pubblico**| Immettere un nome per l'indirizzo IP, ad esempio `primary-gateway-IP`. |
    | &nbsp; | &nbsp; |
1. Lasciare gli altri valori predefiniti e quindi selezionare **Verifica + crea** per esaminare le impostazioni per il gateway di rete virtuale.

   ![Impostazioni del gateway primario](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Selezionare **Crea** per creare il nuovo gateway di rete virtuale. 

## <a name="5---configure-secondary-virtual-network-gateway"></a>5-configurare il gateway di rete virtuale secondario 

Ripetere i passaggi nella sezione precedente per creare la subnet della rete virtuale e il gateway per l'istanza gestita secondaria. Compilare i campi obbligatori per configurare il gateway per l'istanza gestita secondaria. 

   La tabella seguente mostra i valori necessari per il gateway per l'istanza gestita secondaria:

   | **Campo** | Value |
   | --- | --- |
   | **Sottoscrizione** |  Sottoscrizione in cui si trova l'istanza gestita secondaria. |
   | **Nome** | Nome del gateway di rete virtuale, ad esempio `secondary-mi-gateway`. | 
   | **Region** | Area in cui si trova l'istanza gestita secondaria. |
   | **Tipo di gateway** | Selezionare **VPN**. |
   | **Tipo di VPN** | Seleziona **basato su Route** |
   | **SKU**| Lasciare il valore `VpnGw1`predefinito di. |
   | **Location**| Il percorso in cui si trova l'istanza gestita secondaria e la rete virtuale secondaria.   |
   | **Rete virtuale**| Selezionare la rete virtuale creata nella sezione 2, ad esempio `vnet-sql-mi-secondary`. |
   | **Indirizzo IP pubblico**| Selezionare **Crea nuovo**. |
   | **Nome dell'indirizzo IP pubblico**| Immettere un nome per l'indirizzo IP, ad esempio `secondary-gateway-IP`. |
   | &nbsp; | &nbsp; |

   ![Impostazioni del gateway secondario](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

## <a name="6---connect-the-gateways"></a>6-connettere i gateway
In questo passaggio, creare una connessione tra gateway. Una connessione deve essere stabilita dal gateway primario a quello secondario e quindi deve essere stabilita una connessione separata tra la replica secondaria e il gateway primario. Assicurarsi di usare la stessa **chiave condivisa** quando si configura la connettività tra entrambi i gateway. 

Per configurare la connettività, attenersi alla procedura seguente:

1. Passare al gruppo di risorse nel [portale di Azure](https://portal.azure.com) e selezionare il gateway primario creato nel passaggio 4. 
1. Selezionare **connessioni** in **Impostazioni** e quindi selezionare **Aggiungi** per creare una nuova connessione. 

   ![Aggiungere la connessione al gateway primario](media/sql-database-managed-instance-failover-group-tutorial/add-primary-gateway-connection.png)

1. Immettere un nome per la connessione, ad esempio `Primary-connection`, e digitare un valore per la `mi1mi2psk` **chiave condivisa** , ad esempio. 
1. Selezionare il **secondo gateway di rete virtuale** e quindi selezionare il gateway per l'istanza gestita secondaria, ad `secondary-mi-gateway`esempio. 

   ![Crea connessione primaria a secondaria](media/sql-database-managed-instance-failover-group-tutorial/create-primary-to-secondary-connection.png)

1. Selezionare **OK** per aggiungere la nuova connessione gateway primario a secondario.
1. Ripetere questi passaggi per creare una connessione dal gateway dell'istanza gestita secondaria al gateway dell'istanza gestita primaria. 

   ![Creare una connessione secondaria alla connessione primaria](media/sql-database-managed-instance-failover-group-tutorial/create-secondary-to-primary-connection.png)


## <a name="7---create-a-failover-group"></a>7-creare un gruppo di failover
In questo passaggio si creerà il gruppo di failover e si aggiungeranno entrambe le istanze gestite. 

1. Nella [portale di Azure](https://portal.azure.com)passare a tutti i **Servizi** `managed instance` e digitare nella casella di ricerca. 
1. Opzionale Selezionare la stella accanto a **istanze gestite da SQL** per aggiungere istanze gestite come collegamento alla barra di spostamento a sinistra. 
1. Selezionare **istanze gestite di SQL** e selezionare l'istanza gestita primaria, ad `sql-mi-primary`esempio. 
1. In **Impostazioni**passare a **gruppi di failover istanza** , quindi scegliere **Aggiungi gruppo** per aprire la pagina **gruppo di failover dell'istanza** . 

   ![Aggiungere un gruppo di failover](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. Nella pagina **gruppo di failover dell'istanza** Digitare il nome del gruppo di failover, ad esempio `failovergrouptutorial` , quindi scegliere l' `sql-mi-secondary` istanza gestita secondaria, ad esempio dall'elenco a discesa. Selezionare **Crea** per creare il gruppo di failover. 

   ![Crea gruppo di failover](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Una volta completata la distribuzione del gruppo di failover, si tornerà alla pagina del **gruppo di failover** . 

## <a name="8---test-failover"></a>8-failover di test
In questo passaggio si verificherà un errore nel gruppo di failover nel server secondario e quindi si eseguirà il failback utilizzando il portale di Azure. 

1. Passare all'istanza gestita all'interno del [portale di Azure](https://portal.azure.com) e selezionare **gruppi di failover dell'istanza** in impostazioni. 
1. Verificare quale istanza gestita è la primaria e quale istanza gestita è la replica secondaria. 
1. Selezionare **failover** e quindi fare clic su **Sì** nell'avviso relativo alle sessioni TDS disconnesse. 

   ![Eseguire il failover del gruppo di failover](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Verificare quale istanza di gestione è la principale e l'istanza secondaria. Se il failover ha esito positivo, le due istanze dovrebbero avere ruoli passati. 

   ![Istanze gestite con ruoli passati dopo il failover](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Selezionare di nuovo **failover** per eseguire il failback dell'istanza primaria al ruolo primario. 


## <a name="clean-up-resources"></a>Pulire le risorse
Pulire le risorse eliminando prima di tutto l'istanza gestita, il cluster virtuale, le eventuali risorse rimanenti e infine il gruppo di risorse. 

1. Passare al gruppo di risorse nel [portale di Azure](https://portal.azure.com). 
1. Selezionare l'istanza gestita e quindi selezionare **Elimina**. Digitare `yes` nella casella di testo per confermare che si vuole eliminare la risorsa e quindi selezionare **Elimina**. Questo processo può richiedere del tempo per il completamento in background e, fino a quando non viene eseguito, non sarà possibile eliminare il *cluster virtuale* o altre risorse dipendenti. Monitorare l'eliminazione nella scheda attività per confermare che l'istanza gestita è stata eliminata. 
1. Una volta eliminata l'istanza gestita, eliminare il *cluster virtuale* selezionandolo nel gruppo di risorse e quindi scegliendo **Elimina**. Digitare `yes` nella casella di testo per confermare che si vuole eliminare la risorsa e quindi selezionare **Elimina**. 
1. Eliminare eventuali risorse rimanenti. Digitare `yes` nella casella di testo per confermare che si vuole eliminare la risorsa e quindi selezionare **Elimina**. 
1. Eliminare il gruppo di risorse selezionando **Elimina gruppo di risorse**, digitando il nome del gruppo `myResourceGroup`di risorse e quindi facendo clic su **Elimina**. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato configurato un gruppo di failover tra due istanze gestite. Si è appreso come:

> [!div class="checklist"]
> - Creare un'istanza gestita primaria
> - Creare un'istanza gestita secondaria come parte di un [gruppo di failover](sql-database-auto-failover-group.md). 
> - Failover di test

Passare alla Guida introduttiva successiva su come connettersi all'istanza gestita e come ripristinare un database nell'istanza gestita: 

> [!div class="nextstepaction"]
> [Connettersi all'istanza](sql-database-managed-instance-configure-vm.md)
> gestita[ripristinare un database in un'istanza gestita](sql-database-managed-instance-get-started-restore.md)


