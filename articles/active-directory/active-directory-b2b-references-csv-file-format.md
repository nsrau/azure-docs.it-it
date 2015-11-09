<properties
   pageTitle="Formato di file CSV per l'anteprima di Azure Active Directory B2B Collaboration | Microsoft Azure"
   description="Azure Active Directory B2B supporta le relazioni tra società abilitando i partner commerciali ad accedere in modo selettivo alle applicazioni aziendali."
   services="active-directory"
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="10/27/2015"
   ms.author="viviali"/>

# Formato di file CSV per l'anteprima di Azure Active Directory (Azure AD) B2B Collaboration

La versione di anteprima di Azure AD B2B Collaboration richiede un file CSV che specifica le informazioni sugli utenti partner da caricare nel portale di Azure AD. Il file CSV dovrebbe contenere le etichette obbligatorie indicate di seguito e i campi facoltativi, se necessario. Modificare il file CSV di esempio (sotto) senza cambiare l'ortografia delle etichette della prima riga o riordinare le colonne.

>[AZURE.NOTE]La prima riga di etichette (ad esempio, Email, DisplayName...) è necessaria per analizzare correttamente il file CSV. L'ortografia deve corrispondere ai campi specificati sotto. Queste etichette identificano il contenuto corrispondente. Le etichette dei campi facoltativi non necessari possono essere rimosse dal file CSV. L'intera colonna può essere lasciata vuota.

## Campi obbligatori: <br/>
**Email:** indirizzo di posta elettronica dell'utente invitato. <br/> **DisplayName:** nome visualizzato dell'utente invitato, in genere nome e cognome.<br/> **InviteContactUsUrl:** URL da includere negli inviti tramite posta elettronica, qualora l'utente invitato voglia contattare l'organizzazione.<br/>

## Campi facoltativi: <br/>
**InviteAppID:** ID dell'applicazione da usare per personalizzare l'invito tramite posta elettronica e le pagine di accettazione.<br/> **InviteAppResources:** AppID delle applicazioni aziendali per assegnare gli utenti. Per recuperare gli AppID in PowerShell, chiamare `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`<br/> **InviteGroupResources:** ObjectID dei gruppi a cui aggiungere gli utenti. Per recuperare gli ObjectID in PowerShell, chiamare `Get-MsolGroup | fl DisplayName, ObjectId`<br/> **InviteReplyURL:** URL a cui indirizzare un utente invitato dopo l'accettazione dell'invito. Deve essere un URL specifico della società, ad esempio [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/). Se questo campo facoltativo non viene specificato, l'utente invitato viene indirizzato al pannello di accesso alle app da dove può accedere alle app aziendali scelte. L'URL del pannello di accesso all'app è nel formato `https://account.activedirectory.windowsazure.com/applications/default.aspx?tenantId=<TenantID>`.<br/> **Language:** lingua dell'invito tramite posta elettronica e dell'esperienza di riscatto, con l'inglese come impostazione predefinita quando la lingua non vine specificata. I codici delle altre 10 lingue supportate sono:<br/> 1. de: tedesco<br/> 2. es: spagnolo<br/> 3. fr: francese<br/> 4. it: italiano<br/> 5. ja: giapponese<br/> 6. ko: coreano<br/> 7. pt-BR: portoghese (Brasile)<br/> 8. ru: russo<br/> 9. zh-HANS: cinese semplificato<br/> 10. zh-HANT: cinese tradizionale<br/>

## File CSV di esempio
Qui è riportato un file CSV di esempio che è possibile modificare.

>[AZURE.NOTE]Copiarlo e incollarlo nel Blocco note e salvarlo con l'estensione di file ".csv", quindi modificarlo in Excel. Verrà strutturato in una tabella con le etichette nella prima riga.

>[AZURE.NOTE]Aggiungere altri campi facoltativi in Excel specificando l'etichetta e popolando la rispettiva colonna.

```
Email,DisplayName,InviteAppID,InviteReplyUrl,InviteAppResources,InviteGroupResources,InviteContactUsUrl
wharp@contoso.com,Walter Harp,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
jsmith@contoso.com,Jeff Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
bsmith@contoso.com,Ben Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
```

## Articoli correlati
Vedere gli altri articoli su Azure B2B Collaboration.

- [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Funzionamento](active-directory-b2b-how-it-works.md)
- [Procedura dettagliata](active-directory-b2b-detailed-walkthrough.md)
- [Formato del token per l'utente esterno](active-directory-b2b-references-external-user-token-format.md)
- [Modifiche agli attributi dell'oggetto utente esterno](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitazioni correnti della versione di anteprima](active-directory-b2b-current-preview-limitations.md)

<!---HONumber=Nov15_HO1-->