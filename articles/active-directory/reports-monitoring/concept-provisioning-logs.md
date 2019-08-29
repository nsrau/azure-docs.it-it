---
title: Log di provisioning nel portale di Azure Active Directory (anteprima) | Microsoft Docs
description: Introduzione ai report delle attività di provisioning nel portale di Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/29/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74b2d80d21822758991ad8b3bc1d3bb44e2257f4
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129809"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Provisioning dei report nel portale di Azure Active Directory (anteprima)

L'architettura di report in Azure Active Directory (Azure AD) include i componenti seguenti:

- **Attività** 
    - **Accessi**: informazioni sull'uso delle applicazioni gestite e sulle attività di accesso degli utenti.
    - **Log di controllo** - [log di controllo](concept-audit-logs.md) forniscono informazioni sulle attività di sistema relative a gestione di utenti e gruppi, applicazioni gestite e attività di directory.
    - **Log** di provisioning: fornire le attività di sistema relative a utenti, gruppi e ruoli di cui è stato effettuato il provisioning tramite il servizio di provisioning di Azure ad. 

- **Sicurezza** 
    - **Accessi a rischio**: un [accesso a rischio](concept-risky-sign-ins.md) indica un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente.
    - **Utenti contrassegnati per il rischio**: un [utente a rischio](concept-user-at-risk.md) indica un account utente che potrebbe essere stato compromesso.

Questo argomento offre una panoramica del report di provisioning.

## <a name="prerequisites"></a>Prerequisiti

### <a name="who-can-access-the-data"></a>Chi può accedere ai dati?
* Utenti nei ruoli amministratore sicurezza, lettore sicurezza, lettore report, amministratore applicazione e amministratore applicazione cloud
* Amministratori globali


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>Che Azure AD licenza è necessario per accedere alle attività di provisioning?

