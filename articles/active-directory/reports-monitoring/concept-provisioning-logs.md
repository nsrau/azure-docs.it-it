---
title: Provisioning dei log nel portale di Azure Active Directory (anteprima) Documenti Microsoft
description: Introduzione al provisioning dei report attività nel portale di Azure Active Directory
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
ms.date: 11/04/2019
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6e0c697f9ab9796feade9b4d5c2a64794f3980b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73612804"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Provisioning dei report nel portale di Azure Active Directory (anteprima)Provisioning reports in the Azure Active Directory portal (preview)

L'architettura di report in Azure Active Directory (Azure AD) include i componenti seguenti:

- **Attività** 
    - **Accessi**: informazioni sull'uso delle applicazioni gestite e sulle attività di accesso degli utenti.
    - **Registri di controllo** - [I log](concept-audit-logs.md) di controllo forniscono informazioni sulle attività di sistema relative alla gestione di utenti e gruppi, alle applicazioni gestite e alle attività della directory.
    - **Log di provisioning:** fornire l'attività di sistema su utenti, gruppi e ruoli di cui viene eseguito il provisioning dal servizio di provisioning di Azure AD. 

- **Sicurezza** 
    - **Adesempiomenti rischiosi:** un [accesso rischioso](concept-risky-sign-ins.md) è un indicatore di un tentativo di accesso che potrebbe essere stato eseguito da un utente che non è il legittimo proprietario di un account utente.
    - **Utenti contrassegnati per il rischio:** un [utente rischioso](concept-user-at-risk.md) è un indicatore per un account utente che potrebbe essere stato compromesso.

In questo argomento viene fornita una panoramica del report di provisioning.

## <a name="prerequisites"></a>Prerequisiti

### <a name="who-can-access-the-data"></a>Chi può accedere ai dati?
* Utenti nei ruoli Amministratore sicurezza, Lettore sicurezza, Lettore report, Amministratore applicazioni e Amministratore applicazioni cloud
* Amministratori globali


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>Quale licenza di Azure AD è necessaria per accedere alle attività di provisioning?

