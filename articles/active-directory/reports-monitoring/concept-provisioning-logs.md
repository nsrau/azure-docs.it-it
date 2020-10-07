---
title: Log di provisioning nel portale di Azure Active Directory (anteprima) | Microsoft Docs
description: Introduzione ai report dei log di provisioning nel portale di Azure Active Directory
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
ms.date: 10/07/2020
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6109f35c42d4b4a44430eeb99ec115f4cdc1a619
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812557"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Provisioning dei report nel portale di Azure Active Directory (anteprima)

L'architettura di report in Azure Active Directory (Azure AD) include i componenti seguenti:

- **Attività** 
    - **Accessi**: informazioni sull'uso delle applicazioni gestite e sulle attività di accesso degli utenti.
    - **Log**  -  di controllo I [log di controllo](concept-audit-logs.md) forniscono informazioni sulle attività di sistema relative a utenti e gestione dei gruppi, applicazioni gestite e attività di directory.
    - **Log di provisioning** : fornire le attività di sistema relative a utenti, gruppi e ruoli di cui è stato effettuato il provisioning tramite il servizio di provisioning di Azure ad. 

- **Sicurezza** 
    - **Accessi a rischio** : un [accesso rischioso](../identity-protection/overview-identity-protection.md) è un indicatore di un tentativo di accesso che potrebbe essere stato eseguito da un utente che non è il legittimo proprietario di un account utente.
    - **Utenti contrassegnati per il rischio** : un [utente rischioso](../identity-protection/overview-identity-protection.md) è un indicatore per un account utente che potrebbe essere stato compromesso.

Questo argomento offre una panoramica del report di provisioning.

## <a name="prerequisites"></a>Prerequisiti

### <a name="who-can-access-the-data"></a>Chi può accedere ai dati?
* Proprietari dell'applicazione
* Utenti nei ruoli amministratore sicurezza, lettore sicurezza, lettore report, amministratore applicazione e amministratore applicazione cloud
* Amministratori globali


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>Che Azure AD licenza è necessario per accedere alle attività di provisioning?

