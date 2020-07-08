---
title: Informazioni sul funzionamento del provisioning di Azure AD | Microsoft Docs
description: Informazioni sul funzionamento del provisioning di Azure AD
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 05/20/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 38ddfad179a8ef459b5c10f84619a836193ee092
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84781651"
---
# <a name="how-provisioning-works"></a>Come funziona il provisioning

Il provisioning automatico si riferisce alla creazione di identità e ruoli utente nelle applicazioni cloud a cui gli utenti devono accedere. Oltre a creare le identità utente, il provisioning automatico include la manutenzione e la rimozione delle identità utente quando lo stato o i ruoli cambiano. Prima di iniziare una distribuzione, è possibile leggere questo articolo per informazioni sul funzionamento del provisioning di Azure AD e per ottenere consigli sulla configurazione. 

Il **servizio di provisioning di Azure AD** effettua il provisioning degli utenti nelle app SaaS e in altri sistemi connettendosi a un endpoint dell'API di gestione utenti SCIM (System for Cross-Domain Identity Management) 2.0 dai fornitori di ogni applicazione. Questo endpoint SCIM consente ad Azure AD di creare, aggiornare e rimuovere gli utenti a livello programmatico. Per alcune applicazioni, il servizio di provisioning può anche creare, aggiornare e rimuovere oggetti aggiuntivi relative alle identità, ad esempio i gruppi e i ruoli. Il canale usato per il provisioning tra Azure AD e l'applicazione viene crittografato con crittografia HTTPS TLS 1.2.


![Provisioning del servizio di Azure AD](./media/how-provisioning-works/provisioning0.PNG)
*Figura 1: Provisioning del servizio di Azure AD*

![Flusso di lavoro del provisioning di utenti in uscita](./media/how-provisioning-works/provisioning1.PNG)
*Figura 2: Flusso di lavoro di provisioning degli utenti "in uscita" da Azure AD verso applicazioni SaaS comuni*

![Flusso di lavoro del provisioning di utenti in entrata](./media/how-provisioning-works/provisioning2.PNG)
*Figura 3: Flusso di lavoro di provisioning degli utenti "in ingresso" da applicazioni di gestione delle risorse umane (HCM) comuni verso Azure Active Directory e Windows Server Active Directory*

## <a name="provisioning-using-scim-20"></a>Provisioning con SCIM 2.0

Il servizio di provisioning di Azure AD usa il [protocollo SCIM 2.0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards) per il provisioning automatico. Il servizio si connette all'endpoint SCIM per l'applicazione e usa lo schema dell'oggetto utente SCIM e le API REST per automatizzare il provisioning e il deprovisioning di utenti e gruppi. Per la maggior parte delle applicazioni nella raccolta di Azure AD viene fornito un connettore di provisioning basato su SCIM. Quando si compilano app per Azure AD, gli sviluppatori possono usare l'API di gestione utenti SCIM 2.0 per compilare un endpoint SCIM che integri Azure AD per il provisioning. Per informazioni dettagliate, vedere [Creare un endpoint SCIM e configurare il provisioning utenti](../app-provisioning/use-scim-to-provision-users-and-groups.md).

Per richiedere un connettore di provisioning automatico Azure AD per un'app che attualmente non dispone di un connettore, compilare una [Richiesta di applicazione Azure Active Directory](https://aka.ms/aadapprequest).

## <a name="authorization"></a>Autorizzazione

Le credenziali sono necessarie ad Azure AD per la connessione all'API di gestione degli utenti dell'applicazione. Durante la configurazione del provisioning utenti automatico per un'applicazione, è necessario immettere credenziali valide. È possibile trovare i tipi di credenziali e i requisiti per l'applicazione facendo riferimento all'esercitazione sull'app. Nel portale di Azure sarà possibile testare le credenziali mediante un tentativo di connessione di Azure AD all'app di provisioning dell'app con le credenziali fornite.

