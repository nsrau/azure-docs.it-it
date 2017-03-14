---

title: Scenari aggiuntivi relativi alle licenze basate sui gruppi in Azure Active Directory | Microsoft Docs
description: Altri scenari relativi alle licenze basate sui gruppi in Azure Active Directory
services: active-directory
keywords: Licenze di Azure AD
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/03/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 09f0ed3f7624bb242c40868710fb3eae49cda906
ms.openlocfilehash: a739e2320f8ea42912d169353a956e2b2f551619
ms.lasthandoff: 03/01/2017


---

# <a name="scenarios-limitations-and-known-issues-with-using-groups-to-manage-licensing-in-azure-active-directory"></a>Scenari, limitazioni e problemi noti relativi all'uso dei gruppi per gestire le licenze in Azure Active Directory 

## <a name="group-based-licensing-using-dynamic-groups"></a>Licenze basate sui gruppi tramite gruppi dinamici

È possibile usare le licenze basate sui gruppi con qualsiasi gruppo di sicurezza, ovvero anche in associazione ai gruppi dinamici di Azure AD. I gruppi dinamici eseguono regole sugli attributi degli oggetti utente per aggiungere e rimuovere automaticamente gli utenti dai gruppi.

È ad esempio possibile creare più gruppi dinamici, uno per ogni set di prodotti da assegnare agli utenti. Ogni gruppo contiene una regola che esamina un attributo specifico per un utente, che descrive il set di licenze che l'utente deve ricevere. L'attributo può essere assegnato in locale e sincronizzato con Azure AD oppure può essere gestito direttamente nel cloud.

Quando l'attributo è specificato, l'utente verrà aggiunto a uno o più di questi gruppi di licenze dinamici. Le licenze verranno assegnate all'utente subito dopo. Quando l'attributo viene rimosso, l'utente abbandonerà i gruppi e le licenze verranno rimosse.

### <a name="example"></a>Esempio

In questo esempio è presente una soluzione locale di gestione delle identità che stabilisce quali utenti debbano accedere a Microsoft Online Services specifici. La soluzione usa extensionAttribute1 per archiviare un valore stringa che rappresenta le licenze che l'utente deve avere e Azure AD Connect sincronizza questo valore con Azure AD. Si vogliono distribuire licenze di Office 365 E5 ed EMS agli utenti. In Azure AD sono stati creati due gruppi dinamici, uno per ogni prodotto, perché alcuni utenti potrebbero non aver bisogno di entrambi. Sono state specificate la regola di appartenenza dinamica e le impostazioni di licenza per ogni gruppo, come indicato di seguito:

#### <a name="o365-e5--base-services"></a>O365 E5: servizi base

![Servizi base O365 E5](media/active-directory-licensing-group-advanced/o365-e5-base-services.png)

#### <a name="ems-e5--licensed-users"></a>EMS E5: utenti con licenza

![Utenti con licenza di O365 E5](media/active-directory-licensing-group-advanced/o365-e5-licensed-users.png)

È stato modificato un utente in locale impostando il relativo attributo extensionAttribute1 sul valore di `EMS;E5_baseservices;` perché l'utente deve avere entrambe le licenze. Dopo la sincronizzazione della modifica con il cloud, l'utente è stato aggiunto automaticamente a entrambi i gruppi e quindi sono state assegnate le licenze.

![Impostare l'attributo extensionAttribute1 dell'utente](media/active-directory-licensing-group-advanced/user-set-extensionAttribute1.png)

### <a name="modifying-a-dynamic-group-membership-rule"></a>Modifica della regola di appartenenza a un gruppo dinamico

È necessario prestare attenzione quando si modifica la regola di appartenenza di un gruppo esistente. Quando si modifica una regola, tutti gli utenti vengono rimossi dal gruppo, la regola viene valutata e gli utenti vengono aggiunti al gruppo in base alle nuove condizioni.

Se il gruppo ha licenze assegnate, durante la procedura tali licenze verranno rimosse da tutti gli utenti. Le nuove licenze verranno applicate solo dopo aver valutato la nuova regola e aver aggiunto nuovamente gli utenti. Ciò significa che gli utenti possono riscontrare la perdita di servizio o, in alcuni casi, la perdita di dati.

