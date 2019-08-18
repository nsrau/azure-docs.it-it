---
title: 'Gestione degli account: soluzione VMware di Azure con il portale di CloudSimple'
description: Viene descritto come gestire gli account nella soluzione VMware di Azure tramite il portale di CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 00348d36c38fdbfdf69a2e4e80a4c720268b40c8
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69564186"
---
# <a name="manage-accounts-on-the-azure-vmware-solution-by-cloudsimple-portal"></a>Gestire gli account nella soluzione VMware di Azure tramite il portale di CloudSimple

Quando si crea il servizio CloudSimple, viene creato un account in CloudSimple. L'account è associato alla sottoscrizione di Azure in cui si trova il servizio. Tutti gli utenti con ruoli proprietario e collaboratore nella sottoscrizione possono accedere al portale di CloudSimple. L'ID sottoscrizione e l'ID tenant di Azure associati al servizio CloudSimple sono disponibili nella pagina account.

Per gestire gli account nel portale di CloudSimple, [accedere al portale](access-cloudsimple-portal.md) e selezionare **account** dal menu laterale.

Selezionare **Riepilogo** per visualizzare le informazioni sulla configurazione CloudSimple della società. Viene visualizzata la capacità corrente della configurazione cloud, tra cui il numero di cloud privati, lo spazio di archiviazione totale, la configurazione del cluster vSphere, il numero di nodi e il numero di core di calcolo. Se la configurazione corrente non soddisfa tutte le esigenze, viene incluso un collegamento per l'acquisto di altri nodi.

## <a name="email-alerts"></a>Avvisi di posta elettronica

È possibile aggiungere gli indirizzi di posta elettronica degli utenti a cui si desidera inviare una notifica sulle modifiche apportate alla configurazione del cloud privato.

1. Nell'area **avvisi di posta elettronica aggiuntivi** fare clic su **Aggiungi nuovo**.
2. Immettere l'indirizzo di posta elettronica.
3. Premere INVIO.  

Per rimuovere una voce, fare clic su **X**.

## <a name="cloudsimple-operator-access"></a>Accesso dell'operatore CloudSimple

L'impostazione dell'accesso dell'operatore consente a CloudSimple di semplificare la risoluzione dei problemi permettendo a un tecnico del supporto di accedere al portale di CloudSimple.  L'impostazione è abilitata per impostazione predefinita. Tutte le azioni eseguite dal tecnico del supporto quando si è connessi all'account del cliente vengono registrate e disponibili per la revisione nella pagina di**controllo** dell' **attività** > .

Fare clic sull'interruttore abilitata per l'accesso dell' **operatore CloudSimple** per attivare o disattivare l'accesso.
