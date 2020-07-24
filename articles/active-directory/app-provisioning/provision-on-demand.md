---
title: Effettuare il provisioning di un utente su richiesta usando Azure Active Directory
description: Forza sincronizzazione
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 06/23/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: 7799e873afb117481cebafd982df59a3267f4405
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87051595"
---
# <a name="on-demand-provisioning"></a>Provisioning su richiesta
Il provisioning su richiesta consente di effettuare il provisioning di un utente in un'applicazione in pochi secondi. È possibile utilizzare la funzionalità per risolvere rapidamente i problemi di configurazione, convalidare le espressioni definite, testare i filtri di ambito e molto altro ancora. 

## <a name="how-to-use-on-demand-provisioning"></a>Come usare il provisioning su richiesta 

1. Accedere al **portale di Azure**.
2. Passare ad **Applicazioni aziendali**.
3. Selezionare l'applicazione e passare alla pagina di configurazione del provisioning.
4. Configurare il provisioning fornendo le credenziali di amministratore.
5. Fare clic **su provisioning su richiesta**.
6. Cercare un utente per nome, cognome, nome visualizzato, nome dell'entità utente o indirizzo di posta elettronica.
7. Selezionare Provision (provisioning) nella parte inferiore della pagina.

:::image type="content" source="media/provision-on-demand/on-demand-provision-user.jpg" alt-text="Provisioning su richiesta di un utente.":::

## <a name="understanding-the-provisioning-steps"></a>Informazioni sui passaggi del provisioning
La funzionalità di provisioning su richiesta tenta di visualizzare i passaggi eseguiti dal servizio di provisioning quando si effettua il provisioning di un utente. Per eseguire il provisioning di un utente sono in genere disponibili cinque passaggi e una o più delle procedure riportate di seguito saranno visualizzate nell'esperienza di provisioning su richiesta.

### <a name="step-1-test-connection"></a>Passaggio 1: testare la connessione
Il servizio di provisioning tenta di autorizzare l'accesso all'applicazione di destinazione effettuando una richiesta di "utente test". Il servizio di provisioning prevede una risposta che indica che è autorizzato a continuare con la procedura di provisioning. Questo passaggio viene visualizzato solo quando si verifica un errore nel passaggio. Non viene visualizzato nell'esperienza di provisioning su richiesta quando il passaggio ha esito positivo. 

**Suggerimenti per la risoluzione dei problemi**
* Assicurarsi di aver specificato credenziali valide per l'applicazione di destinazione, ad esempio il token segreto e l'URL del tenant. Le credenziali richieste variano in base all'applicazione. Le esercitazioni dettagliate sulla configurazione sono disponibili [qui](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list). 
* Verificare che l'applicazione di destinazione supporti il filtro sugli attributi corrispondenti definiti nel pannello mapping attributi. Potrebbe essere necessario controllare la documentazione dell'API fornita dallo sviluppatore di applicazioni per comprendere i filtri supportati.  
* Per le applicazioni SCIM, è possibile usare uno strumento come posting per garantire che l'applicazione risponda alle richieste di autorizzazione come previsto dal servizio di provisioning Azure AD. Una richiesta di esempio è disponibile [qui](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#request-3).

### <a name="step-2-import-user"></a>Passaggio 2: importare un utente
Il servizio di provisioning recupera quindi l'utente dal sistema di origine. Gli attributi utente recuperati dal servizio vengono successivamente utilizzati per valutare se l'utente è nell'ambito del provisioning, controllare il sistema di destinazione per un utente esistente e determinare quali attributi utente esportare nel sistema di destinazione. 

**Visualizzare i dettagli**

La sezione Visualizza dettagli Mostra le proprietà dell'utente che sono state importate dal sistema di origine, ad esempio Azure AD.

**Suggerimenti per la risoluzione dei problemi**
* L'importazione dell'utente può avere esito negativo quando manca l'attributo corrispondente nell'oggetto utente nel sistema di origine. È possibile risolvere questo problema aggiornando l'oggetto utente con un valore per l'attributo corrispondente o modificando l'attributo corrispondente nella configurazione del provisioning.  
* Se nell'elenco importato non è presente un attributo che era previsto, assicurarsi che l'attributo abbia un valore nell'oggetto utente nel sistema di origine. Il servizio di provisioning non supporta attualmente gli attributi null di provisioning. 
* Verificare che la pagina mapping degli attributi di configurazione del provisioning includa l'attributo previsto. 

