---
title: Scenari comuni di gestione dei diritti Azure AD (anteprima)-Azure Active Directory
description: Informazioni sulle procedure di alto livello da seguire per gli scenari comuni di gestione dei diritti Azure Active Directory (anteprima).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/23/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8e7709abcc97baac9bf657b9fff2110cb2e72c1
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489024"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management-preview"></a>Scenari comuni di gestione dei diritti Azure AD (anteprima)

> [!IMPORTANT]
> Gestione entitlement di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Esistono diversi modi in cui è possibile configurare la gestione dei diritti per l'organizzazione. Tuttavia, se si sta iniziando, è utile comprendere gli scenari comuni per gli amministratori, i responsabili approvazione e i richiedenti.

## <a name="administrators"></a>Administrators

### <a name="im-new-to-entitlement-management-and-i-want-help-with-getting-started"></a>Non ho familiarità con la gestione dei diritti e desidero assistenza per iniziare

> [!div class="mx-tableFixed"]
> | Passaggi | Esempio |
> | --- | --- |
> | [Seguire l'esercitazione per creare il primo pacchetto di accesso](entitlement-management-access-package-first.md) | [![Icona portale di Azure](./media/entitlement-management-scenarios/azure-portal.png)](./media/entitlement-management-scenarios/azure-portal-expanded.png#lightbox) |

### <a name="i-want-to-allow-users-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Desidero consentire agli utenti della mia directory di richiedere l'accesso a gruppi, applicazioni o siti di SharePoint

> [!div class="mx-tableFixed"]
> | Passaggi | Esempio |
> | --- | --- |
> | **1.** [Creare un nuovo pacchetto di accesso in un catalogo](entitlement-management-access-package-create.md#start-new-access-package) | ![Creare un pacchetto di accesso](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Aggiungere i ruoli delle risorse per accedere al pacchetto](entitlement-management-access-package-edit.md#add-resource-roles)<ul><li>Gruppi</li><li>Applicazioni</li><li>Siti di SharePoint</li></ul> | ![Aggiungere i ruoli delle risorse](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Aggiungere un criterio](entitlement-management-access-package-edit.md#policy-for-users-in-your-directory)<ul><li>Per gli utenti nella directory</li><li>Richiedi approvazione</li><li>Impostazioni di scadenza</li></ul> | ![Aggiungi criteri](./media/entitlement-management-scenarios/policy.png) |

### <a name="i-want-to-allow-users-from-my-business-partners-directory-including-users-not-yet-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Desidero consentire agli utenti della directory dei partner commerciali (inclusi gli utenti non ancora presenti nella mia directory) di richiedere l'accesso a gruppi, applicazioni o siti di SharePoint

> [!div class="mx-tableFixed"]
> | Passaggi | Esempio |
> | --- | --- |
> | **1.** [Creare un nuovo pacchetto di accesso in un catalogo](entitlement-management-access-package-create.md#start-new-access-package) | ![Creare un pacchetto di accesso](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Aggiungere i ruoli delle risorse per accedere al pacchetto](entitlement-management-access-package-edit.md#add-resource-roles) | ![Aggiungere i ruoli delle risorse](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Aggiungere un criterio per gli utenti esterni](entitlement-management-access-package-edit.md#policy-for-users-not-in-your-directory)<ul><li>Per gli utenti che non si trovino nella directory</li><li>Richiedi approvazione</li><li>Impostazioni di scadenza</li></ul> | ![Aggiungere criteri per gli utenti esterni](./media/entitlement-management-scenarios/policy-external.png) |
> | **4.** [Invia il collegamento al portale di accesso personale per richiedere il pacchetto di accesso al partner commerciale](entitlement-management-access-package-edit.md#copy-my-access-portal-link)<ul><li>Il partner aziendale può condividere il collegamento con i loro utenti</li></ul> |  |

### <a name="i-want-to-change-the-groups-applications-or-sharepoint-sites-in-an-access-package"></a>Si desidera modificare i gruppi, le applicazioni o i siti di SharePoint in un pacchetto di accesso

> [!div class="mx-tableFixed"]
> | Passaggi | Esempio |
> | --- | --- |
> | **1.** Aprire il pacchetto di accesso | ![Aggiungere i ruoli delle risorse](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Aggiungere o rimuovere ruoli delle risorse](entitlement-management-access-package-edit.md#add-resource-roles) | ![Aggiungere i ruoli delle risorse](./media/entitlement-management-scenarios/resource-roles-add.png) |

### <a name="i-want-to-view-who-has-an-assignment-to-groups-applications-or-sharepoint-sites"></a>Si desidera visualizzare gli utenti che dispongono di un'assegnazione a gruppi, applicazioni o siti di SharePoint

> [!div class="mx-tableFixed"]
> | Passaggi | Esempio |
> | --- | --- |
> | **1.** Aprire un pacchetto di accesso | ![Aggiungere i ruoli delle risorse](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Visualizza assegnazioni](entitlement-management-access-package-edit.md#view-who-has-an-assignment)<ul><li>Visualizzare gli utenti che hanno accesso a un pacchetto di accesso</li><li>Visualizzare l'accesso dell'utente scaduto</li></ul> |  |

### <a name="i-want-to-view-groups-applications-or-sharepoint-sites-a-user-has-access-to"></a>Si desidera visualizzare I gruppi, le applicazioni o i siti di SharePoint a cui un utente può accedere

> [!div class="mx-tableFixed"]
> | Passaggi | Esempio |
> | --- | --- |
> | [Report Visualizza assegnazioni utenti](entitlement-management-reports.md)<ul><li>Visualizza quando hanno richiesto e chi ha approvato</li></ul> |  |

## <a name="approvers"></a>Responsabili approvazione

### <a name="i-want-to-approve-requests-to-access-groups-applications-or-sharepoint-sites"></a>Desidero approvare le richieste di accesso ai gruppi, alle applicazioni o ai siti di SharePoint

> [!div class="mx-tableFixed"]
> | Passaggi | Esempio |
> | --- | --- |
> | **1.** [Apri la richiesta nel portale di accesso personale](entitlement-management-request-approve.md#open-request) | [![Icona del portale di accesso personale](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** [Approva richiesta di accesso](entitlement-management-request-approve.md#approve-or-deny-request) | ![Approva accesso](./media/entitlement-management-scenarios/approve-access.png) |

## <a name="requestors"></a>Richiedenti

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-available-to-me-and-request-access"></a>Desidero visualizzare i gruppi, le applicazioni o i siti di SharePoint disponibili e richiedere l'accesso

> [!div class="mx-tableFixed"]
> | Passaggi | Esempio |
> | --- | --- |
> | **1.** [Accedere al portale di accesso personale](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Icona del portale di accesso personale](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Trova pacchetto di accesso |  |
> | **3.** [Richiedere l'accesso](entitlement-management-request-access.md#request-an-access-package) | ![Richiedi l'accesso](./media/entitlement-management-scenarios/request-access.png) |

### <a name="im-an-external-user-and-i-want-to-request-access-to-groups-applications-or-sharepoint-sites-with-a-direct-link"></a>Sono un utente esterno e voglio richiedere l'accesso a gruppi, applicazioni o siti di SharePoint con un collegamento diretto

> [!div class="mx-tableFixed"]
> | Passaggi | Esempio |
> | --- | --- |
> | **1.** [Trovare il collegamento al portale di accesso personale ricevuto](entitlement-management-access-package-edit.md#copy-my-access-portal-link) |  |
> | **2.** [Accedere al portale di accesso personale](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Icona del portale di accesso personale](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **3.** [Richiedere l'accesso](entitlement-management-request-access.md#request-an-access-package) | ![Richiedi accesso utente esterno](./media/entitlement-management-scenarios/request-access-external.png) |

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-i-already-have-access-to"></a>Desidero visualizzare i gruppi, le applicazioni o i siti di SharePoint a cui ho già accesso

> [!div class="mx-tableFixed"]
> | Passaggi | Esempio |
> | --- | --- |
> | **1.** [Accedere al portale di accesso personale](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Icona del portale di accesso personale](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Visualizzare i pacchetti di accesso attivo |  |

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Creare il primo pacchetto di accesso](entitlement-management-access-package-first.md)
- [Delegare attività](entitlement-management-delegate.md)
