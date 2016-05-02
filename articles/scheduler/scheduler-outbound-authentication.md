<properties
 pageTitle="Autenticazione in uscita dell'Utilità di pianificazione"
 description="Autenticazione in uscita dell'Utilità di pianificazione"
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="03/09/2016"
 ms.author="krisragh"/>

# Autenticazione in uscita dell'Utilità di pianificazione

I processi dell'Utilità di pianificazione potrebbero richiedere servizi che necessitano di autenticazione. In questo modo, un servizio richiamato può determinare se l'utilità di pianificazione può accedere alle sue risorse. Alcuni di questi servizi includono altri servizi di Azure, Salesforce.com, Facebook e siti Web custom protetti.

## Aggiunta e rimozione di autenticazione

Aggiungere un'autenticazione a un'utilità di pianificazione è semplice – è sufficiente aggiungere un elemento figlio JSON `authentication` all'`request` elemento durante la creazione o l’aggiornamento di un processo. I segreti passati al servizio dell’Utilità di pianificazione in una richiesta PUT, PATCH o POST – come parte dell’oggetto `authentication` – non vengono mai restituiti nelle risposte. Nelle risposte, le informazioni segrete sono impostati su null o potrebbero avere un token pubblico che rappresenti l'entità autenticata.

Per rimuovere l'autenticazione, effettuare un’azione PUT o PATCH in modo esplicito, impostando l’oggetto `authentication` su null. Nessuna proprietà di autenticazione verrà visualizzata nella risposta.

Attualmente, gli unici tipi di autenticazione supportati sono il modello `ClientCertificate` (per l'utilizzo di certificati client SSL/TLS), il modello `Basic` (per l'autenticazione di Base) e il modello `ActiveDirectoryOAuth` (per l'autenticazione OAuth di Active Directory).

## Corpo della richiesta per l'autenticazione ClientCertificate

Quando si aggiunge un'autenticazione utilizzando il modello `ClientCertificate`, specificare i seguenti elementi aggiuntivi nel corpo della richiesta.

|Elemento|Descrizione|
|:---|:---|
|_authentication (parent element)_|Oggetto di autenticazione per l'utilizzo di un certificato client SSL.|
|_type_|Obbligatorio. Tipo di autenticazione. Per i certificati client SSL, il valore deve essere `ClientCertificate`.|
|_pfx_|Obbligatorio. I contenuti del file PFX in codifica Base64.|
|_password_|Obbligatorio. La password per accedere al file PFX.|


## Corpo della risposta per l'autenticazione ClientCertificate

Quando viene inviata una richiesta con le informazioni di autenticazione, la risposta contiene i seguenti elementi relativi all'autenticazione.

|Elemento |Descrizione |
|:--|:--|
|_authentication (parent element)_ |Oggetto di autenticazione per l'utilizzo di un certificato client SSL.|
|_type_ |Tipo di autenticazione. Per i certificati client SSL, il valore è `ClientCertificate`.|
|_certificateThumbprint_ |L'identificazione personale del certificato.|
|_certificateSubjectName_ |Il nome distintivo del soggetto del certificato.|
|_certificateExpiration_ |La data di scadenza del certificato.|

## Corpo della richiesta per l'autenticazione di Base

Quando si aggiunge un'autenticazione utilizzando il modello `Basic`, specificare i seguenti elementi aggiuntivi nel corpo della richiesta.

|Elemento|Descrizione|
|:--|:--|
|_authentication (parent element)_ |Oggetto di autenticazione per l’utilizzo dell'autenticazione di Base.|
|_type_ |Obbligatorio. Tipo di autenticazione. Per l'autenticazione di Base, il valore deve essere `Basic`.|
|_username_ |Obbligatorio. Nome utente da autenticare.|
|_password_ |Obbligatorio. Password da autenticare.|

## Corpo della risposta per l'autenticazione di Base

Quando viene inviata una richiesta con le informazioni di autenticazione, la risposta contiene i seguenti elementi relativi all'autenticazione.

|Elemento|Descrizione|
|:--|:--|
|_authentication (parent element)_ |Oggetto di autenticazione per l’utilizzo dell'autenticazione di Base.|
|_type_ |Tipo di autenticazione. Per l'autenticazione di Base, il valore è `Basic`.|
|_username_ |Il nome utente autenticato.|

## Corpo della richiesta per l'autenticazione ActiveDirectoryOAuth

Quando si aggiunge un'autenticazione utilizzando il modello `ActiveDirectoryOAuth`, specificare i seguenti elementi aggiuntivi nel corpo della richiesta.

|Elemento |Descrizione |
|:--|:--|
|_authentication (parent element)_ |Oggetto di autenticazione per l'autenticazione basata su ActiveDirectoryOAuth.|
|_type_ |Obbligatorio. Tipo di autenticazione. Per l'autenticazione ActiveDirectoryOAuth, il valore deve essere `ActiveDirectoryOAuth`.|
|_tenant_ |Obbligatorio. L'identificatore del tenant di Azure AD.|
|_audience_ |Obbligatorio. Viene impostato su https://management.core.windows.net/.|
|_clientId_ |Obbligatorio. Fornisce l'identificativo del client per l'applicazione Active Directory di Azure.|
|_secret_ |Obbligatorio. Segreto del client che richiede il token.|

### Determinazione del l'identificatore del tenant

È possibile trovare l'identificatore del tenant di Azure AD eseguendo `Get-AzureAccount` in Azure PowerShell.

## Corpo della risposta per l'autenticazione ActiveDirectoryOAuth

Quando viene inviata una richiesta con le informazioni di autenticazione, la risposta contiene i seguenti elementi relativi all'autenticazione.

|Elemento |Descrizione |
|:--|:--|
|_authentication (parent element)_ |Oggetto di autenticazione per l'autenticazione basata su ActiveDirectoryOAuth.|
|_type_ |Tipo di autenticazione. Per l'autenticazione ActiveDirectoryOAuth, il valore è `ActiveDirectoryOAuth`.|
|_tenant_ |L'identificatore del tenant di Azure AD. |
|_audience_ |Viene impostato su https://management.core.windows.net/.|
|_clientId_ |L'identificativo del client per l'applicazione Active Directory di Azure.|

## Vedere anche


 [Che cos'è l'Utilità di pianificazione?](scheduler-intro.md)

 [Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione di Azure](scheduler-concepts-terms.md)

 [Introduzione all'uso dell'Utilità di pianificazione di Azure nel portale di Azure](scheduler-get-started-portal.md)

 [Piani e fatturazione nell'utilità di pianificazione di Azure](scheduler-plans-billing.md)

 [Informazioni di riferimento sull'API REST dell'Utilità di pianificazione di Azure](https://msdn.microsoft.com/library/mt629143)

 [Informazioni di riferimento sui cmdlet PowerShell dell'Utilità di pianificazione di Azure](scheduler-powershell-reference.md)

 [Livelli elevati di disponibilità e affidabilità dell'Utilità di pianificazione di Azure](scheduler-high-availability-reliability.md)

 [Limiti, valori predefiniti e codici di errore dell'Utilità di pianificazione di Azure](scheduler-limits-defaults-errors.md)

<!---HONumber=AcomDC_0420_2016-->