Se per l'applicazione è configurato anche Single Sign-On basato su SAML, il limite di archiviazione interno per applicazione di Azure AD è pari a 1024 byte. Questo limite include tutti i certificati, token segreti, credenziali e dati di configurazione correlati associati a una singola istanza di un'applicazione (definita anche record entità servizio in Azure AD). Quando si configura l'accesso Single Sign-On basato su SAML, il certificato usato per firmare i token SAML spesso usa oltre il 50% dello spazio. Qualsiasi elemento aggiuntivo, come token segreto, URI, indirizzo di posta elettronica di notifica, nome utente e password, immesso durante la configurazione del provisioning degli utenti può provocare il superamento del limite archiviazione. Per altre informazioni, vedere [Problema di salvataggio delle credenziali dell'amministratore durante la configurazione del provisioning utenti](../manage-apps/application-provisioning-config-problem-storage-limit.md).

## <a name="mapping-attributes"></a>Attributi di mapping

Quando si abilita il provisioning degli utenti per un'applicazione SaaS di terze parti, il portale di Azure ne controlla i valori degli attributi usando il mapping degli attributi. I mapping determinano gli attributi utente trasmessi tra Azure AD e l'applicazione di destinazione durante il provisioning o l'aggiornamento degli account utente.

Esiste un set preconfigurato di attributi e mapping degli attributi tra gli oggetti utente di Azure AD e gli oggetti utente di ogni app SaaS. Alcune app gestiscono altri tipi di oggetti insieme a utenti e gruppi.

Un aspetto importante da considerare quando si configura il provisioning è quello di esaminare e configurare il mapping degli attributi e i flussi di lavoro che definiscono il tipo di flusso di proprietà utente (o gruppo) da Azure AD all'applicazione. Rivedere e configurare la proprietà corrispondente (**Abbina gli oggetti in base a questo attributo**) che viene usata per identificare e abbinare in modo univoco utenti/gruppi tra i due sistemi.

È possibile personalizzare i mapping degli attributi predefiniti in base alle esigenze aziendali. Quindi è possibile modificare o eliminare i mapping degli attributi esistenti oppure crearne di nuovi. Per i dettagli, vedere [Personalizzazione dei mapping degli attributi del provisioning utenti per le applicazioni SaaS](../manage-apps/customize-application-attributes.md).

Quando si configura il provisioning in un'applicazione SaaS, come mapping degli attributi è possibile specificare il mapping di espressioni. Per questo tipo di mapping è necessario scrivere un'espressione analoga a uno script, che permette di trasformare i dati utente in formati più idonei all'applicazione SaaS. Per i dettagli, vedere [Scrittura di espressioni per i mapping degli attributi](functions-for-customizing-application-data.md).

## <a name="scoping"></a>Scoping 
### <a name="assignment-based-scoping"></a>Ambito basato sull'assegnazione

Per il provisioning in uscita da Azure AD a un'applicazione SaaS, basarsi sulle [assegnazioni di utenti o gruppi](../manage-apps/assign-user-or-group-access-portal.md) è il modo più comune per determinare quali utenti sono inclusi nell'ambito del provisioning. Poiché le assegnazioni utente vengono usate anche per l'abilitazione di Single Sign-On, è possibile usare lo stesso metodo per gestire l'accesso e il provisioning. L'ambito basato sull'assegnazione non si applica agli scenari di provisioning in ingresso, ad esempio giorni lavorativi e Successfactor.

* **Gruppi.** Con un piano di licenza Azure AD Premium è possibile usare gruppi per assegnare l'accesso a un'applicazione SaaS. Quindi, quando l'ambito del provisioning è impostato su **Sincronizza solo gli utenti e i gruppi assegnati**, il servizio di provisioning di Azure AD effettuerà il provisioning o il deprovisioning degli utenti a prescindere che siano membri o meno di un gruppo assegnato all'applicazione. Non viene effettuato il provisioning dell'oggetto gruppo, a meno che l'applicazione non supporti gli oggetti gruppo. Assicurarsi che i gruppi assegnati all'applicazione dispongano della proprietà "SecurityEnabled" impostata su "True".

