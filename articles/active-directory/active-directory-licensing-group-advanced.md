---

title: Scenari aggiuntivi relativi alle licenze basate sui gruppi in Azure Active Directory | Microsoft Docs
description: Altri scenari relativi alle licenze basate sui gruppi in Azure Active Directory
services: active-directory
keywords: Licenze di Azure AD
documentationcenter: 
author: curtand
manager: femila
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
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 9b5b87574edc828a41b421a64f543fc34742c5a3
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


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

### <a name="modify-a-dynamic-group-membership-rule"></a>Modificare la regola di appartenenza a un gruppo dinamico

Prestare attenzione quando si modifica la regola di appartenenza di un gruppo esistente. Quando viene modificata una regola, tutti gli utenti vengono rimossi dal gruppo. La regola viene valutata e quindi gli utenti vengono aggiunti al gruppo in base alle nuove condizioni.

Se il gruppo ha licenze assegnate, durante la procedura tali licenze vengono rimosse da tutti gli utenti. Le nuove licenze vengono applicate solo dopo aver valutato la nuova regola e aver aggiunto nuovamente gli utenti. Gli utenti potrebbero riscontrare la perdita del servizio o, in alcuni casi, la perdita di dati.

È consigliabile non modificare la regola di appartenenza in un gruppo usato per l'assegnazione delle licenze. Creare invece un nuovo gruppo con la nuova regola di appartenenza e specificare le stesse impostazioni di licenza del gruppo originale. Attendere che tutti i membri vengano aggiunti e che le licenze vengano applicate a tutti gli utenti. Solo allora si deve eliminare il gruppo originale. Questo approccio assicura una transizione sicura e graduale alla nuova regola di appartenenza senza alcuna perdita di accesso o di dati per gli utenti.


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
Sebbene la gestione delle licenze basate sui gruppi sia disponibile nell'anteprima pubblica, non è possibile usare PowerShell per controllare totalmente le assegnazioni di licenze a gruppi. Può essere tuttavia usato per individuare le informazioni di base sullo stato dell'utente e determinare se le licenze sono state ereditate da un gruppo o assegnate direttamente. L'esempio di codice seguente illustra come un amministratore può produrre un report di base sulle assegnazioni delle licenze.

1. Eseguire il cmdlet `connect-msolservice` per l'autenticazione e la connessione al tenant.

