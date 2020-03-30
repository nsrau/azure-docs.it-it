---
title: Disponibilità dell'area e residenza dei dati
titleSuffix: Azure AD B2C
description: Disponibilità dell'area, residenza dei dati e informazioni sui tenant di anteprima B2C di Azure Active Directory.Region availability, data residency, and information about Azure Active Directory B2C preview tenants.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3df0f581d0d2a1e5ca02202b4eeaede5a1dd5362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188850"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: aree di disponibilità e residenza dei dati

Le aree di disponibilità e la residenza dei dati sono due concetti molto diversi che si applicano in modo diverso ad Azure AD B2C rispetto al resto di Azure. Questo articolo illustra le differenze tra questi due concetti e confronta le modalità di applicazione ad Azure e Azure AD B2C.

Azure AD B2C è **in genere disponibile in tutto il mondo** con l'opzione per la residenza dei **dati** negli Stati Uniti, in Europa o in **Asia Pacifico.**

L'[area di disponibilità](#region-availability) si riferisce al paese in cui è possibile usare un servizio.

La [residenza dei dati](#data-residency) si riferisce alla zona in cui i dati utente vengono memorizzati.

## <a name="region-availability"></a>Aree di disponibilità

Azure AD B2C è disponibile in tutto il mondo tramite il cloud pubblico di Azure.

Questo comportamento è diverso dal modello seguito dalla maggior parte degli altri servizi di Azure, che in genere *associano la disponibilità* alla *residenza dei dati.* È possibile vedere alcuni esempi di questo tipo di approccio nella pagina [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/) e [Prezzi di Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Residenza dei dati

Azure AD B2C archivia i dati utente negli Stati Uniti, in Europa o nell'area Asia Pacifico.

La residenza dei dati è determinata dal paese selezionato quando si [crea un tenant B2C](tutorial-create-tenant.md)di Azure AD:

![Screenshot di un tenant di anteprima](./media/data-residency/data-residency-b2c-tenant.png)

I dati risiedono **negli Stati Uniti** per i seguenti paesi:

> Stati Uniti, Canada, Costa Rica, Repubblica dominicana, El Salvador, Guatemala, Messico, Panama, Portorico e Trinidad e Tobago

I dati risiedono in **Europa** per i seguenti paesi/aree geografiche:

> Algeria, Austria, Azerbaigian, Bahrain, Bielorussia, Belgio, Bulgaria, Croazia, Cipro, Repubblica Ceca, Danimarca, Egitto, Estonia, Finlandia, Francia, Germania, Grecia, Ungheria, Islanda, Irlanda, Israele, Italia, Giordania, Kazakistan, Kenya, Kuwait, Lettonia, Libano, Liechtenstein, Lituania, Lussemburgo, Macedonia del Nord, Malta, Montenegro, Marocco, Olanda, Nigeria, Norvegia, Oman, Pakistan, Polonia, Portogallo, Qatar, Romania, Russia, Arabia Saudita, Serbia, Slovacchia, Slovenia, Sud Africa, Spagna, Svezia, Svizzera, Tunisia, Turchia, Ucraina, Emirati Arabi Uniti e Regno Unito.

I dati risiedono **nell'area Asia Pacifico** per i seguenti paesi/aree geografiche:

> Afghanistan, Hong Kong, India, Indonesia, Giappone, Corea, Malesia, Filippine, Singapore, Sri Lanka, Taiwan e Thailandia.

I seguenti paesi sono in fase di aggiunta all'elenco. Per ora, è comunque possibile usare Azure AD B2C scegliendo uno dei paesi/aree geografiche indicati precedentemente.

> Argentina, Australia, Brasile, Cile, Colombia, Ecuador, Iraq, Nuova èelanda, Paraguay, Perù, Uruguay e Venezuela.

## <a name="preview-tenant"></a>Tenant di anteprima

Se è stato creato un tenant B2C durante il periodo di anteprima di Azure AD B2C, è probabile che il **tipo di tenant** condivida Preview **tenant.**

In questo caso, è necessario utilizzare il tenant SOLO a scopo di sviluppo e test. NON usare un tenant di anteprima per le applicazioni di produzione.

**Non esiste alcun percorso** di migrazione da un tenant B2C di anteprima a un tenant B2C su scala di produzione. È necessario creare un nuovo tenant B2C per le applicazioni di produzione.

Esistono problemi noti quando si elimina un tenant B2C di anteprima e si crea un tenant B2C su scala di produzione con lo stesso nome di dominio. *È necessario creare un tenant B2C su scala*di produzione con un nome di dominio diverso.

![Screenshot di un tenant di anteprima](./media/data-residency/preview-b2c-tenant.png)