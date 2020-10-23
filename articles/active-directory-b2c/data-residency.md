---
title: Disponibilità a livello di area e residenza dei dati
titleSuffix: Azure AD B2C
description: Disponibilità dell'area, residenza dei dati e informazioni sui tenant di Azure Active Directory B2C Preview.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: references_regions
ms.openlocfilehash: 9cb7a97b3f57ee7ac10babc53ee2263d51838777
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92309673"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: aree di disponibilità e residenza dei dati

Le aree di disponibilità e la residenza dei dati sono due concetti molto diversi che si applicano in modo diverso ad Azure AD B2C rispetto al resto di Azure. In questo articolo vengono illustrate le differenze tra questi due concetti e viene confrontato il modo in cui si applicano ad Azure rispetto a Azure AD B2C.

Azure AD B2C è **disponibile a livello generale in tutto il mondo** con l'opzione per la **residenza dei dati** in **Stati Uniti, Europa o Asia Pacifico**.

L'[area di disponibilità](#region-availability) si riferisce al paese in cui è possibile usare un servizio.

La [residenza dei dati](#data-residency) si riferisce alla zona in cui i dati utente vengono memorizzati.

## <a name="region-availability"></a>Aree di disponibilità

Azure AD B2C è disponibile in tutto il mondo tramite il cloud pubblico di Azure.

Questo comportamento è diverso dal modello seguito dalla maggior parte degli altri servizi di Azure, che in genere corrispondono alla *disponibilità* con la *residenza dei dati*. È possibile vedere alcuni esempi di questo tipo di approccio nella pagina [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/) e [Prezzi di Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Residenza dei dati

Azure AD B2C archivia i dati utente in Stati Uniti, in Europa o nell'area Asia Pacifico.

La residenza dei dati è determinata dal paese selezionato quando si [Crea un tenant di Azure ad B2C](tutorial-create-tenant.md):

![Screenshot di un modulo Crea tenant, selezione paese o area geografica.](./media/data-residency/data-residency-b2c-tenant.png)

I dati risiedono nella **Stati Uniti** per i paesi/aree geografiche seguenti:

> Stati Uniti (US), Canada (CA), Costa Rica (CR), Repubblica Dominicana (DO), El Salvador (SV), Guatemala (GT), Messico (MX), Panama (PA), Puerto Rico (PR) e Trinidad & Tobago (TT)

I dati si trovano in **Europa** per i paesi/aree geografiche seguenti:

> Algeria (DZ), Austria (AT), Azerbaijan (AZ), Bahrain (BH), Bielorussia (BY), Belgio (BE), Bulgaria (BG), Croazia (HR), Cipro (CY), Repubblica Ceca (CZ), Danimarca (DK), Egitto (EG), Estonia (EE), Finlandia (FT), Francia (FR), Germania (DE), Grecia (GR), Ungheria (HU), Islanda (IS), Irlanda (IE), Israele (IL), Italia (IT), Giordania (JO), Kazakistan (KZ), Kenya (KE), Kuwait (KW), Lettonia (LV), Libano (LB), Liechtenstein (LI), Lituania (LT) , Lussemburgo (LU), Nord Macedonia (ML), Malta (MT), Montenegro (ME), Marocco (MA), Paesi Bassi (NL), Nigeria (NG), Norvegia (NO), Oman (OM), Pakistan (PK), Polonia (PL), Portogallo (PT), Qatar (QA), Romania (RO), Russia (UR), Arabia Saudita (SA), Serbia (RS), Slovacchia (SK), Slovenia (ST), Sudafrica (ZA), Spagna (ES), Svezia (SE), Svizzera (CH), Tunisia (TN), Turchia (TR), Ucraina (UA), Emirati Arabi Uniti (AE) e Regno Unito (GB)

I dati risiedono in **Asia Pacifico** per i paesi/aree geografiche seguenti:

> Afghanistan (AF), Hong Kong SAR (HK), India (IN), Indonesia (ID), Giappone (JP), Corea (KR), Malaysia (MY), Filippine (PH), Singapore (SG), Sri Lanka (LC), Taiwan (TW) e Tailandia (TH).

I paesi/aree geografiche seguenti sono in fase di aggiunta all'elenco. Per ora, è comunque possibile usare Azure AD B2C scegliendo uno dei paesi/aree geografiche indicati precedentemente.

> Argentina, Australia, Brasile, Cile, Colombia, Ecuador, Iraq, Nuova Zelanda, Paraguay, Perù, Uruguay e Venezuela.

## <a name="remote-profile-solution"></a>Soluzione profilo remoto

Con Azure AD B2C [criteri personalizzati](custom-policy-overview.md), è possibile eseguire l'integrazione con i [Servizi API RESTful](custom-policy-rest-api-intro.md), che consentono di archiviare e leggere i profili utente da un database remoto, ad esempio un database di marketing, un sistema CRM o un'applicazione line-of-business.  
- Durante i flussi di iscrizione e di modifica del profilo, Azure AD B2C chiama un'API REST personalizzata per salvare in modo permanente il profilo utente nell'origine dati remota. Le credenziali dell'utente vengono archiviate nella directory Azure AD B2C. 
- Al momento dell'accesso, dopo la convalida delle credenziali con un account locale o di social networking, Azure AD B2C richiama l'API REST, che invia l'identificatore univoco dell'utente come chiave primaria utente (indirizzo di posta elettronica o objectId utente). L'API REST legge i dati dal database remoto e restituisce il profilo utente.  

Una volta completata l'iscrizione, la modifica del profilo o l'accesso, Azure AD B2C include il profilo utente nel token di accesso restituito all'applicazione. Per altre informazioni, vedere la [soluzione di esempio Azure ad B2C Remote profile](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) in GitHub.

## <a name="preview-tenant"></a>Tenant di anteprima

Se è stato creato un tenant B2C durante Azure AD periodo di anteprima B2C, è probabile che il **tipo di tenant** indichi **tenant di anteprima**.

In tal caso, è necessario usare il tenant solo a scopo di sviluppo e test. Non usare un tenant di anteprima per le applicazioni di produzione.

Non esiste **alcun percorso di migrazione** da un tenant di anteprima B2C a un tenant B2C a livello di produzione. È necessario creare un nuovo tenant B2C per le applicazioni di produzione.

Si verificano problemi noti quando si elimina un tenant B2C di anteprima e si crea un tenant B2C a livello di produzione con lo stesso nome di dominio. *È necessario creare un tenant B2C a livello di produzione con un nome di dominio diverso*.

![Screenshot di un tipo di tenant, come tenant di anteprima.](./media/data-residency/preview-b2c-tenant.png)

## <a name="next-steps"></a>Passaggi successivi

- [Creare un tenant Azure ad B2C](tutorial-create-tenant.md).
