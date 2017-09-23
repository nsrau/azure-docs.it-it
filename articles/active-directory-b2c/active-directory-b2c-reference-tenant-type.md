---
title: "Azure Active Directory B2C: aree di disponibilità e residenza dei dati | Microsoft Docs"
description: Un argomento sui tipi di tenant di Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: gsacavdm
manager: krassk
editor: bryanla
ms.assetid: 8a0644da-b825-4edc-8ce9-541c3c976afb
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2017
ms.author: gsacavdm
ms.translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: facd66f0324e382ea7609a035de8129ba433846f
ms.contentlocale: it-it
ms.lasthandoff: 04/18/2017

---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: aree di disponibilità e residenza dei dati
Le aree di disponibilità e la residenza dei dati sono due concetti molto diversi che si applicano in modo diverso ad Azure AD B2C rispetto al resto di Azure. In questo articolo vengono illustrate le differenze tra i due concetti e viene confrontato il modo in cui tali concetti vengono applicati ad Azure e ad Azure AD B2C.

## <a name="summary"></a>Riepilogo
Azure AD B2C è **in genere disponibile in tutto il mondo** con l'opzione per la **residenza dei dati negli Stati Uniti o in Europa**.

## <a name="concepts"></a>Concetti
* L'**area di disponibilità** si riferisce al paese in cui è possibile usare un servizio.
* La **residenza dei dati** si riferisce alla zona in cui i dati utente vengono memorizzati.

## <a name="region-availability"></a>Aree di disponibilità
Azure AD B2C è disponibile in tutto il mondo tramite il cloud pubblico di Azure. 

Questo approccio è diverso da quello seguito dalla maggior parte degli altri servizi di Azure in cui sono combinate disponibilità e residenza dei dati. È possibile vedere alcuni esempi di questo tipo di approccio nella pagina [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/) e [Prezzi di Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Residenza dei dati
Azure AD B2C archivia i dati utente negli Stati Uniti o in Europa.

La residenza dei dati viene determinata in base al paese e/o all'area geografica che viene selezionato durante [la creazione di un tenant Azure AD B2C](active-directory-b2c-get-started.md).

![Screenshot di un tenant di anteprima](./media/active-directory-b2c-reference-tenant-type/data-residency-b2c-tenant.png)

I dati risiedono negli Stati Uniti per i paesi/aree geografiche seguenti:

> Stati Uniti, Canada, Costa Rica, Repubblica dominicana, El Salvador, Guatemala, Messico, Panama, Portorico e Trinidad e Tobago

I dati risiedono in Europa per i paesi/aree geografiche seguenti:

> Algeria, Austria, Azerbaigian, Bahrain, Belarus, Belgio, Bulgaria, Croazia, Cipro, Repubblica ceca, Danimarca, Egitto, Estonia, Finlandia, Francia, Germania, Grecia, Ungheria, Islanda, Irlanda, Israele, Italia, Giordania, Kazakhstan, Kenya, Kuwait, Lettonia, Libano, Liechtenstein, Lituania, Lussemburgo, Ex Repubblica Jugoslava di Macedonia, Malta, Montenegro, Marocco, Paesi Bassi, Nigeria, Norvegia , Oman, Pakistan, Polonia, Portogallo, Qatar, Romania, Russia, Arabia Saudita, Serbia, Slovacchia, Slovenia, Sud Africa, Spagna, Svezia, Svizzera, Tunisia, Turchia, Ucraina, Emirati Arabi Uniti e Regno Unito.

I paesi rimanenti saranno aggiunti all'elenco.  Per ora, è comunque possibile usare Azure AD B2C scegliendo uno dei paesi/aree geografiche indicati precedentemente.

> Afghanistan, Argentina, Australia, Brasile, Cile, Colombia, Ecuador, Regione amministrativa speciale di Hong Kong, India, Indonesia, Iraq, Giappone, Corea, Malaysia, Nuova Zelanda, Paraguay, Perù, Filippine, Singapore, Sri Lanka, Taiwan, Thailandia, Uruguay e Venezuela.

## <a name="preview-tenant"></a>Tenant di anteprima
Se è stato creato un tenant B2C durante il periodo di anteprima di Azure AD B2C, è probabile che **Tipo di tenant** sia **Tenant di anteprima**. In questo caso, è NECESSARIO usare il tenant solo per scopi di sviluppo e test e non per le app di produzione.

> [!IMPORTANT]
> Non esiste un percorso di migrazione da una versione di anteprima del tenant B2C a un tenant B2C a livello di produzione. Si noti che si verificano problemi noti quando si elimina un tenant B2C di anteprima e viene creato un tenant B2C a livello di produzione con lo stesso nome di dominio. È necessario creare un tenant B2C a livello di produzione con un nome di dominio diverso.


![Screenshot di un tenant di anteprima](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)

