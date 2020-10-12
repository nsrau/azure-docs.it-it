---
title: Integrare il Key Vault con SQL Server in VM Windows in Azure (Resource Manager) | Documentazione Microsoft
description: Informazioni su come automatizzare la configurazione della crittografia di SQL Server per l'uso con Azure Key Vault. Questo argomento illustra come usare l'integrazione dell'insieme di credenziali delle chiavi di Azure con le macchine virtuali SQL create con Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 7fab8db1fcc02e26d1b19d3889414565ff56351b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91293559"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-resource-manager"></a>Configurare l'integrazione di Azure Key Vault per SQL Server nelle macchine virtuali di Azure (Resource Manager)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Esistono più funzionalità di crittografia di SQL Server, ad esempio [Transparent Data Encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [crittografia a livello di colonna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) e [crittografia di backup](https://msdn.microsoft.com/library/dn449489.aspx). Queste modalità di crittografia richiedono la gestione e l'archiviazione delle chiavi usate per la crittografia. Il servizio Azure Key Vault è progettato per migliorare la sicurezza e la gestione di queste chiavi in una posizione sicura e a elevata disponibilità. Il [connettore di SQL Server](https://www.microsoft.com/download/details.aspx?id=45344) consente a SQL Server di usare queste chiavi dall'insieme di credenziali delle chiavi di Azure.

Se si esegue SQL Server localmente, è possibile seguire una procedura per [accedere ad Azure Key Vault dall'istanza di SQL Server locale](https://msdn.microsoft.com/library/dn198405.aspx). Se invece si esegue SQL Server in macchine virtuali di Azure, è possibile risparmiare tempo usando la funzionalità *Integrazione di Azure Key Vault*.

Quando questa funzionalità è abilitata, installa automaticamente il connettore di SQL Server, configura il provider EKM per accedere all'insieme di credenziali delle chiavi di Azure e crea le credenziali per consentire l'accesso all'insieme di credenziali. Se sono stati esaminati i passaggi nella documentazione locale menzionati in precedenza, si noterà che questa funzionalità consente di automatizzare i passaggi 2 e 3. L'unica attività che è comunque necessario eseguire manualmente è la creazione delle chiavi e dell'insieme di credenziali delle chiavi. Una volta completata questa operazione, l'intera installazione della macchina virtuale di SQL Server è automatizzata. Quando la funzionalità ha completato l'installazione, è possibile eseguire istruzioni Transact-SQL (T-SQL) per iniziare la crittografia dei database o del backup con le modalità consuete.

[!INCLUDE [Prepare for Key Vault integration](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > Il provider Extensible Key Management (EKM) versione 1.0.4.0 è installato nella VM di SQL Server tramite l'[estensione SQL Infrastructure as a Service (IaaS)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension). Se si aggiorna l'estensione SQL IaaS, la versione del provider non verrà aggiornata. Si prega di considerare la possibilità di aggiornare manualmente la versione del provider EKM, se necessario (ad esempio, durante la migrazione a un'istanza gestita di database SQL).


## <a name="enabling-and-configuring-key-vault-integration"></a>Abilitazione e configurazione dell'integrazione di Key Vault
È possibile abilitare l'integrazione di Key Vault durante il provisioning oppure configurarla per le macchine virtuali esistenti.

### <a name="new-vms"></a>Nuove VM
Se si esegue il provisioning di una nuova macchina virtuale SQL con Resource Manager, il portale di Azure offre una procedura per abilitare l'integrazione di Azure Key Vault. La funzionalità Azure Key Vault è disponibile solo per SQL Server Enterprise Edition, Developer Edition e le edizioni di valutazione.

![Integrazione dell'insieme di credenziali delle chiavi di Azure per SQL](./media/azure-key-vault-integration-configure/azure-sql-arm-akv.png)

Per la procedura dettagliata di provisioning, vedere [Effettuare il provisioning di una macchina virtuale SQL nel portale di Azure](create-sql-vm-portal.md).

### <a name="existing-vms"></a>VM esistenti

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Per le macchine virtuali SQL esistenti, aprire la [risorsa Macchine virtuali SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) e selezionare **Sicurezza** in **Impostazioni**. Selezionare **Abilita** per abilitare l'integrazione di Azure Key Vault. 

![Integrazione di SQL Key Vault per le macchine virtuali esistenti](./media/azure-key-vault-integration-configure/azure-sql-rm-akv-existing-vms.png)

Al termine, fare clic sul pulsante **Applica** nella parte inferiore della pagina **Sicurezza** per salvare le modifiche.

> [!NOTE]
> Il nome delle credenziali creato in questo passaggio verrà mappato a un account di accesso SQL in un secondo momento. In questo modo, l'account di accesso SQL può accedere all'insieme di credenziali delle chiavi. 


> [!NOTE]
> È anche possibile configurare l'integrazione Key Vault usando un modello. Per altre informazioni, vedere l'articolo relativo al [modello di avvio rapido di Azure per l'integrazione dell'insieme di credenziali delle chiavi di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).


[!INCLUDE [Key Vault integration next steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]