Il tenant deve avere una licenza di Azure AD Premium associata per visualizzare il report attività di provisioning attivo. vedere [Procedura: Effettuare l'iscrizione alle edizioni Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) per aggiornare l'edizione di Azure Active Directory in uso. 

## <a name="provisioning-logs"></a>Log di provisioning

I log di provisioning forniscono le risposte alle domande seguenti:The provisioning logs provide answers to the following questions:

* Quali gruppi sono stati creati correttamente in ServiceNow?
* Come sono stati importati i ruoli da Amazon Web Services?
* Quali utenti sono stati creati senza successo in DropBox?

È possibile accedere ai log di provisioning selezionando Log di provisioning nella sezione Monitoraggio del pannello Azure Active Directory nel portale di Azure.You can access the provisioning logs by selecting **Provisioning Logs** in the **Monitoring** section of the Azure **Active Directory** blade in the [Azure portal.](https://portal.azure.com) La visualizzazione di alcuni record di provisioning nel portale può richiedere fino a due ore.

![Log di provisioning](./media/concept-provisioning-logs/access-provisioning-logs.png "Log di provisioning")


Un log di provisioning ha una visualizzazione elenco predefinita che mostra:A provisioning log has a default list view that shows:

- L'identità
- L'azione
- Il sistema di origine
- Il sistema di destinazione
- Lo stato
- La data


![Colonne predefinite](./media/concept-provisioning-logs/default-columns.png "Colonne predefinite")

È possibile personalizzare la visualizzazione elenco facendo clic su **Colonne** nella barra degli strumenti.

![Scelta colonne](./media/concept-provisioning-logs/column-chooser.png "Scelta colonne")

In questo modo è possibile visualizzare campi aggiuntivi o rimuovere campi già visualizzati.

![Colonne disponibili](./media/concept-provisioning-logs/available-columns.png "Colonne disponibili")

Selezionare un elemento nella visualizzazione elenco per ottenere maggiori informazioni dettagliate.

![Informazioni dettagliate](./media/concept-provisioning-logs/steps.png "Filtro")


## <a name="filter-provisioning-activities"></a>Filtrare le attività di provisioning

Per restringere i dati segnalati a un livello adatto alle attività, è possibile filtrare i dati di provisioning utilizzando i campi predefiniti seguenti. Si noti che i valori nei filtri vengono popolati dinamicamente in base al tenant. Se, ad esempio, non si dispone di eventi di creazione nel tenant, non sarà disponibile un'opzione di filtro per la creazione.

- Identità
- Azione
- Sistema di origine
- Sistema di destinazione
- Stato
- Data


![Filtro](./media/concept-provisioning-logs/filter.png "Filtro")

Il filtro **Identità** consente di specificare il nome o l'identità desiderata. Questa identità può essere un utente, un gruppo, un ruolo o un altro oggetto. È possibile eseguire la ricerca in base al nome o all'ID dell'oggetto. L'ID varia in base allo scenario. Ad esempio, quando si esegue il provisioning di un oggetto da Azure AD a SalesForce, l'ID origine è l'ID oggetto dell'utente in Azure AD mentre TargetID è l'ID dell'utente in Salesforce. Quando si esegue il provisioning da Workday ad Active Directory, l'ID origine è l'ID dipendente lavoratore Workday. Si noti che il nome dell'utente potrebbe non essere sempre presente nella colonna Identity. Ci sarà sempre un ID. 

Il filtro Sistema di **origine** consente di specificare da dove viene eseguito il provisioning dell'identità. Ad esempio, quando si esegue il provisioning di un oggetto da Azure AD a ServiceNow, il sistema di origine è Azure AD. 

Il filtro Sistema di **destinazione** consente di specificare dove viene eseguito il provisioning dell'identità. Ad esempio, quando si esegue il provisioning di un oggetto da Azure AD a ServiceNow, il sistema di destinazione è ServiceNow.For example, when provisioning an object from Azure AD to ServiceNow, the Target System is ServiceNow. 

Il filtro **Stato** consente di selezionare:

- Tutti
- Operazione completata
- Operazioni non riuscite
- Operazione ignorata

Il filtro **Azione** consente di filtrare:

- Create 
- Aggiornamento
- Delete
- Disabilitazione
- Altri

Il filtro **Date** (Data) permette di definire un intervallo di tempo per i dati restituiti.  
I valori possibili sono:

- 1 mese
- 7 giorni
- 30 giorni
- 24 ore
- Intervallo di tempo personalizzato

Quando si seleziona un intervallo di tempo personalizzato, è possibile configurare una data di inizio e una data di fine.


Oltre ai campi predefiniti, se selezionati, è possibile includere anche i seguenti campi nel filtro:

- **ID processo:** a ogni applicazione per cui è stato abilitato il provisioning, è associato un ID processo univoco.   

- **ID ciclo:** identifica in modo univoco il ciclo di provisioning. È possibile condividere questo ID per supportare per cercare il ciclo in cui si è verificato questo evento.

- **ID modifica-** Identificatore univoco per l'evento di provisioning. Puoi condividere questo ID come supporto per cercare l'evento di provisioning.   



  

## <a name="provisioning-details"></a>Dettagli di provisioning 

Quando si seleziona un elemento nella visualizzazione elenco di provisioning, si ottengono ulteriori dettagli su questo elemento.
I dettagli sono raggruppati in base alle seguenti categorie:

- Passaggi

- Risoluzione dei problemi e consigli

- Proprietà modificate

- Riepilogo


![Filtro](./media/concept-provisioning-logs/provisioning-tabs.png "Tabulazioni")



### <a name="steps"></a>Passaggi

Nella scheda **Passaggi** vengono descritti i passaggi eseguiti per il provisioning di un oggetto. Il provisioning di un oggetto può essere costituito da quattro passaggi:Provisioning an object can consist of four steps: 

- Importa oggetto
- Determinare se l'oggetto è nell'ambito
- Corrispondenza dell'oggetto tra origine e destinazione
- Oggetto di provisioning (azione di azione - potrebbe trattarsi di una creazione, aggiornamento, eliminazione o disabilitazione)Provision object (take action - this could be a create, update, delete, or disable)



![Filtro](./media/concept-provisioning-logs/steps.png "Filtro")


### <a name="troubleshoot-and-recommendations"></a>Risoluzione dei problemi e consigli


La scheda **Risoluzione dei problemi e suggerimenti** fornisce il codice di errore e il motivo. Le informazioni sull'errore sono disponibili solo in caso di errore. 


### <a name="modified-properties"></a>Proprietà modificate

Le **proprietà modificate** mostrano il valore precedente e il nuovo valore. Nei casi in cui non esiste alcun valore precedente, la colonna del valore precedente è vuota. 


### <a name="summary"></a>Riepilogo

La scheda **di riepilogo** fornisce una panoramica di ciò che è accaduto e gli identificatori per l'oggetto nel sistema di origine e di destinazione. 

## <a name="what-you-should-know"></a>Informazioni utili

- Il portale di Azure archivia i dati di provisioning per 30 giorni se si dispone di un'edizione Premium e 7 giorni se si dispone di un'edizione gratuita.

- È possibile utilizzare l'attributo Change ID come identificatore univoco. Ciò è utile, ad esempio, quando si interagisce con il supporto tecnico.

- Al momento non è disponibile alcuna opzione per scaricare i dati di provisioning.

- Attualmente non è disponibile alcun supporto per l'analisi dei log.

- Quando si accede ai log di provisioning dal contesto di un'app, gli eventi non vengono filtrati automaticamente nell'app specifica come fanno i log di controllo.

## <a name="error-codes"></a>Codici di errore

Utilizzare la tabella seguente per comprendere meglio come risolvere gli errori che possono essere trovati nei log di provisioning. Per tutti i codici di errore mancanti, fornire commenti e suggerimenti utilizzando il collegamento nella parte inferiore di questa pagina. 

|Codice di errore|Descrizione|
|---|---|
|Conflitto, EntryConflictConflict, EntryConflict|Correggere i valori degli attributi in conflitto in Azure AD o nell'applicazione oppure esaminare la configurazione dell'attributo corrispondente se l'account utente in conflitto doveva corrispondere e passare al controllo. Per ulteriori informazioni sulla configurazione degli attributi corrispondenti, consultare la [documentazione](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) seguente.|
|TooManyRequests|L'app di destinazione ha rifiutato questo tentativo di aggiornare l'utente perché è sovraccarico e riceve troppe richieste. Non c'è niente da fare. Questo tentativo verrà automaticamente ritirato. Microsoft è stata anche notificata di questo problema.|
|InternalServerError |L'app di destinazione ha restituito un errore imprevisto. Potrebbe essersi verificato un problema di servizio con l'applicazione di destinazione che impedisce il funzionamento di questo. Questo tentativo verrà automaticamente ritirato tra 40 minuti.|
|InsufficientRights, MethodNotAllowed, NotPermitted, Unauthorized| Azure AD è stato in grado di eseguire l'autenticazione con l'applicazione di destinazione, ma non è stato autorizzato a eseguire l'aggiornamento. Si prega di rivedere tutte le istruzioni fornite dall'applicazione di destinazione, nonché il [rispettivo tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)dell'applicazione .|
|UnprocessableEntity|L'applicazione di destinazione ha restituito una risposta imprevista. La configurazione dell'applicazione di destinazione potrebbe non essere corretta o potrebbe essersi verificato un problema di servizio con l'applicazione di destinazione che ne impedisce il funzionamento.|
|WebExceptionProtocolError |Si è verificato un errore di protocollo HTTP durante la connessione all'applicazione di destinazione. Non c'è niente da fare. Questo tentativo verrà automaticamente ritirato tra 40 minuti.|
|InvalidAnchor|Un utente creato in precedenza o corrispondente dal servizio di provisioning non esiste più. Verificare che l'utente esista. Per forzare una rieguaglianza di tutti gli utenti, utilizzare l'API MS Graph per [riavviare](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)il processo . Si noti che il riavvio del provisioning attiverà un ciclo iniziale, il cui completamento può richiedere tempo. Elimina inoltre la cache utilizzata dal servizio di provisioning per operare, il che significa che tutti gli utenti e i gruppi nel tenant dovranno essere valutati di nuovo e alcuni eventi di provisioning potrebbero essere eliminati.|
|NotImplemented | L'app di destinazione ha restituito una risposta imprevista. La configurazione dell'app potrebbe non essere corretta o potrebbe essersi verificato un problema di servizio con l'app di destinazione che impedisce il funzionamento. Si prega di rivedere tutte le istruzioni fornite dall'applicazione di destinazione, nonché il [rispettivo tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)dell'applicazione . |
|MandatoryFieldsMissing, MissingValues |Impossibile creare l'utente perché mancano i valori obbligatori. Correggere i valori degli attributi mancanti nel record di origine o esaminare la configurazione degli attributi corrispondenti per assicurarsi che i campi obbligatori non vengano omessi. [Ulteriori informazioni](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) sulla configurazione degli attributi corrispondenti.|
|SchemaAttributeNotFound |Impossibile eseguire l'operazione perché è stato specificato un attributo che non esiste nell'applicazione di destinazione. Vedere la [documentazione](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) sulla personalizzazione degli attributi e verificare che la configurazione sia corretta.|
|InternalError |Si è verificato un errore interno del servizio all'interno del servizio di provisioning di Azure AD. Non c'è niente da fare. Questo tentativo verrà automaticamente ripetuto tra 40 minuti.|
|InvalidDomain |Impossibile eseguire l'operazione a causa di un valore di attributo contenente un nome di dominio non valido. Aggiornare il nome di dominio nell'utente o aggiungerlo all'elenco consentito nell'applicazione di destinazione. |
|Timeout |Impossibile completare l'operazione perché l'applicazione di destinazione ha impiegato troppo tempo per rispondere. Non c'è niente da fare. Questo tentativo verrà automaticamente ripetuto tra 40 minuti.|
|LicenseLimitSuperato|Impossibile creare l'utente nell'applicazione di destinazione perché non sono disponibili licenze per l'utente. Procurarsi licenze aggiuntive per l'applicazione di destinazione oppure esaminare le assegnazioni utente e la configurazione del mapping degli attributi per assicurarsi che vengano assegnati gli utenti corretti con gli attributi corretti.|
|Voci DuplicateTargetE  |Impossibile completare l'operazione perché è stato trovato più di un utente nell'applicazione di destinazione con gli attributi corrispondenti configurati. Rimuovere l'utente duplicato dall'applicazione di destinazione oppure riconfigurare i mapping degli attributi come descritto [di seguito.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)|
|Voci DuplicateSourceE | Impossibile completare l'operazione perché sono stati trovati più utenti con gli attributi corrispondenti configurati. Rimuovere l'utente duplicato o riconfigurare i mapping degli attributi come descritto [di seguito.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)|

## <a name="next-steps"></a>Passaggi successivi

* [Controllare lo stato del provisioning degli utenti](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [Problemi di configurazione del provisioning utenti in un'applicazione della raccolta di Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


