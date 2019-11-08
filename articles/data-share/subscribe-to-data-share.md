---
title: 'Esercitazione: Accettare e ricevere dati - Condivisione dati di Azure'
description: 'Esercitazione: Accettare e ricevere dati con Condivisione dati di Azure'
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 9c24f54fe846459187488b0a65b2582914e25e2a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499339"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Esercitazione: Accettare e ricevere dati con Condivisione dati di Azure  

In questa esercitazione si apprenderà come accettare un invito alla condivisione dei dati usando Condivisione dati di Azure. Si apprenderà come ricevere i dati condivisi con l'utente, nonché come abilitare un intervallo di aggiornamento regolare per garantire di avere sempre lo snapshot più recente dei dati condivisi con l'utente. 

> [!div class="checklist"]
> * Come accettare un invito di Condivisione dati di Azure
> * Creare un account di Condivisione dati di Azure
> * Specificare una destinazione per i dati
> * Creare una sottoscrizione per la condivisione dei dati per l'aggiornamento pianificato

## <a name="prerequisites"></a>Prerequisiti
Prima di accettare un invito alla condivisione dei dati, è necessario effettuare il provisioning di una serie di risorse di Azure, elencate di seguito. 

Assicurarsi che tutti i prerequisiti siano soddisfatti prima di accettare un invito alla condivisione dei dati. 

* Sottoscrizione di Azure: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Un invito alla Condivisione dati: un invito da Microsoft Azure con un oggetto denominato "Azure Data Share invitation from **<yourdataprovider@domain.com>** " (Invito alla Condivisione dati di Azure da...).

### <a name="receive-data-into-a-storage-account"></a>Ricevere dati in un account di archiviazione: 

