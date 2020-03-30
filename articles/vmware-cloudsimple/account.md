---
title: Gestione account - Azure VMware Solution dal portale CloudSimple
description: Descrive come gestire gli account nel portale Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 00348d36c38fdbfdf69a2e4e80a4c720268b40c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025368"
---
# <a name="manage-accounts-on-the-azure-vmware-solution-by-cloudsimple-portal"></a>Gestire gli account nel portale Azure VMware Solution by CloudSimpleManage accounts on the Azure VMware Solution by CloudSimple portal

Quando crei il tuo servizio CloudSimple, crea un account su CloudSimple. L'account è associato alla sottoscrizione di Azure in cui si trova il servizio. Tutti gli utenti con ruoli di proprietario e collaboratore nella sottoscrizione hanno accesso al portale CloudSimple.All users with owner and contributor roles in the subscription have access to the CloudSimple portal. L'ID sottoscrizione di Azure e l'ID tenant associati al servizio CloudSimple sono disponibili nella pagina Account.The Azure subscription ID and tenant ID associated with the CloudSimple service are found on the Accounts page.

Per gestire gli account nel portale CloudSimple, [accedere al portale](access-cloudsimple-portal.md) e selezionare **Account** nel menu laterale.

Selezionare **Riepilogo** per visualizzare informazioni sulla configurazione CloudSimple dell'azienda. Viene illustrata la capacità corrente della configurazione cloud, inclusi il numero di cloud privati, lo spazio di archiviazione totale, la configurazione del cluster vSphere, il numero di nodi e il numero di core di calcolo. Un collegamento è incluso per l'acquisto di nodi aggiuntivi se la configurazione corrente non soddisfa tutte le esigenze.

## <a name="email-alerts"></a>Avvisi posta elettronica

È possibile aggiungere indirizzi di posta elettronica di tutte le persone che si desidera notificare le modifiche apportate alla configurazione del cloud privato.

1. Nell'area **Avvisi di posta elettronica aggiuntivi** fare clic su Aggiungi **nuovo**.
2. Immettere l'indirizzo di posta elettronica.
3. Premere Invio.  

Per rimuovere una voce, fare clic su **X**.

## <a name="cloudsimple-operator-access"></a>Accesso operatore CloudSimple

L'impostazione di accesso dell'operatore consente a CloudSimple di aiutarti nella risoluzione dei problemi consentendo a un tecnico del supporto di accedere al portale CloudSimple.  L'impostazione è abilitata per impostazione predefinita. Tutte le azioni eseguite dal tecnico del supporto quando si è connessi al tuo account cliente vengono registrate e disponibili per la tua revisione nella pagina**Controllo** **attività.** > 

Fare clic sull'interruttore **abilitato per l'accesso all'operatore CloudSimple** per attivare o disattivare l'accesso.
