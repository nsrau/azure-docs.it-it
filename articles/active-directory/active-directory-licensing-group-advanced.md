---
title: Scenari aggiuntivi relativi alle licenze basate sui gruppi in Azure Active Directory | Microsoft Docs
description: Altri scenari relativi alle licenze basate sui gruppi in Azure Active Directory
services: active-directory
keywords: Licenze di Azure AD
documentationcenter: 
author: curtand
manager: mtillman
editor: piotrci
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/02/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cf18076c81ecf7471771674fe40d36dba3b9866d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Scenari, limitazioni e problemi noti relativi all'uso dei gruppi per gestire le licenze in Azure Active Directory

Usare le informazioni e gli esempi seguenti per comprendere le licenze basate su gruppi di Azure Active Directory (Azure AD) in modo più dettagliato.

## <a name="usage-location"></a>Località di utilizzo

Alcuni servizi Microsoft non sono disponibili in tutte le posizioni. Per poter assegnare una licenza a un utente, l'amministratore deve prima specificare la proprietà **Località di utilizzo** per l'utente. Nel [portale di Azure](https://portal.azure.com) è possibile specificarla in **Utente** &gt; **Profilo** &gt; **Impostazioni**.

Per l'assegnazione di licenze ai gruppi, eventuali utenti per cui non è specificata una località di utilizzo ereditano la posizione della directory. Se gli utenti si trovano in località diverse, assicurarsi che ciò sia rispecchiato correttamente negli oggetti utente prima di aggiungere utenti ai gruppi con licenze.

> [!NOTE]
> L'assegnazione di licenze ai gruppi non modificherà mai un valore di località di utilizzo esistente per un utente. È consigliabile impostare sempre la località di utilizzo nell'ambito del flusso di creazione utente in Azure AD (ad esempio tramite la configurazione di AAD Connect). Si garantirà così che il risultato dell'assegnazione delle licenze sia sempre corretto e che gli utenti non ricevano i servizi in località che non sono consentite.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Usare licenze basate sui gruppi con i gruppi dinamici

È possibile usare le licenze basate sui gruppi con qualsiasi gruppo di sicurezza, ovvero anche in associazione ai gruppi dinamici di Azure AD. I gruppi dinamici eseguono regole sugli attributi degli oggetti utente per aggiungere e rimuovere automaticamente gli utenti dai gruppi.

È ad esempio possibile creare un gruppo dinamico per alcuni set di prodotti che si desidera assegnare agli utenti. Ogni gruppo viene popolato da una regola che aggiunge gli utenti in base ai relativi attributi e a ogni gruppo vengono assegnate le licenze appropriate. È possibile assegnare l'attributo in locale e sincronizzarlo con Azure AD o gestire l'attributo direttamente nel cloud.

Le licenze vengono assegnate all'utente poco dopo che è stato aggiunto al gruppo. Quando l'attributo viene modificato, l'utente abbandona i gruppi e le licenze vengono rimosse.

### <a name="example"></a>Esempio

Si consideri l'esempio di una soluzione locale di gestione delle identità che stabilisca quali utenti debbano accedere ai servizi Web di Microsoft. La soluzione usa **extensionAttribute1** per archiviare un valore stringa che rappresenta le licenze che l'utente deve avere. Azure AD Connect sincronizza questo valore con Azure AD.

Gli utenti potrebbero avere bisogno di una licenza invece di un'altra o potrebbero avere bisogno di entrambe. Ecco un esempio in cui si distribuiscono le licenze Office 365 Enterprise E5 ed Enterprise Mobility + Security (EMS) agli utenti nei gruppi:

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: servizi di base

![Schermata di Office 365 Enterprise E5 servizi di base](media/active-directory-licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: utenti con licenza

![Schermata di Enterprise Mobility + Security utenti con licenza](media/active-directory-licensing-group-advanced/o365-e5-licensed-users.png)

Per questo esempio modificare un utente e impostare extensionAttribute1 sul valore di `EMS;E5_baseservices;` se si desidera che l'utente disponga di entrambe le licenze. È possibile apportare questa modifica in locale. Dopo la sincronizzazione della modifica con il cloud, l'utente è stato aggiunto automaticamente a entrambi i gruppi e sono state assegnate le licenze.

![Schermata che illustra come impostare extensionAttribute1 dell'utente](media/active-directory-licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> Prestare attenzione quando si modifica la regola di appartenenza di un gruppo esistente. Quando si modifica una regola, l'appartenenza al gruppo verrà valutata nuovamente e gli utenti che non corrispondono più alla nuova regola saranno rimossi (gli utenti che corrispondono alla nuova regola non saranno interessati da questo processo). Per questi utenti verranno rimosse le licenze durante il processo; ciò potrebbe comportare la perdita del servizio o, in alcuni casi, la perdita di dati.

> Se si dispone di un gruppo dinamico di grandi dimensioni da cui si dipende per l'assegnazione delle licenze, è consigliabile convalidare le modifiche principali in un gruppo di test più piccolo prima di applicarle al gruppo principale.

## <a name="multiple-groups-and-multiple-licenses"></a>Più gruppi e più licenze

Un utente può essere membro di più gruppi con licenze. Di seguito sono illustrati alcuni aspetti da considerare:

- Più licenze per lo stesso prodotto possono sovrapporsi e di conseguenza tutti i servizi abilitati vengono applicati all'utente. L'esempio seguente mostra due gruppi di licenze: *E3 - base services* (E3 - Servizi di base) contiene i servizi di base da distribuire nella prima fase a tutti gli utenti. *E3 - extended services* (E3 - Servizi estesi) contiene servizi aggiuntivi (Sway e Planner) da distribuire solo ad alcuni utenti. In questo esempio, l'utente è stato aggiunto a entrambi i gruppi:

  ![Schermata dei servizi abilitati](media/active-directory-licensing-group-advanced/view-enabled-services.png)

  Di conseguenza, per l'utente sono abilitati 7 dei 12 servizi disponibili nel prodotto e viene usata una sola licenza.

- Selezionando la licenza *E3* è possibile visualizzare altri dettagli, comprese le informazioni sui gruppi che hanno abilitato servizi specifici per l'utente.

  ![Schermata dei servizi abilitati per gruppo](media/active-directory-licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexist-with-group-licenses"></a>Le licenze dirette coesistono con le licenze di gruppo

Quando un utente eredita una licenza da un gruppo, non è possibile rimuovere o modificare l'assegnazione della licenza direttamente nelle proprietà dell'utente. Le modifiche devono essere apportate nel gruppo e successivamente propagate a tutti gli utenti.

È tuttavia possibile assegnare la stessa licenza prodotto direttamente all'utente oltre alla licenza ereditata. È possibile abilitare servizi aggiuntivi del prodotto solo per un utente senza influire sugli altri utenti.

Le licenze assegnate direttamente possono essere rimosse senza alterare le licenze ereditate. Si consideri l'utente che eredita una licenza Office 365 Enterprise E3 da un gruppo.

1. L'utente eredita inizialmente la licenza solo dal gruppo *E3 – basic services* (E3 - Servizi di base), che offre i quattro piani di servizio seguenti:

  ![Schermata dei servizi abilitati del gruppo E3](media/active-directory-licensing-group-advanced/e3-group-enabled-services.png)

2. È possibile fare clic su **Assegna** per assegnare direttamente una licenza E3 all'utente. In questo caso, si disabilitano tutti i piani di servizio eccetto Yammer Enterprise:

  ![Schermata di come assegnare una licenza direttamente a un utente](media/active-directory-licensing-group-advanced/assign-license-to-user.png)

3. Di conseguenza, l'utente continua a usare una sola licenza del prodotto E3. Tuttavia, l'assegnazione diretta abilita al servizio Yammer Enterprise solo tale utente. È possibile vedere quali servizi sono abilitati in virtù dell'appartenenza al gruppo rispetto all'assegnazione diretta:

  ![Schermata dell'assegnazione ereditata rispetto all'assegnazione diretta](media/active-directory-licensing-group-advanced/direct-vs-inherited-assignment.png)

4. L'assegnazione diretta consente le operazioni seguenti:

  - È possibile disattivare Yammer Enterprise direttamente sull'oggetto utente. Si noti che il commutatore **On/Off** per questo servizio è abilitato, a differenza degli altri servizi. Poiché il servizio è stato abilitato direttamente per l'utente, può essere modificato.
  - È anche possibile abilitare servizi aggiuntivi nell'ambito della licenza assegnata direttamente.
  - È possibile usare il pulsante **Rimuovi** per rimuovere la licenza diretta dall'utente. L'utente dispone a questo punto solo della licenza di gruppo ereditata e solo i servizi originali rimangono abilitati:

    ![Schermata che mostra come annullare un'assegnazione diretta](media/active-directory-licensing-group-advanced/remove-direct-license.png)

## <a name="managing-new-services-added-to-products"></a>Gestione di nuovi servizi aggiunti ai prodotti
Quando Microsoft aggiunge un nuovo servizio a un prodotto, il servizio viene abilitato per impostazione predefinita in tutti i gruppi a cui è stata assegnata la licenza del prodotto. Gli utenti nel tenant che hanno sottoscritto le notifiche sulle modifiche apportate al prodotto riceveranno in anticipo messaggi di posta elettronica con informazioni sulle nuove aggiunte di servizi.

Come amministratore, è possibile esaminare tutti i gruppi interessati dalla modifica e scegliere come agire, ad esempio disabilitando il nuovo servizio in ogni gruppo. Se ad esempio sono stati creati gruppi interessati solo ai servizi specifici per la distribuzione, è possibile rivedere tali gruppi e assicurarsi di disabilitare i servizi appena aggiunti.

Ecco un esempio di come potrebbe presentarsi questo processo:

1. È stato inizialmente assegnato il prodotto *Office 365 Enterprise E5* a diversi gruppi. Uno di questi gruppi, denominato *O365 E5 - Exchange only* (O365 E5 - solo Exchange), è progettato in modo da consentire ai membri solo l'uso del servizio *Exchange Online (Piano 2)*.

2. Si riceve una notifica da Microsoft in cui si informa che il prodotto E5 sarà esteso con un nuovo servizio denominato *Microsoft Stream*. Quando il servizio diventa disponibile nel proprio tenant, è possibile eseguire le operazioni seguenti:

3. Passare al pannello [**Azure Active Directory > Licenze > Tutti i prodotti**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) e selezionare *Office 365 Enterprise E5*, quindi selezionare **Gruppi con licenza** per visualizzare un elenco di tutti i gruppi con quel prodotto.

4. Fare clic sul gruppo che si desidera esaminare, in questo caso *O365 E5 - Exchange only* (O365 E5 - solo Exchange). Verrà visualizzata la scheda **Licenze**. Se si fa clic sulla licenza E5, verrà visualizzato un pannello con un elenco di tutti i servizi abilitati.
> [!NOTE]
> Il servizio *Microsoft Stream* è stato aggiunto e abilitato automaticamente al gruppo, che già dispone del servizio *Exchange Online*:

  ![Schermata del nuovo servizio aggiunto alla licenza di un gruppo](media/active-directory-licensing-group-advanced/manage-new-services.png)

5. Se si desidera disabilitare il nuovo servizio nel gruppo, fare clic sull'interruttore **On/Off** accanto al servizio e fare clic su **Salva** per confermare la modifica. Azure AD elaborerà a questo punto tutti gli utenti del gruppo per applicare la modifica. Per tutti i nuovi utenti aggiunti al gruppo il servizio *Microsoft Stream* non sarà abilitato.

  > [!NOTE]
  > Gli utenti possono ancora ottenere l'attivazione del servizio tramite l'assegnazione di altre licenze (tramite un altro gruppo di cui sono membri o l'assegnazione diretta di una licenza).

6. Se necessario, seguire la stessa procedura per altri gruppi a cui è assegnato il prodotto.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Usare PowerShell per determinare gli utenti con licenze ereditate e dirette
È possibile usare uno script di PowerShell per verificare se gli utenti hanno una licenza assegnata direttamente o ereditata da un gruppo.

1. Eseguire il cmdlet `connect-msolservice` per l'autenticazione e la connessione al tenant.

2. `Get-MsolAccountSku` può essere usato per individuare tutte le licenze del prodotto con provisioning nel tenant.

  ![Schermata del cmdlet Get-Msolaccountsku](media/active-directory-licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Usare il valore *AccountSkuId* per la licenza a cui si è interessati con [questo script di PowerShell](./active-directory-licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group). Verrà restituito un elenco di utenti che dispongono di questa licenza con le informazioni sulle modalità di assegnazione della licenza.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>Usare i log di controllo per monitorare l'attività delle licenze basate su gruppo

È possibile usare i [log di controllo di Azure AD](./active-directory-reporting-activity-audit-logs.md#audit-logs) per visualizzare tutte le attività correlate alle licenze basate su gruppo, inclusi:
- l'utente che ha modificato le licenze nei gruppi
- quando il sistema ha avviato l'elaborazione di una modifica di licenza di gruppo e quando ha terminato
- quali modifiche di licenza sono state apportate a un utente in seguito a un'assegnazione di licenze di gruppo.

>[!NOTE]
> I log di controllo sono disponibili nella maggior parte dei pannelli nella sezione del portale di Azure Active Directory. A seconda di dove si accede ad essi, i filtri potrebbero essere preapplicati per mostrare solo attività rilevanti per il contesto del pannello. Se non vengono visualizzati i risultati previsti, esaminare [le opzioni di filtro](./active-directory-reporting-activity-audit-logs.md#filtering-audit-logs) o accedere ai log di controllo non filtrati in [**Azure Active Directory > Attività > Log di controllo**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit).

### <a name="find-out-who-modified-a-group-license"></a>Individuare chi ha modificato una licenza di gruppo

1. Impostare il filtro **Attività** su *Set group license* (Imposta licenza di gruppo) e fare clic su **Applica**.
2. I risultati includono tutti i casi di licenze impostate o modificate nei gruppi.
>[!TIP]
> È anche possibile digitare il nome del gruppo nel filtro *Destinazione* per limitare l'ambito dei risultati.

3. Fare clic su un elemento nella visualizzazione elenco per visualizzare i dettagli delle modifiche. In *Proprietà modificate* sono elencati i valori vecchi e nuovi dell'assegnazione delle licenze.

Di seguito è riportato un esempio di modifiche recenti alle licenze di gruppo con i dettagli:

![Schermata delle modifiche alle licenze di gruppo](media/active-directory-licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>Sapere quando è iniziata e terminata l'elaborazione delle modifiche al gruppo

Quando una licenza di un gruppo viene modificata, Azure AD inizia ad applicare le modifiche a tutti gli utenti.

1. Per visualizzare quando i gruppi hanno avviato l'elaborazione, impostare il filtro **Attività** su *Start applying group based license to users* (Inizia ad applicare le licenze basate sui gruppi agli utenti). Si noti che l'attore per l'operazione è *Microsoft Azure AD Group-Based Licensing*, un account di sistema che viene usato per eseguire tutte le modifiche delle licenze di gruppo.
>[!TIP]
> Fare clic su un elemento nell'elenco per visualizzare il campo *Proprietà modificate* che mostra le modifiche di licenza che sono state selezionate per l'elaborazione. Ciò è utile se sono state apportate più modifiche a un gruppo e non si è certi di cosa è stato elaborato.

2. Per visualizzare quando i gruppi hanno terminato l'elaborazione usare il valore di filtro *Finish applying group based license to users* (Termina l'applicazione delle licenze basate sui gruppi agli utenti).
>[!TIP]
> In questo caso, il campo *Proprietà modificate* contiene un riepilogo dei risultati, utile per verificare rapidamente se l'elaborazione ha restituito errori. Output di esempio:
> ```
Modified Properties
...
Name : Result
Old Value : []
New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
> ```

3. Per visualizzare il log completo dell'elaborazione di un gruppo incluse tutte le modifiche agli utenti, impostare i filtri seguenti:
  - **Azione avviata da (attore)**: "Microsoft Azure AD Group-Based Licensing"
  - **Intervallo di date** (facoltativo): intervallo personalizzato per quando si conosce l'inizio e la fine dell'elaborazione di un gruppo specifico

Questo output di esempio mostra l'inizio dell'elaborazione, tutte le modifiche utente risultanti e la fine dell'elaborazione.

![Schermata delle modifiche alle licenze di gruppo](media/active-directory-licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> Facendo clic sugli elementi correlati a *Change user license* (Modifica licenza utente) verranno visualizzati i dettagli delle modifiche di licenza applicate a ciascun utente.

## <a name="deleting-a-group-with-an-assigned-license"></a>Eliminazione di un gruppo con una licenza assegnata

Non è possibile eliminare un gruppo con una licenza attiva assegnata. Un amministratore può eliminare un gruppo senza avere la consapevolezza che questa operazione implica la rimozione delle licenze dagli utenti; per questo motivo viene richiesto di rimuovere le licenze dal gruppo prima di poter eliminare il gruppo.

Quando si tenta di eliminare un gruppo nel portale di Azure, è possibile che venga visualizzata una notifica di errore simile alla seguente: ![schermata con eliminazione del gruppo non riuscita.](media/active-directory-licensing-group-advanced/groupdeletionfailed.png)

Passare alla scheda **Licenze** per il gruppo e verificare se sono presenti licenze assegnate. In caso affermativo, rimuovere le licenze e provare di nuovo a eliminare il gruppo.

È possibile che vengano visualizzati errori simili quando si tenta di eliminare il gruppo tramite PowerShell o l'API Graph. Se si usa un gruppo sincronizzato da locale, Azure AD Connect può segnalare errori se non riesce a eliminare il gruppo in Azure AD. In tutti questi casi verificare se sono presenti licenze assegnate al gruppo e procedere prima alla rimozione.

## <a name="limitations-and-known-issues"></a>Limitazioni e problemi noti

Se si usano le licenze basate sui gruppi, è consigliabile acquisire familiarità con l'elenco di limitazioni e problemi noti che segue.

- La gestione delle licenze basate sui gruppi attualmente non supporta i "gruppi annidati", ovvero i gruppi che contengono altri gruppi. Se si applica una licenza a un gruppo annidato, la licenza è applicabile solo ai membri di primo livello del gruppo.

- La funzionalità può essere usata solo con gruppi di sicurezza. I gruppi di Office non sono attualmente supportati e non sarà possibile usarli nel processo di assegnazione delle licenze.

- Il [portale di amministrazione di Office 365](https://portal.office.com ) non supporta attualmente le licenze basate sui gruppi. Se un utente eredita una licenza da un gruppo, la licenza viene visualizzata nel portale di amministrazione di Office come una normale licenza utente. Se si tenta di modificare la licenza o di rimuoverla, il portale restituisce un messaggio di errore. Le licenze di gruppo ereditate non possono essere modificate direttamente per un utente.

- Quando un utente viene rimosso da un gruppo e perde la licenza, i piani di servizio di tale licenza, ad esempio SharePoint Online, vengono impostati su **Sospeso**. I piani di servizio non sono impostati su uno stato disabilitato finale. Questa precauzione evita la rimozione accidentale di dati dell'utente se un amministratore commette un errore nella gestione delle appartenenze al gruppo.

- L'assegnazione o la modifica delle licenze di un gruppo di grandi dimensioni (ad esempio 100.000 utenti) potrebbe influire sulle prestazioni. In particolare, il volume delle modifiche generato tramite l'automazione di Azure AD potrebbe compromettere le prestazioni della sincronizzazione della directory tra Azure AD e i sistemi locali.

- In alcune situazioni di carico elevato, l'elaborazione delle licenze può essere ritardata e l'elaborazione di modifiche, quali l'aggiunta/rimozione di una licenza di gruppo o l'aggiunta/rimozione di utenti dal gruppo, potrebbe richiedere molto tempo. Se l'elaborazione delle modifiche impiega più di 24 ore, [aprire un ticket di supporto](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest) per consentire a Microsoft di analizzare il problema. Le prestazioni di questa funzionalità verranno migliorate prima che raggiunga la *disponibilità generale*.

- L'automazione della gestione delle licenze non reagisce automaticamente a tutti i tipi di modifiche nell'ambiente. Ad esempio, se le licenze sono esaurite, alcuni utenti potrebbero riscontrare uno stato di errore. Per liberare il numero di postazioni disponibili è possibile rimuovere alcune licenze assegnate direttamente ad altri utenti. Il sistema tuttavia non reagisce automaticamente a questa modifica correggendo lo stato di errore degli utenti.

  Come soluzione alternativa a questi tipi di limitazioni è possibile passare al pannello **Gruppo** in Azure AD e fare clic sul pulsante **Rielabora**. Questo comando elabora tutti gli utenti nel gruppo e risolve gli stati di errore, se possibile.

- Le licenze basate sui gruppi non registrano gli errori quando una licenza non viene assegnata a un utente a causa di una configurazione di indirizzo proxy duplicato di Exchange Online; tali utenti verranno ignorati durante l'assegnazione delle licenze. Per ulteriori informazioni su come identificare e risolvere il problema vedere [questa sezione](./active-directory-licensing-group-problem-resolution-azure-portal.md#license-assignment-fails-silently-for-a-user-due-to-duplicate-proxy-addresses-in-exchange-online).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su altri scenari per la gestione delle licenze tramite i gruppi, vedere:

* [Che cosa sono le licenze basate sui gruppi in Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Assegnazione di licenze a un gruppo in Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Identificazione e risoluzione dei problemi relativi alle licenze per un gruppo in Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Come eseguire la migrazione di singoli utenti con licenza alle licenze basate sui gruppi in Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
