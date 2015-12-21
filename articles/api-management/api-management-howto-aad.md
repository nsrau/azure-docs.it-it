<properties 
	pageTitle="Come autorizzare gli account per sviluppatori usando Azure Active Directory in Gestione API di Azure" 
	description="Informazioni su come autorizzare gli utenti usando Azure Active Directory in Gestione API." 
	services="api-management" 
	documentationCenter="API Management" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/07/2015" 
	ms.author="sdanie"/>

# Come autorizzare gli account per sviluppatori usando Azure Active Directory in Gestione API di Azure


## Panoramica
Questa guida illustra come abilitare l'accesso al portale per sviluppatori per tutti gli utenti in una o più directory di Azure Active Directory. Questa guida illustra anche come gestire gruppi di utenti di Azure Active Directory aggiungendo gruppi esterni contenenti gli utenti di una directory di Azure Active Directory.

>Per completare i passaggi di questa guida, è necessaria una directory di Azure Active Directory in cui creare un'applicazione.

## Come autorizzare gli account per sviluppatori usando Azure Active Directory

Per iniziare, fare clic su **Gestisci** nel portale di Azure classico per il servizio Gestione API. Verrà visualizzato il portale di pubblicazione di Gestione API.

![Portale di pubblicazione][api-management-management-console]

>Se non è stato creata un'istanza del servizio Gestione API, vedere [Creare un'istanza del servizio Gestione API][] nell'esercitazione [Introduzione a Gestione API di Azure][].

Fare clic su **Sicurezza** dal menu **Gestione API** sulla sinistra, quindi scegliere **Identità esterne**.

![Identità esterne][api-management-security-external-identities]

Fare clic su **Azure Active Directory**. Prendere nota dell'**URL di reindirizzamento** e passare alla directory di Azure Active Directory nel portale di Azure classico.

![Identità esterne][api-management-security-aad-new]

Fare clic sul pulsante **Add** per creare una nuova applicazione di Azure Active Directory e scegliere **Aggiungi un'applicazione che l'organizzazione sta sviluppando**.

![Aggiungere una nuova applicazione Azure Active Directory][api-management-new-aad-application-menu]

Immettere un nome per l'applicazione, selezionare **Applicazione Web e/o API Web**, quindi fare clic sul pulsante Avanti.

![Nuova applicazione Azure Active Directory][api-management-new-aad-application-1]

Per **URL accesso**, copiare l'**URL di reindirizzamento** dalla sezione **Azure Active Directory** della scheda **Identità esterne** nel portale di pubblicazione e rimuovere il suffisso **-aad** alla fine dell'URL. In questo esempio, l'**URL accesso** è `https://aad03.portal.current.int-azure-api.net/signin`.

In **URL ID app** immettere il dominio predefinito un dominio personalizzato per Azure Active Directory e aggiungervi una stringa univoca. Nel seguente esempio il dominio predefinito di ****https://contoso5api.onmicrosoft.com**viene utilizzato con il suffisso **/api** specificato.

![Proprietà della nuova applicazione Azure Active Directory][api-management-new-aad-application-2]

Fare clic sul segno di spunta per salvare e creare la nuova applicazione e passare alla scheda **Configura** per configurare la nuova applicazione.

![Nuova applicazione Azure Active Directory creata][api-management-new-aad-app-created]

Se per questa applicazione verranno usate più directory di Azure Active Directory, fare clic su **Sì** per **L'applicazione è multi-tenant**. Il valore predefinito è **No**.

