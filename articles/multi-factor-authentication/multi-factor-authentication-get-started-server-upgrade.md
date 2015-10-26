<properties 
	pageTitle="Aggiornamento dell'agente PhoneFactor al server Azure multi-Factor Authentication" 
	description="In questo documento viene descritto come iniziare a utilizzare il server Azure MFA e come eseguire l'aggiornamento dall'agente phonefactor precedente." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/24/2015" 
	ms.author="billmath"/>

# Aggiornamento dell'agente PhoneFactor al server Azure multi-Factor Authentication

Per l'aggiornamento dall'agente PhoneFactor V5.x o versione meno recente al server Azure multi-Factor Authentication, è necessario disinstallare l'agent PhoneFactor e relativi componenti prima di procedere con l'installazione del server Multi-Factor Authentication e relativi componenti.

## Per effettuare l'aggiornamnto dell'agente PhoneFactor al server Azure multi-Factor Authentication
<ol>
<li>Innanzitutto, eseguire il backup del file di dati di PhoneFactor. Il percorso di installazione predefinito è C:\\Programmi\\PhoneFactor\\Data\\Phonefactor.pfdata.


<li>Se il portale utenti è installato:</li>
<ol>
<li>Passare alla cartella di installazione ed eseguire il backup del file web.config. Il percorso di installazione predefinito è C:\inetpub\wwwroot\PhoneFactor.</li>


<li>Se sono stati aggiunti i temi personalizzati al portale, eseguire il backup della cartella personalizzata sotto la directory C:\inetpub\wwwroot\PhoneFactor\App_Themes.</li>


<li>Disinstallare il portale utenti tramite l'agente PhoneFactor (disponibile solo se installato nello stesso server dell'agente PhoneFactor) o tramite Programmi e funzionalità di Windows.</li></ol>




<li>Se è installato il servizio Web app per dispositivi mobili: <ol> <li>passare alla cartella di installazione ed eseguire il backup del file web.config. Il percorso di installazione predefinito è C:\\inetpub\\wwwroot\\PhoneFactorPhoneAppWebService.</li> <li>Disinstallare il servizio Web App per dispositivi mobili tramite Programmi e funzionalità di Windows.</li></ol>

<li>Se l'SDK del servizio Web è installato, disinstallarlo tramite l'agente PhoneFactor o Programmi e funzionalità di Windows.

<li>Disinstallare l'agente PhoneFactor tramite Programmi e funzionalità di Windows.

<li>Installare il server Multi-Factor Authentication. Si noti che il percorso di installazione viene rilevato dal Registro di sistema dall'installazione dell'agente PhoneFactor precedente in modo che venga installato nello stesso percorso (ad esempio, C:\\Programmi\\PhoneFactor). Le nuove installazioni avranno un percorso di installazione predefinito diverso (ad esempio, C:\\Programmi\\Multi-Factor Authentication Server). Il file di dati lasciato dall'agente PhoneFactor precedente deve essere aggiornato durante l'installazione, pertanto le impostazioni e gli utenti dovranno essere ancora disponibili dopo aver installato il nuovo server Multi-Factor Authentication.

<li>Se richiesto, attivare il server Multi-Factor Authentication e assicurarsi che venga assegnato al gruppo di replica corretto.

<li>Se l'SDK del servizio Web in precedenza è stato installato il, installare il nuovo SDK servizio Web tramite l'interfaccia utente del server Multi-Factor Authentication. Si noti che il nome predefinito della directory virtuale è adesso "MultiFactorAuthWebServiceSdk" anziché "PhoneFactorWebServiceSdk". Se si desidera utilizzare il nome precedente, è necessario modificare il nome della directory virtuale durante l'installazione. In caso contrario, si consente all'installazione di usare il nuovo nome predefinito, è necessario modificare l'URL in tutte le applicazioni che fanno riferimento all'SDK del servizio Web, ad esempio il portale utenti e il servizio Web app per dispositivi mobili in modo che punti al percorso corretto.

