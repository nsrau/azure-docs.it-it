---
title: Comprendere il funzionamento del provisioning di Azure AD. Documenti Microsoft
description: Comprendere il funzionamento del provisioning di Azure ADUnderstand how Azure AD provisioning works works
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 241d90981ed9ba54d253e6c22c00f9e5a9197863
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884886"
---
# <a name="how-provisioning-works"></a>Come funziona il provisioning

Il provisioning automatico si riferisce alla creazione di identità utente e ruoli nelle applicazioni cloud a cui gli utenti devono accedere. Oltre a creare le identità utente, il provisioning automatico include la manutenzione e la rimozione delle identità utente quando lo stato o i ruoli cambiano. Prima di avviare una distribuzione, è possibile esaminare questo articolo per informazioni sul funzionamento del provisioning di Azure AD e per ottenere consigli sulla configurazione. 

Il servizio di provisioning di **Azure AD esegue** il provisioning degli utenti nelle app SaaS e in altri sistemi connettendosi a un endpoint API di gestione utenti SCIM (System for Cross-Domain Identity Management) 2.0 fornito dal fornitore dell'applicazione. Questo endpoint SCIM consente ad Azure AD di creare, aggiornare e rimuovere utenti a livello di codice. Per le applicazioni selezionate, il servizio di provisioning può anche creare, aggiornare e rimuovere oggetti correlati all'identità aggiuntivi, ad esempio gruppi e ruoli. Il canale usato per il provisioning tra Azure AD e l'applicazione viene crittografato usando la crittografia HTTPS TLS.


![Figura 1:](./media/how-provisioning-works/provisioning0.PNG)
Servizio di provisioning di Azure ADAzure AD Provisioning Service*Figure 1: The Azure AD Provisioning Service*

![Flusso di](./media/how-provisioning-works/provisioning1.PNG)
lavoro di provisioning degli utenti in uscita Figura 2: flusso di lavoro di*provisioning utente "in uscita" da Azure AD alle applicazioni SaaS più popolariOutbound user provisioning workflow Figure 2: "Outbound" user provisioning workflow from Azure AD to popular SaaS applications*

![Flusso di lavoro](./media/how-provisioning-works/provisioning2.PNG)
di provisioning degli utenti in ingresso Figura 3: Flusso di lavoro di*provisioning degli utenti "in ingresso" dalle applicazioni più popolari di Human Capital Management (HCM) ad Azure Active Directory e Windows Server Active Directory*

## <a name="provisioning-using-scim-20"></a>Provisioning tramite SCIM 2.0

Il servizio di provisioning di Azure AD usa il [protocollo SCIM 2.0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards) per il provisioning automatico. Il servizio si connette all'endpoint SCIM per l'applicazione e utilizza lo schema dell'oggetto utente SCIM e le API REST per automatizzare il provisioning e il deprovisioning di utenti e gruppi. Per la maggior parte delle applicazioni nella raccolta di Azure AD viene fornito un connettore di provisioning basato su SCIM. Quando si creano app per Azure AD, gli sviluppatori possono usare l'API di gestione utenti di SCIM 2.0 per creare un endpoint SCIM che integra Azure AD per il provisioning. Per informazioni dettagliate, vedere [Creare un endpoint SCIM e configurare](../app-provisioning/use-scim-to-provision-users-and-groups.md)il provisioning degli utenti.

Per richiedere un connettore di provisioning automatico di Azure AD per un'app che attualmente non ne ha uno, compilare una [richiesta di applicazione di Azure Active Directory.](https://aka.ms/aadapprequest)

## <a name="authorization"></a>Autorizzazione

Le credenziali sono necessarie per la connessione di Azure AD all'API di gestione degli utenti dell'applicazione. Durante la configurazione del provisioning automatico degli utenti per un'applicazione, è necessario immettere credenziali valide. È possibile trovare i tipi di credenziali e i requisiti per l'applicazione facendo riferimento all'esercitazione sull'app. Nel portale di Azure, sarà possibile testare le credenziali facendo in modo che Azure AD tenti di connettersi all'app di provisioning dell'app usando le credenziali fornite.