Il tenant deve disporre di una licenza di Azure AD Premium associata per visualizzare il report di tutte le attività di provisioning. vedere [Procedura: Effettuare l'iscrizione alle edizioni Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) per aggiornare l'edizione di Azure Active Directory in uso. 

## <a name="provisioning-logs"></a>Log di provisioning

I log di provisioning forniscono risposte alle domande seguenti:

* Quali gruppi sono stati creati correttamente in ServiceNow?
* Come sono stati importati i ruoli da Amazon Web Services?
* Quali utenti non sono stati creati correttamente in DropBox?

È possibile accedere ai log di provisioning selezionando i **log di provisioning** nella sezione **monitoraggio** del pannello **Azure Active Directory** nel [portale di Azure](https://portal.azure.com). Potrebbero essere necessarie fino a due ore per la visualizzazione di alcuni record del provisioning nel portale.

![Log di provisioning](./media/concept-provisioning-logs/access-provisioning-logs.png "Log di provisioning")


Un log di provisioning ha una visualizzazione elenco predefinita che mostra:

- Identità
- Azione
- Sistema di origine
- Sistema di destinazione
- Stato
- Data


![Colonne predefinite](./media/concept-provisioning-logs/default-columns.png "Colonne predefinite")

È possibile personalizzare la visualizzazione elenco facendo clic su **colonne** nella barra degli strumenti.

![Selezione colonne](./media/concept-provisioning-logs/column-chooser.png "Selezione colonne")

In questo modo è possibile visualizzare campi aggiuntivi o rimuovere campi già visualizzati.

![Colonne disponibili](./media/concept-provisioning-logs/available-columns.png "Colonne disponibili")

Selezionare un elemento nella visualizzazione elenco per ottenere maggiori informazioni dettagliate.

![Informazioni dettagliate](./media/concept-provisioning-logs/steps.png "Filtro")


## <a name="filter-provisioning-activities"></a>Filtrare le attività di provisioning

È possibile filtrare i dati di provisioning. Alcuni valori di filtro vengono popolati dinamicamente in base al tenant. Se, ad esempio, non sono presenti eventi di creazione nel tenant, non sarà disponibile un'opzione di filtro per crea.
Nella visualizzazione predefinita è possibile selezionare i filtri seguenti:

- Identità
- Data
- Stato
- Azione


![Aggiungere filtri](./media/concept-provisioning-logs/default-filter.png "Filtro")

Il filtro di **identità** consente di specificare il nome o l'identità a cui si è interessati. Questa identità può essere un utente, un gruppo, un ruolo o un altro oggetto. È possibile eseguire la ricerca in base al nome o all'ID dell'oggetto. L'ID varia in base allo scenario. Ad esempio, quando si esegue il provisioning di un oggetto da Azure AD a SalesForce, l'ID di origine è l'ID oggetto dell'utente in Azure AD mentre TargetID è l'ID dell'utente in Salesforce. Quando si effettua il provisioning da giorni lavorativi a Active Directory, l'ID di origine è l'ID del dipendente del lavoro lavorativo. Si noti che il nome dell'utente potrebbe non essere sempre presente nella colonna Identity. Ci sarà sempre un ID. 


Il filtro **Date** (Data) permette di definire un intervallo di tempo per i dati restituiti.  
I valori possibili sono:

- 1 mese
- 7 giorni
- 30 giorni
- 24 ore
- Intervallo di tempo personalizzato

Quando si seleziona un intervallo di tempo personalizzato, è possibile configurare una data di inizio e una data di fine.


Il filtro **Stato** consente di selezionare:

- All
- Operazione completata
- Operazioni non riuscite
- Operazione ignorata



Il filtro **azione** consente di filtrare:

- Create 
- Aggiornamento
- Delete
- Disabilitazione
- Altri

Inoltre, per i filtri della visualizzazione predefinita, è anche possibile impostare i filtri seguenti:

- ID processo
- ID ciclo
- ID modifica
- ID origine
- ID destinazione
- Applicazione


![Selezionare un campo](./media/concept-provisioning-logs/add-filter.png "Selezionare un campo")


- **ID processo** : un ID processo univoco è associato a ogni applicazione per la quale è stato abilitato il provisioning.   

- **ID ciclo** : identifica in modo univoco il ciclo di provisioning. È possibile condividere questo ID per supportare per cercare il ciclo in cui si è verificato l'evento.

- **ID modifica** : identificatore univoco per l'evento di provisioning. È possibile condividere questo ID per supportare per cercare l'evento di provisioning.   


- **Sistema di origine** : consente di specificare la posizione da cui viene effettuato il provisioning dell'identità. Ad esempio, quando si esegue il provisioning di un oggetto da Azure AD a ServiceNow, il sistema di origine è Azure AD. 

- **Sistema di destinazione** : consente di specificare la posizione in cui viene effettuato il provisioning dell'identità. Ad esempio, quando si esegue il provisioning di un oggetto da Azure AD a ServiceNow, il sistema di destinazione è ServiceNow. 

- **Applicazione** : consente di visualizzare solo i record delle applicazioni con un nome visualizzato che contiene una stringa specifica.

 

## <a name="provisioning-details"></a>Dettagli del provisioning 

Quando si seleziona un elemento nella visualizzazione elenco di provisioning, si ottengono ulteriori dettagli su questo elemento.
I dettagli sono raggruppati in base alle categorie seguenti:

- Passaggi

- Risoluzione dei problemi e suggerimenti

- Proprietà modificate

- Riepilogo


![Dettagli del provisioning](./media/concept-provisioning-logs/provisioning-tabs.png "Schede")



### <a name="steps"></a>Passaggi

Nella scheda **passaggi** vengono descritti i passaggi necessari per eseguire il provisioning di un oggetto. Il provisioning di un oggetto può essere costituito da quattro passaggi: 

- Importa oggetto
- Determinare se l'oggetto è nell'ambito
- Corrispondenza oggetto tra origine e destinazione
- Oggetto provisioning (azione da eseguire: può trattarsi di una creazione, aggiornamento, eliminazione o disabilitazione)



![Screenshot mostra la scheda passaggi, che mostra i passaggi di provisioning.](./media/concept-provisioning-logs/steps.png "Filtro")


### <a name="troubleshoot-and-recommendations"></a>Risoluzione dei problemi e suggerimenti


La scheda **risoluzione dei problemi e indicazioni** fornisce il codice e il motivo dell'errore. Le informazioni sull'errore sono disponibili solo in caso di errore. 


### <a name="modified-properties"></a>Proprietà modificate

Le **proprietà modificate** visualizzano il valore precedente e il nuovo valore. Nei casi in cui non è presente alcun valore precedente, la colonna del valore precedente è vuota. 


### <a name="summary"></a>Riepilogo

La scheda **Riepilogo** fornisce una panoramica delle operazioni eseguite e degli identificatori per l'oggetto nel sistema di origine e di destinazione. 

## <a name="what-you-should-know"></a>Informazioni importanti

- Il portale di Azure archivia i dati di provisioning segnalati per 30 giorni se si dispone di un'edizione Premium e di 7 giorni se si dispone di un'edizione gratuita. I log di provisioning possono essere pubblicati in log Analytics per la conservazione oltre 30 giorni. 

- È possibile usare l'attributo Change ID come identificatore univoco. Questo è, ad esempio, utile quando si interagisce con il supporto tecnico.

- Attualmente non è disponibile alcuna opzione per scaricare i dati di provisioning come file CSV, ma è possibile esportare i dati usando [Microsoft Graph](https://docs.microsoft.com/graph/api/provisioningobjectsummary-list?view=graph-rest-beta&tabs=http).

- È possibile che vengano visualizzati eventi ignorati per gli utenti che non rientrano nell'ambito. Questo comportamento è previsto, soprattutto quando l'ambito di sincronizzazione è impostato su tutti gli utenti e i gruppi. Il servizio valuterà tutti gli oggetti nel tenant, anche quelli che non rientrano nell'ambito. 

- I log di provisioning non sono attualmente disponibili nel cloud per enti pubblici. Se non si è in grado di accedere ai log di provisioning, usare i log di controllo come soluzione temporanea.  

## <a name="error-codes"></a>Codici errore

Usare la tabella seguente per comprendere meglio come risolvere gli errori che si possono trovare nei log di provisioning. Per i codici di errore mancanti, fornire commenti e suggerimenti usando il collegamento nella parte inferiore della pagina. 

|Codice di errore|Descrizione|
|---|---|
|Conflitto, EntryConflict|Correggere i valori di attributo in conflitto in Azure AD o nell'applicazione oppure verificare la configurazione dell'attributo corrispondente se è necessario che l'account utente in conflitto corrisponda e venga ripreso. Per ulteriori informazioni sulla configurazione degli attributi corrispondenti, consultare la seguente [documentazione](../app-provisioning/customize-application-attributes.md) .|
|TooManyRequests|L'app di destinazione ha rifiutato questo tentativo di aggiornare l'utente perché è sovraccarico e riceve troppe richieste. Non è necessario eseguire alcuna operazione. Il tentativo verrà ritirato automaticamente. Microsoft ha inoltre ricevuto una notifica di questo problema.|
|InternalServerError |L'app di destinazione ha restituito un errore imprevisto. Potrebbe essersi verificato un problema del servizio con l'applicazione di destinazione che impedisce l'esecuzione di questa operazione. Questo tentativo verrà ritirato automaticamente tra 40 minuti.|
|InsufficientRights, MethodNotAllowed, NotPermitted, non autorizzato| Azure AD stato in grado di eseguire l'autenticazione con l'applicazione di destinazione, ma non è stato autorizzato a eseguire l'aggiornamento. Esaminare le istruzioni fornite dall'applicazione di destinazione, nonché l' [esercitazione](../saas-apps/tutorial-list.md)relativa all'applicazione corrispondente.|
|UnprocessableEntity|L'applicazione di destinazione ha restituito una risposta imprevista. La configurazione dell'applicazione di destinazione potrebbe non essere corretta o potrebbe essersi verificato un problema del servizio con l'applicazione di destinazione che impedisce il funzionamento di questo.|
|WebExceptionProtocolError |Si è verificato un errore del protocollo HTTP durante la connessione all'applicazione di destinazione. Non è necessario eseguire alcuna operazione. Questo tentativo verrà ritirato automaticamente tra 40 minuti.|
|InvalidAnchor|Un utente creato in precedenza o corrispondente al servizio di provisioning non esiste più. Verificare che l'utente esista. Per forzare una nuova corrispondenza di tutti gli utenti, usare il API Graph MS per [riavviare il processo](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta). Si noti che il riavvio del provisioning attiverà un ciclo iniziale, operazione che può richiedere del tempo. Elimina inoltre la cache usata dal servizio di provisioning. Ciò significa che tutti gli utenti e i gruppi nel tenant dovranno essere nuovamente valutati e potrebbero essere eliminati determinati eventi di provisioning.|
|NotImplemented | L'app di destinazione ha restituito una risposta imprevista. La configurazione dell'app potrebbe non essere corretta o potrebbe essersi verificato un problema del servizio con l'app di destinazione che impedisce il funzionamento. Esaminare le istruzioni fornite dall'applicazione di destinazione, nonché l' [esercitazione](../saas-apps/tutorial-list.md)relativa all'applicazione corrispondente. |
|MandatoryFieldsMissing, MissingValues |Impossibile creare l'utente perché mancano i valori obbligatori. Correggere i valori degli attributi mancanti nel record di origine oppure verificare la configurazione dell'attributo corrispondente per assicurarsi che i campi obbligatori non vengano omessi. [Altre](../app-provisioning/customize-application-attributes.md) informazioni sulla configurazione degli attributi corrispondenti.|
|SchemaAttributeNotFound |Non è stato possibile eseguire l'operazione perché è stato specificato un attributo che non esiste nell'applicazione di destinazione. Vedere la [documentazione](../app-provisioning/customize-application-attributes.md) sulla personalizzazione degli attributi e verificare che la configurazione sia corretta.|
|InternalError |Si è verificato un errore interno del servizio all'interno del servizio di provisioning Azure AD. Non è necessario eseguire alcuna operazione. Questo tentativo verrà ritentato automaticamente tra 40 minuti.|
|InvalidDomain |Non è stato possibile eseguire l'operazione a causa di un valore di attributo contenente un nome di dominio non valido. Aggiornare il nome di dominio dell'utente o aggiungerlo all'elenco delle applicazioni consentite nell'applicazione di destinazione. |
|Timeout |Non è stato possibile completare l'operazione perché l'applicazione di destinazione ha impiegato troppo tempo per rispondere. Non è necessario eseguire alcuna operazione. Questo tentativo verrà ritentato automaticamente tra 40 minuti.|
|LicenseLimitExceeded|Impossibile creare l'utente nell'applicazione di destinazione perché non sono disponibili licenze per questo utente. Ottenere licenze aggiuntive per l'applicazione di destinazione o esaminare le assegnazioni degli utenti e la configurazione del mapping degli attributi per assicurarsi che gli utenti corretti vengano assegnati con gli attributi corretti.|
|DuplicateTargetEntries  |Non è stato possibile completare l'operazione perché è stato trovato più di un utente nell'applicazione di destinazione con gli attributi corrispondenti configurati. Rimuovere l'utente duplicato dall'applicazione di destinazione o riconfigurare i mapping degli attributi come descritto [qui](../app-provisioning/customize-application-attributes.md).|
|DuplicateSourceEntries | Non è stato possibile completare l'operazione perché è stato trovato più di un utente con gli attributi corrispondenti configurati. Rimuovere l'utente duplicato o riconfigurare i mapping degli attributi come descritto [qui](../app-provisioning/customize-application-attributes.md).|
|ImportSkipped | Quando viene valutato ogni utente, si tenta di importare l'utente dal sistema di origine. Questo errore si verifica in genere quando all'utente importato manca la proprietà corrispondente definita nei mapping degli attributi. Senza un valore presente nell'oggetto utente per l'attributo corrispondente, non è possibile valutare le modifiche dell'ambito, della corrispondenza o dell'esportazione. Si noti che la presenza di questo errore non indica che l'utente è nell'ambito perché non è ancora stata valutata la definizione dell'ambito per l'utente.|
|EntrySynchronizationSkipped | Il servizio di provisioning ha eseguito una query sul sistema di origine e ha identificato l'utente. Non è stata eseguita alcuna azione aggiuntiva per l'utente e i relativi elementi sono stati ignorati. Il salto potrebbe essere dovuto all'esterno dell'ambito o all'utente già esistente nel sistema di destinazione senza che siano necessarie altre modifiche.|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| Quando si esegue una richiesta GET per recuperare un utente o un gruppo, nella risposta sono stati ricevuti più utenti o gruppi. Si prevede di ricevere un solo utente o gruppo nella risposta. Se, [ad esempio](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#get-group), si esegue una richiesta GET per recuperare un gruppo e si fornisce un filtro per escludere i membri e l'endpoint scim restituisce i membri, verrà generato questo errore.|

## <a name="next-steps"></a>Passaggi successivi

* [Verificare lo stato del provisioning utenti](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [Problemi di configurazione del provisioning utenti in un'applicazione della raccolta di Azure AD](../app-provisioning/application-provisioning-config-problem.md)
* [API Graph dei log di provisioning](https://docs.microsoft.com/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta)
