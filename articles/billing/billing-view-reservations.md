---
title: Visualizzare le prenotazioni di Azure | Microsoft Docs
description: Informazioni su come visualizzare le prenotazioni di Azure nel portale di Azure.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2018
ms.author: cwatson
ms.openlocfilehash: 2ae30ca55f3ca03a64438025960ddd807e288216
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2018
ms.locfileid: "48272602"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>Visualizzare le prenotazioni di Azure nel portale di Azure

A seconda del tipo di sottoscrizione e di autorizzazioni, esistono due modi per visualizzare le prenotazioni di Azure.

## <a name="view-reservations-as-owner-or-reader"></a>Visualizzare le prenotazioni come proprietario o come lettore

Per impostazione predefinita, quando si acquista una prenotazione, l'utente e l'amministratore dell'account possono visualizzare la prenotazione. L'utente e l'amministratore dell'account ottengono automaticamente il ruolo di proprietario per la prenotazione. Per consentire ad altri utenti di visualizzare la prenotazione, è necessario aggiungerli come **Proprietario** oppure **Lettore** alla prenotazione. Per altre informazioni vedere [Aggiungere o modificare gli utenti che possono gestire una prenotazione](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
 
Per visualizzare una prenotazione come proprietario o lettore,

1. Accedere al [portale di Azure]( http://portal.azure.com).
1. Cercare **Prenotazioni**.

    ![Screenshot che mostra una ricerca nel portale di Azure](./media/billing-view-reservation/portal-reservation-search.png)

1. Viene visualizzato un elenco delle prenotazioni per le quali si ha il ruolo di Proprietario o Lettore.

Se è necessario modificare l'ambito di una prenotazione, dividere una prenotazione, oppure modificare chi può gestire una prenotazione, vedere [gestire le prenotazioni Azure](billing-manage-reserved-vm-instance.md).

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>Visualizzare le transazioni di prenotazione per le registrazioni Enterprise

 Se si dispone di una registrazione Enterprise gestita dal partner, visualizzare le prenotazioni in **Report** nel portale EA. Per altre registrazioni Enterprise, è possibile visualizzare le prenotazioni nel portale EA e nel portale di Azure. È necessario essere un amministratore EA per visualizzare le transazioni di prenotazione.

Per visualizzare le transazioni di prenotazione nel portale di Azure,

1. Accedere al [portale di Azure]( http://portal.azure.com).
1. Eseguire ricerche in **Gestione dei costi e fatturazione**.

    ![Screenshot che mostra una ricerca nel portale di Azure](./media/billing-view-reservation/portal-cm-billing-search.png)

1. Selezionare **Transazioni di prenotazione**.
1. Per filtrare i risultati, selezionare **Intervallo di tempo**, **Tipo**, o **Descrizione**.
1. Selezionare **Applica**.

    ![Screenshot che mostra i risultati delle transazioni di prenotazione](./media/billing-view-reservation/portal-billing-reservation-transaction-results.png)

Per ottenere i dati usando un'API, vedere [Get Reserved Instance transaction charges for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) (Ottenere le tariffe di transazione di istanza riservata per i clienti Enterprise).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md)
- [Pagare in anticipo la capacità riservata di Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Pagare in anticipo le risorse di calcolo del database SQL con capacità riservata del database SQL di Azure](../sql-database/sql-database-reserved-capacity.md)
- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gestire le prenotazioni di Azure](billing-manage-reserved-vm-instance.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Informazioni sull'utilizzo della prenotazione per sottoscrizioni CSP](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