Il tenant deve disporre di una licenza di Azure AD Premium associata per visualizzare il report di tutte le attività di provisioning. vedere [Procedura: Effettuare l'iscrizione alle edizioni Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) per aggiornare l'edizione di Azure Active Directory in uso. 

## <a name="provisioning-logs"></a>Log di provisioning

I log di provisioning forniscono risposte alle domande seguenti:

* Quali gruppi sono stati creati correttamente in ServiceNow?
* Come sono stati importati i ruoli da Amazon Web Services?
* Quali utenti non sono stati creati correttamente in DropBox?

È possibile accedere ai log di provisioning selezionando i **log** di provisioning nella sezione **monitoraggio** del pannello **Azure Active Directory** nel [portale di Azure](https://portal.azure.com). Potrebbero essere necessarie fino a due ore per la visualizzazione di alcuni record del provisioning nel portale.

![Log] di provisioning (./media/concept-provisioning-logs/access-provisioning-logs.png "Log") di provisioning


Un log di provisioning ha una visualizzazione elenco predefinita che mostra:

- Identità
- Azione
- Sistema di origine
- Sistema di destinazione
- Stato
- Data


![Colonne predefinite](./media/concept-provisioning-logs/default-columns.png "Colonne predefinite")

Per personalizzare la visualizzazione elenco, fare clic su **Colonne** nella barra degli strumenti.

![Selezione colonne](./media/concept-provisioning-logs/column-chooser.png "Selezione colonne")

In questo modo è possibile visualizzare campi aggiuntivi o rimuovere campi già visualizzati.

![Colonne disponibili](./media/concept-provisioning-logs/available-columns.png "Colonne disponibili")

Selezionare un elemento nella visualizzazione elenco per ottenere maggiori informazioni dettagliate.

![Informazioni dettagliate](./media/concept-provisioning-logs/detailed-information.png "Informazioni dettagliate")


## <a name="filter-provisioning-activities"></a>Filtrare le attività di provisioning

Per limitare i dati segnalati a un livello che funziona automaticamente, è possibile filtrare i dati di provisioning usando i campi predefiniti seguenti. Si noti che i valori nei filtri vengono popolati in modo dinamico in base al tenant. Se, ad esempio, non sono presenti eventi di creazione nel tenant, non sarà disponibile un'opzione di filtro per crea.

- identità
- Azione
- Sistema di origine
- Sistema di destinazione
- Stato
- Date


![Filtro] di (./media/concept-provisioning-logs/filter.png "Filtro") di

Il filtro di **identità** consente di specificare il nome o l'identità a cui si è interessati. Questa identità può essere un utente, un gruppo, un ruolo o un altro oggetto. È possibile eseguire la ricerca in base al nome o all'ID dell'oggetto. L'ID varia in base allo scenario. Ad esempio, quando si esegue il provisioning di un oggetto da Azure AD a SalesForce, l'ID di origine è l'ID oggetto dell'utente in Azure AD mentre TargetID è l'ID dell'utente in Salesforce. Quando si effettua il provisioning da giorni lavorativi a Active Directory, l'ID di origine è l'ID del dipendente del lavoro lavorativo. Si noti che il nome dell'utente potrebbe non essere sempre presente nella colonna Identity. Ci sarà sempre un ID. 

Il filtro di **sistema di origine** consente di specificare la posizione da cui viene effettuato il provisioning dell'identità. Ad esempio, quando si esegue il provisioning di un oggetto da Azure AD a ServiceNow, il sistema di origine è Azure AD. 

Il filtro di **sistema di destinazione** consente di specificare la posizione in cui viene effettuato il provisioning dell'identità. Ad esempio, quando si esegue il provisioning di un oggetto da Azure AD a ServiceNow, il sistema di destinazione è ServiceNow. 

Il filtro **Stato** consente di selezionare:

- Tutti
- Riuscito
- Errore
- Ignorata

Il filtro **azione** consente di filtrare:

- Create 
- Aggiorna
- Eliminare
- Disattiva
- Altro

Il filtro **Date** (Data) permette di definire un intervallo di tempo per i dati restituiti.  
I valori possibili sono:

- 1 mese
- 7 giorni
- 30 giorni
- 24 ore
- Intervallo di tempo personalizzato

Quando si seleziona un intervallo di tempo personalizzato, è possibile configurare una data di inizio e una data di fine.


Oltre ai campi predefiniti, quando è selezionata, è anche possibile includere i campi seguenti nel filtro:

- **ID processo** : un ID processo univoco è associato a ogni applicazione per la quale è stato abilitato il provisioning.   

- **ID ciclo** : identifica in modo univoco il ciclo di provisioning. È possibile condividere questo ID per supportare per cercare il ciclo in cui si è verificato l'evento.

- **ID modifica** : identificatore univoco per l'evento di provisioning. È possibile condividere questo ID per supportare per cercare l'evento di provisioning.   



  

## <a name="provisioning-details"></a>Dettagli del provisioning 

Quando si seleziona un elemento nella visualizzazione elenco di provisioning, si ottengono ulteriori dettagli su questo elemento.
I dettagli sono raggruppati in base alle categorie seguenti:

- Passaggi

- Risoluzione dei problemi e suggerimenti

- Proprietà modificate

- Riepilogo


![Filtro] di (./media/concept-provisioning-logs/provisioning-tabs.png "Schede") di



### <a name="steps"></a>Passaggi

Nella scheda **passaggi** vengono descritti i passaggi necessari per eseguire il provisioning di un oggetto. Il provisioning di un oggetto può essere costituito da quattro passaggi: 

- Importa oggetto
- Determinare se l'oggetto è nell'ambito
- Corrispondenza oggetto tra origine e destinazione
- Oggetto provisioning (azione da eseguire: può trattarsi di una creazione, aggiornamento, eliminazione o disabilitazione)



![Filtro] di (./media/concept-provisioning-logs/steps.png "Filtro") di


### <a name="troubleshoot-and-recommendations"></a>Risoluzione dei problemi e suggerimenti


La scheda **risoluzione dei problemi e indicazioni** fornisce il codice e il motivo dell'errore. Le informazioni sull'errore sono disponibili solo in caso di errore. 


### <a name="modified-properties"></a>Proprietà modificate

Le **proprietà modificate** visualizzano il valore precedente e il nuovo valore. Nei casi in cui non è presente alcun valore precedente, la colonna del valore precedente è vuota. 


### <a name="summary"></a>Riepilogo

La scheda **Riepilogo** fornisce una panoramica delle operazioni eseguite e degli identificatori per l'oggetto nel sistema di origine e di destinazione. 

## <a name="what-you-should-know"></a>Informazioni utili

- Il portale di Azure archivia i dati di provisioning segnalati per 30 giorni se si dispone di un'edizione Premium e di 7 giorni se si dispone di un'edizione gratuita.

- È possibile usare l'attributo Change ID come identificatore univoco. Questo è, ad esempio, utile quando si interagisce con il supporto tecnico.

- Attualmente non è disponibile alcuna opzione per scaricare i dati di provisioning.

- Attualmente non è disponibile alcun supporto per log Analytics.

- Quando si accede ai log di provisioning dal contesto di un'app, non filtra automaticamente gli eventi per l'app specifica nel modo in cui i log di controllo lo eseguono.

## <a name="next-steps"></a>Passaggi successivi

* [Verificare lo stato del provisioning utenti](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [Problemi di configurazione del provisioning utenti in un'applicazione raccolta Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


