---
title: Funzionalità di richiesta dati dei clienti in Azure IoT Central | Microsoft Docs
description: Questo articolo descrive come identificare, eliminare ed esportare i dati dei clienti nell'applicazione IoT Central di Azure.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 27cd2e58eabcd96414391cac1a517ea242a65c21
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73809802"
---
# <a name="summary-of-customer-data-request-features"></a>Riepilogo delle funzionalità di richiesta dati dei clienti

Azure IoT Central è una soluzione Internet delle cose (IoT) completamente gestita che semplifica la connessione, il monitoraggio e la gestione dei dispositivi IoT su vasta scala, crea informazioni dettagliate approfondite dai dati IoT e intraprende azioni informate.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identificazione dei dati dei clienti

Per identificare gli utenti e assegnare i ruoli, si usano gli ID oggetto di Azure Active Directory. Il portale di Azure IoT Central mostra gli indirizzi di posta elettronica per le assegnazioni di ruolo, ma solo gli ID oggetto di Azure Active Directory sono archiviati, l'indirizzo di posta elettronica viene richiesto con query dinamiche da Azure Active Directory. Gli amministratori di Azure IoT Central possono visualizzare, esportare ed eliminare gli utenti dell'applicazione nella sezione di amministrazione degli utenti di un'applicazione Azure IoT Central.

All'interno dell'applicazione è possibile configurare gli indirizzi di posta elettronica per ricevere avvisi. In questo caso gli indirizzi di posta elettronica vengono archiviati all'interno di IoT Central e devono essere gestiti dalla pagina di amministrazione account nell'applicazione.

Riguardo ai dispositivi, Microsoft non mantiene alcuna informazione e non dispone dell'accesso ai dati che consentono una correlazione del dispositivo con l'utente. Molti dei dispositivi gestiti in Azure IoT Central non sono dispositivi personali, ad esempio un termostato o un robot industriale. Tuttavia, i clienti possono decidere di rendere personalmente identificabili alcuni dispositivi e, a loro discrezione, possono mantenere propri sistemi di monitoraggio di inventario e asset che collegano i dispositivi agli individui. Azure IoT Central gestisce e archivia tutti i dati associati ai dispositivi come se fossero dati personali.

Quando si usano i servizi enterprise di Microsoft, Microsoft genera alcune informazioni, note come log generati dal sistema. Questi log costituiscono azioni pratiche eseguite nel servizio e dati diagnostici relativi ai singoli dispositivi e non sono correlati all'attività dell'utente. I log generati dal sistema di Azure IoT Central non sono accessibili o esportabili dagli amministratori dell'applicazione.

## <a name="deleting-customer-data"></a>Eliminazione di dati del cliente

La possibilità di eliminare i dati utente viene fornita solo tramite la pagina di amministrazione di IoT Central. Gli amministratori di applicazioni possono selezionare l'utente da eliminare e selezionare **Elimina** nell'angolo superiore destro dell'applicazione per eliminare il record. Gli amministratori dell'applicazione possono anche rimuovere singoli account che non sono più associati all'applicazione in questione.

Dopo l'eliminazione di un utente, non gli vengono più inviati avvisi tramite posta elettronica. Tuttavia, l'indirizzo di posta elettronica deve essere rimosso singolarmente da ogni avviso configurato.

Per altre informazioni, vedere [Configurare le regole e le azioni per i dispositivi](tutorial-configure-rules.md).

## <a name="exporting-customer-data"></a>Esportazione di dati dei clienti

La possibilità di esportare i dati viene fornita solo tramite la pagina di amministrazione di IoT Central. I dati dei clienti, compresi i ruoli assegnati, possono essere selezionati, copiati e incollati da un amministratore dell'applicazione.

## <a name="links-to-additional-documentation"></a>Collegamenti a documentazione aggiuntiva

Per altre informazioni sull'amministrazione di account, incluse le definizioni di ruolo, vedere [Come amministrare l'applicazione](howto-administer.md).
