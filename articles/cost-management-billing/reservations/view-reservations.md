---
title: Visualizzare le prenotazioni per le risorse di Azure | Microsoft Docs
description: Informazioni su come visualizzare le prenotazioni per Azure nel portale di Azure.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: fd5d4248e9b1e645628ac8b87df3b4b0a16e2049
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87288962"
---
# <a name="view-azure-reservations"></a>Visualizzare le prenotazioni di Azure

È possibile visualizzare e gestire la prenotazione acquistata dal portale di Azure.

## <a name="permissions-to-view-a-reservation"></a>Autorizzazioni per la visualizzazione di una prenotazione

Per visualizzare o gestire una prenotazione, è necessario disporre dell'autorizzazione di lettore o di proprietario. Per impostazione predefinita, quando si acquista una prenotazione l'utente e l'amministratore dell'account ottengono automaticamente il ruolo di proprietario per l'ordine di prenotazione e la prenotazione. Per consentire ad altri utenti di visualizzare la prenotazione, è necessario aggiungerli con il ruolo di **Proprietario** o **Lettore** all'ordine di prenotazione o alla prenotazione. Se si aggiunge un utente alla sottoscrizione fornita per la fatturazione della prenotazione, questo non viene aggiunto automaticamente alla prenotazione. 

Per altre informazioni vedere [Aggiungere o modificare gli utenti che possono gestire una prenotazione](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

## <a name="view-reservation-and-utilization-in-the-azure-portal"></a>Visualizzare la prenotazione e l'utilizzo nel portale di Azure

Per visualizzare una prenotazione come proprietario o lettore

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare a [Prenotazioni](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
3. L'elenco mostra tutte le prenotazioni per le quali si ha il ruolo di proprietario o lettore. Ogni prenotazione mostra l'ultima percentuale di utilizzo nota.
4. Fare clic sulla percentuale di utilizzo per visualizzare la cronologia di utilizzo e i dettagli. Vedere i dettagli nel video seguente.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="get-reservations-and-utilization-using-apis-powershell-cli"></a>Ottenere le prenotazioni e l'utilizzo con le API, PowerShell e l'interfaccia della riga di comando

Ottenere l'elenco di tutte le prenotazioni usando le risorse seguenti
- [API: ordine di prenotazione/elenco](/rest/api/reserved-vm-instances/reservationorder/list)
- [PowerShell: ordine di prenotazione/elenco](/powershell/module/azurerm.reservations/get-azurermreservationorder)
- [Interfaccia della riga di comando: ordine di prenotazione/elenco](/cli/azure/reservations/reservation-order#az-reservations-reservation-order-list)

È anche possibile ottenere l'[utilizzo della prenotazione](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) usando l'API Utilizzo istanze riservate. 

## <a name="see-reservations-and-utilization-in-power-bi"></a>Visualizzare le prenotazioni e l'utilizzo in Power BI

Per gli utenti di Power BI sono disponibili due opzioni
- Pacchetto di contenuto: gli acquisti delle prenotazioni e i dati di utilizzo sono disponibili nel [pacchetto di contenuto Informazioni dettagliate sul consumo di Power BI](/power-bi/desktop-connect-azure-cost-management). Creare i report desiderati con questo pacchetto di contenuto. 
- App Gestione costi: usare l'app [Gestione costi](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) per ottenere i report predefiniti che è possibile personalizzare ulteriormente.

## <a name="next-steps"></a>Passaggi successivi

- [Gestire le prenotazioni di Azure](manage-reserved-vm-instance.md).
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](understand-reserved-instance-usage-ea.md)
- [Informazioni sull'utilizzo della prenotazione per sottoscrizioni CSP](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).
