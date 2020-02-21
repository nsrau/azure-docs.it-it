---
title: Informazioni sul funzionamento del provisioning Azure AD | Microsoft Docs
description: Informazioni sul funzionamento del provisioning Azure AD
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
ms.openlocfilehash: 892cdeea20780c90ce325e8be9b7b91fee0d9fad
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77522527"
---
# <a name="how-provisioning-works"></a>Come funziona il provisioning

Il provisioning automatico si riferisce alla creazione di identità e ruoli utente nelle applicazioni cloud a cui gli utenti devono accedere. Oltre a creare le identità utente, il provisioning automatico include la manutenzione e la rimozione delle identità utente quando lo stato o i ruoli cambiano. Prima di iniziare una distribuzione, è possibile leggere questo articolo per informazioni sul funzionamento del provisioning di Azure AD e ottenere consigli sulla configurazione. 

Il **servizio di provisioning Azure ad esegue il provisioning** degli utenti nelle app SaaS e in altri sistemi connettendosi a un endpoint dell'API di gestione utenti di gestione delle identità tra domini (SCIM) 2,0 fornito dal fornitore dell'applicazione. Questo endpoint SCIM consente Azure AD di creare, aggiornare e rimuovere gli utenti a livello di codice. Per le applicazioni selezionate, il servizio di provisioning può anche creare, aggiornare e rimuovere oggetti correlati all'identità aggiuntivi, ad esempio gruppi e ruoli. Il canale usato per il provisioning tra Azure AD e l'applicazione viene crittografato con la crittografia SSL HTTPS.


![Azure AD servizio di provisioning](./media/how-provisioning-works/provisioning0.PNG)
*Figura 1: il servizio di provisioning Azure ad*

![flusso di lavoro di provisioning utenti *in uscita](./media/how-provisioning-works/provisioning1.PNG)
figura 2: flusso di lavoro di provisioning utenti "in uscita" da Azure ad a applicazioni SaaS più diffuse*

![flusso di lavoro di provisioning utenti *in ingresso](./media/how-provisioning-works/provisioning2.PNG)
figura 3: flusso di lavoro di provisioning utenti "in ingresso" da applicazioni di gestione di capitale umana più diffuse per Azure Active Directory e Windows Server Active Directory*

## <a name="provisioning-using-scim-20"></a>Provisioning con SCIM 2,0

Il servizio di provisioning Azure AD usa il [protocollo SCIM 2,0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards) per il provisioning automatico. Il servizio si connette all'endpoint SCIM per l'applicazione e usa lo schema dell'oggetto utente SCIM e le API REST per automatizzare il provisioning e il deprovisioning di utenti e gruppi. Per la maggior parte delle applicazioni nella raccolta Azure AD viene fornito un connettore di provisioning basato su SCIM. Quando si compilano app per Azure AD, gli sviluppatori possono usare l'API di gestione utenti SCIM 2,0 per compilare un endpoint SCIM che integra Azure AD per il provisioning. Per informazioni dettagliate, vedere [creare un endpoint scim e configurare il provisioning degli utenti](../app-provisioning/use-scim-to-provision-users-and-groups.md).

Per richiedere un connettore di provisioning automatico Azure AD per un'app che attualmente non dispone di un connettore, compilare una [richiesta Azure Active Directory applicazione](https://aka.ms/aadapprequest).

## <a name="authorization"></a>Autorizzazione

Per Azure AD connettersi all'API di gestione degli utenti dell'applicazione, sono necessarie le credenziali. Durante la configurazione del provisioning utenti automatico per un'applicazione, è necessario immettere credenziali valide. È possibile trovare i tipi di credenziali e i requisiti per l'applicazione facendo riferimento all'esercitazione sull'app. Nel portale di Azure sarà possibile testare le credenziali con Azure AD tentativo di connessione all'app di provisioning dell'app con le credenziali fornite.