<li>Se il portale utenti in precedenza è stato installato nel server dell'agente PhoneFactor, installare il nuovo portale utenti Multi-Factor Authentication tramite la relativa interfaccia utente. Si noti che il nome predefinito della directory virtuale è adesso "MultiFactorAuth" anziché "PhoneFactor". Se si desidera utilizzare il nome precedente, è necessario modificare il nome della directory virtuale durante l'installazione. In caso contrario, se si consente all'installazione di usare il nuovo nome predefinito, è necessario fare clic sull'icona del portale utenti nel server Multi-Factor Authentication e aggiornare l'URL del portale utente nella scheda Impostazioni.

<li>Se il portale per gli utenti e/o il servizio Web App Mobile è stato precedentemente installato in un server diverso dall'agente PhoneFactor: <ol> <li>passare al percorso di installazione (ad esempio, C:\\Programmi\\PhoneFactor) e copiare i programmi di installazione appropriati nell'altro server. Sono disponibili programmi di installazione a 32 bit e a 64 bit per il portale per gli utenti e il servizio Web app per dispositivi mobili. Si chiamano rispettivamente MultiFactorAuthenticationUserPortalSetupXX.msi e MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi.</li> <li>Per installare il portale utenti nel server Web, aprire un prompt dei comandi come amministratore ed eseguire il file MultiFactorAuthenticationUserPortalSetupXX.msi. Si noti che il nome predefinito della directory virtuale è adesso "MultiFactorAuth" anziché "PhoneFactor". Se si desidera utilizzare il nome precedente, è necessario modificare il nome della directory virtuale durante l'installazione. In caso contrario, se si consente all'installazione di usare il nuovo nome predefinito, è necessario fare clic sull'icona del portale utenti nel server Multi-Factor Authentication e aggiornare l'URL del portale utente nella scheda Impostazioni. Gli utenti esistenti dovranno essere informati in merito al nuovo URL.</li> <li>Andare al percorso di installazione del portale utenti (ad esempio C:\\inetpub\\wwwroot\\MultiFactorAuth) e modificare il file web.config. Copiare i valori nelle sezioni appSettings e applicationSettings presenti nel file web.config originale di cui è stato eseguito il backup prima dell'aggiornamento nel nuovo file web.config. Se il nuovo nome della directory virtuale predefinita è stato mantenuto durante l'installazione dell'SDK servizio Web, modificare l'URL nella sezione applicationSettings in modo che punti al percorso corretto. Se nel file web.config precedente sono state modificate altre impostazioni predefinite, applicare le stesse modifiche nel nuovo file web.config.</li> <li>Per installare il servizio Web app per dispositivi mobili, aprire un prompt dei comandi come amministratore ed eseguire il file MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi. Si noti che il nome predefinito della directory virtuale è adesso “MultiFactorAuthMobileAppWebService” anziché “PhoneFactorPhoneAppWebService”. Se si desidera utilizzare il nome precedente, è necessario modificare il nome della directory virtuale durante l'installazione. È possibile scegliere un nome più breve per consentire agli utenti di digitarlo meglio nei propri dispositivi mobili. In alternativa, se si consente l'uso del nuovo nome predefinito durante l'installazione, è necessario fare clic sull'icona dell'app per dispositivi mobili nel server Multi-Factor Authentication e aggiornare l'URL del servizio Web app per dispositivi mobili.</li> <li>Andare al percorso di installazione del servizio Web app per dispositivi mobili (ad esempio C:\\inetpub\\wwwroot\\MultiFactorAuthMobileAppWebService) e modificare il file web.config. Copiare i valori nelle sezioni appSettings e applicationSettings presenti nel file web.config originale di cui è stato eseguito il backup prima dell'aggiornamento nel nuovo file web.config. Se il nuovo nome della directory virtuale predefinita è stato mantenuto durante l'installazione dell'SDK servizio Web, modificare l'URL nella sezione applicationSettings in modo che punti al percorso corretto. Se nel file web.config precedente sono state modificate altre impostazioni predefinite, applicare le stesse modifiche nel nuovo file web.config.</li></ol>


 


 

<!---HONumber=Oct15_HO3-->