Se l'accesso Single Sign-On basato su SAML è configurato anche per l'applicazione, il limite di archiviazione interno di Azure AD per applicazione è 1024 byte. Questo limite include tutti i certificati, i token segreti, le credenziali e i dati di configurazione correlati associati a una singola istanza di un'applicazione (noto anche come record dell'entità servizio in Azure AD). Quando è configurato l'accesso Single Sign-On basato su SAML, il certificato utilizzato per firmare i token SAML spesso consuma oltre il 50% dello spazio. Eventuali elementi aggiuntivi (token segreti, URI, indirizzi di posta elettronica di notifica, nomi utente e password) immessi durante l'installazione del provisioning degli utenti potrebbero superare il limite di archiviazione. Per ulteriori informazioni, vedere [Problema di salvataggio delle credenziali di amministratore durante](../manage-apps/application-provisioning-config-problem-storage-limit.md)la configurazione del provisioning degli utenti .

## <a name="mapping-attributes"></a>Attributi di mappatura

Quando si abilita il provisioning degli utenti per un'applicazione SaaS di terze parti, il portale di Azure controlla i valori degli attributi tramite i mapping degli attributi. I mapping determinano gli attributi utente che passano tra Azure AD e l'applicazione di destinazione quando viene eseguito il provisioning o l'aggiornamento degli account utente.

Esiste un set preconfigurato di attributi e mapping di attributi tra gli oggetti utente di Azure AD e gli oggetti utente di ogni app SaaS. Alcune app gestiscono altri tipi di oggetti insieme agli utenti, ad esempio Gruppi.

Quando si configura il provisioning, è importante esaminare e configurare i mapping degli attributi e i flussi di lavoro che definiscono il flusso di proprietà utente (o gruppo) da Azure AD all'applicazione. Esaminare e configurare la proprietà corrispondente (**Corrispondenza oggetti utilizzando questo attributo**) utilizzata per identificare e abbinare in modo univoco gli utenti/gruppi tra i due sistemi.

È possibile personalizzare i mapping degli attributi predefiniti in base alle esigenze aziendali. In questo modo, è possibile modificare o eliminare i mapping di attributi esistenti o creare nuovi mapping di attributi. Per informazioni dettagliate, consultate Personalizzazione dei mapping degli attributi di provisioning degli utenti per le [applicazioni SaaS.](../manage-apps/customize-application-attributes.md)

Quando si configura il provisioning in un'applicazione SaaS, come mapping degli attributi è possibile specificare il mapping di espressioni. Per questi mapping, è necessario scrivere un'espressione simile a script che consenta di trasformare i dati degli utenti in formati più accettabili per l'applicazione SaaS. Per informazioni dettagliate, vedere [Scrittura di espressioni per i mapping](functions-for-customizing-application-data.md)degli attributi .

## <a name="scoping"></a>Scoping 
### <a name="assignment-based-scoping"></a>Ambito basato sull'assegnazione

Per il provisioning in uscita da Azure AD a un'applicazione SaaS, basarsi sulle [assegnazioni](../manage-apps/assign-user-or-group-access-portal.md) di utenti o gruppi è il modo più comune per determinare quali utenti rientrano nell'ambito per il provisioning. Poiché le assegnazioni utente vengono utilizzate anche per abilitare l'accesso Single Sign-On, lo stesso metodo può essere utilizzato per la gestione dell'accesso e del provisioning. L'ambito basato sulle assegnazioni non si applica agli scenari di provisioning in ingresso, ad esempio Workday e Successfactors.Assignment-based scoping doesn't apply to inbound provisioning scenarios such as Workday and Successfactors.

* **Gruppi.** Con un piano di licenza di Azure AD Premium, è possibile usare i gruppi per assegnare l'accesso a un'applicazione SaaS.With an Azure AD Premium license plan, you can use groups to assign access to a SaaS application. Quindi, quando l'ambito di provisioning è impostato su **Sincronizza solo gli utenti e**i gruppi assegnati, il servizio di provisioning di Azure AD eseguirà il provisioning o il deprovisioning degli utenti in base al fatto che siano membri di un gruppo assegnato all'applicazione. Il provisioning dell'oggetto gruppo stesso non viene eseguito a meno che l'applicazione non supporti gli oggetti gruppo. Assicurarsi che i gruppi assegnati all'applicazione abbiano la proprietà "SecurityEnabled" impostata su "False".