* **Gruppi dinamici.** Il servizio di provisioning utenti Azure AD è in grado di leggere ed effettuare il provisioning degli utenti in [gruppi dinamici](../users-groups-roles/groups-create-rule.md). Tenere presenti questi consigli e avvertenze:

  * L'utilizzo di gruppi dinamici può compromettere le prestazioni del provisioning end-to-end da Azure AD alle applicazioni SaaS.

  * La velocità di provisioning o deprovisioning di un utente di un gruppo dinamico in un'applicazione SaaS dipende dalla velocità con cui il gruppo dinamico riesce a valutare le modifiche all'appartenenza. Per informazioni su come controllare lo stato di elaborazione di un gruppo dinamico, vedere [Controllare lo stato di elaborazione per una regola di appartenenza](../users-groups-roles/groups-create-rule.md).

  * Quando un utente perde l'appartenenza al gruppo dinamico, viene considerato come un evento di deprovisioning. Si consideri questo scenario quando si creano regole per i gruppi dinamici.

* **Gruppi annidati.** Il servizio di provisioning utenti Azure AD non è in grado di leggere o di effettuare il provisioning degli utenti in gruppi annidati. Può effettuare la lettura e il provisioning solo degli utenti che sono membri immediati del gruppo assegnato in modo esplicito. Si tratta di una limitazione delle "assegnazioni basate su gruppi alle applicazioni", che ha effetto anche su Single Sign-On e viene descritta in [Uso di un gruppo per gestire l'accesso ad applicazioni SaaS](../users-groups-roles/groups-saasapps.md). Come soluzione alternativa è consigliabile assegnare in modo esplicito o [definire l'ambito](define-conditional-rules-for-provisioning-user-accounts.md) nei gruppi contenenti gli utenti di cui è necessario effettuare il provisioning.

### <a name="attribute-based-scoping"></a>Ambito basato sugli attributi 

È possibile usare i filtri di ambito per definire regole basate su attributi per determinare gli utenti per i quali viene eseguito il provisioning per un'applicazione. Questo metodo viene usato comunemente per il provisioning in ingresso dalle applicazioni di Gestione connessione ibrida ad Azure AD e Active Directory. I filtri di ambito sono configurati come parte dei mapping degli attributi per ogni connettore di provisioning dell'utente di Azure AD. Per informazioni dettagliate sulla configurazione dei filtri di ambito basati su attributi, vedere [Provisioning dell'applicazione basato su attributi con filtri per la definizione dell'ambito](define-conditional-rules-for-provisioning-user-accounts.md).

### <a name="b2b-guest-users"></a>Utenti B2B (guest)

È possibile usare il servizio di provisioning utenti di Azure AD per effettuare il provisioning degli utenti B2B (o guest) di Azure AD in applicazioni SaaS. Tuttavia, perché gli utenti B2B possano accedere all'applicazione SaaS con Azure AD, la funzionalità Single Sign-On basata su SAML dell'applicazione SaaS deve essere configurata in modo specifico. Per altre informazioni su come configurare le applicazioni SaaS per supportare gli accessi dagli utenti B2B, vedere [Configurare app SaaS per Collaborazione B2B](../b2b/configure-saas-apps.md).

Si noti che il valore di userPrincipalName per un utente guest viene spesso archiviato come "alias#EXT#@domain.com". Quando userPrincipalName viene incluso nei mapping degli attributi come un attributo di origine, #EXT# viene rimosso da userPrincipalName. Se è necessario che #EXT# sia presente, sostituire userPrincipalName con originalUserPrincipalName come attributo di origine. 

## <a name="provisioning-cycles-initial-and-incremental"></a>Cicli di provisioning: Iniziale e incrementale

