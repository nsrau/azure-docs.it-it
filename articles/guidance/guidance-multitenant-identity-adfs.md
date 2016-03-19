<properties
   pageTitle="Federazione con il servizio AD FS del cliente | Microsoft Azure"
   description="Come eseguire la federazione con il servizio AD FS del cliente in un'applicazione multi-tenant"
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

# Federazione con il servizio AD FS del cliente

Questo articolo fa [parte di una serie]. Per questa serie è anche disponibile un'[applicazione di esempio] completa.

Questo articolo descrive come un'applicazione SaaS multi-tenant può supportare l'autenticazione tramite Active Directory Federation Services (AD FS) per la federazione con il servizio AD FS del cliente.

## Panoramica

Azure Active Directory (Azure AD) semplifica la procedura di accesso degli utenti dai tenant di Azure AD, ed è disponibile anche per i clienti di Office365 e Dynamics CRM Online. Per i clienti che usano Active Directory in una Intranet aziendale è tuttavia necessario valutare altre soluzioni.

Una opzione prevede che i clienti sincronizzino il servizio Active Directory locale con Azure AD tramite [Azure AD Connect]. Per alcuni clienti potrebbe risultare impossibile adottare questo approccio a causa di criteri IT aziendali o altri motivi. In questo caso, per la federazione è possibile usare Active Directory Federation Services (AD FS).

Per abilitare questo scenario:

-	Il cliente deve avere una farm AD FS con connessione a Internet.
-	Il provider SaaS distribuisce la propria farm AD FS.
-	Il cliente e il provider SaaS devono configurare un [trust federativo]. Si tratta di un processo manuale.

Esistono tre ruoli principali nella relazione di trust:

-	Il servizio AD FS del cliente rappresenta il [partner account], responsabile dell'autenticazione degli utenti dall'istanza di Active Directory del cliente e della creazione di token di sicurezza con attestazioni utente.
-	Il servizio AD FS del provider SaaS è il [partner risorse] che considera attendibile il partner account e riceve le attestazioni utente.
-	L'applicazione viene configurata come una relying party nell'istanza di AD FS del provider SaaS.

	![Trust federativo](media/guidance-multitenant-identity/federation-trust.png)

> [AZURE.NOTE] In questo articolo si presuppone che l'applicazione usi OpenID Connect come protocollo di autenticazione. Un'altra opzione prevede l'uso di WS-Federation.

> Per il protocollo OpenID Connect, il provider SaaS deve usare AD FS 4.0 in Windows Server 2016, al momento disponibile solo nella versione Technical Preview. AD FS 3.0 non supporta il protocollo OpenID Connect.

> ASP.NET Core 1.0 non include il supporto predefinito per WS-Federation.

Per un esempio che mostra come usare WS-Federation con ASP.NET 4, vedere https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation

## Flusso di autenticazione

1.	Quando l'utente fa clic su "accedi", l'applicazione lo reindirizza a un endpoint OpenID Connect del servizio AD FS del provider SaaS.
2.	L'utente immette il nome utente aziendale ("`alice@corp.contoso.com`"). AD FS usa l'individuazione dell'area di autenticazione principale per il reindirizzamento al servizio AD FS del cliente, in cui l'utente immette le proprie credenziali.
3.	Il servizio AD FS del cliente invia le attestazioni utente al servizio AD FS del provider SaaS tramite WS-Federation o SAML.
4.	Le attestazioni vengono inviate da AD FS all'app tramite OpenID Connect. Questo passaggio richiede una transizione di protocollo da WS-Federation.

## Limitazioni

Al momento della stesura di questo articolo, l'applicazione riceve un set di attestazioni limitato nel token id\_token di OpenID. Poiché AD FS 4.0 è ancora in versione di anteprima è probabile che questo elenco venga ampliato in futuro. Ma se l'app in uso richiede altre attestazioni, questo al momento non è possibile ed è necessario tenerne conto prima di procedere.

Attualmente nel token id\_token vengono inviate le attestazioni seguenti:

Attestazione | Descrizione
------|-------------
aud | Destinatari, ovvero l'applicazione per cui sono state rilasciate le attestazioni.
authenticationinstant | [Istante di autenticazione]
c\_hash | Valore hash del codice
exp | [Scadenza]
iat | [Ora di emissione]
iss | Autorità di certificazione Il valore di questa attestazione corrisponde sempre al servizio AD FS del partner risorse e non a quello del cliente. In altre parole, questa attestazione identifica il provider SaaS come autorità di certificazione e non il cliente.
name | Nome utente. Esempio: `john@corp.fabrikam.com`.
nameidentifier | [Identificatore del nome]
nonce | Nonce sessione
upn | Nome dell'entità utente. Esempio: john@corp.fabrikam.com