È consigliabile non modificare la regola di appartenenza in un gruppo usato per l'assegnazione delle licenze. Creare invece un nuovo gruppo con la nuova regola e specificare le stesse impostazioni di licenza del gruppo originale. Attendere che i membri siano stati aggiunti e le licenze applicate a tutti gli utenti. Solo a quel punto sarà possibile proseguire ed eliminare il gruppo originale. Questo approccio assicura una transizione sicura e graduale alla nuova regola di appartenenza senza alcuna perdita di accesso o di dati per gli utenti.


## <a name="multiple-groups-and-multiple-licenses"></a>Più gruppi e più licenze

Un utente può essere membro di più gruppi con licenze. Di seguito sono illustrati alcuni aspetti da considerare:

1. Più licenze per lo stesso prodotto possono sovrapporsi e tutti i servizi abilitati verranno applicati all'utente. Sono stati creati ad esempio due gruppi di licenze: *E3 – base services* contiene i servizi di base da distribuire per primi, a tutti gli utenti; *E3 – extended services* contiene servizi aggiuntivi (Sway e Planner) da provare con alcuni utenti, mentre Yammer è disabilitato per la distribuzione futura. In questo esempio, l'utente è stato aggiunto a entrambi i gruppi:

  ![visualizzare i servizi abilitati](media/active-directory-licensing-group-advanced/view-enabled-services.png)

  Di conseguenza, per gli utenti sono abilitati 7 dei 12 servizi disponibili nel prodotto e viene usata una sola licenza.