Quando Azure AD è il sistema di origine, il servizio di provisioning usa la [funzionalità di query delta per tracciare le modifiche nei dati di Microsoft Graph](https://docs.microsoft.com/graph/delta-query-overview) per monitorare gli utenti e i gruppi. Il servizio di provisioning esegue un ciclo iniziale rispetto al sistema di origine e a quello di destinazione, seguita da cicli incrementali periodici.

### <a name="initial-cycle"></a>Ciclo iniziale

Quando viene avviato il servizio di provisioning, il primo ciclo consiste nelle operazioni seguenti:

1. Il servizio esegue una query su tutti gli utenti e i gruppi presenti nel sistema di origine e recupera tutti gli attributi definiti nei [mapping degli attributi](customize-application-attributes.md).

2. Il servizio filtra gli utenti e i gruppi restituiti, usando [assegnazioni](../manage-apps/assign-user-or-group-access-portal.md) configurate o [filtri di ambito basati su attributi](define-conditional-rules-for-provisioning-user-accounts.md).

3. Quando un utente viene assegnato o incluso nell'ambito per il provisioning, il servizio esegue una query sul sistema di destinazione per individuare un utente corrispondente usando gli [attributi di corrispondenza](customize-application-attributes.md#understanding-attribute-mapping-properties) specificati. Esempio: Se il nome userPrincipal nel sistema di origine è un attributo di corrispondenza ed è mappato a userName nel sistema di destinazione, il servizio di provisioning esegue una query sul sistema di destinazione per trovare i valori di userName che corrispondono ai valori del nome userPrincipal nel sistema di origine.

4. Se nel sistema di destinazione non viene trovato un utente corrispondente, questo viene creato usando gli attributi restituiti dal sistema di origine. Dopo aver creato l'account utente, il servizio di provisioning rileva e memorizza nella cache l'ID del sistema di destinazione per il nuovo utente. Questo ID viene usato per eseguire tutte le operazioni future su tale utente.

5. Se invece viene trovato un utente corrispondente, questo viene aggiornato usando gli attributi forniti dal sistema di origine. Dopo aver abbinato l'account utente, il servizio di provisioning rileva e memorizza nella cache l'ID del sistema di destinazione per il nuovo utente. Questo ID viene usato per eseguire tutte le operazioni future su tale utente.

6. Se i mapping degli attributi contengono attributi "di riferimento", il servizio esegue altri aggiornamenti nel sistema di destinazione per creare e collegare gli oggetti a cui viene fatto riferimento. È ad esempio possibile che nel sistema di destinazione un utente abbia un attributo "Manager" collegato a un altro utente creato nel sistema di destinazione.

7. Al termine del ciclo iniziale, il servizio salva in modo permanente una filigrana che fornisce il punto di partenza per i successivi cicli incrementali.

Alcune applicazioni come ServiceNow, G Suite e Box supportano non solo il provisioning degli utenti ma anche quello dei gruppi e dei relativi membri. In questi casi, se nei [mapping](customize-application-attributes.md) è abilitato il provisioning di gruppi, il servizio di provisioning sincronizza sia gli utenti che i gruppi e successivamente anche le appartenenze ai gruppi.

### <a name="incremental-cycles"></a>Cicli incrementali

Dopo il ciclo iniziale, tutti gli altri cicli consisteranno in quanto segue:

1. Il servizio esegue una query sul sistema di origine per trovare gli utenti e i gruppi che sono stati aggiornati dopo il salvataggio dell'ultima filigrana.

2. Il servizio filtra gli utenti e i gruppi restituiti, usando [assegnazioni](../manage-apps/assign-user-or-group-access-portal.md) configurate o [filtri di ambito basati su attributi](define-conditional-rules-for-provisioning-user-accounts.md).

3. Quando un utente viene assegnato o incluso nell'ambito per il provisioning, il servizio esegue una query sul sistema di destinazione per individuare un utente corrispondente usando gli [attributi di corrispondenza](customize-application-attributes.md#understanding-attribute-mapping-properties) specificati.

4. Se nel sistema di destinazione non viene trovato un utente corrispondente, questo viene creato usando gli attributi restituiti dal sistema di origine. Dopo aver creato l'account utente, il servizio di provisioning rileva e memorizza nella cache l'ID del sistema di destinazione per il nuovo utente. Questo ID viene usato per eseguire tutte le operazioni future su tale utente.

5. Se invece viene trovato un utente corrispondente, questo viene aggiornato usando gli attributi forniti dal sistema di origine. Se si tratta di un account appena assegnato e abbinato, il servizio di provisioning rileva e memorizza nella cache l'ID del sistema di destinazione per il nuovo utente. Questo ID viene usato per eseguire tutte le operazioni future su tale utente.

6. Se i mapping degli attributi contengono attributi "di riferimento", il servizio esegue altri aggiornamenti nel sistema di destinazione per creare e collegare gli oggetti a cui viene fatto riferimento. È ad esempio possibile che nel sistema di destinazione un utente abbia un attributo "Manager" collegato a un altro utente creato nel sistema di destinazione.

7. Se un utente precedentemente incluso nell'ambito per il provisioning viene rimosso dall'ambito e la relativa assegnazione viene annullata, il servizio disabilita l'utente nel sistema di destinazione eseguendo un aggiornamento.

8. Se un utente precedentemente incluso nell'ambito per il provisioning viene disabilitato o eliminato temporaneamente nel sistema di origine, il servizio disabilita l'utente nel sistema di destinazione eseguendo un aggiornamento.

9. Se un utente precedentemente incluso nell'ambito per il provisioning viene eliminato definitivamente nel sistema di origine, il servizio elimina l'utente nel sistema di destinazione. In Azure AD gli utenti vengono eliminati definitivamente dopo che sono trascorsi 30 giorni dall'eliminazione temporanea.

10. Al termine del ciclo incrementale, il servizio salva in modo permanente una nuova filigrana che fornisce il punto di partenza per i successivi cicli incrementali.

> [!NOTE]
> È possibile disabilitare le operazioni di **creazione**, **aggiornamento** o **eliminazione** usando le caselle di controllo **Azioni oggetto di destinazione** nella sezione [Mapping](customize-application-attributes.md). La logica per disabilitare un utente durante un aggiornamento viene controllata anche tramite un mapping di attributi da un campo come "accountEnabled".

Il servizio di provisioning continuerà a eseguire cicli incrementali back-to-back all'infinito, in base agli intervalli definiti nell'[esercitazione specifica di ogni applicazione](../saas-apps/tutorial-list.md). I cicli incrementali continueranno fino al verificarsi di uno degli eventi seguenti:

- Il servizio viene arrestato manualmente tramite il portale di Azure o con il comando appropriato dell'API Microsoft Graph.
- Viene attivato un nuovo ciclo iniziale tramite l'opzione **Cancella lo stato corrente e riavvia la sincronizzazione** nel portale di Azure o con il comando appropriato dell'API Microsoft Graph. Per effetto di questa operazione, le eventuali filigrane salvate vengono cancellate e tutti gli oggetti di origine vengono nuovamente valutati.
- Viene attivato un nuovo ciclo iniziale a causa di una modifica nel mapping degli attributi o nei filtri di ambito. Per effetto di questa operazione, anche le eventuali filigrane salvate vengono cancellate e tutti gli oggetti di origine vengono nuovamente valutati.
- Il processo di provisioning entra in quarantena (vedere sotto) a causa di errori molto frequenti e resta in quarantena per più di quattro settimane. In questo caso, il servizio viene disabilitato automaticamente.

### <a name="errors-and-retries"></a>Errori e ripetizione di tentativi

Se non è possibile aggiungere, aggiornare o eliminare un singolo utente nel sistema di destinazione a causa di un errore in tale sistema, il tentativo di eseguire questa operazione viene ripetuto nel ciclo di sincronizzazione successivo. Se l'errore si verifica nuovamente, la frequenza dei tentativi viene gradualmente ridotta fino a un solo tentativo al giorno. Per risolvere l'errore, gli amministratori devono verificare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) per determinare la causa radice del problema e intraprendere l'azione appropriata. Tra gli errori comuni possono essere inclusi i seguenti:

- Per gli utenti nel sistema di origine non è definito un valore di attributo che è necessario nel sistema di destinazione.
- Per gli utenti nel sistema di origine è definito un valore di attributo per cui è impostato un vincolo univoco nel sistema di destinazione e lo stesso valore è presente in un altro record utente

Questi errori possono essere risolti modificando i valori di attributo per l'utente interessato nel sistema di origine o modificando i mapping degli attributi in modo da non causare conflitti.

### <a name="quarantine"></a>Quarantena

Se la maggior parte o tutte le chiamate al sistema di destinazione non riuscono a causa di un errore (ad esempio nel caso di credenziali di amministratore non valide), il processo di provisioning passa allo stato di "quarantena". Questo stato viene indicato nel [report di riepilogo sul provisioning](../manage-apps/check-status-user-account-provisioning.md) e tramite posta elettronica, se nel portale di Azure sono state configurate le notifiche di posta elettronica.

In stato di quarantena, la frequenza dei cicli incrementali viene gradualmente ridotta fino a diventare giornaliera.

Dopo che tutti gli errori sono stati risolti, il processo di provisioning esce dalla quarantena e viene avviato il ciclo di sincronizzazione successivo. Se lo stato di quarantena dura per più di quattro settimane, il processo di provisioning viene disabilitato. Per altre informazioni sugli stati di quarantena, vedere [qui](../manage-apps/application-provisioning-quarantine-status.md).

