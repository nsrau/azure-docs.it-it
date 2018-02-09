---
title: 'Servizio di sincronizzazione Azure AD Connect: apportare modifiche alla configurazione nel servizio di sincronizzazione Azure AD Connect | Documentazione Microsoft'
description: Fornisce informazioni dettagliate su come apportare modifiche alla configurazione nel servizio di sincronizzazione Azure AD Connect.
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2018
ms.author: billmath
ms.openlocfilehash: e97d3e3e35ee87864c5d38e75e08e62088e25fdb
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Servizio di sincronizzazione Azure AD Connect: apportare modifiche alla configurazione predefinita
Questo articolo illustra in dettaglio come apportare modifiche alla configurazione predefinita nel servizio di sincronizzazione Azure Active Directory (Azure AD) Connect. Include i passaggi per alcuni scenari comuni. Con queste informazioni si potranno apportare semplici modifiche alla propria configurazione in base alle regole di business.

## <a name="synchronization-rules-editor"></a>Editor regole di sincronizzazione
L'editor delle regole di sincronizzazione viene usato per visualizzare e modificare la configurazione predefinita. È disponibile nel menu **Start** nel gruppo **Azure AD Connect**.  
![Menu Start con l'editor delle regole di sincronizzazione](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

Quando si apre l'editor, vengono visualizzate le regole predefinite.

![Editor delle regole di sincronizzazione](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Spostamento nell'editor
Gli elenchi a discesa nella parte superiore dell'editor consentono di trovare rapidamente una regola specifica. Ad esempio, se si vogliono visualizzare le regole in cui è incluso l'attributo proxyAddresses, è possibile modificare gli elenchi a discesa come indicato di seguito:  
![Filtro nell'editor delle regole di sincronizzazione](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png)  
Per reimpostare il filtro e caricare una nuova configurazione, premere F5 sulla tastiera.

Il pulsante **Aggiungi nuova regola** è disponibile in alto a destra. Usare questo pulsante per creare la regola personalizzata.

Nella parte inferiore sono presenti i pulsanti per eseguire operazioni su una regola di sincronizzazione selezionata. I pulsanti **Edit** (Modifica) e **Delete** (Elimina) eseguono le operazioni previste. **Export** (Esporta) genera uno script di PowerShell per ricreare la regola di sincronizzazione. Questa procedura consente di spostare una regola di sincronizzazione da un server a un altro.

## <a name="create-your-first-custom-rule"></a>Creare la prima regola personalizzata
Le modifiche più comuni riguardano i flussi degli attributi. I dati nella directory di origine potrebbero non corrispondere a quelli in Azure AD. Nell'esempio in questa sezione assicurarsi che il nome di un utente sia sempre nel *formato corretto*.

### <a name="disable-the-scheduler"></a>Disabilitare l'utilità di pianificazione
Per impostazione predefinita, l' [utilità di pianificazione](active-directory-aadconnectsync-feature-scheduler.md) viene eseguita ogni 30 minuti. Accertarsi che non venga avviata mentre si apportano modifiche e si risolvono i problemi delle nuove regole. Per disabilitare temporaneamente l'utilità di pianificazione, avviare PowerShell ed eseguire `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Disabilitare l'utilità di pianificazione](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Creare la regola
1. Fare clic su **Add new rule**(Aggiungi nuova regola).
2. Nella pagina **Description** (Descrizione) immettere le informazioni seguenti:  
   ![Filtro delle regole in ingresso](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)  
   * **Name**: (Nome) assegnare alla regola un nome descrittivo.
   * **Description**: (Descrizione) visualizza alcuni chiarimenti che permettono a un altro utente di comprendere la funzione della regola.
   * **Connected System**: (Sistema connesso) sistema in cui è possibile trovare l'oggetto. In questo caso è selezionato **Active Directory Connector**.
   * **Connected System/Metaverse Object Type**: (Tipo di oggetto sistema connesso/Tipo di oggetto metaverse) selezionare rispettivamente **User** (Utente) e **Person** (Persona).
   * **Link Type**: (Tipo di collegamento) modificare questo valore in **Join** (Unisci).
   * **Precedence**: (Precedenza) fornire un valore univoco nel sistema. Un valore numerico inferiore indica una precedenza maggiore.
   * **Tag**: lasciare vuoto questo campo. È necessario popolare questa casella con un valore solo per le regole predefinite di Microsoft.
3. Nella pagina **Scoping filter** (Filtro di ambito) immettere **givenName ISNOTNULL**.  
   ![Filtro dell'ambito per le regole in ingresso](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png)  
   Questa sezione viene usata per definire gli oggetti a cui dovrà essere applicata la regola. Se lasciata vuota, la regola verrà applicata a tutti gli oggetti utente, ma includerà sale riunioni, account del servizio e altri oggetti utente non di persone.
4. Nella pagina **Join rules** (Regole di unione) lasciare vuoto il campo.
5. Nella pagina **Transformations** (Trasformazioni) modificare **FlowType** in **Expression** (Espressione). Per **Target Attribute** (Attributo di destinazione) selezionare **givenName**. Per **Source** (Origine) immettere **PCase([givenName])**.
   ![Trasformazioni di regole in ingresso](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png)  
   Il motore di sincronizzazione fa distinzione tra maiuscole e minuscole sia nel nome della funzione che nel nome dell'attributo. Nel caso di un errore di digitazione, viene visualizzato un avviso quando si aggiunge la regola. È possibile salvare e continuare, ma è necessario riaprire e correggere la regola.
6. Fare clic su **Add** (Aggiungi) per salvare la regola.

La nuova regola personalizzata sarà visibile insieme alle altre regole di sincronizzazione nel sistema.

### <a name="verify-the-change"></a>Verificare la modifica
Con questa nuova modifica si vuole verificare che funzioni come previsto e non generi errori. A seconda del numero di oggetti disponibili, esistono due modi per eseguire questo passaggio:

- Eseguire una sincronizzazione completa in tutti gli oggetti.
- Eseguire un'anteprima e una sincronizzazione completa in un singolo oggetto.

Aprire **Synchronization Service** (Servizio di sincronizzazione) dal menu **Start**. I passaggi in questa sezione fanno tutti parte di questo strumento.

**Sincronizzazione completa in tutti gli oggetti**  

   1. Selezionare **Connectors** (Connettori) nella parte superiore. Identificare il connettore modificato nella sezione precedente, in questo caso Active Directory Domain Services, e selezionarlo. 
   2. In **Actions** (Azioni) selezionare **Run** (Esegui).
   3. Selezionare **Full Synchronization** (Sincronizzazione completa) e quindi **OK**.
   ![Sincronizzazione completa](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)  
   Gli oggetti vengono aggiornati nel metaverse. Verificare le modifiche esaminando l'oggetto nel metaverse.

**Anteprima e sincronizzazione completa in un singolo oggetto**  

   1. Selezionare **Connectors** (Connettori) nella parte superiore. Identificare il connettore modificato nella sezione precedente, in questo caso Active Directory Domain Services, e selezionarlo.
   2. Selezionare **Search Connector Space**(Cerca spazio connettore). 
   3. Usare l'**ambito** per trovare un oggetto che si vuole usare per testare la modifica. Selezionare l'oggetto e fare clic su **Preview**(Anteprima). 
   4. Nella nuova schermata selezionare **Commit Preview**(Anteprima commit).  
   ![Commit preview](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png)  
   Viene eseguito il commit della modifica nel metaverse.

**Visualizzare l'oggetto nel metaverse**  

1. Selezionare alcuni oggetti di esempio per assicurarsi che il valore sia previsto e la regola applicata. 
2. Selezionare **Metaverse Search** (Ricerca metaverse) nella parte superiore. Aggiungere il filtro necessario per trovare gli oggetti pertinenti. 
3. Dai risultati della ricerca aprire un oggetto. Esaminare i valori dell'attributo e verificare anche che nella colonna **Sync Rules** (Regole di sincronizzazione) la regola sia applicata come previsto.  
![Ricerca metaverse](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Abilitare l'utilità di pianificazione
Se è tutto come previsto, è possibile abilitare di nuovo l'utilità di pianificazione. In PowerShell eseguire `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Altre modifiche comuni del flusso dell'attributo
La sezione precedente descrive come apportare modifiche a un flusso dell'attributo. In questa sezione vengono forniti alcuni esempi aggiuntivi. La procedura per creare la regola di sincronizzazione è abbreviata, ma è possibile trovare quella completa nella sezione precedente.

### <a name="use-an-attribute-other-than-the-default"></a>Usare un attributo diverso da quello predefinito
In questo scenario relativo a Fabrikam è disponibile una foresta in cui viene usato l'alfabeto locale per nome, cognome e nome visualizzato. La rappresentazione in caratteri latini di questi attributi è disponibile negli attributi dell'estensione. Per la compilazione dell'elenco indirizzi globale in Azure AD e Office 365, l'organizzazione vuole invece usare questi attributi.

Con una configurazione predefinita, un oggetto della foresta locale avrà un aspetto simile al seguente:   
![Flusso dell'attributo 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

Per creare una regola con altri flussi di attributi, eseguire queste operazioni:

1. Aprire l'**editor delle regole di sincronizzazione** dal menu **Start**.
2. Con la voce **Inbound** (In ingresso) ancora selezionata a sinistra fare clic sul pulsante **Add new rule** (Aggiungi nuova regola).
3. Assegnare alla regola un nome e una descrizione. Selezionare l'istanza della directory Active Directory locale e i tipi di oggetto pertinenti. In **Link Type** (Tipo di collegamento) selezionare **Join** (Unisci). Nel campo **Precedence** (Precedenza) scegliere un numero non usato da un'altra regola. Le regole predefinite iniziano con 100, quindi in questo esempio si può usare il valore 50.
  ![Flusso dell'attributo 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
4. Lasciare vuoto il campo **Scoping filter** (Filtro di ambito). L'ambito deve essere applicato a tutti gli oggetti utente della foresta.
5. Lasciare vuoto il campo **Join rules** (Regole di unione). In questo modo alla regola predefinita viene consentito di gestire tutte le unioni.
6. In **Transformations** (Trasformazioni) creare i flussi seguenti:  
  ![Flusso dell'attributo 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
7. Fare clic su **Add** (Aggiungi) per salvare la regola.
8. Passare a **Synchronization Service Manager**. In **Connectors**(Connettori) selezionare il connettore in cui è stata aggiunta la regola. Selezionare **Run** (Esegui) e quindi **Full Synchronization** (Sincronizzazione completa). Una sincronizzazione completa ricalcola tutti gli oggetti usando le regole correnti.

Di seguito è riportato il risultato per lo stesso oggetto con questa regola personalizzata:   
![Flusso dell'attributo 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Lunghezza degli attributi
Gli attributi di stringa sono indicizzabili per impostazione predefinita e la lunghezza massima è di 448 caratteri. Se si usano attributi di stringa che potrebbero contenere più caratteri, assicurarsi di includere il codice seguente nel flusso di attributi:  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>Modifica di userPrincipalSuffix
L'attributo userPrincipalName in Active Directory non è sempre noto agli utenti e potrebbe non essere adatto come ID di accesso. L'installazione guidata del servizio di sincronizzazione Azure AD Connect consente di scegliere un attributo diverso, ad esempio *mail*. In alcuni casi è però necessario calcolare l'attributo.

Ad esempio, Contoso ha due directory di Azure AD, una per la produzione e una per i test. L'azienda vuole che gli utenti del tenant di test usino un altro suffisso nell'ID di accesso:  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

In questa espressione selezionare tutti gli elementi che si trovano a sinistra del primo carattere @-sign (Word) e concatenarli con una stringa fissa.

### <a name="convert-a-multi-value-attribute-to-single-value"></a>Convertire un attributo multivalore in un valore singolo
Alcuni attributi in Active Directory sono di tipo multivalore nello schema, anche se compaiono come valore singolo in Utenti e computer di Active Directory, ad esempio l'attributo description:  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

Se in questa espressione l'attributo ha un valore, selezionare il primo elemento (*Item*) dell'attributo, rimuovere gli spazi iniziali e finali (*Trim*) e mantenere i primi 448 caratteri (*Left*) della stringa.

### <a name="do-not-flow-an-attribute"></a>Non trasmettere un attributo
Per informazioni generali sullo scenario per questa sezione, vedere [Controllare il processo del flusso dell'attributo](active-directory-aadconnectsync-understanding-declarative-provisioning.md#control-the-attribute-flow-process).

Sono disponibili due modalità per non trasmettere un attributo. Il primo prevede l'uso dell'installazione guidata per [rimuovere gli attributi selezionati](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Questa opzione funziona se l'attributo non è mai stato sincronizzato in precedenza. Tuttavia, se si è iniziato a sincronizzare questo attributo e lo si rimuove in seguito con questa funzionalità, il motore di sincronizzazione interrompe la gestione dell'attributo e i valori esistenti rimangono in Azure AD.

Se si vuole rimuovere il valore di un attributo e assicurarsi che non venga trasmesso in futuro, creare una regola personalizzata.

In questo scenario relativo a Fabrikam si è notato che alcuni degli attributi sincronizzati nel cloud non avrebbero dovuto esserlo. Si vuole accertarsi che questi attributi vengano rimossi da Azure AD.  
![Attributi di estensione non validi](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

1. Creare una nuova regola di sincronizzazione in ingresso e popolare la descrizione.
  ![Descrizioni](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
2. Creare flussi di attributi impostando **Expression** per **FlowType** e **AuthoritativeNull** per **Source**. Il valore letterale **AuthoritativeNull** indica che il valore dovrebbe essere vuoto nel metaverse anche se una regola di sincronizzazione di precedenza inferiore tenta di popolare il valore.
  ![Trasformazione per gli attributi di estensione](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
3. Salvare la regola di sincronizzazione. Avviare il **servizio di sincronizzazione**, trovare il connettore, selezionare **Run** (Esegui) e quindi **Full Synchronization** (Sincronizzazione completa). Questo passaggio consente di ricalcolare tutti i flussi degli attributi.
4. Verificare che le modifiche richieste stiano per essere esportate mediante la ricerca dello spazio connettore.
  ![Eliminazione di gestione temporanea](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Creare regole con PowerShell
L'uso dell'editor delle regole di sincronizzazione è ottimale quando le regole da modificare sono poche. Se è necessario apportare molte modifiche, PowerShell è un'opzione migliore. Alcune funzionalità avanzate sono disponibili solo con PowerShell.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Ottenere lo script di PowerShell per una regola predefinita
Per visualizzare lo script di PowerShell con il quale è stata creata una regola predefinita, selezionare la regola nell'editor delle regole di sincronizzazione e fare clic su **Esporta**. Questa azione fornisce lo script di PowerShell che ha creato la regola.

### <a name="advanced-precedence"></a>Precedenza avanzata
Le regole di sincronizzazione predefinite iniziano con un valore di precedenza pari a 100. Se si dispone di molte forest ed è necessario apportare numerose modifiche personalizzate, 99 regole di sincronizzazione potrebbero non essere sufficienti.

È possibile indicare al motore di sincronizzazione che si vuole inserire le regole aggiuntive prima di quelle predefinite. Per ottenere questo comportamento, seguire questa procedura:

1. Contrassegnare la prima regola di sincronizzazione predefinita (**In from AD-User Join**) nell'editor delle regole di sincronizzazione e selezionare **Esporta**. Copiare il valore dell'identificatore di SR.  
![PowerShell prima della modifica](./media/active-directory-aadconnectsync-change-the-configuration/powershell1.png)  
2. Creare la nuova regola di sincronizzazione. Per creare la regola, è possibile usare l'editor delle regole di sincronizzazione. Esportare la regola in uno script di PowerShell.
3. Nella proprietà **PrecedenceBefore** inserire il valore dell'identificatore della regola predefinita. Impostare la **Precedenza** su **0**. Assicurarsi che l'attributo dell'identificatore sia univoco e non si stia riusando il GUID di un'altra regola. Assicurarsi anche che la proprietà **ImmutableTag** non sia impostata. Questa proprietà deve essere impostata solo per una regola predefinita.
4. Salvare lo script di PowerShell ed eseguirlo. Il risultato è che alla regola personalizzata viene assegnato un valore di precedenza pari a 100 e tutte le altre regole predefinite vengono incrementate.  
![PowerShell dopo la modifica](./media/active-directory-aadconnectsync-change-the-configuration/powershell2.png)  

È possibile configurare molte regole di sincronizzazione personalizzate usando lo stesso valore **PrecedenceBefore** quando necessario.

## <a name="enable-synchronization-of-usertype"></a>Abilitare la sincronizzazione di UserType
Azure AD Connect supporta la sincronizzazione dell'attributo **UserType** per gli oggetti **Utente** nella versione 1.1.524.0 e successive. In particolare, sono state introdotte le seguenti modifiche:

- Lo schema del tipo di oggetto **Utente** in Azure AD Connector è stato esteso per poter includere l'attributo UserType, che è di tipo stringa a valore singolo.
- Lo schema del tipo di oggetto **Persona** nel metaverse è stato esteso per poter includere l'attributo UserType, che è di tipo stringa a valore singolo.

Per impostazione predefinita, l'attributo UserType non è abilitato per la sincronizzazione perché non è presente alcun attributo UserType corrispondente in Active Directory locale. È necessario abilitare la sincronizzazione manualmente. Prima di procedere, è necessario considerare il comportamento seguente applicato da Azure AD:

- Azure AD accetta solo due valori per l'attributo UserType: **Membro** e **Guest**.
- Se l'attributo UserType non è abilitato per la sincronizzazione in Azure AD Connect, gli utenti di Azure AD creati attraverso la sincronizzazione della directory avranno l'attributo UserType impostato su **Membro**.
- Azure AD non consente ad Azure AD Connect di modificare l'attributo UserType per gli utenti esistenti di Azure AD. Questo attributo può essere impostato solo durante la creazione degli utenti di Azure AD.

Per abilitare la sincronizzazione dell'attributo UserType, è prima necessario decidere in che modo tale attributo verrà ricavato da Active Directory locale. Di seguito sono descritti gli approcci più comuni:

- Individuare un attributo di AD locale non usato, ad esempio extensionAttribute1, e usarlo come attributo di origine. L'attributo designato di AD locale deve essere di tipo **stringa** a valore singolo e contenere il valore **Membro** o **Guest**. 

    Se si sceglie questo approccio, prima di abilitare la sincronizzazione dell'attributo UserType è necessario assicurarsi che l'attributo designato sia popolato con il valore corretto per tutti gli oggetti utente esistenti in Active Directory locale che sono sincronizzati con Azure AD.

- In alternativa, è possibile ricavare il valore dell'attributo UserType da altre proprietà. Ad esempio, si vogliono sincronizzare tutti gli utenti come **Guest** se il relativo attributo userPrincipalName di AD locale termina con la parte di dominio *@partners.fabrikam123.org*. 

    Come accennato in precedenza, Azure AD non consente ad Azure AD Connect di modificare l'attributo UserType per gli utenti esistenti di Azure AD. Di conseguenza, è necessario assicurarsi che la logica decisa sia coerente con la configurazione dell'attributo UserType già eseguita per tutti gli utenti di Azure AD esistenti nel tenant.

La procedura per abilitare la sincronizzazione dell'attributo UserType può essere riepilogata come segue:

1.  Disabilitare l'utilità di pianificazione della sincronizzazione e verificare che non sia in corso alcuna operazione di sincronizzazione.
2.  Aggiungere l'attributo di origine allo schema di AD Connector locale.
3.  Aggiungere UserType allo schema di Azure AD Connector.
4.  Creare una regola di sincronizzazione in ingresso per trasmettere il valore dell'attributo da Active Directory locale.
5.  Creare una regola di sincronizzazione in uscita per trasmettere il valore dell'attributo da Azure AD.
6.  Eseguire un ciclo di sincronizzazione completa.
7.  Abilitare l'utilità di pianificazione della sincronizzazione.

>[!NOTE]
> La parte restante di questa sezione illustra la procedura nel contesto di una distribuzione di Azure AD con topologia a foresta singola e senza regole di sincronizzazione personalizzate. Se sono stati configurati una topologia a più foreste e regole di sincronizzazione personalizzate o si dispone di un server di gestione temporanea, è necessario modificare di conseguenza i passaggi.

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Passaggio 1: Disabilitare l'utilità di pianificazione della sincronizzazione e verificare che non sia in corso alcuna operazione di sincronizzazione
Per evitare l'esportazione di modifiche accidentali in Azure AD, verificare che non venga eseguita alcuna sincronizzazione durante l'aggiornamento delle regole di sincronizzazione. Per disabilitare l'utilità di pianificazione della sincronizzazione predefinita:

 1. Avviare una sessione di PowerShell nel server di Azure AD Connect.
 2. Disabilitare la sincronizzazione pianificata eseguendo il cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 3. Aprire Synchronization Service Manager passando a **Start** > **Synchronization Service** (Servizio di sincronizzazione).
 4. Passare alla scheda **Operazioni** e verificare che per nessuna operazione lo stato sia *In corso*.

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>Passaggio 2: Aggiungere l'attributo di origine allo schema di AD Connector locale
Non tutti gli attributi di Azure AD vengono importati in locale nello spazio di AD Connector locale. Per aggiungere l'attributo di origine all'elenco degli attributi importati:

 1. Passare alla scheda **Connettori** in Synchronization Service Manager.
 2. Fare clic con il pulsante destro del mouse su AD Connector locale e selezionare **Proprietà**.
 3. Nella finestra di dialogo popup passare alla scheda **Seleziona attributi**.
 4. Assicurarsi che l'attributo di origine sia selezionato nell'elenco degli attributi.
 5. Fare clic su **OK** per salvare.
![Aggiungere l'attributo di origine allo schema di AD Connector locale](./media/active-directory-aadconnectsync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-to-the-azure-ad-connector-schema"></a>Passaggio 3: Aggiungere UserType allo schema di Azure AD Connector
Per impostazione predefinita, l'attributo UserType non viene importato nello spazio di Azure AD Connect. Per aggiungere l'attributo UserType all'elenco degli attributi importati:

 1. Passare alla scheda **Connettori** in Synchronization Service Manager.
 2. Fare clic con il pulsante destro del mouse su **Azure AD Connector** e selezionare **Proprietà**.
 3. Nella finestra di dialogo popup passare alla scheda **Seleziona attributi**.
 4. Assicurarsi che l'attributo PreferredDataLocation sia selezionato nell'elenco degli attributi.
 5. Fare clic su **OK** per salvare.

![Aggiungere l'attributo di origine allo schema di AD Connector](./media/active-directory-aadconnectsync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Passaggio 4: Creare una regola di sincronizzazione in entrata per trasmettere il valore dell'attributo da Active Directory locale
La regola di sincronizzazione in ingresso consente la trasmissione del valore dell'attributo dall'attributo di origine di Active Directory locale al metaverse:

1. Aprire l'editor delle regole di sincronizzazione passando a **Start** > **Synchronization Rules Editor** (Editor delle regole di sincronizzazione).
2. Impostare il filtro di ricerca **Direzione** su **In arrivo**.
3. Fare clic sul pulsante **Aggiungi nuova regola** per creare una nuova regola in ingresso.
4. Nella scheda **Descrizione** inserire la configurazione seguente:

    | Attributo | Valore | Dettagli |
    | --- | --- | --- |
    | NOME | *Specificare un nome* | Ad esempio, *In from AD - User UserType* |
    | DESCRIZIONE | *Inserire una descrizione* |  |
    | Connected System | *Scegliere il connettore di AD locale* |  |
    | Connected System Object Type | **Utente** |  |
    | Metaverse Object Type | **Person** |  |
    | Tipo di collegamento | **Join** |  |
    | Precedenza | *Scegliere un numero compreso tra 1 e 99* | I numeri compresi tra 1 e 99 sono riservati alle regole di sincronizzazione personalizzate. Non scegliere un valore usato da un'altra regola di sincronizzazione. |

5. Passare alla scheda **Scoping filter** (Filtro di ambito) e aggiungere un **singolo gruppo di filtri di ambito** con la clausola seguente:

    | Attributo | Operatore | Valore |
    | --- | --- | --- |
    | adminDescription | NOTSTARTWITH | Utente\_ |

    Il filtro di ambito determina a quali oggetti di AD locale viene applicata la regola di sincronizzazione in ingresso. Questo esempio è basato sullo stesso filtro di ambito usato come regola di sincronizzazione predefinita *In from AD – User Common*, che impedisce alla regola di sincronizzazione di essere applicata a oggetti Utente creati tramite la funzionalità di writeback dell'utente di Azure AD. Potrebbe essere necessario perfezionare il filtro di ambito in base alla distribuzione di Azure AD Connect.

6. Passare alla scheda **Trasformazione** e implementare la regola di trasformazione desiderata. Ad esempio, se si è designato un attributo di AD locale non in uso, ad esempio extensionAttribute1, come attributo di origine di UserType, è possibile implementare un flusso di attributi diretto:

    | Tipo di flusso | Attributo di destinazione | Sorgente | Applicare una sola volta | Tipi di unione |
    | --- | --- | --- | --- | --- |
    | Diretto | UserType | extensionAttribute1 | Non selezionato | Aggiornamento |

    In un altro esempio si vuole ricavare il valore dell'attributo UserType da altre proprietà. Ad esempio, si vogliono sincronizzare tutti gli utenti come Guest se il relativo attributo userPrincipalName di AD locale termina con la parte di dominio *@partners.fabrikam123.org*. È possibile implementare un'espressione simile alla seguente:

    | Tipo di flusso | Attributo di destinazione | Sorgente | Applicare una sola volta | Tipi di unione |
    | --- | --- | --- | --- | --- |
    | Diretto | UserType | IIF(IsPresent([userPrincipalName]),IIF(CBool(InStr(LCase([userPrincipalName]),"@partners.fabrikam123.org")=0),"Membro","Guest"),Error("UserPrincipalName non è presente per determinare UserType")) | Non selezionato | Aggiornamento |

7. Fare clic su **Aggiungi** per creare la regola in entrata.

![Creare una regola di sincronizzazione in ingresso](./media/active-directory-aadconnectsync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Passaggio 5: Creare una regola di sincronizzazione in uscita per trasmettere il valore dell'attributo da Azure AD
La regola di sincronizzazione in uscita consente la trasmissione del valore dell'attributo dal metaverse all'attributo PreferredDataLocation in Azure AD:

1. Passare all'editor delle regole di sincronizzazione.
2. Impostare il filtro di ricerca **Direzione** da **In uscita**.
3. Fare clic sul pulsante **Aggiungi nuova regola**.
4. Nella scheda **Descrizione** inserire la configurazione seguente:

    | Attributo | Valore | Dettagli |
    | ----- | ------ | --- |
    | NOME | *Specificare un nome* | Ad esempio, *Out to AAD – User UserType* |
    | DESCRIZIONE | *Inserire una descrizione* ||
    | Connected System | *Selezionare il connettore di AAD* ||
    | Connected System Object Type | **Utente** ||
    | Metaverse Object Type | **Person** ||
    | Tipo di collegamento | **Join** ||
    | Precedenza | *Scegliere un numero compreso tra 1 e 99* | I numeri compresi tra 1 e 99 sono riservati alle regole di sincronizzazione personalizzate. Non scegliere un valore usato da un'altra regola di sincronizzazione. |

5. Passare alla scheda **Scoping filter** (Filtro di ambito) e aggiungere un singolo **gruppo di filtri di ambito** con le due clausole:

    | Attributo | Operatore | Valore |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Utente |
    | cloudMastered | NOTEQUAL | True  |

    Il filtro di ambito determina a quali oggetti di Azure AD viene applicata la regola di sincronizzazione in uscita. In questo esempio, viene usato lo stesso filtro di ambito della regola di sincronizzazione predefinita *Out to AD – User Identity*. Impedisce l'applicazione della regola di sincronizzazione agli oggetti Utente non sincronizzati da Active Directory locale. Potrebbe essere necessario perfezionare il filtro di ambito in base alla distribuzione di Azure AD Connect.

6. Andare alla scheda **Trasformazione** e implementare la regola di trasformazione seguente:

    | Tipo di flusso | Attributo di destinazione | Sorgente | Applicare una sola volta | Tipi di unione |
    | --- | --- | --- | --- | --- |
    | Diretto | UserType | UserType | Non selezionato | Aggiornamento |

7. Fare clic su **Aggiungi** per creare la regola in uscita.

![Creare una regola di sincronizzazione in uscita](./media/active-directory-aadconnectsync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>Passaggio 6: Eseguire un ciclo di sincronizzazione completa
In generale, un ciclo di sincronizzazione completo è necessario perché sono stati aggiunti nuovi attributi per gli schemi di Active Directory e Azure AD Connector e sono state introdotte regole di sincronizzazione personalizzate. Si vuole controllare le modifiche prima di esportarle in Azure AD. 

È possibile usare la procedura seguente per controllare le modifiche, eseguendo al contempo manualmente i passaggi che compongono il ciclo di sincronizzazione completo.

1. Eseguire un'**importazione completa** in **AD Connector locale**:

   1. Passare alla scheda **Operazioni** in Synchronization Service Manager.
   2. Fare clic con il pulsante destro del mouse su **AD Connector locale** e selezionare **Esegui**.
   3. Nella finestra di dialogo popup selezionare **Importazione completa** e fare clic su **OK**.
   4. Attendere il completamento dell'operazione.

    > [!NOTE]
    > È possibile ignorare l'importazione completa in AD Connector locale se l'attributo di origine è già incluso nell'elenco degli attributi importati. In altre parole, non è necessario apportare modifiche durante il [passaggio 2: Aggiungere l'attributo di origine allo schema di AD Connector locale](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Eseguire un'**importazione completa** in **Azure AD Connector**:

   1. Fare clic con il pulsante destro del mouse su **Azure AD Connector** e selezionare **Esegui**.
   2. Nella finestra di dialogo popup selezionare **Importazione completa** e fare clic su **OK**.
   3. Attendere il completamento dell'operazione.

3. Controllare le modifiche alle regole di sincronizzazione in un oggetto Utente esistente:

    L'attributo di origine di Active Directory locale e UserType di Azure AD sono stati importati nei rispettivi spazi connettore. Prima di procedere a eseguire una sincronizzazione completa, è consigliabile eseguire un'**anteprima** su un oggetto Utente esistente nello spazio di AD Connector locale. L'attributo di origine dell'oggetto selezionato deve essere popolato.
    
    Un'operazione di **anteprima** riuscita che mostra UserType popolato nel metaverse indica che le regole di sincronizzazione sono state configurate correttamente. Per informazioni sull'esecuzione dell'**anteprima**, fare riferimento alla sezione [Verify the change](#verify-the-change) (Verificare le modifiche).

4. Eseguire una **sincronizzazione completa** in **AD Connector locale**:

   1. Fare clic con il pulsante destro del mouse su **AD Connector locale** e selezionare **Esegui**.
   2. Nella finestra di dialogo popup selezionare **Sincronizzazione completa** e fare clic su **OK**.
   3. Attendere il completamento dell'operazione.

5. Verificare le **esportazioni in sospeso** per Azure AD:

   1. Fare clic con il pulsante destro del mouse su **Azure AD Connector** e selezionare **Search Connector Space** (Cerca spazio connettore).
   2. Nella finestra di dialogo popup **Search Connector Space** (Spazio connettore di ricerca):

      - Impostare **Ambito** su **Pending Export** (Esportazione in sospeso).
      - Selezionare tutte e tre le caselle di controllo: **Add** (Aggiungi), **Modify** (Modifica) e **Delete** (Elimina).
      - Fare clic sul pulsante **Ricerca** per ottenere l'elenco degli oggetti con le modifiche da esportare. Per esaminare le modifiche per un determinato oggetto, fare doppio clic sull'oggetto.
      - Verificare le modifiche previste.

6. Eseguire il comando **Export** (Esporta) in **Azure AD Connector**:

   1. Fare clic con il pulsante destro del mouse su **Azure AD Connector** e selezionare **Esegui**.
   2. Nella finestra popup **Run Connector** (Esegui connettore) selezionare **Export** (Esporta) e fare clic su **OK**.
   3. Attendere il completamento dell'esportazione in Azure AD.

> [!NOTE]
> Questa procedura non include il passaggio di sincronizzazione completa e quello di esportazione in Azure AD Connector. Questi passaggi non sono necessari perché i valori degli attributi si trasmettono solo da Active Directory locale ad Azure AD.

### <a name="step-7-re-enable-the-sync-scheduler"></a>Passaggio 7: Riattivare l'utilità di pianificazione della sincronizzazione
Riabilitare l'utilità di pianificazione della sincronizzazione predefinita:

1. Avviare una sessione di PowerShell.
2. Riabilitare la sincronizzazione pianificata eseguendo il cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $true`.


## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sul modello di configurazione, vedere [Servizio di sincronizzazione Azure AD Connect: Informazioni sul provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* Per altre informazioni sul linguaggio delle espressioni, vedere [Servizio di sincronizzazione Azure AD Connect: Informazioni sulle espressioni di provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

**Argomenti generali**

* [Servizio di sincronizzazione Azure AD Connect: Comprendere e personalizzare la sincronizzazione](active-directory-aadconnectsync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)