Se per l'applicazione è configurata anche la Single Sign-On basata su SAML, il limite di archiviazione interno per applicazione Azure AD è pari a 1024 byte. Questo limite include tutti i certificati, i token segreti, le credenziali e i dati di configurazione correlati associati a una singola istanza di un'applicazione, nota anche come record dell'entità servizio in Azure AD. Quando la Single Sign-On basata su SAML è configurata, il certificato usato per firmare i token SAML utilizza spesso oltre il 50% dello spazio. Eventuali elementi aggiuntivi (token segreti, Uri, indirizzi di posta elettronica di notifica, nomi utente e password) immessi durante la configurazione del provisioning utenti potrebbero superare il limite di archiviazione. Per ulteriori informazioni, vedere [problemi di salvataggio delle credenziali di amministratore durante la configurazione del provisioning utenti](../manage-apps/application-provisioning-config-problem-storage-limit.md).

## <a name="mapping-attributes"></a>Attributi di mapping

Quando si Abilita il provisioning utenti per un'applicazione SaaS di terze parti, il portale di Azure controlla i valori degli attributi tramite i mapping degli attributi. I mapping determinano gli attributi utente che fluiscono tra Azure AD e l'applicazione di destinazione quando viene eseguito il provisioning o l'aggiornamento degli account utente.

Esiste un set preconfigurato di attributi e mapping degli attributi tra Azure AD oggetti utente e gli oggetti utente di ogni app SaaS. Alcune app gestiscono altri tipi di oggetti insieme agli utenti, ad esempio i gruppi.

Quando si configura il provisioning, è importante esaminare e configurare i mapping degli attributi e i flussi di lavoro che definiscono le proprietà utente (o gruppo) che vengono propagate da Azure AD all'applicazione. Esaminare e configurare la proprietà corrispondente (**corrispondenza di oggetti con questo attributo**) utilizzata per identificare in modo univoco e associare utenti/gruppi tra i due sistemi.

È possibile personalizzare i mapping degli attributi predefiniti in base alle esigenze aziendali. È quindi possibile modificare o eliminare i mapping degli attributi esistenti o creare nuovi mapping degli attributi. Per informazioni dettagliate, vedere [personalizzazione dei mapping degli attributi per il provisioning degli utenti per le applicazioni SaaS](../manage-apps/customize-application-attributes.md).

Quando si configura il provisioning in un'applicazione SaaS, come mapping degli attributi è possibile specificare il mapping di espressioni. Per questi mapping è necessario scrivere un'espressione simile a uno script che consente di trasformare i dati degli utenti in formati più accettabili per l'applicazione SaaS. Per informazioni dettagliate, vedere [scrittura di espressioni per i mapping degli attributi](functions-for-customizing-application-data.md).

## <a name="scoping"></a>Scoping 
### <a name="assignment-based-scoping"></a>Ambito basato sull'assegnazione

Per il provisioning in uscita da Azure AD a un'applicazione SaaS, basarsi sulle [assegnazioni di utenti o gruppi](../manage-apps/assign-user-or-group-access-portal.md) è il modo più comune per determinare quali utenti sono inclusi nell'ambito del provisioning. Poiché le assegnazioni utente vengono usate anche per l'abilitazione di Single Sign-On, è possibile usare lo stesso metodo per gestire l'accesso e il provisioning. L'ambito basato sull'assegnazione non si applica agli scenari di provisioning in ingresso, ad esempio giorni lavorativi e SuccessFactors.

* **Gruppi.** Con un piano di licenza Azure AD Premium, è possibile usare i gruppi per assegnare l'accesso a un'applicazione SaaS. Quindi, quando l'ambito del provisioning è impostato per **sincronizzare solo utenti e gruppi assegnati**, il servizio di provisioning di Azure ad effettuerà il provisioning o il deprovisioning degli utenti a seconda che siano membri di un gruppo assegnato all'applicazione. Non viene effettuato il provisioning dell'oggetto gruppo, a meno che l'applicazione non supporti gli oggetti gruppo.

