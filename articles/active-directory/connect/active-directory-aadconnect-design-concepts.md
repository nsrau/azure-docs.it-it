---
title: Concetti relativi alla progettazione per Azure AD Connect | Documentazione Microsoft
description: Questo argomento illustra alcune aree di progettazione dell'implementazione.
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 53a0f766de9db7e6ee48b6659aad378620c0d727
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: Concetti relativi alla progettazione
L'obiettivo di questo argomento consiste nell'illustrare le aree da esaminare durante la progettazione dell'implementazione di Azure AD Connect. Si tratta di un'analisi approfondita di determinate aree e questi concetti vengono illustrati brevemente anche in altri argomenti.

## <a name="sourceanchor"></a>sourceAnchor
L'attributo sourceAnchor viene definito come *un attributo immutabile durante il ciclo di vita di un oggetto*. Identifica in modo univoco un oggetto come corrispondente all'oggetto locale in Azure AD. L'attributo viene definito anche **immutableId** e i due nomi sono usati in modo intercambiabile.

Il termine immutabile, ovvero "non modificabile", è importante in questo argomento. Poiché il valore di questo attributo non può essere modificato dopo la configurazione, è essenziale scegliere una progettazione in grado di supportare lo scenario specifico.

L'attributo viene usato per gli scenari seguenti:

* Quando viene compilato un nuovo motore di sincronizzazione o quando il motore viene ricompilato dopo uno scenario di ripristino di emergenza, questo attributo collega gli oggetti esistenti in Azure AD con gli oggetti locali.
* Se si passa da un modello di identità solo cloud a un modello di identità sincronizzato, questo attributo consente la "corrispondenza rigida" degli oggetti esistenti in Azure AD con gli oggetti locali.
* Se si usa la federazione, questo attributo viene usato insieme a **userPrincipalName** nell'attestazione per identificare in modo univoco un utente.

Questo argomento esamina sourceAnchor solo relativamente agli utenti. Le stesse regole sono applicabili a tutti i tipi di oggetto, ma in genere presentano problemi solo per gli utenti.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Selezione di un attributo sourceAnchor valido
Il valore dell'attributo deve rispettare le regole seguenti:

* La lunghezza del testo deve essere inferiore a 60 caratteri
  * I caratteri diversi da a-z, A-Z o 0-9 vengono codificati e conteggiati come 3 caratteri
* Non deve contenere un carattere speciale: &#92; ! # $ % & * + / = ? ^ &#96; { } | ~ < > ( ) ' ; : , [ ] " @ _
* Deve essere globalmente univoco
* Deve essere una stringa, un valore intero o un numero binario
* Non deve essere basato sul nome dell'utente, queste modifiche
* Non devono fare distinzione tra maiuscole e minuscole e devono evitare valori che possono variare in base alle maiuscole/minuscole
* Deve essere assegnato quando viene creato l'oggetto

Se l'attributo sourceAnchor selezionato non è di tipo stringa, Azure AD Connect usa Base64Encode per il valore dell'attributo per assicurare che non vengano visualizzati caratteri speciali. Se si usa un altro server federativo, assicurarsi che il server sia in grado di applicare Base64Encode all'attributo.

L'attributo sourceAnchor rispetta la distinzione tra maiuscole e minuscole. Un valore quale "DavideMilano" è diverso da "davidemilano". Non è tuttavia consigliabile creare due oggetti che si differenziano solo per la distinzione tra maiuscole e minuscole.

Se è presente una singola foresta locale, l'attributo da usare è **objectGUID**. Questo attributo deve essere usato anche con le impostazioni rapide in Azure AD Connect e viene usato anche da DirSync.

Se sono presenti più foreste e gli utenti non vengono spostati tra foreste e domini, è possibile usare comunque l'attributo **objectGUID** .