* **Gruppi dinamici.** Il servizio di provisioning degli utenti di Azure AD può leggere ed eseguire il provisioning degli utenti in [gruppi dinamici.](../users-groups-roles/groups-create-rule.md) Tieni presenti queste avvertenze e raccomandazioni:

  * I gruppi dinamici possono influire sulle prestazioni del provisioning end-to-end da Azure AD alle applicazioni SaaS.Dynamic groups can impact the performance of end-to-end provisioning from Azure AD to SaaS applications.

  * La velocità di provisioning o il deprovisioning di un utente in un gruppo dinamico in un'applicazione SaaS dipende dalla velocità con cui il gruppo dinamico può valutare le modifiche di appartenenza. Per informazioni su come controllare lo stato di elaborazione di un gruppo dinamico, vedere Controllare lo stato di elaborazione di una regola di [appartenenza.](../users-groups-roles/groups-create-rule.md)

  * Quando un utente perde l'appartenenza al gruppo dinamico, viene considerato un evento di deprovisioning. Si consideri questo scenario quando si creano regole per i gruppi dinamici.

* **Gruppi nidificati.** Il servizio di provisioning degli utenti di Azure AD non è in grado di leggere o eseguire il provisioning degli utenti in gruppi annidati. Il servizio può solo leggere ed eseguire il provisioning degli utenti che sono membri immediati di un gruppo assegnato in modo esplicito. Questa limitazione delle "assegnazioni basate su gruppo alle applicazioni" influisce anche sull'accesso Single Sign-On (vedere [Utilizzo di un gruppo per gestire l'accesso alle applicazioni SaaS](../users-groups-roles/groups-saasapps.md)). Assegnare invece o altrimenti [l'ambito nei](define-conditional-rules-for-provisioning-user-accounts.md) gruppi che contengono gli utenti di cui è necessario eseguire il provisioning.

### <a name="attribute-based-scoping"></a>Ambito basato su attributi 

È possibile utilizzare i filtri di ambito per definire regole basate su attributi che determinano gli utenti di cui viene eseguito il provisioning in un'applicazione. Questo metodo viene comunemente usato per il provisioning in ingresso dalle applicazioni HCM ad Azure AD e Active Directory.This method is commonly used for inbound provisioning from HCM applications to Azure AD and Active Directory. I filtri di ambito sono configurati come parte dei mapping degli attributi per ogni connettore di provisioning dell'utente di Azure AD. Per informazioni dettagliate sulla configurazione di filtri di ambito basati su attributi, vedere Provisioning di applicazioni basate su attributi [con filtri di ambito](define-conditional-rules-for-provisioning-user-accounts.md).

### <a name="b2b-guest-users"></a>Utenti B2B (ospite)

È possibile usare il servizio di provisioning degli utenti di Azure AD per eseguire il provisioning degli utenti B2B (o guest) in Azure AD alle applicazioni SaaS.It's possible to use the Azure AD user provisioning service to provision B2B (or guest) users in Azure AD to SaaS applications. Tuttavia, per consentire agli utenti B2B di accedere all'applicazione SaaS con Azure AD, l'applicazione SaaS deve disporre della funzionalità Single Sign-On basata su SAML configurata in modo specifico. Per altre informazioni su come configurare le applicazioni SaaS per supportare gli accessi dagli utenti B2B, vedere [Configurare app SaaS per Collaborazione B2B](../b2b/configure-saas-apps.md).

Si noti che il userPrincipalName per un utente guest@domain.comviene spesso archiviato come "alias "EXT". Quando userPrincipalName è incluso nei mapping degli attributi come attributo di origine, l'#EXT viene rimosso da userPrincipalName. Se è necessario che il #EXT sia presente, sostituire userPrincipalName con originalUserPrincipalName come attributo di origine. 

## <a name="provisioning-cycles-initial-and-incremental"></a>Cicli di provisioning: iniziale e incrementale

Quando Azure AD è il sistema di origine, il servizio di provisioning usa la [query Usa delta per tenere traccia delle modifiche nei dati](https://docs.microsoft.com/graph/delta-query-overview) di Microsoft Graph per monitorare utenti e gruppi. Il servizio di provisioning esegue un ciclo iniziale sul sistema di origine e sul sistema di destinazione, seguito da cicli incrementali periodici.

### <a name="initial-cycle"></a>Ciclo iniziale

Quando il servizio di provisioning viene avviato, il primo ciclo:

1. Il servizio esegue una query su tutti gli utenti e i gruppi presenti nel sistema di origine e recupera tutti gli attributi definiti nei [mapping degli attributi](customize-application-attributes.md).

2. Il servizio filtra gli utenti e i gruppi restituiti, usando [assegnazioni](../manage-apps/assign-user-or-group-access-portal.md) configurate o [filtri di ambito basati su attributi](define-conditional-rules-for-provisioning-user-accounts.md).

3. Quando un utente viene assegnato o viene definito nell'ambito per il provisioning, il servizio esegue una query sul sistema di destinazione per un utente corrispondente utilizzando gli [attributi corrispondenti](customize-application-attributes.md#understanding-attribute-mapping-properties)specificati. Se ad esempio il nome userPrincipal nel sistema di origine è un attributo di corrispondenza ed è mappato a userName nel sistema di destinazione, il servizio di provisioning esegue una query sul sistema di destinazione per trovare i valori di userName che corrispondono ai valori del nome userPrincipal nel sistema di origine.

4. Se un utente corrispondente non viene trovato nel sistema di destinazione, viene creato utilizzando gli attributi restituiti dal sistema di origine. Dopo aver creato l'account utente, il servizio di provisioning rileva e memorizza nella cache l'ID del sistema di destinazione per il nuovo utente. Questo ID viene utilizzato per eseguire tutte le operazioni future sull'utente.

5. Se viene trovato un utente corrispondente, viene aggiornato utilizzando gli attributi forniti dal sistema di origine. Dopo la corrispondenza dell'account utente, il servizio di provisioning rileva e memorizza nella cache l'ID del sistema di destinazione per il nuovo utente. Questo ID viene utilizzato per eseguire tutte le operazioni future sull'utente.

6. Se i mapping degli attributi contengono attributi di "riferimento", il servizio esegue ulteriori aggiornamenti nel sistema di destinazione per creare e collegare gli oggetti di riferimento. È ad esempio possibile che nel sistema di destinazione un utente abbia un attributo "Manager" collegato a un altro utente creato nel sistema di destinazione.

7. Mantenere una filigrana alla fine del ciclo iniziale, che fornisce il punto di partenza per i cicli incrementali successivi.

Alcune applicazioni come ServiceNow, G Suite e Box supportano non solo il provisioning degli utenti, ma anche il provisioning dei gruppi e dei relativi membri. In questi casi, se il provisioning dei gruppi è abilitato nei [mapping,](customize-application-attributes.md)il servizio di provisioning sincronizza gli utenti e i gruppi e successivamente sincronizza le appartenenze ai gruppi.

### <a name="incremental-cycles"></a>Cicli incrementali

Dopo il ciclo iniziale, tutti gli altri cicli:

1. Il servizio esegue una query sul sistema di origine per trovare gli utenti e i gruppi che sono stati aggiornati dopo il salvataggio dell'ultima filigrana.

2. Il servizio filtra gli utenti e i gruppi restituiti, usando [assegnazioni](../manage-apps/assign-user-or-group-access-portal.md) configurate o [filtri di ambito basati su attributi](define-conditional-rules-for-provisioning-user-accounts.md).

3. Quando un utente viene assegnato o viene definito nell'ambito per il provisioning, il servizio esegue una query sul sistema di destinazione per un utente corrispondente utilizzando gli [attributi corrispondenti](customize-application-attributes.md#understanding-attribute-mapping-properties)specificati.

4. Se un utente corrispondente non viene trovato nel sistema di destinazione, viene creato utilizzando gli attributi restituiti dal sistema di origine. Dopo aver creato l'account utente, il servizio di provisioning rileva e memorizza nella cache l'ID del sistema di destinazione per il nuovo utente. Questo ID viene utilizzato per eseguire tutte le operazioni future sull'utente.

5. Se viene trovato un utente corrispondente, viene aggiornato utilizzando gli attributi forniti dal sistema di origine. Se si tratta di un account appena assegnato che viene confrontato, il servizio di provisioning rileva e memorizza nella cache l'ID del sistema di destinazione per il nuovo utente. Questo ID viene utilizzato per eseguire tutte le operazioni future sull'utente.

6. Se i mapping degli attributi contengono attributi di "riferimento", il servizio esegue ulteriori aggiornamenti nel sistema di destinazione per creare e collegare gli oggetti di riferimento. È ad esempio possibile che nel sistema di destinazione un utente abbia un attributo "Manager" collegato a un altro utente creato nel sistema di destinazione.

7. Se un utente precedentemente incluso nell'ambito per il provisioning viene rimosso dall'ambito, incluso l'annullamento dell'assegnazione, il servizio disabilita l'utente nel sistema di destinazione tramite un aggiornamento.

8. Se un utente precedentemente incluso nell'ambito per il provisioning viene disabilitato o eliminato temporaneamente nel sistema di origine, il servizio disabilita l'utente nel sistema di destinazione eseguendo un aggiornamento.

9. Se un utente precedentemente incluso nell'ambito per il provisioning viene eliminato definitivamente nel sistema di origine, il servizio elimina l'utente nel sistema di destinazione. In Azure AD gli utenti vengono eliminati definitivamente 30 giorni dopo l'eliminazione temporanea.

10. Mantenere una nuova filigrana alla fine del ciclo incrementale, che fornisce il punto di partenza per i cicli incrementali successivi.

> [!NOTE]
> Se lo si desidera, è possibile disabilitare le operazioni **Create**, **Update**o **Delete** utilizzando le caselle di controllo Azioni oggetto di **destinazione** nella sezione [Mapping](customize-application-attributes.md) . La logica per disabilitare un utente durante un aggiornamento viene controllata anche tramite un mapping di attributi da un campo come "accountEnabled".

Il servizio di provisioning continua a eseguire cicli incrementali back-to-back a tempo indeterminato, a intervalli definiti [nell'esercitazione specifica per ogni applicazione.](../saas-apps/tutorial-list.md) I cicli incrementali continuano fino a quando non si verifica uno dei seguenti eventi:

- Il servizio viene arrestato manualmente tramite il portale di Azure o il comando API Microsoft Graph appropriato.
- Un nuovo ciclo iniziale viene attivato usando l'opzione **Cancella stato e riavvia** nel portale di Azure o usando il comando API Microsoft Graph appropriato. Questa azione cancella qualsiasi filigrana memorizzata e fa sì che tutti gli oggetti di origine vengano valutati nuovamente.
- Un nuovo ciclo iniziale viene attivato a causa di una modifica nei mapping degli attributi o nei filtri di ambito. Questa azione cancella anche qualsiasi filigrana memorizzata e fa sì che tutti gli oggetti di origine vengano valutati nuovamente.
- Il processo di provisioning viene messo in quarantena (vedere di seguito) a causa di un tasso di errore elevato e rimane in quarantena per più di quattro settimane. In questo caso, il servizio viene disabilitato automaticamente.

### <a name="errors-and-retries"></a>Errori e ripetizione di tentativi

Se un errore nel sistema di destinazione impedisce l'aggiunta, l'aggiornamento o l'eliminazione di un singolo utente nel sistema di destinazione, l'operazione viene ritentata nel ciclo di sincronizzazione successivo. Se l'errore si verifica nuovamente, la frequenza dei tentativi viene gradualmente ridotta fino a un solo tentativo al giorno. Per risolvere l'errore, gli amministratori devono controllare [i registri](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) di provisioning per determinare la causa principale ed eseguire l'azione appropriata. Tra gli errori comuni possono essere inclusi i seguenti:

- Per gli utenti nel sistema di origine non è definito un valore di attributo che è necessario nel sistema di destinazione.
- Utenti con un valore di attributo nel sistema di origine per il quale è presente un vincolo univoco nel sistema di destinazione e lo stesso valore è presente in un altro record utente

Risolvere questi errori modificando i valori degli attributi per l'utente interessato nel sistema di origine o modificando i mapping degli attributi in modo che non causino conflitti.

### <a name="quarantine"></a>Quarantena

Se la maggior parte o tutte le chiamate effettuate sul sistema di destinazione hanno esito negativo in modo coerente a causa di un errore (ad esempio credenziali di amministratore non valide), il processo di provisioning passa allo stato "quarantena". Questo stato è indicato nel report di riepilogo del provisioning e tramite posta elettronica se le notifiche tramite posta elettronica sono state configurate nel portale di Azure.This state is indicated in the [provisioning summary report](../manage-apps/check-status-user-account-provisioning.md) and via email if email notifications were configured in the Azure portal.

Quando sei in quarantena, la frequenza dei cicli incrementali viene gradualmente ridotta a una volta al giorno.

Il processo di provisioning esce dalla quarantena dopo che tutti gli errori sono stati corretti e viene avviato il ciclo di sincronizzazione successivo. Se lo stato di quarantena dura per più di quattro settimane, il processo di provisioning viene disabilitato. Scopri di più qui sullo stato di quarantena [qui](../manage-apps/application-provisioning-quarantine-status.md).

### <a name="how-long-provisioning-takes"></a>Tempo richiesto per il provisioning

Le prestazioni dipendono dal fatto che il processo di provisioning esegua un ciclo di provisioning iniziale o incrementale. Per informazioni dettagliate sul tempo necessario per il provisioning e su come monitorare lo stato del servizio di provisioning, vedere [Controllare lo stato del provisioning degli utenti.](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>Come stabilire se il provisioning degli utenti viene eseguito correttamente

Tutte le operazioni eseguite dal servizio di provisioning utenti vengono registrate nei log di provisioning di Azure AD [(anteprima).](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) I log includono tutte le operazioni di lettura e scrittura eseguite nei sistemi di origine e di destinazione e i dati utente letti o scritti durante ogni operazione. Per informazioni su come leggere i log di provisioning nel portale di Azure, vedere la guida alla creazione di report di [provisioning.](../manage-apps/check-status-user-account-provisioning.md)

## <a name="de-provisioning"></a>Deprovisioning

Il servizio di provisioning di Azure AD mantiene sincronizzati i sistemi di origine e di destinazione eseguendo il deprovisioning degli account quando gli utenti non devono più avere accesso. 

Il servizio di provisioning di Azure AD eliminerà temporaneamente un utente in un'applicazione quando l'applicazione elimina temporaneamente (richiesta di aggiornamento con active - false) e si verifica uno degli eventi seguenti:

* L'account utente viene eliminato in Azure ADThe user account is deleted in Azure AD
*   L'utente non è assegnato dall'applicazione
*   L'utente non soddisfa più un filtro di ambito e esce dall'ambito
    * Per impostazione predefinita, il servizio di provisioning di Azure AD elimina o disabilita gli utenti che escono dall'ambito. Se si desidera eseguire l'override di questo comportamento predefinito, è possibile impostare un flag per [ignorare le eliminazioni all'esterno dell'ambito](../app-provisioning/skip-out-of-scope-deletions.md).
*   La proprietà AccountEnabled è impostata su False

Se si verifica uno dei quattro eventi precedenti e l'applicazione di destinazione non supporta le eliminazioni software, il servizio di provisioning invierà una richiesta DELETE per eliminare definitivamente l'utente dall'app. 

30 giorni dopo l'eliminazione di un utente in Azure AD, verranno eliminati definitivamente dal tenant. A questo punto, il servizio di provisioning invierà una richiesta DELETE per eliminare definitivamente l'utente nell'applicazione. In qualsiasi momento durante la finestra di 30 giorni, è possibile [eliminare manualmente un utente in modo permanente,](../fundamentals/active-directory-users-restore.md)che invia una richiesta di eliminazione all'applicazione.

Se nei mapping degli attributi viene visualizzato un attributo IsSoftDeleted, questo viene utilizzato per determinare lo stato dell'utente e se inviare una richiesta di aggiornamento con il valore attivo , false per eliminare temporaneamente l'utente. 

## <a name="next-steps"></a>Passaggi successivi

[Pianificare una distribuzione automatica del provisioning utenti](../app-provisioning/plan-auto-user-provisioning.md)

[Configurare il provisioning per un'app della raccolta](../manage-apps/configure-automatic-user-provisioning-portal.md)

[Creare un endpoint SCIM e configurare il provisioning durante la creazione di un'app personalizzata](../app-provisioning/use-scim-to-provision-users-and-groups.md)

Risolvere i problemi relativi alla configurazione e al provisioning degli [utenti in un'applicazione](../manage-apps/application-provisioning-config-problem.md).