* **Gruppi dinamici.** Il servizio di provisioning utenti Azure AD può leggere ed effettuare il provisioning degli utenti in [gruppi dinamici](../users-groups-roles/groups-create-rule.md). Tenere presenti queste avvertenze e consigli:

  * I gruppi dinamici possono influenzare le prestazioni del provisioning end-to-end da Azure AD alle applicazioni SaaS.

  * La velocità con cui un utente in un gruppo dinamico viene sottoposta a provisioning o deprovisioning in un'applicazione SaaS dipende dalla velocità con cui il gruppo dinamico può valutare le modifiche dell'appartenenza. Per informazioni su come controllare lo stato di elaborazione di un gruppo dinamico, vedere [controllare lo stato di elaborazione di una regola di appartenenza](../users-groups-roles/groups-create-rule.md).

  * Quando un utente perde l'appartenenza al gruppo dinamico, viene considerato un evento di deprovisioning. Si consideri questo scenario quando si creano regole per i gruppi dinamici.

* **Gruppi annidati.** Il servizio di provisioning utenti Azure AD non è in grado di leggere o effettuare il provisioning degli utenti in gruppi Il servizio può solo leggere ed effettuare il provisioning degli utenti che sono membri immediati di un gruppo assegnato in modo esplicito. Questa limitazione delle "assegnazioni basate sui gruppi alle applicazioni" interessa anche Single Sign-On (vedere [uso di un gruppo per gestire l'accesso alle applicazioni SaaS](../users-groups-roles/groups-saasapps.md)). Assegnare invece direttamente o in altro modo l' [ambito nei](define-conditional-rules-for-provisioning-user-accounts.md) gruppi che contengono gli utenti di cui è necessario eseguire il provisioning.

### <a name="attribute-based-scoping"></a>Ambito basato su attributi 

È possibile utilizzare i filtri di ambito per definire regole basate su attributi per determinare gli utenti di cui viene eseguito il provisioning in un'applicazione. Questo metodo viene comunemente usato per il provisioning in ingresso dalle applicazioni HCM a Azure AD e Active Directory. I filtri di ambito sono configurati come parte dei mapping degli attributi per ogni connettore di provisioning dell'utente di Azure AD. Per informazioni dettagliate sulla configurazione dei filtri di ambito basati su attributi, vedere [provisioning di applicazioni basate su attributi con filtri di ambito](define-conditional-rules-for-provisioning-user-accounts.md).

### <a name="b2b-guest-users"></a>Utenti B2B (Guest)

È possibile usare il servizio di provisioning utenti Azure AD per effettuare il provisioning di utenti B2B (o Guest) in Azure AD alle applicazioni SaaS. Tuttavia, per consentire agli utenti B2B di accedere all'applicazione SaaS usando Azure AD, l'applicazione SaaS deve avere la funzionalità Single Sign-On basata su SAML configurata in modo specifico. Per altre informazioni su come configurare le applicazioni SaaS per supportare gli accessi dagli utenti B2B, vedere [Configurare app SaaS per Collaborazione B2B](../b2b/configure-saas-apps.md).

Si noti che il valore di userPrincipalName per un utente guest viene spesso archiviato come "alias # EXT #@domain.com". Quando userPrincipalName viene incluso nei mapping degli attributi come un attributo di origine, il #EXT # viene rimosso dal userPrincipalName. Se è necessario che il #EXT # sia presente, sostituire userPrincipalName con originalUserPrincipalName come attributo di origine. 

## <a name="provisioning-cycles-initial-and-incremental"></a>Cicli di provisioning: iniziale e incrementale

Quando Azure AD è il sistema di origine, il servizio di provisioning usa la [query Delta use per tenere traccia delle modifiche apportate ai dati Microsoft Graph](https://docs.microsoft.com/graph/delta-query-overview) per monitorare gli utenti e i gruppi. Il servizio di provisioning esegue un ciclo iniziale sul sistema di origine e di destinazione, seguito da cicli incrementali periodici.

### <a name="initial-cycle"></a>Ciclo iniziale

Quando il servizio di provisioning viene avviato, il primo ciclo:

1. Il servizio esegue una query su tutti gli utenti e i gruppi presenti nel sistema di origine e recupera tutti gli attributi definiti nei [mapping degli attributi](customize-application-attributes.md).

2. Il servizio filtra gli utenti e i gruppi restituiti, usando [assegnazioni](../manage-apps/assign-user-or-group-access-portal.md) configurate o [filtri di ambito basati su attributi](define-conditional-rules-for-provisioning-user-accounts.md).

3. Quando un utente viene assegnato o nell'ambito del provisioning, il servizio esegue una query sul sistema di destinazione per un utente corrispondente usando gli [attributi corrispondenti](customize-application-attributes.md#understanding-attribute-mapping-properties)specificati. Se ad esempio il nome userPrincipal nel sistema di origine è un attributo di corrispondenza ed è mappato a userName nel sistema di destinazione, il servizio di provisioning esegue una query sul sistema di destinazione per trovare i valori di userName che corrispondono ai valori del nome userPrincipal nel sistema di origine.

4. Se nel sistema di destinazione non viene trovato un utente corrispondente, questo viene creato usando gli attributi restituiti dal sistema di origine. Dopo aver creato l'account utente, il servizio di provisioning rileva e memorizza nella cache l'ID del sistema di destinazione per il nuovo utente. Questo ID viene usato per eseguire tutte le operazioni future su tale utente.

5. Se viene trovato un utente corrispondente, questo viene aggiornato usando gli attributi forniti dal sistema di origine. Dopo che l'account utente è stato associato, il servizio di provisioning rileva e memorizza nella cache l'ID del sistema di destinazione per il nuovo utente. Questo ID viene usato per eseguire tutte le operazioni future su tale utente.

6. Se i mapping degli attributi contengono attributi di riferimento, il servizio esegue ulteriori aggiornamenti nel sistema di destinazione per creare e collegare gli oggetti a cui si fa riferimento. È ad esempio possibile che nel sistema di destinazione un utente abbia un attributo "Manager" collegato a un altro utente creato nel sistema di destinazione.

7. Salva in modo permanente una filigrana alla fine del ciclo iniziale, che fornisce il punto di partenza per i cicli incrementali successivi.

Alcune applicazioni come ServiceNow, G Suite e box supportano non solo il provisioning di utenti, ma anche il provisioning dei gruppi e dei relativi membri. In questi casi, se il provisioning del gruppo è abilitato nei [mapping](customize-application-attributes.md), il servizio di provisioning Sincronizza gli utenti e i gruppi e successivamente sincronizza le appartenenze ai gruppi.

### <a name="incremental-cycles"></a>Cicli incrementali

Dopo il ciclo iniziale, tutti gli altri cicli:

1. Il servizio esegue una query sul sistema di origine per trovare gli utenti e i gruppi che sono stati aggiornati dopo il salvataggio dell'ultima filigrana.

2. Il servizio filtra gli utenti e i gruppi restituiti, usando [assegnazioni](../manage-apps/assign-user-or-group-access-portal.md) configurate o [filtri di ambito basati su attributi](define-conditional-rules-for-provisioning-user-accounts.md).

3. Quando un utente viene assegnato o nell'ambito del provisioning, il servizio esegue una query sul sistema di destinazione per un utente corrispondente usando gli [attributi corrispondenti](customize-application-attributes.md#understanding-attribute-mapping-properties)specificati.

4. Se nel sistema di destinazione non viene trovato un utente corrispondente, questo viene creato usando gli attributi restituiti dal sistema di origine. Dopo aver creato l'account utente, il servizio di provisioning rileva e memorizza nella cache l'ID del sistema di destinazione per il nuovo utente. Questo ID viene usato per eseguire tutte le operazioni future su tale utente.

5. Se viene trovato un utente corrispondente, questo viene aggiornato usando gli attributi forniti dal sistema di origine. Se è un account appena assegnato corrispondente, il servizio di provisioning rileva e memorizza nella cache l'ID del sistema di destinazione per il nuovo utente. Questo ID viene usato per eseguire tutte le operazioni future su tale utente.

6. Se i mapping degli attributi contengono attributi di riferimento, il servizio esegue ulteriori aggiornamenti nel sistema di destinazione per creare e collegare gli oggetti a cui si fa riferimento. È ad esempio possibile che nel sistema di destinazione un utente abbia un attributo "Manager" collegato a un altro utente creato nel sistema di destinazione.

7. Se un utente precedentemente incluso nell'ambito per il provisioning viene rimosso dall'ambito, inclusa la mancata assegnazione, il servizio Disabilita l'utente nel sistema di destinazione tramite un aggiornamento.

8. Se un utente precedentemente incluso nell'ambito per il provisioning viene disabilitato o eliminato temporaneamente nel sistema di origine, il servizio disabilita l'utente nel sistema di destinazione eseguendo un aggiornamento.

9. Se un utente precedentemente incluso nell'ambito per il provisioning viene eliminato definitivamente nel sistema di origine, il servizio elimina l'utente nel sistema di destinazione. In Azure AD, gli utenti vengono eliminati definitivamente 30 giorni dopo essere stati eliminati temporaneamente.

10. Salva in modo permanente una nuova filigrana alla fine del ciclo incrementale, che fornisce il punto di partenza per i cicli incrementali successivi.

> [!NOTE]
> Facoltativamente, è possibile disabilitare le operazioni di **creazione**, **aggiornamento**o **eliminazione** usando le caselle di controllo **azioni oggetto di destinazione** nella sezione [mapping](customize-application-attributes.md) . La logica per disabilitare un utente durante un aggiornamento viene controllata anche tramite un mapping di attributi da un campo come "accountEnabled".

Il servizio di provisioning continua a eseguire cicli incrementali back-to-back a tempo indeterminato, a intervalli definiti nell' [esercitazione specifica per ogni applicazione](../saas-apps/tutorial-list.md). I cicli incrementali continuano fino a quando si verifica uno degli eventi seguenti:

- Il servizio viene interrotto manualmente utilizzando il portale di Azure o utilizzando il comando Microsoft Graph API appropriato.
- Un nuovo ciclo iniziale viene attivato utilizzando l'opzione **Cancella stato e riavvia** nell'portale di Azure o utilizzando il comando Microsoft Graph API appropriato. Questa azione Cancella qualsiasi filigrana archiviata e fa in modo che tutti gli oggetti di origine vengano nuovamente valutati.
- Viene attivato un nuovo ciclo iniziale a causa di una modifica nei mapping degli attributi o nei filtri di ambito. Questa azione Cancella anche eventuali filigrane archiviate e fa in modo che tutti gli oggetti di origine vengano nuovamente valutati.
- Il processo di provisioning entra in quarantena (vedere di seguito) a causa di una frequenza di errori elevata e rimane in quarantena per più di quattro settimane. In questo caso, il servizio viene disabilitato automaticamente.

### <a name="errors-and-retries"></a>Errori e ripetizione di tentativi

Se un errore nel sistema di destinazione impedisce l'aggiunta, l'aggiornamento o l'eliminazione di un singolo utente nel sistema di destinazione, l'operazione viene ritentata nel ciclo di sincronizzazione successivo. Se l'errore si verifica nuovamente, la frequenza dei tentativi viene gradualmente ridotta fino a un solo tentativo al giorno. Per risolvere il problema, gli amministratori devono controllare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) per determinare la causa principale e intraprendere l'azione appropriata. Tra gli errori comuni possono essere inclusi i seguenti:

- Per gli utenti nel sistema di origine non è definito un valore di attributo che è necessario nel sistema di destinazione.
- Gli utenti hanno un valore di attributo nel sistema di origine per il quale esiste un vincolo UNIQUE nel sistema di destinazione e lo stesso valore è presente in un altro record utente

Risolvere questi errori modificando i valori degli attributi per l'utente interessato nel sistema di origine o modificando i mapping degli attributi in modo che non causino conflitti.

### <a name="quarantine"></a>Quarantena

Se la maggior parte o tutte le chiamate effettuate sul sistema di destinazione hanno esito negativo a causa di un errore (ad esempio, credenziali di amministratore non valide), il processo di provisioning passa in uno stato di "quarantena". Questo stato è indicato nel [report di riepilogo del provisioning](../manage-apps/check-status-user-account-provisioning.md) e tramite posta elettronica se le notifiche di posta elettronica sono state configurate nel portale di Azure.

In quarantena, la frequenza dei cicli incrementali viene gradualmente ridotta a una volta al giorno.

Il processo di provisioning termina la quarantena dopo che tutti gli errori sono stati risolti e viene avviato il ciclo di sincronizzazione successivo. Se lo stato di quarantena dura per più di quattro settimane, il processo di provisioning viene disabilitato. Per altre informazioni, vedere lo stato di quarantena [qui](../manage-apps/application-provisioning-quarantine-status.md).

### <a name="how-long-provisioning-takes"></a>Tempo richiesto per il provisioning

Le prestazioni variano a seconda che il processo di provisioning esegua un ciclo di provisioning iniziale o un ciclo incrementale. Per informazioni dettagliate sul tempo necessario per il provisioning e su come monitorare lo stato del servizio di provisioning, vedere [controllare lo stato del](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)provisioning dell'utente.

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>Come stabilire se il provisioning degli utenti è stato eseguito correttamente

Tutte le operazioni eseguite dal servizio di provisioning utenti vengono registrate nei [log di provisioning Azure ad (anteprima)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). I log includono tutte le operazioni di lettura e scrittura effettuate nei sistemi di origine e di destinazione e i dati utente letti o scritti durante ogni operazione. Per informazioni su come leggere i log di provisioning nel portale di Azure, vedere la [Guida alla creazione di report sul provisioning](../manage-apps/check-status-user-account-provisioning.md).

## <a name="de-provisioning"></a>Deprovisioning

Il servizio di provisioning Azure AD mantiene sincronizzati i sistemi di origine e di destinazione tramite il deprovisioning degli account quando gli utenti non devono più avere accesso. 

Il servizio di provisioning di Azure AD eliminerà temporaneamente un utente in un'applicazione quando l'applicazione suupports l'eliminazione temporanea (richiesta di aggiornamento con Active = false) e si verifica uno degli eventi seguenti:

* L'account utente viene eliminato in Azure AD
*   L'utente non è assegnato all'applicazione
*   L'utente non soddisfa più un filtro di ambito e esce dall'ambito
    * Per impostazione predefinita, il servizio di provisioning Azure AD elimina temporaneamente o Disabilita gli utenti che non rientrano nell'ambito. Se si desidera eseguire l'override di questo comportamento predefinito, è possibile impostare un flag per [ignorare le eliminazioni out-of-scope](../app-provisioning/skip-out-of-scope-deletions.md).
*   La proprietà AccountEnabled è impostata su false

Se si verifica uno dei quattro eventi precedenti e l'applicazione di destinazione non supporta le eliminazioni software, il servizio di provisioning invierà una richiesta DELETE per eliminare definitivamente l'utente dall'app. 

30 giorni dopo l'eliminazione di un utente in Azure AD, verranno eliminati definitivamente dal tenant. A questo punto, il servizio di provisioning invierà una richiesta DELETE per eliminare definitivamente l'utente nell'applicazione. In qualsiasi momento durante la finestra di 30 giorni, è possibile [eliminare manualmente un utente in modo permanente](../fundamentals/active-directory-users-restore.md), che invia una richiesta DELETE all'applicazione.

Se viene visualizzato un attributo IsSoftDeleted nei mapping degli attributi, viene usato per determinare lo stato dell'utente e se inviare una richiesta di aggiornamento con Active = false per eliminare temporaneamente l'utente. 

## <a name="next-steps"></a>Passaggi successivi

[Pianificare una distribuzione automatica del provisioning utenti](../app-provisioning/plan-auto-user-provisioning.md)

[Configurare il provisioning per un'app della raccolta](../manage-apps/configure-automatic-user-provisioning-portal.md)

[Compilare un endpoint SCIM e configurare il provisioning quando si crea un'app personalizzata](../app-provisioning/use-scim-to-provision-users-and-groups.md)

[Risolvere i problemi di configurazione e provisioning degli utenti in un'applicazione](../manage-apps/application-provisioning-config-problem.md).