Se si spostano gli utenti tra foreste e domini, è necessario trovare un attributo che non viene modificato o che possa essere spostato con gli utenti durante lo spostamento. Un approccio consigliato consiste nell'introdurre un attributo sintetico. È possibile usare un attributo che include un elemento analogo a un GUID. Un nuovo GUID viene creato e assegnato all'utente durante la creazione di un oggetto. Una regola di sincronizzazione personalizzata può essere creata nel server del motore di sincronizzazione per creare questo valore in base all’ **objectGUID** e aggiornare l'attributo selezionato in ADDS. Quando si sposta l'oggetto, assicurarsi di copiare anche il contenuto di questo valore.

Un'altra soluzione consiste nello scegliere un attributo esistente che si sa che non cambierà. Uno degli attributi più comunemente usati è **employeeID**. Se si prende in considerazione un attributo che contiene lettere, assicurarsi che non vi sia alcuna possibilità che le lettere maiuscole e/o minuscole usate per il valore dell'attributo vengano modificate. Gli attributi non validi che non devono essere usati includono gli attributi con il nome dell'utente. In caso di matrimonio o divorzio è possibile che il nome venga modificato e ciò non è consentito per questo attributo. Per questo motivo non è nemmeno possibile selezionare attributi quali **userPrincipalName**, **mail** e **targetAddress** nell'installazione guidata di Azure AD Connect. Questi attributi contengono anche il carattere "@", che non è consentito in sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Modifica dell'attributo sourceAnchor
Il valore dell'attributo sourceAnchor non può essere modificato dopo la creazione dell'oggetto in Azure AD e la sincronizzazione dell'identità.

Le restrizioni seguenti sono quindi applicabili ad Azure AD Connect:

* L'attributo sourceAnchor può essere configurato solo durante l'installazione iniziale. Se si esegue di nuovo l'installazione guidata, questa opzione sarà di sola lettura. Per modificare questa impostazione, è necessario eseguire la disinstallazione e la reinstallazione.
* Se si installa un altro server di Azure AD Connect, sarà necessario selezionare lo stesso attributo sourceAnchor usato in precedenza. Se si passa ad Azure AD Connect dopo avere usato DirSync, sarà necessario usare **objectGUID** , perché questo è l'attributo usato da DirSync.
* Se il valore di sourceAnchor viene modificato dopo l'esportazione dell'oggetto in Azure AD, il servizio di sincronizzazione Azure AD Connect genera un errore e non consente altre modifiche all'oggetto prima della risoluzione del problema e della reimpostazione del valore precedente di sourceAnchor nella directory di origine.

