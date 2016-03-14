<properties
   pageTitle="Ruoli dell'applicazione | Microsoft Azure"
   description="Come implementare l'autorizzazione con i ruoli dell'applicazione"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

#  Ruoli dell'applicazione nelle applicazioni multi-tenant

Questo articolo fa [parte di una serie]. Per questa serie è anche disponibile un'[applicazione di esempio] completa.

I ruoli dell'applicazione consentono di assegnare le autorizzazioni agli utenti. L'applicazione [Tailspin Surveys][Tailspin] ad esempio definisce i ruoli seguenti:

- Amministratore può eseguire tutte le operazioni CRUD su qualsiasi sondaggio appartenente al tenant.
- Autore può creare nuovi sondaggi.
- Lettore può leggere qualsiasi sondaggio appartenente al tenant.

Durante il processo di [autorizzazione], i ruoli verranno convertiti in autorizzazioni. La prima fase illustra come assegnare e gestire i ruoli. Sono state identificate tre opzioni principali:

-	[Ruoli dell'app Azure AD](#roles-using-azure-ad-app-roles)
-	[Gruppi di sicurezza di Azure AD](#roles-using-azure-ad-security-groups)
-	[Gestione ruoli dell'applicazione](#roles-using-an-application-role-manager).

## Definizione dei ruoli con i ruoli dell'app Azure AD

Si tratta dell'approccio usato nell'app Tailspin Surveys.

Secondo questo approccio, il provider SaaS definisce i ruoli dell'applicazione aggiungendoli al manifesto dell'applicazione. Dopo l'iscrizione, un amministratore della directory AD del cliente assegna gli utenti ai ruoli. Quando l'utente esegue l'accesso, i ruoli assegnati dell'utente vengono inviati come attestazioni.

> [AZURE.NOTE] Se il cliente usa Azure AD Premium, l'amministratore può assegnare un gruppo di sicurezza a un ruolo e i membri del gruppo ereditano il ruolo dell'app. Si tratta di una soluzione pratica per gestire i ruoli, perché il proprietario del gruppo non deve essere un amministratore di Active Directory.

Vantaggi di questo approccio:

-	Modello di programmazione semplice.
-	Ruoli specifici dell'applicazione. Le attestazioni basate su ruolo per un'applicazione non vengono inviate a un'altra applicazione.
-	Se il cliente rimuove l'applicazione dal tenant di Active Directory, anche il ruolo viene rimosso.
-	L'applicazione non necessita di autorizzazioni aggiuntive per Active Directory. È sufficiente l'autorizzazione di lettura del profilo dell'utente.

Svantaggi:

- I clienti che non hanno Azure AD Premium non possono assegnare i gruppi di sicurezza ai ruoli. Per questi clienti, tutte le assegnazioni di utenti devono essere effettuate da un amministratore di Active Directory.
- Se è presente un'API Web back-end separata dall'app Web, le assegnazioni dei ruoli per l'app Web non valgono per l'API Web. Per altre informazioni su questo argomento, vedere [Protezione di un'API Web back-end].

### Implementazione

**Definire i ruoli.** Il provider SaaS dichiara i ruoli dell'app nel manifesto dell'applicazione. Ad esempio, ecco la voce del manifesto per l'app Surveys:

```
"appRoles": [
  {
    "allowedMemberTypes": [
      "User"
    ],
    "description": "Creators can create Surveys",
    "displayName": "SurveyCreator",
    "id": "1b4f816e-5eaf-48b9-8613-7923830595ad",
    "isEnabled": true,
    "value": "SurveyCreator"
  },
  {
    "allowedMemberTypes": [
      "User"
    ],
    "description": "Administrators can manage the Surveys in their tenant",
    "displayName": "SurveyAdmin",
    "id": "c20e145e-5459-4a6c-a074-b942bbd4cfe1",
    "isEnabled": true,
    "value": "SurveyAdmin"
  }
],
```

La proprietà `value` viene visualizzata nell'attestazione basata su ruolo. La `id` proprietà è l'identificatore univoco per il ruolo definito. Genera sempre un nuovo valore GUID per `id`.

**Assegnare gli utenti**. Quando un nuovo cliente effettua l'iscrizione, l'applicazione viene registrata nel tenant di Active Directory del cliente. A questo punto, un amministratore di Active Directory per il tenant può assegnare gli utenti ai ruoli.

> [AZURE.NOTE] Come indicato in precedenza, i clienti che non hanno Azure AD Premium non possono assegnare i gruppi di sicurezza ai ruoli.

Lo screenshot seguente dal portale di Azure mostra tre utenti. Fabiola è stata assegnata direttamente a un ruolo. Davide ha ereditato un ruolo come membro di un gruppo di sicurezza "Admin Surveys", che a sua volta è assegnato a un ruolo. Luca non è assegnato ad alcun ruolo.

![Utenti assegnati](media/guidance-multitenant-identity/role-assignments.png)

> [AZURE.NOTE] In alternativa, l'applicazione può assegnare i ruoli a livello di codice, tramite l'API Graph di Azure AD. Questo tuttavia richiede che l'applicazione ottenga le autorizzazioni di scrittura per la directory Active Directory del cliente. Un'applicazione con queste autorizzazioni può generare danni significativi. I clienti non desiderano che l'app crei problemi alla directory e molti potrebbero non essere disponibili a concedere un tale livello di accesso.

**Ottenere le attestazioni basate su ruolo**. Quando l'utente esegue l'accesso, l'applicazione riceve i ruoli assegnati dell'utente in un'attestazione con il tipo `http://schemas.microsoft.com/ws/2008/06/identity/claims/role`.

Un utente può avere più ruoli o nessun ruolo. Nel codice dell'autorizzazione non supporre che l'utente abbia esattamente un'attestazione del ruolo. Scrivere un codice in grado di verificare se è presente un valore attestazione specifico:

```csharp
if (context.User.HasClaim(ClaimTypes.Role, "Admin")) { ... }
```

## Definizione dei ruoli con i gruppi di sicurezza di Azure AD

In questo approccio, i ruoli sono rappresentati come gruppi di sicurezza di Active Directory. L'applicazione assegna le autorizzazioni agli utenti in base alle rispettive appartenenze ai gruppi di sicurezza.

Vantaggi:

-	Per i clienti che non hanno Azure AD Premium, questo approccio consente di usare i gruppi di sicurezza per gestire le assegnazioni dei ruoli.

Svantaggi:

- Complessità. Poiché ogni tenant invia attestazioni dei gruppi diverse, l'app deve tenere traccia dei gruppi di sicurezza e dei rispettivi ruoli dell'applicazione corrispondenti, per ogni tenant.
- Se il cliente rimuove l'applicazione dal tenant di Active Directory, i gruppi di sicurezza rimangono nella directory di Active Directory.

### Implementazione

Nel manifesto dell'applicazione impostare la proprietà `groupMembershipClaims` su "SecurityGroup". Questa operazione è necessaria per ottenere le attestazioni delle appartenenze a gruppi da AAD.

```
{
   // ...
   "groupMembershipClaims": "SecurityGroup",
}
```

Quando un nuovo cliente effettua l'iscrizione, l'applicazione richiede al cliente di creare i gruppi di sicurezza per i ruoli necessari all'applicazione. Il cliente deve quindi immettere gli ID dell'oggetto gruppo nell'applicazione. L'applicazione archivia gli ID in una tabella che associa gli ID del gruppo ai ruoli dell'applicazione, per ogni tenant.

> [AZURE.NOTE] In alternativa, l'applicazione può creare i gruppi a livello di codice, tramite l'API Graph di Azure AD. Questa soluzione genera meno errori, ma richiede che l'applicazione ottenga le autorizzazioni di "lettura e scrittura in tutti i gruppi" per la directory di Active Directory del cliente e molti potrebbero non essere disponibili a concedere un tale livello di accesso.

Quando un utente esegue l'accesso:

1.	L'applicazione riceve i gruppi dell'utente come attestazioni. Il valore di ogni attestazione costituisce l'ID oggetto di un gruppo.
2.	Azure AD prevede un limite per il numero di gruppi inviati nel token. Se il numero di gruppi supera il limite, Azure AD invia un'attestazione di "eccedenza" speciale. Se l'attestazione in questione è presente, l'applicazione deve eseguire una query sull'API Graph di Azure AD per ottenere tutti i gruppi a cui appartiene l'utente. Per informazioni dettagliate, vedere la sezione che illustra le eccedenze delle attestazioni dei gruppi nell'articolo relativo alla [definizione dell'autorizzazione nelle applicazioni cloud con i gruppi di Active Directory].
3.	L'applicazione cerca gli ID oggetto nel proprio database per trovare i ruoli dell'applicazione corrispondenti da assegnare all'utente.
4.	L'applicazione aggiunge un valore attestazione personalizzato all'entità utente che rappresenta il ruolo dell'applicazione. Ad esempio: `survey_role` = "SurveyAdmin".

I criteri di autorizzazione devono usare l'attestazione del ruolo personalizzata e non l'attestazione del gruppo.

## Definizione dei ruoli con la funzionalità di gestione ruoli dell'applicazione

Con questo approccio, i ruoli dell'applicazione non vengono archiviati in Azure AD. Al contrario, l'applicazione archivia le assegnazioni dei ruoli per ogni utente nel proprio database, ad esempio usando la classe **RoleManager** in ASP.NET Identity.

Vantaggi:

-	L'app mantiene il controllo completo sulle assegnazioni di utenti e ruoli.

Svantaggi:

- Soluzione più complessa, difficile da gestire.
- Non consente di usare i gruppi di sicurezza di Active Directory per gestire le assegnazioni dei ruoli.
- Archivia le informazioni dell'utente nel database dell'applicazione che può non mantenere la sincronizzazione con la directory di Active Directory del tenant quando gli utenti vengono aggiunti o rimossi.   

Sono disponibili numerosi esempi di questo approccio. Ad esempio, vedere [Creare un'app ASP.NET MVC con autenticazione e database SQL e distribuirla nel servizio app di Azure].


## Risorse aggiuntive

-	[Autorizzazione in un'app Web con i ruoli dell'applicazione e le attestazioni basate su ruolo di Azure AD]
-	[Definizione dell'autorizzazione nelle applicazioni cloud con i gruppi di Active Directory]
-	[Controllo degli accessi in base al ruolo nelle applicazioni cloud con Azure AD]
-	[Token a tipi di attestazioni supportati] Descrive le attestazioni basate su ruolo e dei gruppi in Azure AD.
-	[Informazioni sul manifesto dell'applicazione in Azure Active Directory]

<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[parte di una serie]: guidance-multitenant-identity.md
[autorizzazione]: guidance-multitenant-identity-authorize.md
[Protezione di un'API Web back-end]: guidance-multitenant-identity-web-api.md
[definizione dell'autorizzazione nelle applicazioni cloud con i gruppi di Active Directory]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[Creare un'app ASP.NET MVC con autenticazione e database SQL e distribuirla nel servizio app di Azure]: ../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[Informazioni sul manifesto dell'applicazione in Azure Active Directory]: ../active-directory/active-directory-application-manifest.md
[Token a tipi di attestazioni supportati]: ../active-directory/active-directory-token-and-claims.md
[Controllo degli accessi in base al ruolo nelle applicazioni cloud con Azure AD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[Autorizzazione in un'app Web con i ruoli dell'applicazione e le attestazioni basate su ruolo di Azure AD]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims
[applicazione di esempio]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->