### <a name="step-3-determine-if-user-is-in-scope"></a>Passaggio 3: determinare se l'utente è nell'ambito
Successivamente, il servizio di provisioning determina se l'utente è nell' [ambito](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#scoping) per il provisioning. Il servizio considererà alcuni aspetti, ad esempio se l'utente è assegnato all'applicazione, se l'ambito è impostato per la sincronizzazione assegnata o Sincronizza tutto e i filtri di ambito definiti nella configurazione del provisioning.  

**Visualizzare i dettagli**

La sezione Visualizza dettagli Mostra le condizioni di ambito valutate. È possibile che venga visualizzata una o più delle proprietà seguenti:
* **Attivo nel sistema di origine** indica che l'utente ha la proprietà impostata su true in Azure ad.
* **Assegnato all'applicazione** indica che l'utente è assegnato all'applicazione in Azure ad
* La **sincronizzazione dell'ambito** indica che l'impostazione dell'ambito consente tutti gli utenti e i gruppi nel tenant.
* L' **utente ha il ruolo obbligatorio** indica che l'utente ha i ruoli necessari per eseguire il provisioning nell'applicazione. 
* Se sono stati definiti filtri di ambito per l'applicazione, verranno visualizzati anche i **filtri di ambito** . Il filtro verrà visualizzato con il formato seguente: {ambito filtro ambito} {ambito filtro attributo} {ambito filtro operatore} {ambito filtro di ambito}. 

**Suggerimenti per la risoluzione dei problemi**
* Assicurarsi di aver definito un ruolo di ambito valido. Ad esempio, evitare di usare l'operatore ["maggiore di"](https://docs.microsoft.com/azure/active-directory/app-provisioning/define-conditional-rules-for-provisioning-user-accounts#create-a-scoping-filter) con un valore non integer. 
* Se l'utente non ha il ruolo necessario, rivedere i suggerimenti descritti [qui](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned#provisioning-users-assigned-to-the-default-access-role). 

### <a name="step-4-match-user-between-source-and-target"></a>Passaggio 4: associare l'utente tra l'origine e la destinazione
In questo passaggio. il servizio tenta di trovare la corrispondenza con l'utente recuperato nel passaggio di importazione con un utente nel sistema di destinazione. 

**Visualizzare i dettagli**

Nelle pagine Visualizza dettagli vengono visualizzate le proprietà degli utenti che corrispondono al sistema di destinazione. Le proprietà visualizzate nel riquadro del contesto variano nel modo seguente:
* Se non è presente alcun utente corrispondente nel sistema di destinazione, non verranno visualizzate le proprietà.
* Se è presente un utente corrispondente nel sistema di destinazione, si vedranno le proprietà di tale utente corrispondente dal sistema di destinazione.
* Se sono presenti più utenti corrispondenti, verranno visualizzate le proprietà di entrambi gli utenti.
* Se più attributi corrispondenti fanno parte dei mapping degli attributi, ogni attributo corrispondente verrà valutato in sequenza e gli utenti corrispondenti verranno visualizzati. 

**Dettagli sulla risoluzione dei problemi**
* Il servizio di provisioning non è in grado di trovare in modo univoco un utente nell'origine con un utente nella destinazione. Questo problema può essere risolto assicurandosi che l'attributo corrispondente sia univoco. 
* Verificare che l'applicazione di destinazione supporti il filtro sull'attributo definito come attributo corrispondente.  

### <a name="step-5-perform-action"></a>Passaggio 5: eseguire l'azione
Infine, il servizio di provisioning esegue un'azione, ad esempio la creazione, l'aggiornamento, l'eliminazione o l'omissione dell'utente. 

:::image type="content" source="media/provision-on-demand/success-on-demand-provision.jpg" alt-text="Il provisioning dell'utente è riuscito.":::

**Visualizzare i dettagli**

La sezione Visualizza dettagli Visualizza gli attributi che sono stati modificati nell'applicazione di destinazione. Rappresenta l'output finale dell'attività del servizio di provisioning e gli attributi che sono stati esportati. Se questo passaggio ha esito negativo, gli attributi visualizzati rappresentano gli attributi che il servizio di provisioning ha tentato di modificare.  

**Suggerimenti per la risoluzione dei problemi**
* Gli errori di esportazione delle modifiche possono variare significativamente. Per errori comuni, vedere la [documentazione relativa](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs#error-codes) ai log di provisioning.


## <a name="frequently-asked-questions"></a>Domande frequenti
**È necessario disattivare il provisioning per usare il provisioning su richiesta?** Per le applicazioni che usano un bearer token o un nome utente e una password di lunga durata per l'autorizzazione, non sono necessari passaggi aggiuntivi. Le applicazioni che usano OAuth per l'autorizzazione attualmente richiedono che il processo di provisioning venga interrotto prima di usare il provisioning su richiesta. Le applicazioni come G Suite, box, area di lavoro di Facebook e slack rientrano in questa categoria. Il lavoro è in corso per consentire l'esecuzione del provisioning su richiesta per tutte le applicazioni, senza dover arrestare il provisioning. 

**Quanto tempo richiede il provisioning su richiesta?** Richiede in genere meno di 30 secondi. 

## <a name="known-limitations"></a>Limitazioni note
Attualmente esistono alcune limitazioni note. Invia un post sulla [voce utente](https://aka.ms/appprovisioningfeaturerequest) per poter assegnare una priorità migliore ai miglioramenti da apportare. Si noti che queste limitazioni sono specifiche per la funzionalità di provisioning su richiesta. per informazioni specifiche sul fatto che un'applicazione supporti il provisioning di gruppi, eliminazioni e così via, vedere l'esercitazione sull'applicazione. 

* Le applicazioni lavorative, AWS e SuccessFactors non supportano il provisioning su richiesta.
* I gruppi e i ruoli di provisioning su richiesta non sono supportati.
* Il provisioning su richiesta supporta la disabilitazione degli utenti che non sono stati assegnati dall'applicazione, ma non supporta la disabilitazione o l'eliminazione di utenti che sono stati disabilitati o eliminati da Azure Active Directory (tali utenti non verranno visualizzati durante la ricerca di un utente).

## <a name="next-steps"></a>Passaggi successivi

* [Risoluzione dei problemi di provisioning](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem)