## <a name="using-msds-consistencyguid-as-sourceanchor"></a>Uso di msDS-ConsistencyGuid come sourceAnchor
Per impostazione predefinita, Azure AD Connect (versione 1.1.486.0 e precedenti) usa objectGUID come attributo sourceAnchor. ObjectGUID è generato dal sistema. Non è possibile specificare il relativo valore durante la creazione di oggetti di AD locale. Come illustrato nella sezione [sourceAnchor](#sourceanchor), in alcuni scenari è necessario specificare il valore di sourceAnchor. Se gli scenari sono applicabili all'utente, è necessario usare un attributo AD configurabile, ad esempio msDS-ConsistencyGuid, come attributo sourceAnchor.

In Azure AD Connect (versione 1.1.524.0 e successive) è ora facilitato l'uso dell'attributo msDS-ConsistencyGuid come attributo sourceAnchor. Quando si usa questa funzionalità, Azure AD Connect configura automaticamente le regole di sincronizzazione per:

1. Usare msDS-ConsistencyGuid come attributo sourceAnchor per gli oggetti User. ObjectGUID è usato per altri tipi di oggetto.

2. Per ogni oggetto User di AD locale il cui attributo msDS-ConsistencyGuid non è popolato, Azure AD Connect scrive il valore di objectGUID nell'attributo msDS-ConsistencyGuid in AD locale. Dopo aver popolato l'attributo msDS-ConsistencyGuid, Azure AD Connect esporta l'oggetto in Azure AD.

>[!NOTE]
> Se un oggetto AD locale viene importato in Azure AD Connect (ovvero importato nello spazio connettore di AD e proiettato in Metaverse), non è più possibile modificarne il valore sourceAnchor. Per specificare il valore sourceAnchor di un determinato oggetto AD locale, configurare il relativo attributo msDS-ConsistencyGuid prima che venga importato in Azure AD Connect.

### <a name="permission-required"></a>È necessaria l'autorizzazione
Per usare questa funzionalità, l'account AD DS usato per la sincronizzazione con Active Directory locale deve disporre dell'autorizzazione di scrittura per l'attributo msDS-ConsistencyGuid in Active Directory locale.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>Come abilitare la funzionalità ConsistencyGuid: nuova installazione
È possibile abilitare l'uso di ConsistencyGuid come sourceAnchor durante una nuova installazione. Questa sezione descrive dettagliatamente sia l'installazione rapida che quella personalizzata.

  >[!NOTE]
  > Solo le versioni più recenti di Azure AD Connect, 1.1.524.0 e successive, supportano l'uso di ConsistencyGuid come sourceAnchor durante una nuova installazione.

### <a name="how-to-enable-the-consistencyguid-feature"></a>Come abilitare la funzionalità ConsistencyGuid
Al momento la funzionalità può essere abilitata solo durante una nuova installazione di Azure AD Connect.

#### <a name="express-installation"></a>Installazione rapida
Quando si installa Azure AD Connect in modalità rapida, la procedura guidata di Azure AD Connect determina automaticamente l'attributo AD più appropriato da usare come attributo sourceAnchor mediante la logica seguente:

* In primo luogo, la procedura guidata di Azure AD Connect invia una query al tenant di Azure AD per recuperare l'attributo AD usato come attributo sourceAnchor nell'installazione di Azure AD Connect precedente (se presente). Se queste informazioni sono disponibili, Azure AD Connect usa lo stesso attributo AD.

  >[!NOTE]
  > Solo le versioni più recenti di Azure AD Connect, 1.1.524.0 e successive, consentono di archiviare nel tenant di Azure AD le informazioni relative all'attributo sourceAnchor usato durante l'installazione. Le versioni precedenti di Azure AD Connect non lo consentono.

* Se le informazioni sull'attributo sourceAnchor usato non sono disponibili, la procedura guidata controlla lo stato dell'attributo msDS-ConsistencyGuid in Active Directory locale. Se l'attributo non è configurato in un qualsiasi oggetto nella directory, la procedura usa l'attributo msDS-ConsistencyGuid come attributo sourceAnchor. Se l'attributo è configurato su uno o più oggetti nella directory, la procedura conclude che l'attributo è usato da altre applicazioni e non è adatto come attributo sourceAnchor.

* In questo caso, la procedura guidata esegue il fallback usando objectGUID come attributo sourceAnchor.

* Dopo aver definito l'attributo sourceAnchor, la procedura guidata archivia le informazioni nel tenant di Azure AD. Le informazioni verranno usate nelle installazioni future di Azure AD Connect.

Dopo aver completato l'installazione rapida, la procedura guidata informa l'utente dell'attributo selezionato come attributo sourceAnchor.

![La procedura guidata segnala l'attributo di AD selezionato come sourceAnchor](./media/active-directory-aadconnect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>Installazione personalizzata
Quando si installa Azure AD Connect in modalità personalizzata, la procedura guidata di Azure AD Connect offre due opzioni per la configurazione dell'attributo sourceAnchor:

![Installazione personalizzata - Configurazione di sourceAnchor](./media/active-directory-aadconnect-design-concepts/consistencyGuid-02.png)

| Impostazione | Descrizione |
| --- | --- |
| Consenti ad Azure di gestire automaticamente l'ancoraggio di origine | Selezionare questa opzione se si vuole che Azure AD selezioni automaticamente l'attributo. Se si seleziona questa opzione, la procedura guidata di Azure AD Connect applica la stessa [logica di selezione dell'attributo sourceAnchor usata durante l'installazione rapida](#express-installation). Alla stregua dell'installazione rapida, la procedura guidata informa l'utente sull'attributo selezionato come attributo sourceAnchor al termine dell'installazione personalizzata. |
| Attributo specifico | Selezionare questa opzione se si vuole specificare un attributo di AD esistente come attributo sourceAnchor. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>Come abilitare la funzionalità ConsistencyGuid: distribuzione esistente
Se si ha una distribuzione di Azure AD Connect esistente che usa objectGUID come attributo dell'ancoraggio di origine, è possibile far sì che al suo posto usi ConsistencyGuid.

>[!NOTE]
> Solo le versioni più recenti di Azure AD Connect, 1.1.552.0 e successive, supportano il passaggio da ObjectGuid a ConsistencyGuid come attributo dell'ancoraggio di origine.

Per passare da objectGUID a ConsistencyGuid come attributo dell'ancoraggio di origine:

1. Avviare la procedura guidata di Azure AD Connect e fare clic su **Configurazione** per passare alla schermata Attività.

2. Selezionare l'opzione attività **Configura ancoraggio di origine** e fare clic su **Avanti**.

   ![Abilitare ConsistencyGuid per una distribuzione esistente: passaggio 2](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment01.png)

3. Immettere le credenziali di amministratore di Azure AD e fare clic su **Avanti**.

4. La procedura guidata di Azure AD Connect analizza lo stato dell'attributo msDS-ConsistencyGuid in Active Directory locale. Se l'attributo non è configurato in uno qualsiasi degli oggetti della directory, Azure AD Connect conclude che nessun'altra applicazione sta usando l'attributo e che quindi sia possibile usarlo come attributo dell'ancoraggio di origine. Fare clic su **Avanti** per continuare.

   ![Abilitare ConsistencyGuid per una distribuzione esistente: passaggio 4](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment02.png)

5. Nella schermata **Pronto per la configurazione** fare clic su **Configurazione** per modificare la configurazione.

   ![Abilitare ConsistencyGuid per una distribuzione esistente: passaggio 5](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment03.png)

6. Una volta completata la configurazione, la procedura guidata indica che ora viene usato msDS-ConsistencyGuid come attributo dell'ancoraggio di origine.

   ![Abilitare ConsistencyGuid per una distribuzione esistente: passaggio 6](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment04.png)

Durante l'analisi, al passaggio 4, se l'attributo è configurato su uno o più oggetti nella directory, la procedura conclude che l'attributo è usato da un'altra applicazione e restituisce un errore, come illustrato nel diagramma di seguito. Questo errore può verificarsi anche se in precedenza è stata abilitata la funzionalità ConsistencyGuid nel server Azure AD Connect primario e si prova a eseguire la stessa operazione nel server di staging.

![Abilitare ConsistencyGuid per una distribuzione esistente: errore](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeploymenterror.png)

 Se si è certi che l'attributo non viene usato da altre applicazioni esistenti, per eliminare l'errore riavviare la procedura guidata di Azure AD Connect specificando **/SkipLdapSearchcontact**. A tale scopo, al prompt dei comandi eseguire questo comando:

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>Impatto su AD FS o configurazione della federazione di terze parti
Se si usa Azure AD Connect per gestire la distribuzione di AD FS locale, Azure AD Connect aggiorna automaticamente le regole attestazioni per usare lo stesso attributo di AD come sourceAnchor. Ciò garantisce che l'attestazione ImmutableID generata da AD FS sia coerente con i valori di sourceAnchor esportati in Azure AD.

Se si gestisce AD FS dall'esterno di Azure AD Connect o se per l'autenticazione si usano server federativi di terze parti, è necessario aggiornare manualmente le regole attestazioni affinché l'attestazione ImmutableID sia coerente con i valori di sourceAnchor esportati in Azure AD, come descritto nella sezione dell'articolo [Modificare le regole attestazioni per AD FS](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#modclaims). Al termine dell'installazione, viene visualizzato l'avviso seguente:

![Configurazione della federazione di terze parti](./media/active-directory-aadconnect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>Aggiunta di nuove directory alla distribuzione esistente
Si supponga di aver distribuito Azure AD Connect con la funzionalità ConsistencyGuid abilitata e di voler aggiungere un'altra directory alla distribuzione. Quando si tenta di aggiungere la directory, la procedura guidata di Azure AD Connect controlla lo stato dell'attributo mSDS-ConsistencyGuid nella directory. Se l'attributo è configurato su uno o più oggetti nella directory, la procedura conclude che l'attributo è usato da altre applicazioni e restituisce un errore, come mostrato di seguito. Se si è certi che l'attributo non è usato dalle applicazioni esistenti, è necessario contattare il supporto tecnico per informazioni su come eliminare l'errore.

![Aggiunta di nuove directory alla distribuzione esistente](./media/active-directory-aadconnect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Accesso ad Azure AD
Durante l'integrazione della directory locale con Azure AD, è importante capire come le impostazioni di sincronizzazione possono influire sul modo in cui l'utente esegue l'autenticazione. Azure AD usa userPrincipalName (UPN) per autenticare l'utente. Quando si sincronizzano gli utenti è tuttavia necessario scegliere con attenzione l'attributo da usare per userPrincipalName.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Scegliere l'attributo per userPrincipalName
Quando si seleziona l'attributo per specificare il valore di UPN da usare in Azure è necessario verificare quanto segue:

* I valori di attributo devono essere conformi alla sintassi UPN (RFC 822), ovvero devono essere nel formato username@domain
* Il suffisso nei valori deve corrispondere a uno dei domini personalizzati verificati in Azure AD

Nelle impostazioni rapide come attributo deve essere scelto userPrincipalName. Se l'attributo userPrincipalName non contiene il valore che si vuole che gli utenti usino per accedere ad Azure, è necessario scegliere l'opzione **Installazione personalizzata**.

### <a name="custom-domain-state-and-upn"></a>Stato del dominio personalizzato e UPN
È importante verificare che esista un dominio verificato per il suffisso UPN.

John è un utente di contoso.com. Si vuole fare in modo che John usi l'UPN locale john@contoso.com per l'accesso ad Azure dopo che è stata eseguita la sincronizzazione degli utenti con la directory di Azure AD contoso.onmicrosoft.com. A tale scopo, è necessario aggiungere e verificare contoso.com come dominio personalizzato in Azure AD per poter avviare la sincronizzazione degli utenti. Se il suffisso UPN di John, ad esempio contoso.com, non corrisponde a un dominio verificato in Azure AD, il suffisso UPN viene sostituito con contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Domini locali non instradabili e UPN per Azure AD
Alcune organizzazioni usano domini non instradabili, ad esempio contoso.local, o domini semplici con etichetta singola come contoso. In Azure AD non è possibile verificare un dominio non instradabile. Azure AD Connect è in grado eseguire la sincronizzazione con un solo dominio verificato in Azure AD. Quando si crea una directory di Azure AD, viene creato un dominio instradabile che diventa dominio predefinito per Azure AD, ad esempio contoso.onmicrosoft.com. Si rende quindi necessario verificare eventuali altri domini instradabili nello stesso scenario, nel caso in cui non si voglia eseguire la sincronizzazione con il dominio .onmicrosoft.com predefinito.

Per altre informazioni sull'aggiunta e la verifica dei domini, vedere [Aggiungere un nome di dominio personalizzato ad Azure Active Directory](../active-directory-domains-add-azure-portal.md) .

Azure AD Connect rileva se l'esecuzione avviene in un ambiente di dominio non instradabile e avvisa che è opportuno non proseguire con le impostazioni rapide. Se si opera in un dominio non instradabile, è probabile che anche l'UPN degli utenti abbia suffissi non instradabili. Ad esempio, se si opera in contoso.local, Azure AD Connect suggerisce di usare le impostazioni personalizzate anziché le impostazioni rapide. Con le impostazioni personalizzate è possibile specificare l'attributo che deve essere usato come nome UPN per l'accesso ad Azure dopo la sincronizzazione degli utenti con Azure AD.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).
