---
title: 'Azure Active Directory B2C: tenant B2C a livello di produzione e di anteprima | Documentazione Microsoft'
description: Un argomento sui tipi di tenant di Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 8a0644da-b825-4edc-8ce9-541c3c976afb
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7e52c9d331c6967f029427c7eb03f13f65c3a507


---
# <a name="azure-active-directory-b2c-production-scale-vs-preview-b2c-tenants"></a>Azure Active Directory B2C: tenant B2C a livello di produzione e di anteprima
Se si prevede di scrivere un'app di produzione in Azure Active Directory (Azure AD) B2C, è necessario essere certi di avere il "tipo" di tenant corretto per pubblicarla. Per verificarlo, seguire questa procedura per [passare al pannello delle funzionalità B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) e vedere il **Tipo di tenant**.

## <a name="summary"></a>Riepilogo
Azure Active Directory B2C supporta le app di produzione solo nei tenant B2C **a livello di produzione** in America del Nord.

| Tipo di tenant | Paesi/aree geografiche | Disponibile a livello generale? |
| --- | --- | --- |
| **Tenant a livello di produzione** |Paesi/aree geografiche del Nord America |Sì |
| **Tenant a livello di produzione** |Tutti i paesi/le aree geografiche ad eccezione dell'America del Nord |No |
| **Tenant di anteprima** |Tutti i paesi/le aree geografiche |No |

> [!NOTE]
> I tenant di Azure AD B2C (per gli utenti) sono attualmente disponibili in alcuni paesi o aree geografiche in cui sono disponibili tenant di Azure AD (per i dipendenti). Per altre informazioni, vedere le sezioni seguenti.
> 
> 

## <a name="production-scale-b2c-tenant-in-north-america"></a>Tenant B2C a livello di produzione in America del Nord
Se è stato [creato un tenant B2C](active-directory-b2c-get-started.md)in America del Nord, ad esempio, in uno dei paesi o delle aree geografiche seguenti: Stati Uniti, Canada, Costa Rica, Repubblica dominicana, El Salvador, Guatemala, Messico, Panama, Portorico e Trinidad e Tobago E **Tipo di tenant** nell'interfaccia utente di amministrazione di B2C indica **Tenant a livello di produzione**, il tenant può essere usato per le app di produzione.

> [!NOTE]
> I tenant a livello di produzione sono ridimensionabili fino a centinaia di milioni di identità utente per ogni tenant.
> 
> 

![Screenshot di un tenant a livello di produzione](./media/active-directory-b2c-reference-tenant-type/production-scale-b2c-tenant.png)

## <a name="preview-b2c-tenant-in-any-countryregion"></a>Tenant B2C di anteprima in qualsiasi paese/area geografica
Se è stato creato un tenant B2C durante il periodo di anteprima di Azure AD B2C, è probabile che **Tipo di tenant** sia **Tenant di anteprima**. In questo caso, è NECESSARIO usare il tenant solo per scopi di sviluppo e test e non per le app di produzione.

> [!IMPORTANT]
> Non esiste un percorso di migrazione da una versione di anteprima del tenant B2C a un tenant B2C a livello di produzione. Si noti che si verificano problemi noti quando si elimina un tenant B2C di anteprima e viene creato un tenant B2C a livello di produzione con lo stesso nome di dominio. È necessario creare un tenant B2C a livello di produzione con un nome di dominio diverso.
> 
> 

![Screenshot di un tenant di anteprima](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)

## <a name="production-scale-b2c-tenant-outside-of-north-america"></a>Tenant B2C a livello di produzione al di fuori dell'America del Nord
Azure Active Directory B2C attualmente non è disponibile a livello generale al di fuori dell'America del Nord. È tuttavia possibile creare e usare tenant a livello di produzione a scopo di sviluppo e test, in uno dei seguenti paesi o aree geografiche: Algeria, Austria, Azerbaigian, Bahrain, Belarus, Belgio, Bulgaria, Croazia, Cipro, Repubblica ceca, Danimarca, Egitto, Estonia, Finlandia, Francia, Germania, Grecia, Ungheria, Islanda, Irlanda, Israele, Italia, Giordania, Kazakhstan, Kenya, Kuwait, Lettonia, Libano, Liechtenstein, Lituania, Lussemburgo, Ex Repubblica Jugoslava di Macedonia, Malta, Montenegro, Marocco, Paesi Bassi, Nigeria, Norvegia , Oman, Pakistan, Polonia, Portogallo, Qatar, Romania, Russia, Arabia Saudita, Serbia, Slovacchia, Slovenia, Sud Africa, Spagna, Svezia, Svizzera, Tunisia, Turchia, Ucraina, Emirati Arabi Uniti e Regno Unito.

Quando per Azure Active Directory B2C verrà annunciata la disponibilità generale in queste aree o paesi, sarà possibile continuare a usare questi tenant a livello di produzione e pubblicare le applicazioni di produzione senza alcuna perdita di dati.

## <a name="availability-of-b2c-tenants"></a>Disponibilità di B2C tenant
B2C tenant non sono attualmente disponibili nei paesi o nelle aree geografiche seguenti: Afghanistan, Argentina, Australia, Brasile, Cile, Colombia, Ecuador, Hong Kong - R.A.S., India, Indonesia, Iraq, Giappone, Corea, Malaysia, Nuova Zelanda, Paraguay, Perù, Filippine, Singapore, Sri Lanka, Taiwan, Thailandia, Uruguay e Venezuela. Ne è prevista l'inclusione in futuro.




<!--HONumber=Nov16_HO3-->


