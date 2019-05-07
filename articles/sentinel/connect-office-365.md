---
title: Connetti i dati di Office 365 all'anteprima di Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di Office 365 a Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: ff7c862e-2e23-4a28-bd18-f2924a30899d
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 6b1e167d26b5848238dd51bf9792f8316c33a385
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205558"
---
# <a name="connect-data-from-office-365-logs"></a>Connetti i dati dai log di Office 365

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile trasmettere i log di controllo da [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) in Azure Sentinel con un solo clic. È possibile trasmettere i log di controllo da più tenant per una singola area di lavoro in Azure Sentinel. Il connettore di log attività di Office 365 offre informazioni approfondite le attività degli utenti in corso. Otterrai informazioni sui vari utente, amministratore, sistema e le azioni dei criteri e gli eventi da Office 365. Connettendo i log di Office 365 in Azure Sentinel è possibile utilizzare questi dati per visualizzare i dashboard, creare avvisi personalizzati e migliorare il processo di analisi.


## <a name="prerequisites"></a>Prerequisiti

- È necessario essere un amministratore globale o amministratore della sicurezza nel tenant
- Nel computer da cui è registrato in Azure Sentinel per creare la connessione, assicurarsi che la porta 4433 sia aperta per il traffico web.

## <a name="connect-to-office-365"></a>Connettersi a Office 365

1. In Azure Sentinel, selezionare **connettori di dati** e quindi fare clic sui **Office 365** riquadro.

2. Se non è già abilitata, in **Connection** usare i **abilitare** pulsante per abilitare la soluzione Office 365. Se è già stata abilitata, verrà identificata nella schermata di connessione come già abilitata.
1. Office 365 consente di trasmettere dati da più tenant per Azure Sentinel. Per ogni tenant che si desidera connettersi, aggiungere il tenant sotto **connettono i tenant per Azure Sentinel**. 
1. Viene aperta una schermata di Active Directory. Viene chiesto di eseguire l'autenticazione con un utente amministratore globale per ogni tenant a cui che si desidera connettersi a Azure Sentinel e concedere le autorizzazioni al Azure Sentinel per leggere i log. 
5. Nel log attività di Stream Office 365, fare clic su **seleziona** per scegliere quali tipi di log si vuole trasmettere a Sentinel di Azure. Attualmente, Azure Sentinel supporta Exchange e SharePoint.

4. Fare clic su **applicare le modifiche**.

3. Per usare lo schema appropriato nel Log Analitica per i log di Office 365, cercare **OfficeActivity**.


## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato descritto come connettere Office 365 a Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere la visibilità di dati e le potenziali minacce](quickstart-get-visibility.md).
- Iniziare a usare [rilevando minacce con Azure Sentinel](tutorial-detect-threats.md).

