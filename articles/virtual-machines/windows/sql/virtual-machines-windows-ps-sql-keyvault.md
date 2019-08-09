---
title: Integrare il Key Vault con SQL Server in VM Windows in Azure (Resource Manager) | Documentazione Microsoft
description: Informazioni su come automatizzare la configurazione della crittografia di SQL Server per l'uso con Azure Key Vault. Questo argomento illustra come usare l'integrazione dell'insieme di credenziali delle chiavi di Azure con le macchine virtuali di SQL Server create con Gestione risorse.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: e7de54f7da8cef5942a8d8f41031eaf3e2565580
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846241"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Configurare l'integrazione di Azure Key Vault per SQL Server in macchine virtuali di Azure (Resource Manager)

> [!div class="op_single_selector"]
> * [Gestione risorse](virtual-machines-windows-ps-sql-keyvault.md)
> * [Classico](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Panoramica
Esistono più funzionalità di crittografia di SQL Server, ad esempio [Transparent Data Encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [crittografia a livello di colonna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) e [crittografia di backup](https://msdn.microsoft.com/library/dn449489.aspx). Queste modalità di crittografia richiedono la gestione e l'archiviazione delle chiavi usate per la crittografia. Il servizio dell'insieme di credenziali delle chiavi di Azure (AKV) è progettato per migliorare la sicurezza e la gestione di queste chiavi in una posizione sicura e a elevata disponibilità. Il [connettore di SQL Server](https://www.microsoft.com/download/details.aspx?id=45344) consente a SQL Server di usare queste chiavi dall'insieme di credenziali delle chiavi di Azure.

Se si esegue SQL Server con computer locali, sono disponibili [passaggi per accedere all'insieme di credenziali delle chiavi di Azure dal computer locale con SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Se si esegue SQL Server in macchine virtuali di Azure, è possibile risparmiare tempo usando la funzionalità di *Integrazione dell'insieme di credenziali delle chiavi di Azure* .

Quando questa funzionalità è abilitata, installa automaticamente il connettore di SQL Server, configura il provider EKM per accedere all'insieme di credenziali delle chiavi di Azure e crea le credenziali per consentire l'accesso all'insieme di credenziali. Se sono stati esaminati i passaggi nella documentazione locale menzionati in precedenza, si noterà che questa funzionalità consente di automatizzare i passaggi 2 e 3. L'unica attività che è comunque necessario eseguire manualmente è la creazione delle chiavi e dell'insieme di credenziali delle chiavi. Una volta completata questa operazione, l'intera installazione della macchina virtuale di SQL è automatizzata. Quando la funzionalità ha completato l'installazione, è possibile eseguire istruzioni T-SQL per iniziare la crittografia dei database o del backup regolarmente.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > Il Provider EKM versione 1.0.4.0 è installato nella VM di SQL Server tramite l'[estensione SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension). Aggiornare l'estensione SQL IaaS non comporterà l'aggiornamento della versione del provider. Si prega di considerare la possibilità di aggiornare manualmente la versione del provider EKM, se necessario (ad esempio, durante la migrazione a un'istanza gestita di database SQL).


## <a name="enabling-and-configuring-akv-integration"></a>Abilitazione e configurazione dell'integrazione di AKV
È possibile abilitare l'integrazione di AKV durante il provisioning oppure configurarlo per VM esistenti.

### <a name="new-vms"></a>Nuove VM
Se si esegue il provisioning di una nuova macchina virtuale SQL Server con Gestione risorse, il portale di Azure offre una procedura per abilitare l'integrazione di Azure Key Vault. La funzionalità Azure Key Vault è disponibile solo per SQL Server Enterprise Edition, Developer Edition e le edizioni di valutazione.

![Integrazione dell'insieme di credenziali delle chiavi di Azure per SQL](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Per una procedura dettagliata del provisioning, vedere effettuare il provisioning di [una macchina virtuale SQL Server nel portale di Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>VM esistenti

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Per le macchine virtuali SQL Server esistenti, aprire la [risorsa macchine virtuali SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) e selezionare **sicurezza** in **Impostazioni**. Selezionare **Abilita** per abilitare l'integrazione Azure Key Vault. 

![Integrazione di AKV SQL per le VM esistenti](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

Al termine, selezionare il pulsante **applica** nella parte inferiore della pagina **sicurezza** per salvare le modifiche.

> [!NOTE]
> Il nome delle credenziali creato qui verrà mappato a un account di accesso SQL in un secondo momento. In questo modo, l'account di accesso SQL può accedere all'insieme di credenziali delle chiavi. 


> [!NOTE]
> È inoltre possibile configurare l'integrazione di AKV mediante un modello. Per altre informazioni, vedere l'articolo relativo al [modello di avvio rapido di Azure per l'integrazione dell'insieme di credenziali delle chiavi di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).


[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]
