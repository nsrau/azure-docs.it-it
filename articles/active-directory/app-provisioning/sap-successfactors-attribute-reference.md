---
title: Guida di riferimento all'attributo SAP SuccessFactors Documenti Microsoft
description: Scopri quali attributi di SuccessFactors sono supportati dal provisioning guidato da SuccessFactors-HR
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: afb77f2d-5ddd-4c2e-a840-09021b0efef1
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 00b16f969525e7b802c008ba247ecba015875689
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522357"
---
# <a name="sap-successfactors-attribute-reference"></a>Informazioni di riferimento sugli attributi SAP SuccessFactors

## <a name="supported-successfactors-entities-and-attributes"></a>Entità e attributi SuccessFactors supportati

La tabella seguente acquisisce l'elenco degli attributi SuccessFactors supportati dalle due app di provisioning seguenti: 
* [SuccessFactors per il provisioning degli utenti di Active Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors to Azure AD User Provisioning](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

| \# | Entità SuccessFactors                  | Attributo SuccessFactors     | Tipo di operazione |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | Lettura           |
| 2  | PerPerson                              | personId                     | Lettura           |
| 3  | PerPerson                              | perPersonUuid                | Lettura           |
| 4  | PerPersonale                            | displayName                  | Lettura           |
| 5  | PerPersonale                            | firstName                    | Lettura           |
| 6  | PerPersonale                            | gender                       | Lettura           |
| 7  | PerPersonale                            | lastName                     | Lettura           |
| 8  | PerPersonale                            | middleName                   | Lettura           |
| 9  | PerPersonale                            | preferredName                | Lettura           |
| 10 | Utente                                   | IndirizzoRiga1                 | Lettura           |
| 11 | Utente                                   | indirizzoRiga2                 | Lettura           |
| 12 | Utente                                   | addressLIne3                 | Lettura           |
| 13 | Utente                                   | businessPhone                | Lettura           |
| 14 | Utente                                   | Cellulare                    | Lettura           |
| 15 | Utente                                   | city                         | Lettura           |
| 16 | Utente                                   | country                      | Lettura           |
| 17 | Utente                                   | personalizzato01                     | Lettura           |
| 18 | Utente                                   | personalizzato02                     | Lettura           |
| 19 | Utente                                   | personalizzato03                     | Lettura           |
| 20 | Utente                                   | personalizzato04                     | Lettura           |
| 21 | Utente                                   | personalizzato05                     | Lettura           |
| 22 | Utente                                   | personalizzato06                     | Lettura           |
| 23 | Utente                                   | personalizzato07                     | Lettura           |
| 24 | Utente                                   | custom08 (in modo intallto)                     | Lettura           |
| 25 | Utente                                   | custom09 (informazioni in due)                     | Lettura           |
| 26 | Utente                                   | custom10                     | Lettura           |
| 27 | Utente                                   | custom11 (in base all'addio                     | Lettura           |
| 28 | Utente                                   | personalizzato12                     | Lettura           |
| 29 | Utente                                   | personalizzato13                     | Lettura           |
| 30 | Utente                                   | personalizzato14                     | Lettura           |
| 31 | Utente                                   | Empid                        | Lettura           |
| 32 | Utente                                   | homePhone                    | Lettura           |
| 33 | Utente                                   | lavoroFamiglia                    | Lettura           |
| 34 | Utente                                   | nickname                     | Lettura           |
| 35 | Utente                                   | state                        | Lettura           |
| 36 | Utente                                   | timeZone                     | Lettura           |
| 37 | Utente                                   | username                     | Lettura           |
| 38 | Utente                                   | Zipcode                      | Lettura           |
| 39 | Pertelefono                               | areaCode                     | Lettura           |
| 40 | Pertelefono                               | countryCode                  | Lettura           |
| 41 | Pertelefono                               | estensione                    | Lettura           |
| 42 | Pertelefono                               | phoneNumber                  | Lettura           |
| 43 | Pertelefono                               | phoneTipo                    | Lettura           |
| 44 | PerEmail (E-mail)                               | emailAddress                 | Lettura, Scrittura    |
| 45 | PerEmail (E-mail)                               | emailType (tipo e-mail)                    | Lettura           |
| 46 | Occupazione                          | firstDateWorked              | Lettura           |
| 47 | Occupazione                          | lastDateLavorato               | Lettura           |
| 48 | Occupazione                          | userId                       | Lettura           |
| 49 | Occupazione                          | isContingentWorker           | Lettura           |
| 50 | EmpJob (lavoro empjob)                                 | countryOfCompany             | Lettura           |
| 51 | EmpJob (lavoro empjob)                                 | emplStatus (stato mplStatus)                   | Lettura           |
| 52 | EmpJob (lavoro empjob)                                 | endDate                      | Lettura           |
| 53 | EmpJob (lavoro empjob)                                 | startDate                    | Lettura           |
| 54 | EmpJob (lavoro empjob)                                 | jobTitle                     | Lettura           |
| 55 | EmpJob (lavoro empjob)                                 | position                     | Lettura           |
| 65 | EmpJob (lavoro empjob)                                 | customString13               | Lettura           |
| 56 | EmpJob (lavoro empjob)                                 | managerId                    | Lettura           |
| 57 | EmpJob\.BusinessUnit                   | unità aziendale                 | Lettura           |
| 58 | EmpJob\.BusinessUnit                   | businessUnitId               | Lettura           |
| 59 | EmpJob\.Azienda                        | company                      | Lettura           |
| 60 | EmpJob\.Azienda                        | companyId                    | Lettura           |
| 61 | EmpJob\.\.Azienda PaeseOfRegistration | twoCharCountryCode           | Lettura           |
| 62 | Centro cost\.EmpJob                     | costCenter                   | Lettura           |
| 63 | Centro cost\.EmpJob                     | costCenterId (informazioni in stato instato di costo                 | Lettura           |
| 64 | Centro cost\.EmpJob                     | costCenterDescrizione        | Lettura           |
| 65 | Dipartimento\.EmpJob                     | department                   | Lettura           |
| 66 | Dipartimento\.EmpJob                     | departmentId                 | Lettura           |
| 67 | Divisione\.EmpJob                       | divisione                     | Lettura           |
| 68 | Divisione\.EmpJob                       | divisionId (id di divisione)                   | Lettura           |
| 69 | Codice processo\.EmpJob                        | jobCode (Codiceprocesso)                      | Lettura           |
| 70 | Codice processo\.EmpJob                        | jobCodeId (IdDifiled)                    | Lettura           |
| 71 | Percorso EmpJob\.                       | LocationName (NomeLocalmente)                 | Lettura           |
| 72 | Percorso EmpJob\.                       | Indirizzodi ufficio        | Lettura           |
| 73 | Percorso EmpJob\.                       | officeLocationCity           | Lettura           |
| 74 | Percorso EmpJob\.                       | ufficioLocationCustomString4  | Lettura           |
| 75 | Percorso EmpJob\.                       | codice di stampa        | Lettura           |
| 76 | EmpJob\.PayGrade                       | payGrade                     | Lettura           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | Lettura           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | Lettura           |


## <a name="default-attribute-mapping"></a>Mapping degli attributi predefiniti

La tabella seguente fornisce il mapping degli attributi predefinito tra gli attributi SuccessFactors elencati in precedenza e gli attributi AD/Azure AD. Nel pannello "Mapping" dell'app di provisioning di Azure AD è possibile modificare questo mapping predefinito per includere attributi dall'elenco precedente. 

| \# | Entità SuccessFactors                  | Attributo SuccessFactors | Mapping degli attributi AD/Azure AD predefinitoDefault AD/Azure AD attribute mapping   | Elaborazione nota                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | employeeId                              | Utilizzato come attributo corrispondente                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[Non mappato \- utilizzato come ancoraggio di origine\] | Durante la sincronizzazione iniziale, il servizio di provisioning collega il personUuid all'oggetto objectGuid esistente.  |
| 3  | PerPersonale                            | displayName              | displayName                             | ND                                                                                           |
| 4  | PerPersonale                            | firstName                | givenName                               | ND                                                                                           |
| 5  | PerPersonale                            | lastName                 | sn                                      | ND                                                                                           |
| 6  | Utente                                   | IndirizzoRiga1             | streetAddress                           | ND                                                                                           |
| 7  | Utente                                   | city                     | l                                       | ND                                                                                           |
| 8  | Utente                                   | country                  | co                                      | ND                                                                                           |
| 9  | Utente                                   | state                    | st                                      | ND                                                                                           |
| 10 | Utente                                   | username                 | samAccountName                          | ND                                                                                           |
| 11 | Utente                                   | Zipcode                  | postalCode                              | ND                                                                                           |
| 12 | PerEmail (E-mail)                               | emailAddress             | mail                                    | ND                                                                                           |
| 13 | EmpJob (lavoro empjob)                                 | jobTitle                 | title                                   | ND                                                                                           |
| 14 | EmpJob (lavoro empjob)                                 | managerId                | manager                                 | ND                                                                                           |
| 15 | EmpJob\.\.Azienda PaeseOfRegistration | twoCharCountryCode       | c                                       | ND                                                                                           |
| 16 | Dipartimento\.EmpJob                     | department               | department                              | ND                                                                                           |
| 17 | Divisione\.EmpJob                       | divisione                 | company                                 | ND                                                                                           |
| 18 | Percorso EmpJob\.                       | Indirizzodi ufficio    | streetAddress                           | ND                                                                                           |
| 19 | Percorso EmpJob\.                       | codice di stampa    | postalCode                              | ND                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | se activeEmploymentsCount è 0, disabilitare l'account.                                           |

