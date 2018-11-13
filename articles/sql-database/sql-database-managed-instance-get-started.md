---
title: "Portale di Azure: creare un'istanza gestita di SQL | Microsoft Docs"
description: Creare un'istanza gestita di SQL, un ambiente di rete e una VM client per l'accesso.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: Carlrab
manager: craigg
ms.date: 11/01/2018
ms.openlocfilehash: 3eadc2d233fd1716716c323f4c7087ee8363c67c
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2018
ms.locfileid: "50912323"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Guida introduttiva: Creare un'istanza gestita di database SQL di Azure

Questa guida introduttiva illustra come creare un'[istanza gestita](sql-database-managed-instance.md) di database SQL di Azure nel portale di Azure. 

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Creare un'istanza gestita

I passaggi seguenti mostrano come creare un'istanza gestita.

1. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
2. Individuare **Istanza gestita** e quindi selezionare **Azure SQL Managed Instance**.
3. Fare clic su **Create**(Crea).

   ![Creare un'istanza gestita](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. Compilare il modulo relativo all'istanza gestita con le informazioni richieste, usando le informazioni riportate nella tabella seguente:

   | Impostazione| Valore consigliato | DESCRIZIONE |
   | ------ | --------------- | ----------- |
   | **Sottoscrizione** | Sottoscrizione in uso | Una sottoscrizione in cui si dispone dell'autorizzazione per creare nuove risorse |
   |**Nome istanza gestita**|Qualsiasi nome valido|Per informazioni sui nomi validi, vedere [Regole di denominazione e restrizioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Account di accesso amministratore istanza gestita**|Qualsiasi nome utente valido|Per informazioni sui nomi validi, vedere [Regole di denominazione e restrizioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Non usare "serveradmin" perché è un ruolo a livello di server riservato.| 
   |**Password**|Qualsiasi password valida|La password deve contenere almeno 16 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Gruppo di risorse**|Gruppo di risorse nuovo o esistente|Per i nomi di gruppi di risorse validi, vedere [Regole di denominazione e restrizioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Posizione**|La posizione in cui si desidera creare l'istanza gestita|Per informazioni sulle aree, vedere [Aree di Azure](https://azure.microsoft.com/regions/).|
   |**Rete virtuale**|Selezionare **Crea nuova rete virtuale** o una rete virtuale creata in precedenza nel gruppo di risorse fornito in precedenza in questo modulo| Per configurare una rete virtuale per un'istanza gestita con impostazioni personalizzate, vedere [Configurare un'istanza gestita di SQL in un modello di ambiente di rete virtuale](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) in Github. Per informazioni sui requisiti per la configurazione dell'ambiente di rete per un'istanza gestita, vedere [Configurare una rete virtuale per Istanza gestita di database SQL di Azure](sql-database-managed-instance-vnet-configuration.md) |

   ![modulo istanza gestita](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. Fare clic su **Piano tariffario** per definire le dimensioni delle risorse di calcolo e di archiviazione ed esaminare le opzioni del piano tariffario. Il piano tariffario di utilizzo generico con 32 GB di memoria e 16 vCore è il valore predefinito.
6. Usare i dispositivi di scorrimento o le caselle di testo per specificare la quantità di spazio di archiviazione e il numero di core virtuali. 
7. Al termine, fare clic su **Applica** per salvare le impostazioni selezionate.  
8. Fare clic su **Crea** per distribuire l'istanza gestita.
9. Fare clic sull'icona **Notifiche** per visualizzare lo stato della distribuzione.

    ![stato di distribuzione dell'istanza gestita](./media/sql-database-managed-instance-get-started/deployment-progress.png)

10. Fare clic su **Distribuzione in corso** per aprire la finestra dell'istanza gestita e monitorare ulteriormente lo stato di avanzamento della distribuzione. 

> [!IMPORTANT]
> Per la prima istanza in una subnet, i tempi di distribuzione sono in genere molto più lunghi rispetto alle istanze successive. Non annullare l'operazione di distribuzione perché la durata è superiore al previsto. Creare la seconda istanza gestita nella subnet richiederà solo qualche minuto.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Esaminare le risorse e recuperare il nome completo del server

Dopo aver completato la distribuzione, esaminare le risorse create e recuperare il nome completo del server per l'uso in guide introduttive successive.

1. Aprire il gruppo di risorse per l'istanza gestita e visualizzare le relative risorse che sono state create automaticamente nella guida introduttiva [Creare un'istanza gestita](sql-database-managed-instance-get-started.md).

   ![Risorse Istanza gestita](./media/sql-database-managed-instance-get-started/resources.png)Aprire la risorsa Istanza gestita nel portale di Azure.

2. Fare clic sull'Istanza gestita.
3. Nella scheda **Panoramica** individuare la proprietà **Host** e copiare l'indirizzo completo dell'host per l'istanza gestita.


   ![Risorse Istanza gestita](./media/sql-database-managed-instance-get-started/host-name.png)

   Il nome è simile al seguente: **your_machine_name.neu15011648751ff.database.windows.net**.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla connessione a un'istanza gestita, vedere:
  - Per una panoramica delle opzioni di connessione per le applicazioni, vedere [Connettere le applicazioni a un'istanza gestita](sql-database-managed-instance-connect-app.md).
  - Per una guida introduttiva che illustra come connettersi a un'istanza gestita tramite una macchina virtuale di Azure, vedere [Configurare una connessione tramite macchina virtuale di Azure](sql-database-managed-instance-configure-vm.md).
  - Per una guida introduttiva che illustra come connettersi a un'istanza gestita tramite un computer client locale usando una connessione da punto a sito, vedere [Configurare una connessione da punto a sito](sql-database-managed-instance-configure-p2s.md).
- Per ripristinare un database SQL Server esistente da locale a un'istanza gestita, è possibile eseguire il ripristino da un file di backup del database usando il [Servizio Migrazione del database di Azure (DMS) per la migrazione](../dms/tutorial-sql-server-to-managed-instance.md) o usare il [comando T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) per eseguire un'importazione da un file di backup.
- Per informazioni sul monitoraggio avanzato delle prestazioni del database di Istanza gestita con intelligence predefinita per la risoluzione dei problemi, vedere [Monitorare il database SQL di Azure usando Analisi SQL di Azure](../log-analytics/log-analytics-azure-sql.md).