2. `Get-MsolAccountSku` può essere usato per individuare tutte le licenze del prodotto con provisioning nel tenant.

  ![Schermata del cmdlet Get-Msolaccountsku](media/active-directory-licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. In questo esempio si vuole determinare per quali utenti la licenza Enterprise Mobility + Security è stata assegnata direttamente, da un gruppo o entrambi. È possibile usare lo script seguente:

  ```
  #Returns TRUE if the user has the license assigned directly
  function UserHasLicenseAssignedDirectly
  {
      Param([Microsoft.Online.Administration.User]$user, [string]$skuId)
      foreach($license in $user.Licenses)
      {
          #we look for the specific license SKU in all licenses assigned to the user
          if ($license.AccountSkuId -ieq $skuId)
          {
              #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
              #This could be a group object or a user object (contrary to what the name suggests)
              #If the collection is empty, this means the license is assigned directly. This is the case for users who have never been licensed via groups in the past
              if ($license.GroupsAssigningLicense.Count -eq 0)
              {
                  return $true
              }
              \#If the collection contains the ID of the user object, this means the license is assigned directly
              #Note: the license may also be assigned through one or more groups in addition to being assigned directly
              foreach ($assignmentSource in $license.GroupsAssigningLicense)
              {
                  if ($assignmentSource -ieq $user.ObjectId)
                  {
                      return $true
                  }
              }
              return $false
          }
      }
      return $false
  }
  #Returns TRUE if the user is inheriting the license from a group
  function UserHasLicenseAssignedFromGroup
  {
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)
     foreach($license in $user.Licenses)
     {
        #we look for the specific license SKU in all licenses assigned to the user
        if ($license.AccountSkuId -ieq $skuId)
        {
          #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
          #This could be a group object or a user object (contrary to what the name suggests)
            foreach ($assignmentSource in $license.GroupsAssigningLicense)
          {
                  #If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
                  #Note: the license may also be assigned directly in addition to being inherited
                  if ($assignmentSource -ine $user.ObjectId)

            {
                      return $true
            }
          }
              return $false
        }
      }
      return $false
  }
  ```

4. Il resto dello script ottiene tutti gli utenti ed esegue queste funzioni su ogni utente. Quindi formatta l'output in una tabella.

  ```
  #the license SKU we are interested in
  $skuId = "reseller-account:EMS"
  #find all users that have the SKU license assigned
  Get-MsolUser -All | where {$_.isLicensed -eq $true -and $_.Licenses.AccountSKUID -eq $skuId} | select `
      ObjectId, `
      @{Name="SkuId";Expression={$skuId}}, `
      @{Name="AssignedDirectly";Expression={(UserHasLicenseAssignedDirectly $_ $skuId)}}, `
      @{Name="AssignedFromGroup";Expression={(UserHasLicenseAssignedFromGroup $_ $skuId)}}
  ```

5. L'output dello script completo è simile all'esempio seguente:

  ![Schermata dell'output dello script di PowerShell](media/active-directory-licensing-group-advanced/powershell-script-output.png)

## <a name="limitations-and-known-issues"></a>Limitazioni e problemi noti

Se si usano le licenze basate sui gruppi, è consigliabile acquisire familiarità con l'elenco di limitazioni e problemi noti che segue.

- La gestione delle licenze basate sui gruppi attualmente non supporta i "gruppi annidati", ovvero i gruppi che contengono altri gruppi. Se si applica una licenza a un gruppo annidato, la licenza è applicabile solo ai membri di primo livello del gruppo.

- La funzionalità può essere usata solo con gruppi di sicurezza. I gruppi di Office non sono attualmente supportati e non sarà possibile usarli nel processo di assegnazione delle licenze.

- Il [portale di amministrazione di Office 365](https://portal.office.com ) non supporta attualmente le licenze basate sui gruppi. Se un utente eredita una licenza da un gruppo, la licenza viene visualizzata nel portale di amministrazione di Office come una normale licenza utente. Se si tenta di modificare la licenza o di rimuoverla, il portale restituisce un messaggio di errore. Le licenze di gruppo ereditate non possono essere modificate direttamente per un utente.

- Quando un utente viene rimosso da un gruppo e perde la licenza, i piani di servizio di tale licenza, ad esempio SharePoint Online, vengono impostati su **Sospeso**. I piani di servizio non sono impostati su uno stato disabilitato finale. Questa precauzione evita la rimozione accidentale di dati dell'utente se un amministratore commette un errore nella gestione delle appartenenze al gruppo.

- L'assegnazione o la modifica delle licenze di un gruppo di grandi dimensioni (ad esempio 100.000 utenti) potrebbe influire sulle prestazioni. In particolare, il volume delle modifiche generato tramite l'automazione di Azure AD potrebbe compromettere le prestazioni della sincronizzazione della directory tra Azure AD e i sistemi locali.

- L'automazione della gestione delle licenze non reagisce automaticamente a tutti i tipi di modifiche nell'ambiente. Ad esempio, se le licenze sono esaurite, alcuni utenti potrebbero riscontrare uno stato di errore. Per liberare il numero di postazioni disponibili è possibile rimuovere alcune licenze assegnate direttamente ad altri utenti. Il sistema tuttavia non reagisce automaticamente a questa modifica correggendo lo stato di errore degli utenti.

  Come soluzione alternativa a questi tipi di limitazioni è possibile passare al pannello **Gruppo** in Azure AD e fare clic sul pulsante **Rielabora**. Questo comando elabora tutti gli utenti nel gruppo e risolve gli stati di errore, se possibile.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su altri scenari per la gestione delle licenze tramite i gruppi, vedere:

* [Che cosa sono le licenze basate sui gruppi in Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Assegnazione di licenze a un gruppo in Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Identificazione e risoluzione dei problemi relativi alle licenze per un gruppo in Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Come eseguire la migrazione di singoli utenti con licenza alle licenze basate sui gruppi in Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)

