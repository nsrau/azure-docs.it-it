---
title: "Portale di Azure: Creare un'istanza gestita di SQL | Microsoft Docs"
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
ms.date: 03/13/2019
ms.openlocfilehash: 6aa8f362f067a4e3e391f435ee849f96abdf752f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57997505"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Avvio rapido: Creare un'istanza gestita di database SQL di Azure

Questa guida introduttiva illustra come creare un'[istanza gestita](sql-database-managed-instance.md) di database SQL di Azure nel portale di Azure.

> [!IMPORTANT]
> Per le limitazioni, vedere le [aree supportate](sql-database-managed-instance-resource-limits.md#supported-regions) e i [tipi di sottoscrizione](sql-database-managed-instance-resource-limits.md#supported-subscription-types) supportati.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Creare un'istanza gestita

I passaggi seguenti mostrano come creare un'istanza gestita.

1. Scegliere **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
2. Individuare **Istanza gestita** e quindi selezionare **Istanza gestita di SQL di Azure**.
3. Selezionare **Create**.

   ![Creare un'istanza gestita](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. Compilare il modulo **Istanza gestita di SQL** con le informazioni necessario, riportate nella tabella seguente:

   | Impostazione| Valore consigliato | DESCRIZIONE |
   | ------ | --------------- | ----------- |
   | **Sottoscrizione** | Sottoscrizione in uso | Una sottoscrizione in cui si dispone dell'autorizzazione per creare nuove risorse |
   |**Nome istanza gestita**|Qualsiasi nome valido|Per informazioni sui nomi validi, vedere [Regole di denominazione e restrizioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Account di accesso amministratore istanza gestita**|Qualsiasi nome utente valido|Per informazioni sui nomi validi, vedere [Regole di denominazione e restrizioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Non usare "serveradmin" perché è un ruolo a livello di server riservato.|
   |**Password**|Qualsiasi password valida|La password deve contenere almeno 16 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Regole di confronto**|Le regole di confronto da usare per l'istanza gestita|In caso di migrazione di database da SQL Server, controllare le regole di confronto di origine con `SELECT SERVERPROPERTY(N'Collation')` e usare tale valore. Per informazioni sulle regole di confronto, vedere l'articolo relativo alle [regole di confronto a livello di server](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|
   |**Posizione**|La posizione in cui creare l'istanza gestita|Per informazioni sulle aree, vedere [Aree di Azure](https://azure.microsoft.com/regions/).|
   |**Rete virtuale**|Selezionare **Crea nuova rete virtuale** o una rete virtuale valida e una subnet.| Se una rete/subnet non è disponibile, prima di selezionarla come destinazione per la nuova istanza gestita è necessario [modificarla per soddisfare i requisiti di rete](sql-database-managed-instance-configure-vnet-subnet.md). Per informazioni sui requisiti per la configurazione dell'ambiente di rete per un'istanza gestita, vedere [Configurare una rete virtuale per Istanza gestita](sql-database-managed-instance-connectivity-architecture.md). |
   |**Gruppo di risorse**|Gruppo di risorse nuovo o esistente|Per i nomi di gruppi di risorse validi, vedere [Regole di denominazione e restrizioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|

   ![modulo istanza gestita](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. Per usare l'istanza gestita come gruppo di failover dell'istanza secondario, selezionare il checkout e specificare l'istanza gestita DnsAzurePartner. Questa funzionalità è disponibile in anteprima e non è visualizzata nello screenshot associato.
6. Selezionare **Piano tariffario** per definire le dimensioni delle risorse di calcolo e di archiviazione ed esaminare le opzioni del piano tariffario. Il piano tariffario di utilizzo generico con 32 GB di memoria e 16 vCore è il valore predefinito.
7. Usare i dispositivi di scorrimento o le caselle di testo per specificare la quantità di spazio di archiviazione e il numero di core virtuali.
8. Al termine, scegliere **Applica** per salvare le impostazioni selezionate.  
9. Selezionare **Crea** per distribuire l'istanza gestita.
10. Selezionare l'icona **Notifiche** per visualizzare lo stato della distribuzione.

    ![stato di distribuzione dell'istanza gestita](./media/sql-database-managed-instance-get-started/deployment-progress.png)

11. Selezionare **Distribuzione in corso** per aprire la finestra dell'istanza gestita e monitorare ulteriormente lo stato di avanzamento della distribuzione.

> [!IMPORTANT]
> Per la prima istanza in una subnet, i tempi di distribuzione sono in genere molto più lunghi rispetto alle istanze successive. Non annullare l'operazione di distribuzione perché la durata è superiore al previsto. La creazione della seconda istanza gestita nella subnet richiederà solo qualche minuto.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Esaminare le risorse e recuperare il nome completo del server

Dopo aver completato la distribuzione, esaminare le risorse create e recuperare il nome completo del server per l'uso in guide introduttive successive.

1. Aprire il gruppo di risorse per l'istanza gestita e visualizzare le relative risorse che sono state create automaticamente nella guida introduttiva [Creare un'istanza gestita](#create-a-managed-instance).

   ![Risorse dell'istanza gestita](./media/sql-database-managed-instance-get-started/resources.png)

2. Selezionare la tabella di route per esaminare la tabella di route definita dall'utente creata automaticamente.

   ![Tabella di route](./media/sql-database-managed-instance-get-started/route-table.png)

3. Nella tabella di route esaminare le voci per instradare il traffico da e all'interno della rete virtuale dell'istanza gestita. Se la tabella di route viene creata o configurata manualmente, è necessario assicurarsi di includere queste voci.

   ![Voce per la subnet dell'istanza gestita impostata su locale](./media/sql-database-managed-instance-get-started/udr.png)

4. Tornare nel gruppo di risorse e selezionare il gruppo di sicurezza di rete per esaminare le regole di sicurezza.

   ![Gruppo di sicurezza di rete](./media/sql-database-managed-instance-get-started/network-security-group.png)

5. Esaminare le regole di sicurezza in ingresso e in uscita.

   ![Regole di sicurezza](./media/sql-database-managed-instance-get-started/security-rules.png)

6. Tornare nel gruppo di risorse e selezionare l'istanza gestita.

   ![Istanza gestita](./media/sql-database-managed-instance-get-started/managed-instance.png)

7. Nella scheda **Panoramica** individuare la proprietà **Host** e copiare l'indirizzo completo dell'host completo per l'istanza gestita da usare nella guida introduttiva successiva.

   ![Nome host](./media/sql-database-managed-instance-get-started/host-name.png)

   Il nome sarà simile a **nome_computer.a1b2c3d4e5f6.database.windows.net**.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla connessione a un'istanza gestita, vedere:
  - Per una panoramica delle opzioni di connessione delle applicazioni, vedere [Connessione delle applicazioni a un'Istanza gestita](sql-database-managed-instance-connect-app.md).
  - Per una guida introduttiva che illustra come connettersi a un'istanza gestita tramite una macchina virtuale di Azure, vedere [Configurare una connessione tramite macchina virtuale di Azure](sql-database-managed-instance-configure-vm.md).
  - Per una guida introduttiva che illustra come connettersi a un'istanza gestita tramite un computer client locale usando una connessione da punto a sito, vedere [Configurare una connessione da punto a sito](sql-database-managed-instance-configure-p2s.md).
- Per ripristinare un database SQL Server esistente da locale a un'istanza gestita, è possibile eseguire il ripristino da un file di backup del database usando il [Servizio Migrazione del database di Azure (DMS) per la migrazione](../dms/tutorial-sql-server-to-managed-instance.md) o usare il [comando T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) per eseguire un'importazione da un file di backup.
- Per informazioni sul monitoraggio avanzato delle prestazioni del database dell'istanza gestita con intelligence predefinita per la risoluzione dei problemi, vedere [Monitorare il database SQL di Azure usando Analisi SQL di Azure](../azure-monitor/insights/azure-sql.md)
