---
title: Funzionalità di richiesta dati del cliente per i dispositivi gemelli digitali di Azure
titleSuffix: Azure Digital Twins
description: Questo articolo illustra i processi per esportare ed eliminare i dati personali nei dispositivi gemelli digitali di Azure.
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: conceptual
ms.service: digital-twins
services: digital-twins
ms.openlocfilehash: 31da6a6b5375571f53cd7b478e957cc350ef591c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86522347"
---
# <a name="azure-digital-twins-customer-data-request-features"></a>Funzionalità di richiesta di dati dei clienti di Azure Digital Twins

I dispositivi gemelli digitali di Azure sono una piattaforma per sviluppatori per la creazione di rappresentazioni digitali sicure di un ambiente aziendale. Le rappresentazioni sono basate sui dati dello stato attivo dalle origini dati selezionate dagli utenti.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Le rappresentazioni digitali denominate dispositivi *gemelli* digitali nei dispositivi gemelli digitali di Azure rappresentano le entità negli ambienti reali e sono associate agli identificatori. Microsoft non mantiene alcuna informazione e non ha accesso ai dati che consentivano di correlare gli identificatori agli utenti. 

Molti dei gemelli digitali nei dispositivi gemelli digitali di Azure non rappresentano direttamente le entità personali, mentre gli oggetti tipici rappresentati possono essere una sala riunioni di ufficio o una fabbrica. Tuttavia, gli utenti possono considerare che alcune entità sono identificabili personalmente e a loro discrezione possono mantenere i propri metodi di rilevamento di asset o inventario che legano i gemelli digitali a singoli utenti. I dispositivi gemelli digitali di Azure gestiscono e archiviano tutti i dati associati ai dispositivi gemelli digitali come se fossero dati personali.

Per visualizzare, esportare ed eliminare dati personali a cui è possibile fare riferimento in una richiesta di soggetto dati, un amministratore di dispositivi gemelli di Azure può usare la [**portale di Azure**](https://portal.azure.com/) per utenti e ruoli o le [**API REST di Azure Digital gemelli**](how-to-use-apis-sdks.md) per i dispositivi gemelli digitali. Il portale di Azure e le API REST forniscono metodi diversi per consentire agli utenti di soddisfare tali richieste di soggetto ai dati.

## <a name="identifying-customer-data"></a>Identificazione dei dati dei clienti

I dispositivi gemelli digitali di Azure considerano i dati *personali* come dati associati agli amministratori e agli utenti. 

I dispositivi gemelli digitali di Azure archiviano l' *ID oggetto* [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) degli utenti con accesso all'ambiente. I dispositivi gemelli digitali di Azure nella portale di Azure visualizzano gli indirizzi di posta elettronica dell'utente, ma questi indirizzi di posta elettronica non vengono archiviati nei dispositivi gemelli digitali Vengono ricercati in modo dinamico in Azure Active Directory, usando l'ID oggetto Azure Active Directory.

## <a name="deleting-customer-data"></a>Eliminazione dei dati dei clienti

Gli amministratori di dispositivi gemelli digitali di Azure possono usare la portale di Azure per eliminare i dati relativi agli utenti. È anche possibile eseguire operazioni DELETE sui singoli gemelli digitali usando le API REST di Azure Digital gemelli. Per altre informazioni sulle API disponibili, vedere la [documentazione sulle API REST di Azure Digital gemelli](https://docs.microsoft.com/rest/api/azure-digitaltwins/).

## <a name="exporting-customer-data"></a>Esportazione dei dati dei clienti

I dispositivi gemelli digitali di Azure archiviano dati correlati a dispositivi gemelli digitali. Gli utenti possono recuperare e visualizzare questi dati tramite le API REST ed esportare questi dati tramite copia e incolla. 

I dati dei clienti, inclusi i ruoli utente e le assegnazioni di ruolo, possono essere selezionati, copiati e incollati dal portale di Azure. 

## <a name="links-to-additional-documentation"></a>Collegamenti a documentazione aggiuntiva

Per un elenco completo delle API del servizio Azure Digital Twins, vedere la [documentazione sulle API REST di Azure Digital gemelli](https://docs.microsoft.com/rest/api/azure-digitaltwins/).