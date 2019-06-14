---
title: Scenari comuni nella gestione dei diritti di Azure AD (anteprima) - Azure Active Directory
description: Informazioni sulla procedura generale da seguire per scenari comuni nella gestione dei diritti di Azure Active Directory (anteprima).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/23/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a50f4a8a63022668dac68c974f8c828c72777c9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66473212"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management-preview"></a>Scenari comuni nella gestione dei diritti di Azure AD (anteprima)

> [!IMPORTANT]
> Gestione entitlement di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Esistono diversi modi, che è possibile configurare la gestione dei diritti per l'organizzazione. Tuttavia, se sta appena iniziando a usare, è utile comprendere gli scenari comuni per gli amministratori, i responsabili approvazione e i richiedenti.

## <a name="administrators"></a>Administrators

### <a name="im-new-to-entitlement-management-and-i-want-help-with-getting-started"></a>Si familiarità con gestione dei diritti e desidera visualizzare la Guida introduttiva

> [!div class="mx-tableFixed"]
> | Passaggi | Esempio |
> | --- | --- |
> | [Seguire l'esercitazione per creare il primo pacchetto di accesso](entitlement-management-access-package-first.md) | [![Icona del portale di Azure](./media/entitlement-management-scenarios/azure-portal.png)](./media/entitlement-management-scenarios/azure-portal-expanded.png#lightbox) |

### <a name="i-want-to-allow-users-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Vuole consentire agli utenti nella directory per richiedere l'accesso a gruppi, applicazioni o siti di SharePoint

> [!div class="mx-tableFixed"]
> | Passaggi | Esempio |
> | --- | --- |
> | **1.** [Creare un nuovo pacchetto di accesso in un catalogo](entitlement-management-access-package-create.md#start-new-access-package) | ![Creare un pacchetto di accesso](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Aggiungere i ruoli delle risorse per accedere ai pacchetti](entitlement-management-access-package-edit.md#add-resource-roles)<ul><li>Gruppi</li><li>APPLICAZIONI</li><li>Siti di SharePoint</li></ul> | ![Aggiungere i ruoli delle risorse](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Aggiungere un criterio](entitlement-management-access-package-edit.md#policy-for-users-in-your-directory)<ul><li>Per gli utenti della directory</li><li>Richiedi approvazione</li><li>Impostazioni di scadenza</li></ul> | ![Aggiungi criteri](./media/entitlement-management-scenarios/policy.png) |

### <a name="i-want-to-allow-users-from-my-business-partners-directory-including-users-not-yet-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Vuole consentire agli utenti dalla directory partner aziendali (inclusi gli utenti non ancora nella directory personale) per richiedere l'accesso a gruppi, applicazioni o siti di SharePoint

> [!div class="mx-tableFixed"]
> | Passaggi | Esempio |
> | --- | --- |
> | **1.** [Creare un nuovo pacchetto di accesso in un catalogo](entitlement-management-access-package-create.md#start-new-access-package) | ![Creare un pacchetto di accesso](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Aggiungere i ruoli delle risorse per accedere ai pacchetti](entitlement-management-access-package-edit.md#add-resource-roles) | ![Aggiungere i ruoli delle risorse](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Aggiungere un criterio per gli utenti esterni](entitlement-management-access-package-edit.md#policy-for-users-not-in-your-directory)<ul><li>Per gli utenti non della directory</li><li>Richiedi approvazione</li><li>Impostazioni di scadenza</li></ul> | ![Aggiungere i criteri per gli utenti esterni](./media/entitlement-management-scenarios/policy-external.png) |
> | **4.** [Inviare il collegamento del portale di accesso personale per richiedere il pacchetto di accesso al partner commerciale](entitlement-management-access-package-edit.md#copy-my-access-portal-link)<ul><li>Partner di business possono condividere collegamento con i propri utenti</li></ul> |  |

### <a name="i-want-to-change-the-groups-applications-or-sharepoint-sites-in-an-access-package"></a>Modificare i gruppi, applicazioni o siti di SharePoint in un pacchetto di accesso

> [!div class="mx-tableFixed"]
> | Passaggi | Esempio |
> | --- | --- |
> | **1.** Aprire il pacchetto di accesso | ![Aggiungere i ruoli delle risorse](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Aggiungere o rimuovere i ruoli delle risorse](entitlement-management-access-package-edit.md#add-resource-roles) | ![Aggiungere i ruoli delle risorse](./media/entitlement-management-scenarios/resource-roles-add.png) |

### <a name="i-want-to-view-who-has-an-assignment-to-groups-applications-or-sharepoint-sites"></a>Desidero visualizzare chi ha un'assegnazione a gruppi, applicazioni o siti di SharePoint

> [!div class="mx-tableFixed"]
> | Passaggi | Esempio |
> | --- | --- |
> | **1.** Aprire un pacchetto di accesso | ![Aggiungere i ruoli delle risorse](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Visualizzare le assegnazioni](entitlement-management-access-package-edit.md#view-who-has-an-assignment)<ul><li>Visualizza gli utenti che hanno accesso a un pacchetto di accesso</li><li>Visualizzazione di accesso dell'utente che è scaduta</li></ul> |  |

### <a name="i-want-to-view-groups-applications-or-sharepoint-sites-a-user-has-access-to"></a>Desidera visualizzare i gruppi, applicazioni o un utente può accedere a siti di SharePoint

> [!div class="mx-tableFixed"]
> | Passaggi | Esempio |
> | --- | --- |
> | [Visualizzare report di assegnazioni di utenti](entitlement-management-reports.md)<ul><li>Vista quando vengono richiesti e che hanno approvato</li></ul> |  |

## <a name="approvers"></a>Responsabili approvazione

### <a name="i-want-to-approve-requests-to-access-groups-applications-or-sharepoint-sites"></a>Desidera approvare richieste di accesso ai gruppi, applicazioni o siti di SharePoint

> [!div class="mx-tableFixed"]
> | Passaggi | Esempio |
> | --- | --- |
> | **1.** [Richiesta di apertura nel portale di accesso personale](entitlement-management-request-approve.md#open-request) | [![L'icona del portale di accesso](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** [Approva la richiesta di accesso](entitlement-management-request-approve.md#approve-or-deny-request) | ![Approvare l'accesso](./media/entitlement-management-scenarios/approve-access.png) |

## <a name="requestors"></a>Richiedenti

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-available-to-me-and-request-access"></a>Desidera visualizzare i gruppi, applicazioni o siti di SharePoint disponibili e richiedere l'accesso

> [!div class="mx-tableFixed"]
> | Passaggi | Esempio |
> | --- | --- |
> | **1.** [Accedere al portale di accesso personale](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![L'icona del portale di accesso](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Trovare il pacchetto di accesso |  |
> | **3.** [Richiedere l'accesso](entitlement-management-request-access.md#request-an-access-package) | ![Richiedere l'accesso](./media/entitlement-management-scenarios/request-access.png) |

### <a name="im-an-external-user-and-i-want-to-request-access-to-groups-applications-or-sharepoint-sites-with-a-direct-link"></a>Sono un utente esterno e si vuole richiedere l'accesso a gruppi, applicazioni o siti di SharePoint con un collegamento diretto

> [!div class="mx-tableFixed"]
> | Passaggi | Esempio |
> | --- | --- |
> | **1.** [Trovare il collegamento del portale di accesso personale che è stato ricevuto](entitlement-management-access-package-edit.md#copy-my-access-portal-link) |  |
> | **2.** [Accedere al portale di accesso personale](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![L'icona del portale di accesso](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **3.** [Richiedere l'accesso](entitlement-management-request-access.md#request-an-access-package) | ![Richiesta di accesso utente esterno](./media/entitlement-management-scenarios/request-access-external.png) |

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-i-already-have-access-to"></a>Desidera visualizzare i gruppi, applicazioni o siti di SharePoint a che hanno già accesso

> [!div class="mx-tableFixed"]
> | Passaggi | Esempio |
> | --- | --- |
> | **1.** [Accedere al portale di accesso personale](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![L'icona del portale di accesso](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Visualizzare i pacchetti di accesso active |  |

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Creare il pacchetto prima dell'accesso](entitlement-management-access-package-first.md)
- [Modificare e gestire un pacchetto di accesso esistente](entitlement-management-access-package-edit.md)