In particolare, si noti che l'attestazione "iss" non specifica il servizio AD FS del cliente. Per informazioni sul dominio del cliente, è necessario esaminare il nome dell'entità utente. La parte seguente di questo articolo descrive come configurare la relazione di trust tra la relying party (app) e il partner account (cliente).

## Distribuzione di AD FS

Il provider SaaS può distribuire AD FS in locale o in macchine virtuali di Azure. Per garantire la sicurezza e la disponibilità, seguire queste linee guida:

-	Distribuire almeno due server AD FS e due server proxy AD FS per ottenere la maggiore disponibilità del servizio AD FS.
-	I controller di dominio e i server AD FS non devono mai essere esposti direttamente a Internet e devono risiedere in una rete virtuale con accesso diretto.
-	Per pubblicare i server AD FS in Internet è necessario usare i proxy applicazione Web, precedentemente noti come proxy AD FS.

Per configurare una topologia simile in Azure, è necessario usare reti virtuali, gruppi di sicurezza di rete, macchine virtuali di Azure e set di disponibilità. Per altre informazioni, vedere [Linee guida per la distribuzione di Windows Server Active Directory nelle macchine virtuali di Azure](MSDN).

## Configurare l'applicazione per l'uso dell'autenticazione OpenID Connect con AD FS

Il provider SaaS deve abilitare la connessione OpenID Connect tra l'applicazione e AD FS. A tale scopo, aggiungere un gruppo di applicazioni in AD FS. Per istruzioni dettagliate su questa procedura, vedere l'argomento relativo alla configurazione di un'app Web per l'accesso ad AD FS tramite OpenID Connect in questo [post di blog]. Configurare quindi il middleware per OpenID Connect. L'endpoint dei metadati è `https://domain/adfs/.well-known/openid-configuration`, dove il dominio corrisponde al dominio AD FS del provider SaaS.

In genere questo endpoint verrà combinato con altri endpoint OpenID Connect, ad esempio AAD. Sarà necessario prevedere due pulsanti di accesso diversi per poterli distinguere, in modo che l'utente venga indirizzato all'endpoint di autenticazione corretto.

## Configurare il partner risorse AD FS

Il provider SaaS deve eseguire la procedura seguente per ogni cliente che si connette tramite AD FS:

1.	Aggiungere un trust del provider di attestazioni.
2.	Aggiungere le regole attestazioni.
3.	Abilitare l'individuazione dell'area di autenticazione.

Ecco la procedura dettagliata.

### Aggiungere il trust del provider di attestazioni

1.	In Server Manager selezionare **Strumenti** e quindi fare clic su **Gestione AD FS**.
2.	Nell'albero della console, in **AD FS** fare clic con il pulsante destro del mouse su **Attendibilità provider di attestazioni**. Scegliere **Aggiungi attendibilità provider di attestazioni**.
3.	Fare clic su **Avvia** per avviare la procedura guidata.
4.	Selezionare l'opzione "Importa dati sul provider di attestazioni pubblicati online o in una rete locale". Immettere l'URI dell'endpoint dei metadati di federazione del cliente. Esempio: `https://contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`. Questa informazione deve essere fornita dal cliente.
5.	Completare la procedura guidata usando le opzioni predefinite.

### Modificare le regole attestazioni

1.	Fare clic con il pulsante destro del mouse sul trust del provider di attestazioni e scegliere **Modifica regole attestazione**.
2.	Fare clic su **Aggiungi regola**.
3.	Selezionare "Applicare la funzione di pass-through o di filtro a un'attestazione in ingresso" e fare clic su **Avanti**. ![Aggiunta guidata regole attestazione di trasformazione](media/guidance-multitenant-identity/edit-claims-rule.png)
4.	Immettere un nome per la regola.
5.	In "Tipo di attestazione in ingresso" selezionare **UPN**.
6.	Selezionare "Pass-through di tutti i valori attestazione". ![Aggiunta guidata regole attestazione di trasformazione](media/guidance-multitenant-identity/edit-claims-rule2.png)
7.	Fare clic su **Finish**.
8.	Ripetere i passaggi da 2 a 7 e specificare **Tipo di attestazione ancoraggio** per il tipo di attestazione in ingresso.
9.	Fare clic su **OK** per completare la procedura guidata.

### Abilitare l'individuazione dell'area di autenticazione
Eseguire lo script di PowerShell seguente:

```
Set-ADFSClaimsProviderTrust -TargetName "name" -OrganizationalAccountSuffix @("suffix")
```

dove "name" corrisponde al nome descrittivo del trust del provider di attestazioni e "suffix" rappresenta il suffisso del nome dell'entità utente per il dominio Active Directory del cliente, ad esempio "corp.fabrikam.com".

