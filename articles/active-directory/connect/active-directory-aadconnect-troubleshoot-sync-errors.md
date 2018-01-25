---
title: 'Azure AD Connect: Risoluzione dei problemi durante la sincronizzazione | Microsoft Docs'
description: Questo articolo descrive come risolvere i problemi che si verificano durante la sincronizzazione con Azure AD Connect.
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 2209d5ce-0a64-447b-be3a-6f06d47995f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: billmath
ms.openlocfilehash: aaa374d5a11ef5b5860f83a87386ff981319189f
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="troubleshooting-errors-during-synchronization"></a>Risoluzione degli problemi durante la sincronizzazione
Gli errori potrebbero verificarsi durante la sincronizzazione dei dati dell'identità da Windows Server Active Directory (AD DS) ad Azure Active Directory (Azure AD). L'articolo offre una panoramica delle diverse tipologie di errori di sincronizzazione, di alcuni scenari possibili che causano tali errori e delle possibili soluzioni per correggere questi errori. In questo articolo vengono descritti soltanto gli errori più comuni.

 L'articolo presuppone che il lettore abbia sufficiente familiarità con [i basilari concetti di progettazione di Azure AD e Azure AD Connect](active-directory-aadconnect-design-concepts.md).

Insieme alla versione più recente di Azure AD Connect \((agosto 2016 o successive)\), nel [portale di Azure](https://aka.ms/aadconnecthealth) è disponibile un report sugli errori di sincronizzazione a complemento di Azure AD Connect Health per la sincronizzazione.

A partire dal 1 settembre 2016 la funzionalità [Azure Active Directory Duplicate Attribute Resiliency](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) (Resilienza degli attributi duplicati di Azure Active Directory) verrà abilitata per impostazione predefinita per tutti i *nuovi* tenant di Azure Active Directory. Nei prossimi mesi questa funzionalità verrà abilitata automaticamente per i tenant esistenti.

Azure AD Connect esegue 3 tipi di operazioni dalle directory che mantiene sincronizzate: importazione, sincronizzazione ed esportazione. Gli errori possono verificarsi durante tutte le operazioni. L'articolo si concentra prevalentemente sugli errori che si verificano durante l'esportazione in Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Errori durante l'esportazione in Azure AD
La sezione seguente descrive le differenti tipologie di errori di sincronizzazione che possono verificarsi durante l'esportazione in Azure AD usando il connettore di Azure AD, identificabile dal formato del nome: "contoso.*onmicrosoft.com*".
Quando si verificano un errore durante l'esportazione in Azure AD significa che l'operazione di \(aggiunta, aggiornamento, eliminazione e così via\) tentata dal \(motore di sincronizzazione\) di Azure AD Connect in Azure Active Directory non è andata a buon fine.

![Panoramica degli errori di esportazione](./media/active-directory-aadconnect-troubleshoot-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Errori di mancata corrispondenza dei dati
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>DESCRIZIONE
* Quando il \(motore di sincronizzazione\) di Azure AD Connect invia il comando ad Azure Active Directory di aggiungere o aggiornare gli oggetti, Azure AD associa l'oggetto in ingresso usando l'attributo **sourceAnchor** all'attributo **immutableId** degli oggetti presenti in Azure AD. Tale corrispondenza viene detta **corrispondenza rigida**.
* Quando Azure AD **non trova** alcun oggetto che corrisponda all'attributo **immutableId** con l'attributo **sourceAnchor** dell'oggetto in ingresso, prima di eseguire il provisioning di un nuovo oggetto, esegue il fallback per usare gli attributi UserPrincipalName e ProxyAddresses per trovare una corrispondenza. Tale corrispondenza viene detta **corrispondenza flessibile**. La corrispondenza flessibile è pensata per associare gli oggetti già presenti in Azure AD (che sono distribuiti in Azure AD) ai nuovi oggetti aggiunti o aggiornati durante la sincronizzazione che rappresentano la stessa entità (utenti, gruppi) in locale.
* L'errore **InvalidSoftMatch** si verifica quando la corrispondenza rigida non trova oggetti corrispondenti **E** la corrispondenza flessibile trova un oggetto corrispondente, che però ha un differente valore dell'attributo *immutableId* rispetto all'attributo *SourceAnchor* dell'oggetto in ingresso, indicando che l'oggetto corrispondente è stato sincronizzato con un altro oggetto dall'Active Directory locale.

In altre parole, affinché la corrispondenza flessibile funzioni, l'oggetto con cui stabilire una corrispondenza di questo tipo non deve avere alcun valore per l'attributo *immutableId*. Se la corrispondenza rigida di un qualsiasi oggetto con l'attributo *immutableId* impostato con un valore non riesce, ma tale oggetto soddisfa i criteri della corrispondenza flessibile, l'operazione restituirà come risultato un errore di sincronizzazione di tipo InvalidSoftMatch.

Schema di Azure Active Directory non consente a due o più oggetti di avere lo stesso valore degli attributi seguenti. \)L'elenco è incompleto.\(

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier
* ObjectId

> [!NOTE]
> La funzionalità [Resilienza degli attributi duplicati di Azure Active Directory](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) viene anche implementata come comportamento predefinito di Azure Active Directory.  Ciò ridurrà il numero degli errori di sincronizzazione rilevati da Azure AD Connect (e da altri client di sincronizzazione), rendendo Azure AD più resiliente nella modalità di gestione degli attributi ProxyAddresses e UserPrincipalName duplicati che sono presenti negli ambienti AD locali. Questa funzionalità non risolve gli errori di duplicazione, pertanto c'è ancora bisogno di correggere i dati. Tuttavia, questa funzionalità consente il provisioning di nuovi oggetti per i quali, altrimenti, viene bloccato il provisioning a causa dei valori duplicati in Azure AD. Ciò riduce anche il numero di errori di sincronizzazione restituito al client di sincronizzazione.
> Se questa funzionalità è abilitata per il tenant, non si verificheranno gli errori di sincronizzazione InvalidSoftMatch rilevati durante il provisioning di nuovi oggetti.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Scenari di esempio per InvalidSoftMatch
1. Nell'Active Directory locale sono presenti due o più oggetti con lo stesso valore dell'attributo ProxyAddresses, ma il provisioning viene eseguito in Azure AD soltanto per un oggetto.
2. Nell'Active Directory locale sono presenti due o più oggetti con lo stesso valore dell'attributo userPrincipalName, ma il provisioning viene eseguito in Azure AD soltanto per un oggetto.
3. Un oggetto è stato aggiunto all'Active Directory locale con lo stesso valore dell'attributo ProxyAddresses a quello di un oggetto già presente in Azure Active Directory. Per l'oggetto aggiunto in locale non viene eseguito il provisioning in Azure Active Directory.
4. Un oggetto è stato aggiunto all'Active Directory locale con lo stesso valore dell'attributo userPrincipalName a quello di un account presente in Azure Active Directory. Per l'oggetto non viene eseguito il provisioning in Azure Active Directory.
5. Un account sincronizzato è stato spostato da Foresta A a Foresta B. Azure AD Connect (motore di sincronizzazione) stava usando l'attributo ObjectGUID per calcolare l'attributo SourceAnchor. Dopo lo spostamento della foresta, il valore dell'attributo SourceAnchor è differente. La sincronizzazione del nuovo oggetto (da Foresta B) con l'oggetto esistente in Azure AD non riesce.
6. Un oggetto sincronizzato è stato accidentalmente eliminato dall'Active Directory locale e un nuovo oggetto è stato creato in Active Directory per la stessa entità (ad esempio un utente) senza eliminare l'account in Azure Active Directory. La sincronizzazione del nuovo account con l'oggetto di Azure AD esistente non riesce.
7. Azure AD Connect è stato disinstallato e reinstallato. Durante la reinstallazione, come attributo SourceAnchor è stato scelto un attributo differente. La sincronizzazione di tutti gli oggetti già sincronizzati in precedenza è stata interrotta restituendo l'errore InvalidSoftMatch.

#### <a name="example-case"></a>Caso di esempio:
1. **Bob Smith** è un utente sincronizzato in Azure Active Directory da un'Active Directory locale di *contoso.com*
2. Il parametro **UserPrincipalName** di Bob Smith viene configurato come **bobs@contoso.com**.
3. **"abcdefghijklmnopqrstuv = ="** è l'attributo **SourceAnchor** calcolato da Azure AD Connect usando l'attributo **objectGUID** di Bob Smith dall'Active Directory locale, che corrisponde all'attributo **immutableId** di Bob Smith in Azure Active Directory.
4. Bob dispone anche dei seguenti valori per l'attributo **proxyAddresses**:
   * smtp:bobs@contoso.com
   * smtp:bob.smith@contoso.com
   * **smtp:bob@contoso.com**
5. Un nuovo utente, **Bob Taylor**, viene aggiunto all'Active Directory locale.
6. Il parametro **UserPrincipalName** di Bob Taylor viene configurato come **bobt@contoso.com**.
7. **"abcdefghijkl0123456789 = =" "** è l'attributo **sourceAnchor** calcolato da Azure AD Connect usando l'attributo **objectGUID** di Bob Taylor dall'Active Directory locale. L'oggetto di Bob Taylor NON è stato ancora sincronizzato ad Azure Active Directory.
8. Bob Taylor dispone dei valori seguenti per l'attributo proxyAddresses
   * smtp:bobt@contoso.com
   * smtp:bob.taylor@contoso.com
   * **smtp:bob@contoso.com**
9. Durante la sincronizzazione, Azure AD Connect riconoscerà l'aggiunta di Bob Taylor nell'Active Directory locale e chiederà ad Azure AD di apportare la stessa modifica.
10. Azure AD eseguirà prima una corrispondenza rigida, ossia eseguirà la ricerca per vedere se esiste un qualsiasi oggetto con l'attributo immutableId uguale a "abcdefghijkl0123456789==". La corrispondenza rigida avrà esito negativo se nessun altro oggetto in Azure AD avrà quell'attributo immutableId.
11. Azure AD tenterà quindi di eseguire la corrispondenza flessibile con Bob Taylor, ossia, eseguirà la ricerca per vedere se esiste un qualsiasi oggetto con l'attributo proxyAddresses uguale ai tre valori, compreso smtp:bob@contoso.com
12. Azure AD troverà l'oggetto di Bob Smith che corrisponde ai criteri della corrispondenza flessibile. Tuttavia, il valore dell'attributo immutableId per questo oggetto è "abcdefghijklmnopqrstuv==", il quale indica che l'oggetto è stato sincronizzato da un altro oggetto presente nell'Active Directory locale. Di conseguenza, Azure AD non può eseguire la corrispondenza flessibile di questi oggetti e viene restituito l'errore di sincronizzazione **InvalidSoftMatch**.

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Come correggere l'errore InvalidSoftMatch
La causa più comune dell'errore InvalidSoftMatch è che due oggetti con attributi SourceAnchor \(immutableId\) differenti hanno lo stesso valore per gli attributi ProxyAddresses e/o UserPrincipalName, i quali vengono usati durante il processo di corrispondenza flessibile in Azure AD. Per risolvere l'errore di corrispondenza flessibile

1. Identificare il valore duplicato degli attributi proxyAddresses, userPrincipalName o un altro valore di attributo che ha causato l'errore. Identificare anche i due \(o più\) oggetti coinvolti nel conflitto. Il report generato da [Azure AD Connect Health per la sincronizzazione](https://aka.ms/aadchsyncerrors) consente di identificare i due oggetti.
2. Identificare l'oggetto che deve continuare ad avere il valore duplicato e quello che deve essere modificato.
3. Rimuovere il valore duplicato dall'oggetto che NON deve avere tale valore. Si noti che è necessario apportare la modifica nella directory in cui l'oggetto è originato. In alcuni casi, potrebbe essere necessario eliminare uno degli oggetti in conflitto.
4. Se si apporta la modifica nell'Active Directory locale, consentire ad Azure AD Connect di sincronizzare la modifica.

Si noti che il report sugli errori di sincronizzazione all'interno di Azure AD Connect Health per la sincronizzazione viene aggiornato ogni 30 minuti e include gli errori rilevati durante il tentativo di sincronizzazione più recente.

> [!NOTE]
> L'attributo immutableId, per definizione, non deve cambiare nel corso della durata dell'oggetto. Se non si è configurato Azure AD Connect tenendo in considerazione alcuni degli scenari riportati nell'elenco precedente, si potrebbe incorrere in una situazione in cui Azure AD Connect calcola un valore differente dell'attributo SourceAnchor per l'oggetto di Active Directory che rappresenta la stessa entità (stesso gruppo/utente/contatto e così via) che dispone di un oggetto di Azure AD esistente che si desidera continuare a usare.
>
>

#### <a name="related-articles"></a>Articoli correlati
* [Gli attributi duplicati o non validi impediscono la sincronizzazione delle directory in Office 365](https://support.microsoft.com/en-us/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>DESCRIZIONE
Quando Azure AD tenta una corrispondenza flessibile tra due oggetti, è possibile che i due oggetti appartenenti a una "tipologia di oggetto" differente (ad esempio, utente, gruppo, contatto e così via) abbiano gli stessi valori per gli attributi usati per eseguire tale corrispondenza. Poiché la duplicazione di questi attributi non è consentita in Azure AD, l'operazione può portare alla restituzione di un errore di sincronizzazione "ObjectTypeMismatch".

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Scenari di esempio per l'errore ObjectTypeMismatch
* In Office 365 viene creato un gruppo di protezione abilitato alla posta elettronica. L'amministratore aggiunge un nuovo utente o contatto all'Active Directory locale (che non è ancora stato sincronizzato con Azure AD) con lo stesso valore per l'attributo ProxyAddresses del gruppo di Office 365.

#### <a name="example-case"></a>Caso di esempio
1. L'amministratore crea un nuovo gruppo di sicurezza abilitato alla posta elettronica in Office 365 per il reparto addetto alle imposte e mette a disposizione un indirizzo di posta elettronica come tax@contoso.com. Ciò consente di assegnare l'attributo ProxyAddresses per questo gruppo con il valore di **smtp:tax@contoso.com**
2. Un nuovo utente entra in Contoso.com, per il quale viene creato un account locale con l'attributo proxyAddress come **smtp:tax@contoso.com**
3. Quando Azure AD Connect sincronizzerà il nuovo account utente, verrà restituito l'errore "ObjectTypeMismatch".

#### <a name="how-to-fix-objecttypemismatch-error"></a>Come correggere l'errore ObjectTypeMismatch
La causa più comune dell'errore ObjectTypeMismatch è che due oggetti di tipo differente (utente, gruppo, contatto e così via) hanno lo stesso valore per l'attributo ProxyAddresses. Per correggere l'errore ObjectTypeMismatch:

1. Identificare il valore duplicato dell'attributo proxyAddresses (o su un altro attributo) che ha causato l'errore. Identificare anche i due \(o più\) oggetti coinvolti nel conflitto. Il report generato da [Azure AD Connect Health per la sincronizzazione](https://aka.ms/aadchsyncerrors) consente di identificare i due oggetti.
2. Identificare l'oggetto che deve continuare ad avere il valore duplicato e quello che deve essere modificato.
3. Rimuovere il valore duplicato dall'oggetto che NON deve avere tale valore. Si noti che è necessario apportare la modifica nella directory in cui l'oggetto è originato. In alcuni casi, potrebbe essere necessario eliminare uno degli oggetti in conflitto.
4. Se si apporta la modifica nell'Active Directory locale, consentire ad Azure AD Connect di sincronizzare la modifica. Il report sugli errori di sincronizzazione all'interno di Azure AD Connect Health per la sincronizzazione viene aggiornato ogni 30 minuti e include gli errori rilevati durante il tentativo di sincronizzazione più recente.

## <a name="duplicate-attributes"></a>Attributi duplicati
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>DESCRIZIONE
Lo schema di Azure Active Directory non consente a due o più oggetti di avere lo stesso valore degli attributi seguenti, vale a dire che ogni oggetto in Azure AD è obbligato ad avere un valore univoco di questi attributi a una determinata istanza.

* ProxyAddresses
* UserPrincipalName

Se Azure AD Connect tenta di aggiungere un nuovo oggetto o di aggiornare un oggetto esistente con un valore degli attributi indicati sopra che è già stato assegnato a un altro oggetto in Azure Active Directory, l'operazione restituisce l'errore di sincronizzazione "AttributeValueMustBeUnique".

#### <a name="possible-scenarios"></a>Scenari possibili:
1. Il valore duplicato viene assegnato a un oggetto già sincronizzato, che entra in conflitto con un altro oggetto sincronizzato.

#### <a name="example-case"></a>Caso di esempio:
1. **Bob Smith** è un utente sincronizzato in Azure Active Directory da un'Active Directory locale di contoso.com
2. Il parametro **UserPrincipalName** di Bob Smith in locale è configurato come **bobs@contoso.com**.
3. Bob dispone anche dei seguenti valori per l'attributo **proxyAddresses**:
   * smtp:bobs@contoso.com
   * smtp:bob.smith@contoso.com
   * **smtp:bob@contoso.com**
4. Un nuovo utente, **Bob Taylor**, viene aggiunto all'Active Directory locale.
5. Il parametro **UserPrincipalName** di Bob Taylor viene configurato come **bobt@contoso.com**.
6. **Bob Taylor** dispone dei valori seguenti per l'attributo **proxyAddresses**: smtp:bobt@contoso.com ii. smtp:bob.taylor@contoso.com
7. L'oggetto di Bob Taylor viene sincronizzato correttamente con Azure AD.
8. L'amministratore ha deciso di aggiornare l'attributo **ProxyAddresses** di Bob Taylor con il seguente valore: i. **smtp:bob@contoso.com**
9. Azure AD tenterà di aggiornare l'oggetto di Bob Taylor in Azure AD con il valore indicato sopra, ma tale operazione avrà esito negativo poiché il valore di ProxyAddresses è già stato assegnato a Bob Smith; di conseguenza verrà restituito l'errore "AttributeValueMustBeUnique".

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Come correggere l'errore AttributeValueMustBeUnique
La causa più comune dell'errore AttributeValueMustBeUnique è che due oggetti con attributi SourceAnchor \(immutableId\) differenti hanno lo stesso valore per gli attributi ProxyAddresses e/o UserPrincipalName. Per correggere l'errore AttributeValueMustBeUnique

1. Identificare il valore duplicato degli attributi proxyAddresses, userPrincipalName o un altro valore di attributo che ha causato l'errore. Identificare anche i due \(o più\) oggetti coinvolti nel conflitto. Il report generato da [Azure AD Connect Health per la sincronizzazione](https://aka.ms/aadchsyncerrors) consente di identificare i due oggetti.
2. Identificare l'oggetto che deve continuare ad avere il valore duplicato e quello che deve essere modificato.
3. Rimuovere il valore duplicato dall'oggetto che NON deve avere tale valore. Si noti che è necessario apportare la modifica nella directory in cui l'oggetto è originato. In alcuni casi, potrebbe essere necessario eliminare uno degli oggetti in conflitto.
4. Se si apporta la modifica nell'Active Directory locale, consentire ad Azure AD Connect di sincronizzare la modifica per correggere l'errore.

#### <a name="related-articles"></a>Articoli correlati
-[Gli attributi duplicati o non validi impediscono la sincronizzazione delle directory in Office 365](https://support.microsoft.com/en-us/kb/2647098)

## <a name="data-validation-failures"></a>Errori di convalida dei dati
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>DESCRIZIONE
Azure Active Directory applica varie restrizioni sui dati prima di consentire la scrittura dei dati nella directory al fine di garantire agli utenti finali di beneficiare delle migliori esperienze mentre usano le applicazioni che dipendono da questi dati.

#### <a name="scenarios"></a>Scenari
a. Il valore dell'attributo UserPrincipalName contiene caratteri non validi o non supportati.
b. L'attributo UserPrincipalName non è conforme al formato richiesto.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Come correggere l'errore IdentityDataValidationFailed
a. Assicurarsi che l'attributo userPrincipalName contenga caratteri supportati e rispetti il formato richiesto.

#### <a name="related-articles"></a>Articoli correlati
* [Preparazione per il provisioning degli utenti tramite la sincronizzazione delle directory a Office 365](https://support.office.com/en-us/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>FederatedDomainChangeError
#### <a name="description"></a>DESCRIZIONE
Questo è un caso specifico che restituisce un errore di sincronizzazione **"FederatedDomainChangeError"** quando il suffisso dell'attributo UserPrincipalName di un utente viene modificato da un dominio federato a un altro dominio federato.

#### <a name="scenarios"></a>Scenari
Per un utente sincronizzato, il suffisso dell'attributo UserPrincipalName è stato modificato da un dominio federato a un altro dominio federato locale. Ad esempio, *UserPrincipalName = bob@contoso.com* è stato modificato in *UserPrincipalName = bob@fabrikam.com*.

#### <a name="example"></a>Esempio
1. Bob Smith, un account di Contoso.com, viene aggiunto come nuovo utente in Active Directory con l'attributo UserPrincipalName bob@contoso.com
2. Bob passa a un'altra divisione di Contoso.com denominata Fabrikam.com e il suo attributo UserPrincipalName diventa bob@fabrikam.com
3. Entrambi i domini di contoso.com e fabrikam.com sono domini federati con Azure Active Directory.
4. L'attributo userPrincipalName di Bob non viene aggiornato e si verifica l'errore di sincronizzazione "FederatedDomainChangeError".

#### <a name="how-to-fix"></a>Modalità di correzione
Se il suffisso dell'attributo UserPrincipalName dell'utente è stato aggiornato da bob@**contoso.com** a bob@**fabrikam.com**, dove entrambi i domini **contoso.com** e **fabrikam.com** sono **domini federati**, seguire questa procedura per correggere l'errore di sincronizzazione.

1. Aggiornare l'attributo UserPrincipalName dell'utente in Azure AD da bob@contoso.com a bob@contoso.onmicrosoft.com. È possibile utilizzare il comando PowerShell seguente con il modulo di Azure AD PowerShell: `Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. Consentire al ciclo di sincronizzazione successivo di eseguire un nuovo tentativo di sincronizzazione. Questa volta la sincronizzazione andrà a buon fine e permetterà di aggiornare l'attributo UserPrincipalName di Bob su bob@fabrikam.com, come previsto.

#### <a name="related-articles"></a>Articoli correlati
* [Le modifiche non sono sincronizzate dallo strumento di sincronizzazione di Azure Active Directory dopo aver modificato l'UPN di un account utente per usare un dominio federato diverso](https://support.microsoft.com/en-us/help/2669550/changes-aren-t-synced-by-the-azure-active-directory-sync-tool-after-you-change-the-upn-of-a-user-account-to-use-a-different-federated-domain)

## <a name="largeobject"></a>LargeObject
### <a name="description"></a>DESCRIZIONE
Quando un attributo supera il limite di dimensioni consentite, di lunghezza o di conteggio impostati dallo schema di Azure Active Directory, la sincronizzazione restituisce come risultato un errore di sincronizzazione di tipo **LargeObject** o **ExceededAllowedLength**. In genere questo errore si verifica per gli attributi seguenti:

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>Scenari possibili
1. L'attributo userCertificate di Bob contiene troppi certificati assegnati a Bob, tra i quali possono esserci certificati scaduti o meno recenti. Il limite rigido consente 15 certificati. Per altre informazioni su come gestire gli errori di LargeObject con attributo userCertificate, vedere l'articolo [Handling LargeObject errors caused by userCertificate attribute](active-directory-aadconnectsync-largeobjecterror-usercertificate.md) (Gestione degli errori di LargeObject causati dall'attributo userCertificate).
2. L'attributo userSMIMECertificate di Bob contiene troppi certificati assegnati a Bob, tra i quali possono esserci certificati scaduti o meno recenti. Il limite rigido consente 15 certificati.
3. L'attributo thumbnailPhoto di Bob impostato in Active Directory è troppo grande per essere sincronizzato in Azure AD.
4. Durante il popolamento automatico dell'attributo ProxyAddresses in Active Directory a un oggetto sono stati assegnati troppi elementi ProxyAddresses.

### <a name="how-to-fix"></a>Modalità di correzione
1. Verificare che l'attributo che ha causato l'errore non superi il limite consentito.

## <a name="related-links"></a>Collegamenti correlati
* [Individuare oggetti Active Directory in Centro di amministrazione di Active Directory](https://technet.microsoft.com/library/dd560661.aspx)
* [Come eseguire una query di Azure Active Directory per un oggetto usando Azure Active Directory PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)
