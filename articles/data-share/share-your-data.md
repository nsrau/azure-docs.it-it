---
title: "Esercitazione: Condividere all'esterno dell'organizzazione - Condivisione dati di Azure"
description: Esercitazione - Condividere dati con clienti e partner usando Condivisione dati di Azure
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 4ef9256404b0d0d4d6379e4f5a76c0d41a38c7cd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499309"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Esercitazione: Condividere dati con Condivisione dati di Azure  

Questa esercitazione illustra come configurare una nuova condivisione dati di Azure e iniziare a condividere i dati con clienti e partner all'esterno dell'organizzazione di Azure. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una condivisione dati.
> * Aggiungere set di dati alla condivisione dati.
> * Abilitare una pianificazione della sincronizzazione per la condivisione dati. 
> * Aggiungere i destinatari alla condivisione dati. 

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Indirizzo di posta elettronica per l'accesso ad Azure dei destinatari (gli alias di posta elettronica non funzionano).

### <a name="share-from-a-storage-account"></a>Condividere da un account di archiviazione:

* Un account di Archiviazione di Azure: se non se ne ha già uno, è possibile creare un [account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
* L'autorizzazione per aggiungere l'assegnazione di ruolo all'account di archiviazione, inclusa nell'autorizzazione*Microsoft.Authorization/role assignments/write*. Questa autorizzazione è presente nel ruolo proprietario. 

### <a name="share-from-a-sql-based-source"></a>Condividere da un'origine basata su SQL:

* Un database o un data warehouse SQL di Azure con tabelle e visualizzazioni da condividere.
* Autorizzazione per la condivisione dati che deve accedere al data warehouse. A tale scopo, seguire questa procedura: 
    1. Impostarsi come amministratore di Azure Active Directory per il server.
    1. Connettersi al database SQL di Azure o ad Azure SQL Data Warehouse con Azure Active Directory.
    1. Usare l'Editor di query (anteprima) per eseguire lo script seguente in modo da aggiungere l'identità del servizio gestita di Condivisione dati come db_owner. È necessario connettersi usando Active Directory e non l'autenticazione di SQL Server. 
    
```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```                   
Si noti che *<share_acc_name>* è il nome del proprio account di Condivisione dati. Se ancora non si è provveduto a creare un tale account, è possibile tornare a questo prerequisito in un secondo momento.  

* Accesso del firewall di SQL Server dell'indirizzo IP client: A tale scopo, seguire questa procedura: 1. Passare a *Firewall e reti virtuali* 1. Fare clic sull'interruttore di **attivazione** per consentire l'accesso ai servizi di Azure. 

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Creare un account di condivisione dati

Creare una risorsa di condivisione dati di Azure in un gruppo di risorse di Azure.

1. Selezionare il pulsante **Crea una risorsa** (+) nell'angolo superiore sinistro del portale.

1. Cercare *Condivisione dati*.

1. Selezionare Condivisione dati e quindi **Crea**.

1. Compilare i dettagli di base della risorsa di condivisione dati di Azure con le informazioni seguenti. 

     **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | NOME | *datashareacount* | Specificare un nome per l'account di condivisione dati. |
    | Subscription | Sottoscrizione in uso | Selezionare la sottoscrizione di Azure da usare per l'account di condivisione dati.|
    | Resource group | *test-resource-group* | Usare un gruppo di risorse esistente oppure crearne uno nuovo. |
    | Location | *Stati Uniti orientali 2* | Selezionare un'area per l'account di condivisione dati.
    | | |

1. Selezionare **Crea** per effettuare il provisioning dell'account di condivisione dati. Il completamento del provisioning di un nuovo account di condivisione dati in genere impiega al massimo 2 minuti. 

1. Una volta completata la distribuzione, selezionare **Vai alla risorsa**.

## <a name="create-a-data-share"></a>Creare una condivisione dati

1. Passare alla pagina di panoramica della condivisione dati.

    ![Condividere i dati](./media/share-receive-data.png "Condividere i dati") 

1. Selezionare **Start sharing your data** (Inizia a condividere i dati).

1. Selezionare **Create** (Crea).   

1. Immettere i dettagli della condivisione dati. Specificare un nome, una descrizione del contenuto della condivisione e le condizioni per l'utilizzo (facoltativo). 

    ![Immettere i dettagli della condivisione](./media/enter-share-details.png "Immettere i dettagli della condivisione") 

1. Selezionare **Continua**

1. Per aggiungere set di dati alla condivisione dati, selezionare **Add Datasets** (Aggiungi set di dati). 

    ![Set di dati](./media/datasets.png "Set di dati")

1. Selezionare il tipo di set di dati da aggiungere. 

    ![Aggiungere i set di dati](./media/add-datasets.png "Aggiungere i set di dati")    

1. Passare all'oggetto che si vuole condividere e selezionare "Add Datasets" (Aggiungi set di dati). 

    ![Selezionare i set di dati](./media/select-datasets.png "Selezionare i set di dati")    

1. Nella scheda Destinatari immettere l'indirizzo di posta elettronica del consumer di dati selezionando "+ Aggiungi il destinatario". 

    ![Aggiungere i destinatari](./media/add-recipient.png "Aggiungere destinatari") 

1. Selezionare **Continua**

1. Se si vuole consentire al consumer di dati di ottenere gli aggiornamenti incrementali dei dati, abilitare la pianificazione degli snapshot. 

    ![Abilitare gli snapshot](./media/enable-snapshots.png "Abilitare gli snapshot") 

1. Selezionare un'ora di inizio e un intervallo di ricorrenza. 

1. Selezionare **Continua**

1. Nella scheda Rivedi e crea verificare il contenuto del pacchetto, le impostazioni, i destinatari e le impostazioni di sincronizzazione. Selezionare **Crea**

La condivisione dati di Azure è stata creata e il suo destinatario è ora pronto ad accettare l'invito. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come creare una condivisione dati di Azure e invitare destinatari. Per informazioni su come un consumer di dati può accettare e ricevere una condivisione dati, continuare con l'esercitazione [Accettare e ricevere dati](subscribe-to-data-share.md). 