* Un account di Archiviazione di Azure: se non se ne ha già uno, è possibile creare un [account di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* L'autorizzazione per aggiungere l'assegnazione di ruolo all'account di archiviazione, inclusa nell'autorizzazione*Microsoft.Authorization/role assignments/write*. Questa autorizzazione è presente nel ruolo proprietario. 
* Registrazione del provider di risorse Microsoft.DataShare. Per informazioni su come eseguire questa operazione, vedere la documentazione sui [provider di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

> [!IMPORTANT]
> Per accettare e ricevere una Condivisione dati di Azure, è prima di utto necessario registrare il provider di risorse Microsoft.DataShare ed essere un proprietario dell'account di archiviazione in cui si accettano i dati. Seguire le istruzioni riportate in [Risolvere i problemi relativi a Condivisione dati di Azure](data-share-troubleshoot.md) per registrare il provider di risorse di condivisione dati e aggiungersi come proprietario dell'account di archiviazione. 

### <a name="receive-data-into-a-sql-based-source"></a>Ricevere dati in un'origine basata su SQL:

* L'autorizzazione per l'identità del servizio gestita di Condivisione dati per l'accesso al database SQL di Azure o ad Azure SQL Data Warehouse. A tale scopo, seguire questa procedura: 
    1. Impostarsi come amministratore di Azure Active Directory per il server.
    1. Connettersi al database SQL di Azure o ad Azure SQL Data Warehouse con Azure Active Directory.
    1. Usare l'Editor di query (anteprima) per eseguire lo script seguente in modo da aggiungere l'identità del servizio gestita di Condivisione dati come db_owner. È necessario connettersi usando Active Directory e non l'autenticazione di SQL Server. 

```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```      
Si noti che *<share_acc_name>* è il nome del proprio account di Condivisione dati. Se ancora non si è provveduto a creare un tale account, è possibile tornare a questo prerequisito in un secondo momento.         

* Accesso del firewall di SQL Server all'indirizzo IP client: A tale scopo, seguire questa procedura: 1. Passare a *Firewall e reti virtuali* 2. Fare clic sull'interruttore di **attivazione** per consentire l'accesso ai servizi di Azure. 

Una volta completati questi prerequisiti, si è pronti per ricevere dati in SQL Server.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="open-invitation"></a>Aprire l'invito

Controllare la posta in arrivo per verificare di aver ricevuto un invito dal provider di dati. L'invito proviene da Microsoft Azure con l'oggetto **Azure Data Share invitation from<yourdataprovider@domain.com>** (Invito alla Condivisione dati di Azure da...). Prendere nota del nome condivisione per assicurarsi di accettare la condivisione corretta se sono presenti più inviti. 

Selezionare **Visualizza invito** per visualizzare l'invito in Azure. Verrà visualizzata la pagina Received Shares (Condivisioni ricevute).

![Inviti](./media/invitations.png "Elenco di inviti") 

Selezionare la condivisione che si vuole visualizzare. 

## <a name="accept-invitation"></a>Accettare l'invito
Assicurarsi di rivedere tutti i campi, tra cui le **condizioni per l'utilizzo**. Se si accettano le condizioni per l'utilizzo, verrà richiesto di selezionare la casella per confermare l'accettazione. 

![Condizioni d'uso](./media/terms-of-use.png "Condizioni per l'utilizzo") 

In *Target Data Share Account* (Account di condivisione dati di destinazione), selezionare la sottoscrizione e il gruppo di risorse in cui verrà distribuita la condivisione dati. 

Nel campo **Data Share Account** (Account di condivisione dati), selezionare **Create new** (Crea nuovo) se non si ha un account di Condivisione dati esistente. In caso contrario, selezionare un account esistente di Condivisione dati in cui si vuole accettare la condivisione dei dati. 

Nel campo *Received Share Name* (Nome condivisione ricevuta) si può lasciare il valore predefinito specificato dal provider di dati o specificare un nuovo nome per la condivisione ricevuta. 

![Account di condivisione dati di destinazione](./media/target-data-share.png "Account di condivisione dati di destinazione") 

Dopo aver accettato le condizioni per l'utilizzo e specificato un percorso per la condivisione, selezionare *Accept and Configure* (Accetta e configura). Se si sceglie questa opzione, verrà creata una sottoscrizione di condivisione e la schermata successiva chiederà di selezionare un account di archiviazione di destinazione in cui copiare i dati. 

![Opzioni di accettazione](./media/accept-options.png "Opzioni di accettazione") 

Se si preferisce accettare l'invito adesso ma configurare l'archiviazione in un secondo momento, selezionare *Accept and Configure later* (Accettare e configurare in seguito). Questa opzione consente di configurare l'account di archiviazione di destinazione in un secondo momento. Per continuare a configurare l'archiviazione in seguito, vedere la pagina su [come configurare l'account di archiviazione](how-to-configure-mapping.md) per la procedura dettagliata di ripristino della configurazione della condivisione dati. 

Se non si vuole accettare l'invito, selezionare *Reject* (Rifiuta). 

## <a name="configure-storage"></a>Configurare l'archivio
In *Target Storage Settings* (Impostazioni archiviazione di destinazione) selezionare la sottoscrizione, il gruppo di risorse e l'account di archiviazione in cui si vogliono ricevere i dati. 

![Impostazioni archiviazione di destinazione](./media/target-storage-settings.png "Archiviazione di destinazione") 

Per ricevere aggiornamenti periodici dei dati, assicurarsi di abilitare le impostazioni di snapshot. Tenere presente che la pianificazione delle impostazioni di snapshot sarà visibile solo se il provider di dati l'avrà inclusa nella condivisione dati. 

![Impostazioni dello snapshot](./media/snapshot-settings.png "Impostazioni dello snapshot") 

Selezionare *Salva*. 

> [!IMPORTANT]
> Se si ricevono dati basati su SQL e si intende ricevere tali dati in un'origine basata su SQL, visitare la guida pratica sulla [configurazione del mapping di un set di dati](how-to-configure-mapping.md) per imparare a configurare un server SQL come destinazione per il set di dati. 

## <a name="trigger-a-snapshot"></a>Attivazione di uno snapshot

È possibile attivare uno snapshot in Received Shares -> Details selezionando **Trigger snapshot** (Attiva snapshot). Qui è possibile attivare uno snapshot completo o incrementale dei dati. Se è la prima volta che si ricevono i dati dal provider, selezionare Full copy (Copia completa). 

![Attivazione dello snapshot](./media/trigger-snapshot.png "Attivazione dello snapshot") 

Quando l'ultimo stato di esecuzione è *successful*, aprire l'account di archiviazione per visualizzare i dati ricevuti. 

Per controllare quale account di archiviazione è stato usato, selezionare **Datasets**. 

![Set di dati consumer](./media/consumer-datasets.png "Mapping dei set di dati consumer") 

## <a name="view-history"></a>Visualizzare la cronologia
Per visualizzare una cronologia degli snapshot, passare a Received Shares -> History. Qui è presente una cronologia di tutti gli snapshot generati negli ultimi 60 giorni. 

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come accettare e ricevere una Condivisione dati di Azure. Per altre informazioni sui concetti di Condivisione dati di Azure, passare a [Concetti: Terminologia di Condivisione dati di Azure](terminology.md).