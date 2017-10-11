---
title: Considerazioni relative alla sicurezza per SQL Server in Macchine virtuali di Azure | Microsoft Docs
description: Questo argomento include linee guida generali per proteggere SQL Server in esecuzione in una macchina virtuale di Azure.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2017
ms.author: jroth
ms.openlocfilehash: 4ad9156e481eac0bae32bca35a2b126363e5d8b6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Considerazioni relative alla sicurezza per SQL Server in Macchine virtuali di Azure

Questo argomento include linee guida di sicurezza generali che consentono di stabilire l'accesso sicuro alle istanze di SQL Server in una macchina virtuale (VM) di Azure.

Azure è conforme a molteplici normative e standard di settore che possono favorire la compilazione di una soluzione compatibile con SQL Server in esecuzione in una macchina virtuale. Per informazioni sulla conformità alle normative con Azure, vedere il [Centro protezione di Azure](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>Controllare l'accesso alla VM SQL

Quando si crea la macchina virtuale SQL Server, è importante controllare attentamente chi ha accesso al computer e a SQL Server. Come prassi generale è consigliabile eseguire le operazioni seguenti:

- Limitare l'accesso a SQL Server solo alle applicazioni e ai client che ne hanno necessità.
- Seguire le procedure consigliate per la gestione di account utente e password.

Le sezioni seguenti offrono alcuni suggerimenti e alcune considerazioni su questi punti.

## <a name="secure-connections"></a>Connessioni sicure

Quando si crea una macchina virtuale di SQL Server con un'immagine della raccolta, l'opzione **SQL Server Connectivity** (Connettività SQL Server) offre la possibilità di scegliere tra **Locale (all'interno della macchina virtuale)**, **Privata (solo all'interno della rete virtuale)** o **Pubblico (Internet)**.

![Connettività di SQL Server](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

Per ottimizzare la sicurezza, scegliere l'opzione più restrittiva per lo scenario in uso. Se ad esempio si esegue un'applicazione che accede a SQL Server nella stessa VM, **Locale** è la scelta più sicura. Se si esegue un'applicazione Azure che richiede l'accesso a SQL Server, l'opzione **Privata** consente di proteggere la comunicazione a SQL Server solo all'interno della [rete virtuale di Azure](../../../virtual-network/virtual-networks-overview.md) specificata. Se è richiesto l'accesso **Pubblico** (Internet) alla VM di SQL Server, assicurarsi di seguire le altre procedure consigliate in questo argomento per ridurre la superficie di attacco.

Le opzioni selezionate nel portale usano le regole di sicurezza in ingresso nel [Gruppo di sicurezza di rete](../../../virtual-network/virtual-networks-nsg.md) (NSG) di VM per consentire o negare il traffico di rete alla macchina virtuale. È possibile modificare o creare nuove regole NSG in ingresso per consentire il traffico verso la porta SQL Server (porta predefinita 1433). È anche possibile specificare indirizzi IP specifici che sono autorizzati a comunicare tramite questa porta.

![Regole dei gruppi di sicurezza di rete](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

Oltre alle regole NSG per limitare il traffico di rete, è anche possibile usare Windows Firewall nella macchina virtuale.

Se si usano gli endpoint con un modello di distribuzione classico, rimuovere tutti gli endpoint nella macchina virtuale che non vengono usati. Per istruzioni sull'uso di ACL con gli endpoint, vedere [Gestire l'elenco di controllo di accesso su un endpoint](../classic/setup-endpoints.md#manage-the-acl-on-an-endpoint). Questa operazione non è necessaria per le VM che usano Resource Manager.

Occorre infine considerare di abilitare le connessioni crittografate per l'istanza del motore di database SQL Server nella macchina virtuale di Azure. Configurare l'istanza di SQL server con un certificato firmato. Per altre informazioni, vedere [Abilitazione di connessioni crittografate al Motore di database](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) e [Sintassi della stringa di connessione](https://msdn.microsoft.com/library/ms254500.aspx).

## <a name="use-a-non-default-port"></a>Usare una porta diversa da quella predefinita

Per impostazione predefinita, SQL Server è in ascolto sulla porta 1433 che tutti conoscono. Per una maggiore sicurezza, configurare SQL Server per l'ascolto su una porta non predefinita, ad esempio la porta 1401. Se si esegue il provisioning di un'immagine della raccolta di SQL Server nel portale di Azure, è possibile specificare questa porta nel pannello **Impostazioni di SQL Server**.

Per configurare questa porta dopo il provisioning, sono disponibili due opzioni:

- Per le macchine virtuali di Resource Manager, è possibile selezionare **Configurazione di SQL Server** dal pannello di panoramica della macchina virtuale. Da qui è possibile modificare la porta.

  ![Modificare la porta TCP nel portale](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- Per le VM classiche o per le VM di SQL Server di cui non è stato eseguito il provisioning con il portale, è possibile configurare la porta manualmente tramite connessione remota alla VM. Per i passaggi di configurazione, vedere [Configure a Server to Listen on a Specific TCP Port](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port) (Configurare un server per l'ascolto su una porta TCP specifica). Se si usa questa tecnica manuale, è necessario anche aggiungere una regola di Windows Firewall per consentire il traffico in ingresso su tale porta TCP.

> [!IMPORTANT]
> Specificare una porta non predefinita è una buona idea se la porta di SQL Server è aperta a connessioni Internet pubbliche.

Quando SQL Server è in ascolto su una porta non predefinita, è necessario specificare la porta quando si esegue la connessione. Si consideri ad esempio uno scenario in cui l'indirizzo IP del server è 13.55.255.255 e SQL Server è in ascolto sulla porta 1401. Per connettersi a SQL Server, è necessario specificare `13.55.255.255,1401` nella stringa di connessione.

## <a name="manage-accounts"></a>Gestisci account

È necessario fare in modo che gli utenti malintenzionati non indovinino facilmente i nomi di account o le password. Usare a tal fine i suggerimenti seguenti:

- Creare un account amministratore locale univoco non denominato **Amministratore**.

- Usare password complesse per tutti gli account. Per altre informazioni sulla creazione di password complesse, vedere l'articolo [Crea una password complessa](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password).

- Per impostazione predefinita, in Azure viene selezionata l'autenticazione di Windows durante l'installazione della macchina virtuale SQL Server. L'account di accesso **SA** è pertanto disabilitato e viene assegnata una password tramite il programma di installazione. È consigliabile non usare o abilitare l'account di accesso **SA**. Se è necessario avere un account di accesso SQL, usare una delle strategie seguenti:

  - Creare un account SQL con un nome univoco che abbia appartenenza **sysadmin**. È possibile creare questo account dal portale attivando **Autenticazione di SQL Server** durante il provisioning.

    > [!TIP] 
    > Se non si attiva l'autenticazione di SQL Server durante il provisioning, è necessario modificare manualmente la modalità di autenticazione in **Autenticazione di SQL Server e di Windows**. Per altre informazioni, vedere [Modifica della modalità di autenticazione del server](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Se è necessario usare l'account di accesso **SA**, attivarlo dopo il provisioning e assegnare una nuova password complessa.

## <a name="follow-on-premises-best-practices"></a>Seguire le procedure consigliate locali

Oltre alle procedure descritte in questo argomento, è consigliabile rivedere e implementare le procedure di sicurezza locali tradizionali, ove applicabili. Per altre informazioni, vedere [Considerazioni sulla sicurezza per un'installazione di SQL Server](https://docs.microsoft.com/sql/sql-server/install/security-considerations-for-a-sql-server-installation).

## <a name="next-steps"></a>Passaggi successivi

Se si è interessati anche alle procedure consigliate relative alle prestazioni, vedere [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-performance.md).

Per altri argomenti relativi all'esecuzione di SQL Server nelle macchine virtuali di Azure, vedere [Panoramica di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).

