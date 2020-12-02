---
title: Risolvere i problemi di connettività tra Synapse Studio e archiviazione
description: Risolvere i problemi di connettività tra Synapse Studio e archiviazione
author: saveenr
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: cee6d030a9639a7203a32a3c0957733cecb1f8b6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445314"
---
# <a name="troubleshoot-connectivity-between-azure-synapse-analytics-synapse-studio-and-storage"></a>Risolvere i problemi di connettività tra Azure Synapse Analytics Synapse Studio e la risorsa di archiviazione

In sinapsi studio è possibile esplorare le risorse dei dati presenti nella risorsa di archiviazione collegata. Questa guida consente di risolvere i problemi di connettività quando si tenta di accedere alle risorse di dati. 

## <a name="case-1-storage-account-lacks-proper-permissions"></a>Caso #1: l'account di archiviazione non dispone delle autorizzazioni appropriate

Se l'account di archiviazione non dispone delle autorizzazioni appropriate, non è possibile espandere la struttura di archiviazione tramite "data", > "Linked" in sinapsi Studio. Vedere la schermata del sintomo del problema riportata di seguito. 

Il messaggio di errore dettagliato può variare, ma il significato generale del messaggio di errore è: "questa richiesta non è autorizzata per eseguire questa operazione".

![Problema di connettività di archiviazione 1](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue.1.png)

**Soluzione**: per assegnare l'account al ruolo appropriato, vedere [usare la portale di Azure per assegnare un ruolo di Azure per l'accesso ai dati BLOB e di Accodamento](../../storage/common/storage-auth-aad-rbac-portal.md)


## <a name="case-2-failed-to-send-the-request-to-storage-server"></a>Case #2: non è stato possibile inviare la richiesta al server di archiviazione

Quando si seleziona la freccia per espandere la struttura di archiviazione in "dati"-> "collegato" in sinapsi studio, è possibile che venga visualizzato il problema "REQUEST_SEND_ERROR" nel pannello di sinistra. Vedere la schermata del sintomo del problema seguente:

![Problema di connettività dell'archiviazione 2](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue.2.png)

Questo problema può essere dovuto a diversi motivi:

### <a name="the-storage-resource-is-behind-a-vnet-and-a-storage-private-endpoint-needs-to-configure"></a>La risorsa di archiviazione si trova dietro un vNet e un endpoint privato di archiviazione deve configurare

**Soluzione**: in questo caso, è necessario configurare l'endpoint privato di archiviazione per l'account di archiviazione. Per informazioni su come configurare l'endpoint privato di archiviazione per vNet, vedere [usare la portale di Azure per assegnare un ruolo di Azure per l'accesso ai dati BLOB e di Accodamento](../security/how-to-connect-to-workspace-from-restricted-network.md).

È possibile usare il comando "nslookup \<storage-account-name\> . DFS.Core.Windows.NET" per verificare la connettività dopo la configurazione dell'endpoint privato di archiviazione. Deve restituire una stringa simile a: " \<storage-account-name\> . privatelink.DFS.Core.Windows.NET".

### <a name="the-storage-resource-is-not-behind-a-vnet-but-the-blob-service-azure-ad-endpoint-is-not-accessible-due-to-firewall-configured"></a>La risorsa di archiviazione non si trova dietro un vNet ma l'endpoint del servizio BLOB (Azure AD) non è accessibile a causa del firewall configurato

**Soluzione**: in questo caso, è necessario aprire l'account di archiviazione nell'portale di Azure. Nella barra di spostamento a sinistra scorrere verso il basso fino a **supporto e risoluzione dei problemi** e selezionare **Verifica connettività** per verificare lo stato di connettività del **servizio BLOB (Azure ad)** . Se non è accessibile, seguire la guida promossa per verificare la configurazione dei **firewall e delle reti virtuali** nella pagina dell'account di archiviazione. Per altre informazioni sui firewall di archiviazione, vedere [configurare i firewall e le reti virtuali di archiviazione di Azure](../../storage/common/storage-network-security.md).

### <a name="other-issues-to-check"></a>Altri problemi da controllare 

* La risorsa di archiviazione a cui si accede è Azure Data Lake Storage Gen2 ed è dietro un firewall e vNet (con l'endpoint privato di archiviazione configurato) allo stesso tempo.
* La risorsa contenitore a cui si accede è stata eliminata o non esiste.


## <a name="next-steps"></a>Passaggi successivi
Se la procedura precedente non consente di risolvere il problema, [creare un ticket di supporto](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).
