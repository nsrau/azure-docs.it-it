---
title: 'Servizio di sincronizzazione Azure AD Connect: Interfaccia utente di Synchronization Service Manager | Documentazione Microsoft'
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
ms.date: 09/07/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 87c73981c74fc763fd1aec6c283e934c77008441
ms.openlocfilehash: 4d248fedea7c3b05b5bf4a28cd99923302c43a9b


---
# <a name="azure-ad-connect-sync-synchronization-service-manager"></a>Servizio di sincronizzazione Azure AD Connect: Synchronization Service Manager
| [Operazioni](active-directory-aadconnectsync-service-manager-ui-operations.md) | [Connectors (Connettori) (Connettori)](active-directory-aadconnectsync-service-manager-ui-connectors.md) | [Finestra di progettazione metaverse](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) | [Ricerca metaverse](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) |
| --- | --- | --- | --- |
|  | | | |

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
![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

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
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png) Da questa griglia è possibile selezionare un oggetto, selezionare **properties**(Proprietà) e [seguirlo](#follow-an-object-and-its-data-through-the-system) from the source connector space, through the metaverse(Proprietà) e to the target connector space.

## <a name="follow-an-object-and-its-data-through-the-system"></a>seguire un oggetto e i relativi dati attraverso il sistema
In fase di risoluzione di un problema relativo ai dati, seguire un oggetto dallo spazio connettore di origine fino al metaverse e allo spazio connettore di destinazione è una procedura fondamentale per capire perché i dati non hanno i valori previsti.

### <a name="connector-space-object-properties"></a>Proprietà dell’oggetto spazio connettore
**Import (Importa) (Import (Importa)a)**  
 Quando si apre un oggetto cs, nella parte superiore sono presenti diverse schede. La scheda **Import** (Importa) visualizza i dati di gestione temporanea dopo l'importazione.
![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/csimport.png) La voce **Old Value** (Valore precedente) indica il valore attualmente presente nel sistema, mentre in **New Value** (Nuovo valore) è indicato il valore ricevuto dal sistema di origine e non ancora applicato. In questo caso è impossibile applicare la modifica perché si è verificato un errore di sincronizzazione.

**Error (Errore) (Error (Errore)e)**  
 La pagina di errore è visibile solo se si è verificato un problema con l'oggetto. Per altre informazioni su come [risolvere gli errori di sincronizzazione](active-directory-aadconnectsync-service-manager-ui-operations.md#troubleshoot-errors-in-operations-tab), vedere i dettagli nella pagina Operations (Operazioni).

**Derivazione**  
 La scheda Lineage (Derivazione) visualizza la correlazione fra l'oggetto spazio connettore e l'oggetto metaverse. È possibile vedere l'ultima volta in cui il connettore ha importato una modifica dal sistema connesso e quali regole sono state applicate per popolare i dati nel metaverse.
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cslineage.png) Nella colonna **Action** (Azione) è presente una regola di sincronizzazione con direzione **Inbound** (In ingresso) e l'azione **Provision** (Provisioning). Questo indica che l'oggetto metaverse rimarrà fino a quando sarà presente l'oggetto spazio connettore. Se nell'elenco delle regole di sincronizzazione è invece visualizzata una regola di sincronizzazione con direzione **Outbound** (In uscita) e l'azione **Provision** (Provisioning), questo oggetto viene eliminato alla rimozione dell'oggetto metaverse.
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cslineageout.png) Inoltre, nella colonna **PasswordSync** (Sincronizzazione password) lo spazio connettore in ingresso può modificare la password, poiché una regola di sincronizzazione presenta il valore **True**. Questa password viene poi inviata ad Azure AD attraverso la regola in uscita.

Dalla scheda Lineage (Derivazione) è possibile accedere al metaverse facendo clic su [Metaverse Object Properties](#metaverse-object-properties)(Proprietà oggetto metaverse).

In fondo a tutte le schede sono disponibili due pulsanti: **Preview** (Anteprima) e **Log** (Log).

**Anteprima**  
 La pagina Preview (Anteprima) viene usata per sincronizzare un solo oggetto. È utile mentre si risolvono problemi relativi a regole di sincronizzazione di un cliente e si vuole vedere l'effetto di una modifica su un singolo oggetto. È possibile scegliere tra **Full Sync** (Sincronizzazione completa) e **Delta sync** (Sincronizzazione differenziale). È anche possibile scegliere tra **Generate Preview** (Genera anteprima), per mantenere semplicemente la modifica in memoria, e **Commit Preview** (Esegui commit anteprima), per inserire tutte le modifiche negli spazi connettore di destinazione.
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/preview1.png) Si può esaminare l'oggetto e individuare la regola applicata per un particolare flusso di attributi.
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/preview2.png)

**Log**  
La pagina Log consente di visualizzare lo stato e la cronologia di sincronizzazione della password.

### <a name="metaverse-object-properties"></a>Metaverse Object Properties
**Attributes (Attributi) (Attributi)**  
 Nella scheda Attributes (Attributi) è possibile visualizzare i valori e il connettore che li ha indicati.
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/mvattributes.png)
**Connectors (Connettori)**  
 La scheda Connectors (Connettori) visualizza tutti gli spazi connettore che hanno una rappresentazione dell'oggetto.
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/mvconnectors.png) Questa scheda consente anche di accedere all' [oggetto spazio connettore](#connector-space-object-properties).

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Ulteriori informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).



<!--HONumber=Nov16_HO3-->