### <a name="how-long-provisioning-takes"></a>Tempo richiesto per il provisioning

Le prestazioni variano a seconda che il processo di provisioning esegua un ciclo di provisioning iniziale o incrementale. Per informazioni dettagliate sul tempo necessario per il provisioning e su come monitorare lo stato del servizio di provisioning, vedere [Controllare lo stato del provisioning dell'utente](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md).

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>Come stabilire se il provisioning utenti viene eseguito correttamente

Tutte le operazioni eseguite dal servizio di provisioning utenti vengono registrate nei [Log di provisioning (anteprima)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) di Azure AD. Nei log sono incluse tutte le operazioni di lettura e scrittura eseguite nei sistemi di origine e di destinazione, oltre ai dati degli utenti che sono stati letti o scritti durante ogni operazione. Per informazioni su come leggere i log di provisioning nel portale di Azure, vedere la [guida alla creazione di report sul provisioning](../manage-apps/check-status-user-account-provisioning.md).

## <a name="de-provisioning"></a>Deprovisioning

Il servizio di provisioning di Azure AD mantiene sincronizzati i sistemi di origine e di destinazione tramite il deprovisioning degli account quando gli utenti non devono più avere accesso. 

Il servizio di provisioning di Azure AD eliminerà temporaneamente un utente in un'applicazione quando l'applicazione supporta le eliminazioni automatiche (richiesta di aggiornamento con Active = false) e si verifica uno degli eventi seguenti:

* L'account utente viene eliminato in Azure AD
*   L'utente non è più assegnato all'applicazione
*   L'utente non soddisfa più un filtro di ambito ed esce dall'ambito
    * Per impostazione predefinita, il servizio di provisioning di Azure AD elimina temporaneamente o disabilita gli utenti che non rientrano nell'ambito. Se si desidera eseguire l'override di questo comportamento predefinito, è possibile impostare un flag per [ignorare le eliminazioni out-of-scope](../app-provisioning/skip-out-of-scope-deletions.md).
*   La proprietà AccountEnabled è impostata su False

Se si verifica uno dei quattro eventi precedenti e l'applicazione di destinazione non supporta le eliminazioni software, il servizio di provisioning invierà una richiesta DELETE per eliminare definitivamente l'utente dall'app. 

30 giorni dopo l'eliminazione in Azure AD, l'utente verrà eliminato definitivamente dal tenant. A questo punto, il servizio di provisioning invierà una richiesta DELETE per eliminare definitivamente l'utente nell'applicazione. In qualsiasi momento durante la finestra di 30 giorni, è possibile [eliminare manualmente un utente in modo permanente](../fundamentals/active-directory-users-restore.md), operazione che invia una richiesta di eliminazione all'applicazione.

Se viene visualizzato un attributo IsSoftDeleted nei mapping degli attributi, esso viene usato per determinare lo stato dell'utente e se inviare una richiesta di aggiornamento con active = false per eliminarlo temporaneamente. 

## <a name="next-steps"></a>Passaggi successivi

[Pianificare una distribuzione automatica del provisioning utenti](../app-provisioning/plan-auto-user-provisioning.md)

[Configurare il provisioning per un'app della raccolta](../manage-apps/configure-automatic-user-provisioning-portal.md)

[Creare un endpoint SCIM e configurare il provisioning durante la creazione dell'app](../app-provisioning/use-scim-to-provision-users-and-groups.md)

[Risolvere i problemi di configurazione e provisioning degli utenti in un'applicazione](../manage-apps/application-provisioning-config-problem.md).
