---
title: Connettori nell&quot;interfaccia utente di Synchronization Service Manager per Azure AD | Microsoft Docs
description: Comprendere la scheda Connettori in Synchronization Service Manager di Azure AD Connect.
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: bd2240678fed44db748ae062bdf91e457159b4a2
ms.lasthandoff: 03/04/2017

---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Uso dei connettori con Sync Service Manager di Azure AD Connect

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

La scheda Connettori consente di gestire tutti i sistemi a cui il motore di sincronizzazione è connesso.

## <a name="connector-actions"></a>Azioni del connettore
| Azione | Commento |
| --- | --- |
| Create |Non usare. Per la connessione ad altre foreste AD, usare l'installazione guidata. |
| Proprietà |Si usa per i filtri di unità organizzativa e dominio. |
| [Eliminazione](#delete) |Si usa per eliminare i dati nello spazio connettore o per eliminare la connessione a una foresta. |
| [Configura profili di esecuzione](#configure-run-profiles) |Fatta eccezione per i filtri di dominio, qui non è richiesta alcuna configurazione. Questa azione consente di visualizzare i profili di esecuzione già configurati. |
| Esegui |Si usa per avviare l'esecuzione occasionale di un profilo. |
| Arresto |Arresta un connettore che sta eseguendo attualmente un profilo. |
| Esporta connettore |Non usare. |
| Importa connettore |Non usare. |
| Aggiorna connettore |Non usare. |
| Aggiorna schema |Aggiorna lo schema memorizzato nella cache. È preferibile usare l'opzione nell'installazione guidata perché aggiorna anche le regole di sincronizzazione. |
| [Spazio connettore di ricerca](#search-connector-space) |Consente di trovare oggetti e [seguire un oggetto e i relativi dati attraverso il sistema](#follow-an-object-and-its-data-through-the-system). |

### <a name="delete"></a>Eliminazione
L'azione di eliminazione viene usata per due scopi diversi.  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

L'opzione **Delete connector space only** (Elimina solo lo spazio connettore) rimuove tutti i dati, ma mantiene la configurazione.

L'opzione **Delete Connector and connector space** (Elimina connettore e spazio connettore) rimuove i dati e la configurazione. Questa opzione viene usata quando non si intende più connettersi a una foresta.

Entrambe le opzioni sincronizzano tutti gli oggetti e aggiornano gli oggetti del metaverse. L'esecuzione di questa azione richiede molto tempo.

### <a name="configure-run-profiles"></a>Configura profili di esecuzione
Questa opzione consente di visualizzare i profili di esecuzione configurati per un connettore.

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/configurerunprofiles.png)

### <a name="search-connector-space"></a>Spazio connettore di ricerca
L’azione Cerca spazio connettore è utile per trovare oggetti e risolvere problemi relativi ai dati.

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

Iniziare selezionando un **ambito**. È possibile eseguire ricerche in base ai dati (RDN, DN, Ancoraggio, Sottoalbero) o allo stato dell'oggetto (tutte le altre opzioni).  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png)  
Se ad esempio si esegue una ricerca nel sottoalbero, si ottengono tutti gli oggetti in una OU.  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png)  
Da questa griglia è possibile selezionare un oggetto, selezionare le **proprietà** e [seguirlo](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md) dallo spazio connettore di origine attraverso il metaverse e fino allo spazio connettore di destinazione.

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Ulteriori informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