Con questa configurazione, gli utenti possono specificare il proprio account aziendale e AD FS seleziona automaticamente il provider di attestazioni corrispondente. Per altre informazioni, vedere la sezione "Configurare il provider di identità per l'uso di determinati suffissi di posta elettronica" in [Personalizzazione delle pagine di accesso ad AD FS].

## Configurazione del partner account di AD FS

Procedura per il cliente:

1.	Aggiungere un trust della relying party.
2.	Aggiungere le regole attestazioni.

### Aggiungere il trust della relying party.

1.	In Server Manager selezionare **Strumenti** e quindi fare clic su **Gestione AD FS**.
2.	Nell'albero della console, in **AD FS** fare clic con il pulsante destro del mouse su **Attendibilità componente**. Scegliere **Aggiungi attendibilità componente**.
3.	Selezionare **In grado di riconoscere attestazioni** e fare clic su **Avvia**.
4.	Nella pagina **Seleziona origine dati** scegliere l'opzione "Importa dati sul provider di attestazioni pubblicati online o in una rete locale". Immettere l'URI dell'endpoint dei metadati di federazione del provider SaaS. ![Aggiunta guidata attendibilità componente](media/guidance-multitenant-identity/add-rp-trust.png)
5.	Nella pagina **Specifica nome visualizzato** immettere un nome.
6.	Nella pagina **Scegliere i criteri di controllo di accesso** scegliere un criterio. È possibile autorizzare tutti gli utenti dell'organizzazione o scegliere un gruppo di sicurezza specifico. ![Aggiunta guidata attendibilità componente](media/guidance-multitenant-identity/add-rp-trust2.png)
7.	Fare clic su **Avanti** per completare la procedura guidata.

### Aggiungere le regole attestazioni

1.	Fare clic con il pulsante destro del mouse sul trust della relying party appena aggiunto e quindi scegliere **Modifica criteri di rilascio delle attestazioni**.
2.	Fare clic su **Aggiungi regola**.
3.	Selezionare "Inviare attributi LDAP come attestazioni" e fare clic su **Avanti**. ![Aggiunta guidata regole attestazione di trasformazione](media/guidance-multitenant-identity/add-claims-rules.png)
4.	Immettere un nome per la regola, ad esempio "UPN".
5.	In **Archivio attributi** selezionare **Active Directory**.
6.	Nella sezione **Mapping degli attributi LDAP**:
  -	In **Attributo LDAP** selezionare **User-Principal-Name**.
  - In **Tipo di attestazione in uscita** selezionare **UPN**. ![Aggiunta guidata regole attestazione di trasformazione](media/guidance-multitenant-identity/add-claims-rules2.png)
7.	Fare clic su **Finish**.
8.	Fare nuovamente clic su **Add Rule**.
9.	Selezionare "Inviare attestazioni mediante una regola personalizzata" e fare clic su **Avanti**.
10.	Immettere un nome per la regola, ad esempio "Attestazione ancoraggio".
11.	In **Regola personalizzata** immettere quanto segue:

	```
    EXISTS([Type == "http://schemas.microsoft.com/ws/2014/01/identity/claims/anchorclaimtype"])=>
      issue (Type = "http://schemas.microsoft.com/ws/2014/01/identity/claims/anchorclaimtype",
             Value = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn");
	```

    Questa regola esegue il mapping dell'attestazione UPN all'attestazione ancoraggio.

12.	Fare clic su **Finish**.
13.	Fare clic su **OK** per completare la procedura guidata.

<!-- Links -->
[parte di una serie]: guidance-multitenant-identity.md
[Azure AD Connect]: ../active-directory/active-directory-aadconnect.md
[trust federativo]: https://technet.microsoft.com/library/cc738707.aspx
[partner account]: https://technet.microsoft.com/library/cc758463(v=ws.10).aspx
[partner risorse]: https://technet.microsoft.com/library/cc758463(v=ws.10).aspx
[Istante di autenticazione]: https://msdn.microsoft.com/library/system.security.claims.claimtypes.authenticationinstant%28v=vs.110%29.aspx
[Scadenza]: http://tools.ietf.org/html/draft-ietf-oauth-json-web-token-25#section-4.1.4
[Ora di emissione]: http://tools.ietf.org/html/draft-ietf-oauth-json-web-token-25#section-4.1.6
[Identificatore del nome]: https://technet.microsoft.com/library/ee913589.aspx
[Guidelines for Deploying Windows Server Active Directory on Azure Virtual Machines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[post di blog]: http://www.cloudidentity.com/blog/2015/08/21/OPENID-CONNECT-WEB-SIGN-ON-WITH-ADFS-IN-WINDOWS-SERVER-2016-TP3/
[Personalizzazione delle pagine di accesso ad AD FS]: https://technet.microsoft.com/library/dn280950.aspx
[applicazione di esempio]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->