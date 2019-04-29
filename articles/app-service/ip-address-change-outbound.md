---
title: Preparare un cambio di indirizzo IP in uscita - Servizio app di Azure
description: Se l'indirizzo IP in uscita sta per essere modificato, in questo articolo viene spiegato come procedere in modo che l'app continui a funzionare anche dopo la modifica.
services: app-service\web
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 06/28/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ac62217af096653d61a79ff29ae352c8e950f8af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269774"
---
# <a name="how-to-prepare-for-an-outbound-ip-address-change"></a>Come preparare un cambio di indirizzo IP in uscita

Se si riceve una notifica di modifica degli indirizzi IP in uscita dell'app Servizio app di Azure, seguire le istruzioni riportate in questo articolo.

## <a name="determine-if-you-have-to-do-anything"></a>Determinare se sia necessario intraprendere qualche operazione

* Opzione 1: se l'app Servizio app di Azure non usa il filtro IP, un elenco di inclusione esplicita o una gestione speciale del traffico in uscita, ad esempio il routing o un firewall, non è richiesta alcuna azione.

* Opzione 2: se l'app ha una gestione speciale per gli indirizzi IP in uscita (vedere esempi riportati di seguito), aggiungere i nuovi indirizzi IP in uscita ogni volta che vengono visualizzati quelli esistenti. Non sostituire gli indirizzi IP esistenti. È possibile trovare i nuovi indirizzi IP in uscita seguendo le istruzioni nella sezione successiva.

  Ad esempio, un indirizzo IP in uscita può essere incluso in modo esplicito in un firewall esterno all'app o un servizio di pagamento esterno può avere un elenco autorizzato che contiene l'indirizzo IP in uscita per l'app. Se l'indirizzo in uscita è configurato in un elenco in un punto qualsiasi all'esterno dell'app, è necessario apportare modifiche.

## <a name="find-the-outbound-ip-addresses-in-the-azure-portal"></a>Trovare gli indirizzi IP in uscita nel portale di Azure

I nuovi indirizzi IP in uscita vengono visualizzati nel portale prima di diventare effettivi. Quando Azure inizia a usare i nuovi file, quelli meno recenti non verranno più usati. Viene usato un solo set alla volta, perciò le voci negli elenchi di inclusione devono avere indirizzi IP sia vecchi che nuovi per evitare interruzioni del servizio al momento della modifica. 

1.  Aprire il [portale di Azure](https://portal.azure.com).

2.  Nel menu di spostamento a sinistra selezionare **Servizi app**.

3.  Selezionare il servizio app nell'elenco.

1.  Se l'app è un'app per le funzioni, vedere [Function app outbound IP addresses](../azure-functions/ip-addresses.md#find-outbound-ip-addresses) (Indirizzi IP in uscita dell'app per le funzioni).

4.  Nell'intestazione **Impostazioni** fare clic su **Proprietà** nel menu di spostamento a sinistra e individuare la sezione denominata **Indirizzi IP in uscita**.

5. Copiare gli indirizzi IP e aggiungerli alla gestione speciale del traffico in uscita, ad esempio un filtro o elenco dei valori consentiti. Non eliminare gli indirizzi IP esistenti nell'elenco.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come preparare un cambio di indirizzo IP avviato da Azure. Per altre informazioni sugli indirizzi IP in Servizio app di Azure, vedere [Indirizzi IP in ingresso e in uscita in Servizio app di Azure](overview-inbound-outbound-ips.md).
