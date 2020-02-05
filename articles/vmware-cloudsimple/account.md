---
title: 'Gestione degli account: portale di Azure VMware Solutions (AVS)'
description: Viene descritto come gestire gli account nel portale di Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1fd7cb1c55fb664828448cef0b67ea9b16323bdf
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025368"
---
# <a name="manage-accounts-on-the-azure-vmware-solutions-avs-portal"></a>Gestire gli account nel portale di Azure VMware Solutions (AVS)

Quando si crea il servizio AVS, viene creato un account in AVS. L'account è associato alla sottoscrizione di Azure in cui si trova il servizio. Tutti gli utenti con ruoli proprietario e collaboratore nella sottoscrizione possono accedere al portale AVS. L'ID sottoscrizione e l'ID tenant di Azure associati al servizio AVS sono disponibili nella pagina account.

Per gestire gli account nel portale AVS, [accedere al portale](access-cloudsimple-portal.md) e selezionare **account** dal menu laterale.

Selezionare **Summary (riepilogo** ) per visualizzare le informazioni sulla configurazione AVS della società. Viene visualizzata la capacità corrente della configurazione cloud, tra cui il numero di cloud privati AVS, l'archiviazione totale, la configurazione del cluster vSphere, il numero di nodi e il numero di core di calcolo. Se la configurazione corrente non soddisfa tutte le esigenze, viene incluso un collegamento per l'acquisto di altri nodi.

## <a name="email-alerts"></a>Avvisi di posta elettronica

È possibile aggiungere gli indirizzi di posta elettronica degli utenti a cui si desidera inviare una notifica sulle modifiche apportate alla configurazione del cloud privato AVS.

1. Nell'area **avvisi di posta elettronica aggiuntivi** fare clic su **Aggiungi nuovo**.
2. Immettere l'indirizzo di posta elettronica.
3. Premere INVIO.  

Per rimuovere una voce, fare clic su **X**.

## <a name="avs-operator-access"></a>Accesso operatore AVS

L'impostazione dell'accesso dell'operatore consente ad AVS di semplificare la risoluzione dei problemi permettendo a un tecnico del supporto di accedere al portale AVS. L'impostazione è abilitata per impostazione predefinita. Tutte le azioni eseguite dal tecnico del supporto quando si è connessi all'account del cliente vengono registrate e disponibili per la revisione nella pagina di **controllo** dell' **attività** > .

Fare clic sull'interruttore **AVS operator Access Enabled** per attivare o disattivare l'accesso.