2. Selezionando la licenza *E3* è possibile visualizzare altri dettagli, comprese le informazioni sui gruppi che hanno abilitato servizi specifici per l'utente.

  ![visualizzare i servizi abilitati per gruppo](media/active-directory-licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexisting-with-group-licenses"></a>Licenze dirette coesistenti con licenze di gruppo

Quando un utente eredita una licenza da un gruppo, non è possibile rimuovere o modificare tale assegnazione direttamente nell'oggetto utente. È invece necessario apportare tutte le modifiche nel gruppo; il sistema provvederà quindi a propagarle a tutti gli utenti.

È tuttavia possibile assegnare la stessa licenza SKU direttamente all'utente oltre alla licenza ereditata. Questa operazione può essere usata ad esempio per abilitare servizi aggiuntivi dello SKU solo per un utente, senza modificare i servizi degli altri.

Le licenze assegnate direttamente possono essere rimosse senza alterare la licenza ereditata. Si consideri l'utente seguente che eredita una licenza *Office 365 Enterprise E3* da un gruppo, con l'abilitazione di una vasta gamma di servizi.

1. L'utente eredita inizialmente la licenza solo dal gruppo *E3 – basic services*. Vengono così abilitati i 4 piani di servizio elencati di seguito:

  ![servizi abilitati per il gruppo E3](media/active-directory-licensing-group-advanced/e3-group-enabled-services.png)

2. Facendo clic sul pulsante **Assegna** è possibile assegnare direttamente una licenza E3 all'utente. In questo caso verranno disabilitati tutti i piani di servizio, ad eccezione di Yammer Enterprise.

  ![assegnare la licenza a un utente](media/active-directory-licensing-group-advanced/assign-license-to-user.png)

3. Di conseguenza, l'utente utilizzerà comunque solo 1 licenza del prodotto E3, tuttavia l'assegnazione diretta abilita il servizio *Enterprise Yammer* solo per tale utente. L'elenco indica i servizi abilitati in virtù dell'appartenenza al gruppo rispetto all'assegnazione diretta:

  ![assegnazione ereditata rispetto all'assegnazione diretta](media/active-directory-licensing-group-advanced/direct-vs-inherited-assignment.png)

4. L'assegnazione diretta consente le operazioni seguenti:

  a. È possibile disattivare *Yammer Enterprise* direttamente sull'oggetto utente. Si noti che l'interruttore On/Off è abilitato a differenza degli interruttori degli altri servizi. Ciò è dovuto al fatto che il servizio è abilitato direttamente sull'utente e può essere quindi modificato.

  b. È anche possibile abilitare servizi aggiuntivi nell'ambito della licenza assegnata direttamente.

  c. È possibile usare il pulsante **Rimuovi** per rimuovere la licenza diretta dall'utente. È possibile vedere che l'utente ha ora solo la licenza di gruppo ereditata e solo i servizi originali rimangano abilitati.

    ![rimuovere l'assegnazione diretta](media/active-directory-licensing-group-advanced/remove-direct-license.png)

## <a name="usage-location"></a>Località di utilizzo

Alcuni servizi Microsoft non sono disponibili in tutte le località; prima di assegnare una licenza a un utente, l'amministratore deve specificare la proprietà "Località di utilizzo" per l'utente. Questa operazione può essere eseguita nella sezione Utente-&gt; Profilo-&gt;Impostazioni del [portale di Azure](https://portal.azure.com).

Quando si usa l'assegnazione di licenze in base al gruppo, eventuali utenti per cui non è specificata una località di utilizzo erediteranno la località della directory. Se gli utenti si trovano in località diverse, assicurarsi che questo sia rispecchiato correttamente negli oggetti utente prima di aggiungere utenti ai gruppi con licenze.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Usare PowerShell per determinare gli utenti con licenze ereditate e dirette

Durante l'anteprima pubblica, PowerShell non può essere usato per controllare completamente le assegnazioni di licenze in base al gruppo. Può essere tuttavia usato per individuare le informazioni di base sullo stato dell'utente e determinare se le licenze sono state ereditate da un gruppo o assegnate direttamente. L'esempio di codice seguente illustra come un amministratore può produrre un report di base sulle assegnazioni delle licenze.

1. Eseguire il cmdlet `connect-msolservice` per l'autenticazione e la connessione al tenant.

2. `Get-MsolAccountSku` può essere usato per individuare tutte le licenze SKU con provisioning nel tenant.

  ![uso del cmdlet Get-Msolaccountsku](media/active-directory-licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. In questo esempio si vuole determinare per quali utenti la licenza *EMS* è stata assegnata direttamente, da un gruppo o entrambi. Si userà uno script di PowerShell che contiene due funzioni che possono rispondere a questa domanda per un oggetto utente e uno SKU
  ```
  # Returns TRUE if the user has the license assigned directly

  function UserHasLicenseAssignedDirectly
  {
      Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

      foreach($license in $user.Licenses)
      {
          # we look for the specific license SKU in all licenses assigned to the user
          if ($license.AccountSkuId -ieq $skuId)
          {
              # GroupsAssigningLicense contains a collection of IDs of objects assigning the license
              # This could be a group object or a user object (contrary to what the name suggests)
              # If the collection is empty, this means the license is assigned directly - this is the case for users who have never been licensed via groups in the past

              if ($license.GroupsAssigningLicense.Count -eq 0)
              {
                  return $true
              }
              # If the collection contains the ID of the user object, this means the license is assigned directly
              # Note: the license may also be assigned through one or more groups in addition to being assigned directly
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
  # Returns TRUE if the user is inheriting the license from a group
  function UserHasLicenseAssignedFromGroup
  {
      Param([Microsoft.Online.Administration.User]$user, [string]$skuId)
      foreach($license in $user.Licenses)
      {
          # we look for the specific license SKU in all licenses assigned to the user
          if ($license.AccountSkuId -ieq $skuId)
          {
              # GroupsAssigningLicense contains a collection of IDs of objects assigning the license
              # This could be a group object or a user object (contrary to what the name suggests)
              foreach ($assignmentSource in $license.GroupsAssigningLicense)
              {
                  # If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
                  # Note: the license may also be assigned directly in addition to being inherited
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
4. Il resto dello script ottiene tutti gli utenti, esegue queste funzioni su ogni utente e quindi mette a disposizione l'output in una tabella.

  ```
  # the license SKU we are interested in
  $skuId = "reseller-account:EMS"
  # find all users that have the SKU license assigned
  Get-MsolUser -All | where {$_.isLicensed -eq $true -and $_.Licenses.AccountSKUID -eq $skuId} | select `
      ObjectId, `
      @{Name="SkuId";Expression={$skuId}}, `
      @{Name="AssignedDirectly";Expression={(UserHasLicenseAssignedDirectly $_ $skuId)}}, `
      @{Name="AssignedFromGroup";Expression={(UserHasLicenseAssignedFromGroup $_ $skuId)}}
  ```

5. L'output dello script completo è simile al seguente:

  ![Output dello script di PowerShell](media/active-directory-licensing-group-advanced/powershell-script-output.png)

## <a name="limitations-and-known-issues"></a>Limitazioni e problemi noti

1. Le licenze basate sui gruppi non supportano attualmente "gruppi annidati", ovvero gruppi che contengono altri gruppi. Se si applica una licenza a un gruppo annidato, la licenza sarà applicabile solo ai membri di primo livello del gruppo.

2. Le licenze basate sui gruppi sono tuttavia attualmente esposte solo tramite il [portale di Azure](https://portal.azure.com). Non è al momento possibile usare PowerShell per impostare o modificare le licenze nei gruppi.

3. Il [portale di amministrazione di Office 365](https://portal.office.com ) non supporta attualmente le licenze basate sui gruppi. Se un utente eredita una licenza da un gruppo, la licenza verrà visualizzata nel portale di amministrazione di Office come una normale licenza utente. Se si prova a modificare la licenza, ad esempio per disabilitare un servizio o provare a rimuoverla, il portale restituirà un messaggio di errore perché le licenze di gruppo ereditate non possono essere modificate direttamente su un utente.

  `One or more of the licenses could not be modified because they are inherited from a group membership. To view or modify group based licenses visit the Azure admin portal.`

4. Quando un utente viene rimosso da un gruppo e perde la licenza, i piani di servizio di tale licenza, ad esempio Exchange Online o SharePoint Online, vengono impostati in uno stato di sospensione invece di uno stato di disabilitazione finale. Questa operazione viene eseguita come precauzione per evitare la rimozione accidentale di dati dell'utente se un amministratore commette un errore nella gestione delle appartenenze al gruppo.

  Verrà prossimamente implementato un flusso di lavoro in cui lo stato di questi piani di servizio sarà infine completamente disabilitato per gli utenti. Fino a quando questa opzione non sarà disponibile, alcuni servizi potranno continuare a funzionare per gli utenti che sono stati rimossi da un gruppo e non hanno più una licenza.

5. Quando le licenze vengono assegnate o modificate in un gruppo di utenti estremamente vasto, ad esempio 100.000 utenti, l'elevato numero di modifiche generate dall'automazione di Azure AD può influire negativamente sulle prestazioni della sincronizzazione delle directory tra Azure AD e i sistemi locali. Possono quindi verificarsi ritardi di sincronizzazione delle directory nell'ambiente in uso.

6. L'automazione della gestione delle licenze non reagisce automaticamente a tutti i tipi di modifiche nell'ambiente. È ad esempio possibile che le licenze si esauriscano e si verifichi lo stato di errore "Le licenze non sono sufficienti" per alcuni utenti. Sarà quindi possibile rimuovere alcune licenze assegnate direttamente ad altri utenti per liberare il numero di postazioni disponibili. Il sistema tuttavia non reagirà automaticamente a questa modifica correggendo lo stato di errore degli utenti.

  Come soluzione alternativa a questi tipi di limitazioni è possibile passare al pannello del gruppo in Azure AD e fare clic sul pulsante **Rielabora**. Verranno così elaborati tutti gli utenti nel gruppo per risolvere gli stati di errore, se possibile.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su altri scenari per la gestione delle licenze tramite i gruppi, vedere:

* [Che cosa sono le licenze basate sui gruppi in Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Assegnazione di licenze a un gruppo in Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Identificazione e risoluzione dei problemi relativi alle licenze per un gruppo in Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Come eseguire la migrazione di singoli utenti con licenza alle licenze basate sui gruppi in Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)