![L'applicazione è multi-tenant][api-management-aad-app-multi-tenant]

Copiare l'**URL di reindirizzamento** dalla sezione **Azure Active Directory** della scheda **Identità esterne** nel portale di pubblicazione e incollarlo nella casella di testo **URL di risposta**.

![URL di risposta][api-management-aad-reply-url]

Scorrere fino alla fine della scheda Configura, selezionare l'elenco a discesa **Autorizzazioni applicazione** e selezionare **Lettura dati directory**.

![Autorizzazioni applicazione][api-management-aad-app-permissions]

Selezionare l'elenco a discesa **Autorizzazioni delegate** e selezionare **Abilita accesso e lettura dei profili degli utenti**.

![Autorizzazioni delegate][api-management-aad-delegated-permissions]

>Per altre informazioni sulle autorizzazioni applicazione e delegate, vedere [Accesso all'API Graph][].

Copiare l'**ID client** negli Appunti.

![Client Id][api-management-aad-app-client-id]

Tornare al portale di pubblicazione e incollare l'**ID client** copiato dalla configurazione dell'applicazione di Azure Active Directory.

![Client Id][api-management-client-id]

Tornare alla configurazione di Azure Active Directory e fare clic sull'elenco a discesa **Seleziona durata** menu nella sezione **Chiavi** e specificare un intervallo. In questo esempio viene usato **1 anno**.

![Chiave][api-management-aad-key-before-save]

Fare clic su **Salva** per salvare la configurazione e visualizzare la chiave. Copiare la chiave negli Appunti.

>Annotare il valore relativo alla chiave. Una volta chiusa la finestra di configurazione di Azure Active Directory, la chiave non potrà più essere visualizzata.

![Chiave][api-management-aad-key-after-save]

Tornare al portale di pubblicazione e incollare la chiave nella casella di testo **Segreto client**.

![Client Secret][api-management-client-secret]

**Tenant consentiti** specifica le directory che hanno accesso alle API dell'istanza del servizio Gestione API. Specificare i domini delle istanze di Azure Active Directory a cui si vuole concedere l'accesso. È possibile separare più domini con virgole, spazi o caratteri di nuova riga.

![Tenant consentiti][api-management-client-allowed-tenants]

Nella sezione **Tenant consentiti** si possono specificare più domini. Per consentire a un utente di accedere da un dominio diverso da quello originale in cui è stata registrata l'applicazione, un amministratore globale dell'altro dominio deve concedere l'autorizzazione che permette all'applicazione di accedere ai dati della directory. Per concedere l'autorizzazione, un amministratore globale deve accedere all'applicazione e fare clic su **Accetta**. Nel seguente esempio `miaoaad.onmicrosoft.com` è stato aggiunto a **Tenant consentiti** e un amministratore globale di tale dominio accede per la prima volta.

![Autorizzazioni][api-management-permissions-form]

>Se un amministratore non globale cerca di accedere prima che vengano concesse le autorizzazioni da un amministratore globale, il tentativo di accesso non riesce e viene visualizzata una schermata di errore.

Una volta specificata la configurazione desiderata, fare clic su **Salva**.

![Salva][api-management-client-allowed-tenants-save]

Una volta salvate le modifiche, gli utenti nella directory di Azure Active Directory specificata possono accedere al portale per sviluppatori seguendo i passaggi in [Accedere al portale per sviluppatori con un account Azure Active Directory][].

## Come aggiungere un gruppo di Azure Active Directory esterno

Dopo avere abilitato l'accesso per gli utenti in Azure Active Directory, è possibile aggiungere gruppi di Azure Active Directory in Gestione API per gestire più facilmente l'associazione degli sviluppatori del gruppo ai prodotti desiderati.

> Per configurare un gruppo esterno di Azure Active Directory, prima è necessario configurare Azure Active Directory nella scheda Identità seguendo la procedura della sezione precedente.

I gruppi esterni di Azure Active Directory vengono aggiunti dalla scheda **Visibilità** del prodotto per cui si desidera concedere l'accesso al gruppo. Fare clic su **Prodotti** e quindi fare clic sul nome del prodotto desiderato.

![Configure product][api-management-configure-product]

Passare alla scheda **Visibilità** e fare clic su **Aggiungi gruppi da Azure Active Directory**.

![Aggiungere i gruppi][api-management-add-groups]

Selezionare **Tenant di Azure Active Directory** nell'elenco a discesa e quindi digitare il nome del gruppo desiderato nella casella di testo dei **Gruppi** da aggiungere.

![Selezionare un gruppo][api-management-select-group]

Questo nome di gruppo è presente nell'elenco **Gruppi** di Azure Active Directory, come illustrato nell'esempio seguente.

![Elenco di gruppi di Azure Active Directory][api-management-aad-groups-list]

Fare clic su **Aggiungi** per convalidare il nome del gruppo e aggiungere il gruppo. In questo esempio viene aggiunto il gruppo esterno **Contoso 5 Developers**.

![Gruppo aggiunto][api-management-aad-group-added]

Fare clic su **Salva** per salvare la nuova selezione di gruppi.

Una volta che un gruppo di Azure Active Directory è stato configurato da un prodotto, può essere selezionato nella scheda **Visibilità** scheda di altri prodotti nell'istanza del servizio Gestione API.

Per rivedere e configurare le proprietà per i gruppi esterni dopo che sono stati aggiunti, fare clic sul nome del gruppo nella scheda **Gruppi**.

![Gestire i gruppi][api-management-groups]

Da qui è possibile modificare il **Nome** e la **Descrizione** del gruppo.

![Modificare un gruppo][api-management-edit-group]

Gli utenti della directory di Azure Active Directory configurata possono accedere al portale per sviluppatori e visualizzare e sottoscrivere qualsiasi gruppo su cui hanno visibilità, seguendo le istruzioni della sezione seguente.

## Come accedere al portale per sviluppatori con un account Azure Active Directory

Per accedere al portale per sviluppatori con un account Azure Active Directory configurato nelle sezioni precedenti, aprire una nuova finestra del browser con l'**URL accesso** della configurazione dell'applicazione Active Directory e fare clic su **Azure Active Directory**.

![Portale per sviluppatori][api-management-dev-portal-signin]

Immettere le credenziali di uno degli utenti in Azure Active Directory e fare clic su **Accedi**.

![Accesso][api-management-aad-signin]

È possibile che venga richiesto di compilare un modulo di registrazione se sono necessarie altre informazioni. Compilare il modulo di registrazione e fare clic su **Iscrizione**.

![Registrazione][api-management-complete-registration]

L'utente ora è connesso al portale per sviluppatori per l'istanza del servizio Gestione API.

![Registrazione completata][api-management-registration-complete]



[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-security-external-identities.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Introduzione a Gestione API di Azure]: api-management-get-started.md
[Get started with advanced API configuration]: api-management-get-started-advanced.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Creare un'istanza del servizio Gestione API]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accesso all'API Graph]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Accedere al portale per sviluppatori con un account Azure Active Directory]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account

<!---HONumber=AcomDC_1210_2015-->